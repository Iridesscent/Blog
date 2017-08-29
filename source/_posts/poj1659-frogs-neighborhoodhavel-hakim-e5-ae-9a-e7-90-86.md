---
title: POJ1659 Frogs' Neighborhood(Havel-Hakim定理)
id: 212
categories:
  - Algorithm
date: 2016-05-03 16:17:28
tags:
---

链接：[Frogs' Neighborhood](http://poj.org/problem?id=1659)

数据弱，0ms爆过去了

学习一下Havel-Hakim定理：

给出一个无向图中各点的度数，问是否可以构造一个图满足给出度数。

1.一个非负整数组成的有限序列如果是某个无向图的序列，则称该序列是可图的。

2.判定过程：

1.  对当前数列排序，使其呈非递增序列
2.  从第二个数开始对其后d[i]个数字减1，d[i]代表排序后第i个数的值
3.  然后删除第一个之后对剩下的数继续排序
4.  一直循环直到当前序列出现负数（即不是可图的情况）或者当前序列全为0 （可图）时退出。
AC代码，写的暴力：
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cstring&gt;
#include&lt;cstdlib&gt;
#include&lt;assert.h&gt;
#include&lt;cmath&gt;
#define FS first
#define SE second
using namespace std;

typedef long long LL;

const int INF = 0x3f3f3f3f;
const int MAXN = 13;
int G[MAXN][MAXN];
int x[MAXN];
vector&lt;int&gt; C[12];
inline int count_bits(int x){
    x = (x &amp; 0x55555555) + ((x &amp; 0xaaaaaaaa) &gt;&gt; 1);
    x = (x &amp; 0x33333333) + ((x &amp; 0xcccccccc) &gt;&gt; 2);
    x = (x &amp; 0x0f0f0f0f) + ((x &amp; 0xf0f0f0f0) &gt;&gt; 4);
    x = (x &amp; 0x00ff00ff) + ((x &amp; 0xff00ff00) &gt;&gt; 8);
    x = (x &amp; 0x0000ffff) + ((x &amp; 0xffff0000) &gt;&gt; 16);
    return x;
}
int n;
bool ok() {
    for(int i = 0 ; i &lt; n ; i++) if(x[i]) return false;
    return true;
}
bool dfs(int d) {
    if(ok()) return true;
    if(d == n) return false;
    if(x[d] == 0) return dfs(d + 1);
    for(int i = 0 ; i &lt; (int)C[x[d]].size() ; i++) {
        int mask = C[x[d]][i];
        if(mask &amp; (1 &lt;&lt; d)) continue;
        bool good = true;
        for(int j = 0 ; j &lt; 10 ; j++)
            if((mask &amp; (1 &lt;&lt; j)) &amp;&amp; x[j] == 0) {
                good = false;
                break;
            }
        if(!good) continue;
        for(int j = 0 ; j &lt; 10 ; j++) {
            if(mask &amp; (1 &lt;&lt; j)) {
                x[j]--;
                G[d][j] = G[j][d] = 1;
            }
        }

        int tmp = x[d];
        x[d] = 0;
        if(dfs(d + 1)) return true;
        x[d] = tmp;
        for(int j = 0 ; j &lt; 10 ; j++)
            if(mask &amp; (1 &lt;&lt; j)) {
                G[d][j] = G[j][d] = 0;
                x[j]++;
            }
    }
    return false;
}
int main() {
    int T;
    scanf("%d",  &amp;T);
    bool fir = true;
    while(T--) {
        memset(G, 0, sizeof G);
        scanf("%d", &amp;n);
        for(int i = 0 ; i &lt; n ; i++) scanf("%d", &amp;x[i]);
        for(int i = 0 ; i &lt; 11 ; i++) C[i].clear();
        for(int i = 1 ; i &lt; (1 &lt;&lt; n) ; i++)
            C[count_bits(i)].push_back(i);
        if(!fir) printf("\n");
        fir = false;
        if(!dfs(0)) printf("NO\n");
        else {
            printf("YES\n");
            for(int i = 0 ; i &lt; n ; i++) {
                for(int j = 0 ; j &lt; n ; j++)
                    printf("%d%c", G[i][j], j == n - 1?'\n':' ');
            }
        }
    }
    return 0;
}
</pre>
&nbsp;

&nbsp;

&nbsp;