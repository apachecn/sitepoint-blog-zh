# 级联样式表(CSS)介绍

> 原文：<https://www.sitepoint.com/cascading-style-sheets-css/>

这篇简介并不是要教你关于级联样式表的一切，而是概述 CSS 能做些什么。通读这篇文章，然后决定 CSS 是否适合你的网站。至少，你会知道使用 CSS 能做什么，所以当需要对你的站点的布局和排版有更多的控制时，你会知道去哪里。

##### 什么是级联样式表？

CSS 是 HTML 标记的一种形式，它为 web 设计者提供了对页面上的排版和元素间距的更大控制。

##### 我为什么要费心使用 CSS 呢？

1.更好地控制布局和排版。CSS 为设计者提供了对网站上使用的字体的精确控制，包括大小、字母间距和文本装饰。

页面上的元素可以使用 CSS 精确定位。事实上，至少有一个主流的 HTML 编辑器 Dreamweaver 2 使用 CSS 来控制页面的布局，尽管它确实提供了转换为表格的选项。

2.站点范围的更改变得很容易。可以指定在格式化特定页面时使用的样式表的 URL，而不是将样式表作为页面的 HTML 代码的一部分。这使得通过简单地编辑单个文件来修改整个站点变得非常容易。

##### 浏览器兼容性呢？

Internet Explorer 4+和 Netscape Navigator 4+中提供了 CSS 支持。然而，一些恼人的浏览器不一致性继续让那些决定在他们的网站上使用 CSS 的人日子不好过。用一点点 JavaScript 就可以根据浏览站点的浏览器提供不同的样式表。或者，可以使用变通方法，使样式表在两种浏览器中都能正确工作。

##### CSS 是什么样子的？

CSS 代码的基本模板如下所示:

<q>标签:`{Property: value; Property2: value2}`</q>

标签–将受到影响的元素
属性–选择器的哪一部分将受到影响
值–将如何受到影响

注意 CSS 使用了花括号而不是< and >。

**一个小片段:**

<q>H1`{FONT-SIZE: 9pt; FONT-WEIGHT: bold}`</q>

上面的例子将 H1 标签中的所有文本设为粗体，大小为 9。

##### 使用 CSS 的三种方式

使用级联样式表有三种方式:内嵌、嵌入和外部/链接样式表。下面我逐一简单解释一下。

**内嵌**

如果您只想对一小段文本应用格式，可以使用内嵌样式，如下所示:

<q><H3 style = " font-weight:bold ">这将被加粗嵌入</q>

嵌入的样式表放在您想要使用样式表的每个页面上的 标签之间。这是它们的样子:

<q><头像>
<样式 TYPE="text/css" >
<！–</q>

标记:{ Property:value；属性 2:值 2}

–>
</风格>
</头像>

**外部**

还记得我说过，您可以在整个站点范围内使用单个样式表，然后通过编辑一个文件来更改所有页面吗？这是通过外部样式表完成的，看起来像这样:

<q><头>
<链接 REL= "样式表" HREF="/PATH/SHEET。CSS TYPE = " TEXT/CSS ">
</HEAD></q>

床单。然后，CSS 文件将包含所有样式表代码，这些代码将应用于使用上述代码调用它的任何页面。

##### 去掉下划线——一个完整的样式表

您想删除网站上链接的下划线吗？下面是一个短小精悍的样式表，只需将它复制并粘贴到网站的 标签之间:

<q>STYLE TYPE = " text/CSS ">
！–
a { text-decoration:none }
–>
</STYLE></q>

##### 从这里去哪里

如果你对上面的简要概述感兴趣，并且想了解更多关于级联样式表的知识，请查看下面的教程:

HTML Goodies–一个关于 CSS 中使用的类和 ID 的简短教程。

W3.org——比你挥舞棍子能得到的信息还要多。

HTML 帮助–几个教程涵盖了级联样式表的不同方面。

[编写样式表](http://www.canit.se/%7Egriffon/web/writing_stylesheets.html)——一个简短的指南。

[CSS 很简单！SitePoint 的凯文·杨克用大量的例子解释了事情的本质。](http://www.webmasterbase.com/article/309)

祝你好运！

## 分享这篇文章