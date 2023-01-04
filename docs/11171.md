# 正确的心态:应用 CSS 框架的经验

> 原文：<https://www.sitepoint.com/applying-lessons-from-css-frameworks/>

近年来，web 设计和开发领域经历了一场变革。开发框架——无论是服务器端的(Rails、Django、CakePHP)还是客户端的 JavaScript (jQuery、Prototype)和 CSS (CSS 框架，如 Blueprint 和 960 Grid System)——都脱颖而出，有望提高生产力水平，减少常见功能的重复编码，并且在客户端框架的情况下，从浏览器不一致中抽象出来。虽然服务器端和 JavaScript 框架受到了开发人员的广泛欢迎和接受，但 CSS 框架在设计人员中的争议要大得多。

虽然我碰巧同意 CSS 框架确实有各种各样的缺点(如不必要的体积、额外的 HTTP 请求和过度依赖其他开发人员的代码的风险)和优点(更快的生产时间、更少的浏览器问题、初学者更容易开发)，但它们的存在为我们提供了一个机会来重新审视我们如何编写自己的代码。理想情况下，如果我们能够从这些框架中吸收可重用性、抽象性和易用性的最佳经验，我们就可以通过调整我们的代码来遵循它们的理想，从而避免使用 CSS 框架。CSS 框架只是 web 设计者日常遇到的问题的解决方案，使他们能够产生干净、敏捷、跨浏览器的代码。尽管这些工具有自己的问题要解决，但它们可以作为一个优秀的模型来产生更高质量、更可移植和可重用的 CSS。

本文的目的不是讨论是否应该使用框架，而是研究为什么需要这样的解决方案。我们将采用在创建过程中使用的过程(和学到的经验)，并尝试展示如何将它们应用到您自己的样式表中。如果您检查任何框架的源代码，您将发现一个充满创新方法的世界，这些方法可以增加风格的可移动性，从而提高您自己的生产力，而没有使用第三方组件通常带来的缺点。

## 框架中有什么？

当为自己的设计生成 CSS 代码时，您可能会经历一个非常简单的过程来决定您希望 HTML 元素如何显示。你可以从应用整个站点继承的约定开始(通过元素选择器)，一旦完成，你就可以使用足够多的`class`和`id`选择器来设计站点的各个不同部分。这当然需要时间和精力，如果你创建了不止一个网站，你很快就会发现自己在重复某些惯例(比如分栏布局、默认排版，或者甚至是导航菜单的显示方式)。

框架试图解决的问题可以分为三类:

Browser quirks

所有的浏览器对待 CSS 都不一样；任何不幸为 Internet Explorer 6 编写过代码的人都会告诉你这一点。大多数设计者经常处理的 CSS 的一个方面是浏览器遭受的一系列缺陷和怪癖，既包括浏览器如何实现 CSS 代码，也包括它们对不同层次的 CSS 规范的支持。

Common components

约定的存在是有原因的:它们提供了用户可以快速适应的设计。注册表单、警告和错误通知、文章及其相关元数据——都可以有一组通用的设计功能。因为这些组件是一遍又一遍地编写的，所以您可能会发现自己只是在重复老一套。

Repeat properties

代码膨胀是任何开发工作中的一个主要问题，CSS 也不例外:随着我们在设计中加入越来越多的样式，我们可能会发现自己以一百种不同的方式重用 CSS 属性，其中许多可能具有相同的价值。一些设计者选择将他们的代码分组；然而，这在很大程度上仍然是一个试探性风格应用的问题。

所有使用 CSS 的设计师都面临这些问题，如果你选择不使用框架，你仍然需要在工作中解决这些问题。如果我们不小心，我们会花费大量的时间重新发明轮子。有时这是出于需要，而其他时候是因为我们发现自己为了可用性而一遍又一遍地重复一个设计惯例。检查我们自己的代码，进行有助于解决这些问题的优化和改进，通常是我们在项目中最后考虑的事情。然而，正如我希望向您展示的那样，CSS 库非常小心谨慎地确保代码尽可能地持久和兼容。

## 下刀

为了更好地理解 CSS 库如何产生可以被重用或应用于大量设计的组件(从而加快开发过程并减少您需要做的工作量)，我们应该仔细看看它们的源代码。我在这里要做的是强调框架利用各种 CSS 选择器类型来设置它们的约定和挂钩的方式。

**note:** Libraries We’ll Be Examining

用作检查它们如何工作的基础的 CSS 库包括: [960 网格系统](http://960.gs/)、[蓝图](http://www.blueprintcss.org/)、 [BlueTrip](http://bluetrip.org/) 、[元素](http://elements.projectdesigns.org/)、 [Emastic](http://code.google.com/p/emastic/) 、[埃里克·迈耶重置](http://meyerweb.com/eric/thoughts/2007/05/01/reset-reloaded/)、[森 CSs](http://sencss.kilianvalkhof.com/) 、[特里波利](http://devkick.com/lab/tripoli/)、 [Typogridphy](http://csswizardry.com/typogridphy/) 、 [YUI 网格](http://developer.yahoo.com/yui/grids/)和

### 方法一:复位

几乎所有被检查的 CSS 框架都使用了某种形式的重置机制。CSS 重置机制获取 HTML 规范中由某些浏览器给定了默认样式的每个元素，并用零值覆盖这些样式(从而将它们重置为“无样式的”默认值)。

Method

使用元素和偶然通配符(`*`)选择器来应用浏览器范围的样式。

Used by

960，蓝图，BlueTrip，元素，Emastic，EMR，SenCSs，Tripoli，Typogridphy，YUI，YAML

Reasoning

通过移除任何默认样式，重置机制减少了浏览器之间可能出现的呈现不一致和异常。

Example:

从埃里克·迈耶复位:

```
html, body, div, span, applet, object, iframe,h1, h2, h3, h4, h5, h6, p, blockquote, pre,a, abbr, acronym, address, big, cite, code,del, dfn, em, font, img, ins, kbd, q, s, samp,small, strike, strong, sub, sup, tt, var,b, u, i, center, dl, dt, dd, ol, ul, li,fieldset, form, label, legend,table, caption, tbody, tfoot, thead, tr, th, td {  margin: 0;  padding: 0;  border: 0;  outline: 0;  font-size: 100%;  vertical-align: baseline;  background: transparent;}body {  line-height: 1;}ol, ul {  list-style: none;}blockquote, q {  quotes: none;}/* remember to define focus styles! */:focus {  outline: 0;}/* remember to highlight inserts somehow! */ins {  text-decoration: none;}del {  text-decoration: line-through;}/* tables still need 'cellspacing="0"' in the markup */table {  border-collapse: collapse;  border-spacing: 0;}
```

The lesson

即使您没有使用框架，了解浏览器应用于某些元素的默认样式也很重要。忘记在元素范围内覆盖属性通常会导致令人困惑的跨浏览器问题。您可以开发自己的重置样式集，或者只保留一份来自第三方框架的重置样式副本，并将它们放入每个新项目中。

### 方法二:基线风格

很多 CSS 框架，除了重置默认浏览器样式，还引入了一些新的默认。不同于被重置剥离的浏览器默认设置，这些设置将在不同浏览器间保持一致和可靠，因为它们是由框架本身引入的。这些新的默认值提供了一个合理的基线，当您开始编写自己的样式时，可以从这个基线开始。

Method

使用元素或属性选择器在元素的每个实例中应用样式。

Used By

960，蓝图，BlueTrip，元素，Emastic，SenCSs，Tripoli，Typogridphy，YUI，YAML

Reasoning

基线样式可以减少未来开发中所需的代码量，因为基线旨在处理常见的设计范围的变更。

Example

在 BlueTrip CSS 框架中:

```
html { font-size: 62.5%; font-family: "Liberation Sans", Helvetica, Arial, sans-serif; }strong, th, thead td, h1, h2, h3, h4, h5, h6 { font-weight: bold; }cite, em, dfn { font-style: italic; }code, kbd, samp, pre, tt, var, input[type='text'], input[type='password'], textarea { font-size: 92%; font-family: monaco, "Lucida Console", courier, monospace; }del { text-decoration: line-through; color: #666; }ins, dfn { border-bottom: 1px solid #ccc; }small, sup, sub { font-size: 85%; }abbr, acronym { text-transform: uppercase; font-size: 85%; letter-spacing: .1em; }a abbr, a acronym { border: none; }abbr[title], acronym[title], dfn[title] { cursor: help; border-bottom: 1px solid #ccc; }sup { vertical-align: super; }sub { vertical-align: sub; }
```

The lesson

看看你最近的几个项目。你在每个项目中都倾向于使用元素范围的样式吗？寻找一种方法来抽象出您始终应用于给定元素或元素组的样式。

### 方法三:`id`挂钩

虽然在 CSS 框架中很少见——仅仅是因为它们的性质使得它们不太可能跨项目适用— `id`钩子有时会被使用，最常见的是在处理核心页面元素时，比如包装器、页眉、导航或页脚。

Method

将`id`属性(HTML)和`id`选择器(CSS)用于一次性组件。

Usage

元素，电子版，错别字，YUI，YAML

Reasoning

许多网页最终都是由相同的核心结构元素组成的。如果这些元素的某些属性在项目之间是一致的，那么将它们抽象出来并使用命名约定可以帮助减少重复并节省开发时间。

Example

类型网格框架

```
#wrapper{  width:94em;  margin:0 auto;}#header{  width:94em;  padding-top:1.5em;  margin-bottom:2em;}#nav{  font-size:2em;}#nav a{  font-variant: small-caps;  color:#000;}#nav a:hover{  text-decoration: none;}#nav span{  float: right;  margin-top:-1.2em;}#footer-nav{  word-spacing:0.2em;}#footer-nav a{   color:#000;   font-variant: small-caps;}
```

The lesson

与基线风格一样，这里的关键是自省。仔细检查你的代码，试着找到重复的地方。考虑如何构建一组样式，使其更容易将这些基线移动到新项目中，同时保持灵活性，并使其易于根据需要覆盖它们或在它们之间进行选择。

### 方法四:`class`挂钩

比`id`钩子更常见的是，`class`钩子几乎出现在每一个 CSS 框架中。通过提供一组与预定义样式相关联的`class`,它们可以让你在编写标记的时候快速地使用经过测试的样式。

Method

使用`class`属性和选择器来应用可重用的样式。

Usage

960，蓝图，BlueTrip，元素，Emastic，SenCSs，Tripoli，Typogridphy，YUI，YAML

Reasoning

虽然`id`钩子通常用于不同站点或项目中具有相同语义目的的元素，但是`class`钩子允许你预先定义特定的样式分组；例如，用于修饰或说明排版或布局。通过添加相关的`class`或`class` es，这些可以很容易地应用于任何项目中的任何元素。

Example #1

960 网格系统利用`class`属性来简化网格结构的开发。任何块或列可以被分配一个`.grid_x` `class`给它一个宽度跨越 *x* 列的主网格:

```
.container_24 .grid_1{width:30px}.container_24 .grid_2{width:70px}.container_24 .grid_3{width:110px}.container_24 .grid_4{width:150px}.container_24 .grid_5{width:190px}.container_24 .grid_6{width:230px}.container_24 .grid_7{width:270px}.container_24 .grid_8{width:310px}.container_24 .grid_9{width:350px}.container_24 .grid_10{width:390px}
```

Example #2

Blueprint 还使用许多数字命名的类来设置网格元素的宽度，尽管它不依赖于像 960 Grid System 这样的容器:

```
.span-1 {width: 30px;}.span-2 {width: 70px;}.span-3 {width: 110px;}.span-4 {width: 150px;}.span-5 {width: 190px;}.span-6 {width: 230px;}.span-7 {width: 270px;}.span-8 {width: 310px;}.span-9 {width: 350px;}.span-10 {width: 390px;}
```

Example #3

SenCSs 使用类挂钩的目的不同；它没有依赖它们来快速访问预定义的网格布局，而是使用一组`class` es 来设计特定的、经常使用的 UI 通知:

```
.error{  background:#fee;  border:1px solid red;  margin:1.5em;  padding:0.666em;  text-align:center;}.notice{  background:#eef;  border:1px solid #00f;  margin:1.5em;  padding:0.666em;  text-align:center;}.success{  background:#efe;  border:1px solid #0f0;  margin:1.5em;  padding:0.666em;  text-align:center;}.warning{  background:#ffe;  border:1px solid #ff0;  margin:1.5em;  padding:0.666em;  text-align:center;}
```

The lesson

这个更难。如果你仔细查看现有的代码，你可以构建类挂钩的样式类型不会总是被`class`打包在一起。但是您仍然可以找到具有很强重用潜力的代码块。目标还是抽象。想象自己在未来的项目中融入这些风格。如果您能够以一种更容易挂钩的方式提取出一组布局或印刷属性，您可以在未来的开发中节省大量时间。

## 便携式生产力

因此，我们已经看到了框架旨在使其代码可重用的一些方法。也许你现在想开发自己的框架，但即使你不这样做，这些经验教训也可以转化为生产力。我列出了六个简单的规则来优化你的代码，这些规则基于我们从 CSS 框架中学到的经验:

Use name conventions

所研究的框架允许其风格快速“插入”新设计的主要方式之一是通过使用命名约定。通过对页面元素进行一致的命名，您可以确保任何想要重用的样式都可以在很少修改或不修改的情况下快速就位。

Plan for reuse

这是你应该从研究这些 CSS 框架的代码中学到的关键一课。对您来说显而易见的是，这些库从一开始就被计划用于非常不同的上下文中。无论是建立一组合理的默认值，将通用的排版定义打包到一组预定义的`class`钩子中，还是准备一个更加成熟的基于类的布局系统，提前思考和规划重用都将为您节省最多的时间和精力。

Fix common browser problems

IE6 让我们中的一些人想要举起双手哭泣。CSS 库是一个极好的例子，说明了对常见浏览器缺陷的适当处理可以减少调试代码所需的时间，因为某些基本的修复可以很容易地移植到新的项目中。一个有价值的教训是，当错误发生时，通常有一个可重用的方法来解决它们。

Structure and group code

随着时间的推移，代码库变得高度优化、结构化和增强，以获得最大的灵活性、兼容性和可移植性。在 CSS 中，我们经常使用任何能完成工作的东西，而不是花时间进行压力测试或调整我们的风格，这样它就可以被重用，而不必到处重复。教训:一点爱和关心会带来好处，特别是对于正在进行的网站维护。以一种合乎逻辑的方式构建你的 CSS 不仅会使重用变得更容易，还会产生可读性更好的代码。

Remove redundancy

虽然 CSS 库通常是紧密打包的，但是它们通常有一些你永远不会用到的特性，导致不必要的膨胀。在您自己的样式表的上下文中，任何您停止使用或不需要执行特定任务的可重用元素都可以根据具体情况删除；这将节省不必要的带宽使用。

Document, document, document

大多数库和框架都有非常好的文档记录。我相信我们可以从这些工具中吸取的最好的教训是永远记录你的代码。注意，你应该避免评论比 CSS 多(算上那些带宽卡路里！)，但是文档良好的 CSS 使得维护和未来的开发更省时，不需要花时间去弄清楚为什么要以某种方式编码解决方案。

## 总结

在整篇文章中，我试图强调 CSS 框架是非常容易被误解的东西。它们产生于对通用解决方案的需求，这些解决方案已知有效，并且在开始新的设计时可以很容易地依赖。我希望我已经向您展示了这些框架与您自己的代码的相似之处，以及如何通过一点点的关心和注意，将您自己的 CSS 转变为适合您的代码:比预先编写的示例做得更好，在足够灵活地用于其他设计的同时，考虑到您项目的需要。

CSS 框架是一个很好的榜样，但不幸的是，它遭到了负面报道。我认为，如果你去掉与它们相关的嗡嗡声，以及人们似乎认为它们的使用暗示的懒惰，你可以去掉一些可能有助于你成为更好的设计师的实践。谁知道呢，一年之内你可能已经建立了你自己的有用代码片段库，这些代码片段可以交给社区并添加到不断发展的框架列表中。

## 分享这篇文章