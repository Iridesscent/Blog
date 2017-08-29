---
title: POJ 3621 Sightseeing Cows
id: 205
tags:
  - Binary search
date: 2016-05-02 16:30:41
categories:
  - Algorithm
---

题目链接： [POJ3621 Sightseeing Cows](http://poj.org/problem?id=3621)

01分数规划，最优比率环。

不懂01分数规划可以先去《挑战程序设计竞赛》看，讲的通俗易懂。

不得不说，01分数规划的思路真的很巧妙。

首先有一个结论，答案的环一定是一个简单环，不会经过一个点两次。

也就是说一个点不会同时存在于两个环中。

证明： 假设点s存在在两个环中，权重为w，环1总点权为a1，总边权为b1，环2总点权为a2，总边权为b2

设:

a1/b1 &lt;= a2/b2

则两个环的总点权/总边权为

(a1+a2-w)/(b1+b2)

又因为：

a1/b1 &lt;= (a1+a2)/(b1+b2) &lt;= a2/b2

这样环2永远是最优解

这样就证明了道路一定是一个简单环。

我们设最终答案为ans，则对于所有的环都有

∑w_v/∑w_e &lt;= ans

w_v表示环中点的点权，w_e为边权

整理得：

ans*∑w_e - ∑w_v &gt;= 0

∑(ans * w_e - w_v) &gt;= 0

这样就可以将边权设置为k * w_e - w_v（w_v为边终点的权重，起点也可以，统一就行）

这样对于所有的k &gt;= ans 图中不存在负环

对于所有的k &lt; ans总存在负环

这样就可以二分答案，spfa判断

&nbsp;

Hint:

SPFA判断负环如果图不联通不能一次判断（不过这道题数据略弱，没有这种），可以通过一个虚拟节点，比如0向1..n各连一条单项边，权重为0，然后SPFA(0)，判断是否有负环

AC代码：
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cstring&gt;
#include&lt;cstdlib&gt;
#include&lt;assert.h&gt;
#include&lt;cmath&gt;
#define FS first
#define SE second
using namespace std;

typedef long long LL;

const int INF = 0x3f3f3f3f;
const int MAXN = 1000 + 5;
const int MAXM = 5015 + MAXN;
struct Edge {
    int to, next;
    double w;
    double dis;
} edge[MAXM];
int head[MAXN], tot;

void addedge(int u, int v, double dis) {
    edge[tot].to = v;
    edge[tot].w = dis;
    edge[tot].next = head[u];
    head[u] = tot++;
}
void init() {
    memset(head, -1, sizeof head);
    tot = 0;
}
bool vis[MAXN];
int cnt[MAXN];
double d[MAXN];
int wei[MAXN];
void build(double k, int n) {
    for(int u = 1 ; u &lt;= n ; u++) {
        for(int i = head[u] ; i != -1 ; i = edge[i].next) {
            int v = edge[i].to;
            if(u == v) {
                edge[i].dis = 0;
                continue;
            }
            edge[i].dis = k * edge[i].w - wei[v];
        }
    }

}
bool SPFA(int s, int n) {
    memset(vis, 0, sizeof vis);
    memset(cnt, 0, sizeof cnt);
    for(int i = 0 ; i &lt;= n ; i++) d[i] = INF;
    vis[s] = true;
    d[s] = 0;
    queue&lt;int&gt; Q;
    Q.push(s);
    cnt[s] = 1;
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
                    if(++cnt[v] &gt; n) return false;
                }
            }
        }
    }
    return true;
}
int main() {
    init();
    int n, m;
    scanf("%d%d", &amp;n, &amp;m);
    for(int i = 1 ; i &lt;= n ; i++) scanf("%d", &amp;wei[i]);
    int u, v, dis;
    for(int i = 0 ; i &lt; m ; i++) {
        scanf("%d%d%d", &amp;u, &amp;v, &amp;dis);
        addedge(u, v, dis);
    }
    for(int i = 1 ; i &lt;= n ; i++)
        addedge(0, i, 0);
    double l = 0;
    double r = 2000;
    for(int i = 0 ; i &lt; 30 ; i++) {
        double mid = (l + r) / 2;
        build(mid, n);
        if(SPFA(0, n)) r = mid;
        else l = mid;
    }
    if(l &lt; 0.00001) printf("0\n");
    else printf("%.2f\n", l);
    return 0;
}
/*
5 7
30
10
10
5
10
1 2 3
2 3 2
3 4 5
3 5 2
4 5 5
5 1 3
5 2 2
*/
</pre>
&nbsp;