# ES6 难题

> 原文：<https://www.sitepoint.com/the-es6-conundrum/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

ECMAScript 6 或者 ES6 是 JavaScript 的进化，是未来。这是我们一直期待的网络技术创新。它充满了我们一直渴望的强大功能，最终使我们能够以可伸缩和易于维护的方式为 web 构建巨大的应用程序。它让成千上万来自 Java 或 C#或其他“高级”语言的开发人员最终能够编写 JavaScript，并将他们所有的经验带到一个急需组织和指导的市场。

这就是推销词，公平地说，大部分都是真的。然而，它也对网络上需要修复的东西做了很多假设。发展像 JavaScript 这样无处不在的语言，却不能控制它的执行环境，这是一个真正的问题。网络是一个分布式平台。更换一辆不断行驶的汽车的车轮要比换一辆你可以带回商店、锁上、升级并再次开出去的汽车的车轮困难得多。

## JavaScript 似乎不再适用了

我们应该说，JavaScript 的宽松架构一直是来自其他语言的程序员的困惑之源。特别是缺少类和原型继承对很多人来说是不可行的。这感觉违背直觉，也不代表学校里教的关于计算的内容。更糟糕的是，我们拥有漂亮但令人困惑的[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures?WT.mc_id=13412-DEV-sitepoint-article36)构造，却缺乏常量和类型安全。所有这些都意味着 JavaScript 因为糟糕的架构和不值得信任的大代码库而声名狼藉。尽管如此，它还是设法发展成为现在软件开发中最常用的语言之一——这在很大程度上要归功于网络的兴起。

ES6 和该语言的后续版本旨在消除这些烦恼，使 JavaScript 成为更好、更可靠、更高效的语言。

公平地说，这并不新鲜。过去我们有一些基于 JavaScript 的语言，并试图解决这些问题。 [TypeScript](http://www.typescriptlang.org/) 、 [Dart](https://www.dartlang.org/) 、 [CoffeeScript](http://coffeescript.org/) 甚至 Flash 的 [ActionScript](http://www.adobe.com/devnet/actionscript.html) 都试图解决同一个问题。不同之处在于它们都需要某种转换或容器来显示在浏览器中。ES6 现在已经完成了，它是浏览器的原生版本，使用指向它的脚本元素像 JavaScript 一样运行。ES6 有许多令人印象深刻的功能:

*   [**箭头函数**](https://www.sitepoint.com/javascript-arrow-functions/) 为匿名函数的简写版本。
*   [**块级作用域**](/joys-block-scoping-es6/) 使用 *let* 代替 *var* 使得变量作用域为一个块(if、for、while 等。)
*   [**类**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) 来封装和扩展代码。
*   [**常量**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const) 使用*常量*关键字。
*   [**foo 等功能的默认参数**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)(bar = 3，baz = 2)
*   [**析构**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 将数组或对象中的值赋给变量。
*   使用*函数** 和*生成*关键字的 [**生成器**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Generator_comprehensions) 创建迭代器。
*   [**Map**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) ，可以用来存储键/值对的字典类型对象。和 [**设置**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 为集合对象存储数据值列表。
*   [**模块**](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-scripts-and-modules) 作为组织和加载代码的方式。
*   [**承诺**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 为异步操作避免回调地狱
*   [**休息参数**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) 而不是使用*参数*来访问函数参数。
*   [**模板字符串**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings) 建立包括多行字符串的字符串值。

考虑到我们最近用 JavaScript 做的事情，这似乎是一个真正的需求。在 JavaScript 中，游戏、应用开发甚至服务器端开发都是可能的。对于每个环境，我们都有习惯不同工具和方法的开发人员。过去狂野的西部 web 开发似乎与今天的性能和维护需求背道而驰。这让我想起了我们将[从 DHTML 转移到 DOM 脚本](http://icant.co.uk/articles/from-dhtml-to-dom/from-dhtml-to-dom-scripting.html)的时候。秩序，我们需要秩序和可靠性。

这个问题的症结在于，目前 ES6 还远未准备好部署在 web 上。这不是语言的错，而是网络的本质。我们不能也不应该规定人们用什么上网。但是当前不同浏览器对 ES6 的支持并不令人鼓舞。

[![A comprehensive ES6 support grid maintained by Juriy Zaytsev](img/4949d45dcfb36baf8882306f54121bbc.png)](http://kangax.github.io/compat-table/es6/)

更大的问题是，ES6 在 JavaScript 历史上第一次打破了网络独立于设备和浏览器的观念。

## 用新语法打破网页

ES6 的问题不在于它对语言做了什么——JavaScript 总是用新的方法和 API 来扩展。为了安全地使用它们，我们需要做的就是在应用之前测试当前的浏览器或环境是否知道这个功能。这被称为渐进增强，意味着我们永远不会交付破碎的体验。

当您在调用某个方法之前测试它是否存在时，您是安全的。就像在跳进河里之前检查河水是否足够深是一个好主意。ES6 的问题是它破坏了向后兼容性，因为它引入了 JavaScript 的许多语法变化，而不仅仅是新的方法和对象。

这不应该是一个问题，但与所有其他用于在网络上构建“东西”的语言不同(有意识地避开这里的网站与应用程序之争)，JavaScript 是不容错的。

以下 HTML 对浏览器来说不是问题:

```
<p><span>Nesting</p></span>
```

在内部，这个问题得到了解决，浏览器继续以自己喜欢的方式呈现页面的其余部分:

![Firefox Developer tools showing how the HTML5 parser fixed the wrongly nested HTML](img/a52a3c990dd3032fec0334fd491a8d60.png)

展示 HTML5 解析器如何修复错误嵌套的 HTML 的 Firefox 开发工具

这种容错是 HTML5 的主要思想之一。HTML5 解析器非常宽容，因为我们知道开发人员会犯错误，我们的用户不应该因此而受到伤害。只有一个错误的 XHTML 文档将无法呈现。这还不够好——我们需要一个更坚固的网络，因为最终用户体验高于一切。这甚至被定义为 HTML 的一个设计原则，作为选区的[优先级:](https://www.w3.org/TR/html-design-principles/#priority-of-constituencies)

在冲突的情况下，考虑用户优先于作者优先于实现者优先于说明符优先于理论纯度。换句话说，用户的成本或困难应该比作者的成本更重要；这反过来应该比实施者的成本更受重视；对于规范本身的作者来说，应该给予比成本更多的考虑，而不是仅仅因为理论上的原因而提出修改。当然，最好是一次让多个选区的情况变得更好。

CSS 解析器对代码的态度是一样的。例如，应用了该 CSS 的文档中的段落将是浅橙色。

```
p {color: microsoftblue;}
p {color: peachpuff;}
p {colour: powderblue;}
```

“peachpuff”是 CSS 颜色的有效名称，而“microsoftblue”则不是。虽然“粉蓝色”也是一种有效的颜色，但语法上正确拼写的“颜色”需要是 CSS 中的“颜色”，这就是它没有被应用的原因。本质上，CSS 解析器不能处理的任何代码行将被跳过。

这种容错在 JavaScript 中不起作用，这就是为什么它是 web 栈中最脆弱的部分。任何导致 JavaScript 错误的问题都意味着整个脚本无法执行——浏览器中没有容忍度。

当人们在网上讨论逐步增强的需求时，这一点经常被遗忘。这与禁用 JavaScript 的最终用户无关——这些人是极少数。它是关于在 JavaScript 执行之前和浏览器最终试图运行它时所有可能出错的事情。Stuart Langridge 维护着一个有趣的[决策树](http://kryogenix.org/code/browser/everyonehasjs.html)告诉你从请求你的脚本到执行过程中所有可能出错的事情。

ES6 对 JavaScript 的语法做了很多改变。虽然 ES6 兼容的浏览器或预处理器对下面的代码没有问题，但对于非 ES6 浏览器来说，这只是一个语法错误。

```
function showperson(ismember = 0, hasphoto = 0, ...moar) {
/* … */
}
```

这给我们提出了一个大问题。使用 ES6 是不可能的，除非我们把自己限制在少数已经支持它的浏览器上。微软 Edge、火狐、Chrome 和 iOS Safari 都实现了 ES6 的一个很好的子集。然而，并不是所有这些浏览器都是我们的用户所拥有的，我们也不能假设人们总是在升级。可悲的是，有很多硬件都带有不可升级的操作系统，其中包含过时的浏览器。

## 功能检测语法支持？

解决这个问题的一个非常有趣的方法是[凯尔·辛普森](https://twitter.com/getify)的 [Featuretests.io](http://featuretests.io/) 。它是一个非常小的 JavaScript 库，允许您测试 ES6 特性，因此只有在浏览器支持时才加载 ES6 脚本。也就是说，在句法层面上的渐进增强。

使用这个库，您可以测试您拥有的浏览器，看看它们支持什么。我现在对我的机器进行了快速检查，结果显示:

![A table showing how various browsers support ES6 features](img/455710decebe97b8332b32af04064dc8.png)

我不想在这里比较浏览器——这是浪费时间，看看变化有多快。我想展示的是，当谈到浏览器对 ES6 的支持时，它们之间有相当多的差异。这使得特性测试变得尴尬，因为只有当你测试了所有你想用的东西时，它才是安全的。假设只测试一个特性就支持其他特性，这是一条不归路。

如果你一路测试每一个你想要使用的特性，你的代码就不会出错。然而，这很容易变成过度杀戮。对于许多开发者来说，ES6 的意义不在于零碎地使用这些特性，而是从一开始就在 ES6 中编写整个应用程序。

这没什么新鲜的。当 HTML5 和 CSS3 是一个大肆宣传的词，我们迫不及待地想使用它时，我们得到了很多“这个很酷的东西只能在 Safari 中工作”或“你需要使用 Chrome 才能看到这个网站”，因为许多功能仍然在变化中。这些产品仍然在网上，很大程度上没有得到维护，浏览器需要在它们的代码库中包含许多 cruft，以免破坏网络。我们想要的太多，太快，而且事后没有清理干净。

大量的努力投入到允许开发者使用 [Modernizr](http://modernizr.com/) 来自动检测一切。对于遗留浏览器支持，这仍然是一个非常好的想法，但是在一个更加明确的环境中，开发人员发现这是一个不必要的开销。取而代之的是，我们开始设定一个支持的基线，并且只给那些通过筛选的浏览器提供脚本功能和高级样式。BBC 的开发人员在三年前称之为“[切芥菜](http://responsivenews.co.uk/post/18948466399/cutting-the-mustard)”，他们的基线是这几个检查:

```
if ('querySelector' in document &&
'localStorage' in window &&
'addEventListener' in window) {
// bootstrap the javascript application
}
```

也许这也是我们可以在 ES6 工作中想到的东西？定义一些需要检查的把关者功能，并从那里继续前进？

如果你不想依赖于浏览器支持，并且你想要的只是完整地使用 ES6，你将需要使用一些转换你的代码的东西。这可能是一种自己的语言，如 TypeScript 或所谓的 Transpiler(因为它将 ES6 翻译和编译为 JavaScript)。

## 去营救吗？

如今 JavaScript 的好处是它摆脱了浏览器的限制，也可以在服务器端使用。 [node.js](https://nodejs.org/) 使用 Chrome 的 [V8 引擎作为独立二进制，现在也可以使用](https://code.google.com/p/v8/)[微软的 Chakra JS 引擎](https://github.com/Microsoft/node)。这允许我们使用任何 JavaScript 转换工具将 ES6 代码转换成可以在任何浏览器中运行的旧 JavaScript。如果您已经在使用任务管理，使用 [Grunt](http://gruntjs.com/) 或 [Gulp](http://gulpjs.com/) ，这可能只是在部署之前运行的另一个任务。

有几个选择。最著名的 transpilers 是起源于 Google 的 Traceur T1 和 T2 的 Babel T3，这两个 trans pilers 最初被称为 6 到 5，但是随着 ECMAScript 的发展，甚至当我们试图实现它时，也需要一个更通用的名字。

Transpiling 似乎是目前在实际项目中使用 ES6 最安全的方式，而不用担心跨环境的支持差异。它还很好地结合了习惯于更严格的基于类的语言的开发人员的工作流程。但是，仔细反省一下，还是有一些奇怪的缺点:

*   首先，transpiling 在大多数情况下根本不做任何特性检测 ES6 代码被完全转换成 ES5(或者在某些情况下甚至转换成 ES3，如果你想这样做的话)。这意味着原生支持 ES6 的浏览器永远不会得到代码。这使得浏览器中的实现有点多余，更糟糕的是，它不允许在浏览器中测试 ES6 实现的性能和有效性。
*   传统上，web 是视图源。这就是我们过去学习如何为它编码的方法。我们查看了源代码，发现了其他人使用的东西，我们的调试过程也在做同样的事情，在错误控制台中看到有东西坏了。现在，我们有开发工具，有更先进的功能。然而，如果我们传输代码，我们永远不会写出被执行的代码。我们在浏览器中调试的代码是 transpiler 创建的，这是为了性能而不是可读性而优化的。为了调试我们的代码，我们需要找到一种方法将生成的代码与编写的代码连接起来。为此，我们使用[源地图](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/)。这也适用于由 [Sass](http://sass-lang.com/) 或 [less](http://lesscss.org/) 生成的 CSS。
*   从其他代码生成代码可能会产生大量文件。当最终结果意味着用户必须为一个更轻量级的界面下载数兆字节的 JavaScript 时，我们编写的原始代码是否紧凑、干净和结构化并不一定是重要的事情。

## 性能呢？

每当我们在浏览器中引入新的功能时，都会有这样一个问题:我们是让事情变得更快，还是让事情变得更慢。我们只是不知道，直到我们收集了足够的数据，找出在哪里进行优化。在 ES6 的情况下，如果我们传输代码，数据收集就不会真正发生。现在，ES6 在浏览器中的性能看起来并不太令人鼓舞，正如 Kevin Decker 的速度报告所示。

![A report shows the performance of ES6 features relative to the ES5 baseline operations per second.](img/c87385f83de2683ca5828b9c02b85549.png)

此报告显示 ES6 功能相对于每秒 ES5 基线操作的性能。不是深绿色的东西都比 JavaScript 慢。不是深绿色的东西要么慢，要么和 ES5 的性能一样。这意味着，只有[传播运营商](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)、[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)、[地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)和[集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)带来性能收益。

## ES6 很重要，需要我们的关注

现在，就整个 web 开发而言，ES6 处于一个奇怪的位置。它是一个标准，得到了很好的支持(例如，比 Web 组件更受支持)，但是它也破坏了向后兼容性。我不想赞扬它所做的所有伟大的事情，也不想展示对你有用或没用的例子，我想邀请你参加围绕 ES6 的讨论。这就是为什么我想在这里用几个问题来结束这篇文章，供你思考，我们希望在评论中听到你的意见。

如果我们的主要目的是通过我们的编码工作来创建 web 内容，我们需要问自己几个问题:

*   我们是否让开发者的便利凌驾于最终用户体验之上？为特定环境提供更多、更快的解决方案比提供最佳性能的解决方案更重要吗？
*   在对我们来说更容易、更快、更易维护的情况下，构建对终端用户环境要求更高的大型应用程序可以吗？
*   能够更容易地更改应用程序并以更明确的方式扩展它是否弥补了锁定用户的做法？
*   是时候让 JavaScript 变得更安全、更易于扩展了吗？
*   web 持续向后兼容的概念有缺陷吗？我们是否因为迎合最小公分母而退缩了？
*   ES6 的新功能真的是一种优势，还是我们只是试图赶上并重复其他环境所做的事情？鉴于[软件作为一个整体可能会崩溃](https://medium.com/message/everything-is-broken-81e5f33a24e1)，这真的是我们所能做到的最好的吗？
*   这些变化的好处值得 it 部门花费精力去利用吗？刚进入 web 的开发者应该使用 transpilers、preprocessors 和 task runners 来开始吗？
*   当 transpilers 创建 ES5 时，如何帮助浏览器更好地支持 ES6？
*   使用 JavaScript 的子集，比如 TypeScript，是不是更好的选择？
*   把我们写的代码从浏览器得到的代码中抽象出来是正确的方向吗？view-source 已经过时了吗？
*   ES6 只是另一种在网络上工作的方式吗——很像使用 emscripten is 或 Flash was 将字节码转换成 JavaScript？对于 web 开发人员来说，预编译步骤可能看起来很奇怪，但是来自 Java 的人完全可以接受。换句话说，是不是 ES6 并不适合所有人，但我们试图让它成为那样？

接下来的几个月围绕 ES6 将会是令人兴奋的，它的本质意味着在相当长的一段时间内将会有持续的讨论和谈话。它对那些不喜欢 JavaScript 的灵活性和随机性的人很有吸引力。这让那些坚定地使用 JavaScript 的人感到困惑，感觉开销很大，却没有多少直接的好处。进化总是伴随着成长的烦恼。是时候畅所欲言，尝试适合自己的方法了。

## JavaScript 的更多实践

这篇文章是微软技术倡导者的 web 开发系列的一部分，内容涉及实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=13412-DEV-sitepoint-article36) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13412-DEV-sitepoint-article36)。

我们鼓励您使用 [dev.modern.IE](http://dev.modern.ie/tools?utm_source=SitePoint&utm_medium=article36&utm_campaign=SitePoint) 上的免费工具跨浏览器和设备进行测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article36&utm_campaign=SitePoint)
*   [在 Mac、Linux 和 Windows 上使用虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article36&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article36&utm_campaign=SitePoint)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development?utm_source=SitePoint&utm_medium=article36&utm_campaign=SitePoint)

来自我们的工程师和布道者的关于 Microsoft Edge 和 Web 平台的深入技术学习:

*   【2015 年微软 Edge 网络峰会(对新浏览器、新支持的网络平台标准以及来自 JavaScript 社区的演讲嘉宾有何期待)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=13412-DEV-sitepoint-article36)
*   使网络正常工作的边缘渲染引擎(Jacob Rossi)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge?WT.mc_id=13412-DEV-sitepoint-article36)(来自大卫·卡图赫，包括[伏龙。JS](http://vorlonjs.com/?WT.mc_id=13412-DEV-sitepoint-article36) 和 [babylonJS](http://babylonjs.com) 项目)
*   [托管网络应用和网络平台创新](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Hosted-web-apps-and-web-platform-innovations?WT.mc_id=13412-DEV-sitepoint-article36)(来自律师奶爸和基里尔·赛克谢诺夫，包括[流形。JS](http://manifold.js.com/?WT.mc_id=13412-DEV-sitepoint-article36) 项目)

### 更多面向网络平台的免费跨平台工具和资源:

*   [适用于 Linux、MacOS 和 Windows 的 Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=13412-DEV-sitepoint-article36)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422?WT.mc_id=13412-DEV-sitepoint-article36) 和[在 Azure 上免费试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13412-DEV-sitepoint-article36)

## 分享这篇文章