# CSS 很简单！

> 原文：<https://www.sitepoint.com/css-is-easy/>

****层叠样式表**。光是这个名字就令人生畏，让人联想起晦涩难懂的代码和语法，外行人很难理解。然而，实际上，CSS 是 Web 开发人员可用的最简单和最方便的工具之一。在本文中，我将向您介绍 CSS 的基础知识，展示如何应用 CSS 来简化管理格式一致的网站的任务，同时又不会带来太多麻烦。**

#### HTML 的问题

CSS 是一种定义网站中使用的格式的语言。这包括颜色、背景图像、字体、边距和缩进。“但是我现在用 HTML 标签做所有的事情，”你可能会想。“我为什么需要 CSS？”这是一个有效的问题，回答这个问题的最好方法是举例说明使用 HTML 定义这样的样式有什么问题。

目前常见的设计选择是使用无衬线字体(如 Arial、Verdana、Tahoma 等。)作为站点的主体文本。由于大多数 Web 浏览器默认使用 Times New Roman 这样的衬线字体，使用无衬线字体创建复杂的 Web 页面布局通常会涉及到大量的`<font>`标签。随着嵌套表格布局在 Web 上变得司空见惯，十个或二十个`<font>`标签专门用于对页面上的所有文本应用相同的字体并不罕见。对于一个普通的网站来说，把这个数字乘以 5 页，我们就有了 100 个标签。一个更强大的网站可能有 50 页或更多，在这种情况下，你会看到一千个`<font>`标签，它们都致力于将一种基本的、一致的风格应用到你的文档文本中。

现在问题来了:如果你的客户在一个周五下午打电话给你说，“Verdana 很好，但是每个人都用它。还是用宋体吧。”抛开花哨的搜索和替换工具不谈，你现在面临的任务是调整一百个、一千个甚至更多的`<font>`标签，从你的客户的角度来看，这似乎是一个非常简单的改变。你可以和你计划好的滑雪周末吻别了。尽量不要大声抱怨——这不会让大多数客户满意。

如果您了解 HTML，您可能会认为`<basefont>`标签为这个问题提供了一个很好的解决方案，它允许您设置在整个页面中使用的默认字体。即使这样，你也必须为你站点的每个页面调整一个标签。在等式中添加另一种字体样式(比如你想为你的漂亮的导航栏使用不同的字体)，问题又回来了。

对于这个问题和其他问题，级联样式表是解决方案。

#### 用 CSS 定义样式

CSS 的基本原理是允许设计者定义一个**样式**(一系列格式细节，如字体、大小和颜色)，然后使用**选择器**将其应用于一个或多个 HTML 页面的一个或多个部分。让我们看一个基本的例子，看看这是如何做到的。

考虑下面的 HTML 文档大纲:

```
<HTML>

<HEAD>

<TITLE> A simple page </TITLE>

</HEAD>

<BODY>

<H1><FONT FACE="sans-serif" COLOR="#3366CC">First Title</FONT></H1>

<P>...</P>

<H1><FONT FACE="sans-serif" COLOR="#3366CC">Second Title</FONT></H1>

<P>...</P>

<H1><FONT FACE="sans-serif" COLOR="#3366CC">Third Title</FONT></H1>

<P>...</P>

</BODY>

</HTML>
```

该文档包含三个标题，使用`<H1>`标签创建。为了让这些标题更加突出，我使用了`<FONT>`标签，用无衬线字体将它们变成浅蓝色(例如，Windows 浏览器会用 Arial 字体显示它们)。注意其中的重复，即使是在这个基础水平上。我必须分三次指定我想要的字体的细节。只定义一次字体，然后将它应用于我的标题，这不是很有意义吗？下面是 CSS 版本:

```
<HTML>  

<HEAD>  

<TITLE> A simple page </TITLE>  

<STYLE TYPE="text/css">  

<!--  

H1 {  

  font-family: sans-serif;  

  color: #3366CC;  

}  

-->  

</STYLE>  

</HEAD>  

<BODY>  

<H1>First Title</H1>  

<P>...</P>  

<H1>Second Title</H1>  

<P>...</P>  

<H1>Third Title</H1>  

<P>...</P>  

</BODY>  

</HTML>
```

所有的魔法都在文档的`<HEAD>`中的`<STYLE>`标签之间，在这里我们定义了浅蓝色的无衬线字体，并将其应用于文档中的`<H1>`标签。不要担心语法；一会儿我会详细解释的。与此同时，`<FONT>`标签已经从`<BODY>`中完全消失，让我们的文档看起来不那么杂乱。对页面顶部样式定义的更改会影响所有三个标题，添加到页面的任何其他标题也会采用该样式。

现在你对 CSS 的作用有了一些了解，让我解释一下在 HTML 文档中使用 CSS 样式的不同方法。

#### 向 HTML 文档添加 CSS 样式

将 CSS 样式放入网页的最简单的方法是使用`<STYLE>`标签，就像我在上面的例子中所做的那样。这允许您通过将 CSS 样式放在开始的`<STYLE>`标签和结束的`</STYLE>`标签之间来声明任意数量的 CSS 样式，如下所示:

```
<STYLE TYPE="text/css">   

  **CSS Styles here**   

</STYLE>
```

属性指定了你用来定义你的风格的语言。在撰写本文时，CSS 是唯一被广泛使用的语言，用值`text/css`表示。另一种只被网景 4.x 支持的语言叫做 JavaScript 样式表(JSS)，由`text/javascript`指定。然而，由于 JSS 非常有限的兼容性，它很少被使用。

由于 Web 浏览器被设计为忽略它们无法识别的任何标签，所以不支持 CSS 的旧浏览器通常会在文档中以纯文本的形式输出 CSS 样式定义。为了防止这种情况，通常的做法是将样式定义包含在 HTML 注释标记中:

```
<STYLE TYPE="text/css">   

<!--   

  **CSS Styles here**   

-->   

</STYLE>
```

虽然美观简单，但是`<STYLE>`标签有一个主要的缺点。具体来说，如果你想在你的站点中使用一组特定的样式，你必须在你的站点页面顶部的一个`<STYLE>`标签中重复这些样式定义。

更明智的选择是将这些定义放在一个纯文本文件中(通常有一个`.css`文件名)，然后将您的文档链接到那个文件。对该文件中样式定义的任何更改都会影响链接到它的所有网页。正如我们前面提到的，这为我们提供了站点范围样式定义的理想目标。

要将一个文档链接到一个 CSS 文本文件(比如，`styles.css`)，请在文档头中放置一个`<LINK>`标签:

```
<LINK REL="STYLESHEET" TYPE="text/css" HREF="styles.css">
```

回到我最初的三个标题共享一种样式的例子，文档现在看起来像这样:

```
<HTML>

<HEAD>

<TITLE> A simple page </TITLE>

<LINK REL="STYLESHEET" TYPE="text/css" HREF="styles.css">

</HEAD>

<BODY>

<H1>First Title</H1>

<P>...</P>

<H1>Second Title</H1>

<P>...</P>

<H1>Third Title</H1>

<P>...</P>

</BODY>

</HTML>
```

并且`styles.css`文件将包含样式定义:

```
H1 {   

  font-family: sans-serif;   

  color: #3366CC;   

}
```

像图像文件一样，您可以在任意多的页面中重用这个`styles.css`文件。这不仅可以节省你的打字时间，还可以确保整个网站的标题外观一致。

#### CSS 选择器

每个 CSS 样式定义都有两个组件:定义样式将应用于哪些标签的**选择器**，以及指定样式实际作用的**属性**。在前面的例子中，选择器是`H1`，指定样式应该应用于所有的`<H1>`标签。样式定义的其余部分是属性，指定样式应该应用的字体和颜色。在这一节中，我将描述基本的 CSS 选择器类型，并给出每种类型的例子。

##### 标签选择器

选择器的最基本形式是我们已经看到的。通过命名特定的 HTML 标记，您可以将样式定义应用于文档中该标记的每一次出现。这通常用于设置将出现在整个网站中的基本样式。例如，以下内容可用于设置网站的默认字体:

```
BODY, P, TD, TH, DIV, BLOCKQUOTE, DL, UL, OL {    

  font-family: Tahoma, Verdana, Arial, Helvetica, sans-serif;    

  font-size: 10pt;    

  color: #000000;    

}
```

这个相当长的选择器是一个标签列表，所有的标签都具有样式定义(字体、大小和颜色)。从理论上讲，`BODY`标签就是所需要的全部(因为所有其他标签都出现在`BODY`标签中，因此会继承属性)，但是许多浏览器不能正确地将样式属性携带到表格和其他元素中，所以为了完整起见，我指定了其他元素。

##### 伪类选择器

HTML 中的`<A>`标签的格式比大多数其他标签更加通用。通过在`BODY`标签中指定`LINK`、`VLINK`和`ALINK`属性，您可以为页面中链接的各种状态设置颜色(分别为未访问、已访问和被点击)。CSS 提供了自己的方式来实现这一点，并为鼠标停留在链接上时添加了第四种状态。考虑下面的例子:

```
A:link { color: #0000FF; }

A:visited { color: #FF00FF; }

A:hover { color: #00CCFF; }

A:active { color: #FF0000; }
```

这段代码包含四个 CSS 样式定义。每个选择器都使用被称为`<A>`标签的**伪类**。第一个应用于所有链接，并指定它们应该是蓝色的。第二个应用于访问过的链接，并通过使它们变成洋红色来覆盖第一个定义。第三个样式定义覆盖了前两个，当鼠标停留在链接上时，链接变成浅蓝色(注意伪类的名称:`hover`)，不管它们是否被访问。最终的样式定义使链接在被点击时变成红色。因为它出现在最后，它覆盖了前三个，所以不管链接是否被访问，不管鼠标是否在它们上面，它都会生效。注意，Netscape 4.x 不支持 hover 伪类(但是 Netscape 6 支持它)。

```
<A> is the only tag that has pseudo-classes under the original CSS1 specification, and all four are demonstrated in the example above. The newer CSS2 spec that is beginning to see support in recent browsers specifies several other pseudo-classes, most of which have to do with how elements look when printed.类别选择器

为标签指定样式固然很好，但是如果您想为文档中不同位置的相同标签指定不同的样式，该怎么办呢？这就是 CSS 类的用武之地。考虑以下样式，该样式使页面中的所有段落文本为蓝色:

```
P { color:#0000FF; }
```

现在，如果你的页面上有一个蓝色背景的侧边栏会怎么样？你不希望边栏中的文本也是蓝色的，因为它是不可见的！您需要做的是为侧边栏文本定义一个类，然后将 CSS 样式分配给该类:

```
P { color: #0000FF; }

.sidebar { color: #FFFFFF; }
```

第二条规则使用了一个**类选择器**，它指示样式应该应用于`sidebar`类的任何标签。句点表示正在命名一个类，而不是标记。要创建一段`sidebar`类的文本，您需要向标签添加一个`CLASS`属性:

```
<P CLASS="sidebar">This text will be white, as specified by the CSS style definitions above.</P>
```

现在，如果你的侧边栏有链接会怎么样？默认情况下，它们会像页面中的任何其他链接一样呈现；然而，给标签添加一个`CLASS="sidebar"`属性，它们也会变成白色:

```
<P CLASS="sidebar">This text will be white, <A CLASS="sidebar" HREF="link.html">and so will this link</A>.</P>
```

这非常简洁，但是如果您想通过用粗体文本显示链接来使它们更加突出呢？将 bold text 属性添加到`sidebar`类会使你的整个侧边栏变成粗体。你需要一个 CSS 选择器来选择侧边栏类的链接。通过结合标签选择器和类选择器，您可以做到这一点:

```
P { color: #0000FF; }

.sidebar { color: #FFFFFF; }

A.sidebar:link { font-weight: bold; }
```

注意，我们还使用了一个伪类来指定作为链接的`<A>`标签(与不是链接的`<A NAME>`标签相反)。
还要注意，我们的侧边栏链接仍然是白色的——属于侧边栏类的两种样式都适用于我们的侧边栏链接。但是，如果我们在第三种样式中指定了一种不同的颜色，那么链接将采用这种新的颜色，因为第三个选择器更加具体，而且 CSS 样式是按照增加选择器具体性的顺序应用的。
顺便说一句，这种将多种样式应用到单个页面元素的效果被称为**级联**，级联样式表也因此得名。
最后，您可能会发现，在 Netscape 4.x 下，由于浏览器中的一个错误，链接没有以粗体显示。下一节将演示实现相同效果的另一种方法。
上下文选择器
如果你的侧边栏碰巧包含很多链接，那么给每一个`<A>`标签分配`sidebar`类就变得很繁琐。相反，使用选择器来选择出现在`sidebar`类的段落中的任何链接不是很好吗？这就是上下文选择器的作用:基于标签的**上下文**选择标签；也就是说，基于包含它的标签。
这是新的 CSS:

```
P { color: #0000FF; }    

.sidebar { color: #FFFFFF; }    

P.sidebar A:link {    

  font-weight: bold;    

  color: #FFFFFF;    

}
```

这是更新后的 HTML:

```
<P CLASS="sidebar">This text will be white, <A HREF="link.html">and so will this link</A>.</P>
```

如您所见，上下文选择器提供了一个由空格分隔的选择器列表，这些选择器必须“从外向内”匹配标签。在这种情况下，由于链接(由`A:link`选择器匹配)在侧边栏段落内(由`P.sidebar`选择器匹配)，样式应用于链接。
注意，为了保持链接为白色，我们必须将这种颜色添加到链接的 CSS 属性中，因为`sidebar`类的样式不再适用于链接。
ID 选择器
类似于类选择器，ID 选择器用于选择一个特定的标签，而不是一组标签。您选择的标签由一个`ID`属性标识，如下所示:

```
<P ID="sidebar1">This paragraph is uniquely identified by the ID "sidebar1".</P>
```

ID 选择器就是前面加了一个散列(`#`)的 ID。因此，下面的样式将使上面的段落变白:

```
#sidebar1 { color: #FFFFFF; }
```

ID 选择器可以与其他选择器类型结合使用。例如，以下样式适用于出现在`sidebar1`段落中的链接:

```
#sidebar1 A:link {

  font-weight: bold;

  color: #FFFFFF;

}
```

其他选择器类型也存在，但它们并没有被广泛使用，一些浏览器(尤其是 Netscape 4.x)对它们的支持充其量也只是有缺陷。

#### CSS 属性

在本文迄今为止使用的例子中，我使用了几个 CSS 属性，如`color`、`font-family`、`font-size`和`font-weight`。在这一节中，我将把您的注意力引向当今浏览器支持的最常见的 CSS 属性。除非另有说明，否则这些属性应该在 Netscape 4 或更高版本以及 MSIE 4 或更高版本中起作用。

CSS 的一个令人兴奋的方面是，它允许您实现许多使用普通 HTML 无法再现的效果。有关 CSS1 属性的完整列表(当前浏览器支持其中的大部分)，请参见 CSS1 规范的第 5 章[。关于 CSS2 属性的完整列表(目前的浏览器支持其中许多属性)，请参见](https://www.w3.org/TR/REC-CSS1#css1-properties) [CSS2 规范](https://www.w3.org/TR/REC-CSS2)。

##### `border-color`

设置选定元素周围边框的颜色。可以使用`border-top-color`、`border-right-color`、`border-bottom-color`和`border-left-color`在 IE4 和 NS6 或更高版本中单独设置每一面的颜色。

例:`.errormessage { border-color: #FF0000; }`

##### `border-style`

设置选定元素周围的边框样式。4.x 浏览器支持的样式有:`double`、`groove`、`inset`、`none`、`outset`、`ridge`、`solid`。可以使用`border-top-style`、`border-right-style`、`border-bottom-style`和`border-left-style`在 IE4 和 NS6 或更高版本中单独设置每一侧的样式。

例:`.framed { border-style: ridge; }`

##### `border-width`

设置选定元素周围的边框宽度。可以使用`border-top-width`、`border-right-width`、`border-bottom-width`和`border-left-width`单独设置每一侧的样式。

例:`.thickborder {  border-width: 3px; }`

##### `margin`

设置元素边框外部(如果有)和周围页面元素之间的边距大小。可以使用`margin-top`、`margin-right`、`margin-bottom`和`margin-left`单独设置每边的边距。

例:`.spacedout { margin: 10px; }`

##### `padding`

设置元素边框内部(如果有)和元素本身之间的填充大小。可以使用`padding-top`、`padding-right`、`padding-bottom`和`padding-left`单独设置每一侧的填充。

例:`.roomyborder { padding: 10px; }`

##### `background-attachment`

指定元素的背景图像(尤其是`BODY`标签)是随该元素一起滚动，还是在元素内容滚动时保持固定。该属性可以设置为`fixed`或`scroll`。这在 NS4 中不受支持。

例:`BODY { background-attachment: fixed; }`

##### `background-color`

设置选定元素的背景颜色。可设置为任何 HTML 颜色代码或名称，或`transparent`。

例:`.highlighted { background-color: yellow; }`

##### `background-image`

设置选定元素的背景图像。可以设置为一个 URL(注意下面例子中的语法)，或者`none`。

例:`.flowery { background-image: url(flowers.gif); }`

##### `background-position`

NS4 不支持，此属性允许您设置元素背景的位置。对于平铺背景，这将指定第一个平铺的左上角。对于非平铺背景(见下面的`background-repeat`，它指定了背景图像左上角的位置。您可以指定一个或两个百分比、长度或位置(`left`、`center`或`right`，其中第一个指定水平位置，第二个指定垂直位置。如果没有指定垂直位置，则认为是`50%`。

例:
 `.flowermiddle {     

  background-image: url(flowers.gif);     

  background-position: center center;     

  background-repeat: no-repeat;     

}     

.watermark {     

  background-image: url(fadedlogo.gif);     

  background-position: 50px 50px;     

  background-repeat: no-repeat;     

}`

##### `background-repeat`

为选定元素的背景图像设置平铺方法。您可以选择水平(`repeat-x`)、垂直(`repeat-y`)、双向(`repeat`)或根本不平铺背景(`no-repeat`)。NS4 不支持此属性。

例:
 `TD.sidebar {     

  background-image: url(bar.gif);     

  background-repeat: repeat-y;     

}`

##### `color`

设置选定元素的前景色。这通常是元素中文本的颜色。

例:
 `P.inverse {     

  color: white;     

  background-color: black;     

}`

##### `list-style-type`

设置在`<UL>`或`<OL>`标签中使用的列表类型。`<UL>`标签的可用类型有`circle`、`disc`和`square`。`<OL>`标签的可用类型有`decimal`(1)`lower-alpha`(A)`lower-roman`(I)`upper-alpha`(A)`upper-roman`(I)。

例:`UL { list-style-type: square; }`

##### `list-style-image`

允许您指定图像的 URL，该图像将用作列表中的项目符号(`<DL>`、`<OL>`、`<UL>`)，或者用于特定的列表项目(`<DT>`、`<LI>`)。特定列表项的设置`none`指定应该使用默认项目符号而不是图像，并且可以覆盖为整个列表指定的图像。NS4 不支持此属性。

例:`#listItem1 { list-style-image: url(fileicon.gif); }`

##### `list-style-position`

设置列表标记(项目符号或编号)是放在列表项文本框中的`inside`还是`outside`。看待该属性的另一种方式是，`inside`的设置导致文本换行到行首，而`outside`的设置导致文本换行到第一行列表标记后的第一个字符。NS4 不支持此属性。

例:`OL { list-style-position: inside; }`

##### `font-family`

设置要在选定元素中使用的字体/字样。执行与`<FONT>`标签的`FACE`属性相同的功能。该值应该是以逗号分隔的字体名称列表，按照首选项的顺序排列，包含空格的字体名称用引号括起来。如同在`<FONT>`标签中一样，字体列表中的最后一项通常应该是从下列字体中选择的“通用”字体名称:`serif`、`sans-serif`、`cursive`、`fantasy`或`monospace`。

例:`P { font-family: "Times New Roman", Times, serif; }`

##### `font-size`

指定所选元素中使用的字体大小。您可以使用以下可用预设之一来指定字体大小:`xx-small`、`x-small`、`small`、`medium`、`large`、`x-large`、`xx-large`。预设`larger`和`smaller`也可用于设置与从父元素继承的大小相关的字体大小，或浏览器默认值。或者，您可以用像素(`px`)、磅(`pt`)、ems ( `em`)或父元素字体大小的百分比(`%`)来指定字体大小。

例:`.navbar { font-size: 8px; }`

##### `font-style`

指定所选元素中的字体是正常呈现(`normal`)还是斜体(`italic`)。

例:`EM { font-style: italic }`

##### `font-variant`

可用于以大写字母显示文本，源代码的小写字母以较小的字体显示。设置`small-caps`启用该效果，而`normal`禁用该效果。NS4 不支持此属性。

例:`H1 { font-variant: small-caps; }`

##### `font-weight`

设置选定元素中字体的粗细。大多数浏览器只真正支持`normal`和`bold`，但 CSS 规范定义了以下值:`bold`、`bolder`、`lighter`、`normal`、`100`、`200`、`300`、`400`、`500`、`600`、`700`、`800`、`900`，其中`normal`与`400`相同，`bold`与`700`相同。

例:`B { font-weight: bolder; }`

##### `letter-spacing`

指定选定元素中字符之间的间距。值`normal`使用默认间距，而正测量值和负测量值(`pt`、`em`、`px`)可分别用于收紧或放松间距。NS4 不支持此属性。

例:`.tight { letter-spacing: -0.1em }`

##### `text-align`

将所选元素内文本的水平对齐方式设置为`left`、`right`、`center`或`justify`。在 Windows 的 NS4 和 Macintosh 的 IE4 中,`justify`选项呈现为`left`。

例:`.newscolumn { text-align: justify; }`

##### `text-decoration`

将所选元素中的文本设置为使用几种效果之一进行渲染，包括`underline`、`overline`、`line-through`或`none`。NS4 忽略了`overline`设置。

例:`A:link { text-decoration: none; }`

##### `line-height`

指定选定元素内文本的行距。可接受的值包括`normal`(浏览器默认值)、数字(其中`1`是单倍行距、`2`是双倍行距、`1.5`是两者的中间值)、度量值(`px`、`pt`、`em`)或百分比(其中`100%`是单倍行距)。当您在设计中使用这个属性时，请仔细测试它，因为它在版本 4 的浏览器中是有问题的。

例:`.doublespace { line-height: 2; }`

##### `text-indent`

设置选定元素中第一行文本的缩进(如果为负值，则为突出)。该值可以是一个度量值(`px`、`pt`、`em`)，或者是父元素宽度的百分比。

例:`P.story { text-indent: 3em; }`

##### `text-transform`

设置选定元素中文本的大小写。值`none`指定文本应该像在源代码中一样大写。`capitalize`将每个单词的首字母设为大写。`lowercase`和`uppercase`将所有字母转换成指定的大小写。

例:`H1 { text-transform: capitalize; }`

##### `vertical-align`

设置所选元素(`bottom`或`top`)内文本的垂直对齐方式，或者所选元素相对于周围文本(`baseline`、`middle`、`sub`、`super`、`text-bottom`或`text-top`)的垂直对齐方式。

例:`.superscript { vertical-align: super; text-size: smaller }`

#### 总结和进一步阅读

看到了吗？CSS 不是很容易吗？在这篇文章中，我提供了一个非常全面的 CSS 介绍；然而，为了简洁起见，我省略了这种强大的格式化语言的许多更高级的选择器特性和样式属性。虽然你可以在[http://www.w3.org](http://www.w3.org)浏览 CSS1 和 CSS2 规范，但是这些规范中描述的许多高级特性在当前的浏览器中仍然不受支持。尤其是 CSS2 规范，即使在最新的浏览器中也只实现了一半。与听觉演示和打印相关的功能并没有成为浏览器制造商的优先考虑，即使它们已经在官方规范中存在多年了。

更好的办法是拿起一本关于这个主题的好书。我推荐 O'Reilly 的**动态 HTML:权威参考文献**,但是还有许多其他选择，都非常好。只要确保您选择的参考资料包括关于讨论的每个功能的浏览器兼容性的信息。当事情不像预期的那样工作时，它会让你不用花很长时间抓耳挠腮。

## 分享这篇文章

```