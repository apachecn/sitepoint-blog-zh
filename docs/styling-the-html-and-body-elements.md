# html 和 body 元素的样式

> 原文：<https://www.sitepoint.com/styling-the-html-and-body-elements/>

在 HTML 中开始布局的一个最常见的方法是:

```
<html>
  <head>...</head>
  <body>
    <div id="wrapper">
      <div id="container">
        ...
      </div>
    </div>
  </body>
</html>
```

这就是老的双包`div`布局技术。但是，因为我们已经有了`html`和`body`元素，所以`div`元素在很多情况下可能是多余的。因此，为了找出 CSS 样式是否可以像我们辛勤工作的包装器一样应用于`html`和`body`元素，我在 FireFox 3、Safari 3.2、Opera 9.6 和 Internet Explorer 6、7 和 8 中测试了一系列 CSS 属性。这是我的发现。

您可以向`html`和`body`元素添加填充，它可以在所有测试的浏览器中工作。然而，边界是另一回事。虽然你可以在所有测试过的浏览器中给`html`元素添加边框，但是 IE6 和 ie7 只允许实心边框。此外，IE6 扩展了整个视窗的边框，不管有多少内容可见，甚至扩展到滚动条的右侧。

[![](img/1dad57aad19b0a7869954522d8b92726.png)](https://i2.sitepoint.com/blogs/wp-content/uploads/2009/02/tt227-ie-border.jpg)

向`html`元素添加边距在所有测试的浏览器中都有效，除了 IE6，IE6 忽略了它。不管边距如何，`html`或`body`元素上的背景色将覆盖整个视窗。

我发现的一个奇怪之处是，如果你在`html`元素上有一个背景图像和一个顶部边距，Safari 和 IE7 会忽略这个边距，从视窗的左上角开始显示背景。Firefox Opera 和 IE8 会像普通元素一样在空白区域显示背景图片。所有浏览器都尊重`body`元素的边距，并正确放置背景图像。

如果你尝试一些疯狂的 CSS，比如绝对定位`html`元素，你会发现它实际上在 Opera、IE7 和 ie8 中工作。但是 Firefox 和 IE6 会忽略，Safari 会极度混乱，显示一片残破的乱七八糟。

![](img/399a5c9c43654c55c632e24acc3e02b6.png)

那么什么有效呢？向`html`和`body`元素添加背景图像效果很好，你可以用它来代替目前只有 Safari 支持的多个背景图像。SitePoint 书中的解决方案之一，[*CSS 选集*](https://www.sitepoint.com/books/cssant2/) 使用了这种技术。

您会很高兴地注意到，将一个固定宽度的列居中很容易:

```
html {
  background-color: #333;
}
body {
  background-color: #fff;
  width: 750px;
  margin: 0 auto;
}
```

上述代码将在所有测试的浏览器中完美运行。

[![](img/be61e5e03c6004ed0cd3a96ad525250e.png)](https://i2.sitepoint.com/blogs/wp-content/uploads/2009/02/tt227-final.jpg)

不过有一个大的*问题*:如果你需要对`body`元素内部的元素使用绝对或相对定位。我假设由于所有元素默认从`body`元素获得定位上下文，如果我将`body`元素居中，默认定位上下文应该相应调整。我错了！默认定位上下文对于视口保持固定；您必须将`position:relative;`添加到`body`风格中，以创建新的定位环境:

```
body {
  position: relative;
  background-color: #fff;
  width: 750px;
  margin: 0 auto;
}
```

令人耳目一新的是，这在所有测试过的浏览器中都是一致的。

## 分享这篇文章