---
title: POJ2452 Sticks Problem（RMQ+二分）
id: 183
categories:
  - Algorithm
date: 2016-04-27 17:29:08
tags:
---

[Sticks Problem](http://poj.org/problem?id=2452)[POJ2452 Sticks Problem](http://poj.org/problem?id=2452)

1.  枚举l，二分找到r使得r最大且[l, r]之间最小值大于等于num[i]
2.  在[l,r]之间二分找到m，使得m最大且num[m]为[l,r]之间最大值
3.  ans = max(ans, m - l)
AC代码：
<pre class="lang:c++ decode:true ">#include&lt;vector&gt;
#include&lt;queue&gt;
#include&lt;algorithm&gt;
#include&lt;map&gt;
#include&lt;string&gt;
#include&lt;cstring&gt;
#include&lt;cmath&gt;
#include&lt;cstdlib&gt;
#include&lt;cstdio&gt;

using namespace std;

const int MAXN = 50000 + 10;
int num[MAXN];

struct RMQmax {
    int dp[MAXN][20];
    int mm[MAXN];
    void init(int n, int b[]) {
        mm[0] = -1;
        for(int i = 1 ; i &lt;= n ; i++) {
            mm[i] = ((i &amp; (i - 1)) == 0)?mm[i - 1] + 1:mm[i - 1];
            dp[i][0] = b[i];
        }
        for(int j = 1 ; j &lt;= mm[n] ; j++)
            for(int i = 1 ; i + (1 &lt;&lt; j) - 1 &lt;= n ; i++)
                dp[i][j] = max(dp[i][j - 1], dp[i + (1 &lt;&lt; (j - 1))][j - 1]);
    }
    int rmq(int x, int y) {
        if(x &gt; y) return 0;
        int k = mm[y - x + 1];
        return max(dp[x][k], dp[y - (1 &lt;&lt; k) + 1][k]);
    }
} ma;

struct RMQmin {
    int dp[MAXN][20];
    int mm[MAXN];
    void init(int n, int b[]) {
        mm[0] = -1;
        for(int i = 1 ; i &lt;= n ; i++) {
            mm[i] = ((i &amp; (i - 1)) == 0)?mm[i - 1] + 1:mm[i - 1];
            dp[i][0] = b[i];
        }
        for(int j = 1 ; j &lt;= mm[n] ; j++)
            for(int i = 1 ; i + (1 &lt;&lt; j) - 1 &lt;= n ; i++)
                dp[i][j] = min(dp[i][j - 1], dp[i + (1 &lt;&lt; (j - 1))][j - 1]);
    }
    int rmq(int x, int y) {
        int k = mm[y - x + 1];
        return min(dp[x][k], dp[y - (1 &lt;&lt; k) + 1][k]);
    }
} mi;
int find(int l, int r) {
    if(l == r) return l;
    int m = l + (r - l) / 2;
    if(ma.rmq(l, m) &gt; ma.rmq(m + 1, r)) return find(l, m);
    else return find(m + 1, r);
}

int main() {
    int n;
    while(scanf("%d", &amp;n) == 1) {
        for(int i = 1 ; i &lt;= n ; i++)
            scanf("%d",  &amp;num[i]);
        mi.init(n, num);
        ma.init(n, num);
        int ans = 0;
        for(int i = 1 ; i &lt;= n ; i++) {
            int l = i, r = n;
            while(l &lt;= r) {
                int m = l + (r - l) / 2;
                if(mi.rmq(i, m) &gt;= num[i]) l = m + 1;
                else r = m - 1;
            }
            ans = max(ans, find(i, r) - i);
        }
        printf("%d\n", ans == 0?-1:ans);
    }
    return 0;
}
/*
4
0 1 1
0 2 1
0 3 1
1
0 1 2
*/
</pre>
题解还写了另一种方法，并没有看懂怎么分治的。。。:

本题要求对于给定的正整数序列Ak ( 1 &lt;= k &lt;= n)，求最大的下标差j-i,使得位于位置i 和 j 之间的所有整数值大于Ai小于Aj。

一般的算法很容易达到O(N^2)的复杂度，但是对于这里的数据量，显然是不合适的。我们如下考虑：

对于给定的序列A[low…up]，先求出最大数的下标maxIndex和最小数的下标minIndex。

1． 如果minIndex &lt; maxIndex，很容易由此将此数列分成三部分，A[low…minIndex-1], A[minIndex…maxIndex], A[maxIndex+1…up].显然中间部分minIndex, maxIndex满足条件，做如下跟新maxDifference = max(maxDifference, maxIndex-minIndex)。然后递归处理第一部分和第三部分。

2． 如果minIndex &gt; maxIndex，此数列依然可以分成三部A[low…maxIndex], A[maxIndex+1…minIndex-1], A[minIndex…up].依然可以对此三部分进行递归处理。

如果对于一般的数据，以上的分治可以达到O(nlogn)的复杂度，但是对于数列为递减的极端情况，以上算法依然高达平方的复杂度！考虑到连续递减序列的中间部分均不满足要求，我们可以做如下改进：对于最小的元素，我们从数列中去掉以其为结束的连续递减的一部分，对于最大的元素，去掉以其为开始的连续递减的一部分。可以达到优化的目的。

本题主要考虑由数据量估计出算法复杂度，然后采用分治的策略，同时采用基本的观察对算法进行合理的优化。