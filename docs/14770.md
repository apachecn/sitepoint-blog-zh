# 轻松实现随机内容轮换

> 原文：<https://www.sitepoint.com/random-content-rotation-made/>

在阅读了丹·本杰明的[随机图像旋转](http://www.alistapart.com/articles/randomizer/)之后，我想分享一下我最近在我的网站上使用的旋转“成功故事”的方法。我将在本文中介绍的概念和脚本可以像 Dan 的一样使用，以防止页面对重复访问者变得乏味。

概念相近但不相同；我们在这里讨论的脚本可以用来创建你选择的任何(X)HTML 代码的随机旋转。它是基于服务器的，不依赖于 JavaScript。

##### 概观

您创建了一些`<div>`,导入了一个由 Perl 脚本生成的样式表。样式表将除了一个随机的`<div>`之外的所有设置为`display:none`。您的设计看起来很新鲜，搜索引擎会提取所有内容(因此，随机内容中的任何链接每次都会被看到)，屏幕阅读器用户也会全部获得*，从而保持您的内容可访问性。这个脚本很简单，但是很有用。

*正如乔·克拉克的[所指出的，一些屏幕读者尊重`display:none`，所以这不适用于那些不良少年。如果你愿意，你可以使用除了`display:none`之外的东西。](http://www.alistapart.com/articles/fir/#RESULTS-TABLE)

##### 安装脚本

该脚本是用 Perl 编写的，应该可以在几乎所有的 Web 主机上使用——你可以从这里下载。如果你不知道如何让这个脚本工作，请参考你的网站主机的支持台或帮助文件。您可能需要更改的惟一一行是脚本的第一行，它指向您的服务器上的 Perl 可执行文件。

##### 这个例子

例如，我们将使用 3 个“成功案例”；每个包含一页内容:

```
success1.html 

success2.html 

success3.html
```

当我们在可视浏览器上显示我们的主页页面时，我们一次只能指出其中一个成功的故事，但是没有理由为什么屏幕阅读器和搜索引擎不能每次都看到所有三个成功的故事。

让我们创建三段代码，用于将用户从主页引导至完整的成功案例内容页面:

```
<div id="success1" class="success-story"> 

  <p><a href="/success1.html"><img srcimg/success1.jpg" alt="Foo Ltd." border="0" />Foo Ltd. has been invaluable in bringing our product to market successfully.....</a></p> 

</div> 

<div id="success2" class="success-story"> 

  <p><a href="/success2.html"><img srcimg/success2.jpg" alt="Foo Ltd." border="0" />Foo Ltd. delivered a highly professional service.....</a></p> 

</div> 

<div id="success3" class="success-story"> 

  <p><a href="/success3.html"><img srcimg/success3.jpg" alt="Foo Ltd." border="0" />Foo Ltd. provided the project management skills we were looking for.....</a></p> 

</div>
```

对于我们希望包含在循环中的每个内容部分，代码都有一个`<div>`。然而,`<div>`的内容对脚本来说并不重要。重要的部分是每个`<div>`的 id，它有两个部分:一个前缀和一个数字。为了 CSS 显示的目的，`<div>`的类可以是你需要的任何东西。当然，您可以使用 id，但是如果您这样做，您将在您的选择器中显示每个 id。使用一个类使这变得更容易。

***前缀***

id 的第一部分是前缀。前缀包括数字前面的所有内容。在本例中，前缀为:

```
success
```

我们将把这个前缀传递给脚本。

***数字***

id 的第二部分是数字；id 必须始终从 1 开始，并以 1 为增量递增(1、2、3 等。).你想要多少 id 都可以。

我们将把最大的数字传递给脚本。

##### 剧本

脚本背后的概念很简单。该脚本生成一个样式表，它将使用`display:none`隐藏除了一个(随机的)`<div>`之外的所有内容。我们使用标准的`<link>`元素将它添加到我们的主页:

```
<link type="text/css" rel="stylesheet" href="/cgi-bin/random_content.pl?t=3&p=success" />
```

这里重要的部分是指向样式表的 href:

```
/cgi-bin/random_content.pl?t=3&p=success
```

让我们把它拆开，依次看每一节。

```
/cgi-bin/random_content.pl?
```

此部分标识脚本在服务器上的位置，后面跟一个问号。

```
t=3
```

```
t stands for total. You should replace 3#ec#/ with the total number of content `<div>`s you used above.

```
&
```

您需要用&符号来分隔传递的两个值。

```
p=success
```

```
p stands for prefix. You should replace success with the prefix you used for the ids of your divs.就是这样！不使用 CSS 的访问者，比如忽略 CSS 的搜索引擎爬虫和屏幕阅读器，每次都会得到所有的内容。只要有一点想象力，您可以用这个简单的脚本做许多有趣的事情。它对我来说非常好用；我希望对你也一样！

更改 CSS

正如我前面提到的，你不必使用`display:none`——如果你愿意，你可以改变它。一种替代方法是绝对定位内容，使其位于可视屏幕区域之外，比如说，向左 1000 个像素。为此，您需要更改以下代码行:

```
my $css = '{display:none;}';
```

将其更改为:

```
my $css = '{position:absolute; left:-1000px; top:0px;}';
```

这应该使非随机内容更容易被屏幕阅读器访问。选择权在你！
临别的评论
由于 Internet Explorer 的出色表现，如果您试图直接在浏览器中查看该脚本的输出，可能会遇到问题。出现问题是因为当您试图直接查看脚本输出的 MIME 类型时，浏览器并不监听它(尽管当它作为样式表导入时，它工作得很好)。
如果您想使用这个浏览器查看输出，您需要将`&debug=1`添加到您用来访问脚本的 URL 中。这会导致脚本使用 text/plain 的 MIME 类型输出，而不是 text/css。作为样式表导入时不要使用`debug=1`；只有在出于测试目的在浏览器中查看脚本输出时遇到问题时，才添加它。
另一个选择是重命名脚本”。css”，这应该也可以解决这个问题，而不需要你添加`debug=1`，因为这会欺骗浏览器相信它正在获取 css。

``` 
```

## 分享这篇文章