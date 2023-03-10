# Dreamweaver 8 符合标准！

> 原文：<https://www.sitepoint.com/dreamweaver-8-standards/>

如果你正在阅读这篇文章，你可能已经对“Web 标准”这个主题产生了兴趣，并且对使用 [Dreamweaver](https://www.sitepoint.com/blog/) 构建的网站中标准的应用感到好奇。

也许您已经了解了 Web 标准，但是您不确定如何使用 Dreamweaver 创建符合标准的代码。或者，您可能是一名 Dreamweaver 用户，希望遵守 Web 标准，更广泛地使用 CSS，并生成更易于访问的文档。无论哪种方式，本文都有您需要的答案:它将向您展示如何使用 Dreamweaver 工作到 Web 标准。

这篇文章实际上摘自 SitePoint 的新版本，Rachel Andrew 的《使用 Dreamweaver 8 构建你自己的符合标准的网站》。这本书一步一步地向你展示了如何使用 XHTML 严格标记和 CSS 开发一个符合标准的网站。有了这本书，您可以使用 Dreamweaver 8 的广泛功能，快速成功地开发符合 Section 508 法规的有吸引力的功能性网站，并通过具有 3a 评级的 WAI 可访问性指南。和往常一样，你可以[下载 pdf 格式的信息](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，如果你想离线阅读的话。

正如我们将在本章中发现的，有很好的商业理由来说明为什么网站应该被开发来满足网络标准。决定采用 Web 标准不应该是为了赶时髦，或者跟上最新的 Web 开发潮流。这是关于生产高质量的工作，并且知道你的开发方法将有益于你的客户或雇主以及网站访问者。

##### 定义的 Web 标准

由于我们将在本书中关注 Web 标准，所以让我们花点时间来澄清一下我们到底在谈论什么。

Web 标准是指导开发语言在 Web 上的使用的规范，由万维网联盟(或 W3C)制定。这些规范涵盖了 HTML、XHTML 和 CSS 等语言，以及一系列其他语言，如 MathML，这是一种用于表示数学公式的标记语言，如果您有特殊需要，可能会遇到这种语言。W3C 还通过网页可访问性倡议(WAI)发布了网页内容可访问性指南(WCAG)-解决网页可访问性的建议。

*注:获取 Spec，直接！*

您可以在 W3C 网站上阅读这些规范和建议，尽管它们有时有点难懂。

*   [HTML 4.01](https://www.w3.org/TR/html4/)
*   [XHTML 1.0](https://www.w3.org/TR/xhtml1/)
*   [CSS 1](https://www.w3.org/TR/CSS1/)
*   [CSS 2.1](https://www.w3.org/TR/CSS21/)
*   [WCAG 1.0](https://www.w3.org/TR/WAI-WEBCONTENT/)

在本书中，我们将使用 XHTML 1.0、CSS 1 和 2.1 以及 WCAG 1.0 规范和建议，尽管您会很高兴知道我们不会过多阅读实际的 W3C 文档本身！

##### 谁需要 Web 标准？

你可能会有一个模糊的概念，认为网络标准是一件好事，但是许多网站——包括许多知名网站——并不符合网络标准，它们看起来确实管理得很好。那么，我们为什么要努力遵守 Web 标准呢？这样做有什么真正的好处吗？谁需要 Web 标准，谁需要注意 W3C 规范和建议？

***网页设计师和开发者***

最需要担心网络标准的是像我们这样的人:设计和开发网站的人。我们花在学习如何开发 Web 标准上的时间会有回报吗？

*更清晰的标记使错误修复更快*

如果您使用 W3C 验证器来验证您的页面，至少您会知道无效标记不是您可能遇到的任何页面显示错误的原因。有时，验证页面并修复发现的错误的过程可以清除由元素未正确关闭或标记拼写错误导致的显示问题。

即使验证文档不能解决问题，至少您知道问题存在于有效的文档中。一旦你知道这个问题不是一个错误，你就可以开始考虑其他问题，比如 CSS 在不同浏览器中的不同实现。

*符合可访问性要求更加容易*

如果您创建了有效的 XHTML 标记，并且确保了文档在语义上是正确的，并且将文档的内容和表示分离开来(所有这些我们都将在本章中讨论)，那么您已经在 WCAG 1.0 中概述的许多可访问性检查点上取得了相当大的进步。同样重要的是要认识到无障碍环境不仅仅是为残疾人设计的。一个可访问的网站可以被许多不同的设备读取，包括搜索引擎索引器和“资源有限”的设备，如移动电话和 PDA，它们没有处理能力来处理混乱、不标准的标记。

*向前兼容性*

如果您只考虑新开发的页面在当前几种浏览器中的外观，您如何确定它在下一个新的浏览器中显示良好？新的浏览器可能会显示你的页面很糟糕，让你在接到投诉时手忙脚乱地寻找和解决问题。如果您依赖于特定于某些浏览器的标签，或者已经从规范中完全删除了，那么您将自己暴露在这个问题面前。

遵从 Web 标准不会彻底根除这个问题；然而，遵循标准使得你的设计不太可能出现严重的失败，因为浏览器制造商现在也遵循这些标准。虽然他们可能偶尔会曲解规范的某些部分，但他们不太可能完全停止支持它。如果最坏的事情发生了，一个新的浏览器对你的基于标准的网站产生了奇怪的影响，修复它可能比修复一个不符合标准的网站更容易。如果你遇到一个问题，它可能会影响到其他标准投诉网站。网络社区的精英们将会找出解决方案并撰写文章来解释他们的解决方案。而且，正如我们已经讨论过的，在一个兼容的文档中修复 bug 要比在一个不兼容的文档中容易得多。

*更简单的重新设计*

你是否曾经不得不重新设计一个网站，从网站上撕下文字，然后从头开始？你有没有见过被字体标签和小表格单元弄得乱七八糟的标记，以至于从头开始更容易？我知道我有，这是一个缓慢的过程，可以咀嚼了大量的时间和金钱致力于网站重新设计。

将文档的内容和它的表现分离开来，不仅会给你一种标准遵从的温暖:这意味着下次有人不得不重新设计网站时，他们不需要从 Web 文档中复制所有的文本。所有的网站文本都将使用 semantic (X)HTML 进行标记，所有的表示性信息——网站所有者想要更改的内容——都将存储在一个可以轻松替换的 CSS 文件中。

有些客户甚至不会等到重新设计之后才开始要求你做出改变:他们会等到你几乎完成了他们庞大的网站，然后要求你“只需将那一列从左边切换到右边。”有了一个页面布局由 CSS 控制的符合标准的站点，您可以轻松地移动页面元素，而不需要在许多页面上处理复杂的表格结构。这使得页面布局的更改相对简单。

结构和表现的分离也可以更容易地提供附加的功能，例如为喜欢以这种方式使用网站的访问者提供高对比度、低图形版本的网站。当您可以简单地交换样式表时，为什么要为所有页面创建单独的纯文本版本呢？

***浏览器厂商***

访问我们网站的浏览器制造商确实注意到了网络标准。过去，浏览器制造商在基本语言中添加他们自己的专有标签和属性。但是现在，他们比以往任何时候都更加努力遵守标准，当然，对于最新的浏览器，他们正在尝试显示规范中描述的(X)HTML 和 CSS。

在可预见的未来，网络浏览器将试图呈现哪怕是最糟糕的标记、无效的代码，因为如果它们不这样做，成千上万写得很糟糕的网站将显示为一片混乱——而一般公众很可能会责怪浏览器，而不是网站设计者。然而，其他设备不具备桌面计算机的渲染能力，更依赖于它们遇到的标记的标准遵从性。

***创作工具制造商***

创作工具制造商——如开发 Dreamweaver 的 Macromedia——必须像 Web 设计人员一样遵循 Web 标准，因为他们的客户越来越要求这些创作环境输出有效的标记。传统上，可视化开发环境因为创建混乱、无效的标记而受到负面评价；然而，领先的可视化开发环境的较新版本将标准遵从性和可访问性特性作为主要卖点。制造商肯定在倾听市场的需求，并对此做出回应。

***网络用户***

我们设计的网站的用户也受益于我们采用的网络标准，即使他们没有意识到这一点！也许他们无意中使用了专门为在最流行的浏览器中良好显示而开发的网站。如果这些用户切换到不同的浏览器，他们可能会发现他们不再享受如此美好的在线体验，因为这些网站使用的专有标记在新浏览器中无法工作。一个符合标准的站点有更大的机会在所有的浏览器上运行良好，包括那些在你开发站点时已经存在的浏览器，以及在站点生命周期后期将要推出的新浏览器。

此外，按照可访问性建议开发的网站很可能会被那些觉得浏览网页是一种令人沮丧的体验的用户所访问。网络应该为视力受损或其他残疾的用户提供更方便的购物、阅读和研究的机会。它不应该用使用专有标记的网站或其他技术来挫败他们，这些技术可以有效地将任何不以“常规”方式使用“常规”浏览器的人拒之门外。

##### 使用网络标准

我们如何确保正确使用这些网络标准？符合标准需要什么？

首先，我们需要符合规范。这意味着我们应该只使用规范中包含的那些元素和属性，避免浏览器制造商引入的专有元素，如 Internet Explorer 的`<marquee>`标签和 Netscape 的`<blink>`标签。我们还应该避免使用出现在早期规范(如 HTML 3.2)中的元素，这些元素在我们处理开发到后来规范的文档时已经被删除了。

##### 创建有效的 XHTML 文档

我们将在整本书中使用 XHTML，所以我们将遵循 W3C 的 XHTML 1.0 推荐标准(在 W3C 的说法中，“推荐标准”是一种规范)。XHTML 基本上是 HTML 的最新版本，旨在取代 HTML 成为网页的标记语言。虽然它是 HTML 的 XML 的重新表述，但是除了一些我们将在第 3 章“XHTML 和语义”中讨论的小差异之外，XHTML 和 HTML 几乎是相同的。

您可以通过 Dreamweaver 的“新建文档”对话框(“文件”>“新建...”)创建 XHTML 文档。确保在“类别”列表中选择了“基本页面”，然后从出现的基本页面列表中选择“HTML”，如图 1.1“在 Dreamweaver 中创建新的 XHTML 文档”所示然后，您可以从文档类型(DTD)下拉列表中选择一个 XHTML 选项。

单击“创建”将创建新文档。通过单击文档窗口顶部的“代码”按钮，切换到“代码”视图，查看简单 XHTML 文档中包含的确切内容。图 1.2“在代码视图中显示新的 XHTML 文档”说明了这一点

图 1.1。在 Dreamweaver 中创建新的 XHTML 文档。
![1492_1.1](img/8fcf513748959d3cab55b3b101742de3.png)

图 1.2。在代码视图中显示新的 XHTML 文档。
![1492_1.2](img/afd5152d7766898461096d4c9655464e.png)

文档的第一行看起来会像这样:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

这被称为*文档类型声明*，或 DOCTYPE。正如你可以很容易地从它的名字推断出的，DOCTYPE 声明了你的文档是什么——你正在工作的是哪个(X)HTML 规范。在这个例子中，我们使用的是 XHTML 1.0 Transitional，Dreamweaver 8 的默认版本。过渡部分告诉我们关于我们正在使用的 XHTML 版本的其他信息。XHTML 1.0 有三种风格:“严格、过渡和框架集”。默认情况下，Dreamweaver 使用过渡文档类型，如果在文档中插入框架，则使用框架集。

如你所料，XHTML Strict 是 XHTML 最严格的形式。XHTML Strict DOCTYPE 如下所示:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" 

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
```

如果您使用的是严格的 DOCTYPE，那么您不能在文档中使用任何不赞成使用的*元素(标签)或属性；也不能用框架。不推荐使用的元素是那些在 XHTML 的未来版本中被标记为删除的元素。许多不赞成使用的元素控制页面的外观，执行 CSS 可以处理的各种功能。严格文档类型和过渡文档类型之间的主要区别在于，对于严格文档类型，您在可以包含在文档中的表示属性和元素方面受到更多的限制。*

*注意:在 Dreamweaver 中使用 Strict DOCTYPE*

Dreamweaver 在遵守标准方面并没有尽可能的小心。如果您使用 Strict DOCTYPE，请特别注意验证您的文档并替换任何无效的属性。通常，用 CSS 替换它们会非常容易。

Frameset DOCTYPE 支持使用框架，如果您在文档中包含任何框架，Dreamweaver 将自动使用它。然后，框架集页面将引用至少两个其他 HTML 页面，这些页面可以使用他们喜欢的任何 DOCTYPE。框架集文档类型如下所示:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" 

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

HTML 4.01 提供了同样的三种文档类型——过渡、严格和框架集——其工作方式与上述 XHTML 文档类型完全相同。如果您使用其中的一种，您需要用 HTML 而不是 XHTML 来标记您的文档。在本书的后面，当我们开始创建我们的网站时，我们将探索 HTML 和 XHTML 之间的区别。

***验证您的文档***

如何确保用 HTML 或 XHTML 创建的文档是有效的，并且符合规范？通过图 1.3“使用 W3C 验证器”中所示的 W3C 验证器运行它

要使用验证器，您可以输入您想要验证的页面的 URL(如果它在 Web 上运行)，或者您可以从您的计算机上传文件。

图 1.3。使用 W3C 验证器。
![1492_1.3](img/f69970225b4afb22f3a01cb1778b67d7.png)

一旦您告诉 W3C 验证器在哪里可以找到您的(X)HTML，单击 Check 按钮。将显示一个页面，宣布您的页面有效的喜讯，或者提供您可以在重新检查页面有效性之前解决的错误列表。如果您已经在 Dreamweaver 中创建了一个 XHTML 文档，那么您应该很少需要修复错误；然而，在本书的后面，我们将会看到一些在我们建立网站时可能会出现的更常见的错误。

为了帮助您在开发过程中确保页面的有效性，Dreamweaver 提供了内置的验证器。要运行验证程序，请选择“文件”>“检查页面”>“验证标记”,或单击“验证标记”按钮。验证结果出现在窗口的底部。如图 1.4“Dreamweaver 中的验证程序”所示，您可以选择验证当前文档、整个本地站点或站点中的选定文件。

图 1.4。Dreamweaver 中的验证程序。
![1492_1.4](img/44c02fcd8c7b8ed17fb21cd6992a6c4e.png)

虽然这个验证器提供了有用的检查，但是我总是在文档在线发布之前在 W3C 的在线验证器上检查我的文档。Dreamweaver 验证器不能验证任何使用服务器端语言(如 PHP 或 ASP)动态生成的标记。所以，如果你用这种方式生成页面或者部分页面，你肯定需要在上传页面后使用在线验证器。

***使用有效的 CSS***

当我们建立我们的站点时，我们会发现 CSS 取代了 HTML 中所有不推荐使用的表示元素，并且为有趣的设计想法增加了大量的空间，而这些想法仅仅使用 HTML 是不可能的。CSS 也是一个 Web 标准，W3C 已经开发了规范，我们可以根据这些规范来验证我们的 CSS 代码，就像我们对(X)HTML 所做的那样。

对于 CSS，您有三个验证选项:指向实时服务器上的文件(CSS 文件或嵌入 CSS 的 HTML 页面)，从您的计算机上传 CSS 文件，或将 CSS 直接粘贴到文本区域。与(X)HTML 验证器一样，结果要么是一条祝贺消息，要么是一个错误列表，供您在重新验证样式表之前修复。Dreamweaver 不提供内置的 CSS 验证器。

***验证可访问性***

当设计一个网站时，设计者和开发者会被网页在一个或多个浏览器中的显示方式所吸引；我们可以忘记，对许多人来说，获得内容才是最重要的。许多网络用户使用某种辅助技术，比如屏幕阅读器，它可以大声朗读网页上的文字，或者有残疾，这使得我们大多数人使用网络浏览器的图形显示方式非常困难，如果不是不可能的话。

W3C 通过其网站可访问性倡议(WAI)提供了一些建议，我们可以遵循这些建议来确保我们的网站对这些用户是可访问的；因此，我们应该检查我们的网站是否符合 WCAG 1.0 的建议。正如我们将在第 7 章“可访问性”和第 8 章“构建网站”中看到的，验证 Web 文档的可访问性比检查文档的有效(X)HTML 和 CSS 要复杂得多。对于 WCAG 1.0 建议的不同检查点，并不总是提供“是”和“否”的答案。

Dreamweaver 包含一个可访问性验证程序，它可以从“报告”对话框(“站点”>“报告...”)中运行，如图 1.5“从报告对话框中运行可访问性报告”所示。选中可访问性复选框，然后单击运行。

图 1.5。从“报告”对话框运行辅助功能报告。
![1492_1.5](img/72c35d78b3c83cbcfb613103eb80cf21.png)

显示在结果面板中的报告将包括诸如“颜色并不重要”之类的注释(出现在图 1.6 中，“UsableNet 辅助功能参考显示在参考面板的结果选项卡中。”);这涉及到一个检查点，它建议文档中颜色的使用不应该是用户理解页面的关键。举例来说，如果你在你的站点上传达一篇文章的状态的唯一方式是通过彩色编码的图标，那么你就通过不了这个检查点。在这种情况下，无法区分颜色的用户将无法访问您的页面。如果同时使用颜色编码和文本状态注释，您就可以通过检查点。当然，自动验证器没有办法知道您采用了哪种方法，所以您需要自己手动检查并决定您是否通过了检查点。而且，要以一种明智的方式做到这一点，你需要理解每一点的含义。

也就是说，Dreamweaver 可以提供帮助:在“结果”面板的“参考”选项卡中，您可以找到图 1.6 中描述的 UsableNet 辅助功能参考，“UsableNet 辅助功能参考显示在“参考”面板的“结果”选项卡中。”，它解释了检查点并提供了检查站点是否通过每个检查点的方法。右键单击任何检查点并选择更多信息…以显示该检查点的说明。

图 1.6。“参考”面板的“结果”选项卡中显示的 UsableNet 辅助功能参考。
![1492_1.6](img/d087fbe3aa783360ae14714a39e79b06.png)

还有在线可访问性验证器，其中最流行的是 WebXACT(以前称为 Bobby)，Cynthia 说。这些都伴随着与 Dreamweaver 验证程序相同的附带条件，因为这些系统提供的结果需要解释:它们不能给你一个“是”或“否”的答案。

***应用语义文档结构***

我们对 Web 和可访问性标准的坚持意味着采用语义文档结构。语义文档使用 HTML 标签是为了它们的含义，而不是它们的外观。例如，语义文档会使用`<h1>`、`<h2>`和类似的标签来标记页面的标题。如果我们不想显示这些标题的默认外观，我们可以使用 CSS 很容易地改变它。类似地，使用无序列表(`<ul>`)标记来标记链接列表比用换行符分隔这些链接更可取。如果我们希望链接列表没有项目符号，我们可以使用 CSS 来实现这个效果。

我们将在第 3 章“XHTML 和语义”中更深入地探讨这个问题；然而，这里有一个简单的例子。如果我将这一章的一个部分标记为 XHTML 文档，我可能会得到如图 1.7 所示的结果，“将这一章标记为 XHTML”

图 1.7。将章节标记为 XHTML。
![1492_1.7](img/8bc856403e330fc9971bb0f440b7c646.png)

页面的标记可能是这样的:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  

<html >  

<head>  

<title>Untitled Document</title>  

<meta http-equiv="Content-Type" content="text/html;  

    charset=iso-8859-1" />  

<style type="text/css">  

<!--  

.heading {  

  font-size: 24px;  

  font-weight: bold;  

}  

-->  

</style>  

</head>  

<body>  

<p class="heading">Web Standards Defined</p>  

<p>As we'll be concerned with Web standards throughout this book,  

  let's take a moment to clarify exactly what we're talking  

  about.</p>  

<p>Web standards are specifications that direct the use of  

  development languages on the Web, and are set by the  

  <a href="https://www.w3.org/">World Wide Web Consortium (or  

  W3C)</a>. These specifications cover languages such as HTML,  

  XHTML, and CSS, along with a range of other languages, such as  

  MathML, a markup language designed to represent mathematical  

  equations, that you might come across if you have a specific  

  need. The W3C also publishes the Web Content Accessibility  

  Guidelines (WCAG)&mdash;recommendations that address the  

  accessibility of Web pages&mdash;via the Web Accessibility  

  Initiative (WAI).</p>  

<p>You can read these specifications and recommendations at the  

  W3C site, though they're a little heavy going at times.</p>  

<p>HTML 4.01: <a href="https://www.w3.org/TR/html4/">  

  https://www.w3.org/TR/html4/</a><br />  

  XHTML 1.0: <a href="https://www.w3.org/TR/xhtml1/">  

  https://www.w3.org/TR/xhtml1/</a><br />  

  CSS 1: <a href="https://www.w3.org/TR/CSS1">  

  https://www.w3.org/TR/CSS1</a><br />  

  CSS 2.1: <a href="https://www.w3.org/TR/CSS21/">  

  https://www.w3.org/TR/CSS21/</a><br />  

  WCAG 1.0: <a href="https://www.w3.org/TR/WAI-WEBCONTENT/">  

  https://www.w3.org/TR/WAI-WEBCONTENT/</a></p>  

<p>In this book, we'll use the XHTML 1.0, CSS 1 and 2.1, and WCAG  

  1.0 specifications and recommendations, although you'll be glad  

  to know that we won't be doing too much reading of the actual  

  W3C documents themselves!</p>  

</body>  

</html>
```

由于许多设计者现在已经习惯于使用 CSS 来设计文本，我们经常看到被标记为段落的文本(例如，它出现在`<p>`和`</p>`标签之间)，但是使用 CSS 被设计为标题。为了在语义上正确，我应该使用一个 h1 元素(因为它表示一个标题)，然后使用 CSS 使它根据需要显示。

看一下不同 W3C 规范的 URL 列表:它们被使用`<br/>`标签分成了单独的行。构建这些链接的正确方式应该是一个无序列表。我们甚至可以使用一个`<div>`标签来表示这个内容构成了一个笔记:

```
<div class="note">  

<p>You can read these specifications and recommendations at the  

  W3C site, though they're a little heavy going at times.</p>  

<ul>  

  <li>HTML 4.01 <a href="https://www.w3.org/TR/html4/">  

    https://www.w3.org/TR/html4/</a></li>  

  <li>XHTML 1.0 <a href="https://www.w3.org/TR/xhtml1/">  

    https://www.w3.org/TR/xhtml1/</a></li>  

  <li>CSS 1 <a href="https://www.w3.org/TR/CSS1">  

    https://www.w3.org/TR/CSS1</a></li>  

  <li>CSS 2.1 <a href="https://www.w3.org/TR/CSS21/">  

    https://www.w3.org/TR/CSS21/</a></li>  

  <li>WCAG 1.0 <a href="https://www.w3.org/TR/WAI-WEBCONTENT/">  

    https://www.w3.org/TR/WAI-WEBCONTENT/</a></li>  

</ul>  

</div>
```

当以非语义的方式构建时，文档可以完全符合其规范。我们需要运用常识和判断力来查看我们的文档，并决定我们是否使用了正确的标签来标记页面上的每个元素。

多亏了 CSS，文档的外观和感觉不会因为我们使用正确的元素和坚持语义文档结构而受到影响。然而，当我们开始考虑不使用传统 Web 浏览器阅读内容的站点访问者时，正确标记文档是非常有益的。我们将在本书中更多地讨论这个问题。

***将演示文稿与文档结构分离***

遵循 Web 标准的另一个问题是将表示与内容分离。内容包括我们在上一节中讨论的语义文档；演示文稿是使它看起来像在计算机、投影仪或打印页面上一样的东西。

如果您使用的是过渡 DOCTYPE，您可以在文档中包含许多标签和属性，这些标签和属性并不描述文档中的不同元素，而是说明它们应该是什么样子。表示性标签包括`<font>`和``，可以用 CSS 替换。其他属性用于表示元素，如边框:例如，`<img border="0"/>`。

使用这样的表示元素会使改变元素的外观变得困难。Web 开发的最佳实践需要将文档的结构与其表现形式分离开来。这种分离是通过尽可能使用 CSS 来指定文档的外观来实现的。如果您想要根据严格的 DOCTYPE 进行验证，在大多数情况下，您需要应用这种分离，因为严格的 DOCTYPE 中缺少的标签和属性在很大程度上是表示性的。

这种结构与表示的分离也是不将表格用于页面布局的建议的基础。`<table>`标签最初被设计用来描述表格数据，比如电子表格中的数据，而不是强迫页面元素进入页面上的特定位置。如果你的页面使用一个表格来布局页面元素，你就混淆了你的结构和表现——即使页面可能很好地验证了一个严格的文档类型。

##### 摘要

在这一章中，我们已经学习了什么是 Web 标准，并且探索了如果我们想要开发符合 Web 标准的网页，我们必须考虑的核心问题。我们还研究了为什么 Web 标准对那些为 Web 设计的人有帮助，以及为什么投入时间去理解这种方法在将来会有回报的一些原因。

与所有可视化开发环境一样，Dreamweaver 在创建干净的标记方面并没有获得声誉。然而，对许多人来说，在可视化环境中开发比在文本编辑器中手工编写 HTML 和 CSS 更好。这本书将讨论如何使用 Dreamweaver 来确保您的作品符合标准，并解决我们在本章中提到的所有问题:有效的标记和 CSS、语义文档结构、结构与表示的分离，以及满足可访问性建议。任何工具——无论是记事本还是 Dreamweaver——都取决于它的操作人员，因此让我们继续使用 Dreamweaver 8 创建一个符合标准的网站。

##### 第二章。开发的场地规划和设置

在我们开始开发我们的网站之前，我们需要决定网站的外观、托管方式和结构。我们将通过本书的课程建立的网站将被称为代码火花；这将是一个网页设计资源网站很像 sitepoint.com。我选择这个作为网站的主题，并不是因为我假设每个读者都想建立一个网页设计资源网站，而是因为我们在创建这个网站时将探索的概念对许多其他类型的网站都是通用的。在这个项目中，我们需要做出的决定将与你将要建立的大部分网站的选项相类似。无论您是遵循本书，使用提供的示例图形创建 Code Spark 资源站点，还是使用这里描述的技术开发您自己的项目，都完全取决于您。

##### 代码火花网站设计

***代码星火网站的特点***

在我们开始设计网站的布局之前，我们需要考虑网站的特点，这样我们才能确切地知道我们需要在布局中包括什么。Code Spark 是一个 Web 设计和开发教程网站，所以我们必须处理探索设计和开发技术的教程，以及讨论 Web 设计领域有趣发展的文章。

*教程页面*

教程将包含网站的大部分内容，所以我们显然需要一个有利于这种内容格式的设计。教程倾向于包括大量的截图——可能很宽——和代码样本，这些样本需要被格式化以便教程作者的换行符被保留。我们希望确保我们的文章页面允许显示这样的页面元素。

我们还希望确保，如果读者喜欢他们正在阅读的教程，他们将能够在 Code Spark 网站上找到类似的教程。也许最简单的方法是在教程页面上添加一个该作者撰写的其他文章的列表。

*首页*

我们希望 Code Spark 的重点——网页设计和开发教程——从一开始就显而易见，所以我们将在网站主页上展示一些定期更新的功能教程。这样，新的访问者可以很快很容易地确定网站的内容，而回访者可以一眼看到新的内容。

*网站导航*

一旦我们准备并发布了我们的教程内容，我们将需要给访问者各种方法来找到它。“网页设计和开发”的主题相当广泛，所以我们的内容需要合理分类。这样，访问者可以快速访问所有讨论 Dreamweaver、CSS 或 PHP 开发的文章。教程索引页面显示了类别列表，以及几个示例教程，可以让用户立即了解类别中包含的信息。在网站的每个页面上列出这些类别有助于在用户心中强化内容类别，并让他们快速、方便地访问他们最感兴趣的主题的最新教程。

列出最受欢迎和评价最高的教程是让你的网站脱颖而出的好方法。通过让用户更容易地从主页上找到你的网站的最佳内容，你的访问者将更有可能阅读那些教程，并对 Code Spark 留下良好的印象——甚至可能会告诉他们的朋友。网站地图对访问者也非常有用:它是一个众所周知的工具，允许用户快速感受 Code Spark 内容的组织方式，并为他们提供了一个直接访问感兴趣的教程的途径。

例如，现在，通过浏览精心设计的类别来浏览网站可能对希望提高 CSS 技能的用户有好处。但是如果一个用户需要一个特定主题的教程——比如构建一个基于 CSS 的三列页面布局——这个人会想要一个更快的方法来找到它。通过提供一个搜索栏，你可以让用户很容易地快速找到特定的教程，如果你曾经访问过像微软开发者网络(MSDN)这样的大型网站，你就会知道这是一项非常困难的任务。

由于我们关注可访问性，我们希望添加一些功能来帮助访问者使用网站，例如允许用户增加和减少字体大小的样式表切换器，以及他们可以用来导航网站的键盘快捷键列表。我们还需要提供一个解释网站内容的页面，并为访问者提供一种与网站运营人员联系的方式。

记住以上所有内容，我们可以列出我们网站需要的重要功能。它需要:

*   导航到站点的主要部分:教程索引、关于站点和运行它的人的信息、联系页面和站点地图
*   教程类别列表
*   搜索设备
*   主页上显示了特色教程
*   在单个教程页面上展示文章内容的大区域

***设计网站***

一旦你创建了一个关键元素的列表，你就可以在纸上或图形应用程序中设计出一个页面布局。为了开始 Code Spark 项目站点，让我们看看由 SitePoint 的 Alex Walker 在 Fireworks 中创建的一个设计，如图 2.1 所示，“Alex 的主页设计，结合了我们的要求”，这样我们就可以讨论如何实现一个利用图形的布局，同时仍然遵守 Web 标准。

图 2.1。亚历克斯的主页设计，纳入我们的要求。
![1492_2.1](img/99dc233e4c2900c8e6b1f77e46217487.png)

这种设计包含了我们认为主页上需要的每一个元素:

1.  我们的主导航显示在顶部。

3.  类别链接和“读者收藏夹”部分出现在右边。

5.  搜索栏也在右边。

7.  我们的辅助功能控件出现在右上角。

9.  特色教程构成了主要的内容区域。

在设计布局之前，仔细考虑要包含的元素，这样可以避免以后为没有想到的额外元素寻找空间。

网站的教程页面布局如图 2.2 所示，“教程页面布局，显示内容区域。”包括一个大的内容区域，允许我们轻松地发布包含图像和示例代码块的文章。

图 2.2。教程页面布局，显示内容区域。
![1492_2.2](img/8ad8a476e06e505b963f6a9212901e09.png)

无论你是在主页、文章页面还是网站上的任何其他位置，都会出现许多一致的页面元素:标题，包括 Code Spark 徽标、顶部导航和我们稍后将讨论的辅助功能；页面右侧的栏包含搜索框，以及根据您在站点中的位置而变化的元素。这个侧边栏提供了对教程主题的快速、简单的访问。

主页上的内容区域将包含两个栏目，在这两个栏目中，我们将重点介绍网站上发布的最新文章和教程。当用户从该显示点击进入文章页面时，文章内容填满了该主要内容区域的整个宽度。我们的将是一个流动的布局:不管用户的屏幕分辨率如何，它都会延伸到用户的浏览器窗口。

*注:液体还是定宽？*

固定宽度的布局(不会随着浏览器窗口的大小而增减)需要足够窄，以确保屏幕分辨率较低的用户不必侧向滚动来阅读网站的内容。在实践中，这一事实意味着我们确实需要为用户设计 800 x 600 像素的分辨率，除非我们知道大多数目标用户的分辨率至少为 1024 x 768 像素。设计师经常使用固定宽度的布局，因为他们发现在已知的页面宽度内放置图形密集的设计更容易。

然而，对于一个内容丰富的网站来说，使用流动布局(随着浏览器窗口的大小而伸缩)是一个很好的设计选择。当然，您应该始终以 800 x 600 像素的分辨率检查您的工作，以确保在此分辨率下不会出现水平滚动条，并且列不会窄得离谱，但是具有更高屏幕分辨率的用户在查看站点的方式上会有更大的灵活性。

##### 构建网站

现在我们有了一个网站设计，我们可以开始考虑如何将网站放在一起，以及我们将使用的技术。同样，一旦我们开始，花一些时间进行规划将有助于使网站的开发更加流畅。

***处理普通元素***

我们已经在设计中确定了一些常见的页面元素；其中一些元素出现在每一页上，而另一些元素出现在一些(但不是全部)页面上。我们可以利用我们在网站设计中拥有这些通用元素的事实来简化开发。

*“复制、粘贴、复制、粘贴”技术*

在开发站点时，您可能会创建一个新文档，编写 XHTML 代码，应用 CSS，然后，当您准备创建一个新页面时，只需将上一个文档中出现的所有常见元素复制并粘贴到新文档中。这样，您站点的所有文件都可能包含大量的标记——可能还有图像——这些标记和图像在每个页面上都是重复的。

这不是一个太大的问题，直到你决定改变标题中的图形，并且必须打开站点的每个页面来复制和粘贴新的徽标标记。使用 CSS 确实减少了在文件之间复制和粘贴公共元素的需要——这些信息的大部分可以在控制网站页面的 CSS 文件中找到——但是，仍然有许多元素需要出现在单独的页面文档中。因此，当您以这种方式创建页面时，一些复制和粘贴是不可避免的。最后，您必须将所有修改过的页面上传到网站，以便显示修改后的徽标。

*Dreamweaver 模板*

如果您有使用 Dreamweaver 的经验，您可能会知道 *Dreamweaver 模板*。模板基本上是一个普通的 HTML 页面，Dreamweaver 可以将其用作创建新页面的基础。您可以将模板的一个或多个区域标记为可编辑、可选或重复。这些是 Dreamweaver 允许您在基于该模板的页面中更新的唯一区域；其余的被 Dreamweaver 锁定，如图 2.3 所示，“一个基于 Dreamweaver 模板的页面，带有一个可编辑区域。”

图 2.3。基于具有单个可编辑区域的 Dreamweaver 模板的页面。
![1492_2.3](img/4072c2656cff4e713ced4688234585be.png)

Dreamweaver 通过在标记中放置特殊的 HTML 注释来锁定部分页面。这些注释指示哪些区域被锁定，哪些区域是可编辑的，如下所示。

```
<h1>Code Spark Notice</h1>   

 <!-- TemplateBeginEditable name="EditRegion3" -->   

 <p>This is where the text of the notice will appear. </p>   

 <!-- TemplateEndEditable -->   

 <p>&copy; 2005 Code Spark</p>
```

使用 Dreamweaver 模板真正有用的方面是，如果您更改主模板的任何部分，Dreamweaver 将询问您是否希望它更新基于该模板的所有其他页面，从而节省您在整个站点中复制和粘贴更改的时间和麻烦。

模板帮助我们避免复制和粘贴常见的元素，这很好。然而，它不能帮助我们克服必须重新上传所有更改的网站文件的任务，无论我们使用 Dreamweaver 模板还是其他一些设计方法，我们都必须这样做。当然，Dreamweaver 模板对不使用 Dreamweaver 的设计师没有好处:它是唯一知道如何处理这些模板的程序。

*服务器端包括(SSI)*

我们管理重复标记部分的第三个选择是利用 use *服务器端包含(SSI)* 。SSI 允许您在将页面呈现给浏览器之前合并两个或多个文件。*包含文件*，或者*包含文件*，仅仅是一个复制了重复代码段的文件。它本身并不是一个完整的 XHTML 文档:它只是完整文档的一部分。

要创建 include，只需从原始文件中复制重复的内容，并将其保存为新文件。然后，使用以下代码行将该文件包含在主文档中:

```
<!--#include file="newfile.html" -->
```

例如，您可能有一个如下所示的 XHTML 文档:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">   

<html >   

<head>   

<meta http-equiv="Content-Type" content="text/html;   

    charset=iso-8859-1" />   

<title>Code Spark Notice</title>   

</head>   

<body>   

<h1>Code Spark Notice</h1>   

<p>This is where the text of the notice will appear.</p>   

<p>&copy; 2005 Code Spark</p>   

</body>   

</html>
```

从本文档顶部一直到`<h1>`标题的内容对于网站的所有页面都是通用的，从版权声明开始的内容也是如此。我们可以把这一页分成三部分:顶部、底部和中间——中间是唯一会改变的部分。

通过将文档的顶部和底部分别复制到 top.html 和 bottom.html，我们可以用包含信息替换原始文档中的它们。

```
<!--#include file="top.html" -->   

<p>This is where the text of the notice will appear.</p>   

<!--#include file="bottom.html" -->
```

一旦该文件被上传到 Web 服务器(我们将在本章后面设置一个)，任何使用 Web 浏览器查看该页面的人都会看到该文件与您使用 SSI 之前完全一样。Web 服务器截取我们的 HTML 中的`#include`文件代码，并用指定的文件替换它，如图 2.4 所示，“使用服务器端包含从三个文件创建一个完整的文档。”

图 2.4。使用服务器端 Includes 从三个文件创建一个完整的文档。
![1492_2.4](img/cd1848d9307d123b50322aab59bf79c6.png)

要创建包含相同顶部的新文件，我们只需在希望显示顶部的每个页面上添加适当的 include 行。然后，如果我们想更改徽标，我们只需在`top.html`文件中编辑它，并重新上传该文件。所有包含 top.html 的网页将自动显示新的标志。

服务器端包含对于创建易于维护的文档来说是一种非常有用的方式，而且，考虑到我们的教程站点可能会随着我们添加新的教程而变得非常大，SSI 对我们来说是一个很好的选择。毕竟，我们不希望必须复制和粘贴我们的更改，然后在每次对一个部分进行更改时重新上传我们的所有文件；我们也不想限制任何在网站上工作的人使用 Dreamweaver。如果这个网站是一个只有几页的小册子网站，并且不希望有更多的发展，你可能会决定不使用 SSI。然而，多年来我维护了许多大型网站，我知道——至少在 Code Spark 的例子中——SSI 以后会为我们节省大量的时间和精力。

Dreamweaver 理解 includes:当您加载包含 include 指令的页面时，它将显示 SSI 中包含的文档部分。但是，您需要直接打开包含文件，以便对其进行任何更改。当我们继续构建我们的站点时，我们将了解如何使用 Dreamweaver 和 SSI。

*注意:使用内置函数包含文件*

如果你正在使用 PHP、ASP.NET 或其他服务器端语言构建一个站点，你可以使用这种语言的内置函数来包含文件。这些方法倾向于提供更好的错误处理特性，但是如果内容是静态的，就不太可能需要这些特性。

在 PHP 中，你可以使用`include`、`require`、`include_once`或`require_once`。更多信息参见 PHP 手册中的相关讨论。在 ASP 中，`Server.Execute`类似，但不完全相同。

##### 设置 Web 服务器

因为我们正在构建可能成为大型静态内容站点的内容，所以我们将使用 SSI 来重用每个页面公共部分的代码。如果我们想测试和查看包含所有网页部分的网站，我们需要运行一个本地 Web 服务器。在这种情况下，“Web 服务器”是指通过发回网页来响应浏览器请求的软件。这个软件负责 SSI 中涉及到的繁重工作:截取 HTML 文件中的`#include`命令，用指定的文件替换。术语“Web 服务器”也可以用来指托管网站的计算机。

如果您使用 PHP、Perl 或 ASP 等语言执行任何服务器端 Web 开发，您可能已经安装了 Apache 或 IIS。如果你不确定你是否安装了网络服务器，试着在你的网络浏览器中输入`http://localhost/`:这将从你的本地网络服务器请求默认页面，如果一个正在运行的话。Apache 和 IIS 都安装测试页；如果你看到这样一个页面，你运行的是哪个 Web 服务器就很明显了。如果您只看到一条错误消息，抱怨找不到 localhost，那么您可能需要安装 Web 服务器。

如果您运行的是 Apache，您可能想跳到下面的“测试 SSI”一节；如果您使用的是 IIS，请直接跳到“使用 IIS 作为您的本地 Web 服务器”一节。如果您还没有运行本地 Web 服务器，我们可以设置 Apache Web 服务器来处理我们的 includes。Apache 可用于所有主流平台，并且可以免费下载和使用。

***安装阿帕奇***

几乎 70%在互联网上运行网站的网络服务器都使用 Apache(根据 netcraft.com 的数据)。如果你曾经在 Linux 服务器上使用过共享主机，很可能你的站点是在 Apache 上运行的。Apache Web server 是一个开源项目，可以免费下载和安装，大多数操作系统都提供了安装程序。

*窗户*

图 2.5。下载 Apache 安装程序。
![1492_2.5](img/c8d6eee7cc572e170c005b9f433339ca.png)

要在 Windows 上安装 Apache，进入[http://httpd.apache.org/](http://httpd.apache.org/)，点击下载，抓取“Win32 二进制(自解压)”版本，如图 2.5“下载 Apache 安装程序”。在整个讨论中，我将使用 Apache 1.3，但是如果您希望使用 Apache 2，说明或多或少是相同的。

找到并运行下载的可执行文件，并通过单击“下一步”继续完成安装问题。不理解呈现的选项也不用担心；在我们简单的测试服务器中，它们并不重要。

**在 IIS 旁边安装 Apache**

您可以使用 IIS 来提供带有服务器端 includes 的 Web 页面——这项工作它做得非常好——但是如果您想在已经安装了 IIS 的计算机上安装 Apache，请使用下面的说明。

如果您在成功安装后在 Web 浏览器中键入`http://localhost/`，将会显示 Apache 的测试页面(如前所述，localhost 指的是您的计算机)。默认情况下，Apache 将在目录`C:Program FilesApache GroupApachehtdocs`中查找要服务的文件。要改变这个设置，我们需要编辑主 Apache 配置文件`httpd.conf`。从开始菜单中，选择所有程序> Apache HTTP Server >配置 Apache Server >编辑 Apache httpd.conf 配置文件，在记事本中打开该文件。找到以`DocumentRoot`开头的行，如图 2.6“在记事本中编辑 httpd.conf”。

图 2.6。在记事本中编辑 httpd.conf。
![1492_2.6](img/fd627d1e25dbdb799f358eabb6ef904f.png)

这个片段告诉服务器你的网站文件存储在哪里。让我们把这个位置换到一个更容易接近的地方。在 C: drive 上创建一个名为 Apache Sites 的文件夹，然后将 httpd.conf 中的行更改如下:

```
#    

# DocumentRoot: The directory out of which you will serve your    

# documents. By default, all requests are taken from this    

# directory, but symbolic links and aliases may be used to point    

# to other locations.    

#    

DocumentRoot "C:/Apache Sites"
```

*注意:编辑前请备份！*

在编辑 httpd.conf 之前，制作该文件的备份副本，这样，如果一切出错，Apache 无法启动，您就有了原始文件的副本，可以用它替换编辑过的文件。httpd.conf 可以在`C:Program FilesApache GroupApacheconf`中找到。

文件后面的部分也会引用原始目录，如下所示:

```
#    

# This should be changed to whatever you set DocumentRoot to.    

#    

<Directory "C:/Program Files/Apache Group/Apache/htdocs">    

Change this to:    

#    

# This should be changed to whatever you set DocumentRoot to.    

#    

<Directory "C:/Apache Sites">
```

*注意:安全问题！*

抵制诱惑，不要重复使用现有的文件夹来托管你的网站，或者更糟糕的是，将`DocumentRoot`设置为 C:/。请记住:外部世界可能能够访问您为您的网站设置的目录；您不希望陌生人能够下载或修改您的个人文件！

为了让 Apache 注意到对 httpd.conf 的任何更改，我们需要重新启动它。在 Windows XP 中，您可以从服务管理控制台执行此操作，从“管理工具”菜单中选择“服务”,或者从“开始”菜单中选择“运行”,然后在出现的对话框中键入`services.msc`,即可打开服务管理控制台。要重启 Apache，请在服务列表中找到 Apache，右键单击它，然后选择 restart。

麦克·OS X

如果你在经营麦克·OS X，你很幸运！Apache 已经安装好了:您只需要启动它。为此，请访问“系统偏好设置”，选择“共享”，然后启动“个人 Web 共享”。然后，您应该能够在浏览器中输入`http://localhost/`来查看 Apache 测试页面。在 Mac 上，这个页面是从`/Library/WebServer/Documents/`提供的——根据你的用户配置文件的设置方式，你可能有权也可能无权访问这个位置。幸运的是，Mac 上的 Apache 还被配置为在`http://localhost/*~username*/`为每个用户提供他或她自己的网络空间。该目录的文件位于您主目录的`Sites`文件夹中。

***测试 SSI***

在我们完成这部分设置过程之前，我们需要检查 SSI 是否在我们的系统上正常工作。为此，我们将创建一个非常简单的包含 include 的 Web 页面。在文本编辑器中创建以下两个文件:

**例 2.1。`hello.html`**

```
<p>Hello, World!</p>
```

**例 2.2。`ssi_test.shtml`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"    

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">    

<html >    

<head>    

<title>Testing SSI</title>    

<meta http-equiv="Content-Type" content="text/html;     

    charset=iso-8859-1" />    

</head>    

<body>    

<!--#include file="hello.html" -->    

</body>    

</html>
```

现在，您应该能够打开浏览器，键入

```
http://localhost/ssi_test.shtml
```

进入地址栏，看到“你好，世界！”显示在网页上，如图 2.7 所示，“确认 SSI 正常工作。”

*启用 SSI*

如果您看到一个空白页，或者来自`ssi_test.shtml`的代码，您需要调整您的 Apache 配置来启用 SSI。打开 httpd.conf 文件，查看以下行是否存在且未被注释。

```
AddType text/html .shtml    

AddHandler server-parsed .shtml
```

图 2.7。确认 SSI 工作正常。
![1492_2.7](img/aff9fa8e5fe5a715eed033065dcf2377.png)

*有评论吗？*

在 httpd.conf 中，Apache 会忽略以`#`开头的行。这些是评论。这些行使您能够向文件添加注释，或者在不删除它们的情况下禁用某些选项。例如，httpd.conf 中禁用了以下选项。要重新启用它们，我们需要从每一行的开头删除`#`。

```
# AddType text/html .shtml    

# AddHandler server-parsed .shtml
```

如果文件中没有这些行，请添加它们:它们确保文件的扩展名为。shtml 由服务器解析。接下来，找到以下部分:

```
#    

# This should be changed to whatever you set DocumentRoot to.    

#    

<Directory "C:/Apache Sites">    

#    

# This may also be "None", "All", or any combination of "Indexes",    

# "Includes", "FollowSymLinks", "ExecCGI", or "MultiViews".    

#    

# Note that "MultiViews" must be named *explicitly* ---    

# "Options All" doesn't give it to you.    

#    

    Options Indexes FollowSymLinks MultiViews
```

在`Options`行中，将`Includes`添加到列表中，以启用该目录的 SSI:

```
 Options Indexes FollowSymLinks MultiViews Includes
```

您还应该检查以下行没有被注释掉:

```
AddModule mod_include.c
```

为了让服务器注意到您的更改，您需要重启 Apache。完成后，在浏览器中重新测试页面。

*注意:Mac OS X 上的 SSI 默认值*

Mac OS X 上 Apache 的默认安装应该启用 SSI。如果没有，或者由于其他原因需要编辑 httpd.conf，您可能需要以管理用户的身份登录。

***使用 IIS 作为本地 Web 服务器***

如果您已经安装了 IIS 并将其设置为本地 Web 服务器，您应该能够使用。shtml 扩展名来分析包含 SSI 指令的文件。为了测试 SSI，按照上一节的描述创建`hello.html`和`ssi_test.shtml`文件，并将它们保存到目录`C:Inetpubwwwroot`中。通过在浏览器中加载`http://localhost/ssi_test.shtml`来运行测试。

如果您的测试包含页面不工作，您将需要检查带有`.shtml`扩展名的页面是否被解析。为此，打开 IIS 管理控制台(控制面板>管理工具>互联网信息服务)。右键单击您的网站并选择属性。在默认网站属性对话框中选择主目录选项卡，然后单击配置按钮。

检查`.shtml`是否列在扩展名下，其可执行路径是否以 ssinc.dll 结尾，如图 2.8 所示。正在分析 shtml 以在 IIS 上启用包含。

##### 托管您的网站

我们将创建的网站应该能够托管在任何标准的托管帐户，允许使用服务器端包括。这个要求应该被大多数共享主机账户的最基本的包所覆盖。然而，如果你已经有了一个主机帐户，并想检查 SSI 是否可用，只需上传我们之前创建的测试页面来测试我们自己的服务器。如果这些页面有效，您就可以使用 SSI。

图 2.8。正在检查。正在解析 shtml 以在 IIvS 上启用 includes。
![1492_2.8](img/abdd3cf91d2b4508d9ff7396dbf3f502.png)

##### 设置 Dreamweaver

现在您的服务器已经设置好了，让我们为 Code Spark 网站创建一个目录。转到 Web 服务器的根文件夹(`C:Apache Files`对于 Apache`C:Inetpubwwwroot`为 IIS)并创建一个名为`codespark`的文件夹。该文件夹将作为`http://localhost/codespark/`可访问。

现在，我们已经准备好设置 Dreamweaver 并开始在站点上进行开发。打开 Dreamweaver，然后选择“站点”>“新建站点”。这将打开站点定义向导。在第一个屏幕中，将您的站点命名为`codespark`，并输入其 URL: `http://localhost/codespark`。

单击 Next 后，会询问您是否想使用服务器端语言。在本书中，您不需要使用服务器端技术来创建网站，因为我们使用的是服务器端包含，但 Dreamweaver 在这里询问的服务器技术是它用于 ASP、PHP、ASP.NET、JSP 和 ColdFusion 中的数据库驱动网站的技术。在此对话框中选择“否”,然后继续。

在下一个屏幕中，选择标记为“使用本地网络在服务器上直接编辑”的单选按钮，然后浏览您创建的站点目录。

图 2.9。向导显示摘要。
![1492_2.9](img/68fe1614c00cf854f824a91c254765fa.png)

在这一步之后，向导完成，提供了一个类似于图 2.9“向导显示摘要”中所示的站点创建细节的摘要如果一切正常，请单击“完成”创建站点并在 Dreamweaver 中打开它。

***你的工作区***

根据您在安装 Dreamweaver 时所做的选择，现在您应该会看到一个巨大的灰色屏幕，面板位于其左侧(如图 2.10“Dreamweaver 8 工作区”所示))或其右。

图 2.10。Dreamweaver 8 工作区。
![1492_2.10](img/427191cb83d37551afd52bdb5cc39147.png)

当面板出现在右侧时，Dreamweaver 处于“设计器”工作区布局中；当面板在左边时，它处于“编码器”布局。您可以通过选择“窗口”>“工作区布局”>“编码器”或“设计器”来选择您喜欢的视图。双屏选项对于那些有双屏设置的人来说很方便。

图 2.11。改变工作空间。
![1492_2.11](img/dc4c53298abe1de805a482ad0a068a7d.png)

我倾向于使用左边的面板，所以本书中的截图会显示配置；然而，你喜欢安排工作空间的方式是个人的选择:它不会对项目产生任何影响。

*注意:Dreamweaver 面板说明*

如果您是 Dreamweaver 的新手，您会发现在我们使用它们时，您很快就会了解不同的面板和工具栏用途。每次我们使用新的面板或工具时，我都会解释如何找到它并使用它的基本功能。所有面板都可以从窗口菜单中打开和关闭。每个面板与做类似事情的其他面板组合在一起。您可以使用面板组顶部的选项卡在它们之间切换，如图 2.12 所示，“资源面板是文件面板组的一部分”。

图 2.12。“资源”面板是“文件”面板组的一部分。
![1492_2.12](img/6a4afc4cbc3702deb2c6d7a143f3c55d.png)

***设置首选项***

通过设置您自己的首选项，您可以对 Dreamweaver 的运行方式进行许多更改。当你开发一个符合 Web 标准的站点时，正确设置某些参数是很重要的，所以，在我们创建第一个页面之前，让我们确保这些基本的参数已经准备好了。

打开首选项对话框(编辑>首选项)，并选择常规类别以显示图 2.13“设置常规首选项”中所示的信息。确保:

1.  未选中允许多个连续空格。如果选中此选项，它将允许您在每次多次按空格键时插入一系列不间断空格( )。如果您需要在布局中添加更多的空间，最好使用 CSS，以确保 Dreamweaver 不会对您不利！

3.  用`<strong>`和`<em>`代替`<b>`，勾选`<i>`。`<b>`和`<i>`是表示性标记的典型例子:它们不传达元素加粗或斜体的原因，只传达它就是这样的事实。`<em>`和`<strong>`告诉浏览器(或 Web 索引器、屏幕阅读器或任何其他想要解析站点的程序)文本被强调或强烈强调。这是我们在第 1 章“什么是 Web 标准”中讨论的语义文档结构的另一个例子。我们将在第 7 章“可访问性”和第 8 章“构建网站”中更详细地讨论屏幕阅读器，以及它们阅读文本的方式。

5.  选中“使用 CSS 代替 HTML 标签”。这一条相当简单:我们不希望 Dreamweaver 为我们插入任何`<font>`标签！

图 2.13。设置常规首选项。
![1492_2.13](img/f345103b3abc72d6b3c2bd20fecace90.png)

选择“插入时显示属性”下的“辅助功能”类别，并选中所有四个复选框，如图 2.14“设置辅助功能首选项”所示这意味着，当您输入任何这些元素时，Dreamweaver 将显示其他对话框，提示您输入这些元素的辅助功能属性。这使得您在创建文档时不太可能忘记输入这些重要的属性。

图 2.14。设置辅助功能偏好设置。
![1492_2.14](img/db6f2a36ef415cd8ad326b2da3287125.png)

还有许多其他的首选项，但大多数都与您使用产品的方式有关:它们不会像我们在这里讨论的首选项一样影响您正在处理的实际文档。但是，如果您发现开发环境的某些方面让您感到烦恼，请检查您的偏好:可能有一种方法可以修改程序的行为以适合您。

##### 摘要

在这一章的过程中，我们已经就如何继续开发这个网站做出了一些关键的决定。我们已经决定了要在我们的站点中包含的元素，并且我们已经提出了一个包含所有这些元素的布局。我们已经考虑了构建站点的最佳方式，以便管理将在站点的每个页面上显示的公共元素，并且，因为随着我们添加文章和教程，站点可能会变得非常大，所以我们决定使用服务器端包含(SSI)来管理这些公共元素。为了做到这一点——并能够在本地测试站点——我们安装了一个 Web 服务器，并检查 SSI 是否工作。最后，我们在 Dreamweaver 中创建了站点，并设置了我们需要的首选项，以便在开发符合标准的网站的道路上取得领先。

在开始时做出这种决定，并在这些决定的基础上建立我们的系统，这意味着您可以在清楚地了解您要去哪里以及您希望实现什么的情况下开始开发过程。考虑网站将如何成长和发展是很重要的。你不能先发制人任何可能发生的事情，但是，当计划开发时，如果你考虑到网站在下一年可能如何发展，这些期望可以支持你的决策过程。例如，如果网站永远不会超过一个三页的小册子网站，我们可能不会决定使用 SSI，因为复制、粘贴和重新上传内容的问题对于这种类型的项目来说是不存在的。

在下一章，我们将看看 XHTML。我们将发现它与您可能已经使用过的 HTML 有何不同，以及 Dreamweaver 如何帮助您在站点开发中使用 XHTML。

##### 第三章。XHTML 和语义

Dreamweaver MX 是 Macromedia Dreamweaver 的第一个版本，为使用 XHTML 的人提供支持，这一发展反映了许多开发人员已经从 HTML 转向 XHTML 的事实。在这一章中，我们将深入探索 XHTML。我们将理解为什么它不同于 HTML，为什么我们可能想优先使用它而不是 HTML，以及我们如何去做。

在这一章中，我们不仅将看到如何编写有效的 XHTML，还将看到如何正确地构造 XHTML 文档，以便所有用户都可以访问我们的内容。

##### 什么是 XHTML？

XHTML 基本上是两种语言的结合:HTML 和 XML。您可能已经熟悉 HTML，但是 XML 可能需要简单介绍一下。

***XML***

可扩展标记语言(XML)是一种通用语言，用于以易于人类和计算机阅读的方式组织数据，如下所示:

```
<?xml version="1.0" encoding="iso-8859-1"?>     

<orders date="March 31 2006" >     

  <order productID="52478">     

    <description>Dreamweaver 8 (OS X)</description>     

    <recipient>     

      <name>Sally Smith</name>     

      <address>     

        <street>474 Smith St.</street>     

        <city>Collingwood</city>     

        <state>Victoria</state>     

        <zipCode>3068</zipCode>     

        <country>Australia</country>     

      </address>     

    </recipient>     

  </order>     

  <order productID="52477">     

    <description>Dreamweaver 8 (Windows)</description>     

    <recipient>     

      <name>John Jameson</name>     

      <address>     

        <street>Level 5, 142 Park Avenue</street>     

        <city>New York</city>     

        <state>New York</state>     

        <zipCode>10167</zipCode>     

        <country>United States</country>     

      </address>     

    </recipient>     

  </order>     

</orders>
```

我们可以看到，该代码由 2006 年 3 月 31 日的两个订单组成:一个是 Mac OS X 的 Dreamweaver 8(将交付给澳大利亚的 Sally Smith)，另一个是 Windows 的 Dreamweaver 8(将交付给纽约的 John Jameson)。

这里使用的实际标签不是 XML 的一部分；XML 只定义了如何编写标签。由创建和使用这些文件的实体来商定实际使用的标签。这样，我们可以在 XML 中定义许多有用的语言；XHTML 就是其中之一。

***XHTML***

XHTML 是 W3C 在 2000 年 1 月 26 日发布的一个推荐标准。XHTML 代表了 HTML——网站的原始语言——到 XML 应用程序的重新表述，旨在满足网络的未来需求。事实上，XHTML 可以被认为是 HTML 的最新版本，因为没有进一步的 HTML 规范将被开发或发布。

由于 XHTML 是 HTML 的重构，而不是一种全新的标记语言，所以对于已经使用过 HTML 的人来说，它似乎非常熟悉。XHTML 和 HTML 之间几乎没有区别，这使得希望使用 XHTML 的 Web 开发人员的工作变得很容易。我们将在这一章中讨论这些差异，并看看为什么我们可能想要使用 XHTML 而不是 HTML。

##### 什么是有效的 XHTML 文档？

为了从一开始就创建有效的 XHTML 文档，我们需要在开始标记内容之前在文档中包含某些元素。幸运的是，如果我们使用“文件”>“新建”…,打开“新建文档”对话框，然后选择“基本页面”、“HTML ”,然后从“文档类型”( DTD)下拉列表中选择一种 XHTML 1.0 文档类型，Dreamweaver 将为我们提供一个有效的 XHTML 文档作为起点。默认选择是 XHTML 1.0 Transitional，它将创建一个包含以下标记的页面。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">     

<html >     

<head>     

<title>Untitled Document</title>     

<meta http-equiv="Content-Type" content="text/html;     

    charset=iso-8859-1" />     

</head>     

<body>     

</body>     

</html>
```

**T2`DOCTYPE`T4**

有效的 XHTML 文档必须使用 XHTML DOCTYPE。我们在第 1 章“什么是 Web 标准”中讨论了文档类型。；您将记住这一行标识了文档所遵循的规范。XHTML 过渡文档类型是:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

*注意:使用 XML 声明*

有时，您会看到一个 XML 声明，如下所示，作为 XHTML 文档的第一行。

```
<?xml version="1.0" encoding="UTF-8"?>
```

这个 XML 声明声明文档是 XML，这是推荐的，但不是必需的。这一行是 Dreamweaver MX 插入的。然而，Dreamweaver 8 没有插入 XML 声明，因为它具有将 Internet Explorer 6 切换到“古怪模式”的不幸效果，这是一种忽略 Web 标准而支持 Internet Explorer 5 的非标准规则的特殊模式。

***`html`元素***

```
<html >
```

`html`元素被称为文档的根元素。要成为有效的 XHTML 文档，这个元素需要包含`部分；这个属性是 XML 的一部分，它声明文档中的元素默认符合 XHTML 标准。`

 `*注意:XML 名称空间*

XML 文档中使用的实际标签可以由一个或多个文档类型定义(dtd)来定义(这些不同于我们前面讨论的 DOCTYPEs、document type *声明*)。可以使用`xmlns`属性将 dtd 链接到 XML 文档中。(严格来说，`xmlns`属性不需要指向 DTD，正如用来标识 XHTML 名称空间的 URL(`https://www.w3.org/1999/xhtml`)所证明的那样。XML 实际上允许任何文本字符串来标识 XML 名称空间。使用相关 DTD 的公共 URL 对于定制 XML 文档类型来说只是一个有用的约定。然而，对于我们的目的来说，这些都不重要。)

每个 DTD 都有一个“名称空间”，它构成了该 DTD 中标签的前缀。一个 DTD 可能被赋予“默认名称空间”(没有前缀)，但是文档中使用的其他 DTD 需要唯一的前缀。

例如，如果我们想将订单列表中的一些 XML 添加到 XHTML 文档中，我们可以向文档中添加一个名称空间，如下所示:

```
<html       

    xmlns:ord="http://myshop.com/orders.dtd">
```

在 XHTML 文档中，我们可以使用`ord:`前缀来表示元素来自顺序列表 DTD:

```
<h2>Orders Placed</h2>     

<ord:orders>     

  <ord:order productID="52478">     

    <ord:description>Dreamweaver 8 (OS X)</description>     

    <ord:recipient>     

      <ord:name>Sally Smith</ord:name>     

      <ord:address>     

        <ord:street>474 Smith St.</ord:street>     

        <ord:city>Collingwood</ord:city>     

        <ord:state>Victoria</ord:state>     

        <ord:zipCode>3068</ord:zipCode>     

        <ord:country>Australia</ord:country>     

      </ord:address>     

    </ord:recipient>     

  </ord:order>     

</ord:orders>
```

***`head`元素***

```
<head>     

<title>Untitled Document</title>     

<meta http-equiv="Content-Type" content="text/html;     

    charset=iso-8859-1" />     

</head>
```

`head`元素包含`title`元素，它给页面一个标题。在上面的代码片段中，您可以看到 Dreamweaver 在默认情况下插入了“无标题文档”: Web 上有成千上万个标题为“无标题文档”的文档，因为它们的作者忘记了更改文档的标题！

在文档的头部，我们还可以看到一个`<meta>`标签。这个`<meta>`标签声明了文档的`Content-Type`，以及使用的字符编码。

***`body`元素***

```
<body>     

</body>
```

这里是`body`元素，您将把所有您希望提供给站点访问者的内容放入其中。

##### XHTML 和 HTML 的区别

当使用 XHTML 而不是 HTML 时，只需要记住一些规则。虽然我们将使用 Dreamweaver 来编写 XHTML——并且可以依靠该程序来很好地完成这项工作——但是理解这两种语言之间的差异是值得的。有时，不可避免地需要手工编写标记，或者编辑从其他来源复制的标记，以使其符合 XHTML。

***引用属性值***

在 HTML 中，不引用属性值是完全有效的。例如，以下图像标记是有效的 HTML:

```
<img src="/img/me.jpg" alt="A picture of me" height=400 width=200>
```

要使这成为有效的 XHTML，需要在属性值周围插入引号，height = " 400 " width = " 200"。Dreamweaver 用引用的属性值编写 HTML 和 XHTML 但是，您可能会发现从其他来源复制的标记包含这些未加引号的 HTML 值。

***关闭所有空元素和非空元素***

正如您可能期望的那样，非空元素是在开始和结束标记之间包含某些内容(例如，文本、脚本或其他数据内容)的任何元素。`p`和`li`是非空元素的例子。在 HTML 中，我们不需要关闭这些元素，所以下面的列表是有效的 HTML。

```
<ul>     

  <li>List item one     

  <li>List item two     

  <li>List item three     

</ul>
```

但是，这将构成无效的 XHTML，因为 li 元素还没有结束。此问题已在下面的有效 XHTML 标记中得到纠正:

```
<ul>     

  <li>List item one</li>     

  <li>List item two</li>     

  <li>List item three</li>     

</ul>
```

那么`hr`、`img`、`br`等元素呢？这些空元素也必须关闭。在 XML 中，可以用`<hr></hr>`或者使用 XML 的简写符号`<hr/>`来实现这一点。不幸的是，较老的浏览器可能会回避这种奇怪的标记。正如您可能已经猜到的，开发 XHTML 的聪明人想出了一个解决这个问题的方法:使用简写符号，但是在元素名和结束斜杠(`<hr />`)之间插入一个空格。这仍然表示有效的 XML，因此支持 XHTML 的浏览器不会有问题，并且旧的浏览器会将结束斜杠视为不可识别的属性。

***避开最小化属性***

HTML 支持最小化属性，或者忽略不需要的属性值。考虑这个属性最小化的例子:

```
<input type="checkbox" checked>
```

在上面，属性 checked 表示当复选框显示在页面上时应该被选中。XML 不像 HTML 那样支持最小化属性，所以要使用有效的 XHTML 来实现这一点，我们需要给这些属性一个值:

```
<input type="checkbox" checked="checked" />
```

在这里，属性的值变得与其名称相同。HTML 中最小化的几个属性就是这种情况:

*   `selected="selected"`
*   `disabled="disabled"`
*   `readonly="readonly"`

##### 用小写字母书写元素和属性

XHTML 要求所有的标签和属性都用小写字母书写。HTML 不区分大小写:我们甚至可以在语言中混合使用大写字母和小写字母。然而 XML 是区分大小写的，所以 XHTML 要求使用小写标签，如下例所示。

```
<p>This line is <em class="formal">valid</em> XHTML</p>     

<p>This line is <STRONG STYLE="text-transform: uppercase;">     

  not</STRONG> valid XHTML</p>
```

***恰当地嵌套元素***

Web 浏览器通常非常容忍 HTML 中的错误，但不太容忍 XHTML 中的错误。以下示例包含无效的 HTML 和 XHTML，但通常会按照作者(可能)的意图显示:

```
<p><em>This text is emphasized</p></em>
```

在 HTML 中，标签必须正确嵌套；也就是说，最后打开的标签必须是第一个关闭的标签。当我们开始使用 XML，并标记文档的含义时，这个需求变得更加重要。因此，我们需要编辑上面的代码来正确地嵌套我们的标签:

```
<p><em>This text is emphasized</em></p>
```

***用`id`代替`name`来标识元素***

HTML 允许我们使用 name 属性来标识页面上的特定元素。name 可以有多种用途:使用 JavaScript 引用元素、命名表单元素以便表单提交后可以被收集，等等。以下是行动中的名称:

```
<form method="post" action="/cgi-bin/search.cgi">     

  <img src="search.gif" name="Image1" alt="Search ">     

  <input type="text" name="searchField">     

  <input type="submit" value="Search">     

</form>
```

在 XHTML 中，我们必须使用 id 属性:

```
<form method="post" action="/cgi-bin/search.cgi">     

  <img src="search.gif" id="Image1" alt="Search " />     

  <input type="text" id="searchField" name="searchField" />     

  <input type="submit" value="Search" />     

</form>
```

仔细看看，注意我已经在`<input>`标签上留下了`name`属性。表单域是`name`属性仍然合适的地方；但是，它并不用于标识文档中的这些元素:它用于提供变量名，字段值将在该变量名下提交。在 XHTML Strict 中，这是合法使用`name`属性的唯一目的。

Dreamweaver 会将`name`和`id`属性添加到 XHTML 过渡文档中的给定元素(赋予两个属性相同的值)。

*注意:`id`必须是唯一的*

与`name`不同，元素的`id`属性必须是惟一的:在任何给定的文档中，不能有多个元素具有特定的`id`。

##### 为什么要用 XHTML？

我们已经探索了 XHTML 和 HTML 之间的差异，现在我们对每种语言都有了更清晰的理解。但事实是，如果我们愿意，我们可以创建一个符合标准的、可访问的、语义化的网站来验证 HTML 4.01。为什么要考虑迁移到 XHTML？

***创建干净标记***

HTML 允许开发人员以非常灵活的方式编写标记。它对规则的应用不是很严格，比如像`<p>`这样的结束标签。例如，考虑以下情况:

```
<p>This is a paragraph.     

<p>This is another.
```

HTML 允许这种标记，而不是要求使用结束的`</p>`标记来标记段落的结尾，如下所示:

```
<p>This is a paragraph.</p>     

<p>This is another.</p>
```

HTML 允许创建更简单但更模糊的标记，而在 XHTML 中，每个开始标记都必须与其结束标记相匹配。虽然采用灵活的标记方法似乎是一个很好的主意，它使没有多少技术专长的人能够轻松地为 Web 创建文档，但这种方法也可能导致许多问题，特别是当这些杂乱的标记文档要由不具备台式计算机处理能力的设备阅读时。

***让代码更容易被机器处理***

对于计算机来说，XHTML 比 HTML 更容易处理，因为 XHTML 不具备 HTML 所具备的灵活性。这意味着使用 XHTML 标记的文档更容易被传统网络浏览器之外的设备阅读或显示，如屏幕阅读器、网络电话、盲文阅读器和搜索引擎。

***提升内容的可移植性***

您在网页中标记的内容本身就很有价值；将来，您可能希望以不同的格式重用它。如果内容是用 HTML 标记的——甚至是有效的 HTML——那么在另一个应用程序中重用这些内容比用 XHTML 标记要困难得多。XHTML 严格遵循 XML 规则，这意味着将 XHTML 文档转换成其他格式要容易得多。例如，如果您决定用数据库驱动的后端重新构建您的站点，并且需要将所有标记的内容放入新的数据库中，这将非常有用。

***允许与其他 XML 应用集成***

XHTML 允许合并来自其他 XML 应用程序的标记，如 MathML、SMIL(同步多媒体集成语言)和 SVG(可缩放矢量图形)。这可能现在看起来不是特别有用，除非您有非常特殊的需求，但是 XHTML 的集成能力在未来可能会变得更加重要。

##### Dreamweaver 中的 XHTML

通读了前面几节中的所有注意事项，您会很高兴地知道我们将让 Dreamweaver 为我们编写大部分 XHTML 标记。现在，让我们看看 Dreamweaver 提供的帮助我们编写有效的 XHTML 文档的工具。

***创建新页面***

我们已经看到 Dreamweaver 可以用 HTML 或 XHTML 创建新页面。一旦 Dreamweaver 识别出您的页面具有 XHTML 文档类型，它将使用正确的 XHTML 语法而不是 HTML 来插入元素。您可以通过查看标题栏中是否显示(XHTML)来确认 Dreamweaver 是否在 XHTML 中工作，如图 3.1“Dreamweaver 在标题栏中显示 XHTML”所示

图 3.1。Dreamweaver 在标题栏显示 XHTML。
![1492_3.1](img/6a38d870297555763223ebe5d07cedb8.png)

在“设计”视图中，键入 Shift-Enter 插入换行符。切换到代码视图，查看输入的标记。Dreamweaver 将插入正确的`<br />`标签，而不是 HTML 的`<br>`。尝试添加图像:您会注意到 Dreamweaver 正确地关闭了图像标签。我们在 HTML 和 XHTML 中使用 Dreamweaver 的方式几乎没有区别。只要 Dreamweaver 知道我们正在处理哪种类型的文档，它就会为我们编写正确的标记。

*创建框架集*

如果您需要创建框架集，Dreamweaver 将帮助您使用正确的 DOCTYPE。

在新的 XHTML 文档中，使用“插入框架”按钮创建一个带有顶部框架的框架集，如图 3.2“在 Dreamweaver 中创建框架集”所示，您可以在“插入”工具栏的“布局”面板中找到它。

图 3.2。在 Dreamweaver 中创建框架集。
![1492_3.2](img/499eccecb2dc13de8a0dea9b72fefb23.png)

您现有的页面将成为底部框架，同时在框架集中创建一个新的顶部框架。如果您查看单个帧的源，它们应该使用 XHTML 转换文档类型。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"     

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

现在，看一下包含框架。该框架应该有一个 XHTML 框架集文档类型，如下面的代码和图 3.3“在 Dreamweaver 中创建框架集文档”所示

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN"     

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

图 3.3。在 Dreamweaver 中创建框架集文档。
![1492_3.3](img/2ba8b47f7a63bbc1486239e0c2036155.png)

***转换现有页面***

一旦开始使用 XHTML，您可能想将一些旧的站点转换成 XHTML。也许您需要将一些用 HTML 标记的内容转换成 XHTML 格式，以便与您的站点集成。Dreamweaver 具有“转换为 XHTML”的功能，可以使这个过程变得非常容易。

要转换文档，请先在 Dreamweaver 中打开它，然后选择“文件”>“转换”。最后，选择您想要将文档转换成的规范。您需要单独转换框架集和每个框架页面。

Dreamweaver 将尽最大努力应用我们之前讨论过的 XHTML 规则，但是如果原始标记不符合 Web 标准，可能会出现一些问题。您需要浏览文档并自己解决这些问题。如果这似乎是一件不得不做的乏味的事情，请记住避免这样的问题是我们使用符合 Web 标准的 XHTML 的原因之一:我们不太可能再次经历这种繁琐的事情。当然，您可以让 Dreamweaver 使用其内置的标记验证器来发现这些问题。

*Dreamweaver 验证器*

正如我们在第一章中讨论的，什么是 Web 标准？，文档验证过程允许您确认您的标记符合您选择的特定规范。

通过选择“文件”>“检查页面”>“验证标记”来验证您的文档。如果文档是使用有效的 XHTML 构建的，结果面板中将显示一条消息。如果文档无效，您将看到一个错误列表以及出现这些错误的行号，如图 3.4 所示，“XHTML 文档通过验证后在结果面板中显示错误”这些错误很可能是由我们上面讨论的一些要点引起的；例如，“标签‘img’的预期结尾”意味着文档中的图像标签需要一个结束的`/>`来使其成为有效的 XHTML。

图 3.4。验证 XHTML 文档后，在结果面板中显示错误。
![1492_3.4](img/fcd06e3100c7cf14701d76c5d8d9df1d.png)

*注:查看行号*

在“代码”视图中工作时，可以打开行编号，以便更容易地跟踪任何有问题的代码行。可以通过“视图”>“代码视图选项”>“行号”菜单项打开和关闭行号。您也可以双击结果面板中的行，跳转到文档中的该行，该行将被突出显示。

##### 语义标记

正如我们已经同意的，我们不仅仅关心编写有效的 XHTML:我们还希望创建语义文档。语义是对意义的研究，因此具有语义标记的文档是包含试图传达文本意义的标签的文档。例如，`<h1>`标签用于指示顶级标题，而`<ul>`和`<li>`标签用于标记没有特定顺序的项目列表。如果我们使用语义标记，浏览器不仅可以阅读和显示(或者，在屏幕阅读器的情况下，大声朗读)页面的内容，而且知道以适当的格式显示或朗读元素。正如我们所看到的，当涉及到编写有效的 XHTML 时，Dreamweaver 将为我们做大部分艰苦的工作，但是当涉及到创建适当结构化的文档时，我们需要自己采取主动的方法。

我在 Dreamweaver 这样的可视化环境中工作时遇到的最大问题之一是，很容易全神贯注于事物的外观，而完全忘记 Dreamweaver 生成的标记。例如，对我来说，很容易决定让一些文本从屏幕左侧缩进几英寸。问题是我可能会以下面的标记结束:

```
<blockquote>      

  <blockquote>      

    <blockquote>      

      <p>My indented text</p>      

    </blockquote>      

  </blockquote>      

</blockquote>
```

从语义的角度来看，这显然不好:我缩进的文本不是引用，当然也不是引用的引用的引用！

当然，确保我们的网站在浏览器中看起来很棒是非常重要的，但这不是全部。我们的一些用户可能看不到我们设计的任何部分，因为他们使用的是屏幕阅读器或纯文本设备。然而，通过在创建 Web 文档时采取一些谨慎的措施，我们可以在不影响其他用户对网站的观感的情况下，为这些用户提供出色的体验。

*注意:移除 CSS 样式渲染*

当您在“设计”视图中工作时，Dreamweaver 8 使您很容易看到文档在没有样式的情况下的外观。通过选择视图>工具栏>样式渲染，可以打开样式渲染工具栏。“切换 CSS 样式显示”按钮可以打开和关闭文档中的 CSS，如图 3.5“切换 CSS 样式呈现”所示

图 3.5。切换 CSS 样式呈现。
![1492_3.5](img/4fe7f937bafcab2e16383bfd0fd0e6a8.png)

***语义上使用元素***

在这一节中，我们将看看 XHTML 中一些最常见的元素，并了解如何在 Dreamweaver 中使用它们。这不是一个详尽的列表，但旨在提供一些可能犯的更常见错误的示例，尤其是当我们使用 Dreamweaver 这样的工具时。

在这一章中，我使用了 W3C 规范中使用的术语“应该”和“不应该”,以表明这些问题影响了我们试图达到的 Web 标准。每个 XHTML 元素都应该以特定的方式使用。当然，在实践中，必须决定我们将使用的元素类型以及使用它们的最佳方式；在我们建设网站时，我们将密切关注这些决定。本章具体解释了标准告诉我们什么；使用规范本身使用的术语类型有助于强调以下事实:此处提供的信息不是个人意见，而是标准！

*标题*

XHTML 提供了六个标题级别。这些标题可以被认为与书中可能出现的标题相似:

```
<h1>Introduction</h1>      

<h1>Starters</h1>      

  <h2>Soups</h2>      

    <h3>Vegetable Soup</h3>      

    <h3>Pea and Ham Soup</h3>      

    <h3>Minestrone</h3>      

  <h2>Other Starters</h2>      

<h1>Mains</h1>      

  <h2>Beef</h2>      

  <h2>Chicken</h2>      

  <h2>Vegetarian</h2>      

<h1>Deserts</h1>      

  <h2>Cakes</h2>      

  <h2>Biscuits</h2>
```

使用 Dreamweaver，我们可以通过选择要设置为标题样式的文本来创建标题，然后在属性检查器中选择所需的标题级别，如图 3.6“在 Dreamweaver 中创建一级标题”所示

图 3.6。在 Dreamweaver 中创建一级标题。
![1492_3.6](img/be50e18dbf14d0df9f1296c4b7d48cb6.png)

每当讨论中的文本逻辑上包含一个标题时，我们都应该使用标题样式。当我们仅仅想要大的文本时，我们不应该使用标题:使用 CSS 来创造这种效果。

我们不应该通过用 CSS 样式化一个段落或其他元素来“伪造”标题，这样它看起来像一个标题，但在语义上是一个段落。没有区分标题的页面可能会被浏览器以我们不希望的方式呈现(或说出),这反过来会在用户中造成混乱。稍后我们将更详细地讨论这一点。

在可能的情况下，我们也应该避免在标题逻辑上属于文档的地方使用图像。如果我们使用一幅图像，使用屏幕阅读器或其他纯文本设备的用户将不会按照预期的那样理解标题。

*列表*

XHTML 为您提供了三种不同类型的列表；当你的内容逻辑上包含一系列条目时，应该使用列表。

无序列表样式通常在浏览器中显示为项目符号列表；但是，您可以使用 CSS 来更改项目符号的外观，甚至可以将列表更改为在屏幕上水平显示，而不是垂直显示。您可以使用属性检查器的无序列表图标在 Dreamweaver 中创建无序列表，如图 3.7“创建无序列表”所示

图 3.7。创建一个无序列表。
![1492_3.7](img/992d15ab897e82b5e1d7d4aee7ee19b5.png)

无序列表标记如下:

```
<ul>      

  <li>250 grams (9 ounces) Plain Flour</li>      

  <li>1 teaspoon Baking Powder</li>      

  <li>50 grams (2 ounces) Butter</li>      

  <li>1 egg</li>      

  <li>Half a Cup of Milk</li>      

</ul>
```

只要列表中的项目顺序很重要，就应该使用有序列表格式。您可以使用属性检查器的“有序列表”图标在 Dreamweaver 中创建有序列表，如图 3.8“创建有序列表”所示

图 3.8。创建有序列表。
![1492_3.8](img/37d49fceb96099a0a8c9b0020ebc050a.png)

如果在创建有序列表后，切换到代码视图，您将看到以下标记。

```
<ol>      

  <li>Preheat the oven to 200 degrees Celsius (400 degrees      

    Fahrenheit)</li>      

  <li>Put the flour, baking powder and sugar in a mixing bowl,      

    then rub in the margarine until the mixture resembles      

    breadcrumbs.</li>      

  <li>Beat the egg and add it, with the milk, to the rest of the      

    ingredients. Beat into a dough.</li>      

  <li>Turn the dough out onto a floured surface and knead it      

    briefly.</li>      

  <li>Put into a greased tray and bake for 45 minutes.</li>      

</ol>
```

`<ol>`元素表示这是一个有序列表。默认情况下，浏览器会显示这些列表，如图 3.9 所示，“显示无序列表和有序列表”，但是您可以使用 CSS 来更改任何列表的显示。

图 3.9。显示一个无序列表和一个有序列表。
![1492_3.9](img/acbb38cab8568b563cd5c4a7b2c40814.png)

*注意:嵌套列表的正确结构*

在有序列表和无序列表中，都有可能创建嵌套列表——列表中的列表。在这种情况下，子列表必须嵌套在父列表的列表项元素中，如下例所示:

```
<ul>      

  <li>List item one</li>      

  <li>List item two      

    <ul>      

      <li>Sub item one</li>      

      <li>Sub item two</li>      

    </ul>      

  </li>      

  <li>List item three</li>      

</ul>
```

如果你有一个术语和定义的列表要标记，最后一种列表是有用的；这叫做定义列表。您可以使用“插入”工具栏的“文本”面板在 Dreamweaver 中创建定义列表。要创建列表，请单击 dl 按钮，如图 3.10“使用插入工具栏创建定义列表”所示

图 3.10。使用插入工具栏创建定义列表。
![1492_3.10](img/5c3ba049bfe2cfe32970ace723a7cf73.png)

您在定义列表中键入的第一项将成为第一个术语；按回车键将向前移动一行，以创建该术语的定义。再次按 Enter 将创建第二个定义，依此类推。

您最终会得到类似如下的标记:

```
<dl>      

  <dt>Cardamom</dt>      

  <dd>An Indian spice from the ginger family.</dd>      

  <dt>Caster Sugar</dt>      

  <dd>Super fine sugar.</dd>      

</dl>
```

图 3.11。显示定义列表。
![1492_3.11](img/14298ec680a547b6a09280467f34c69d.png)

默认情况下，此标记将显示在浏览器和 Dreamweaver 中，如图 3.11“显示定义列表”所示同样，您可以使用 CSS 来更改这个显示。

*创建段落和换行符*

换行符和段落之间的区别是常见的混淆来源。辨别这种差异的一个可靠的方法是从一首喜爱的歌曲中标记出几段歌词:

```
<p>      

It's time to play the music,<br />      

It's time to light the lights,<br />      

It's time to meet the Muppets on the Muppet Show tonight!      

</p>      

<p>      

It's time to put on make up,<br />      

It's time to dress up right,<br />      

It's time to raise the curtain on the Muppet Show tonight!      

</p>
```

段落标记将诗句联系在一起。换行符用来标记每一行的结尾。

要在 Dreamweaver 中创建段落，请按 Enter 要创建一个`<br />`标签，请按 Shift-Enter。无论何时使用`<br />`，都要考虑它是否是最合适的元素。使用 CSS 或者段落来创造额外的空间，你会更好吗？也许内容真的应该标记为列表，就像上面的例子一样。

确保不要在段落中使用换行符来模拟列表！虽然此标记可能在 Web 浏览器中显示为有序的项目列表，但屏幕阅读器无法按预期显示内容:

```
<p>      

  1\. Preheat the oven to 200 degrees Celsius (400 degrees      

  Fahrenheit)<br />      

  2\. Put the flour, baking powder and sugar in a mixing bowl,      

  then rub in the margarine until the mixture resembles      

  breadcrumbs.<br />      

  3\. Beat the egg and add it, with the milk, to the rest of the      

  ingredients. Beat into a dough.<br />      

  4\. Turn the dough out onto a floured surface and knead it      

  briefly.<br />      

  5\. Put into a greased tray and bake for 45 minutes.      

</p>
```

*表示强调*

在印刷文本中，我们经常用加粗或斜体来强调特定的单词。这种方法让读者理解我们对特定单词的强调:我们不仅仅是为了加粗或斜体而加粗或斜体。例如，我们在本书中第一次使用一个新的或重要的术语时，我们会将该术语加粗。我们使用粗体格式向读者强调这些新单词:将它们标记为您需要记住的单词。

当我们在第 2 章“开发的场地规划和设置”中设置我们的首选项时，我们在首选项对话框中设置了复选框“使用`<strong>`和`<em>`代替`<b>`和`<i>`”。这样做意味着“属性”检查器中的 B(粗体)按钮将插入 strong 元素，I(斜体)按钮将插入 em 元素，如下所示:

```
<strong>Make sure that you preheat the oven.</strong> Cooking at      

the correct temperature is <em>really</em> important.
```

默认情况下，大多数网络浏览器会将`<strong>`显示为粗体文本，将`<em>`显示为斜体文本。这就是为什么许多网页设计师错误地认为这些标签等同于`<b>`和`<i>`，它们纯粹是表象，并没有提供太多的意义。像几乎所有的标签一样，`<strong>`和`<em>`的外观可以用 CSS 改变。

*注:`<em>`和`<strong>`有什么区别？*

W3C 和大多数 HTML 文档只将这些元素描述为“强调”和“强强调”，这没有多大用处。把`<strong>`想象成一个响亮缓慢的声音，把`<em>`想象成一个提高的声调。

*缩进和`blockquote`元素*

Dreamweaver 的属性检查器是图 3.12“使用文本缩进图标”中所示的文本缩进图标的位置你唯一应该使用这个图标的时候是缩进引用的文本。

图 3.12。使用文本缩进图标。
![1492_3.12](img/ac5242e6a7742ecf568b55e964a79e87.png)

该图标插入了一个`blockquote`元素，这就是它用于标记报价文本的原因。我们在前面的例子中看到了这个按钮的效果。在大多数浏览器中，这将稍微缩进文本，以便更清楚地区分引用和周围的文本。

如果你只是想在一段文本上创建缩进效果，正确的做法是使用 CSS 在元素的左边和右边创建填充:不要使用像`<blockquote>`这样的结构标签。

***语义标记和纯文本设备***

在第一章中，什么是 Web 标准？，我标记了这本书的一部分来演示语义标记。首先，我用非语义的方式标记它，我唯一关心的是它看起来如何。然后，我将同一个文档进行语义标记，这样内容结构在没有 CSS 的情况下也有意义。要想亲身体验理解以非语义方式编写的文档有多困难，可以在纯文本浏览器中查看这样的文档。一个容易获得的纯文本浏览器是 Lynx。您可以免费下载 Mac、Unix/Linux 和 Windows 版本。

*Windows 安装*

要在 Windows 上安装 Lynx，您需要下载适用于 Windows 的 Lynx 安装程序。使用产品的安装程序将 Lynx 安装到您的系统上，然后从“开始”菜单或桌面上的 Lynx 图标(如果您允许安装程序创建)运行它。启动时，将显示一个类似于图 3.13 的窗口，“启动 Windows 版 Lynx”。

图 3.13。启动 Windows 版 Lynx。
![1492_3.13](img/0c37ce9e0f792dfcd82a22c03222ba00.png)

*Mac 安装*

图 3.14。在 Mac OS X 上运行 Lynx
![1492_3.14](img/9e6282d59db8adbf48fc3cb2be9146c4.png)

Mac OS X 用户也可以下载 Lynx:只需双击下载的文件，然后按照说明进行操作。要运行 Lynx，您可能需要打开终端应用程序来访问命令行。终端位于您的`Applications`文件夹中的`Utilities`文件夹中。图 3.14，“在 Mac OS X 上运行 Lynx”显示了在 Mac 平台上运行的浏览器。

*Linux*

大多数 Linux 或其他 UNIX 用户可能会发现他们的系统上已经安装了 Lynx。如果没有，快速的网络搜索应该会发现为您的系统开发的软件包。

*使用 Lynx*

Lynx 在 Windows、Mac 和 Linux 机器上的工作方式完全相同。要使用 Lynx，您需要学习一些简单的命令。首先，要访问一个网站，输入 g。

然后 Lynx 会显示一个字段，您可以在其中键入您想要访问的网站的 URL，如图 3.15 所示，“使用 Lynx 打开网页”

图 3.15。使用 Lynx 打开网页。
![1492_3.15](img/c920c34b3d26d0eb246b901079ebac79.png)

按回车键让 Lynx 访问此 URL。如果您试图访问的网站使用 cookie，Lynx 将询问您是否希望允许 cookie；键入 Y 表示是，或 N 表示否，A 表示总是接受来自该站点的 cookie，V 表示从不接受来自该站点的 cookie。如果在 Lynx 中按 H 键，将出现 Lynx 帮助系统。你可以像浏览网站一样浏览它。

一旦你在 Lynx 中加载了一个页面，你可以使用箭头键来导航它。上下箭头键可以让你从一个链接跳到另一个链接，从页面的左侧跳到右侧，从页面的顶部跳到底部。点击右箭头键以跟踪您当前所在的链接；向左箭头键将带你回到上一页。

上下箭头键也将选择页面中的任何表单域。通过键入来选择文本栏。选择所需选项后，按 Enter 键切换复选框和单选按钮。要查看下拉列表中的选项，请选择它，按 Enter 键，然后使用向上和向下箭头滚动浏览列出的项目。再次按回车键使用选定的选项。当你按下回车键时，按钮被“点击”。

您可以使用 Lynx 查看本地文件，这在开发中非常有用。如果您运行的是本地 Web 服务器，比如 Apache 或 IIS，您可以将 Lynx 指向内部本地主机 URLs 但是，如果您将位置传递给 Lynx，它也会读取 HTML 文件，例如，`c:webmyfile.html`。

*注:猞猁间距混乱*

Lynx 很难处理包含空格的路径名。用%20 替换文件路径中的任何空格以加载文件。

如果我在 Lynx 中查看我的非语义文档，其显示如图 3.16 所示，“在 Lynx 中显示非语义标记”，我看到每个元素看起来就像一个段落:它是可读的，但没有强调任何部分，所以标题“什么是 Web 标准？”实际上是一个标题。想象一下，在没有任何结构线索的情况下阅读这一整章，以确定哪一节是哪一节——这就是文本浏览器用户在阅读没有正确标记的文档时不得不忍受的影响。

图 3.16。在 Lynx 中显示非语义标记。
![1492_3.16](img/0c2da3c2e73d14c2253eba7d5f6a5b76.png)

对于屏幕阅读器用户来说，这个问题更加突出。页面标题、列表和其他元素的标记让屏幕阅读器知道以适合这些特定元素的声音来阅读内容的每一部分，就像大声朗读页面一样。你通常会强调一个标题，在列表项之间留下适当的停顿，等等。如果屏幕阅读器知道元素是什么，它也可以做到这一点；它能知道的唯一方法是页面元素是否被正确标记。

图 3.17，“在 Lynx 中显示语义标记”描述了使用一级标题(`<h1>`)作为文档标题以及显示 Web 链接的列表进行标记的同一文档。如您所见，Lynx 现在理解了标题是标题，并按此处理它；Lynx 通过对每个项目符号点应用星号来证明该列表是一个列表。

图 3.17。在 Lynx 中显示语义标记。
![1492_3.17](img/127911eff8fc1018b9f5516e163236dc.png)

*注:Lynx 预览版*

如果你没有安装 Lynx，而你只是想在纯文本设备上快速预览网站，使用在线的 Lynx 浏览器。

##### 摘要

在这一章中，我们讨论了 XHTML，包括它是什么，以及我们为什么要使用它。我们还探索了使用 Dreamweaver 处理 XHTML 的基础知识。

我们花了一些时间讨论语义，看看如何创建一个每个人都能理解的文档——即使是那些使用不能直观显示设计的设备的人。在下一章中，我们将把这个理论应用到实际中，为我们的项目网站建立一个布局。

##### 第四章。构建文档

如果你像大多数人一样，你可能会设计一个网站，考虑它应该是什么样子，然后移动图形元素——要么通过手工编码 HTML 和 CSS，要么使用 Dreamweaver——直到页面“看起来”像你想要的那样。

在这一章中，我们将采用一种完全不同的方法来设计页面。首先，我们将考虑如何构建文档，以使其有效、可访问和有语义。然后，在第 6 章，用 CSS 构造布局，我们将使用 CSS 让文档显示我们想要的样子。

在这一章中，我们将使用 XHTML 来构建我们主页的内容；这将为我们的网站提供一个框架。

##### 新的 XHTML 文档

在本章中，我们将使用 XHTML Strict DOCTYPE 开发我们的文档。XHTML Transitional 允许您使用不推荐使用的元素和属性；这些大多是表象的性质。使用 XHTML Strict 有助于我们记住表示属于 CSS，而不是文档:这是确保我们的站点符合 Web 标准的好方法。

但是，有几个原因可以解释为什么您可能想要使用 XHTML Transitional:

*   该网站将由使用 Dreamweaver 的人维护，但不知道删除 Dreamweaver 插入的表示属性，这些属性在 XHTML Strict 中是无效的。
*   您正在使用一个内容管理系统——或者其他第三方代码——它将验证过渡文档类型，但是包含严格不允许的属性。
*   您特别需要让布局在非常旧的浏览器中工作，比如 Netscape 4。为此，您将使用某些表示属性(比如图像上的`border="0"`),因为这些浏览器提供有限的 CSS 支持。

也就是说，选择使用 XHTML Strict 并不一定是您的最终决定。如果你的目标是严格的，然后意识到一些第三方特性将需要使用过渡的 DOCTYPE，你总是可以改变 DOCTYPE 声明来适应。尽可能地开发严格的 DOCTYPE 将确保您记住在 CSS 中保留表示，它属于那里！

通过选择“文件”>“新建”,在 Dreamweaver 中创建新的 XHTML 文档。在“新建文档”对话框中，选择“基本页面”和“HTML”，然后从“文档类型”下拉列表中选择“XHTML 1.0 严格版”，如图 4.1“在 Dreamweaver 中创建新的 XHTML 页面”所示单击 Create 创建新的 XHTML 页面，然后将该页面保存到 Code Spark 站点，命名为`homepage-layout.html`。

这将在设计视图中为您提供一个空白页。如果使用文档窗口上方的按钮切换到代码视图，您将看到基本的 XHTML 文档，如图 4.2“查看基本 XHTML 文档的代码”所示

此页面将成为我们的主页文档。但是现在，我们将集中精力创建一个符合语义和标准的文档，为网站的设计和结构奠定坚实的基础。

图 4.1。在 Dreamweaver 中创建新的 XHTML 页面。
![1492_4.1](img/cc788283d0a8134b4c55557903d78852.png)

图 4.2。查看基本 XHTML 文档的代码。
![1492_4.2](img/6612268932616f35eb6563dea11f07e9.png)

##### 主要内容区域

让我们从文档的主要部分开始:页面内容。在主页上，内容包括特色教程摘要、近期教程、浏览器统计以及图 4.3“专注于主页主要内容区域”所示的其他内容。我们将把注意力集中在这个图像中没有变灰的区域。

图 4.3。专注于主页的主要内容区域。
![1492_4.3](img/df01e973e49e769bf976339353d88640.png)

使用 Dreamweaver 中的设计视图，在`homepage-layout.html`中键入`Latest ideas`。这将是主页本身的主要标题，所以它应该被标记为一级标题。我们可以在 Dreamweaver 中选择标题文本，然后从属性检查器中选择标题 1，如图 4.4“使用属性检查器创建一级标题”所示。&qv uot；

图 4.4。使用属性检查器创建一级标题。
![1492_4.4](img/9cf2b45b2a90705670766b86ae9e0520.png)

我们在主页上有六块内容。最上面的四个包含了站点上最新教程的细节，并且都有相同的结构:一个标题(链接到教程)，作者的名字和教程的出版日期，作者的图片，以及一些关于教程本身的文本。我们现在可以创建这些元素。

在设计视图中，按 Enter 并添加以下文本:

```
ASP is from Mars â€“ PHP is from Venus
```

选择此文本，并通过在属性检查器中选择标题 2 使其成为二级标题。当文本仍处于选中状态时，在属性检查器的链接字段中键入`#`。这将把文本变成一个链接，您可以在以后修改它以指向实际的教程。按回车键创建一个新段落，并键入:`Julian Carroll Jan 15, 2005`。

现在可以添加作者的图像了。要使用 Dreamweaver 插入图像，请单击工具栏中的“图像”按钮，如图 4.5 所示，“在“插入”工具栏的“常用”面板上选择“图像”按钮”

图 4.5。在“插入”工具栏的“常用”面板上选择“图像”按钮。
![1492_4.5](img/fdc77258b8ca5e78a460df9fcd68f450.png)

当您点按“图像”按钮时，将会打开一个对话框，允许您在电脑中浏览作者图像。找到后，单击确定。

*注:所有图片均有提供！*

我们用来建立这个网站的所有图片都可以作为这本书的代码档案的一部分。如果你还没有这样做，去 sitepoint.com 拿吧。

此时，辅助功能属性对话框，如图 4.6 所示，“添加图像标签的辅助功能属性”应该会打开。如果您告诉 Dreamweaver 在您的首选项中显示图像的辅助功能属性，则会出现此对话框。我们在第 2 章“站点规划和开发设置”中设置 Dreamweaver 时就做到了这一点。

在“辅助功能属性”对话框提供的框中键入一些替代文本。这些文字应该清楚地描述图像，以便用户在关闭图像或使用无法显示图像的设备浏览网站时使用。由于我们的图像包含一些文本(“Julian–PHP & MySQL”)，我们应该将这些信息添加到替换文本属性中，如图 4.6 所示，“添加图像标签的可访问性属性”我添加了以下文本:`Photo of Julian â€“ PHP and MySQL`

图 4.6。添加图像标签的辅助功能属性。
![1492_4.6](img/9c54aeb8263e9443232b110f2b984424.png)

最后，让我们添加几段简短的文字来解释教程。此时，我添加了一些虚拟占位符文本来填充空间。我们的页面现在显示在 Dreamweaver 中，如图 4.7“在 Dreamweaver 中显示文档”所示

图 4.7。在 Dreamweaver 中显示文档。
![1492_4.7](img/909149e437edeabce484787e730dcc47.png)

注意:立即下载模拟拉丁语！

我们使用的虚拟文本是标准的“模拟拉丁语”,由设计师用来填写模型。你可以下载一些你自己的模拟拉丁语。

切换到代码视图，看看我们的 XHTML 是如何形成的。此时，文档应该包含以下标记和内容:

**例 4.1。`homepage-layout.html`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"       

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">       

<html >       

<head>       

<title>Code Spark layout</title>       

<meta http-equiv="Content-Type" content="text/html;       

    charset=iso-8859-1" />       

</head>       

<body>       

<h1>Latest ideas </h1>       

<h2><a href="#">ASP is from Mars - PHP is from Venus</a></h2>       

<p>Julian Carroll Jan 15, 2005</p>       

<p><img src="img/julian.jpg" alt="Photo of Julian - PHP and MySQL"       

      width="104" height="135" /> </p>       

<p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem       

  accusantium doloremque laudantium, totam rem aperiam, eaque        

  ipsa quae ab illo inventore veritatis et quasi architecto beatae       

  vitae dicta sunt explicabo.</p>       

<p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut        

  odit aut fugit.</p>       

</body>       

</html>
```

注意:分屏！

如果您有足够的屏幕空间，在创建 XHTML 文档时使用 Dreamweaver 的分屏视图会很有帮助。分屏功能允许您同时查看设计和代码视图。要切换到拆分屏幕视图，请单击文档窗口上方的拆分按钮。如图 4.8 所示，“在分屏视图中工作”，当您在“设计”视图中向页面添加元素时，您将能够在“代码”视图中看到标记的创建。

图 4.8。在分屏视图中工作。
![1492_4.8](img/39d65459ba2db0d74459ba8002df19b2.png)

我们需要添加三个部分，就像这个为其他特色教程之一。按照上述步骤从左到右、从上到下添加这些部分。完成后，您的文档应该包含以下标记:

**例 4.2。`homepage-layout.html`(节选)**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"       

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">       

<html >       

<head>       

<title>Code Spark layout</title>       

<meta http-equiv="Content-Type" content="text/html;       

    charset=iso-8859-1" />       

</head>       

<body>       

<h1>Latest ideas </h1>       

<h2><a href="#">ASP is from Mars - PHP is from Venus</a></h2>       

<p>Julian Carroll Jan 15, 2005</p>       

<p><img src="img/julian.jpg" alt="Photo of Julian - PHP and MySQL"       

    width="104" height="135" /> </p>       

<p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem       

  accusantium doloremque laudantium, totam rem aperiam, eaque       

  ipsa quae ab illo inventore veritatis et quasi architecto beatae       

  vitae dicta sunt explicabo.</p>       

<p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut       

  odit aut fugit.</p>       

*<h2><a href="#">IIS Security - Tightening the .NET</a></h2>       

<p>Brigitte Walker Jan 11,2005</p>       

<p><img src="img/brigitte.jpg" alt="Photo of Brigitte - ASP and       

    .NET" width="104" height="135" />  </p>       

<p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem       

  accusantium doloremque laudantium, totam rem aperiam, eaque ipsa       

  quae ab illo inventore veritatis et quasi architecto beatae        

  vitae dicta sunt explicabo.</p>       

<p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut        

  odit aut fugit.</p>       

<h2><a href="#">CSS: Designing with Style, not class</a></h2>       

<p>Georgina Laidlaw Jan 7, 2005</p>       

<p><img src="img/georgina.jpg" alt="Photo of Georgina -        

    CSS Design" width="104" height="135" /> </p>       

<p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem       

  accusantium doloremque laudantium, totam rem aperiam, eaque ipsa       

  quae ab illo inventore veritatis et quasi architecto beatae        

  vitae dicta sunt explicabo.</p>       

<p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut        

  odit aut fugit.</p>       

<h2><a href="#">Java - Servlets yourself right</a></h2>       

<p>Thomas Rutter Jan 3, 2005</p>       

<p><img src="img/tom.jpg" alt="Photo of Tom - JSP and Servlets"       

    width="104" height="135" /> </p>       

<p>Sed ut perspiciatis unde omnis iste natus error sit voluptatem       

  accusantium doloremque laudantium, totam rem aperiam, eaque ipsa       

  quae ab illo inventore veritatis et quasi architecto beatae        

  vitae dicta sunt explicabo.</p>       

<p>Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut        

  odit aut fugit.</p>*       

</body>       

</html>
```

***链接到其他教程***

主页底部的两个部分包含的内容略有不同。它们也有标题，但其中一个包含到网站上其他教程的链接，而另一个显示访问者用来浏览网站的浏览器的最新信息。首先，让我们考虑包含教程链接的部分。该部分的布局如图 4.9“查看链接部分的布局”所示

图 4.9。查看链接部分的布局。
![1492_4.9](img/9551dbc5403c63d0dc010a84199fc5a9.png)

“最新教程”部分以表格形式显示，标题位于每列的顶部，数据排列在后面的单元格中。因为这是表格数据，所以使用表格以语义方式组织这些信息是合适的。

首先，在“设计”视图中，添加带有可选文本`RSS`的 RSS 按钮图像。按回车键，输入`Recent Tutorials`；使用“属性”检查器将此标题设为二级标题，并再次按 Enter 键。

现在，让我们插入一个表格。要在 Dreamweaver 中执行此操作，请打开“插入”工具栏的“布局”面板，然后单击“表格”按钮，如图 4.10“单击“插入”工具栏上的“表格”按钮”所示

图 4.10。单击“插入”工具栏上的“表格”按钮。
![1492_4.10](img/96c731e81e9c5aab9746addd210191af.png)

*注:Dreamweaver 的插入工具栏*

您可能已经注意到了“插入”工具栏上的下拉列表:这允许您在不同的工具集之间切换。Dreamweaver 将这些工具集称为“面板”您可以通过从该下拉列表中选择“显示为选项卡”来将不同的面板显示为选项卡。

将打开如图 4.11“表格对话框”所示的表格对话框。这使您能够设置将要插入的表格的功能。给你的表格七行两列。在 Header 部分下，选择 Top 将表格顶行中的单元格转换为`th`元素:表格标题。

图 4.11。表格对话框。
![1492_4.11](img/46438f712fdfd2816926cc8c7822578a.png)

我们还可以在这里添加一个总结。摘要不会在常规的图形浏览器中显示在屏幕上，但是为屏幕阅读器用户提供了关于表的附加信息，以帮助他们将它放入上下文中。如果由于页面布局的原因，表格的上下文很模糊，那么这个字段和标题字段就特别重要。然而，在我们的文档中，应该很容易理解表格的内容。

单击对话框中的“确定”按钮，将表格插入到文档中。现在，将表格数据输入到单元格中:从顶行的标题“标题”和“已发布”开始，然后填写剩余的教程及其发布日期，如图 4.12“在设计视图中查看表格”所示

图 4.12。在设计视图中查看表。
![1492_4.12](img/0b815eee1f0ebac6297afd097574b3ca.png)

通过选择标题并在属性检查器的“链接”字段中输入一个`#`，将每个教程标题制作成一个链接，就像您对教程标题所做的那样。完成后，切换到代码视图查看这一部分的标记。它应该是这样的:

```
<h2>Recent Tutorials</h2>       

<table width="100%" border="0" summary="This table shows the most       

    recent tutorials posted on the site and their publication        

    date.">       

  <tr>       

    <th scope="col">Title</th>       

    <th scope="col">Published</th>       

  </tr>       

  <tr>       

    <td><a href="#">CSS forms - Massive feedback distortion?       

      </a></td>       

    <td>26-12-2005</td>       

  </tr>       

  <tr>       

    <td><a href="#">Buttons &amp; Dials - Java Controls Explained       

      </a></td>       

    <td>23-12-2005</td>       

  </tr>       

  <tr>       

    <td><a href="#">Graphic Violence - Crazy Graphs with PHP       

      </a></td>       

    <td>19-12-2005</td>       

  </tr>       

  <tr>       

    <td><a href="#">Making the .NET Framework Work</a></td>       

    <td>16-12-2005</td>       

  </tr>       

  <tr>       

    <td><a href="#">CSS: Designing with Style, not Class       

      </a></td>       

    <td>12-12-2005</td>       

  </tr>       

  <tr>       

    <td><a href="#">JavaScript's Presentational Presence</a></td>       

    <td>9-12-2005</td>       

  </tr>       

</table>
```

***显示浏览器统计数据***

这个主要内容区域的最后一部分显示浏览器统计信息。此内容显示在图 4.13“设计主页的浏览器统计显示”中的 Fireworks 设计中

图 4.13“设计主页的浏览器统计显示”由一个标题和一个饼图组成，显示了不同类型浏览器的市场份额。提供了文本描述，以及图表的图例。

首先，添加标题。然后，插入图像；不要忘记添加一个`alt`属性，清楚地解释图像是什么。没有必要描述图表中的数据:图表下面的文字对此有所帮助。

*注意:使表格数据可访问*

图表是简洁显示大量数据的好方法，但是它不太容易访问。为了纠正这一点，您可以在单独的页面上显示图表的数据，并使用`img`元素的`longdesc`属性来链接到它。我们将在第 7 章“可访问性”中更详细地了解`longdesc`。

图 4.13。设计主页的浏览器统计显示。
![1492_4.13](img/7db40db0a7d2e24c85b18e8dac6ae61e.png)

我们现在可以添加列表了。我们使用 Dreamweaver 的属性检查器中的“无序列表”按钮来创建列表，如图 4.14“创建无序列表”所示在文档中键入第一个列表项，就像输入新段落一样，然后单击属性检查器上的“无序列表”按钮。当您按 Enter 键时，将会创建一个新的列表项。

图 4.14。创建一个无序列表。
![1492_4.14](img/9f836bc95e112ebb2a7345f0801f592b.png)

创建完列表中的所有项目后，按两次 Enter 键关闭列表并创建一个新段落。现在，输入这一部分的两段文字。就是这样！主页内容区最后一部分的代码现在已经完成:

```
<h2>Browser Stats - December - 04</h2>       

<p><img src="img/browser_chart.gif" alt="Pie chart showing        

    browser statistics for Dec 2004" width="180" height="180" />       

</p>       

<ul>       

  <li>IE6 Win (72%)</li>       

  <li>Firefox/Moz (19%)</li>       

  <li>IE5+ Win (&lt;5%)</li>       

  <li>Safari (2%)</li>       

  <li>Opera (&lt;1%)</li>       

  <li>Other (&lt;1%)</li>       

</ul>       

<p>December saw Firefox's market share jump 1% across all major       

  site categories presumably on the back of it's 1.0 launch push.       

  Increases of as much as 3% were observed in some        

  technically-oriented categories.</p>       

<p>Overall, though IE6 continued the gradual decrease in market        

  share it has endured since it held 92% in November 2003, it       

  still enjoys 4 times greater usage than any other browser.</p>
```

如果你在浏览器中查看页面，你会看到所有显示的内容，如图 4.15“在 Firefox 1.0 中显示文档”所示:我们的逻辑标题、段落、表格和列表以浏览器的默认显示风格显示。

图 4.15。在 Firefox 1.0 中显示文档。
![1492_4.15](img/1a8ad8ec697e677fb7993197e7c36d39.png)

##### 其他页面元素

现在我们已经完成了主页主要内容区域的结构，是时候继续考虑页面的其余部分了。我们有标题区，其中包含徽标和标语，网站控件，帮助用户改变文本大小，以及主导航。我们还必须考虑显示在布局侧边栏中的内容:一个搜索表单，以及指向网站上各种主题和其他感兴趣的项目的快速链接。

在我们决定将这些元素放在文档中的什么位置之前，让我们花点时间想想网站访问者将如何使用这些页面。拥有常规网络浏览器的用户将看到完整的布局，使用 CSS，我们将能够定位所有的页面元素，使网站尽可能地可用。对于这些用户来说，页面元素在文档中的位置并不重要，因为他们可以看到完整的页面设计。但是，对于纯文本设备的用户来说，各种内容元素在实际 XHTML 文档中的位置非常重要。

我们的主页将由纯文本浏览器或屏幕阅读器按照内容在实际 XHTML 文档中出现的顺序显示或朗读，从标题开始。目前，我们的文档只包含页面内容，因此，在标题之后，屏幕阅读器将开始阅读页面上的第一个元素:我们的“最新想法”标题，如图 4.16 所示，“在纯文本的 Lynx 浏览器中显示文档。”

图 4.16。在纯文本 Lynx 浏览器中显示文档。
![1492_4.16](img/f87f0491174a33b08b4998f4d6d2a5ca.png)

页面的标题部分通过提供关于网站本身的信息——徽标和标语——以及快速访问网站的主导航，包括网站地图，来帮助用户定位，如图 4.17“标题区域，包括主导航”

图 4.17。标题区域，包括主导航。
![1492_4.17](img/8f0d21ccca5e859d0f61ca76c1164b55.png)

*注意:简化纯文本导航*

对于使用纯文本浏览器的访问者来说，网站地图非常有用:它让他们可以快速地从一个页面跳到另一个页面，而不必遵循复杂的导航结构，虽然在图形浏览器中易于使用，但通过其他方法使用时会慢得多。记住:对于屏幕阅读器的用户，或者那些必须使用键盘浏览 Web 文档的人来说，Web 页面是线性文档。例如，使用屏幕阅读器的访问者必须等待网页被大声朗读出来，才能到达他们想要的链接。网站地图可以为这些访问者节省时间，也可以为那些使用普通浏览器的访问者减少麻烦。

由于标题区域包含了有助于用户理解和浏览网站的信息，所以我们应该把这个元素放在文档的顶部，主页主要内容之前，这似乎是合乎逻辑的。

图 4.18“侧边栏”中显示的信息在帮助用户立即理解和访问网站的能力方面不太重要。作为教程和信息的列表，它也可能变得很长。

如果我们把这个元素放在主页的主要内容之前，屏幕阅读器的用户需要在到达主要内容之前听完所有这些信息；这将开始变得相当沉闷后，第一页！因此，让我们将这一部分添加到页面主要内容之后，当前文档的底部。然后我们可以用 CSS 把它放在我们的主要内容旁边。

图 4.18。侧栏。
![1492_4.18](img/0abc1048351c98ecd180d4358bbde53a.png)

##### 航向和主导航

既然我们已经决定了在文档中放置其余页面元素的位置，我们就可以开始添加标题区域了。

返回到文档，在 Dreamweaver 的“设计”视图中，将光标放在“最新想法”标题之前。切换到代码视图，确保光标在`h1`元素之外。切换回设计视图并插入代码 Spark 徽标(`logo.gif`)，记住添加适当的替代文本。在图片旁边，键入标语`Inspiration for Coders`；这应该位于 Code Spark 徽标的底部。

接下来，我们将添加我们的辅助功能控件。按回车键并键入页面控件。我们的页面控件只不过是一个链接列表，所以我们将把它们作为无序列表添加到页面中。单击无序列表按钮插入第一个列表项，然后插入第一个辅助功能控件图像`control_larger.gif`。接下来，将图像制作成链接:选择图像并在属性检查器的链接字段中键入`#`。按回车键插入下一项，并对其他控件(`control_smaller.gif`、`control_low_graphics.gif`和`control_default_style.gif`)重复该过程。

图 4.19。创建页面的标题区域。
![1492_4.19](img/a17e31f7e0b17c4cd5ae2e6859d6d3bb.png)

当您在设计视图中仔细阅读文档时，它看起来应该如图 4.19“创建页面的标题区域”所示不要担心它现在看起来有点乱；稍后我们将使用 CSS 来转换这些内容。

我们最后的任务是在这个标题区域添加主导航，它将链接到教程，关于，联系和网站地图页面。同样，导航包含一个无序的链接列表，所以继续将它们添加到文档中。切换到代码视图，我们可以看到构成标题部分的标记:

**例 4.3。`homepage-layout.html`(节选)**

```
<p><img src="img/logo.gif" alt="Code Spark" width="290"         

    height="160" />Inspiration for Coders </p>        

<p>Page Controls</p>        

<ul>        

  <li> <a href="#"><img src="img/control_larger.gif"        

      alt="Increase Text Size" width="43" height="35"        

      border="0" /></a></li>        

  <li><a href="#"><img src="img/control_smaller.gif"         

      alt="Decrease Text Size" width="43" height="35"        

      border="0" /></a></li>        

  <li><a href="#"><img src="img/control_low_graphics.gif"         

      alt="Low Graphics" width="43" height="35"        

      border="0" /></a></li>        

  <li><a href="#"><img src="img/control_default_style.gif"        

      alt="Default Style" width="43" height="35"        

      border="0" /></a></li>        

</ul>        

<ul>        

  <li><a href="">Tutorials</a></li>        

  <li><a href="">About</a></li>        

  <li><a href="">Contact</a></li>        

  <li><a href="">Sitemap</a></li>        

</ul>
```

##### 侧栏

我们将添加到文档中的最后一个部分是侧边栏，正如我们已经决定的那样，它将被放在文档结构中主要内容的下面。

在“设计”视图中，将光标放在主要内容文本的底部，浏览器统计信息部分的下方。侧边栏的第一部分是一个搜索框，所以添加一个三级标题“搜索这个网站”

要创建搜索框，请使用“插入”工具栏的“表单”面板添加一个表单元素，如图 4.20“添加表单”所示该表单将显示为红色虚线轮廓。

图 4.20。添加表单。
![1492_4.20](img/84352bee6a3adf48df4ff6d2149012f8.png)

在表单中插入文本字段。这样做时，将会打开图 4.21 所示的“输入标签辅助功能属性”对话框，帮助您将正确的属性添加到元素中。

图 4.21。“输入标签辅助功能属性”对话框。
![1492_4.21](img/e6b1535cd70f254cd7d447b3513ae2d6.png)

我们最初的设计没有包括搜索栏的标签，但是我打算加入一个:当我们为了可访问性的目的来验证我们的站点时，它会有所帮助。在上面的图 4.21“输入标签可访问性属性对话框”中，我添加了标签`Search keywords`并选择了“用标签标签包装”选项。

单击“确定”插入文本输入和标签。在属性检查器中，将输入字段的名称更改为`keywords`。我还在标签搜索关键字之后，搜索框之前添加了一个`<br />`。Shift-Enter 在 Dreamweaver 中创建一个`<br />`。

现在，使用“插入”工具栏插入一个按钮。此按钮不需要标签，因此在单击“确定”之前，请选择“无标签”选项。选择按钮，然后使用属性检查器将值更改为`Search`，如图 4.22 所示，“将按钮的值更改为”搜索。""

图 4.22。将按钮的值更改为“搜索”
![1492_4.22](img/ee8e26f7a58a43747346fcdeea55044c.png)

如果您检查“代码”视图，我们刚刚添加的表单的标记应该如下所示:

**例 4.4。`homepage-layout.html`(节选)**

```
<h3>Search this Site</h3>        

<form name="form1" id="form1" method="post" action="">        

  <label>Search keywords<br />        

  <input name="keywords" type="text" id="keywords" />        

  </label>        

  <input type="submit" name="Submit" value="Search" />        

</form>
```

添加另一个标题——“教程主题”——作为第三级标题，并使用无序列表标记网站的主要部分。将列表项中的文本制作成虚拟链接，因为这些链接最终会链接到网站的实际部分:

*   PHP 和 MySQL
*   CSS 设计
*   JavaScript 和 DHTML
*   ASP &。网
*   jsp 和 servlet

图 4.23。添加搜索功能和三个列表后查看侧边栏部分。
![1492_4.23](img/b99ff8370532d5e3e5d0038d72757ae3.png)

我们现在可以添加另一个标题。插入“读者收藏夹”作为第三级标题，同样，使用一个带有虚拟链接的列表来标记网站上最喜欢的教程。图 4.23，“添加搜索功能和三个列表后查看侧边栏部分。”添加三个列表后，在设计视图中显示此部分。

##### 验证您的 XHTML

我们已经完成了基本的 XHTML 文档。稍后，我们将需要向这个文档添加一些分组元素，使我们能够对文档进行样式化。然而，通过首先考虑内容应该如何结构化，我们已经做了很多工作来确保我们的文档是语义结构化的，并且可以在最基本的级别上访问。我们应该承担的最后一项任务是验证我们的文档，以确保它构成正确的 XHTML。

***在 Dreamweaver 中验证***

要验证文档，请单击文档窗口顶部的“验证标记”按钮，并选择“验证当前文档”，如图 4.24“验证当前文档”所示

图 4.24。验证当前文档。
![1492_4.24](img/65cc8ee66fb18d606ce6561c26a0783f.png)

如果幸运的话，面板上会显示“没有发现错误或警告”的消息。但是，虽然 Dreamweaver 允许您创建具有 XHTML Strict DOCTYPE 的文档，但它有时会缺少必需的元素，或者添加该 DOCTYPE 不允许的属性。幸运的是，这些问题很容易找到并删除:在 Dreamweaver 的验证器运行后，任何问题都会在结果面板中列出，如图 4.25“检查结果面板中出现的错误”所示

图 4.25。查看结果面板中显示的错误。
![1492_4.25](img/6d6f9459dcbc887121520b424748828b.png)

您可能会看到的一个错误如下:

<q>标签:“label”不允许出现在:“form”中，它只允许出现在:a，abbr，acronym，address，b，…</q>

该错误表明 label 元素需要包含在某个其他元素中(不仅仅是 form)。XHTML Strict——顾名思义——对哪些元素可以包含在哪些其他元素中非常严格。我们可以通过将整个部分包装在`<p>`和`</p>`标签中来解决这个问题，就像这样:

**例 4.5。`homepage-layout-strict.html`(节选)**

```
<p><label>Search keywords<br />        

  <input name="keywords" type="text" id="keywords" />        

  <input type="submit" name="Submit" value="Search" />        

  </label></p>
```

现在，重新验证您的文档，看看它是否没有错误。如果没有，请浏览错误列表，并逐个修复它们。很快，你的工作应该没有错误，并准备好了！将文件另存为`homepage-layout-strict.html`。

你使用 Dreamweaver 越多，你就越能理解它的古怪之处，你就能更好地修改它的标记。XHTML 有时会变得非常复杂，Dreamweaver 8 在大多数情况下都能很好地完成标记，所以请尽量放松。

##### 摘要

在这一章中，我们采用了一种可能被视为有点不寻常的方式来开始我们的页面布局。我们几乎只关注文档的结构，以及如何标记实际内容，牢记我们的标准遵从性目标。

在接下来的两章中，我们将继续看 CSS。我们将看到如何利用这个结构化的、有效的文档，并使用 CSS 创建一个视觉上有吸引力的布局，而不损害其可访问性或语义结构。

以上是使用 Dreamweaver 8 构建您自己的符合标准的网站的摘录。下一步是什么？

[下载这些 PDF 格式的章节](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，你就有了一份可以随时参考的副本。

查看这本书的目录,找出到底包括了什么。

现在就在 SitePoint.com 买一本你自己的书吧。

我们希望您喜欢使用 Dreamweaver 8 构建自己数据库驱动的网站。

## 分享这篇文章`