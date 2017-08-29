---
title: SPOJ FTOUR2(树分治)
id: 293
tags:
  - Data structure
  - Tree partition
date: 2016-10-10 17:48:52
categories:
  - Algorithm
---

[传送门](http://www.spoj.com/problems/FTOUR2/)

注意k等于0的情况，在一个就是答案最小是0，就是一个边都不走

些合并的时候手残把root写成u错了好久。。

code：
<pre class="lang:c++ decode:true ">#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;algorithm&gt;
#include &lt;cmath&gt;
#include &lt;vector&gt;
#include &lt;queue&gt;
#include &lt;map&gt;
#define FS first
#define SE second

using namespace std;
typedef long long LL;
const int MAXN = 200000 + 10;
const int INF = 0x3f3f3f3f;
int head[MAXN], tot;
bool vis[MAXN];
bool col[MAXN];
int minn;
int sz[MAXN];
int malen[MAXN];
int malent[MAXN];
int cnt;
int cntt;
struct Edge {
  int to, next, w;
} edge[MAXN &lt;&lt; 1];
void init() {
  memset(head, -1, sizeof head);
  tot = 0;
}
void addedge(int u, int v, int w) {
  edge[tot].to = v;
  edge[tot].next = head[u];
  edge[tot].w = w;
  head[u] = tot++;
}
int getsz(int u, int pre) {
  sz[u] = 1;
  for(int i = head[u] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(v == pre || vis[v]) continue;
    sz[u] += getsz(v, u);
  }
  return sz[u];
}
void getroot(int u, int pre, int totsz, int&amp; root) {
  int maxx = totsz - sz[u];
  for(int i = head[u] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(v == pre || vis[v]) continue;
    getroot(v, u, totsz, root);
    maxx = max(maxx, sz[v]);
  }
  if(maxx &lt; minn) {
    minn = maxx;
    root = u;
  }
}

void getlen(int u, int pre, int len, int num) {
  if(col[u]) num++;
  malent[num] = max(malent[num], len);
  cntt = max(cntt, num);
  for(int i = head[u] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(v == pre || vis[v]) continue;
    getlen(v, u, len + edge[i].w, num);
  }
}

int k;
struct Item {
  int v, dis;
  Item(int v = 0, int dis = 0) : v(v), dis(dis) {}
} V[MAXN];
bool cmp(const Item&amp; a, const Item&amp; b) {
  return sz[a.v] &lt; sz[b.v];
}
int vpos;
int solve(int u) {
  int totsz = getsz(u, -1);
  int ret = -INF;
  minn = INF;
  int root;
  getroot(u, -1, totsz, root);
  vis[root] = true;
  for(int i = head[root] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(vis[v]) continue;
    ret = max(ret, solve(v));
  }

  vpos = 0;
  for(int i = head[root] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(vis[v]) continue;
    V[vpos++] = Item(v, edge[i].w);
    getsz(v, root);
  }

  cnt = -1;
  sort(V, V + vpos, cmp);
  int add = col[root]?1:0;
  //printf("%d-------------------\n", root);
  for(int i = 0 ; i &lt; vpos ; i++) {
    int &amp;v = V[i].v;
    cntt = -1;
    getlen(v, root, V[i].dis, 0);
    //printf("%d %d**\n", v, sz[v]);
    //for(int j = 0 ; j &lt;= cntt ; j++) printf("%d ", malent[j]);
    //printf("\n");
    ret = max(ret, malent[0]);
    for(int j = 1 ; j &lt;= cntt ; j++) {
      if(add + j &lt;= k) ret = max(ret, malent[j]);
      malent[j] = max(malent[j - 1], malent[j]);
    }
    int e = cntt;
    for(int j = 0 ; j &lt;= cnt ; j++) {
        while(e &gt;= 0 &amp;&amp; j + e + add &gt; k) e--;
        if(e &lt; 0) break;
        ret = max(ret, malen[j] + malent[e]);
    }
    cnt = max(cnt, cntt);
    for(int j = 0 ; j &lt;= cnt ; j++) {
      malen[j] = max(malen[j], malent[j]);
      if(j) malen[j] = max(malen[j], malen[j - 1]);
      malent[j] = -INF;
    }
  }

  for(int i = 0 ; i &lt;= cnt ; i++) malen[i] = -INF;
  vis[root] = false;
  return ret;
}

int main() {
  int n, m;
  scanf("%d%d%d", &amp;n, &amp;k, &amp;m);

  init();
  for(int u, i = 0 ; i &lt; m ; i++) {
    scanf("%d", &amp;u);
    col[u] = true;
  }
  for(int u, v, w, i = 1 ; i &lt; n  ; i++) {
    scanf("%d%d%d", &amp;u, &amp;v, &amp;w);
    addedge(u, v, w);
    addedge(v, u, w);
  }
  for(int i = 0 ; i &lt;= n ; i++) malen[i] = malent[i] = -INF;
  printf("%d\n", max(0, solve(1)));
  return 0;
}

/*
7 0 2
2 3
1 2 1
1 3 1
2 4 1
2 5 1
3 6 1
3 7 1
*/
</pre>
&nbsp;