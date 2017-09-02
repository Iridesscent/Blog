---
title: bzoj2119 股市预测(Suffix Array)
id: 299
tags:
  - String Algorithms
  - Suffix Automaton
date: 2016-10-17 18:49:36
categories:
  - Algorithm
---

[传送门](http://www.lydsy.com/JudgeOnline/problem.php?id=2119)

将原序列差分，求形如ABA序列的数量，A长度不为0，B长度为m

首先可以枚举A序列的长度，这样对于每个长度i，将原序列分块，每块i个字符

对于每块开头位置j，统计所有开头在“以j开头长度为i的块内”形如ABA串的个数，累加得答案

统计方法：

对于每个i， j 令a = j + i - 1, b = j + i * 2 + m - 1，也就是第一个A的末尾和第二个A的末尾，求出向后的lcp长度last和向前的lcp长度pre(当然， pre和last不能大于块的长度)，这样只要第一个A完全包含在[a - pre + 1, a + last - 1]内，这样的都是对于一对i， j要统计的，也就是答案累加max(0, pre + last - i)

AC代码：
```
#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;algorithm&gt;
#include &lt;cmath&gt;
#include &lt;vector&gt;
#include &lt;queue&gt;
#include &lt;assert.h&gt;
#include &lt;map&gt;
#define FS first
#define SE second

using namespace std;
typedef long long LL;
typedef pair&lt;int, int&gt; PII;
const int MAXN = 50000 + 10;

struct SuffixArray {
  int s[MAXN];
  int sa[MAXN];
  int Rank[MAXN];
  int height[MAXN];
  int t[MAXN], t2[MAXN], c[MAXN];
  int n;

  void clear() { n = 0; memset(sa, 0, sizeof(sa)); }

  void build_sa(int m) {
    int i, *x = t, *y = t2;
    for(i = 0; i &lt; m; i++) c[i] = 0;
    for(i = 0; i &lt; n; i++) c[x[i] = s[i]]++;
    for(i = 1; i &lt; m; i++) c[i] += c[i-1];
    for(i = n-1; i &gt;= 0; i--) sa[--c[x[i]]] = i;
    for(int k = 1; k &lt;= n; k &lt;&lt;= 1) {
      int p = 0;
      for(i = n-k; i &lt; n; i++) y[p++] = i;
      for(i = 0; i &lt; n; i++) if(sa[i] &gt;= k) y[p++] = sa[i]-k;
      for(i = 0; i &lt; m; i++) c[i] = 0;
      for(i = 0; i &lt; n; i++) c[x[y[i]]]++;
      for(i = 1; i &lt; m; i++) c[i] += c[i-1];
      for(i = n-1; i &gt;= 0; i--) sa[--c[x[y[i]]]] = y[i];
      swap(x, y);
      p = 1; x[sa[0]] = 0;
      for(i = 1; i &lt; n; i++)
        x[sa[i]] = y[sa[i-1]]==y[sa[i]] &amp;&amp; y[sa[i-1]+k]==y[sa[i]+k] ? p-1 : p++;
      if(p &gt;= n) break;
      m = p;
    }
  }

  void build_height() {
    int i, k = 0;
    for(i = 0; i &lt; n; i++) Rank[sa[i]] = i;
    for(i = 0; i &lt; n; i++) {
      if(k) k--;
      int j = sa[Rank[i]-1];
      while(s[i+k] == s[j+k]) k++;
      height[Rank[i]] = k;
    }
  }

  int RMQ[MAXN];
  int mm[MAXN];
  int best[20][MAXN];
  void initRMQ() {
    mm[0] = -1;
    for(int i = 1 ; i &lt;= n ; i++)
      mm[i] = ((i &amp; (i - 1)) == 0)?mm[i - 1] + 1 : mm[i - 1];
    for(int i = 1 ; i &lt;= n ; i++) best[0][i] = i;
    for(int i = 1 ; i &lt;= mm[n] ; i++)
      for(int j = 1 ; j + (1 &lt;&lt; i) - 1 &lt;= n ; j++) {
        int a = best[i - 1][j];
        int b = best[i - 1][j + (1 &lt;&lt; (i - 1))];
        if(RMQ[a] &lt; RMQ[b]) best[i][j] = a;
        else best[i][j] = b;
      }
  }
  int rmq(int a, int b) {
    int t;
    t = mm[b - a + 1];
    b -= (1 &lt;&lt; t) - 1;
    a = best[t][a]; b = best[t][b];
    return RMQ[a] &lt; RMQ[b]? a:b;
  }
  int lcp(int a, int b) {
    a = Rank[a];
    b = Rank[b];
    if(a &gt; b) swap(a, b);
    return height[rmq(a + 1, b)];
  }
}sa, sar;

struct HASH {
  int Hash[MAXN];
  int cnt;
  void add(int x) {
    Hash[cnt++] = x;
  }
  void build() {
    sort(Hash, Hash + cnt);
    cnt = unique(Hash, Hash + cnt) - Hash;
  }

  int get(int x) {
    return lower_bound(Hash, Hash + cnt, x) - Hash + 1;
  }
} h;

int num[MAXN];

int main() {
  int n, m;
  scanf("%d%d", &amp;n, &amp;m);
  for(int i = 0 ; i &lt; n ; i++) scanf("%d", &amp;num[i]);
  for(int i = n - 1 ; i &gt;= 1 ; i--) num[i] -= num[i - 1], h.add(num[i]);
  h.build();
  for(int i = 1 ; i &lt; n ; i++) sar.s[n - i - 1] = sa.s[i - 1] = h.get(num[i]);
  sa.n = n;
  sar.n = n;
  sa.build_sa(h.cnt + 1);
  sar.build_sa(h.cnt + 1);
  sa.build_height();
  sar.build_height();
  n--;
  sa.n--;
  sar.n--;
  for(int i = 1 ; i &lt;= sa.n ; i++) sa.RMQ[i] = sa.height[i], sar.RMQ[i] = sar.height[i];
  sa.initRMQ();
  sar.initRMQ();
  LL ans = 0;

  for(int i = 1 ; i &lt;= n ; i++)
    for(int j = 0 ; j + i * 2 + m - 1 &lt; n ; j += i) {
      int a = j + i - 1, b = j + i * 2 + m - 1;
      int pre = sar.lcp(n - 1 - a, n - 1 - b);
      int last = sa.lcp(a, b);
      if(pre &gt; i) pre = i;
      if(last &gt; i) last = i;
      ans += max(pre + last - i, 0);
    }
  printf("%lld\n", ans);
  return 0;
}
```