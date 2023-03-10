# 带有@supports 的原生 CSS 功能检测

> 原文：<https://www.sitepoint.com/supports-native-css-feature-detection/>

考虑下面的 CSS 片段:

```
 .blur-text { color: transparent; text-shadow: 0 0 5px #000; } 
```

任何具有类`blur-text`的元素都将呈现模糊的文本效果:

![CSS3 Blurred Text](img/48f43775717d384061ccd7dc24bcdafa.png)

很可爱。遗憾的是，并不是所有的浏览器都支持`text-shadow`。IE9 和更低版本应用透明色，但不显示阴影，文本变得不可见。我们需要依靠 JavaScript 解决方案，如 [Modernizr](http://www.modernizr.com/) 或[我们自己的文本阴影检测代码](https://www.sitepoint.com/detect-css3-property-browser-support/)来检测 CSS 支持并做出相应的反应。

使用 JavaScript 来检测 CSS3 属性是很糟糕的。如果 JavaScript 被禁用并且感觉很脏，它就会失败。每次做都要用消毒液洗澡。幸运的是，另一个解决方案出现在原生 CSS 中:`@supports`规则。基本语法:

```
 @supports <condition> { /* rules */ } 
```

我们将对模糊文本使用以下代码:

```
 @supports (text-shadow: 0 0 5px #000) { .blur-text { color: transparent; text-shadow: 0 0 5px #000; } } 
```

你可以用逻辑 AND、OR 和 NOT，例如

```
 @supports ( (display: table-cell) and (display: list-item) ) { /* styles */ } 
```

以及检查特定于供应商的前缀:

```
 @supports ( (-webkit-transform: rotate(90deg)) or (-moz-transform: rotate(90deg)) or (-ms-transform: rotate(90deg)) or (-o-transform: rotate(90deg)) or (transform: rotate(90deg)) ) { /* styles */ } 
```

不理解`@support`的旧浏览器不会呈现样式——但是它们不太可能理解你试图使用的属性。

不幸的是，`@supports`的浏览器兼容性相当有限。在撰写本文时，只有 Opera 12.1 将它作为标准配置。如果`layout.css.supports-rule.enable` about:config 设置改为 true，在 Firefox 17 中可用。它应该也能在 Chrome 24 中实现，但是`@support`在 IE 和 Safari 中出现可能还需要一段时间。

也就是说，`@supports`很有前途，使用类似 Modernizr 的 JavaScript 风格检测的日子已经屈指可数了。

## 分享这篇文章