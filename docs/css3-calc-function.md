# CSS3 Gems:calc()函数

> 原文：<https://www.sitepoint.com/css3-calc-function/>

模块化的 CSS3 规范中有许多隐藏的瑰宝。在这篇文章中，我们将看看 calc()；一个非常有用的属性，可能会改变你的布局设计方式。

CSS3 calc()函数主要用于计算长度、数字、角度、过渡/动画时间或声音频率。但是，它允许您混合度量类型——这是 CSS 中一个强大的概念。

考虑一个包含两个浮动元素的网站布局。您希望两个元素的宽度相等，由 60px 的水平边距分隔。听起来很容易？在定宽设计中不是问题；如果页面宽度是 960 像素，两个元素都是 450 像素。

但是流畅的或者反应式的布局呢？不可能确定页面宽度，所以大多数开发人员会将每个元素设置为，比如说，45%。如果页面恰好是 600 像素，则 10%的边距只有 60 像素；更宽或更窄的浏览器窗口会相应地放大或缩小边距。

幸运的是，新的 calc()函数允许我们计算宽度。在这种情况下，我们希望两个元素都是 50%减去 30px，例如

```
#element1, #element2 { 
  float: left;
  width: calc(50% - 30px);
}

#element2 { 
  margin-left: 60px;
}
```

也许你想要一个相对于字体大小的边距，比如 4em？没问题:

```
#element1, #element2 {
  width: calc(50% - 2em);
}
```

或者，您可能希望两个元素周围都有 2px 边框:

```
#element1, #element2 { 
  width: calc(50% - 2em - 4px);
  border: 2px solid #000;
}
```

我建议你保持简单的计算，但是有可能使用复杂的公式，例如

```
 #element1, #element2 { width: calc((50% + 2em)/2 + 14px); } 
```

## 浏览器支持

calc()函数是 W3C 推荐的，所以猜猜哪个浏览器提供本地支持？

你错了。在写这篇文章的时候，它只是 Internet Explorer 9。Firefox 也支持带前缀的；-moz-calc()。它还没有在 webkit (Chrome 和 Safari)或 Opera 中实现，但是它非常有用，我想我们不需要等太久。

幸运的是，您可以在样式表中使用渐进式增强:

```
#element1, #element2 {
  width: 45%; /* all browsers */
  width: -moz-calc(50% - 30px); /* Firefox 4+ */
  width: calc(50% - 30px); /* IE9+ and future browsers */
}
```

请记住，您还需要相应地调整页边距，例如

```
#element2 {
  margin-left: 10%; /* all browsers */
  margin-left: -moz-calc(60px); /* Firefox 4+ */
  margin-left: calc(60px); /* IE9+ and future browsers */
}
```

## CSS3 最小值()和最大值()

如果你喜欢 calc()，你会喜欢 min()和 max()函数。它们接受两个或多个逗号分隔的值，并相应地返回最小值或最大值，例如

```
#myelement {
  width: max(300px, 30%, 30em);
  font-size: min(10px, 0.6em);
}
```

当使用相对字体大小来确保文本不会变得过大或过小时，这些函数尤其有用。

不幸的是，目前任何最新的浏览器都不支持 min()和 max()。让我们希望他们很快出现。

## 分享这篇文章