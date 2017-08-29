---
title: UVA1108 - Mining Your Own Business（双连通分量）
tags:
  - Biconnected Compoent
  - Graph theory
id: 142
categories:
  - Algorithm
date: 2016-03-01 09:38:15
---

**题目链接：**[UVA1108 - Mining Your Own Business](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;category=545&amp;page=show_problem&amp;problem=3549)

**题目大意：**给出一个没有重边的无向图，保证连通。让你选择最少的点染成黑色，保证任意删除一个点后，图中所有的点都可以到达一黑点，输出选点数和方案数。

**解题思路：  **

*   由于无向图连通，因此所有的店都在某个双联通分量上，并且有些是割点。
*   任意删除一个点后，保证每个联通分量上都有一个黑点
*   选择割点是没有意义的，因为如果选择割点，删除割点后，新增加的每一个连通分量上都要有一个黑点，这样黑点的存在没有意义
*   一个双联通分量上放置两个及以上黑点没有意义
这样题目就转化成了选择一些双连通分量，每个双连通分量选一个点。

如果一个双连通分量有两个及以上割点的话，没有必要放置黑点，因为总能有一条路通向别的双连通分量。

这样题目就转化成了求有多少只有一个割点的个双联通分量，选点的时候任意选择非割点的点就行。

特殊情况：整个图是一个双连通分量的时候，选两个点n(n - 1) / 2种选法（n为点数）。

**AC代码：**

由于不知道点编号是否连续，加了个离散化，可能不加也可以
<pre class="lang:c++ decode:true " title="UVA1108 - Mining Your Own Business">#include&lt;cstdio&gt;
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
#include&lt;assert.h&gt;
#define FS first
#define SE second
#define lson (o &lt;&lt; 1), L, M
#define rson (o &lt;&lt; 1) | 1, M + 1, R

using namespace std;

typedef long long LL;

const int INF = 0x3f3f3f3f;
const double eps = 1e-7;
const int MAXN = 50000 + 10;

struct Edge {
    int u, v;
    Edge(int u = 0, int v = 0) : u(u), v(v) {}
} edges[MAXN];

vector&lt;int&gt; G[MAXN], bcc[MAXN];
stack&lt;Edge&gt; s;

int pre[MAXN], iscut[MAXN], bccno[MAXN], dfs_clock, bcc_cnt;
int num[MAXN];

int b_Serach(int l, int r, int v) {
    while(l &lt; r) {
        int m = (l + r) &gt;&gt; 1;
        if(num[m] == v) return m;
        if(num[m] &gt; v) r = m;
        else l = m + 1;
    }
    return -1;
}

int dfs(int u, int fa) {
    int lowu = pre[u] = ++dfs_clock;
    int child = 0;
    for(int i = 0 ; i &lt; (int)G[u].size() ; i++) {
        int v = G[u][i];
        Edge e = Edge(u, v);
        if(!pre[v]) {
            s.push(e);
            child++;
            int lowv = dfs(v, u);
            lowu = min(lowu, lowv);
            if(lowv &gt;= pre[u]) {
                iscut[u] = true;
                bcc_cnt++; bcc[bcc_cnt].clear();
                for(;;) {
                    Edge x = s.top(); s.pop();
                    if(bccno[x.u] != bcc_cnt) {
                        bcc[bcc_cnt].push_back(x.u);
                        bccno[x.u] = bcc_cnt;
                    }
                    if(bccno[x.v] != bcc_cnt) {
                        bcc[bcc_cnt].push_back(x.v);
                        bccno[x.v] = bcc_cnt;
                    }
                    if(x.u == u &amp;&amp; x.v == v) break;
                }
            }
        }
        else if(pre[v] &lt; pre[u] &amp;&amp; v != fa) {
            s.push(e);
            lowu = min(lowu, pre[v]);
        }
    }
    if(fa &lt; 0 &amp;&amp; child == 1) iscut[u] = 0;
    return lowu;
}

void find_bcc(int n) {
    memset(pre, 0, sizeof pre);
    memset(iscut, 0, sizeof iscut);
    memset(bccno, 0, sizeof bccno);
    dfs_clock = bcc_cnt = 0;
    for(int i = 0 ; i &lt; n ; i++) {
        if(!pre[i]) dfs(i, -1);
    }
}

int main() {
    int n;
    int cases = 1;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        for(int i = 0 ; i &lt; n + 1 ; i++) G[i].clear();
        int cnt = 0;
        for(int i = 0 ; i &lt; n ; i++) {
            scanf("%d%d", &amp;edges[i].u, &amp;edges[i].v);
            num[cnt++] = edges[i].u;
            num[cnt++] = edges[i].v;
        }
        sort(num, num + cnt);
        cnt = unique(num, num + cnt) - num;
        for(int i = 0 ; i &lt; n ; i++) {
            edges[i].u = b_Serach(0, cnt, edges[i].u);
            edges[i].v = b_Serach(0, cnt, edges[i].v);
            G[edges[i].u].push_back(edges[i].v);
            G[edges[i].v].push_back(edges[i].u);
        }
        find_bcc(n);

        LL ans = 0, ansnum = 1;
        if(bcc_cnt == 1) {
            ansnum = LL(cnt) * LL(cnt - 1) / 2LL;
            ans = 2;
        }
        else {
            for(int i = 1 ; i &lt;= bcc_cnt ; i++) {
                int tmp = 0;
                for(int j = 0 ; j &lt; (int)bcc[i].size() ; j++)
                    if(iscut[bcc[i][j]]) tmp++;
                if(tmp == 1) {
                    ans++;
                    ansnum *= LL(bcc[i].size()) - 1;
                }
            }
        }
        printf("Case %d: %lld %lld\n", cases++, ans, ansnum);
    }
    return 0;
}
</pre>
&nbsp;