# 包围你的 Web 应用——通过 HTTP 进行负载测试

> 原文：<https://www.sitepoint.com/besiege-your-web-application-load-testing-over-http/>

在对系统进行性能调优时，持续监控您所做的任何更改的效果是非常重要的。应该使用工具来提供对一致度量的连续访问。

Web 应用程序也不例外。然而，它们可能是复杂的动物，因此您可能会受益于许多不同的工具层。如果您正在构建一个功能丰富的框架，您可能会在代码中访问计时器和计数器。这对于告诉您应用程序的不同阶段需要多长时间来执行非常有用，并且可以对外部系统的调用(如 SQL 查询)进行计数和计时。

然而，有时您需要一个更宏观的视角——一个更准确地代表您的用户体验的性能的基准，并且包含服务他们的请求的所有方面，包括网络延迟和 HTTP 服务器开销。

围攻是 T2 JoeDog 软件公司提供的免费 UNIX (Linux / Mac OS 等)工具。

> Siege 是一个 http 回归测试和基准测试工具。它的设计目的是让 web 开发人员测量他们的代码在压力下的性能，看看它在互联网上能承受多大的负载。围攻支持基本认证，cookies，HTTP 和 HTTPS 协议。它允许用户使用可配置数量的并发模拟用户访问 web 服务器。这些用户将网络服务器置于“围攻之下”

围攻可以通过 MacPorts 安装在 Mac OS 上，或者如果你有 T2 XCode 和喜欢命令行的话，很容易从源代码编译。如果你在 Windows PC 上，你可以在 [Cygwin](http://www.cygwin.com/) 下运行它，或者使用免费的 [VMware Server](http://www.vmware.com/products/server/) 安装一个 Linux 发行版如 [Ubuntu](http://www.ubuntu.com/) 作为虚拟机。

浏览一下文档和~/。siegerc 文件和围攻-帮助输出以了解它能做什么。特别是，[URL 文件](http://www.joedog.org/Siege/Manual#a05)让您可以很好地控制如何测试 web 应用程序的不同部分。

我将留给你一个非常基本的例子来开始这个话题:

```
# siege -b -c5 -r20 http://test.ubuntu.vm/static.html

** SIEGE 2.66
** Preparing 5 concurrent users for battle.
The server is now under siege..      done.
Transactions:                    100 hits
Availability:                 100.00 %
Elapsed time:                   0.18 secs
Data transferred:               0.01 MB
Response time:                  0.01 secs
Transaction rate:             555.56 trans/sec
Throughput:                     0.07 MB/sec
Concurrency:                    4.83
Successful transactions:         100
Failed transactions:               0
Longest transaction:            0.03
Shortest transaction:           0.00 
```

```
# siege -b -c5 -r20 http://test.ubuntu.vm/randomsleep.php

** SIEGE 2.66
** Preparing 5 concurrent users for battle.
The server is now under siege..      done.
Transactions:                    100 hits
Availability:                 100.00 %
Elapsed time:                   2.95 secs
Data transferred:               0.01 MB
Response time:                  0.07 secs
Transaction rate:              33.90 trans/sec
Throughput:                     0.00 MB/sec
Concurrency:                    2.36
Successful transactions:         100
Failed transactions:               0
Longest transaction:            2.69
Shortest transaction:           0.00 
```

## 分享这篇文章