# ！重要实际上是有用的(在打印样式表中)

> 原文：<https://www.sitepoint.com/important-is-actually-useful-in-print-style-sheets/>

![!important](img/28b3970fcb635065569412dff801b454.png "!important")可怜的老 [`!important`](https://reference.sitepoint.com/css/importantdeclarations) 的声明在 CSS 社区受到了很多抨击，而且理由很充分。这是不必要的，创造了一个维护的噩梦，并使级联空洞的嘲弄。我已经很多年没有想过使用`!important`了，直到我发现了它的一个非常好的用途:打印样式表。

让我们假设我们想要创建一个图片库。尽我们最大的努力实现渐进增强的理念，我们首先确保图片包含在一个 HTML 列表中。最终，我们希望使用 JavaScript 创建一个淡入/淡出的幻灯片效果，但我们的基础是一列图像。如果访问者禁用了 JavaScript，他们就会看到这个。

好的，目前为止还不错。然而，当我们开始制作打印样式表时，我们会发现我们的计划中有一个缺陷:当您使用 JavaScript 来制作 HTML 元素的动画时，您不可避免地会修改元素的`style`属性。在我们的幻灯片中，JavaScript 改变了图像的`position`和`opacity`，所以一次只能显示一个。当我们打印 gallery 页面时，虽然我们想要单列图像，但我们最终打印的只是单个图像。这是因为您无法为元素的`style`属性中声明的 CSS 指定目标媒体类型；你不能说在`style`属性中声明的所有样式都只适用于`screen`。

因此，您需要的是一种指定打印样式的方法，它可以覆盖内联样式。只有一种方法可以做到。在您的打印样式表中，重置图像元素的`position`和`opacity,`，并添加`!important`:

```
img.gallery {
  position: static !important;
  opacity: 1 !important;
}
```

真的就是这样；发现了`!important`的合法用途，想分享一下。

## 分享这篇文章