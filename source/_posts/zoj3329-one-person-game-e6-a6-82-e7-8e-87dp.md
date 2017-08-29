---
title: ZOJ3329 One Person Game(概率dp)
categories:
  - Algorithm
id: 157
tags:
  - Dynamic programing
date: 2016-03-10 18:04:01
---

Orz，，这公式推的太神奇了

**题目链接：**[ZOJ3329 One Person Game](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemCode=3329)

还有一道相似的题目，都需要类似与手动消元的优化。[HDU 4089 Activation](http://acm.hdu.edu.cn/showproblem.php?pid=4089)

**题目大意：**一次投三枚骰子，每次得分是三个骰子点数之和，如果骰子1的点数为a，骰子b的点数是b，骰子3的点数是c则得分清零，问达到n分以上的期望投掷次数

**解题思路：**

明显是概率dp，我们设dp[i]为现在得分为i，达到目标积分还要的分数。可得递推方程

dp[i] = ∑dp[i+k]*p[k] + dp[0] *p0+1

p0是使积分归零的概率

方程中每个状态的计算都要dp[0]，硬推是不行的。。。

设dp[i]=A[i]*dp[0]+B[i]

带入第一个公式得

dp[i]=∑(A[i+k]*dp[0]*p[k]+B[i+k]*p[k])+dp[0]*p[0]+1

∴dp[i]=(∑(A[i+k]*p[k])+p0)*dp[0] + ∑(B[i+k]*p[k])+1

∴A[i]=∑(A[i+k]*p[k])+p0    B[i]=∑(B[i+k]*p[k])+1

这样先地推出A[0]和B[0]，dp[0]=B[0]/(1-A[0])

**AC代码：**

写的挺搓的。。。
<pre class="lang:c++ decode:true " title="ZOJ3593 One Person Game">#include &lt;cstdio&gt;
#include &lt;cstring&gt;
#include &lt;iostream&gt;
#include &lt;algorithm&gt;
#include &lt;cstdlib&gt;
#include&lt;cstring&gt;
#include&lt;assert.h&gt;
#include&lt;stack&gt;
#include&lt;set&gt;
#include&lt;queue&gt;
#include&lt;map&gt;
#include&lt;set&gt;
#define FS first
#define SE second
#define lson (o &lt;&lt; 1), L, M
#define rson ((o &lt;&lt; 1) | 1), M + 1, R
#define lc (o &lt;&lt; 1)
#define rc ((o &lt;&lt; 1) | 1)

using namespace std;

typedef long long LL;
const int DEG = 20;
const double eps = 1e-7;
const int MAXN = 500 + 100;

double A[MAXN], B[MAXN];
map&lt;int, double&gt; go;

int main() {
    int T;
    scanf("%d", &amp;T);
    int n, k1, k2, k3, a, b, c;
    while(T--) {
        scanf("%d%d%d%d%d%d%d", &amp;n, &amp;k1, &amp;k2, &amp;k3, &amp;a, &amp;b, &amp;c);
        memset(A, 0, sizeof A);
        memset(B, 0, sizeof B);
        go.clear();
        for(int i = 1 ; i &lt;= k1 ; i++)
            for(int j = 1 ; j &lt;= k2 ; j++)
                for(int k = 1 ; k &lt;= k3 ; k++) {
                    go[i + k + j] += (1.0 / k1) * (1.0 / k2) * (1.0 / k3);
                }

        go[a + b + c] -= (1.0 / k1) * (1.0 / k2) * (1.0 / k3);
        double p0 = (1.0 / k1) * (1.0 / k2) * (1.0 / k3);
        for(int i = n ; i &gt;= 0 ; i--) {
            for(map&lt;int, double&gt;::iterator it = go.begin() ; it != go.end() ; ++it) {
                A[i] += A[i + it-&gt;FS] * it-&gt;SE;
                B[i] += B[i + it-&gt;FS] * it-&gt;SE;
            }
            A[i] += p0;
            B[i] += 1;
        }
        printf("%.10f\n", B[0] / (1 - A[0]));
    }
    return 0;
}
</pre>
&nbsp;