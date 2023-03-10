# href (HTML 属性)

> 原文：<https://www.sitepoint.com/href-html-attribute/>

## 描述

`href`定义了链接指向的文档。这可能是同一目录中的网页、同一服务器上其他地方的网页、当前网页中的某个位置，或者是存储在另一台服务器上的网页或任何其他类型的文档。

该示例显示了同一目录中两个文档之间的链接，但是如果蛋糕列表文档位于比引用文档高一级的目录中，则语法如下:

```
<a href="*../*cakes.html">lovely range of cakes</a>
```

这里，../等同于指令，“在层次结构中上移一个目录。”

您还可以引用相对于网站根目录(域名后的文件或文件夹)的文档:

```
<a href="*/*cakes.html">lovely range of cakes</a> 
```

该链接基本上指示浏览器“链接到可以在 www.mydomain.com/."找到的文档 cakes.html”。这是一种非常方便的引用文档的方式，因为您可以将包含该链接的文档移动到文件系统上的任何位置，而不会断开该链接。

如果你链接到另一个服务器上的文档(无论什么类型),你可以用一个完整的 URI 来表示 href，就像这样:

```
<a href="*http://www.cakebrothers.com/cakes.html*">lovely range of cakes</a>
```

在到同一页面内另一部分的链接中，目的地在`href`属性中通过与目的地的`id`属性结合的散列符号来标识。这种符号被称为片段标识符，如下所示:

```
<!-- Here is the link -->
<p>You can check the facts by reading the
  *<a href="#refs">*references at the end of this article*</a>*</p>
⋮
<!-- Here is the link's destination -->
<h3>*<a id="refs" href="#refs">*References*</a>*</h3> 
```

请注意上例中的以下几点:

`href`属性在第二个 a 元素中重复出现。这不是一个错误——它在那里是因为没有它的*,导航到目的地的 Internet Explorer 用户会发现，尽管文档已经移动到屏幕上的正确位置，但焦点不会转移。如果用户要跳转到下一个链接，焦点将立即移动到用户在页面上选择的链接之后的链接，而不是页面中包含引用的点之后的下一个链接。
可以简单地将一个 id 属性应用于另一个元素——例如`<h3
id="refs">References</h3>`——一些浏览器会允许你激活一个链接并跳转到那个点。然而，并不是所有的浏览器都允许这样做，最明显的例子是 Internet Explorer。这就是为什么我们需要看似多余的第二个 a 元素作为锚，它被包裹在 h3 中的文本周围。这种简化的(但不是 IE 友好的)标记的语法如下所示:*

```
<p>You can check the facts by reading the
  *<a href="#refs">*references at the end of this article*</a>*</p>
⋮
<h3 *id="refs"*>References</h3> 
```

在您自己的工作中，您可能会发现在 anchor a 元素中同时包含名称和 id 的例子。这种方法旨在确保链接在较旧的浏览器中工作，如果没有`name`属性，这些浏览器可能不允许用户从文档的一部分跳到另一部分。然而，最后一个不能以这种方式链接到页面的一部分的普通浏览器是 Netscape 4，谢天谢地，它现在几乎完全过时了。另外，请注意在 HTML 5 中,`name`属性已经被移除，所以打破使用`name`的习惯是个好主意。

除了到文档(如网页或其他文档类型)的链接之外，href 属性还可以指定不同的协议，包括:

*   **ftp** 输入为`<a href="ftp://someftpserver.com/">Browse the FTP server</a>`，这将打开一个到 ftp 服务器的连接
*   键入为`<a href="mailto:mrwhatever@somedomain.com">Email me!</a>`的 **mailto** ，这将触发电子邮件客户端打开并创建一个新消息，其收件人地址与 mailto:协议之后出现的任何内容相匹配(在本例中，是一封给 mrwhatever@somedomain.com 的电子邮件)

## 价值

该属性的值是目标文档相对于引用文档的位置、相对于服务器根目录的位置，或者是包含`http://`协议、服务器名称和该服务器上文档路径的完整 URI 的形式。它还可能包含对`ftp://`或`mailto:`协议的引用。

## 分享这篇文章