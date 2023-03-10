# 使用 JPC 在手机上运行 PC 应用程序

> 原文：<https://www.sitepoint.com/pc-apps-mobile-web-browser/>

英国牛津大学的一个团队[开发了一个 x86 PC 模拟器](http://www-jpc.physics.ox.ac.uk/)。令人惊讶的是，它是用 Java 写的。更神奇的是，它真的有效！

Web 开发人员是诸如 VMware、Virtual PC 和 [VirtualBox](https://www.sitepoint.com/virtualbox-review/) 等 PC 模拟器的主要用户。这些软件产品*模拟* PC 硬件，允许你在虚拟机中安装另一个操作系统。由于虚拟机是数据文件的集合，因此可以对其进行克隆、备份或恢复。虚拟化是企业界的一个热门话题，它为 web 开发人员提供了一种跨多种设备测试多种浏览器的可靠方法。即使易用性和省钱不是问题，虚拟化也消除了在办公室塞满硬件的需要！

那么，为什么 JPC(纯 Java x86 PC 仿真器)不同于其他仿真器呢？

**它是跨平台的**
JPC 将在任何支持 Java 的设备上运行；Windows，Mac，Linux，以及大部分手机。几乎可以肯定，它将在谷歌的 Chrome 操作系统上运行。

![Mario Bros running on a mobile phone](img/3535a06ca152c82ea91ca24b5cebc31f.png)

(注意 iPhone 是个例外。尽管它有对 Java 的硬件支持，史蒂夫·乔布斯说过“Java 不值得内置。没人再用 Java 了。就是这个重量级的大球链。”)

**它是安全的**
虚拟 PC 完全在 Java 沙箱中运行。仿真硬件与真实机器隔离，不会干扰底层操作系统。

**它在浏览器中工作**
这是杀手锏。开发人员可以建立一个预装操作系统和软件的虚拟机，任何授权用户都可以从任何支持 Java 的 web 浏览器直接访问该虚拟机。这有巨大的潜力；Chrome 操作系统或手机用户可以随时随地运行 Windows 应用程序。

实际上，这不会很快发生。JPC 提供了一系列可用的 DOS 游戏和 Linux 发行版，但是完全兼容 x86 和 Windows 还有一段路要走。毁灭战士是可玩的，你不会玩太久。

软件层的数量使得仿真速度成为一个问题。然而，软件和硬件将会改进——这使得可能性变得非常有趣。

这项技术会比谷歌的 Chrome 操作系统更具有颠覆性吗？

## 分享这篇文章