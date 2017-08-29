---
title: hdu 5709(主席树)
id: 266
tags:
  - ICPC/Data structure
  - Persistent data structure
date: 2016-08-04 22:38:17
categories:
  - Algorithm
---

给出一个树，每个点有一种颜色，q个强制在线查询x,d要求输出x的子树中和x的深度差小于等于d的点有多少种颜色

&nbsp;

每个点建立两棵线段树，T1[x]的[l, r]区间和表示x的子树内有多少深度在[l, r]之间的颜色，T2[x]维护x的子树中每种颜色的最小深度

初始每个线段树的初值初始化为一条链，就是对应节点的信息，这样自底向上合并线段树，先合并T1，再合并T2，合并T2的时候如果有重复点就在T1中删除深度大的哪一个，并维护深度最小值，这样对于查询x, d就是T1[x] 中区间[d[x], min(n, d[x] + d)]的区间和。

orz，主席树真是神一样的数据结构。。。

AC代码：
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;algorithm&gt;
#include&lt;string&gt;
#include&lt;queue&gt;
#include&lt;vector&gt;
#include&lt;set&gt;
#include&lt;vector&gt;
#include&lt;map&gt;
#define FS first
#define SE second
using namespace std;
typedef long long LL;
typedef pair&lt;int, int&gt; PII;
const int MAXN = 100000 + 10;
const int M = MAXN * 100;
int T1[MAXN], T2[MAXN], lson[M], rson[M], C[M];
int tot;

int update(int root, int pos, int val, int l, int r) {
    int newroot = tot++;
    int tmp = newroot;
    C[newroot] = C[root] + val;
    while(l &lt; r) {
        int mid = (l + r) &gt;&gt; 1;
        if(pos &lt;= mid) {
            lson[newroot] = tot++;
            rson[newroot] = rson[root];
            newroot = lson[newroot];
            root = lson[root];
            r = mid;
        } else {
            rson[newroot] = tot++;
            lson[newroot] = lson[root];
            newroot = rson[newroot];
            root = rson[root];
            l = mid + 1;
        }
        C[newroot] = C[root] + val;
    }
    return tmp;
}

int n, q;

int merge1(int rt1, int rt2, int l, int r) {
    if(rt1 == 0 || rt2 == 0) return rt1 | rt2;
    int newroot = tot++;
    C[newroot] = C[rt1] + C[rt2];
    if(l == r) return newroot;
    int mid = (l + r) &gt;&gt; 1;
    lson[newroot] = merge1(lson[rt1], lson[rt2], l, mid);
    rson[newroot] = merge1(rson[rt1], rson[rt2], mid + 1, r);
    return newroot;
}

int merge2(int rt1, int rt2, int l, int r, int p) {
    if(rt1 == 0 || rt2 == 0) return rt1 | rt2;
    int newroot = tot++;
    if(l == r) {
        if(C[rt1] &lt; C[rt2]) {
            T1[p] = update(T1[p], C[rt2], -1, 1, n);
            C[newroot] = C[rt1];
        } else {
            T1[p] = update(T1[p], C[rt1], -1, 1, n);
            C[newroot] = C[rt2];
        }
        return newroot;
    }
    int mid = (l + r) &gt;&gt; 1;
    lson[newroot] = merge2(lson[rt1], lson[rt2], l, mid, p);
    rson[newroot] = merge2(rson[rt1], rson[rt2], mid + 1, r, p);
    return newroot;
}

int query(int l, int r, int o, int L, int R) {
    if(o == 0) return 0;
    if(l &lt;= L &amp;&amp; r &gt;= R) return C[o];

    int M = L + (R - L) / 2;
    int res = 0;
    if(l &lt;= M) res += query(l, r, lson[o], L, M);
    if(r &gt; M) res += query(l, r, rson[o], M + 1, R);
    return res;
};

int fa[MAXN];
int col[MAXN];
int d[MAXN];
int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        scanf("%d%d", &amp;n, &amp;q);
        fa[1] = 1;
        d[1] = 0;
        tot = 1;
        for(int i = 1 ; i &lt;= n ; i++)
            scanf("%d", &amp;col[i]);
        for(int i = 2 ; i &lt;= n ; i++)
            scanf("%d", &amp;fa[i]);
        for(int i = 1 ; i &lt;= n ; i++) {
            d[i] = d[fa[i]] + 1;
        }

        for(int i = 1 ; i &lt;= n ; i++) {
            T1[i] = update(0, d[i], 1, 1, n);
            T2[i] = update(0, col[i], d[i], 1, n);
        }

        for(int i = n ; i &gt;= 1 ; i--) {
            T1[fa[i]] = merge1(T1[fa[i]], T1[i], 1, n);
            T2[fa[i]] = merge2(T2[fa[i]], T2[i], 1, n, fa[i]);
        }

        int last = 0;
        int dd, x;
        while(q--) {
            scanf("%d%d", &amp;x, &amp;dd);
            dd ^= last;
            x ^= last;
            printf("%d\n", last = query(d[x], min(d[x] + dd, n), T1[x], 1, n));
        }
    }
    return 0;
}
</pre>
&nbsp;