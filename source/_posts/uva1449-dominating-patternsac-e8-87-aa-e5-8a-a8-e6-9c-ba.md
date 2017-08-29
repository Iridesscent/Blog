---
title: UVA1449 - Dominating Patterns(AC自动机)
categories:
  - Algorithm
id: 130
tags:
  - Aho-Corasick automation
  - String Algorithms
date: 2016-02-24 17:44:48
---

**题目链接：**[UVA1449 - Dominating Patterns](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&amp;Itemid=8&amp;category=505&amp;page=show_problem&amp;problem=4195)

**题目大意：**给出n个模板串，求哪个模板串在目标串里出现次数最多

**解题思路：**AC自动机模板题，输入期间输出顺序错误wa了一次。

[Aho-Corasick automation（AC自动机）](https://iridescent.com.cn/index.php/125.html)

**AC代码：**
<pre class="lang:c++ decode:true " title="UVA1449 - Dominating Patterns">#include&lt;cstdio&gt;
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
const int MAXN1 = 150 * 70 + 10;
const int MAXN2 = 1000000 + 10;
const double eps = 1e-7;

char input[160][80];
int ans[160];

struct Tire {
    int next[MAXN1][26], fail[MAXN1], end[MAXN1];
    int dep[MAXN1];
    char str[80];
    map&lt;int, int&gt; con;
    int root, L;
    int ma, pos;
    int newnode() {
        for(int i = 0 ; i &lt; 26 ; i++)
            next[L][i] = -1;
        end[L++] = 0;
        return L - 1;
    }
    void init() {
        ma = 0;
        L = 0;
        root = newnode();
        memset(dep, 0, sizeof dep);
        con.clear();
    }
    void insert(char buf[]) {
        int len = strlen(buf);
        int now = root;
        for(int i = 0 ; i &lt; len ; i++) {
            if(next[now][buf[i] - 'a'] == -1) {
                next[now][buf[i] - 'a'] = newnode();
                dep[next[now][buf[i] - 'a']] = i + 1;
            }
            now = next[now][buf[i] - 'a'];
        }
        end[now]++;
    }
    void build() {
        queue&lt;int&gt; Q;
        fail[root] = root;
        for(int i = 0 ; i &lt; 26 ; i++) {
            if(next[root][i] == -1)
                next[root][i] = root;
            else  {
                fail[next[root][i]] = root;
                Q.push(next[root][i]);
            }
        }
        while(!Q.empty()) {
            int now = Q.front();
            Q.pop();
            for(int i = 0 ; i &lt; 26 ; i++) {
                if(next[now][i] == -1)
                    next[now][i] = next[fail[now]][i];
                else {
                    fail[next[now][i]] = next[fail[now]][i];
                    Q.push(next[now][i]);
                }
            }
        }
    }

    int dfs(int id, int cnt, int en) {
        if(en == id) return cnt;
        for(int i = 0 ; i &lt; 26 ; i++) {
            if(next[id][i] &gt; 0 &amp;&amp; dep[next[id][i]] &gt; dep[id]) {
                str[cnt] = 'a' + i;
                int len = dfs(next[id][i], cnt + 1, en);
                if(len) return len;
            }
        }
        return 0;
    }

    void slove(int id, int num) {
        con[id] += num;
    }

    int query(char buf[]) {
        int len = strlen(buf);
        int now = root;
        int res = 0;
        for(int i = 0 ; i &lt; len ; i++) {
            now = next[now][buf[i] - 'a'];
            int temp = now;
            while(temp != root) {
                res += end[temp];
                if(end[temp])
                    slove(temp, end[temp]);
                temp = fail[temp];
            }
        }
        return res;
    }
} ac;
char s[MAXN2];

int main() {
    int n;
    while(scanf("%d", &amp;n) == 1 &amp;&amp; n) {
        ac.init();
        memset(ans, 0, sizeof ans);
        for(int i = 0 ; i &lt; n ; i++) {
            scanf("%s", input[i]);
            ac.insert(input[i]);
        }
        ac.build();
        scanf("%s", s);
        ac.query(s);
        int ma = 0;
        for(map&lt;int, int&gt;::iterator i = ac.con.begin() ; i != ac.con.end() ; ++i) {
            ma = max(ma, i-&gt;second);
        }

        printf("%d\n", ma);
        for(map&lt;int, int&gt;::iterator i = ac.con.begin() ; i != ac.con.end() ; ++i) {
            if(ma == i-&gt;second) {
                int l = ac.dfs(0, 0, i-&gt;first);
                ac.str[l] = 0;
                for(int i = 0 ; i &lt; n ; i++) {
                    if(strcmp(input[i], ac.str) == 0) ans[i] = ma;
                }
            }
        }
        for(int i = 0 ; i &lt; n ; i++) if(ans[i] == ma) printf("%s\n", input[i]);
    }
    return 0;
}
</pre>
&nbsp;

&nbsp;