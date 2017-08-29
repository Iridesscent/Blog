---
title: Gym 100803G 线段树
tags:
  - Interval tree
  - Data structure
id: 91
categories:
  - Algorithm
date: 2016-02-20 22:53:31
---

好长时间前做的题，来补一下题解。

给出括号化的序列，每次改变一个括号方向，求出下标p，是的改变p处的括号方向可以使括号化仍然成立，且p最小。

保证括号化看似和线段树没有联系，我们可以把括号"("表示为1，把括号")"表示为-1，则保证括号化的充要条件就是使数字序列前缀和始终大于等于零

用线段树维护前缀和

查询有两种情况

*   "(" 变成 ")"  这种情况我们只需找到最左边的一个")"，就是答案。(可以用一个set维护所有的")"的位置)
*   ")" 变成 "(" 这种情况我们要找一个最左边的“（”变成”）“，也就是把p处及以后的前缀和都减2，且不能出现负的前缀和。
于是我们就要借助线段树查询前缀和小于2的最右边节点，这样把区间[p + 1, n]减二之后才不会出现负值。

用线段树维护区间最小值，也就是区间最小前缀和。线段树能往右走就往又走，注意查询过程中区间边界的特判处理。
<pre class="lang:c++ decode:true " title="Gym 100803G 线段树">#include&lt;cstdio&gt;
#include&lt;algorithm&gt;
#include&lt;cstring&gt;
#include&lt;set&gt;
#include&lt;iostream&gt;
#define lson o &lt;&lt; 1, L, M
#define rson (o &lt;&lt; 1) | 1, M + 1, R
using namespace std;
const int MAXN = 300010;
const int INF = 0x3f3f3f3f;
char str[MAXN];
int num[MAXN];
int minv[MAXN &lt;&lt; 2];
int addv[MAXN &lt;&lt; 2];
int len;
void pushup(int o, int L, int R) {
    minv[o] = 0;
    if(R &gt; L) {
        minv[o] = min(minv[o &lt;&lt; 1], minv[(o &lt;&lt; 1) | 1]);
    }
    minv[o] += addv[o];
}
void update(int p, int v, int o, int L, int R) {
    if(p == L &amp;&amp; p == R) {
        addv[o] = v;
        minv[o] = 0;
    } else {
        int M = L + (R - L) / 2;
        if(p &lt;= M) update(p, v, lson);
        else update(p, v, rson);

    }pushup(o, L, R);
}
void add(int l, int r, int v, int o, int L, int R) {
    if(l &lt;= L &amp;&amp; r &gt;= R) {
        addv[o] += v;
    } else {
        int M = L + (R - L) / 2;
        if(l &lt;= M)add(l, r, v, lson);
        if(r &gt; M) add(l, r, v, rson);
    }
    pushup(o, L, R);
}
int query_min(int l ,int r, int add, int o, int L, int R) {
    if(l &lt;= L &amp;&amp; r &gt;= R) {
        return minv[o] + add;
    } else {
        int M = L + (R - L) / 2;
        int res = INF;
        if(l &lt;= M) res = min(res, query_min(l, r, add + addv[o],lson));
        if(r &gt; M) res = min(res, query_min(l, r, add + addv[o], rson));
        return res;
    }
}
int query(int l, int r, int add, int o, int L, int R) { //cout &lt;&lt; L &lt;&lt; "!!!" &lt;&lt; R &lt;&lt; endl;
    if(L == R) {
        if(L == 1) {
            if(minv[o] + add &gt;= 2) return 0;
            return L;
        } else {
            return L;
        }
    }
    int M = L + (R - L) / 2;
    if(r &lt; M + 1) return query(l, r, add + addv[o], lson);
    int minr = query_min(M + 1, min(r, R), 0, 1, 1, len);
    if(minr &lt; 2) return query(l, r, add + addv[o], rson);
    return query(l, r, add + addv[o], lson);
}
int main() {
    int n, q; set&lt;int&gt; fir;
    scanf("%d%d", &amp;n, &amp;q);
    scanf("%s", str + 1);
    len = n;
    for(int i = 1 ; i &lt;= len ; i++)
        if(str[i] == '(') num[i] = 1;
        else {
            num[i] = -1;
            fir.insert(i);
        }
    for(int i = 1 ; i &lt;= len ; i++) num[i] += num[i - 1];
    for(int i = 1 ; i &lt;= len ; i++) update(i, num[i], 1, 1, len);
    //cout &lt;&lt; query_min(1, 1, 0, 1, 1, len) &lt;&lt; "!!" &lt;&lt; endl;
    for(int i = 0 ; i &lt; q ; i++) {
        //cout &lt;&lt; str + 1 &lt;&lt; endl;
        int pos; scanf("%d", &amp;pos);
        if(str[pos] == '(') {
            str[pos] = ')';
            fir.insert(pos);
            int pp = *fir.begin();
            fir.erase(fir.begin());
            add(pos, len, -2, 1, 1, len);
            add(pp, len, 2, 1, 1, len);
            printf("%d\n", pp);
            str[pp] = '(';
        } else { // str[pos] == ')'
            str[pos] = '(';
            add(pos, len, 2, 1, 1, len);
            fir.erase(pos);
            int pp = query(1, pos, 0, 1, 1, len);
            //cout &lt;&lt; pp &lt;&lt; "~~" &lt;&lt; endl;
            pp++;
            str[pp] = ')';
            fir.insert(pp);
            add(pp, len, -2, 1, 1, len);
            printf("%d\n", pp);
        }
    }
    return 0;
}</pre>
&nbsp;