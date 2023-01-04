# 为什么 Windows 7 将彻底改变您的浏览器测试

> 原文：<https://www.sitepoint.com/windows-7-browser-testing/>

我敢肯定，有些开发人员最喜欢在 57 种不同的网络浏览器中测试他们精心制作的应用程序。漫长的冬夜一定会飞逝而过。对于我们其余的人来说，浏览器测试是一种必要的罪恶，而 Internet Explorer 让它变得更加痛苦。多亏了微软在文明之初做出的决定，IE 与操作系统紧密结合，所以不可能在同一台机器上运行多个版本。遗憾的是，IE 的市场份额平均分布在 IE6、IE7 和 IE8 之间。

IE 测试人员有许多解决方案:

*   IE8 可以切换到 IE7 兼容模式(虽然没有 IE6 模式)
*   使用 IE 的“黑客”独立版本，或者
*   安装 IE 测试应用程序，如 [IE Tester](http://www.my-debugbar.com/wiki/IETester/HomePage) 。

这些解决方案有用，但有缺陷。浏览器在某些 Windows 版本中可能会失败，相互冲突，使用错误的 JavaScript 引擎，或者无法正确应用 PNG 和滤镜效果。最可靠的测试方法是使用多台 PC 或虚拟机软件，如优秀的 [VirtualBox](https://www.sitepoint.com/virtualbox-review/) 。

## Windows 虚拟 PC

[虚拟 PC](http://www.microsoft.com/windows/virtual-pc/) 是微软对 VMware 和 VirtualBox 的竞争对手。2007 年的[版本](http://www.microsoft.com/windows/virtual-pc/support/virtual-pc-2007.aspx)已经足够了，但是它没有提供其他套件中可用的速度和设施。

新的 Windows 7 专用更新现在可以作为候选发布版本获得，尽管最终版本可能最好等待几周。该软件已经过彻底检查，并利用了处理器虚拟化——一种在大多数现代芯片上可用的技术，但可能需要在您的 BIOS 中启用。

Windows Virtual PC 可以安装在任何版本的 Windows 7 上，包括家庭基本版和家庭高级版，尽管您可能需要至少 2GB 的内存。

## Windows XP 模式

XP Mode 是 Windows XP SP3 的单一许可精简虚拟副本，免费提供给 Windows 7 专业版、企业版和终极版用户(非 Windows Home)。

XP 模式允许您在虚拟机上安装应用程序，但在 Windows 7 下运行它们，就像它们是本机应用程序一样。他们可以访问本地驱动器，保存到主机的“文档”文件夹，或者在桌面上有一个图标。只有较慢的启动速度表明你使用的是虚拟 XP 应用程序。

## 运行多个 ie

XP 模式将大幅缩短浏览器测试时间，因为最终将有可能同时在同一桌面上运行 IE6、IE7 和 IE8 的真正版本。

要运行 IE6，您需要启动 XP 虚拟机，并在来宾操作系统中，在“所有用户”桌面或开始菜单文件夹中创建浏览器的快捷方式。该快捷方式将在 Windows 7 开始菜单中复制。

不可能在同一个虚拟机中安装 IE7，因为它会删除 IE6。但是，您可以通过复制/克隆硬盘映像、安装 IE7 并像以前一样添加快捷方式来创建虚拟操作系统的新实例。

*注意:当最终版本可用时，我们将在 SitePoint 上提供逐步说明。*

## 买家当心！

如果您计划使用 XP 模式，请确保您购买了正确的 Windows 7-仅支持专业版、旗舰版和企业版。虚拟 PC 将在 Windows 7 家庭普通版/高级版上运行，但您需要安装自己的许可客户操作系统，并且将无法利用 XP 模式功能。

Windows 7 将于 10 月 22 日发布。预购优惠可能仍然可用，因此要警惕定价假设！美国和加拿大亚马逊商店的价格是合理的，例如

*   [美国微软 Windows 7 专业版升级——200 美元](http://www.amazon.com/exec/obidos/ASIN/B002DHGM50/mswindows7-21)
*   [美国微软 Windows 7 专业版(完整版)——300 美元](http://www.amazon.com/exec/obidos/ASIN/B002DHLVII/mswindows7-21)
*   [美国微软 Windows 7 旗舰版升级——220 美元](http://www.amazon.com/exec/obidos/ASIN/B002DHLV8S/mswindows7-21)
*   [美国微软 Windows 7 旗舰版(完整版)——320 美元](http://www.amazon.com/exec/obidos/ASIN/B002DHGMVY/mswindows7-21)

但是，[欧盟浏览器投票困惑](https://www.sitepoint.com/microsoft-browser-windows-7-europe/)继续导致[英国](https://www.sitepoint.com/windows-7-cheaper-in-europe/)的怪异定价。Windows 7 升级现在比完整版更贵？…

*   [英国微软 Windows 7 专业版升级— 155](http://www.amazon.co.uk/exec/obidos/ASIN/B002DGS82G/mswindows7-21)
*   [英国微软 Windows 7 专业版(完整版)— 148](http://www.amazon.co.uk/exec/obidos/ASIN/B002DUCMTC/mswindows7-21)
*   [英国微软 Windows 7 旗舰版升级— 170](http://www.amazon.co.uk/exec/obidos/ASIN/B002DGS83K/mswindows7-21)
*   [英国微软 Windows 7 旗舰版(完整版)— 160](http://www.amazon.co.uk/exec/obidos/ASIN/B001XCWGII/mswindows7-21)

法国和德国的亚马逊商店似乎以与美国相似的价格出售完整版(仍标有“E”)。欧洲买家可能会从在英国购买中受益。

XP 模式会让你的浏览器测试实践变得更好吗？还是微软正在修复一些本来就不应该成为问题的东西？

相关阅读:

*   [微软 Windows XP 模式和 Windows 虚拟 PC](http://www.microsoft.com/windows/virtual-pc/)
*   在欧洲购买 Windows 7……价格只有美国的一半！
*   [微软放弃 Windows E 并展示浏览器投票屏幕](https://www.sitepoint.com/microsoft-windows-browser-ballot/)
*   [微软放弃无浏览器的 Windows 7 E](https://www.sitepoint.com/microsoft-browser-windows-7-europe/)
*   [Windows 7 预购在第 1 天售罄](https://www.sitepoint.com/windows-7-sold-out/)
*   [微软从 Windows 7 中移除 IE 不会有任何影响](https://www.sitepoint.com/ie-windows-7-europe/)
*   [可以从 Windows 7 中卸载 Internet Explorer】](https://www.sitepoint.com/ie-windows-7-uninstall/)
*   微软将在 Windows 中提供竞争浏览器？

## 分享这篇文章