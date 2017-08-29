---
title: 红黑树详解(未完待续)
id: 333
tags:
  - Data structure
date: 2017-02-15 22:02:52
caregories:
  - Algorithm
---

# 红黑树详解(未完待续)

最近在学红黑树，感觉这是一个比较复杂的数据结构，看起红黑树的资料来比较吃力。一来是实力有限，二来是找的资料要么是说的含糊不清，要么就是算导这样的书写的比较麻烦不好理解。于是决定写一下自己的理解，希望能对你学习红黑树有所帮助。　　

对于初学者而言，算法实现和证明相穿插反而会增大学习门槛。先了解怎么做，然后再去想为什么反而会更快的掌握一些相对复杂的数据结构，本文就是要解决怎么做的问题，详细算法证明请看算法导论等专业资料。

本文力用简洁的语言描述红黑树，省略了复杂度的证明部分，重点放在红黑树的实现原理细节，以及代码实现。

## 目录

1.  储备知识
2.  红黑树的定义
3.  旋转
4.  插入
5.  删除
6.  封装后的完整代码
7.  参考文献

## 储备知识

[二叉搜索树](https://en.wikipedia.org/wiki/Binary_search_tree) 二叉搜索树的定义，插入，删除都要了解，红黑树的每一种操作都是在起之上进行扩展

## 红黑树定义

红黑树也是一种二叉搜索树，而且要满足下列性质。　　　

1.  首先要满足二叉搜索树的性质
2.  所有节点不是红色就是黑色
3.  根节点是黑色的，叶子节点也是黑色的
4.  红色节点的子节点一定是黑色的
5.  对于每个节点，到任意叶子节点的路径上都有相同数目的黑色节点

注意：

1.  叶子节点不存储信息，用T.nil统一表示，叶子节点只是为了方便代码中的边界处理
2.  树节点(非叶子节点)存储信息，每个树节点都有两个儿子
3.  一个有ｎ个节点的红黑树高度最多是2lg(n+1) 证明略，有兴趣可以看《算法导论》第三版P174

## 旋转

规则同二叉搜索树

##插入 插入新节点，新节点一定是红色的。 首先，按照二叉搜索树的原则讲节点插入到相应位置，下面开始对红黑树性质进行维护。 设新插入的是n，n的父节点为p，兄弟节点为s,祖先节点为g，叔节点为u（如果存在的话）。 ####Case1 如果n是树根，则直接讲n染成黑色(性质３)

    void insert_case1(Node* n) {

    }`</pre>

    #### Case2

    如果p为黑色，则红黑树依旧合法（插入红色节点不会对任何性质产生影响）

    <pre>`void insert_case2(Node* n) {

    }`</pre>

    #### Case3

    现在n, p都为红色，如果u也是红色就进行接下来的调整，否则执行Case4

    现在p,u都是红色，则g一定存在(根节点为黑色，p不可能是根节点)并且一定是黑色(性质4)。我么可以吧g染成红色。p,u染成黑色，这样g虽然不是黑色了，但是所有通过ｇ的路径都要通过p或u这两个黑色节点，所以经过的黑色节点数依然不变。这样就把调整n这个点转化为调整g这个点，我们再递归调用insert_case1(g)

    ![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d6/Red-black_tree_insert_case_3.svg/800px-Red-black_tree_insert_case_3.svg.png)</img>

    <pre>`void insert_case3(Node* n) {

    }`</pre>

    #### Case4

    现在n,p是红色，u是黑色。 这种情况下，g一定存在(根节点为黑色，p不可能是根节点)并且是黑色(如果g是红色p是红色，这就违反了性质4)。

    如果n是g左儿子的右儿子或右儿子的左儿子，我们就执行下俩算法转化到Case5，否则就直接执行Case5 下面以n是g左儿子的右儿子为例，另一种情况翻转left和right就好了 仅仅在p上进行一次左旋，就可以转化到情况5

    ![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/89/Red-black_tree_insert_case_4.svg/774px-Red-black_tree_insert_case_4.svg.png)</img>

    <pre>`void insert_case4(Node* n) {

    }`</pre>

    #### Case5

    现在，让我们来处理最后一种情况。 n为ｇ的左儿子的左儿子或右儿子的右儿子 以左儿子的左儿子为例，另一种情况直接翻转left,right 我们在g上进行一次右旋，这样p到了g的位置，g到了u的位置，再交换p,g的颜色。这样每个点到叶子节点经过的黑色节点数依然不变。

    ![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/Red-black_tree_insert_case_5.svg/800px-Red-black_tree_insert_case_5.svg.png)</img>

    <pre>`void insert_case5(Node* n) {

    }`</pre>

    ## 删除

    ## 完整代码

    <pre>`#include &lt;cstdio&gt;
    #include &lt;algorithm&gt;
    using namespace std;

    template&lt;typename T&gt;
    class RedBlackTree {
        enum COLOR { RED, BLACK};

        typedef struct Node {
            Node() : color(BLACK), val(T()){ left = right = parent = nil; }
            Node(COLOR color, T val) : color(color), val(val) { left = right = parent = nil; }
            COLOR color;
            T val;
            Node* left, right, parent;
        } Node;

        Node *head;
        Node *nil;

        RedBlackTree&lt;T&gt;() {
            nil = new Node();
            nil-&gt;color = BLACK;
            nil-&gt;val = 0;
            head = nil;
        }

        ~RedBlackTree&lt;T&gt;() {
            delete nil;
        }

        Node* grandparent(Node* n) {
            if((n != nil) &amp;&amp; (n-&gt;parent != nil)) {
                return n-&gt;parent-&gt;parent;
            } else {
                return nil;
            }
        }
        Node* uncle(Node* n) {
            Node* g = grandparent(n);
            if(g == nil)
                return nil;
            if(n-&gt;parent == g-&gt;left)
                return g-&gt;right;
            else
                return g-&gt;left;
        }

        void rotate_left(Node* n) {
            if(n == nil || n-&gt;right == nil)  return;
            Node* p = n-&gt;right;
            n-&gt;left = p-&gt;left;
            if(p-&gt;left != nil) {
                p-&gt;left-&gt;parent = n;
                p-&gt;left = n;
            }
            p-&gt;parent = n-&gt;parent;
            n-&gt;parent = p;
            if(p-&gt;parent != nil) {
                if(n == p-&gt;parent-&gt;left) p-&gt;parent-&gt;left = p;
                else p-&gt;parent-&gt;right = p;
            }
        }
        void rotate_right(Node* n) {
            if(n == nil || n-&gt;left == nil)  return;
            Node* p = n-&gt;left;
            n-&gt;right = p-&gt;right;
            if(p-&gt;right != nil) {
                p-&gt;right-&gt;parent = n;
                p-&gt;right = n;
            }
            p-&gt;parent = n-&gt;parent;
            n-&gt;parent = p;
            if(p-&gt;parent != nil) {
                if(n == p-&gt;parent-&gt;left) p-&gt;parent-&gt;left = p;
                else p-&gt;parent-&gt;right = p;
            }
        }

        void insert_case1(Node* n) {
            if(n-&gt;parent == nil)
                n-&gt;color = BLACK;
            else insert_case2(n);
        }
        void insert_case2(Node* n) {
            if(n-&gt;parent.color == BLACK)
                return; /* 此时红黑树依然合法 */
            else
                insert_case3(n);
        }
        void insert_case3(Node* n) {
            Node* u = uncle(n), *g;
            if((u != nil) &amp;&amp; (u-&gt;color == RED)) {
                n-&gt;parent.color = BLACK;
                u-&gt;color = BLACK;
                g = grandparent(n);
                g-&gt;color = RED;
                insert_case1(g);
            } else {
                insert_case4(n);
            }
        }
        void insert_case4(Node* n) {
            Node* g = grandparent(n);
            if((n == n-&gt;parent-&gt;right) &amp;&amp; (n-&gt;parent == g-&gt;left)) {
                rotate_left(n-&gt;parent);
                n = n-&gt;left;
            } else if((n == n-&gt;parent-&gt;left) &amp;&amp; (n-&gt;parent == g-&gt;right)) {
                rotate_right(n-&gt;parent);
                n = n-&gt;right;
            }
            insert_case5(n);

        }
        void insert_case5(Node* n) {
            Node *g = grandparent(n);
            n-&gt;parent-&gt;colcr = BLACK;
            g-&gt;color = RED;
            if(n == n-&gt;parent-&gt;left)
                rotate_right(g);
            else
                rotate_left(g);
        }
    public:
        void Insert(T val) {
            Node* n = new Node(RED, val);
    //        if(head == nil) {
    //            head = n;
    //            insert_case1(n);
    //        }
            Node* p = head;
            Node* last = nil;
            while(p != nil) {
                last = p;
                if(n-&gt;val &gt;= p-&gt;val) p = p-&gt;left;
                else p = p-&gt;right;
            }
            // 树是空的
            if(last == nil) {
                head = n;
            } else {
                if(n-&gt;val &gt;= last-&gt;val) last-&gt;right = n;  //插入右子树
                else last-&gt;right = n;                     //插入左子树
                n-&gt;parent = last;
            }

            //对红黑树进行调整
            insert_case1(p);
        }
        void Delete(T val) {

        }

    };

    int main() {
        return 0;
    }

## 参考文献

[Red Black Tree](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)