# 虚拟化–节省时间、金钱和理智

> 原文：<https://www.sitepoint.com/virtualization-save-time-money-and-sanity/>

一段时间以来，在 SitePoint HQ，我们在开发和试运行环境中广泛使用了虚拟服务器。如果您对以下任何一个问题的回答是肯定的，那么虚拟化可能也适合您:

*   您是否曾经上传代码或对生产(实时)服务器进行配置更改，却发现您的站点已经崩溃？
*   安装新服务器是否耗费了您大量的时间？
*   硬件是否供不应求？你有没有想过:“我希望我有另一个盒子来测试这个？”
*   你需要一个隔离的环境让你的客户看到你的作品吗？

设置多服务器开发和登台环境可能比您想象的更便宜、更容易。

免费版的 [VMware Server](http://www.vmware.com/products/server/) 与现在内置于大多数英特尔芯片中的[虚拟化技术](http://www.intel.com/technology/virtualization/)相结合，几乎就是您开始工作所需要的一切。与以前版本的 VMware 不同，它的性能非常好(当然还有相当大的内存和磁盘空间)。

![VMware Console](img/8e229f04da321c7786bfce780a00c038.png)

**开发中**

*   这是有趣的事情发生的地方。我们可以修改各种配置和操作系统，而不需要额外的硬件。
*   如果出现不可恢复的故障，我们可以从备份 VMware 映像恢复全新的开发环境。节省大量时间。

**暂存中**

*   将变更转移到我们的试运行环境本质上是一个部署预演。仅仅知道您刚刚对代码所做的更改将在生产中起作用是不够的。测试实际的部署过程本身和测试代码一样重要。
*   这是生产服务器的镜像。在生产环境中实施之前，可以在这里测试配置更改。
*   我们可以让我们的客户在这种环境中放松，因为它与开发和生产是隔离的。

**浏览器测试**

![Parallels](img/c0872085f9461c257cdef6a3525ff34d.png)

当然我们也在 MacBooks 上使用 [Parallels](http://www.parallels.com/) (耶！)和/或 Windows 上的[虚拟 PC](http://www.microsoft.com/windows/products/winfamily/virtualpc/) ，带有微软提供的[免费 VPC 镜像](http://go.microsoft.com/fwlink/?LinkId=70868)(针对 IE6 和 IE7)。在 Internet Explorer 中测试 CSS 变化非常方便。

总之，如果虚拟化是您从未考虑过的事情，那么它绝对值得一试。您将有更多的时间，节省硬件成本，并充满信心地进行部署！

## 分享这篇文章