---
title: HDU5572 2015年ACM/ICPC上海现场赛1001/A
id: 53
tags:
  - Geometry
date: 2016-02-09 12:51:16
categories:
  - Algorithm
---

醉了，，现场读错题了，分反弹和不反弹两种情况讨论就好了，加点特判
<pre class="lang:c++ decode:true ">#include&lt;iostream&gt;
#include&lt;cstdio&gt;
#include&lt;cstring&gt;
#include&lt;queue&gt;
#include&lt;map&gt;
#include&lt;set&gt;
#include&lt;cmath&gt;
using namespace std;
typedef long long LL;
const double eps = 1e-8;
int sgn(double x) {
    if(fabs(x) &lt; eps) return 0;
    if(x &lt; 0) return -1;
    else return 1;
}
struct Point {
    double x, y;
    Point(double x = 0, double y = 0) : x(x), y(y) {}
    Point operator - (const Point&amp; t) const {
        return Point(x - t.x, y - t.y);
    }
    double operator ^ (const Point&amp; t) const {
        return x * t.y - y * t.x;
    }
    double operator * (const Point&amp; t) const {
        return x * t.x + y * t.y;
    }
    Point operator * (const double&amp; k) const {
        return Point(x * k, y * k);
    }
    Point operator / (const double&amp; k) const {
        return Point(x / k, y / k);
    }
    Point operator + (const Point&amp; t) const {
        return Point(x + t.x, y + t.y);
    }
    double distance(Point p) {
        return hypot(x - p.x, y - p.y);
    }
    double len() {
        return hypot(x, y);
    }
    double len2() {
        return x * x + y * y;
    }
    Point trunc(double r) {
        double l = len();
        if(!sgn(l)) return *this;
        r /= l;
        return Point(x * r, y * r);
    }
};
struct Line {
    Point s, e;
    Line() {}
    Line(Point s, Point e) {
        this-&gt;s = s; this-&gt;e = e;
    }
    double length() {
        return sqrt((s.x - e.x) * (s.x - e.x) + (s.y - e.y) * (s.y - e.y));
    }
    int relation(Point p) {
        int c = sgn((p - s) ^ (e - s));
        if(c &lt; 0) return 1;
        else if(c &gt; 0) return 2;
        else return 3;
    }
    double dispointtoline(Point p) {
        return fabs((p - s) ^ (e - s)) / length();
    }
    double dispointtoseg(Point p) {
        if(sgn((p - s) * (e - s)) &lt; 0 || sgn((p - e) * (s - e)) &lt; 0)
            return min(p.distance(s), p.distance(e));
        return dispointtoline(p);
    }
    Point lineprog(Point p) {
        return s + (((e - s) * ((e - s) * (p - s))) / ((e - s).len2()));
    }
    Point symmetrypoint(Point p) {
        Point q = lineprog(p);
        return Point(2 * q.x - p.x, 2 * q.y - p.y);
    }
};
struct Circle {
    Point p;
    double r;
    Circle(double x, double y, double r) {
        p  = Point(x, y);
        this-&gt;r = r;
    }
    int relationline(Line v) {
        double dst = v.dispointtoline(p);
        if(sgn(dst - r) &lt; 0) return 2;
        else if(sgn(dst - r) == 0) return 1;
        else return 0;
    }
    int relationseg(Line v) {
        double dst = v.dispointtoseg(p);
        if(sgn(dst - r) &lt; 0) return 2;
        else if(sgn(dst - r) == 0) return 1;
        else return 0;
    }
    int pointcrossline(Line v, Point&amp; p1, Point&amp; p2) {
        if(!(*this).relationline(v)) return 0;
        Point a = v.lineprog(p);
        double d = v.dispointtoline(p);
        d = sqrt(r * r - d * d);
        if(sgn(d) == 0) {
            p1 = a; p2 = a;
            return 1;
        }
        p1 = a + (v.e - v.s).trunc(d);
        p2 = a - (v.e - v.s).trunc(d);
        return 2;
    }
};
int main() {
    int T; scanf("%d", &amp;T);
    int ca(1);
    while(T--) {
        int ox, oy, r;
        int ax, ay, vx, vy;
        int bx, by;
        scanf("%d%d%d", &amp;ox, &amp;oy, &amp;r);
        scanf("%d%d%d%d", &amp;ax, &amp;ay, &amp;vx, &amp;vy);
        scanf("%d%d", &amp;bx, &amp;by);
        Line l(Point(ax, ay), Point(ax + vx, ay + vy));
        Circle c(ox, oy, r);
        Point d(bx, by);
        Line t(Point(ax, ay), Point(bx, by));
        if(l.relation(d) == 3 &amp;&amp; c.relationseg(t) &lt;= 1 &amp;&amp; sgn(Point(bx - ax, by - ay) ^ Point(vx, vy)) == 0) printf("Case #%d: Yes\n", ca++);
        else {
            Point a, b;
            if(c.pointcrossline(l, a, b) != 2) printf("Case #%d: No\n", ca++);
            else {
                Point cut;
                if(a.distance(Point(ax, ay)) &gt; b.distance(Point(ax, ay))) cut = b;
                else cut = a;
                Line mid(cut, c.p);
                Point en = mid.symmetrypoint(Point(ax, ay));
                Line aft(cut, en);
                if(aft.e.distance(d) &gt; aft.s.distance(d)) swap(aft.s, aft.e);
                if(sgn((aft.e - aft.s) ^ Point(d.x - cut.x, d.y - cut.y)) == 0) printf("Case #%d: Yes\n", ca++);
                else printf("Case #%d: No\n", ca++);
            }
        }
    }
    return 0;
}</pre>
&nbsp;