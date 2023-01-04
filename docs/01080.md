# 如何建立一个反应灵敏的引导网站

> 原文：<https://www.sitepoint.com/build-responsive-bootstrap-website/>

在本文中，我们将从头开始构建一个响应性的引导网站。到本文结束时，您将足够熟悉这个流行的 CSS 框架的最新版本，能够根据项目的需要构建自己的变体。

如今，建立响应性网站是必须的。人们通过各种设备访问网站，谷歌在网页结果排名时强调了响应性网页设计的重要性。

从头开始设计响应式网站对初学者来说可能很费力，尽管最新的 Flexbox 和 CSS 网格规范使得在这一领域取得巨大成就比过去容易得多。

然而，对于那些还没有准备好处理尖端布局技术的人来说，Bootstrap grid 仍然是一个很好的选择。

## “响应式引导网站”是什么意思

当我们使用“响应式设计”这个词时，首先想到的是网站应该兼容各种设备和屏幕尺寸。业内一直有这样的需求，即让每个网站都能在不同的环境下更好地响应在线内容的可读性。

在 CSS3 和 HTML5 的帮助下，这已经成为一种趋势。但是如果你是一名开发人员而不是设计师呢？邦克！

你不用再担心了。由于 Bootstrap 是 CSS 框架领域的超级英雄，在它的帮助下，你很快就能处理响应式网页设计。

## 安装

确保你得到一个响应迅速的引导网站就像在你的网页头部放置正确的 meta 标签一样简单:

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

上面的 meta 标签本质上是不言自明的。我们将页面的宽度设置为设备的宽度，并最初将其缩放为默认大小 1。

除此之外，您可以开始了:默认情况下，Bootstrap 是有响应的。

```
<link rel="stylesheet" href="css/bootstrap.css">
<link rel="stylesheet" href="css/bootstrap-responsive.css">
```

这是我们将要构建的页面的演示:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )使用 Bootstrap 查看 Pen [构建响应式网站。](https://codepen.io/SitePoint/pen/rJewLG/)