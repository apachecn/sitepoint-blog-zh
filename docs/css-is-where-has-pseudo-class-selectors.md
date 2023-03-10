# CSS :is，:where 和:如何让伪类选择器工作

> 原文：<https://www.sitepoint.com/css-is-where-has-pseudo-class-selectors/>

**[CSS 选择器](https://www.sitepoint.com/css-selectors/)允许你通过类型、属性或者在 HTML 文档中的位置来选择元素。本教程解释了三个新选项— :is()、:where()和:has()。**

选择器通常用在样式表中。以下示例定位所有`<p>`段落元素，并将字体粗细更改为粗体:

```
p {
  font-weight: bold;
} 
```

您还可以在 JavaScript 中使用选择器来定位 DOM 节点:

*   [document.querySelector()](https://developer.mozilla.org/docs/Web/API/Document/querySelector) 返回第一个匹配的 HTML 元素
*   [document . queryselectorall()](https://developer.mozilla.org/docs/Web/API/Document/querySelectorAll)以类似数组的 [NodeList](https://developer.mozilla.org/docs/Web/API/NodeList) 返回所有匹配的 HTML 元素

[伪类选择器](https://www.sitepoint.com/getting-to-know-css3-selectors-structural-pseudo-classes/)基于 HTML 元素的当前状态定位它们。也许最广为人知的是`:hover`，它在光标移动到一个元素上时应用样式，所以它被用来高亮显示可点击的链接和按钮。其他[热门选项包括](https://developer.mozilla.org/docs/Web/CSS/Pseudo-classes):

*   `:visited`:匹配访问过的链接
*   `:target`:匹配文档 URL 的目标元素
*   `:first-child`:以第一个子元素为目标
*   `:nth-child`:选择特定的子元素
*   `:empty`:匹配没有内容或子元素的元素
*   `:checked`:匹配选中的复选框或单选按钮
*   `:blank`:样式化一个空的输入字段
*   `:enabled`:匹配[一个启用的输入字段](https://www.sitepoint.com/atoz-css-enabled-pseudo-class/)
*   `:disabled`:匹配禁用的输入字段
*   `:required`:目标为必填输入字段
*   `:valid`:匹配有效的输入字段
*   `:invalid`:匹配无效的输入字段
*   `:playing`:针对正在播放的音频或视频元素

浏览器最近又收到了三个伪类选择器…

## CSS:是伪类选择器

*注意:这最初被指定为`:matches()`和`:any()`，但是`:is()`已经成为 CSS 标准。*

您经常需要对多个元素应用相同的样式。例如，`<p>`段落文本默认为黑色，但当它出现在`<article>`、`<section>`或`<aside>`中时为灰色:

```
/* default black */
p {
  color: #000;
}

/* gray in <article>, <section>, or <aside> */
article p,
section p,
aside p {
  color: #444;
} 
```

这是一个简单的例子，但是更复杂的页面会导致更复杂和冗长的选择器字符串。任何选择器中的语法错误都可能破坏所有元素的样式。

像 Sass 这样的 CSS 预处理程序允许嵌套(这也是本地 CSS 的特点):

```
article, section, aside {

  p {
    color: #444;
  }

} 
```

这将创建相同的 CSS 代码，减少键入工作量，并可以防止错误。但是:

*   在原生嵌套出现之前，您将需要一个 CSS 构建工具。您可能希望使用像 Sass 这样的选项，但是这会给一些开发团队带来复杂性。
*   嵌套会导致其他问题。构建深度嵌套的选择器很容易，但读取和输出冗长的 CSS 变得越来越困难。

`:is()`提供原生 CSS 解决方案，在所有现代浏览器(非 IE)中有[完全支持:](https://caniuse.com/css-matches-pseudo)

```
:is(article, section, aside) p {
  color: #444;
} 
```

单个选择器可以包含任意数量的`:is()`伪类。例如，下面的复杂选择器将绿色文本应用于所有的`<h1>`、`<h2>`和`<p>`元素，这些元素是具有`.primary`或`.secondary`类的`<section>`的子元素，并且不是`<article>`的第一个子元素:

```
article section:not(:first-child):is(.primary, .secondary) :is(h1, h2, p) {
  color: green;
} 
```

没有`:is()`的等价代码需要六个 CSS 选择器:

```
article section.primary:not(:first-child) h1,
article section.primary:not(:first-child) h2,
article section.primary:not(:first-child) p,
article section.secondary:not(:first-child) h1,
article section.secondary:not(:first-child) h2,
article section.secondary:not(:first-child) p {
  color: green;
} 
```

注意，`:is()`不能匹配`::before`和`::after`伪元素，所以这个示例代码将失败:

```
/* NOT VALID - selector will not work */
div:is(::before, ::after) {
  display: block;
  content: '';
  width: 1em;
  height: 1em;
  color: blue;
} 
```

## CSS :where 伪类选择器

`:where()`选择器语法与`:is()`相同，也是所有现代浏览器(非 IE)支持的[。这通常会导致相同的风格。例如:](https://caniuse.com/mdn-css_selectors_where)

```
:where(article, section, aside) p {
  color: #444;
} 
```

不同的是 *[特异性](https://developer.mozilla.org/docs/Web/CSS/Specificity)* 。**特异性**是用于确定哪个 CSS 选择器应该覆盖所有其他选择器的算法。在下面的示例中，`article p`比单独的`p`更具体，因此`<article>`中的所有段落元素都将是灰色的:

```
article p { color: #444; }
p { color: #000; } 
```

在`:is()`的例子中，特异性是在它的参数中找到的最具体的选择器。在`:where()`的情况下，特异性为零。

考虑以下 CSS:

```
article p {
  color: black;
}

:is(article, section, aside) p {
  color: red;
}

:where(article, section, aside) p {
  color: blue;
} 
```

让我们将这个 CSS 应用到下面的 HTML 中:

```
<article>
  <p>paragraph text</p>
</article> 
```

段落文本将被染成红色，如下面的 CodePen 演示所示。

使用 [CodePen](https://codepen.io) 上的:is 选择器通过 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
查看笔[。](https://codepen.io/SitePoint/pen/BarGKWQ)