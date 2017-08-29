---
title: UVA 1169 - Robotruck
id: 256
tags:
  - Dynamic programing
date: 2016-07-06 16:34:51
categories:
  - Algorithm
---

dp[i] 代表拿完前i个并回到起点所需最小路程

状态转移

dp[i] = min{dp[j] + dist(j + 1, i) + dist(0, j + 1) + dist(0, i) | sum(i, j) &lt;= c}

dist(i, j) 代表从i点以此走到j点要走的路程，sum(i, j) 代表i~j件的总重量。

状态方程等价于

dp[i] = min{dp[j] + dist(j + 1, i) + dist(0, j + 1) | sum(i, j) &lt;= c} + dist(0, i)

可以用线段树优化

时间复杂度O(nlogn)

AC代码：

<pre class="lang:c++ decode:true ">#pragma comment(linker, "/STACK:102400000,102400000")
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;algorithm&gt;
#include&lt;cstdlib&gt;
#include&lt;string&gt;
#include&lt;queue&gt;
#include&lt;vector&gt;
#include&lt;set&gt;
#include&lt;iomanip&gt;
#include&lt;vector&gt;
#include&lt;stack&gt;
#include&lt;map&gt;
#include&lt;iostream&gt;
#include&lt;cmath&gt;
#include&lt;assert.h&gt;
#include&lt;sstream&gt;
#define FS first
#define SE second
#define lson (o &lt;&lt; 1), L, M
#define rson ((o &lt;&lt; 1) | 1), M + 1, R
#define lc (o &lt;&lt; 1)
#define rc ((o &lt;&lt; 1) | 1)

using namespace std;
typedef long long LL;
const int MAXN = 100000 + 10;
struct Item {
    int x, y, w;
} it[MAXN];
LL dp[MAXN];
LL sum[MAXN];
LL addv[MAXN &lt;&lt; 2];
LL minv[MAXN &lt;&lt; 2];

void pushup(int o, int L, int R) {
    minv[o] = 0;
    if(R &gt; L) minv[o] = min(minv[lc], minv[rc]);
    if(addv[o]) minv[o] += addv[o];
}

void update(int l, int r, LL v, int o, int L, int R) {
    if(l &lt;= L &amp;&amp; r &gt;= R) {
        addv[o] += v;
    } else {
        int M = L + (R - L) / 2;
        if(l &lt;= M) update(l, r, v, lson);
        if(r &gt; M) update(l ,r, v, rson);
    }
    pushup(o, L, R);
}

LL query(int l, int r, LL add, int o, int L, int R) {
    if(l &lt;= L &amp;&amp; r &gt;= R) return minv[o] + add;

    int M = L + (R - L) / 2;
    LL res = 0x3f3f3f3f3f3f3f3fLL;
    if(l &lt;= M) res = min(res, query(l, r, add + addv[o], lson));
    if(r &gt; M) res = min(res, query(l, r, add + addv[o], rson));
    return res;
}

LL cal(int i) {
    return abs(it[i].x) + abs(it[i].y);
}

int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int c, n;
        scanf("%d%d", &amp;c, &amp;n);

        for(int i = 1 ; i &lt;= n ; i++) {
            scanf("%d%d%d", &amp;it[i].x, &amp;it[i].y, &amp;it[i].w);
            sum[i] = sum[i - 1] + it[i].w;
        }
        dp[1] = (cal(1) &lt;&lt; 1);

        if(n == 1) {
            printf("%lld\n", dp[1]);
            if(T &gt; 0) printf("\n");
            continue;
        }
        memset(addv, 0, sizeof addv);
        memset(minv, 0, sizeof minv);
        update(1, 1, dp[1] + cal(2), 1, 1, n);
        LL mi = cal(1);
        int pos = 1;
        for(int i = 2 ; i &lt;= n ; i++) {
            while(sum[i] - sum[pos - 1] &gt; c) pos++;
            if(i &gt;= 3) update(1, i - 2, abs(it[i].x - it[i - 1].x) + abs(it[i].y - it[i - 1].y), 1, 1, n);
            dp[i] = query(max(1, pos - 1), i - 1, 0, 1, 1, n) + cal(i);
            mi += (abs(it[i].x - it[i - 1].x) + abs(it[i].y - it[i - 1].y));
            if(pos == 1) dp[i] = min(dp[i], mi + cal(i));

            if(i != n) update(i, i, dp[i] + cal(i + 1), 1, 1, n);
        }
        printf("%lld\n", dp[n]);
        if(T &gt; 0) printf("\n");
    }
    return 0;
}

/*
9 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * X * 1 -
X = 1/2541865828329

9 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * 9 * X *
*/
</pre>
&nbsp;