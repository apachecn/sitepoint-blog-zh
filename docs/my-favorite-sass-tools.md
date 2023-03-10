# 我最喜欢的 Sass 工具

> 原文：<https://www.sitepoint.com/my-favorite-sass-tools/>

Sass 是大约 7 年前作为 CSS 缺陷的一种变通方法引入的，从那时起已经走过了很长的路。它在推广 CSS 预处理方面发挥了重要作用，问题不再是*如果*你应该使用一个预处理程序，而是*你应该使用哪一个*。事实证明，这个问题的答案通常是*Sass*——因为语法更好(更接近 CSS)，因为它是许可的，因为它可以做很多事情并且工作得很好。

自从推出以来，Sass 已经成为当今最重要的 CSS 预处理器。它有一个完整的生态系统围绕着它成长(从 [Compass](http://compass-style.org) 开始)——操场、应用程序、网格系统、图书馆等等……但是挖掘关于 Sass 的有趣的东西并不总是那么容易。东西越多，你就越需要寻找你想要的东西。

因此有了这篇博文。如果我通过向你展示我认为最有价值的 Sass 工具来启发你呢？听起来很酷，对吧？我们走吧。

## 操场

开始使用 Sass 并不容易。主要是因为它是用 Ruby 写的。虽然 Ruby 是一种流行的语言，但前端开发人员通常并不了解 Ruby。这是导致新手少用或者手写笔的主要原因，手写笔是用 JavaScript 写的(其实是 Node)。人们通常不想仅仅为了看看 Sass 的价值而将 Ruby 添加到他们的堆栈中。

幸运的是，你可以在网上试试 Sass。有一些游戏场，或者在线工具，允许你写 Sass，查看编译后的 CSS，以及(有时)代码呈现的方式。在撰写本文时，有两个主要的领域可以使用 Sass:

### 密码笔

克里斯·科伊尔、亚历克斯·瓦斯奎兹和蒂姆·萨巴特设计的 CodePen 是迄今为止你能使用的最好的游乐场。它提供了前端测试所需的一切。关于 Sass，它支持稳定版本(在撰写本文时版本为 3.3.4 ),您可以使用旧的缩进语法或 SCSS 语法。

[![CodePen](img/aa18816ac83f91893b85f64b6a3b4a3f.png)](http://codepen.io)

你也可以在指南针和波本威士忌之间选择。它还会显示语法错误。它真的很棒，所以如果你还不知道的话，[来参观一下](http://codepen.io/hello)，试试 Sass 的功能。

### 萨斯梅斯特

杰德·福斯特(Jed Foster)设计的 SassMeister(T1)是一个专门经营**与 Sass 相关的东西的游乐场。显然，它比 CodePen 做得更好。您可以使用 stable 或 Sass 3.2.14，甚至是 LibSass，Sass 的 C 端口(在撰写本文时仍运行在 Sass 3.1.x 上)。**

[![SassMeister](img/2614f1d94d9d4cc2c4c563281f34dcf4.png)](http://sassmeister.com)

当然，SassMeister 允许您选择自己更喜欢的语法，甚至可以选择输出样式(扩展、压缩等)。最重要的是，因为 SassMeister 是特定于 Sass 的，所以它允许您导入 Sass 库(实际上是 Compass 扩展)。如果您认为应该添加扩展，您甚至可以提出自己的扩展。

用一个方便的图表总结一下:

|  | 密码笔 | 萨斯梅斯特 |
| --- | --- | --- |
| 版本 | 稳定的 | 稳定或 3.2.14 |
| 利布萨斯 | 不 | 是 |
| 输出风格 | 扩大 | 任何的 |
| 允许扩展 | 不 | 是 |
| 句法 | 两者 | 两者 |
| 自定义编辑器 | 是 | 有一点 |
| 专注于 | 翻译 | 输出 |

我认为最后一行说明了一切:因为它的横向特性，CodePen 关注的是渲染，也就是说，一旦 DOM 被你的(经过某种预处理的)CSS 代码样式化，被你的 JavaScript 操作，页面看起来是什么样子。另一方面，虽然 SassMeister 允许您查看渲染，但我觉得它更侧重于 CSS 输出。CodePen 使得查看编译后的代码变得很困难，因为它是 SCSS 和编译后的 CSS 的同一个编辑器，你必须通过点击一个按钮来切换模式。

*注:也有 CSSDeck、JSBin、JSFiddle 等一些提供预处理选项的 playgrounds，但在 Sass 方面却比不上 CodePen 和 SassMeister。*

## 编译程序

好吧，当你想尝试 Sass 时，code playgrounds 非常酷，但是当在实际项目中使用 Sass 时，你只能靠自己了。不能把编译推迟到操场什么的。你必须自己去做——除非你决定使用工具。有很多这方面的应用，但我觉得其中有两个非常突出。

### 预加工

Prepros 是一个相当新的软件，旨在减轻处理预处理编译的痛苦，尤其是在命令行(oooohh，命令行！).

[![Prepros](img/ed807b98b4c2a5e23db35c3e5e8ca585.png)](http://alphapixels.com/prepros/)

它提供了一个简洁的用户界面，让您可以无缝地编译源代码，而无需键入任何命令。我在发布时试过，它真的很酷，所以我认为这是一个非常可靠的选择。

### 代码包

另一方面，CodeKit 并不是全新的，而是在[升级到 v2.0](http://css-tricks.com/codekit-2-0/) 后更新的。

[![CodeKit](img/ef4f03f0c18c93a62bb99ab17ee3b1ea.png)](https://incident57.com/codekit/)

在过去，我只听过关于 CodeKit 的最好的消息，所以我只能假设 CodeKit v2.0 很棒。然而，我可以看到 CodeKit 的两个缺点:首先，它只对 Mac 用户开放，而且不是免费的。

所以总结一下:

|  | 代码包 | 预加工 |
| --- | --- | --- |
| 费用 | $29 | 免费或 25 美元(专业版) |
| 环境 | 苹果个人计算机 | Mac 和 Windows |

如果你不想支付任何费用，或者没有使用 Mac，就去 Prepros。如果你有一台苹果电脑，并且能省下 25 美元，那就要看情况了。我要说 CodeKit 到目前为止有更好的记录，而 Prepros 仍然是相当新的。

或者你可以学习使用命令行…

*注意:还有其他选项，如 [Compass.app](http://compass.kkbox.com/) 、 [Scout](http://mhs.github.io/scout-app/) (由于一些错误和它似乎不再受支持的事实，我不会推荐这些选项)。但是没有一个替代品能与 CodeKit 和 Prepros 相比。*

## 网格系统

有数百个 CSS 网格系统可供选择。许多都是建立在 CSS 预处理器之上的，其中大部分都使用了 Sass。因此，当您必须为一个项目选择一个现有的、可靠的、强大的基于 Sass 的网格系统时，这可能会很复杂。你怎么知道哪个好，哪个不好？或者哪个很好地处理了响应，哪个足够容易上手？

如果你问我，有 2 个用 Sass 编写的网格系统确实高于平均水平。

### Singularity.gs

Scott Kellum 和 Sam Richard 的 Singularity.gs 不是一般的网格系统。它提供了一个强大的 API，允许你创建你想要的任何类型的网格，控制列数、装订线宽度、长宽比等等。

[![Singularity](img/633ee398ba777887cf0defc3af96b307.png)](http://singularity.gs/)

Singularity 有一个完整的 [wiki](https://github.com/Team-Sass/Singularity/wiki) 来帮助你开始。

### 苏西！苏西

Eric M. Suzanne 的 Susy 刚刚迁移到 2.0 版本，使它成为 Sass 代码的杰作，并帮助您部署 wickedest 网格。

[![Susy](img/c6a8a5e0883da9f92a70c489db46454f.png)](http://susy.oddbird.net/)

Susy 是我见过的最有趣的 Sass 项目之一，它提供了一个完整的文档来学习如何使用它。

|  | 奇异 | 苏西！苏西 |
| --- | --- | --- |
| 默认 | [隔离](https://github.com/Team-Sass/Singularity/wiki/Output-Styles#isolation) | [浮动](http://susydocs.oddbird.net/en/latest/toolkit/#span-mixin) |
| 插件 | 奇点附加功能 | 没有人 |
| 需要指南针 | 不 | 不 |
| 工作流集成 | 鲍尔，指南针 | 指南针，波旁，鲍尔，约曼 |

这两个选项都是顶级的网格系统，比您需要做的要多，所以您可以随意选择其中一个。也就是说，因为 Susy 不依赖于 Compass，所以它是运行没有 Compass 的项目的完美选择(例如由 LibSass 支持的项目)。另外，如果你对阅读萨斯诗歌感兴趣，一定要看看 Susy 的源代码！

*注意:在 2013 年的前几个月，两个电网系统本应合并成所谓的`Susy Next`。现在情况不同了，两个项目仍然共存。*

## 测试工具

无论您是建立一个健壮的 Sass 架构还是编写一个很酷的小库，总有一天您会想要测试您的代码。虽然我们不习惯测试 CSS，但是聪明的人已经提供了一些智能工具来帮助测试 Sass 代码。

Matias Singers 就此写了一篇很棒的文章,所以我不会在这里深入讨论测试工具，但是让我们来讨论其中的三个:

### 真实的

Eric M. Suzanne 设计的 True T1 是一个 <abbr title="Test-Driven Development">TDD</abbr> 风格的 Sass 框架，灵感来自于 [QUnit](https://qunitjs.com/) 。它帮助你为你的函数编写测试。我自己没有尝过，但是我只听说过它的优点。例如，它是我们刚刚讨论过的用于 Susy 的测试框架；Compass 也计划使用 True 进行自己的测试。

下面是使用 True 的测试结果:

```
@include test-module('Column Math') {
@include test('[function] is-symmetrical()') {
@include assert-true(true, 'Number should be symmetrical');
}
}
```

### 训练营

James Kyle 开发的 Bootcamp 是一个测试 Sass 功能的 <abbr title="Behaviour-Driven Development">BDD</abbr> 框架，其语法非常接近于 [Jasmine](http://jasmine.github.io/) 为 JavaScript 提供的语法。Bootcamp 真正酷的地方在于它是作为 Grunt 插件存在的，这使得它成为添加到任何运行在 Grunt 上的项目中的完美选择。我个人用它来测试我的 Sass 库(SassyLists、SassyCast、SassyJSON、SassyStrings、SassyMatrix)，我非常高兴。

```
@include describe("A suite") {
@include it("contains spec with an expectation") {
@include should(expect(1 + 1), to(be(true)));
}
}
```

总而言之:

|  | 真实的 | 训练营 |
| --- | --- | --- |
| 类型 | TimeDivisionDuplex 时分双工 | BDD |
| 基于 | 维护 | 预期 |
| 受到...启发 | QUnit | 茉莉 |
| 工作流集成 | 鲍尔指南针 | Grunt，Bower，npm |
| 重量 | 光 | 中度到重度 |

如您所见，这两个框架看起来很相似。我喜欢 Bootcamp 用非常简单的语法在 Grunt 和 NPM 上运行。另一方面，True 已经被证明在 Susy 上工作得非常好，并且非常轻量级。另一方面，Bootcamp 2 正在开发中，它将使语法更加简单。

### SCSS 林特公司

SCSS-Lint 是一个用于 Sass 的 Lint 工具。JavaScript 有 JSHint 和 JSLint，CSS 有 CSSLint，现在 Sass 有 SCSS-Lint。SCSS-林特帮助你保持一个干净整洁的代码库，只要有什么不对劲的地方就发出警告。像任何 linter 一样，SCSS-lint 允许您用配置文件指定自己的规则，如下所示:

```
test.scss:2 [W] Prefer single quoted strings
test.scss:2 [W] Line should be indented 0 spaces, but was indented 1 space
test.scss:5 [W] Prefer single quoted strings
test.scss:6 [W] URLs should be enclosed in quotes
```

## 图书馆

有数不清的 Sass 库(大部分作为 Compass 扩展)。自从 [Sache.in](http://sache.in) 被创建以来，发现新的扩展从未如此容易。基本上，Sache.in 是 Sass 的插件查找器。您可以查找特定的库或标签，或者简单地浏览最佳的 Sass 扩展。

在有趣的作品中，我想提一下:

### Modernizr 辅助混合

Daniel Guillan 开发的 Modernizr Helper Mixin 是一个非常轻量级的 Compass 扩展，为 Modernizr 集成提供了一个非常简单的 API。基本上，当你在样式表中使用 Modernizr 的`.no-*`类时，你想做的事情就很明显了。

```
/* SCSS */
.my-selector {
@include yep(translate3d opacity) {
transform: translate3d(0,100px,0);
opacity: 0;
}
@include nope(translate3d opacity) {
top: 100px;
display: none;
}
}

/* CSS */
.translate3d.opacity .my-selector {
transform: translate3d(0,100px,0);
opacity: 0;
}

.no-js my-selector,
.no-translate3d .selector,
.no-opacity .selector {
top: 100px;
display: none;
}
```

### 引号

尼科·哈根伯格(Nico Hagenburger)创作的引号有点奇怪——我非常喜欢。这个微型库可以根据你使用的语言帮助你使用准确的引号。它目前支持超过 55 种不同的语言，并且很容易扩展到更多。

```
/* SCSS */
.my-class {
@include quotation-marks(en);
}

/* CSS */
.my-class:before {
content: "“";
}

.my-class:after {
content: "”";
}
```

### 动画.萨斯

Polikin 开发的 Animate.sass 是 [Animate.css](http://daneden.github.io/animate.css/) 的光荣继承者，这是一个即插即用的 css 动画集合，用来增强你的界面。它只不过是利用 Sass 的特性，比如循环、混合、变量等等，重写了原始库。

```
@include animation-keyframes(fading-entrances/fadeInRight);
.my-class {
@include animations(fadeInRight);
}
```

### HiDPI

HiDPI 是由《卫报》的 Kaelig 编写的一个非常有价值的指南针扩展，无缝地将高分辨率背景图像提供给高密度(视网膜)显示器。考虑到处理视网膜显示器有多难，这样的工具是一种福气。

```
/* SCSS */
.logo {
@include hidpi(my-logo);
}

/* CSS */
.logo {
background-image: url('img/my-logo.png');
}
@media (-webkit-min-device-pixel-ratio: 1.3),
(min-resolution: 124.8dpi),
(min-resolution: 1.3dppx) {
#logo-auto {
background-image: url('img/my-logo_x2.png');
background-size: 250px 188px;
}
}
```

### SassyJSON

SassyJSON 最初是我和 Fabrice Weinberg 之间的一个有趣的项目，直到我们最终用 Sass 编写了一个 JSON 解析器。基本上，SassyJSON 提供了一个简单的 API 来编码和解码 Sass 中的 Sass。

```
@include json-encode(((a: (1 2 ( b : 1 )), b: ( #444444, false, ( a: 1, b: test ) ), c: (2 3 4 string))));
// {"a": [1, 2, {"b": 1}], "b": ["#444444", false, {"a": 1, "b": "test"}], "c": [2, 3, 4, "string"]}

$json-decode: json-decode('{"a": [1, 2, {"b": 1}], "b": ["#444444", false, {"a": 1, "b": "test"}], "c": [2, 3, 4, "string"]}');
// ("a": 1 2 ("b": 1), "b": #444444 false ("a": 1, "b": "test"), "c": 2 3 4 "string")
```

SassyJSON 的酷之处在于，它在解码 JSON 时保留了 Sass 数据类型。这意味着如果您有一个包含`"#444444"`的 JSON 字符串，它将在 Sass 中作为`color`返回。数字、长度、角度等等也是如此。因此您肯定可以获得 JSON 数据，在 Sass 中解码数据，并在样式表中无缝地使用它们。

为了帮助人们在实际项目中使用 SassyJSON，例如同步共享模块的 Sass 和 JavaScript 之间的配置，我们在 Ruby 中添加了一个 JSON 文件导入器(这是整个项目中唯一的一块 Ruby)。有了它，您可以导入并解码一个`.json`文件，以便在样式表中访问它的内容。

## 最后的想法

差不多了，伙计们！我认为这是对 Sass 工具的一个很好的总结。

如果您有任何更有趣的 Sass 工具和资源可以分享，请务必发表评论！

## 分享这篇文章