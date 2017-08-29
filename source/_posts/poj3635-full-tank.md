---
title: POJ3635 Full Tank?
id: 195
tags:
  - Brute force
date: 2016-04-28 22:03:54
categories:
  - Algorithm
---

[POJ3635 Full Tank?](http://poj.org/problem?id=3635)

这个搜索并不是很理解啊。。。

vis[i][j] 代表到点i剩下了j单位的油这个状态是否到达过

每次有两种决策，呆在原地加1单位的油和走向下一个点，这样每个状态第一次到达就是最小消耗，判重剪纸。

仔细想想好像也没错，每次在原地+1的油挡在优先队列的队首，保证可以拓展出最优解（这个说法有点别扭）

代码：
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cstring&gt;
#define FS first
#define SE second
using namespace std;

typedef long long LL;

const int INF = 0x3f3f3f3f;
const int MAXN = 1010;
const int MAXM = 10010;
const double eps = 1e-7;

struct Edge {
    int to, next, dis;
} edge[MAXM &lt;&lt; 1];
int head[MAXN], tot;
void addedge(int u, int v, int dis) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    edge[tot].dis = dis;
    head[u] = tot++;
}

int vis[MAXN][105];
int p[MAXN];
int c, s, t;
struct Node {
    int u, f, cost;
    Node(int u, int f, int cost) : u(u), f(f), cost(cost) {}
    bool operator &lt; (const Node&amp; t) const {
        if(cost != t.cost) return cost &gt; t.cost;
        return f &lt; t.f;
    }
};
void BFS() {
    memset(vis, 0, sizeof vis);
    vis[s][0] = 1;
    priority_queue&lt;Node&gt; Q;
    Q.push(Node(s, 0, 0));
    while(!Q.empty()) {
        Node u = Q.top();
        Q.pop();
        if(u.u == t) {
            printf("%d\n", u.cost);
            return;
        }
        if(u.f + 1 &lt;= c) {
            Node ne = u;
            ne.f++;
            ne.cost += p[u.u];
            Q.push(ne);
        }
        for(int i = head[u.u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(u.f - edge[i].dis &lt; 0) continue;
            Node ne(v, u.f - edge[i].dis, u.cost);
            if(vis[v][ne.f]) continue;
            vis[v][ne.f] = 1;
            Q.push(ne);
        }
    }
    printf("impossible\n");
}

int main() {
    memset(head, -1, sizeof head);
    int n, m;
    scanf("%d%d", &amp;n, &amp;m);
    for(int i = 0 ; i &lt; n ; i++)
        scanf("%d", &amp;p[i]);
    for(int i = 0 ; i &lt; m ; i++) {
        int u, v, dis;
        scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
        addedge(u, v, dis);
        addedge(v, u, dis);
    }
    int q;
    scanf("%d", &amp;q);
    while(q--) {
        scanf("%d%d%d", &amp;c, &amp;s, &amp;t);
        BFS();
    }
    return 0;
}
</pre>
&nbsp;