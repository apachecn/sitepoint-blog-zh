# es 2016:JavaScript 的未来应该是开发者驱动的吗？

> 原文：<https://www.sitepoint.com/es2016-should-future-of-javascript-be-developer-driven/>

最近，有人宣布 JavaScript 的下一个版本(ES2016)将由一个[非常小的功能集](https://ponyfoo.com/articles/es2016-features-and-ecmascript-as-a-living-standard)组成，即 [Array.prototype.includes](https://github.com/tc39/Array.prototype.includes/) (它决定一个特定的值是否包含在一个数组中)和[取幂运算符](https://github.com/rwaldron/exponentiation-operator)(它对一个数进行指数幂运算)。考虑到 ES6 引入的过多新功能，一些人可能会惊讶于 ES2016 的发布会有多小。另一方面，其他人可能会很高兴只需要学习两个新功能——以大多数人的标准来看，这是一个可管理的目标。

虽然 ES2016 如此之小引起了一些人的关注，但它也凸显了另一个问题——`Array.prototype.includes`方法最初将被命名为`Array.prototype.contains`，但事实证明这个名称与[网络兼容](https://github.com/tc39/Array.prototype.includes/tree/b6671aec098db241ab2d27d7bc182cc8a074edef#status)(阅读它会与 MooTools 库发生冲突，可能会导致许多网站崩溃)。

因此它被重新命名。

我们今天要问的是，对于社区来说，像这样推动语言的发展方向是否是一件好事，或者规范因为库冲突而改变是否“有点不正常”。我们的两位作者([莫里茨](https://www.sitepoint.com/blog/)和[蒂姆](https://www.sitepoint.com/author/tseverien/))在这个问题上持相反的观点。

## Tim:规范应该起作用，图书馆应该服从

如果你忽略它的怪癖，JavaScript 很容易掌握并且非常灵活——它是一种很好的第一语言。它也是很好的第二语言。我认识的许多开发人员在编写 JavaScript 之前都有过用其他语言进行历史编程的经历，随着 Node 变得越来越稳定和优秀，我相信很多人会效仿。

似乎编程界不同意命名一个方法来检查数组项或子串是否存在于数组或字符串中。C#和 Java 有针对类数组和字符串类的`.contains()`，Ruby 有`.include?()`，Python 有`in`-运算符，PHP 有`in_array()`和`strstr()`函数。有点乱。然而在 JavaScript-land 中，有 jQuery、下划线、MooTools 和一堆其他框架/库都有`.contains()`。也许我们可以谈谈这里正在进行的一个小小的会议。

> 如果他们打算在命名 API 时考虑旧库，我担心这只是超级怪异名称的开始

我得到的理念是，变化可能会破坏许多网站和/或应用程序，但我们必须意识到，随着现有库的多样性，突破性的变化将会发生。我讨厌我们愿意做出设计选择来躲避一颗子弹的想法。这并不是说我不同意所选择的名字，但是这种理念可能会导致未来糟糕的设计选择，如果它可能会因为他们糟糕的设计选择而破坏 web 的 1%。

更困扰我的是前后矛盾。这一改变不会把 JavaScript 变成另一个 PHP，但是 TC39 应该保持高标准。例如，DOM 规范包括 [Node.contains()](https://developer.mozilla.org/en-US/docs/Web/API/Node/contains) 。尽管有人可能会说当前实现的`.includes()`是 JavaScript 的核心部分，而 DOM API 不是，但当你为 web 构建时，这是令人困惑的，这似乎是选择`.includes()`而不是`.contains()`的首要考虑。

我认为 TC39 应该专注于保持 JavaScript 整洁。在其他语言中，您通常可以构建并坚持特定的版本。然而，网络并不擅长压抑和改变——每一个选择都是永恒的。在使用设计糟糕的库破坏 1%的网络和关注语言的未来和持久性之间选择，我宁愿选择后者。

## 莫里茨:我们不应该仅仅因为命名偏好而破坏网络

网络总是关于可访问性。用旧标准(如 XHTML 1.0)编写的网站今天仍然可用，不会使你的浏览器崩溃。JavaScript 几乎已经成为一种必需品，并为我们称之为互联网的很大一部分提供了动力。去年的 ECMAScript 2015 功能集的一个很好的部分(如果不是大部分)是语法糖，旨在提供向后兼容性。

> 重点应该是修复真正的语言缺陷和需求，而不是从其他语言中采用更多的句法糖。

JavaScript 并不总是像今天这样功能丰富。有了 Prototype、jQuery、MooTools 等库和框架，网络社区自己填补了空白，满足了需求。其中一些变得非常流行，另一些消失了，但所有这些最终都塑造了网络和语言规范。

许多最新的 JavaScript 附加物只是对这些库已经提供的东西的一个回答。只有在创建新的语言特性时考虑这一点才有意义。因此，我很高兴看到第 39 个技术委员会更名为`Array.prototype.includes`。

正如 Tim 已经指出的，命名一个检查某项是否存在的函数，是整个编程界的一个活跃话题。DOM API 从`contains()`命名模式([element . class list . contains()](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)， [Node.contains()](https://developer.mozilla.org/en-US/docs/Web/API/Node/contains) )开始，然后语言规范将一个 API 改为`includes()`，剩下的似乎按照初始模式进行: [Selection.containsNode()](https://developer.mozilla.org/en-US/docs/Web/API/Selection/containsNode) 。我也希望 JavaScript 成为一种一致的语言，但是我们不应该仅仅因为命名偏好就开始破坏 web。一致性很重要，但是保持网络的可访问性更重要。

除此之外，我欢迎 ECMAScript 规范的新发布过程。不要每年都有大量的新特性，这将有助于开发人员跟上语言的发展。尽管如此，ECMAScript 的发展方向仍然应该有一个大致的计划。仅仅为了满足当前趋势的需求而增加新的功能将会以臃肿的语言告终。重点应该是修复真正的语言缺陷和[需要](https://github.com/tc39/ecmascript-asyncawait)，而不是从其他语言中吸收更多的句法糖。

## 轮到你了

所以你有它。我们应该坚定立场，专注于我们热爱的语言的未来和持久性，还是应该避免因命名偏好而破坏网络？

不可否认，给事物命名是困难的。正如[所说的](http://martinfowler.com/bliki/TwoHardThings.html)，在计算机科学中有两个难题:缓存失效、命名事物和一个接一个的错误。

在这场辩论中，你站在哪一边？我们希望在评论中听到你的意见。

## 分享这篇文章