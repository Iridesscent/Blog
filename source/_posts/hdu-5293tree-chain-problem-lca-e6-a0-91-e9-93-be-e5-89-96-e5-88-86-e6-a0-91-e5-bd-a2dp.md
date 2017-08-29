---
title: HDU 5293Tree chain problem (LCA+树链剖分+树形dp)
id: 220
tags:
  - Heavy-Light Decomposition
  - Data structure
  - Dynamic programing(dp)
  - Graph theory
  - LCA
date: 2016-05-05 15:52:55
categories:
  - Algorithm
---

树形dp

dp[i]为以i为根节点的权值最大和

维护sum[i]=∑dp[v] (v是i的儿子)

对于每个节点，有两种情况

*   不加入新链 即dp[u] = sun[u];
*   加入以u为LCA的新链 dp[u] = max(dp[u], sum[u]+∑(sum[v] - dp[v])) v是链上的点且u！= v
关键就是第二种转移方程，方程有了就可以用树链剖分加速dp了

而sum[u]+∑(sum[v] - dp[v])就恰好是和加入链相连的所有点的dp和（不包括加入链上的点），因为每一个sum[v]都在其子节点中被减去了链上的点。

AC代码，跑了1800ms，姿势一直挺差的。。
<pre class="lang:c++ decode:true">#pragma comment(linker, "/STACK:1024000000,1024000000")
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;algorithm&gt;
#include&lt;cstdlib&gt;
#include&lt;string&gt;
#include&lt;queue&gt;
#include&lt;vector&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#define FS first
#define SE second
#define lson (o &lt;&lt; 1), L, M
#define rson ((o &lt;&lt; 1) | 1), M + 1, R
#define lc (o &lt;&lt; 1)
#define rc ((o &lt;&lt; 1) | 1)

using namespace std;
const int INF = 0x3f3f3f3f;
const int MAXN = 100000 + 10;

const int DEG = 20;
struct Edge {
    int to, next;
} edge[MAXN &lt;&lt; 1];
struct Query {
    int u, v, w;
    Query(int u, int v, int w) : u(u), v(v), w(w) {}
};
vector&lt;Query&gt;  q[MAXN];
int head[MAXN], tot;
int fa[MAXN][DEG];
int deg[MAXN];
int n, m;
int top[MAXN];
int num[MAXN];
int p[MAXN];
int fp[MAXN];
int son[MAXN];
int pos;
int sumv[MAXN &lt;&lt; 2];
int dp[MAXN];
int sum[MAXN];

void addedge(int u, int v) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
}

void init() {
    memset(head, -1, sizeof head);
    for(int i = 0 ; i &lt;= n ; i++) q[i].clear();
    tot = 0;
    pos = 1;
    memset(son, -1, sizeof son);
    memset(sumv, 0, sizeof sumv);
}

void BFS(int root) {
    queue&lt;int&gt; Q;
    deg[root] = 0;
    fa[root][0] = root;
    Q.push(root);
    while(!Q.empty()) {
        int u = Q.front();
        Q.pop();
        for(int i = 1 ; i &lt; DEG ; i++)
            fa[u][i] = fa[fa[u][i - 1]][i - 1];
        for(int i = head[u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(v == fa[u][0]) continue;
            fa[v][0] = u;
            deg[v] = deg[u] + 1;
            Q.push(v);
        }
    }
}
int LCA(int u, int v) {
    if(deg[u] &gt; deg[v]) swap(u, v);
    int hu = deg[u], hv = deg[v];
    int tu = u, tv = v;
    for(int det = hv - hu, i = 0 ; det ; det &gt;&gt;= 1, i++)
        if(det &amp; 1)
            tv = fa[tv][i];
    if(tu == tv) return tu;
    for(int i = DEG - 1 ; i &gt;= 0 ; i--) {
        if(fa[tu][i] == fa[tv][i]) continue;
        tu = fa[tu][i];
        tv = fa[tv][i];
    }
    return fa[tu][0];
}
int query(int l, int r, int o, int L, int R) {
    if(L &gt;= l &amp;&amp; R &lt;= r) return sumv[o];
    int M = L + (R - L) / 2;
    int res = 0;
    if(l &lt;= M) res += query(l, r, lson);
    if(r &gt; M) res += query(l, r, rson);
    return res;
}
void update(int p, int v, int o, int L, int R) {
    if(L == R) {
        sumv[o] = v;
        return;
    }
    int M = L + (R - L) / 2;
    if(p &lt;= M) update(p, v, lson);
    else update(p, v, rson);
    sumv[o] = sumv[lc] + sumv[rc];
}
void dfs1(int u, int pre) {
    num[u] = 1;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(v != pre) {
            dfs1(v, u);
            num[u] += num[v];
            if(son[u] == -1 || num[v] &gt; num[son[u]])
                son[u] = v;
        }
    }
}
void dfs2(int u, int rt) {
    top[u] = rt;
    p[u] = pos++;
    fp[p[u]] = u;
    if(son[u] == -1) return;
    dfs2(son[u], rt);
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(v != son[u] &amp;&amp; v != fa[u][0])
            dfs2(v, v);
    }
}
int Sum(int u, int v) {
    int f1 = top[u];
    int f2 = top[v];
    int res = 0;
    while(f1 != f2) {
        if(deg[f1] &lt; deg[f2]) {
            swap(f1, f2);
            swap(u, v);
        }
        res += query(p[f1], p[u], 1, 1, pos - 1);
        u = fa[f1][0];
        f1 = top[u];
    }
    if(u == v) {
        res += query(p[u], p[u], 1, 1, pos - 1);
        return res;
    }
    if(deg[u] &gt; deg[v]) swap(u, v);
    res += query(p[u], p[v], 1, 1, pos - 1);
    return res;
}
int DP(int u) {
    sum[u] = 0;
    dp[u] = 0;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(v == fa[u][0]) continue;
        sum[u] += DP(v);
    }
    dp[u] = sum[u];
    for(int i = 0 ; i &lt; (int)q[u].size() ; i++) {
        dp[u] = max(dp[u], sum[u] + Sum(u, q[u][i].u) + Sum(u, q[u][i].v) + q[u][i].w);
    }
    update(p[u], sum[u] - dp[u], 1, 1, pos - 1);
    return dp[u];
}
int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        scanf("%d%d", &amp;n, &amp;m);
        init();
        for(int i = 0 ; i &lt; n - 1 ; i++) {
            int u, v;
            scanf("%d%d", &amp;u, &amp;v);
            addedge(u, v);
            addedge(v, u);
        }

        BFS(1);
        dfs1(1, 0);
        dfs2(1, 1);
        for(int i = 0 ; i &lt; m ; i++) {
            int u, v, dis;
            scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
            q[LCA(u, v)].push_back(Query(u, v, dis));
        }
        printf("%d\n", DP(1));
    }
    return 0;
}
</pre>
&nbsp;