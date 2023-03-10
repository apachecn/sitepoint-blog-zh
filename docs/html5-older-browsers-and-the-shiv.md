# HTML5、旧浏览器和 Shiv

> 原文：<https://www.sitepoint.com/html5-older-browsers-and-the-shiv/>

HTML5 引入了一些旧浏览器不支持的语义元素。其中一些新元素与一般的块元素没有什么不同，所以它们不会造成任何兼容性问题。要确保兼容性，您只需在网站中添加一个 CSS 规则，使相关元素的行为类似于块元素。

但是 Internet Explorer 及以下版本带来了挑战。任何不在官方元素列表中的元素都不能用 CSS 样式化。这意味着我们不能让 then 表现得像块元素或者给它们任何格式。

例如，下面的代码将不起作用。

```
<style>
section {display: block}
</style>
<section>This is on its own line.</section>
<section>This should appear on a separate line.</section>
```

但这还不是全部。这些新元素表现得好像不存在一样。例如，下面的 CSS 不起作用，因为`section`元素不匹配通用选择器。

```
<style>
body * span {color: red}
</style>
<body>
  <section>
    <span>This should be red, but won't be red in IE 8.</span>
  </section>
</body>
```

对我们来说幸运的是，存在一种变通方法，允许 Internet Explorer (IE)识别这些新元素，允许对它们进行样式化，从而让我们充分利用这些新的语义标签。是一款名为 [HTML5Shiv](http://code.google.com/p/html5shiv/) 的工具。

如链接的谷歌页面所述，“shiv”和“shim”在本文中是可互换的术语。

但是我们是如何从 IE 甚至不承认这个元素的存在，到现在能够使用它的呢？

诀窍在于调用`document.createElement("section")`会突然导致 IE 识别出`section`元素。没有人知道为什么，但是它确实有效，你甚至不需要使用这个函数返回的节点。

但是你需要确保在使用这些元素之前，在你的网站中尽早调用它，否则它将不起作用。

您需要为每个新的 HTML5 元素调用它，如下所示:

```
"abbr article aside audio bdi canvas data datalist details figcaption figure "+
  "footer header hgroup main mark meter nav output progress section " +
  "summary template time video"
  .replace(/w+/g, function(a){ document.createElement(a) });
```

注意，我们使用`string`对象的`replace`方法简洁地迭代正则表达式匹配的每个连续长度的字符，并为每个字符块执行回调函数，该函数又调用`createElement`。

在这里，我们将调用这个方法，“移动文档”，这样文档就可以呈现新的 HTML5 元素。

现在，我们前面的两个 HTML 例子工作了。但这还不是全部。

## 陷阱 1: HTML5 和 innerHTML

如果 HTML 是使用`innerHTML`生成的，并且它是在一个当前没有附加到文档的节点上被调用的(也就是一个孤立的节点)，那么这又是一种似曾相识的感觉。下面的两个例子不会呈现`section`元素，即使它运行在一个已经被剪切的文档上。

```
var n1 = document.getElementById("n1");
n1.parentNode.removeChild(n1);
n1.innerHTML = "<section>Sect 1</section>";  //won't work
```

```
var n2 = document.createElement("div");
n2.innerHTML = "<section>Sect 2</section>";  //won't work
```

在上面的第二个例子中，如果我们在调用`innerHTML`之前先将节点追加到文档中，那么它将会工作:

```
var n2 = document.createElement("div");
document.body.appendChild(n2);
n2.innerHTML = "<section>Sect 2</section>";  //works
```

我们可以得出这样的结论，尽管我们在前面对文档进行了 shiv，但是当调用`innerHTML`时，孤立元素并没有从 shiv 中受益。

我们能做什么？首先，每当我们需要设置`innerHTML`时，我们应该首先将它附加到文档中。另一种方法是在处理孤儿之前先切断孤儿的`document`属性。

首先让我们把我们的刀放在它自己的功能里。

```
function iehtml5shiv(doc) {
  "abbr article aside audio bdi canvas data datalist details " +
    "figcaption figure footer header hgroup main mark meter nav " +
    "output progress section summary template time video"
    .replace(/w+/g, function(a){ doc.createElement(a) });
}
```

下一次我们有一个孤儿元素时，我们可以这样做:

```
var n1 = document.createElement("div");
iehtml5shiv(n1.document);
n1.innerHTML = "<section>Sect 1</section>";  //works
```

注意这就像移动文档一样，但是是在元素的`document`属性上。请注意，我们访问的是`document`而不是`ownerDocument`。两者是不同的东西，如下所示:

```
alert(n1.document == document);  //false
alert(n1.ownerDocument == document);  //true
```

现在我们有两个方法来确保我们对`innerHTML`的调用在处理 HTML5 元素时有效。

## 陷阱 2:克隆节点

看来我们的表亲`cloneNode`也容易失去它的刀。任何被克隆的 HTML5 元素，或者其父元素被克隆的 html 5 元素，都将失去它们的身份。

注意 below 元素的`nodeName`中有冒号，这意味着它与另一个名称空间中的元素相混淆。

```
var n2 = n1.cloneNode(true);
alert(n2.innerHTML);  //outputs: <:section>Sect 1</:section>
```

即使节点已经附加到文档，也会发生这种情况。

除了推出您自己的`cloneNode`实现之外，我们在这里没有什么可做的，这已经足够琐碎了。

## 陷阱 3:打印

每当你打印一个网页，IE 似乎会在打印前生成一个新的文档，这意味着所有的 shiv 变通办法都不会被保留。

你无法减轻这一点。HTML5Shiv 工具通过监听`onbeforeprint`事件并用普通元素替换页面上的所有 HTML5 元素，然后对`onafterprint`事件执行相反的操作来解决这个问题。

谢天谢地，HTML5Shiv 工具很好地为我们做到了这一点。

## 参考

*   HTML5Shiv 工具:[https://github.com/aFarkas/html5shiv](https://github.com/aFarkas/html5shiv)
*   HTML5 Shiv 的故事:[http://paulirish.com/2011/the-history-of-the-html5-shiv/](http://paulirish.com/2011/the-history-of-the-html5-shiv/)

## 分享这篇文章