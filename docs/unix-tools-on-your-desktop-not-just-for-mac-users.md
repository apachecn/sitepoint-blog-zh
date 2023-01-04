# 桌面上的 Unix 工具——不仅仅是为 Mac 用户

> 原文：<https://www.sitepoint.com/unix-tools-on-your-desktop-not-just-for-mac-users/>

我转向 Mac 的一个主要原因是底层的类 Unix 内核和命令行。我一直有一个本地 Linux 服务器用于开发，将这种功能整合到我的台式机上是一个很好的卖点。

事实证明，虽然我喜欢 Mac OS，但我并没有把它作为我的开发服务器环境——我使用了一个在 Windows 机器上也能很好工作的解决方案。

**虚拟化**

如果你还没有遇到过虚拟化软件，如 [VMware](http://www.vmware.com/) 和 [Parallels](http://www.parallels.com/) 可以让你在现有的计算机上创建[虚拟机](http://en.wikipedia.org/wiki/Virtual_machine)，你可以在其上安装任何操作系统，并在现有的操作系统上作为应用程序运行。从运行在虚拟机上的“客户操作系统”的角度来看，它似乎运行在它自己的硬件上，并且除了通过专门设置的共享工具之外，不能访问“主机操作系统”。

我们的系统和网络管理员 Lucas 最近写了一篇关于使用虚拟化在一台备用机器上建立多个开发和试运行环境的文章，这为我们节省了大量电力、机架空间和办公室资金。

虚拟化还可以用于从您的台式机中获得两个世界的最佳效果——一个精致的 GUI 的易用性，以及对一个强大的以服务器为中心的操作系统的访问。

**Mac 或 Windows——甚至 Linux**

如果你运行的是 Windows 或 Linux，VMware 是虚拟化的市场领导者，并免费提供 [VMware Server](http://www.vmware.com/products/server/) 。用于 Mac 的[VMware Fusion](http://www.vmware.com/products/beta/fusion/)的免费测试版已经推出，但最终版本将不会免费。

[Parallels Desktop](http://www.parallels.com/en/products/desktop/) 是目前 Mac OS 唯一的非测试版解决方案，拥有非常强大的追随者。Parallels 还为 Windows 和 Linux 提供虚拟化软件。

两家供应商的软件在界面和功能上非常相似，都很容易安装和运行。您创建一个新的虚拟机，指定要分配多少磁盘空间、RAM 等，然后从 CD、DVD 或 ISO 映像文件安装您的客户操作系统。

如果你认为这是让 Mac OS 在你的电脑上运行的好方法，那么你是对的。但不幸的是，Mac OS 许可证不允许这样做，因此没有一个虚拟化平台支持它。

**Mac OS 的麻烦**

理论上，在 Mac OS 上运行定制的开发环境是不错的，但是可能会非常复杂。从源代码编译在 95%的情况下都可以正常工作，但是另外的 5%可能会花费你几个小时去追踪链接器问题、OpenSSL 冲突等等。

有像 [MacPorts](http://www.macports.org/) 和 [Fink](http://finkproject.org/) 这样的软件包管理器，但是他们无法与他们的 Linux 竞争对手竞争。举个极端的例子，“fink install kcachegrind”触发了大约 2 天的自动下载和编译 [KCachegrind](http://kcachegrind.sourceforge.net/cgi-bin/show.cgi) 及其所有依赖——包括整个 [KDE！](http://www.kde.org/)相比之下，我在 Ubuntu 中输入“aptitude install kcachegrind ”,几秒钟后应用程序就开始运行了。

【Windows 的麻烦

虽然 cygwin 会让你在 Windows 下运行许多移植的类似 Unix 的应用程序，并且许多应用程序如 Apache、MySQL 和 PHP 都有 Windows 版本，但 Windows 并没有提供运行严肃开发环境的标准环境和工具来满足命令行爱好者。有所好转，但不在。

**一个 Linux 虚拟服务器**

我是一名最近的 Ubuntu 转换者，在多年运行稍微老化的 T2 Slackware Linux 发行版之后。如果你对 Linux 相当陌生，我建议首先在虚拟机上安装 [Ubuntu Desktop](http://www.ubuntu.com/products/WhatIsUbuntu/desktopedition) 。这将给你一个很好的直观的图形用户界面，同时你也掌握了事情的诀窍。我用的是 Ubuntu 服务器，它不提供 GUI。我在后台运行它，使用 [iTerm](http://iterm.sourceforge.net/) (或者 Windows 上的 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) )而不是更笨重的 VM 窗口通过 SSH 访问它。

Ubuntu 在他们的在线资源库中提供了大量的最新软件，可以使用图形化的新立得软件包管理器，或者命令行中的“aptitude”轻松安装。例如，“sudo aptitude install subversion”将在几秒钟内为您整理出源代码管理，“sudo aptitude install rails”将预测您的下一个 web 应用程序并为您编写它！(抱歉，我忍不住了)

在无数可用的有用应用程序中，Samba(sudo aptitude install Samba)可能是最值得一提的一个。它提供了与 Mac OS 和 Windows 兼容的文件共享，因此您可以作为网络驱动器连接到您的虚拟机，并就地编辑文件，您的更改只需保存并刷新浏览器即可。没有更多的 FTP 传输来查看每一个变化。

只是两个快速警告。首先，在 Parallels(直到当前版本 3188)和 Ubuntu 7(代号 Feisty Fawn)以及其他最新的 Linux 发行版所使用的 Linux 内核之间存在一个问题。虽然有一些[变通办法](http://paul.annesley.cc/articles/2007/05/01/ubuntu-704-feisty-server-parallels-cdromkernel-workaround)，但如果你想在 Mac 上运行最新的 Ubuntu，我会选择 VMware Fusion Beta，直到 Parallels 发布更新。其次，运行虚拟机会让你的功耗增加一点。虽然仍然比运行两台物理机效率高得多，但如果您有一台笔记本电脑，您肯定会注意到电池寿命的差异。然而，一个拥有 256mb 内存的 Linux 虚拟机只需要几秒钟就可以挂起和恢复，所以这并没有困扰我太多。

## 分享这篇文章