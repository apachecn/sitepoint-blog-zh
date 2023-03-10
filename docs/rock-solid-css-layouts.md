# 坚如磐石的 CSS 布局

> 原文：<https://www.sitepoint.com/rock-solid-css-layouts/>

**这一章摘自 SitePoint 的 [HTML Utopia:使用 CSS 设计无表格，第二版](https://www.sitepoint.com/books/css2/)，它提供了对 CSS 的完整介绍，并向您展示如何从头开始构建坚如磐石的基于 CSS 的网站。在这本书的 12 章结束时，你将理解 CSS 的来龙去脉，并且你将能够创建健壮的、符合标准的站点设计，这些设计在旧的浏览器中优雅地退化并且易于维护。**

如果你喜欢离线阅读，你可以[下载 PDF 格式的这一章](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，以及这本书的前三章。现在，让我们开始构建基于 CSS 的页面布局吧！

我们现在已经有了一些可靠的理论。这本书的其余部分将集中在你如何在开发自己的网站时将 CSS 付诸实践。在这个过程中，我们将学习如何使用 CSS 布局页面——从简单的布局到更复杂的布局——以及如何结合一些你已经读过的概念来创建好看的网站。

本章将从创建一个简单的两列布局开始。在这个过程中，我们将发现如何使用绝对和相对定位，并了解边距、填充和边框如何协同工作。然后，我们将通过创建一个两列布局来了解如何在实践中一起使用所有这些工具，该布局使用了我们在本书中已经讨论过的许多技术。

虽然我们在本章中创建的布局相对简单，但它是许多网站都使用的结构；我们在这里开发的布局可以很容易地构成生产站点的基础。

##### 布局

许多网站设计都是从图形程序的实体模型开始的。我们的第一个示例站点也不例外:我们有一个在 Fireworks 中创建的示例布局或“设计组合”作为起点。

![1523_fig1](img/2d90a941c68e5a008a47b89b8cfdf4d5.png)
图 8.1。将布局创建为图像文件

从这样的视觉效果开始，我们可以在开始编写任何 XHTML 或 CSS 之前，思考我们将如何构建网站。它让我们有机会在编写一行代码之前决定如何最好地处理这个特殊的布局。

这种布局将页面分为三个主要部分:一个标题，包含站点徽标和一些主要导航；主要内容区域，包括新闻故事列表上方的大图像；和一个侧边栏，显示一些附加项目。

![1523_fig2](img/bd32f27f23434bc0fff8a941469e87bb.png)
图 8.2。在布局上标记主要部分

这种布局可以描述为带有标题区域的两列布局。能够将一个设计可视化为其主要部分的组合，可以简化决定如何处理页面布局的过程。

##### 创建文档

决定了页面的基本组件之后，我们就可以开始工作了。我们要做的第一件事是创建一个 XHTML 文档，其中包含我们在布局图像中看到的所有文本元素，并使用正确的 XHTML 元素进行标记。

以这种方式工作起初可能看起来有点奇怪，特别是如果您已经习惯于在可视化环境(如 Dreamweaver)中工作，并且仅仅关注设计的外观。然而，使用 CSS 进行布局的一个好处是，我们能够将页面的结构与其外观分开。这允许我们在使用 CSS 添加设计之前，集中精力构建一个好的、可靠的文档作为我们网站的基础。

我们从 XHTML Strict 文档的基本要求开始。由于我们将使用 CSS 来表示这个网站上的所有信息，所以没有理由不使用严格的 DOCTYPE。过渡文档类型(适用于 XHTML 和 HTML 4.01)允许您使用 W3C 推荐标准中现在不推荐使用的属性和元素。不推荐使用的元素和属性主要用于表示，因为我们将使用 CSS——而不是 XHTML——来表示，所以我们不需要使用它们。

```
Example 8.1\. index.html 

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" 

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 

<html > 

  <head> 

    <title>Footbag Freaks</title> 

    <meta http-equiv="Content-Type" 

        content="text/html; charset=iso-8859-1" /> 

  </head> 

  <body> 

  </body> 

</html>
```

*声明字符集*

*在我们的页面中，我们使用 meta 元素和`http-equiv="Content-Type"`属性来声明文档的字符集。这使得浏览器(和 W3C 验证器)很容易确定文档中使用的是哪种字符集。如果这些信息丢失了，浏览器可能会错误地解释页面中的字符，从而将您的页面视为无法理解的垃圾。*

本书中的所有例子都使用 ISO-8859-1 编码，这是大多数流行的文本编辑器和程序(如 Dreamweaver)的默认编码。如果您处理的是不同的字符集，比如 Unicode，那么您需要相应地更改元元素。

***表头***

让我们开始将该页面的内容添加到我们的文档中。当我们这样做时，我们将把它分成上面确定的各个部分，包含在`<div>`和`</div>`标签之间的每个页面部分。我们将给每个 div 一个 id 来标识该部分；我们将使用这些 id 来处理每个部分，并使用 CSS 对其进行样式化。

在`<body>`标签之后，添加以下标记:

```
Example 8.2\. index.html (excerpt) 

<div id="header"> 

  <p>The Home of the Hack</p> 

  <ul> 

    <li><a href="">Contact Us</a></li> 

    <li><a href="">About Us</a></li> 

    <li><a href="">Privacy Policy</a></li> 

    <li><a href="">Sitemap</a></li> 

  </ul> 

</div> <!-- header -->
```

在这一点上，我们不会担心任何图像元素，因为有许多方法可以使用 CSS 将图像添加到页面中；我们将在创建 CSS 时决定添加每张图片的最佳方式。因此，标题区域只包含标签行“黑客之家”和一个包含主要导航链接的列表。

***主要内容章节***

接下来是主要内容部分，包含在带有`content`的`id`的`div`中:

```
Example 8.3\. index.html (excerpt)  

<div id="content">  

  <h2>Simon Says</h2>  

  <p>Simon Mackie tells us how a change of shoes has given him new  

      moves and a new outlook as the new season approaches.</p>  

  <p><a href="">Read More</a></p>  

  <h2>Recent Features</h2>  

  <ul>  

    <li>  

      <h3>Head for the Hills: Is Altitude Training the  

          Answer?</h3>  

      <p>Lachlan 'Super Toe' Donald</p>  

      <p>Vestibulum ante ipsum primis in faucibus orci luctus et  

          ultrices posuere cubilia Curae; Praesent hendrerit  

          iaculis arcu.</p>  

      <p><a href="">Full Story</a></p>  

    </li>  

    <li>  

      <h3>Hack up the Place: Freestylin' Super Tips</h3>  

      <p>Jules 'Pony King' Szemere</p>  

      <p>Vestibulum ante ipsum primis in faucibus orci luctus et  

          ultrices posuere cubilia Curae; Praesent hendrerit  

          iaculis arcu.</p>  

      <p><a href="">Full Story</a></p>  

    </li>  

    <li>  

      <h3>The Complete Black Hat Hacker's Survival Guide</h3>  

      <p>Mark 'Steel Tip' Harbottle</p>  

      <p>Vestibulum ante ipsum primis in faucibus orci luctus et  

          ultrices posuere cubilia Curae; Praesent hendrerit  

          iaculis arcu.</p>  

      <p><a href="">Full Story</a></p>  

    </li>  

    <li>  

      <h3>Five Tricks You Didn't Even Know You Knew</h3>  

      <p>Simon 'Mack Daddy' Mackie</p>  

      <p>Vestibulum ante ipsum primis in faucibus orci luctus et  

          ultrices posuere cubilia Curae; Praesent hendrerit  

          iaculis arcu.</p>  

      <p><a href="">Full Story</a></p>  

    </li>  

  </ul>  

</div> <!-- content -->
```

该区域将包含带有文本覆盖的大图，突出显示专题报道。下面将列出四条新闻。

***工具条***

最后，让我们添加侧栏，它包含一个搜索框和一些重要的日期:

```
Example 8.4\. index.html (excerpt)  

<div id="sidebar">  

  <h3>Site Search</h3>  

  <form method="post" action="" id="searchform">  

    <div>  

      <label for="keywords">Keywords</label>:  

      <input type="text" name="keywords" id="keywords" />  

    </div>  

    <div>  

      <input type="submit" name="btnSearch" id="btnSearch" />  

    </div>  

  </form>  

  <h3>Coming Events</h3>  

  <ul>  

    <li>10 Apr 06 -<br /><a href="">Seattle Zone  

        Qualifier</a></li>  

    <li>13 Apr 06 -<br /><a href="">World Cup - Round 8</a></li>  

    <li>21 Apr 06 -<br /><a href="">FootbagOOM 05 - NY</a></li>  

    <li>28 Apr 06 -<br /><a href="">WFPA AGM - Hong Kong</a></li>  

    <li>3 May 06 -<br /><a href="">World Cup - Round 9</a></li>  

  </ul>  

  <h3>Move of the Month</h3>  

  <h4>The Outer Stall</h4>  

  <p>Eti bibendum mauris nec nulla. Nullam cursus ullamcorper  

      quam. Sed cursus vestibulum leo.</p>  

  <p><a href="">more</a></p>  

</div> <!-- sidebar -->
```

这就完成了我们对网站主页的标记。保存页面并在浏览器中查看。文档的内容将使用我们已经使用过的元素的默认样式来显示，如图 8.3 所示。它不会很漂亮，但应该容易阅读！

在开始添加将创建我们在示例图形中看到的设计的 CSS 之前，我们的最后一项工作是验证我们的标记。通过在这一点上验证文档，我们将知道我们正在向一个有效的文档添加 CSS:我们不会遇到由现有的无效标记引起的问题。

![1523_fig3](img/ce7531e925df49f00239f043a1353629.png)
图 8.3。添加内容后显示页面

##### 定位页面元素

我们现在可以开始创建样式表了。但是，在此之前，我们需要花点时间来理解一些在创建这样的布局(以及许多其他布局)时会用到的基本概念:显示属性、定位的概念以及 CSS Box 模型技术。

***`display`地产***

在我们继续看 CSS 定位问题之前，我们应该快速看一下`display`属性，因为它对页面布局有很大的影响。

属性决定了浏览器如何显示一个元素——是将其视为一个块、一个内联文本片段还是其他什么。虽然它可以被赋予 17 个合法值中的任何一个，但是浏览器支持的现实将列表限制为 6 个，其中只有 4 个是真正重要的。有关显示的完整参考，请参见附录 C，CSS 属性参考。

display 属性的六个可能值是:

*   `block`
*   `inline`
*   `list-item`
*   `none`
*   `table-footer-group`
*   `table-header-group`

默认值因元素而异。诸如`p`、`h1`、`div`等块元素默认为`block`，而诸如`strong`、`code`、`span`等行内元素(通常出现在一段文本中的元素)默认为`inline`。列表项默认为`list-item`。将非默认设置分配给元素可以产生有趣且有用的效果。在本书的后面，我们将会看到如何使用`display: inline`来使列表水平显示。

如果您提供一个值`none`，它所应用的元素将不会显示，并且它通常占据的空间将被折叠。这将`display: none`声明与`visibility: hidden`声明区分开来，后者通常用于隐藏一个元素，但保留它在可见时所占据的空间。

***绝对、相对、定位语境***

CSS position 属性采用单个常量值，该值确定块在页面上的定位方式。两个最常用的值是`absolute`和`relative`。另一个值`static`是该属性的默认值；Internet Explorer 6 不支持第四个值`fixed`。

CSS 中的定位可能会令人困惑，因为指导块在页面上放置的参考点会根据块的定位上下文而变化。即使使用绝对定位值，也没有统一的坐标来指导放置。每次使用 static 以外的位置设置将块定位在页面上时，它都会为其后代创建一个新的定位上下文，其中其内容区域的左上角具有坐标(0，0)。因此，如果您使用 CSS 在该块中定位一个元素，那么它的位置将会相对于新的坐标系进行计算——它的“定位上下文”

理解这个概念的最好方法是看几个简单的、相互关联的例子。让我们从一张白纸开始。在这种情况下，视窗的左上角(浏览器窗口的可视区域)是初始(0，0)坐标所在的位置。让我们在一个`div`中放置一段简单的文本，如图 8.4 所示。

![1523_fig4](img/0cae1163643171f860fd25ae9488c801.png)
图 8.4。文本的第一行

下面是产生上述结果的 HTML 片段。CSS 属性 top 和 left 用于在页面上定位`div`,将其定位在距离页面顶部 75 像素的位置，并从页面左侧缩进 125 像素:

```
Example 8.5\. positioning.html (excerpt)  

<div style="position: absolute; left: 125px; top: 75px;"  

    class="big">  

  This is the first line of text being positioned.  

</div>
```

现在，将第二个`div`放在第一个里面，如下所示:

```
Example 8.6\. positioning.html (excerpt)  

<div style="position: absolute; left:125px; top: 75px;"  

    class="big">  

  This is the first line of text being positioned.  

  <div style="position: absolute; left: 25px; top: 30px;"  

      class="big">  

    This is a second line.  

  </div>  

</div>
```

![1523_fig5](img/1558139e1d1976819f1b08782a9945dd.png)
图 8.5。定位在定位块内部的元素

结果如图 8.5 所示。请注意，第二行文本从第一行文本的左侧缩进 25 个像素，因为第一行设置了第二行的定位上下文:它是第二行的父元素。两条线绝对定位；但是，第一条线位于视口的顶部和左侧，第二条线位于第一条线的顶部和左侧。还要注意，它的字体很大。为什么？看一看大班的风格法则，你就明白了:

```
Example 8.7\. positioning.html (excerpt)  

.big {  

  font-family: Helvetica, Arial, sans-serif;  

  font-size: 2em;  

  font-weight: bold;  

}
```

由于第二个`div`是第一个的子元素，所以它的字体大小是相对于第一个`div`来计算的。样式规则将字体定义为大小为 ems，这指示浏览器以两倍的大小呈现文本。当两个 em 规则应用于第一行时，其大小加倍。但是当它应用于第二行时，第一行的字体大小被加倍以计算第二行的字体大小。

我们可以使用绝对字体大小常量来纠正这一点:

```
Example 8.8\. positioning.html (excerpt)  

.big {  

  font-family: Helvetica, Arial, sans-serif;  

  font-size: large;  

  font-weight: bold;  

}
```

这两个`div`现在应该共享相同的字体大小。

页面现在有两个`div`元素，一个嵌套在另一个里面。两者都采用绝对定位。现在，让我们添加第三个元素——这一次，一个包含在第二个`div`中的`span`元素。使用相对定位，HTML 看起来像这样:

```
Example 8.9\. positioning.html (excerpt)  

<div style="position: absolute; left: 125px; top: 75px;"  

    class="big">  

  This is the first line of text being positioned.  

  <div style="position: absolute; left: 25px; top: 30px;">  

    This is <span  

        style="position: relative; left: 10px; top: 30px;">an  

        example of</span> a second line.  

 </div>  

</div>
```

这个标记的结果可以在下面看到。请注意，span 中包含的单词“的示例”出现在它们原始位置的右下方。相对定位总是基于被定位元素在页面上的原始位置。换句话说，使用相对定位的元素的定位上下文是由其默认位置提供的。在本例中，跨度的位置如图 8.6 所示。如果没有应用定位，它会出现在正常位置的右下方，如图 8.7 所示。

![1523_fig6](img/bf5d5c5b225ea4edce3df376cede69d2.png)
图 8.6。相对定位的示例

![1523_fig7](img/6f1368666ffe77cca954839a76d355f7.png)
图 8.7。移除了定位的相同示例

如果这个概念仍然看起来有点混乱，不要担心；当我们创建布局时，我们将看看这些概念在实践中是如何工作的。

***盒子型号***

从样式表的角度来看，您在 HTML 页面中处理的每一项都可以被视为存在于一个盒子中。当你格式化大块的内容时，这个事实通常更加明显，就像我们在设计中确定的三个主要页面区域。但是，即使是在处理这些元素的单个组件时也是如此，比如标题、列表、列表元素，甚至是文本片段。

基本的 CSS 盒子模型如图 8.8 所示。

![1523_fig8](img/d0d19de3c030b3598cdfeda0c3f4734b.png)
图 8.8。基本的 CSS 盒子模型

CSS 盒子模型的中心是内容本身。不要认为这个“内容”与可能构成新闻故事内容或一组链接的文字或图像是一样的。“内容”描述盒子区域内包含的任何项目。

请注意，从图中可以看出，框的可见宽度是由内容宽度、填充和边框的总和决定的。边距决定了可见框各边与相邻元素之间的距离。类似地，通过将内容的高度添加到填充和边框设置中来确定框的可见高度。同样，边距决定了框与相邻对象的垂直距离。

这些元素的宽度——边距、边框和填充——可以使用四个 CSS 属性(盒子的每一边一个)或一个速记属性来设置。边框行为稍微复杂一些，因为除了宽度之外，边框还可以具有线条样式和颜色等特征。

在这次讨论中，我将从详细解释和演示填充的使用开始。然后，我将继续讨论边距，这将更简短，因为它与填充非常相似。最后，我将讨论边界。

在接下来的几节中，我将使用一个基本的单框布局来演示 CSS 规则技术。它开始时的布局如图 8.9 所示，没有填充、边框或空白:内容与框的大小相同。

![1523_fig9](img/776187f8ab24fbfcfca9ec5904ab025a.png)
图 8.9。箱式模型演示的起点

我给 h1 元素一个灰色背景，这样你可以更容易地看到我将要演示的效果的影响。下面的 HTML 生成了如图 8.9 所示的页面:

```
Example 8.10\. boxmodel.html   

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">   

<html >   

  <head>   

    <title>Box Model Demo</title>   

    <meta http-equiv="Content-Type"   

        content="text/html; charset=iso-8859-1" />   

    <style type="text/css">   

      h1 {   

        background-color: #c0c0c0;   

        color: black;   

      }   

    </style>   

  </head>   

  <body>   

    <h1>Help! I'm stuck in a box model!</h1>   

  </body>   

</html>
```

在接下来的讨论中，我将只修改样式表信息，所以我将只复制代码的这一部分，用粗体表示任何变化。

*像素与百分比*

由于盒子模型处理屏幕上内容的显示，像素是 CSS 中最常用的绝对度量单位。然而，如果你需要创建一个占据所有可用空间的布局，不管浏览器窗口有多大，有必要使用百分比而不是像素。这种布局的特点是它们的“弹性”行为——当用户调整浏览器窗口大小时，页面元素成比例地扩展和收缩。

*填充属性*

四个属性一起定义了 CSS 规则中对象周围的填充:`padding-left`、`padding-right`、`padding-top`和`padding-bottom`。

让我们只改变一个填充设置来感受一下它是如何工作的。修改示例文件中的样式表，以便它复制下面的片段(请记住，新材料在下面以粗体显示):

```
Example 8.11\. boxmodel.html (excerpt)   

h1 {   

  background-color: #c0c0c0;   

  color: black;   

  padding-left: 25px;   

}
```

这一变化的结果如图 8.10 所示。请注意，文本现在从距框左侧 25 像素处开始，导致文本左侧出现 25 像素的空白灰色空间。

![1523_fig10](img/e3fcfd19c65082978b8035210328c0ba.png)
图 8.10。演示填充-左侧

如您所料，您可以用同样的方式设置其他填充大小，如以下代码片段所示:

```
Example 8.12\. boxmodel.html (excerpt)   

h1 {   

  background-color: #c0c0c0;   

  color: black;   

  padding-left: 25px;   

  padding-top: 15px;    

  padding-bottom: 30px;   

  padding-right: 20px;   

}
```

![1523_fig11](img/34c858f79f28c077a529e5ff6da345fd.png)
图 8.11。定义所有四个填充属性

你可以在图 8.11 中看到这些变化的影响。

您可能会注意到，右手边的衬垫似乎不起作用。您要求 20 个像素，但是无论您将窗口扩展到多宽，定义包含 h1 元素的框的灰色区域都会继续下去。

这是因为`padding-right`在文本的右边缘和标题的右边缘之间创建了一个空格，如灰色框所示。在这种情况下，很难看到间距，因为标题会自动跨越浏览器窗口的宽度，为文本在右侧留出足够的空间。不过，如果你让浏览器足够窄，你可以看到填充生效。

![1523_fig12](img/a2fd83ae68ac4d97d985e1d51abf412c.png)
图 8.12。演示`padding-right`的效果

图 8.12 展示了这一原理。第一个屏幕截图显示了图 8.11 中的页面，如果您缩小浏览器窗口，以便在没有设置 padding-right 的情况下，在第一行有空间放置单词“in”。第二个屏幕截图强调了这一点，它显示了调整后的页面，每行只能显示一个单词。请注意，在某些情况下，正确的填充大小看起来足够容纳下一行中的单词。事实上，仅仅从样式表中移除`padding-right`声明就会产生如图 8.12 所示的结果。

因为经常需要调整 HTML 中对象周围的填充，所以 CSS 标准定义了一个简单的属性，简称为 padding。您最多可以为该属性赋予四个值；表 8.1 确定了在每种情况下如何分配属性。

表 8.1。多个值对填充速记属性
![1523_table1](img/065ba09e9bf354a75ff14cbfcd1a9e48.png)的影响

*记住顺序*

要记住这些值被指定的顺序，只需回想它们是从顶部开始按顺时针方向被识别的，或者记住助记符的麻烦(顶部、右侧、底部和左侧)。

例如，上面的样式规则可以使用 padding 速记属性重写，如下所示:

```
Example 8.13\. boxmodel.html (excerpt)   

h1 {   

  background-color: #c0c0c0;   

  color: black;   

  padding: 15px 20px 30px 25px;   

}
```

要创建相等的顶部和底部填充，以及相等的左侧和右侧填充，您可以使用:

```
Example 8.14\. boxmodel.html (excerpt)   

h1 {   

  background-color: #c0c0c0;   

  color: black;   

  padding: 15px 25px;   

}
```

最后，要在`h1`元素的四边创建相等的填充，可以使用以下标记:

```
Example 8.15\. boxmodel.html (excerpt)   

h1 {   

  background-color: #c0c0c0;   

  color: black;   

   padding: 25px;   

}
```

如果使用 ems 或百分比作为填充值，会发生什么情况？这两个单位的效果稍有不同:em 单位根据内容的字体大小缩放填充，而 percentage 单位根据包含元素的块的宽度或高度缩放填充。为了演示这些效果，让我们使用一个新的 HTML 页面，它在一个对比色页面上的彩色背景下显示两个标题。

以下是该演示页面的 HTML:

```
Example 8.16\. boxmodel2.html   

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">   

<html >   

  <head>   

    <title>Box Model Demo</title>   

    <meta http-equiv="Content-Type"   

        content="text/html; charset=iso-8859-1" />   

    <style type="text/css">   

      body {   

        background-color: #808080;   

        color: black;   

      }   

      h1, h4 {   

        background-color: #c0c0c0;   

        color: black;   

      }   

    </style>   

  </head>   

  <body>   

    <h1>Help! I'm stuck in a box model!</h1>   

    <h4>But it's not too crowded if you're just a little old   

        heading like me! In fact, it's kind of cozy in here.</h4>   

  </body>   

</html>
```

注意，我给了页面一个深灰色的背景，并添加了一个`h4`元素，我用与`h1`元素相同的 CSS 规则对其进行了样式化。

该 HTML 页面如图 8.13 所示。

![1523_fig13](img/86db7a2ca10b13529761a0e73609a346.png)
图 8.13。按比例填充页面起点

现在，让我们更改该页面的样式表，以便它使用 padding 属性在对象周围创建单个 em 填充空间。下面的代码片段可以完成这个任务:

```
Example 8.17\. boxmodel2.html (excerpt)   

body {   

  background-color: #808080;   

  color: black;   

}   

h1, h4 {   

  background-color: #c0c0c0;   

  color: black;   

  padding: 1em;   

}
```

如图 8.14 所示，出现在两个标题元素周围的填充量与元素本身使用的字体大小成正比。

*em:高度测量值*

*记住，一个 em 等于所用字体的高度。因此，`h1`元素周围的空间比`h4`元素周围的空间大得多。*

让我们看看，如果我们对比例填充值使用百分比而不是 em，会发生什么。更改 HTML，使样式表如下所示:

```
Example 8.18\. boxmodel2.html (excerpt)   

body {   

  background-color: #808080;   

  color: black;   

}   

h1, h4 {   

  background-color: #c0c0c0;   

  color: black;   

  padding: 10%;   

}
```

![1523_fig14](img/fa14dcd684a08250e91f655ee8c15d82.png)
图 8.14。使用 ems 进行比例填充

这种变化的结果可以在图 8.15 中看到。哇！这些元素周围有大量的空间。浏览器将页面宽度的 10%用作四边的填充。

![1523_fig15](img/61a2bbd56fcc9a8d8d28083dc7200331.png)
图 8.15。将百分比用于比例间距

我在这些元素的文本后面使用了一种背景色，以便于看到不同填充设置的效果，但是背景色不是必需的。图 8.16 使用了与图 8.15 相同的 HTML 代码；唯一的区别是我已经从 body、`h1`和`h4`元素中移除了背景色。如您所见，这些元素保持了它们的相对间距。

![1523_fig16](img/bc0edcc74992880a6c6d5075a7b49a78.png)
图 8.16。演示不带彩色背景的衬垫

***边距属性***

边距和填充的区别在于，边距存在于对象的边界之外，而填充存在于这些边界之内。图 8.17 根据下面代码片段中设置的样式表规则说明了这种差异。边距的设置方式与填充方式相同；唯一的区别是用“边距”代替了“填充”

```
body {    

  background-color: #808080;    

  color: black;    

}    

h1 {    

  background-color: #c0c0c0;    

  color: black;    

}    

h2 {    

  background-color: #c0c0c0;    

  color: black;    

  margin-left: 5%;    

}    

p {    

  background-color: #c0c0c0;    

  color: black;    

  margin-left: 20%;    

}
```

![1523_fig17](img/70a4495e22b23f01cd7a9ceb7e723698.png)
图 8.17。左边距设置将内容和背景向右推

请注意，第二级标题和段落都具有左边距属性，从浏览器的左边缘缩进。但是，与我们设置 padding-left 属性的示例不同，在这种情况下，文本及其背景色块是缩进的。这是因为填充、颜色块和文本在内容框内，而边距在内容框外。

接下来，让我们将`padding-left`和`margin-left`设置应用于代码片段:

```
body {    

  background-color: #808080;    

  color: black;    

}    

h1 {    

  background-color: #c0c0c0;    

  color: black;    

}    

h2 {    

  background-color: #c0c0c0;    

  color: black;    

  margin-left: 5%;    

  padding-left: 1em;    

}    

p {    

  background: #c0c0c0;    

  color: black;    

  margin-left: 20%;    

  padding-left: 10%;    

}
```

正如你在图 8.18 中看到的，上面的标记导致了页边空白将 HTML 元素和它们周围的背景色块推到右边，而填充将文本移到了彩色背景色块的右边。

![1523_fig18](img/c2d581742342a1a51a7792d447c4d19e.png)
图 8.18。组合左边距和左边填充

如果您加载上面的 HTML(来自本书代码档案中包含的文件)并调整它的大小，您会注意到段落和标题的缩进随着窗口宽度的变化而变化。这是因为我们使用 20%的相对值作为边距，10%作为填充。这两个值都是相对于包含块(在本例中是浏览器窗口)的宽度计算的。浏览器窗口越大，段落的边距和填充就越大。标题上的填充不会改变，因为它是在 ems 中指定的。

***、页边距、*和列表**

默认情况下，所有可视化浏览器都会在列表的左边缘应用 50 像素的边距。这为列表项标记(项目符号列表中的项目符号；在有序列表的情况下是数字)。不幸的是，CSS 规范没有明确说明这个空格是应该实现为左边距还是浏览器默认样式规则中的左填充。然而，对标记偏移量属性的描述确实暗示了边距是一种方法。

无论规范的意图是什么，Firefox 和 Safari 在列表的左侧应用默认填充，而大多数其他浏览器(包括 Internet Explorer 和 Opera)使用边距。你可以通过将一个`background-color`应用到一个`ol`或者`ul`元素上来测试这个。在大多数浏览器上，背景不会覆盖列表项标记；在 Firefox 和 Safari 上，它们会。

因此，每当您将自己的左边距或填充值应用于列表时，必须确保同时指定这两个值。例如，如果您只应用了一个边距，默认的列表缩进将在 Firefox 中显示，但在所有其他浏览器上都将被覆盖。如果只应用填充值，默认的 50 像素边距将显示在 Internet Explorer 上。只有通过指定边距和填充(通常通过设置`padding: 0`并使用`margin`来完成这项工作)，才能确保在当前浏览器上的一致呈现。

您可以使用`margin-top`和`margin-bottom`属性设置垂直边距。这是另一个演示垂直边距的 HTML 页面:

```
Example 8.19\. boxmodel3.html    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">    

<html >    

  <head>    

    <title>Box Model Demo</title>    

    <meta http-equiv="Content-Type"    

        content="text/html; charset=iso-8859-1" />    

    <style type="text/css">    

      body {    

        background-color: #808080;    

        color: black;    

      }    

      h1 {    

        background-color: #c0c0c0;    

        color: black;    

        margin-bottom: 5cm;    

      }    

      h2 {    

        background-color: #c0c0c0;    

        color: black;    

        margin-left: 5%;    

        margin-top: 5cm;    

        margin-bottom: 5cm;    

        padding-left: 1em;    

      }    

      p {    

        background: #c0c0c0;    

        color: black;    

        margin-left: 20%;    

        padding-left: 10%;    

        margin-top: 5cm;    

        margin-bottom: 5cm;    

      }    

    </style>    

  </head>    

  <body>    

    <h1>No top margin but a 5cm bottom margin</h1>    

    <h2>Top and bottom margins are set to 5cm</h2>    

    <p>A paragraph with top and bottom margins set to 5cm</p>    

  </body>    

</html>
```

该页面如图 8.19 所示。

![1523_fig19](img/9c741fca4e15bbbbb17115d72215af43.png)
图 8.19。展示垂直边距

与水平边距不同，垂直边距不是累积的。如果你有两个元素一个堆叠在另一个之上，如图 8.19 所示的`h1`和`h2`元素，它们之间的垂直间距将是顶部元素的边距-底部设置和底部元素的边距-顶部设置中的较大值。在这种情况下，它们都是 5 厘米，所以两个元素之间的距离是 5 厘米(而不是你可能认为的 10 厘米)。如果我将 h1 的边距底部定义为 10 厘米，那么这两个元素之间的垂直距离应该是 10 厘米。在这种情况下，包含块是主体，实际上，它与浏览器窗口的客户区相同。

边距属性设置可以使用负值。当您已经为 HTML 页面的主体设置了一个`margin-left`属性，但是您想要将一个元素移动到更靠近页面左边距的位置时，这就很方便了。图 8.20 显示了以下 HTML 结果:

```
Example 8.20\. boxmodel4.html    

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">    

<html >    

  <head>    

    <title>Box Model Demo</title>    

    <meta http-equiv="Content-Type"    

        content="text/html; charset=iso-8859-1" />    

    <style type="text/css">    

      body {    

        background-color: #808080;    

        color: black;    

        margin-left: 5cm;    

      }    

      h1 {    

        background-color: #c0c0c0;    

        color: black;    

        margin-left: -3cm;    

      }    

      h2 {    

        background-color: #c0c0c0;    

        color: black;    

      }    

    </style>    

  </head>    

  <body>    

    <h1>The body's margin-left is 5cm, but mine is -3cm. </h1>    

    <h2>I have no margin-left setting, so I use the body's 5cm    

        setting.</h2>    

  </body>    

</html>
```

![1523_fig20](img/5be9d3cd7c50d07167dce41c3d6cf0d4.png)
图 8.20。实践中的负差幅设置

与 padding 属性一样，margin 速记属性允许您用一个声明设置所有四个边距，并使用表 8.1 中所示的规则解释多个值。

***边框属性***

边框属性比填充和边距属性更复杂，因为它们不仅影响对象之间的间距，还影响中间空间的外观。边界可以是，而且通常是可见的。在大多数情况下，管理边框属性类似于管理边距和填充的过程，但是有一些关键的区别。

边框有三种类型的属性:`style`、`width`和`color`。默认情况下，一个边框的`style`被设置为`none`，它的`width`被设置为`medium`(注意，Netscape 4 将默认边框宽度设置为 0，因此如果您希望针对该浏览器，您不能依赖默认值)，它的`color`被设置为它所应用的 HTML 元素的文本颜色。

`border-style`属性可以取一系列常数值中的任何一个。可用的值有`solid`、`dashed`、`dotted`，

双精度、`groove`、`ridge`、`inset`、`outset`、`hidden`、`none`。

隐藏值与`none`具有相同的效果，但应用于表格布局时除外。有关详细信息，请参考附录 C CSS 属性参考中的`border-style`属性。

W3C 规范在很大程度上把这些边界的精确外观问题留给了浏览器，所以如果使用这些特征的结果因浏览器和平台的不同而略有不同，也不要感到惊讶。但是，就像其他边框设置的默认行为一样，一般来说，浏览器在合理的范围内可以预见并令人满意地处理这个问题。

对象周围边框的宽度可以用四个单独的声明或者用`border-width`简写语法来设置。这四个属性是`border-top-width`、`border-right-width`、`border-bottom-width`和`border-left-width`。这些属性中的每一个都可以用绝对或相对长度单位(如像素、ems、百分比或英寸)来设置，或者用三种描述性设置之一来设置:`thin`、`medium`或`thick`。

如果您使用`thin`、`medium`和`thick`的描述性设置，结果取决于浏览器。然而，它们在不同的浏览器和操作系统中是相当可预测和一致的，对于三个描述性设置中的每一个都在一个像素左右。

*具体的边界测量*

如果您希望使用特定的边框宽度尺寸，您应该使用像素。这是屏幕布局最有意义的度量单位，在这里边框宽度是一个重要的属性。

您可以使用`border-top-color`、`border-right-color`、`border-bottom-color`和`border-left-color`属性来控制与所有四个边框相关的颜色，或者您可以只使用`border-color`属性。

正如我们在第五章中所发现的，添加一些颜色，你可以以任何标准的方式提供颜色参数:使用十六进制 RGB 代码(如在`#ff9900`中)，使用三位十六进制 RGB 快捷键(如在`#f90`中)，通过`rgb`函数(如在`rgb(102,153,0)`中)，或者使用标准颜色名称(如在`red`)。

速记属性`border-style`、`border-width`和`border-color`都接受多个值。

有一个额外的简写属性可能是使用最广泛的。属性允许你以简洁的形式指定一个对象的所有四个边框的样式、宽度和颜色。因为所有边都一致的边框通常是您想要的，所以这是设置 border 属性值的有效方法。

下面的样式规则将在任何带有`class="warning"`的元素周围生成一个统一的三像素红色实心边框:

```
.warning {    

  border: 3px solid red;    

}
```

##### 构建布局

现在我们已经对使用 CSS 定位元素时元素的行为方式有了一些背景知识，我们可以将我们的学习应用到我们的第一个布局中。

创建一个名为`styles.css`的新样式表，通过在文档头添加以下标记，将其链接到我们之前创建的 Footbag Freaks 文档:

```
Example 8.21\. index.html (excerpt)     

<head>     

  <title>Footbag Freaks</title>     

  <meta http-equiv="Content-Type"     

      content="text/html; charset=iso-8859-1" />     

  <link rel="stylesheet" type="text/css" href="styles.css" />     

</head>
```

我们将添加 CSS 的第一个元素是`body`元素。该设计有一个背景图像，以一个图案开始，但逐渐融入深蓝色。为了在我们的页面上创建这种效果，我们将应用图像作为平铺背景，并给页面一个蓝色背景。这样，当背景图像完成时，它会无缝地合并到蓝色页面背景中。

*下载足球怪胎*

Footbag Freaks 网站，包括所有图片，可以作为本书代码档案的一部分下载。

让我们使用下面的标记来设置字体系列和字体大小，并将页面的边距和填充(视窗边缘和内容之间的空间)设置为 0。

```
Example 8.22\. styles.css     

body {     

  margin: 0;     

  padding: 0;     

  background-color: #050845;     

  color: white;     

  background-image: url(img/bg.jpg);     

  background-repeat: repeat-x;     

  font: small Arial, Helvetica, Verdana, sans-serif;     

}
```

***设定畸形人`font-size`***

我已经使用关键字`small`在主体上设置了`font-size`。当我们创建样式表的其余部分时，我将使用百分比字体大小来使每个元素的大小减小一个百分比。

现在，你的背景图片应该平铺在页面的宽度上，如图 8.21 所示。

![1523_fig21](img/3d0eba7141d5c818997cf18724e203c8.png)
图 8.21。背景图像沿页面宽度平铺

在我们的布局图像中，页面的内容包含在一个灰白色的框中。要创建这个盒子，我们需要添加另一个`div`来包装内容。因此，在文档中的开始`<body>`标记之后，立即添加下面以粗体显示的标记:

```
Example 8.23\. index.html (excerpt)     

<body>     

  <div id="wrapper">     

    <div id="header">     

      <p>The Home of the Hack</p>
```

不要忘记在文档的结束标记`</body>`之前关闭这个`div`，就像这样:

```
Example 8.24\. index.html (excerpt)     

      <p><a href="">more</a></p>     

    </div> <!-- main -->     

  </div> <!-- wrapper -->     

</body>
```

现在，让我们向样式表中添加规则，这些规则将为盒子提供灰白色背景。我们还将插入向包装区域添加边距的规则，在包装和主体元素之间创建一个空间，以便让背景图像显示出来:

```
Example 8.25\. styles.css (excerpt)     

#wrapper {     

  background-color: #fdf8f2;     

  color: black;     

  margin: 30px 40px 30px 40px;     

}
```

![1523_fig22](img/c8c19431b86a58bcf766144d2740b64c.png)
图 8.22。样式化包装的效果`div`

图 8.22 显示了我们工作的结果。边缘创造了一个空间，让背景显示出来，但包装内的内容正好撞上了灰白色区域的边缘。我们可以通过向`#wrapper`规则添加填充来创建一些额外的空间，如下面的标记所示。结果显示如图 8.23 所示。

```
Example 8.26\. styles.css (excerpt)     

#wrapper {     

  background-color: #fdf8f2;     

  color: black;     

  margin: 30px 40px 30px 40px;     

  padding: 10px;     

}
```

![1523_fig23](img/b55fcacc904cb988f18a69f7f5593c66.png)
图 8.23。额外的填充在框的边缘和它的内容之间创建空间

***页眉区***

让我们把注意力转向我们布局的标题区域，它包含了站点标识和主导航。您会记得，当我们创建 HTML 文档时，我们没有添加任何图像:我们将在开发布局时决定如何最好地包含我们的图像。但是现在，让我们使用`img`元素添加徽标图像。我们还将把网站名称作为图片的替代文本，这样那些在关闭图片的情况下浏览网站的用户，以及那些使用屏幕阅读器的用户，就可以阅读网站的名称。

在文档中，将图像直接插入开始标题`div`的下方，如下所示:

```
Example 8.27\. index.html (excerpt)     

<body>     

  <div id="wrapper">     

    <div id="header">     

      <img src="img/logo.gif" alt="Footbag Freaks" height="77"     

          width="203" />     

      <p>The home of the hack</p>     

      <ul>     

        <li><a href="">Contact Us</a></li>     

        <li><a href="">About Us</a></li>     

        <li><a href="">Privacy Policy</a></li>     

        <li><a href="">Sitemap</a></li>     

      </ul>     

    </div> <!-- header -->
```

如果您在浏览器中查看该页面，您应该会在灰白色框的左上角看到该图像。

我们页面布局的图形显示了一个薄的浅蓝色边框，出现在网站标语和导航的上方和下方。我们将如何创造这种效果？让我们在另一个 div 中包含标签行和导航，我们可以对其应用顶部和底部边框。像这样加上`div`:

```
Example 8.28\. index.html (excerpt)     

<body>     

  <div id="wrapper">     

    <div id="header">     

      <img src="img/logo.gif" alt="Footbag Freaks" height="77"     

          width="203" />     

      <div id="header-bottom">     

        <p>The home of the hack</p>     

        <ul>     

          <li><a href="">Contact Us</a></li>     

          <li><a href="">About Us</a></li>     

          <li><a href="">Privacy Policy</a></li>     

          <li><a href="">Sitemap</a></li>     

        </ul>     

      </div> <!-- header-bottom -->     

    </div> <!-- header -->
```

我们现在可以添加顶部和底部边框来处理`#header-bottom`:

```
Example 8.29\. styles.css (excerpt)     

#header-bottom {     

  border-top: 1px solid #b9d2e3;     

  border-bottom: 1px solid #b9d2e3;     

}
```

为了设计导航列表和标签行的样式，我们将使用一些简单的文本格式属性，这些属性现在应该很熟悉了！

```
Example 8.30\. styles.css (excerpt)     

#header-bottom ul {     

  margin: 0;     

  padding: 0;     

}     

#header-bottom li {     

  display: inline;     

}     

#header-bottom a:link, #header-bottom a:visited {     

  text-decoration: none;     

  background-color: #fdf8f2;     

  color: #050845;     

}     

#tagline {     

  font-weight: bold;     

  background-color: #fdf8f2;     

  color: #050845;     

  font-style: italic;     

}
```

我们还需要给包含我们标语的段落添加一个`id`属性:

```
Example 8.31\. index.html (excerpt)     

<p id="tagline">The home of the hack</p>
```

![1523_fig24](img/c45bc6498ff64916e1cd1a1a2a26cc81.png)
图 8.24。用`display: inline`设计导航列表项目的样式

我们将该区域内列表上的`margin`和`padding`设置为`0`，然后将`li`元素的 display 属性设置为 inline，这将导致列表项显示在同一行上，而不是让每个项显示在新的一行上。图 8.24 显示了这种效果。我们还设计了导航链接——再次使用深蓝色并去掉下划线——以及标语，我们将其加粗、倾斜，并与导航项目的蓝色相同。

图 8.24 中显示的问题是很难区分导航列表中的链接。这个问题的建议解决方案是在每个链接之间添加一个可见字符，比如管道字符(|)，正如我在下面的标记中所做的那样(注意，这个建议是作为 Web 内容可访问性指南(WCAG) 1.0 的一部分提出的。涵盖此特定问题的检查点可以在此处看到[):](https://www.w3.org/TR/WCAG10/wai-pageauth.html#tech-divide-links)

```
Example 8.32\. index.html (excerpt)     

<ul>     

  <li><a href="">Contact Us</a> | </li>     

  <li><a href="">About Us</a> | </li>     

  <li><a href="">Privacy Policy</a> | </li>     

  <li><a href="">Sitemap</a></li>     

</ul>
```

我们还可以将列表项的颜色设置为深蓝色(`#050845`)，这样位于锚元素外部的管道字符也将是蓝色的。我们改进的标题设计如图 8.25 所示。

```
Example 8.33\. styles.css (excerpt)     

#header-bottom li {     

  display: inline;     

  background-color: #fdf8f2;     

  color: #050845;     

}
```

![1523_fig25](img/ef60f020b6cf9e1387dd7de1481ba16a.png)
图 8.25。在对标题区域中的文本元素进行样式化之后

头部现在真的开始成型了！我们的下一步是将标语和导航移到同一行。为此，我们将不得不使用一个属性，虽然我们还没有详细讨论它，但随着我们在这些布局中的进展，它将变得更加重要。那个属性是`float`。

*`float`房产*

```
float is one of the most interesting and often-used CSS properties. It takes a value of left, right, or none (though none, the default, is rarely used). float forces the element to which it's applied to display outside its natural position in the containing box; a float value of left or right pushes the element to the left or the right of its natural position, respectively. This property can be used within any block element.
```

`float`属性旨在替换与 HTML `img`元素相关联的 align 属性，并且实际上具有完全相同的效果。在 W3C 最近发布的 HTML 推荐标准中，`align`属性被弃用，取而代之的是`float`属性。下面的 HTML 片段使用`float`属性产生如图 8.26 所示的结果:

```
<p><img src="logo.gif" alt="Footbag Freaks Logo"     

    width="203" height="77"     

    style="float: left; padding-right: 1em;" />The Footbag Freaks     

    logo appears to the left of this paragraph. Depending on     

    whether or not I use the CSS <code>float</code> property, I     

    may see more than one line of text beside the logo. The CSS     

    <code>float</code> property replaces the deprecated     

    <code>align</code> attribute of the HTML <code>img</code>     

    element and has an identical effect.</p>
```

![1523_fig26](img/6b45aef7f4808d0d797a665a7bb91350.png)
图 8.26。使用 CSS `float`属性实现图文对齐

与 align 属性相比，`float`属性有一个主要优势:`float`可以应用于图像以外的元素，而旧的`align`属性的应用仅限于`img`、`applet`和`object`元素。

*没有维度？`width`宣告一个*

*当在没有明确定义维度的元素上使用`float`属性时，必须在 CSS 中包含一个`width`声明。img 是一个定义良好的维度元素的例子，而段落、标题或 div 则不是。*

*在我们的标题*中使用`float`

在下一章，当我们创建一个依赖于`float`来定位页面主要部分的布局时，我们将更详细地探讨`float`属性。然而，在这一点上，我们可以使用我们的浮动知识来正确地对齐标签行和导航。我们要浮动的元素是标语段落，所以将下面用粗体标记的规则添加到您的标语规则中:

```
Example 8.34\. styles.css (excerpt)     

#tagline {     

  font-weight: bold;     

  background-color: #fdf8f2;     

  color: #050845;     

  font-style: italic;     

  margin: 0;     

  padding: 0 0 0 20px;     

  width: 300px;     

  float: left;     

}
```

我们将`float`设置为`0`,这样段落的默认边距就被删除了。然后，我们添加 20 个像素的左填充以将标语从左侧移入，并给它 300 个像素的宽度以在其右侧提供一点空间，如页面的原始布局图所示。然后，我们将 float 的值设置为 left，因此它位于其余内容的左侧，在本例中是我们的导航列表。

对标语段落的规则进行更改后，保存样式表并在浏览器中查看页面。您应该会在标语旁边看到导航显示。这些元素的行为方式与我们上面讨论的例子中环绕图像的段落完全相同。我们现在需要做的就是将导航项目列表向右对齐，并改变列表上的填充，使其从右侧边缘稍微向内移动。这是您需要的标记；结果显示如图 8.27 所示。

```
Example 8.35\. styles.css (excerpt)     

#header-bottom ul {     

  margin: 0;     

  padding: 0 30px 0 0;     

  text-align: right;     

}
```

![1523_fig27](img/4e2b29a43db13e1a75bd98fc68fe0466.png)
图 8.27。浮动标志行并对齐导航后的显示

完成标题的最后一项任务是添加小足球图像，该图像显示在布局图像中导航右侧。首先，将实际的图像添加到文档中，在导航列表的下面。在下面的标记中，我给了这个图像一个空的`alt`属性，这样屏幕阅读器就不会读出这个图像的任何内容——这个图像只是为了显示的目的。我也给了这张图片一个`ball`的`id`。

```
Example 8.36\. index.html (excerpt)     

<div id="header">     

  <img src="img/logo.gif" alt="Footbag Freaks" height="77"     

      width="203" />     

  <div id="header-bottom">     

    <p id="tagline">The home of the hack</p>     

    <ul>     

      <li><a href="">Contact Us</a> | </li>     

      <li><a href="">About Us</a> | </li>     

      <li><a href="">Privacy Policy</a> | </li>     

      <li><a href="">Sitemap</a></li>     

    </ul>     

    <img src="img/header-ball.gif" height="24" width="20" alt=""     

        id="ball" />     

  </div> <!-- header-bottom -->     

</div> <!-- header -->
```

现在，让我们使用 CSS 中的第一点绝对定位来使图像正确对齐。我们知道图像相对于文档顶部和右侧的位置，因为我们知道包装器 div 上徽标的高度和边距的宽度。下面的 CSS 将在导航结束时把球放在正确的位置:

```
#ball {     

  position: absolute;     

  top: 110px;     

  right: 55px;     

}
```

标题部分现在完成了！它显示在图 8.28 中。

![1523_fig28](img/c5613b61488709cd2cb2fe91952e8f64.png)
图 8.28。布局的完整页眉部分

***内容区***

让我们继续创建页面主要内容区域的外观。我们要做的第一件事是在另一个 id 为 main 的 div 中包含侧栏和内容 div。这将有助于我们对齐标题下的侧边栏和内容部分。在割台关闭`</div>`后添加开始`<div id="main">`:

```
Example 8.37\. index.html (excerpt)      

    <img src="img/header-ball.gif" height="24" width="20" alt=""      

        id="ball" />      

  </div> <!-- header-bottom -->      

</div> <!-- header -->      

<div id="main">      

  <div id="content">      

    <h2>Simon Says</h2>
```

在侧边栏`div`的结束`</div>`标签后立即关闭此`div`。在样式表中，给`#main`一个十像素的`margin-top`来分隔内容和标题区域，如下面的代码片段所示。我们稍后将回到`#main`，创建我们的布局。

```
Example 8.38\. styles.css (excerpt)      

#main {      

  margin-top: 10px;      

}
```

现在，让我们为`#content`创建一个规则。将下列声明集添加到样式表中:

```
Example 8.39\. styles.css (excerpt)      

#content {      

  margin: 0 240px 0 0;      

  border: 1px solid #b9d2e3;      

  background-color: white;      

  color: black;      

}
```

我们将`#content`的上边距设置为`0`。然后，我们添加一个 240 像素的右边空白，为我们以后放置侧边栏留出空间。

我们也给这个框一个单一像素的纯色边框，和我们给标题边框用的蓝色一样，并给它一个白色的背景色。

***主要特征***

在页面的最顶端是一个“框选区”:一个视觉上包含在突出显示它的框内的区域。这个特别的专栏突出了主要的专题文章。现在让我们来看看。

通过添加 id 为 main feature 的 div，为主功能区创建一个容器；围绕主要功能的标题、段落和链接:

```
Example 8.40\. index.html (excerpt)      

<div id="content">      

  <div id="mainfeature">      

    <h2>Simon Says</h2>      

    <p>Simon Mackie tells us how a change of shoes has given him      

        new moves and a new outlook as the new season approaches.      

        </p>      

    <p><a href="">Read More</a></p>      

  </div> <!-- mainfeature -->      

  <h2>Recent Features</h2>
```

现在，您可以在样式表中设置主要功能区域的样式:

```
Example 8.41\. styles.css (excerpt)      

#mainfeature {      

  background-image: url(img/mainimg.jpg);      

  background-repeat: no-repeat;      

  background-color: #112236;      

  color: white;      

  padding: 2em 2em 1em 200px;      

}
```

在这里，我们添加背景图像`maining.jpg`，并将其设置为`no-repeat`。但是如果用户打开的浏览器尺寸比图片的尺寸大，我们不希望页面的暴露区域显示为白色。为了防止这种情况发生，我们添加了`#112236`的背景色；这与图像最右侧的颜色相同，因此图像应该无缝地淡入背景色。然后，我们将文本颜色设置为`white`，并使用填充将文本放置在距离盒子顶部两个 em、距离右侧两个 em、距离底部一个 em 以及距离左侧 200 像素的位置，这样就可以清楚地看到足球运动员的图像。

接下来，我们将设置 boxout 中的标题和段落的样式:

```
Example 8.42\. styles.css (excerpt)      

#mainfeature h2 {      

  margin: 0;      

  font-weight: normal;      

  font-size: 140%;      

}      

#mainfeature p {      

  font-size: 110%;      

}
```

最后，我们需要设计“更多阅读”链接，引导读者阅读整篇文章。让我们从给段落元素添加一个`class="more"`属性开始，这样我们就可以用我们的样式规则来定位它:

```
Example 8.43\. index.html (excerpt)      

<div id="mainfeature">      

  <h2>Simon Says</h2>      

  <p>Simon Mackie tells us how a change of shoes has given him new      

      moves and a new outlook as the new season approaches.</p>      

  <p class="more"><a href="">Read More</a></p>      

</div>
```

首先，我们从包含链接的段落中移除上边距，以减少它和段落之间的空间。然后，我们将`text-align`设置为`right`:

```
Example 8.44\. styles.css (excerpt)      

#mainfeature p.more {      

  margin-top: 0;      

  text-align: right;      

}      

#mainfeature p.more a:link, #mainfeature p.more a:visited {      

  color: white;      

  background-image: url(img/more-bullet.gif);      

  background-repeat: no-repeat;      

  background-position: center left;      

  padding-left: 14px;      

}
```

然后我们设计`link`和`visited`伪类的样式，将它们的颜色改为白色，并添加`more-bullet.gif`背景图像。我们只想看一次子弹，所以我们将`repeat`设置为`no-repeat`，然后将背景定位在中心和左侧。这会将图像放置在链接文本的中心。最后，为了防止文本显示在背景图像的顶部，我们将`padding-left`设置为 14 像素。这些变化的影响如图 8.29 所示。

![1523_fig29](img/3f3dd2becb07e6217711ffba1609cf9a.png)
图 8.29。在对主要特征部分进行样式化之后

***特性列表***

我们的布局现在真的开始成型了！让我们花一些时间来设计这个页面的主要内容:专题文章列表。

此时，内容区域内的文本紧靠着框的边界。我想在边框和内容之间创造一些空间。主页内容 div 的内容包含在一个无序列表中，所以我们的一个选择是向该列表及其上方的`h2`添加一个边距。然而，另一个页面可能有不同种类的主要内容，所以为了所有的页面都能以相同的方式处理，让我们添加另一个`div`，它包装了标题和特性列表，并给它一个内部类:

```
Example 8.45\. index.html (excerpt)      

<div id="content">      

  <div id="mainfeature">      

    <h2>Simon Says</h2>      

    <p>Simon Mackie tells us how a change of shoes has given him      

        new moves and a new outlook as the new season approaches.      

        </p>      

    <p class="more"><a href="">Read More</a></p>      

  </div> <!-- mainfeature -->      

  <div class="inner">      

    <h2>Recent Features</h2>      

    <ul>      

      <li>      

        <h3>Head for the Hills: Is Altitude Training the      

            Answer?</h3>      

        <p>Lachlan 'Super Toe' Donald</p>      

       <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

            ultrices posuere cubilia Curae; Praesent hendrerit      

            iaculis arcu.</p>      

        <p><a href="">Full Story</a></p>      

      </li>      

      <li>      

        <h3>Hack up the Place: Freestylin' Super Tips</h3>      

        <p>Jules 'Pony King' Szemere</p>      

       <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

            ultrices posuere cubilia Curae; Praesent hendrerit      

            iaculis arcu.</p>      

        <p><a href="">Full Story</a></p>      

      </li>      

      <li>      

        <h3>The Complete Black Hat Hacker's Survival Guide</h3>      

        <p>Mark 'Steel Tip' Harbottle</p>      

       <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

            ultrices posuere cubilia Curae; Praesent hendrerit      

            iaculis arcu.</p>      

        <p><a href="">Full Story</a></p>      

      </li>      

      <li>      

        <h3>Five Tricks You Didn't Even Know You Knew</h3>      

        <p>Simon 'Mack Daddy' Mackie</p>      

       <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

            ultrices posuere cubilia Curae; Praesent hendrerit      

            iaculis arcu.</p>      

        <p><a href="">Full Story</a></p>      

      </li>      

    </ul>      

  </div>      

</div> <!-- content -->
```

为了在特性列表和包含框的边框之间创建一些空间，让我们在样式表中为`#content .inner`添加一个边距:

```
Example 8.46\. styles.css (excerpt)      

#content .inner {      

  margin: 10px 20px 10px 40px;      

}
```

如果您在浏览器中查看布局，您应该会看到此边距创建的空间。我们现在可以讨论这一部分的内容。

首先，让我们来设计标题的样式。在我们的布局图像中，标题有一条蓝色下划线，延伸到内容的整个宽度——我们可以使用底部边框来创建这种效果。让我们在`h2`的底部添加少量的填充，在文本和边框之间插入一些空间:

```
Example 8.47\. styles.css (excerpt)      

#content .inner h2 {      

  color: #245185;      

  padding-bottom: 0.2em;      

  border-bottom: 1px solid #b9d2e3;      

  font-size: 110%;      

}
```

接下来，让我们添加一个规则来删除边距，并从功能项列表中列出项目符号。虽然我们可以简单地为`#content .inner ul`创建这个规则，但是由于这个页面的布局中只有一个列表，这种方法可能会在其他页面上引起问题，这些页面的内容包括与这个特殊功能列表不同的列表。因此，让我们先给`ul`元素添加一个`class="features"`属性，这样我们就可以设计这个特定的列表——以及其他类似的列表——而不会影响页面内容中任何普通的非功能列表:

```
Example 8.48\. index.html (excerpt)      

<div class="inner">      

  <h2>Recent Features</h2>      

  <ul class="features">      

    <li>      

Example 8.49\. styles.css (excerpt)      

#content .inner ul.features {      

  margin: 0;      

  padding: 0;      

  list-style: none;      

}
```

每个功能都有一个三级标题；我们将通过增加字体大小来设置这些样式:

```
Example 8.50\. styles.css (excerpt)      

#content .inner h3 {      

  font-size: 130%;      

}
```

让我们也让这些标题中的每一个都成为 Footbag Freaks 网站上相应文章的链接。我们也可以设计`link`和`visited`伪类的样式:

```
Example 8.51\. index.html (excerpt)      

<li>      

  <h3><a href="">Head for the Hills: Is Altitude Training the      

      Answer?</a></h3>      

  <p>Lachlan 'Super Toe' Donald</p>      

  <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

      ultrices posuere cubilia Curae; Praesent hendrerit iaculis      

      arcu.</p>      

  <p><a href="">Full Story</a></p>      

</li>      

<li>      

  <h3><a href="">Hack up the Place: Freestylin' Super Tips</a></h3>      

  <p>Jules 'Pony King' Szemere</p>      

  <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

      ultrices posuere cubilia Curae; Praesent hendrerit iaculis      

      arcu.</p>      

  <p><a href="">Full Story</a></p>      

</li>      

Example 8.52\. styles.css (excerpt)      

#content .inner h3 a:link, #content .inner h3 a:visited {      

  color: #245185;      

}
```

最后，让我们将页面的段落文本设置为深灰色，并将字体大小减小到 90%:

```
Example 8.53\. styles.css (excerpt)      

#content .inner p {      

  color: #666666;      

  font-size: 90%;      

}
```

***作者图片***

我们希望在每个专题文章列表旁边显示作者的图像。将图像添加到每个功能项目的标题后面，如下所示:

```
Example 8.54\. styles.css (excerpt)      

<li>      

  <h3><a href="">Head for the Hills: Is Altitude Training the      

      Answer?</a></h3>      

  <img src="img/lachlan.jpg" alt="Lachlan Donald" height="48"      

      width="35" />      

  <p>Lachlan 'Super Toe' Donald</p>      

  <p>Vestibulum ante ipsum primis in faucibus orci luctus et      

      ultrices posuere cubilia Curae; Praesent hendrerit iaculis      

      arcu.</p>      

  <p class="more"><a href="">Full Story</a></p>      

</li>
```

这个标记产生了如图 8.30 所示的显示。

![1523_fig30](img/513be74f8dd08f0517356af4d8ada57b.png)
图 8.30。在文档中显示作者图像

让我们使用`float: left`声明将这些作者照片移动到段落文本的左侧。注意，我们不需要在这里包含图像的宽度，因为每个`img`已经定义了一个`width`。

```
Example 8.55\. styles.css (excerpt)      

#content .inner .features li img {      

  float: left;      

  margin: 0 5px 5px 0;      

}
```

这里，我们使用了一个选择器，它只处理那些在具有`class="features"`属性的 li 元素中的图像。这样，我们可以避免影响可能添加到您的内容中的任何其他图像。

我们将图像设置为向左浮动，并增加了一个空白，这样文本就不会紧挨着图像，它有一个小小的呼吸空间，如图 8.31 所示。

![1523_fig31](img/7148f0bfe5be2ae5689e1ae4057ac99e.png)
图 8.31。浮动作者图像

在我们的布局图形中，作者姓名以粗体显示，所以让我们给作者姓名周围的段落一个值为 author 的类属性，并使用 CSS 规则将其样式化为粗体。我们没有使用任何`<strong>`或`<b>`标签，因为我们设计作者姓名的样式纯粹是出于审美原因，而不是出于任何结构目的。通过将作者姓名样式排除在页面标记之外，我们坚持了将内容与表现分离的目标。而且，由于我们使用的是 CSS，如果我们想在将来改变作者姓名的显示方式，我们可以简单地编辑适当类的规则，而不是找到显示作者姓名的每个页面并在那里进行更改。下面是我们需要对页面标记进行的更改，然后是 CSS 规则，该规则将使所有适当标记的作者姓名变为粗体:

```
Example 8.56\. index.html (excerpt)      

<img src="img/lachlan.jpg" alt="Lachlan Donald" height="48"      

    width="35" />      

<p class="author">Lachlan 'Super Toe' Donald</p>      

<p>Vestibulum ante ipsum primis in faucibus orci luctus et ultrices      

    posuere cubilia Curae; Praesent hendrerit iaculis arcu.</p>      

Example 8.57\. styles.css (excerpt)      

#content .inner p.author {      

  font-weight:  bold;      

}
```

我们需要为这一部分设计的最后一个页面元素是出现在每个功能下面的“完整故事”链接。将一个类`more`添加到每个链接的开始`<p>`标签，然后将以下规则添加到您的样式表中:

```
Example 8.58\. styles.css (excerpt)      

#content .inner p.more{      

  margin-top: 0;      

  text-align: right;      

}      

#content .inner p.more a:link, #content .inner p.more a:visited {      

  color: black;      

  background-image: url(img/more-bullet.gif);      

  background-repeat: no-repeat;      

  background-position: center left;      

  padding-left: 14px;      

  font-size: 90%;      

  color: #1e4c82;      

}
```

我相信你已经注意到了，这种风格非常类似于页面顶部专题文章部分的“阅读更多”链接。

您的布局现在应该看起来很像原来的布局图形。我们的进度如图 8.32 所示。该页面非常接近完成:我们只剩下侧边栏的风格！

![1523_fig32](img/99c0b79bbb7e5c851a6e91afc9cca408.png)
图 8.32。在样式化主要内容区域后显示页面

***工具条***

![1523_fig33](img/c63f237ab98636bf0e75208c61c4dd45.png)
图 8.33。无样式的侧边栏

如图 8.33 所示，侧边栏的内容在主内容区之下日渐式微。没有对它应用任何规则，所以它只是停留在文档中的自然位置。

我们的第一项工作是将侧边栏从这个位置移动到我们在内容区域右侧为它创建的空间。

首先，让我们看看如果我们从顶部和右侧使用绝对定位来定位侧边栏会发生什么。将以下规则添加到样式表中:

```
Example 8.59\. styles.css (excerpt)       

#sidebar {       

  position: absolute;       

  top: 0;       

  right: 0;       

  width: 220px;       

  background-color: #256290;       

  color: white;       

  margin: 0;       

  padding: 0;       

}
```

在浏览器中查看您的页面。侧边栏固定在视窗的右上角，如图 8.34 所示。

![1523_fig34](img/be64d0ce209c444e8ccb7a2c0505526c.png)
图 8.34。将侧边栏定位在顶部和右侧

当我们在前面讨论绝对和相对定位时，我解释了一个元素总是相对于它的父元素的位置定位，并且这个概念被描述为一个元素的定位上下文。在这种情况下，`#sidebar`没有定位的父元素，所以它将视口作为其定位上下文。

然而，我们确实有一个可以定位的元素为我们提供了一个有用的定位上下文——带有`id="main"`的`div`。

在样式表中找到`#main`,并添加以下声明:

```
Example 8.60\. styles.css (excerpt)       

#main {       

  position: relative;       

  top:0;       

  left:  0;       

  width: 100%;       

  margin-top: 10px;       

}
```

侧边栏现在将`#main`作为其父项，因此它位于由`div`和`id`定义的区域内，如图 8.35 所示。

![1523_fig35](img/27e61874d61510c5af13ca6d048f1c48.png)
图 8.35。将侧边栏定位到相对定位的容器的顶部和右侧

侧边栏就位后，我们可以开始设计它的内容了。首先，我们将设计侧边栏不同部分的标题样式:

```
Example 8.61\. styles.css (excerpt)       

#sidebar h3 {       

  font-size: 110%;       

  background-image: url(img/sidebar-header-bg.jpg);       

  background-repeat: no-repeat;       

  margin: 0;       

  padding: 0.2em 0 0.2em 10px;       

  font-weight: normal;       

}
```

这里，我们在标题后面显示了一个背景图像，以创建我们在 design comp 中看到的渐变效果。

*背景好看*

在标题后面使用背景图片是让标题更有吸引力的一个好方法，而不需要在实际的标题文本中使用图片。使用图像来显示标题会使您的站点更难维护，因为每当您想做一些微小的更改时，都需要操作这些图像。

让我们仔细看看侧边栏中每个标题下面显示的内容部分。我们需要添加一个带有类`inner`的`div`到其中的每一个，以便创建一个小空间，将文本内容从边界移开。将这个`div`添加到三个部分中的每一个，如下所示:

```
Example 8.62\. index.html (excerpt)       

<div id="sidebar">       

  <div class="inner">       

    <h3>Site Search</h3>       

    <form method="post" action="" id="searchform">       

      <div>       

        <label for="keywords">Keywords</label>:       

        <input type="text" name="keywords" id="keywords" />       

      </div>       

      <div>       

        <input type="submit" name="btnSearch" id="btnSearch" />       

      </div>        

    </form>       

  </div>       

  <div class="inner">       

    <h3>Coming Events</h3>       

    <ul>       

      <li>10 Apr 06 -<br /><a href="">Seattle Zone       

          Qualifier</a></li>       

      <li>13 Apr 06 -<br /><a href="">World Cup - Round 8</a></li>       

      <li>21 Apr 06 -<br /><a href="">FootbagOOM 05 - NY</a></li>       

      <li>28 Apr 06 -<br /><a href="">WFPA AGM - Hong       

          Kong</a></li>       

      <li>3 May 06 -<br /><a href="">World Cup - Round 9</a></li>       

    </ul>       

  </div>       

  <div class="inner">       

    <h3>Move of the Month</h3>       

    <h4>The Outer Stall</h4>       

    <p>Eti bibendum mauris nec nulla. Nullam cursus ullamcorper       

        quam. Sed cursus vestibulum leo.</p>       

    <p><a href="">more</a></p>       

  </div>       

</div> <!-- sidebar -->
```

现在，让我们给`inner`添加 10 个像素的填充:

```
Example 8.63\. styles.css (excerpt)       

#sidebar .inner {       

  padding: 10px;       

}
```

![1523_fig36](img/769c551d02bba4c713db5a04d1dabc42.png)
图 8.36。样式化标题和内部类后的显示

如图 8.36 所示，侧边栏开始成形。现在，让我们处理事件列表。

```
Example 8.64\. styles.css (excerpt)       

#sidebar ul {       

  list-style-image: url(img/more-bullet.gif);       

  margin-left: 0;       

  padding-left: 20px;       

}
```

在上面的标记中，我们使用`more-bullet.gif`图像作为列表项目符号，删除空白，并添加 20 个像素的左填充，以便显示与标题一致的列表。

```
Example 8.65\. styles.css (excerpt)       

#sidebar p, #sidebar li {       

  font-size: 90%;       

  line-height: 1.4em;       

}
```

接下来，我们将段落和列表项文本的字体大小缩小到 90%。在`line-height`属性的帮助下，我们还在两行之间增加了一点间距。

```
Example 8.66\. styles.css (excerpt)       

#sidebar ul a:link, #sidebar ul a:visited {       

  color:  white;       

}
```

侧边栏中的链接是白色的，并在模型中加了下划线，所以我们用上面的规则将它们设置为白色。

最后，让我们将事件列表中的所有日期都加粗。将带有`class="date"`的`span`添加到列表中的每个日期，然后使用选择器`#sidebar .date`对它们进行样式化，如下所示:

```
Example 8.67\. index.html (excerpt)       

<ul>       

  <li><span class="date">10 Apr 06</span> -<br />       

      <a href="">Seattle Zone Qualifier</a></li>       

  <li><span class="date">13 Apr 06</span> -<br /><a href="">World       

      Cup - Round 8</a></li>       

  <li><span class="date">21 Apr 06</span> -<br />       

      <a href="">FootbagOOM 05 - NY</a></li>       

  <li><span class="date">28 Apr 06</span> -<br /><a href="">WFPA       

      AGM - Hong Kong</a></li>       

  <li><span class="date">3 May 06</span> -<br /><a href="">World       

      Cup - Round 9</a></li>       

</ul>       

Example 8.68\. styles.css (excerpt)       

#sidebar .date {       

  font-weight: bold;       

}
```

侧边栏中的事件现在显示如图 8.37 所示。

![1523_fig37](img/bcb2efa104f31348699d55c209ccf21a.png)
图 8.37。在边栏中显示样式化的事件

*表单*

是时候为侧边栏顶部的搜索表单创建一些规则了。将`class="text"`添加到`input type="text"/#ce#/ element, then create a rule for `#searchform .text`中，使文本框具有 196 像素的宽度和边框。下面是标记:`

```
Example 8.69\. styles.css (excerpt)       

#searchform .text {       

  width: 196px;       

  border: 1px solid #45bac0;       

}
```

将`searchbutton`类应用于提交按钮周围的`div`，并为其添加一个规则到`styles.css`，将`text-align`设置为`right`，并添加一个上边距，这样按钮就不会碰到文本框。

```
Example 8.70\. styles.css (excerpt)       

#searchform .searchbutton {       

  text-align: right;       

  margin-top: 4px;       

}
```

最后，让我们来设计按钮本身的样式，给它一个与文本字段相同颜色的边框，一个与侧边栏背景的蓝色相匹配的背景色，以及一个白色的文本颜色，如下面的规则中所定义的。您还需要向 input 元素添加一个值为`btn`的`class`属性。您的工作结果应该如图 8.38 所示。

```
Example 8.71\. styles.css (excerpt)       

#searchform .btn {       

  border: 1px solid #45bac0;       

  background-color: #256290;       

  color:  white;       

}
```

![1523_fig38](img/5fb6ffc35137b0b9fdcb6574fb9ba8e2.png)
图 8.38。显示样式化的站点搜索

*每月一次*

我们需要考虑的侧边栏的最后一个元素是底部月份部分的移动。此部分包括一幅图像；我们需要首先将它添加到文档中。将其插入到`h4`下面，并给它一个类`motm-image`:

```
Example 8.72\. index.html (excerpt)        

<h3>Move of the Month</h3>        

<h4>The Outer Stall</h4>        

<img src="img/sidebar-player.gif"        

    alt="player demonstrating the outer stall move" height="110"        

    width="60" class="motm-image" />        

<p>Eti bibendum mauris nec nulla. Nullam cursus ullamcorper quam.        

    Sed cursus vestibulum leo.</p>        

<p><a href="">more</a></p>
```

让我们将此图像向右浮动，以便在图像的一侧显示文本:

```
Example 8.73\. styles.css (excerpt)        

#sidebar .motm-image {        

  float: right;        

  margin: 0 30px 0 20px;        

}
```

正如你所看到的，我们还为图片添加了左右边距。我们需要做的最后一件事是格式化“更多”链接，这与布局其余部分中的“阅读更多”和“完整故事”链接非常相似。然而，与那些链接不同，这个链接通常会出现在浮动图像的旁边。我们希望确保它不会出现在图片旁边:我们希望它总是显示在下方。因此，正如您在下面的标记中看到的，我们使用了`clear: right`声明来确保图像右侧没有浮动元素。我们还需要将`more`类添加到包含链接的段落中:

```
Example 8.74\. styles.css (excerpt)        

#sidebar p.more {        

  clear: right;        

  margin: 0 30px 0 0;        

  text-align: right;        

}
```

我们将在下一章更详细地讨论`clear`。现在，注意它可以接受`left`(清除一个左浮动)、`right`(清除一个右浮动)和`both`(清除左右浮动)的值。如果你在布局中使用浮动元素，你会发现这是一个有用的属性。

下面的最终规则，你应该从其他“阅读更多”和“完整故事”链接中熟悉:

```
Example 8.75\. styles.css (excerpt)        

#sidebar p.more a:link, #sidebar p.more a:visited {        

  color: white;        

  background-image: url(img/more-bullet.gif);        

  background-repeat: no-repeat;        

  background-position: center left;        

  padding-left: 14px;        

}
```

这个标记完成了您的两列布局！完成后的页面显示如图 8.39 所示。

![1523_fig39](img/cfa1ca772e3499bded3b8c79a61f7452.png)
图 8.39。完整的两栏布局

*重新定位侧边栏*

当我们决定尝试时，我们真的可以开始欣赏 CSS 布局的灵活性！例如，假设我们想看看如果我们把侧边栏放在左边，而不是右边，这个布局会是什么样子。为此，您只需在 CSS 中做两处修改。

首先，找到`#content`规则并更改`margin`的值:给它 240 像素的左边距，而不是 240 像素的右边距。然后，将右边距设置为`0`:

```
#content {        

  margin: 0 240px 0 0;        

  border: 1px solid #b9d2e3;        

  background-color: white;        

  color: black;        

}
```

现在，找到`#sidebar`规则，将定位声明`right: 0`改为`left: 0`:

```
Example 8.76\. styles.css (excerpt)        

#sidebar {        

  position: absolute;        

  top: 0;        

  left: 0;        

  width: 220px;        

  background-color: #256290;        

  color: white;        

  margin: 0;        

  padding: 0;        

}
```

保存样式表并在浏览器中刷新页面。侧边栏现在将出现在内容的左侧，如图 8.40 所示。

![1523_fig40](img/6a130911cc7808f171c864c2cd16e68e.png)
图 8.40。重新定位侧边栏

##### 摘要

这一章我们已经讲了很多了！我们从一个无样式的 XHTML 文档开始，在学习了一点使用 CSS 进行布局的理论之后——特别是绝对和相对定位、边距、填充和边框——我们开始使用绝对定位的侧栏创建一个两列布局。

您现在有了一个使用 CSS 定位的完整页面布局；这是我们每天在网上看到的许多网站使用的基本布局。不过，这种布局方法确实有其局限性——我们将在下一章中发现这些局限性，并讨论一些替代布局。但是，如果你需要两栏布局，这种结构是健壮的，可以作为无数吸引人的站点设计的基础。

这就是摘自 [*HTML 乌托邦:使用 CSS*](https://www.sitepoint.com/books/css2/) 设计无表格，但是还有很多要学的！本书前面的章节介绍了 CSS 的基础知识，从探索 CSS 规则的各个部分，到使用选择器、注释、表达式度量等等，再到控制颜色、字体和文本效果等方面。CSS 标记的验证也包括在内。

在本书的后面，您将开始开发三列布局、固定宽度布局、“缩放”布局等等。本书的附录包括 CSS 颜色参考、CSS 属性参考和 CSS 杂集(如 At-rules ),旨在帮助您使用本书中介绍的技术开发站点。关于这个标题的更多内容，请参见该书的第页，别忘了:[这一章可以下载 PDF 格式](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。

## 分享这篇文章