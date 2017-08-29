---
title: HDU5790 Prefix (字典树+主席树) 2016 Multi-University Training Contest 5
id: 261
tags:
  - Data structure
  - Persistent data structure
date: 2016-08-04 18:57:20
categories:
  - Algorithm
---

[HDU5790 传送门](http://acm.hdu.edu.cn/showproblem.php?pid=5790)

给出n个字符串，q个强制在线查询，每次输出第l~r个字符串有多少不重复的前缀。

将n个字符串依次插入字典树，维护每个前缀最后出现在第几个字符串中

假设当前正在插入第i个字符串中的第j个字符，入股以j结尾的前缀以前没在主席树中出现过，则第i个线段树的第i个位置加1，否则如果上次出现在第k个字符串中，则第i个线段树k位置减1，第i个位置加1。

这样，第i个线段树维护的就是1~i个字符串每个位置有多少个后缀出现最后一次，这样对于l，r的查询就等于在第r个主席树中求区间和[l, r]。

AC代码：
<pre class="lang:c++ decode:true ">#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;algorithm&gt;
#include&lt;string&gt;
#include&lt;queue&gt;
#include&lt;vector&gt;
#include&lt;set&gt;
#include&lt;vector&gt;
#include&lt;map&gt;
#define FS first
#define SE second
using namespace std;
typedef long long LL;
typedef pair&lt;int, int&gt; PII;
const int MAXN = 100000 + 10;
const int M = MAXN * 60;
int T[MAXN], lson[M], rson[M], C[M];
int tot;
int totTire;
int ch[MAXN][26];
int last[MAXN];
char str[MAXN];

int build(int l, int r) {
    int root = tot++;
    C[root] = 0;
    if(l != r) {
        int mid = (l + r) &gt;&gt; 1;
        lson[root] = build(l, mid);
        rson[root] = build(mid + 1, r);
    }
    return root;
}

int update(int root, int pos, int val, int l, int r) {
    int newroot = tot++;
    int tmp = newroot;
    C[newroot] = C[root] + val;
    while(l &lt; r) {
        int mid = (l + r) &gt;&gt; 1;
        if(pos &lt;= mid) {
            lson[newroot] = tot++;
            rson[newroot] = rson[root];
            newroot = lson[newroot];
            root = lson[root];
            r = mid;
        } else {
            rson[newroot] = tot++;
            lson[newroot] = lson[root];
            newroot = rson[newroot];
            root = rson[root];
            l = mid + 1;
        }
        C[newroot] = C[root] + val;
    }
    return tmp;
}

int newnood() {
    memset(ch[totTire], 0, sizeof ch[totTire]);
    last[totTire] = 0;
    return totTire++;
}

void Insert(char s[], int pos, int n) {
    int len = strlen(s);
    int now = 0;
    int preroot = T[pos - 1];
    for(int i = 0 ; i &lt; len ; i++) {
        int p = s[i] - 'a';
        if(ch[now][p] == 0) {
            ch[now][p] = newnood();
            last[ch[now][p]] = pos;
            T[pos] = update(preroot, pos, 1, 1, n);
            preroot = T[pos];
        } else {
            T[pos] = update(preroot, last[ch[now][p]], -1, 1, n);
            preroot = T[pos];
            T[pos] = update(preroot, pos, 1, 1, n);
            preroot = T[pos];
            last[ch[now][p]] = pos;
        }
        now = ch[now][p];
    }
}

int query(int l, int r, int o, int L, int R) {
    if(l &lt;= L &amp;&amp; r &gt;= R) return C[o];

    int M = L + (R - L) / 2;
    int res = 0;
    if(l &lt;= M) res += query(l, r, lson[o], L, M);
    if(r &gt; M) res += query(l, r, rson[o], M + 1, R);
    return res;
}

int main() {
    int n, q;
    while(scanf("%d", &amp;n) == 1) {
        tot = 0;
        totTire = 0;
        newnood();
        T[0] = build(1, n);

        for(int i = 1 ; i &lt;= n ; i++) {
            scanf("%s", str);
            Insert(str, i, n);
        }

        scanf("%d", &amp;q);
        int la = 0;
        int l, r;
        while(q--) {
            scanf("%d%d", &amp;l, &amp;r);
            int ll = min((la+l)%n,(la+r)%n)+1;
            int rr = max((la+l)%n,(la+r)%n)+1;
            printf("%d\n", la = query(ll, rr, T[rr], 1, n));
        }
    }
    return 0;
}
</pre>
&nbsp;