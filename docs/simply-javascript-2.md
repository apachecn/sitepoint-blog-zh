# 简单来说就是 JavaScript:网络的三层

> 原文：<https://www.sitepoint.com/simply-javascript-2/>

***“从前，有…‘一个国王！’我的小读者马上会说。不，孩子们，你们错了。从前有一块木头……*T3—*匹诺曹历险记***

没有 JavaScript 也可以做很多事情。使用超文本标记语言(HTML——在本文中，我们将 HTML 和 XHTML 统称为 HTML。选择哪一个取决于你自己，与 JavaScript 没有太大关系。如果这对您很重要，我们将在本文中展示的 HTML 代码将是有效的 XHTML 1.0 Strict)，您可以生成复杂的文档，这些文档错综复杂地描述页面的内容以及内容的含义，甚至是最微小的细节。在新的 [*简单 Javascript*](https://www.sitepoint.com/books/javascript1/) 的这一章中(你也可以[下载这篇文章](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，以及另外两篇文章，作为 PDF)，我将向你展示通过使用层叠样式表(CSS)，你可以用无数种方式来呈现内容，变化小到一种颜色，大到用图像替换文本。

然而，无论你如何装扮它，HTML 和 CSS 只能实现一个动画怪物，当有东西在附近移动时，它就会不稳定地摇晃。当你推进 JavaScript 时，你真的可以给你的匹诺曹注入生命，把你从卑微的店员提升到网页设计大师！

但是，你的新作品是具有 t 台模特的优雅步伐，还是弗兰肯斯坦博士的怪物的缓慢步态，既取决于它的 HTML 和 CSS 起源的质量，也取决于赋予它生命的 JavaScript 代码的质量。

因此，在我们学习创造奇迹之前，让我们花一点时间来回顾如何构建一个内外都好看的网站，并看看 JavaScript 是如何融入其中的。

**把他们分开**

不久前，专业的网页设计师会兴高采烈地将 HTML、CSS 和 JavaScript 代码堆在一个文件中，命名为`index.html`(或者`default.htm`，如果他们被微软洗脑了的话)，称之为网页。我将此概念化为类似图 1 的东西。今天你仍然可以这样做，但是要准备好让你的同伴称之为不太礼貌的事情。

![Figure 1\. A single-file mess](img/f9406e93c04a2e6b16112e235fe369a5.png)

在这一过程中，网页设计者意识到他们编写的代码在制作网页时做了三件基本的事情:

*   它描述了页面的*内容*。
*   它指定了该内容的*呈现方式*。
*   它控制该内容的*行为*。

他们还意识到，将这三种类型的代码分开，如图 2“关注点分离”所示，使他们的工作更容易，并帮助他们制作在不利条件下更好工作的网页，例如当用户在浏览器中禁用 JavaScript 时。

计算机极客们知道这个已经很多年了，他们甚至给这个原则起了一个极客的名字:关注点分离。

![Figure 2\. The separation of concerns](img/69efa55fac4dce1e84eb228a547a5c04.png)

现在，认识到这一点是一回事，但实际上*做*是另一回事——尤其是如果你不是一个电脑极客的话。我*是*一个电脑怪胎，我总是想做错事。

我会很高兴地编辑描述网页内容的 HTML 代码，突然我会发现自己在想，如果文本的灰色稍微有些不同，如果它稍微向左移动一点，如果它的背景中有我在上次 SitePoint office party 上制作的我的面部照片，那该有多好。尽管我很容易分心，但我想马上做出改变。现在哪个更容易:打开一个单独的 CSS 文件来修改页面的样式表，还是只需将这些样式属性输入到我正在编辑的 HTML 代码中？

就像你在工作中的行为一样，保持你编写的代码类型的独立性需要自律。但是一旦你明白了这些好处，你也将能够鼓起坚持下去的意志力。

**三层**

在任何类型的编程中，尽可能将不同类型的代码分开是一个好主意。这使得在未来的项目中重用部分代码变得更加容易，减少了您最终编写的重复代码的数量，并且使得在数月或数年后发现和修复问题变得更加容易。

说到 Web，还有一个理由让你的代码保持独立:它可以让你迎合人们访问网页的不同方式。

根据您的受众，您的大多数访问者可能会使用配备齐全的桌面浏览器，支持最先进的 CSS 和 JavaScript，但许多人可能会受到公司 IT 政策的限制，这些政策迫使他们使用较旧的浏览器，或在禁用某些功能(如 JavaScript)的情况下浏览。

视力受损的用户经常使用屏幕阅读器或屏幕放大镜软件浏览，对于这些用户来说，你流畅的视觉设计可能是一个障碍而不是一个帮助。

一些用户甚至不会*访问*你的网站，更喜欢阅读 RSS 或类似格式的内容提要，如果你提供的话。当需要构建这些提要时，您会希望能够将您的 HTML 内容发送给这些用户，而不包含任何 JavaScript 或 CSS 垃圾。

让尽可能多的访问者访问你的网站的关键是从*三层*的角度来考虑 Web，这很方便地对应于我前面提到的三种代码。这些层如图 3“网络的三层”所示

![Figure 3\. The three layers of the Web](img/bff470b76038d2c6d67f6dc4ecf62389.png)

当建立一个站点时，我们自下而上地处理这些层:

1.  我们从生成 HTML 格式的内容开始。这是基础层，任何使用任何浏览器的访问者都应该能够看到。

*   完成后，我们可以专注于让网站看起来更好，通过使用 CSS 添加一层*表示*信息。对于能够显示 CSS 样式的用户来说，网站现在看起来很好。*   最后，我们可以使用 JavaScript 引入一个额外的交互层和动态行为层，这将使网站在装有 JavaScript 的浏览器中更容易使用。

如果我们将 HTML、CSS 和 JavaScript 代码分开，我们会发现，在表示层和/或行为层无法工作的浏览环境中，确保内容层保持可读要容易得多。这种“从底层开始”的网页设计方法在业内被称为*渐进增强*。

让我们孤立地看看每一层，看看我们如何最好地维护这种代码分离。

*内容的 HTML】*

阅读和理解网页内容所需的一切都属于该页面的 HTML 代码——不多也不少。就这么简单。当 Web 设计者忘记了 K.I.S.S .原则(保持简单，愚蠢)并将非内容信息塞进他们的 HTML 代码，或者将页面的一些内容移入页面的 CSS 或 JavaScript 代码时，他们就会陷入麻烦。

填充到页面中的非内容信息的一个常见例子是*表示性 HTML* —描述内容在浏览器中显示时应该如何*的 HTML 代码。这可以包括老式的 HTML 标签，如`<b>`、`<i>`、`<u>`、`<tt>`和`<font>`:*

```
<p>Whatever you do, <a href="666.html">*<font color="red"*>don't

  click this link*</font>*</a>!</p>
```

它可以采用应用了 style 属性的内联 CSS 的形式:

```
<p>Whatever you do, <a href="666.html" *style="color: red;"*>don't

  click this link</a>!</p>
```

它也可能包括许多善意的 web 设计者的秘密耻辱:CSS 样式应用了表示性的类名:

```
<p>Whatever you do, <a href="666.html" *class="red"*>don't click

  this link</a>!</p>
```

***表象类名称？**如果最后一个例子对你来说没问题，你并不孤单，但它绝对是坏运气。如果您后来决定将该链接设为黄色，您要么需要更新类名和应用于该链接的 CSS 样式，要么需要忍受名为“red”的类实际上是黄色样式的尴尬。那会*让你的脸变黄——呃，变红！

你应该关注动作的*原因*,而不是在 HTML 代码中嵌入表示信息——例如，你希望一个链接以不同的颜色显示。链接是不是特别重要？考虑用一个标签来描述您想要强调的重点:

```
<p>Whatever you do, <*em*><a href="evil.html">don't click this

  link</a></*em*>!</p>
```

链接是警告吗？HTML 没有描述警告的标签，但是您可以选择一个传达此信息的 CSS 类名:

```
<p>Whatever you do, <a href="evil.html" *class="warning"*>don't

  click this link</a>!</p>
```

当然，这种方法可能会走得太远。一些设计者将类似

# 的标签误认为是表示性的，并试图从他们的 HTML 中删除这些表示性的代码:

```
<p class="heading">A heading with an identity crisis</p>
```

实际上，应该在文档中保留的表示信息是显示标题的字体、大小和颜色。一段文本是标题*的事实是内容的一部分，因此应该反映在 HTML 代码中。所以这段代码非常好:*

```
<h1>A heading at peace with itself</h1>
```

简而言之，你的 HTML 应该尽一切可能传达页面内容的含义，或者说语义，同时避免描述它应该是什么样子。网络标准极客称做这种事情的 HTML 代码为“T2”语义标记(T3)。

编写语义标记允许 HTML 文件作为有意义的文档独立存在。无论出于什么原因，人们不能通过在典型的桌面网络浏览器中查看这些文档来阅读它们，这样可以更好地理解它们。例如，视力受损的用户将能够使用辅助软件，如屏幕阅读器，在大声朗读页面时听它，并且你的 HTML 代码越清楚地描述内容的含义，这些工具就越能理解它。

然而，最棒的是，语义标记让您可以应用新的样式(表示)和交互特性(行为),而不必做出许多(或者，在某些情况下，任何！)更改您的 HTML 代码。

*用于演示的 CSS*

显然，如果页面的内容应该完全包含在其 HTML 代码中，那么它的样式(或表示)应该在应用于页面的 CSS 代码中得到充分描述。

你已经做了很多工作来保持你的 HTML 没有表示性的代码和丰富的语义，如果用 CSS 的片段来填充这个文件，那就太糟糕了。

您可能知道，CSS 样式可以通过三种方式应用到您的页面:

**内联样式:**
`<a href="evil.html" *style="color: red;"*>`
内联样式很有吸引力，原因我在前面解释过:你可以在创建内容时将样式应用于内容，而不必切换和编辑单独的样式表。但是正如我们在上一节中看到的，如果你想让你的 HTML 代码对那些看不到样式的人有意义，你就要避免像瘟疫一样的内联样式。

**嵌入样式:**
`<style type="text/css">
*.warning {
color: red;
}*
</style>
&#8942;
<a href="evil.html" *class="warning"*>`
嵌入样式使您的标记保持整洁，但是将您的样式绑定到单个文档。在大多数情况下，你会希望在网站的多个页面上共享你的风格，所以最好也避开这种方法。

**外在风格:**
`<link rel="stylesheet" href="styles.css" />
&#8942;
<a href="evil.html" *class="warning"*>
**Example 1.1.** styles.css`

```
*.warning {

  color: red;

}*

External styles are really the way to go, because they let you share your styles between multiple documents, they reduce the amount of code browsers need to download, and they also let you modify the look of your site without having to get your hands dirty editing HTML.
```

但这些你都知道，对吧？毕竟，我们正在处理一本 JavaScript 书，所以让我们来谈谈进入页面的 JavaScript。

*行为的 JavaScript】*

与 CSS 一样，您可以通过多种方式将 JavaScript 添加到您的网页中。

你可以直接在你的 HTML 内容中嵌入 JavaScript 代码:
`<a href="evil.html" *onclick="JavaScript code here"*>`

您可以在 HTML 文档顶部的一个

***CDATA？**如果你想知道在`<script>`标签之后和`</script>`标签之前的所有官样文章是什么，这是合法地在 XHTML 文档中嵌入 JavaScript 而不混淆不理解 XHTML 的 web 浏览器(如 Internet Explorer)所需要的。*

如果你用 HTML 而不是 XHTML 来编写你的页面，你可以用这个简单得多的语法:
`<script type="text/javascript">
JavaScript code here
</script>`

您可以将 JavaScript 代码放在一个单独的文件中，然后从任意多个 HTML 文档中链接到该文件:
`<script type="text/javascript" src="script.js"></script>
&#8942;
<a href="evil.html" *class="warning"*>
**Example 1.2.** script.js **(excerpt)**
*JavaScript code here*`

猜猜你应该用哪种方法。

编写增强可用性的 JavaScript 代码，而不弄乱它所应用的 HTML 文档，不锁定在浏览器中禁用 JavaScript 的用户，并且不干扰可能应用于同一页面的其他 JavaScript 代码，这被称为*非介入式脚本*。

不幸的是，尽管许多专业的 web 开发人员已经意识到将 CSS 代码保存在单独的文件中的好处，但是仍然有大量的 JavaScript 代码混杂在 HTML 中。通过向你展示使用 JavaScript 的正确方法，我们希望能改变这种情况。

**正确的方式**

那么，这些东西到底有多重要呢？毕竟，多年来人们一直在混合使用 HTML、CSS 和 JavaScript 来构建网站，对于大多数浏览网页的人来说，这些网站都是有效的。

嗯，当你开始学习 JavaScript 时，掌握正确的语言比以往任何时候都更重要。到目前为止，JavaScript 是你用来设计网站的三种语言中最强大的，因此它给了你前所未有的自由去彻底搞乱事情。

举个例子，如果你真的真的喜欢 JavaScript，你甚至可以把所有东西——内容、表示和行为——都放到你的 JavaScript 代码中。我实际上已经看到这样做了，它并不漂亮——尤其是当禁用 JavaScript 的浏览器出现时。

更能说明问题的是，JavaScript 是这三种语言中唯一一种能够挂起浏览器，使其对用户无响应的语言。

因此，我们将尽最大努力向您展示使用 JavaScript 的正确方法，不仅仅是因为它使您的代码保持整洁，还因为它有助于保持 web 的正常运行——使尽可能多的人可以访问内容，而不管他们选择使用哪种 Web 浏览器。

**JavaScript 库**

正如我所提到的，将不同种类的代码分开的好处之一是，它使得您为一个站点编写的代码更容易在另一个站点上重用。某些 JavaScript 狂热者(从现在开始被称为“人”)花时间收集了大量有用的、不引人注目的 JavaScript 代码库，你可以在自己的网站上免费下载和使用。

贯穿 [*简单的 Javascript*](https://www.sitepoint.com/books/javascript1/) ，我们从头开始构建每个例子——你需要的所有 Javascript 代码都可以在那里找到。然而，由于在现实世界中并不总是有时间这样做，而且因为库正迅速成为 JavaScript 领域的重要组成部分，我们也将看看流行的 JavaScript 库是如何在机会出现时做事情的。

以下是我们在书中使用的库:

*   [原型](http://www.prototypejs.org/)
*   [script.aculo.us](http://script.aculo.us/)
*   雅虎！用户界面库(YUI)
*   [道场](http://dojotoolkit.org/)
*   [jQuery](http://jquery.com/)
*   [MooTools](http://mootools.net/)

*有很多免费代码，但并不是所有的都是好的。一般来说，好的库以 JavaScript ( `.js`)文件的形式出现，你可以不引人注目地链接到你的页面，而不是直接将 JavaScript 粘贴到你的 HTML 代码中。如果您没有信心判断某个 JavaScript 库是好是坏，可以在 SitePoint 论坛上寻求一些建议，或者坚持使用我刚刚提到的库——它们都非常好。*

**我们开始吧！**

说教到此为止——您阅读这篇文章是为了学习 JavaScript，对吗？干净的 HTML 和 CSS 固然很好，但是是时候投入到 Web 的第三层了:行为。这篇文章的下一章 [*只是 Javascript*](https://www.sitepoint.com/books/javascript1/) 更深入地讨论了这个话题——因为我们觉得很慷慨，我们决定把它们扔进去让你开心！所以不要忘记[下载 PDF 格式的本章](https://www.sitepoint.com/show-modal-popup-after-time-delay/)，以及第 2 章:用 JavaScript 编程和第 3 章:文档访问。

在第二章中，您将了解到，由于 JavaScript 是一种编程语言，您必须了解计算机程序的工作方式，这在某种程度上意味着学习像计算机一样思考，以便最有效地使用它。本章介绍的简单概念——语句、变量、表达式、循环、函数和对象——是您编写的每一个 JavaScript 程序的基础。

第三章呢？虽然某些人喜欢为编写 JavaScript 代码而编写代码，但你不会想在晚上的黑暗小巷中碰到他们。作为一名训练有素的 web 开发人员，您可能希望使用 JavaScript 通过文档对象模型(DOM)来修改 web 页面的内容。你很幸运，我们写了整整一章来告诉你怎么做！

然后，当然，[还有更多…](https://www.sitepoint.com/books/javascript1/toc.php)

准备好开始使用一些很酷(并且不显眼)的 JavaScript 吧！

## 分享这篇文章