---
title: Redis源码分析(2.5)-跳跃表
date: 2017-09-06 21:07:43
tags:
  - Redis
categories:
  - Redis源码分析
---
# 总体介绍
skiplist是一个高效的有序集合存储数据结构，有着优秀的时间复杂度和空间复杂度，见下表。
{% table %}
|指标|平均|最坏情况|
|---|:---:|----:|
|space|O(n)|O(nlogn) |
|Search|O(logn)|O(n)|
|Insert|O(logn)|O(n)|
|Delete|O(logn)|O(n)|
{% endtable %}
skiplist 是一个分层的链式数据结构，对于一个节点，每一层都会指向同层的下一个节点，如图所示。  
![skiplist](http://iridescent.com.cn/Reference/skiplist.png)  
而插入一个节点的时候，会以一个概率递减创建更高层。比如redis中这个概率是0.25，则插入新的节点时，有0.25的概率会创建两层节点，(0.25)^2的概率会创建三层的节点，以此类推。 
每一个节点的每一层都指向下一个同层的节点。当寻找一个节点的时候，会从链表首节点的最高层开始，如果指向的节点大于要寻找的key，就一直前进，如果当前层指向为null，或者指向的下一个节点小于key，就下降一层。  
而插入一个节点也是先找到第一个大于key的节点，再插入，动图如下。
![skiplist](http://iridescent.com.cn/Reference/skiplist_insert.gif)

在Redis中，skiplist添加了一个反向指针，每一个节点都会指向前一个相邻的节点，便于反向遍历。
# 源码分析
## 数据定义
Redis跳跃表和普通的跳跃表略有不同，数据定义如下。
```
typedef struct zskiplistNode {
    robj *obj; // 节点成员对象
    double score; // 节点分值，在Redis跳跃表中，各节点按照score从小到大排列。
    struct zskiplistNode *backward; // 反向指针
    struct zskiplistLevel {
        struct zskiplistNode *forward; // 下一个同层节点
        unsigned int span; // 跨度节点数
    } level[];
} zskiplistNode;

typedef struct zskiplist {
    struct zskiplistNode *header, *tail;
    unsigned long length; // 跳跃表长度
    int level; // 记录跳跃表内，层数最大的节点数，头结点不算在内。
} zskiplist;
```
Redis中的跳跃表如下图所示  
![](https://iridescent.com.cn/Reference/skiplist_redis.png)
## API
### create
```
zskiplistNode *zslCreateNode(int level, double score, robj *obj) {
    zskiplistNode *zn = zmalloc(sizeof(*zn)+level*sizeof(struct zskiplistLevel));
    zn->score = score;
    zn->obj = obj;
    return zn;
}

zskiplist *zslCreate(void) {
    int j;
    zskiplist *zsl;

    zsl = zmalloc(sizeof(*zsl));
    zsl->level = 1;
    zsl->length = 0;
    zsl->header = zslCreateNode(ZSKIPLIST_MAXLEVEL,0,NULL);
    for (j = 0; j < ZSKIPLIST_MAXLEVEL; j++) {
        zsl->header->level[j].forward = NULL;
        zsl->header->level[j].span = 0;
    }
    zsl->header->backward = NULL;
    zsl->tail = NULL;
    return zsl;
}
```
建立一个score为0的头结点，注意一下zskiplistNode和zskiplistLevel在内存中是连续存储的。
### Insert
```
/* Returns a random level for the new skiplist node we are going to create.
 * The return value of this function is between 1 and ZSKIPLIST_MAXLEVEL
 * (both inclusive), with a powerlaw-alike distribution where higher
 * levels are less likely to be returned. */
int zslRandomLevel(void) {
    int level = 1;
    while ((random()&0xFFFF) < (ZSKIPLIST_P * 0xFFFF))
        level += 1;
    return (level<ZSKIPLIST_MAXLEVEL) ? level : ZSKIPLIST_MAXLEVEL;
}

zskiplistNode *zslInsert(zskiplist *zsl, double score, robj *obj) {
    zskiplistNode *update[ZSKIPLIST_MAXLEVEL], *x;
    unsigned int rank[ZSKIPLIST_MAXLEVEL];
    int i, level;

    serverAssert(!isnan(score));
    x = zsl->header;
    /* 下面这个循环完成了两个数组的维护
     * update: 在上图所示插入过程中，每一个level所到达的最后一个节点 
     * rank: 每一个对应的update节点在跳跃表中的rank值
     * */
    for (i = zsl->level-1; i >= 0; i--) {
        /* store rank that is crossed to reach the insert position */
        rank[i] = i == (zsl->level-1) ? 0 : rank[i+1];
        while (x->level[i].forward &&
            (x->level[i].forward->score < score ||
                (x->level[i].forward->score == score &&
                compareStringObjects(x->level[i].forward->obj,obj) < 0))) {
            rank[i] += x->level[i].span;
            x = x->level[i].forward;
        }
        update[i] = x;
    }
    /* we assume the key is not already inside, since we allow duplicated
     * scores, and the re-insertion of score and redis object should never
     * happen since the caller of zslInsert() should test in the hash table
     * if the element is already inside or not. */
    level = zslRandomLevel();
    if (level > zsl->level) {
        for (i = zsl->level; i < level; i++) {
            rank[i] = 0;
            update[i] = zsl->header;
            update[i]->level[i].span = zsl->length;
        }
        zsl->level = level;
    }
    x = zslCreateNode(level,score,obj);
    for (i = 0; i < level; i++) {
        x->level[i].forward = update[i]->level[i].forward;
        update[i]->level[i].forward = x;

        /* update span covered by update[i] as x is inserted here */
        x->level[i].span = update[i]->level[i].span - (rank[0] - rank[i]); // rank[0]-rank[i] 为 update和x之间的跨度
        update[i]->level[i].span = (rank[0] - rank[i]) + 1;
    }

    /* increment span for untouched levels */
    for (i = level; i < zsl->level; i++) {
        update[i]->level[i].span++;
    }

    x->backward = (update[0] == zsl->header) ? NULL : update[0];
    if (x->level[0].forward)
        x->level[0].forward->backward = x;
    else
        zsl->tail = x;
    zsl->length++;
    return x;
}
```
### Delete
```
// 还是维护insert涉及到的那些东西，没什么可说的。。。
/* Internal function used by zslDelete, zslDeleteByScore and zslDeleteByRank */
void zslDeleteNode(zskiplist *zsl, zskiplistNode *x, zskiplistNode **update) {
    int i;
    for (i = 0; i < zsl->level; i++) {
        if (update[i]->level[i].forward == x) {
            update[i]->level[i].span += x->level[i].span - 1;
            update[i]->level[i].forward = x->level[i].forward;
        } else {
            update[i]->level[i].span -= 1;
        }
    }
    if (x->level[0].forward) {
        x->level[0].forward->backward = x->backward;
    } else {
        zsl->tail = x->backward;
    }
    while(zsl->level > 1 && zsl->header->level[zsl->level-1].forward == NULL)
        zsl->level--;
    zsl->length--;
}
void zslFreeNode(zskiplistNode *node) {
    decrRefCount(node->obj);
    zfree(node);
}
/* Delete an element with matching score/object from the skiplist. */
int zslDelete(zskiplist *zsl, double score, robj *obj) {
    zskiplistNode *update[ZSKIPLIST_MAXLEVEL], *x;
    int i;

    x = zsl->header;
    for (i = zsl->level-1; i >= 0; i--) {
        while (x->level[i].forward &&
            (x->level[i].forward->score < score ||
                (x->level[i].forward->score == score &&
                compareStringObjects(x->level[i].forward->obj,obj) < 0)))
            x = x->level[i].forward;
        update[i] = x;
    }
    /* We may have multiple elements with the same score, what we need
     * is to find the element with both the right score and object. */
    x = x->level[0].forward;
    if (x && score == x->score && equalStringObjects(x->obj,obj)) {
        zslDeleteNode(zsl, x, update);
        zslFreeNode(x);
        return 1;
    }
    return 0; /* not found */
}
```
# 总结
跳跃表作为一个随机化的集合数据结构，有着优秀的特性，没有平衡树复杂的旋转操作，比平衡树占用了更小的空间（平均O(n)）非常适合在内存数据库中使用