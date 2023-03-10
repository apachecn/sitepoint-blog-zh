# DHTML 乌托邦:使用 JavaScript 和 DOM 的现代网页设计

> 原文：<https://www.sitepoint.com/dhtml-utopia-modern-web-design/>

动态 HTML，简称 DHTML，是一组 Web 开发技术的名称，主要用于具有重要用户输入功能的网页。DHTML 意味着操纵 HTML 文档的文档对象模型，摆弄样式信息中的 CSS 指令，并使用客户端 JavaScript 脚本将一切联系在一起。

##### *DHTML 乌托邦:使用 JavaScript 的现代网页设计& DOM*

这段摘录摘自 SitePoint 的新版本， *DHTML Utopia:使用 JavaScript & DOM* 的现代网页设计。本书包含了 300 多页的 DHTML 技术，丰富了用户体验，同时又不牺牲可用性或符合标准。

作者是 SitePoint 自己的 Stuart Langridge，他利用 Web 标准，在构建引人注目的、可用的和交互式的 DHTML 应用程序时，将代码与标记分开。

DHTML 乌托邦:使用 JavaScript 的现代 Web 设计& DOM 探索了文档对象模型、远程脚本、Ajax、XML-RPC 和 xPath 等技术，向您展示了如何为自己的 Web 项目创建应用程序。构建您自己的符合标准的分层导航菜单；创建动画工具提示；使用正则表达式验证表单数据，并向用户提供动态反馈；构建一个 AJAX 注册表单，让用户知道他们选择的用户名是否已被占用，而无需提交表单。

这本书提供了无数 DHTML 的应用，这些应用将提高你的站点在支持标准的浏览器中的交互性，并在不支持标准的浏览器中优雅地降级。

这前四章应该会让你体验一下这个动作。要了解这本书的更多信息，请访问这本书的第页，或者查看整个出版物的内容[。和往常一样，如果你愿意的话，你可以](https://www.sitepoint.com/books/dhtml1/toc.php)[下载这个节选的 PDF 文件](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。现在，到第一章。

##### 第一章。DHTML 技术

在这个介绍部分，我将提供一些你需要知道的事情的简要概述:构成 DHTML 网站的构件。如果你需要恢复记忆，你会发现这本书很有用。如果你已经知道了所有这些细节，你可能还是想浏览一下这一章；你甚至会对其中的一些感到有点惊讶。在接下来的几页中，我们将了解到 DHTML 实际上是内容的 HTML、设计的层叠样式表和交互性的 JavaScript 的组合。将这些技术混合在一起可能会导致一个不起眼的炖菜或一个浮夸的自助餐。这都是烹饪的艺术，所以让我们开始摇动那些锅碗瓢盆吧！

##### HTML 起点

网站是用 HTML 写的。如果你正在读这本书，你几乎肯定知道什么是 HTML，并且可能至少对它有一些经验。对于一个成功的 DHTML 增强的网站来说，你的 HTML 有两点很关键:有效和语义。这些需求可能会迫使你放弃以前编写 HTML 的经验。它们可能还需要一种不同的方法，而不是让您喜欢的工具为您编写 HTML。

***逐步升级到有效的 HTML***

HTML 推荐标准中列出了一组特定的规则，规定了 HTML 应该如何编写。符合这些规则的 HTML 被称为“有效的”您的 HTML 需要是有效的，这样它才能被用作构建 DHTML 增强的基础。虽然这组规则相当复杂，但是您可以通过遵循一些简单的准则来确保您的 HTML 是有效的。

**正确嵌套标签**

不要让标签互相“交叉”。例如，不要使用如下所示的 HTML 代码片段:

```
Here is some <strong>bold and <em>italic</strong> text</em>.
```

这里，`<strong>`和`<em>`标签相互交叉；它们嵌套不正确。嵌套对于 DHTML 的正确使用极其重要。在本书后面的章节中，我们将研究 DOM 树，不正确的嵌套导致问题的原因将变得清晰。现在，只要记住，如果你跨越了你的标签，每个浏览器会根据不同的规则(而不是根据标准)以不同的方式解释你的代码。除非你做对了，否则任何跨浏览器控制页面外观和功能的希望都会落空。

**关闭集装箱标签**

包含其他项目的标签，如`<strong>`或`<p>`，应始终以`</strong>`或`</p>`或适当的结束标签结束。知道哪些标签包含内容(例如文本或其他标签)并确保关闭它们是很重要的。例如，`<p>`的意思不是“在这里放一个段落分隔符”，而是“一个段落从这里开始”，应该和`</p>`搭配，“这个段落到这里结束。”(那些知道如何处理容器标签的人会意识到，HTML 4.01 实际上并不要求所有的容器标签都是关闭的(尽管 XHTML 仍然要求)。然而，关闭容器标签从来不是无效的，尽管有时不这样做是无效的。关闭所有内容比记住哪些标签允许打开要容易得多。)同样的逻辑也适用于`<li>`标签。

**始终使用文件类型**

文档类型(或 DOCTYPE)描述了已经使用的 HTML 的方言；有几种不同的选择。在本书中，我们将使用名为 HTML 4.01 Strict 的方言。(如果你在想，“但是我想用 XHTML！”那么我敢打赌，您已经对 DOCTYPEs 有了足够的了解，可以正确地使用它们。)您的 DOCTYPE 应该出现在每个 HTML 页面的最顶端，应该是这样的:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" 

    "https://www.w3.org/TR/html4/strict.dtd">
```

这些信息可以在一行中输入，或者在`EN"`后换行。暂时不要担心这意味着什么:只要确保把它放在每一页的顶部。文章 [*用正确的 DOCTYPE 修复你的站点！*](http://www.alistapart.com/articles/doctype/) ，发表在上，列出了你可能想使用的所有文档类型，以及你为什么需要使用它们。我经常访问那篇文章来剪切和粘贴我需要的那篇！

**验证您的页面**

最重要的页面创建步骤是检查您的 HTML 是否有效。有许多工具可以下载并在你自己的计算机上运行来测试你的代码的有效性——一些 HTML 编辑器甚至内置了这样的工具——或者你可以使用许多在线验证器中的一个，其中最常见的是 [W3C 自己的验证器](http://validator.w3.org/)。一个验证器会告诉你如何调整你的 HTML 以使它与 DHTML 技术兼容。构成有效 HTML 的最终参考是 HTML 推荐标准。它既复杂又详细，但是如果你对 HTML 应该如何编写有任何疑问，或者一个标签是否真的存在，你会在那里找到答案。如上所述，浏览器依赖于一个标准，该标准描述了应该如何解释经过验证的 HTML。但是，没有标准来描述无效的 HTML 应该如何解释；每个浏览器制造商都建立了自己的规则来填补这一空白。试图理解每一条规则将是困难和费力的，你有更好的事情要做。坚持使用有效的 HTML 意味着你发现的任何问题都被认为是浏览器中的错误——你可以解决这些错误。因此，使用有效的 HTML 可以让你有更多的时间和家人在一起，玩台球等等。如果你问我，这是一个很好的理由。

***逐步提升到语义 HTML***

除了有效性之外，你的 HTML 应该是语义性的，而不是表现性的。这意味着您应该使用 HTML 标记来描述文档中某个元素的性质，而不是该元素的外观。因此，如果你的意思是“在这里放一个空行”，就不要使用`<p>`标签用它来表示“一个段落从这里开始”(并在该段落的结尾放置一个`</p>`)。不要用`<blockquote>`来表示“缩进下一段文字”用它来表示，“这个块是一个报价。”如果你以这种方式标记你的 HTML，你会发现以后应用 DHTML 技术要容易得多。这种方法被称为*语义标记*——一种奇特的说法，“使用标签来描述意义。”

让我们看几个例子片段。首先，想象你的网站有一个不同部分的链接列表。这个列表应该根据它是什么来标记:一个列表。不要做成一组用`<br>`标签隔开的`<a>`标签；这是一个列表，所以应该用`<ul>`和`<li>`标记出来。它可能看起来像这样:

```
<ul> 

  <li><a href="index.html">Home</a></li> 

  <li><a href="about.html">About this Website</a></li> 

  <li><a href="email.html">Contact details</a></li> 

</ul>
```

你会发现自己经常使用`<ul>`标签。网站中的许多项目实际上是列表:面包屑轨迹是链接列表，菜单结构是链接列表，照片库是图像列表。

类似地，如果您的列表包含与注释相关联的项目，也许应该将其标记为定义列表:

```
<dl> 

  <dt><a href="index.html">Home</a></dt> 

    <dd>Back to the home page</dd> 

  <dt><a href="about.html">About this Website</a></dt> 

    <dd>Why this site exists, how it was set up, and who did it 

    </dd> 

  <dt><a href="email.html">Contact details</a></dt> 

    <dd>Getting in contact with the Webmaster: email addresses 

      and phone numbers</dd> 

</dl>
```

记住:页面的外观并不重要。重要的部分是页面中的信息以描述它是什么的方式被标记。HTML 中有很多标签；不要认为它们是在页面上展示信息的一种方式，而是定义信息含义的一种手段。

如果你不使用 HTML 来控制你的页面的表现，你怎么能让它们看起来像你想要的样子呢？这就是级联样式表的用武之地。

##### 添加 CSS

级联样式表(CSS)是一种允许你描述你的 HTML 表现的技术。本质上，它允许你陈述你希望页面上的每个*元素*是什么样子。元素是一段 HTML，代表一件事:一个段落，一个标题，一个图像，一个列表。元素通常对应于特定的标签及其内容。当使用 CSS 样式时，DHTML 页面可以独立地处理页面的外观和内容。这是一个方便和干净的分离。如果你想看起来不错，你需要学会如何打扮*和*定期去健身房！

***一个简单的 CSS 例子***

假设你希望你的主页标题(一个`<h1>`标签)以大红色居中文本显示。您应该在样式表中指定，如下所示:

```
h1 { 

  font-size: 300%; 

  color: #FF0000; 

  text-align: center; 

}
```

参见本章末尾的“进一步阅读”部分，获得一些 CSS 入门教程的链接，如果上面的几行对你来说没有太大意义，这些链接应该会有所帮助。

下面是应用这些样式前后的一个简单的 HTML 页面:

**图 1.1。那个 HTML 的风格！**

![](img/43857624748257cc8d2066bb2d6f6c68.png)

这里的关键点是将表示方面从 HTML 中移除，并将它们放入样式表中。例如，如果您通过在 HTML 中放置`<font>`标签来增大页面标题，那么您需要将这些标签粘贴到每一个使用了标题的页面中。通过使 HTML 具有语义并将页面的表示移入 CSS，您可以通过一个样式表控制整个站点标题的外观。这使得你作为网站开发者的工作变得更加容易。

当然，事情没那么简单。尽管 CSS 的完整定义允许你做一些相当令人惊奇的事情，并且在很大程度上控制你的页面的显示，但是并不是每个浏览器都支持 CSS 提供的所有功能。

为了了解浏览器对 CSS 支持的差异，您需要知道 CSS 能做什么。有两种浏览器不兼容:给定浏览器没有实现的东西，以及它没有正确实现的东西。偶尔，浏览器也会添加他们自己的“特殊功能”，但我们不会担心这本书里的那些。

缺失的实现相对容易处理:如果你想让你的 CSS 在没有实现它们的浏览器中工作，不要依赖这样的规则。这可能是一种痛苦，特别是因为世界上最常用的浏览器，Windows 的 Internet Explorer，在其 CSS 支持中有一些严重的漏洞；然而，这种“解决方案”往往是一种必要的妥协。学习哪些规则你能使用，哪些不能使用是通往 CSS 大师之路的一步。

执行不力的标准是一个更大的问题。在这种情况下，浏览器会出错。CSS 大师的另一个步骤是准确理解每个浏览器做错了什么，以及如何解决这些问题。不过，你一开始并不需要这些知识:你会在学习的过程中逐渐掌握。不同浏览器中 CSS 错误的解决方法通常是使用 CSS *hacks* 来实现的。这些黑客利用浏览器的 CSS 解析器中的错误，向其提供特定的样式表指令，以解决其糟糕的标准实现。在网上的不同地方，为每个浏览器记录了大量的 CSS 技巧；详见“进一步阅读”一节。

学习理解和适应各种浏览器中 CSS 处理的不确定性是有效使用 CSS 所需要的工作的一部分。虽然工作量很大，但许多 CSS 错误只有在复杂使用这种技术时才会显现出来；大多数 CSS 可以跨平台和浏览器完美处理，不需要黑客攻击或复杂的测试。

虽然 CSS 很强大，但它并没有给我们提供真正的表示灵活性。CSS 的功能一直在增加，更多的“交互式”特性也在不断地被添加到 CSS 规范中。然而，它不是为构建真正的交互式网站而设计的。为此，我们需要 DHTML 的最后一个构件:JavaScript。

##### 添加 JavaScript

JavaScript 是一种简单但功能强大的编程语言。它用来给你的网站添加动态行为 DHTML 中的 D。HTML 定义了页面的结构，CSS 定义了页面的外观，但动作，即当你与页面交互时发生的事情——通过点击按钮、拖动图像或移动鼠标——是在 JavaScript 中定义的。JavaScript 使用文档对象模型，在下一章中描述，将动作附加到不同的事件(鼠标悬停、拖动和单击)。我们不打算在这里详细描述所有血腥的 JavaScript 语法——如果你需要的话,“进一步阅读”部分有一些 JavaScript 教程的链接。

***一个简单的 JavaScript 例子***

下面是一段简单的 JavaScript 代码，当用户切换到字段之外时，它将文本字段的值转换为大写。首先让我们看看旧的，不好的方法:

**例 1.1。`oldlisteners.html`(节选)**

```
<input id="street" type="text" 

    onchange="this.value = this.value.toUpperCase();">
```

在本书中，我们将推荐一种更现代的技术。首先，HTML:

**例 1.2。`newlisteners.html`(节选)**

```
<input id="street" type="text">
```

第二，JavaScript，它通常位于页面的`<head>`部分:

**例 1.3。`newlisteners.html`(节选)**

```
<script type="text/javascript"> 

function uppercaseListener() { 

  this.value = this.value.toUpperCase(); 

} 

function installListeners() { 

  var element = document.getElementById('street'); 

  element.addEventListener('change', uppercaseListener, false);  

} 

window.addEventListener('load', installListeners, false); 

</script>
```

第一个函数负责转换文本。第二个函数确保第一个函数连接到正确的 HTML 标记。页面完全加载后，最后一行执行这个连接。尽管这意味着更多的代码，但是请注意它是如何保持 HTML 内容的干净和简单的。在以后的章节中，我们将会深入探讨这种方法。现在不要担心太多的技术问题——有足够的时间来解决这个问题！

***拿点工具！***

一个好的 JavaScript 开发环境会使使用 JavaScript 变得更加容易。Internet Explorer (IE)中的测试页面可能会有不尽人意的地方；如果您的页面生成了 JavaScript 错误(在您构建它的过程中，它总是会这样！)，IE 不太可能有助于诊断它们在哪里，或者是什么。JavaScript 调试最有用也最简单的工具是 Mozilla 或 Mozilla Firefox 中的 JavaScript 控制台。这个控制台将清楚地显示页面上哪里出现了 JavaScript 错误，以及错误是什么。在构建 JavaScript 脚本时，这是一个非常有价值的工具。Mozilla Firefox 几乎可以在所有平台上运行，下载量也不大；它还提供了比 Internet Explorer 更好的 CSS 支持，应该成为您开发工具包的一部分。除此之外，Mozilla 中还有 JavaScript 调试器，名为 Venkman 如果你是那种使用其他语言从事大型项目并习惯于调试器的程序员，Venkman 可能会很有用，但要知道它需要一点设置。然而，在实践中，当你用 DHTML 增强你的站点时，你不需要像调试器那样复杂的东西；JavaScript 控制台和明智地使用警报语句来识别正在发生的事情将帮助您度过几乎所有的情况。

另一个绝对有用的工具是一个好的代码编辑器，用来编写你的网站。JavaScript 的语法高亮显示是一个非常方便的特性；当你写代码的时候，它让你的代码更容易阅读，并且当你遗漏了一个括号或者引号的时候，它会很快提醒你。编辑器是一个非常个人化的工具，在你找到你的王子之前，你可能不得不亲吻一些青蛙，但是一个好的编辑器将会加速和简化你的编码工作。如果你还没有喜欢的程序，大量强大的、可定制的编辑器是免费的。但是，如果您目前正在 Windows 记事本中编写代码，请看看还有什么其他产品可以提供您更喜欢的环境。如前所述，您需要突出显示语法；一种结合页面外部验证的方法也很有用。Textpad 和 [Crimson Editor](http://www.crimsoneditor.com/) 是基于 Windows 的编辑器，如果你在 Windows 平台上开发，它们涵盖了基本的内容；Mac 用户倾向于信[BBEdit](http://www.barebones.com/)；Linux 用户有 gedit 或 Kate 或 vim 来做基础工作，还有 Emacs。

JavaScript 是 DHTML 运行的引擎。DHTML 专注于操纵 HTML 和 CSS，使页面按照用户的要求运行，而影响这种操纵的正是 JavaScript。在本书的其余部分，我们将会越来越详细地探究这种操纵。

##### 进一步阅读

如果你对 CSS 本身有更多的了解，试试这些链接。

SitePoint 对 CSS 世界的简单介绍是一个很好的起点。

W3Schools 的 CSS 教程无论你是在学习，还是只是在温习你的 CSS 知识，都很有帮助。

CSS Zen Garden 很好地展示了级联样式表的力量。它有一个真正的惊喜因素！

如果你需要隐藏特定的 CSS 指令(或发送特定的指令)到特定的浏览器，这份全面的 CSS 攻击列表会向你展示哪些浏览器会受到特定攻击的影响。

这个网站展示了各种浏览器中的 CSS 问题，并解释了如何解决它们。

CSS 讨论邮件列表致力于讨论 CSS 以及在现实世界中使用它的方法；换句话说，实际用途和应用。”相关的 wiki 是有用的提示和技巧的储存库。

如果你在寻找更权威的东西，SitePoint 的书， [*HTML Utopia:使用 CSS*](https://www.sitepoint.com/books/css1/) 设计无表格是 CSS 初学者的完整指南和参考。[*《CSS 选集:101 个小技巧、小窍门& Hacks*](https://www.sitepoint.com/books/cssant1/) 如果你更喜欢边做边学，那么这是一个完美的选择。

网上很多教程都涉及 JavaScript。有些人探索了 DHTML 和 DOM，而有些人没有；你应该试着找到前者。

本教程为非程序员提供了 JavaScript 基础知识的介绍。本文中介绍的一些技术不如本书中介绍的替代技术现代，但是您会对语言本身有很好的感觉。

Peter-Paul Koch 列出的 JS 技术和脚本涵盖了这一领域的大量内容。

##### 摘要

在这一章中，我们概述了 DHTML 的基本组成部分:HTML 到底是什么，如何使用 CSS 在文档中安排和显示它，以及如何使用 JavaScript 增加交互性。在这本书的其余部分，我们将着眼于你可以用来开始使你的网站动态的基本技术，然后继续讨论某些涵盖特定领域的高级脚本技术。继续表演！

##### 第二章。文档对象模型

有一天，有人走进来，在一个非常孤立地展示的电传打字机上，看到了这样一句不祥的话:

![Beta Character](img/8cd14982bed712da349f9659a35a66de.png)
的值会产生 dom！

…这句话本身就是如此引人注目！完全没有意义，但它看起来像什么…一个警告？什么是“多姆？”
——[丹尼斯·m·里奇](http://cm.bell-labs.com/cm/cs/who/dmr/odd.html)

网页是一个文档。要查看该文档，您可以在浏览器窗口中显示它，也可以查看 HTML 源代码。两种情况下都是同一份文件。万维网联盟的文档对象模型(DOM)提供了查看同一文档的另一种方式。它将文档内容描述为 JavaScript 程序可以看到的一组对象。自然，这对于出现大量脚本的 DHTML 页面非常有用。(上面的引用纯属巧合——它来自网络出现之前的时代！)

根据[万维网联盟](https://www.w3.org/DOM/#what)的说法，“文档对象模型是一个平台和语言中立的接口，它将允许程序和脚本动态地访问和更新文档的内容、结构和风格。该文档可以被进一步处理，并且该处理的结果可以被合并回所呈现的页面中。”这句话基本上是说 DOM 不仅仅是一个新奇的东西——它对做事很有用。在接下来的几页中，我们将简要回顾一下 DOM 的历史，然后更深入地研究它是什么以及如何使用它。我们将以一些示例脚本结束，这些脚本展示了 DHTML 这一关键方面的强大功能。

##### 大教堂的起源

在 Netscape Navigator 2 中，Netscape Communications 引入了 JavaScript(简称 LiveScript ),它使 Web 开发人员能够通过脚本访问其网页中的元素——首先是表单，然后是图像、链接和其他功能。微软在 Internet Explorer 3 中实现了 JavaScript(尽管他们称之为 JScript)以便跟上 Netscape。

到了第 4 版，这两种浏览器在各自的功能集和对页面内容的访问方面有了很大的不同。每个浏览器制造商都实现了自己的专有方法来提供对层的脚本访问。想要在两种浏览器中都工作的脚本需要包含每种方法的代码。命运多舛的“浏览器大战”都是关于这些专有的网络扩展，因为每个制造商都努力通过新功能的诱惑来吸引更多的开发者加入其平台。很少考虑跨浏览器兼容性，尽管微软抄袭并支持网景公司的大部分早期创新。

在这一切发生的同时，W3C 开发了文档对象模型 1 级规范，它概述了使用脚本访问 XML 文档各个部分的通用标准方法。因为 HTML 可以被认为是 XML 的一种方言，所以 DOM Level 1 规范也适用于 HTML。

两家主要的浏览器制造商都实现了 DOM Level 1 规范:在 Internet Explorer 5 和 Netscape 6 中。先前存在的专有规范被追溯命名；因为新标准是 DOM Level 1，所以那些旧的和现在被废弃的方法被称为 DOM Level 0。(此后，W3C 还发布了 DOM Level 2 和 DOM Level 3 规范，它们增加了更多功能，并被分解为单独的模块。)但是没有正式的 DOM 级标准。

##### 什么是 DOM？

所以，你知道以前的 DOM *是什么。现在我们来讨论一下是什么。*

本质上，DOM 通过将页面中的元素映射到节点树来提供对 HTML 页面结构的访问。每个元素成为一个元素节点，每个文本比特成为一个文本节点。以这个 HTML 片段为例:

```
<body>  

  <p>   

    This is a paragraph, containing  

    <a href="#">   

      a link  

    </a>  

    in the middle.  

  </p>  

  <ul>  

    <li>   

      This item has  

      <em>   

        some emphasized text  

      </em>  

      in it.  

    </li>   

    <li>   

      This is another list item.  

    </li>  

  </ul>  

</body>
```

我添加了很多额外的缩进，以便您可以将这个代码片段与匹配的 DOM 树进行比较。在现实生活中不要这样做——我只是想让事情变得更清楚。匹配的 DOM 树如图 2.1 所示。

如您所见，HTML 中位于`p`元素内部的`a`元素成为 DOM 树中`p`节点的*子节点*，或者仅仅是*子节点*。(对称地，`p`节点是`a`节点的*父节点*。同一个父节点的两个`li`节点被称为*兄弟节点*或者仅仅是*兄弟节点*。)

请注意，HTML 标记中每个标记的嵌套级别与到达 DOM 树中相同项目所需的行数相匹配。例如，`<a>`标签在其他标签内嵌套了两次(标签为`<p>`和`<body>`)，所以树中的`a`节点位于顶部的两行。

**图 2.1。一个 DOM 树的例子。**

![1478_ExampleDOMTree](img/203538bc4ee2bbdc9185229551bcdfbb.png)

***有效 HTML 的重要性***

从最后这个例子中，我们可以更清楚地看到为什么有效的 HTML，包括适当嵌套的元素，是重要的。如果元素嵌套不当，就会出现问题。走下面这条线:

```
<strong>These <em>elements are</strong> badly nested</em>.
```

由这种不正确嵌套的代码生成的 DOM 树根本不是一棵树:为了表达 HTML 请求的无效元素布局，它需要是畸形的。每个浏览器都以不同的方式修复畸形的内容，这可能会产生这样的问题，比如一个元素是它自己的父节点。保持 HTML 的有效性可以避免所有这些问题。

##### 行走的 DOM 树

节点树在计算中经常出现，因为除了其他方面，它们还有一个非常有用的特性:很容易用很少的代码“遍历树”(也就是说，按顺序迭代树的每个节点)。遍历树很容易，因为任何元素节点都可以被认为是它自己的小树的顶端。因此，要遍历树，可以使用一系列步骤，例如:

1.  对我们看到的节点做些什么

3.  这个节点有子节点吗？如果是这样:

5.  对于每个子节点，转到步骤 1

这个过程被称为*递归*，被定义为调用自身的函数的使用。每个孩子和父母是同一类型的东西，因此可以用同样的方式处理。我们自己并不怎么使用递归，但是我们非常依赖浏览器通过页面的树进行递归。这在处理事件时特别有用，我们将在*第 3 章，处理 DOM 事件中看到。*

***寻找树顶***

为了遍历 DOM 树，您需要引用它顶部的节点:根节点。那个“引用”将是一个指向根节点的变量。JavaScript 应该可以使用根节点 document.documentElement。不是所有的浏览器都支持这种方法，但幸运的是这没关系，因为您很少需要从根节点开始遍历整个文档的 DOM 树。相反，所采用的方法是使用一个`getElementsBy*Whatever*`方法来直接获取树的特定部分。这些方法从 window.document 对象开始，简称为 document。

***从树中获取一个元素***

有两种主要的方法可以用来获取特定的元素或元素集。DHTML 编程中一直使用的第一种方法是`getElementById`。第二个是`getElementsByTagName`。另一种方法`getElementsByName`，很少使用，所以我们现在只看前两种。

**T2`getElementById`**

在 HTML 中，任何元素都可以有唯一的 ID。ID 必须用 HTML id 属性指定:

```
<div id="codesection">  

  <p id="codepara">  

  </p>  

  <ul>  

    <li><a href="https://www.sitepoint.com/" id="splink"  

        >SitePoint</a></li>  

    <li><a href="http://www.yahoo.com/" id="yalink"  

        >Yahoo!</a></li>  

  </ul>  

</div>
```

代码片段中的每个非列表元素都有一个 ID。你应该能找到其中的四个。ID 在您的文档中必须是惟一的——每个元素必须有一个不同的 ID(或者根本没有 ID)——这样您就可以知道一个特定的 ID 单独标识了一个给定的元素。要在 JavaScript 代码中获得对该元素的引用，请使用`document.getElementById(*elementId*)`:

```
var sitepoint_link = document.getElementById('splink')
```

现在，变量`sitepoint_link`包含了对上面 HTML 代码片段中第一个`<a>`标签的引用。稍后我们会看到你可以用这个元素引用做什么。这个 HTML 片段的 DOM 树如图 2.2 所示。

**图 2.2。代码段的 DOM 树。**

![1478_SnippetDOMTree](img/c843ff0a0ead02bfcc48bbec56d4e2aa.png)

**T2`getElementsByTagName`**

`document.getElementsByTagName`方法用于检索特定类型的所有元素。该方法返回一个包含所有匹配元素的数组(从技术上讲，它返回一个节点集合，但这就像一个数组一样工作。):

```
var all_links = document.getElementsByTagName('a');  

var sitepoint_link = all_links[0];
```

`all_links`变量包含一个数组，该数组包含两个元素:对 SitePoint 链接的引用和对 Yahoo！链接。这些元素按照它们在 HTML 中出现的顺序返回，所以`all_links[0]`是站点链接,`all_links[1]`是 Yahoo！链接。

注意`document.getElementsByTagName`总是返回一个数组，即使只找到一个匹配的元素。想象我们使用如下方法:

```
var body_list = document.getElementsByTagName('body');
```

在这种情况下，要获得对唯一的`body`元素的引用，我们需要使用以下代码:

```
var body = body_list[0];
```

如果`body_list.length`(数组的大小)不是 1，我们会非常惊讶，因为应该只有一个`<body>`标记！我们还可以通过用下面这一行替换前面的两行来稍微缩短这个过程:

```
var body = document.getElementsByTagName('body')[0];
```

JavaScript 允许你像这样折叠表达式。它可以使你的代码更加紧凑，并且让你不用声明很多实际上没有用的变量。

还有一个有用的特性；`getElementsByTagName`是在任何节点上定义的，不仅仅是文档。因此，要找到文档主体中的所有`<a>`标签，我们可以使用如下方法:

```
var links_in_body = body.getElementsByTagName('a');
```

注意“元素”在这个方法的名字中是复数，但在`getElementById`中是单数。这提醒我们，前者返回一个元素数组，而后者只返回单个元素。

***从父母走到孩子身边***

每个节点都有一个父节点(根元素除外)，并且可能有多个子节点。您可以从节点的 parentNode 属性获取对其父节点的引用；节点的子节点位于节点的 childNodes 属性中，该属性是一个数组。如果节点没有子节点，`childNodes`数组可能什么也不包含(这样的节点被称为*叶节点*)。

假设变量节点指向 DOM 树的`ul`元素。我们可以像这样得到节点的父节点(元素):

```
parent = node.parentNode;
```

我们可以通过查看`childNodes`数组的 length 属性来检查无序列表是否有任何列表项(子列表):

```
if (node.childNodes.length == 0) {  

  alert('no list items found!');  

}
```

如果有孩子，他们的编号从零开始。我们可以获得示例 HTML 中的第二个子元素(一个`li`元素)，如下所示:

```
list_item = node.childNodes[1];
```

对于第一个孩子的特殊情况，位于:

```
list_item = node.childNodes[0];
```

我们也可以用这个简写:

```
child = node.firstChild;
```

同样，最后一个孩子(在本例中是第二个`li`)也有自己的特殊属性:

```
child = node.lastChild;
```

我们将在本书的其余部分看到所有这些常规使用的属性。

##### 如何处理元素

现在您知道了如何获取对元素的引用 HTML 页面中的节点。DHTML 的核心——D 代表动态位——在于我们能够改变、删除和添加新的元素。在本章的其余部分，我们将使用前面看到的以下代码片段:

```
<div id="codesection">  

  <p id="codepara">  

  </p>  

  <ul>  

    <li><a href="https://www.sitepoint.com/" id="splink"  

        >SitePoint</a></li>  

    <li><a href="http://www.yahoo.com/" id="yalink"  

        >Yahoo!</a></li>  

  </ul>  

</div>
```

***改变元素属性***

元素的每个属性，以及可以应用于它的每个 CSS 样式，都可以从 JavaScript 中设置。可以应用于 HTML 中元素的属性(例如，`<a>`标记的`href`属性)也可以从脚本中设置和读取，如下所示:

```
// using our sitepoint_link variable from above  

sitepoint_link.href = "http://www.google.com/";
```

在脚本运行后点击那个链接，你会被带到 Google 而不是 SitePoint。浏览器想象中的新 HTML 内容(HTML 文件本身没有改变)如下所示:

```
<div id="codesection">  

  <p id="codepara">  

  </p>  

  <ul>  

    <li><a href=**"http://www.google.com/"** id="splink"  

        >SitePoint</a></li>  

    <li><a href="http://www.yahoo.com/" id="yalink"  

        >Yahoo!</a></li>  

  </ul>  

</div>
```

每个元素都有一组不同的可以更改的属性:`a`元素有`href`属性，`<img>`元素有`src`属性，依此类推。一般来说，可以应用于 HTML 中的标记的属性也可以从 JavaScript 中作为节点上的属性来获取和设置。因此，如果我们的代码包含对`img`元素的引用，我们可以通过改变`img_element.src`属性来改变显示的图像。(与这条规则的一个显著区别是，HTML 中元素的`class`属性在 JavaScript 中是作为`node.className`而不是`node.class`提供的。这是因为“class”是一个 JavaScript 保留字。)

记录元素及其支持的属性的两个最有用的参考文献是由两个主要的浏览器制造商提供的:微软 DOM 参考文献和 T2 Mozilla 基金会的 DOM 参考文献。

然而，重要的是，当我们改变上面的链接的`href`时，我们改变的只是链接的目的地。链接的文字，之前写着“SitePoint”，没有变化；如果我们需要改变这一点，我们必须单独这样做。更改页面中的文本比更改属性稍微复杂一些；要改变文本，您需要理解文本节点的概念。

***改变文本节点***

在上面的图 2.1 中，您可以看到文档中的 HTML 是如何表示为 DOM 树的。该图说明的一件重要事情是，元素内的文本不是该元素的一部分。事实上，文本在不同的节点中:元素节点的子节点。如果有对该文本节点的引用，可以使用节点的`nodeValue`属性更改其中的文本:

```
myTextNode.nodeValue = "Some text to go in the text node";
```

我们怎样才能获得对文本节点的引用呢？我们需要遍历 DOM 树——毕竟，我们必须知道文本节点在哪里，然后才能改变它。如果我们考虑上面的`sitepoint_link`节点，我们可以看到它的`childNodes`数组应该包含一个节点:一个节点值为`"SitePoint"`的文本节点。我们可以按如下方式更改该文本节点的值:

```
sitepoint_link.childNodes[0].nodeValue = 'Google';
```

现在，该链接在屏幕上显示的文本将显示为 Google，这与我们之前更改的链接目的地相匹配。我们可以将代码稍微缩短如下:

```
sitepoint_link.firstChild.nodeValue = 'Google';
```

您可能还记得，节点的 firstChild 属性和`childNodes[0]`都指向同一个节点；这种情况下可以用成功代替`childNodes[0]`。更改后，浏览器将看到以下文档代码:

```
<div id="codesection">  

  <p id="codepara">  

  </p>  

  <ul>  

    <li><a href="http://www.google.com/" id="splink"  

        >Google</a></li>  

    <li><a href="http://www.yahoo.com/" id="yalink"  

        >Yahoo!</a></li>  

  </ul>  

</div>
```

***改变样式属性***

正如我们所看到的，HTML 标签上设置的属性可以作为相应 DOM 节点的属性。CSS 样式属性也可以通过 DOM 使用节点的样式属性应用于该节点。每个 CSS 属性都是该 style 属性的一个属性，只是其名称略有变化:单词和破折号样式的 CSS 属性变成了 style 属性，其中删除了破折号，除了第一个单词以外，所有单词都以大写字母开头。这叫做 *InterCaps 格式*。这里有一个例子。名为的 CSS 属性:

```
some-css-property
```

在脚本中显示为以下 JavaScript 属性:

```
someCssProperty
```

因此，要为我们的`sitepoint_link`元素节点设置 CSS 属性 font-family，我们将使用以下代码:

```
sitepoint_link.style.fontFamily = 'sans-serif';
```

JavaScript 中的 CSS 值几乎总是被设置为字符串；有些值(如 font-size)是字符串，因为它们必须包含一个维度，如“px”或“%”。(Internet Explorer 将允许您不使用维度，因为它假设无量纲数实际上是像素度量。但是，不要试图利用这种假设；它会在其他浏览器中破坏你的代码，这是违反规范的。)只有全数字属性，如 z-index(根据上述规则，设置为`*node*.style.zIndex`)可以设置为数字:

```
sitepoint_link.style.zIndex = 2;
```

许多设计者改变样式属性来使元素出现或消失。在 CSS 中，display 属性用于这个目的:如果它被设置为`none`，元素不会在浏览器中显示。因此，为了隐藏一个元素，我们可以将它的 display 属性设置为`none`:

```
sitepoint_link.style.display = 'none';
```

为了再次显示它，我们给它另一个有效值:

```
sitepoint_link.style.display = 'inline';
```

关于可用的 CSS 样式属性和每个属性的作用的完整参考，请参见 SitePoint 的 [*HTML Utopia:使用 CSS*](https://www.sitepoint.com/books/css1/) 设计无表格。

##### 更大的 DOM 树变化

除了改变已经存在的元素的属性之外，DOM 操作的下一个层次是动态地添加和删除元素。能够更改现有元素的显示属性，以及读取和更改这些元素的属性，这需要大量的处理能力，但是能够动态地创建或删除页面的某些部分需要我们利用一套全新的技术。

***移动元素***

要添加元素，我们必须使用将成为添加节点的父节点的节点的`appendChild`方法。换句话说，要添加新元素作为文档中现有节点的子节点，我们使用该节点的`appendChild`方法:

```
// We'll add the link to the end of the paragraph  

var para = document.getElementById('codepara');  

para.appendChild(sitepoint_link);
```

在这之后，我们的页面看起来会有点奇怪。下面是更新后的 HTML 代码:

```
<div id="codesection">  

  <p id="codepara">  

    <a href="http://www.google.com/" id="splink">Google</a>  

  </p>  

  <ul>  

    <li></li>  

    <li><a href="http://www.yahoo.com/" id="yalink"  

        >Yahoo!</a></li>  

  </ul>  

</div>
```

另一个需要知道的有用的事情是，为了将节点移动到文档中的新位置，我们不需要先用*移除*。如果您使用`appendChild`将一个节点插入到文档中，并且该节点已经存在于文档的其他地方，则该节点不会被复制；相反，它会从以前的位置移动到您插入它的新位置。我们可以用雅虎做同样的事情！链接:

```
para.appendChild(document.getElementById('yalink'));
```

此后，页面将再次重新排列以匹配 HTML:

```
<div id="codesection">  

  <p id="codepara">  

    <a href="http://www.google.com/" id="splink">Google</a>  

    <a href="http://www.yahoo.com/" id="yalink">Yahoo!</a>  

  </p>  

  <ul>  

    <li></li>  

    <li></li>  

  </ul>  

</div>
```

图 2.3 显示了到目前为止新的 DOM 树。

**图 2.3。更改后的 DOM 树。**

![1478_ChangesDOMTree](img/3943ac6cde7ec8b1933b7376bf7d00ce.png)

如果您不想将新的(或移动的)元素添加到该段落的末尾，该怎么办？除了`appendChild`之外，每个节点都有一个`insertBefore`方法，调用该方法时有两个参数:要插入的节点，以及要插入的节点之前的节点。移动雅虎！链接到段落的开头，我们希望将它作为出现在谷歌链接之前的*的子段落插入。所以，插入雅虎！link(第一个参数)作为 Google link ( `sitepoint_link`，第二个参数)之前的子段落，我们会使用以下内容:*

```
para.insertBefore(document.getElementById('yalink'),  

    sitepoint_link);
```

确保第二个参数(`sitepoint_link`)确实是 para 的现有子节点，否则这个方法将失败。

***扔掉元素***

删除一个元素与添加一个元素的过程非常相似:同样，我们在元素的父节点上使用`removeChild`方法。记住前面的内容，我们可以通过`*node*`访问给定节点的父节点

。parentNode，我们可以从文档中完全删除我们的`sitepoint_link`:

```
// never hurts to be paranoid: check that our node *has* a parent  

if (sitepoint_link.parentNode) {  

  sitepoint_link.parentNode.removeChild(sitepoint_link);  

}
```

该操作会将 HTML 代码更改为如下所示:

```
<div id="codesection">  

  <p id="codepara">  

    <a href="http://www.yahoo.com/" id="yalink">Yahoo!</a>  

  </p>  

  <ul>  

    <li></li>  

    <li></li>  

  </ul>  

</div>
```

***注***

即使在移除节点之后，`sitepoint_link`仍然构成对该链接的引用。它仍然存在，只是不再存在于文档中:它漂浮在中间。如果我们愿意，可以将它添加回文档的其他地方。将变量设置为`null`使被删除的元素永远消失。

***创造元素***

在页面内移动现有元素是一种强大而有用的技术(有了它，你就可以实现 Space Invaders 或 Pac Man 了！).但是，除此之外，我们有能力创建全新的元素并将它们添加到页面中，为真正的动态内容提供容量。需要记住的一点是，和以前一样，页面的文本驻留在文本节点中，所以如果我们需要创建一个包含文本的元素，我们必须创建新的元素节点和一个包含文本的文本节点。为了实现这一点，我们需要两个新方法:`document.createElement`和`document.createTextNode`。

首先，我们创建元素本身:

```
var linux_link = document.createElement('a');
```

尽管我们已经创建了元素，但它还不是文档的一部分。接下来，我们以设置现有链接属性的相同方式设置它的一些属性:

```
linux_link.href = 'http://www.linux.org/';
```

然后，我们为将出现在链接内部的文本创建文本节点。我们将文本节点的文本作为参数传递:

```
var linux_tn =  

    document.createTextNode('The Linux operating system');
```

文本节点也是浮动的，独立于文档。我们将文本节点添加到元素的子元素列表中，如上所示:

```
linux_link.appendChild(linux_tn);
```

元素和文本节点现在形成了一个由两个节点组成的迷你树(官方的说法是一个*文档片段*),但是它们仍然独立于 DOM。最后，我们将元素插入页面，这与将它放入 DOM 树是一样的:

```
para.appendChild(linux_link);
```

下面是生成的 HTML:

```
<div id="codesection">  

  <p id="codepara">  

    <a href="http://www.yahoo.com/" id="yalink">Yahoo!</a>  

    <a href="http://www.linux.org/">The Linux operating system</a>  

  </p>  

  <ul>  

    <li></li>  

    <li></li>  

  </ul>  

</div>
```

如您所见，为了创建元素，我们使用了相同的技术和知识——文本节点是元素节点的子节点，我们用`node.appendChild`附加一个子节点——我们使用已经是文档一部分的节点。对于 DOM 来说，节点就是节点，不管它是否是文档的一部分:它只是一个节点对象。

***复制元素***

正如我们所见，创建一个元素很简单。但是，如果您想在页面中添加大量动态内容，该怎么办呢？必须创建一整批新元素和文本节点——将文本节点附加到它们的元素上，将元素相互附加，将顶部元素附加到页面上——这是一个费力的过程。幸运的是，如果您要向页面添加已经存在的内容的副本，有一个快捷方式可用:`cloneNode`方法。这将返回节点的一个*副本*，包括它的所有属性和所有子节点。(通过将`false`传递给`cloneNode`方法，您可以选择只克隆节点，而不是其子节点。)如果您有一个包含许多元素的中等复杂程度的 HTML，那么`cloneNode`是一种非常快速的方法，可以返回该 HTML 块的副本，以便插入到文档中:

```
var newpara = para.cloneNode(true);  

document.getElementById('codesection').appendChild(newpara);
```

但是，你不能仓促行事，因为小心行事是有好处的。这个方法克隆节点及其所有子节点的所有属性，*包括【This，id 在文档中必须是惟一的。因此，如果在克隆的 HTML 块中有带有 id 的元素，那么在将克隆的块追加到文档之前，需要修复这些 id。*

这将是很好的能够抓住雅虎！使用以下代码链接我们的克隆块:

```
var new_yahoo_link = newpara.getElementById('yalink');
```

但是，不幸的是，我们不能。`getElementById`方法只在文档上定义，而不是在任意节点上定义。解决这个问题的最简单的方法是避免在您想要克隆的块中的元素上定义 id。这里有一行代码将删除雅虎！链接的`id`:

```
newpara.firstChild.removeAttribute('id');
```

不过，我们仍然有段落本身的 ID，这意味着当我们将新段落添加到文档中时，我们将有两个 ID 为`codepara`的段落。这很糟糕——这不应该发生。我们必须在添加新段落之前修复它，修改上面的代码如下:

```
var newpara = para.cloneNode(true);  

newpara.id = 'codepara2';  

newpara.firstChild.removeAttribute('id');  

document.getElementById('codesection').appendChild(newpara);
```

此代码返回以下结果:

```
<div id="codesection">  

  <p id="codepara">  

    <a href="http://www.yahoo.com/">Yahoo!</a>  

    <a href="http://www.linux.org/">The Linux operating system</a>  

  </p>  

  <p id="codepara2">  

    <a href="http://www.yahoo.com/">Yahoo!</a>  

    <a href="http://www.linux.org/">The Linux operating system</a>  

  </p>  

  <ul>  

    <li></li>  

    <li></li>  

  </ul>  

</div>
```

如您所见，如果您选择复制大块的文档，会涉及到一点外科手术。这个演示结束了我们对这段代码的实验。

##### 形成一个扩展的形式

作为我们的第一个完整示例，我们将使用 DOM 的元素创建方法来构建一个可以随着用户填充而增长的表单。这允许用户向表单中添加任意多的条目。

让我们想象一个在线系统，通过这个系统，人们可以为自己和任何数量的朋友注册免费啤酒。(也许有一个疯狂的百万富翁慈善家逍遥法外。不，我不能给你这个系统真正运行的网址！)

用户添加他们自己的名字，然后添加他们希望邀请的所有朋友的名字。如果没有 DOM，我们会要求表单要么包含大量朋友姓名的位置(比任何人使用的都多)，要么定期提交回服务器以获得一个新的(空的)姓名输入区域列表。

在我们勇敢的新世界中，我们可以动态地添加额外的姓名输入字段。我们将在表单上放置一个按钮，显示“添加另一个朋友”。单击该按钮将向列表中添加一个新字段，准备提交给服务器。每个新创建的字段都需要一个不同的`name`属性，这样当服务器最终收到提交的表单时就可以区分它。(根据用于处理表单的服务器端语言，这并不是绝对必要的。因为我们的示例表单实际上不会提交任何东西，所以我们将把它作为一个有用的练习来实现。)

我们的表单将提供一个用户姓名的文本输入框，一个包含一个朋友姓名的文本输入框的`fieldset`，以及一个添加更多朋友的按钮。当点击按钮时，我们将为另一个朋友的名字添加一个新的文本输入框。

**例 2.1。expandingForm.html**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"  

    "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

  <head>  

    <title>Free beer signup form</title>  

    <script type="text/javascript">  

      var fieldCount = 1;  

      function addFriend() {  

        fieldCount++;  

        var newFriend = document.createElement('input');  

        newFriend.type = 'text';  

        newFriend.name = 'friend' + fieldCount;  

        newFriend.id = 'friend' + fieldCount;  

        document.getElementById('fs').appendChild(newFriend);  

      }  

    </script>  

    <style type="text/css">  

      input {  

        display: block;  

        margin-bottom: 2px;  

      }  

      button {  

        float: right;  

      }  

      fieldset {  

        border: 1px solid black;  

      }  

    </style>  

  </head>  

  <body>  

    <h1>Free beer signup form</h1>  

    <form>  

      <label for="you">Your name</label>  

      <input type="text" name="you" id="you">  

      <fieldset id="fs">  

        <legend>Friends you wish to invite</legend>  

        <button onclick="addFriend(); return false;">  

          Add another friend  

        </button>  

        <input type="text" name="friend1" id="friend1">  

      </fieldset>  

      <input type="submit" value="Save details">  

    </form>  

  </body>  

</html>
```

注意我们的`fieldCount`变量；这将跟踪有多少个朋友字段。

**例 2.2。`expandingForm.html`(节选)**

```
var fieldCount = 1;
```

当按钮被点击时，我们运行`addFriend`函数(我们将在下一章讨论处理点击和各种其他类型的事件):

```
<button onclick="addFriend(); return false;">
```

`addFriend`函数每次运行时都会完成许多任务:

1.  增加`fieldCount`:

**例 2.3。`expandingForm.html`(节选)**

`fieldCount++;`

*   创建一个新的`input`元素:

**例 2.4。`expandingForm.html`(节选)**

`var newFriend = document.createElement('input');`

*   将其类型设置为`text`–我们需要一个文本输入框，一个由`<input type="text">`指定的元素:

**例 2.5。`expandingForm.html`(节选)**

`newFriend.type = 'text';`

*   设置唯一的 id 和名称(因为 ID 必须是唯一的，并且所有输入框必须有不同的名称，以便在提交表单时可以区分):

**例 2.6。`expandingForm.html`(节选)**

`newFriend.name = 'friend' + fieldCount;  
       newFriend.id = 'friend' + fieldCount;`

*   将这个新创建的元素添加到文档中:

**例 2.7。`expandingForm.html`(节选)**

`document.getElementById('fs').appendChild(newFriend);`

点击“添加另一个朋友”按钮两次后，页面看起来是这样的，添加了两个朋友的名字:

**图 2.4。注册免费啤酒。**

![Figure 2.4](img/740f5b69ad70d012b3c6d6115e20477e.png)

免费啤酒，感谢大教堂的力量。我们不能抱怨那个！

##### 制作模块化图像翻转

图像翻转脚本是 Web 上 JavaScript 编程的主流，在这种脚本中，图像被用作链接，当用户将鼠标放在图像上时，图像会发生变化。传统上，开发人员需要大量的脚本和定制。DOM 的自省功能——脚本检查其运行的页面结构的能力——使我们能够自动检测翻转图像，并在没有任何定制的情况下设置它们。这代表了一种比老式的使用`onmouseover`和`onmouseout`属性更系统的方法，并且将翻转代码与其他内容分开。

我们将构建我们的页面，以便我们想要显示翻转效果的链接有一个类`rollover`。它们将包含一个`img`元素——没有别的。我们还将提供专门命名的翻转图像:如果页面中的图像被称为`foo.gif`，那么匹配的翻转图像将被命名为`foo_over.gif`。当页面加载时，我们将遍历 DOM 树，识别所有适当的链接(通过检查它们的类和它们是否包含一个`img`元素)，并在每个链接上设置翻转。这个特别命名的翻转图像允许我们推断出任何翻转图像的名称，而无需在任何地方保存该名称。它减少了我们必须管理的数据量。

另一种技术是在图像标签中使用非 HTML 属性:

```
<img src="basic_image.gif" oversrc="roll_image.gif">
```

然而，由于`oversrc`不是一个标准属性，这种方法会导致 HTML 无效。

下面的一些脚本可能看起来有点不透明:我们将把侦听器附加到 DOM 事件，以确保脚本在适当的时间运行。如果这令人困惑，那么在您阅读了下一章中关于 DOM 事件的讨论之后，请随时重温这个例子。

***一个 HTML 页面示例***

首先，HTML:这里有我们的链接，类`rollover`，包含图片。

**例 2.8。`rollovers.html`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"  

    "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

  <head>  

    <title>Modular rollovers</title>  

    <script type="text/javascript" src="rollovers.js"></script>  

    <style type="text/css">  

      /* Remove the blue border on the rollover images */  

      a.rollover img {  

        border-width: 0;  

      }  

    </style>  

  </head>  

  <body>  

    <h1>Modular rollovers</h1>  

    <p>Below we have two links, containing images that we want   

      to change on mouseover.</p>  

        <ul>  

      <li>  

        <a href="" class="rollover" alt="Roll"  

            ><img src="basic_image.gif" /></a>  

      </li>  

      <li>  

        <a href="" class="rollover" alt="Roll"  

            ><img src="basic_image2.gif"></a>  

      </li>  

    </ul>  

  </body>  

</html>
```

该页面还包括完成所有工作的 JavaScript 文件:

**例 2.9。`rollovers.js`**

```
function setupRollovers() {  

  if (!document.getElementsByTagName)  

    return;  

  var all_links = document.getElementsByTagName('a');  

  for (var i = 0; i < all_links.length; i++) {  

    var link = all_links[i];  

    if (link.className &&  

        (' ' + link.className + ' ').indexOf(' rollover ') != -1)  

    {  

      if (link.childNodes &&  

          link.childNodes.length == 1 &&  

          link.childNodes[0].nodeName.toLowerCase() == 'img') {  

        link.onmouseover = mouseover;  

        link.onmouseout = mouseout;  

      }  

    }  

  }  

}  

function findTarget(e)  

{  

  /* Begin the DOM events part, which you */  

  /* can ignore for now if it's confusing */  

  var target;  

  if (window.event && window.event.srcElement)  

    target = window.event.srcElement;  

  else if (e && e.target)  

    target = e.target;  

  if (!target)  

    return null;  

  while (target != document.body &&  

      target.nodeName.toLowerCase() != 'a')  

    target = target.parentNode;  

  if (target.nodeName.toLowerCase() != 'a')  

    return null;  

  return target;  

}  

function mouseover(e) {  

  var target = findTarget(e);  

  if (!target) return;  

  // the only child node of the a-tag in target will be an img-tag  

  var img_tag = target.childNodes[0];  

  // Take the "src", which names an image called "something.ext",  

  // Make it point to "something_over.ext"  

  // This is done with a regular expression  

  img_tag.src = img_tag.src.replace(/(.[^.]+)$/, '_over$1');  

}  

function mouseout(e) {  

  var target = findTarget(e);  

  if (!target) return;  

  // the only child node of the a-tag in target will be an img-tag  

  var img_tag = target.childNodes[0];  

  // Take the "src", which names an image as "something_over.ext",  

  // Make it point to "something.ext"  

  // This is done with a regular expression  

  img_tag.src = img_tag.src.replace(/_over(.[^.]+)$/, '$1');  

}  

// When the page loads, set up the rollovers  

window.onload = setupRollovers;
```

这段代码的 DOM 遍历部分在`setupRollovers`和`findTarget`中找到，这是从两个`mouseover/mouseout`函数调用的。让我们依次来看一下每一项。

**`setupRollovers`功能**

`setupRollovers`函数的代码如下开始:

**例 2.10。`rollovers.js`(节选)**

`if (!document.getElementsByTagName)  
   return;`

这段代码确认了我们在一个支持 DOM 的浏览器中。如果我们不存在(也就是说，如果方法`document.getElementsByTagName`不存在)，我们就退出这里，不再前进。如果该方法确实存在，我们继续:

**例 2.11。`rollovers.js`(节选)**

`var all_links = document.getElementsByTagName('a');`

这里，我们将`all_links`作为文档中所有`<a>`标签列表的引用。

**例 2.12。`rollovers.js`(节选)**

`for (var i = 0; i < all_links.length; i++) {  
   var link = all_links[i];`

上面的代码以标准的 JavaScript 方式遍历检索到的标签列表。我们将`link`变量分配给每个链接，以简化下面的代码。

**例 2.13。`rollovers.js`(节选)**

`if (link.className &&  
       (' ' + link.className + ' ').indexOf(' rollover ') != -1)  
   {`

我们需要知道每个链接是否属于类`rollover`。然而，一个元素可以有多个类；例如，如果这个标签有两个类，`rollover`和`hotlink`，它就会有`className="rollover hotlink"`。这意味着我们不能使用下面的方法来检查具有特定类的元素:

```
if (*element*.className == "*my*class")
```

如果元素有多个类，上述条件将总是评估为`false`。这里一个有用的方法是在字符串`' ' + element.className + ' '`(元素的`class`属性，前后有一个空格)中查找字符串`' myclass '`(前后有一个空格的类名)。正如你所期望的，它总是会找到你的类。它还避免了一个类似技术的问题，该技术使用`className.indexOf`来寻找`'myclass'`。如果正在讨论的元素属于类`myclassroom`，这个技术将给出一个假阳性。(另一种选择是使用正则表达式来找出类名。然而，为了简单起见，我们将坚持使用已经介绍过的方法。)

**例 2.14。`rollovers.js`(节选)**

`if (link.childNodes &&  
         link.childNodes.length == 1 &&  
         link.childNodes[0].nodeName.toLowerCase() == 'img') {`

我们想确认这个链接只包含一个`img`元素，所以我们利用了 JavaScript 的一个非常方便的属性，叫做*短路评估*。在一个形式为**if`(a && b && c)`的【T4 if**语句中

，如果 *`a`* 为假，那么 *`b`* 和 *`c`* 根本不求值。这意味着*`b`**`c`*可以是依赖于 *`a`* 的真值的东西:如果 *`a`* 不为真，那么它们就不会被求值，所以把它们放入 **if** 语句是安全的。

看一下上面的代码可能会更清楚这一点。我们需要测试链接的第一个子节点的节点名是否是`img`。我们可以使用下面的代码:

```
if (link.childNodes[0].nodeName.toLowerCase == 'img')
```

但是，如果当前链接没有任何子节点，这段代码将会导致一个错误，因为没有`link.childNodes[0]`。所以，我们必须首先检查子节点是否存在；第二，我们确认有且只有一个孩子；第三，我们检查这个唯一的第一个孩子是否是一个图像。我们可以在图像检查中安全地假设`link.childNodes[0]`存在，因为我们已经证实了这一点:如果它不存在，我们就不会走到这一步。

**例 2.15。`rollovers.js`(节选)**

```
link.onmouseover = mouseover;
```

这段代码将一个事件处理程序附加到节点上的 mouseover 事件。

**例 2.16。`rollovers.js`(节选)**

```
link.onmouseout = mouseout;
```

这一行将事件处理程序附加到该节点上的 mouseout 事件。仅此而已！

**`findTarget`功能**

这个小函数由`mouseover`和`mouseout`函数调用。正如我们将看到的，它们将事件对象传递给`findTarget`，反过来，它将返回生成事件的图像周围的链接标签，如果找到这样的标签的话。

```
findTarget starts like this:
```

**例 2.17。`rollovers.js`(节选)**

`var target;  

 if (window.event && window.event.srcElement)  
   target = window.event.srcElement;  
 else if (e && e.target)  
   target = e.target;  
 if (!target)  
   return null;`

第一部分与 DOM 事件处理相关，这将在下一章中解释。我们暂时忽略它的工作方式，只是说它迎合了 Internet Explorer 和完全支持 DOM 的浏览器之间的差异。然而，一旦这段代码运行，我们应该在变量 target 中加入浏览器认为负责 mouseover 或 mouseout 事件的元素——最好是`<a>`标签。

**例 2.18。`rollovers.js`(节选)**

`while (target != document.body &&  
     target.nodeName.toLowerCase() != 'a')  
   target = target.parentNode;  

 if (target.nodeName.toLowerCase() != 'a')  
   return null;`

变量 target 应该是对用户点击的`<a>`标签的引用，但也可能是在`<a>`标签内的某个东西(因为一些浏览器以这种方式处理事件)。在这种情况下，上面的代码不断获取该标签的父节点，直到它到达一个`<a>`标签(这将是我们想要的)。如果我们发现文档主体——一个`<body>`标签——相反，我们已经走得太远了。我们将放弃，从函数中返回`null`(什么也不返回)，不再继续。

然而，如果我们找到了一个`<a>`标签，我们返回:

**例 2.19。`rollovers.js`(节选)**

`return target;  
}`

**`mouseover`/`mouseout`功能**

这些函数以相似的方式工作，做非常相似的事情:`mouseover`在我们将鼠标移动到滚动链接上时被调用，而`mouseout`在我们再次将鼠标移出时被调用。

`mouseover`的代码是这样开始的:

**例 2.20。`rollovers.js`(节选)**

`var target = findTarget(e);  
 if (!target) return;`

如上所述，我们调用`findTarget`函数来获取鼠标所在链接的引用。如果没有元素返回，我们放弃，优雅地降级。否则，我们在目标中有鼠标悬停的标签`<a>`。接下来，我们把图像挖出来。

**例 2.21。`rollovers.js`(节选)**

`var img_tag = target.childNodes[0];`

我们还知道`<a>`标签有且只有一个子节点，那就是`<img>`标签。我们知道这一点，因为当我们在`setupRollovers`中设置事件处理程序时，我们检查了这种情况。

**例 2.22。`rollovers.js`(节选)**

`img_tag.src = img_tag.src.replace(/(.[^.]+)$/, '_over$1');`

图像有一个`src`属性，您可以通过 DOM 使用元素的 src 属性来访问它。在上面的代码片段中，我们对该字符串应用了正则表达式替换。(尽管正则表达式的全部细节超出了本书的范围，我们将在*第 6 章，表单和验证*中查看基础知识。更详细的参考资料是 Kevin Yank 关于 sitepoint.com 的文章，[*JavaScript 中的正则表达式*](https://www.sitepoint.com/article/expressions-javascript) 。)改变一个`<img>`标签的`src`属性的值会导致它用新图像重新加载自己；因此，进行这种替换(用`something_over.gif`替换`something.gif`)会导致原始图像变为翻转图像。`mouseout`函数的作用正好相反:它将图像的 src 属性中对`something_over.gif`的引用更改为 something.gif，使原始图像重新出现。

**不劳而获(几乎)**

如果你看看这个模块化翻转的代码，你会看到它被分成几个部分。`setupRollovers`函数除了安装监听器什么也不做。`findTarget`函数除了查找给定事件的链接标签之外什么也不做。`mouseover`和`mouseout`功能除了实际的图像交换工作之外没做什么。任务分工明确。

这意味着这段代码对其他应用程序也有好处。我们可以改变`mouseover`和`mouseout`函数来做其他事情——例如，让弹出帮助内容出现——而不需要从头开始让它工作。我们可以重用脚本中的其他函数(或者至少在改动很小的情况下去掉)。这不仅方便；也很整洁干净。我们正在向更好的脚本前进！

##### 摘要

在介绍中，我们提到 DOM 是 DHTML 的关键部分。探索 DOM——能够在文档中查找、更改、添加和删除元素——本身就是一项强大的技术，也是现代 DHTML 的一个基本方面。一旦你掌握了本章所描述的技术，其他的事情就会水到渠成了。在本书的其余部分，我们将描述一些技巧和窍门，通过它们你可以在你的站点上，在你的 Web 应用程序中，使用 DHTML 做一些奇妙的事情。它们都建立在操作文档对象模型的基本方法之上。

##### 第三章。处理 DOM 事件

当我不能处理事情时，我让他们自己处理。

亨利·福特

事件是发生的事情，无论是在现实生活中，还是在 DHTML 编程中。但是对于那些使用 DHTML 的人来说，事件有着非常特殊的意义。当一个元素发生某些事情时，就会生成或触发一个事件:例如，鼠标单击一个按钮，或者对一个表单进行了更改。DHTML 编程都是关于事件处理的；您的代码将运行以响应这个或那个事件的触发。

了解哪些事件是可用的，如何将代码与它们挂钩，以及如何最好地利用它们是构建动态 Web 应用程序的关键部分。(看起来确实有不少“关键”的位，我知道！)这就是我们在本章中所涉及的内容，以及几个真实世界的例子。

##### 关于元素和事件

我们使用一种现代的方法来处理 DHTML，所以我们所有的 DHTML 代码都将被设置为响应事件的触发而运行。如果您以前做过 JavaScript Web 编程，您可能已经在不知不觉中使用了这种技术。让我们看看代码传统上与事件挂钩的过程，学习如何在 DOM 下做这件事(以及为什么 DOM 方法更好)，并找出这些技术到底使什么成为可能。

***常见事件***

每个页面元素触发给定的事件选择。有些事件是所有元素共有的；其他的更具体。例如，当鼠标移动到所有可见元素上时，这些元素都会触发 mouseover 事件。但是，change 事件只能由内容可以更改的元素触发:文本框、文本区域和下拉列表。

你可能已经注意到我在上面使用了 mouseover，而不是`onmouseover`作为事件名称。尽管处理该事件的 HTML 属性是`onmouseover`，但描述事件本身的现代方式是简单的鼠标悬停。这使得我们可以分别讨论*事件*(鼠标悬停)和*事件处理程序* ( `onmouseover`)。*事件处理程序*是放置事件处理程序的位置。在糟糕的旧浏览器时代，这些概念都是混在一起的，但现在我们可以放心地将它们视为独立的实体。

描述由给定元素触发的事件的文档是 W3C DOM 规范和 HTML 建议，它们在上一章中提到过，还有 [W3C DOM 2 事件规范](https://www.w3.org/TR/DOM-Level-2-Events/Overview.html)。在 [DOM 3 事件规范](https://www.w3.org/TR/2003/NOTE-DOM-Level-3-Events-20031107/events.html)中还有一些关于关键事件的额外信息。

表 3.1 总结了您可能会发现有用的、支持跨浏览器的事件。请注意，这不是一个详尽的调查:这是一个您可能经常使用的事件列表，而不是天底下的所有事情。

**表 3.1。有用的事件。**

![1478_table3.1sml](img/96fa1a0310afb031e2832d594f72de20.png)
[点击可弹出此表的大图。](https://i2.sitepoint.com/graphics/1478tab31.png)

***将代码与事件*** 挂钩

现在你知道了一些常见的事件，当它们发生时。但是，如何让代码运行来响应这些事件呢？

**勾搭老路子**

如果您以前编写过 JavaScript 代码，您可能会编写类似这样的代码:

```
<a href="somewhere.html"   

    onclick="myJavaScriptFunction(); return false;"   

    >click me!</a>
```

这个`onclick`属性将一些 JavaScript 代码连接到链接的 click 事件。当单击链接时，它将触发一个 click 事件，代码将运行。没问题！但是，请注意，代码实际上从未提到“click”，这是事件的实际名称。

如果我们想检测一个按键呢？下面是等效的脚本:

```
function aKeyWasPressed() {   

  // put event handler code here ...   

}
```

下面是匹配的 HTML 代码片段:

```
<textarea id="myta" onkeypress="aKeyWasPressed()"></textarea>
```

在这种情况下，我们的`aKeyWasPressed`函数如何知道哪个键被按下了呢？嗯，没有。这是老式方法的一个主要限制。但是我们可以改进！

**挂接 DOM 方式**

DOM 规范通过提供*事件目标*和*事件监听器*扩展了事件处理程序的概念。事件目标是事件所针对的事物，本质上是一个元素。事件侦听器是在事件出现时捕获事件并对其做出响应的东西。事件首先来自哪里？它们来自用户。浏览器软件捕获用户动作并将事件发送到正确的事件目标。

给定的事件源可以与多个事件侦听器相关。使用上面的老式方法，只有一段代码可以运行来响应任何事件。例如，一个元素只能有一个`onclick`属性。(实际上，您可以拥有任意多个，但是每一个都会覆盖之前的一个，所以实际上，您只有一个。或者，您可以将 JavaScript 语句串在一起，在属性中使用分号，但这会使 HTML 代码更加混乱。)使用现代方法，您可以在触发一个或多个事件时运行任意多段代码。侦听器共享事件，事件共享侦听器。为了方便起见，我们必须将我们的“连接”代码从 HTML 移到一个单独的脚本部分:如上所述，任何元素都不能有一个以上的`onclick`属性。

根据浏览器的不同，事件处理有不同的方式。在研究 Internet Explorer 中的事件处理之前，我们将首先研究 W3C 认可的方式。这是 W3C 的方法。

**例 3.1。`keycodedetect.html`(节选)**

```
function aKeyWasPressed(e) {   

  // put event listener code here...   

}   

var textarea = document.getElementById('myta');   

textarea.addEventListener('keyup', aKeyWasPressed, false);
```

这是相应的 HTML:

**例 3.2。`keycodedetect.html`(节选)**

```
<textarea id="myta"></textarea>
```

***HTML 之前的脚本…目前为***

如果你在你选择的 HTML 编辑器中处理这个例子，一定要把 JavaScript 代码放在本章的这个例子和接下来的几个例子的 HTML 后面。在 JavaScript 代码为其分配事件监听器之前,`textarea`必须存在。

如果您习惯于将 JavaScript 放在 HTML 文件的顶部，不要担心。在本节的最后，我们将讨论一种绕过这种限制的优雅方式。

这几行代码包含了许多复杂的概念。考虑以下片段:

**例 3.3。`keycodedetect.html`(节选)**

```
var textarea = document.getElementById('myta');
```

在这里，我们看到一个熟悉的引用`<textarea>`。接下来，有一些新的东西:

**例 3.4。`keycodedetect.html`(节选)**

```
textarea.addEventListener('keyup', aKeyWasPressed, false);
```

这是设置一切的关键线。每个元素都有一个`addEventListener`方法，允许您将一个函数与元素接收到的任何事件挂钩。(我们在这里使用了 keyup 事件，而不是更常见的按键，因为在编写本文时，Macintosh 上的 Safari 不支持使用`addEventListener`分配按键事件。也许更重要的是，DOM3 建议没有提到按键事件。)该方法有三个参数:事件、应该调用的函数和`*useCapture*`的真值或假值

。最后一项与 DOM 事件的一个很少使用的特性有关，叫做*事件捕获*。目前，我们只是将它设置为`false`，以表明我们不想使用事件捕获。如果您想了解完整的故事，请参阅 DOM Level 3 Events 规范(不适合胆小的人！).

事件被指定为字符串，即事件的(现代)名称(即没有“on”前缀)。仅使用函数的名称来指定函数；不要像在`aKeyWasPressed()`中那样在它后面放括号，因为这会调用函数。我们现在不想称之为；我们想在事件触发后调用它。(如果你从事过其他语言的工作，你可能会认可这意味着函数在 JavaScript 中是一级对象；我们可以使用函数名来传递对函数的引用，但不需要调用它。这个过程并不适用于所有语言，但是它是 JavaScript 的一个非常有用的特性。)

现在，当在我们的`<textarea>`中按下一个键时，我们的`aKeyWasPressed`函数将被调用。请注意，JavaScript 不再搞乱我们的 HTML 很像 CSS 促进了设计和内容的分离，*我们已经将页面内容(HTML)和页面行为(JavaScript)分离开来*。这是新技术的一个重要好处:我们可以在不改变页面 HTML 的情况下切换新的事件侦听器。这是现代的方式！

我们仍然没有解决我们之前提出的问题:函数`aKeyWasPressed`如何知道哪个键被按下了？

**获取事件信息**

我们在上面的代码中做了一个微妙的改变，给了`aKeyWasPressed`函数一个参数`*e*`

。

**例 3.5。`keycodedetect.html`(节选)**

```
function aKeyWasPressed(e) {   

  ...
```

当一个函数作为事件监听器被调用时，在符合 W3C events 的浏览器中，它被传递给一个保存事件细节的*事件对象*。这个对象有许多包含有用信息的属性，比如 target 和对触发事件的元素的引用。可用的精确属性将取决于所讨论的事件类型，但表 3.2 中列出了最有用的属性。

**表 3.2。有用的属性。**
![1478_table3.2sml](img/18bcf73836f45681bb09ae82fdd14172.png)
[点击可弹出此表的大图。](https://i2.sitepoint.com/graphics/table3.2.png)

[a]不要在这里使用`charCode`，即使有些网站告诉你这样做。`keyCode`有很好的跨浏览器支持，`charCode`没有。DOM 中的关键代码是标准的混乱！获取代码的方式有三种:`keyCode` (IE)、`charCode` (Mozilla/Netscape)和数据(官方的 DOM 3 Events 方式)。幸运的是，所有主流浏览器都支持非标准键码。所以总是使用这个，至少直到数据属性广泛传播(大约在 2010 年！).

标识哪个键被按下的代码如下所示:

**例 3.6。`keycodedetect.html`(节选)**

```
function aKeyWasPressed(e) {   

  var key = e.keyCode;   

  alert('You pressed the key: ' + String.fromCharCode(key));   

}   

var textarea = document.getElementById('myta');   

textarea.addEventListener('keyup', aKeyWasPressed, false);
```

当一个键被按下时，我们的函数会弹出一个对话框告诉我们。(注意，我们使用`String.fromCharCode`方法将 keyCode 提供的键盘代码转换为人类可读的字符串。)

**跨目标重用监听器**

目标属性可能看起来不是很有用；毕竟，我们知道这将是对`<textarea>`的引用。但是我们可以将同一个函数作为事件监听器挂接到多个元素上。例如，我们可以为页面中的每个链接附加一个函数作为点击事件的事件监听器。当任何一个链接被点击，我们的函数就会被调用；然后我们可以通过检查函数的`e.target`来判断哪个链接被点击了。我们将在本章后面的例子中回到这一点。

现在，我们需要知道的是，我们不必为我们感兴趣的每个标签编写单独的事件侦听器。

事件发生后会发生什么？

事件还有两个更重要的属性:*冒泡*和*默认动作*。考虑一个 HTML 文档。它是分层的:元素被其他元素包含。考虑这个 HTML 片段:

```
<div>   

  <p>   

    <a href="">a link</a>   

  </p>   

</div>
```

单击链接将导致该链接触发一个单击事件。但是链接包含在段落内，段落包含在`<div>`内。所以点击链接也会导致段落和`<div>`看到点击事件。这叫做*事件冒泡*；一个事件“冒泡”穿过 DOM 树，从目标元素开始，直到它到达顶部。不是所有的事件都是泡沫；例如，聚焦和模糊事件不会。冒泡通常可以被忽略，但有时您会希望防止特定事件冒泡。(有许多关于事件冒泡和事件捕获的复杂规则，事件传播阶段发生在事件冒泡之前。实际上，除了如何阻止它的发生，我们不需要知道太多，但是对于那些想知道更多 DOM 这方面的理论的人来说，在可以找到完整的文章。)

一旦你有了一个事件，DOM Events 规范说你可以停止任何类似的冒泡:

```
function aKeyWasPressed(e) {   

  var key = e.keyCode;   

  e.stopPropagation();   

  ...   

}
```

一旦对`topPropagation`的调用到位，事件将只在`<a>`标签上发生:任何在`<p>`或`<div>`标签上的监听器都将错过。如果其他标签上没有侦听器，就没有必要停止冒泡。在这种情况下，事件悄悄地通过父标记，没有额外的影响。

有些事件有默认操作。最明显的例子是单击链接:该事件的默认动作是将当前窗口或框架导航到链接的目的地。如果我们希望完全在 JavaScript 代码中处理链接上的点击，我们可能希望阻止默认操作的发生。

在我们到目前为止的示例中，我们已经处理了 keyup 事件，该事件在释放一个键时被触发。事实证明，这个事件没有默认动作。一个有默认动作的密切相关的事件是 keypress，每当使用 keydown 和 keyup 的组合键入字符时都会发生该事件。keypress 事件是非标准的(即 W3C DOM 标准没有描述它)，这就是为什么我一直避免提及它，但它得到了主流浏览器的良好支持。

假设我们想要阻止按键事件将文本输入到我们的`textarea`中。我们可以通过设置一个事件监听器来取消该类型事件的默认操作。DOM 标准指定了一个名为`preventDefault`的方法来实现这一点，但是同样，Internet Explorer 实现了它自己的专有技术。下面是 DOM 方法:

```
function aKeyWasPressed(e) {   

  e.preventDefault();   

}   

var textarea = document.getElementById('myta');   

textarea.addEventListener('keypress', aKeyWasPressed, false);
```

**在页面加载时分配事件监听器**

到目前为止，在本章的所有例子中，JavaScript 代码必须遵循它分配事件监听器的 HTML 代码。如果 JavaScript 代码先出现，它将无法找到所讨论的 HTML 元素，因为它们还不存在。

这个问题的一个解决方案是在分配给窗口的 load 事件的侦听器中为特定的文档元素分配事件侦听器。因此，只有当文档完成加载并且所有元素都可用时，才会分配事件侦听器。

下面是我们的击键检测示例的完整列表，以这种方式重新组织:

**例 3.7。`keycodedetect.html`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"   

    "https://www.w3.org/TR/html4/strict.dtd">   

<html>   

  <head>   

    <title>Detect keystrokes</title>   

    <script type="text/javascript">   

      function aKeyWasPressed(e) {   

        var key = e.keyCode;   

        alert('You pressed the key: ' + String.fromCharCode(key));   

      }   

      function addListeners(e) {   

        var textarea = document.getElementById('myta');   

        textarea.addEventListener('keyup', aKeyWasPressed, false);   

      }   

      window.addEventListener('load', addListeners, false);   

    </script>   

  </head>   

  <body>   

    <form>   

      <textarea id="myta"></textarea>   

    </form>   

  </body>   

</html>
```

我们的主事件监听器`aKeyWasPressed`没有改变。改变的是这个侦听器的分配方式。分配它的代码已经放在一个新的函数中，`addListeners`:

**例 3.8。`keycodedetect.html`(节选)**

`function addListeners(e) {  
       var textarea = document.getElementById('myta');  
       textarea.addEventListener('keyup', aKeyWasPressed, false);  
     }`

这个函数本身是一个事件监听器，我们将它分配给窗口对象的`load`事件:

**例 3.9。`keycodedetect.html`(节选)**

`window.addEventListener('load', addListeners, false);`

一旦文档完成加载，就会触发此事件，表示所有 HTML 元素现在都可用。`addListeners`函数利用这个机会根据需要将侦听器分配给元素。

在本章和本书的其余部分，我们将继续使用这种结构。

***让事件跨浏览器工作***

自然，让事件跨浏览器工作并不像遵循 DOM 标准那样简单。Internet Explorer 没有很好地实现 DOM 事件模型。相反，它提供了一种专有的、不同的方法来连接事件侦听器并获得对事件数据的访问。

**便携式添加事件监听器**

IE 没有在一个元素上使用`addEventListener`方法，而是有了一个`attachEvent`方法，没有给每个事件监听器传递一个事件对象，而是在 window.event 中有了一个全局事件对象，这虽然不方便，但不是灾难性的；这只是意味着你必须对不同的浏览器采取不同的操作。实际上，这意味着您有少量的标准函数和技术用于执行事件处理操作。其中之一是由 Scott Andrew 创建的`addEvent`函数:

**例 3.10。`portabledetect.php`(节选)**

```
function addEvent(elm, evType, fn, useCapture)    

// cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko   

// By Scott Andrew   

{   

  if (elm.addEventListener) {   

    elm.addEventListener(evType, fn, useCapture);    

    return true;    

  } else if (elm.attachEvent) {   

    var r = elm.attachEvent('on' + evType, fn);    

    return r;    

  } else {   

    elm['on' + evType] = fn;   

  }   

}
```

调用 IE 的`attachEvent`方法，用一个事件名和一个函数做监听器，但是事件名开头要有“on”。上面的`addEvent`函数考虑了跨浏览器的差异(注意，如果浏览器既不支持`addEventListener`也不支持`attachEvent`，Macintosh 的 IE5 就是这种情况，代码使用其`on*event*`将事件监听器直接分配给元素作为事件处理程序)

财产。这将覆盖附加到该事件的任何以前的事件处理程序，这并不好，但这是一个临时的解决方案(比它根本不工作要好)。有一种方法可以解决这个问题，尽管它会使代码变得非常复杂，但确实可以避免这个问题；细节可以在[这篇时髦的脚本博文](https://www.sitepoint.com/blog/)中找到；只需将它包含在您的代码中，然后使用它来附加事件。因此，上面的代码变成了:

```
function aKeyWasPressed(e) {   

  var key = e.keyCode;   

  alert('You pressed the key: ' + String.fromCharCode(key));   

}   

function addListeners(e) {   

  var textarea = document.getElementById('myta');   

  addEvent(textarea, 'keyup', aKeyWasPressed, false);   

}   

addEvent(window, 'load', addListeners, false);   

function addEvent(elm, evType, fn, useCapture)   

// cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko   

// By Scott Andrew   

{   

  if (elm.addEventListener) {   

    elm.addEventListener(evType, fn, useCapture);   

    return true;    

  } else if (elm.attachEvent) {   

    var r = elm.attachEvent('on' + evType, fn);   

    return r;   

  } else {   

    elm['on' + evType] = fn;   

  }   

}
```

我们现在使用`addEvent`函数让`aKeyWasPressed`监听`textarea`上的按键事件。

**便携式检查事件对象**

这不是唯一需要的改变；我们还必须考虑到 IE 不会将事件对象传递给事件侦听器，而是将事件对象存储在窗口对象中。只是为了让我们作为 DHTML 开发人员的生活稍微复杂一点，它还在它创建的事件对象上使用稍微不同的属性。这些如表 3.3 所示。

**表 3.3。W3C 事件对象属性。**

![1478_table3.3sml](img/8d147c236c1718566dc395b0c08e9a6f.png)
[点击可弹出此表的大图。](https://i2.sitepoint.com/graphics/table3.3.png)

[a] 0 =左键；2 =右键；1 =中间按钮。

[b] 1 =左键；2 =右键；4 =中间按钮。对于组合，添加数字:7 表示三个按钮都被按下。

 `如前所述，标准数据属性没有得到很好的支持。

考虑到所有这些，我们的可移植代码变成了:

**例 3.11。`portabledetect.html`(节选)**

```
function aKeyWasPressed(e) {   

  if (window.event) {   

    var key = window.event.keyCode;   

  } else {   

    var key = e.keyCode;   

  }   

  alert('You pressed the key: ' + String.fromCharCode(key));   

}   

function addListeners(e) {   

  var textarea = document.getElementById('myta');   

  addEvent(textarea, 'keyup', aKeyWasPressed, false);   

}   

addEvent(window, 'load', addListeners, false);   

function addEvent(elm, evType, fn, useCapture)   

// cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko   

// By Scott Andrew   

{   

  if (elm.addEventListener) {   

    elm.addEventListener(evType, fn, useCapture);    

    return true;    

  } else if (elm.attachEvent) {   

    var r = elm.attachEvent('on' + evType, fn);    

    return r;    

  } else {   

    elm['on' + evType] = fn;   

  }   

}
```

这个更新版本的`aKeyWasPressed`首先检查 window.event 对象是否存在:

**例 3.12。`portabledetect.html`(节选)**

`if (window.event) {`

如果是，那么它和它对应的 window.event.keyCode 属性被用来获取被按下的键的代码。(这种检查某物是否存在的技术被称为*特征嗅探*，将在下一章详细解释。)如果不是，事件对象传递给函数(如`*e*`

)，它也有一个 keyCode 属性。

**便携式停止传播和默认动作**

停止冒泡可以通过两种方式来完成，就像许多事件处理一样:通过 DOM 方法和 Internet Explorer 方法。在兼容 DOM 的浏览器中，我们可以通过在事件监听器中调用事件对象的`stopPropagation`方法来防止事件冒泡。

在 Internet Explorer 中(其中有一个全局 window.event 对象)，我们在事件侦听器中将 window.event.cancelBubble 设置为`true`。在实践中，通常的技术是使用特性嗅探来做正确的事情:

```
if (window.event && window.event.cancelBubble) {   

  window.event.cancelBubble = true;   

}   

if (e && e.stopPropagation) {   

  // e is the event object passed to this listener   

  e.stopPropagation();   

}
```

不幸的是，即使这样也不能覆盖所有的主流浏览器。可以说，苹果的 Safari 浏览器提供了`stopPropagation`方法，但当它被调用时，实际上并不做任何事情。没有简单的方法可以解决这个问题，但是由于事件冒泡不会显著影响本书中的任何例子，我们现在就忽略这个问题。

我们还需要特征嗅探来停止默认动作。对于 DOM，我们使用传递的事件对象的`preventDefault`方法；使用 Internet Explorer，我们将全局事件对象的 returnValue 属性设置为`false`。

```
if (window.event && window.event.returnValue) {   

  window.event.returnValue = false;   

}   

if (e && e.preventDefault) {   

  e.preventDefault();   

}
```

同样，Safari 似乎支持`preventDefault`，但是当它被调用时，实际上并不做任何事情。不幸的是，对于本书中的许多例子来说，阻止与事件相关的默认动作是一个非常重要的特性。在 Safari 中做到这一点的唯一方法(至少在苹果修复其 DOM 标准事件支持之前)是使用返回`false`的旧式事件处理程序。

例如，为了防止链接的单击事件导航到链接的目标，我们通常只使用一个事件侦听器来阻止链接的默认操作:

```
function cancelClick(e) {   

  if (window.event && window.event.returnValue) {   

    window.event.returnValue = false;   

  }   

  if (e && e.preventDefault) {   

    e.preventDefault();   

  }   

}   

addEvent(myLink, 'click', cancelClick, false);
```

为了在 Safari 中实现这一点，我们需要第二个函数，它将返回`false`来取消事件，我们将把它指定为链接的 onclick 事件处理程序:

```
function cancelClick(e) {   

  if (window.event && window.event.returnValue) {   

    window.event.returnValue = false;   

  }   

  if (e && e.preventDefault) {   

    e.preventDefault();   

  }   

}   

**function cancelClickSafari() {   

  return false;   

}**   

addEvent(myLink, 'click', cancelClick, false);   

**myLink.onclick = cancelClickSafari;**
```

这实际上是一个非常糟糕的解决方案，因为它将覆盖另一个脚本可能已经安装的任何 onclick 事件处理程序。这种脚本间的冲突是现代事件侦听器所要避免的。不幸的是，在 Safari 中没有更好的方法来解决这个问题。在本章的后面，我们将看到这个解决方案的一个实例。

这种跨浏览器编码在很大程度上被实现 W3C DOM 的浏览器制造商所避免，但是对于事件处理来说，它仍然是必需的。

##### 事件的巧妙运用

关于事件如何工作，这就足够了。让我们看几个实际的例子。你现在应该已经知道了足够多的信息，可以完全理解我们在第 2 章*文档对象模型中看到的图像翻转代码。*

***创造更智能的链接***

有些网站会在新窗口中打开所有点击过的链接。通常，他们这样做是为了让用户更容易回到他们的网站，如果它在另一个浏览器窗口中仍然打开的话。一些用户认为这很有用；其他人觉得这非常讨厌。考虑到我们上面的事件处理技术，让他们选择是可能的。

假设我们在页面上放置了一个复选框，它最初是未选中的，并带有标签“在新窗口中打开链接”。如果复选框被选中，单击任何链接将在新窗口中打开该链接。

我们可以使用事件监听器的组合来实现这个功能:我们为页面上的每个链接附加一个点击监听器，它会检查复选框，如果复选框被选中，就会在新窗口中打开相应的链接。我们还需要一个监听器在页面加载时运行，将监听器实际连接到每个链接。

首先，这是我们将要处理的 HTML 页面:

**例 3.13。`smartlinks.html`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"   

    "https://www.w3.org/TR/html4/strict.dtd">   

<html>   

  <head>   

    <title>Smart Links</title>   

    <script type="text/javascript" src="smartlink.js"></script>   

    <style type="text/css">   

      form {   

        float: right;   

        width: 25em;   

        height: 5em;   

        border: 1px solid blue;   

        padding: 1em;   

      }   

    </style>   

  </head>   

  <body>   

    <h1>Smart Links</h1>   

    <form action=""><p>   

      <label for="newwin">Open links in new window?   

        <input type="checkbox" id="newwin">   

      </label>   

    </p></form>   

    <p>This page contains several links, such as   

      <a href="https://www.sitepoint.com/">SitePoint</a>,    

      <a href="http://www.yahoo.com/">Yahoo!</a>, and   

      <a href="http://www.google.com/">Google</a>.   

      These links should ordinarily open in the same window when   

      clicked, unless the checkbox is checked; this will make them   

      open in a new window.   

    </p>   

  </body>   

</html>
```

如您所见，这个页面非常简单，除了`<script>`标签带来的文件之外，不包含任何 JavaScript。图 3.1 显示了代码的显示方式:

**图 3.1。示例“智能链接”网页。**

![Figure 3.1](img/0758753e8f9b54415885cd3428072d74.png)

接下来，我们来看看`smartlink.js`的内容。这段代码是从我们之前的讨论中收集来的，尽管它包含了这个特殊页面的一些额外代码。首先，这里有一个脚本内容的概要:

**例 3.14。`smartlink.js`(节选)**

```
function addEvent(elm, evType, fn, useCapture) { ... }   

function handleLink(e) { ... }   

function cancelClick() { ... }   

function addListeners(e) { ... }   

addEvent(window, 'load', addListeners, false);
```

以下是这四项的详细内容:

**例 3.15。`smartlink.js`**

```
function addEvent(elm, evType, fn, useCapture) {   

  // cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko   

  // By Scott Andrew    

  if (elm.addEventListener) {    

    elm.addEventListener(evType, fn, useCapture);    

    return true;    

  } else if (elm.attachEvent) {    

    var r = elm.attachEvent('on' + evType, fn);    

    return r;    

  } else {   

    elm['on' + evType] = fn;   

  }   

}   

function handleLink(e) {   

  var el;   

  if (window.event && window.event.srcElement)   

    el = window.event.srcElement;   

  if (e && e.target)   

    el = e.target;   

  if (!el)   

    return;   

  while (el.nodeName.toLowerCase() != 'a' &&   

      el.nodeName.toLowerCase() != 'body')   

    el = el.parentNode;   

  if (document.getElementById('newwin') &&   

      document.getElementById('newwin').checked) {   

    window.open(el.href);   

    if (window.event) {   

      window.event.cancelBubble = true;   

      window.event.returnValue = false;   

    }   

    if (e && e.stopPropagation && e.preventDefault) {   

      e.stopPropagation();   

      e.preventDefault();   

    }   

  }   

}   

function cancelClick() {   

  if (document.getElementById('newwin') &&   

      document.getElementById('newwin').checked) {   

    return false;   

  }   

  return true;   

}   

function addListeners() {   

  if (!document.getElementById)   

    return;   

  var all_links = document.getElementsByTagName('a');   

  for (var i = 0; i < all_links.length; i++) {   

    addEvent(all_links[i], 'click', handleLink, false);   

    all_links[i].onclick = cancelClick;   

  }   

}   

addEvent(window, 'load', addListeners, false);
```

我们的代码包括现在熟悉的`addEvent`函数来执行跨浏览器事件连接。一旦页面加载完毕，我们就用它来调用`addListeners`函数。

`addListeners`函数使用另一种熟悉的技术；它遍历页面上的所有链接，并对它们做一些事情。在这种情况下，它附加了`handleLink`函数作为每个链接的点击事件监听器，这样当一个链接被点击时，该函数将被调用。它还附加了`cancelClick`函数作为每个链接的旧式点击事件监听器——这将允许我们在 Safari 中取消每个链接的默认操作。

当我们单击一个链接时，该链接触发一个 click 事件，然后运行`handleLink`。该函数执行以下操作:

**例 3.16。`smartlink.js`(节选)**

`if (window.event && window.event.srcElement)  
   el = window.event.srcElement;  
 if (e && e.target)  
   el = e.target;  
 if (!el)  
   return;`

这是跨浏览器识别哪个链接被点击的方法；我们检查一个`window.event`对象，如果它存在，就用它来获取被点击的链接`window.event.srcElement`。或者，如果`*e*`

，传入的参数，存在，`e.target`也存在，那么我们用它作为点击的链接。如果我们已经检查了`*e*`和`e.target`，但是都不存在，我们就放弃并退出这个函数(用**返回**)。

接下来，我们要确保我们有对 link 元素的引用:

**例 3.17。`smartlink.js`(节选)**

`while (el.nodeName.toLowerCase() != 'a' &&  
     el.nodeName.toLowerCase() != 'body')  
   el = el.parentNode;  
 if (el.nodeName.toLowerCase() == 'body')  
   return;`

一些浏览器可能将链接中的文本节点作为被点击的节点传递，而不是链接本身。如果被点击的元素不是一个`<a>`标签，我们就沿着 DOM 树向上，得到它的父元素(以及那个节点的父元素，等等),直到到达`a`元素。(我们还检查了`body`，以防止无限循环；如果我们像文档`body`一样爬到树上，我们就放弃。)

注意，我们还在元素的 nodeName 上使用了`toLowerCase`。这是确保函数正确处理返回节点名为`A`的浏览器和返回节点名为`a`的`nodeName`的浏览器的最简单方法。

接下来，我们选中我们的复选框:

**例 3.18。`smartlink.js`(节选)**

`if (document.getElementById('newwin') &&  
     document.getElementById('newwin').checked) {`

我们首先确认(出于偏执的原因)有*有*一个带有`id newwin`的元素(T0 是复选框)。然后，如果该复选框被选中，我们将在新窗口中打开该链接:

**例 3.19。`smartlink.js`(节选)**

`window.open(el.href);`

我们知道被点击的链接`el`是一个链接对象，并且链接对象有一个 href 属性。`window.open`方法创建一个新窗口，并将其导航到指定的 URL。

最后，我们负责之后发生的事情:

**例 3.20。`smartlink.js`(节选)**

`if (window.event) {  
     window.event.cancelBubble = true;  
     window.event.returnValue = false;  
   }  
   if (e && e.stopPropagation && e.preventDefault) {  
     e.stopPropagation();  
     e.preventDefault();  
   }  
 }`

我们不希望这个链接有正常的效果，将当前窗口导航到链接的目的地。因此，在跨浏览器的方式中，我们阻止了链接的正常操作。

如前所述，Safari 不支持取消链接默认操作的标准方法，所以我们有一个旧式的事件监听器`cancelClick`，它将在浏览器中取消事件:

**例 3.21。`smartlink.js`(节选)**

```
function cancelClick() {   

  if (document.getElementById('newwin') &&   

      document.getElementById('newwin').checked) {   

    return false;   

  }   

  return true;   

}
```

您可以看到，这些代码中的一些可能会出现在我们尝试的每个项目中，尤其是那些与监听器安装有关的部分。

***让表格更具可读性***

许多应用程序用来显示数据表的一个方便的技巧是突出显示查看者正在查看的单个行和列；基于纸张的表格通常交替地对表格的行和列进行着色，以提供类似的(尽管不是动态的(…至少在纸张技术变得比现在更酷之前！)效果。

下面是这个效果的截图。注意光标的位置。如果我们有另一个光标，您可以看到第二个表以不同的方式突出显示。但是我们没有，所以您只能自己尝试示例代码…

**图 3.2。网页中突出显示表格的示例。**

![Figure 3.2](img/dfe5dd211a6ccd8ec59a391204657264.png)

我们可以使用事件侦听器将这种效果应用于 HTML 文档中的表格。我们将鼠标悬停监听器附加到表中的每个单元格，并让该监听器突出显示位于该单元格的行和列中的所有其他单元格。我们还将附加一个 mouseout 监听器，再次关闭高亮显示。

当我们将 DHTML 的动态功能与 CSS 的页面样式结合起来时，我们在本章中探索的技术就发挥了最大的作用。我们不需要对每个想要照亮的单元格应用高亮，我们只需要对这些单元格应用一个新的类`hi`；我们的 CSS 将准确定义带有类`hi`的表格单元格应该如何显示。要更改突出显示，只需更改 CSS。为了获得更强大的效果，使用 CSS 的选择器根据单元格出现的表格对高亮单元格应用不同的样式。

下面是一个包含表格的示例页面:

**例 3.22。`tableHighlight.html`**

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN"   

    "https://www.w3.org/TR/html4/strict.dtd">   

<html>   

  <head>   

    <title>Highlighted Tables</title>   

    <script type="text/javascript" src="tableHighlight.js">   

    </script>   

    <style type="text/css">   

      tr.hi td, td.hi {   

        background-color: #ccc;   

      }   

      table.extra tr.hi td, table.extra td.hi {   

        color: red;    

        text-decoration: underline overline;   

        background-color: transparent;   

      }   

    </style>   

  </head>   

  <body>   

    <h1>Highlighted Tables</h1>   

    <h2>A table with highlighting</h2>   

    <table>   

      <tr>   

        <td></td>   

        <td>Column 1</td>   

        <td>Column 2</td>    

        <td>Column 3</td>   

        <td>Column 4</td>   

      </tr>   

      <tr>   

        <td>Row 1</td>   

        <td>1,1</td><td>1,2</td><td>1,3</td><td>1,4</td>   

      </tr>   

      <tr>   

        <td>Row 2</td>   

        <td>2,1</td><td>2,2</td><td>2,3</td><td>2,4</td>   

      </tr>   

      <tr>   

       <td>Row 3</td>   

       <td>3,1</td><td>3,2</td><td>3,3</td><td>3,4</td>   

      </tr>   

      <tr>   

        <td>Row 4</td>   

        <td>4,1</td><td>4,2</td><td>4,3</td><td>4,4</td>   

      </tr>   

    </table>   

    <h2>A table with different highlighting</h2>   

    <table class="extra">   

      <tr>   

        <td></td>   

        <td>Column 1</td>   

        <td>Column 2</td>   

        <td>Column 3</td>   

        <td>Column 4</td>   

      </tr>   

      <tr>   

        <td>Row 1</td>   

        <td>1,1</td><td>1,2</td><td>1,3</td><td>1,4</td>   

      </tr>   

      <tr>   

        <td>Row 2</td>   

        <td>2,1</td><td>2,2</td><td>2,3</td><td>2,4</td>   

      </tr>   

      <tr>   

        <td>Row 3</td>   

        <td>3,1</td><td>3,2</td><td>3,3</td><td>3,4</td>   

      </tr>   

      <tr>   

        <td>Row 4</td>   

        <td>4,1</td><td>4,2</td><td>4,3</td><td>4,4</td>   

      </tr>   

    </table>   

  </body>   

</html>
```

该代码创建了两个 4x 4 的表格，每个表格都有列标题和行标题(因此每个表格总共包含 5 行和 5 列)。请注意，这些样式都没有任何效果，因为到目前为止，还没有包含`class="hi"`的元素。

再来看看配套的`tableHighlight.js`脚本。它的结构反映了我们之前的讨论，但是它包含了一些针对这种特殊技术的附加代码。以下是脚本的概要:

**例 3.23。`tableHighlight.js`(节选)**

```
function addEvent(elm, evType, fn, useCapture) { ... }   

function ascendDOM(e, target) { ... }   

function hi_cell(e) { ... }   

function lo_cell(e) { ... }   

function addListeners() { ... }   

addEvent(window, 'load', addListeners, false);
```

请注意，函数大纲与智能链接示例是多么相似。这是六个项目的全部细节。

**例 3.24。`tableHighlight.js`**

```
function addEvent(elm, evType, fn, useCapture)   

// cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko    

// By Scott Andrew    

{   

  if (elm.addEventListener) {   

    elm.addEventListener(evType, fn, useCapture);   

    return true;    

  } else if (elm.attachEvent) {   

    var r = elm.attachEvent('on' + evType, fn);   

    return r;   

  } else {   

    elm['on' + evType] = fn;   

  }   

}   

// climb up the tree to the supplied tag.   

function ascendDOM(e, target) {   

  while (e.nodeName.toLowerCase() != target &&    

      e.nodeName.toLowerCase() != 'html')   

    e = e.parentNode;   

  return (e.nodeName.toLowerCase() == 'html') ? null : e;   

}   

// turn on highlighting   

function hi_cell(e) {   

  var el;   

  if (window.event && window.event.srcElement)   

    el = window.event.srcElement;   

  if (e && e.target)   

    el = e.target;   

  if (!el) return;   

  el = ascendDOM(el, 'td');   

  if (el == null) return;   

  var parent_row = ascendDOM(el, 'tr');   

  if (parent_row == null) return;   

  var parent_table = ascendDOM(parent_row, 'table');   

  if (parent_table == null) return;   

  // row styling   

  parent_row.className += ' hi';   

  // column styling   

  var ci = -1;   

  for (var i = 0; i < parent_row.cells.length; i++) {   

    if (el === parent_row.cells[i]) {   

      ci = i;   

    }   

  }   

  if (ci == -1) return; // this should never happen   

  for (var i = 0; i < parent_table.rows.length; i++) {   

    var cell = parent_table.rows[i].cells[ci];   

    cell.className += ' hi';   

  }   

}   

// turn off highlighting   

function lo_cell(e) {   

  var el;   

  if (window.event && window.event.srcElement)   

    el = window.event.srcElement;   

  if (e && e.target)   

    el = e.target;   

  if (!el) return;   

  el = ascendDOM(el, 'td');   

  if (el == null) return;   

  var parent_row = ascendDOM(el, 'tr');   

  if (parent_row == null) return;   

  var parent_table = ascendDOM(parent_row, 'table');   

  if (parent_table == null) return;   

  // row de-styling   

  parent_row.className =   

      parent_row.className.replace(/b ?hib/, '');   

  // column de-styling   

  var ci = el.cellIndex;   

  for (var i = 0; i < parent_table.rows.length; i++) {   

    var cell = parent_table.rows[i].cells[ci];   

    cell.className = cell.className.replace(/b ?hib/, '');   

  }   

}   

function addListeners() {   

  if (!document.getElementsByTagName) return;   

  var all_cells = document.getElementsByTagName('td');   

  for (var i = 0; i < all_cells.length; i++) {   

    addEvent(all_cells[i], 'mouseover', hi_cell, false);   

    addEvent(all_cells[i], 'mouseout', lo_cell, false);   

  }   

}   

addEvent(window, 'load', addListeners, false);
```

我们使用标准方法添加 mouseover 和 mouseout 事件侦听器。`addListeners`函数将我们的`hi_cell`和`lo_cell`函数分别设置为 mouseover 和 mouseout 事件监听器。

为了尽量减少重复代码，我们添加了一个叫做`ascendDOM`的方便的小实用函数。这将从第一个参数中提供的元素开始沿着树向上移动，以找到其名称与第二个参数匹配的第一个封闭标记。

处理过程如下。将鼠标悬停在表格单元格上会触发`hi_cell`功能。这将找到鼠标经过的单元格，然后计算该单元格所在的行和表格。代码中经常调用`ascendDOM`函数，因此您可以看到将代码放入函数中的好处。在`hi_cell`中，实际完成造型工作的线条是这样的:

**例 3.25。`tableHighlight.js`(节选)**

`parent_row.className += ' hi';`

**例 3.26。`tableHighlight.js`(节选)**

`cell.className += ' hi';`

代码的其余部分只是关心为这些行选择正确的元素。

我们这里的目的是将类`hi`应用于包含鼠标悬停单元格的行中的其他单元格及其列。上面的第一行执行第一个任务。第二行将该类应用于给定的单元格，但是我们的脚本需要首先找到合适的单元格。

这就是事情变得有点复杂的地方。行是一个简单的`<tr>`标记，而列是分散在表中所有行的单元格列表。根据 DOM Level 2 规范，表格单元格元素有一个`cellIndex`属性，表示单元格在行中的索引。为了找到该列中的其他单元格，我们可以遍历表中的所有行，并在每一行中找到具有相同`cellIndex`的单元格。

遗憾的是，Safari 并不支持`cellIndex`——它总是被设置为`0`，不管实际的索引应该是什么。如果 Safari 支持`cellIndex`，这个过程可能会很简单:

`var ci = el.cellIndex;`

事实上，这个简洁的片段必须替换为下面更长的部分:

**例 3.27。`tableHighlight.js`(节选)**

`var ci = -1;  
 for (var i = 0; i < parent_row.cells.length; i++) {  
   if (el === parent_row.cells[i]) {  
     ci = i;  
   }  
 }  
 if (ci == -1) return; // this should never happen`

```
ci is the cellIndex, and can be used to highlight other cells with the same cellIndex in the other rows in the table:
```

**例 3.28。`tableHighlight.js`(节选)**

`for (var i = 0; i < parent_table.rows.length; i++) {  
   var cell = parent_table.rows[i].cells[ci];  
   cell.className += ' hi';  
 }`

表格的所有行都保存在表格的 rows 数组中。我们遍历该数组，将`hi`类应用于每行中与鼠标悬停单元格具有相同索引的单元格。

本练习的结果是，鼠标悬停在同一列中的所有单元格都将拥有类`hi`；包含该单元格的表格行也将拥有类`hi`。

我们的 CSS 代码会处理这些单元格的外观:

**例 3.29。`tableHighlight.html`(节选)**

`tr.hi td, td.hi {  
       background-color: #ccc;  
     }`

我们已经将类别为`hi`的背景色应用于类别为`hi`的`tr`中的`td`和 tds 因此，这些单元格将被突出显示。`lo_cell`函数的工作方式类似，只是它从行和列中删除了类`hi`，而不是应用它。删除是通过以下几行完成的:

**例 3.30。`tableHighlight.js`(节选)**

`parent_row.className =  
     parent_row.className.replace(/b ?hib/, '');`

**例 3.31。`tableHighlight.js`(节选)**

`cell.className = cell.className.replace(/b ?hib/, '');`

由于 className 是一个字符串，所以它有一个字符串的所有方法，其中一个是`replace`；我们可以用一个正则表达式(第一个参数)和一个替代字符串(第二个参数)调用`replace`方法。如果在字符串中找到正则表达式的匹配项，它将被替换字符串替换。在我们的例子中，我们寻找与表达式`b ?hib`(注意正则表达式由斜杠而不是引号分隔)的匹配，也就是说，一个单词边界后跟一个可选的空格、单词“hi”和另一个单词边界，并用一个空字符串替换它，从而将其从类名中删除。

使用 CSS 提供样式信息的额外好处是，我们可以在不改变脚本的情况下对页面上的不同表格应用不同的突出显示。例如，页面的 HTML 包含两个表，一个包含一个类`extra`。我们将一些 CSS 专门应用于具有类`extra`的表:

**例 3.32。`tableHighlight.html`(节选)**

```
table.extra tr.hi td, table.extra td.hi {    

  color: red;    

  text-decoration: underline overline;    

  background-color: transparent;    

}
```

因此，该特定表格中突出显示的单元格将以不同方式突出显示。CSS 使得实现这种效果非常容易。

##### 摘要

理解触发事件的过程，以及哪些代码与这些事件挂钩，对于 DHTML 编程至关重要。几乎你在 DHTML 中做的每一件事都会涉及到将代码附加到事件上，正如本章所描述的。我们已经研究了一些常见的事件和监听它们的两种浏览器模型。我们还讨论了当事件触发时会发生什么，以及如何中断或改变这个过程。最后，我们详细查看了几个事件，并看到了一些简单的例子，说明代码如何附加到这些事件上，并在使用这些技术的站点上改善用户体验。

##### 第四章。检测浏览器功能

你刚刚列出了我所有的优点。

–猫，*红矮星*，第三季，DNA 集

在你的网站上添加 DHTML 时，一个重要的设计约束是它应该不引人注目。所谓“不唐突”，我的意思是如果一个给定的 Web 浏览器不支持您正在使用的 DHTML 特性，这种缺失应该尽可能少地影响用户体验。不应该向用户显示错误:没有 DHTML 增强，站点应该完全可用。呈现网站的浏览器分为以下几大类:

1.  完全不提供 JavaScript 支持，或者关闭 JavaScript。

3.  提供一些 JavaScript 支持，但缺少现代功能。

5.  拥有完整的 JavaScript 支持，但完全不提供 W3C DOM 支持。

7.  提供不完整的 DOM 支持，但是一些 DOM 特性缺失或者有问题。

9.  提供完整的 DOM 支持，没有错误。

作为 DHTML 开发人员，第一类和最后一类与您无关。一个根本不运行 JavaScript 的浏览器不需要调用任何 DHTML 代码就可以工作，所以在讨论中可以忽略它。您只需要确保在关闭 JavaScript 时页面能够正确显示。(例如，如果您的 DHTML 显示和隐藏页面的某些区域，这些区域最初应该显示，然后用 DHTML 隐藏，以便它们对非 DHTML 浏览器可用。)类似地，一个完全实现 DOM 并且没有错误的浏览器会让生活变得非常容易。可惜这样的浏览器不存在。

在这一章中，我们关心列表中间的三个类别。在这里，我们将探索如何在试图利用这些特性运行我们的代码之前，识别给定浏览器支持哪些 DHTML 特性。

基本上有两种方法可以判断正在使用的浏览器是否支持给定的特性。(实际上，还有第三种方法来识别浏览器支持。DOM 标准指定了一个`document.implementation.hasFeature`方法，可以用来检测 DOM 支持。虽然很少使用。)第一种方法是找出正在使用的浏览器，然后在代码中列出哪个浏览器支持哪些功能。第二种方法是直接测试所需特性的存在。在接下来的讨论中，我们将会看到，按类型对浏览器进行分类不如逐个检测特性好。

##### 老式的浏览器嗅探

在过去糟糕的日子里，在浏览器制造商对 DOM 进行标准化之前，JavaScript 开发人员依赖于通过称为浏览器嗅探的过程来检测浏览器的品牌和版本。每个浏览器都提供了一个 window.navigator 对象，包含浏览器的详细信息，可以从 JavaScript 中查看。例如，我们可以找到浏览器的名称(“用户代理字符串”)，如下所示:

```
var browserName = navigator.userAgent;    

var isIE = browserName.match(/MSIE/); // find IE and look-alikes
```

不要再这样了！这种技术，就像 JavaScript 编码黑暗时代(W3C DOM 规范出现之前)的许多遗迹一样，*不应该被使用*。浏览器嗅探是不可靠的，容易出错，应该像躲避瘟疫一样避免。*真的*:我不是在开玩笑。

为什么我对浏览器嗅探如此不热心？有很多原因。一些浏览器撒谎，或试图掩盖他们的真实细节；有些工具，比如 Opera，可以被配置为提供用户选择的用户代理字符串。几乎不可能保持每个浏览器的每个版本都是最新的，也不可能知道每个版本在发布时支持哪些功能。此外，如果您的站点需要持续一段合理的时间，新的浏览器版本将在您的站点之后发布，您的浏览器嗅探代码将无法解释它们。浏览器嗅探——它仅存的一点点——应该被限制在历史的垃圾箱里。把它归入“我们不知道更好的”一类。有一种更好的方法:特性嗅探。

##### 现代 DOM 特性嗅探

与其检测用户的浏览器，然后自己判断它是否支持给定的特性，不如直接询问浏览器是否支持该特性。例如，很大一部分 DHTML 脚本使用 DOM 方法`getElementById`。要确定特定访问者的浏览器是否支持这种方法，您可以使用:

```
if (document.getElementById) {    

  // and here you know it is supported    

}
```

如果`if`语句测试通过，我们知道浏览器支持这个特性。需要注意的是`getElementById`后面没有括号！我们不说:

```
if (document.getElementById()) 
```

如果我们包括括号，我们称这个方法为`getElementById`。如果我们不包括括号，我们指的是作为方法基础的 JavaScript `Function`对象。这是一个非常重要的区别。包含括号意味着我们在测试方法调用的返回值，这是我们不想做的。首先，这将在非 DOM 浏览器中导致错误，因为我们根本不能在那里调用`getElementById`方法——它不存在！当我们测试`Function`对象时，我们评估它的存在。不支持该方法的浏览器将无法通过测试。因此，它们不会运行由`if`语句包围的代码；它们也不会显示错误。

JavaScript 的这个特性——测试一个方法是否存在的能力——从一开始就是语言的一部分；因此，即使在最老的支持 JavaScript 的浏览器上使用它也是安全的。您可能还记得上一章中提到的不调用对象就引用一个`Function`对象的技巧。在第 3 章，*处理 DOM 事件*，我们用它来分配一个函数作为事件监听器，而不是实际调用它。在 JavaScript 中，只要你足够努力，任何东西都可以被当作一个对象；方法也不例外！

***我们应该测试哪些 DOM 特性？***

最简单的方法是测试您打算使用的每一个 DOM 方法。如果你的代码使用了`getElementById`和`createElement`，测试这两种方法是否存在。这将涵盖上述第四类浏览器:实现部分(但不是全部)DOM 的浏览器。

假设一个支持`getElementById`的浏览器也支持`getElementsByTagName`是不合理的。您必须明确测试每个特性。

***我们应该在哪里测试 DOM 特性？***

处理这些测试的一个简单方法是在您的 DHTML 设置任何事件侦听器之前执行它们。DHTML 脚本的一个大的子集通过在页面加载上设置一些事件侦听器来工作，这些事件侦听器将作为浏览器触发事件中的各种元素来调用。如果在设置事件侦听器之前，您检查浏览器是否提供了代码所需的所有 DOM 功能，则不会为不支持这些功能的浏览器设置事件侦听器。因此，在设置事件侦听器时，您可以合理地假设您需要的所有特性都是可用的；这种假设可以极大地简化您的代码。这里有一个例子:

```
function myScriptInit() {    

  if (!document.getElementById ||    

      !document.getElementsByTagName ||    

      !document.createElement) {    

    return;    

  }    

  // set up the event listeners here    

}    

function myScriptEventListener() {    

  var foo = document.getElementById('foo');  // safe to use    

}    

addEvent(window, 'load', myScriptInit, false);
```

这个脚本包含一个`myScriptInit`函数，它将`myScriptEventListener`设置为一个事件监听器。但是，在设置监听器之前，我们检查 DOM 方法`getElementById`、`getElementsByTagName`和`createElement`是否存在。

**if** 语句说:“如果 JavaScript `Function`对象`document.getElementById`不存在，或者`Function`对象`document.getElementsByTagName`不存在，或者`Function`对象`document.createElement`不存在，退出`myScriptInit`函数。”这意味着，如果这些对象中的任何一个不被支持，`myScriptInit`函数将在该点退出:它甚至不会设置事件监听器。我们的代码将只在支持这些方法的浏览器上设置监听器。因此，如上所述，监听器函数`myScriptEventListener`在使用`document.getElementById`时可以感到安全，而无需首先检查以确保它被支持。如果它不被支持，监听器功能就不会被设置。

所有这些嗅探都依赖于 JavaScript 的运行时行为。即使脚本在加载时被浏览器读取，也不会对脚本中声明的对象进行检查，直到代码运行。这允许我们将浏览器对象放在所有脚本中，并且仅当我们的检测代码找到它时才使用它们:一种称为*后期绑定*的安排。

***测试非 DOM 特征***

特性嗅探可以用于任何 JavaScript 对象:不仅仅是方法，也不仅仅是那些属于 DOM 的方法。常用的例子是元素的偏移属性(`offsetWidth`、`offsetHeight`、`offsetLeft`和`offsetTop`)。这些 JavaScript 属性是所有主流浏览器提供的 DOM 的扩展。它们以像素为单位返回元素的大小和位置信息。我们可以测试这些属性是否在给定元素的对象上定义，如下所示:

```
var foo = document.getElementById('foo');    

if (typeof foo.offsetHeight != 'undefined') {    

  var fooHeight = foo.offsetHeight;    

}
```

这里，当且仅当 foo 支持 offsetHeight 时，我们才设置 fooHeight。这是一种与我们之前使用的方法不同的检查类型:难道不能简单地说， **if `(foo.offsetHeight)`** ？这不是一个好的方法。如果`foo.offsetHeight`没有定义， **if (foo.offsetHeight)** 不会为真，正如我们所料。然而，如果`foo.offsetHeight`确实存在，但等于`0`(零)，那么 **if** 语句也将失败。这是可能的，因为 JavaScript 将零视为表示`false`。测试一个给定的项是否被定义变得稍微复杂了一点(但只是一点点！).

如果您正在测试函数`functionName`或方法`methodName`(在对象`obj`上)的存在性，请使用不带括号的函数/方法名称:

```
if (functionName) { ... }    

if (obj.methodName) { ... }
```

同样，如果您正在测试一个变量`v`，或者一个对象的 DOM 属性 prop，您通常可以直接使用变量或者 DOM 属性的属性名:

```
if (v) { ... }    

if (obj.prop) { ... }
```

但是，小心！如果变量或属性包含数字或字符串(例如`offsetHeight`)，那么就使用`typeof`，因为数字可能是`0`(零)，而字符串可能是空字符串“”，两者的计算结果都是`false`:

```
if (typeof v != 'undefined') { ... }    

if (typeof obj.prop != 'undefined') { ... }
```

##### 工作中的嗅探:`scrollImage`

许多网站都包含照片库:列出照片缩略图的页面，点击后，可以显示照片的全尺寸。对这样一个网站的一个有趣的增强可能是让用户看到全尺寸的照片，而不必点击加载它。当用户将鼠标放在缩略图上时，该缩略图会变成一个“查看区域”,其中会显示全尺寸图像的一部分。如果您的缩略图不够详细，用户无法区分表面相似的图像，这种技术非常有用。如果您的缩略图显示类似于文档的内容，而不是照片，这将非常方便。图 4.1 显示了最终效果:

**图 4.1。由`scrollImage`示例实现的缩略图显示。**

![1478_ScrollImage](img/3fbb1144b25dec4576071c9f71a3ba5f.png)。

我们一会儿会描述这里发生的事情。我们将首先检查代码，然后在得到解释之前看一个演示。

***设置页面***

这项技术的 HTML 文件很简单:

**例 4.1。`scrollImage.html`**

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"    

    "https://www.w3.org/TR/html4/strict.dtd">    

<html>    

  <head>    

    <title>ScrollImage demonstration</title>    

    <script src="scrollImage.js" type="text/javascript"></script>    

    <style type="text/css">    

      .scrollimage {    

        display: block;    

        float: left;    

        border: 1px solid black;    

        margin: 1em;    

        padding: 0;    

      }    

      .scrollimage:hover {    

        position: relative;    

      }    

      .scrollimage img {    

        border: none;    

      }    

      .scrollimage:hover img {    

        display: none;    

      }    

    </style>    

  </head>    

  <body>    

    <h1>Scanned documents</h1>    

    <p>    

      <a href="1.jpg" class="scrollimage"    

         mainx="563" mainy="823" thumbx="82" thumby="120"    

         style="background: url(1.jpg); width: 82px;    

         height: 120px;"    

      ><img src="1-thumb.jpg"></a>    

      <a href="2.jpg" class="scrollimage"    

         mainx="563" mainy="777" thumbx="87" thumby="120"    

         style="background: url(2.jpg); width: 87px;    

         height: 120px;"    

      ><img src="2-thumb.jpg"></a>    

      <a href="3.jpg" class="scrollimage"    

         mainx="567" mainy="823" thumbx="83" thumby="120"    

         style="background: url(3.jpg); width: 83px;    

         height: 120px;"    

      ><img src="3-thumb.jpg"></a>    

      <a href="4.jpg" class="scrollimage"    

         mainx="558" mainy="806" thumbx="83" thumby="120"    

         style="background: url(4.jpg); width: 83px;    

         height: 120px;"    

      ><img src="4-thumb.jpg"></a>    

      <a href="5.jpg" class="scrollimage"    

         mainx="434" mainy="467" thumbx="112" thumby="120"    

         style="background: url(5.jpg); width: 112px;    

         height: 120px;"    

      ><img src="5-thumb.jpg"></a>    

    </p>    

  </body>    

</html>/#pc#/    

The content of this page is fairly obvious. Notice how the image elements are hidden by CSS styles when the mouse moves over them. This page also includes - with the <script src="scrollImage.js" type="text/javascript"></script> line - this JavaScript file:    

Example 4.2\. `scrollImage.js`    

/#pc#/// Based on findPos*, by ppk    

// (http://www.quirksmode.org/js/findpos.html)    

function findPosX(obj) {    

  var curLeft = 0;    

  if (obj.offsetParent) {    

    do {    

      curLeft += obj.offsetLeft;    

    } while (obj = obj.offsetParent);    

  }    

  else if (obj.x) {    

    curLeft += obj.x;    

  }    

  return curLeft;    

}    

function findPosY(obj) {    

  var curTop = 0;    

  if (obj.offsetParent) {    

    do {    

      curTop += obj.offsetTop;    

    } while (obj = obj.offsetParent);    

  }    

  else if (obj.y) {    

    curTop += obj.y;    

  }    

  return curTop;    

}    

// cross-browser event handling for IE5+, NS6+ and Mozilla/Gecko    

// By Scott Andrew    

function addEvent(obj, evType, fn, useCapture) {    

  if (obj.addEventListener) {    

    obj.addEventListener(evType, fn, useCapture);    

    return true;    

  } else if (obj.attachEvent) {    

    var r = obj.attachEvent('on' + evType, fn);    

    return r;    

  } else {     

    obj['on' + evType] = fn;    

  }     

}    

addEvent(window, 'load', scrollInit, false);    

function scrollInit() {    

  if (!document.getElementsByTagName)    

    return;    

  var allLinks = document.getElementsByTagName('a');    

  for (var i = 0; i < allLinks.length; i++) {    

    var link = allLinks[i];    

    if ((' ' + link . className + ' ').indexOf(' scrollimage ') !=    

        -1) {    

      addEvent(link, 'mousemove', moveListener, false);    

    }    

  }    

}    

function attVal(element, attName) {    

  return parseInt(element.getAttribute(attName));    

}    

function moveListener(ev) {    

  var e = window.event ? window.event : ev;    

  var t = e.target ? e.target : e.srcElement;    

  var xPos = e.clientX - findPosX(t);    

  var yPos = e.clientY - findPosY(t);    

  if (t.nodeName.toLowerCase() == 'img')     

    t = t.parentNode;    

  if (t.nodeName.toLowerCase() == 'a') {    

    // scaleFactorY = (width(big) - width(small)) / width(small)    

    var scaleFactorY =    

        (attVal(t, 'mainy') - attVal(t, 'thumby')) / attVal(t,    

        'thumby');    

    var scaleFactorX =    

        (attVal(t, 'mainx') - attVal(t, 'thumbx')) / attVal(t,    

        'thumbx');    

    t.style.backgroundPosition =    

        (-parseInt(xPos * scaleFactorX)) + 'px ' +    

        (-parseInt(yPos * scaleFactorY)) + 'px';    

  }    

}
```

我们将探索(并修复！)这段代码。最后，页面还包含图像:五个全尺寸图像和五个缩略图。您可以在本书的代码档案中找到它们。

***演示 DHTML 效应***

让我们看看这个页面是如何工作的。HTML 文档将五幅图像显示为缩略图；在本例中，它们是扫描文档中各个页面的缩略图。图 4.2 显示了正常情况下的页面内容。

**图 4.2。文档的缩略图。**

![1478_Thumbnails](img/f4001ef6105d674a0db2609d67ff1f3c.png)

但是，当我们将鼠标悬停在缩略图上时，缩略图的显示会发生变化，显示它所链接的实际图像，如图 4.3 所示。

缩略图变成了一个查看区域，我们可以在其中看到完整大小图像的片段。当光标在第三个图像上移动时，我们可以通过查看区域看到第三个图像的全尺寸内容。对于这样的文档缩略图，我们可以使用光标在查看区域内移动文档，这样我们就可以阅读内容并查看它是否是我们想要的文档。如上所述，这种技术在包含以缩略图尺寸显示时看起来相似的图像的照片图库中也很有用。

**图 4.3。鼠标悬停在缩略图上。**

![1478_MousingThumb](img/0ba88a6cc2fec03f48c0e28067399310.png)

***代码如何工作***

从概念上讲，代码工作如下:我们设置页面，使每个“可滚动”的图像都由类`scrollimage`的`<a>`标签组成，该标签包含显示缩略图的`<img>`标签。我们应用全尺寸图像作为`<a>`标签的 CSS 背景图像。然后，当用户将鼠标放在`a`元素上时，我们完全隐藏`img`元素，允许`a`元素的背景图像显示出来。然后，我们操纵背景图像的位置，使其随光标移动。(我们将大图的尺寸存储在`a`元素的自定义属性中:`mainx`、`mainy`、`thumbx`和`thumby`。这是一个有点可疑的技术:它会阻止 HTML 的验证，因此应该小心使用。然而，在这种情况下，将所需的值与每个 a 元素联系起来是最简单的方法。)

这都是相当高级的东西，所以我们需要确认运行的浏览器支持我们需要的所有功能，以便使它工作。我们从在页面加载时用下面一行初始化脚本开始:

**例 4.3。`scrollImage.js`(节选)**

```
addEvent(window, 'load', scrollInit, false);/#pc#/    

We saw the addEvent method in Chapter 3, *Handling DOM Events*, but, with what we've learned about feature detection, its workings should now be much clearer to you. First, we check for the existence of an addEventListener method on the passed object, to see if the user's browser supports the DOM Events model correctly:    

Example 4.4\. `scrollImage.js` (excerpt)    

/#pc#/function addEvent(obj, evType, fn, useCapture) {    

  if (obj.addEventListener) {    

    obj.addEventListener(evType, fn, useCapture);    

    return true;
```

如果失败，我们就在对象上寻找 Internet Explorer 专有的`attachEvent`方法。

**例 4.5。`scrollImage.js`(节选)**

`} else if (obj.attachEvent) {    
   var r = obj.attachEvent('on' + evType, fn);    
   return r;`

如果*没有*，我们将事件监听器直接附加到元素上，作为事件处理程序；这是 Macintosh 上的 IE5 所必需的。

**例 4.6。`scrollImage.js`(节选)**

`} else {    
   obj['on' + evType] = fn;    
 }`

这个过程满足了我们可能附加事件侦听器的所有方式，使用特性嗅探来查看哪个选项可用。

设置滚动效果的初始化函数`scrollInit`，使用`document.getElementsByTagName`查找文档中所有的`a`元素。因此，`scrollInit`在继续之前检查该方法是否存在:

**例 4.7。`scrollImage.js`(节选)**

```
function scrollInit() {    

  if (!document.getElementsByTagName)    

    return;
```

如果用户的浏览器不支持文档`.getElementsByTagName`，那么我们从`scrollInit`函数返回，不再前进。

如第 3 章*处理 DOM 事件*中所述，特性嗅探代码中的一个额外技巧是，当我们在`moveListener`事件侦听器中时，解决我们找到事件对象的方法。正如我们所知，DOM Events 规范要求将事件对象作为参数传递给事件监听器，而 Internet Explorer 将事件对象作为全局 window.event 提供。因此，我们的代码检查`window.event`是否存在，如果存在，就将其用作事件对象；如果 window.event 不存在，则代码回退到传入的参数:

**例 4.8。`scrollImage.js`(节选)**

```
function moveListener(ev) {    

  var e = window.event ? window.event : ev;
```

接下来，我们需要从事件对象中获取事件的目标；DOM 指定了`e.target`，Internet Explorer 提供了`e.srcElement`。另一个特性——嗅探为我们提供了适当的值:

**例 4.9。`scrollImage.js`(节选)**

`var t = e.target ? e.target : e.srcElement;`

这是我们在第 3 章*中看到的代码的压缩简写版本，处理 DOM 事件*。

下一步是让代码获取鼠标在缩略图区域内的位置。这是上面完整清单中的代码，应该可以做到这一点:

`var xPos = e.clientX - findPosX(t);    
 var yPos = e.clientY - findPosY(t);`

理论上，e.clientX 和 e.clientY 分别给出了鼠标在浏览器窗口中的 x 和 y 坐标。通过从这些数据中减去目标元素的 x 和 y 坐标，我们获得了鼠标在该元素中的位置。

根据您选择的浏览器，乍一看，这似乎很好。Peter-Paul Koch 的`findPosX`和`findPosY`函数可以快速获得目标元素的位置。(关于`findPosX`和`findPosY`如何工作的完整描述，请访问[彼得-保罗·科赫关于这个主题的页面](http://www.quirksmode.org/js/findpos.html)。)不幸的是，事件对象的 clientX 和 clientY 属性并不可靠。

***clientX 和 clientY 问题***

上面的代码有缺陷:事件监听器使用`e.clientX`和`e.clientY`来确定鼠标的位置。

但这不是缺点，对吧？毕竟是在 DOM 规范里面！

嗯，这是一种缺陷——浏览器制造商解释规范的一种缺陷。彼得-保罗·科赫在他的综合文章 [*不可能完成的任务——鼠标位置*](http://evolt.org/article/Mission_Impossible_mouse_position/17/23335/) 中非常详细地研究了这个问题。只有当页面滚动时，问题才会出现(上面的页面不是这种情况)。当页面滚动时，对于 clientX 和 clientY 是相对于整个文档返回，还是相对于窗口(可见的文档部分)返回，规范是相当模糊的。Internet Explorer 相对于窗口返回它们，Mozilla 也是如此，但是 Opera、Konqueror 和 iCab 都相对于文档返回它们。Netscape 还提供 pageX 和 pageY，它们是相对于文档的鼠标坐标。(具有讽刺意味的是，Internet Explorer 可能是唯一完全符合该标准的浏览器；对规范的最好理解是 clientX 和 clientY 应该是相对于窗口的。)

所以，如果 pageX 和 pageY 存在，我们需要使用它们，如果它们不存在，我们需要使用 clientX 和 clientY 但是，如果我们在 Internet Explorer 中，我们必须向 clientX 和 clientY 添加页面滚动的数量。但是我们如何知道我们是否在 Internet Explorer 中呢？我们使用浏览器检测。

**你无法避免的浏览器检测**

你可以在背景中听到的杂乱噪音是人们正确地指出，我们在几页前将浏览器检测扔进了历史的垃圾箱，他们没有错。然而，有时不同的浏览器以不同的方式*和*实现相同的属性(在这种情况下，clientX 和 clientY ),这时没有其他对象可供嗅探，我们可以知道使用的是不同的实现中的哪一个。

在这种情况下，别无选择，只能使用可怕的浏览器嗅探来确定该做什么。这里描述的鼠标位置问题几乎是唯一的这种情况。一想到可能有必要使用浏览器检测，所有思维正常的 DHTML 开发人员都会感到内疚，但是，可悲的是，没有任何办法！我们将浏览器检测脚本添加到代码中，然后调用`addEvent`来设置我们的窗口加载监听器:

**例 4.10。`scrollImage.js`(节选)**

```
var isIE = !window.opera && navigator.userAgent.indexOf('MSIE') !=    

    -1;
```

注意，首先，我们检查 window.opera 是`false`还是不存在；Opera 设置这个变量是为了让脚本更容易检测到它是正在使用的浏览器(Opera 还实现了用户代理切换，因此，从`navigator.userAgent`的角度来看，它可以表现为 Internet Explorer)。一旦我们确定我们是*而不是*在使用 Opera，我们继续在用户代理字符串中寻找“MSIE ”;如果存在，则 Internet Explorer 是正在使用的浏览器。

我们更新后的`moveListener`事件监听器现在看起来像这样:

**例 4.11。`scrollImage.js`(节选)**

```
function moveListener(ev) {    

  var e = window.event ? window.event : ev;    

  var t = e.target ? e.target : e.srcElement;    

  var mX, mY;    

  if (e.pageX && e.pageY) {    

    mX = e.pageX;    

    my = e.pageY;    

  } else if (e.clientX && e.clientY) {    

    mX = e.clientX;    

    mY = e.clientY;    

    if (isIE) {    

      mX += document.body.scrollLeft;    

      mY += document.body.scrollTop;    

    }    

  }    

  var xPos = mX - findPosX(t);    

  var yPos = mY - findPosY(t);    

// ... the rest as before ...
```

注意，我们首先检查 pageX 和 pageY(对于 Mozilla ),然后检查 clientX 和 clientY。我们通过检查`isIE`变量来处理 Internet Explorer 如果是`true`，我们根据需要添加文档的滚动量。我们尽可能少地使用浏览器检测；具体来说，Netscape/Mozilla 提供了 pageX 和 pageY 属性，我们通过特性嗅探来寻找它们，*而不是*通过对 Mozilla 执行浏览器检测。

***计算屏幕位置***

我们代码的最后一部分与浏览器检测没有什么关系，但是，花了这么多时间来获得正确的 X 和 Y 坐标，理解如何使用它们是有意义的。

`moveListener`函数的最后一部分以几个 **if** 开始，这确保我们手头有一个对感兴趣的缩略图`<img>`周围的`<a>`标签的引用。这并不奇怪，所以我们获取了所需的 DOM 元素:

**例 4.12。`scrollImage.js`(节选)**

`if (t.nodeName.toLowerCase() == 'img')    
   t = t.parentNode;    
 if (t.nodeName.toLowerCase() == 'a') {`

接下来，我们进行两组计算中的第一组:

**例 4.13。`scrollImage.js`(节选)**

`// scaleFactorY = (width(big) - width(small)) / width(small)    
   var scaleFactorY =    
       (attVal(t, 'mainy') - attVal(t, 'thumby')) / attVal(t,    
       'thumby');    
   var scaleFactorX =    
       (attVal(t, 'mainx') - attVal(t, 'thumbx')) / attVal(t,    
       'thumbx');`

像这样的代码很可能是针对你所承担的每一个 DHTML 效果的，但是你必须做的令人困惑的事情在所有情况下都是相似的。深呼吸:开始了！

通过查看区域显示大背景图像，当光标位于该查看区域的左上角时，应该出现什么？大图的左上角应该在查看区域的左上角:这很简单。现在，当光标位于查看区域的右下角时，应该出现什么？全尺寸图像的右下角应该在观看区域的左上角吗？这就是当光标在查看区域上移动完整距离时，如果大图像在查看区域上移动其完整尺寸会发生的情况。仔细想想；你可能想尝试用两张纸做实验，其中一张纸上有一个长方形的洞。大图像最终会从查看区域的左上角消失！如果背景图像是平铺的(默认)，图像的额外副本将在右下角可见-这是一个非常奇怪的结果。

我们不希望图像移动那么远。如果我们将光标移动到查看区域的最右下角，我们希望大图像移动几乎它的整个尺寸-但不完全是这样！我们希望大图像的右下角只移动到查看区域的右下角，而不要再向左上方移动。

现在，为了让大图像移动，我们必须计算移动的距离。举几个例子:假设大图是缩略图的十倍。假设图像每边 500 像素，缩略图每边 50 像素。对于光标移动的每一个像素，大图像应该移动 500/50:十倍的速度。所以“比例因子”是 10。但是，等一下！如果光标向左移动 50 个像素，大图像将从查看区域的左边缘向左:右移动 500 个像素。那太远了。我们希望它最多移动 500 *减去* 50 个像素，这样它总是在可视区域内。因此，实际的比例因子是(500-50)/50 = 9。全尺寸图像的移动速度应该是光标的九倍。这就是第一组计算所做的，除了它在两个维度上计算比例因子，因为大多数图像是矩形，而不是正方形。

接下来，我们要移动大图像。这是第二组计算:

**例 4.14。`scrollImage.js`(节选)**

`t.style.backgroundPosition =    
       (-parseInt(xPos * scaleFactorX)) + 'px ' +    
       (-parseInt(yPos * scaleFactorY)) + 'px';`

现在，如果(举例来说)我们将鼠标从左上向右下移动，我们将对角线扫描整个查看区域。当我们移动时，我们希望大图像的新区域进入视野。所以大图最好向鼠标相反的方向滑动:向上，超过左上方。这就像使用负边距将文本出血到页面的左侧和顶部。这就是我们通过计算负像素数所做的。

这个想法最初可能看起来前后颠倒。把它想象成你在为一部电影拍摄一个场景。相机(缩略图查看区域)是固定的，所以如果要有平移效果，必须是相机指向的场景在移动。另一方面，想象你不转头看着窗外行驶的火车。又是同样的效果，前提是火车倒着开！

##### 摘要

在这一章中，我们了解到浏览器并不总是支持我们想要的所有 DOM 特性，并讨论了特性嗅探如何帮助我们作为 DHTML 开发人员围绕这个问题进行防御性编码。浏览器嗅探允许我们将动态特性交付给能够处理它们的浏览器，同时避免不能处理它们的浏览器崩溃或抛出错误。我们查看了旧的方法，浏览器嗅探，并解释了如果可能的话为什么不应该使用它。然后，我们研究了一种情况，在这种情况下，特性嗅探不能提供我们所需要的一切，只能将旧的方法作为最后的手段。

以上就是这个节选的 *DHTML 乌托邦:使用 JavaScript & DOM* 的现代网页设计！下一步是什么？

[下载 PDF 格式的本章内容](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，您将拥有一份可以随时参考的副本。

回顾一下这本书的目录，找出到底包括了什么。

现在就在 SitePoint.com 买一本属于你自己的书。

我们希望你喜欢 [*DHTML 乌托邦:使用 JavaScript&DOM*的现代网页设计。](https://www.sitepoint.com/books/dhtml1/)

## 分享这篇文章`