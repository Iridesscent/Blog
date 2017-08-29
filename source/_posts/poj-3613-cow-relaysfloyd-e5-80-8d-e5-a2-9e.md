---
title: POJ 3613 - Cow Relays(floyd倍增)
id: 189
tags:
  - Graph theory
date: 2016-04-28 17:06:49
categories:
  - Algorithm
---

类似于矩阵快速幂的思路，只不过矩阵相乘变成了两个矩阵跑一下floyd

由于点的id可能比较大，先对点的标号离散化一下
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cstring&gt;
using namespace std;

typedef long long LL;
#define FS first
#define SE second
const int INF = 0x3f3f3f3f;
const int MAXN = 105;
const double eps = 1e-7;

pair&lt;int, int&gt; edge[MAXN];
int w[MAXN];
int Hash[MAXN &lt;&lt; 1];
int cnt;
struct Mat {
    int d[MAXN][MAXN];
};

Mat mul(Mat&amp; a, Mat&amp; b) {
    Mat res;
    memset(res.d, 0x3f, sizeof res.d);
    for(int k = 0 ; k &lt; MAXN ; k++)
        for(int i = 0 ; i &lt; MAXN ; i++)
            for(int j = 0 ; j &lt; MAXN ; j++)
                res.d[i][j] = min(res.d[i][j], a.d[i][k] + b.d[k][j]);
    return res;
}

Mat Pow(Mat a, int k) {
    Mat b = a;
    while(k) {
        if(k &amp; 1) a = mul(a, b);
        k &gt;&gt;= 1;
        b = mul(b, b);
    }
    return a;
}

int main() {
    int n, t, s, e;
    Mat be;
    memset(be.d, 0x3f, sizeof be.d);
    scanf("%d%d%d%d", &amp;n, &amp;t, &amp;s, &amp;e);
    for(int i = 0 ; i &lt; t ; i++) {
        scanf("%d%d%d", &amp;w[i], &amp;edge[i].FS, &amp;edge[i].SE);
        Hash[cnt++] = edge[i].FS;
        Hash[cnt++] = edge[i].SE;
    }
    sort(Hash, Hash + cnt);
    cnt = unique(Hash, Hash + cnt) - Hash;
    for(int i = 0 ; i &lt; t ; i++) {
        edge[i].FS = lower_bound(Hash, Hash + cnt, edge[i].FS) - Hash;
        edge[i].SE = lower_bound(Hash, Hash + cnt, edge[i].SE) - Hash;
        be.d[edge[i].FS][edge[i].SE] = be.d[edge[i].SE][edge[i].FS] = w[i];
    }
    s = lower_bound(Hash, Hash + cnt, s) - Hash;
    e = lower_bound(Hash, Hash + cnt, e) - Hash;
    Mat ans = Pow(be, n - 1);
    printf("%d\n", ans.d[s][e]);
    return 0;
}
</pre>
&nbsp;