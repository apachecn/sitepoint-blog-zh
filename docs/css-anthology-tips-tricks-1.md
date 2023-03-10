# CSS 选集:101 个基本提示、技巧和窍门 1

> 原文：<https://www.sitepoint.com/css-anthology-tips-tricks-1/>

层叠样式表听起来很吓人。光是这个名字就让人联想到外行人难以理解的神秘代码和语法。然而，实际上，CSS 是 Web 开发人员可用的最简单和最方便的工具之一。

这篇文章实际上是 SitePoint 书籍[《CSS 选集:101 个基本技巧、诀窍&黑客](https://www.sitepoint.com/books/cssant1)中的一章。

##### 谁应该读这本书？

这本书的目标读者是那些需要使用 CSS 的人——网页设计者和开发者，他们已经看到了很酷的 CSS 设计，但是没有时间去研究大量的理论和争论来创建一个网站。每个问题都有一个工作解决方案，可以按原样实现，也可以作为一个起点。

这本书不是教程；虽然第 1 章《CSS 入门》讲述了 CSS 的基础知识，而且前几章讲述了比后面更简单的技术，但是如果你有 CSS 的基础知识，你会发现这些例子更容易掌握。

##### 这个系列涵盖了哪些内容？

这一系列的章节将由 CSS 选集的前四章组成:101 个基本提示，技巧&黑客。

[第 1 章:CSS 入门](https://www.sitepoint.com/css-anthology-tips-tricks-1/)

这一章没有遵循和本书其余部分一样的格式——它只是一个简单的快速 CSS 教程，适合任何需要温习 CSS 基础知识的人。如果你已经在你自己的项目中使用 CSS，如果你发现你想更详细地研究基本概念，你可能想跳过这一章并在需要的基础上回头参考它。

[第 2 章:文本样式和其他基础知识](https://www.sitepoint.com/css-anthology-tips-tricks-2/)

本章介绍了在文档中设置文本样式和格式的技术；字体大小，颜色，以及删除页面元素周围多余的空白空间等内容将在本章中进行解释。即使您已经在使用 CSS 进行文本样式化，您也会在这里找到一些有用的提示。

[第三章:CSS 和图像](https://www.sitepoint.com/css-anthology-tips-tricks-3/)

将 CSS 和图像结合起来可以创造出强大的视觉效果。这一章着眼于你可以做到这一点的方法，包括背景图片(不仅仅是在身体上)，以及用图片定位文本，等等。

[第四章:导航](https://www.sitepoint.com/article/css-anthology-tips-tricks-4/)

我们都需要导航，这一章解释了如何做到这一点，CSS 样式。基于图像的导航的 CSS 替换，CSS“标签”导航，结合背景图像和 CSS 文本来创建有吸引力的和可访问的菜单，以及使用列表以一种可访问的方式来构建导航，这些问题都将在本章中讨论。

有关信息，[请参见本书的第](https://www.sitepoint.com/books/cssant1/)页。

但是现在，让我们从 CSS 开始吧！

在第一章中，它采用了与本书其他部分不同的格式，我将引导你了解 CSS 的基础知识，并向你展示如何用它来简化管理一个格式一致的网站的任务。如果你已经使用 CSS 来格式化你的网站上的文本，你可能想跳过这一章，直接跳到第 2 章，文本样式和其他基础开始的解决方案。如果你想离线阅读这些信息，你可以下载。pdf 版的前 4 章在这里。

##### HTML 的问题

CSS 是一种用于定义应用于网站的格式的语言，包括颜色、背景图像、字体、边距和缩进。如果你以前从未使用过 CSS，你可能会想，“嗯，我现在用 HTML 标签做所有的事情。我为什么需要 CSS？”这是一个有效的问题，最好用我们使用 HTML 定义样式时可能出现的问题的例子来回答。

目前流行的一种设计选择是使用无衬线字体(如 Arial、Verdana、Tahoma 等。)作为站点的主体文本。由于大多数网络浏览器默认使用衬线字体，如 Times New Roman，使用无衬线字体创建复杂的网页布局通常会涉及到大量的`<font>`标签。在一个复杂的布局中，你可能会看到十个或二十个`<font>`标签专用于对页面上的所有文本应用相同的字体。将这个数字乘以 5——一个普通网站的页面数量——我们就有大约 100 个标签。一个更强大的网站可能有 50 页或更多，在这种情况下，你会看到 1000 个`<font>`标签，它们都致力于将一种基本的、一致的风格应用到你的文档文本中。

现在问题来了:你的客户在一个周五的下午打电话给你说，“Verdana 很好，但是每个人都用它。还是用宋体吧。”抛开花哨的搜索和替换工具不谈，你现在面临的任务是调整一百个、一千个甚至更多的`<font>`标签，从你的客户的角度来看，这似乎是一个非常简单的改变。你可以和你计划好的滑雪周末吻别了。此外，尽量不要大声呻吟——大多数顾客都不喜欢这样。

如果您了解 HTML，您可能会认为`<basefont>`标签为这个问题提供了一个很好的解决方案，它允许您设置在整个页面中使用的默认字体。但是即使这样，你也必须为你的站点的每个页面调整一个标签。在等式中添加另一种字体样式(比如说，如果您想为自己的漂亮导航栏使用不同的字体)，问题又会全部出现。

不应该使用 HTML 来格式化站点的另一个原因是，这些表示性元素已被否决(在未来的规范中被标记为删除)，如果您希望遵循严格的文档类型，如 HTML 4.01 Strict 或 XHTML 1.0 Strict，则不能使用这些元素。虽然如果您使用过渡 DOCTYPE，您的页面仍然有效，但最好尽可能避免使用这些不推荐使用的元素。正如你将通过本书中的例子发现的，CSS 允许你做许多你不能单独用 HTML 做的事情，所以有许多你应该使用 CSS 的理由——但是让我们停止谈论，看看一些实际的 CSS！

##### 用 CSS 定义样式

CSS 的基本目的是允许设计者定义一种样式(一系列格式细节，如字体、大小和颜色)，然后使用选择器将其应用于一个或多个 HTML 页面的一个或多个部分。让我们看一个基本的例子，看看这是如何做到的。

考虑下面的 HTML 文档大纲:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-  

     transitional.dtd">  

<html >  

<head>  

<title>A Simple Page</title>  

<meta http-equiv="content-type"  

    content="text/html; charset=iso-8859-1" />  

</head>  

<body>  

<h1><**font face="sans-serif" color="#3366CC"**>First   

Title<**/font**>  

</h1>  

<p>...</p>  

<h2><**font face="sans-serif" color="#3366CC"**>Second   

Title<**/font**>  

</h2>  

<p>...</p>  

<h2><**font face="sans-serif" color="#3366CC"**>Third   

Title<**/font**>  

</h2>  

<p>...</p>  

</body>  

</html>
```

该文档包含三个标题，使用`<h1>`和`<h2>`标签创建。为了让这些标题更加突出，我使用了`<font>`标签，用浅蓝色无衬线字体显示(例如，Windows 浏览器会用 Arial 字体显示)。注意其中的重复，即使是在这个基础水平上。我必须分三次指定我想要的字体的细节。只定义一次字体，然后将它应用于我的标题，这不是很有意义吗？下面是 CSS 版本:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  

<html >  

<head>  

<title>A Simple Page</title>  

<meta http-equiv="content-type"  

    content="text/html; charset=iso-8859-1" />  

**<style type="text/css">  

h1, h2 {  

 font-family: sans-serif;  

 color: #3366CC;  

}  

</style>**  

</head>  

<body>  

<h1>First Title</h1>  

<p>...</p>  

<h2>Second Title</h2>  

<p>...</p>  

<h2>Third Title</h2>  

<p>...</p>  

</body>  

</html>
```

所有的神奇之处都在文档的`<head>`中的`<style>`标签之间，在这里我们定义了浅蓝色的无衬线字体，并将其应用于文档中的所有`<h1>`和`<h2>`标签。不要担心语法；一会儿我会详细解释的。同时，`<font>`标签已经从`<body>`中完全消失，让我们的文档看起来不那么杂乱。对页面顶部样式定义的更改将影响所有三个标题，以及添加到页面的任何其他标题。

现在你对 CSS 有了一个概念，让我解释一下在你的 HTML 文档中使用 CSS 样式的不同方法。将 CSS 样式放入网页的最简单方法是使用`<style>`标签，正如我在上面的例子中所做的那样。这允许您通过将 CSS 样式放在`<style>`标签中来声明任意数量的 CSS 样式，如下所示:

```
<style type="text/css">  

CSS Styles here  

</style>
```

属性指定了你用来定义你的风格的语言。在撰写本文时，CSS 是唯一被广泛使用的语言，用值`text/css`表示。

虽然这个标签很好也很简单，但是它有一个很大的缺点。具体来说，如果你想在你的站点中使用一组特定的样式，你必须在你的站点页面顶部的一个`<style>`标签中重复这些样式定义。

更明智的选择是将这些定义放在一个纯文本文件中(通常有一个`.css`文件名)，然后将您的文档链接到该文件。对该文件中样式定义的任何更改都会影响链接到它的所有网页。这实现了前面提到的站点范围样式定义的目标。

要将一个文档链接到一个 CSS 文本文件(比如，`styles.css`)，请在文档头中放置一个`<link>`标签:

```
<link rel="stylesheet" type="text/css" href="styles.css" />
```

让我们回到最初的例子，其中三个标题共享一个样式；该文档现在看起来像这样:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  

<html >  

<head>  

<title>A Simple Page</title>  

<meta http-equiv="content-type"  

    content="text/html; charset=iso-8859-1" />  

<**link rel="stylesheet" type="text/css" href="styles.css" /**>  

</head>  

<body>  

<h1>First Title</h1>  

<p>...</p>  

<h2>Second Title</h2>  

<p>...</p>  

<h2>Third Title</h2>  

<p>...</p>  

</body>  

</html>
```

`styles.css`文件将包含样式定义:

```
h1, h2 {  

  font-family: sans-serif;  

  color: #3366CC;  

}
```

与图像文件一样，您可以根据需要在任意多的页面上重用这个`styles.css`文件。它不仅能节省你的打字时间，还能确保整个网站的标题外观一致。

##### CSS 选择器

每个 CSS 样式定义都有两个组件:定义样式将应用的标签的*选择器*，以及指定样式实际作用的*属性*。在前面的例子中，选择器是`h1`、`h2`，指定样式应该应用于所有的`<h1>`和`<h2>`标签。样式定义的其余部分由属性组成，指定样式应该应用的字体和颜色。在这一节中，我将描述基本的 CSS 选择器类型，并给出每种类型的例子。

***标签选择器***

选择器的最基本形式是我们已经看到的。通过命名特定的 HTML 标记，您可以将样式定义应用于文档中该标记的每一次出现。这通常用于设置整个网站的基本样式。例如，以下内容可用于设置网站的默认字体:

```
body, p, td, th, div, blockquote, dl, ul, ol {   

  font-family: Tahoma, Verdana, Arial, Helvetica, sans-serif;   

  font-size: 1em;   

  color: #000000;   

}
```

这个相当长的选择器是一个标签列表，所有的标签都具有样式定义(字体、大小和颜色)。从理论上讲，`<body>`标签就是所需要的全部(因为所有其他标签都出现在`<body>`标签中，因此会继承它的属性)，但是许多浏览器不能正确地将样式属性带入表格和其他元素中。因此，为了完整起见，我指定了其他元素。

***伪类选择器***

HTML 中的`<a>`标签的格式比大多数其他标签的格式更加通用。通过在`<body>`标签中指定`link`、`vlink`和`alink`属性，您可以为页面中链接的各种状态(分别为未访问、已访问和被点击)设置颜色。CSS 提供了自己的方式来实现这一点，并添加了第四种状态，当鼠标悬停在链接上时会应用该状态。考虑下面的例子:

```
a:link { color: #0000FF; }   

a:visited { color: #FF00FF; }   

a:hover { color: #00CCFF; }   

a:active { color: #FF0000; }
```

这段代码包含四个 CSS 样式定义。每个选择器使用被称为`<a>`标签的*伪类*。第一个是`link`，仅适用于未访问的链接，并指定它们应该是蓝色的。第二个是`visited`，应用于访问过的链接，并使它们变成洋红色。第三个样式定义`hover`覆盖了前两个样式定义，当鼠标移动到链接上时，不管链接是否被访问过，它都会使链接变成浅蓝色。最终的样式定义是当链接被点击时变成红色。因为`active`出现在最后，它覆盖了前三个，所以不管链接是否被访问过，不管鼠标是否在上面，它都会生效。

***班级选择器***

为标签指定样式固然很好，但是如果您想为出现在文档中不同位置的相同标签指定不同的样式，该怎么办呢？这就是 CSS *类*的用武之地。考虑以下样式，该样式使页面中的所有段落文本为蓝色:

```
p { color: #0000FF; }
```

现在，如果你的页面上有一个蓝色背景的侧边栏会怎么样？你不希望边栏中的文本也是蓝色的，因为它是不可见的！您需要做的是为侧边栏文本定义一个类，然后为该类分配一个 CSS 样式:

```
p { color: #0000FF; }   

.sidebar { color: #FFFFFF; }
```

第二个规则使用了一个类选择器，它指示样式应该应用于`sidebar`类的任何标签。句点表示正在命名一个类，而不是一个标签。要创建一段`sidebar`类的文本，您需要向标签添加一个 class 属性:

```
<p **class="sidebar"**>This text will be white, as specified by the   

  CSS style definitions above.</p>
```

现在，如果你的侧边栏有链接会怎么样？默认情况下，它们会像页面中的任何其他链接一样呈现；然而，给标签添加一个`class="sidebar"`属性，它们也会变成白色:

```
<p class="sidebar">This text will be white, <a class="sidebar"   

      href="link.html">and so will this link</a>.</p>
```

这非常简洁，但是如果您想通过用粗体文本显示链接来使它们更加突出呢？将`bold`文本属性添加到`sidebar`类将会使你的整个侧边栏加粗。你需要一个 CSS 选择器来选择侧边栏类的链接。通过结合标签选择器和类选择器，您可以做到这一点:

```
p { color: #0000FF; }   

.sidebar { color: #FFFFFF; }   

a.sidebar:link, a.sidebar:visited { font-weight: bold; }
```

注意，我们还使用了`:link`和`:visited`伪类来指定作为链接的`<a>`标签(与不是链接的`<a name="...">`标签相反)。

还要注意我们的侧边栏链接仍然是白色的——属于侧边栏类的两种样式都适用于我们的侧边栏链接。但是，如果我们在第三种样式中指定了一种不同的颜色，链接将采用这种新的颜色，因为第三个选择器更加具体，CSS 样式是按照增加选择器具体性的顺序应用的。

顺便提一下，将多种样式应用到单个页面元素的过程称为，级联样式表就是由此得名的。

***上下文选择器***

如果你的侧边栏碰巧包含很多链接，那么给每一个`<a>`标签分配`sidebar`类就变得很乏味。使用选择器来选择出现在侧边栏类段落中的任何链接不是很好吗？这就是上下文选择器的作用:基于上下文选择标签，也就是说，基于包含它的标签。

这是新的 CSS:

```
p { color: #0000FF; }   

.sidebar { color: #FFFFFF; }   

p.sidebar a:link, p.sidebar a:visited {   

  font-weight: bold;   

  color: #FFFFFF;   

}
```

这是更新后的 HTML:

```
<p class="sidebar">This text will be white,   

  <a href="link.html">and so will this link</a>.</p>
```

如您所见，上下文选择器提供了一个由空格分隔的选择器列表，这些选择器必须“从外向内”匹配标签在这种情况下，由于链接(由`a:link`或`a:visited`选择器匹配)出现在侧边栏段落(由`p.sidebar`选择器匹配)内，样式应用于链接。

注意，为了保持链接为白色，我们必须将这种颜色添加到链接的 CSS 属性中，因为`sidebar`类的样式不再适用于链接。

***ID 选择器***

类似于类选择器，ID 选择器用于选择一个特定的标签，而不是一组标签。您选择的标签由 ID 属性标识，如下所示:

```
<p id="sidebar1">This paragraph is uniquely identified by the ID   

  "sidebar1".</p>
```

ID 选择器就是前面加一个散列(#)的 ID。因此，下面的样式将使上面的段落变白:

```
#sidebar1 { color: #FFFFFF; }
```

ID 选择器可以与其他选择器类型结合使用。例如，以下样式适用于出现在`sidebar1`段落中的未访问链接:

```
#sidebar1 a:link {   

  font-weight: bold;   

  color: #FFFFFF;   

}
```

存在其他类型的选择器，但是它们没有被广泛使用。一些浏览器(尤其是 Netscape 4)对它们的支持最多也就是漏洞百出。

***CSS 属性***

在本章的例子中，我们已经使用了几种常见的 CSS 属性，如颜色、字体系列、字体大小和字体粗细。在本书的其余部分，我们将使用这些属性-以及更多。

##### 摘要

这一章让你在最基本的层面上体验了 CSS 及其用法。如果你以前没有使用过 CSS，但是对本章讨论的概念有了基本的理解，你应该可以开始使用本书中的例子。本书前几章中的例子比最后几章中的要简单一些，所以，如果你以前没有使用过这种技术，你可以从前几章开始。这些将建立在你在本章中获得的知识的基础上，让你使用 CSS，我希望，享受 CSS。

敬请关注 CSS 选集的下一章[*:101 个基本提示、技巧&黑客*](https://www.sitepoint.com/books/cssant1)——它将很快在 SitePoint 上发布。或者，如果你等不及了，[现在就免费下载前 4 章](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。

## 分享这篇文章