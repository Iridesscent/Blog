---
title: Redis源码分析-序言
date: 2017-08-31 20:03:09
categories:
  - Redis源码分析
tags:
  - Redis
---
# 序言
从今年四月份开始，实习有了着落，就开始陆陆续续阅读Redis源码，经常被那简洁而优雅地源码所震撼。于是想写点东西，一是作为自己度源码的一个总结，二是希望给同样阅读源码的同学一点帮助。此外，由于本人水平有限，如有错误，欢迎指正。  

我用的Redis版本为3.2.9，操作系统为Linux
# 目录
Redis是我读的第一个比较大的开源项目，我刚刚开始读源码的时候，有一种无从下手的感觉。最后还是用工具分析了一下代码的依赖关系，顺着依赖关系读下来了。目录的顺序也大致等于我的阅读顺序。   
读代码之前要对linux系统调用，网络，数据结构算法有一定了解。
## 1. 内存管理
## 2. 基础数据结构
#### 2.1 Dynamic string
#### 2.2 Dictionary
#### 2.3 intset
#### 2.4 list
#### 2.5 skip list
#### 2.6 zip list
## 3. 网络IO
## 4. 事件驱动
# 参考资料


# Copyright

>MIT License
>
>Copyright (c) 2017 Donglin Zhuang | dongliniris@gmail.com
>
>Permission is hereby granted, free of charge, to any person obtaining a copy
>of this software and associated documentation files (the "Software"), to deal
>in the Software without restriction, including without limitation the rights
>to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
>copies of the Software, and to permit persons to whom the Software is
>furnished to do so, subject to the following conditions:
>
>The above copyright notice and this permission notice shall be included in all
>copies or substantial portions of the Software.
>
>THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
>IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
>FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
>AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
>LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
>OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
>SOFTWARE.

# 联系我
Email: dongliniris@gmail.com