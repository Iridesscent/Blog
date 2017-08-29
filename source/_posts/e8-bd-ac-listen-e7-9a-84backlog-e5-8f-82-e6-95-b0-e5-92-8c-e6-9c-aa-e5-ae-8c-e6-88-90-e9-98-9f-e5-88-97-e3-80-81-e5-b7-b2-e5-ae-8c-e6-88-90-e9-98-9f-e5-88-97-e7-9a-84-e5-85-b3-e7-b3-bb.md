---
title: '[转] listen()的backlog参数和未完成队列、已完成队列的关系'
id: 350
categories:
  - Linux
date: 2017-03-31 15:52:37
tags:
  - Network
---

[listen()的backlog参数和未完成队列、已完成队列的关系](http://blog.chinaunix.net/uid-24782829-id-3456109.html)

listen函数是网络编程一个基本的函数，而且它的backlog参数却是和TCP协议息息相关的。我们来看一下一个backlog参数到底有多少秘密。

**一、   为了理解backlog参数，我们必须首先认识listen函数。**

&nbsp;
<pre class="lang:c++ decode:true">#include&lt;sys/socket.h&gt;
int listen(int sockfd, int backlog);</pre>

1.  listen函数仅由TCP服务器调用，它做两件事情：当socket函数创建一个套接口时，它被假设为一个主动套装口，也就是说，它是一个将调用connet发起连接的客户套接口。listen函数把一个未连接的套接口转换成一个被动套接口，指示内核应接受指向该套接口的连接请求。根据TCP状态转换图，调用listen导致套接口从CLOSED状态转换到LISTEN状态。
2.  本函数的第二个参数规定了内核应该为相应套接口排队的最大连接个数。
为了更好的理解backlog参数，我们必须认识到内核为任何一个给定的监听套接口维护两个队列：

1.  未完成连接队列（incomplete connection queue），每个这样的SYN分节对应其中一项：已由某个客户发出并到达服务器，而服务器正在等待完成相应的TCP三路握手过程。这些套接口处于SYN_RCVD状态。
2.  已完成连接队列（completed connection queue），每个已完成TCP三路握手过程的客户对应其中一项。这些套接口处于ESTABLISHED状态。
当来自客户的SYN到达时，TCP在未完成连接队列中创建一个新项，然后响应以三路握手的第二个分节：服务器的SYN响应，其中稍带对客户SYN的ACK（即SYN+ACK）。这一项一直保留在未完成连接队列中，直到三路握手的第三个分节（客户对服务器SYN的ACK）到达或者该项超时为止（曾经源自Berkeley的实现为这些未完成连接的项设置的超时值为75秒）。如果三路握手正常完成，该项就从未完成连接队列移到已完成连接队列的队尾。当进程调用accept时，已完成连接队列中的队头项将返回给进程，或者如果该队列为空，那么进程将被投入睡眠，直到TCP在该队列中放入一项才唤醒它。

**二、查看man手册。看看man listen怎么说**

![](http://blog.chinaunix.net/attachment/201212/30/24782829_1356852544vo3O.jpg)

可以看到<span lang="EN-US">man</span>手册是这样描述<span lang="EN-US">backlog</span>参数的：“<span lang="EN-US">backlog</span>参数确定了<span lang="EN-US">connection</span>队列可以增长的最大长度”，如果<span lang="EN-US">connection</span>队列已满（达到<span lang="EN-US">backlog</span>确定的长度），那么新的<span lang="EN-US">connection</span>请求到来时，客户端会得到一个<span lang="EN-US">ECONNREFUSED</span><span lang="EN-US"><span class="Apple-converted-space"> </span>error</span>，或者，如果底层协议支持重发，那么这个请求会被服务器忽略而使客户端重新发送<span lang="EN-US">connection</span>请求。

![](http://blog.chinaunix.net/attachment/201212/30/24782829_135685259555z5.jpg)

现在backlog用来确定已完成队列（完成三次握手等待accept）的长度，而不再是已完成队列和未完成连接队列之和（linux 2.2之前）。

未完成队列（incomplete connection queue）的长度现在由/proc/sys/net/ipv4/tcp_max_syn_backlog设置，在现在大多数最新linux内核都是默认512，这个设置有效的前提是系统的syncookies功能被禁用，如果系统的syncookies功能被启用，那么这个设置是无效的。Syncookies是在内核编译的时候设置的，查看syncookies是否启动：
<pre class="lang:sh decode:true">cat /proc/sys/net/ipv4/tcp_syncookies</pre>
如果是“1”说明已启用，为“0”说明未启用。

那么为syncookies是做什么的呢，为什么它会和未完成队列有关系。简单的说它是为防范SYN Flood攻击的设计。具体请参考“syncookies介绍”（http://baike.baidu.com/view/9033755.htm）。

继续看backlog，如果我们给listen的backlog参数设值超过了/proc/sys/net/core/somaxconn，那么backlog参数的值为自动被改写为/proc/sys/net/core/somaxconn的值，它的默认大小为128.

&nbsp;

**更多资料可以查看**

**1、 man tcp**

**2、 man listen**

**3、 《unix网络编程卷一》**

**4、 百度百科syncookies介绍：http://baike.baidu.com/view/9033755.htm**