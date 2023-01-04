# Internet Explorer 10 有什么新功能

> 原文：<https://www.sitepoint.com/ie10-new-features/>

当微软发布 Internet Explorer 9.0 时，我们希望这标志着他们传统的 T2 2 年发布计划的结束。不到一个月后，该公司宣布了他们的 IE10 计划，并发布了一个[平台预览下载](http://ie.microsoft.com/testdrive/Info/Downloads/)。

与之前的大块更新不同，IE10 目前提供了一些比 IE9 小的改进…

## 新的 CSS3 属性

IE10 将支持 CSS3 渐变( [W3C 草案规范](https://www.w3.org/TR/css3-images/#gradients))，以下属性可应用于元素背景:

*   -质谱线性梯度
*   -质谱-径向梯度
*   -ms-重复-线性-梯度
*   -ms-重复-径向-梯度

有用的是，微软在他们的[试驾](http://ie.microsoft.com/testdrive/)网站上提供了一个 [CSS 渐变背景制作工具](http://ie.microsoft.com/testdrive/Graphics/CSSGradientBackgroundMaker/)。

如果浮动元素让你感到悲伤，IE10 将提供几种可选的布局方法:

*   CSS3 柔性盒模块——或“柔性盒”( [W3C 草案规范](https://www.w3.org/TR/2009/WD-css3-flexbox-20090723/))
*   CSS3 多栏布局( [W3C 草案规范](https://www.w3.org/TR/css3-multicol/))
*   CSS3 网格对齐( [W3C 草案规范](http://www.interoperabilitybridges.com/css3-grid-align/))

其他浏览器也支持 Flexbox 和多栏布局(带有厂商前缀)，所以如果你愿意接受 IE9 及更低版本的降级外观，这些技术应该是可行的。假设没有其他厂商击败微软，IE10 将是第一个支持网格对齐的浏览器。

请在 SitePoint 上寻找更多的 CSS3 布局教程。

## JavaScript (ECMAScript 5)严格模式

一个新的`use strict;`命令将您的代码置于严格模式。这提供了更健壮的解析、改进的错误检查，并将突出与该语言未来版本的潜在兼容性问题。

## 新用户代理

IE10 的新用户代理字符串是:

```
Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/6.0)
```

看看 IE 是否会遭遇 Opera 在升级到版本 10 时遇到的同样问题将会很有趣。他们发现许多网站使用有缺陷的浏览器嗅探技术，只解析第一个数字，假设浏览器是 Opera 1，并降级或阻止该网站。为了解决这个问题，Opera 在其用户代理字符串中使用了 9.80 版本。

虽然[浏览器嗅探很糟糕](https://www.sitepoint.com/why-browser-sniffing-stinks)，但随着开发者开始支持移动设备，它最近又重新流行起来。也许是时候仔细检查你的代码了？

## IE10 什么时候发布？

微软还没有承诺一个日期。有传言称 beta 1 将于 2011 年 9 月发布，最终版本将于 2012 年 3 月发布——即 IE9 发布 12 个月后。鉴于改进的数量很少，这似乎有点太过了。也许微软会用更早的版本或额外的功能给我们惊喜？ *(HTML5 表格拜托！)*

无论发生什么，微软似乎都有可能打破他们两年的发布惯例。

**note:** Users flock from Flock

其他消息, [Flock 浏览器](http://www.flock.com/)将于 2011 年 4 月 26 日停止使用。该应用基于 Mozilla 的 Gecko 引擎，主要面向社交网络用户。

## 分享这篇文章