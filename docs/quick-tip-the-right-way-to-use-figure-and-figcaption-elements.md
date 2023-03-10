# 快速提示:使用图形和图片标题元素的正确方法

> 原文：<https://www.sitepoint.com/quick-tip-the-right-way-to-use-figure-and-figcaption-elements/>

`figure`和`figcaption`元素是两个经常一起使用的语义元素。如果你还没有[看过规范](https://www.w3.org/TR/html5/grouping-content.html#the-figure-element)，没有机会在你的项目中使用它们，或者不知道如何使用，这里有一些关于如何正确使用它们的提示。

`figure`元素通常用于图像:

```
<figure>
  <img src="dog.jpg" alt="Maltese Terrier">
</figure>
```

`figure`元素表示一个自包含的内容单元。这意味着，如果您要将元素在文档中向下移动，或者移动到文档的末尾，都不会影响文档的含义。

因此，我们也需要记住，并不是每个图像都是一个`figure`。

## `figure`中的多个图像

如果多个`img`标签在您的文档上下文中相关，您可以将它们放在一个`figure`中。

```
<figure>
  <img src="dog1.jpg" alt="Maltese Terrier">
  <img src="dog2.jpg" alt="Black Labrador">
  <img src="dog3.jpg" alt="Golden Retriever">
</figure>
```

## 其他元素可与`figure`一起使用

`figure`元素也不局限于图像。您可以将它用于以下用途:

*   代码块，
*   视频，
*   音频剪辑，或
*   广告。

下面是一个将`figure`用于代码块的例子:

```
<figure>
  <pre>
    <code>
      p {
          color: #333;
          font-family: Helvetica, sans-serif;
          font-size: 1rem;
      }
    </code>
  </pre>
</figure>
```

## 嵌套在另一个`figure`中的`figure`

如果有意义的话，可以将一个`figure`嵌套在另一个`figure`中。这里，添加了一个 ARIA `role`属性来改进语义。

```
<figure role="group">
  <figcaption>Dog breeds</figcaption>
  <figure>
    <img src="dog1.jpg" alt="Maltese Terrier">
    <figcaption>Adorable Maltese Terrier</figcaption>
  </figure>
  <figure>
    <img src="dog2.jpg" alt="Black Labrador">
    <figcaption>Cute black labrador</figcaption>
  </figure>
</figure>
```

关于在 ARIA 中使用`figure`和`figcaption`元素的进一步指导，请参见我之前的文章《T2》如何在 HTML5 中有效地使用 ARIA。

## `figcaption`的正确用法

元素代表一个标题或图例。

不是每个`figure`都需要一个`figcaption`。

然而，当使用`figcaption`时，理想情况下它应该是`figure`块中的第一个或最后一个元素:

```
<figure>
  <figcaption>Three different breeds of dog.</figcaption>
  <img src="dog1.jpg" alt="Maltese Terrier">
  <img src="dog2.jpg" alt="Black Labrador">
  <img src="dog3.jpg" alt="Golden Retriever">
</figure>
```

或者:

```
<figure>
  <img src="dog1.jpg" alt="Maltese Terrier">
  <img src="dog2.jpg" alt="Black Labrador">
  <img src="dog3.jpg" alt="Golden Retriever">
  <figcaption>Three different breeds of dog.</figcaption>
</figure>
```

## 您也可以在`figcaption`中使用流动元素

如果需要给图像添加更多语义，可以使用`h1`和`p`等元素。

```
<figure>
  <img src="dogs.jpg" alt="Group photo of dogs">
  <figcaption>
    <h2>Puppy School</h2>
    <p>Championship Class of 2016</p>
  </figcaption>
</figure>
```

对于使用`figure`和`figcaption`元素，你还有其他的技巧吗？

## 分享这篇文章