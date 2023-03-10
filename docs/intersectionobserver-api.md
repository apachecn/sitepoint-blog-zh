# 带有 IntersectionObserver API 的本机无限滚动

> 原文：<https://www.sitepoint.com/intersectionobserver-api/>

*这篇文章由[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington)和[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

最近，一个有趣的新客户端 JavaScript API 出现在 Web 平台上，[intersect observer API](https://wicg.github.io/IntersectionObserver/)。

这个小而有用的 API 提供了一种有效监视(观察)指定 DOM 元素可见性的方法，也就是说，当它们在视口(浏览器窗口或元素的视口)之内或之外时。通过指定与视口矩形相交的元素面积的分数，可以精确定义元素可见性。

此功能的一些常见应用和使用案例包括:

*   内容的延迟加载
*   无限滚动
*   广告可见性
*   滚动触发的动画(**注:**这不是目标用例。API 报告的可见性信息可能会有一点延迟，并且不能保证数据的像素完整性)。

## 浏览器支持

作为一个相当新的 API，在撰写本文时，它的支持[仍然有限](https://www.chromestatus.com/feature/5695342691483648):

*   Chrome 桌面 51
*   Android 51 的 Chrome 浏览器
*   Android WebView 51
*   歌剧 38
*   Android 38 版 Opera

然而，Github 上有一个正在开发的 polyfill(不支持根边距)[,所以我们现在就可以开始使用交叉点观察器了。](https://github.com/surma-dump/IntersectionObserver/blob/polyfill/polyfill/intersectionobserver-polyfill.js)

在本文中，我们将实现无限滚动 UX 模式。我们将使用前面提到的 polyfill，甚至一些 ES6/ES2015 特性，如承诺、模板字符串和箭头函数。

## 无限滚动

想象一下，我们有一个很长的项目列表，我们希望通过无限滚动来浏览，这样当用户接近文档的底部时，下一批项目就会被加载并追加到列表的末尾。

这是我们将要建造的:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[无限滚动演示](http://codepen.io/SitePoint/pen/xOompk/)。