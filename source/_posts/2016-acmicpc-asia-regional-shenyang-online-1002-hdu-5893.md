---
title: 2016 ACM/ICPC Asia Regional Shenyang Online 1002 hdu 5893
id: 285
categories:
  - Algorithm
date: 2016-09-29 15:36:12
tags:
---

数链剖分，处理一下重链之间的数值是否相同
```
#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;algorithm&gt;
#include &lt;cmath&gt;
#include &lt;vector&gt;
#include &lt;queue&gt;
#define lc (o &lt;&lt; 1)
#define rc ((o &lt;&lt; 1) | 1)
#define lson lc, L, M
#define rson rc, M + 1, R
#define FS first
#define SE second

using namespace std;

typedef long long LL;
typedef pair&lt;int, int&gt; PII;
const int MAXN = 50000 + 10;

struct Edge {
  int to, next;
} edge[MAXN &lt;&lt; 1];
int head[MAXN], tot;
int top[MAXN];
int fa[MAXN][20];
int deep[MAXN];
int num[MAXN];
int p[MAXN];
int fp[MAXN];
int son[MAXN];
int pos;

int sumv[MAXN &lt;&lt; 2], setv[MAXN &lt;&lt; 2];
PII seg[MAXN &lt;&lt; 2];
void init() {
  tot = 0;
  memset(head, -1, sizeof head);
  pos = 1;
  memset(son, -1, sizeof son);
  memset(sumv, 0, sizeof sumv);
}

void AddEdge(int u, int v) {
  edge[tot].to = v;
  edge[tot].next = head[u];
  head[u] = tot++;
}

void BFS(int root) {
  queue&lt;int&gt; Q;
  fa[root][0] = root;
  Q.push(root);
  while(!Q.empty()) {
    int tmp = Q.front();
    Q.pop();
    for(int i = 1 ; i &lt; 20 ; i++)
      fa[tmp][i] = fa[fa[tmp][i - 1]][i - 1];
    for(int i = head[tmp] ; i != -1 ; i = edge[i].next) {
      int v = edge[i].to;
      if(v == fa[tmp][0]) continue;
      fa[v][0] = tmp;
      Q.push(v);
    }
  }
}

void dfs1(int u, int pre, int d) {
  deep[u] = d;
  num[u] = 1;
  for(int i = head[u] ; i != -1 ; i = edge[i].next) {
    int v = edge[i].to;
    if(v != pre) {
      dfs1(v, u, d + 1);
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

void pushup(int o, int L, int R) {
  if(R &gt; L) {
    sumv[o] = sumv[lc] + sumv[rc];
    seg[o].FS = seg[lc].FS;
    seg[o].SE = seg[rc].SE;
    if(seg[lc].SE == seg[rc].FS) sumv[o]--;
  } else {
    sumv[o] = 1;
    seg[o].FS = seg[o].SE = setv[o];
  }

  if(setv[o] &gt;= 0) {
    sumv[o] = 1;
    seg[o].FS = seg[o].SE = setv[o];
    return ;
  }
}

void pushdown(int o, int L, int R) {
  if(setv[o] &gt;= 0) {
    setv[lc] = setv[o];
    setv[rc] = setv[o];
    setv[o] = -1;
  }
  pushup(o, L, R);
}
void update(int l, int r, int v, int o, int L, int R) {
  if(l &lt;= L &amp;&amp; r &gt;= R) {
    setv[o] = v;
  } else {
    pushdown(o, L, R);
    int M = L + (R - L) / 2;
    if(l &lt;= M) update(l, r, v, lson); else pushup(lson);
    if(r &gt; M) update(l, r, v, rson); else pushup(rson);
  }
  pushup(o, L, R);
}

int query(int l, int r, int o, int L, int R) {
  if(setv[o] &gt;= 0) return 1;

  if(l &lt;= L &amp;&amp; r &gt;= R) {
    return sumv[o];
  }

  int M = L + (R - L) / 2;
  int res = 0;
  if(l &lt;= M) res += query(l, r, lson);
  if(r &gt; M) res += query(l, r, rson);
  if(l &lt;= M &amp;&amp; r &gt; M) {
    if(seg[lc].SE == seg[rc].FS) res--;
  }
  return res;
}

int e[MAXN][3];

char op[10];

int LCA (int u, int v) {
  int p = top[u], q = top[v];
  while (p != q) {
    if (deep[p] &lt; deep[q]) {
      swap(p, q);
      swap(u, v);
    }
    u = fa[p][0];
    p = top[u];
  }
  return deep[u] &lt; deep[v] ? u : v;
}

int getval(int p, int o, int L, int R) {
  while(L &lt; R) {
    if(setv[o] &gt;= 0) return setv[o];
    int M = L + (R - L) / 2;
    if(p &lt;= M) {
      R = M;
      o = lc;
    } else {
      L = M + 1;
      o = rc;
    }
  }

  return setv[o];
}

int getans(int u, int v, int n) { //printf("-----\n");
  if(u == v) return 0;
  int pu = u, pv = v;
  int lca = LCA(u, v);
  int f1 = top[u];
  int f2 = top[v];
  int tmp = 0;
  while(f1 != f2) {
    if(deep[f1] &lt; deep[f2]) {
      swap(f1, f2);
      swap(u, v);
    }
    tmp += query(p[f1], p[u], 1, 1, n);
    //printf("------%d %d %d\n", u, f1, query(p[f1], p[u], 1, 1, n));
    //printf("%d %d\n", getval(p[fa[f1][0]], 1, 1, n), getval(p[f1], 1, 1, n));
    if(fa[f1][0] != lca &amp;&amp; getval(p[fa[f1][0]], 1, 1, n) == getval(p[f1], 1, 1, n))/* puts("!!"), */tmp--;
    u = fa[f1][0]; f1 = top[u];
  }

  if(u == v) {
    if(pu != lca &amp;&amp; pv != lca) {
      for(int i = 19 ; i &gt;= 0 ; i--) {
        if(deep[fa[pu][i]] &gt; deep[lca]) pu = fa[pu][i];
        if(deep[fa[pv][i]] &gt; deep[lca]) pv = fa[pv][i];
      }
      if(getval(p[pu], 1 ,1, n) == getval(p[pv], 1, 1, n))/*puts("!!"), */tmp--;
      //printf("%d %d~~~\n", pu, pv);
    }
    return tmp;
  }

  if(deep[u] &gt; deep[v]) swap(u, v);
  tmp += query(p[son[u]], p[v], 1, 1, n);
  //printf("******%d %d %d\n", son[u], v, query(p[son[u]], p[v], 1, 1, n));
  if(pu != lca &amp;&amp; pv != lca) {
    for(int i = 19 ; i &gt;= 0 ; i--) {
      if(deep[fa[pu][i]] &gt; deep[lca]) pu = fa[pu][i];
      if(deep[fa[pv][i]] &gt; deep[lca]) pv = fa[pv][i];
    }
    if(getval(p[pu], 1 ,1, n) == getval(p[pv], 1, 1, n))/*puts("!!"), */tmp--;
    //printf("%d %d~~~\n", pu, pv);
  }
  return tmp;
}

void Change(int u, int v, int val, int n) {
  int f1 = top[u];
  int f2 = top[v];
  while(f1 != f2) {
    if(deep[f1] &lt; deep[f2]) {
      swap(f1, f2);
      swap(u, v);
    }
    update(p[f1], p[u], val, 1, 1, n);
    u = fa[f1][0]; f1 = top[u];
  }
  if(u == v) return ;
  if(deep[u] &gt; deep[v]) swap(u, v);
  update(p[son[u]], p[v], val, 1, 1, n);
}

int main() {
  int n, Q;
  while(scanf("%d%d", &amp;n, &amp;Q) == 2) {
    init();
    // ha.init();
    for(int i = 0 ; i &lt; n - 1 ; i++) {
      scanf("%d%d%d", &amp;e[i][0], &amp;e[i][1], &amp;e[i][2]);
      //ha.add(e[i][2]);
      AddEdge(e[i][0], e[i][1]);
      AddEdge(e[i][1], e[i][0]);
    }

    BFS(1);
    dfs1(1, 0, 0);
    dfs2(1, 1);
    setv[1] = 0;
    for(int i = 0 ; i &lt; n - 1 ; i++) {
      if(deep[e[i][0]] &lt; deep[e[i][1]]) swap(e[i][0], e[i][1]);
      update(p[e[i][0]], p[e[i][0]], e[i][2], 1, 1, pos - 1);
    }

//    for(int i = 1 ; i &lt;= n ; i++) printf("%d ", top[i]);
//    printf("\n");
    int u, v, val;
    while(Q--) {
      scanf("%s", op);
      if(op[0] == 'Q') {
        scanf("%d%d", &amp;u, &amp;v);
        printf("%d\n", u == v?0:getans(u, v, pos - 1));
      }
      if(op[0] == 'C') {
        scanf("%d%d%d", &amp;u, &amp;v, &amp;val);
        Change(u, v, val, pos - 1);
      }
    }

  }
  return 0;
}
```