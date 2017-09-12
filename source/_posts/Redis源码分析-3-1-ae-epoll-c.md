---
title: Redis源码分析(3.1)-ae_epoll.c
date: 2017-09-12 15:56:08
tags:
---
# 总览
ae_epoll.c是Redis中对linux下epoll系统调用的封装，调用原生的epoll要有很多的细节处理，统一了事件源IO事件（包括网络IO和文件IO），定时器事件。ae_epoll将epoll的一套系统调用封装的更加的human friendly～
# 源码分析
## 数据定义
ae_epoll依赖的的数据定义部分写在了ae.h中，这里一并给出。
```
typedef void aeFileProc(struct aeEventLoop *eventLoop, int fd, void *clientData, int mask);
typedef int aeTimeProc(struct aeEventLoop *eventLoop, long long id, void *clientData);
typedef void aeEventFinalizerProc(struct aeEventLoop *eventLoop, void *clientData);
typedef void aeBeforeSleepProc(struct aeEventLoop *eventLoop);
```
上述接口进行了事件回调函数的定义，举个栗子，aeFileProc 的类型为 void(*)(struct aeEventLoop *, int, void*, int)类型的函数指针。
```
/* File event structure */
typedef struct aeFileEvent {
    int mask; /* one of AE_(READABLE|WRITABLE) */
    aeFileProc *rfileProc;
    aeFileProc *wfileProc;
    void *clientData;
} aeFileEvent;
```
mask表示事件类型，分为AE_READABLE可读和AE_WRITABLE可写，rfileProc为读数据的callback，wfileProc为写数据的callback，clientData可以携带额外数据
```
/* Time event structure */
typedef struct aeTimeEvent {
    long long id; /* time event identifier. */
    long when_sec; /* seconds */
    long when_ms; /* milliseconds */
    aeTimeProc *timeProc;
    aeEventFinalizerProc *finalizerProc;
    void *clientData;
    struct aeTimeEvent *next;
} aeTimeEvent;
```
定时器事件里，和IO事件不同的一点为，多个IO事件存在一个数组里，而定时器事件组成了一个链表，时间小的在前面，大的在后面。
```
/* A fired event */
typedef struct aeFiredEvent {
    int fd;
    int mask;
} aeFiredEvent;
```
触发的IO事件，fd为文件标识符，mask为AE_(READABLE|WRITABLE)
```
/* State of an event based program */
typedef struct aeEventLoop {
    int maxfd;   /* highest file descriptor currently registered */
    int setsize; /* max number of file descriptors tracked */
    long long timeEventNextId;
    time_t lastTime;     /* Used to detect system clock skew */
    aeFileEvent *events; /* Registered events */
    aeFiredEvent *fired; /* Fired events */
    aeTimeEvent *timeEventHead;
    int stop;
    void *apidata; /* This is used for polling API specific data */
    aeBeforeSleepProc *beforesleep;
} aeEventLoop;
```
事件循环，fd是最大支持文件描述符的大小，timeEventNextId用于记录该时间循环中定时器事件的id，lastTime实用来记录最后一次执行事件的时间，用来检测[clock skew](https://en.wikipedia.org/wiki/Clock_skew)现象。并在processTimeEvents方法中做相应的处理，events[i]表示值为i的文件描述符对应的aeFileEvent。
stop如果设置为true就会终止事件循环。apidata中用与存放epoll api相关的数据。
```
typedef struct aeApiState {
    int epfd;
    struct epoll_event *events;
} aeApiState;
```
对epoll事件结构进行了一个简单的封装。存放在aeEventLoop的apidata中。events用于存储被触发的时事件。
## API
```
static int aeApiCreate(aeEventLoop *eventLoop) {
    aeApiState *state = zmalloc(sizeof(aeApiState));

    if (!state) return -1;
    state->events = zmalloc(sizeof(struct epoll_event)*eventLoop->setsize);
    if (!state->events) {
        zfree(state);
        return -1;
    }
    state->epfd = epoll_create(1024); /* 1024 is just a hint for the kernel */
    if (state->epfd == -1) {
        zfree(state->events);
        zfree(state);
        return -1;
    }
    eventLoop->apidata = state;
    return 0;
}
```
向eventLoop中注册epoll api的数据。由setsize决定event数组大小
```
static int aeApiAddEvent(aeEventLoop *eventLoop, int fd, int mask) {
    aeApiState *state = eventLoop->apidata;
    struct epoll_event ee = {0}; /* avoid valgrind warning */
    /* If the fd was already monitored for some event, we need a MOD
     * operation. Otherwise we need an ADD operation. */
    int op = eventLoop->events[fd].mask == AE_NONE ?
            EPOLL_CTL_ADD : EPOLL_CTL_MOD;

    ee.events = 0;
    mask |= eventLoop->events[fd].mask; /* Merge old events */
    if (mask & AE_READABLE) ee.events |= EPOLLIN;
    if (mask & AE_WRITABLE) ee.events |= EPOLLOUT;
    ee.data.fd = fd;
    if (epoll_ctl(state->epfd,op,fd,&ee) == -1) return -1;
    return 0;
}
```
封装了epoll_ctl函数，注释很清楚。
```
static void aeApiDelEvent(aeEventLoop *eventLoop, int fd, int delmask) {
    aeApiState *state = eventLoop->apidata;
    struct epoll_event ee = {0}; /* avoid valgrind warning */
    int mask = eventLoop->events[fd].mask & (~delmask);

    ee.events = 0;
    if (mask & AE_READABLE) ee.events |= EPOLLIN;
    if (mask & AE_WRITABLE) ee.events |= EPOLLOUT;
    ee.data.fd = fd;
    if (mask != AE_NONE) {
        epoll_ctl(state->epfd,EPOLL_CTL_MOD,fd,&ee);
    } else {
        /* Note, Kernel < 2.6.9 requires a non null event pointer even for
         * EPOLL_CTL_DEL. */
        epoll_ctl(state->epfd,EPOLL_CTL_DEL,fd,&ee);
    }
}
```
删除对应fd上的mask，如果对应fd上mask被全部删除，就把对应的fd在epoll中删除。
```
static int aeApiPoll(aeEventLoop *eventLoop, struct timeval *tvp) {
    aeApiState *state = eventLoop->apidata;
    int retval, numevents = 0;

    retval = epoll_wait(state->epfd,state->events,eventLoop->setsize,
                        (int) (tvp ? (tvp->tv_sec * 1000 + tvp->tv_usec / 1000) : -1));
    if (retval > 0) {
        int j;

        numevents = retval;
        for (j = 0; j < numevents; j++) {
            int mask = 0;
            struct epoll_event *e = state->events+j;

            if (e->events & EPOLLIN) mask |= AE_READABLE;
            if (e->events & EPOLLOUT) mask |= AE_WRITABLE;
            if (e->events & EPOLLERR) mask |= AE_WRITABLE;
            if (e->events & EPOLLHUP) mask |= AE_WRITABLE;
            eventLoop->fired[j].fd = e->data.fd;
            eventLoop->fired[j].mask = mask;
        }
    }
    return numevents;
}
```
epoll_wait获取触发的事件，放到aeEventLoop的fired数组中。   
附上EPOLLERR和EPOLLHUB的出现原因
man epoll_ctl
```
EPOLLERR
        Error condition happened on the associated file descriptor.  epoll_wait(2) will always wait for this event; it is not necessary to set it in events.

EPOLLHUP
        Hang up happened on the associated file descriptor.  epoll_wait(2) will always wait for this event; it is not necessary to set it in events.  Note that when reading from a channel such as a pipe or a stream socket, this event merely indicates that the peer closed its end of the channel.  Subsequent reads from the channel will return 0 (end of file)  only  after  all outstanding data in the channel has been consumed.
```
# 总结
一定程度封装了epoll原生api的繁琐操作和略微让人烦躁的api设计，统一了事件源，统一了事件的获取api以及callback操作。