# 实用网页设计——表格介绍，第 2 部分

> 原文：<https://www.sitepoint.com/design-introduction-tables-2/>

一些发人深省的事实:

 ***   世界上三分之二安装的个人电脑已经使用了三年或更久
*   视窗 98 是世界上个人电脑使用最多的操作系统**  **这意味着大量的人用“过时的”浏览器浏览我们的网页，如 Internet Explorer 4.x 或 5.x，或更早版本的 Netscape Navigator。你我都知道，大多数用户都懒得更新他们的系统或浏览器，无论是在办公室还是在家里。他们坚持从盒子里拿出来的东西。如果每个人都使用完全兼容 CSS 和 CSS2 的最新浏览器，那将是最佳选择，但事实并非如此。事实上，大多数冲浪世界并没有使用最新的浏览器；因此，大多数用户不能正确地查看你的构造精美、无表格、基于 CSS 的页面。

因此，除非你为完全由尖端用户组成的受众(即典型的 SitePoint 或 [Slashdot](http://www.slashdot.org) 用户)编写网页，否则你将不得不与表格打交道。这就是生活的现实。如果你是一个新兴的网页设计师，你需要了解表格，即使它们很快就会过时。把它想象成学习如何驾驶手动挡汽车…当然，你可能会花大部分时间驾驶自动变速器的汽车，但知道如何驾驶手动挡汽车是值得的。你会更好地理解汽车的操作。有时候驾驶手动挡不仅仅是一种选择，而是一种必须。

在本初级读本的第一部分，HTML 表格:初级读本，第一部分，我们讨论了表格构造的基础，以及一些更高级的选项。这一部分将概述 HTML 4 中为表格提供的选项，以及一些其他项目。和第一篇专栏文章一样，这篇文章不会涉及任何新的或与众不同的内容。这是严格为我们中不太专业的人准备的。

学习使用这些标签的最好方法就是玩它们。剪切并粘贴下面的代码示例，并将其放入您的 HTML 编辑器中。构建一个示例表，一次插入一个命令，看看会发生什么。将他们分组使用，看看他们之间的互动如何。和他们玩得开心！

##### HTML 4 和 4.01

HTML 4.0 于 1997 年 12 月作为 W3C 推荐标准首次发布，它为我们提供了大量表格构造的“新”选项。“新”是一个相对的术语:这些选项中有许多是由 Netscape 或微软引入的，最初只在这些浏览器中工作(有些选项仍然是特定于浏览器的)。1999 年 12 月，HTML 4.01 成为 W3C 标准，并且在我们慢慢向 XML/XHTML 发展的今天仍然如此。以下是一些在 HTML 4 中成为标准的表格选项。再次注意，不是所有的浏览器都支持严格的 HTML 4，因此不是所有的命令都适用于所有的浏览器。

如果你打算使用大量的 4.01 标签，你需要在“严格的”HTML 范围内工作。这意味着使用以下 DOCTYPE:

```
!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Strict //EN 

"https://www.w3.org/TR/html4/strict.dtd">
```

如果您决定这样做，请做好与旧的、不推荐使用的 HTML 命令不兼容的准备。严格的意思就是严格。旧标签不能在严格的 HTML 格式下工作。这是一个权衡:您是否希望放弃使用旧的、不推荐使用的标签(并冒着失去那些使用不能正确显示严格 HTML 的旧浏览器的观众的风险)而使用新的 4.01 标签(更不用说充分使用 CSS)？这是需要考虑的事情，也是需要花一些时间去处理的事情。IE 5x 和 IE 6 应该能够处理严格的 HTML 的约束，Netscape Navigator 6、Opera 7.02 和 Mozilla 1.2 也是如此。不要打赌早期的浏览器能够处理严格的 HTML，也不要打赌即使是这些浏览器也会完全处理严格的 HTML 4.01。

此外，请注意，Mozilla 的某些版本在正确处理严格的 HTML 方面存在很多问题，基于 Mozilla 的浏览器如 Netscape 6、Netscape 7、Camino、Galeon、最近发布的 Phoenix/Firebird 和 Beonex 也是如此。一些浏览器如 Mozilla、Mac 的 Safari、Opera 7 和 IE 6 有两种主要的布局模式，“怪癖”和“标准”。Mozilla 和 Safari 也有“几乎标准”模式，会影响表格布局。所有这些都是在你决定使用哪种文档类型之前需要调查和处理的因素。

一般的经验法则:“过渡”DOCTYPE 在今天的编码中可能在整体意义上工作得更好，但是 HTML 世界正在迅速向严格的标准发展，不再支持“过渡”HTML 文档。习惯在严格合规的氛围中工作是个好主意。对于不同种类的文档类型的概述，请参见我之前的文章[Web 设计基础](https://www.sitepoint.com/blog/)。

前往[该页面](http://www.howtocreate.co.uk/tutorials/jsexamples/brotest/)测试您的浏览器功能。

<q>*“这是一个反馈回路，在一定程度上。如果我们想让人们编写 HTML 4 兼容的页面，就必须支持完整的 HTML 4 规范，并且公开支持。”—乔·克拉克*</q>

***表数据***

你什么意思？表格数据，或者说`<TD>`标签，从表格开始就已经存在了！没错，没错，但是 HTML 4 增加了一些新的令人兴奋的属性……好吧，没那么新，而且“有趣”可能比“令人兴奋”更合适。然而，您的工具箱中已经为旧的 warhorse TD 标签准备了一些新工具。让我们来看几个:

*   ABBR:当您将鼠标指针停留在特定单元格上时，提供翻转“工具提示”
*   轴:给非可视浏览器一个标签来描述单元格内的数据
*   CHAR/CHAROFF:使用 ALIGN 属性，这个属性允许您设置一个特定的文本字符作为对齐点，通常是一个小数；从那里，你可以按照单元格宽度的百分比来偏移文本字符(这个需要做一些实验才能对你有用)
*   HEADERS: most useful with non-visual browsers, this attribute works with the table header `<TH>` command to give a header to each cell, like so:

    `<TABLE>  
    <TR>  
    <TH ID="1">SIZES  
    <TH ID="2">WIDTHS  
    </TR>  
    <TD HEADERS="1">99  
    <TD HEADERS="2">100`

*   当鼠标经过时，非可视化浏览器会给出标题信息。更多`<TH>`信息见下文。
*   范围:留出表的一部分作为拥有属性的 TD。作用域本身有四个影响其功能的值:
    *   COL 设置列的其余部分
    *   COLGROUP 设置列组的其余部分
    *   ROW 设置行的其余部分
    *   ROWGROUP 设置行组的其余部分

警告:非可视化浏览器如 Lynx 最多只能支持不稳定的表格。如果您的 Lynx 用户错过了一些表格内容，不要感到惊讶。

***表格行***

和上面的`<TD>`标签一样，`<TR>`标签也有一些新的属性。其中大部分与`<TD>`有关。它们是:ALIGN、CHAR、CHAROFF、BGCOLOR 和 VALIGN。前面三个解释了。BGCOLOR 是一个熟悉的属性，它只是设置行的背景色。VALIGN 设置行内容的垂直对齐方式:顶端对齐、居中对齐或底端对齐。当然，还有其他的属性，但是我会让你自己去发现。

***表头***

一个表头，由`<TH>`命令激活，它接受与上面`<TD>`部分解释的相同的六个属性:

*   ABBR，
*   轴，
*   CHAR，
*   夏若夫，
*   标题，以及
*   范围，

以及其他一些我们现在不会涉及的。

***边框颜色***

在本文的第 1 部分中已经讨论过，BORDERCOLOR 是微软的优秀人员给我们的一个属性，最初只在 Internet Explorer 中可用。现在可以在 IE 3 及以上版本，Navigator 4 及以上版本中运行。这很简单:

```
<TABLE BORDER="1" BORDERCOLOR="#ff0000">
```

这给了我们一个漂亮的红色边框。不幸的是，边框颜色在不同浏览器中的显示方式并不总是可靠的。这个例子抄袭了 [WDVL](http://wdvl.internet.com/Authoring/HTML/Tables/AdvTables/advtab3.html) 的内容，展示了一个在 Internet Explorer 中给出棕色边框，在 Netscape 中给出浅绿色边框的代码示例:

```
<TABLE BORDER="20" WIDTH="200" BGCOLOR="#ccff66" BORDERCOLOR="#33cc66" BORDERCOLORLIGHT="#996666" BORDERCOLORDARK="#663300">
```

本示例中的两个命令 BORDERCOLORLIGHT 和 BORDERCOLORDARK 使用 BGCOLOR 标记:BORDERCOLORLIGHT 是行边框突出显示的颜色，BORDERCOLORDARK 是行边框阴影的颜色。如果这个没有意义，那就试试看，看哪个边框哪个部分。

想要所有浏览器的边框都一样吗？一个好的方法是使用嵌套表格(表格中的表格)，外部表格比内部表格稍大，并有自己的背景颜色。

***帧***

这不是备受指责的框架命令，它将页面显示分成单独的部分。相反，它只显示你想看到的那部分边框。它在`<TABLE>`命令中工作，并具有以下值:

*   上方:仅显示顶部边缘
*   下方:仅显示底部边缘
*   BORDER:默认情况下，显示所有四条边(因此，不需要使用)
*   BOX:BORDER 属性的变体，它也显示所有四条边
*   HSIDES:仅显示顶部和底部边缘
*   LHS:仅显示左侧边缘
*   RHS:仅显示右侧边缘
*   空心:不显示任何边
*   VSIDES:显示左右边缘

试试看。下面是一个代码示例:

```
<TABLE BORDER="1" FRAME="below">
```

它没有被广泛使用，因为不是所有的浏览器都支持它。

***规则***

同样，这不是一个被广泛支持的表属性。这个属性的工作原理类似于上面提到的 FRAME 属性，但是只适用于内部边框(试试吧，你会明白我的意思)。它具有以下值:

*   全部:显示所有边框
*   列:显示单元格之间的边框
*   组:显示所有单元格组之间的边框
*   无:不显示边框
*   行:显示行之间的边框

代码可能看起来像这样:

```
<TABLE BORDER="1" RULES="all">
```

***汇总***

一些人仍然使用文本浏览器，比如 Lynx，还有一些人仍然使用太旧的浏览器，根本不支持表格。这些人看不到您的表格，但他们可能可以看到内容。您可以使用 SUMMARY 标记来给出表格的文本描述。可以把它想象成表格相当于图形的 ALT 标记:

```
<TABLE BORDER="1" SUMMARY="This table is full of    

information about the different games we can play with    

HTML tables.">
```

***字幕***

这个标记与 SUMMARY 标记配合得很好，让读者对表中的内容有所了解；标题可以看作是表格的标题。每张桌子只能有一个标题。像这样试试:

```
<TABLE BORDER="1" SUMMARY="This table is full of    

information about the different games we can play with    

HTML tables.">   

<CAPTION="Games Tables Play">
```

***事件处理程序***

这些很有趣。如果你不知道这个术语，“事件处理器”字面上处理一个事件。事件是您，或者更准确地说，是您的代码使之发生的一个操作。处理程序决定什么键盘或鼠标命令将激活动作。

在这种情况下，您可以向您的表添加一个弹出窗口，该窗口由以下命令激活:`onBlur`、`onClick`、`onDblClick`、`onDragStart`、`onFocus`、`onKeyDown`、`onKeyPress`、`onKeyUp`、`onMouseDown`、`onMouseMove`、`onMouseOut`、`onMouseOver`、`onMouseUp`或`onScroll`。

我建议您亲自尝试这些命令，看看哪一个最适合您，而不是告诉您每个命令的作用；或者您可以深入研究每个命令的 W3C 定义。

下面是示例代码(将其剪切并粘贴到您的 HTML 编辑器中，在您的浏览器中显示，然后单击表格，看看会发生什么):

```
<TABLE BORDER="1" onClick="alert('This is what happens    

when you try this command out!')">
```

尝试修改代码以适合您，但是不要忘记将所有的引号(单引号和双引号)和括号保留在它们的位置上。

***列群***

这个标记允许您为表中的整列数据设置参数。它在设置宽度、高度和样式表效果时效果最好。像许多这样的标签一样，它有自己的一套属性。在这个标记之前，您必须为每一个`<TD>`设置一列的属性。同样，并不是所有的旧浏览器都支持这个标签。它有 24 个(！)属性，许多属性指的是相关的 JavaScripts，但是现在我们只限于下面的五个:

*   对齐:内容左对齐、右对齐、居中对齐
*   CHAR/CHAROFF:同上；使用 ALIGN 属性，这个属性允许您设置一个特定的文本字符作为对齐点，通常是一个小数；从那里，你可以用单元格宽度的百分比来偏移文本字符(这个需要做一些实验才能对你有用)
*   SPAN:允许 COLGROUP 标记“跨越”多列
*   WIDTH:以像素或百分比为单位设置列的宽度
*   VALIGN:垂直设置单元格的内容，可以是顶部、居中或底部

这里有一个例子:

在一个 5 列的表格中，要使每列的宽度为 35 像素，请写出:

```
<COLGROUP SPAN="5" WIDTH="35">   

</COLGROUP>
```

没有 SPAN 属性的 COLGROUP 命令使用默认的 SPAN 1。COLGROUP 命令的另一个很好的功能是它告诉浏览器表中有多少列。如果您已经给定了表格的总宽度，浏览器可以增量显示表格。因此，每一列将在加载后立即显示，而不是等到整个表下载完毕后才显示。

***山坳***

这会对齐一个或多个表格列中的内容。像许多其他的一样，它起源于微软的扩展，后来被添加到 HTML 4 中。与 COLGROUP 命令不同，它不在结构上将列组合在一起，但可以将多列的属性组合在一起。这些属性可以与 COLGROUP 命令一起使用，也可以独立使用。它有 22 个独立的属性，但是最常用的是 ALIGN、CHAR、CHAROFF、SPAN、VALIGN 和 WIDTH。

***THEAD，TBODY，TFOOT(行组)***

这些属性最初是微软独有的扩展，已经被合并到 HTML 4 中，由于同样的原因，我们不能依赖样式表——不完善的浏览器支持，这些属性没有被经常使用。太糟糕了，因为它们很有用。使用这些命令，您可以为表格定义“标题”、“正文”和“页脚”。如果您陷入了线性思维，您可能希望按照假定的外观顺序组织这些命令:头、体和尾。不要。`<THEAD>`之后是`<TFOOT>/#ec# command, which is then followed by the` `<TBODY>`命令。

HEAD 是一个有趣的命令。像对待 HTML 页面中的`<HEAD>`部分一样看待它——作为一种在整个表格中设置行为的方式。在一个表中只能使用一个 THEAD 命令。它接受以下属性:ALIGN、BGCOLOR、CHAR、CHAROFF、VALIGN。下面是一个简单的例子，使用 THEAD 命令设置表格中的颜色:

```
<TABLE BORDER="1">   

<THEAD>   

<THEAD BGCOLOR="#ff0000">   

<TR>   

<TD>Bleah!</TD>   

<TD>Bleah!</TD>   

</TR>   

<TR>   

<TD>Bleah!</TD>   

<TD>Bleah!</TD>   

</TR>   

</THEAD>   

<TABLE>
```

现在我们有一张非常非常红的桌子。但是设置颜色不是很容易吗？总比一行一行，或者一个细胞一个细胞地做好。

TFOOT 命令和它的兄弟 THEAD 一样，每个表只能使用一次。它基本上充当了表格头部的“页脚”。和 THEAD 一样，它支持以下属性:ALIGN、BGCOLOR、CHAR、CHAROFF、VALIGN。

TBODY 几乎相当于表中的 BODY 命令。您可以使用与其他属性相同的属性:ALIGN、BGCOLOR、CHAR、CHAROFF、VALIGN。

##### 非 HTML 4 标签

仍然有相当多的非标准表格标签值得一提。小心使用它们，因为并非所有浏览器都支持它们。

**数据页大小/数据行 T3**

这些是仅用于 Internet Explorer 的命令，用于显示数据库中的信息。DATAPAGESIZE 属性允许您指定表需要多少列，而 DATASRC 属性表示应该在表中显示的来自另一个数据库的特定数据列。它在`<TABLE>`命令中工作。这些属性可能如下所示:

```
<TABLE DATAPAGESIZE="5" DATASRC="DB7SR2">
```

这取决于您的数据来源。

其他的非 HTML 4 标签到处都是，但是它们比上面的命令用得更少，所以我们现在就让它们躺着吧。

##### 杂项提示

***空细胞***

有时候你会想故意空出一个单元格。如果您需要这样做，并且这会破坏您的表结构，那么可以使用不间断空格(`&nbsp;`)或透明的。GIF 给单元格一些“看不见的”内容。使用透明的。GIF 能让你更好地控制显示的大小。Netscape 4 是这个特殊问题的罪魁祸首，因为它拒绝显示空列。

***删除不想要的换行符***

有些浏览器在单元格内显示不需要的换行符。你查看你的 HTML，但是没有一个`<BR>`标签。怎么会有断线？嗯，有些浏览器会把代码中的回车符当作换行符，通常是在内容结尾和`</TD>`标签之间。而不是这个:

```
<TD>    

Bleah!    

</TD>
```

尝试写作:

```
<TD>Bleah!</TD>
```

***对齐问题***

前一篇专栏文章中讨论的 ALIGN 属性已经被 HTML 4 否决，它的一些行为并不总是正确的。由于浏览器通常将使用 ALIGN 属性的表格显示为“浮动”表格，因此它们可能会选择将内容显示为在表格中流动。在表格的末尾使用`<BR="clear">`命令来强制你的内容行为正常。

当你使用 ALIGN 属性来定位你的内容时，记住一些老的浏览器不支持“居中”命令。在这种情况下，使用`<DIV ALIGN="center">`命令将内容居中。

为什么不应该在表格中使用背景图片？IE 和 Netscape 的一些版本在表格单元格内显示背景图像时都有问题，所以一些设计者避免在他们的表格中显示背景图像。然而，如果这对你有用，那就想尽一切办法去做。

表格的 ALIGN="right "属性允许您在页面的右侧放置"悬挂"表格。这不是经常做的，因为它比通常的左侧表格要难一点，但它可以在视觉上吸引人。这里有一个简单的例子，包含在一个更大的页面中。剪切并粘贴到一个 HTML 页面，看看它是如何工作的:

```
<P>More content, drivel, and useless page filler to give us something to work with.  Let's just keep going for a bit longer.  There, that should do it.</P>
```

```
<H4>Looky, there's a table on the right side coming up!</H4>
```

```
<TABLE BORDER="1" WIDTH="150" ALIGN="right" BGCOLOR="#ff0000" CELLPADDING="5">    

<TR>    

<TD>    

<H4>And here's the table content!</H4>    

Bleah!  Bleah!  Bleah!  Bleah!  Bleah!  Bleah!  Bleah!  Bleah!  Bleah!    

</TD>    

</TR>    

</TABLE>
```

```
<P>The regular content continues along the left side of the page, nicely formatted to include the table on the right side.  Looks nice, doesn't it?  A fellow could get sick of that ubiquitous red color he keeps using for an example.  Maybe he should consider using a different color sometime?</P>
```

一些浏览器被 ALIGN="right "代码卡住了，所以如果每个人看到的结果不一样，不要感到惊讶。

***XHTML 用户***

注意:我并没有试图在本文中解决 XHTML 用户关心的问题，基本上是因为任何熟悉 XHTML 的人可能都不需要这篇文章来编写他或她的代码。我只想指出，本专栏中给出的许多例子都不符合 XHTML，就这样吧。

##### 最后的想法

表格并不特别难处理，但是很棘手。表格的“学习曲线”比更基本的 HTML 代码更陡峭，后者基本上只是向文本添加标签。学习处理表格是成为一名真正熟练的网页设计师的第一步，因为你现在正在学习实际设计网页，而不仅仅是装饰文本块。

我的建议是在表格设计的编码丛林中慢慢前进。首先使用`<TABLE>`、`<TR>`和`<TD>`标签熟悉基本的表格设计。了解这三种标签的不同用法和配置可以做什么，不可以做什么。然后开始向知识库添加新标签，一次添加一个。

在使用另一组命令之前，先熟悉一个或一组命令。尝试不同的属性。实验。犯错误，并从中吸取教训。玩得开心。这才是真正的意义所在，不是吗？

##### 文献学

使用文档类型声明
[http://www.hut.fi/u/hsivonen/doctype.html](http://www.hut.fi/u/hsivonen/doctype.html)激活右侧布局模式

高级 HTML 表格
[http://wdvl . internet . com/Authoring/HTML/Tables/adv Tables/index . HTML](http://wdvl.internet.com/Authoring/HTML/Tables/AdvTables/index.html)

文档类型及其各自的布局模式
[http://gutfeldt . ch/Matthias/articles/doctype switch/table . html](http://gutfeldt.ch/matthias/articles/doctypeswitch/table.html)

光荣人民的达标神话
[http://www.joeclark.org/glorious.html](http://www.joeclark.org/glorious.html)

简明英语的 HTML，第二版，桑德拉·e·艾迪 1998

HTML 表格
[http://www.w3schools.com/html/html_tables.asp](http://www.w3schools.com/html/html_tables.asp)

HTMLHelp:表格
[http://www.htmlhelp.com/reference/html40/tables/table.html](http://www.htmlhelp.com/reference/html40/tables/table.html)

符合标准的世界中的新窗口链接
[https://www.sitepoint.com/standards-compliant-world/](https://www.sitepoint.com/standards-compliant-world/)

HTML 文档中的脚本
[https://www.w3.org/TR/html401/struct/tables.html#h-11.2.4.2](https://www.w3.org/TR/html401/struct/tables.html#h-11.2.4.2)

表格教程

(断开的链接)

HTML 文档中的表格
[https://www.w3.org/TR/html401/struct/tables.html](https://www.w3.org/TR/html401/struct/tables.html)

表格可访问性
[http://www . your html source . com/tables/tables Accessibility . html](http://www.yourhtmlsource.com/tables/tablesaccessibility.html)

测试浏览器的性能
[http://www.howtocreate.co.uk/tutorials/jsexamples/brotest/](http://www.howtocreate.co.uk/tutorials/jsexamples/brotest/)

XHTML:简介
[https://www.sitepoint.com/article/590](https://www.sitepoint.com/blog/)

## 分享这篇文章**