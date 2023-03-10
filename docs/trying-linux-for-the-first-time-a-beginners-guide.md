# 首次尝试 Linux:初学者指南

> 原文：<https://www.sitepoint.com/trying-linux-for-the-first-time-a-beginners-guide/>

你可能熟悉 Windows 和/或 Mac OS。但它们不是唯一可用的操作系统。一个流行的选择是 Linux T2。在本文中，Lesley Lutomski 介绍了 Linux，以及尝试使用它需要了解的内容。

* * *

![Linux penguin](img/15b8ab8983333f54319244ba92d89e85.png)

我经常惊讶于那些告诉我他们想尝试 Linux，但是认为它“太难”的人。

似乎有一种普遍的误解，认为 Linux 是“为极客准备的”。当然，这曾经是一种情况:专注的用户编译他们自己的内核，它不适合胆小的人。

但是从那时起，Linux 已经走了很长的路。所以，如果你从来没有尝试过，或者很多年前尝试过然后放弃了，我鼓励你再想想。

## 选择你的口味

Linux 有许多“风格”，或“发行版”——通常被称为**发行版**。其中一些是针对主流观众的，我建议用其中一个来试试。其中最著名的可能是 [Ubuntu](http://www.ubuntu.com) ，这是我使用的一个，也是我将在这里集中讨论的一个。 [Linux Mint](http://www.linuxmint.com) 也很受欢迎，但是还有更多。

![Ubuntu Gnome desktop](img/7788a51109887de5540627788dbd2077.png) * Ubuntu Gnome 桌面*

## 难不难？

那么很难用吗？以我的经验来看没有。当我们切换到 Ubuntu 时，我注意到的第一件事是我收到的来自我丈夫的求救电话数量突然减少了。与在 Windows XP 上相比，他在使用该系统时遇到的问题似乎更少了，而且似乎对自己尝试更有信心，而不是担心自己可能会“打破什么东西”。

我还为一位从未使用过任何电脑的老年朋友设置了一个 Xubuntu 系统，她很快就掌握了它。

## 有什么好处？

对于许多人来说，成本将是一个考虑因素。大多数流行的 Linux 发行版及其相关软件都可以免费下载和使用。对其他人来说，操作系统的开源特性很有吸引力。

Linux 比 Windows 更不容易感染病毒。其主要原因很简单，大多数病毒是针对 Windows 机器设计的，对 Linux 系统没有影响。Linux 系统对病毒免疫不是真的，但这种情况非常罕见。幽默的解释可以在这里找到[。](http://www.gnu.org/fun/jokes/evilmalware.html)

这种额外的安全性是我们为我们的老朋友选择它的一个原因。虽然 Linux 病毒很少见，但 ClamAV 是免费的，它有助于确保你不会无意中下载病毒并把它传给使用 Windows 的朋友。

![ubuntu gnome desktop traditional](img/38fc462aeb33d11f9a8397a36e5ba3d1.png) * Ubuntu Gnome 桌面–繁体版*

## Linux 会和我的硬件兼容吗？

Linux 可以在大多数个人电脑上很好地运行，尽管如果你有最新的尖端技术，你可能会发现它不能立即得到支持。

另一方面，安装 Linux 是给旧硬件注入新生命的好方法。一些发行版被设计成轻量级的——比如 Ubuntu 变体[Xubuntu](http://xubuntu.org)——在资源有限的系统上表现良好。

在 Mac 上运行 Linux 也是可能的，尽管我没有这方面的经验。Ubuntu 论坛——一个巨大的帮助和支持资源——为苹果硬件用户提供了一个[的专用部分](http://ubuntuforums.org/forumdisplay.php?f=328)。

## 如何选择发行版？

最简单的方法是简单地尝试一个，看看你是否喜欢它。这远没有听起来那么激进。

许多发行版都是免费下载的，之后你可以把它们刻录成 DVD。然后，它们可以作为“实时”CD/DVD 运行。换句话说，您从 DVD 或 USB 驱动器启动系统，并从那里运行操作系统。它不需要安装，也不会写入你的硬盘——尽管在 Linux 下你应该可以访问硬盘上的文件。

这是一个很好的方法，可以毫无风险地感受发行版，并且可以让你检查你的硬件是否有问题。Ubuntu 网站为完成这一切提供了非常清晰的教程。

![Ubuntu options](img/786c292878334d67d79240d3a9a51f84.png)

不过，有一点需要注意；从实况 DVD 运行明显比从硬盘运行慢，因此您应该考虑到这一点。此外，如果您决定继续使用 Linux，您可能会发现额外的专有驱动程序来提高图形和其他硬件的性能。

## 我如何安装它？

同样，这是一个简单的过程。“live”CD/DVD 包括安装系统的选项，如果您选择这样做，它将引导您完成安装过程。

您可以选择与另一个操作系统一起安装，安装在同一个驱动器或不同的驱动器上，并运行双引导系统。这允许您使用任何一个系统。你也可以在一个<abbr title="virtual machine">虚拟机</abbr>中运行它。

如果你决定安装 Ubuntu 或它的变种，无论是作为一个双引导或独立的系统，这个过程很简单，只要回答几个问题，然后坐下来，让系统做工作。如果你在同一个驱动器上安装一个现有的操作系统，Ubuntu 会对它进行分区。如果您有宽带连接，您可以选择下载并安装更新作为该过程的一部分。

请注意，Linux 和 Windows 使用不同的[文件系统](https://en.wikipedia.org/wiki/File_system)。虽然 Linux 可以读写 Windows 分区，但 Windows 不能读取 Linux 分区。如果您正在进行双引导，并且需要从两个系统中访问文件，请确保将它们保存到 FAT 或 NTFS 格式的分区中。

作为第一次使用 Linux 的用户，我自己的经历并不简单。我为 Ubuntu 添加了第二个硬盘，安装很顺利。后来，我启动了 Ubuntu，一切都很好——直到我试图启动 WinXP，发现我不能。

在短暂的恐慌之后，我去了 Ubuntu 论坛，那里有人耐心地向我介绍了几个可能的解决方案，直到我们找到一个适合我的系统的。那是七年前，我唯一一次遇到这样的问题。

## 其他软件呢？

这可能是 Linux 是否适合你的最终决定因素。一方面，你可以获得大量免费的、易于安装的软件；另一方面，许多流行的商业应用程序都不支持 Linux。

当然，你可以运行 VM，或者使用 [Wine](https://www.winehq.org/about/) ，这是一种用于 Windows 软件的翻译层。Wine 网站维护着一个[应用程序数据库](https://appdb.winehq.org/)，该数据库为每个应用程序在 Wine 下的运行提供指导。根据软件版本的不同，这通常会有很大的不同，比如下面这些 Photoshop 的[结果:](https://appdb.winehq.org/appview.php?appid=17)

![photoshop on wine](img/749b07911be3ad1e68ed055699787aec.png)

我在老游戏和小应用上取得了很好的成功，但一般来说，我使用原生 Linux 替代品。

libre office(open office 的一个分支)作为标准安装，Firefox 也是。 [Chromium](https://www.chromium.org/) 是 Chrome 浏览器的原生 Linux 版本，支持许多相同的扩展。 [GIMP](http://www.gimp.org/) 几乎是 Photoshop 的替代品，尽管对于它的优劣众说纷纭。除了捆绑的应用程序之外，其他软件也很容易从存储库中安装。

Ubuntu 现在配备了 Ubuntu 软件中心，这是一个图形界面，可以让你按类别查找软件并查看评级:

![ubuntu software centre](img/7d13f4edcb3924f4582391fcfc9c3baf.png)

我倾向于更喜欢旧的**新立得软件包管理器**。您可以按名称或关键词搜索应用程序，并查看建议的结果:

![synaptic package manager](img/46ea517793ac662cc24549066f875d3d.png)

## 更新呢？

更新会自动通知，只需一次点击即可安装。很少需要重启系统，除了内核本身的更新:

![updates](img/d151c81588a22d749113e6a46218eb2b.png)

Ubuntu 的新版本每六个月发布一次，但是你可以选择使用“长期支持”(LTS)版本。这些每两年发布一次。已安装的应用程序会收到更新，但一般不会升级到新版本，直到下一个 Ubuntu 版本。因此，如果你想运行最新版本的东西，LTS 版本不会适合你。

## 你还在等什么？

这从来就不是一个“如何做”的指南，而仅仅是一个激发你的兴趣并鼓励你自己尝试 Linux 的概述。希望我成功了，现在你可以下载你的第一个发行版了！

如果你更愿意以一种更温和的方式尝试，甚至不需要刻录和从 DVD 启动，Ubuntu 网站提供了一个你可能会喜欢的操作系统虚拟之旅。它让你领略了 Ubuntu 的外观和感觉，包括它附带的主要应用程序的一瞥。

最后，如果你已经冒险安装了 Linux，并且你是一名 web 开发人员，这里有一些关于在 Linux 中建立开发环境的建议。

如果你有任何问题或经验要分享，请在评论中提出。

## 分享这篇文章