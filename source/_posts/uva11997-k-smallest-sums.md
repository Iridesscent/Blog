---
title: UVA11997 - K Smallest Sums（多路归并）
id: 84
categories:
  - Algorithm
date: 2016-02-20 17:29:59
tags:
---

**题目链接：**[UVA11997 - K Smallest Sums](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;category=497&amp;page=show_problem&amp;problem=3148)

**题目大意：**给出n*n的矩阵，每行选取一个数做和，就能产生n<sup>n</sup>个和，从中选取n个最小的输出。

**解题思路：**这是一个n路归并问题，相当于进行n-1次两两归并。那么怎么进行两两归并呢？

两两归并：对与数列{A<sub>1</sub>, A<sub>2</sub>, ... , A<sub>n</sub>}和{B<sub>1</sub>, B<sub>2</sub>, ... , B<sub>n</sub>}可得矩阵

A<sub>1</sub>+B<sub>1</sub>&lt;=A<sub>1</sub>+B<sub>2</sub>&lt;= ... &lt;= A<sub>1</sub>+B<sub>n</sub>

A<sub>2</sub>+B<sub>1</sub>&lt;=A<sub>2</sub>+B<sub>2</sub>&lt;= ... &lt;= A<sub>2</sub>+B<sub>n</sub>

...

A<sub>n</sub>+B<sub>1</sub>&lt;=A<sub>n</sub>+B<sub>2</sub>&lt;= ... &lt;= A<sub>n</sub>+B<sub>n</sub>

我们可以将用一个结构体存储第一列元素和各自的列坐标，放入优先队列中，每次取最小的，令其列坐标加1并计算出新的数值放回优先队列中，这样取n次就完成了两两归并。代码见下文merge函数

解决了两两归并问题，将原题n个数列归并n - 1次，就得到了答案

**AC代码：**
<pre class="lang:c++ decode:true " title="UVA11997 - K Smallest Sums">#include&lt;cstdio&gt;
#include&lt;iostream&gt;
#include&lt;cstring&gt;
#include&lt;string&gt;
#include&lt;algorithm&gt;
#include&lt;vector&gt;
#include&lt;stack&gt;
#include&lt;string&gt;
#include&lt;set&gt;
#include&lt;map&gt;
#include&lt;queue&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#define lson (o &lt;&lt; 1), L, M
#define rson (o &lt;&lt; 1) | 1, M + 1, R

using namespace std;

const int INF = 0x3f3f3f3f;
const int MAXN = 800;
const double eps = 1e-7;

int num[MAXN][MAXN];

struct Item {
    int num, id;
    Item(int num, int id) : num(num), id(id) {}
    bool operator &lt; (const Item&amp; t) const {
        return num &gt; t.num;
    }
};

void merge(int *A, int *B, int *C, int n) {
    priority_queue&lt;Item&gt; que;
    for(int i = 0 ; i &lt; n ; i++)
        que.push(Item(A[i] + B[0], 0));
    for(int cnt = 0 ; cnt &lt; n ; cnt++) {
        Item tmp = que.top();
        que.pop();
        C[cnt] = tmp.num;
        tmp.num = tmp.num - B[tmp.id] + B[tmp.id + 1];
        tmp.id++;
        que.push(tmp);
    }
}

int main() {
    int n;
    while(scanf("%d", &amp;n) == 1) {
        for(int i = 0 ; i &lt; n ; i++) {
            for(int j = 0 ; j &lt; n ; j++)
                scanf("%d", &amp;num[i][j]);
            sort(num[i], num[i] + n);
        }

        for(int i = 1 ; i &lt; n ; i++) {
            merge(num[0], num[i], num[0], n);
        }

        for(int i = 0 ; i &lt; n ; i++)
            if(i == n - 1) printf("%d\n", num[0][i]);
            else printf("%d ", num[0][i]);
    }
    return 0;
}
</pre>
&nbsp;