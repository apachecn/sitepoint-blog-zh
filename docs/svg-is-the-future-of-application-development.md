# SVG 是应用程序开发的未来

> 原文：<https://www.sitepoint.com/svg-is-the-future-of-application-development/>

随着最近谷歌 Chrome 浏览器的发布，我一直在思考网络不断增长的能力给我们带来的可能性。几乎所有关于新谷歌浏览器的东西，从每页进程沙箱到应用程序窗口 UI，[都叫嚣着“应用程序平台”](https://www.sitepoint.com/google-turns-its-back-on-firefox/ "Google makes no bones about promoting Chrome over Firefox, either.")其中一个关键因素是 Chrome 新的 [V8 JavaScript 引擎](https://code.google.com/p/v8/)，由于其执行速度，该引擎正迅速使该语言成为严肃计算的有力竞争者。

当然，谷歌人并不是第一个或唯一一个梦想在桌面上让基于网络的应用成为一等公民的人。还有 [Prism(前身是 WebRunner)](https://wiki.mozilla.org/Prism) ，这是一个 Mozilla 项目，它启动一个没有任何浏览器相关 chrome 的网页。还有[流体](https://fluidapp.com/)，它为 [Mac 的 WebKit 引擎](https://webkit.org/)做同样的事情。这些程序基本上都是云计算应用程序的瘦客户端，比如苹果的 MobileMe、谷歌的工具套件或近年来涌现的各种“Web 2.0”属性。

像 Adobe 和微软这样的公司也都加入了这股潮流。Adobe 的产品名为 AIR，让熟悉网络开发技术的开发者能够编写网络桌面应用，如 T2 MXML T3、ActionScript、Flash 或 HTML、CSS 和 JavaScript。微软有一个竞争产品叫做 [Silverlight](https://silverlight.net/) 。两者都是跨平台的，并且能够利用 Web 标准作为应用程序代码库的核心部分。

## (X)HTML/CSS 真的是未来的后记吗？

我的技术生涯始于 90 年代制作网页，因为那时 HTML 是最容易学习的“图形用户界面语言”。正如我们所知，在很长一段时间里，试图编写跨平台的基于浏览器的代码无异于噩梦。然而，随着 web 的稳定和[可靠的工程实践开始渗透到 web 开发社区](https://www.ibm.com/developerworks/web/library/wa-cranky76/)，HTML、CSS 和 JavaScript 的三驾马车无疑成为了这个星球上最广泛实现的 <acronym title="Graphical User Interface">GUI</acronym> 工具包。现在，它的无处不在和与实现无关的特性使得开发 web 应用程序如此吸引人，但是我们会不会从错误的角度来看待这个挑战呢？

HTML 是一种显示语言的概念很久以前就被证明是一件坏事。CSS 教会了我们把我们的表现和我们的内容分开，当我们这样做的时候打开的门是无价的。然而今天，我们仍然以 HTML 文档的形式交付 web 应用程序，并在顶层添加了一些脚本。这是可行的，但是被 HTML 的所有限制扼杀了。从根本上来说， **HTML 不是一种应用交付格式，它是一种内容描述语言**，尽管 HTML5 工作组做出了[的英勇努力。](https://www.sitepoint.com/w3c-markup-validation-service-adds-experimental-html5-support/)

## 进入网络图形的红头继子:SVG

我们的阴影在哪里？圆角仍然是 HTML 中需要做的事情。让动画和样式化的模态对话框轻松工作需要我们以库的形式将 JavaScript 抽象的[层](https://www.prototypejs.org/ "The Prototype JavaScript library; widely deployed and, sadly, also very necessary.")堆积在[层](https://script.aculo.us/ "Scrip.aculo.us is a JavaScript graphical effects library built on top of Prototype.")之上，而*仍然*我们不能做[GTK 和 OpenGL 可以在桌面应用](https://arstechnica.com/news.ars/post/20071217-bringing-more-bling-to-gtk-with-opengl.html)中为我们做的事情。

因此，如果 HTML 不能为我们提供帮助，还有什么可以呢？当然，微软希望我们使用 Silverlight，Adobe 希望我们使用 Flash 和 AIR。而苹果…？苹果表面上希望我们使用 HTML5 的`[canvas](https://www.whatwg.org/specs/web-apps/current-work/#the-canvas-element)`。微软和 Adobe 的竞争者都是专有的，这似乎是 web 开发人员在一定程度上避免使用它们的足够理由，并且它们都是模糊的 HTML，这对语义 web 来说是一件危险的事情。

但是苹果实际上有锦囊妙计。就像 Mozilla 一直在做的 Firefox 一样，苹果已经悄悄地在 WebKit 中实现了对 T2、SVG 和 T4 的更好支持，这是 W3C 推荐的基于 XML 的矢量图形。SVG 提供了与 Flash 相同的矢量图形功能，但是它利用了 XML 带来的所有互操作性优势。

SVG 非常适合以图形方式显示文本和图像，用声明性的视觉原语来操纵它们，并且它有许多令人满意的效果。具有讽刺意味的是，SVG 最初是由 Adobe 和 Sun Microsystems 联合开发的，但最近是 Sun 实验室在用这项技术做有趣的事情。这类实验中最引人注目的当属 [Sun 实验室的生动内核项目](https://research.sun.com/projects/lively/)。

## 给网络注入一些活力

![A screenshot of Sun Labs's Lively Kernel running in the Safari 3 web browser.](img/93b6afab11d11437e2d4f8881689c1ad.png)

尽管它没有像许多其他项目一样声称自己是一个“web 应用程序”或“T0”web 操作系统，但 Lively Kernel 是一个成熟的图形应用环境，甚至包括自己的 <acronym title="Integrated Development Environment">IDE</acronym> 。这让人想起了早期的 [SmallTalk](https://en.wikipedia.org/wiki/SmallTalk) 实现，毫无疑问这要归功于它的核心开发者之一[丹·英格尔斯](https://research.sun.com/people/mybio.php?c=394)的影响，他也是 SmallTalk 的联合创始人之一。更重要的是，Lively 内核完全是用 JavaScript 和 SVG 构建的。

该项目的页面说，它<q cite="https://research.sun.com/projects/lively/">特别强调将 web 应用程序视为真正的应用程序，而不是今天大多数 web 应用程序面向文档的本质。</q>对于 web 应用程序开发人员来说，这个项目最有趣的地方在于它完成了许多我们在 HTML 中无法完成的图形化工作(仅使用了 SVG 标准的一个子集*和*提供了与真正的 JavaScript 对象交互的能力，而不是 HTML DOM 节点。窗口是 JavaScript 窗口对象，滑块和按钮也是，当您更改按钮[的属性时，它的 GUI 表示会相应地更新](https://research.sun.com/projects/lively/tutorial/Tutorial12-OnTheFly.htm "The twelfth section of the Lively Kernel tutorial demonstrates on-the-fly source code changes to objects.")。

此外，由于 Lively 内核基于已发布的开放标准，它可以在任何支持所述标准的用户代理之间移植，包括 Firefox 3、Safari 和 Chrome。当 Mobile Safari 增加 SVG 支持时，Lively 内核可能也会在 iPhones 上运行。此外， [WebKit 是作为 KDE 项目](https://en.wikipedia.org/wiki/WebKit#Origins)的一部分正在开发的基础设施，它被用作[许多移动设备](https://en.wikipedia.org/wiki/WebKit#Ports)的基础(就像最近的诺基亚智能手机模型)，并且它已经搭载了许多游戏，[主要使用 SVG](https://techbase.kde.org/Projects/Games/Status#Status.2FRoadMap "View the KDE Games roadmap, which talks about a desire to move entirely to SVG-based artwork.") 作为它们的图形原语。

## 语义学呢？让我们一起吃蛋糕吧！

HTML 取得了巨大的成功，因为它非常擅长描述 Web 文档中的内容。因为 SVG 和 XHTML 都是 XML 的应用程序，所以我们可以在同一个文件中混合两者，或者从另一个文件中引用一个文件，这样就可以真正地获得两个世界的优点。这也正是 Lively Kernel 项目所提倡的，因为他们说<q cite="https://research.sun.com/projects/lively/">总的来说，我们的目标之一就是尽可能地利用现有的技术。</q>

“可点击的”图形与语义内容的结合正是我们多年来一直追求的。结合 HTML 和 CSS 构建的界面并不能提供漂亮的 GUI，但是这从来都不是该技术的设计目的，这种限制并不奇怪。另一方面，每个应用程序都有一些相关的数据，如果 Web“混搭”教会了我们一件事，那就是一个应用程序的数据在与另一个应用程序的数据结合时要有用得多。幸运的是，描述数据及其与其他数据的关系是 XHTML 和 RDFa 等标准 Web 技术的天然契合点。

描述我们真正在谈论的东西的一种方式是将“[语义桌面](https://en.wikipedia.org/wiki/Semantic_desktop)”与“[语义网](https://en.wikipedia.org/wiki/Semantic_Web)”联系起来在这方面，KDE 项目的人们也有一些有趣的东西向我们展示。KDE 的最新版本[在语义桌面](https://nepomuk.kde.org/)领域取得了一些引人入胜的进展，它提供了低级别的框架，统称为 [<acronym title="Networked Environment for Personalized, Ontology-based Management of Unified Knowledge">NEPOMUK</acronym>](https://nepomuk.semanticdesktop.org/) ，供应用程序相互通信，交换正在发生的事情的信息，并使应用程序能够智能地决定显示什么信息或如何补充用户的活动。就其本身而言，这一 Linux.com 专题引用了 Nepomuk 协调员 Ansgar Bernardi 的话说，这不是什么新鲜事。但是结合语义网可以打开的大规模分布式知识库，这样的语义桌面让我们离实现 Vannevar Bush 的 Memex 梦想又近了一步。

今天，有远见的网站已经在使用 SVG，并且有许多工具可以帮助简化它的实现。拉斐尔 JavaScript 库尤其值得注意，因为它能够将 SVG 输出到兼容的浏览器，将 <acronym title="Vector Markup Language">VML</acronym> 输出到 Internet Explorer。这使得前端开发人员能够以跨浏览器兼容的方式使用矢量图形，只需维护一个源代码。

随着这些 Web 标准的日益普及和可访问性，语义结构化数据与 SVG 等显示原语的结合显然是 Web 甚至语义桌面未来图形设计工具集的核心部分。

## 分享这篇文章