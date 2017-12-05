---
title: Redis源码分析(5)-Database
date: 2017-12-03 22:23:52
tags:
  - Redis
categories:
  - Redis源码分析
---
# 总览

# 源码分析
## 数据定义
Redis database相关的代码主要存在于db.c 中，不过还要涉及一些其他的在源码中出现频率非常高的数据结构，例如 redisDb（server.h中）、redisObject/robj（server.h中）、client（server.h中）、redisCommand（server.h中）。  

```
/* Redis database representation. There are multiple databases identified
 * by integers from 0 (the default database) up to the max configured
 * database. The database number is the 'id' field in the structure. */
typedef struct redisDb {
    dict *dict;                 /* The keyspace for this DB */
    dict *expires;              /* Timeout of keys with a timeout set */
    dict *blocking_keys;        /* Keys with clients waiting for data (BLPOP) */
    dict *ready_keys;           /* Blocked keys that received a PUSH */
    dict *watched_keys;         /* WATCHED keys for MULTI/EXEC CAS */
    struct evictionPoolEntry *eviction_pool;    /* Eviction pool of keys */
    int id;                     /* Database ID */
    long long avg_ttl;          /* Average TTL, just for stats */
} redisDb;
```
一个redisDB对象对应一个redis数据库，redis默认有16个数据库，可以通过select命令选择数据库。
{% table %}
|字段|含义|
|---|:---:|
|dict|db键值空间,存储kv映射|
|expires|存放即将过期的key，val表示相应key过期的时间点|
|blocking_keys|用于存放在BLPOP命令中阻塞的key以及相对应的client，该dict结构中key为BLPOP命令中组的的键，value为阻塞在该键上的客户端队列|
|ready_keys|setDictType字典类型，即当作集合使用，用于存放被block的key中就绪的部分|
|watched_keys|为事物中的watch命令以及cas乐观锁提供支持，watched_keys中的key为数据库中的键，value是由client组成的队列，表示对应key在哪些client的监视之下|
|eviction_pool|维护一个候选释放对象的列表，当内存不足时释放|
|id|数据库id|
|avg_ttl|平均ttl，统计使用|
{% endtable %}

```
/* A redis object, that is a type able to hold a string / list / set */

/* The actual Redis Object */
/* Object types */
#define OBJ_STRING 0
#define OBJ_LIST 1
#define OBJ_SET 2
#define OBJ_ZSET 3
#define OBJ_HASH 4

#define LRU_BITS 24
#define LRU_CLOCK_MAX ((1<<LRU_BITS)-1) /* Max value of obj->lru */
#define LRU_CLOCK_RESOLUTION 1000 /* LRU clock resolution in ms */
typedef struct redisObject {
    unsigned type:4;
    unsigned encoding:4;
    unsigned lru:LRU_BITS; /* lru time (relative to server.lruclock) */
    int refcount;
    void *ptr;
} robj;
```
{% table %}
|字段|含义|
|---|:---:|
|type|ptr指向的类型类型，如OBJ_STRING，OBJ_LIST等|
|encoding|一部分对象例如string和hash可能有多种编码方式，具体编码方式通过encoding指定，例如string有OBJ_ENCODING_RAW和OBJ_ENCODING_EMBSTR两种编码|
|lru|lru时间戳|
|refCount|引用计数|
|ptr|指向数据|
{% endtable %}

```
/* With multiplexing we need to take per-client state.
 * Clients are taken in a linked list. */
typedef struct client {
    uint64_t id;            /* Client incremental unique ID. */
    int fd;                 /* Client socket. */
    redisDb *db;            /* Pointer to currently SELECTed DB. */
    int dictid;             /* ID of the currently SELECTed DB. */
    robj *name;             /* As set by CLIENT SETNAME. */
    sds querybuf;           /* Buffer we use to accumulate client queries. */
    size_t querybuf_peak;   /* Recent (100ms or more) peak of querybuf size. */
    int argc;               /* Num of arguments of current command. */
    robj **argv;            /* Arguments of current command. */
    struct redisCommand *cmd, *lastcmd;  /* Last command executed. */
    int reqtype;            /* Request protocol type: PROTO_REQ_* */
    int multibulklen;       /* Number of multi bulk arguments left to read. */
    long bulklen;           /* Length of bulk argument in multi bulk request. */
    list *reply;            /* List of reply objects to send to the client. */
    unsigned long long reply_bytes; /* Tot bytes of objects in reply list. */
    size_t sentlen;         /* Amount of bytes already sent in the current
                               buffer or object being sent. */
    time_t ctime;           /* Client creation time. */
    time_t lastinteraction; /* Time of the last interaction, used for timeout */
    time_t obuf_soft_limit_reached_time;
    int flags;              /* Client flags: CLIENT_* macros. */
    int authenticated;      /* When requirepass is non-NULL. */
    int replstate;          /* Replication state if this is a slave. */
    int repl_put_online_on_ack; /* Install slave write handler on ACK. */
    int repldbfd;           /* Replication DB file descriptor. */
    off_t repldboff;        /* Replication DB file offset. */
    off_t repldbsize;       /* Replication DB file size. */
    sds replpreamble;       /* Replication DB preamble. */
    long long reploff;      /* Replication offset if this is our master. */
    long long repl_ack_off; /* Replication ack offset, if this is a slave. */
    long long repl_ack_time;/* Replication ack time, if this is a slave. */
    long long psync_initial_offset; /* FULLRESYNC reply offset other slaves
                                       copying this slave output buffer
                                       should use. */
    char replrunid[CONFIG_RUN_ID_SIZE+1]; /* Master run id if is a master. */
    int slave_listening_port; /* As configured with: REPLCONF listening-port */
    char slave_ip[NET_IP_STR_LEN]; /* Optionally given by REPLCONF ip-address */
    int slave_capa;         /* Slave capabilities: SLAVE_CAPA_* bitwise OR. */
    multiState mstate;      /* MULTI/EXEC state */
    int btype;              /* Type of blocking op if CLIENT_BLOCKED. */
    blockingState bpop;     /* blocking state */
    long long woff;         /* Last write global replication offset. */
    list *watched_keys;     /* Keys WATCHED for MULTI/EXEC CAS */
    dict *pubsub_channels;  /* channels a client is interested in (SUBSCRIBE) */
    list *pubsub_patterns;  /* patterns a client is interested in (SUBSCRIBE) */
    sds peerid;             /* Cached peer ID . */

    /* Response buffer */
    int bufpos;
    char buf[PROTO_REPLY_CHUNK_BYTES];
} client;
```
client对象，注释非常全，看注释可以把功能了解各大概~具体等到用时再细说。  

只说一个没有备注的字段obuf_soft_limit_reached_time
redis server对output boffer的大小有限定，具体分为hard limit 和soft limit，如果超过hard limit的大小链接会被立刻终止，超过soft limit并且持续一定的秒数链接才会被终止，具体可以到redis的conf文件中查看。  
而networking.c文件中的checkClientOutputBufferLimits方法会定期检查softlimit 和hardlimit，obuf_soft_limit_reached_time会记录第一次memory use达到softlimit的时间，如果后续检查内存使用还是高于softlimit并且时间超过了softlimit的允许时间，就会引发连接中断。
```
typedef void redisCommandProc(client *c);
typedef int *redisGetKeysProc(struct redisCommand *cmd, robj **argv, int argc, int *numkeys);
struct redisCommand {
    char *name;  /* 命令名称 */
    redisCommandProc *proc; /* 执行函数 */
    int arity;
    char *sflags; /* Flags as string representation, one char per flag. */
    int flags;    /* The actual flags, obtained from the 'sflags' field. */
    /* Use a function to determine keys arguments in a command line.
     * Used for Redis Cluster redirect. */
    redisGetKeysProc *getkeys_proc;
    /* What keys should be loaded in background when calling this command? */
    int firstkey; /* The first argument that's a key (0 = no keys) */
    int lastkey;  /* The last argument that's a key */
    int keystep;  /* The step between first and last key */
    long long microseconds, calls;
};
```
注释较全，补充几个。
{% table %}
|字段|含义|
|---|:---:|
|arity|表示命令参数数量限定，3表示三个参数，-3表示大于等于三个参数|
|microseconds|该redisCommand的总执行时间|
|calls|该redisCommand的执行次数|
{% endtable %}
microseconds/calls为平均执行时间，可以用作统计数据

## API
Database相关实现在db.c文件中，声明在server.h中，主要包含了一下几类的API
1. C level Database API
2. Hooks for space changes
3. DB command 实现
4. key失效控制
5. API to get key arguments from commands  

1提供了基本的增删改查操作，2每次进行相应变更时调用（signal database modified，signal db flush），3是一些database操作命令的具体实现，4是expires相关功能实现，5实现了从各种命令中抽取key。
### C level Database API

### Hooks for space changes
### DB command 实现
### key失效控制
### API to get key arguments from commands