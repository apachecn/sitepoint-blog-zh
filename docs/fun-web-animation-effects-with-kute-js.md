# 用 KUTE.js 制作有趣的网络动画效果

> 原文：<https://www.sitepoint.com/fun-web-animation-effects-with-kute-js/>

![KUTE.js Animation Library](img/f6dbf265added0d85c1f35686657c215.png)

我在这篇文章中的目的是介绍 [KUTE.js](http://thednp.github.io/kute.js/index.html) ，这是一个由 thednp 和 dalisoft 开发的开源、免费且功能丰富的 JavaScript 动画引擎。

这是 CSS:动态 DOM 动画库系列*的第二篇文章。如果您想了解更多关于如何最好地使用 web 上的动画，或者何时可以考虑使用 JavaScript 动画库而不是仅使用 CSS 的动画，本系列的第一篇文章 [*用 Anime.js*](https://www.sitepoint.com/animating-the-dom-with-anime-js/) 制作 DOM 动画包含一些有用的提示和资源。*

## 用 KUTE.js 可以做什么？

js 提供了一个核心动画引擎和一些插件来制作特定类型的属性动画。这种模块化架构有助于保持这个库的高性能和高度灵活性。

### 使用 KUTE.js 核心引擎可以制作什么动画

只需单独使用[核心引擎](http://thednp.github.io/kute.js/examples.html)，就可以制作动画:

*   不透明属性
*   除了`matrix`、双轴`skew`和双轴`scale`之外的所有 2D 变换属性
*   除了`matrix3d`和`rotate3d`之外的所有 3D 变换属性
*   以下盒子模型属性:`width`、`height`、`top`和`left`
*   `color`和`backgroundColor`属性
*   滚动动画，在`window`对象和任何可滚动的 DOM 元素上

### 用 KUTE.js CSS 插件可以制作什么动画

使用 [CSS 插件](http://thednp.github.io/kute.js/css.html)动画的可能性增加了。你可以这样做:

*   制作所有盒子模型属性的动画，如`margin`、`padding`、`borderWidth`等。
*   制作`borderRadius`属性的动画
*   动画显示文本属性，如`fontSize`、`lineHeight`、`letterSpacing`、`wordSpacing`等。
*   动画颜色属性，如`borderColor`和`outlineColor`
*   动画显示 [`clip`](https://developer.mozilla.org/en/docs/Web/CSS/clip) 属性，现在 CSS 中已弃用。
*   动画显示`backgroundPosition`属性。

### 使用 KUTE.js SVG 插件可以制作什么动画

SVG(可缩放矢量图形)插图和图标遍布网络。这不是偶然的。无论屏幕分辨率如何，SVG 图形看起来都很棒，用标记语言编写使它们更容易访问，并且当适当优化时，可以有一个小文件大小。

用 SVG 图形可以做的一件很棒的事情是制作它的不同部分的动画，KUTE.js 提供了一个很棒的插件，可以让你毫不费力地实现复杂的动画。

特别是，KUTE.js 的 SVG 插件允许您:

*   变形 SVG 路径
*   为 SVG 笔划属性设置动画
*   以可靠的跨浏览器方式制作 SVG 转换动画

### 使用 KUTE.js 属性插件可以制作什么动画

有了 Attributes 插件，KUTE.js 可以让你用动画显示任何数字表示属性，不管有没有像 px、em 等度量单位。这个插件，结合 SVG 插件，使得创建一些很酷的动画成为可能。

### 使用 KUTE.js 文本插件可以制作什么动画

用文本插件扩展 KUTE.js 可以让你以两种方式激活文本元素:

*   增加和减少数字的字符串表示
*   一次写一个字符串。

请访问 KUTE.js 网站上的[专用页面](http://thednp.github.io/kute.js/properties.html)了解其功能的全部详情:

## 使用 KUTE.js

是时候试验一下 KUTE.js 了。

### 将 KUTE.js 包含到您的项目中

你可以从项目网站的[下载按钮，从 KUTE.js](http://thednp.github.io/kute.js/index.html) [GitHub 页面](https://github.com/thednp/kute.js)，甚至是 [CDN 链接](http://www.jsdelivr.com/projects/kute.js)下载 KUTE.js。

然后，在结束的`</body>`标签之前，通过一个常规的`<script>`标签将`kute.min.js`文件包含在 HTML 文档中。

也可以使用 Bower 和 npm 安装 KUTE.js。

关于安装过程的所有细节都在 KUTE.js [安装页面](http://thednp.github.io/kute.js/start.html)上。

### 使用 KUTE.js 的简单单属性动画

KUTE.js 使用两种核心方法:

*   `.to()`允许你将单个元素上的 CSS 属性从一个给定的默认值或者从一个计算出的值变成一个想要的值。此方法最适用于简单的滚动或显示/隐藏动画，或者当您不知道要制作动画的属性的当前值时
*   使用`.fromTo()`你可以通过定义开始和结束值来激活一个元素。这种方法比前一种执行得更好，主要是因为 KUTE.js 不必计算动画的初始值。

`.to()`的语法是:

```
KUTE.to(
  element,
  {propertyName:propertyValue}
).start();
```

`.fromTo()`的语法是:

```
KUTE.fromTo(
  element,
  {fromPropertyName:fromPropertyValue}, 
  {toPropertyName: toPropertyValue}
).start();
```

上面的语法创建了一个 [tween 对象](http://thednp.github.io/kute.js/about.html)，即一个 JavaScript 对象，它存储了关于 DOM 元素动画的数据，例如 CSS 属性的定义、动画持续时间、动画延迟等。

需要指出的是，动画不是默认触发的。相反，要让动画继续，您需要调用补间对象上的`.start()`方法。

您还可以使用`.stop()`、`.pause()`和`.play()`来停止、暂停和恢复动画。

在开始、恢复或暂停动画之前，您可以分别使用`.playing`和`.paused`来检查动画当前是活动的还是非活动的:

```
tween.playing;
tween.paused;
```

为了体验一下，使用`.to()`将星形图标的`opacity`值从样式表中设置的初始值动画化为零值。使用元素的 class 属性选择元素。以下是相关片段:

```
KUTE.to('.icon-star-dark', {opacity: 0}).start();
```

查看 Pen [KUTE.js 动画中的一个属性。通过](http://codepen.io/SitePoint/pen/wgwrQj/) [CodePen](http://codepen.io) 上的 site point([@ site point](http://codepen.io/SitePoint))to()。