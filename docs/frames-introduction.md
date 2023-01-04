# 框架设计——简介

> 原文：<https://www.sitepoint.com/frames-introduction/>

框架是 HTML 的一个特性，允许你作为作者在用户的浏览器中控制网站的布局。具体来说，框架允许您将浏览器窗口分成矩形部分，可以将它们视为独立的浏览器窗口。它们可以滚动和调整大小，并加载不同的网页。这些部分被称为框架。

尽管 W3C 直到当前的 HTML 4.0 才承认框架是任何 HTML 规范的正式方面，但从早期版本开始，两种主流浏览器都支持框架(Netscape Navigator 从版本 2.0 开始，Microsoft Internet Explorer 从版本 3.0 开始)。这一事实帮助他们成为最常用的(和误用！)美化网站设计的“窍门”。

如果使用得当，框架可以增加网站的结构和易用性。如果使用不当，框架会使网站看起来拥挤、杂乱，有时在某些硬件设置上完全无法使用。它们最常见的用法是在窗口的左侧创建一个非滚动菜单栏，这通常是一个好主意；但是，如果您试图将太多的信息塞进菜单栏，一些显示器尺寸较小的用户将无法访问菜单底部的项目。像这样被误导的框架布局在大多数经验丰富的网页设计者最讨厌的列表中赢得了一个特殊的位置。因此，重要的是你不仅要了解框架式布局的优点，还要了解它们的缺点。

出于本文的目的，假设您对 HTML 有基本的了解。JavaScript 及其原理的一些知识在最后一节也会有所帮助，但不是必需的。如果你想阅读这两种语言中的任何一种，我推荐[初学者 HTML](http://www.webmasterbase.com/article/453) 系列和 [Javascript 101](http://www.webmasterbase.com/article/452) 教程。

##### 基本框架

在这一节中，我们将创建一组非常基本的帧。然后，我们将修改它们的一些属性，使它们按照我们希望的方式工作。

***我们的第一个框架集***

人们经常惊讶于创建包含框架的网页是如此容易。您要将浏览器窗口分成的每个矩形部分称为一个框架。要定义您希望在网站中出现的框架集，您所要做的就是编写一个特殊的 HTML 文件，定义每个框架的大小、位置和初始内容。然后，您必须为每个框架的内容编写单独的 HTML 文件，就像您对普通浏览器窗口所做的那样。

如果这对你来说还没有意义，不要担心…我一会儿将回到这些想法。现在，让我们专注于定义框架集的 HTML 文件。这被简单地称为框架集，看起来像这样:

```
<HTML>  

<HEAD>  

<TITLE> Our First Frameset </TITLE>  

</HEAD>  

<FRAMESET>  

 <FRAME>  

 <FRAME>  

 ...  

</FRAMESET>  

<NOFRAMES>  

...  

</NOFRAMES>  

</HTML> 
```

注意，这个 HTML 文件不包含`<BODY>`标签。还要注意在这个文件中使用了三个新标签:

*   `<FRAMESET>`
*   `<FRAME>`
*   `<NOFRAMES>`

`<FRAMESET>`标签取代了“普通”HTML 文件中的`<BODY>`标签，用于提供将出现在浏览器窗口中的一组框架的信息。使用这个标签来指定诸如如何分割窗口、将有多少帧以及与作为一个整体的一组帧相关的任何其他信息。

我们用一个具体的例子。假设您想要创建一个网页，在浏览器窗口的正中间有一个水平分隔线。换句话说，你有两个框架，每个占据了窗口中 50%的垂直空间。这种将帧视为可用空间的划分的方式很重要，因为这就是标签的工作方式:

```
<FRAMESET ROWS="50%,*"> 
```

这只是对浏览器说:

“我想要一套框架，把窗户分成几行。在可用空间中，第一帧将占据 50%，第二帧将占据剩下的所有空间。”

请特别注意星号(“*”)代表“剩下的东西”。稍后将详细介绍。

要在页面中间垂直向下放置分割线，只需键入以下内容:

```
<FRAMESET COLS="50%,*"> 
```

注意，为了简洁起见，HTML 用“`COLS`”来表示“列”。就是`COLUMNS="50%,*"` 不行！

现在，您可能已经猜到了，`<FRAME>`标签提供了特定于每个单独帧的信息。你必须输入与你用`<FRAMESET>`标签指定的帧数相等的`<FRAME>`标签。在我们这个简单的例子中，这个标签只是用来告诉浏览器将哪个 HTML 文件加载到每个框架中，如下所示:

```
<FRAME SRC="topframe.html">  

<FRAME SRC="botframe.html"> 
```

因此，我们框架集中的第一个(顶部)框架将加载有`topframe.html`，而第二个(底部)框架将包含`botframe.html`。

最后，`<NOFRAMES>`标签跟在`<FRAMESET>`标签后面，用于指定如果用户的浏览器不能显示框架时要显示的内容。虽然这变得越来越不常见，但许多教育机构仍然允许学生在基于文本的终端上访问互联网。这种媒体的 Web 浏览器，其中最流行的是 Lynx，通常不能显示帧。

```
<NOFRAMES>  

<P>Sorry, but it appears you are using a Web browser  

that does not support frames. Click <A HREF="noframes/  

index.html">here</A> to access the non-frames version   

of our site.</P>  

</NOFRAMES> 
```

标签的工作方式是对任何支持框架的浏览器说:“不要显示这个。”因为任何不支持框架的 Web 浏览器都不知道这个标签会是`<FRAMESET>` 和`<FRAME>`标签，所以它会简单地忽略所有这些标签，只显示包含的文本。在上面的例子中，我提供了一个不使用框架的网站链接。如果这样做不可行，最好在浏览器中添加一条提示，告诉不支持框架的用户，他们的浏览器无法访问您的站点。

将所有这些收集在一起，我们就有了简单的双框架框架集。

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seebasic.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codebasic.html)

***更上<框架>***

如上所述，`<FRAMESET>` 标签用于提供关于窗口应该如何划分成帧的信息。这是通过使用 `ROWS`和`COLS` 属性来分别定义窗口被划分的行和列来完成的。

这些划分可以用三种方式定义:百分比、像素和比率。这三种方法可以用几个例子清楚地说明。

```
<FRAMESET COLS="50,*,*"> 
```

在这个例子中，窗口将被分成三列；第一个将占据 50 像素的宽度。第二和第三帧将分别占据剩余空间的一半。

```
<FRAMESET COLS="50%,*,20%"> 
```

本例定义了三列，分别占窗口的 50%、30%和 20%。第一个和第三个窗口定义了它们的百分比，而第二个窗口占据了剩余的 30%。

```
<FRAMESET ROWS="3*,1*,2*"> 
```

这一次窗口将被分成行而不是列。第一个会占窗户高度的一半，第二个会占六分之一，最后一个会占窗户高度的三分之一。乍一看，这似乎令人困惑，但是从比率的角度来考虑它。该窗口被分成 6 个相等的部分(3+1+2=6)。第一帧占其中的 3 帧(六分之三是二分之一)，第二帧占 1 帧(六分之一)，最后一帧将占剩下的两帧(六分之二是三分之一)。

```
<FRAMESET ROWS="30,50%,2*,1*"> 
```

这次将有四排。第一帧将占用窗口高度的 30 个像素，而其余的帧将占用其余的像素。第二个框架将占据浏览器窗口总高度的 50%。第三和第四帧以 2 比 1 的比例占据剩余的空间。也就是说，第二帧占用的空间是第三帧的两倍。

一般不建议像这样把三种方法混用。这种方法使得阅读您的代码的人很难提前猜到它会是什么样子。在这种情况下，为了可读性，我通常推荐使用嵌套框架集。稍后会有更多的介绍。

您可以单独使用`ROWS`或`COLS`属性，或者一起使用它们来定义网格布局:

```
<FRAMESET ROWS="60,50%,*" COLS="1*,2*,3*">   

 <FRAME SRC="frame1.html">   

 <FRAME SRC="frame2.html">   

 <FRAME SRC="frame3.html">   

 <FRAME SRC="frame4.html">   

 <FRAME SRC="frame5.html">   

 <FRAME SRC="frame6.html">   

 <FRAME SRC="frame7.html">   

 <FRAME SRC="frame8.html">   

 <FRAME SRC="frame9.html">   

</FRAMESET> 
```

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seegrid.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codegrid.html)

如果在定义布局时需要更多的灵活性，可以使用一种称为嵌套框架集的技术。顾名思义，这涉及到在框架集中放置框架集。例如，如果您希望页面左侧有一个 50 像素宽的导航栏，但您还希望页面顶部有一个 30 像素高的标题框架，您可以使用以下代码:

```
<FRAMESET COLS="50,*">   

 <FRAME SRC="menu.html">   

 <FRAMESET ROWS="30,*">   

   <FRAME SRC="title.html">   

   <FRAME SRC="body.html">   

 </FRAMESET>   

</FRAMESET> 
```

请注意“inner” `<FRAMESET>`标签是如何到达`<FRAME>`标签通常会去的地方的。您可以用嵌套的框架集替换任意多的框架，甚至可以在框架集中的框架集中定义框架集(等等)！

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seenested.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codenested.html)

除了`ROWS` 和`COLS`之外，`<FRAMESET>` 标签还有其他几个属性，允许您影响框架集的外观:

**T2`FRAMEBORDER="n"`**

设置是否在框架之间显示三维边框。如果 n 为 1，将显示一个 3D 边框，就像该属性已被忽略一样。如果 n 为 0，边框将不可见。也可以用 `YES` 代替 1，用 `NO` 代替 0。此属性适用于 Netscape 和 Internet Explorer 版本 3 及更高版本。

**T2`BORDER="n"`**

设置框架之间三维边框的宽度。n 是像素数。此属性适用于 Netscape 版本 3 和更高版本，以及 Internet Explorer 版本 4 和更高版本。

**T2`FRAMESPACING="n"`**

设置框架之间的间距。n 是像素数。这样，即使您选择不显示 3D 边框，您也可以隔开框架。此属性仅适用于 Internet Explorer 浏览器。

**T2`BORDERCOLOR="color"`**

设置框架之间三维边框的颜色。颜色是一个标准的 HTML 颜色(例如“T0”、“T1”等)。).此属性适用于 Netscape 3 和更高版本，以及 Internet Explorer 4。

自己尝试一下，但是要记住哪些浏览器支持你使用的属性，哪些不支持。此外，确保你的框架集可以在尽可能多的浏览器上使用，这样人们就可以访问你的网站最重要的部分——内容。

***更上<帧>***

毫无疑问，您现在已经知道了，`<FRAME>`标签的主要功能是设置框架集中每个框架的初始内容。像`<FRAMESET>`标签一样，它也接受其他几个属性，允许您更改单个框架的工作方式。除非另有说明，否则这些属性适用于 Netscape 2 和更高版本，以及 Internet Explorer 3 和更高版本。

**T2`NAME="name"`**

允许您为框架设置名称。`name`是一段文本，通常用引号括起来。给你的框架起一个名字允许你用超文本链接定位它，并使用 JavaScript 操纵它。这些技术的示例将在本文的后续部分中展示。

**T2`FRAMEBORDER="n"`**

设置是否在此框架周围显示三维边框。如果`n`为 1，将显示 3D 边框。如果`n`为 0，边框将不可见。也可以用`YES`代替 1，用`NO`代替 0。在一个`<FRAME>`标签中使用它将覆盖封闭 `<FRAMESET>`的 `FRAMEBORDER`属性，如果有的话。此属性适用于 Netscape 和 Internet Explorer 版本 3 及更高版本。

**T2`NORESIZE`**

防止用户通过用鼠标拖动边框来更改此框架的大小。语法简单来说就是`NORESIZE`；不需要给它赋值。

**T2`SCROLLING="value"`**

定义滚动条是否应该在这个框架中显示。 `value`可能是`YES`、`NO`或`AUTO`。如果选择`YES`，水平和垂直滚动条将一直显示，即使框架的内容很小，无法一次放入框架。如果选择`NO`，滚动条将永远不会显示，即使框架的内容太大而无法一次放入框架。`AUTO`表示框架应该表现正常；仅当内容超过框架大小时才显示滚动条。

*注意:*除非你特别小心，否则使用`NORESIZE`和`SCROLLING=NO` 的框架可能会给屏幕尺寸较小的用户带来问题。除非需要，否则不要使用这种组合。如果你这样做，一定要在 640×480 像素的屏幕上测试它，以确保它仍然可用。

**T2`MARGINWIDTH="n"`**

设置框架及其内容的左右边框之间的间距。`n`是像素数。Internet Explorer 浏览器接受 0 或更高的值，但 Netscape 浏览器只接受 1 或更高的值，0 表示 1。

**T2`MARGINHEIGHT="n"`**

设置框架及其内容的上下边框之间的间距。 `n`是像素数。Internet Explorer 浏览器接受 0 或更高的值，但 Netscape 浏览器只接受 1 或更高的值，0 表示 1。

提示:如果你想让框架的内容与边缘齐平，你必须结合几种不同浏览器的技术。首先，对于 Internet Explorer 浏览器，使用 0 的`MARGINWIDTH`和`MARGINHEIGHT`。对于 Netscape 4，您还必须在 HTML 文件的 `<BODY>` 标签中设置一个`MARGINWIDTH` 和`MARGINHEIGHT`为 0，以便加载到正在讨论的框架中。不过，Netscape 3 或更早版本不会识别这些，因为在帧中有一个像素的最小边距是不可避免的。

再一次，这些属性的效果最好通过你自己来演示。始终记住哪些浏览器将支持您使用的每个功能。永远不要使用可能会排斥部分目标受众的属性。

##### 航行

既然你已经知道如何制作相框，那么是时候学习如何使用它们了。这一节将教你框架如何影响页面上链接的工作方式。之后，我们将利用目前所学的知识，设计一个使用众所周知的“菜单栏”布局的网站。

***针对性链接***

在框架出现之前，Web 作者在创建超文本链接时唯一需要担心的是要加载的文档的位置。对于框架，引入了一个新的问题。文件应该装入哪个框架？

任何对 HTML 稍有了解的人都应该知道，您使用锚标签`<A>`和`HREF`属性创建了一个超文本链接。

```
<A HREF="newdoc.html">...</A> 
```

默认情况下，`newdoc.html`将被加载到链接被点击的框架中。在某些情况下，这正是你想要的。但是，如果您创建了一个有两个框架的站点，其中一个框架包含可以加载到另一个框架中的文档菜单，那会怎么样呢？在这种情况下，您希望菜单框架中的链接指向主框架。

完成这个的第一步是给你的框架命名。如前一节所述，您可以用`<FRAME>`标签的 `NAME` 属性来完成。在我们的示例中，我们将框架菜单和主菜单命名如下:

```
<FRAMESET COLS="75,*" FRAMEBORDER=0>     

 <FRAME SRC="ourmenu.html" NAME="menu">     

 <FRAME SRC="introduction.html" NAME="main">     

</FRAMESET> 
```

然后，要使菜单框架中的所有链接都指向我们的主框架，我们必须使用 ourmenu.html 头部的`<BASE>` 标签的`TARGET`属性，它是加载到我们的菜单框架中的文件。

```
<HTML>     

<HEAD>     

<BASE TARGET="main">     

</HEAD>     

<BODY>     

... 
```

“好吧，这一切都很好，”你说。"但是如果我想让一个文件中的不同链接指向不同的框架呢？"没问题！`<A>` 标签还支持一个`TARGET` 属性，该属性覆盖您使用`<BASE>`标签指定的任何目标。

```
<A HREF="newdoc.html" TARGET="main">...</A> 
```

使用这个，你实际上可以忘记`<BASE>` 标签，只在所有链接上放置`TARGET` s，但是除非没有明确的默认目标，否则你通常会通过使用`<BASE>`标签来设置它，从而节省文件的大小。

***特殊目标***

由于您并不总是希望链接加载到您的一个框架中，因此有几个特殊的目标可供您使用。这些可以与`<BASE>`或`<A HREF>`标签一起使用，并且必须完全按照它们在这里出现的样子键入，因为它们区分大小写。

**T2`TARGET="_self"`**

将链接加载到与链接本身相同的框架中。这是默认行为，就像根本没有指定`TARGET` 属性一样。

**T2`TARGET="_top"`**

清除窗口中的所有框架，然后将新文档加载到窗口中。通常，每当您提供一个指向另一个站点的链接时，您都会用到它。如果你不这样做，另一个网站将被加载到你的框架中，导致一个丑陋的，有时无法使用的屏幕布局。除非你有特殊的理由不这样做，否则在你的网站之外提供链接的时候一定要用这个。对于用户来说，没有什么比被困在别人的框架中更令人讨厌的了。

**T2`TARGET="_parent"`**

将链接加载到当前框架的父框架中。简单地说，它清除包含链接的框架的框架集，并在它的位置加载目标文档。`_parent`和`_top`之间的区别出现在嵌套框架集的情况下，在这种情况下，您可能希望将链接加载到嵌套顺序中的下一步，而不清除任何“外部”框架集。在一个简单的非嵌套框架集中，`_parent` 和`_top` 做同样的事情。

**T2`TARGET="_blank"`**

打开一个新的浏览器窗口并将目标文档加载到其中，保持当前框架集不变。在某些情况下，当提供一个到别人站点的链接时，这比使用`_top` 更可取。

##### 基于框架的网站

现在我们已经介绍了使用框架所需的所有基本技能，让我们将所学的一切结合到一个具体的示例中。过去几年经常上网的人无疑已经注意到，最流行的网站布局之一是在页面左侧使用菜单栏。一种流行的做法是将这个菜单栏放在一个框架中，这样网站的主要内容可以上下滚动，而用户不会看不到菜单。

与任何基于框架的站点一样，我们从设置框架集开始。我们将调用我们的框架集文件`index.html`，假设它将是当有人进入我们的站点时加载的第一个页面。与任何框架集文件一样，它不包含`<BODY>`标签，而是使用`<FRAMESET>`、`<FRAME>`和`<NOFRAMES>`标签来定义布局。

```
<HTML>      

<HEAD>      

<TITLE> Welcome to my Web site! </TITLE>      

</HEAD>      

<FRAMESET FRAMEBORDER=0 COLS="150,*">      

 <FRAME NAME="menu" NORESIZE SRC="mainmenu.html">      

 <FRAME NAME="main" SRC="welcome.html">      

</FRAMESET>      

<NOFRAMES>      

<H1>Welcome!</H1> <P>This site was designed to use frames to       

provide a menu bar to ease navigation; however, your browser       

does not support frames. Click <AHREF="welcome.html">here</A> to go      

to the main page of our site, where you can use the alternative       

links we have provided to get around the site.      

</NOFRAMES>      

</HTML> 
```

注意这个例子中的一些事情。我们在`<FRAMESET>`标签中使用了 `FRAMEBORDER=0` 来隐藏默认情况下出现在框架之间的 3D 边框。在许多情况下，这使得基于框架的布局看起来更加无缝，从而更具吸引力。

接下来，我们使用了`<FRAME>`标签的`NAME` 属性为我们的每个帧分配一个合适的名称。这将有助于我们以后定义框架之间的链接。

还要注意在我们的第一个`<FRAME>`标签中使用了`NORESIZE`属性。这可以防止用户改变菜单栏的宽度，并保持我们网站的布局如我们所愿。我们不需要为主框架指定 `NORESIZE`，因为当我们禁用菜单框架的调整大小时，这是自动发生的。我们有两个框架。显然，如果不允许我们调整其中一个的大小，我们将无法调整另一个的大小，因为这将意味着调整第一个的大小以适当地填充窗口。

最后，我们使用了`<NOFRAMES>`标签为浏览器不支持框架的用户提供了另一条欢迎消息。因为我们不想为不支持框架的浏览器创建一个完全不同的站点版本，所以我们简单地提供了一个链接，指向我们通常会加载到主框架中的同一个文件。然后，我们可以在主页中提供一组谨慎的链接来浏览我们的网站。

接下来，我们需要设计我们的菜单。正如 frameset 中设置的那样，我们的主菜单将包含在`mainmenu.html`文件中。这将是一个 HTML 文件，除了我们不需要一个`<TITLE>`标签，因为框架没有标题栏来显示它们的标题。

```
<HTML>      

<HEAD>      

<BASE TARGET="main">      

</HEAD>      

<BODY BGCOLOR="DarkSlateBlue" BACKGROUND="menuback.gif"       

TEXT="white" LINK="LightSkyBlue" ALINK="Orange" VLINK="#FF9900">      

<BASEFONT SIZE="2" FACE="Arial, Helvetica, sans-serif" SIZE="-1">      

<H3>Main Menu</H3>      

<HR NOSHADE>      

<A HREF="welcome.html">Welcome</A>      

<P><A HREF="products.html">Products</A>      

<P><A HREF="order.html">Order</A>      

<P><A HREF="support.html">Support</A>      

<P><A HREF="info.html">Info</A>      

<HR NOSHADE>      

</BODY>      

</HTML> 
```

这里要注意的主要事情是`<BASE>` 标签，我们用它来指定这个文件中链接的默认目标。很简单，这使得菜单中的所有链接都将它们的目标文件加载到主框架中。

剩下的工作就是创建我们网站的主页。这些将被写得就像我们的网站上根本没有框架一样。没有必要使用`<BASE>`标签，因为除非我们通过使用`<A>`标签中的`TARGET`属性指定其他方式，否则我们希望主页中的链接加载到主框架中。记住，对于那些浏览器不支持框架的用户，我们必须在菜单框架的某个地方放置一组谨慎的链接。

我已经提供了这个网站看起来像什么的基础。使用以下链接查看。完成后，关闭窗口返回此处。如果您愿意，可以在自己的站点中随意使用这些代码。

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seenav.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codenav.html)

##### 高级问题

对于那些特别好奇的人，或者那些已经对框架有了很好的了解，但还想学习一两个花哨的技巧的人，这一节涵盖了两个高级的框架技术。第一个问题可能是框架设计中最常见的问题:“我如何让一个链接改变两个框架？”第二个是一个聪明的技巧，它可以让你避免编写许多小的 HTML 文件来填充简单和重复的框架。

这两种技术都需要对 JavaScript 有一定的理解，但是我将从头开始解释一切。任何对编程概念有基本理解的人都应该可以理解。

***多帧链接***

所以，你一整天都在便利贴上涂写矩形框，你终于想出了你的新网站的最终布局。你在覆盖你桌子的皱巴巴的黄色方块中筛选，找到你的鼠标，然后打开你的编辑器。当你盯着屏幕时，你突然意识到一件可怕的事情。为了让你的布局工作，你需要让一个链接改变两个框架的内容。

你沉没了吗？当然不是！输入 JavaScript。使用这个简单的语言，我们将改变我们的链接，而不是将文件加载到一个框架中，它将触发一小段 JavaScript，我们已经在文件的标题中定义了它。这段 JavaScript 被称为函数，将完成一次改变几个帧的棘手工作。稍后会有更多的介绍。

在我们进一步讨论之前，我想说这里描述的技术将适用于所有的 Netscape 浏览器，也适用于 Internet Explorer 4.0。如果你使用这种技术，Internet Explorer 3.0 的用户将无法正常访问你的网站，所以一定要权衡你的选择，以决定多框架链接是否真的值得。

如果您不熟悉 JavaScript，让我来描述一下这种聪明的语言是如何工作的。JavaScript 通常被称为面向对象的编程语言。简单来说，这意味着它从对象的角度考虑一切。您的 Web 浏览器是一个对象，浏览器窗口是一个对象，您的网页中的图像是对象，您的框架是对象！对象的好处是你可以改变它们。您可以在特定对象中更改的内容称为该对象的属性。

表示浏览器窗口的对象(窗口对象)的属性之一是文档。事实证明，文档也是一个对象(我不是告诉过你一切都是对象吗？).文档对象的属性之一是它的位置。该位置是窗口当前显示的文档的 URL。

“这和我的多框架链接问题有什么关系？”你问。正如我已经说过的，你可以改变对象的属性。那么，如果您更改其中一个框架的文档的 location 属性，会发生什么情况呢？当然，框架会加载新的 URL。在 JavaScript 中，您可以随心所欲地更改任意多个对象的任意多个属性！

首先，让我向您展示更改单个框架的内容所涉及的 JavaScript。

```
parent.frames["name"].document.location = "newdoc.html";
```

其中`name`是要更改的框架的名称，`newdoc.html`是要加载到该框架中的新文档的文件名或 URL。这是 JavaScript 的说法:

<q>“在父窗口中，在名为`"name"`的框架中，将文档的位置更改为`"newdoc.html"`</q>

好了，现在你有了一个非常奇特的方法去做你已经知道该怎么做的事情。如何更改多个框架的位置？好吧，你只需为你想要改变的每一帧重复那行 JavaScript 代码。

```
parent.frames["frame1"].document.location = "newdoc1.html";       

parent.frames["frame2"].document.location = "newdoc2.html";       

... 
```

那么，这些奇特的 JavaScript 到底去了哪里呢？正如我提到的，你的每个链接都会触发一个叫做函数的 JavaScript 小程序。在 HTML 文件的标题中，在一个`<SCRIPT>`标签中定义函数。

```
<HTML>       

<HEAD>       

...       

<SCRIPT LANGUAGE="JavaScript">       

<!-- Hide from older browsers       

function speciallink() {       

 parent.frames["frame1"].document.location = "newdoc1.html";       

 parent.frames["frame2"].document.location = "newdoc2.html";       

}       

// -->       

</SCRIPT>       

</HEAD>       

... 
```

在上面的例子中，我们定义了一个名为`speciallink()`的 JavaScript 函数，它改变两个框架的内容。现在剩下的就是当用户点击正确的链接时触发该功能。

所有这些都需要一种我们都知道并喜爱的奇特的标签形式:

```
<A HREF="javascript:speciallink()">...</A> 
```

我们称之为 JavaScript 链接。这只是告诉浏览器，当链接被点击时，它将运行名为`speciallink()`的 JavaScript 函数，正如您所知，它是在文件的标题中定义的。

使用这种技术时，不要使用`<A>`标签的`TARGET` 属性，这一点很重要。记住，在这种情况下，您有多个“目标”框架，并且它们已经在 JavaScript 函数中被指定了。如果您要使用`TARGET` 属性，您的浏览器将在您指定的`TARGET`框架中寻找 JavaScript 函数。在某些特殊情况下，您可能希望这样做，但是对于我们的目的来说，这是应该避免的。出于同样的原因，确保不要使用`<BASE TARGET=...>` 方法在带有 JavaScript 链接的文档中指定默认目标框架。

综上所述，对于每一个需要修改不止一帧的链接，创建一个 JavaScript 函数来进行所需的修改。然后，用 JavaScript 链接指向该函数。

例如，考虑我们在上一节中构建的基于框架的示例站点。在每一页的顶部，我们有一个大号字体的标题，与你所在的网站部分相对应(产品、订购、客户支持等)。).如果我们想把标题放在页面顶部自己的框架中，这样当用户向下滚动时，它仍然可见，会怎么样？每当用户点击菜单上的链接时，我们都必须改变标题和主页。

用我们学过的新技术，这很容易做到。以下是我们修改后的示例:

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seemf.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codemf.html)

如果您打算大规模使用这种技术，您可能需要花一点时间来学习更多关于 JavaScript 的知识。例如，假设您的站点有五个主要部分，每个部分都有多个子部分，您希望不仅能够在菜单中处理多框架链接，而且能够在主页中处理多框架链接。您必须编写数量惊人的 JavaScript 函数，并在站点中几乎每个 HTML 文件的标题中重复这些函数。

有了 JavaScript 的良好知识，就有可能在所有的框架和 HTML 文件之间共享一个单一的、多用途的功能。因此，您可以大大减少在大型站点中实现这种技术所需的代码量。不过，我在这里演示的简单技术将在一个小型的个人网站上完美地工作。

***照本宣科的画面***

通常当你写一个基于框架的站点时，你想用一个框架来显示一些简单的东西，比如一个标题或者一个定义，你会发现自己写了许多小的 HTML 文件来加载到这个框架中。

让我们以一个实际的(如果平凡的)框架为例，我们可以选择文本和背景颜色。在一个框架中，我们将有每个文本/背景颜色组合的链接。另一方面，我们将显示用户选择的结果。现在，虽然我们只需要一个 HTML 文件用于包含链接的框架，但是我们需要一个不同的 HTML 文件用于我们想要在第二个框架中显示的每种颜色组合。每个文件都很小，如下所示:

```
<HTML>        

<BODY BGCOLOR="bgcolor" TEXT="textcolor">        

Can you read this?        

</BODY>        

</HTML> 
```

很无聊，是吧？最糟糕的部分不是你需要一个接一个地输入这些，而是用户的浏览器必须单独下载每一个！如果我告诉你没有必要写这些小文件呢？如果我告诉你，你可以改变一个框架的内容，而不必加载一个新的 HTML 文件？

在上一节中，我教了您一些关于 JavaScript 对象、属性和函数的知识。碰巧的是，一些 JavaScript 对象，除了有你可以改变的属性之外，还有你可以用来对它们做特殊事情的内置函数。这些内置函数称为方法。

您可能还记得，document 对象表示加载到给定框架中的 HTML 文件。我们已经看到 document 对象有一个名为 location 的属性，您可以用它来加载一个新文件。事实证明，document 对象也有一个名为`write()`的方法，可以让你直接修改框架的内容。除了`write()`，我们还需要另外两个方法，叫做`open()`和`close()`。`open()` 使用`write()` 方法清除文件内容并准备写入。告诉你的浏览器你已经完成了对文档的写入，现在它可以显示你所做的修改。

因此，我们可以像这样更改示例框架的内容，而不是加载新文件:

```
parent.frames["sample"].document.open();        

parent.frames["sample"].document.write('<HTML>');        

parent.frames["sample"].document.write('<BODY BGCOLOR="bgcolor"         

TEXT="textcolor">');        

parent.frames["sample"].document.write('Can you read this?');        

parent.frames["sample"].document.write('</BODY>');        

parent.frames["sample"].document.write('</HTML>');        

parent.frames["sample"].document.close(); 
```

注意我们要写的文本是如何放在`write()`的圆括号中，并且用单引号(`'`)括起来的。选择单引号是为了不让它们干扰正在编写的 HTML 代码中的任何双引号(`"`)。

作为一种快捷方式，我们将创建一个变量来存储我们正在写入的文档的位置。可以把它想象成一个 JavaScript“书签”，我们可以用一个方便的名字来引用`parent.frames["sample"].document` 。我们使用关键字`var`来创建一个新变量并给它赋值。

```
var sampledoc = parent.frames["sample"].document;        

sampledoc.open();        

sampledoc.write('<HTML>');        

sampledoc.write('<BODY BGCOLOR="bgcolor" TEXT="textcolor">');        

sampledoc.write('Can you read this?');        

sampledoc.write('</BODY>');        

sampledoc.write('</HTML>');        

sampledoc.close(); 
```

现在，大魔术开始了。如果我们也为文本`bgcolor` 和`textcolor`使用变量会怎么样？然后，我们可以将这些变量的值更改为我们想要的任何值，并且每次都重用这一整块代码来编写我们的样本帧！

```
var bgcolor = 'black';        

var textcolor = 'lightsteelblue';        

var sampledoc = parent.frames["sample"].document;        

sampledoc.open();        

sampledoc.write('<HTML>');        

sampledoc.write('<BODY BGCOLOR="' +         

bgcolor + '" TEXT="' + textcolor + '">');        

sampledoc.write('Can you read this?');        

sampledoc.write('</B></FONT>');        

sampledoc.write('</BODY>');        

sampledoc.write('</HTML>');        

sampledoc.close(); 
```

请注意我们如何使用'`+`'操作符将文本片段与两个变量的内容粘在一起。

为了完整起见，我们将这一整块代码插入到一个 JavaScript 函数中。这个函数让我们在用户点击链接时触发这个写操作。它还做了另一件重要的事情。它让我们在触发函数时指定两个颜色变量的值！我们通过在函数名的括号中插入变量的名称来实现这一点，我们希望在函数被触发时指定变量的值，用冒号分隔。

我们完成的函数将如下所示:

```
function chgcolor(bgcolor,textcolor) {        

 var sampledoc = parent.frames["sample"].document;        

 sampledoc.open();        

 sampledoc.write('<HTML>');        

 sampledoc.write('<BODY BGCOLOR="' +         

 bgcolor + '" TEXT="' + textcolor + '">');        

 sampledoc.write('Can you read this?');        

 sampledoc.write('</B></FONT>');        

 sampledoc.write('</BODY>');        

 sampledoc.write('</HTML>');        

 sampledoc.close();        

} 
```

使用黑色背景和 LightSteelBlue 文本触发此函数的链接如下所示:

```
<A HREF="javascript:chgcolor('Black','LightSteelBlue');">...</A> 
```

就是这样！现在，你已经大幅削减了用户浏览器的下载量和打字量(取决于你打算提供多少种颜色组合)。

[See it in Action](http://www.webmasterbase.com/examples/framesintro/seecolourpicker.html) | [View the Code](http://www.webmasterbase.com/examples/framesintro/codecolourpicker.html)

##### 概括起来

在这篇文章中，我努力展示了一幅关于使用框架构建网站的技术的完整图片。我不仅讲述了基础知识，希望任何具有 Web 设计和一般编程基础知识的人都可以理解，而且我还讲述了一些更高级的主题，这些主题将对更有经验的 Web 开发人员有用。

有关框架的更多信息，请尝试:

*   [CNET 建筑商。COM:“趣味画框”](http://builder.cnet.com/webbuilding/pages/Authoring/Frames/)
*   [Webmonkey 创作:框架](http://hotwired.lycos.com/webmonkey/authoring/frames/)
*   [W3C 的 HTML 4.01 规范](https://www.w3.org/TR/REC-html40/)

## 分享这篇文章