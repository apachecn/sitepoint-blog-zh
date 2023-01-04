# VirtualBox 3.1.x 的新增功能

> 原文：<https://www.sitepoint.com/virtualbox-31-new-features/>

![VirtualBox](img/b771abe938e14cb4491a53254f69dcce.png "VirtualBox") [VirtualBox](http://www.virtualbox.org/) 是 Sun(即将成为[甲骨文](https://www.sitepoint.com/oracle-mysql-commitment/))的虚拟机模拟器，适用于 Windows、Linux 和 Mac OS X。这是一款优秀的软件，我在之前就已经[赞不绝口了。](https://www.sitepoint.com/virtualbox-review/)

尽管有来自 Windows 7 和 T2 XP 模式的竞争，我仍然使用 VirtualBox 来测试浏览器和软件。开源产品以相当大的优势击败了微软的虚拟个人电脑，并且与 VMware 不相上下。

VirtualBox 3.1 于 11 月底发布，几周后 3.1.2 出现。有许多更新是 web 开发人员感兴趣的。

## 1.提高性能

VirtualBox 一直很快，但 3.1.x 进一步提高了速度。虽然我是 XP 模式的粉丝，但冷启动 XP 虚拟机和启动 IE 一样快。操作系统和浏览器的响应速度也远远超过虚拟 PC。

硬件爱好者会很高兴知道 VirtualBox 支持比以前更多的设备，可以模拟多达四个处理器，并利用 2D/3D 加速。

## 2.心灵运输

传送或实时迁移是一种工具，允许您将实时运行的虚拟机从一台主机移动到另一台主机。这些演示令人印象深刻，毫无疑问也很聪明，但我不确定我会找到使用它的理由。理论上，您可以在工作中使用虚拟机桌面，将其传输到智能手机，然后传输到您的家用 PC。您将始终保留相同的操作系统和应用程序。

我怀疑该工具对企业系统管理员有用，但也许它对应用程序演示或用户支持是可行的。

## 3.分支快照

VirtualBox 支持虚拟机(VM)快照已经有一段时间了。但是，在 3.1 版之前，只能恢复到以前的状态，这会销毁层次结构中较低位置的快照。VirtualBox 3.1 引入了分支快照，允许您从单个根映像创建任意数量的虚拟机。

分支快照为 web 开发人员提供了显著的好处，我将在下一篇文章中向您展示如何使用它们。

你使用 VirtualBox 吗？它与其他虚拟机解决方案相比如何？

## 分享这篇文章