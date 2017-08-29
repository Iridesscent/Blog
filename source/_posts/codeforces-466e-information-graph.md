---
title: Codeforces 466E Information Graph
tags:
  - find-union-set
  - LCA
id: 155
categories:
  - Algorithm
date: 2016-03-09 20:09:46
---

**题目链接：**[Codeforces 466E](http://codeforces.com/contest/466/problem/E)

**题目大意：**输入数字n，m代表有n个点，m个查询

初始是所有点都孤立

接下来有n行，每行代表一个查询，形式是以下形式之一：

1.  1 x y 把y设为x的根节点，保证x之前没有根节点
2.  2 x 把x到起根节点的所有得点标记cnt cnt为2操作的出现次数，从1开始
3.  3 x i 询问x节点是否被编号i曾经标记过，是输出YES，否输出NO
**解题思路：**询问2是标记到当时生成的树的树根，这种不断往里加边生成树的过程很容易让人联想到并查集，在并查集中记录深度，这样就能求出每次2操作影响到它上面多少个节点。在最终形成的树/森林中，询问3的节点如果和标记i在一条链上，就能通过深度求出是否受影响了。

是否在一条链上可以通过倍增LCA求出，把两个节点调整到统一高度后，如果两个点重合就在一条链上，反之不在一条链上。

当然了，如果要用LCA的话就要离线一下，先建树求2操作的距离，在用LCA和求得的距离直行操作3

这里要注意一点，给出的图可能是一个森林，如果是的话就设置一个虚拟节点，在LCA查询是调到同一高度后不要在虚拟节点上就行了。

**AC代码：**
<pre class="lang:default decode:true " title="Codeforces 466 E Information Graph">#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;iostream&gt;
#include &lt;algorithm&gt;
#include &lt;cstdlib&gt;
#include&lt;cstring&gt;
#include&lt;assert.h&gt;
#include&lt;stack&gt;
#include&lt;set&gt;
#include&lt;queue&gt;
#include&lt;map&gt;
#include&lt;set&gt;
#define FS first
#define SE second
#define lson (o &lt;&lt; 1), L, M
#define rson ((o &lt;&lt; 1) | 1), M + 1, R
#define lc (o &lt;&lt; 1)
#define rc ((o &lt;&lt; 1) | 1)

using namespace std;

typedef long long LL;
const int MAXN = 100010;
const int INF = 0x3f3f3f3f;
const int DEG = 20;
const double eps = 1e-7;

struct Edge {
    int to, next;
} edge[MAXN &lt;&lt; 1];

int head[MAXN], tot;
void AddEdge(int u, int v) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
}

void init() {
    tot = 0;
    memset(head, -1, sizeof head);
}

int fa[MAXN][20];
int deg[MAXN];
void BFS(int root) {
    queue&lt;int&gt; que;
    deg[root] = 0;
    fa[root][0] = root;
    que.push(root);
    while(!que.empty()) {
        int tmp = que.front();
        que.pop();
        for(int i = 1 ; i &lt; DEG ; i++)
            fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
        for(int i = head[tmp] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(v == fa[tmp][0]) continue;
            deg[v] = deg[tmp] + 1;
            fa[v][0] = tmp;
            que.push(v);
        }
    }
}
int n, m;

bool LCA(int u, int v) {
    if(deg[u] &gt; deg[v]) swap(u, v);
    int hu = deg[u], hv = deg[v];
    int tu = u, tv = v;
    for(int det = hv - hu, i = 0 ; det ; det &gt;&gt;= 1, i++) {
        if(det &amp; 1)
            tv = fa[tv][i];
    }
    return tu == tv &amp;&amp; tu != n + 1;
}

int op[MAXN][3];
int in[MAXN];

int F[MAXN];
int d[MAXN];
int find(int x) {
    if(F[x] != x) {
        int root = find(F[x]);
        d[x] += d[F[x]];
        return F[x] = root;
    } else return x;
}
map&lt;int, pair&lt;int, int&gt; &gt; go;
int Count;
int main() {
    init();
    for(int i = 0 ; i &lt; MAXN ; i++) F[i] = i;

    scanf("%d%d", &amp;n, &amp;m);
    for(int i = 0 ; i &lt; m ; i++) {
        scanf("%d", &amp;op[i][0]);
        if(op[i][0] == 1) {
            scanf("%d%d", &amp;op[i][1], &amp;op[i][2]);
            AddEdge(op[i][2], op[i][1]);
            in[op[i][1]]++;
            int u = find(op[i][1]);
            int v = find(op[i][2]);
            F[u] = v;
            d[u] = d[op[i][2]] + 1;
        }
        if(op[i][0] == 2) {
            scanf("%d", &amp;op[i][1]);
            find(op[i][1]);
            go[++Count] = pair&lt;int, int&gt;(op[i][1], d[op[i][1]]);
        }
        if(op[i][0] == 3) {
            scanf("%d%d", &amp;op[i][1], &amp;op[i][2]);
        }
    }

    Count = 0;
    int root;
    for(int i = 1 ; i &lt;= n ; i++) {
        if(in[i] == 0) {
            root = i;
            Count++;
        }
    }
    if(Count &gt; 1) {
        root = n + 1;
        for(int i = 1 ; i &lt;= n ; i++)
            if(in[i] == 0)
                AddEdge(n + 1, i);
    }

    BFS(root);
    for(int i = 0 ; i &lt; m ; i++) {
        if(op[i][0] == 3) {
            if(!LCA(op[i][1], go[op[i][2]].FS)) {
                printf("NO\n");
            }
            else {
                int u = op[i][1];
                int v = go[op[i][2]].FS;
                int dis = go[op[i][2]].SE;
                if(deg[u] + dis &lt; deg[v] || deg[v] &lt; deg[u]) {
                    printf("NO\n");
                } else {
                    printf("YES\n");
                }
            }
        }
    }
    return 0;
}
/*
3 4
1 2 1
1 3 2
2 3
3 1 1
*/
</pre>
&nbsp;