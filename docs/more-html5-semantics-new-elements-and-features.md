# 更多 HTML5 语义:新元素和特性

> 原文：<https://www.sitepoint.com/more-html5-semantics-new-elements-and-features/>

我们已经向您介绍并阐述了一些更实用的新元素和特性。在这一节中，让我们接触一下 HTML5 规范中添加的鲜为人知的元素、属性和特性。

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

### `details`元素

这个新元素有助于标记文档中隐藏的部分，但是可以扩展以显示附加信息。该元素的目的是为 Web 上常见的功能提供本机支持——一个可折叠的盒子，它有一个标题，隐藏了更多的信息或功能。

通常这种小部件是使用 HTML 和 JavaScript 的组合创建的。将它包含在 HTML5 中消除了脚本需求，简化了 web 作者的实现，从而有助于减少页面加载时间。

下面是它被标记后的样子:

```
<details>
  <summary>Some Magazines of Note</summary>
  <ul>
    <li><cite>Bird Watcher's Digest</cite></li>
    <li><cite>Rower's Weekly</cite></li>
    <li><cite>Fishing Monthly</cite></li>
  </ul>
</details>
```

在本例中，`summary`元素的内容将向用户显示，但其余内容将被隐藏。点击`summary`，隐藏内容出现。

如果`details`缺少已定义的`summary`，浏览器将定义一个默认摘要(例如，“详细信息”)。如果您希望隐藏的内容在默认情况下可见，您可以使用`details`元素上的[布尔](https://www.sitepoint.com/boolean-data-type/) `open`属性。

`summary`元素只能作为`details`的子元素使用，如果使用的话必须是第一个子元素。

在撰写本文时，`details`缺乏完整的浏览器支持，但它正在改进。为了填补这些空白，有几个基于 JavaScript 的 polyfills 可供选择，包括马蒂亚斯·拜恩斯的 jQuery 版本和马克西姆·切梅里苏克的普通 JavaScript 版本。

###  <customized ordered="" lists="" h3="">使用`ol`元素标记的有序列表在网页中很常见。HTML5 引入了一个名为`reversed`的新布尔属性，这样当它出现时，它会反转列表项上的数字，允许您以降序显示列表。此外，HTML5 还带回了在 HTML4 中被弃用的`start`属性。`start`属性允许您指定列表应该从哪个编号开始。

支持对`reversed`和`start`都有好处。在撰写本文时，Internet Explorer 是唯一不支持逆序列表的浏览器。如果你想要一本多用途的书，你可以使用[这本书作者之一的剧本。](https://github.com/impressivewebs/HTML5-Reverse-Ordered-Lists)

### 范围样式

在 HTML5 中，`style`元素用于直接在页面中嵌入样式(而不是引用链接的样式表)，允许使用名为`scoped`的布尔属性。以下面的代码为例:

```
<h1>Page Title</h1>
<article>
  <style scoped> h1 {
      color: blue;
    } </style>
  <h1>Article Title</h1>
  <p>Article content.</p>
</article>
```

因为存在`scoped`属性，所以在`style`元素中声明的样式将只应用于父元素及其子元素(如果级联规则允许的话)，而不是整个文档。这使得文档中的特定部分(比如本例中的`article`元素)及其相关样式可以很容易地移植。

这当然是一个方便的新功能，但要在所有浏览器中实现它可能还需要一段时间。目前唯一支持作用域样式的浏览器是 Firefox。Chrome 以前支持它，但由于“代码高度复杂”而被移除。在撰写本文时，IE 团队[还没有立即增加这一功能的计划。](https://status.modern.ie/scopedstyles)

### 脚本的`async`属性

`script`元素现在允许使用`async`属性，这类似于现有的`defer`属性。使用`defer`指定浏览器应该等到页面的标记被解析后再加载脚本。新的`async`属性允许您指定脚本应该异步加载。这意味着它应该在可用时尽快加载，而不会导致页面上的其他元素在加载时延迟。`defer`和`async`都是布尔属性。

这些属性只能在`script`元素定义外部文件时使用。对于遗留浏览器，如果必要的话，你可以同时包含`async`和`defer`，以确保其中一个被使用。实际上，这两个属性的作用是在下载脚本时不会暂停浏览器对页面的呈现；然而，`async`可能更有优势，因为它会在其他渲染任务发生时在后台加载脚本，并在脚本可用时立即执行。

如果您正在加载的脚本没有其他依赖项，并且如果尽早加载脚本(而不是在页面加载之后)有利于用户体验，那么`async`属性特别有用。然而，还应该注意的是，如果你有一个加载多个脚本的页面，`defer`属性确保它们按照出现的顺序加载，而`async`不能保证顺序。

### `picture`元素

HTML5 规范中最近增加的一个元素是`picture`元素，它旨在帮助响应式 web 设计，特别是响应式图像。`picture`允许您定义多个图像源。这使得移动浏览器用户可以下载低分辨率版本的图像，同时为平板电脑和台式机提供更大的版本。

`picture`元素有其伴随的`source`元素(也用于`video`和`audio`元素，如第 5 章所述)，此外还有一些新的属性，如`srcset`和`sizes`。这两个属性可以用在`picture`、`img`和`source`上。

关于这些新特性在响应式图像实现中的使用方式的详细讨论，请参见 Eric Portis 在 *A List Apart* 上的这篇精彩文章。

### 其他名人

这里有一些你想使用的 HTML5 新特性，每一个都有不同程度的浏览器支持:

*   `dialog`元素，表示“用户与之交互以执行任务的应用程序的一部分；例如，对话框、检查器或窗口

*   `a`元素的`download`属性，用于指示目标资源应该被下载而不是被导航(例如，对 pdf 有用)。

*   `iframe`元素的`sandbox`和`seamless`属性。`sandbox`允许您运行带有限制的外部页面，并且`seamless`属性将`iframe`内容与父文档更紧密地集成在一起，更无缝地采用其样式。

*   `menu`元素及其`menuitem`子元素，允许您创建一个交互式命令列表。例如，您可以标记一个带有复制、剪切和粘贴选项的编辑菜单，根据需要添加脚本功能。

*   `address`元素，让您标记适用于最近的`article`或`body`元素的联系信息。

由于篇幅有限，这里没有讨论其他新的内容。请务必不时查看一下规格，看看是否有新的内容被添加或更改。</customized> 

## 分享这篇文章