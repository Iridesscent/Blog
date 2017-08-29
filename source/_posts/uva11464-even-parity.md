---
title: UVA11464 - Even Parity
id: 60
categories:
  - Algorithm
date: 2016-02-17 16:06:59
tags:
---

题目链接：[UVA11464 - Even Parity](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;page=show_problem&amp;category=456&amp;problem=2459&amp;mosmsg=Submission+received+with+ID+16859738)

很传统的思路

暴力第一行，然后从第二行开始一行一行的求解
<pre class="lang:c++ decode:true " title="UVA11464 - Even Parity">#include&lt;cstdio&gt;
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
const int MAXN = 18;
const double eps = 1e-7;

int mat[MAXN][MAXN];
int ansmat[MAXN][MAXN];
int ans;

int get(int r, int c) {
    return ansmat[r][c - 1] + ansmat[r][c + 1] + ansmat[r - 1][c] + ansmat[r + 1][c];
}

void calc(int add, int n) {
    for(int i = 2 ; i &lt;= n ; i++) {
        for(int j = 1 ; j &lt;= n ; j++) {
            ansmat[i][j] = mat[i][j];
        }
    }

    for(int i = 2 ; i &lt;= n ; i++) {
        for(int j = 1 ; j &lt;= n ; j++) {
            if(get(i - 1, j) % 2 != 0) {
                if(ansmat[i][j] == 0) {
                    ansmat[i][j] = 1;
                    add++;
                    if(add &gt;= ans) return;
                } else {
                    return;
                }
            }
        }
    }

    for(int i = 1 ; i &lt;= n ; i++) if(get(n, i) % 2 != 0) return;
    ans = min(ans, add);
}

void dfs(int d, int n, int add) {
    if(add &gt;= ans) return;
    if(d &gt; n) {
        calc(add, n);
        return;
    }

    ansmat[1][d] = mat[1][d];
    dfs(d + 1, n, add);

    if(mat[1][d] == 0) {
        ansmat[1][d] = 1;
        dfs(d + 1, n, add + 1);
    }

}

int main() {
    int T;
    scanf("%d", &amp;T);

    for(int cnt = 1 ; cnt &lt;= T ; cnt++) {
        int n;
        scanf("%d", &amp;n);
        memset(mat, 0, sizeof mat);
        memset(ansmat, 0, sizeof ansmat);

        for(int i = 1 ; i &lt;= n ; i++) {
            for(int j = 1 ; j &lt;= n ; j++) {
                scanf("%d", &amp;mat[i][j]);
            }
        }

        ans = 0x3f3f3f3f;
        dfs(1 ,n, 0);
        printf("Case %d: %d\n", cnt, ans == INF?-1:ans);

    }
    return 0;
}
</pre>
&nbsp;