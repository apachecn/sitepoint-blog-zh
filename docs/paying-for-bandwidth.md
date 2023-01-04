# 为带宽付费

> 原文：<https://www.sitepoint.com/paying-for-bandwidth/>

在某些情况下，您的主机提供商可能会向您收取带宽费用。在这种情况下，您可能希望能够构建自己的关于服务器总吞吐量的每日、每周和每月报告。

有几种方法可以做到这一点，然而，一些开源应用程序恰好是现成的，如果你愿意，可以直接放在你的 web 服务器上。

这些应用不仅可以测量多个端口上的流量(即不仅是端口 80，而且总吞吐量可以包括端口 21–FTP、22–SSH、23–Telnet、25/110–SMTP 和 POP 以及您管理的 web 应用的任何自定义端口),而且还可以监控磁盘空间、cpu 和内存利用率以及更多用于您自己的系统管理的内容。最后，这些应用程序使您能够执行简单的数学运算，根据您的以太网卡流量来验证总带宽。注意:这些应用程序都没有被认证为可用于计费目的，但是，我相信它们可以用来分析由您的 ISP 提供的带宽报告，用于托管计费。

MRTG(代表多路由器流量图示器)和 Cricket 可以轻松管理必要的数据采集和绘图，为您的系统提供易于使用的可视化报告。http://www.mrtg.org 已经预装了一些 Linux 发行版，或者可以从那里下载。在 http://cricket.sourceforge.net/的 T2 可以看到板球。

例如，在 Red Hat 发行版中，可以在/var/www/html/mrtg 找到安装的 MRTG，并且可以在/etc/mrtg 找到它的配置文件。在[http://people.ee.ethz.ch/~oetiker/webtools/mrtg/links.html](http://people.ee.ethz.ch/~oetiker/webtools/mrtg/links.html)这里，MRTG 有一个得到很好支持的社区，有很多网站可以查看提示、技巧和技术。

我积极使用 Cricket 来监控数据中心基础设施，包括集线器、路由器和交换机以及*Nix 和 Windows 服务器。有一个活跃的社区，为您的设备提供了许多现成的配置文件。查看这里的例子和模板->[http://cricket.sourceforge.net/contrib/](http://cricket.sourceforge.net/contrib/)。

如果对此有浓厚的兴趣，我很期待写一篇关于使用 Cricket 监控 web 服务器和设备健康状况的专题文章。

## 分享这篇文章