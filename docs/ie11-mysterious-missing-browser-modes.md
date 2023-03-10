# IE11 和神秘失踪的浏览器模式

> 原文：<https://www.sitepoint.com/ie11-mysterious-missing-browser-modes/>

请注意，本文中的一些信息已经被取代。请看 [IE11:浏览器模式返回](https://www.sitepoint.com/ie11-browser-modes-return/)。

网络开发者让微软日子不好过。*“遵循 W3C 标准”*，我们喊。*“强迫每个人升级他们的浏览器”*，我们尖叫。*《IE 吃了我的小猫》*，我们惨叫。

一些批评是应该的。微软放弃了他们的旗舰浏览器，阻碍了网络发展，而且他们仍然需要太长时间来发布新版本。但是，如果我们忽略令人厌恶的 IE7，该公司在过去的五年里已经显著改善了 Internet Explorer。除了最复杂的应用之外，IE8 和一些垫片对于所有应用都足够了。IE9 不会引起很多问题，除非你想看 CSS3 动画。IE10 是任何竞争浏览器的可靠替代品。

你仍会在 IE 中发现问题(请微软— [添加`transform-style: preserve-3d;`](/css3-transformations-3d/) )，但你会在每个浏览器的**中发现问题。没有人是完美的。那些咆哮得最大声的人通常发现指责微软比在自己的代码中修复跨浏览器问题更容易。**

IE11 将于 2013 年 10 月 18 日与 Windows 8.1 一起发布，最终包括 WebGL 和一套[伟大的开发工具](/ie11-developer-tools/)。如果你是 MSDN 的用户，你今天就可以安装它(感谢[对微软之前决定的一点回溯](http://blogs.windows.com/windows/b/bloggingwindows/archive/2013/09/09/download-windows-8-1-rtm-visual-studio-2013-rc-and-windows-server-2012-r2-rtm-today.aspx))。Windows 7 的版本将在几周后推出。

然而，一个缺失的特性将招致各地开发者的愤怒:*浏览器模式已经没有了！*

## 什么是浏览器模式？

IE8、9 和 10 中的开发工具提供了越来越多的浏览器渲染模式，这可以追溯到 IE7。

![IE10 browser modes](img/79f42d92f6fe9210a9563f2a52a03425.png)

这些模式是在 2006 年 IE7 发布后，由于兼容性问题而增加的。浏览器修复了开发者在 IE6 中使用的一些怪癖和专有技术，因此很多应用程序在浏览器升级后崩溃。浏览器模式是一种解决方案，允许公司在修复问题时继续使用传统应用程序。HTTP 头或 meta 标签可以强制 IE 的新版本像以前的版本一样工作。

## 但这不是模仿

也许公司仍在使用这些模式，但我敢打赌，更多的开发人员在旧版本的 IE 中使用它们进行快速和肮脏的测试。我当然有。浏览器模式的移除会被嘲笑，但是有很多原因可以解释为什么他们的消亡会受到欢迎:

*   浏览器模式是用于特定目的的临时补丁；不是应用程序测试。
*   浏览器模式不能代替测试真正的应用程序。微软没有在 IE10 中包含四个版本的 Trident 浏览器只是做了表面上的调整。你的站点可能在 IE7 模式下工作，但在实际安装中会失败。*(注意这也适用于第三方解决方案，如 [IETester](http://my-debugbar.com/wiki/IETester/HomePage) 。)*
*   浏览器模式应该消亡。IE6、7、8 和 9 之间存在着根本的差异，但 IE9、10 和 11 之间的差异要小得多。在一个用户自动升级的世界里，没有必要测试旧的浏览器。你不测试 Chrome 23 或者 Firefox 17IE 应该没什么不同。

## 回到现实

对我们大多数人来说，IE6 和 ie7 已经死了。随着 Windows 7/8 用户升级到 IE10，IE9 正在迅速下降——这两种浏览器通常都表现良好。不幸的是，尽管微软试图鼓励 Windows XP 迁移，IE8 仍然保持着 8%的市场份额。因此，一旦 IE11 安装到您的电脑上，您有三种选择:

1.  不用费心测试 IE8 了。谷歌没有。幸运的话——再加上一点小心的编码——你的网站会像其他老化的浏览器一样优雅地退化。
2.  使用浏览器渲染服务，如 [BrowserStack](http://www.browserstack.com/) 、 [BrowserShots](http://browsershots.org/) 或 [NetRenderer](http://netrenderer.com/) 。这些对基本的美容测试很有帮助。
3.  在 IE8 的真实安装中测试你的站点。你不需要再买一台电脑；微软在 [modern.ie](http://www.modern.ie/en-US/virtualization-tools#downloads) 为 Windows、Mac OS 和 Linux 提供免费虚拟机。

但请不要试图保留 IE10 或使用注册表黑客来重新启用浏览器模式！它们总是有缺陷，会让你产生一种虚假的满足感。人们越早升级，我们就能越早抛弃古老的浏览器。

请注意，本文中的一些信息已经被取代。请看 [IE11:浏览器模式返回](https://www.sitepoint.com/ie11-browser-modes-return/)。

## 分享这篇文章