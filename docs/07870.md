# jQuery 1.8 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-jquery-1-8/>

jQuery 1.7 发布已经[九个月了](https://www.sitepoint.com/jquery-17-whats-new/)，所以我们推出了全新的版本，帮助你在更短的时间内编写更少的代码，同时保持良好的跨浏览器兼容性(目前为)。让我们看看主要的新功能和更新…

## 更多咝咝声

jQuery 的 Sizzle 选择器引擎已经针对性能和效率进行了优化。它依赖于本地的 querySelectorAll 方法(如果可用的话),但即使这样，浏览器之间的实现也有所不同。jQuery 1.8 解决了这个问题，并且仍然为 IE6 和 ie7 提供了编码支持。

## 自动 CSS 前缀

当您在方法中使用 CSS 属性时，如。css()或。animate()，jQuery 1.8 将自动添加任何所需的前缀。因此，您可以应用单个`transform`和-webkit-、-moz-、-ms-和-o-前缀。

如果你已经在你的 CSS 中使用了`box-sizing: border-box`，你也会很高兴听到 width，innerWidth，innerHeight，outerWidth，outerHeight 方法现在可以正确计算了。

## 高级动画

jQuery 团队承认动画代码变得有点笨拙。这个问题已经解决，动画性能也因此得到了改善。

然而，我怀疑 jQuery 和 JavaScript 驱动的动画时代已经屈指可数了。它永远不会完全消失，但是 CSS3 用更少的代码提供了更好更快的效果。幸运的是，如果不需要的话，不需要包含 jQuery 的动画方法…

## 模块化程度提高

jQuery 1.8 比 1.7.2 版本小几百字节，但是可以使用基于 grunt 的构建系统去掉不需要的特性，从而进一步减小它。

## 虫子压扁了

最后，jQuery 1.8 修复了 160 多个 bug。代码基础已经坚如磐石，所以他们发现这么多问题需要解决真是令人惊讶。

## 是时候升级了？

那些使用 1.7.x 版本的用户可能会切换到 1.8 版本，而不会出现重大问题。旧版本应该也可以，但是要做好一些严格测试的准备。

也就是说，问题通常会在发布后几天内被报告，所以那些更谨慎的人可能更愿意等待几周时间来发布 jQuery 1.8.1。

jQuery 可在 jQuery.com[获得。CDN 托管的版本将很快出现。](http://jquery.com/)

## 分享这篇文章