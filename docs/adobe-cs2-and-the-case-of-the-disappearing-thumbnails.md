# Adobe CS2 和消失的缩略图案例！

> 原文：<https://www.sitepoint.com/adobe-cs2-and-the-case-of-the-disappearing-thumbnails/>

尽管 Adobe Creative Suite 2 拥有无可置疑的强大功能，但它确实有一些令人不快的习惯。

在我的列表中，第一个重点是它故意禁用 Illustrator 和 Photoshop 原生文件的缩略图。

这两个程序的前 CS 版本在提供每个文件包含的内容的快速刷新方面做得很好，但随着 CS 的诞生(足够可疑的是，Adobe Bridge 的*给了你完整的缩略图),所有的 PSD、EPS 和 AI 文件突然恢复为在 Windows 资源管理器中显示为无关紧要的小图标。欢迎来到 1999 年。*

考虑到这可能是被禁用而不是移除的东西， [Matt](https://www.sitepoint.com/articlelist/423) 今天下午花了一点时间研究这个问题，感谢 [Mike Golding](http://www.notestips.com/80256B3A007F2692/1/TAIO-68UEVH) 和 [Stewart Whaley](http://www.notestips.com/80256B3A007F2692/1/TAIO-68UEVH#DB0E7CE68A6AADF780256FB60074F777) 的出色工作，以下是研究结果。

![A screenshot of icons changed to thumbnails](img/2ddac8ae906220900c7c764ee3280327.png)

简而言之，这个问题是可以解决的。然而，这个解决方案确实需要您进行一些注册表更改并安装一些 dll。它还假设您将 CS2 安装在它的默认文件夹中。

但是要注意:玩弄注册表是一种黑暗、危险的艺术，只有伟大的巫师和临床精神病患者才能正确理解。基本上，我们不能保证你不会把你的 PC [变成一个大的 blancmange](http://en.wikipedia.org/wiki/Blancmange) 。

然而，如果你觉得自己很勇敢(或者你真的很喜欢 blancmange)，这里有一些指导。

1.  [将文件](https://www.sitepoint.cimg/aiicon.zip)下载到临时文件夹。
2.  关闭所有 Adobe 程序。
3.  设置一个“系统返回点”,以便在出现任何问题时允许您回滚更改(如果您的系统支持的话)。
4.  解压到“`C:Program FilesCommon FilesAdobeShell`”。您很可能已经拥有了其中一个 DLL 文件。没关系。
5.  双击运行“`aiicon.dll.reg`”。
6.  双击运行“`psicon.dll.reg`”。

应该可以了。如果你现在闻不到烟味，那可能是有效的。公平地说，我们从来没有遇到过任何问题，而且每次都运行得完美无缺。然而，对过去很久的注册表崩溃的记忆难以磨灭。

啊，..有时候，是微小的快乐让事情变得不同。

<ins datetime="2007-10-26T07:20:12+00:00">此处发布了针对 Adobe CS3 用户的这篇文章的[更新。](https://www.sitepoint.com/adobe-cs3-and-the-case-of-the-disappearing-thumbnails-the-sequel/)</ins>

## 分享这篇文章