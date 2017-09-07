---
title: Redis源码分析(1)-内存管理
date: 2017-08-31 20:40:53
categories:
  - Redis源码分析
tags:
  - Redis
---
## 总体介绍  
Redis内存管理的代码位于zmalloc.h和zmalloc.c中。

## API
```
#if defined(__ATOMIC_RELAXED)
#define update_zmalloc_stat_add(__n) __atomic_add_fetch(&used_memory, (__n), __ATOMIC_RELAXED)
#define update_zmalloc_stat_sub(__n) __atomic_sub_fetch(&used_memory, (__n), __ATOMIC_RELAXED)
#elif defined(HAVE_ATOMIC)
#define update_zmalloc_stat_add(__n) __sync_add_and_fetch(&used_memory, (__n))
#define update_zmalloc_stat_sub(__n) __sync_sub_and_fetch(&used_memory, (__n))
#else
#define update_zmalloc_stat_add(__n) do { \
    pthread_mutex_lock(&used_memory_mutex); \
    used_memory += (__n); \
    pthread_mutex_unlock(&used_memory_mutex); \
} while(0)

#define update_zmalloc_stat_sub(__n) do { \
    pthread_mutex_lock(&used_memory_mutex); \
    used_memory -= (__n); \
    pthread_mutex_unlock(&used_memory_mutex); \
} while(0)

#endif
```
update_zmalloc_stat_add 和 update_zmalloc_stat_sub两个宏实现了内存使用量的统计，由变量used_memory记录  
这里涉及到了两个宏__ATOMIC_RELAXED 和HAVE_ATOMIC 不同的宏定义会产生不同的结果  
1. 两个都没定义  
对used_memory变量的操作由一个互斥量进行同步。
2. 定义了HAVE_ATOMIC  
规定了用原子操作来修改used_memory
3. 定义了__ATOMIC_RELAXED
规定了用原子操作来修改used_memory 并规定了用 relax的内存序（内存序又是一个大的话题，有兴趣可以自行搜索相关资料）

```
#define update_zmalloc_stat_alloc(__n) do { \
    size_t _n = (__n); \
    if (_n&(sizeof(long)-1)) _n += sizeof(long)-(_n&(sizeof(long)-1)); \
    if (zmalloc_thread_safe) { \
        update_zmalloc_stat_add(_n); \
    } else { \
        used_memory += _n; \
    } \
} while(0)

#define update_zmalloc_stat_free(__n) do { \
    size_t _n = (__n); \
    if (_n&(sizeof(long)-1)) _n += sizeof(long)-(_n&(sizeof(long)-1)); \
    if (zmalloc_thread_safe) { \
        update_zmalloc_stat_sub(_n); \
    } else { \
        used_memory -= _n; \
    } \
} while(0)
```
这里涉及到了update_zmalloc_stat_alloc 和 update_zmalloc_stat_free两个宏，用来在zmalloc分配和释放内存的时候更新内存使用量，并加入了线程安全的考虑，_n为字节数。  
if (_n&(sizeof(long)-1)) _n += sizeof(long)-(_n&(sizeof(long)-1));这一行代码用途是为内存对齐考量。默认情况下G++编译器进行4字节的内存对齐，也就是说如果_n不是4字节，实际消耗内存就是大于_n的第一个4的倍数。


```
void *zmalloc(size_t size); 
void *zcalloc(size_t size);
void *zrealloc(void *ptr, size_t size);
void zfree(void *ptr);
char *zstrdup(const char *s);
```
几个api作用类似于malloc calloc realloc free strdup 系统调用，但又稍微有些不同。以zmalloc为例。
```
void *zmalloc(size_t size) {
    void *ptr = malloc(size+PREFIX_SIZE);

    if (!ptr) zmalloc_oom_handler(size);
#ifdef HAVE_MALLOC_SIZE
    update_zmalloc_stat_alloc(zmalloc_size(ptr));
    return ptr;
#else
    *((size_t*)ptr) = size;
    update_zmalloc_stat_alloc(size+PREFIX_SIZE);
    return (char*)ptr+PREFIX_SIZE;
#endif
}
```
zmalloc实现了malloc的功能，又有以下区别：
1. 实际分配内存数量为size+PREFIX_SIZE， PREFIX_SIZE定义了内存区prefix大小，也就是说每一段存储空间最前PREFIX_SIZE个字符记录了这段内存到底有多少个字节。
2. malloc失败的话就会执行out of memory 的handler
3. update_zmalloc_stat_alloc维护分配的内存大小。

zcalloc zrealloc zfree类似
```
char *zstrdup(const char *s) {
    size_t l = strlen(s)+1;
    char *p = zmalloc(l);

    memcpy(p,s,l);
    return p;
}
```
zstrdup 实现非常简单，不懂得可以去dynamic string一章看一下Redis内置string的结构。

有了上面的基础，下面的api更多的是进行一些功能上的实现，不再赘述。
```
size_t zmalloc_used_memory(void);  // 获取使用内存大小，并加入了线程安全/原子操作的考量。
void zmalloc_enable_thread_safeness(void); // 设置zmalloc操作为线程不安全
void zmalloc_set_oom_handler(void (*oom_handler)(size_t)); 设置 out of memory handler
float zmalloc_get_fragmentation_ratio(size_t rss);  // 得到内存碎片率
size_t zmalloc_get_rss(void);  // 在os层面获取内存使用
size_t zmalloc_get_private_dirty(void); // 未实现
size_t zmalloc_get_smap_bytes_by_field(char *field); // 获取/proc/self/smaps中某一字段的value
size_t zmalloc_get_memory_size(void); // 获取物理内存大小
void zlibc_free(void *ptr); // libc的原生free方法
```

## 总结
在我看来zmalloc 一共做了三方面的工作  
1. 统一内存分配API 对ptmalloc tcmalloc jemalloc等原生api封装为zmalloc
2. 平台无关封装，可以看到源码里有很多平台相关的条件编译选项
3. 内存运行信息统计，包括内存大小，内存碎片率等