# 如何在 HTML5 和 CSS3 中创建日历图标

> 原文：<https://www.sitepoint.com/create-calendar-icon-html5-css3/>

你看到 Simone Sala 最近的文章了吗，[如何在 Photoshop](/calendar-app-icon-photoshop/) 中创建日历应用图标？它看起来很棒，是智能手机日历应用的理想选择，也是 2014 年的热门话题。

![calendar icon](img/335c4efcf012366eeab0b960c3d75eff.png)

有没有可能在 HTML5 中创建一个类似的图标，以便在任何网页中使用和重用？当然会… *不然我也不会写这篇文章了！*

[**查看 HTML5 和 CSS3 日历图标演示**](http://cssdeck.com/labs/calendar-icon) (将鼠标悬停在元素上可以获得不错的效果！)

## 选项

几年前，我们可能需要 PNG 或 GIF 图像。你仍然可以这样做，但它们不容易改变，不能被搜索引擎索引，也不可扩展。更好的选择是可缩放矢量图形(SVG)；这将允许更多的灵活性，但我们不一定需要它。

因此，我将使用鲜为人知的 HTML5 `time`元素。它的唯一目的是表示日期和/或时间——可能是某个事件或周年纪念日。基本元素对 2014 年 9 月 20 日下午 9.01 使用以下语法:

```
<time>2014-09-20T21:01:00Z</time>
```

您可以只定义日期:`2014-09-20`、月份:`2014-09`或年份:`2014`。最后的“Z”代表协调世界时(UTC)。您可以用您自己的时区替换它，例如+01:00，-06:00 等。

虽然内容是机器可读的，但对人类来说并不容易。因此，您可以将日期/时间定义放在一个`datetime`属性中，并为特定于您的语言和地区的内容使用更友好的内容，例如

```
<time datetime="2014-09-20T21:01:00+08:00">9:01pm, Saturday September 20<sup>th</sup>, 2014</time>
```

当使用[微数据和微格式](http://schema.org/docs/gs.html#advanced_dates)时，您当然应该考虑`time`元素。

## 日历图标 HTML

我们的日历图标将使用以下 HTML 代码:

```
<time datetime="2014-09-20" class="icon">
  <em>Saturday</em>
  <strong>September</strong>
  <span>20</span>
</time>
```

`time`元素被赋予一个“icon”类来表示它应该被样式化。

我用`em`、`strong`和`span`表示日、月和日，主要作为 CSS 选择器挂钩。它们是没有严格语义的内联元素，所以即使没有样式，日期也是可读的。但是，如果需要，您可以使用不同的元素。

## 日历图标 CSS

首先，我们将定义图标的外部样式:

```
time.icon
{
  font-size: 1em; /* change icon size */
  display: block;
  position: relative;
  width: 7em;
  height: 7em;
  background-color: #fff;
  border-radius: 0.6em;
  box-shadow: 0 1px 0 #bdbdbd, 0 2px 0 #fff, 0 3px 0 #bdbdbd, 0 4px 0 #fff, 0 5px 0 #bdbdbd, 0 0 0 1px #bdbdbd;
  overflow: hidden;
}
```

图标的高度和宽度设置为 7em。因此，我们可以将字体更改为我们喜欢的任何大小，图标也会相应地缩放。[试试看！](http://cssdeck.com/labs/calendar-icon)

图标底部的页面效果是使用不同 y 偏移的多个`box-shadow`样式应用的。我最初考虑使用伪元素，但是它们不起作用，因为我们已经定义了 `overflow: hidden`(它将月份横幅裁剪为圆形边缘)。

请注意，我们已经设置了一个相对位置，因此可以定位内部元素。让我们应用他们的基本风格:

```
time.icon *
{
  display: block;
  width: 100%;
  font-size: 1em;
  font-weight: bold;
  font-style: normal;
  text-align: center;
}
```

通用选择器(*)将相同的样式应用于图标中的任何元素。这可能没问题，但是，如果你有不同的日历布局，你可能想更明确。

月份的样式使用以下代码:

```
time.icon strong
{
  position: absolute;
  top: 0;
  padding: 0.4em 0;
  color: #fff;
  background-color: #fd9f1b;
  border-bottom: 1px dashed #f37302;
  box-shadow: 0 2px 0 #fd9f1b;
}
```

这会将横幅放置在图标的顶部，并使用虚线边框创建穿孔。由于横幅比穿孔的边缘大，我在它下面用了另一个`box-shadow`。

日期位于底部边缘:

```
time.icon em
{
  position: absolute;
  bottom: 0.3em;
  color: #fd9f1b;
}
```

调整大日期数字的大小，并应用一些基本定位:

```
time.icon span
{
  font-size: 2.8em;
  letter-spacing: -0.05em;
  padding-top: 0.8em;
  color: #2f2f2f;
}
```

最后，我添加了一些当你悬停或聚焦在图标上时的动画——但是我让你自己去解决。

[**查看 HTML5 和 CSS3 日历图标演示…**](http://cssdeck.com/labs/calendar-icon)

该图标适用于所有现代浏览器，包括 IE9 和更高版本(动画适用于 IE10+)。在 IE8 及以下版本中，这种效果会逐渐减弱，但如果您将 HTML5 填充程序应用到页面，应该会看到合理的效果。然而，在开发演示时，我确实发现了一个不寻常的 Webkit/Blink 动画错误——但是我将把它留到另一篇文章中。

享受使用你自己的日历图标效果代码的乐趣。

## 分享这篇文章