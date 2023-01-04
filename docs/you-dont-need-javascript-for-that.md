# 你不需要 JavaScript 就能做到！

> 原文：<https://www.sitepoint.com/you-dont-need-javascript-for-that/>

今天的 Web 开发可能是各种技术的旋风，即使是最简单的小部件也可能非常复杂。

记住这一点，我想把重点放在只用 HTML 和 CSS 就能做的各种事情上，不需要 JavaScript。你会问为什么？虽然这些解决方案中的一些可能并不适用于每个用例，但它们确实激发了创新思维，从而降低了复杂性，并提供了更广泛的浏览器支持。这里有一些很酷的事情，你不用写一行 JavaScript 就可以做到。

## 构建选项卡小部件

我们都知道 [checkbox hack](http://www.thecssninja.com/talks/abuse_checkboxes/) 如何在没有 JavaScript 的情况下用于交互性，但不幸的是，这是以可访问性为代价的。因此，我想展示如何使用 CSS 中的`:focus`伪类创建一个可访问的 tabs 小部件，而不是另一个复选框教程。

### 使用:聚焦

[`:focus`伪类](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus)用于定位用户(通过使用键盘或鼠标)获得焦点的元素。包括 IE8+在内的所有浏览器都支持它。此外，您可以将一个`:focus`状态应用到任何 HTML 元素，只要您给它一个`tabindex`属性。

### 使用`tabindex`属性

HTML 的 [`tabindex`属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes#tabindex)表示一个元素是否可以获得焦点。它可以取几个值，包括负值、零或正值。这些值中的每一个都决定了元素应该关注的顺序。

### 选项卡小部件的演示

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到 Pen [纯 CSS Tabs Widget](http://codepen.io/SitePoint/pen/tcCnu/) 。