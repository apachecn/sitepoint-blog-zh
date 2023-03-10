# 微软 Windows 8、Office 2013、IE10 以及对网络开发人员的影响

> 原文：<https://www.sitepoint.com/microsoft-windows-8-office-ie10-web-developers/>

微软本周透露，Windows 8 将于 2012 年 10 月 26 日星期五发布。版本已经过简化:

*   **Windows 8**
    面向国内市场的初级版本。
*   **Windows 8 Pro**
    Windows 8 具备专业功能，包括远程桌面服务器、文件加密、虚拟化、VHD 启动等。Windows Media Center 不包括在内，但将在媒体包中另行收费。
*   **Windows 8 企业版**
    Windows 8 专业版具备协助大型组织进行软件管理的功能。
*   **Windows RT**
    只能作为预装操作系统在基于 ARM 的平板电脑上使用。

在 2013 年 1 月 31 日之前，现有的 Windows XP、Vista 和 7 用户可以花 40 美元获得可下载的升级。运行哪个版本并不重要；你可以换成 Windows 8 标准版或专业版，价格不变。积极的折扣对网络开发者来说是个好消息；许多 XP 用户将升级并放弃传统版本的 Internet Explorer。

![Windows 8](img/b0a2710ded6ad29e3372d000ff69ba28.png)

Windows 8 提供了几个新的机会。原生 Windows 应用程序可以用 HTML5 和 JavaScript 开发，网站可以与 Metro UI tiles 交互，可以使用无铬浏览器，新的 Windows Marketplace 可以将您的开发工作货币化。

用户是否喜欢这个操作系统是另一回事。Windows 8 与以前的版本截然不同，尽管 Metro 可能是平板电脑的理想选择，但它可能会让那些使用台式电脑的人感到困惑。18 年后放弃“开始”按钮要么是一个勇敢和创新的决定，要么是一场类似 Vista 的灾难。

## 微软 Office 2013

根据[微软系统要求](http://technet.microsoft.com/en-us/library/ee624351%28v=office.15%29)，Office 2013 将只在 Windows 7、Windows 8、Windows Server 2008 R2 或 Windows Server 2012 上可用。因此，使用 XP 或 Vista 的公司必须:

1.  保留旧套房(即使他们已经支付了持续的办公室升级费用)，或者
2.  迁移到 Windows 7/8。

选择第二个选项的人不会(或不能)留在 IE8。Office 放弃 XP/Vista 支持的决定可能会鼓励比 Windows 8 本身更多的浏览器升级。

## Internet Explorer 10

虽然 IE10 可能会更早到来，但我怀疑微软会在同一天发布他们的新浏览器。IE9 发布时可能是竞争浏览器的对手，但 10 月份它已经 18 个月了。它已经落后了，早该升级了。

IE10 看起来令人印象深刻。如果他们能够提供承诺的 HTML5 功能集、性能和自动更新，微软将重新获得 web 开发人员社区的更多尊重。

不出所料，IE10 不会在 Windows XP 上运行。不幸的是，它也不能在 Vista 上运行。虽然 Vista 对微软来说算不上成功，但它目前占据了 8.1%的桌面用户——比所有版本的 Mac OS 加起来还要高。

Vista 正在下降，但速度远低于 XP。它是微软不到三年前的旗舰操作系统，当时购买个人电脑的人仍然拥有运行 Vista 的硬件。虽然指责早期版本很容易，但随着时间的推移，操作系统确实有所改进——许多用户没有什么理由升级。

因此，我们有 Windows XP 用户停留在 IE8 和 Vista 用户停留在 IE9。如果 Windows 8 不成功，这种情况可能会持续很多年。

## IE 旧版本

IE10 是一个重要的心理飞跃:IE8 将老两个版本。浏览器不会在一夜之间消亡，但它是棺材上的另一颗钉子。

然而，web 开发人员仍然需要测试 IE8。IE6 和 IE7 也没有完全消失。在同一台 PC 上安装传统浏览器是不可能的，尽管有一些有用的工具，如 IE 的兼容模式和 [IETester](http://my-debugbar.com/wiki/IETester/HomePage) ，但它们并不相同，只能用于表面测试。

Windows 7 提供 [XP 模式](https://www.sitepoint.com/ie6-ie7-ie8-win7-xp-mode/)；Windows XP 的虚拟副本，允许真实安装的 IE6、7 和 8 像本地应用程序一样运行。这是我选择 Windows 7 专业版的主要原因，尽管如果你有备用的 XP 许可证，它也可以在家庭版上运行。

Windows 8 已经放弃了 XP 模式，尽管它确实提供了内置的 Hyper-V OS 机器虚拟化。因此，运行 XP 虚拟机进行 IE 测试是可能的，不需要额外的软件，如 [VirtualBox](http://www.virtualbox.org/) 或 [VMware](http://www.vmware.com/) 。我会怀念 XP 模式，但是当旧的浏览器死去并被埋葬的时候，使用它的理由会更少。

## 欧盟浏览器选择屏幕

2010 年 3 月 1 日，欧盟强迫微软提供一个浏览器选择界面，这样欧洲用户就可以选择一个替代 Internet Explorer 的浏览器。该系统一直受到诸如[指责不公平偏见](https://www.sitepoint.com/vendors-push-for-fairer-browser-choice-screen/)和[非随机数组洗牌算法](https://www.sitepoint.com/microsoft-fix-their-non-random-browser-choice-screen/)等问题的困扰。

微软现在已经接受了欧盟的进一步调查。自 2011 年 2 月以来，一个技术问题阻止了该屏幕出现在新安装的 Windows 7 SP1 上，多达 2800 万用户可能受到影响。微软已经道歉，但欧盟调查人员表示，如果侵权行为被证实，将会受到制裁。

我不相信浏览器选择屏幕对市场份额有重大影响。它姗姗来迟了十年，在投入运营的那一年里几乎没有什么变化，Chrome 在没有援助的情况下成功超越了 IE。它的重新引入不会影响 IE6/7/8 迁移，因为大多数 Windows 7 用户已经有了 IE9 …但法律就是法律，屏幕必须出现在 Windows 8 中 *(Windows RT 是另一回事，但我将把这一辩论留到另一篇文章中)*。

## 基本上是好的？

无论你是否是微软用户，竞争性的 Windows 8 升级、Office 2013 和 IE10 的结合对网络开发者来说都是好消息。虽然 XP 和 Vista 上的 IE 仍然是一个问题，但旧版本应该会以更快的速度消亡，HTML5 应用程序开发将变得更容易。

让我们只希望微软能与其他厂商并驾齐驱。频繁的类似 Chrome/Firefox 的更新是不必要的，但是 IE 版本之间的 18-24 个月对于网络时间来说是很长的时间。

(声明一下，我是 Windows 用户。这篇文章包含我自己的观点，并没有得到微软或他们的竞争对手的资助。让支持/反对微软的评论开始吧！…)

## 分享这篇文章