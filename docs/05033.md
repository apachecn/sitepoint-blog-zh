# 使用字体 Awesome 和 IcoMoon 的图标字体介绍

> 原文：<https://www.sitepoint.com/introduction-icon-fonts-font-awesome-icomoon/>

图标字体现在非常流行，越来越多的开发者在他们的设计中利用它们。如果你还没有研究过图标字体，这篇文章可以帮助你入门。

我将展示使用图标字体可以实现的伟大成就，但首先我要看看什么是图标字体，它们有什么优点和缺点。然后，我们将研究两种流行的图标字体，我们将看到不同的例子，展示我们如何在我们的项目中使用它们。

## 什么是图标字体？

图标字体只是字体。然而，它们不包含字母或数字，而是包含符号和字形。你可以用 CSS 对它们进行样式化，就像你对普通文本进行样式化一样，这使得它们成为网络上的流行选择。

## 图标字体的利与弊

在项目中使用图标字体而不是位图图像有一些显著的好处。以下是其中的一些:

*   您可以对它们应用任何 CSS 效果。
*   因为它们是矢量图形，所以是可扩展的。这意味着你可以放大或缩小它们，而不会损失它们的质量。
*   您可以发送一个或几个 HTTP 请求来加载它们，而不是位图图像可能需要的多个 HTTP 请求。
*   因为它们体积小，所以装载速度快。
*   所有浏览器都支持它们(甚至回到 IE6)。

那么，图标字体是否适合所有情况呢？不，当然不是。它们是增强项目设计的极好方法，但是它们也有局限性。例如，如果您想在项目中显示复杂的图像，而不是简单的图标，那么图标字体不是最佳解决方案。

它们通常也仅限于单一颜色，除非你应用一些 CSS 技巧。此外，图标字体是根据特定的网格设计的，例如 16px16、32×32、48×48 等。如果出于某种原因，您将网格系统更改为 25×25，您可能不会得到清晰的结果(特定的 CSS 属性也有帮助)。

除了图标字体，值得一提的是还有另一种流行的矢量图标解决方案:SVG 图标。这些有能力克服图标字体可能有的一些问题。例如，您可以对它们应用多种颜色。

在本文的其余部分，我们将介绍以下两个图标字体库的基础知识:

*   [字体牛逼](http://fortawesome.github.io/Font-Awesome/)
*   [icmoon](https://icomoon.io/)

我们将展示四个使用图标字体的不同例子。

## 字体超赞图标

字体牛逼是 439 个图标的丰富集合。这个库对于个人或商业用途都是完全免费的。最初，它是为 Bootstrap 设计的，但是你可以在任何你喜欢的框架中使用它。

### 字体牛逼入门

将 Font Awesome 包含在您的项目中的最简单方法是通过内容交付网络(CDN)。然而，如果你是离线工作，你必须下载图标包。

您还必须找到并包含 CSS 文件和包含不同字体格式的生成的字体文件夹。然后，您必须将 CSS 文件添加到 HTML 文档中。最后，您必须验证 CSS 文件中的`@font-face` `src` URL 路径对应于适当的文件夹。为你的项目设置这些图标的所有可能方式的完整列表，见[他们的入门指南](http://fortawesome.github.io/Font-Awesome/get-started/)。

为了使用图标，你必须把它们放在一个`span`或者一个`i`元素中。然后，你要给他们分配两个班级。这个`fa`类加上第二个类，这个类应该是你想要添加到你的项目中的图标的名字，例如`fa-home`。[在他们的备忘单](http://fortawesome.github.io/Font-Awesome/cheatsheet/)中，你可以看到软件包中所有可用图标的名称。

附加的预定义类允许您轻松定制图标。关于这些类的更多信息以及如何使用它们的例子，[到这里](http://fortawesome.github.io/Font-Awesome/examples/)。

现在让我们看看使用字体 Awesome 图标包的三个不同的例子。

### 字体很棒的例子#1

在第一个例子中，我们创建了一个垂直导航菜单。首先，我们将图标包装在一个`i`元素中，并使用预定义的`fa-2x`类将它们的大小相对于容器的大小增加一倍。然后，我们使用 CSS 对它们进行样式化。

两个导航项目的 HTML 如下所示:

```
<li>
    <a href="#" data-name="Home">
        <i class="fa fa-home fa-2x"></i>
    </a>
</li>
<li>
    <a href="#" data-name="About">
        <i class="fa fa-bullhorn fa-2x"></i>
    </a>
</li>
```

这是相关的 CSS:

```
nav li {
    background: #ededed;
    height: 80px;
    width: 80px;
    line-height: 80px;
    text-align: center;
}

nav li:not(:first-child) {
      margin-top: 1px;
}

nav li a {
    outline: none;
    position: relative;
    width: 100%;
    height: 100%;
}

nav i {
    color: steelblue;
    vertical-align: middle;
}

nav li a:after {
    background: #ededed;
    content: attr(data-name);
    display: none;
    margin-left: 1px;
    width: 160px;
    height: 80px;
    left: 80px;
    position: absolute;
    font-size: 1.2em;
}

nav li a:hover:after {
    display: inline-block;
}
```

结果就在下面的演示中:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[字体牛逼图标字体示例#1](http://codepen.io/SitePoint/pen/GFspr/) 。