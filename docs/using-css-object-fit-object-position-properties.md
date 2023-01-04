# 使用 CSS 的对象适合和对象位置属性

> 原文：<https://www.sitepoint.com/using-css-object-fit-object-position-properties/>

视频和图像在设计网站时可能会带来问题。

假设您已经在 CSS 中明确指定了图像的宽度。如果宽度以百分比或视口单位指定，并且图像的高度设置为`auto`，调整浏览器窗口的大小将保持图像的纵横比。

但是，有时您可能空间有限，需要对图像的高度加以限制。现在调整浏览器窗口的大小肯定会打乱它的长宽比。

许多与图像大小和纵横比相关的问题，包括我刚刚讨论的问题，都可以使用 CSS 的对象匹配属性和对象位置属性来解决。

这些属性类似于我们更熟悉的`background-size`和`background-position`属性。所以，即使你以前从未听说过它们，你也不会很难理解它们是如何工作的。

这两个属性都适用于被替换的元素。然而，为了简洁起见，在本文中我将使用术语图像而不是被替换的元素。

## 为什么要用`object-fit`和`object-position`？

您可能想知道，当您可以使用`background-size`和`background-position`时，为什么还要使用这两个属性。其实至少有两个很好的理由。

首先，考虑一个场景，其中有一张图片应该是一篇文章的一部分。如果图像尺寸是在 CSS 中设置的，就像引言中描述的情况一样，调整浏览器窗口的大小会打乱它的纵横比。在这种情况下，您可能会尝试使用带有`background-size`和`background-position`属性的`div`，因为仅仅使用一个`img`标签是不够的。

这种解决方案的问题是，你应该总是试图保持你的内容和表现分开。背景属性旨在用于演示目的。当你的图像实际上是内容的一部分时，使用`object-fit`和`object-position`以及`img`标签来达到同样的效果更有意义。

另一个原因是背景属性不能应用于视频，而`object-fit`和`object-position`可以。因此，在显示视频时，`object-fit`和`object-position`是您唯一的选择。

## `object-fit`属性

此属性决定被替换的内容(如图像和视频)如何占据其内容框中的空间。它有五个可能的值:

*   `fill`
*   `contain`
*   `cover`
*   `none`
*   `scale-down`

使用该属性的语法如下所示:

```
.fit-image {
  object-fit: fill|contain|cover|none|scale-down;
}
```

当设置为`fill`时，图像大小完全适合内容框。在这种情况下，图像的高度等于盒子本身的高度。这也是`object-fit`属性的初始值。

见笔[对象拟合:在](http://codepen.io/SitePoint/pen/BKbGZz/) [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )填充。