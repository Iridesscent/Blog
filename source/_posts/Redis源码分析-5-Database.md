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
    sds peerid;             /* Cached peer ID. */

    /* Response buffer */
    int bufpos;
    char buf[PROTO_REPLY_CHUNK_BYTES];
} client;
```
## 数据定义

## API