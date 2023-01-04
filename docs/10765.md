# 在 IE 中使用 CSS3

> 原文：<https://www.sitepoint.com/ie-css3-pie/>

当微软宣布 CSS3 支持 ie 9 时，我们都欢呼了一下。也许现在我们将能够使用那些已经在其他浏览器中支持了几十年(几年左右)的时髦效果。不幸的是，IE9 仍然不可用，更有可能在明年出现。此外，它将无法在 XP 上使用，因此大多数 Windows 用户最多只能使用 IE8。从很多方面来说，这并不重要。IE 将忽略它无法理解的 CSS3 选择器和属性，但是如果你小心的话，你的站点仍然可以工作。这很好，直到你的客户或老板要求知道为什么他们的浏览器没有圆角。你可能会为浏览器兼容性和渐进增强提出一个合理的、符合逻辑的论点，但他们不在乎。他们花了很多钱买圆角，他们现在就想要！在你开始切割背景图片之前，你可能有兴趣听听另一个选择: [CSS3 派](http://css3pie.com/)(渐进式 Internet Explorer)。这是 Jason Johnston 的一个新项目，提供了一个类似于[狄恩·爱德华兹的 IE7 shim](http://code.google.com/p/ie7-js/) 的解决方案，它使用 JavaScript 将以下 CSS3 特性添加到 IE6、7 和 8 中:

*   `border-radius`
*   `box-shadow`
*   `border-image`
*   多个背景图像
*   RGBA 颜色值，以及
*   梯度

除了少数例外，没有必要改变现有的 CSS3 属性；`border-radius: 5px`将简单地在 Internet Explorer 中应用。它还可以监控 JavaScript 行为，因此您可以使用如下代码:

```
var myelement = document.getElementById("myelement");myelement.style.borderRadius = "20px";
```

## CSS3 PIE 是如何工作的？

[下载 PIE.htc 文件](http://github.com/lojjic/PIE/downloads)，放入你的 CSS 文件夹。注意，它需要使用 MIME 类型的`text/x-component`(在[已知问题页面](http://css3pie.com/documentation/known-issues/)的末尾有进一步的说明)。然后，您需要将 behavior 属性添加到需要 CSS3 效果的每个元素中，例如:

```
#myelement {    behavior: url(PIE.htc);    border-radius: 10px;}
```

IE 加载 PIE 脚本，并使用微软专有的滤镜或 VML 来应用效果，以达到相同的效果。

**Note:** What are IE filters?

1997 年 IE4 中引入了过滤器和过渡，但大多数更好的选项出现在 2000 年的 IE5.5 中。滤镜允许在 CSS3 中刚刚变得常见的效果。你看:IE 并不都是坏的，微软可以创新！不幸的是，过滤器是一个黑客，有时表现良好，并使用可怕的代码，例如:

```
filter:progid:DXImageTransform.Microsoft.Shadow(Color=#000000,Direction=45);
```

过滤器永远不可能成为 W3C 标准，很少有开发人员使用它们。

你应该注意到 CSS3 PIE 不太可能在每种情况下都是可行的解决方案，而且作为 beta 产品，可能会出现问题。然而，对于想要采用 CSS3 而又不忽视 IE 用户的 web 开发人员来说，这是一个很好的选择。你试过 [CSS3 派](http://css3pie.com/)吗？它有用吗，或者你更喜欢在 IE8 和早期版本中隐藏效果吗？

## 分享这篇文章