---
title: UVA11825 - Hackers' Crackdown（状压dp）
id: 68
categories:
  - Algorithm
tags:
  - Dynamic programing
date: 2016-02-19 15:12:52
---

**题目链接：**[UVA11825 - Hackers' Crackdown](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=464&amp;problem=2925&amp;mosmsg=Submission+received+with+ID+16870101)

**题目大意：**给出n台电脑，一共有n种服务，每台电脑上都运行着所有的服务，给出电脑之间的联通情况，在每台电脑上可以选择一种服务，则这台电脑及和这台电脑**直接/间接**相连的电脑该服务都会停止。问：最多停止多少服务（没有任何电脑运行一个服务，则该服务停止）

**解题思路：**

*   不同服务之间是等价的，所以我们可以只考虑服务数量，不关心具体那个服务
*   要停止一项服务，就要选择x台电脑，使这x太电脑可以覆盖到所有电脑，且x应尽可能小（这样才能停止更多的服务），在这x台选中的电脑上都停止该服务。
*   每一个服务都应该有一个数量尽量少的并且能覆盖所有电脑的电脑集合S停止
这样题目就转化成了，将所有电脑的全集S划分成尽可能多的子集{S<sub>1</sub>, S<sub>2</sub>, .... ,S<sub>n</sub>}，并且所有的子集都要覆盖全部电脑，划分数即为答案

则状态转移方程为
<pre class="lang:default decode:true ">dp[S] = max{dp[S], dp[S - S0] + 1}</pre>
S0为S的子集，且S0可以覆盖所有电脑，S-S0代表差集

&nbsp;

小技巧：枚举S的子集S0
<pre class="lang:c++ decode:true">for(int S0 = S ; S ; S0 = (S0 - 1) &amp; S)</pre>
**AC代码：**
<pre class="lang:c++ decode:true " title="11825 - Hackers' Crackdown">#include&lt;cstdio&gt;
#include&lt;iostream&gt;
#include&lt;cstring&gt;
#include&lt;string&gt;
#include&lt;algorithm&gt;
#include&lt;vector&gt;
#include&lt;string&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#include&lt;assert.h&gt;
#define lson (o &lt;&lt; 1), L, M
#define rson (o &lt;&lt; 1) | 1, M + 1, R

using namespace std;

const int INF = 0x3f3f3f3f;
const int MAXN = 17;
const double eps = 1e-7;

int G[MAXN];
int Covers[1 &lt;&lt; MAXN];
int dp[1 &lt;&lt; MAXN];

int main() {
    //freopen("in.txt", "r", stdin);
    //freopen("out.txt", "w", stdout);

    int n;
    int cases(1);
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        for(int i = 0 ; i &lt; n ; i++) G[i] = (1 &lt;&lt; i);
        memset(dp, 0, sizeof dp);
        for(int i = 0 ; i &lt; n ; i++) {
            int m;
            scanf("%d", &amp;m);
            for(int j = 0 ; j &lt; m ; j++) {
                int t;
                scanf("%d", &amp;t);
                G[i] |= (1 &lt;&lt; t);
            }
        }

        for(int mask = 1 ; mask &lt; (1 &lt;&lt; n) ; mask++) {
            int S = 0;
            for(int i = 0 ; i &lt; n ; i++)
                if(mask &amp; (1 &lt;&lt; i)) S |= G[i];
            Covers[mask] = S;
        }

        int ALL = (1 &lt;&lt; n) - 1;
        for(int mask = 1 ; mask &lt; (1 &lt;&lt; n) ; mask++) {
            if(Covers[mask] == ALL) dp[mask] = 1;

            for(int S = mask ; S ; S = (S - 1) &amp; mask)
                if(Covers[S] == ALL) dp[mask] = max(dp[mask], dp[mask ^ S] + 1);
            //printf("%d!!\n", dp[mask]);
        }

        printf("Case %d: %d\n", cases++, dp[ALL]);
    }
    return 0;
}
</pre>
&nbsp;