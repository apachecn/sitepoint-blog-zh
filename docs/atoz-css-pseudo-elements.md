# AtoZ CSS 截屏:CSS 伪元素

> 原文：<https://www.sitepoint.com/atoz-css-pseudo-elements/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

伪元素是页面上 HTML 代码中找不到的元素。

它们可以用应用于任何其他元素的任何 CSS 来操作。

两个特殊的伪元素——`:before`和`:after`可以用来从 CSS 生成页面上的内容，并且有许多潜在的用例。

在这一集里，我们将了解:

*   五种不同的伪元素
*   从 CSS 生成文本、图像、属性值和计数器等内容
*   以及如何用最少的标记制作复杂的形状

### 伪元素

CSS 中有五个伪元素:

*   `:first-line`
*   `:first-letter`
*   `:selection`
*   `:before`
*   `:after`

这些类不同于带有双冒号的伪类，但为了简洁起见，它们通常用单冒号写在 CSS 中。

我这里有一个很长的占位符文本的引用。我可以用`:first-line`改变第一行文本的`color`，甚至在文本重排时也是如此。我可以通过将`:first-letter`设计成`float`和更大的`font-size`来创建首字下沉。而且我可以用`:selection`改变选中文本的`color`。

我可以在带有`:before`和`:after`伪元素的`blockquote`前后添加大引号。文本从`content`属性生成，然后可以用 CSS 样式化以获得想要的效果。

```
blockquote {
  position: relative;
  border-left: 5px solid #66d9ef;	
  padding: 1em 1em 1em 2em;
}
blockquote p: first-line {
  color: #cc3f85;
}
blockquote p: first-letter {
  float: left;
  font-size: 4em;
  margin-right: 0.5em;
}
::selection {
  background: #cc3f85;
  color: #fff;
}
blockquote:before {
  content: "“";
  position: absolute;
  top: 0;
  left: -0.6em;
  font- size: 8em;
  font-family: Georgia;
}
blockquote:after {
  content: "”";
  bottom: -0.25em;
  right: -0.5em;
  line-height: 1rem;

}
blockquote:before,
blockquote: after {
  position: absolute;
  color: #66d9ef;
  font-size: 8em;
  font-family: Georgia;
}
```

### 生成的内容

使用`:before`和`:after`伪元素允许我们向页面添加各种不同的内容。

我们已经看到了如何将文本内容添加到页面中，但是我们也可以添加图像、属性值、计数器或空字符串来提供对这两个额外元素的访问。

添加图像类似于用`url()`添加一个`background-image`。这里，使用`url()`作为`content`属性的值。我实际上更喜欢使用背景图片，并通过为`content`创建一个空字符串来提供对伪元素的访问。这样可以更好地控制图像，因为所有常见的属性如`background-position`、`background-repeat`和`background-size`都可用。

```
li:before {
  content: url(star.png);
  display: inline-block;
  vertical-align: middle;
  margin-right: 0.5em;
}
```

也可以使用`content`属性将 HTML 属性的值注入页面。在创建打印样式表时，我喜欢添加下面的代码片段来输出链接的 URL，以便可以从页面中读取它们:

```
a[href]: not([href*="#"]): after {
  content: attr(href);
}
```

这将在非内部链接或哈希链接的链接文本后添加链接。

生成内容的最后一个特例是插入计数器变量的值。我发现这在过去对复杂的法律条款和条件列表进行编号时非常有用。

我这里有一系列标题，下面有一系列嵌套列表。我希望每个部分标题有一个数字，每个列表项作为每个部分的子项进行编号。

对于每一个`h2`，我将增加一个“部分”计数器，对于每一个列表项，我将增加一个“项目”计数器。在每个节标题之前，我将输出节计数器的值，在每个列表项之前，我将输出项计数器的值。可以在计数器之间添加额外的字符串，以创建复杂的编号系统。该方法的一个简化形式可用于控制列表中数字或项目符号的样式。

```
h2 {counter-increment: section;}
ul {counter-reset: item;}
li {counter-increment: item;}

h2:before {
  content: counter(section) " - ";
}
li:before {
  content: counter(section) "." counter(item);
}
```

### 形状

由于页面上的每个元素都可以有两个“额外”元素，并且这些元素可以按照我们喜欢的方式进行设计，因此可以制作各种复杂的形状。

当我想用一个例子来演示的时候，我遇到了一个关于 CSS-Tricks 的形状的参考；其中一个非常突出，我将介绍一下它是如何工作的。让我们用单一元素来制作阴阳符号。

```
.yin-yang {
  position: relative;
  width: 200px;
  height: 200px;
  border-style: solid;
  border-color: black;
  border-width: 4px 4px 100px 4px;
  background: #fff;
  border-radius: 100%;
}
.yin-yang: before,
.yin-yang: after {
  content: "";
  position: absolute;
  top: 50%;
  border-radius: 100%;
  width: 24px;
  height: 24px;
}
.yin-yang: before {
  background: white;
  left: 0;
  border: 36px solid black;
}
.yin-yang:after {
  background: black;
  left: 50%;
  border: 36px solid white;
}
```

从盒子开始，这个可以用`border-radius`变成一个圆。通过使用等于圆的`height`的`border-bottom`可以创建两个彩色半圆。这两个点是通过用伪元素再做两个圆并用`position:absolute`放置它们来创建的。使用与半圆的`color`相匹配的边界，可以创建符号的两个圆端。如果你问我，我会觉得很甜蜜。

我非常喜欢使用伪元素；你可以用它们做很多事情，给页面添加各种视觉效果，而不会弄乱标记。

## 分享这篇文章