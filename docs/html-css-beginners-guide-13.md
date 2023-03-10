# 学习 HTML 和 CSS:绝对初学者指南

> 原文：<https://www.sitepoint.com/html-css-beginners-guide-13/>

## 外部样式表

### 为什么外部样式表比嵌入样式更好

一个*外部样式表*提供了一个位置，您可以在其中放置可以应用于所有网页的样式。这就是 CSS 的真正威力所在，也正是因为这个原因，我们没有花太多时间在潜水俱乐部项目网站上应用内嵌样式或嵌入式样式。
糟糕的旧时光

在过去，或者我们称之为糟糕的旧时代，人们会一页一页地创建网站，并在一页一页地使用各种我甚至不敢说的讨厌元素来设计它们！有时候，这些网站的发展超出了网站管理员最疯狂的想象。“太棒了，”站长先生或夫人想。“我的网站现在有 200 多页！很快我就会超过微软。”几个月后，网站管理员决定重新设计网站，并惊恐地意识到，为了以一致的方式重新设计网站，他或她必须修改每一个网页。每个页面需要 20 个或更多不同的调整，并且每个调整必须一致地应用到网站的每个页面。不可避免地，一些页面被遗漏了，最终重新设计的计划被毫不客气地放弃了。简而言之，丑陋的网站在由于完全的疏忽而悲惨地死去之前，仍然丑陋了很长时间(事实上，今天网络上散布着许多这样的遗留文档)。然而这并不是必须的。

CSS 给了你在一个地方设置样式规则的能力。当您想要更改网站时，只需在一个地方进行更改，整个网站就会自动更改以反映这些新样式。
快乐的日子！CSS 支持在这里

好消息是，目前使用的绝大多数 web 浏览器都提供了对 CSS 的出色支持(尽管情况并非总是如此，这也是过去有些人迟迟不采用基于 CSS 的设计的原因)。一些浏览器可能会被一些更高级的 CSS 技术卡住，但是，总的来说，您可以使用 CSS 来设计您的网页，并且确信您在屏幕上看到的视图与 99.5%的目标观众看到的视图是相同的。
创建外部 CSS 文件

如果要利用外部样式表提供的所有好处，首先需要创建一个可以在网站的所有页面之间共享的 CSS 文件。打开文本编辑器，输入以下内容:

/*
CSS 用于网站下的气泡
*/
p {
font-weight:bold；
颜色:蓝色；
}

将文件保存在与 HTML 文件相同的文件夹中，命名为`style1.css`；您可以像保存 HTML 文件一样保存 CSS 文件。

注意，我们在 CSS 文件中输入的前几行实际上不会做任何事情。像 HTML 一样，CSS 允许你添加评论。遗憾的是，HTML 注释的标签不同于 CSS 注释的标签，但它们执行完全相同的功能:它们允许您在不影响屏幕显示的情况下记录您的工作。在 CSS 中，注释以一个`/*`开始，以一个`*/`结束；浏览器会忽略两者之间的任何内容。在上面，我们使用注释只是为了说明文件的用途，即它是 site 下气泡的 CSS。我们还添加了一条规则，将所有段落中的字体变为粗体和蓝色。

## 将 CSS 链接到网页

在您的 CSS 生效之前，您需要将它链接到您想要应用样式的网页。为此，您需要将一个`link`元素添加到每一个将使用 CSS 样式化的网页的`head`中。我们的网站目前只包含三个页面，所以这将是很好的和容易的。`link`元素只是将一个文件链接到该元素所在的页面；在这种情况下，链接文件是一个样式表。

下面，新行出现在主页的上下文中:

< title >泡下——西南潜水俱乐部
英国</title>
<meta http-equiv = " Content-Type "
Content = " text/html；charset = utf-8 "/>
**<link href = " style 1 . CSS " rel = " style sheet " type = " text/CSS "/>**
</head>

让我们来看看标记是什么意思。

`href`属性告诉 web 浏览器在哪里可以找到样式表文件(`style1.css`)，就像在锚中使用`href`属性指向目标文件一样(例如`<a href="home.htm">Home</a>`)。

链接标签的`rel="stylesheet"`和`type="text/css"`部分告诉浏览器链接的是什么类型的文件，以及浏览器应该如何处理内容。当链接到一个`.css`文件时，你应该总是包括这些重要的属性。
空元素警报！

`link`元素是我们在本文前面讨论过的另一种特殊的空元素:它没有单独的开始和结束标签。`link`本身是一个完整的元素，使用 XHTML 要求的空格和正斜杠结束。

现在我们知道了如何将页面链接到 CSS 文件，让我们在我们的项目站点上尝试一下:

*   在您的文本编辑器中打开您的每个网页— `index.html`、`about.html`和`contact.html`。在每个文件的结束标记`</head>`之前添加下面一行:
    `<link href="style1.css" rel="stylesheet" type="text/css"/>`
*   请务必保存每一页。然后，尝试在 web 浏览器中打开每一个。

所有的段落现在都应该以蓝色粗体显示。如果是这样，那么恭喜您——您现在已经将一个样式表链接到了三个独立的页面。如果您将在`.css`文件中指定的颜色从蓝色更改为红色，您应该会在下次打开页面时看到这一更改。来吧，试一试。

现在，使用蓝色的粗体文本可能是确保样式表正确链接的好方法，但这不一定是我们想要使用的设计效果。移除样式表中的`p`规则，但保留注释，让我们开始真正构建我们的样式表。

**Go to page:** [1](https://sitepoint.com/html-css-beginners-guide) | [2](https://sitepoint.com/html-css-beginners-guide-2/) | [3](https://sitepoint.com/html-css-beginners-guide-3/) | [4](https://sitepoint.com/html-css-beginners-guide-4/) | [5](https://sitepoint.com/html-css-beginners-guide-5/) | [6](https://sitepoint.com/html-css-beginners-guide-6/) | [7](https://sitepoint.com/html-css-beginners-guide-7/) | [8](https://sitepoint.com/html-css-beginners-guide-8/) | [9](https://sitepoint.com/html-css-beginners-guide-9/) | [10](https://sitepoint.com/html-css-beginners-guide-10/) | [11](https://sitepoint.com/html-css-beginners-guide-11/) | [12](https://sitepoint.com/html-css-beginners-guide-12/) | [13](https://sitepoint.com/html-css-beginners-guide-13/) | [14](https://sitepoint.com/html-css-beginners-guide-14/) | [15](https://sitepoint.com/html-css-beginners-guide-15/) | [16](https://sitepoint.com/html-css-beginners-guide-16/) | [17](https://sitepoint.com/html-css-beginners-guide-17/) | [18](https://sitepoint.com/html-css-beginners-guide-18/) | [19](https://sitepoint.com/html-css-beginners-guide-19/)

## 分享这篇文章