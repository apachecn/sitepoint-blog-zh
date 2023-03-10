# 实用网页设计-解释框架和框架的使用

> 原文：<https://www.sitepoint.com/frames-frame-usage-explained/>

**啊，镜框。当网景公司在 1995 年左右首次提供它们时，我们讨厌它们；当它们在短短的几年里风靡一时时，我们为它们感到惋惜；我们希望它们会走开，永远不再让我们的展示变暗。嗯，也许吧。**

关于框架使用的争论已经持续了八年，虽然大多数专家坚决反对在大多数情况下使用框架，但我不会在这里继续争论。我会说，以我在 Web 上的经验，90%还在使用框架的站点都没有很好地使用框架；当我发现一个网站有框架比没有框架更好的时候，我还是有点惊讶。

大多数网页设计起步较晚或刚刚开始学习的人都会被告知，“框架？忘了他们吧！你不会用框架来设计网页，它们很难做好，你学习它们是浪费时间。“现在，这种说法有很多道理。学习框架设计并不是必须的:许多专业的网页设计师已经设计了数百个没有框架的优雅、功能性的网站，他们会继续设计而不考虑框架。框架网站的比例正在稳步下降，尤其是现在越来越多的设计师转向基于 CSS 的设计，这实质上使得框架的整个概念变得无关紧要。请注意，虽然 HTML 4 包含框架作为官方代码的一部分，但 HTML 4.01 Strict 不支持框架，而是依赖于样式表。

关于框架的利弊，请看汤姆·查普林在 SitePoint 上发表的文章。这是几年前的事了，但仍然切题。

然而，有时候框架设计的知识是必要的。也许你的商业网站使用框架，你被限制在公司的范围内运作。也许你有一个客户，或者一个阿姨，她非常喜欢框架，坚持要你使用框架式设计。或者你已经想出了一个在框架中更好工作的设计概念(这听起来不太可能，但确实发生了)。

框架有其用途，尽管不常见。所以我决定为新手或中级网页设计师提供一些关于框架设计的信息——一些操作方法，以及一些观察、警告和警告。框架技术没有那么难，虽然它很棘手，如果没有其他原因，你会知道所有这些大惊小怪的是什么，这是值得学习的。

##### 基本设计

框架打乱了网络的整个基本概念:一大堆用超文本链接在一起的独立页面。…框架违反了太多公认的 Web 标准，不适合作为一个有价值的信息传递系统。—罗斯·香农

*帧:说不就行了——雅各布·尼尔森*

很难反驳那些说法。框架的确会扰乱网络的流动。理想情况下，这个想法是不受阻碍地从一个站点到另一个站点，从一个页面到另一个页面，从一个位置导航到另一个位置没有任何问题。点，点击，细读内容，点，点击，去别的地方。

该网本质上是固有的流体；框架通过给网页添加静态结构来打破这种模式。当你进入一个有框架的页面时，这种流动的模式就改变了。内容显示在框架的“围栏”内，被锁在代码的路障内。导航中断；通常的“点击进入，点击退出”方法不再奏效。在一些较老的浏览器中，如 Netscape 2，甚至后退按钮也不总是能让你跳出框架页面。令人欣慰的是，这个小问题不再是一个因素，因为不能很好地处理框架的浏览器已经从当前使用的浏览器中消失了，只有 Lynx 是一个明显的例外，它显示无框架页面的内容，并提供带标签的链接到框架内容。

书签并不总是有效；您没有标记框架内容的特定 URL，而是将框架集加入了书签，该框架集可能具有不同的 URL，并且在您返回时可能不包含相同的内容。复制框架的 URL 作为另一个页面中的超链接可能不会将访问者带到正确的页面，因为框架页面的 URL 不会显示出来；仅显示框架集 URL。打印带框架的页面并不总是很好。Cookies 不能很好地跟踪框架集页面。框架内容在笔记本和手持设备等较小的屏幕上显示效果很差。

框架最大的麻烦可能是偶尔出现的框架页面，一旦你进入，它就不会让你出去；它坚持(不管是因为糟糕的设计还是意图)在它自己的框架集中显示你去的每一个其他页面。最后一种方法对于一些商业网站来说是一种技术上可行的选择，这些商业网站希望将自己的导航设置或广告内容始终呈现在访问者面前，但这并不是一个好的选择。把访问者困在你网站的框架内是促使他们尽快离开你网站的最好方法——即使这包括完全关闭浏览器，从头开始浏览网页。

还需要考虑额外的服务器负载和页面维护。框架网页通常比非框架网页占用更多的服务器空间，而且这些框架也会增加网页的加载时间。修改一堆框架页面会更加困难和耗时。当您创建一个新的框架集时，您将在一次访问中至少有两次(只有一个框架—如果您有更多的框架，则更多)对服务器的点击。有了更多的框架集，您就可以在不产生额外的页面视图的情况下产生更多的服务器点击率。如果你能接受有限的网站流量，这可能会引起问题；它也可以破坏你跟踪网站访问的尝试。

现在，在某些情况下，框架是一种合理的解决方案。一种是将一个长文档分成几章或几块。一个简单可行的解决方案是构建一个有两列的框架集，左侧提供导航链接，而右侧显示内容的实际章节。单击左侧框架中的链接会导致特定章节显示在右侧框架中。这就避免了一个非常长的文档需要花费太多的时间来显示，并且以易于管理的块来显示内容。使用框架集是解决这些内容的唯一方法吗？当然不是，但这是一个可行的方法。

用于外包购物车的框架集是另一种有用的可能性。如果你为你的横幅或徽标、导航和内容使用单独的框架，并将购物车合并到内容页面中，即使外包的购物车没有使用你的徽标或导航材料，你的网站仍然工作良好，具有适当的外观和感觉。

关键是，框架被认为是 Web 设计的 bugbears，不是因为它们毫无用处，而是因为它们在过去被如此可怕地滥用(现在如此，将来也可能如此)。请记住，框架背后的基本思想是保持某些信息永久可见，同时查看其他可能发生变化的信息。当这种方式运行良好时，它允许浏览者在访问站点的不同区域时，保持一个导航窗口、一个目录、一个广告或一个标题栏(或这些元素的组合)不动。对于商业网站来说，这有助于将公司的徽标、导航或广告始终呈现在浏览者面前。对于非商业网站，这可能是最有用的保持导航或其他信息总是方便的，而不是在每个页面上放置一个菜单(顺便说一下，大多数设计师的选择)。

在最坏的情况下，你可能会被锁进“框架地狱”，在那里，设计糟糕或故意限制的框架集将你锁进一个没有出路的站点。后退按钮不像它们应该的那样工作，浏览网站本身是一场噩梦，框架在另一个框架中复制自己，甚至在地址栏中键入一个全新的 URL 也会在旧框架中打开新网站。这很烦人。

如果你要使用框架集，确保你的内容适合框架显示。还有别的展示方式吗？想想你的观众。尽管现在几乎每个人都使用支持框架的浏览器，但有些人不喜欢框架，当框架网站出现在他们的屏幕上时，他们会像被烫伤的猫一样逃跑。对这些人来说，镜框就像超人的氪石。你和他们争论不会有什么结果；你最好的办法是给他们一个选择，或者从一开始就设计一个没有框架的网站。

假设你想用框架来建立一个站点，不管是出于什么原因。好吧，你是怎么做的？

注意:我通常提倡在你自己的 HTML 代码编辑器中进行代码测试。您也应该在这里这样做，但是您必须构建几个 HTML 文件作为框架源文件，否则您将看不到任何东西。最简单的方法可能是创建几个简单的 HTML 文件，没有`<body>`内容，有不同的`<body> bgcolor`属性，这样就会显示一些内容。

要使用下面的示例代码，请尝试构建以下页面:您的 index.html 页面(将成为您的框架集页面)和六个内容页面:

*   leftside.html，
*   mainpage.html,
*   rightside.html，
*   title.html,
*   navigation.html，还有
*   moreinfo.html。

这些页面都不需要任何文本或图形内容，除非您真的想在其中放置一些内容。给它们不同的背景颜色，这样它们就能在你的屏幕上区分开来。

##### 基本框架结构

首先，你必须使用正确的框架文档类型。对于 HTML 4 文档，它是:

```
<DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 FRAMESET//EN" "http://www.w3c.org/TR/html4/frameset.dtd"> 
```

这是 HTML 4 过渡文档类型的变体。

对于 XHTML 1.0 文档，doctype 是:

```
<DOCTYPE HTML PUBLIC "-//W3C//DTD XHTML 1.0 FRAMESET//EN" "http://www.w3c.org/TR/xhtml1/DTD/xhtml1-frameset.dtd"> 

请记住，由于早期版本的 HTML 不正式支持框架，如果出于某种原因，您还没有离开 Wayback 机器，并且仍然在编写要在 HTML 3.2 或更早版本中查看的页面，您不需要特定的 doctype 来编写框架页面。大多数现代浏览器都会支持框架和它们的内容，但是不要期望 100%的兼容性。

任何一组框架网页的起点都是`<frame>`标签，它通常使用`src`属性指向一个特定的 HTML 文档。

```
<frame src="mainpage.html">
```

标签定义了单个框架页面的内容。上面的例子假设您希望在框架中显示一个名为 mainpage.html 的文件。标签唯一需要的属性是 src 属性，它明确地告诉浏览器在框架中显示什么文件。到目前为止一切顺利。

您还可以使用 scrolling 属性控制框架内滚动条的外观:

```
<frame src="mainpage.html" scrolling="yes">
```

该属性的默认值是 auto，它让浏览器决定是否显示滚动条。另外两个值是 yes 和 no，这是不言自明的。

没有结束的`</frame>`标签。

您不应该在 mainpage.html 文件中包含任何`<head>`标签信息，因为该文件代表一个更大的文档。该页面上的所有内容都应该包含在`<body>`标签中。

现在您需要构建您的框架集页面。这个页面，出于显而易见的原因，通常被称为 index.html，它包含了整个网站的标题信息以及构成网站框架设计的`<frameset>`标签。这里有一个基本的例子，省去必要的标签，如`DOCTYPE` s 和`<meta>`:

```
<html>   

<head>   

<title>Frames Document</title>   

</head>   

<frameset rows="50%,50%">   

***add framed pages here using the <frame> tags***   

</frameset>   

</html>
```

注意，这个文档中没有任何`<body>`标签。`<frameset>`标签取代了它们的位置(将`BODY`代码放在`<frameset>`标签之前会导致框架集被忽略)。`<frameset>`标签有一个必要的属性，`ROWS`或`COL` (umn) `S`。上面的例子使用百分比将可用的显示空间分成两个相等的水平区域。`COLS`属性将做同样的事情，但是将可用空间分成两个相等的垂直区域:

```
<frameset cols="50%,50%">
```

很多网页设计者喜欢将他们的网页分成三个垂直的栏，两个外侧的框架比中间的栏窄，这将包含信息的主体(这种布局模仿了传统的网站设计的三栏风格)。如果您想这样做，请尝试以下方式:

```
<frameset cols="100,*,100">
```

其中左边和右边的列是 100 像素宽，较大的中心占据显示空间的其余部分。一如既往，我建议尽可能坚持百分比宽度(或高度)，因为人们使用各种各样的显示尺寸。注意，上面代码中间的星号仅仅表示“显示的其余部分”如果你这样写:

```
<frameset cols="100,500,100">
```

你将有一个 700 像素的页面宽度，这将为我们有更大显示器的朋友留下大量未使用的显示空间。您还可以用相对术语来组织显示，相对术语指定框架相对于其他框架的大小。此示例显示了两列，其中右边的一列的宽度是左边的两倍:

```
<frameset cols=*,2*">
```

`*`只是一个占位符。上面的代码告诉显示器，“将显示器切割成两列，并使右边的大小是左边的两倍。”

如果没有设置`ROWS`属性，列将扩展到页面的整个长度。如果未设置 COLS 属性，行将扩展到页面的整个宽度。如果两个属性都没有设置，那么框架就占据了浏览器显示的确切区域——那有什么用呢？(也许没有 COLS 或 ROWS 属性的框架集是有用的，但是我一时想不出来。如果可以的话，把它贴在论坛上。)

您可以混合绝对、相对和百分比测量，但要仔细检查它们的显示方式。事实上，您应该仔细检查所有的列和行尺寸，看看它们是如何显示的，并且不要忘记在不同的显示尺寸和不同的浏览器中检查布局。这里有三个混合测量的例子，抄袭自 HTML 框架上的 [W3C 页面:](http://www.w3c.org/TR/REC-html40/present/frames.html)

```
<frameset cols="1*,250,3*>   

***yadayada***   

</frameset>
```

这个例子创建了三列:中间的空间固定为 250 像素，适用于已知大小的内容块，如图形或 Flash 窗口，两边各有两列；左侧列获得剩余空间的 25%，右侧列获得剩余空间的 75%。

```
<frameset rows="30%,70%" cols="33%,34%,33%>   

***yadayada***   

</frameset>
```

这个例子给你一个 2 乘 3 的子空间网格。

```
<frameset rows="30%,400,*,2*">   

***yadayada***   

</frameset>
```

这里，您有一个水平划分为四个独立区域的浏览器显示。第一个获得 30%的显示空间。第二个正好得到 400 个像素。另外两个人平分剩下的草皮，第四个区域得到的面积是第三个区域的两倍。如果你的显示器正好是 1000 像素高，这是不可能的，第一行将得到 300 像素，第二行 400 像素，第三行 100 像素，第四行 200 像素。

现在，让我们使用上面的示例代码和其他一些代码，制作一个示例框架集页面:

```
<html>   

<head>   

<title>Frames Document</title>   

</head>   

<frameset cols="100,*,100">   

   <frame src="leftside.html">   

   <frame src="mainpage.html">   

   <frame src="rightside.html">   

</frameset>   

</html>
```

这就产生了三列显示，两边的列限制为 100 像素，中间的列用于显示主要内容。请记住，在每一列中，如果您的内容超出了可用的宽度，您会得到水平滚动条。没有人喜欢水平滚动条。同样，调整列宽，这样可以尽可能避免滚动条。在框架集中使用行会产生垂直滚动条，这更容易被接受。

请记住，只需将光标移到边框上，等到它变成双箭头，然后将边框拖动到您想要的位置，就可以调整框架的大小。文本将使自己适应变化；图形和其他固定大小的内容不会出现，滚动条会出现。不想让你的访客改变你的框架？使用 noresize 属性(它没有值)。

```
<noframes>
```

总是包含一个带有`<noframes>`标签的部分，供没有框架兼容浏览器的人使用(仍然有一些)。使用这种浏览器的人只能看到标签中显示的内容；我们其他人根本看不到它，因为我们兼容框架的浏览器不会显示它:

```
<noframes>Your browser won't handle frames.  <a href="noframes.html">Go here for a better view!</a></noframes>
```

当然，这个 noframes.html 文件应该包括框架集中包含的所有内容，在设计中使用—您猜对了—没有框架。两个网站合二为一…嗯，这里有一个关于无框架设计的论点…

将`<noframes>`代码放在结束`</frameset>`标签的正上方。

屏幕阅读器和音频浏览器在处理框架页面时有很大的困难。这就是使用`<noframes>`页面的唯一理由，即使每个访问你页面的人都在使用最新的浏览器技术。一些屏幕阅读器可以处理简单的框架集，所以这个警告慢慢变得无关紧要，但是有很多人在使用根本不能处理框架的旧屏幕阅读器。最大的可访问性是必须的，而不是可以随便考虑的选项。

##### 更棘手的事情

对于更复杂的布局，您可以将一个框架集“嵌套”在另一个框架集中。从简单的事情开始:

```
<frameset cols=*,2*>    

   <frame src="navigation.html">    

   <frame src="mainpage.html">    

</frameset>
```

这里我们有两列，右边的是左边的两倍大。根据文件名，您可以推断出我们将使用左边的栏进行导航，而右边较大的栏用于显示内容。现在，让我们来处理它:

```
<frameset rows="20%,*">    

   <frame src="title.html">    

      <frameset cols="*,2*>    

         <frame src="navigation.html">    

         <frame src="mainpage.html">    

      <frameset>    

</frameset>
```

这允许我们将行和列组合在一起，创建经典的“报纸”网站设计:一个水平的“报头”用作标题，两列，一个窄的左侧列用于导航，一个宽的右侧列用于显示主要内容。注意，第二个`<frameset>`，带列的那个，完全包含在第一个`<frameset>`的第二行中。

想要一个两列显示器，右边较大的一列分成两行？只需切换上面的行和列属性:

```
<frameset cols="20%,*">    

   <frame src="title.html">    

      <frameset rows="*,2*>    

         <frame src="navigation.html">    

         <frame src="mainpage.html">    

      </frameset>    

</frameset>
```

如果左边的列不够宽，只需将值从 20%更改为 30% —或者任何适合您的值。

 ***边距*** 

HTML 4 提供了两个边距控制命令，marginheight 和 marginwidth。第一个以像素为单位指定框架内容的上下边距之间的间距。第二个对左边距和右边距做同样的操作。没有具体的默认。

***无边框***

 *您可以向您的`<frameset>`标签添加某些属性来消除默认显示的框架边框。不同版本的 Netscape 和 Internet Explorer 使用不同的命令，因此您可能需要下面的所有属性来进行正确的站点设计，尽管下面的代码并不完全是标准的 HTML 4:

```
<frameset cols="*,*" framespacing="0" frameborder="0" border="0">
```

网景和 IE 都使用了`border`属性，但它不是有效的 HTML 4。它的值应该以像素表示边框的宽度。

```
framespacing is an IE-only attribute that, like Netscape's border, lets you define the border's size in pixels.  It is not a valid HTML 4 tag.

```

```
frameborder works with both Netscape and IE.  Before HTML 4, the tag used different values for the two browsers: Netscape's values were yes and no.  IE's were 1 and 0, which correspond to the yes and no of Netscape.  The defaults are yes and 1, which tells the border to draw the border.  HTML 4 standardized the tag to use 1 and 0.

如果你在 HTML 4 中编码，如果你想保持你的框架无边界，使用`<... frameborder="0">`标签。

 ***彩色边框*** 

```

```
bordercolor is a tag that lets you set your border's colors, like so:

```

```
<frameset cols="*,*" bordercolor="#F0F0F0">
```

像前面的标签一样，bordercolor 不是正式的 HTML 4，尽管它可以在 Netscape 和 IE 上使用。

 ***页边距宽度和页边距高度*** 

没有太多人费心去指定这些属性，它们也在`<frameset>`标签中。标签以像素为单位确定边距的高度和宽度。试试这些标签，看看它们是否适合你。

 ***互动画面*** 

框架页面能够相互交互是一个好主意；也就是说，直接超链接到框架页面。为此，您需要使用 name 和 target 属性。

从命名框架内容页面开始:

```
<frameset cols=*,2*>     

   <frame src="navigation.html" name="frame1">     

   <frame src="mainpage.html" name="frame2">     

</frameset>
```

命名后，可以使用 target 属性将超链接指向它们:

```
<a href="moreinfo.html" target="frame2">More content</a>
```

如果你把这个超链接放入 navigation.html 页面，当用户点击它时，mainpage.html 文件将被右边的 moreinfo.html 文件取代。

如果您给出一个没有用名称属性定义的目标，页面将在一个新的浏览器窗口中打开。

但是，如果我们希望二十个不同的页面在主要内容显示中循环，该怎么办呢？我们可以在 navigation.html 写二十个不同的链接，都指向“frame2”，或者我们可以在 navigation.html 页面的`<head>`部分使用一个`<base>`标签:

```
<base target="frame2">
```

这类似于在每个超链接中都有`target="frame2"`属性。

 ***特殊目标命令*** 

有四个特殊的目标命令，它们都以奇妙、神奇、神秘的下划线开头:`_`。他们在这里:

*   `target="_blank"`–使 URL 载入新的浏览器窗口。

*   `target="_top"`–将 URL 定位到整个浏览器窗口，并清除所有先前的框架；主要用于从您自己的框架内容链接到外部网站的情况。

*   `target="_self"`–将 URL 加载到同一窗口(默认；如果您希望发生这种情况，就没有必要在代码中指定它)。

*   `target="_parent"`–将 URL 加载到框架集的父窗口中；除非您使用嵌套框架集，否则该命令的工作方式就像`_top`命令一样。如果您使用的是嵌套框架集，那么这个命令会将 URL 加载到“下一步”的框架中，即父窗口。

##### 一些其他命令

```
marginheight and marginwidth define the amount of space, in pixels, that falls between the margins and the content.

`name`属性用于定位框架。

在适用的情况下，`scrolling`属性决定滚动条的条件。默认值为`on`、`off`和`auto`。除非您特别需要打开或关闭滚动条，否则最好不要在代码中使用该属性，显示器将选择是否需要滚动条。请注意，`scrolling="no"`和`noresize`命令如果两者结合使用的话，会让使用较小显示器的用户感到棘手。如果你一起使用它们，在 640x480 的显示器上测试你的站点，看看它看起来怎么样。

`title`属性可以包含一个用作 iframe 标题的短语。有些浏览器会在鼠标悬停在元素上时显示这些信息，而有些浏览器会在右键菜单中显示这些信息。如果您想要包含更长的描述(通常用于非可视化浏览器)，请使用`longdesc`标签。它将提供到更长描述的链接。

高级功能

 *** IFrames *** 

IFrames 最初是 Internet Explorer 独有的功能，现在可以在 Internet Explorer 2.x 和更高版本、Netscape 7、Mozilla、Opera 3 和 6 中查看，甚至可以在 WebTV/MSN 浏览器中查看。IFrames 包含在 HTML 4.0 规范和 XHTML 1.0 规范中。IFrames 是“内嵌”或“独立框架”,位于常规 HTML 页面的正文中，可以像图形一样嵌套在页面中，并且不需要使用框架集。它们易于使用，对于侧边栏和其他与内容主体不直接相关的感兴趣的信息非常有用。你用得越多，你就会发现它们的用处越多。当您构建页面并到达您希望 iframe 所在的区域时，只需使用下面的代码(当然是根据您自己的用途修改的):

```

```
<iframe src="sidecontent.html" width="200" height="100" align="right">Inside this tag is content contained within an iframe.  Your browser may not display this material.  You can click <a href="sidecontent.html">here</a> to see it for yourself, and use the BACK button to return to the main page.</iframe>
```

这给了你一个小小的独立框架的 HTML 页面在你的主网页的主体内，填充你喜欢的任何内容。不兼容的浏览器将会看到两个`<iframe>`标签中的内容。尽管宽度和高度属性被认为是可选的，但是它们通常被使用；您可以选择像素(如上例所示)或百分比作为大小选择。

请注意，`align`属性已经被弃用，取而代之的是样式表。如果您选择使用它，您的主要选择是:

*   `bottom`:默认；对象的底部与基线对齐

*   `left`:对象沿左边距对齐，后面的对象排列在右边

*   `middle`:对象的中心与基线对齐

*   `right`:对象沿右边距对齐，后面的对象排列在左边

*   `top`:与基线对齐的对象顶部

您也可以在 iframe 中使用`frameborder`和`scrolling`命令。

CSS 用户可以使用`class`和/或 id 属性根据样式类或样式 ID 定义标签的内容。CSS 用户也可以使用绝对定位、浮动的`div`标签和样式表声明，让 iframe 元素“浮动”在主内容之上。查看[网猴的教程](http://hotwired.lycos.com/webmonkey/96/37/index2a_page3.html)如何完成这个漂亮的小壮举。

 ***层层*** 

层在概念上有点类似于 IFrames，尽管在执行上甚至外观上都不一样。它们完全是 Netscape 特有的元素，从来没有包含在官方的 HTML 规范中。由于它们不受新版 Netscape/Mozilla 的支持，也不是官方 HTML 规范的一部分，并且逐渐变得无关紧要，我在这里就不详细介绍它们的护理和喂养了。

 ***搜索引擎排名*** 

框架给搜索引擎带来了大问题。包括谷歌在内的大多数搜索引擎在读取框架集页面时都有问题。其中一些会自动搜索“无框架”版本，如果没有，搜索引擎通常会满足于只索引“主页”或框架集页面。像旧的浏览器一样，许多搜索引擎忽略了生成框架的指令。只读取 noframes 标签中的信息，这是支持框架的浏览器会忽略的信息。因此，你经常在搜索引擎结果中看到“这个网站使用框架，但是你的浏览器不支持它们”的信息。您可能不希望框架集页面在搜索引擎上被强调；更糟糕的是，你冒着让引擎显示不完整的或者误导性的信息的风险，而不是你想要在引擎列表上显示的精心制作的定位信息。

那么你如何解决这个问题呢？包含在框架集页面中的标签是一个解决方案，但不是一个很好的方案，因为大多数主流搜索引擎已经放弃了对标签的支持。尽管如此，对于仍在使用它们的引擎和内部网来说，它们还是值得添加的。

注意，有一个强有力的论点是`<meta>`标签不再值得花时间去写它们；我不会在这里争论这个问题，但是如果你想阅读更多关于这个问题的内容，可以看看搜索引擎观察的文章，[Meta 标签之死](http://searchenginewatch.com/sereport/article.php/2165061)。唯一还在使用`<meta>`标签的主要搜索引擎是 Inktomi，据他们的产品营销总监说，他们从来没有给标签很大的权重。

更好的方法是在`<noframes>`指令中包含有用的信息。如果您希望文本在页面上尽可能高的位置，请将`<noframes>`信息放在第一个`<frameset>`标签之后。请注意，在某些版本的 Netscape 中，将信息放在第一个 frameset 标记的上方会禁用框架信息。

不要忘记在框架集页面的`<noframes>`标签中使用`<body>`标签。为框架集页面提供标题是有帮助的，即使在正确查看页面时标题不会出现。搜索引擎做索引标题；它们是页面中最重要的元素之一。

至此，您已经创建了一个搜索引擎可以看到并正确索引的页面。很好…目前为止。你还没有做的是给搜索引擎用户一个在你的框架集内的页面；他们有一个超出正常框架约束的页面。这对于像 AltaVista 这样“支持框架”的引擎来说尤其麻烦这些引擎可以链接到你的框架站点中的任何页面，并自己显示它，而不需要框架集的帮助。

如果一个页面没有显示在你设计的框架内，你可以很容易地把直接进入你网站的访问者拒之门外。幸运的是，这个问题有一个简单的答案。只是要确保在每个页面的顶部或底部都有一个“主页”链接，可以返回到主框架集页面。确保使用了`target="_top"`属性，就像这样:

```
<a href="index.html" target="_top">Home Page</a>
```

如果没有这个属性，点击你的“主页”链接的访问者将会看到在主框架内绘制的一组新的框架；不好看，也不太方便用户使用。

另一种解决方案使用 JavaScript 强制重画框架，但我将把它留给学生作为查找和使用的练习。有许多不同种类的 JavaScript 编码来完成这个特殊的任务。通常，使用 JavaScript 处理框架重绘的页面也会阻止访问者正常使用“后退”按钮回到原来的位置。用户必须非常快速地点击两次“后退”按钮，然后前一页才有机会把他们扔回到框架集中，或者使用后退按钮的历史菜单离开框架集。

##### 最终提示

对于那些没有支持框架的浏览器的人来说，在你的框架 HTML 页面中总是包含可替换的导航链接。

总是，总是，总是将`target="_blank"`属性添加到框架页面中指向外部网页的链接中。否则，您的框架可能会将外部站点包围在其框架集内。这种效果(有时是邪恶的网站设计者故意强加给可怜的无辜冲浪者的，最常见的是被商业网站雇佣，他们想让他们的广告框架集一直在你面前)激怒了不希望你的框架集“劫持”他们的内容的网站冲浪者和网站所有者。甚至在你的框架中包含其他网站可能会有法律后果。

通过在每个页面的`<head>`部分包含这个`<meta>`标签，防止其他人的框架吞没你的页面:

```
<meta http-equiv="Window-target" content="top" />
```

给一个带框架的 HTML 页面添加书签非常简单——只需在框架内右键单击，选择“添加到收藏夹”或“添加书签”,这个带框架的页面就会被添加书签。当然，大多数现代版本的 Netscape 和 Internet Explorer 都可以用书签标记有框架的页面，而不需要用鼠标右键点击。

您可以使用`<object>`标签在几个帧之间共享数据。这比我介绍的其他材料稍微复杂一点，所以我将把它留给您来了解如何使用这个命令的更多信息。从 W3C 规范的[开始。](http://www.w3c.org/TR/REC-html40/present/frames.html)

一个与上面的`<object>`标签相关的建议:使用 JavaScript 或 CGI 脚本来帮助管理你的框架网站，极大地提高了你在合理的时间内将所有东西组织在一起的能力，并且让你的访问者对他们的导航和你的网站的使用有更多的控制。因为我既不是 JavaScript 也不是 Perl 代码大师，所以我将让您自己去了解它！你也可以从上面的 W3C 规范开始这项研究，并继续学习 SitePoint 自己的优秀的[框架设计](https://www.sitepoint.com/blog/)教程，该教程来自无与伦比的 Kevin Yank。

想学习如何制作一个既适用于 IE 和 Netscape，也适用于不支持该效果的浏览器的索引页面？About.com 有一个很好的关于如何使用 JavaScript 片段实现这个目标的小教程。

有乐趣框架，一如既往，随时在论坛上发表评论。谁知道呢，你可能真的会在你的站点中发现你以前没有考虑过的框架的用途！

##### 文献学

选择一个文档类型
[http://www.htmlhelp.com/tools/validator/doctype.html](http://www.htmlhelp.com/tools/validator/doctype.html)

元标签之死
[http://searchenginewatch.com/sereport/article.php/2165061](http://searchenginewatch.com/sereport/article.php/2165061)

框架设计——简介
[https://www.sitepoint.com/article/622](https://www.sitepoint.com/blog/)

道格的画框教程
[http://users.snowcrest.net/dougbnt/frametut.html](http://users.snowcrest.net/dougbnt/frametut.html)

定格是野餐
[http://hotwired.lycos.com/webmonkey/96/31/index3a.html](http://hotwired.lycos.com/webmonkey/96/31/index3a.html)

HTML 文档中的框架
[https://www.w3.org/TR/REC-html40/present/frames.html](https://www.w3.org/TR/REC-html40/present/frames.html)

无泪定格
http://www.alistapart.com/stories/frames/frames4.html

帧–为什么和为什么不
[http://webdesign.about.com/library/weekly/aa111097.htm](http://webdesign.about.com/library/weekly/aa111097.htm)

构架网络
[http://webreference.com/dev/frames/i](http://webreference.com/dev/frames/i)

群情激奋超过定格
[http://webclipart.about.com/library/weekly/aa062797.htm](http://webclipart.about.com/library/weekly/aa062797.htm)

HTML 
的历史[http://hotwired.lycos.com/webmonkey/97/17/index0a.html?tw =创作](http://hotwired.lycos.com/webmonkey/97/17/index0a.html?tw=authoring)

如何使用 HTML Meta 标签
[http://www . search engine watch . com/webmasters/article . PHP/2167931](http://www.searchenginewatch.com/webmasters/article.php/2167931)

 <iframe></iframe> 元素——内嵌框架
[http://webdesign.about.com/library/tags/bltags-iframe.htm](http://webdesign.about.com/library/tags/bltags-iframe.htm)

IFrames
[http://webdesign.about.com/cs/frameshelp/a/aaiframe.htm](http://webdesign.about.com/cs/frameshelp/a/aaiframe.htm)

IFrames 的内幕
http://hotwired.lycos.com/webmonkey/96/37/index2a.html

帧的来龙去脉
[http://www . ddj . com/documents/s = 2361/Nam 1011135274/index . html](http://www.ddj.com/documents/s=2361/nam1011135274/index.html)

画面简介
[http://www.jalfrezi.com/frames.htm](http://www.jalfrezi.com/frames.htm)

Lynx 用户指南版本 2 . 8 . 3
[http://Lynx . isc . org/release/Lynx 2-8-3/Lynx _ help/Lynx _ Users _ Guide . html](http://lynx.isc.org/release/lynx2-8-3/lynx_help/Lynx_users_guide.html)

搜索引擎和框架
[http://www . search engine watch . com/webmasters/article . PHP/2167901](http://www.searchenginewatch.com/webmasters/article.php/2167901)

使用帧
[http://www.evolt.org/article/rating/22/293/index.html](http://www.evolt.org/article/rating/22/293/index.html)的一些注意事项

框还是不框？
[https://www.sitepoint.com/article/122](https://www.sitepoint.com/blog/)

三年后重访十大错误
[http://useit.com/alertbox/990502.html](http://useit.com/alertbox/990502.html)

Web 开发者基础:第 5 章:XHTML 框架
[http://webdevfoundations.net/chapter5/l](http://webdevfoundations.net/chapter5/l)

人人网框
[http://webdesign.about.com/library/weekly/aa110199.htm](http://webdesign.about.com/library/weekly/aa110199.htm)

网站管理员
[http://www.ehsco.com/opinion/19980209.html](http://www.ehsco.com/opinion/19980209.html)

为什么帧吸(大部分时间)
[http://www.useit.com/alertbox/9612.html](http://www.useit.com/alertbox/9612.html)

为音频浏览器编写 HTML 框架
[http://web design . about . com/cs/Frames help/a/aaaccessframes . htm](http://webdesign.about.com/cs/frameshelp/a/aaaccessframes.htm)

## 分享这篇文章* 
```