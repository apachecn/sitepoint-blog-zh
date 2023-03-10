# 将组件化引入 Web:概述

> 原文：<https://www.sitepoint.com/bringing-componentization-web-overview-web-components/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

Microsoft Edge 开发人员建议框上五个最受欢迎的功能中有四个属于称为 Web 组件的功能家族——阴影 DOM、模板、自定义元素和 HTML 导入。在本帖中，我们将讨论 Web 组件，并给出我们的观点，为那些可能不太熟悉它们的人提供一些背景知识，并推测一下我们对它们未来发展的预期。要做到公正，需要一点时间，所以坐下来，喝杯咖啡(或不含咖啡因的饮料)，继续读下去。

## 组件化:一个古老的设计实践为 web 带来了新的变化

Web 应用程序现在和任何其他软件应用程序一样复杂，并且经常需要许多人协作来生产发布的产品。为了更有效率，找到正确的方法来划分开发工作，使人和系统之间的重叠最小是很重要的。组件化(一般来说)就是这样做的。任何组件系统都应该通过提供**隔离**或一个天然屏障来隐藏一个系统的复杂性，从而*降低*整体复杂性。良好的隔离还使得可重用性和可维护性更容易。

最初，web 应用程序的复杂性主要是在服务器上通过将应用程序隔离到单独的页面来管理的，每个页面都需要用户在浏览器中逐页导航。随着 AJAX 和相关功能的引入，开发人员不再需要在 web 应用程序的不同页面之间“导航”。对于一些常见的场景，如阅读电子邮件或新闻，预期已经发生了变化。例如，在登录你的电子邮件后，你可能从一个 URL“运行电子邮件应用程序”，并整天停留在那个页面上(又名[单页应用程序](https://en.wikipedia.org/wiki/Single-page_application))。客户端 web 应用程序逻辑可能要复杂得多，甚至可能与服务器端的逻辑不相上下。帮助解决这种复杂性的一个可能的解决方案是在单个网页或文档中进一步组件化和隔离逻辑。

web 组件的目标是通过隔离一组相关的 HTML、CSS 和 JavaScript 来降低复杂性，以便在单个页面的上下文中执行一个公共功能*。*

## 如何组件化？

因为 web 组件必须集合 HTML、CSS 和 JavaScript 中的每一种，所以每种技术支持的现有隔离模型有助于在整个 web 组件中保留重要的场景。这些独立的隔离模型包括(在以下段落中有更详细的描述):

*   [CSS 样式隔离](http://blogs.windows.com/msedgedev/2015/07/14/bringing-componentization-to-the-web-an-overview-of-web-components/?WT.mc_id=16519-DEV-sitepoint-article55/#css-style-isolation)
*   [JavaScript 和作用域(闭包)](http://blogs.windows.com/msedgedev/2015/07/14/bringing-componentization-to-the-web-an-overview-of-web-components/?WT.mc_id=16519-DEV-sitepoint-article55/#javascript-and-scopes)
*   [全局对象隔离](http://blogs.windows.com/msedgedev/2015/07/14/bringing-componentization-to-the-web-an-overview-of-web-components/?WT.mc_id=16519-DEV-sitepoint-article55/#global-object-isolation)
*   [元素封装(iframe)](http://blogs.windows.com/msedgedev/2015/07/14/bringing-componentization-to-the-web-an-overview-of-web-components/?WT.mc_id=16519-DEV-sitepoint-article55/#element-encapsulation)

### CSS 样式隔离

目前还没有很好的方式在平台中本地组件化 CSS(尽管像 [Sass](http://sass-lang.com/) 这样的工具肯定会有所帮助)。一个组件模型必须支持将一组 CSS 与另一组 CSS 隔离的方法，这样一组 CSS 中定义的规则就不会干扰另一组。此外，组件样式应该只应用于组件的必要部分，而不是其他部分。说起来容易做起来难！

在样式表中，使用选择器将 CSS 样式应用于文档。选择器总是被认为对整个文档具有潜在的适用性，因此它们的范围基本上是全局的。当一个项目的许多贡献者将他们的 CSS 文件放在一起时，这种全球性的影响会导致真正的冲突。重叠和重复的选择器在解决冲突时有既定的优先顺序(例如，级联、特异性和源顺序)，但是出现的行为可能不是开发人员想要的。这个问题有许多潜在的解决方案。一个简单的解决方案是将参与组件的元素和相关样式从主文档移动到另一个文档(影子文档),这样它们就不再是选择器匹配的候选对象。这就产生了第二个问题:既然已经建立了边界，那么一种风格如何跨越边界呢？这在 JavaScript 中显然是可行的，但是依靠 JavaScript 来跨越边界调节样式似乎是很尴尬的，因为这似乎是 CSS 中的一个缺口。

为了跨组件边界有效地传输样式，并保护组件的结构(例如，允许在不破坏样式的情况下自由地修改结构)，有两种通用的方法可以达成共识:使用定制伪元素的“部件”样式和使用定制属性的(以前称为 CSS“变量”)。有一段时间，超级强大的*跨界选择器组合器* ' > > >'也被考虑过(在 [CSS 范围](http://dev.w3.org/csswg/css-scoping/#deep-combinator)中指定)，但这现在被普遍认为是一个坏主意，因为它太容易破坏组件隔离。

部件样式化将允许组件作者创建定制的伪元素进行样式化，从而只向外界公开其内部结构的一部分。这类似于浏览器用来暴露其本地控件的[“部分】的模型。为了完成这个场景，作者可能需要某种方法来限制可以应用于伪元素的样式集。对基于伪元素的“部件模型”的进一步探索可能会产生一个有用的样式原语，尽管细节还需要解决。部件模型中的进一步工作也应该使](https://msdn.microsoft.com/en-us/library/windows/apps/hh767361.aspx/?WT.mc_id=16519-DEV-sitepoint-article55)[浏览器内置的本地控件样式](http://dev.w3.org/csswg/css-forms/)(一个迫切需要关注的领域)合理化。

[定制属性](https://www.w3.org/TR/css-variables/)允许作者描述他们希望在样式表中重用的样式*值*(定义为以双破折号为前缀的定制属性名)。自定义属性通过文档的子树继承，允许选择器覆盖给定子树的自定义属性的值，而不影响其他子树。自定义属性名也能够跨组件边界继承，为组件提供一个优雅的样式机制，避免暴露组件的结构性质。各种 Google 组件框架已经对定制属性进行了评估，并且据报道可以满足大多数样式需求。

在迄今为止考虑的所有样式方法中，未来的“部件”模型和当前的自定义属性规范似乎具有最积极的势头。我们认为自定义属性是 web 组件规范家族中一个新的重要成员。

### 其他 CSS 风格的隔离方法

就完整性而言，CSS 的范围和隔离并不像上面假设的那样非黑即白。事实上，过去和现在的一些提议提供了范围和隔离的好处，对 web 组件的适用性各不相同。

CSS 为特定场景提供了一些有限形式的选择器隔离。例如，`@media`规则将一组选择器组合在一起，并在满足媒体条件时有条件地应用它们(如视窗的大小/尺寸，或媒体类型，如打印)；`@page`规则定义了一些只适用于打印条件的样式(分页介质)； [`@supports`规则](http://dev.w3.org/csswg/css-conditional/)将选择器收集在一起，仅当实现*支持*特定的 CSS 特性——CSS 特性检测的新形式时才适用)；对于 [`@document`](https://developer.mozilla.org/en-US/docs/Web/CSS/@document) 的建议是将选择器组合在一起，仅当加载样式表的文档与规则匹配时才应用。

[CSS 作用域](http://dev.w3.org/csswg/css-scoping/#scope)特性(最初作为 web 组件工作的一部分形成)是一个在单个 HTML 文档中限制 CSS 选择器适用性的提议。它定义了一个新的规则`@scope`，该规则使选择器能够识别*作用域根*，然后使得包含在`@scope`规则中的所有选择器的评估仅对该根具有子树范围的适用性(而不是文档范围的适用性)。该规范允许 HTML 声明性地定义作用域根(例如，提议的`<style scoped>`属性，目前只有 Firefox 实现；该功能之前在 Chrome 中作为一项自愿实验提供，但后来被完全删除了。特性的各个方面(即:在[选择器 L4](http://dev.w3.org/csswg/selectors/#scope-element) 中定义的范围)也将用于 [DOM 规范的新查询 API](https://dom.spec.whatwg.org/#dom-parentnode-query) 中的*相对选择器*评估。)

需要注意的是，`@scope`只建立了一个*单向*隔离边界:包含在`@scope`内的选择器被限制在作用域内，而任何其他选择器(在`@scope`之外)仍然可以在`@scope`内自由选择(尽管级联可能对它们进行不同的排序)。这是一个不幸的设计，因为它没有为`@scope`子集中*非*的任何选择器提供作用域/隔离——为了避免在另一个`@scope`规则内的意外样式，所有 CSS 都必须“玩得好”。参见[选项卡的@in-shadow-of sketch](http://lists.w3.org/Archives/Public/public-webapps/2015JanMar/0127.html) ，它与保护元件隔离的模型更加一致。

另一种提议的作用域形式是 [CSS 包容](http://dev.w3.org/csswg/css-containment/)。包容范围不是关于样式/选择器隔离，而是关于“布局”隔离。使用“包含”属性，某些具有自然继承性的 CSS 功能的行为(就文档中从父元素到子元素的适用性而言，例如计数器)将被阻止。主要的用例是让开发人员指出某些元素具有强大的包容承诺，这样适用于该元素及其子树的布局就不会影响文档其他部分的布局。这些包含承诺(通过使用“contain”属性来实施)允许浏览器优化布局和呈现，使得所包含的子树中的“脏”布局只需要更新该子树的布局，而不是整个文档。

随着跨浏览器供应商的 web 组件技术的实现变得成熟并得到越来越多的公众使用，可能会出现更多的样式模式和问题；我们应该期待看到进一步的投资和各种 CSS 提案取得更多的进展，从而改进 web 组件的样式。

### JavaScript 和作用域

所有包含在网页中的 JavaScript 都可以访问同一个共享的全局对象。像任何编程语言一样，JavaScript 的作用域为函数代码提供了一定程度的“隐私”。这些词法范围用于将变量和函数与全局环境的其余部分隔离开来。如今流行的 JavaScript“模块模式”(使用词法作用域)是从需要多个 JavaScript 框架在单个全局环境中“共存”而不“践踏”彼此(取决于加载顺序)发展而来的。

JavaScript 中的词法范围是一个单向隔离边界——范围内的代码可以访问该范围的内容以及直到全局范围的任何祖先范围，而范围外的代码不能访问该范围的内容。*重要的原则*是单向隔离有利于范围内的代码，保护它。词法范围内的代码可以选择对环境的其余部分保护/隐藏它的代码(或者不保护)。

JavaScript 的词法作用域对 web 组件的贡献是要求有一种“关闭”组件的方式，这样它的内容就可以变得相当私密。

### 全局对象隔离

如上所述，一些代码可能不想共享对全局环境的访问。例如，一些 JavaScript 代码可能不被应用程序开发人员信任——但它提供了一个至关重要的价值。广告和广告框架就是这样的例子。为了保证 JavaScript 的安全性，需要在一个独立、干净的脚本环境中运行不受信任的代码(一个有自己唯一的全局对象的环境)。开发人员也可能更喜欢一个全新的全局对象，在其中进行编码，而不需要考虑其他脚本。今天为了做到这一点(不借助 iframe 元素)，开发人员可以使用一个 [Worker](https://www.w3.org/TR/workers/) 。Workers 的缺点是它们不提供对元素的访问，因此也不提供对 UI 的访问。

当设计一个支持全局对象隔离的组件时，有很多需要考虑的问题——特别是当这种隔离将启用一个安全边界时(下面将详细介绍)。在最初的一组 web 组件规范被锁定(即“为 v2 保存”)之前，孤立的组件不会被完全开发。然而，现在花一些时间来展望一下隔离元件可能是什么样子，将有助于为今天正在进行的一些工作提供信息。已经提出了几个建议，值得研究。

全局对象隔离填补了 web 组件的一个重要缺失场景。与此同时，我们必须依赖当今 web 上最成功和最广泛部署的组件化形式:iframe 元素。

### 元素封装(iframe)

Iframe 元素及其相关的表亲:对象元素、框架集和命令式的`window.open()` API 已经提供了托管独立元素子树的能力。不像在单个文档内部运行的组件，iframes 将整个 HTML 文档连接在一起；就好像两个独立的 web 应用程序位于同一位置，一个在另一个的内部。每个都有唯一的文档 URL、全局脚本环境和 CSS 范围；每个文档都是完全独立的。

iframes 是当今 web 上最成功的(也是唯一广泛部署的)组件化形式。Iframes 使不同的 web 应用程序能够协作。例如，许多网站使用 iframes 作为一种组件形式来实现从广告到联合身份登录的所有功能。Iframes 有一系列挑战和应对这些挑战的方法:

一个 HTML 文档中的 JavaScript 代码可能会侵入另一个文档的隔离边界(例如，通过 iframe 的 contentWindow 属性)。这种突破 iframe 隔离边界的能力可能是一个必需的功能，但当 iframe 中的内容包含不打算共享的敏感信息时，这也是一个安全风险。如今，[同源策略](http://en.wikipedia.org/wiki/Same-origin_policy)减轻了不必要的违规行为:默认情况下，来自同一来源的文档 URL 被允许违规，而跨来源的文档相互通信的能力有限。

单独入侵并不是唯一的安全风险。 [`<iframe sandbox>`属性](https://www.w3.org/TR/html5/embedded-content-0.html#attr-iframe-sandbox)的使用对跨源 iframe 提供了进一步的限制，以保护主机免受不需要的脚本、弹出窗口、导航和其他在框架中可用的功能的影响。

框架文档外部的 CSS 样式无法应用于内部的文档。这种设计保留了隔离原则。然而，当使用 iframe 作为一个组件时，风格隔离在 iframe 的集成中产生了一个明显的*接缝*(在同源中)。HTML 通过为同源 iframes 提议的[属性](https://www.w3.org/html/wg/drafts/html/master/single-page.html#attr-iframe-seamless)解决了这个问题。无缝属性的使用消除了框架内容的样式隔离；无缝框架文档复制其宿主文档的样式，并呈现为不受其宿主框架元素的限制。

有了良好的安全策略和无缝框架特性，使用 iframe 作为组件模型似乎是一个非常有吸引力的解决方案。然而，web 组件模型缺少一些理想的属性:

深度融合。Iframes 限制(并且大部分完全停止)宿主和框架文档之间的集成和交互模型。例如，相对于宿主，焦点和选择模型是独立的，事件传播被隔离到一个或另一个文档。对于想要更紧密集成的组件，如果没有宿主文档中的“代理”来帮助跨越边界，支持这些行为是不可能的。

全球对象的扩散。对于在页面上创建的每个 iframe 实例，也总是会创建一个唯一的全局对象。创建全局对象及其关联的完整类型系统并不便宜，最终会消耗大量内存和浏览器中的额外开销。在一个页面上使用的同一组件的多个副本可能不需要彼此完全隔离，事实上共享一个全局对象是更可取的，尤其是在需要公共共享状态的情况下。

宿主内容模型分发。Iframes 目前不允许在框架文档中重用宿主元素的内容模型。(简单地说:元素的*内容模型*是它支持的元素和文本的子树。)例如，select 元素有一个包含 option 元素的内容模型。作为 web 组件实现的 select 元素也希望以类似的方式使用这些相同的子元素。

选择性造型。无缝 iframe 不适用于跨来源文档。如果允许这种情况发生，会有微妙的安全风险。主要问题是“无缝”是由主机控制的，而不是框架文档(框架文档更多时候是相关攻击的受害者)。对于一个组件来说，二进制的“无缝”特性可能过于极端；组件可能希望更有选择性地决定宿主的哪些样式应该适用于其内容(而不是自动继承所有样式，即无缝如何工作)。**一般来说，什么风格的问题应该属于组件。**

API 暴露。web 组件的许多场景都涉及到创建全功能的新“定制”元素，这些元素具有自己公开的 API 表面、呈现语义和生命周期管理。使用 iframe 限制了开发人员将 iframe 的 API 表面作为基线，以及随之而来的所有假设。例如，元素的身份及其生命周期语义是不可更改的。

### 不是第一次了

值得注意的是，为了改进 HTML 的 iframe 和相关的封装特性，已经提出并实现了几种过去的技术。这些都没有以任何有意义的方式存在于今天的公共网络上:

[HTML Components](https://www.w3.org/TR/NOTE-HTMLComponents) (1998)是微软从 IE5.5 开始提出并实现的(在 IE10 已经过时)。它使用声明性模型将事件和 API 附加到主机元素(考虑到隔离)，并将组件解析到一个“视图链接”(一个“影子 DOM”)。两种风格的组件是可能的，一种永久地附加到一个元素，另一种通过 CSS“行为”属性动态绑定。

XBL (2001)和它的继任者 [XBL2](https://www.w3.org/TR/xbl/) (2007)由 Mozilla 提出，作为他们 XUL 用户界面语言的伴侣。一种具有两种绑定风格的声明性绑定语言(类似于微软的 HTML 组件)，XBL 支持主机内容模型分发和内容生成的附加功能。

## 当今的 Web 组件

在前两次发布失败后，又到了新一轮组件提案的时候了，这次是由谷歌牵头的。以 XBL 描述的概念为起点，单片组件系统被分解成组件构建块的集合。这些构件允许 web 开发人员在 web 组件的整体愿景完全实现之前，尝试一些有用的独立特性。正是这种组件化和独立有用特性的开发促成了它的成功。几乎每个人都能找到一些对他们的应用有用的 web 组件！

这种新的 web 组件渴望满足一组[定义的用例](https://www.w3.org/2008/webapps/wiki/Component_Model_Use_Cases)，并通过解释现有的内置元素如何在当今的 web 平台中工作来实现这一点。理论上，web 组件允许开发人员构建新类型的 HTML 元素原型，具有与原生元素相同的保真度和特性(在*实践中*HTML 中的可访问性行为目前尤其难以匹配)。

很明显，交付所有 web 组件用例所必需的全套技术，起初不会在浏览器中全部可用。实现者们正在一起工作，以就核心技术集达成一致，在转移到其他用例之前，可以一致地实现这些核心技术集的细节。

“第一代”web 组件技术是:

*   **[自定义元素](http://w3c.github.io/webcomponents/spec/custom/)** :自定义元素为 HTML 解析器定义了一个扩展点，能够识别新的“自定义元素”名称，并自动为其提供 JavaScript 支持的对象模型。自定义元素不启用组件边界，而是为浏览器提供将 API 和行为附加到作者定义的元素的方法。不支持的浏览器可以使用变异观察器/事件和原型调整将自定义元素聚合填充到不同的精度级别。把握好时机并理解其中的含义是我们即将到来的会议的主题之一。
    *   **“是”属性**。隐藏在 Custom Elements 规范中的另一个重要特性是——指示内置元素应该被赋予自定义元素名称和 API 功能的能力。在正常情况下，定制元素作为通用元素开始；在“是”的情况下，可以使用原生元素代替(如`<input is="custom-input">`)。虽然这个特性是继承默认呈现、可访问性、解析等所有优点的好方法。，内置于特定的 HTML 元素中，它的语法在某种程度上被认为是一种欺骗，其他人想知道可访问性原语加上本机控件样式是否是更好的长期标准化路线。
*   **[Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/)** :提供一个命令式 API，用于创建一个单独的元素树，该元素树可以(仅一次)连接到一个主机元素。在呈现文档时，这些“影子”子代会替换“真实”子代。Shadow DOM 还使用新的 *slot* 元素(最近提出)、事件目标修正和关闭/打开操作模式(也是最近采用的)，提供了主机元素内容模型的重用。这个相对琐碎的想法对从焦点模型和选择到合成和分布(对于阴影穹顶内的阴影穹顶)的一切都有惊人的大量副作用。
    *   **[CSS 作用域](http://dev.w3.org/csswg/css-scoping/)** 定义了与阴影 DOM 样式相关的各种伪元素，包括`:host`、`::content`(即将出现的`::slot`？？)，以及现在正式否定的前'> > >'(暗影 dom 穿刺组合器)。
*   **[模板元素](https://www.w3.org/TR/html5/scripting-1.html#the-template-element)** :为了完整性，这个早期的 web 组件特性现在是 [HTML5 推荐标准](https://www.w3.org/TR/html5/)的一部分。template 元素引入了惰性的概念(template 的子元素不触发下载或响应用户输入，等等。)并且是在 HTML 中以声明方式创建断开连接的元素子树的第一种方式。模板可以用于多种用途，从模板标记和数据绑定到传递影子 DOM 的内容。
*   **[HTML 导入](http://w3c.github.io/webcomponents/spec/imports/)** :定义一个声明性语法，将 HTML“导入”(请求、下载和解析)到一个文档中。导入(使用链接元素的`rel="import"`)在宿主页面的上下文中执行导入文档的脚本(因此可以访问相同的全局对象和状态)。web 组件的 HTML、JavaScript 和 CSS 部分可以使用单个导入方便地部署。
*   **[自定义属性](https://www.w3.org/TR/css-variables/)** :如前所述，在组件外部描述的自定义属性可以在组件内部使用，这是目前设计组件样式的一种简单而有用的模型。鉴于此，我们将自定义属性作为第一代 web 组件技术的一部分。

## Web 组件:下一代

正如本文开头所提到的，构建 web 组件的功能是一个旅程。一些扩展和填补当前一代功能中的空白的想法已经开始流传(这不是一个完整的索引):

*   **声明式阴影 DOM** 。当考虑如何以序列化形式重新传递组件时，声明性影子 DOM 变得很重要。如果没有声明形式，innerHTML 或 XMLSerializer 等技术就不能构建包含任何阴影内容的 DOM 的字符串表示。因此，如果没有脚本的帮助，阴影穹顶是不可往返的。来自 Mozilla 的 Anne 提出了一个`<shadowdom>` 元素作为稻草人提案。类似地，template 元素已经是一种用于构建某种形式的影子标记的声明性方法，浏览器中的序列化技术已经进行了调整，以解决这一问题并相应地序列化模板“影子”内容。
*   **完全隔离的组件**。[三家](http://lists.w3.org/Archives/Public/public-webapps/2015AprJun/0052.html) [浏览器](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Isolated-Imports-Proposal.md) [厂商](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Cross-Origin-Custom-Elements:-Concept-and-Proposal.md)在这个领域提出了三种不同的方案。这三项提案已经相当一致，从建立共识的角度来看，这是一个好消息。如前所述，隔离的组件将使用一个新的全局对象，并可能从跨原点导入。他们还会有一个合理的模型来呈现 API 和跨越隔离边界的相关行为。
*   **可访问性原语**。可访问性社区中的许多人喜欢将“is”(来自自定义元素)作为扩展现有原生元素的方式，因为原生元素具有 JavaScript 开发人员通常不具备的可访问性行为。理想情况下，一个通用的 web 组件(不使用“is”)可以像本地元素一样紧密地集成在可访问性方面，包括表单提交和聚焦能力等；这些扩展点在今天是不可能的，但是应该被探索和定义。
*   **统一的原生控件样式**。浏览器之间缺乏一致的控件样式一直是 interop 的一个问题领域，阻碍了简单的“面向主题”扩展的广泛部署。这导致开发人员经常考虑将影子 DOM 作为替代解决方案(尽管模拟一个与原生控件具有相同行为保真度的影子 DOM 可能具有挑战性)。一些[控件风格的想法](http://dev.w3.org/csswg/css-forms/)已经在 CSS 中酝酿了一段时间，尽管由于缺乏动力，这些想法没有得到很快发展。
*   **CSS 零件造型**。虽然 CSS 自定义属性可以为当今的 web 组件提供广泛的样式选项，但在其他情况下，公开一些组件的封装框进行样式设置更直接或更合适。如果这也合理化了现有浏览器使用来暴露本地控件的部件样式的方法[，这将特别有用。](http://tjvantoll.com/2013/04/15/list-of-pseudo-elements-to-style-form-controls/)
*   **解析器定制**。当与自定义元素一起使用时，只有标准的开始/结束标记可用于自定义元素解析。可能需要[在解析器中允许额外的定制](https://github.com/w3c/webcomponents/issues/113)，以便 web 组件利用。

最后，虽然没有被官方认为是 web 组件的一部分，但古老的 iframe 不应该被忽视。如前所述，iframes 仍然是构建类似组件的 web 平台的一个非常有用的特性。理解并潜在地改进 iframes 的“组件”故事将是有用的。例如，用`<iframe seamless>`理解和解决未解决的问题似乎是一个很好的起点。

Web 组件是 web 向前迈出的变革性一步。我们很高兴能继续支持并为这一旅程做出贡献。明天，我们将分享更多关于我们的路线图和实施计划。同时，请在 Twitter 或下面的评论中分享您的反馈 [@MSEdgeDev](https://twitter.com/msedgedev) 。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16519-DEV-sitepoint-article55) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16519-DEV-sitepoint-article55)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools/?utm_source=SitePoint&utm_medium=article55&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article55&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article55&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article55&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=16519-DEV-sitepoint-article55)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=16519-DEV-sitepoint-article55)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/?WT.mc_id=16519-DEV-sitepoint-article55) 和 [babylonJS](http://babylonjs.com/?WT.mc_id=16519-DEV-sitepoint-article55) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations/?WT.mc_id=16519-DEV-sitepoint-article55)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifoldjs.com/?WT.mc_id=16519-DEV-sitepoint-article55) 项目)

更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=16519-DEV-sitepoint-article55) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16519-DEV-sitepoint-article55)

## 分享这篇文章