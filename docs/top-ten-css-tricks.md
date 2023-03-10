# 我的十大 CSS 技巧文章

> 原文：<https://www.sitepoint.com/top-ten-css-tricks/>

CSS 可能很复杂，随着每个新浏览器版本的发布，你可能会发现自己很难跟上最新的技巧和窍门。但是这些技巧和窍门会拯救你的理智！在这里，我总结了我认为最有帮助的 10 条建议，让你在时间紧迫的情况下，省去在网上搜寻解决方案的麻烦。

##### 1.块级元素与内联级元素

几乎所有的 HTML 元素都是块元素或行内元素。块元素的特征包括:

*   总是从新一行开始
*   高度、行高和上下页边距都可以操作
*   除非指定宽度，否则宽度默认为其包含元素的 100%

块元素的例子包括`<div>`、`<p>`、`<h1>`、`<form>`、`<ul>`和`<li>`。另一方面，内联元素的特征与块元素相反:

*   从同一行开始
*   高度、行高和上下页边距不能更改
*   宽度与文本/图像一样长，无法操作

内嵌元素的例子包括`<span>`、`<a>`、`<label>`、`<input>`、`<img>`、`<strong>`和`<em>`。

要更改元素的状态，您可以使用`display: inline`或`display: block`。但是，将一个元素从 block 改为 inline 或者从 inline 改为 block 有什么意义呢？嗯，乍一看，你可能很少使用这个技巧，但实际上，这是一个非常强大的技术，你可以随时使用它:

*   让内联元素从新行开始
*   让块元素在同一行开始
*   控制内嵌元素的宽度(对导航链接特别有用)
*   操纵行内元素的高度
*   为块元素设置与文本一样宽的背景色，而不必指定宽度

##### 2.另一种箱式模型黑客替代方案

[box model hack](http://tantek.com/CSS/Examples/boxmodelhack.html)用于修复 PC 上 IE 6 之前版本的浏览器中的渲染问题，其中边框和填充包含在元素的宽度中，而不是添加到元素的宽度上。已经提出了许多基于 CSS 的解决方案来补救这一点；这是另一个我非常喜欢的例子:

```
padding: 2em; 

border: 1em solid green; 

width: 20em; 

width/**/:/**/ 14em;
```

第一个宽度命令被所有浏览器读取；第二种是 PC 上除 IE5.x 以外的所有浏览器。因为第二个命令排在第二位，所以它优先于第一个命令:任何排在第二位的命令总是优先于前面的命令。那么，这一切是如何运作的呢？

通过在冒号前放置空的注释标签(`/**/`)，我们指示 IE5.0 忽略该命令。同样，如果我们在冒号后放置空的注释标签，IE5.5 将忽略该命令。结合使用这两条规则，我们可以在所有的 IE5.x 浏览器中隐藏这个命令。

##### 3.页面的最小宽度

一个非常方便的 CSS 命令是 min-width 命令，通过它可以指定任何元素的最小宽度。这对于指定页面的最小宽度特别有用。

不幸的是，IE 不理解这个命令，所以我们需要想出一个新的方法让这个功能在这个浏览器中工作。首先，我们将在`<body>`标签下插入一个`<div>`，因为我们不能给`<body>`分配最小宽度:

```
<body> 

<div class="container">
```

接下来，我们创建 CSS 命令，创建 600 像素的最小宽度:

```
#container 

{ 

min-width: 600px; 

width:expression(document.body.clientWidth < 600? "600px": "auto" ); 

}
```

第一个命令是常规的最小宽度命令；第二个是只有 IE 才懂的简短 JavaScript 命令。但是请注意，这个命令将导致您的 CSS 文档无效；您可能更愿意将它插入到每个 HTML 文档的头部来避免这个问题。

您可能还想将最小宽度与最大宽度结合起来:

```
#container 

{ 

min-width: 600px; 

max-width: 1200px; 

width:expression(document.body.clientWidth < 600? "600px" : document.body.clientWidth > 1200? "1200px" : "auto"); 

}
```

##### 4.IE 和宽度和高度问题

他做事的方式很奇怪。它不理解最小宽度和最小高度命令，而是将宽度和高度解释为最小宽度和最小高度——想想看！

这可能会导致问题，因为如果我们需要在框中放入更多的文本，或者用户需要调整文本的大小，我们可能需要调整框的大小。如果我们在一个框上只使用宽度和高度命令，非 IE 浏览器将不允许框调整大小。但是，如果我们只使用最小宽度和最小高度命令，我们就不能在 IE 中控制宽度和高度！

当使用背景图像时，这尤其成问题。如果您使用 80px 宽、35px 高的背景图像，您需要确保使用此图像的框的默认大小正好是 80 x 35px。但是，如果用户调整文本的大小，框的大小将需要适度扩大。

要解决这个问题，您可以对带有`class="box"`的盒子使用以下代码:

```
.box 

{ 

width: 80px; 

height: 35px; 

} 

html>body .box 

{ 

width: auto; 

height: auto; 

min-width: 80px; 

min-height: 35px; 

}
```

所有浏览器都会通读第一个 CSS 规则，但是 IE 会忽略第二个规则，因为它使用了[子选择器命令](http://meyerweb.com/eric/articles/webrev/200006b.html)。非 IE 浏览器将通读第二个规则，这将覆盖第一个规则的值，因为这个 CSS 规则更具体，更具体的 CSS 规则总是覆盖那些不太具体的规则。

##### 5.文本转换命令

一个不太为人所知，但是非常有用的 CSS 命令是`text-transform`命令。这个规则的三个比较常见的值是:`text-transform: uppercase`、`text-transform: lowercase`和`text-transform: capitalize`。第一个规则将所有字符都变成大写字母，第二个规则将所有字符都变成小写字母，第三个规则将每个单词的第一个字母变成大写字母。

这个命令非常有用，有助于确保整个网站的风格一致，尤其是在有许多内容编辑器的情况下。比方说，你的风格指南规定标题中的单词必须总是以大写字母开头。要确保始终如此，请使用 text-transform:capital。即使网站编辑忘记了大写，他们的错误也不会出现在网站上。

使用文本转换也更好:大写单词，因为屏幕阅读器可能会将大写的短单词读成首字母缩写词。一个很好的例子是“联系我们”，它被一些屏幕阅读器读作“联系我们”。

##### 6.IE 中消失的文字或图像？

IE 表现出一个非常奇怪的缺陷，文本或背景图像有时会从视线中消失。这些项目实际上仍然存在，如果您高亮显示屏幕上的所有内容或点击刷新，它们会经常重新出现。有点奇怪，是吧？

这个问题主要发生在背景图像和位于浮动元素旁边的文本上。要解决这个问题，只需在 CSS 命令中为消失的元素插入 position: relative，出于某种奇怪的原因，这通常会解决问题。如果这不起作用(有时确实不起作用)，就给 CSS 中的违规元素分配一个宽度——这应该可以解决问题。

##### 7.不可见文本

有时候，你可能真的想让文本不可见。对于屏幕阅读器用户来说，不可见文本可能特别有用，例如为表单项分配标签，或者在某一节的前面插入标题。不想通过插入这些元素来改变视觉外观？把它们隐藏起来，没有人会知道它们的存在。

如果使用打印或手持 CSS 文件，您可能还希望使文本不可见，因为有些信息可能不需要在这些介质上显示(参见下文了解更多信息)。

要使文本不可见，可以使用`display: none` —简单！这对于隐藏手持设备(如果支持 CSS)和打印网页的文本很有效，但对于许多屏幕阅读器来说就不那么好了。屏幕阅读器现在变得太聪明了，有些人实际上会忽略任何分配了规则`display: none`的文本。

因此，对于屏幕阅读器用户来说，需要一种新的方法:`position: absolute; left: -9000px`。这基本上是将文本放置在屏幕左边缘左侧 9000 像素的位置，使其不可见。

##### 8.手持设备的 CSS 文档

可以为 PDA 和移动电话创建单独的 CSS 文档，并且只有当这些设备中的一个被用来访问您的站点时才被激活。越来越多的网站正在为打印创建[单独的 CSS 文档，因此当用户选择打印时，网页会自动变得易于打印。您可以对手持设备进行同样的操作。](http://www.alistapart.com/articles/goingtoprint/)

以下命令用于调用手持设备的 CSS 文档:

```
<link type="text/css" rel="stylesheet" href="handheldstyle.css" media="handheld" />
```

手持 CSS 文件中的 CSS 命令会覆盖主 CSS 文档中的任何等效命令。那么，应该在这个文件中放置什么命令呢？理想情况下，你希望手持设备的用户避免水平滚动。

为了测试这一点，在一个常规的浏览器窗口中打开你的网站，并将其宽度调整为 150 像素。然后，打开你的主 CSS 文件，在文件的底部插入一些新的命令。您在这里放置的命令应该可以调整网站的布局，这样就不需要以 150 像素的宽度水平滚动了。然后，打开一个新文档，剪切并粘贴这些新命令，并将其保存为 handheldstyle.css(或者您想给它取的任何名称)。

你的网站向手持设备用户提供的服务应该与向传统网络浏览器提供的服务完全不同，因为用户在手持设备上的体验是完全不同的。如需了解更多信息，S.W. Weiss 的《手持可用性》是一本很好的读物。

##### 9.三维按钮效果

在网络发展的早期，当鼠标经过时，3d 按钮会被按下，这种做法非常流行。在那个时候，这只能通过图像和 JavaScript 来实现，但现在，随着 CSS 的出现，我们可以回到过去，重新创建这种 3d 效果。

您需要的主要 CSS 命令是:

```
a { 

display: block; 

border: 1px solid; 

border-color: #aaa #000 #000 #aaa; 

width: 8em; 

background: #fc0; 

} 

a:hover 

{ 

position: relative; 

top: 1px; 

left: 1px; 

border-color: #000 #aaa #aaa #000; 

}
```

除了这些命令之外，您还可以插入其他命令来实现所需的演示效果—唯一的限制是您的想象力！

##### 10.每页都有相同的导航代码

大多数网站突出显示与每个用户在网站中的位置相关的导航项目，以帮助用户确定自己的位置。这是基本可用性的基本要求，但也可能是痛苦的:我们需要调整每个页面导航背后的 HTML 代码。我们能两全其美吗？有没有可能在每一页上都突出显示导航，而不必调整每一页的 HTML 代码？当然是啦！

首先，您需要为每个导航项目分配一个类:

```
<ul> 

<li><a href="#" class="home">Home</a></li> 

<li><a href="#" class="about">About us</a></li> 

<li><a href="#" class="contact">Contact us</a></li> 

</ul>
```

然后，您需要将一个`id`插入到`<body>`标签中。id 应该代表用户在站点中的位置，并且应该在用户移动到不同的站点部分时发生变化。在“主页”上，它应该显示为`<body id="home">`，在“关于我们”中，它应该显示为`<body id="about">`，在“联系我们”中，它应该显示为`<body id="contact">`。

接下来，创建一个新的 CSS 规则:

```
#home .home, #about .about, #about .about, #contact .contact 

{ 

commands for highlighted navigation go here 

}
```

这基本上创建了一个规则，只有当`class="home"`包含在`id="home"`中，并且`class="about"`在`id="about"`中，`class="contact"`在`id="contact"`中时，该规则才生效。只有当用户在网站的适当部分，无缝地创建我们突出显示的导航项目时，这些情况才会发生。

## 分享这篇文章