---
title: UVA1328 - Period(KMP)
tags:
  - KMP
  - String Algorithm
id: 107
categories:
  - Algorithm
date: 2016-02-23 15:17:07
---

**题目链接：**[UVA1328 - Period](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=505&amp;problem=4074&amp;mosmsg=Submission+received+with+ID+16890105)

**题目大意：**给出一个长度为n的字符串，找出所有的1 &lt;=i&lt;=n使得前i的字符是由某个前缀重复k次得到，并使k最大，并输出所有的i和k。

**解题思路：**本来想用类似于筛素数的方法打表过，但是这样判断字符串相等要用LCP，但是本题空间复杂度不允许LCP。

后来想想，KMP算法中的next数组next[i]表示前i个字符前缀后缀的最长相等长度，比如abaa的next[3]=1，因为b两侧的两个a是相同的。

&nbsp;

![UVA1328-Period](https://www.iridescent.com.cn/wp-content/uploads/2016/02/UVA1328-Period.png)

图中中间长方形为题目中给出的串的前i个字符组成的字符串，令len(StrA) = len(StrB)=next[i]，这样StrA和StrB就表示相同的前缀后缀，及StrA==StrB。

这样如果i % (i - len(StrA)) == 0就一定存在一个解，且循环节长度最短为i-len(StrA)，也就是一种类似于错位的思想。

这样先计算书next数组，就能在O(n)的时间内解出答案。

**AC代码：**
<pre class="lang:c++ decode:true " title="UVA1328 - Period">#include&lt;cstdio&gt;
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

typedef long long LL;

const int INF = 0x3f3f3f3f;
const int MAXN = 1000000 + 10;
const double eps = 1e-7;

char s[MAXN];
int len[MAXN];
int F[MAXN];

void getFail(char *P, int *f) {
    int m = strlen(P);
    f[0] = 0;
    f[1] = 0;
    for(int i = 1 ; i &lt; m ; i++) {
        int j = f[i];
        while(j &amp;&amp; P[i] != P[j]) j = f[j];
        f[i + 1] = P[i] == P[j]?j + 1:0;
    }
}

int main() {
    int n;
    int ca(1);
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        scanf("%s", s);
        getFail(s, F);
        printf("Test case #%d\n", ca++);
        for(int i = 2 ; i &lt;= n ; i++) {
            if(F[i] &amp;&amp; i % (i - F[i]) == 0) {
                printf("%d %d\n", i, i / (i - F[i]));
            }
        }
        printf("\n");
    }
    return 0;
}
</pre>
&nbsp;