---
title: 图论专题
id: 178
tags:
  - Graph theory
date: 2016-04-26 16:46:14
categories:
  - Algorithm
---

准被完整的刷一遍图论，包括巩固基础知识，更注重运用技巧和提升难度，包括但不限于最短路，2SAT，网络流，匹配，相应的和搜索，dp的结合。有经典模型，也要注重新题的灵活运用。

&nbsp;

1.[poj2449 Remmarguts' Date第k短路+A*](http://poj.org/problem?id=2449)

有个小坑，当s等于t的时候，最短路不是0，而是绕一下再回来的最短路。
<pre class="lang:c++ decode:true">#include&lt;vector&gt;
#include&lt;queue&gt;
#include&lt;algorithm&gt;
#include&lt;map&gt;
#include&lt;string&gt;
#include&lt;cstring&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#include&lt;cstdio&gt;

using namespace std;

const int MAXN = 1000 + 10;
const int MAXM = 100000 + 10;
const double eps = 1e-7;
const int INF = 0x3f3f3f3f;

int head[MAXN], tot;
int headr[MAXN], totr;
int d[MAXN];

struct Edge {
    int to, next, dis;
} edge[MAXM], edger[MAXM];

void addedge(int u, int v, int dis) {
    edge[tot].to = v;
    edge[tot].dis = dis;
    edge[tot].next = head[u];
    head[u] = tot++;
}

void addedge2(int u, int v, int dis) {
    edger[totr].to = v;
    edger[totr].dis = dis;
    edger[totr].next = headr[u];
    headr[u] = totr++;
}

bool vis[MAXN];
void SPFA(int s, int h[], Edge e[]) {
    memset(d, 0x3f, sizeof d);
    memset(vis, false, sizeof vis);
    vis[s] = true;
    d[s] = 0;
    queue&lt;int&gt; Q;
    Q.push(s);
    while(!Q.empty()) {
        int u = Q.front();
        Q.pop();
        vis[u] = false;
        for(int i = h[u] ; i != -1 ; i = e[i].next) {
            int v = e[i].to;
            if(d[v] &gt; d[u] + e[i].dis) {
                d[v] = d[u] + e[i].dis;
                if(!vis[v]) {
                    vis[v] = true;
                    Q.push(v);
                }
            }
        }
    }
}

struct Node {
    int u, dis;
    Node(int u = 0, int dis = 0) : u(u), dis(dis) {}
    bool operator &lt; (const Node&amp; t) const {
        return dis + d[u] &gt; t.dis + d[t.u];
    }
};

int Astar(int s, int t, int k) {
    if(s == t) ++k;
    priority_queue&lt;Node&gt; Q;
    int cnt = 0;
    Q.push(Node(s, 0));
    while(!Q.empty()) {
        Node tmp = Q.top();
        Q.pop();
        if(tmp.u == t) {
            cnt++;
            if(cnt == k) return tmp.dis;
        }
        for(int i = head[tmp.u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            Q.push(Node(v, tmp.dis + edge[i].dis));
        }
    }
    return -1;
}

int main() {
    int n, m;
    while(scanf("%d%d", &amp;n, &amp;m) == 2) {
        tot = totr = 0;
        memset(head, -1, sizeof head);
        memset(headr, -1, sizeof headr);
        int u, v, dis;
        for(int i = 0 ; i &lt; m ; i++) {
            scanf("%d%d%d", &amp;u,  &amp;v, &amp;dis);
            addedge(u, v, dis);
            addedge2(v, u, dis);
        }
        int s, t, k;
        scanf("%d%d%d", &amp;s, &amp;t, &amp;k);
        SPFA(t, headr, edger);
        printf("%d\n", Astar(s, t, k));
    }
    return 0;
}
/*
4
0 1 1
0 2 1
0 3 1
1
0 1 2
*/
</pre>
&nbsp;

2.[Big Christmas Tree](http://poj.org/problem?id=3013) (SPFA变形)

比较巧妙的一个最短路，最短路可以保证每个点在最终答案中的权值贡献最小，答案就是最短路树的权值。

SPFA用于稀疏图，dij适合稠密图。

有两个坑点，一个是 距离会爆int，还有一个听说是特判n等于0和n等于1，某些最短路板子会出问题（非常意外我的竟然不用特判）

AC代码：
<pre class="lang:c++ decode:true ">#include&lt;vector&gt;
#include&lt;queue&gt;
#include&lt;algorithm&gt;
#include&lt;map&gt;
#include&lt;string&gt;
#include&lt;cstring&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#include&lt;cstdio&gt;

using namespace std;
const int MAXN = 50000 + 10;
const int INF = 0x3f3f3f3f;
const long long LLINF = 4557430888798830399LL;
typedef long long LL;
struct Edge {
    int to, next, dis;
} edge[MAXN &lt;&lt; 1];
LL d[MAXN];
int head[MAXN], tot;
void addedge(int u, int v, int dis) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    edge[tot].dis = dis;
    head[u] = tot++;
}

void init() {
    tot = 0;
    memset(head, -1, sizeof head);
}
bool vis[MAXN];
void SPFA(int s) {
    memset(vis, false, sizeof vis);
    memset(d, 0x3f, sizeof d);
    vis[s] = true;
    d[s] = 0;
    queue&lt;int&gt; Q;
    Q.push(s);
    while(!Q.empty()) {
        int u = Q.front();
        Q.pop();
        vis[u] = false;
        for(int i = head[u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(d[v] &gt; d[u] + edge[i].dis) {
                d[v] = d[u] + edge[i].dis;
                if(!vis[v]) {
                    vis[v] = true;
                    Q.push(v);
                }
            }
        }
    }
}
int w[MAXN];

int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int n, m;
        scanf("%d%d", &amp;n, &amp;m);
        init();
        int u, v, dis;
        for(int i = 1 ; i &lt;= n ; i++)
            scanf("%d", &amp;w[i]);
        for(int i = 0 ; i &lt; m ; i++) {
            scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
            addedge(u, v, dis);
            addedge(v, u, dis);
        }
        SPFA(1);
        LL ans = 0;
        bool ok = true;
        for(int i = 1 ; i &lt;= n ; i++) {
            if(d[i] == LLINF) {
                ok = false;
                break;
            }
            ans += LL(w[i]) * LL(d[i]);
        }
        if(!ok) printf("No Answer\n");
        else printf("%I64u\n", ans);
    }
    return 0;
}
</pre>
3.[POJ 3463 - Sightseeing](http://poj.org/problem?id=3463)（SPFA+记忆化搜索）

统计起点到终点，长度为最短路和最短路+1的路径数

先用SPFA求出最短路，所有边反向再建一遍图，在这个图上就可以记忆化搜出最短路条数（第一次直接把状态写在SPFA里，，WA了）。

最短路+1的路径数同样可以在反向图种求出

设num[i]为到i点最短路的路径数

num1[i]为到i点最短路+1长度的路径数

如果存在反向边&lt;u, v&gt;

num1[u] = Σnum1[v]      (d[u] = d[v] + dis(u, v))

+ Σnum[v]    (d[u] + 1 = d[v] + dis(u, v))

最终答案为num[t] + num1[t] （t为终点）

AC代码
<pre class="lang:c++ decode:true">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cstring&gt;
using namespace std;

typedef long long LL;
const int INF = 0x3f3f3f3f;
const int MAXN = 1000 + 10;
const int MAXM = 10000 + 10;
const int mod = 1000000009;
const double eps = 1e-7;

struct Edge {
    int to, next, dis;
} edge[MAXM], edger[MAXM];
struct Node {
    int d, u;
    Node(int d, int u) : d(d), u(u) {}
    bool operator &lt; (const Node&amp; t) const {
        return d &gt; t.d;
    }
};
int head[MAXN], tot;
int headr[MAXN], totr;
int num[MAXN];
int num1[MAXN];
int d[MAXN];
bool vis[MAXN];
void init() {
    tot = 0;
    totr = 0;
    memset(headr, -1, sizeof headr);
    memset(head, -1, sizeof head);
    memset(num, 0, sizeof num);
    memset(num1, 0, sizeof num1);
}

void addedge(int u, int v, int dis) {
    edge[tot].to = v;
    edge[tot].dis = dis;
    edge[tot].next = head[u];
    head[u] = tot++;
    edger[totr].to = u;
    edger[totr].next = headr[v];
    edger[totr].dis = dis;
    headr[v] = totr++;
}

void SPFA(int s) {
    memset(vis, false, sizeof vis);
    memset(d, 0x3f, sizeof d);
    vis[s] = true;
    d[s] = 0;
    queue&lt;int&gt; Q;
    Q.push(s);
    while(!Q.empty()) {
        int u = Q.front();
        Q.pop();
        vis[u] = false;
        for(int i = head[u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(d[v] &gt; d[u] + edge[i].dis) {
                d[v] = d[u] + edge[i].dis;
                if(!vis[v]) {
                    vis[v] = true;
                    Q.push(v);
                }
            }
        }
    }
}
int dfs(int u) {
    if(vis[u]) return num[u];
    int &amp;res = num[u];
    for(int i = headr[u] ; i != -1 ; i = edger[i].next) {
        int v = edger[i].to;
        if(d[u] == d[v] + edger[i].dis) res += dfs(v);
    }
    vis[u] = true;
    return res;
}
int dfs1(int u) {
    if(vis[u]) return num1[u];
    int &amp;res = num1[u];
    for(int i = headr[u] ; i != -1 ; i = edger[i].next) {
        int v = edger[i].to;
        if(d[u] == d[v] + edger[i].dis) res += dfs1(v);
        else if(d[u] + 1 == d[v] + edger[i].dis) res += num[v];
    }
    vis[u] = true;
    return res;
}

int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        int n, m;
        scanf("%d%d", &amp;n, &amp;m);
        init();
        for(int i = 0 ; i &lt; m ; i++) {
            int u, v, dis;
            scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
            addedge(u, v, dis);
        }
        int s, t;
        scanf("%d%d", &amp;s, &amp;t);
        SPFA(s);
        memset(vis, 0, sizeof vis);
        num[s] = 1;
        for(int i = n ; i &gt;= 1 ; i--) dfs(i);
        memset(vis, 0, sizeof vis);
        dfs1(t);
        printf("%d\n", num[t] + num1[t]);
    }
    return 0;
}
</pre>
4.[POJ3613 Cow Relays](http://acm.pku.edu.cn/JudgeOnline/problem?id=3613)

floyd倍增，有点类似矩阵快速幂

[POJ3613 Cow Relays](https://iridescent.com.cn/index.php/189.html)

5\. 01分数规划相关

*   POJ2976 裸01分数规划 题解[POJ2976](https://iridescent.com.cn/index.php/199.html)
*   POJ2728 最有比例生成树
*   POJ3621 最优比例环 题解[POJ3621](https://iridescent.com.cn/index.php/205.html)
6.生成树专题

只能算理论AC了一下吧0.0

POJ1639 单度限制最小生成树

POJ1679 MST是否唯一/次小生成树

POJ2728 01分数规划，最优比例

POJ3164最小树形图，刘朱算法【待看】

&nbsp;