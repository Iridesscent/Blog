---
title: '[java] 如何写出一个完美的equals方法'
id: 326
categories:
  - Java
date: 2016-12-02 18:05:40
tags:
---

1.  Name explicit parameter obj **of type Object**.
2.  Test weather this happens to be identical to obj.
<pre class="lang:java decode:true">if(this == obj) return true;</pre>

3.  Test weather obj is null and return false it is. This test is required.
4.  Compare the classes of this and obj. If the semantics of equals can be change in subclasses, use getClass test:
<pre class="lang:java decode:true">if(getClass() != obj.getClass()) return false;</pre>
If same semantics holds for all subclasses, you can use an instanceof test:
<pre class="lang:java decode:true">if(!(obj instanceof ClassName)) return false;
</pre>

5.  Cast obj to a variable of your class type:
<pre class="lang:default decode:true">ClassName other = (ClassName) obj;</pre>

6.  Now compare the fields, as required by your notion of equality. Use == for primitive type fields, Objects.equals for object fields. Return true if all fields match, false otherwise.
7.  If you redefine equals in a subclass, include a call to super.equals(other).