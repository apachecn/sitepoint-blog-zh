# 理解 CSS 的“内容”属性

> 原文：<https://www.sitepoint.com/understanding-css-content-property/>

如果你是前端开发人员，你很可能听说过伪元素以及 CSS 的内容属性。我不会在这里深入讨论伪元素，但是如果你对这个概念不熟悉或者需要快速复习，我建议你看看这篇 [Louis Lazaris 关于 Smashing Magazine](http://www.smashingmagazine.com/2011/07/13/learning-to-use-the-before-and-after-pseudo-elements-in-css/) 的文章。

在本文中，我们将重点关注`content`属性。CSS 的`content`属性处理`::before`和`::after`伪元素(可以使用单冒号或双冒号的 synax)。属性用于在网页中插入生成的内容，所有主流浏览器都完全支持它。

## `content`属性的基本语法

`content`属性的语法分解如下，每个值表示为:

```
p::before {
  content: normal|counter|attr|string|open-quote|url|initial|inherit;
}
```

CSS 在不同的值之间略有不同。例如，要将`attr()`值与`::before`或`::after`一起使用，您需要编写如下 CSS:

```
p::after {
  content: " (" attr(title) ")";
}
```

这只是一个例子，后面还会有更多。在接下来的部分中，我将讨论每个值，包括`attr()`。

## 值:`none`或`normal`

当设置为`none`时，不生成伪元素。如果您将它设置为`normal`，它将为`::before`和`::after`伪元素计算出`none`。

```
p::before {
  content: normal;
}

p::after {
  content: none;
}
```

这种事情可能用在已经定义了伪元素的嵌套元素中，但是您希望在某些上下文中覆盖伪元素。

## 值:`<string>`

该值将内容设置为字符串，可以是任何文本内容。如果使用非拉丁字符，则需要对字符进行编码。让我们来看看每个例子。考虑下面的 HTML:

```
<h2>Tutorial Categories</h2>
<ol>
  <li>HTML and CSS</li>
  <li class="new">Sass &amp; Less</li>
  <li>JavaScript</li>
</ol>

<p class="copyright">SitePoint, 2015<p>
```

然后是下面的 CSS:

```
.new::after {
  content: " New!";
  color: green;
}

.copyright::before {
  content: "\00a9  ";
}
```

这里，我们将文本内容插入到一个列表项中，还将一个编码字符(在本例中是版权符号)插入到段落元素中。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看带有文本的 Pen [内容属性。](http://codepen.io/SitePoint/pen/pJeLGP/)