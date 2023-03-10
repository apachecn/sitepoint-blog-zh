# HTML 乌托邦:使用 CSS 设计无表格，第 5 章:构建框架

> 原文：<https://www.sitepoint.com/css-5-building-skeleton/>

**构建骨架**

大多数关于 CSS 的书都是从教你如何处理网页的一些小细节开始的:字体、颜色、列表等等。然后，他们继续解释与 CSS 定位(CSS-P)相关的更广泛的问题，这些问题影响页面的布局，而不是单个元素的外观。

在本书中，我采取了相反的方法。我首先看一下 CSS 设计涉及的站点级和页面级问题，这样我们就可以理解没有表格的页面布局的大图透视(这毕竟是本书的主要主旨)。稍后，在第三部分，我将讨论我们将在本章和下一章布局的页面内容的样式。

这一章特别关注使用 CSS 创建网页或网站的基本结构布局。在这篇文章中，我将讨论多栏布局——包括一般的和非常具体的——因为它们与 Footbag Freaks 网站有关。我会教你关于盒子，边框，以及 CSS 设计中著名的盒子模型。我将深入研究创建和使用两列和三列页面布局的复杂性，以及浮动对象的奥秘。我将指导您为您在 Footbag Freaks 项目以及您可能创建的其他项目中会遇到的页面创建虚拟布局。

我将用处理本章中出现的布局问题的常用技巧来结束本文——将页面从现有的以表格为中心的设计转换为 CSS 所涉及的问题。

##### 枚举设计类型

当你创建一个网站时，首先要做的一个决定是，你需要多少不同类型的页面和元素，尤其是当你想最有效地使用 CSS 的时候。

***有多少种页面类型？***

大多数网站使用不止一种基本的页面布局。首页或索引页通常具有与“内页”不同的外观和感觉。例如，在 Footbag Freaks 站点中，规范告诉我们面包屑导航将出现在除首页之外的所有页面上。检查发现，显示在首页顶部附近的大图片不会出现在网站的其他页面上。

在一个典型的复杂的电子商务网站上，你可能会遇到更多的页面类型。例如，这样的站点可能包括不同的布局:

*   首页(索引)
*   目录页面
*   安全订购页面
*   主要内容页面
*   站点地图页面

其中一些页面可能会显示存储在数据库中的动态内容，这些内容是为响应特定的用户请求而生成的。其他的可能是平面的 HTML 页面，除非你重新设计，否则永远不会改变。

Footbag Freaks 网站似乎只需要一种*类型*的页面布局。二级页面的元素比主页少，但是公共元素的相对位置和布局不会因页面类型的不同而改变。

***有多少设计元素？***

![1171_5.1](img/af9c38ef5de3078c7a2f549059929eaf.png)
图 5.1。足球怪胎主页

查看 Footbag Freaks 主页，我们可以很容易地确定以下七个设计元素，如图 5.1 所示:

1.  the top of the page where the Footbag Freaks logo appears against a colored background
2.  the left-hand column where the site’s navigation is located
3.  inside the navigation area, the text field for newsletter sign-up and related text
4.  the large image area where the sun, the sky, and the hacky sack appear
5.  the news area
6.  the sponsor area
7.  出现版权信息的页脚

网站的第二页删除了列表中的第四个设计元素，并在主导航中添加了一个子菜单导航元素。因此，每种页面类型都有七个设计元素，其中六个是通用的，一个是每个页面独有的。

##### CSS 定位和多列页面布局

既然你已经知道了要定义 CSS 规则的设计的数量，让我们后退一步，了解如何使用特定的 CSS 规则来创建这些布局和效果。

***CSS 盒子模型***

从样式表的角度来看，您在 HTML 页面中处理的所有内容都可以被视为存在于一个盒子中。当你格式化大块内容时，这个事实通常更加明显，就像 Footbag Freaks 网站中的七个设计元素。但是，即使是在处理这些元素的单个组件时也是如此，比如标题、列表、列表元素，甚至是文本片段。

基本的 CSS 盒子模型如图 5.2 所示。

![1171_5.2](img/da4e6612075936ca6f70ddf9a390faaf.png)
图 5.2。基本 CSS 盒子模型

CSS 盒子模型的中心是内容本身。不要认为这种“内容”与组成新闻故事内容或一组链接的文字或图片是一样的。内容是包含在盒子区域内的任何东西。

从图 5.2 中可以看出，框的可视宽度是由内容宽度、填充和边框的总和决定的。边距决定了可见框和相邻元素之间的距离。类似地，通过将内容的高度添加到填充和边框设置中来确定框的可见高度。同样，边距决定了框与相邻对象的垂直距离。

这些元素的宽度——边距、边框和填充——可以使用四个 CSS 属性(盒子的每一边一个)或使用一个速记属性来设置。边框行为稍微复杂一些，因为边框不仅可以有宽度，还可以有可见的特征，如线条样式和颜色。

我将从详细解释和演示填充的使用开始。然后，我将继续讨论边距，这将更简短，因为它与填充非常相似。最后，我将讨论边界属性及其变体。

在接下来的几节中，我将使用一个基本的单框布局来演示 CSS 规则技术。它从图 5.3 开始，没有定义填充、边框或边距属性，所以内容和盒子一样大。

![1171_5.3](img/56c1526ad3137de77c637227d675bd1c.png)
图 5.3。箱式模型演示的起点

我给 h1 元素一个灰色背景，这样你可以更容易地看到我将要演示的效果的影响。我将在第 7 章更全面地描述我在这里使用的背景色属性。

这个 HTML 生成了如图 5.3 所示的页面:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  

<html >  

<head>  

<title>Box Model Demo</title>  

<meta http-equiv="Content-Type"  

content="text/html; charset=iso-8859-1" />  

**<style type="text/css">  

<!--  

h1 {  

  background-color: #c0c0c0;  

}  

-->  

</style>**  

</head>  

<body>  

<h1>Help! I'm Stuck in a Box Model!</h1>  

</body>  

</html>
```

在接下来的讨论中，我将只修改样式表信息，所以我将只复制代码的这一部分，用粗体表示任何变化
。

***像素对百分比***

因为盒子模型处理内容在屏幕上的显示，像素度量(缩写为 px)是 CSS 中最常用的绝对度量单位。然而，我们经常希望创建一个“可拉伸”的布局，在这种情况下，使用百分比模型(带有%符号)而不是像素是必要且合适的。在第六章，我会对这个问题有更多的阐述。

***设置填充属性***

有四个属性共同定义了 CSS 规则中对象周围的填充:左填充、右填充、上填充和下填充。

让我们只改变一个填充设置来感受一下它是如何工作的。修改示例文件中的样式表，使其看起来像下面的片段(记住新材料是粗体的):

```
h1 {  

  background-color: #c0c0c0;  

  **padding-left: 25px;**  

}
```

这一变化的结果如图 5.4 所示。请注意，文本现在从距框左侧 25 像素处开始，导致文本左侧出现 25 像素的空白灰色空间。

![1171_5.4](img/0ce41a7adf7bb5d895a938e4ddd20d37.png)
图 5.4。左填充演示

如您所料，您可以用同样的方式设置其他填充大小，如以下代码片段所示:

```
h1 {  

  background-color: #c0c0c0;  

  padding-left: 25px;  

  **padding-top: 15px;  

  padding-bottom: 30px;  

  padding-right: 20px;**  

}
```

你可以在图 5.5 中看到这些变化的效果。

![1171_5.5](img/e2e1a72859505a9711325b67a5dee548.png)
图 5.5。定义了所有四个填充属性

您可能会注意到，衬垫的右侧似乎不起作用。您要求 20 个像素，但是无论您将窗口扩展到多宽，定义包含 h1 元素的框的灰色区域都会继续下去。

这是因为右填充在文本的右边缘和标题的右边缘之间创建了一个空格，如灰色框所示。在这种情况下很难看到这个间距，因为标题会自动跨越浏览器窗口的宽度，为文本在右侧留出足够的空间。但是，如果你让浏览器足够窄，你可以看到填充
生效。

图 5.6 展示了这一原理。第一个屏幕截图显示了图 5.5 中的页面，如果您缩小浏览器窗口，以便在没有设置 padding-right 的情况下，在第一行有空间放置单词“in”。第二个屏幕截图强调了这一点，它显示了调整后的页面，每行只能显示一个单词。请注意，在某些情况下，正确的填充大小足以容纳下一行中的单词。事实上，仅仅从样式表中删除 padding-right 属性就会产生如第三个屏幕截图所示的结果。

![1171_5.6](img/a22b21b4954be8a94eb49f7f242bfaa6.png)
图 5.6。右填充效果演示

因为经常需要调整 HTML 中对象周围的填充，CSS 标准定义了一个简称为 padding 的简化属性。您最多可以为该属性赋予四个值。表 5.1 告诉你在每种情况下如何分配属性。

![1171_table5.1](img/d8e65786e7bb7858df9602503f59fa02.png)
表 5.1。多个值对填充速记属性
*1 的影响你可以把这个记为顺时针，从顶部开始，或者记为 TRBL(麻烦)，只要你觉得更容易记住。*

例如，上面的最后一段代码可以使用填充速记属性重写，如下所示:

```
<style type="text/css">   

<!--   

h1 {   

  background-color: #c0c0c0;   

  **padding: 15px 20px 30px 25px;**   

}   

-->   

</style>
```

要创建相等的顶部和底部填充，以及相等的左侧和右侧填充，即使右侧填充在这种情况下几乎没有意义，您可以使用:

```
<style type="text/css">   

<!--   

h1 {   

  background-color: #c0c0c0;   

  **padding: 15px 25px;**   

}   

-->   

</style>
```

这个代码片段的结果如图 5.7 所示。

![1171_5.7](img/29575f1600915ce3301003f33e0618dd.png)
图 5.7。使用填充速记使上下和左右填充相等

最后，要在 h1 元素的所有四个边上创建相等的填充，您可以编写以下代码:

```
h1 {   

  background-color: #c0c0c0;   

  **padding: 25px;**   

}
```

这段代码产生的结果如图 5.8 所示。

![1171_5.8](img/353b851f8d96fbeb75fa3f2c7bd3c5e0.png)
图 5.8。使用填充速记在所有边上进行相等的填充

如果使用 ems 或百分比作为填充值会怎么样？两者的效果略有不同。em 单位根据内容的字体大小缩放填充，而百分比单位根据包含元素的块的宽度缩放填充。为了演示这些效果，我们将使用一个新的 HTML 页面，该页面在一个对比色页面上的彩色背景下显示两个标题。

这是演示页面的 HTML。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"   

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">   

<html >   

<head>   

<title>Box Model Demo</title>   

<meta http-equiv="Content-Type"   

content="text/html; charset=iso-8859-1" />   

**<style type="text/css">   

<!--   

body {   

  background-color: #808080;   

}   

h1, h4 {   

  background-color: #c0c0c0;   

}   

-->   

</style>**   

</head>   

<body>   

<h1>Help! I'm Stuck in a Box Model!</h1>   

<h4>But it's not too crowded if you're just a little old h4   

heading like me! In fact, it's kind of cozy in here.</h4>   

</body>   

</html>
```

请注意，我给了页面一个深灰色的背景，并添加了一个 h4 元素，它的样式与 h1 元素的样式相同。

该 HTML 页面如图 5.9 所示。

![1171_5.9](img/63465131ac3fd7d26b28da2a361e29b0.png)
图 5.9。按比例填充页面起点

现在，让我们更改这个页面中的样式表，以便它使用填充简写在对象周围创建一个 1em 的填充空间。下面的代码片段可以完成这个任务:

```
body {   

  background-color: #808080;   

}   

h1, h4 {   

  background-color: #c0c0c0;   

  **padding: 1em;**   

}
```

如图 5.10 所示，两个标题元素周围的填充量与元素本身的字体大小成正比。回想一下，1em 等于所用字体的高度。因此，h1 元素周围的空间比 h4 元素周围的空间大得多。

![1171_5.10](img/c1785eba178939de87bfc36397dad64b.png)
图 5.10。使用 ems 进行比例填充

现在，让我们看看如果我们使用百分比而不是 em 作为比例填充值会发生什么。更改 HTML，使样式表看起来像这样:

```
body {   

  background-color: #808080;   

}   

h1, h4 {   

  background-color: #c0c0c0;   

  **padding: 10%;**   

}
```

这种变化的结果可以在图 5.11 中看到。哇！这些元素周围有大量的空间。浏览器将页面宽度的 10%(正文是包含标题元素的块)作为四边的填充。

![1171_5.11](img/0c6703589353fe3ea43c2d32cda5d63a.png)
图 5.11。将百分比用于比例间距

我已经在元素的文本后面使用了背景色，以便于您看到这些填充设置的效果，但是没有必要在这些元素后面使用背景色来定位它们。图 5.12 使用了与图 5.11 相同的 HTML 代码，唯一的区别是我去掉了主体以及 h1 和 h4 元素的背景颜色。如你所见，它们保持着相对间距。

![1171_5.12](img/7c58b0fb7e3d190318e8d2d85437a32d.png)
图 5.12。没有彩色背景的填充演示

***设置页边距属性***

我们设置边距属性的方式与设置填充属性的方式相同。属性名用单词“margin”代替单词“padding”，包括速记属性。

边距和填充的区别在于，边距存在于对象的边界之外，而填充存在于这些边界之内。图 5.13 根据下面的代码片段中的样式表规则说明了这种差异。

```
body {    

  background-color: #808080;    

}    

h1 {    

  background-color: #c0c0c0;    

}    

h2 {    

  background-color: #c0c0c0;    

  margin-left: 5%;    

}    

p {    

  background-color: #c0c0c0;    

  margin-left: 20%;    

}
```

![1171_5.13](img/9c6cb21501cb9a6acf97a0324b36a762.png)
图 5.13。左边距设置将内容和背景推过来

请注意，第二级标题和段落，我们已经为它们设置了左边距属性，从浏览器的左边缘缩进。但是在这里，与我们设置左填充属性的例子不同，文本及其背景色块是缩进的。这是因为颜色块和文本在内容框内，而边距在内容框外。

接下来，让我们对代码片段应用左填充和左空白设置。

```
body {    

  background-color: #808080;    

}    

h1 {    

  background-color: #c0c0c0;    

}    

h2 {    

  background-color: #c0c0c0;    

  margin-left: 5%;    

  **padding-left: 1em;    

}**    

p {    

  background: #c0c0c0;    

  margin-left: 20%;    

  **padding-left: 10%;**    

}
```

如图 5.14 所示，页边空白将 HTML 元素及其周围的背景色块推到了右边，而填充将彩色背景色块中的文本移到了右边。

![1171_5.14](img/57205ec6e838e12201177b745f8b8b52.png)
图 5.14。左边距与左填充设置相结合

水平边距的影响是累积的。看看下面的 HTML 代码，以及图 5.15，它显示了它是如何呈现的。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"    

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">    

<html >    

<head>    

<title>Box Model Demo</title>    

<meta http-equiv="Content-Type"    

content="text/html; charset=iso-8859-1" />    

**<style type="text/css">    

<!--    

body {    

  background-color: #808080;    

}    

h1 {    

  background-color: #c0c0c0;    

}    

h2 {    

  background-color: #c0c0c0;    

  margin-left: 5%;    

  padding-left: 1em;    

}    

p {    

  background-color: #c0c0c0;    

  margin-left: 20%;    

  padding-left: 10%;    

}    

li {    

  background-color: #ffffff;    

}    

li p {    

  margin-left: 10%;    

}    

-->    

</style>**    

</head>    

<body>    

<h1>No left margin set for this level-one heading</h1>    

<h2>Left margin set at 5% for me</h2>    

<p>A paragraph with a margin-left set at 20%. This will result in    

a deep indent of the paragraph from the left margin.</p>    

<ul>    

<li>Item one</li>    

<li><p>Paragraph item</p></li>    

</ul>    

</body>    

</html>
```

![1171_5.15](img/126c41862ad9f4e61cccbfb1c6b870b3.png)
图 5.15。水平边距设置的累积效果

这里最大的区别在于项目符号列表。请注意，列表中的第一项显示时没有额外的缩进。这并不奇怪，因为样式规则没有为 li 元素定义任何额外的边距设置。但是，请看第二个列表元素，它是一个段落。上面 HTML 中的最后一个样式规则将 li 元素的后代段落的左边距设置为 10%。如您所见，此边距应用于项目符号列表的现有左边距，这导致段落项被进一步向右推。还要注意，这个元素是一个段落，所以它保留了所有 p 元素的样式，包括它们的 10%的左填充设置。这将在列表中的灰色框内产生额外的段落文本缩进。

如果您加载上面的 HTML(来自本书代码档案中包含的文件 boxmodel4.html)并调整它的大小，您会注意到列表中段落元素的缩进随着窗口宽度的变化而变化。这是因为我使用了相对值 20%的空白和 10%的填充。因此，这两个值都是相对于包含块(列表项)的宽度来计算的，而包含块又从浏览器窗口中获取其宽度。浏览器窗口越大，嵌套段落的边距和填充就越大。

您可以使用 margin-top 和 margin-bottom 属性设置垂直边距。这是另一个演示垂直边距的 HTML 页面:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">     

<html >     

<head>     

<title>Box Model Demo</title>     

<meta http-equiv="Content-Type"     

content="text/html; charset=iso-8859-1" />     

**<style type="text/css">     

<!--     

body {     

  background-color: #808080;     

}     

h1 {     

  background-color: #c0c0c0;     

  margin-bottom: 5%;     

}     

h2 {     

  background-color: #c0c0c0;     

  margin-left: 5%;     

  margin-top: 5%;     

  margin-bottom: 5%;     

  padding-left: 1em;     

}     

p {     

  background: #c0c0c0;     

  margin-left: 20%;     

  padding-left: 10%;     

  margin-top: 5%;     

  margin-bottom: 5%;     

}     

-->     

</style>**     

</head>     

<body>     

<h1>No top margin but 5% bottom margin</h1>     

<h2>Top and bottom margins set 5% for me</h2>     

<p>A paragraph with top and bottom margins set at 5%</p>     

</body>     

</html>
```

该页面呈现如图 5.16 所示。如果您加载此文档(boxmodel5.html)并调整浏览器的大小，您会注意到垂直间距会相应地增加和减少，但保持成比例。

![1171_5.16](img/6b68971e31c13a1488dd0b9c547b5cf5.png)
图 5.16。垂直边距的演示

与水平边距不同，垂直边距不是累积的。如果有两个元素堆叠在一起，如图 5.16 中的 h1 和 h2 元素，它们之间的垂直间距将是顶部元素的 marginbottom 设置和底部元素的 margin-top 设置中的较大值。在这种情况下，它们都是 5%，所以两个元素之间的距离是 5%，而不是您可能已经猜到的 10%。如果我将 h1 的底部边距定义为 10%，那么分隔两个元素的垂直距离将是包含块高度的 10%。在这种情况下，包含块是主体，实际上，它与浏览器窗口的客户区相同。

边距属性设置可以使用负值。当您已经为 HTML 页面的主体设置了 margin-left 属性，但是您想要将元素移动到更靠近页面左边距的位置时，这就很方便了。下面的 HTML 结果显示在图 5.17 中。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">     

<html >     

<head>     

<title>Box Model Demo</title>     

<meta http-equiv="Content-Type"     

content="text/html; charset=iso-8859-1" />     

**<style type="text/css">     

<!--     

body {     

  background-color: #808080;     

  margin-left: 5%;     

}     

h1 {     

  background-color: #c0c0c0;     

  margin-left: -3%;     

  margin-bottom: 5%;     

}     

h2 {     

  background-color: #c0c0c0;     

  margin-top: 5%;     

  margin-bottom: 5%;     

}     

-->     

</style>**     

</head>     

<body>     

<h1>Body margin is 5%, but I'm set to -3%</h1>     

<h2>I have no margin-left setting, so I use the body 5%     

setting</h2>     

</body>     

</html>
```

![1171_5.17](img/5528c7efb8f6d121181e7d0628820c8f.png)
图 5.17。负边距设置的有用性

与 padding 属性一样，margin 速记属性允许您用一个属性声明设置所有四个边距，并使用表 5.1 中所示的规则解释多个值。

***设置边框属性***

边框属性比填充和边距属性更复杂，因为它们不仅影响对象之间的间距，还影响中间空间的外观。边界可以是，而且通常是可见的。在大多数情况下，管理边框属性类似于管理边距和填充的过程。但是有一些关键的区别。

边框有三种类型的属性:样式、宽度和颜色。默认情况下，它们的样式设置为“无”，宽度设置为“中等”，颜色设置为应用它们的 HTML 元素的文本颜色。

border-style 属性可以采用一系列常数值中的任何一个。可用的值和支持它们的浏览器如表 5.2 所示。

![1171_table5.2](img/14b1f861beca48b85cb6f45efa9a0543.png)
表 5.2。CSS 边框样式常量

隐藏值与无具有相同的效果，除非应用于表格布局。有关详细信息，请参考附录 C 中的 border-style 属性。

W3C 规范将这些边界的精确外观问题很大程度上留给了浏览器，所以如果使用这些特征的结果因浏览器和平台的不同而有所差异，也不要感到惊讶。但是，就像其他边框设置的默认行为一样，浏览器在很大程度上可以预测并令人满意地处理这个问题。

对象周围边框的宽度可以用四个单独的属性值对来设置，也可以用边框宽度速记语法来设置。这四个属性值对是边框上宽、边框右宽、边框下宽和边框左宽。这些值中的每一个都可以用像素或 em 值设置来设置，或者用三种描述性设置之一来设置:薄、中或厚。

如果使用瘦、中、厚的描述性设置，结果取决于浏览器。然而，它们在不同的浏览器和操作系统中是相当可预测和一致的，对于三个描述性设置中的每一个都在一个像素左右。

请注意，如果您希望使用特定的边框宽度度量，您应该使用像素。这是屏幕布局最有意义的度量单位，在这里边框宽度是一个重要的属性。

使用 bordercolor 速记属性可以控制与所有四个边框相关联的颜色。或者，您可以使用“上边框颜色”、“右边框颜色”、“下边框颜色”和“左边框颜色”属性为所有四个边框创建不同的颜色。

正如我将在第 7 章中更详细地解释的，你可以用任何标准的方式提供一个颜色参数:使用完整的 RGB 代码如#ff9900，使用三位数的
RGB 快捷键如#f90，使用 RGB 方法如 rgb(102，153，0)，或者使用标准的颜色名称如 red。

根据表 5.1 中的规则，速记属性 border-style、border-width 和 border-color 都接受多个值。但是，请注意，Netscape Navigator 4.x 不识别这些属性的多个参数，也不支持特定于边的样式和颜色属性。

还有一个额外的简写属性，这可能是定义边框属性最广泛使用的方法。使用 border 属性，您可以以简洁的形式指定对象所有四个边框的样式、宽度和颜色。因为您通常希望对象周围有统一的边框，所以这是设置边框属性值的有效方法。

此属性声明将在合法应用的任何元素周围生成统一的 3 像素绿色实心边框:

```
border: 3px solid green;
```

##### 显示属性

在我们继续研究 CSS 定位问题之前，我们还需要了解一个 CSS 属性。它很少出现，但一旦出现，就会对页面布局产生重大影响。

display 属性决定了浏览器如何显示元素，是将其视为块、内联文本片段还是其他。虽然它可以被赋予 17 个合法值中的任何一个，但是浏览器支持的现实将列表限制为 6 个，其中只有 4 个是真正重要的。有关显示器的完整参考，请参见附录 c。

display 属性的六个可能值是:

*   街区
*   在一条直线上的
*   列表项目
*   没有人
*   表格-页脚-组
*   表格标题组

默认值因元素而异。p、h1 和 div 等块元素默认为 Block，而 strong、code 和 span 等内联元素默认为 inline。显然，列表项默认为 list-item。将非默认设置分配给元素(比如将 div 设置为 display: inline)可以产生一些有趣的效果(想象一个包含两个 div 的段落和一个显示在一行文本中间的列表)。

如果提供的值为 none，则不显示它所应用的元素，并且它通常占据的空间会被折叠。这将 display: none 属性-值对与 visibility: hidden 设置区分开来，后者通常用于隐藏元素，但保留元素在可见时所占据的空间。

##### CSS 定位和多栏布局

到目前为止，我们在本章中研究的盒子模型适用于内容组。通常，您使用一个`<div>`标签将相关内容的集合组合在一起，并将 CSS 样式分配给这样一个组。

但是 CSS 定位(CSS-P)涉及的不仅仅是处理相关信息的单个组。HTML 页面上各组内容之间的联系对于决定页面的布局同样重要。这些连接中涉及的主要 CSS 属性是 position 属性。

position 属性采用单个常数值。该值确定块在页面上的定位方式。position 属性最常用的两个值是绝对值和相对值。另一个值 static 是默认值，很少在 CSS 规则中使用。第四个值是 fixed，它在 Windows 上根本不受 IE 支持，不幸的是，这意味着它在实际应用中几乎不可用。关于这些更深奥的设置的完整细节，请参考附录 C。

***绝对、相对、定位语境***

CSS 中的定位可能会令人困惑，因为放置块的坐标系取决于块的定位上下文。即使将绝对值赋给 position 属性，也没有一组通用的坐标来指导这种放置。每次在页面上定位一个块时(使用 static 以外的位置设置)，它都会为其后代创建一个新的定位上下文，其中其内容区域的左上角具有坐标(0，0)。因此，如果您使用 CSS 来定位块内的元素，它的位置将相对于新的坐标系进行计算——这是
定位上下文。

理解这个概念的最好方法是看几个简单的、相互关联的例子。我将从一张白纸开始。在这种情况下，客户区的左上角(在现代 Web 设计中也称为“文档”)是初始(0，0)坐标所在的位置。在这种情况下，如图 5.18 所示，在 div 中放置一段简单的文本(使用与 big-Title 类相关联的样式表规则，使其可读性更好)。

![1171_5.18](img/7f6fe1eb27567d8eb16bb879003cce6b.png)
图 5.18。元素在空白页上的初始定位

下面是产生图 5.18 所示结果的 HTML 片段。CSS 属性 top 和 left 用于在页面上定位 div。

```
<div class="bigTitle"       

style="position:absolute; left:125px; top:75px;">       

This is the first line of text being positioned.       

</div>
```

现在将第二个 div 完全放在第一个 div 中，如下所示:

```
<div class="bigTitle"       

  style="position:absolute; left:125px; top:75px;">       

  This is the first line of text being positioned.       

    **<div class="bigTitle"       

    style="position:absolute; left:25px; top:30px;">       

    This is a second line.       

  </div>**       

</div>
```

![1171_5.19](img/dcba784ecf35d70ffe31a17ae71cf123.png)
图 5.19。将元素定位在预先定位的块中

结果如图 5.19 所示。请注意，第二行文本从第一行文本的左侧缩进 25 个像素，因为第一行为第二行设置了定位上下文。还要注意，它的字体很大。为什么？看一看 bigTitle 类的样式规则，您就会明白:

```
.bigTitle {       

  font-family: Arial, Verdana, sans-serif;       

  font-size: 2em;       

  font-weight: bold;       

}
```

因为第二个 div 是第一个 div 的子 div，所以它的字体大小是相对于第一个 div 计算的。样式规则将字体定义为大小为 2 ems，这指示浏览器以两倍的大小呈现文本。当 2 em 规则应用于第一行时，它的大小会加倍。但是当它应用于第二行时，第一行的字体大小被加倍以计算第二行的字体大小。

该页面现在有两个 div 元素，一个嵌套在另一个中。两者都采用绝对定位。现在我将添加第三个元素，这次是一个 span 元素，它将包含在第二个 div 块中。使用相对定位，HTML 看起来像这样:

```
<div class="firstDiv"       

    style="position:absolute; left:125px; top:75px;">       

  This is the first line of text being positioned.       

  <div class="firstDiv"       

    style="position:absolute; left:25px; top:30px;">       

    This is**<span style="position:relative; left:10px;       

top:30px;">an example of</span>** a second line.       

  </div>       

</div>
```

这部分 HTML 的结果如图 5.20 所示。请注意，span 中包含的单词“的示例”出现在下方，并且稍微位于其原始位置的右侧。相对定位总是基于被定位元素在页面上的原始位置。换句话说，使用相对定位的元素的定位上下文是由其默认位置提供的。在这个例子中，跨度位于线中位置的右下方，如果这个位置被移除，如图 5.21 所示。

![1171_5.20](img/ac06572e157150add81502368b8686d0.png)
图 5.20。在页面上相对定位元素

![1171_5.21](img/f60e191fb645564c5dd2b676cad37fde.png)
图 5.21。相对定位内容的原始位置

总之，当我们使用 CSS position 属性时，决定元素定位上下文的基本规则是:

1.  Absolutely positioned elements are positioned relative to the positioning context in which they are located.
2.  相对定位的元素基于它们的静态(原始)位置创建它们自己的定位上下文。

***基本三栏布局***

这本书的示例站点 Footbag Freaks 使用了三列布局的组合，顶部有页眉，底部有页脚。这是一个经典的网页设计。当它包括一个流体中心柱时，一些人甚至称它为“圣杯”。我第一次看到这个参考是在埃里克·科斯特洛的网站上，[http://www.glish.com/](http://www.glish.com/)。

为了理解创建这个基本页面布局所涉及的 CSS，让我们先来看一下构建一个带有一个浮动中心列的三列布局的核心代码。然后，我们将添加顶级标题区域。最后，我们将剖析 Footbag Freaks 主页的核心，看看我们是如何在这些标准的基础上构建网站的，但对其进行了一些调整，以产生更具创造性的设计。

一个基本的三列布局涉及一个 CSS 样式表，它为左侧列、中间列和右侧列的布局和定位提供了单独的规则。我们将这三个部分称为左、中、右。稍后，我们将混合顶部和底部区域。下面是定义其标识符为左的块的 CSS 规则:

```
#left {        

  position: absolute;        

  left: 10px;        

  top: 10px;        

  width: 200px;        

}
```

这很简单。使用绝对定位，该列的左上角位于浏览器文档区域顶部下方 10 个像素处，该区域左边距右侧 10 个像素处。它为该列设置了一个固定的宽度，但是正如我们将看到的，您可以提供一个相对值(比如一个百分比)来创建一个可拉伸的布局，该布局将保持左列的宽度与文档区域的宽度成比例。

三栏布局的中间栏使用以下 CSS 规则:

```
#center {        

  margin-left: 220px;        

  margin-right: 220px;        

}
```

请注意，此列未定位。因此，根据它在生成页面的 HTML 文件中的位置，它的位置将保持“自然”的位置。220px 的边距设置可确保左右栏(宽度设置为 200 像素，距离文档边缘 10 像素)有足够的空间容纳内容，而不会与任何相邻的栏重叠。

最后，一个基本的右列看起来很像左列:

```
#right {        

  position: absolute;        

  right: 10px;        

  top: 10px;        

  width: 200px;        

}
```

这里，right: 10px 属性用于确保该列的右侧距离页面右侧 10 个像素。这些样式规则对一个演示 HTML 页面的影响可以在图 5.22 中看到。

![1171_5.22](img/d85ff446b7fbfb136f3865cb2c12a467.png)
图 5.22。演示基本的三列布局

这是图 5.21 中页面的 HTML。头中的`<link>`标签指向 threecoldemo.css 文件，该文件包含上面的三个 css 规则。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"        

"https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">        

<html >        

<head>        

<title>Three-Column Layout Demonstration</title>        

<meta http-equiv="Content-Type"        

content="text/html; charset=iso-8859-1" />        

**<link rel="stylesheet" href="threecoldemo.css" type="text/css"** />        

</head>        

<body>        

  <div id="left">        

    <p>        

      This is quite straight-forward. Using absolute positioning,        

      this column has its upper left corner placed 10 pixels down        

      from the top of the document area of the browser and 10        

      pixels to the right of the left margin of that space. It        

      sets a fixed width for the column, though as we will see,        

      you could supply a relative value (such as a percentage) to        

      create a stretchy layout that would keep the left column's        

      width proportional to the document area's width.        

    </p>        

  </div>        

  <div id="center">        

    <p>        

      Notice that this column is not able to be positioned. Its        

      position will thus retain its "natural" place based on its        

      location in the HTML file that generates the page. Margin        

      settings ensure that the left and right columns (which are        

      set to 200 pixels in width) will have room for their content        

      without creating a visible space between any of the        

      adjoining columns.        

    </p>        

  </div>        

  <div id="right">        

    <p>        

      The right-hand column is so much like the left-hand column        

      that it seems unworthy of comment.        

    </p>        

  </div>        

</body>        

</html>
```

##### 添加顶部标题区域

另一种常见的页面布局通过添加顶级标题区域来修改基本的三列设计。可以想象，这并不难实现。下面是这样一个页面上四个内容块的样式规则。这三个延期几乎是相同的；我给中间的块添加了一个灰色背景，给顶部的块也添加了一个灰色背景，只是为了更容易看到块的开始和结束。

```
#top {         

  margin: 20px;         

  padding: 10px;         

  background: #ccc;         

  height: 100px;         

}         

#left {         

  position: absolute;         

  left: 10px;         

  top: 160px;         

  width: 200px;         

}         

#center {         

  background: #ccc;         

  margin-top: 0;         

  margin-left: 220px;         

  margin-right: 220px;         

}         

#right {         

  position: absolute;         

  right: 10px;         

  top: 160px;         

  width: 200px;         

}
```

图 5.23 显示了将这些规则应用到一个页面的结果，这个页面与生成图 5.22 的 HTML 几乎相同。唯一的区别是这个 HTML 包含下面的片段，它定义了页面上顶部块的内容:

```
<div id="top">         

  <h1>         

    This is the header area of the three-column-plus-header layout         

  </h1>         

</div>
```

![1171_5.23](img/c2c2dc1209079062ca8a79f3dde27b88.png)
图 5.23。带有顶部标题块的基本三列布局

这些布局主题有许多变化。据我所知，学习如何有效应用这些变化的最好地方之一是欧文·布里格斯的精彩网站，面条事件。

##### 摘要

本章介绍了 CSS 布局和定位中涉及的重要概念，从盒子模型开始，一直到 position 属性的各种变化。然后，我们通过组装一个“经典的”三列布局的例子来阐明这些观点。

您可能已经注意到，在 CSS 中，实现给定效果的方法通常不止一种。例如，如果您要将一个块放在浏览器窗口的右侧，占据其宽度的 20%，您可以给它 80%的左边距，使用绝对定位并将其 left 属性设置为 80%，将其 width 属性设置为 20%，或者使用绝对定位并将其 right 属性设置为 0，将其 width 属性设置为 20%。

在第 6 章中，我们将看到，在实践中，并非所有这些选项都完全相同。我们将探索当前浏览器的一些非理想行为，这些行为导致我们选择一个选项而不是另一个。我们还将研究一些更高级的 CSS 属性，它们会影响页面上元素的布局关系。

这四章到此为止！如果您想在购买前试用，只需立即[下载所有 4 个样本章节，外加免费样本参考资料](https://www.sitepoint.com/books/css1/signoff.php)！关于建立你自己的基于 CSS 的网站的完整指南，请点击[这里](https://www.sitepoint.com/books/css1/)查阅纸质书。

这本书不仅包含您刚刚阅读的四章，还包含另外八章，涵盖布局的精确细节、文本和其他内容的样式，以及 CSS 的一些不太常见但很重要的用途，包括用户体验的改善，以及验证和向后兼容性。这本书还包括一套完整的附录，使其成为基于 CSS 的网页开发的绝佳参考。有关更多信息，[请参见本书第](https://www.sitepoint.com/books/css1/)页。

## 分享这篇文章