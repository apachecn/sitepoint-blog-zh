# CSS 函数的 8 个妙招

> 原文：<https://www.sitepoint.com/8-clever-tricks-with-css-functions/>

CSS 的能力远远超过许多 web 开发者所意识到的。样式表语言逐年变得越来越强大，为浏览器带来了原本由 JavaScript 实现的功能。在这篇文章中，我们将看看八个完全不需要 JavaScript 的 CSS 函数技巧。

## 1.纯 CSS 工具提示

许多网站仍然使用 JavaScript 来创建工具提示，但使用 CSS 实际上要容易得多。最简单的解决方案是在 HTML 代码的数据属性中提供工具提示文本，例如`data-tooltip="…"`。在您的标记中，您可以将以下内容放入您的 CSS 中，以显示`attr()`函数中的工具提示文本:

```
.tooltip::after {
  content: attr(data-tooltip);
}
```

不言自明，对吧？当然，需要更多的代码来设计工具提示的样式，但是不用担心，有一个很棒的纯 CSS 库可以实现这个目的，叫做 [Hint.css](https://github.com/chinchang/hint.css) 。

## 2.(Ab)使用自定义数据属性和`attr()`功能

我们已经将`attr()`用于工具提示，但它还有其他一些用例。结合数据属性，您可以仅使用一行 HTML 代码构建带有标题和描述的缩略图:

```
<a class="caption" href="#" data-title="Vulture" data-description="...">
  <img src="img.jpg" alt="Illustration"/>
</a>
```

现在您可以使用`attr()`功能来显示标题和描述:

```
.caption::after {
  content: attr(data-title);
    ...
}
```

下面是一个缩略图的工作示例，带有悬停时显示的动画标题:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看带动画字幕的笔[缩略图。](http://codepen.io/SitePoint/pen/akAmPw/)

注意:生成的内容*在可访问性方面可能会有问题。这篇关于 CSS 生成内容的[可访问性支持的文章是一篇非常有用的阅读材料。](http://tink.uk/accessibility-support-for-css-generated-content/)*

## 3.CSS 计数器

你能用 CSS 计数器做的事情看起来很神奇。这不是最广为人知的特性，大多数人可能会猜测它没有得到很好的支持，但实际上，每个浏览器都支持 CSS 计数器:

我能使用 css 计数器吗？caniuse.com 主要浏览器对 css-counters 特性支持的数据。

虽然您不应该对有序列表使用 CSS 计数器(`<ol>`)，但计数器对于分页或在图库中的项目下显示数字非常有用。您还可以计算选中项目的数量，考虑到您只需要很少的代码(并且没有 JavaScript)，这是非常令人印象深刻的:

参见 [CodePen](http://codepen.io) 上 Will Boyd ( [@lonekorean](http://codepen.io/lonekorean) )的笔[选择 CSS 计数器](http://codepen.io/lonekorean/pen/wKbzv/)。

CSS 计数器对于动态改变项目列表中的数字也很有用，这些项目可以通过拖放来重新排序:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [CSS 计数器拖放演示](http://codepen.io/SitePoint/pen/bdeOKJ/)。

就像上一个例子一样，记住—生成的内容*在可访问性方面可能会有问题。*

## 4.带 CSS 滤镜的磨砂玻璃

在 iOS 7 中，苹果为我们带来了“毛玻璃”效果——半透明、模糊的元素，看起来像毛玻璃窗口。受苹果实施的启发，这种效应开始在许多地方出现。重建这个效果有点棘手——在有 CSS 滤镜之前，你必须用模糊的图像来创建磨砂玻璃效果。现在几乎所有主流浏览器都完全支持 CSS 滤镜，[重现这种效果](http://codepen.io/adobe/pen/d056d1b26b9683c018f9bb9e0f1b0e1c)就容易多了。

我能使用 css 过滤器吗？caniuse.com 主要浏览器支持 css-filters 特性的数据。

将来，我们可以使用背景滤镜和[滤镜()函数](http://iamvdo.me/en/blog/advanced-css-filters#filter)来创建类似的效果，这两个功能目前都只有 Safari 支持。

## 5.使用 HTML 元素作为背景图像

通常情况下，你会指定一个 JPEG 或 PNG 文件作为背景图像或渐变。但是你知道吗，有了`element()`功能，你还可以用一张`<div>`作为背景图片。目前，`element()`功能仅受 Firefox 支持:

见[我能使用 css-element-function 吗？](http://caniuse.com/#feat=css-element-function)caniuse.com 主要浏览器对 css-element-function 特性的支持数据。

可能性几乎是无穷无尽的，[这里有一个来自 MDN 的例子](https://developer.mozilla.org/en-US/docs/Web/CSS/element)。

## 6.使用`calc()`的智能电网

流体网格是一个伟大的东西，但也有一些严重的问题。例如，顶部和底部的装订线不可能与左侧和右侧的装订线大小相同。此外，标记非常混乱，这取决于您使用的网格系统。即使 flexbox 本身并不是最终的解决方案，但是有了可以在 CSS 中用作值的`calc()`函数，网格可以变得更好。在[site point](https://www.sitepoint.com/using-modern-css-to-build-a-responsive-image-grid/)的这个教程中，乔治·马苏科斯展示了一些实际的例子，比如具有完美间距的画廊网格。使用 CSS 预处理程序，比如 Sass，[组装一个创造性的网格系统可以非常简单](https://www.sitepoint.com/creative-grid-system-sass-calc/)并且易于维护。随着浏览器支持也接近完美，`calc()`是一个你绝对应该使用的便利特性。

参见[我可以使用 calc 吗？](http://caniuse.com/#feat=calc)caniuse.com 主要浏览器对 calc 功能的支持数据。

## 7.将`position:fixed`元素与 CSS `calc()`对齐

`calc()`功能的另一个用例是将元素与固定位置对齐。例如，如果您有一个左右间距可变的内容包装器，并且您想要精确地对齐该包装器内部的固定元素，那么您将很难决定为“right”或“left”属性选择哪个值。使用`calc()`，您可以结合相对值和绝对值来完美对齐元素:

```
.wrapper {
  max-width: 1060px;
  margin: 0 auto;
}
.floating-bubble {
  position: fixed;
  right: calc(50% - 530px); /* 50% - half your wrapper width */
}
```

这里有一个例子:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[用 CSS calc()](http://codepen.io/SitePoint/pen/NAVRZQ/) 对齐“位置:固定”元素。

## 8.带`cubic-bezier()`的动画

为了让网站或应用程序的用户界面更有吸引力，你可以使用动画，但标准的放松选项非常有限。比如`"linear"`或者`"ease-in-out"`。像弹性动画这样的东西在标准选项下甚至是不可能的。使用`cubic-bezier()`功能，你可以完全按照你想要的方式制作动画元素。

有两种方法可以使用`cubic-bezier()` — [理解它背后的数学](http://roblaplaca.com/blog/2011/03/11/understanding-css-cubic-bezier/)并自己建造它，或者使用[三次贝塞尔生成器](http://cubic-bezier.com/)。

老实说，我会选择后者。

## 结论

巧妙使用 CSS 函数不仅可以解决已知的问题，比如建立更智能的网格系统，还可以给你更多的创作自由。随着浏览器支持越来越好，你真的应该认真审视你的 CSS，并用类似`calc()`的功能来改进它。