# 极客场景背后:花哨的字体和活泼的输入框

> 原文：<https://www.sitepoint.com/behind-the-geek-out-scenes-fancy-fonts-and-jaunty-input-fields/>

在启动了 [Geek Out](https://www.sitepoint.com/behind-the-geek-out-scenes-fancy-fonts-and-jaunty-input-fields/ "Geek Out") 之后，我们认为展示我们如何构建页面中一些更漂亮的效果可能会很有趣。

使用的两个 CSS 效果是`@font-face`和`transform: rotate`。有了这两种 CSS 样式，您可以制作一些非常漂亮的表单。

是那些被许多设计师忽略的小宝石之一。在 Internet Explorer 5、Firefox 3.5、Safari 3.1、Opera 10、Chrome 4 中引入了对它的支持，它是 CSS2 规范的一部分。然而，困难在于字体格式支持。Internet Explorer 将只支持 EOT(嵌入式 OpenType)文件，而 Mozilla、Safari、Opera 和 Chrome 将愉快地接受 TTF (TypeType)字体。

| 浏览器 | 磁带结束符 | TrueType 字型文件 | 光学传递函数 | 挽救（saving 的简写） | WOFF |
| --- | --- | --- | --- | --- | --- |
| 微软公司出品的 web 浏览器 | X |  |  |  | X (IE9) |
| Mozilla 浏览器 |  | X | X |  | X |
| 旅行队 |  | X | X | X |  |
| 歌剧 |  | X | X | X |  |
| 铬 |  | X |  | X | X |

正如您在表中看到的，支持跨四种格式，但是您可以通过使用 EOT 和 TTF 安全地处理几乎所有查看您站点的浏览器。迎合所有用户的最简单的方法是使用 Paul Irish 的[防弹@font-face 语法](http://paulirish.com/2009/bulletproof-font-face-implementation-syntax/ "Paul Irish's Bulletproof @font-face syntax")。它依赖于你以特定的顺序包含不同的文件格式，这样所有的浏览器将只使用他们能使用的第一种格式。

`@font-face`可以如下图所示实现，并且有几个移动部件。`font-family`声明允许您命名字体，因此您可以设置元素在以后使用它。所有的`src: url()`声明都是字体文件的路径。没有必要拥有*所有的*文件格式，但是如果你有，你也可以使用它们。这样，它们覆盖了最大可能的浏览器份额，并提供了更好的选择(例如，WOFF 是一种压缩的无损格式，因此文件大小更小，但保持了与 TTF/OTF 格式文件相同的质量水平)。Paul Irish 有一个很好的技巧:命名主文件集意味着您不会意外地使用文件的本地副本(即存储在用户计算机上的副本)，因此您知道您的页面将使用您想要的字体。

```
@font-face {
font-family: 'Graublau Web';
src: url('GraublauWeb.eot');
src: local('☺'),
  url("GraublauWeb.woff") format("woff"),
  url("GraublauWeb.otf") format("opentype"),
  url("GraublauWeb.svg#grablau") format("svg");
}

/* Now you can use the font on any element you like. */
h1 {
  font-family: 'Graublau Web';
} 
```

最后，有一些很好的方法可以获得免费的字体。谷歌字体目录提供了一个免费字体的小列表(我们在这里使用了龙虾字体)，以及 [FontSquirrel 的字体工具包](http://www.fontsquirrel.com/fontface "FontSquirrel's Font Kits")。

![](img/90573e5372a398e16c3ec08b24bd6ff3.png "geekout")

第二个 CSS 效果是`transform: rotate`样式。Webkit、Firefox 3.5 及以上版本、Opera 及以上版本都提供了对`transform: rotate`的支持。像大多数新的 CSS3 样式一样，它们使用特定于浏览器的命名约定。

```
-webkit-transform: rotate(-13deg);
-moz-transform: rotate(-13deg);
-o-transform: rotate(-13deg)
```

Internet Explorer 有一个使用`filter: progid:DXImageTransform.Microsoft.BasicImage(rotation=3);`的基本实现，其中 0、1、2 和 3 分别将应用的元素旋转 0 度、90 度、180 度或 270 度。

因此，如果你在这四个方向上旋转某个东西，你可以使用所有四种 CSS 样式来覆盖所有浏览器，代码如下所示:

```
filter: progid:DXImageTransform.Microsoft.BasicImage(rotation=1);
-webkit-transform: rotate(90deg);
-moz-transform: rotate(90deg);
-o-transform: rotate(90deg)
```

如果像我们对 Geek Out 页面所做的那样，我们想以任意方向(在我们的例子中是-13 度)旋转表单，Internet Explorer 将不得不停止这个操作。IE 将正常显示它(没有应用旋转)，而其他浏览器将播放并很好地旋转。要了解更多，看看黄邦贤·斯努克的文章[用 CSS](http://snook.ca/archives/html_and_css/css-text-rotation "Jonathon Snook's Text Rotation with CSS") 进行文本旋转。

希望这对您有所帮助！让表单变得有趣是一件有趣的事情，你不觉得吗？

## 分享这篇文章