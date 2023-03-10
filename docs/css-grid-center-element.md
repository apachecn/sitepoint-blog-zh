# 如何使用 CSS 网格将 Div 居中

> 原文：<https://www.sitepoint.com/css-grid-center-element/>

在这篇文章中，我们将看看使用 CSS Grid 水平和垂直居中 div 的四种方法。当然，这些定心技术可以用于任何类型的元素。我们之前已经介绍过如何使用 [Flexbox](https://www.sitepoint.com/atoz-css-quick-tip-vertical/#use-flexbox-for-vertical-centering) 和[定位转换](https://www.sitepoint.com/atoz-css-quick-tip-vertical/#position-transform-centering)来水平和垂直居中元素。

## 安装

让我们首先创建一个容器，里面有一个简单的 box 元素，我们将使用它来演示这些居中方法。这是 HTML:

```
<article>
  <div></div>
</article> 
```

这是我们的起始 CSS:

```
article {
  width: 100%;
  min-height: 100vh;
  background: black;
  display: grid;
}

div {
  width: 200px;
  background: yellow;
  height: 100px;
} 
```

![Our starting position, with a yellow square sitting top left in a black container](img/b5f5151cc2d09f591da7b9a3108dd46c.png)

在我们所有的例子中，我们将使用`display: grid`属性。这将`<article>`元素建立为一个网格容器，并为该容器生成一个块级网格。(这里有[我们在 CodePen](https://codepen.io/SitePoint/pen/bGMLdPj) 上的演示模板，如果你想尝试一下的话。)

现在，让我们看看将 div 居中的各种方法。

## 1.用 CSS 网格和 place-self 将 Div 居中

我最喜欢用 Grid 将元素居中的方法是使用`place-self`属性。(你可以在这里了解更多信息[。)](https://developer.mozilla.org/en-US/docs/Web/CSS/place-self)

将 div 居中就像这样简单:

```
article {
  display: grid;
}

div {
  place-self: center;
} 
```

见[码笔](https://codepen.io)上的 [笔使用网格居中和](https://codepen.io/SitePoint/pen/jOxZbPJ)通过 site point([@ site point](https://codepen.io/SitePoint))
放置自我。