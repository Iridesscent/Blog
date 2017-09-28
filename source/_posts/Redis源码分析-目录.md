---
title: Redis源码分析-序言
date: 2017-08-31 20:03:09
categories:
  - Redis源码分析
tags:
  - Redis
---
# 序言
从今年四月份开始，实习有了着落，抽出时间就开始陆陆续续阅读Redis源码，经常被那简洁而优雅地源码所震撼。于是想写点东西，一是作为自己度源码的一个总结，二是希望给同样阅读源码的同学一点帮助。此外，由于本人水平有限，如有错误，欢迎指正。  

我用的Redis版本为3.2.9，操作系统为Ubuntu
# 小白该如何读源码&如何阅读本文

# 目录
Redis是我读的第一个比较大的开源项目，我刚刚开始读源码的时候，有一种无从下手的感觉。最后还是用工具分析了一下代码的依赖关系，顺着依赖关系读下来了。目录的顺序也大致等于我的阅读顺序。   
读代码之前要对linux系统调用，网络，数据结构算法有一定了解，还有最重要的，要有耐心，坚持下去，共勉～
- 1. 内存管理
- 2. 基础数据结构
  - 1. Dynamic string
  - 2. Dictionary
  - 3. intset
  - 4. list
  - 5. skip list
  - 6. zip list
- 3. 事件驱动 
  - 1. ae_epoll.c
  - 2. ae.h/ae.c
- 4. 网络库
- 5. Database
- 6. Server
- 7. Cluster
- 8. 杂项

# 参考资料 

# Copyright
```
BSD 3-Clause License

Copyright (c) 2017, Donglin Zhuang | dongliniris@gmail.com
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of the copyright holder nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```
# 联系我
Email: dongliniris@gmail.com