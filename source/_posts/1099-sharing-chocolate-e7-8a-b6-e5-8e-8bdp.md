---
title: 1099 - Sharing Chocolate(状压dp)
id: 258
categories:
  - Algorithm
date: 2016-07-06 16:38:19
tags:
---

dp[mask][r] 代表一边长为r的方格能否用mask的二进制为对应的小块拼起来

设S0为mask的子集

则 dp[mask][r] = (dp[S0][r] || dp[S0][sum[S0] / r]) &amp;&amp; (dp[mask ^ S0][r] || dp[mask ^ S0][sum[mask ^ S0] / r])

&nbsp;

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
const int MAXN = 105;
const double eps= 1e-7;
int n;
int area[20];
bool dp[1 &lt;&lt; 15][MAXN];
int sum[1 &lt;&lt; 15];

inline int count_bits(int x){
    x = (x &amp; 0x55555555) + ((x &amp; 0xaaaaaaaa) &gt;&gt; 1);
    x = (x &amp; 0x33333333) + ((x &amp; 0xcccccccc) &gt;&gt; 2);
    x = (x &amp; 0x0f0f0f0f) + ((x &amp; 0xf0f0f0f0) &gt;&gt; 4);
    x = (x &amp; 0x00ff00ff) + ((x &amp; 0xff00ff00) &gt;&gt; 8);
    x = (x &amp; 0x0000ffff) + ((x &amp; 0xffff0000) &gt;&gt; 16);
    return x;
}

string con(int num, int n) {
    string res;
    while(num) {
        if(num &amp; 1) res += "1";
        else res += "0";
        num &gt;&gt;= 1;
    }
    while((int)res.length() &lt; n) res += "0";
    reverse(res.begin(), res.end());
    return res;
}

int main() {
    int ca = 1;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        memset(sum ,0, sizeof sum);
        memset(dp, 0, sizeof dp);
        int x, y;
        scanf("%d%d", &amp;x, &amp;y);
        for(int i = 0 ; i &lt; n ; i++) scanf("%d", &amp;area[i]);
        if(x &gt; y) swap(x, y);

        for(int mask = 1 ; mask &lt; (1 &lt;&lt; n) ; mask++) {
            for(int i = 0 ; i &lt; n ; i ++)
                if(mask &amp; (1 &lt;&lt; i)) sum[mask] += area[i];
        }

        if(x * y != sum[(1 &lt;&lt; n) - 1]) {
            printf("Case %d: No\n", ca++);
            continue;
        }

        for(int i = 0 ; i &lt; n ; i++) {
            for(int j = 1 ; j &lt;= max(x, y) ; j++)
                if(area[i] % j == 0 &amp;&amp; area[i] / j &lt;= max(x, y)) dp[1 &lt;&lt; i][j] = true;
        }

        for(int mask = 1 ; mask &lt; (1 &lt;&lt; n) ; mask ++) {
            if(count_bits(mask) &lt; 2) continue;
            for(int r = 1 ; r &lt;= max(x, y) ; r++)
                if(sum[mask] % r == 0 &amp;&amp; sum[mask] / r &lt;= max(x, y))
                    for(int S0 = mask ; S0 &amp;&amp; !dp[mask][r] ; S0 = (S0 - 1) &amp;  mask) {
                        //if(mask == 3 &amp;&amp; S0 == 1 &amp;&amp; r == 1) printf("%d %d %d\n", sum[mask], sum[mask ^ S0], sum[S0]);
                        if(sum[mask ^ S0] % r == 0 &amp;&amp; sum[S0] % r == 0 &amp;&amp; sum[mask ^ S0] / r &lt;= max(x, y) &amp;&amp; sum[S0] / r &lt;= max(x, y))
                            if((dp[S0][r] || dp[S0][sum[S0] / r]) &amp;&amp; (dp[mask ^ S0][r] || dp[mask ^ S0][sum[mask ^ S0] / r]))
                                dp[mask][r] = dp[mask][sum[mask] / r] = true;
                    }

        }

        if(dp[(1 &lt;&lt; n) - 1][x] || dp[(1 &lt;&lt; n) - 1][y]) printf("Case %d: Yes\n", ca++);
        else printf("Case %d: No\n", ca++);

    }
    return 0;
}

/*
4
2 2
1 1 1 1
*/
</pre>
&nbsp;