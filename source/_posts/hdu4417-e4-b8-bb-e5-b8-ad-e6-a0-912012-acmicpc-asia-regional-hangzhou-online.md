---
title: hdu4417 (主席树)2012 ACM/ICPC Asia Regional Hangzhou Online
id: 263
tags:
  - Data structure
  - Persistent data structure
date: 2016-08-04 19:33:40
caregories:
  - Algorithm
---

去检查询l，r之间有多少数小于等于h
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
const int M = MAXN * 30;
int T[MAXN], lson[M], rson[M], C[M];
int tot;
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

int a[MAXN];
int Hash[MAXN];
int cnt;

int query(int l, int r, int lroot, int rroot, int L, int R) { //printf("%d %d %d %d\n", lroot, rroot, L, R);
    if(l &gt; r) return 0;
    if(L &gt;= l &amp;&amp; R &lt;= r) return C[rroot] - C[lroot];
    int M = L + (R - L) / 2;
    int res = 0;
    if(l &lt;= M) res += query(l, r, lson[lroot], lson[rroot], L, M);
    if(r &gt; M) res += query(l, r, rson[lroot], rson[rroot], M + 1, R);
    return res;
}

int main() {
    int TT;
    scanf("%d", &amp;TT);
    int ca = 1;
    while(TT--) {
        int n, q;
        scanf("%d%d", &amp;n, &amp;q);
        tot = 0;
        cnt = 0;
        T[0] = build(1, n);
        for(int i = 1 ; i &lt;= n ; i++) {
            scanf("%d", &amp;a[i]);
            Hash[cnt++] = a[i];
        }
        sort(Hash, Hash + cnt);
        cnt = unique(Hash, Hash + cnt) - Hash;
        for(int i = 1 ; i &lt;= n ; i++) {
            T[i] = update(T[i - 1], lower_bound(Hash, Hash + cnt, a[i]) - Hash + 1, 1, 1, n);
        }

        printf("Case %d:\n", ca++);
        while(q--) {
            int l, r, h;
            scanf("%d%d%d", &amp;l, &amp;r, &amp;h);
            l++; r++;
            if(h &lt; Hash[0]) {
                printf("0\n");
                continue;
            }
            int hh = lower_bound(Hash, Hash + cnt, h) - Hash + 1;
            if(Hash[hh - 1] != h) hh--;
            printf("%d\n", query(1, hh, T[l - 1], T[r], 1, n));
        }
    }
    return 0;
}
</pre>
&nbsp;