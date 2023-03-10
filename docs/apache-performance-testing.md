# Apache 性能测试

> 原文：<https://www.sitepoint.com/apache-performance-testing/>

我已经开始使用 ab——包括在 Apache 中，而不是一个新工具。ab 允许基于用户负载对网页、站点甚至动态脚本进行性能测试。

ab 支持每秒创建一个或多个请求，并提供一个很好的结果报告。

一些关键选项，可以通过在任何安装了 Apache 的 Linux 或 OS X 系统上运行“man ab”来查看。

选项:

-k:这将启用 HTTP KeepAlive，以便在一个会话中执行多个请求。

-n:要执行的请求数，如 150。它们将一个接一个地发行，直到完成为止。

-t:在时间范围内发布执行请求的时间限制。

-c:允许多个请求同时进行，而不是一次一个。这有助于测试脚本性能。

-用冒号分隔的用户名和密码:如果您测试的站点需要登录，这将用于发送身份验证。

还有其他几个选项，包括通过代理服务器工作，添加头和 cookies。

结果输出到命令行(下面的示例来自 OS X 本地主机 Apache 安装):

 `This is ApacheBench, Version 1.3d <$Revision: 1.73 $> apache-1.3
Copyright (c) 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Copyright (c) 1998-2002 The Apache Software Foundation, http://www.apache.org/`

 `基准测试本地主机(耐心点).....完成
服务器软件:Apache/1.3.33
服务器主机名:localhost
服务器端口:80

文件路径:/
文件长度:1456 字节

并发级别:1
测试时间:1.103 秒
完成请求:50
失败请求:0
管道中断错误:0
总传输量:93500 字节
HTML 传输量:72800 字节
每秒请求数:45.33[#/秒](平均值)
每请求时间:22.06 [ms](平均值)
每请求时间:22.06 [ms](平均值，跨所有并发请求)【平均】

连接次数(ms)
min mean[+/-SD]median max
连接:0 0 0.3 0 3
处理:12 21 60.0 12 438
等待:12 21 59.8 12 436
总计:12 21 60.4 12 441` 

`Percentage of the requests served within a certain time (ms)
50% 12
66% 13
75% 13
80% 13
90% 14
95% 17
98% 441
99% 441
100% 441 (last request)`

您也可以使用'-e filename '选项将结果输出到 CSV 文件。-w '将结果输出到 HTML 表中。有能力在 PHP 中利用 ab，以便从 web 浏览器生成负载测试并返回结果，尽管我还没有尝试过。在转移到生产环境之前，我主要在开发服务器上运行 ab——然后在发布之前在生产环境中再次运行它。

## 分享这篇文章