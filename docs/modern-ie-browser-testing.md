# 有了 modern，浏览器测试变得更加容易。工业管理学(Industrial Engineering)

> 原文：<https://www.sitepoint.com/modern-ie-browser-testing/>

Internet Explorer 的开发总是比它应该有的更困难:

*   IE 目前有五个版本，至少有三个需要注意。
*   IE6、7 和 8 很少支持现代网络标准。
*   只有版本 9 和 10 支持 HTML5，并且落后于竞争对手的浏览器。
*   一次只能安装和运行一个版本的 IE。
*   IE 只适用于 Windows。

不可否认，一部分开发者发现指责浏览器比修复问题更容易，但是创建跨浏览器兼容的网站仍然是一场斗争，即使你对传统 IE 解决方案有不健康的了解。

幸运的是，微软似乎后悔多年来给网络开发者带来的痛苦。 [**现代。IE**](http://www.modern.ie/) 是微软的一项新资源，用于帮助在主要版本的 Internet Explorer 上测试网站和应用程序。有几个免费的特性会引起所有 web 开发人员的兴趣，不管他们喜欢什么平台或浏览器…

[![modern.IE](img/3083000b19859ceaa27f9c35cf0d787b.png)](http://www.modern.ie/)

## 网页扫描仪

[**扫描网页**](http://www.modern.ie/report) 是一种分析工具，可生成已知兼容性问题的报告，例如:

*   标准/怪癖模式
*   过时的库
*   缺少 CSS 前缀
*   对浏览器插件的依赖
*   浏览器检测代码
*   响应式网页设计实践

有许多只适用于 IE/Windows 8 的建议，但是，即使你没有兴趣添加这些建议，该报告仍然提供了有用的信息，可以帮助你找到并消除错误。

## 免费虚拟机映像

如果你正在测试一个特定版本的浏览器，IE 模拟器和 F12 开发工具不一定会突出所有的问题。理想情况下，您需要一台专用的 PC 或虚拟机安装。

[**现代。IE 的虚拟工具**](http://www.modern.ie/virtualization-tools) 在 Windows、Mac 和 Linux 上为 Hyper-V、虚拟 PC、 [VirtualBox](http://www.virtualbox.org/) 和 [VMware](http://www.vmware.com/) 提供免费的 VM 镜像:

*   Windows XP 上的 IE6
*   Windows Vista 上的 IE7
*   Windows 7 上的 IE8
*   Windows 7 上的 IE9
*   Windows 8 上的 IE10

操作系统映像的大小在 1GB 到 7GB 之间，因此请确保您有足够的可用磁盘空间。*期待即将在 SitePoint 上发布的 Hyper-V 教程…*

## 免费浏览器堆栈订阅

[**浏览器堆栈**](http://www.browserstack.com/) 在虚拟机上提供数十种桌面和移动浏览器，可以通过浏览器远程访问。无需安装、管理或配置，只需登录、选择平台并加载您的网站。该服务还提供隧道设施，以便您可以测试本地 PC 或网络上托管的应用程序。

[**报现代。IE**T3，你将免费获得三个月的浏览器堆栈订阅。](http://www.modern.ie/virtualization-tools)

## 兼容性建议

最后，还有 [**20 个构建现代网站的技巧**](http://www.modern.ie/cross-browser-best-practices) ，其中包含了最大化兼容性的简单最佳实践建议。

你们中更愤世嫉俗的人会声称这一混乱是微软造成的，是现代的。IE 一开始就不应该被要求。不过，至少公司已经认识到了我们的测试问题，并提供了一些切实可行的解决方案。

## 分享这篇文章