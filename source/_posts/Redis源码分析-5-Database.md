---
title: Redis源码分析(5)-Database
date: 2017-12-03 22:23:52
tags:
  - Redis
categories
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
## 数据定义

## API