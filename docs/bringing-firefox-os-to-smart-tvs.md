# 将 Firefox 操作系统引入智能电视

> 原文：<https://www.sitepoint.com/bringing-firefox-os-to-smart-tvs/>

*这篇文章由 [Agbonghama Collins](https://github.com/collizo4sky) 和 [Simon Codrington](https://github.com/simonrcodrington) 进行了同行评审。感谢所有 SitePoint 的[同行评审](https://www.sitepoint.com/introduction-to-sitepoints-peer-review/)让 SitePoint 的内容尽可能做到最好！*

* * *

随着松下最新系列高端电视设备在今年的 ces 上首次亮相， [Firefox OS](https://www.mozilla.org/en-US/firefox/os/1.3/) 现在已经渗透到智能电视市场。

松下的维耶拉设备系列获得了令人惊讶的好评。随着 Firefox OS 今年采取新的策略，这可能是一个再一次重新审视 Firefox OS 环境的好时机。

SitePoint 已经发表了许多关于 Firefox OS 的文章，但在这篇文章中，我们将重点讨论 Firefox OS 如何与电视配合使用。我将介绍如何更好地设置您的环境和电视操作系统的风格指南。

## 要记住的事情

为 Firefox OS 驱动的电视开发应用程序与移动设备类似，但在专门为电视设备开发应用程序时，需要记住一些不同之处:

*   **输入/控制机制:**电视上的 Firefox OS 应用程序将通过电视遥控器使用，而不是键盘、鼠标或触摸屏。这是要记住的最关键的事情。
*   分辨率:你必须处理全高清(1920×1080)或超高清(4K)分辨率。电视上的设备像素比率与大多数移动设备不同。

## 准备您的环境

**注意:**常规的 Gaia 构建过程不能在标准的 Windows 环境下运行。Windows 用户应该使用该工具，它在虚拟机内部构建 Gaia。

您需要三个主要组件来构建开发环境:

*   **桌面模拟器:**下载安装最新的[夜间桌面模拟器](https://archive.mozilla.org/pub/mozilla.org/b2g/nightly/latest-mozilla-central/)(又名 B2G 桌面)，或者自己搭建。以 *b2g-* 作为文件名的开头，获得一个适合您系统的文件。
*   **火狐每夜/开发者版:**你需要安装[火狐每夜](https://nightly.mozilla.org/)或者[火狐开发者版](https://www.mozilla.org/en-US/firefox/developer/)。
*   **一个本地可用的[盖亚库](https://github.com/mozilla-b2g/gaia) :** 的分支，你可以通过:
    *   在 GitHub 上分叉[主 Gaia repo。](https://github.com/mozilla-b2g/gaia)
    *   在本地克隆你的叉子，`git clone https://github.com/your-username/gaia.git`
    *   将上游添加回 GitHub，`git remote add upstream https://github.com/mozilla-b2g/gaia`

在 Mozilla 开发者网络上找到更多关于如何运行你自己的 Gaia 构建的信息。

**注意:**分叉 Gaia repo 并设置上游是可选的，但如果您想对项目做出贡献，这是一个很好的做法。

要为电视创建特殊的 Gaia 配置文件，请在 repo 文件夹中运行以下命令:

```
make GAIA_DEVICE_TYPE=tv DEVICE_DEBUG=1
```

进行更改时，您可以刷新应用程序(或浏览器窗口)来查看结果。不需要重建配置文件并将其重新推送到设备。这种方法非常适合快速的 CSS/JS/HTML 黑客攻击。

## 运行模拟器

运行 Firefox 桌面模拟器，设置指定的屏幕大小、远程调试服务器和配置文件路径:

```
path/to/b2g-bin -screen 1600x900 -start-debugger-server 6000 -profile /path/to/gaia/profile
```

### 笔记

*   指定高于电脑屏幕分辨率的分辨率将导致系统应用程序无法正常工作。
*   *b2gbin* 在安装火狐 OS 模拟器的`b2g`包里面。
*   创建配置文件时，会显示所需的准确配置文件路径(通过运行上面的`make`命令)。

现在你可以在电视上为 Firefox OS 创建你的第一个应用了。我推荐你阅读 Mozilla 的 Hello World 教程开始学习。

![TV Emulator](img/bb709cb4264bc97f216b408e00f7fb51.png)

![Application List](img/672d31aa9d3e91c3da38e2a8f9ff5493.png)

## 了解 UX

Mozilla 对其操作系统的电视版进行了广泛的用户测试和研究。火狐操作系统的 UI 和 UX 的每个关键方面都在 Mozilla 的风格指南中有记录，包括一篇关于 MDN 的文章。

Firefox OS for TV 背后的设计理念基于并类似于谷歌的材料设计元素。电视操作系统利用了卡的比喻，每张卡代表一个内容或服务的入口点。卡片排成一列，如下图所示:

![Firefox OS TV Card overview](img/61569603fe4331f556f205a0b367f33f.png)

这些“基础”卡片被称为“卡片组”，因为它们是其他子项目的聚合器。例如，电视卡组可以包括 200 多张电视频道卡。动画是流畅自然的，借用了真实生活中的元素，就像在材料设计中一样。风格指南强调微妙是关键。

> 它应该作为交互设计的重点，帮助用户保持专注，而不是主导体验。

![Easing example](img/0aadbe01d41082c9494a20af5cf1e303.png)

首页菜单是一切开始的地方。用户通过按遥控器上的*主页*按钮来访问它。它提供了强大的定制能力，以迎合用户的喜好。

![Home Screen](img/c4deff4b2ae90a9f74e60cafa18f2ab9.png)

仪表板是操作系统的主要枢纽，提供了用户可以查看有用信息的单一区域。它是主页上的默认卡片之一。

![TV Dashboard](img/02d1a434c3c9cb3ea57dd648a1225c91.png)

仪表板通过遥控器的方向板进行导航。

*   **右:**通知
*   **左:**即将播出的电视节目
*   **Up:** 音乐播放&控件
*   **下:**世界时钟

最后，还有*app*和*设备*卡。它们以类似于 Android(应用程序抽屉)的方式存储，如果用户愿意，可以钉在*主页*屏幕上。

![App and Devices cards](img/109096f08b3a4e029a00258e49e42bb8.png)

## 资源

如果你想更深入地了解 Firefox OS TV 的风格指南，只需看看 Mozilla 开发者网络。

推荐阅读[电视布局](https://developer.mozilla.org/en-US/Apps/Design/Firefox_OS_TV_UX/App_layout_guide)、[图标设计](https://developer.mozilla.org/en-US/Apps/Design/Firefox_OS_TV_UX/Icon_design)、[积木](https://developer.mozilla.org/en-US/Apps/Design/Firefox_OS_TV_UX/Building_blocks)、[界面&动画设计](https://developer.mozilla.org/en-US/Apps/Design/Firefox_OS_TV_User_Interface_Animation_Design)以及[动画设置](https://developer.mozilla.org/en-US/Apps/Design/Firefox_OS_TV_UX/Animation)指南。

![Firefox OS TV range](img/a4d818823d3f43f3418a05c3b4646d53.png)

关于当前可用的 Firefox OS 设备(包括电视)的概述，请访问 [Firefox OS 设备](https://firefoxosdevices.org/en/#type:tvs%7Ccoming-devices:no)网站。它由 Mozilla 志愿者[索伦·亨茨谢尔](https://www.soeren-hentzschel.at/)维护，在 Mozilla 内部被广泛使用。

## 看狐狸吗？

随着 5 款高端电视已经发布，看到松下与 Mozilla 合作令人欣慰。火狐操作系统还没有给人留下我希望的印象，所以看到像松下这样的大公司投资火狐操作系统肯定对 Mozilla 的操作系统有帮助。

该生态系统仍处于早期阶段，但成为早期采用者之一并为 Firefox OS 电视开发可能是有利的。让我们看看 2016 年 Firefox OS 在电视上有什么表现。你想看什么？

## 分享这篇文章