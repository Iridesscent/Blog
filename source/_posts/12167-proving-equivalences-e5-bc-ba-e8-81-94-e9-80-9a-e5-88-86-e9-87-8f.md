---
title: 12167 - Proving Equivalences(强联通分量)
tags:
  - Strongly Connected Componenet
id: 144
categories:
  - Algorithm
date: 2016-03-01 11:17:45
---

**题目链接：**[UVA12167 - Proving Equivalences](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=545&amp;problem=3319&amp;mosmsg=Submission+received+with+ID+16926611)

**题目大意：**给出一个证明关系，问至少再添加多少个证明就能完成整个证明？

**解题思路：**完成整个证明和整个图是一个SCC（强连通分量）是等价的，而且在每个SCC中都是无需添加新的证明的，这样我们可以先求出所有的强连通分量，将每个SCC看成一个点，这样就得到了一个新的DAG，设DAG中入度为0的点有a个，初度为0得点有b个，答案就是max{a, b}。特判当初始图是SCC时，整个图缩为一个点此时答案是0而不是1。

自己想的ans=max{a, b}的一点证明，可能不严谨，不过说的通。

1.  SCC中所有的点入度初度都不为0
2.  因此我们要在得到的DAG中建立适当的边，数量尽量少，使得DAG中得点入度初度都不为零
3.  因此建立的边数=max{a, b}，因为从一个初度为零的点连出一条边到一个入度为零得点，这样a和b每次都减一，a，b有一个为零的时候再从剩下的点各连max{a - min{a, b}, b - min{a, b}}条边就好了。
**AC代码：**
<pre class="lang:c++ decode:true " title="UVA12167 - Proving Equivalences">#include&lt;cstdio&gt;
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
const int MAXN = 20000 + 10;

vector&lt;int&gt; G[MAXN];
int pre[MAXN], lowlink[MAXN], sccno[MAXN], dfs_clock, scc_cnt;
stack&lt;int&gt; s;

void dfs(int u) {
    pre[u] = lowlink[u] = ++dfs_clock;
    s.push(u);
    for(int i = 0 ; i &lt; (int)G[u].size() ; i++) {
        int v = G[u][i];
        if(!pre[v]) {
            dfs(v);
            lowlink[u] = min(lowlink[v], lowlink[u]);
        } else if(!sccno[v]) {
            lowlink[u] = min(lowlink[u], pre[v]);
        }
    }
    if(lowlink[u] == pre[u]) {
        scc_cnt++;
        for(;;) {
            int x = s.top();
            s.pop();
            sccno[x] = scc_cnt;
            if(x == u) break;
        }
    }
}

void find_scc(int n) {
    dfs_clock = scc_cnt = 0;
    memset(sccno, 0, sizeof sccno);
    memset(pre, 0, sizeof pre);
    for(int i = 0 ; i &lt; n ; i++)
        if(!pre[i]) dfs(i);
}

int in[MAXN], out[MAXN];
int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int n, m;
        scanf("%d%d", &amp;n, &amp;m);

        memset(in, 0, sizeof in);
        memset(out, 0, sizeof out);
        for(int i = 0 ; i &lt;= n ; i++) {
            G[i].clear();
        }
        for(int i = 0 ; i &lt; m ; i++) {
            int u, v;
            scanf("%d%d", &amp;u, &amp;v);
            u--; v--;
            G[u].push_back(v);
        }
        find_scc(n);

        for(int i = 0 ; i &lt; n ; i++) {
            for(int j = 0 ; j &lt; (int)G[i].size() ; j++) {
                int u = sccno[i] - 1;
                int v = sccno[G[i][j]] - 1;
                if(u != v) {
                    in[v]++;
                    out[u]++;
                }
            }
        }
        int ans1 = 0, ans2 = 0;
        for(int i = 0 ; i &lt; scc_cnt ; i++) {
            if(in[i] == 0) ans1++;
            if(out[i] == 0) ans2++;
        }
        if(scc_cnt == 1) ans1 = ans2 = 0;
        printf("%d\n", max(ans1,  ans2));
    }
    return 0;
}
</pre>
&nbsp;