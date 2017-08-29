---
title: HDU5296 Annoying problem LCA
id: 218
tags:
  - LCA
date: 2016-05-04 22:41:43
categories:
  - Algorithm
---

题目链接：[Annoying problem](http://acm.hdu.edu.cn/showproblem.php?pid=5296)

很神奇的一道LCA

先做一个dfs序，以后所有的查询x都可以对应到u，v所在的链到查询点x的权重，对应加减就可以

u是S中小于u的dfs序的第一个点

v是S中大于u的dfs序的第一个点

如果u，v中有一个不存在，就把u，v分别换成dfs序最小和最大的两个点。

这样处理就非常神奇的完成了所有的题目要求。

每次影响的权重为dist[x] + dist[LCA(u, v)] - dist[LCA(x, u)] - dist[LCA(x, v)]

AC代码：
<pre class="lang:c++ decode:true ">#include&lt;iostream&gt;
#include&lt;stdio.h&gt;
#include&lt;set&gt;
#include&lt;algorithm&gt;
#include&lt;cmath&gt;
#include&lt;vector&gt;
#include&lt;queue&gt;
#include&lt;ctime&gt;
#include&lt;cstring&gt;
using namespace std;

typedef long long LL;
const int MAXN = 100000 + 10;
const int DEG = 20;
struct Edge {
    int to, next;
    int dis;
} edge[MAXN &lt;&lt; 1];
int head[MAXN], tot;
void addedge(int u, int v, int dis) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    edge[tot].dis = dis;
    head[u] = tot++;
}
void init() {
    memset(head, -1, sizeof head);
    tot = 0;
}
int fa[MAXN][DEG];
int deg[MAXN];
int dist[MAXN];
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
int dfn[MAXN];
int fdfn[MAXN];
int pos;
void dfs(int u, int fa) {
    dfn[u] = pos++;
    fdfn[dfn[u]] = u;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(v != fa) {
            dist[v] = dist[u] + edge[i].dis;
            dfs(v, u);
        }
    }
}
int main() {
    int ca = 1;
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int n, q;
        scanf("%d%d", &amp;n, &amp;q);
        init();
        int u, v, dis;
        for(int i = 0 ; i &lt; n - 1 ; i++) {
            scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
            addedge(u, v, dis);
            addedge(v, u, dis);
        }
        BFS(1);
        pos = 1;
        memset(dist, 0, sizeof dist);
        dfs(1, -1);
        int ans = 0;
        set&lt;int&gt; s;
        printf("Case #%d:\n", ca++);
        while(q--) {
            int op, x;
            scanf("%d%d", &amp;op, &amp;x);
            if(op == 1) {
                if(s.size() == 0) {
                    s.insert(dfn[x]);
                }
                else if(!s.count(dfn[x])) {
                    set&lt;int&gt;::iterator it = s.lower_bound(dfn[x]);
                    if(it == s.begin() || it == s.end()) {
                        u = fdfn[*s.begin()];
                        v = fdfn[*s.rbegin()];
                    } else {
                        u = fdfn[*it]; --it;
                        v = fdfn[*it];
                    }
                    s.insert(dfn[x]);
                    ans += (dist[x] + dist[LCA(u, v)] - dist[LCA(x, u)] - dist[LCA(x, v)]);
                }
            } else {
                if(s.count(dfn[x])) {
                    s.erase(dfn[x]);
                    if(s.size() != 0) {
                        set&lt;int&gt;::iterator it = s.lower_bound(dfn[x]);
                        if(it == s.begin() || it == s.end()) {
                            u = fdfn[*s.begin()];
                            v = fdfn[*s.rbegin()];
                        } else {
                            u = fdfn[*it]; --it;
                            v = fdfn[*it];
                        }
                        ans -= (dist[x] + dist[LCA(u, v)] - dist[LCA(x, u)] - dist[LCA(x, v)]);
                    }
                }
            }
            printf("%d\n", ans);
        }
    }
    return 0;
}
</pre>
&nbsp;