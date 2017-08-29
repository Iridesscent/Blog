---
title: UVA10881 - Piotr's Ants
id: 56
categories:
  - Algorithm
date: 2016-02-17 15:03:07
tags:
---

题目链接：[UVA10881 - Piotr's Ants](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;category=456&amp;page=show_problem&amp;problem=1822)

只要想出两点，这个题就很好解出了

1.  两个蚂蚁相遇在总体上相当于互相穿过
2.  蚂蚁运动的过程中前后相对顺序不会改变
这样的话我们就可以通过1算出蚂蚁的终止位置，再通过2确定蚂蚁的id（输入顺序）

详见代码
<pre class="lang:c++ decode:true " title="UVA10881 - Piotr's Ants">#include&lt;cstdio&gt;
#include&lt;iostream&gt;
#include&lt;cstring&gt;
#include&lt;string&gt;
#include&lt;algorithm&gt;
#include&lt;vector&gt;
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
const int MAXN = 10000 + 10;
const double eps = 1e-7;

struct Ant {
    int id;
    int p;
    char dir;

    Ant(int id = -1, int p = 0, char dir = 0) : id(id), p(p), dir(dir) {}

    bool operator &lt; (const Ant&amp; t) const {
        return p &lt; t.p;
    }
} ant[MAXN], antend[MAXN];

int cmp(const Ant&amp; a, const Ant&amp; b) {
    return a.id &lt; b.id;
}

int main() {
    int L, T, n;
    int cases;
    scanf("%d", &amp;cases);
    for(int cnt = 1 ; cnt &lt;= cases ; cnt++) {
        scanf("%d%d%d", &amp;L, &amp;T, &amp;n);
        for(int i = 0 ; i &lt; n ; i++) {
            int p;
            char dir;
            scanf("%d%*c%c", &amp;p, &amp;dir);
            antend[i] = ant[i] = Ant(i, p, dir);
        }

        for(int i = 0 ; i &lt; n ; i++) {
            if(antend[i].dir == 'L') antend[i].p -= T;
            else antend[i].p += T;
        }

        sort(antend, antend + n);
        sort(ant, ant + n);

        for(int i = 0 ; i &lt; n ; i++) antend[i].id = ant[i].id;

        if(antend[0].p &lt; 0 || antend[0].p &gt; L) antend[0].dir = 'D';
        for(int i = 1 ; i &lt; n ; i++) {
            if(antend[i].p &lt; 0 || antend[i].p &gt; L) antend[i].dir = 'D';
            else if(antend[i].p == antend[i - 1].p) antend[i].dir = antend[i - 1].dir = 'T';
        }

        sort(antend, antend + n, cmp);

        printf("Case #%d:\n", cnt);
        for(int i = 0 ; i &lt; n ; i++) {
            if(antend[i].dir == 'D') printf("Fell off\n");
            else if(antend[i].dir == 'T') printf("%d Turning\n", antend[i].p);
            else printf("%d %c\n", antend[i].p, antend[i].dir);
        }
        printf("\n");
    }
    return 0;
}
</pre>
&nbsp;