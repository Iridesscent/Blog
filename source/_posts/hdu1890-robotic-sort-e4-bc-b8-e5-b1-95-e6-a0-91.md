---
title: HDU1890 Robotic Sort(伸展树)
id: 241
tags:
  - Data structure
  - Splay
date: 2016-05-18 17:50:01
tags:
  - Algorithm
---

题目链接：[HDU1890Robotic Sort](http://acm.hdu.edu.cn/showproblem.php?pid=1890)

每个节点维护最小值

每次先找到最小的那个节点是第几个，假设是第k个。

把第k-1个节点旋转到根(这里为了防止最小的节点是第一个造成re，在最前面设置了一个虚拟节点)

把最小节点删除，两边的序列拼接成一个新序列，重复n次就是答案

题目要求保持原有相对顺序，没想好在伸展树里能怎么搞，，索性伸展树里存值的时候存pair，第二个数表示这个数是第几次出现，，水过去了0.0

伸展树模板题，，还是挺费劲的，，太弱了。。

AC代码：
<pre class="lang:c++ decode:true ">#include &lt;cstdio&gt;
#include &lt;iostream&gt;
#include&lt;map&gt;
#include &lt;cstring&gt;
#include &lt;vector&gt;
#include &lt;algorithm&gt;
#define FS first
#define SE second
using namespace std;

const int MAXN = 100005;
const int INF = 0x3f3f3f3f;

struct Node {
    Node *ch[2];
    pair&lt;int, int&gt; v;
    int flip, s;
    pair&lt;int, int&gt; Min;
    Node() {
        ch[0] = ch[1] = NULL;
        s = flip = 0;
        v = make_pair(INF, INF);
        Min = make_pair(INF, INF);
    }

    int cmp(int k) {
        int d = k-ch[0]-&gt;s;
        if(d == 1) return -1;
        return d &lt;= 0 ? 0 : 1;
    }

    void pushup() {
        s = ch[0]-&gt;s+ch[1]-&gt;s+1;
        Min = min(v, min(ch[0]-&gt;Min, ch[1]-&gt;Min));
    }

    void pushdown() {
        if(flip) {
            flip = 0;
            swap(ch[0], ch[1]);
            ch[0]-&gt;flip = !ch[0]-&gt;flip;
            ch[1]-&gt;flip = !ch[1]-&gt;flip;
        }
    }
};

Node *null = new Node();

void rotate(Node *&amp;o, int d) {
    Node *k = o-&gt;ch[d^1]; o-&gt;ch[d^1] = k-&gt;ch[d]; k-&gt;ch[d] = o;
    o-&gt;pushup(); k-&gt;pushup(); o = k;
}

void splay(Node *&amp;o, int k) {
    o-&gt;pushdown();
    int d = o-&gt;cmp(k); ///根据某个值表示它为第k个元素
    if(d == 1) k -= o-&gt;ch[0]-&gt;s+1;
    if(d != -1) {
        splay(o-&gt;ch[d], k);
        rotate(o, d^1);
    }
}

int find(Node *&amp;o) {
    o-&gt;pushdown();
    if(o-&gt;ch[0]-&gt;Min &lt;= o-&gt;v &amp;&amp; o-&gt;ch[0]-&gt;Min &lt;= o-&gt;ch[1]-&gt;Min) {
        return find(o-&gt;ch[0]);
    } else if(o-&gt;v &gt; o-&gt;ch[1]-&gt;Min) {
        return find(o-&gt;ch[1]) + 1 + o-&gt;ch[0]-&gt;s;
    }
    return 1 + o-&gt;ch[0]-&gt;s;
}

///将left, right合并成一棵树
Node *merge(Node *left, Node *right) {
    splay(left, left-&gt;s); ///左树的最大值伸展到根部, 右节点肯定为空, 因为它是左树最大.
    left-&gt;ch[1] = right;
    left-&gt;pushup();
    return left;
}
//
/////将根为o的树, 划分为两棵子树, 并且前k个元素在left, 其余的在right
//void split(Node *o, int k, Node *&amp;left, Node *&amp;right) {
//    splay(o, k);
//    left = o;
//    right = o-&gt;ch[1];
//    o-&gt;ch[1] = null;
//    left-&gt;pushup();
//}

pair&lt;int, int&gt; num[MAXN];
struct splaysequence ///伸展树
{
    int n;
    Node seq[MAXN];
    Node *root;

    ///用1～n序列构建
    Node *build(int sz) {
        if(!sz) return null;
        Node *L = build(sz/2);
        Node *o = &amp;seq[++n];
        o-&gt;v = num[n - 1];
        o-&gt;Min = num[n - 1];
        o-&gt;ch[0] = L;
        o-&gt;ch[1] = build(sz-sz/2-1);
        o-&gt;flip = o-&gt;s = 0;
        o-&gt;pushup();
        return o;
    }

    ///初始化
    void init(int sz) {
        n = 0;
        null-&gt;s = 0;
        root = build(sz);
    }
};

int n, m;
splaysequence sp;
//void pr(Node *rt, int d = 0) {
//    if(rt-&gt;ch[0] != null) pr(rt-&gt;ch[0], d + 1);
//    printf("%d %d %d dis=%d\n", rt-&gt;Min, rt-&gt;v, rt-&gt;s, d);
//    if(rt-&gt;ch[1] != null) pr(rt-&gt;ch[1], d + 1);
//}
int main() {
///    freopen("input.txt", "r", stdin);
    int n;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        num[0] = make_pair(INF, INF);
        map&lt;int, int&gt; go;
        for(int i = 1 ; i &lt;= n ; i++) {
            scanf("%d", &amp;num[i].FS);
            num[i].SE = go[num[i].FS]++;
        }

        sp.init(n + 1);
        for(int i = 0 ; i &lt; n ; i++) {
//            if(i == n - 1) {
//                printf("--------\n");
//                pr(sp.root);
//            }
            Node *l, *r;
            int k = find(sp.root);//printf("-----------1\n");
            printf("%d%c", k + i - 1, i == n - 1?'\n':' ');//printf("-----------3 %d\n", k);
            splay(sp.root, k - 1);
            l = sp.root;
            r = sp.root-&gt;ch[1];
            sp.root-&gt;ch[1] = null;
            sp.root-&gt;pushup();
            splay(r, 1);
            r = r-&gt;ch[1];
            if(r != null) r-&gt;pushup();
            splay(l, 1);
            l-&gt;ch[1]-&gt;flip ^= 1;
            sp.root = merge(l, r);
        }
    }
    return 0;
}
</pre>
&nbsp;