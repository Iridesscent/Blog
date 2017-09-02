---
title: 'codeforses #80 div1 D'
id: 280
tags:
  - Square partition
date: 2016-08-24 10:40:10
categories:
  - Algorithm
---

传送门

题意：给出n个数num[1..n]，q个查询，对于每个查询(a, b) 输出num[a] + num[a + b] + num[a + 2*b]+....

1 &lt;= n &lt;= 300000 1 &lt;= q &lt;= 300000 时限4S

离线查询，按照查询的b值排序，

对于b大的暴力，对于b小的维护一个数组tmp
<pre class="lang:default decode:true ">for i = n to 1
  tmp[i] = num[i]
  if(tmp[i] + b &lt;= n) tmp[i] += tmp[i + b]</pre>
每当遇到一个新的b更新一次数组，答案就是tmp[a]

b大时复杂度每次查询复杂度O(√n)，b小时tmp数组最多更新√n次，总复杂度O(n√n).

AC代码：
```
#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;algorithm&gt;
#include &lt;vector&gt;
#include &lt;iostream&gt;
#include &lt;cmath&gt;
#define FS first
#define SE second

using namespace std;
typedef long long LL;

const int MAXN = 300010;
int num[MAXN];

struct Query {
    int a, b, id;
    bool operator &lt; (const Query&amp; t) const {
        return b &lt; t.b;
    }
} query[MAXN];
LL tmp[MAXN];
LL ans[MAXN];
int main() {
    int n, q;
    scanf("%d", &amp;n);
    for(int i = 1 ; i &lt;= n ; i++)
        scanf("%d", &amp;num[i]);
    scanf("%d", &amp;q);
    int base = sqrt(n);
    for(int i = 0 ; i &lt; q ; i++) {
        scanf("%d%d", &amp;query[i].a, &amp;query[i].b);
        query[i].id = i;
    }
    sort(query, query + q);
    for(int i = 0 ;i &lt; q ; i++) {
        if(query[i].b &gt;= base) {
            for(int j = query[i].a ; j &lt;= n ; j += query[i].b)
                ans[query[i].id] += num[j];
        } else {
            if(i == 0 || query[i].b != query[i - 1].b) {
                for(int j = n ; j &gt;= 1 ; j--) {
                    tmp[j] = num[j];
                    if(j + query[i].b &lt;= n) tmp[j] += tmp[j + query[i].b];
                }
            }
            ans[query[i].id] = tmp[query[i].a];
        }
    }

    for(int i = 0 ; i &lt; q ; i++) printf("%I64d\n", ans[i]);
    return 0;
}
```