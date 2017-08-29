---
title: UVA1160 - X-Plosives（并查集，好题）
categories:
  - Algorithm
id: 89
tags:
  - find-union set
  - Graph theory
date: 2016-02-20 18:46:05
---

**题目链接：[http://UVA1160 - X-Plosives](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=497&amp;problem=3601&amp;mosmsg=Submission+received+with+ID+16876020)**

**题目大意：**给出一个化学品装车序列，每个化学品由两种元素组成，如果车内存在一个化学品数大于2的组合，化学品数等于元素数就会爆炸，如果一个化学品装车会引起爆炸你就要拒绝装车，问要拒绝装车多少次

**解题思路：**把元素看做点，化学品看成两点间的边，化学品爆炸就等价于无向图里产生了环，用并查集可解。

**AC代码：**
<pre class="lang:c# decode:true " title="UVA1160 - X-Plosives">#include&lt;cstdio&gt;
#include&lt;iostream&gt;
#include&lt;cstring&gt;
#include&lt;string&gt;
#include&lt;algorithm&gt;
#include&lt;vector&gt;
#include&lt;stack&gt;
#include&lt;string&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#define lson (o &lt;&lt; 1), L, M
#define rson (o &lt;&lt; 1) | 1, M + 1, R

using namespace std;

const int INF = 0x3f3f3f3f;
const int MAXN = 100010;
const double eps = 1e-7;

int fa[MAXN];

int find(int p) {
    if(fa[p] == p) return p;
    return fa[p] = find(fa[p]);
}

int main() {
    int a, b;
    while(scanf("%d%d", &amp;a, &amp;b) == 2) {
        for(int i = 0 ; i &lt; MAXN ; i++) fa[i] = i;
        int cnt = 0;
        fa[a] = b;
        while(scanf("%d", &amp;a) == 1) {
            if(a == -1) break;
            scanf("%d", &amp;b);
            int u = find(a);
            int v = find(b);
            if(u == v) cnt++;
            else fa[u] = v;
        }
        printf("%d\n", cnt);
    }
    return 0;
}
</pre>
&nbsp;