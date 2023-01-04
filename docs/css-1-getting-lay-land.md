# HTML 乌托邦:使用 CSS 设计无表格，第 1 章:了解情况

> 原文：<https://www.sitepoint.com/css-1-getting-lay-land/>

 **##### 上下文中的 CSS

几乎就在第一个图形浏览器(网景导航器的前身)的出现使网络开始普及的时候，图形设计师们就意识到了一个问题。Web 浏览器显示 HTML 文件中存储的信息的方法不在设计者的控制范围内。不，是用户主要负责他们访问的网页如何在他们的系统上显示。

虽然有很多人，包括我自己，认为这是一件好事，但专业设计师们却忧心忡忡。在他们看来，这是一个根本性的缺陷。他们认为，“用户对好的设计一无所知”。如果设计者不能非常精确地控制颜色、字体以及网页上每个设计元素的精确的像素级定位，他们的创作很容易在用户的浏览器中以丑陋的模仿告终。

虽然少数人认为这是新媒体带来的挑战，但大多数习惯于印刷和其他固定布局的设计师完全控制了用户所看到的内容，他们找到了按照自己的意愿改变网络的方法。

为了避免惹怒每一个阅读这本书的设计师，让我赶紧补充一点，我不认为这是一件坏事。当然，设计师比用户自己更了解如何向用户展示内容。

像间距、颜色组合和其他设计元素都会影响可读性和可用性。我的观点与谁应该负责关系不大，而是与设计者或多或少被迫采取的行动有关，以实现至少某种程度的控制。

很快，专业设计人员发现他们可以使用表格来获得对向用户呈现内容的有效控制。通过小心地在表格中的表格中布局表格，他们可以非常精确地定位表格单元格中包含的任何设计元素。这几乎涵盖了一切。

第一个桌面出版风格的网页设计工具 NetObjects Fusion 使设计人员能够以高度的精确度设计页面。它生成复杂的、基于表格的 HTML，这使得网页尽可能接近设计者最初的设想。

我们从未回头。

但是表格并不是用来作为布局工具的，所以虽然它们有一点点效果，但是它们的效率也非常低。我们将在本章的稍后部分探讨使用表格进行布局任务的一些缺点和不足；现在，只要知道每个人，包括使用这些技术的设计师，都非常清楚他们的解决方案是多么的笨拙。

##### CSS 的基本目的

CSS 作为网页设计的标准出现，在很大程度上，是对过度使用过于复杂的表格，在原本没有此目的的介质上强制精确布局的一种反应。虽然这有点过分简化了事实，但也不是不公平的。

在 Web 开发之初的一系列短暂冲突之后，谁应该控制页面或网站的整体外观的问题以设计师成为胜利者而告终。事实上，几乎没有开一枪。毕竟，用户最终最关心的是可用性、可访问性和便利性，而不是设计技术的本质细节。

尽管胜利在望，但设计师们发现自己很难找到非常好的、符合标准的方式来为他们的客户——以及他们客户的用户——提供既有效又高效的优秀设计。

因此，他们被迫主要依靠桌子。

随着混乱的桌子越来越像一个巨大的灌木丛，甚至设计界也变得不安起来。维护一个由六个或更多深度交织的表格组成的网页是一场噩梦。大多数设计师不喜欢在这样的细节层次上处理代码——即使是简单的 HTML 标记。

万维网联盟应运而生，更广为人知的名字是 W3C，它是由蒂姆·伯纳斯·李创立的一个机构，负责监督网络的技术发展。他们认为将网站的内容从其形式(或外观)中分离出来是最合理的解决方案。这将使内容专家——作家、艺术家、摄影师和程序员——能够提供人们到网站来观看、阅读或体验的“素材”。它还将解放设计专家——艺术家、平面设计师和印刷工人——独立于网站内容来决定网站的美学。

结果是 CSS。

##### 为什么大多数(但不是所有)表格都不好

为什么表格作为一种设计机制不是一个好主意？原因有很多，但在这种情况下我们最关心的是:

*   它们会导致加载时间过长。
*   他们鼓励使用低效的“占位图形”,这会进一步降低性能。
*   它们的维护可能是一场噩梦，甚至微小的变化都会“破坏”整个布局。

***表意为长加载时间***

大多数人不知道，Web 浏览器被故意设计成确保每个表作为单个实体下载。因此，在表中包含的所有内容都下载到客户机并可供显示之前，表中包含的任何材料都不会显示。

当考虑到表的原始预期目的时，这是有意义的。表格被设计用来显示…嗯…数据表。每个单元格包含一个与表中其他单元格的值相比较或相关的值。准随机出现的孤立数据比特就不行；桌子是一个单一的整体。

当设计者开始依赖表格来包含网页的全部或大部分内容时，他们也要承担这个设计决策的后果。除了许多用户由于表格一次显示所有内容而经历的明显延迟之外，创建今天的具有嵌套表格的网页布局所需的大量 HTML 代码也会由于页面大小的增加而增加实际的加载时间。几乎可以肯定，基于表格的布局比其他任何单一因素都更能引起用户对长页面加载时间的关注。

避免如此长的加载时间显然是一件好事。

***使用透明图像减慢我们的速度***

即使有了表格作为布局机制，设计者也不能完全达到他们想要的对页面设计的详细控制水平。

例如，有时候，设计师可能需要在表格单元的一部分周围有更多的呼吸空间(这是表格设计所不允许的)。这种精度是无法达到的。

早期，一些设计师想出了创建一个透明的. gif 图像文件的主意——一个没有可见内容的小 GIF 图像。通过创建包含这些透明图像的表格单元格，我们可以在表格中增加额外的垂直和水平空间，这些表格的单元格被设计为彼此紧密相邻。

问题是，给定一个包含几十个甚至几百个图像的表格，并且取决于各种其他因素，透明 gif 对网页的性能影响可能是显著的。然而，更重要的是，这种技术通常会将页面限制为固定的像素大小，并且它会使页面上的图像变得杂乱，而这些图像对于页面的内容没有实际意义。正如我们将在后面看到的，这严重影响了残疾用户理解您的网站的能力。

***维护牌桌是噩梦***

大多数表不好的第三个原因是维护一个复杂的深度嵌套的表数组是一场噩梦。如果您使用 Macromedia Dreamweaver 或 Adobe GoLive 等工具来管理您的站点及其设计，通常可以忽略使设计成为可能的嵌套表格的混乱。

但是，即使这些工具也不是万无一失的，当它们“搞砸”(用一个高度技术性的术语来说)时，修改它们创建的难看的页面可能是一个相当大的挑战。

如果你像大多数设计师一样，并且你不会死于使用 HTML 生成工具，因为你觉得如果你手动编码一切，你会获得更多的控制和理解，那么你会熟悉这个问题。

出现困难是因为，必然地，表格有一组相当复杂的标记，即使它们没有嵌入到其他表格中。当我们有嵌套表时，我们就有了一个明显的丑陋的例子。

与编程编辑器不同，HTML 编辑器通常不强制或支持代码的干净缩进，这使得情况更加复杂。

因此，为给定的表格、行、甚至单元格寻找起点和终点被证明是软件人员所说的“非平凡任务”虽然一个有能力的 HTML 编码者或设计者确实可以让这个问题变得更容易处理，但是不管我们做什么，这个问题都是无法解决的。

***没事的时候用桌子***

桌子不是好东西这一基本原则有一个明显的例外。如果您有表格数据，并且该数据的外观没有其与同一数据集的其他部分相关的适当显示重要，那么表格是合适的。

一般来说(尽管毫无疑问这个规则也有一些例外)，这意味着表的使用应该局限于数字或文本数据的表示，而不是图形、多媒体数据类型、表单或任何其他交互式用户界面组件。

##### CSS 到底是什么？

好了，现在我们已经认识到 CSS 在我们作为设计师的生活中的一个重要作用是将我们从使用表格进行页面布局的苦差事和背叛中解放出来，让我们看看 CSS 到底是什么。

级联样式表标签中最重要的词是中间的一个:Style。只有当我们开始使用相当复杂的样式时，级联问题才变得重要，而“sheet”这个词有时甚至有点误导。因此，即使我们在最广泛和最准确的意义上指的是级联样式表，我们也不会关注这些野兽的级联或片状性质，而是关注它们在决定我们的网页和站点的样式中的作用。样式以规则的形式定义。

这些规则告诉任何理解它们(即支持 CSS)的 Web 浏览器，当它在向
用户交付网页时遇到这些结构时，如何显示特定类型的内容结构。

为了理解样式是如何影响网页外观的，我们需要确保我们理解在没有任何样式规则的情况下网页会发生什么。

图 1.1 显示了客户端(网络浏览器)和网页或网站所在的服务器之间交互的一般过程。请注意，浏览器会自动确定服务器提供的信息如何显示给用户，除非特别说明。换句话说，每个浏览器都有一种默认的方式来显示所有 HTML 标记的内容。因此，`<h1></h1>`标签集中包含的第一级标题将总是使用相对较大的黑色字体显示。使用的“默认”字体可能会因浏览器而异，也会受到用户定义的设置的影响。

![1167_1.1](img/e343856157d73bb287fffbc187bb1694.png)
图 1.1 正常浏览器页面显示行为

图 1.2 描述了当一个特定类型的 HTML 结构存在一个样式规则时会发生什么。该规则会覆盖浏览器对该元素的默认处理，并且样式会接管。即使用户已经为这个元素定义了他或她自己的设置，这些愿望通常也不会被尊重(尽管有一些有趣的例外，我们将在本书的后面讨论)。

![1167_1.2](img/899a9d52d6263a1d0db2087c7e266df5.png)
图 1.2。显示样式规则生效的页面的浏览器

##### CSS 规则的一部分

每种样式，无论是否嵌入到单独的样式表中，都由一个或多个规则组成。图 1.3 显示了一个 CSS 规则，所有部分都被标记了。

每条规则都有两个部分:

1.  定义应用规则的 HTML 元素的选择器，以及
2.  一个或多个属性的集合，描述文档中与选择器匹配的所有元素的外观。

![1167_1.3](img/7379d38489491600c52ab3fea93da4eb.png)
图 1.3。CSS 规则的一部分

每个属性由一对用冒号分隔的值组成。该对中的第一项定义了被修改的特定属性。第二项描述属性的值。每个属性-值对后面必须跟一个分号，有一个例外:最后一个属性后面的分号是可选的，可以省略。然而，在本书中，我们将总是添加这个可选的分号。我鼓励你也养成这个习惯，因为训练自己总是添加分号比记住什么时候需要什么时候不需要要容易得多。它还使向现有样式规则添加属性变得更加容易。

这里有几个越来越复杂的 CSS 规则的例子，这些规则的各个部分都有标识，这样你就可以在脑子里清楚地记住这些语法。本质上，这是你为了掌握 CSS 而必须学习的唯一真正的语法问题，所以它很重要！

```
h1 {    

color: red;    

}
```

选择器 h1 表示此规则适用于文档中的所有 h1 标题。被修改的属性的名称是 color，它适用于字体颜色。我们希望颜色属性采用的值是红色。第 7 章和第 9 章详细探讨了 CSS 中的字体和颜色。

```
p {    

font-size: 14px;    

color: green;    

}
```

选择器 p 表示样式规则应该应用于文档中的所有段落。规则中有两个属性名称-值对。第一个是 fontsize，它将文档中所有段落的字体大小设置为 14 像素。像素是屏幕上的一个点，是 CSS 中最常用的度量单位。参见第 3 章，了解 CSS 中的这个和其他度量问题的解释。第二个属性是 color，被设置为绿色。该规则的结果是，文档中的所有段落将以 14 像素高的绿色字体显示。

```
p {    

font-family: 'New York', Times, serif;    

}
```

同样，这条规则处理段落，正如 p 选择器所证明的那样。这一次，选择器影响用于显示文本的字体系列。这个例子的新特点是它包含了 font-family 属性的值列表，其中一个值用引号括起来。

font-family 属性是少数几个 CSS 属性之一，您可以为其分配一系列可能的值，而不是一个固定的值。使用列表时，必须用逗号分隔各个成员。在这种情况下，fontfamily 属性列表告诉浏览器使用纽约作为字体，如果用户的计算机安装了它。如果没有，它指示浏览器使用时间。如果用户的系统中没有这两种字体，浏览器就会被告知默认使用 serif 类型的字体。同样，这个主题在第 7 章和第 9 章中有更深入的讨论。

每当 CSS 规则中的属性值名称包含空格时(如名为“New York”的字体)，您必须将该值放在引号中。许多设计者使用单引号的原因有很多，不仅仅是因为它们更容易输入，但是你可以使用单引号或者双引号。

##### CSS 规则的类型

有几种可能的方法来分类和思考 CSS 规则。首先，存在规则定义什么类型的样式属性的问题。第二，需要描述规则影响的 HTML 元素的类型。最后，还有样式是“内联的”、“嵌入的”还是“外部的”的问题

让我们简要地看一下这些分类，以便在开始详细研究 CSS 规则的实际使用之前，您对 CSS 规则的组织有一个很好的概述。

***CSS 规则可以影响哪些属性？***

CSS 规则可以包括影响网站上信息呈现的几乎每个方面的属性。附录 c 中给出了这些属性的完整参考。

***CSS 能影响哪些元素？***

换句话说，这个问题问的是“CSS 规则如何明确地针对网页上的一条信息进行特殊呈现？”CSS 允许设计者影响所有的段落，但是你如何将这种影响限制在某些特定的段落中呢？这可能吗？

不出所料，答案是肯定的。通过选择器使用的各种组合，设计者可以变得非常具体，知道在什么情况下实施样式规则。例如，您可以分配规则，使其影响:

*   特定类型的所有元素
*   分配给一个公共组或类的特定类型的所有元素
*   包含在特定类型的其他元素中的特定类型的所有元素
*   all elements of a specific type that are both contained within another specific
    element type and assigned to a common group or class
*   all elements of a specific type only when they come immediately after an element
    of some other type
*   只有分配了唯一 ID 的特定类型的特定元素

第 3 章详细讨论了可以用来实现这种精确定位的所有 CSS 选择器。

##### CSS 样式可以在哪里定义？

最后，您可以在三个位置中的任何一个位置定义 CSS 样式，并结合一个网页。

***内联 CSS***

首先，您可以完全在一个适当的 HTML 标签中定义一个样式。这种样式被称为内联样式，因为它是根据文档的 HTML 代码定义的。几乎所有的 HTML 元素都可以分配一个样式属性。例如，要使文档中的第二级标题以红色文本和全部大写字母显示，您可以编写如下代码:

```
<h2 style="color: red; text-transform: uppercase;">An Unusual     

Heading</h2>
```

如果你遵循这本书里的建议，你就不会使用很多内联样式。正如您将了解到的，将内容与表示分离是 CSS 的一大优势，而将样式直接嵌入 HTML 标签违背了这一目的。内联样式主要用于快速原型制作，即将样式属性快速应用于特定元素以试验效果，然后在嵌入或外部样式规则中赋予属性更永久的位置。

***嵌入 CSS***

在嵌入式样式中指定样式属性可能是当今最常见的方法，尤其是在初学网页设计或刚刚学习 CSS 设计技术的人当中。这不是我最喜欢的，但它确实有一个独特的优点，就是容易处理，所以你会在本书中不时看到它的使用。

要在网页中嵌入样式表，您需要在文档的 HTML 头部放置一个样式块，如下所示:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

"https://www.w3.org/TR/xhtml11/DTD/xhtml1-transitional.dtd">     

<html >     

<head>     

<title>CSS Style Sheet Demo</title>     

<meta http-equiv="Content-Type"     

content="text/html; charset=iso-8859-1" />     

**<style type="text/css">     

<!--     

h1, h2 {     

color: green;     

}     

h3 {     

color: blue;     

}     

-->     

</style>**     

</head>     

...
```

样式块中包含的 CSS 规则适用于当前文档的所有指定部分。在这种情况下，第一个规则指示浏览器以绿色显示所有 1 级和 2 级标题(h1、h2)。第二个规则以蓝色显示所有 3 级标题(h3)。

注意就在`<style>`标签内部的 HTML 注释分隔符(`<!-- -->`)。

这些阻止了不支持 CSS 的古代浏览器将样式规则解释为文档内容并在浏览器窗口中显示它们。所有支持 CSS 的浏览器都会忽略注释分隔符。尽管今天省略这些符号可能是安全的(或者几乎是安全的),因为很少古代浏览器仍在使用，但包含它们也没有坏处。我建议你这样做，只是因为这是好的形式。

关于 style 元素的语法，要注意的第二件事是，每个规则都从新行开始，规则中指定的每个属性都缩进在自己行的大括号中。严格地说，这不是必需的，但是这是一个很好的经验法则，可以提高代码的可读性，特别是如果您习惯了 JavaScript 代码的外观。

##### 外部 CSS

最后，你可以在一个完全独立于网页的文件中定义 CSS 规则。然后，您可以通过在想要实现该文件中包含的样式的任何网页的头部包含一个`<link>`标签来链接到该文件。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

"https://www.w3.org/TR/xhtml11/DTD/xhtml1-transitional.dtd">     

<html >     

<head>     

<title>CSS Style Sheet Demo</title>     

<meta http-equiv="Content-Type"     

content="text/html; charset=iso-8859-1" />     

**<link rel="stylesheet" type="text/css" href="corpstyle.css" />**     

</head>     

...
```

在这个例子中，文件 corpstyle.css 包含一组外部样式，这些样式已经链接到这个页面。这个文件的内容可能看起来像这样:

```
h1, h2 {     

  color: green;     

}     

h3 {     

  color: blue;     

}
```

这是我个人对我们应该处理所有 CSS 使用的方式的偏好，有几个原因。

首先，这是设计师可以用来在网页中插入样式的三种基本方法中“锁定”最少的一种。如果您定义了一个外部样式表文件，您可以将它应用到站点上任意多的页面上，只需从您希望使用它的每个页面链接到样式表即可。对出现在站点每个页面上的样式进行更改变成了一件简单的事情。css 文件。如果你使用嵌入样式，或者更糟糕的是，内联样式，如果你想使用它们，你必须将它们复制并粘贴到其他文档中。

其次，与第一个优点密切相关的是，这种方法是确保 CSS 样式可维护性的最简单的方法。如果您在外部文件中定义了站点的所有样式，那么在单个文件中进行一次编辑就可以实现站点范围的样式更改。使用该样式表的所有页面将在这次更改后立即显示新的样式。

对于其他技术，您必须记住哪些样式是在哪些页面上定义的，或者使用搜索机制来帮助您处理分散的样式规则。

第三，外部样式表被浏览器视为单独的文件。当浏览器使用相同的样式表导航到新页面时，不需要再次下载外部样式表。因此，使用外部样式的页面加载速度更快。

最后，但同样重要的是，外部样式表更专业。通过使用它们，你展示了对我刚刚提出的前两个问题的重要性的理解，并且你使得讨论它们、与同事分享它们、分析它们的效果，以及，总的来说，使用它们就好像它们是网站设计的一个重要部分，而不是事后的想法。

##### 何必呢？

好了，现在你已经对 CSS 是什么有了一个基本的了解，为什么我们有它，为什么我认为它是网页设计师应该采用的一个重要技术，证据在哪里呢？让我们看一个小的，但不是过于简单的网页的例子(见图 1.4)。

![1167_1.4](img/6fe00cbf083072b51308411be0980e94.png)
图 1.4。演示嵌入样式的示例网页

使用嵌入的 CSS，这是生成页面的 HTML。看，妈妈，没有桌子！不要让代码的复杂性吓倒你——到第三章结束时，你应该能够在没有我的帮助下推断出大部分代码的意思。现在，您可以从该书的网站下载代码档案，并在浏览器中惊叹其结果。这个文件叫做 ch1sample.html。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"      

"https://www.w3.org/TR/xhtml11/DTD/xhtml1-transitional.dtd">      

<html >      

<head>      

<title>Basic 3-Column Sample Page</title>      

<meta http-equiv="Content-Type"      

content="text/html; charset=iso-8859-1" />      

<style type="text/css">      

<!--      

body {      

  background-color: teal;      

  margin: 20px;      

  padding: 0;      

  font-size: 1.1em;      

  font-family: verdana, arial, helvetica, sans-serif;      

}      

h1 {      

  font-family: verdana, arial, helvetica, sans-serif;      

  margin: 0 0 15px 0;      

  padding: 0;      

  color: #888;      

}      

h2 {      

  font-family: verdana, arial, helvetica, sans-serif;      

  margin: 0 0 5px 0;      

  padding: 0;      

  font-size: 1.1em;      

}      

p {      

  font-family: verdana, arial, helvetica, sans-serif;      

  line-height: 1.1em;      

  margin: 0 0 16px 0;      

  padding: 0;      

}      

.content>p {      

  margin: 0;      

}      

.content>p+p {      

  text-indent: 30px;      

}      

a {      

  color: teal;      

  font-family: verdana, arial, helvetica, sans-serif;      

  font-weight: 600;      

  text-decoration: none;      

}      

a:link {      

  color: teal;      

}      

a:visited {      

  color: teal;      

}      

a:hover {      

  background-color: #bbb;      

}      

/* All the content boxes belong to the content class. */      

.content {      

  position: relative;      

  width: auto;      

  min-width: 120px;      

  margin: 0 210px 20px 170px;      

  border: 1px solid black;      

  background-color: white;      

  padding: 10px;      

  z-index: 3;      

}      

#navleft {      

  position: absolute;      

  width: 128px;      

  top: 20px;      

  left: 20px;      

  font-size: 0.9em;      

  border: 1px dashed black;      

  background-color: white;      

  padding: 10px;      

  z-index: 2;      

}      

#navright {      

  position: absolute;      

  width: 168px;      

  top: 20px;      

  right: 20px;      

  border: 1px dashed black;      

  background-color: #eee;      

  padding: 10px;      

  z-index: 1;      

}      

-->      

</style>      

</head>      

<body>      

<div class="content">      

  <h1>Getting the Lay of the Land</h1>      

  <p>We can look at Cascading Style Sheets (CSS) from a number of      

     contextual perspectives. I prefer to view them as a      

     correction to a fundamental mistake that was made at the      

     beginning of Web Time, back in the old days of the mid-1990's      

     when Tim Berners-Lee and a subsequent phalanx of Web builders      

     first envisioned the beginnings of the Web.</p>      

  <p>What was that mistake?</p>      

</div>      

<div class="content">      

  <h2>CSS in Context</h2>      

  <p>Almost as soon as the Web became popular, graphic designers      

     began noticing what they saw as a fundamental flaw: the      

     method by which a Web browser displayed information in HTML      

     files was not within the designers' control. No, it was the      

     users who were in primary charge of how the Web pages they      

     visited would appear on their systems.</p>      

</div>      

<div class="content">      

  <h2>Keep Adding Content</h2>      

  <p>You can see that as you keep adding content to this page, it      

     adds nicely boxed and centered material down the center of      

     the page.</p>      

</div>      

<div id="navleft">      

  <h2>Some Links</h2>      

  <p>      

    <a href="http://www.danshafer.com/"      

title="Dan Shafer's Personal Web Site">Dan's Home Page</a><br/>      

    <a href="https://www.sitepoint.com/"      

title="SitePoint Home Base">SitePoint Home</a><br/>      

    <a href="http://www.sitepointforums.com/"      

title="Discussion Board for This Book">Discuss This Book</a><br/>      

    <a href="" title="">Fake Link One</a><br/>      

    <a href="" title="">Nothing Here</a><br/>      

    <a href="" title="">Links Nowhere</a><br/>      

    <a href="" title="">Fake Link Four</a><br/>      

    <a href="" title="">Fifth Fake Link</a><br/>      

  </p>      

</div>      

<div id="navright">      

  <h2>Why CSS is Better</h2>      

  <p>Style sheets allow you to separate content from its      

     presentation, which leads to pages that are more easily      

     reproduced as templates for other pages and to vastly easier      

     maintenance. Smaller file sizes, fewer place-holder graphics,      

     and faster load times are some of the other benefits of CSS.</p>      

  <p>If you have other ideas on this subject,      

     <a href="mailto:dan@danshafer.com">drop me an email</a> and      

     let's talk about it!</p>      

</div>      

</body>      

</html>
```

##### 摘要

现在，您应该了解 CSS 出现的历史和技术背景，它旨在解决什么主要问题，以及它在表层是如何工作的。您还应该知道为什么表格作为网页布局设备不是一个好主意，尽管它们有其他完全有效的用途。

此外，你可以确定 CSS 规则的组成部分，以及至少三种对 CSS 样式规则进行分类的方式。

第 2 章更深入地探讨了围绕 CSS 的潜在问题。它澄清了你可能对这项技术的一些误解，并描述了你必须考虑的一些重要问题，因为 Web 浏览器使用(或不使用)CSS 规则的方式。

阅读第 3 章:挖掘地表下的东西。

## 分享这篇文章**