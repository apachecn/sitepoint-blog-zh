# internet Explorer 7–游戏状态

> 原文：<https://www.sitepoint.com/internet-explorer-7-the-state-of-play/>

本文的早期版本发表在 SitePoint Tech Times #160 上。

在《科技时报》第 158 期上，我询问了你改造网站以支持新发布的 Internet Explorer 7 的经历。以下是我收到的一些有趣的回复，以及一些可能有助于你使用这款新浏览器的信息。

来自安吉拉(强调我的):

> “我倾向于严格遵循标准建议，并进行彻底的测试，所以我没有发现 IE7 有太多令人惊讶的地方(我是一个悲观主义者，这可能有所帮助！).条件注释使处理问题变得简单而直观。无论如何，我不是 IE 的粉丝，但 IE7 并没有想象中那么糟糕。”

条件注释绝对是我在 Internet Explorer 中处理 CSS 问题的方式，Angela。对于门外汉，我在 Tech Times #131 中讨论了条件注释。然而，简而言之，条件注释允许您通过使用 Internet Explorer 将处理的特殊形式的 HTML 注释，将附加样式表应用于特定版本的 Internet Explorer，而其他浏览器和开发工具将忽略它们:

```
<link rel="stylesheet" type="text/css" href="styles.css" />
<!--[if IE]>
<link rel="stylesheet" type="text/css" href="styles.ie.css" />
<[endif]-->
<!--[if lt IE 7]>
<link rel="stylesheet" type="text/css" href="styles.ie6.css" />
<[endif]-->
```

在上面的代码中，大多数浏览器只会将`styles.css`应用于页面。然而，Internet Explorer 浏览器也会将`styles.ie.css`应用到页面上。此外，IE7 之前的 Internet Explorer 版本也将对页面应用`styles.ie6.css`。

社区中的许多开发者已经[反对](http://meiert.com/en/blog/20070201/why-conditional-comments-are-bad-repeat-bad/)使用条件注释。相反，他们提倡使用 CSS hacks，比如`* html`(稍后会有更多相关内容)。这有几个原因:

*   条件注释要求你添加额外的 HTML 标记到你的
    页面来解决真正的 CSS 问题。
*   条件注释将特定于 IE 的 CSS 代码与普通的 CSS 代码分开，很难一眼看出什么样式应用于特定的元素。
*   条件注释对于许多开发工具(如 W3C CSS validator)是不可见的，这使得它们很难使用。

事实上，如果你需要做的只是在 IE7 之前的 IE 浏览器上应用特定的风格，那么`* html`黑客可以是一个非常干净的解决方案:

```
div.thingo {
  float: left;
  margin-left: 1em;
}
* html div.thingo {
  display: inline; /* Fix double float margin in IE6 */
}
```

在 Internet Explorer 6 及更早版本中，浏览器误认为`html`元素有父元素，可以用通用选择器(`*`)匹配。

这种特殊攻击的好处是它使用的 CSS 代码完全有效——它只是依靠 IE6 中的一个错误来进行不同的处理。

类似的攻击也可以用来攻击 IE7。

大多数情况下，归结为个人喜好问题。条件注释有一个好处，那就是它有一个可预测的未来。我相当肯定地知道我的条件注释在 Internet Explorer 的未来版本中会如何表现，而对于 CSS hacks，IE8 很有可能会修复使 hack 工作的问题，但不是 hack 已经用来解决的问题。

来自玛丽·兰森:

> “我试着让我的 CSS 保持简单明了，并避免黑客攻击。我的网站在 IE7 中运行良好，足以通过测试。话虽如此，IE7 确实以一种我不觉得有帮助的方式增加了各种不同的渲染，仅仅因为一个页面适用于 IE7，并不意味着它将适用于 IE6。由于 IE7 无法安装在旧版本的 Windows 上，我预计 IE6 将比以前的版本继续占据相当大的用户比例。”

这是一个非常好的观点。微软声称，他们预计 Windows XP 中的自动更新系统将引领新浏览器前所未有的普及。虽然这被证明是正确的，但在世界上行动缓慢的机构中，大量老化的 Windows 2000 和 Windows 98 机器可能在几年内都不会迁移到 IE7。

值得庆幸的是，这次微软还为我们提供了一个免费的实用工具，用于在 IE6 和 IE7 中进行并行测试: [Virtual PC 2007](http://www.microsoft.com/windows/products/winfamily/virtualpc/) 。在你的 Windows XP 或 Windows Vista 系统上下载并安装这个免费软件，然后获取微软的[免费虚拟机镜像](http://blogs.msdn.com/ie/archive/2006/11/30/ie6-and-ie7-running-on-a-single-machine.aspx)，其中包含运行在预激活的 Windows XP 上的 Internet Explorer 6(包括 [IE 开发者工具栏](http://www.microsoft.com/downloads/details.aspx?FamilyID=e59c3964-672d-4511-bb3e-2d5e1db91038))。现在，您可以安装 IE7 来进行您的主要开发和测试工作，然后当需要在 IE6 中进行测试时，只需启动虚拟 PC 并键入 URL 即可！

![](img/72a954b6e119261fca83377f0f2b4fda.png)

虚拟映像中包含的 Windows XP 副本将于 2007 年 4 月 1 日到期，但从微软的声明来看，他们似乎计划在该日期之前更新映像。

我真的希望看到微软发布一个包含 IE5.5 的免费虚拟机，为我们这些仍然有兴趣为该浏览器提供一些支持的人服务。但是这种仅用于 IE6 测试的解决方案对于 web 设计人员来说是一个巨大的负担，他们以前必须准备一台备用机器(带有额外的 Windows 许可证)来测试浏览器。

顺便说一下，如果你想知道为什么我没有提到你可以从像 [evolt](http://browsers.evolt.org/) 和 [Tredosoft](http://tredosoft.com/Multiple_IE) 这样的地方下载的 Internet Explorer 的“独立”版本，那是因为这些版本不足以进行深入的浏览器兼容性测试。具体来说，这些独立版本都使用系统上安装的当前(最新)版本的 JavaScript，而不是这些旧浏览器实际附带的 JavaScript 版本。亚历克斯·罗素[有一篇关于这个话题的精彩博文](http://alex.dojotoolkit.org/?p=588)。

谢天谢地，虚拟 PC 2007 做到了这一切，而且是免费的！唯一的缺点是它需要 Windows XP Professional(不是 XP Home)或 Windows Vista Business、Enterprise 或 Ultimate(不是 Home 或 Home Premium)，所以如果你的操作系统不是微软认为的“开发者级别”，你就不走运了。

<ins datetime="2007-03-29T11:52:00+00:00">**关于 Windows XP Home 问题的更新:**几个人告诉我，Virtual PC 2007 确实可以在 Windows XP Home 上运行——微软在这种配置下不支持它。显然，如果您在安装过程中忽略警告，它会正常工作。</ins>

## 分享这篇文章