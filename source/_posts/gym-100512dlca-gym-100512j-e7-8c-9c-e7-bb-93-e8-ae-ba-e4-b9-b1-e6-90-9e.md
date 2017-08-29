---
title: 'Gym 100512D(LCA), Gym 100512J(猜结论乱搞)'
id: 289
tags:
  - Graph theory
  - LCA
date: 2016-10-02 23:08:27
categories:
  - Algorithm
---

100521D：

假定以1为根，O(nlogn)预处理lca，查询时分类讨论u, v的lca是不是在LCA(1, root)形成的链上，O(1)查询

AC代码：
<pre class="lang:c++ decode:true ">#include &lt;cstdio&gt;
#include &lt;algorithm&gt;
#include &lt;cstring&gt;
#include &lt;queue&gt;

using namespace std;
const int MAXN = 100000 + 10;

int rmq[MAXN &lt;&lt; 1];
struct ST {
    int mm[MAXN &lt;&lt; 1];
    int dp[MAXN &lt;&lt; 1][20];
    void init(int n) {
        mm[0] = -1;
        for(int i = 1 ; i &lt;= n ; i++) {
            mm[i] = ((i &amp; (i - 1)) == 0)?mm[i - 1] + 1:mm[i - 1];
            dp[i][0] = i;
        }
        for(int j = 1 ; j &lt;= mm[n] ; j++)
            for(int i = 1 ; i + (1 &lt;&lt; j) - 1 &lt;= n ; i++)
                dp[i][j] = rmq[dp[i][j - 1]] &lt; rmq[dp[i + (1 &lt;&lt; (j - 1))][j - 1]]? dp[i][j - 1] : dp[i + (1 &lt;&lt; (j - 1))][j - 1];
     }
    int query(int a, int b) {
        if(a &gt; b) swap(a, b);
        int k = mm[b - a + 1];
        return rmq[dp[a][k]] &lt;= rmq[dp[b - (1 &lt;&lt; k) + 1][k]]?dp[a][k]:dp[b - (1 &lt;&lt; k) + 1][k];
    }
};
int head[MAXN], tot;
int deep[MAXN];
struct Edge {
    int to, next;
} edge[MAXN &lt;&lt; 1];
void init() {
    memset(head, -1, sizeof head);
    tot = 0;
}
void addedge(int u, int v) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
}
int F[MAXN &lt;&lt; 1];
int P[MAXN];
int cnt;
ST st;
void dfs(int u, int pre, int dep) {
    deep[u] = dep;
    F[++cnt] = u;
    rmq[cnt] = dep;
    P[u] = cnt;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(v == pre) continue;
        dfs(v, u, dep + 1);
        F[++cnt] = u;
        rmq[cnt] = dep;
    }
}
void LCA_init(int root, int node_num) {
    cnt = 0;
    dfs(root, root, 0);
    st.init(node_num * 2 - 1);
}
int LCA(int u, int v) {
    return F[st.query(P[u], P[v])];
}
int main() {
    freopen("dynamic.in", "r", stdin);
    freopen("dynamic.out", "w", stdout);
    int n, q;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        init();
        int u, v;
        for(int i = 1 ; i &lt; n ; i++) {
            scanf("%d%d", &amp;u, &amp;v);
            addedge(u, v);
            addedge(v, u);
        }
        LCA_init(1, n);
        int root = 1;
        char op[5];
        scanf("%d", &amp;q);
        while(q--) {
            scanf("%s", op);
            if(op[0] == '?') {
                scanf("%d%d", &amp;u, &amp;v);
                if(root == 1) {
                    printf("%d\n", LCA(u, v));
                    continue;
                }
                int lca = LCA(u, v);
                if(lca != LCA(lca, root)) {
                    printf("%d\n", LCA(u, v));
                    continue;
                }

                if(LCA(root, u) == u &amp;&amp; LCA(root, v) == v) {
                    if(deep[u] &gt; deep[v]) printf("%d\n", u);
                    else printf("%d\n", v);
                    continue;
                }

                int lcau = LCA(root, u);
                int lcav = LCA(root, v);
                if(lcau == lca &amp;&amp; lcav == lca) {
                    printf("%d\n", lca);
                    continue;
                }
                if(lcau == lca) printf("%d\n", lcav);
                else printf("%d\n", lcau);
            }
            if(op[0] == '!') {
                scanf("%d", &amp;root);
            }
        }
    }
    return 0;
}</pre>
100512J：

要求答案至少为最优答案的一半，虽然不知道这有啥用，但打比赛时觉得一定有用

感觉这种题是一堆证明加一个简单的结论的，索性才个结论

首先答案只可能存在于入度为0的点中

每个入度为0的点进行一次dfs，vis数组标记，保证所有的dfs中每个点只被访问一次，维护num数组，表示点可达点的大概值，一次num不累加一次dfs中计算得到的num值，累加不在一次dfs中得到的num值，用一个dfs_clock可以实现

这样的得到的应该是可达点的一个下限(同一次dfs中的全部统计，其他dfs中的链合并得到的是点的下线)

AC代码：
<pre class="lang:c++ decode:true ">#include &lt;bits/stdc++.h&gt;
using namespace std;
const int MAXN = 100010;
const int MAXM = 200000 + 10;
int head[MAXN], tot;
int num[MAXN];
int vis[MAXN];
int in[MAXN];
int vis_clock;
struct Edge {
    int to, next;
} edge[MAXM];
void init() {
    memset(head, -1, sizeof head);
    memset(num, 0, sizeof num);
    memset(in, 0, sizeof in);
    memset(vis, 0, sizeof vis);
    tot = 0;
}
void addedge(int u, int v) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
}
void dfs(int u) {
    vis[u] = vis_clock;
    num[u] = 1;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(!vis[v]) {
            dfs(v);
            num[u] += num[v];
        } else if(vis[v] != vis_clock) num[u] += num[v];
    }
}
void dfs2(int u) {
    vis[u] = vis_clock;
    num[u] = 1;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(vis[v] != vis_clock) {
            dfs2(v);
            num[u] += num[v];
        }
    }
}
int main() {
    freopen("journey.in", "r", stdin);
    freopen("journey.out", "w", stdout);
    int n, m;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        scanf("%d", &amp;m);
        init();
        vis_clock = 1;
        for(int i = 0 ; i &lt; m ; i++) {
            int u, v;
            scanf("%d%d", &amp;u, &amp;v);
            addedge(u, v);
            in[v]++;
        }
        int ans = 1;
        int best = 0;
        for(int i = 1 ; i &lt;= n ; i++)
            if(in[i] == 0) {
                dfs(i);
                if(num[i] &gt; best) {
                    best = num[i];
                    ans = i;
                }
                vis_clock++;
            }
        memset(num, 0, sizeof num);
        dfs2(ans);
        printf("%d %d\n", ans, num[ans]);
    }
    return 0;
}</pre>
&nbsp;