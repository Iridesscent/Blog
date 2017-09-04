---
title: Redis源码分析-Dictionary
date: 2017-09-04 20:24:30
tags:
  - Redis
categories:
  - Redis源码分析
---
# 总体介绍
dict为Redis内置的字典结构，可以容纳任意类型的key和value（通过void指针实现）。和普通的哈希表相比，显著优势在于字典容量巨大的时候，普通字典的一次扩容可能有巨大的耗时，也许会让redis停止响应几秒钟甚至更长，而redis的dict的渐进式rehash则避免了这一情况。
# 数据定义
```
// 字典Entry
typedef struct dictEntry {
    void *key;
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;
    struct dictEntry *next; // 所有hash相同的的Entry被组织成一条链表
} dictEntry;
// 标示字典类型
typedef struct dictType {
    unsigned int (*hashFunction)(const void *key);
    void *(*keyDup)(void *privdata, const void *key);
    void *(*valDup)(void *privdata, const void *obj);
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);
    void (*keyDestructor)(void *privdata, void *key);
    void (*valDestructor)(void *privdata, void *obj);
} dictType;

/* This is our hash table structure. Every dictionary has two of this as we
 * implement incremental rehashing, for the old to the new table. */
typedef struct dictht {
    dictEntry **table; // hash table
    unsigned long size;  // hash table 容量
    unsigned long sizemask; // mask
    unsigned long used;  // hash table里Entity数量
} dictht;

typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2]; // 两个hashtable以便渐进式rehash
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    int iterators; /* number of iterators currently running */
} dict;
```
# 主要API
