# 你知道这八个 HTML5 标签吗？

> 原文：<https://www.sitepoint.com/eight-html5-tags-you-might-not-know/>

作为一名 web 开发人员，在构建下一个站点时，您可能会利用各种不同的标签。你很有可能已经精通 HTML5 中引入的一些众所周知的标签，比如`<article>`、`<header>`和`<footer>`，但是你可能不知道一些你可以利用的鲜为人知的/边缘的标签。

其中一些标签要么是 HTML5 中的新标签，要么是 HTML4 规范中的新标签。从 HTML4 重新利用的标签可能看起来很熟悉，但是它们被赋予了新的含义，并且在使用方式上有所改变。

对于每个标签，我们将仔细阅读 W3C 规范，并应用一个实际的例子来展示如何使用它。我们走吧！

### 简单说一下解释

虽然 W3C 规范对于概念性概述来说很棒，但有时它们缺乏实际的例子，这使得在寻找使用这些标签的最佳方式时很有挑战性。

你可能以前使用过其中的一些标签——也许只是方式不同。对于什么被认为是好的实践，通常没有硬性的规则。以下是我建议如何对待和使用这些鲜为人知的元素的一些例子。

## 1–用`<mark>`突出显示上下文

`<mark>`标签的[规范说这个标签应该用来表示*“相关性”或者“审查”*。](https://www.w3.org/TR/html5/text-level-semantics.html#the-mark-element)

“相关性”很难描述。当我们执行一项活动时，元素和文本是相关的，它们在那时对我们是有用的(或者在将来可能是有用的)。

例如，如果您使用关键字“jQuery”搜索一个网站，出现了几篇文章，您可以将结果匹配包含在`<mark>`标签中。标记的目的是告诉浏览器*“嘿，这个东西和你正在做的事情有关”*。

### 实际例子

我们可以使用标记标签来突出相关的内容。为了说明这一点，请考虑以下场景:

我们在一个名为“最便宜的度假套餐”的页面上，它向我们展示了一个按价格范围排序的度假套餐网格。它从顶部最便宜的开始，到底部最贵的。

对于顶级假期，价格本身可以用`<mark>`标签突出显示，因为我们来到这个页面是为了便宜的假期交易，这些是这个页面提供的最便宜的——它们是最相关的。

```
<section class="deal-list">
  <article>
    <h2>Vanuatu Cruise</h2>
    <p><mark>$499</mark>- 5 Nights</p>
    <p>A relaxing cruise around the southern most 
    parts of Vanuatu</p>
  </article>
  <article>
    <h2>Fiji Resort Getaway</h2>
    <p><mark>$649</mark> - 6 Nights</p>
    <p>Includes all you can eat buffet and 
    entertainment</p>
  </article>
  <article>
    <h2>Pacific Island Hiking</h2>
    <p>$1199 - 5 Nights</p>
    <p>Hike your way though several pacific islands 
    on this exercise focused holiday</p>
  </article>
</section>
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[标记标签示例](http://codepen.io/SitePoint/pen/QNLyLV/)。