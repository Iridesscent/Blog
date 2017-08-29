---
title: POJ2762 Going from u to v or from v to u?(弱连通判定)
id: 215
tags:
  - Strongly Connected Componenet
date: 2016-05-03 17:42:22
categories:
  - Algorithm
---

题目：[Going from u to v or from v to u?](http://poj.org/problem?id=2762)

先缩点，看有几个点入度为0，不是一个输出No

也就是说缩点后现在是一个单项树，树的每个节点必须仅有一个儿子，也就是说这必须是一个单项链。

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
const int MAXN = 1000 + 10;
const int MAXM = 6010;
struct Edge {
    int to, next;
} edge[MAXM];
int head[MAXN], tot;
vector&lt;int&gt; G[MAXN];
void init() {
    tot = 0;
    memset(head, -1, sizeof head);
}
int Low[MAXN], DFN[MAXN], Stack[MAXN], Belong[MAXN];
int Index, top;
int scc;
bool Instack[MAXN];
void addedge(int u, int v) {
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
}
void Tarjan(int u) {
    int v;
    Low[u] = DFN[u] = ++Index;
    Stack[top++] = u;
    Instack[u] = true;
    for(int i = head[u] ; i != -1 ; i = edge[i].next) {
        int v = edge[i].to;
        if(!DFN[v]) {
            Tarjan(v);
            if(Low[u] &gt; Low[v]) Low[u] = Low[v];
        } else if(Instack[v] &amp;&amp; Low[u] &gt; DFN[v]) Low[u] = DFN[v];
    }
    if(Low[u] == DFN[u]) {
        scc++;
        do {
            v = Stack[--top];
            Instack[v] = false;
            Belong[v] = scc;
        } while(u != v);
    }
}
int n, m;
void slove() {
    memset(DFN, 0, sizeof DFN);
    memset(Instack, false, sizeof Instack);
    Index = scc = top = 0;
    for(int i = 1 ; i &lt;= n ; i++)
        if(!DFN[i]) Tarjan(i);
}
int in[MAXN];
int main() {
    int T;
    scanf("%d", &amp;T);
    while(T--) {
        scanf("%d%d", &amp;n, &amp;m);
        init();
        for(int i = 0 ; i &lt; m ; i++) {
            int u, v;
            scanf("%d%d", &amp;u, &amp;v);
            addedge(u, v);
        }
        slove();
        memset(in, 0, sizeof in);
        for(int i = 0 ; i &lt; MAXN ; i++) G[i].clear();
        for(int u = 1 ; u &lt;= n ; u++) {
            for(int i = head[u] ; i != -1 ; i = edge[i].next) {
                int v = edge[i].to;
                if(Belong[u] != Belong[v]) {
                    in[Belong[v]]++;
                    G[Belong[u]].push_back(Belong[v]);
                }
            }
        }
        int cnt = 0;
        for(int i = 1 ; i &lt;= scc ; i++)
            if(in[i] == 0) cnt++;
        if(cnt != 1) {
            puts("No");
            continue;
        }
        bool ok = true;
        for(int i = 1 ; i &lt;= scc ; i++)
            if(G[i].size() &gt; 1) {
                ok = false;
                break;
            }
        if(ok) puts("Yes");
        else puts("No");
    }
    return 0;
}
</pre>
&nbsp;