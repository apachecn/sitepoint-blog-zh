# 可以用于字体大小的 CSS 长度单位的演练

> 原文：<https://www.sitepoint.com/a-walkthrough-of-css-length-units-you-can-use-for-font-size/>

![A Walkthrough of CSS Length Units You Can Use for Font Size](img/54cb13d7549a062a1c53f92c2ff9ac04.png)

这篇文章由汤姆·哈金斯同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

CSS 为开发者提供了许多单元来指定不同属性的长度，如`margin`、`padding`、`line-height`或`font-size`。我们有许多单元的原因是它们被设计用于不同的目的。尽管您可以使用这些单位为给定的 CSS 属性指定相同的值，但是该值的实际**大小**是以不同的方式计算的。这可以使一些单元在其他单元可能做得不好的情况下有用。例如，如果您希望一个元素的`width`或`height`属性依赖于视口的宽度或高度，唯一可信任的单位是`vh`、`vw`、`vmin`和`vmax`。

在本文中，您将了解不同的长度单位，以及它们如何影响应用它们的元素的字体大小。

## 像素单位

像素是固定大小的单位。它们通常被称为用户屏幕上的一个点。然而，现在的设备可以具有不同的像素密度。这意味着，在像素密度是标准器件 4 倍的器件上，这个我们一般称之为像素的点的大小将是 1/4 左右。通过使用参考像素来计算 CSS 像素的大小，可以避免这个问题。**参考像素**定义为设备上一个像素的视角，像素密度为 96dpi，与阅读器的距离为一臂长(等于 28 英寸)。这使得像素的大小等于大约 0.26 毫米

以像素为单位设置网页上不同文本元素的`font-size`，既不容易维护，也不方便用户使用。如果你正在重新设计一个非常大的网站，改变所有元素的字体会变成一场噩梦。你还必须在不同的断点调整许多元素的`font-size`来适应不同的屏幕尺寸。此外，任何想要使用浏览器字体大小设置来缩小或放大文本的用户都无法做到这一点。

让我们看看以像素为单位设置的字体大小如何影响不同元素的计算值`font-size`。这是我们将在接下来的几节中用作参考的标记。

```
<div class="container-box">
  This text is directly inside the parent div element.
  <p>This is the first paragraph of our container.</p>
  <p>The second paragraph contains a link to <a href="http://en.wikipedia.org/">WikiPedia</a>, the free encyclopedia.</p>
  <p>I have also included a link to SitePoint as a direct child of the containing div element.</p>
  <a href="https://www.sitepoint.com/">A link to SitePoint.</a>
</div>
```

下面是以像素为单位设置所有元素的字体大小属性的 CSS。

```
div {
  font-size: 20px;
}

code {
  font-size: 18px;
}

p, a {
  font-size: 22px;
}
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )设置字体大小像素的笔[。](https://codepen.io/SitePoint/pen/YVLGbv/)