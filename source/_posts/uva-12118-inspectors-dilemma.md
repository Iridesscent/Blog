---
title: UVA-12118 Inspector's Dilemma
id: 51
tags:
  - Brute force
date: 2016-02-09 12:47:34
categories:
  - Algorithm
---

欧拉回路+dfs
任意两点之间都有连通，要输出经过所有给出的边的最小时间
要使经过所有所给边的时间最小，一定不会将一个边走过两次，这样就变成
了构造一个欧拉道路的问题，输入也许有多个连通块，所以每个连通块都要
构造成一个欧拉道路（回路），通过度数统计需要增加的边，再加上连接不同
连通快的边，再加上所给出的边就是答案。
跑了500多ms，挺长的，个人感觉是因为样例中有大量的稀疏图，邻接矩阵浪费了大量时间,改成邻接表
应该会快点。
<pre class="lang:c++ decode:true " title="UVA-12118 Inspector's Dilemma">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;cstring&gt;
#include&lt;set&gt;
#define inf 0x7f7f7f7f
#define mem(a) memset(a,0,sizeof(a))
#define rep(i,n) for(int i=0;i&lt;n;i++)
using namespace std;
const int maxn = 1010;
int G[maxn][maxn];
int vis[maxn];
int v,e,t;
int ans; int degree[maxn];
int dfs(int n){
    for(int i=1;i&lt;=v;i++){
        if(G[n][i]){
            degree[n]++;
            degree[i]++;
            G[n][i]=G[i][n]=0;
            ans++;//printf("%d\n",ans);
            dfs(i);
        }
    }
}
int main()
{
    int cases=1;
    while(scanf("%d%d%d", &amp;v, &amp;e, &amp;t) == 3 &amp;&amp; v){
        mem(G); mem(vis);
        int uu,vv;
        rep(i,e){
            scanf("%d%d", &amp;uu, &amp;vv);
            G[uu][vv] = G[vv][uu] = 1;
        }
        ans=0;
        for(int i=1;i&lt;=v;i++){
            for(int j=1;j&lt;=v;j++){
                if(G[i][j]){
                    ans++;
                    mem(degree);///补全有两种方案，第一种度数全部补成偶数，第二种保留两个奇点
                    dfs(i);
                    int p1=0;
                    for(int i=1;i&lt;=v;i++){
                        if(degree[i]%2==1) p1++;
                        //printf("%d ",degree[i]);
                    }
                    //printf("\n");
                    if(p1&gt;=2) p1-=2;
                    ans+=p1/2;
                    //printf("%d\n",p1);
                }
            }
        }
        if(ans) ans--;//连接连通快的边等于连通快数减一
        printf("Case %d: %d\n",cases++,ans*t);
    }
    return 0;
}</pre>
&nbsp;