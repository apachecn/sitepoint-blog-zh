# 优化 CSS: ID 选择器和其他神话

> 原文：<https://www.sitepoint.com/optimizing-css-id-selectors-and-other-myths/>

**在今天的典型场景中，普通网站通过 3G 在中型 Android 设备上运行，传输 500KB 的 gzipped JavaScript 和 1.5MB 的图像，往返时间为 400 毫秒，CSS 选择器的性能是我们最不关心的问题。**

尽管如此，关于这个话题还是有一些要说的，尤其是剔除一些围绕着它们的神话和传说。所以让我们开始吧。

## CSS 解析的基础

首先，达成共识——本文不是关于 CSS 属性和值的性能。我们在这里讨论的是选择器本身的性能成本。我将重点关注闪烁渲染引擎，特别是 Chrome 62。

选择器可以分成几组，并从最便宜到最贵(大致)排序:

| 等级 | 类型 | 例子 |
| --- | --- | --- |
| 1. | 身份证明 | `#classID` |
| 2. | 班级 | `.class` |
| 3. | 标签 | `div` |
| 4. | 一般和相邻兄弟姐妹 | `div ~ a`，`div + a` |
| 5. | 子代和后代 | `div > a`，`div a` |
| 6. | 普遍的 | `*` |
| 7. | 属性 | `[type="text"]` |
| 8. | 伪类和元素 | `a:first-of-type`，`a:hover` |

这是否意味着您应该只使用 id 和类？嗯……不尽然。看情况。首先，让我们来看看浏览器如何解释 [CSS 选择器](https://www.sitepoint.com/css-selectors/)。

浏览器从右向左读取 CSS。复合选择器中最右边的选择器称为**键**选择器。例如，在`#id .class > ul a`中，键选择器是`a`。浏览器首先匹配所有的按键选择器。在这种情况下，它会找到页面上与`a`选择器匹配的所有元素。然后，它会找到页面上的所有`ul`元素，并将`a`过滤到那些是`ul`的后代的元素——依此类推，直到它到达最左边的选择器。

因此，选择器越短越好。如果可能的话，确保键选择器是一个类或一个 ID，以保持它的快速性和明确性。

## 衡量绩效

Ben Frain 在 2014 年创造了一系列测试来衡量选择器的表现。测试包括一个巨大的 DOM，其中包含 1000 个相同的元素，并测量解析各种选择器的速度，从 id 到一些非常复杂和长的复合选择器。他发现最慢和最快的选择器之间的时间差约为 15 毫秒。

然而，那是 2014 年的事了。从那时起，事情发生了很大的变化，在不断变化的浏览器环境中，记忆规则几乎是无用的。永远记住做你自己的测试，特别是当性能被关注的时候。

我去做了自己的测试，为此我使用了 Paul Lewis 的测试，在 Paul Irish 的评论中提到，表达了对有用但令人费解的“[数量选择器](https://alistapart.com/article/quantity-queries-for-css)的关注:

> 这些选择器可能是最慢的。比类似“div . box:not(:empty):last-of-type”这样的狂野动作要慢 500 左右。标题”。测试页 http://jsbin.com/gozula/1/quiet

测试增加了一点，达到 50000 个元素，你可以[自己测试](https://codepen.io/ivancuric/pen/ZaWxqV)。我在我的 2014 款 MacBook Pro 上平均运行了 10 次，得到的结果如下:

| 选择器 | 查询时间(毫秒) |
| --- | --- |
| `div` | 4.8740 |
| `.box` | Three point six two five |
| `.box > .title` | 4.4587 |
| `.box .title` | 4.5161 |
| `.box ~ .box` | 4.7082 |
| `.box + .box` | 4.6611 |
| `.box:last-of-type` | Three point nine four four |
| `.box:nth-of-type(2n - 1)` | 16.8491 |
| `.box:not(:last-of-type)` | 5.8947 |
| `.box:not(:empty):last-of-type .title` | 8.0202 |
| `.box:nth-last-child(n+6) ~ div` | 20.8710 |

当然，结果会有所不同，这取决于您使用的是`querySelector`还是`querySelectorAll`，以及页面上匹配节点的数量，但是`querySelectorAll`更接近 CSS 的真实用例，它针对所有匹配元素。

即使在这样一个极端的情况下，有 50000 个元素要匹配，并且使用一些非常疯狂的选择器，比如上一个，我们发现最慢的是大约 20 毫秒，而最快的是简单类，大约 3.5 毫秒。没有太大的区别。在一个实际的、更加“驯服”的 DOM 中，大约有 1000 到 5000 个节点，您可以预期这些结果会下降 10 倍，从而使它们的解析速度达到亚毫秒级。

从这个测试中我们可以看到，担心 CSS 选择器的性能是不值得的。只是不要过度使用伪选择器和真正长的选择器。我们还可以看到 Blink 在过去两年中的改进。与“数量选择器”(`.box:nth-last-child(n+6) ~ div`)相比，“疯狂选择器”(`.box:not(:empty):last-of-type .title`)的速度下降了约 500 倍，我们只看到了约 1.5 倍的速度下降。这是一个惊人的改进，我们只能期待浏览器变得更好，使 CSS 选择器的性能更少影响。

然而，你*应该*尽可能坚持使用类，并采用某种命名空间约定，如 BEM、SMACSS 或 OOCSS，因为这不仅有助于网站的性能，而且极大地有助于代码的可维护性。过度限定的复合选择器，尤其是与标签和通用选择器(如`.header nav ul > li a > .inner`)一起使用时，是非常脆弱的，并且是许多不可预见错误的来源。维护它们也是一场噩梦，尤其是当你从别人那里继承代码的时候。

## 质量胜于数量

简单地拥有昂贵的选择器的一个更大的问题是拥有大量的选择器。这就是所谓的“风格膨胀”，你可能已经见过很多这样的问题了。典型的例子是导入整个 CSS 框架(如 Bootstrap 或 Foundation)的网站，而只使用了不到 10%的 CSS。另一个例子是在旧的、从未重构过的项目中看到的，它们的 CSS 已经退化为，我喜欢称之为“按时间顺序排列的样式表”——随着项目的变化和发展，CSS 在文件的末尾附加了大量的类，现在看起来更像一个杂草丛生的花园。

不仅一个大的 CSS 文件需要更长的时间来传输，(而且网络是网站性能的最大瓶颈)，它们也需要更长的时间来解析。除了从 HTML 构建 DOM 之外，浏览器还需要构建一个 CSSOM (CSS 对象模型)来与 DOM 进行比较，并匹配选择器。

所以，保持你的风格简洁干爽，不要把所有东西和厨房水槽都包括在内，在你需要的时候装上你需要的东西，如果需要的话就用 [UNCSS](https://github.com/giakki/uncss) 。

如果你想更深入地了解浏览器是如何解析 CSS 的，[看看妮可·沙利文在 Webkit 上的帖子](https://calendar.perfplanet.com/2011/css-selector-performance-has-changed-for-the-better/)，[伊利亚·格里戈利克关于它如何闪烁的文章](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model)，或者[林·克拉克关于 Mozilla 新的 Stylo CSS 引擎的文章](https://hacks.mozilla.org/2017/08/inside-a-super-fast-css-engine-quantum-css-aka-stylo/)。

## 房间里的大象:风格失效

到目前为止，我们所讨论的都很好，但是我们只讨论了一个渲染过程。今天的网站不再是静态的文档，而是类似于用户可以与之互动的动态内容的应用程序。

这使事情变得复杂，因为解析 CSS 只是浏览器呈现管道中的一个步骤。下面是一个面向渲染的视图，展示了浏览器如何将单个帧渲染到屏幕上(来源: [Google](https://developers.google.com/web/fundamentals/performance/renderiimg/intro/frame-full.jpg) ):

![The browser rendering pipeline](img/f5d8fc56ef9599f0961402732be58ff8.png)

我们不会讨论 JavaScript 性能和合成，而是将重点放在紫色部分——样式解析和元素布局。

在构建了 DOM 和 CSSOM 之后，浏览器需要将这两者组合成一个渲染树，然后最终在屏幕上绘制它。在这一步中，浏览器需要计算出每个匹配元素的 CSS。你可以在开发者工具的**元素>样式**面板中看到这一点。它采用所有匹配的样式、层叠和特定于浏览器的用户代理样式来构造元素的最终计算 CSS。

然后，它可以进入布局(也称为回流)步骤，在该步骤中，它计算几何图形，并构造页面的方框模型，将每个元素放在视口中各自的位置上。布局是这个过程中计算量最大的部分。

最后，浏览器在绘制阶段将渲染树中的每个节点转换为屏幕上的实际像素。

现在，当我们通过*改变*页面上的一些类，添加或删除一些节点，修改一些属性，或者以任何方式搞乱 HTML ( [或样式表本身](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet))来改变 DOM 时，会发生什么呢？

我们使计算的样式无效，浏览器需要使匹配的选择器树中的所有无效。虽然今天的浏览器更加智能，但过去的情况是，如果您在`body`元素上更改了一个类，所有的后代元素都需要重新计算它们的计算样式。

避免这个问题的一个方法是降低选择器的复杂性。不用写`#nav > .list > li > a`，用单个选择器，比如`.nav-link`。这样，您减少了样式无效的范围，因为如果您修改了`#nav`中的任何内容，您将不会触发整个节点的重新计算。

另一种方法是缩小范围——比如无效元素的数量。具体到你的 CSS。请记住这一点，尤其是在制作动画时，浏览器只有大约 10 毫秒的时间来完成所有需要的工作。

如果你想深入了解样式失效的本质细节，我推荐阅读 Blink 中的[样式失效。](https://docs.google.com/document/d/1vEW86DaeVs4uQzNFI5R-_xS9TcS1Cs_EUsHRSgCHGu8/edit)

## 结论

综上所述，你不应该担心选择器的性能，除非你*真的*过分了。虽然这个话题在 2012 年风靡一时，但自那以后，浏览器已经变得*更快、更智能了。就连谷歌也不再担心这个问题了。如果你查看谷歌的[页面速度洞察页面](https://developers.google.com/speed/docs/insights/rules)，你不会看到“使用高效 CSS 选择器”[的规则，该规则已于 2013 年被删除。](https://web.archive.org/web/20130330123924/https://developers.google.com/speed/docs/insights/rules)*

相反，重点是让你的 CSS 易于维护和阅读。你的同事和未来的你会为此感谢你。尝试通过仅包含使用的样式来优化 CSS 交付。之后，了解渲染管道。与选择器不同，样式本身*可能*很昂贵，而[一个花哨的网站和一个流畅的网站之间的差异](https://www.sitepoint.com/check-css-animation-performance-with-the-browsers-dev-tools/)通常可以在 CSS 的实现方式上找到。

最后一点:永远做你自己的测试。

不要只相信几年前网上有人写的东西。景观正在以令人难以置信的速度急剧变化。今天相关的东西可能会比你想象的更快过时。

## 分享这篇文章