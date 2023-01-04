# HTML5“时间”元素指南

> 原文：<https://www.sitepoint.com/html5-time-element-guide/>

时间——少数已知的无限事物之一。人类，以及动物和植物，从一开始就和时间打交道。

在网络上，这种需求并没有什么不同。即使在这种媒介中，我们也需要向其他人传达在某一点、某一特定日期或与另一设定时间相关的某件事。

在 HTML5 之前，我们没有从语义上标记日期或时间的元素。近年来，其他解决方案，最著名的是[微格式](http://microformats.org/)和[微数据](http://en.wikipedia.org/wiki/Microdata_%28HTML%29)，已经尝试为特定情况(出生日期、图书出版等)填补这一空白。

在本文中，我将介绍 HTML5 的`<time>`元素，它有助于解决刚才讨论的需求。

## 什么是`<time>`元素？

在 2009 年，HTML5 规范中引入了`<time>`元素。后来在 2011 年，为了支持`<data>`，它被放弃了。然后`<time>`被重新引入，然后被改进以允许新的日期/时间格式。由此可以看出，规范可能会引起很大争议。

`<time>`元素表示公历[中的日期和/或时间](http://en.wikipedia.org/wiki/Gregorian_calendar)。它是一个内嵌元素(像`<span>`和`<a>`)，并且必须有一个结束标签(像`<div>`和`<span>`)。以最简单的形式使用时，元素的内容必须是有效的日期和/或时间字符串。

下面显示了一个示例:

```
<!-- 1st February 2009 -->
<time>2009-02-01</time>
```

在上面的代码中，我定义了一个日期，特别是 2009 年 2 月 1 日。如果您在 Linux 上花过一些时间，那么代码中使用的格式(`yyyy-mm-dd`)应该很熟悉，但是，正如我们将在本文后面看到的，这不是唯一有效的格式。

在规范的第一稿中，精确的日期是您可以编写的少数几种格式之一。例如，您不能指定像“2014 年 11 月”或“476”(中世纪的开始)这样的日期。这对于几个案例来说是个大问题，比如一幅画或一个历史事件的年代，因为确切的日期是未知的。

幸运的是，这种类型的日期现在在规范中是允许的。所以今天我们可以描述一年中没有一天的某个月:

```
<!-- January 2014 -->
<time>2014-01</time>
```

## `datetime`属性

元素的规范也标准化了一个叫做 T1 的属性。

虽然用上一节讨论的格式书写日期在某些国家/文化中行得通，但它不适合其他国家/文化。例如，意大利人用格式`dd/mm/yyyy`写日期。因此，以另一种格式显示日期可能会导致混乱。

使用`<time>`元素的`datetime`属性可以很容易地解决这个问题。它是一个可选属性，包含机器可读格式的信息，就像示例中看到的那样，允许我们以任何喜欢的方式编写元素的内容。

事实上，如果没有指定`datetime`，内容必须是有效的日期/时间格式之一，否则我们可以随意使用它。这很棒，因为它允许我们编写如下代码:

```
The next meeting is scheduled for <time datetime="2014-10">October</time>.
```

或者甚至:

```
The next meeting is scheduled for <time datetime="2014-10">next month</time>.
```

根据规范，这两个例子都有机器可读的日期内容，但是它们是可以接受的，因为存在属性`datetime`，而*使用了有效的格式。*

 *乍一看，这似乎很奇怪。但是`<time>`元素的内容是为人类服务的，而不是为机器服务的。此外，这个事实允许日期的国际化。例如:

```
<!-- Same message as before but in Italian -->
Il prossimo incontro è programmato per <time datetime="2014-10">il mese prossimo</time>.
```

在上面的代码中，我们有和以前一样的消息，但是是用意大利语。

## `pubdate`属性

规范的第一稿为`<time>`元素定义了一个`pubdate`属性。这个属性是一个布尔值，表示给定的日期是父`<article>`元素的发布日期，或者如果没有父`<article>`元素，则是整个文档的发布日期。

例如，您可以写:

```
<article>
  <h1>A good title</h1>
  <p>This is the content of a great article.</p>
  <footer>
    <p>Article published on <time datetime="2014-09-05" pubdate>September the 5th, 2014</time>
  </footer>
</article>
```

在这种情况下，2014 年 9 月 5 日将是这篇“文章”的发表日期。

自从我知道了这个属性，我就一直是它的忠实粉丝，但不幸的是，它被从规范中删除了。这个决定产生了一个大问题，因为很多人(包括我)用发表日期来判断一篇文章或新闻的新鲜度和相关性。虽然您确实仍然可以访问文章的页面并查看出版日期，但我们需要一种标准的方法让机器读取日期。

目前还没有一个属性可以替代`pubdate`，但是您可以使用[博客发布模式](http://schema.org/BlogPosting)，特别是`datePublished`值，如下所示:

```
<article itemscope itemType="http://schema.org/BlogPosting">
  <h1 itemprop="headline">A good title</h1>
  <p itemprop="articleBody">This is the content of a great article.</p>

  <footer>
    <p>Article published on <time datetime="2014-09-05" itemprop="datePublished">5th September 2014</time>
  </footer>
</article>
```

现在您已经对`<time>`元素有了一个完整的概述，让我们看看允许的几种格式。

在没有`datetime`属性的情况下，`<time>`元素内容的有效格式以及`datetime`属性本身的有效格式将在下面的章节中描述。

## 有效的月份

这应该是一个字符串，以`yyyy-mm`的形式指定一年中的特定月份。例如:

```
<!-- September 2014 -->
<time>2014-09</time>
```

## 有效日期(一个月中的某一天)

这应该是一个以`yyyy-mm-dd`形式指定精确日期的字符串。例如:

```
<!-- 16th September 2014 -->
<time>2014-09-16</time>
```

## 有效的无年份日期

这应该是一个字符串，以`mm-dd`的形式指定一个月和一天，而不是一年。例如:

```
<!-- 29th June -->
<time>06-29</time>
```

## 有效时间

这应该是一个指定时间的字符串，没有日期，使用 24 小时格式，形式为`HH:MM[:SS[.mmm]]`，其中:

*   `H`代表小时
*   `M`代表分钟
*   `S`代表秒
*   `m`代表毫秒
*   方括号表示可选的部分

这种格式的一个例子如下所示:

```
<!-- 16 hours and 10 minutes
    (or 4 hours and 10 minutes pm) -->
<time datetime="16:10">afternoon</time>
```

另一个例子是:

```
<!-- 18 hours, 20 minutes, and 30 seconds
    (or 6 hours, 20 minutes, and 30 seconds) -->
<time>18:20:30</time>
```

## 有效的浮动日期和时间

W3C 规范中有这种格式，但 WHATWG 版本中没有。这应该是精确的日期和时间，格式为`yyyy-mm-ddTHH:MM[:SS[.mmm]]`或`yyyy-mm-dd HH:MM[:SS[.mmm]]`。例如:

```
<!-- 16th September 2014 at 18 hours,
    20 minutes, and 30 seconds -->
<time datetime="2014-09-16T18:20:30">Tuesday at 18:20</time>
```

## 有效的时区偏移量

这应该是表示时区偏移量的字符串。例如:

```
<!-- GMT+1 (like Italy) -->
<time>+01:00</time>
```

## 有效的全局日期和时间

这应该是一个表示完整日期的字符串，包括时间和时区。例如:

```
<!-- 16th September 2014 at 18 hours,
    20 minutes, and 30 seconds in a 
    time zone of GMT+1 (like Italy) -->
<time>2014-09-16T18:20:30+01:00</time>
```

## 有效的一周

这应该是一个表示一年中某周的字符串。例如:

```
<!-- The 18th week of 2014 -->
<time>2014-W18</time>
```

## 有效年份

这应该是一个表示年份的字符串。例如:

```
<!-- 2014 -->
<time datetime="2014">this year</time>
```

## 有效的持续时间字符串

这应该是一个表示持续时间的字符串。持续时间可以以前缀“P”开始，代表“周期”，并使用“D”来标记天数。例如:

```
<!-- A duration of four days -->
<time datetime="P4D">four days</time>
```

如果您需要进一步指定周期，您可以在“D”后添加一个“T”，代表“时间”，使用“H”代表小时，“M”代表分钟，“S”代表秒。像这样:

```
<!-- A duration of four days,
    four hours, and three minutes -->
<time datetime="P4DT4H3M">four days</time>
```

这种格式还允许您指定一个或多个持续时间组件。

## 限制

当前的规范对使用`<time>`元素定义的内容有一些限制。其中一个限制是你不能指出日期范围。因此，如果你正在写一篇关于持续一天以上的会议的帖子，例如从 2014 年 6 月 26 日到 2014 年 6 月 28 日，你必须使用两个`<time>`元素。一个很好的例子可以在我的网站的[演讲页面中找到，我在这里使用了`<time>`元素，如下所示:](http://aurelio.audero.it/speaking)

```
<time datetime="2014-06-28">26<span class="hidden">June 2014</span></time>-<time datetime="2014-06-28">28 June 2014</time>
```

另一个限制是你不能使用`<time>`元素来表示在[公共纪元](http://en.wikipedia.org/wiki/Common_Era)之前的日期。

## 浏览器支持

基于 MDN 上的 [`<time>`元素文章，此元素在以下版本中受支持:](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)

*   铬 33+
*   Firefox 22+
*   Internet Explorer 9+
*   Opera 22+
*   Safari 7+

然而，在旧的浏览器中没有太多需要担心的。事实上，如果它缺乏对这个元素的支持，浏览器会简单地将它显示为一个未知的内联元素。

## 结论

如果你还没有开始在你的页面中使用`<time>`元素，我希望这个指南能启发你这样做。

要了解更多信息，以下是一些相关链接:

*   W3C 上的 [`<time>`元素](https://www.w3.org/html/wg/drafts/html/master/text-level-semantics.html#the-time-element)
*   WHATWG 上的 [`<time>`元素](https://html.spec.whatwg.org/multipage/semantics.html#the-time-element)
*   MDN 上的 [`<time>`元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)
*   [`<time>`WHATWG 上的元素 Wiki](https://wiki.whatwg.org/wiki/Time_element)

*这篇文章在[无表格](http://tableless.com.br/um-guia-para-o-elemento-time-html5/)T3 上也有葡萄牙语版本*

## 分享这篇文章*