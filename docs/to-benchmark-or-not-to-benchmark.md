# 社论:对标，还是不对标？

> 原文：<https://www.sitepoint.com/to-benchmark-or-not-to-benchmark/>

你可能已经看到了最近一些关于谷歌计划让他们的 Octane JavaScript 基准测试套件退役的头条新闻。如果你没有意识到这一点，或者没有看过标题，让我简单回顾一下。谷歌推出 Octane 来取代行业标准 SunSpider 基准。SunSpider 由苹果的 Safari 团队创建，是最早的 JavaScript 基准之一。

![Performance meter with arrow on 100%. Performance benchmark.](img/b49320a0df27e34ae16bf2468be62eba.png)

SunSpider 有两个问题。首先，它基于微基准测试(想想测试一个新数组的创建数千次)，不能非常准确地反映真实世界的使用情况。其次，SunSpider 排名在浏览器制造商中变得举足轻重，导致一些人为了更好的基准分数而不是真实程序的需要来优化他们的 JavaScript 引擎。在某些情况下，这些调整甚至会导致生产代码运行*比以前更慢*！

Octane 致力于创建更精确地模拟真实工作负载的测试，并成为衡量 JavaScript impĺementations 的标准。然而，浏览器制造商再次迎头赶上，我们看到了为 Octane 的测试量身定制的优化。这并不是说基准测试没有用。浏览器之间的竞争导致了 JavaScript 性能的全面提升。

你可能会说，这有点意思，但是这如何影响我作为开发人员的日常工作呢？当试图让人们相信框架 y 优于框架 x 时，基准常常被引用，一些人非常重视这些数字。上周，我注意到一个名为 [MoonJS](http://moonjs.ga/) 的新 UI 库正在一些新闻聚合器上运行。MoonJS 将自己定位为一个“最小的、极快的”库，并引用了一些[基准数据](http://moonjs.ga/docs/overview.html)来支持这一点。

澄清一下，我不是在这里挑 MoonJS 的毛病。这种对速度的关注非常普遍，尤其是在 UI 库中(以任何一个 React 克隆为例)。正如我们在上面 SunSpider 和 Octane 的例子中看到的，基准测试可能会产生误导。许多现代 JavaScript 视图库和框架利用某种形式的虚拟 DOM 来呈现输出。在研究不同实现的过程中， [Boris Kaul 花了一些时间研究基准测试虚拟 DOM 性能的方法](https://medium.com/@localvoid/how-to-win-in-web-framework-benchmarks-8bc31af76ce7),发现调整 VDOM 性能以在基准测试中表现良好相对容易。他的结论？“当你选择一个框架或一个库的时候，不要使用任何 web 框架基准的数字来做决定。”

在根据库声称的速度进行比较时，还有其他原因需要谨慎。重要的是要记住，像 SunSpider 一样，许多基准都是微基准；他们在为你的应用程序创建接口时，以你不可能比拟的规模测量重复的操作。

同样值得一问的是，速度对于您的特定用例有多重要。构建一个基本的 CRUD 应用程序不太可能让任何 UI 库屈服，学习曲线、可用人才库和开发人员满意度等因素也是重要的考虑因素。我在过去看到过很多关于 Ruby 对于构建 web 应用程序来说是否太慢的讨论，但是，尽管有更快的选择，很多应用程序已经并且继续用 Ruby 编写。

速度指标可能会误导人，但根据您正在构建的内容，它们也可能用处有限。正如所有的经验法则和良好实践一样，停下来想想它如何(或是否)适用于你的情况总是好的。我很想听听你的经历:你在实践中使用过不符合基准要求的软件吗？你开发过速度差异很重要的应用吗？给我留言让我知道！

## 分享这篇文章