---
title: UVA10859 - Placing Lampposts（树形dp）
categories:
  - Algorithm
id: 71
tags:
  - Dynamic programing
date: 2016-02-19 17:17:32
---

**题目链接：**[UVA10859 - Placing Lampposts](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=464&amp;problem=1800&amp;mosmsg=Submission+received+with+ID+16870685)

**题目大意：**给出一个无向无环图，要求选取尽量少的点建立路灯，要求用最少的路灯着凉所有的点，并且在点最少的情况下使尽量多的道路被两个路灯照亮。

**解题思路：**

*   树上的最优化问题，可用动态规划解决，对于每个点都有建立路灯和不建立路灯两种决策。
*   对于不同的联通块独立解决，任选一个作为树根，形成一个dfs树
*   设计最优化函数，我用的建立路灯数*1500+被一个路灯照亮的道路数*1，则该数值越小越优
dp0(i)表示以i作为树根，且i点不建立路灯的最优解，dp1(i)表示以i作为树根，且i点建立路灯的最优解

则状态转移可以表示为

dp0(i) = 1 + ∑dp1(j) (j为1的子节点)

dp1(i) = ∑min(dp1(j), dp0(j) + 1) (j为i的子节点)

最优化函数值为∑min(dp0(i), dp1(i))  i为各联通块的根节点，可以进一步得出答案

**AC代码：**
<pre class="lang:c++ decode:true " title="UVA10859 - Placing Lampposts">#include&lt;cstdio&gt;
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
const int MAXN = 1010;
const int MAGIC = 1500;
const double eps = 1e-7;

vector&lt;int&gt; G[MAXN];
int dp1[MAXN];
int dp0[MAXN];
bool vis[MAXN];
int DP0(int root, int fa);
int DP1(int root, int fa);

int DP0(int root, int fa) {
    if(dp0[root] != INF) return dp0[root];

    if(G[root].size() == 0) return dp0[root] = 0;
    if(G[root].size() == 1 &amp;&amp; G[root][0] == fa) return dp0[root] = 0;

    int res = 0;
    for(int i = 0 ; i &lt; (int)G[root].size() ; ++i)
        if(G[root][i] != fa)
            res += (DP1(G[root][i], root) + 1);
    return dp0[root] = res;
}

int DP1(int root, int fa) {
    if(dp1[root] != INF) return dp1[root];

    if(G[root].size() == 0) return dp1[root] = 0;
    if(G[root].size() == 1 &amp;&amp; G[root][0] == fa) return dp1[root] = MAGIC;

    int res = 0;
    for(int i = 0 ; i &lt; (int)G[root].size() ; ++i) {
        if(G[root][i] != fa)
            res += min(DP1(G[root][i], root), DP0(G[root][i], root) + 1);
    }
    return dp1[root] = (res + MAGIC);
}

void dfs(int root) {
    vis[root] = true;
    for(int i = 0 ; i &lt; (int)G[root].size() ; ++i)
        if(!vis[G[root][i]]) dfs(G[root][i]);
}
// 以root为树根评分的最小值
int slove(int root) {
    dfs(root);
    return min(DP0(root, -1), DP1(root, -1));
}

int main() {
    //freopen("in.txt", "r", stdin);
    //freopen("out.txt", "w", stdout);

    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int n, m;
        scanf("%d%d", &amp;n, &amp;m);
        for(int i = 0 ; i &lt; MAXN ; i++) G[i].clear();
        memset(dp1, 0x3f, sizeof dp1);
        memset(dp0, 0x3f, sizeof dp0);
        memset(vis, false, sizeof vis);

        for(int i = 0 ; i &lt; m ; i++) {
            int u, v;
            scanf("%d%d", &amp;u, &amp;v);
            G[u].push_back(v);
            G[v].push_back(u);
        }

        int ans = 0;
        for(int i = 0 ; i &lt; n ; i++)
            if(!vis[i]) {
                ans += slove(i);
            }
        printf("%d %d %d\n", ans / MAGIC, m - (ans %  MAGIC), ans % MAGIC);

    }
    return 0;
}
</pre>
&nbsp;