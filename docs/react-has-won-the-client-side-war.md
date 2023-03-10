# 忘记 Angular & Ember，React 已经赢得了客户端战争

> 原文：<https://www.sitepoint.com/react-has-won-the-client-side-war/>

*这篇文章由[尼尔森·雅克](https://twitter.com/nilsonjacques)、[克里斯·佩里](https://www.sitepoint.com/author/cperry/)和[托马斯·格列柯](https://www.sitepoint.com/author/tgreco/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在 SitePoint 的论坛上，我无意中发现了一个名为[So much Frameworks](https://community.sitepoint.com/t/so-many-frameworks/205290)的帖子，Guido 对可用的选项数量感到困惑，他问自己应该采用哪种 JavaScript 框架来使自己的应用程序更加动态。鉴于我在行业中所看到的和我自己使用过的，我声明 [React 已经赢得了客户端战争](https://community.sitepoint.com/t/so-many-frameworks/205290/28?u=pullo)。像这样的战斗词汇需要更多的解释，所以这里开始。

[React](https://facebook.github.io/react/) 不仅仅是一个炙手可热的新成员，而是一个统一的范例。它可以作为 web 应用程序的基础技术，我们可以满怀信心地在此基础上进行构建，因为我们知道它不会在下个月被更热门的同类所取代。让我们看看 React 在当前 MVC 框架中的位置，探索它的优势，并以预测 JavaScript 开发的未来走向作为结束。

## 客户端 MVC

在过去的几年里，许多聪明人一直试图建立一个完美的框架来制作[单页应用](https://en.wikipedia.org/wiki/Single-page_application)——由 JavaScript 呈现的应用，通过即时响应用户输入和数据随时间的变化，提高了感知性能。Guillermo Rauch 的文章[Rich Web Applications 的 7 个原则](http://rauchg.com/2014/7-principles-of-rich-web-applications)是为什么这很重要以及我们在构建它们时应该考虑的事情的最佳理由之一。

您可以在[到](http://todomvc.com/)找到这些类型的应用程序是如何构建的示例，顾名思义，它们传统上是由模型、视图和控制器组成的。

*React 从舞台左侧进入*

React 首次发布时，看起来有点奇怪。它只关注视图层，没有模型*或*控制器。代码示例是用一种叫做 [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) 的奇怪语法编写的，对许多人来说，这似乎是向过去的倒退，在过去，HTML 和 JavaScript 混合在一起是很常见的。

除了组件层次结构之外，没有提供任何关于如何构建应用程序的信息，组件层次结构是指可以在状态发生变化时有效地重新呈现的可组合 UI 块。React 对这个领域存在的流行观点采取了一些值得注意的立场，双向数据绑定和模板化等功能被抨击为最好避免的坏主意。

## 广泛采用

反应迅速达到临界质量。如今，很难找到与 JavaScript 相关的邮件列表、会议或聚会，而*没有*提到 React。我所在城市的所有本地开发团队似乎都在采用 React，与其他流行的框架相反，结论似乎是一致的——与我交谈过的每个人都吹捧单向数据流、组件层次结构和简单显式呈现的优点，使任务更简单，代码更可预测。

React 的采用让我很惊讶，因为 JavaScript 场景是如此的不稳定。我们很少在任何事情上达成如此广泛的一致。有一小部分人忠于一个框架，但我们中的大多数人从一个框架跳到另一个框架，一路上对引入复杂性和错误的某些模式感到沮丧。我还没有听说过一个人因为这些挫折而离开 React 的案例，自从 jQuery 出现以来，我们还没有遇到过如此明显的赢家。

## 你有一个工作反应，一个工作。

它对视图层的关注意味着它比其他试图解决所有问题的框架更少固执己见。它的 [wafer thin API](https://www.youtube.com/watch?v=4anAwXYqLG8) 意味着真的没有太多东西要学，它不保证超过几页文档。这对那些学习者来说是一个很大的帮助，它也使开发变得更简单，因为你没有太多的认知开销。只要有可能，React 就选择普通的 JavaScript 结构和语言特性，而不是定制的 API 或模板语言。

这种设计对 JavaScript 开发人员也很有吸引力，他们似乎更喜欢专注的实用程序库，如[下划线](http://underscorejs.org/)和[矩](http://momentjs.com/)，它们很好地完成了一件事，非常符合 [Unix 哲学](https://en.wikipedia.org/wiki/Unix_philosophy)。做好一件事也让对项目感兴趣的人有空间向 React 生态系统贡献他们自己的作品。因此，像 [React Router](https://github.com/rackt/react-router) 、 [Redux](https://github.com/rackt/redux) 和 [CSS 模块](https://github.com/gajus/react-css-modules)这样的项目已经出现。

## 反应组分很简单

组件负责小块孤立的 UI，它们每次都接受数据并一致地呈现。前端开发人员、后端开发人员和设计人员可以很容易地理解组件并为其做出贡献，因为它们看起来像 HTML，而[最小的 API 占用量](https://www.youtube.com/watch?v=4anAwXYqLG8)意味着您可以在一天内了解您需要了解的组件，并立即开始做出贡献。

## 服务器呈现的 HTML

React 使得服务器端呈现变得微不足道，因为组件可以被认为是获取数据并返回 HTML 的函数。这种设计很容易将服务器端呈现集成到任何服务器端编程语言或框架中。

在客户端 MVC 的早期，我们用 hashbang 路由之类的东西打破了 web，在任何东西到达屏幕之前都要花费大量的加载时间。我们还破坏了搜索引擎爬虫，在页面加载后用 JavaScript 呈现所有内容。从那以后，我们从错误中吸取了教训，重新认真对待网络的核心原则——URL、服务器渲染 HTML 和快速加载时间。React 在其他框架苦苦挣扎的地方大放异彩。

## DOM 更新很混乱

主干网是 JavaScript 的一个重要里程碑。作为将 MVC 引入客户端的第一次也是最重要的一次尝试，它向我们展示了一种构建应用程序的新方法。T2 在其文档中提到的第一件事是在 DOM 中维护你的状态是一个坏主意。当 DOM 仍然是事实的来源时，你的应用程序很快变得脆弱和难以理解。不可能知道哪段代码改变了哪个元素。Backbone 鼓励总是基于世界的当前状态重新呈现视图的理念，React 用它的单向数据流模式强化了这一理念。

React 组件不定义状态之间的转换。相反，它们基于当前状态显式地呈现视图，完全消除了手动调整 DOM 的任务。它的单向数据流阻止了 DOM 成为事实的来源。

不可否认，这使得某些任务如动画*变得更加困难*，因为在这些情况下你需要定义状态之间的转换。然而，对于绝大多数情况，更简单的做法是只关心组件应该如何呈现的最终状态。

## 未来

React 将会越来越受欢迎，我们将会看到更多的支持工具和项目。随着 React 周围生态系统的成熟，库可能会发生变化，但单向数据流、组件层次结构、显式呈现和虚拟 DOM 协调的核心思想将继续存在。

[React Native](https://facebook.github.io/react-native/) 展示了如何重新设计一个简单的视图层来构建其他类型的 UI。业界已经向这种构建 UI 的模式转变，而且不会很快消失。

总之 React 赢了，前途一片光明。

## 想法？

你同意 React 的原则将继续存在，还是我们下个月会找到一个更好的想法？

你已经开始使用 React 了吗？目前为止你的想法是什么？

请务必在下面的评论中让我知道。

## 分享这篇文章