# 使用微前端的 5 个陷阱以及如何避免它们

> 原文：<https://www.sitepoint.com/micro-frontend-architecture-pitfalls/>

**我最近写了关于[值得采用微前端架构的五个理由](https://www.sitepoint.com/micro-frontend-architecture-benefits/)。当然，凡事有利有弊。微前端是一种全新的架构方法，很可能代表 web 开发的未来。同时，它们也有一些陷阱，了解它们对于能够完全解决或避免它们是至关重要的。**

在本文中，您将学到我和我的团队在使用微前端时学到的最重要的经验。在两年的时间里，我们发现了这个架构的许多问题，也犯了同样多的错误。所以，是时候分享它们来帮助你解决或避免它们了。

让我们首先回忆一下什么是微前端架构，然后深入研究它们的缺陷以及如何避免它们。

## 简而言之，微前端

[Martin Fowler](https://martinfowler.com/articles/micro-frontends.html) 将微前端开发方法定义为:

> 一种架构风格，其中独立可交付的前端应用程序被组合成一个更大的整体。

当应用于 web 开发时，它意味着让许多独立的小型前端应用程序成为同一个网站或 web 应用程序的一部分。正如这里已经提到的，我的团队已经成功地使用了这种方法。特别是，我们有机会利用它的所有优势，例如可伸缩性、技术独立性和可维护性。另一方面，从长远来看，我们注意到一些严重的问题。因此，我们决定放弃这种架构方法，回到更传统的整体架构。

这意味着我们不仅学到了微前端带来的好处，也学到了它们的主要缺点。现在让我们深入研究这些问题，看看我们应该做些什么来避免或解决它们。

## 1.冗余依赖

根据定义，每个微前端应用程序都是相互独立的。换句话说，微前端架构包含不止一个前端应用程序，它们应该能够在没有其他应用程序的情况下工作。为此，它们都有自己的依赖项。因此，从整体来看，您正在失去使用包管理器的好处。事实上，您的整个应用程序很可能由相同库的许多版本组成，分散在微前端。

这无疑是一个问题，因为它使您的 web 应用程序不必要地比它的单片对应物更大。这落在最终用户身上，他们被迫下载更多的数据。此外，这会影响渲染时间，进而影响 [Google Web Vitals](https://web.dev/vitals/) 得分，也会影响网站的 SEO。

### 如何解决这个问题

一个可能的解决方案包括三个步骤。首先，确定所有微前端的公共库集。其次，创建一个包含所有共享库的微前端。然后，更新您的微前端，使其构建的包从这个共享项目中导入所需的库。

正如 Martin Fowler 最初的博客文章中所描述的，在应用程序之间共享依赖关系存在许多障碍，并且不能被认为是一项容易完成的任务。所以，当你努力实现这个目标时，请记住这一点。

## 2.冲突和重叠的风格

同样，技术和团队的独立性是很好的，但是它也会引入一些问题。在处理造型的时候尤其如此。其实每个微前端从业务角度来说都不可能有自己的风格。这是因为您肯定不希望您的应用程序看起来由许多补丁组成。一切都应该看起来一致，无论是在风格，用户界面和 UX 方面。

同一个应用程序包含多个前端带来的另一个问题是，您可能会无意中覆盖 CSS 规则。在处理微前端时，CSS 方面不希望的重叠变得很常见，您可能会在只部署应用程序后发现它们。原因是每个团队通常只开发自己的应用程序，在部署之前看不到全局。

这些问题会对你的品牌声誉产生负面影响。此外，最终用户将为这些不一致付出代价，尤其是在 UI 方面。

### 如何解决这个问题

当涉及到 UI 和 UX 时，唯一可能的解决方案是确保每一个团队都与另一个团队交流，并在头脑中有相同的结果。此外，在前面提到的共享微前端项目中添加样式化组件可能会有所帮助。然而，这将使每个微前端应用程序依赖于它，结果破坏了底层的独立性。但至少它将防止您的应用程序作为一个整体看起来异构。

如果您想避免 CSS 重叠，一个解决方案是向前端容器`<div>`添加一个 id。然后，配置 webpack 在每个 CSS 规则之前插入这个 ID。否则，您可以决定采用 CSS 方法，如 BEM(块-元素-修改器)。这鼓励您将网站视为可重用组件块的集合，其类名在您的项目中应该是唯一的。阅读[BEM](https://www.sitepoint.com/css-architecture-block-element-modifier-bem/)简介，了解这个系统如何工作的更多信息。

## 3.性能差

在同一个页面上运行多个 JavaScript 前端应用程序会降低整个应用程序的速度。这是因为每个框架实例都需要 CPU、RAM 和网络带宽方面的资源。

此外，请记住，在测试与他人隔离的微前端时，您可能不会注意到这一点。当一个框架的多个实例同时运行时，问题就出现了。这是因为，如果它们是独立运行的，它们就不必像部署时那样共享底层机器的资源。

### 如何解决这个问题

解决这个问题的一个思路是加强团队沟通，避免做同样的电话和阐述。然后，将它们的结果存储在每个微前端可以访问的地方，或者允许它们在执行繁重的操作之前进行通信，以验证相同的数据之前是否已经被检索或生成。

此外，当涉及到性能时，您必须测试应用程序的所有微前端，不要依赖于对每个微前端单独进行的测试。

## 4.前端之间的通信

最初，您不需要让您的微前端进行通信，除非在极少数情况下。这可能会愚弄你，让你以为它会一直这样。此外，尽管微前端架构模式是关于独立性的，但这与通信是对立的。

当应用程序作为一个整体增长时，使您的微前端能够毫不费力地相互通信可能会成为一个优先事项。最重要的是，如果你想一遍又一遍地重复同样的操作，尤其是它们不是等幂的。

此外，如上所述，通信对于实现更高的性能是必要的。例如，您不希望应用程序两次调用相同的 API 来检索相同的数据，从而不必要地降低服务器的速度。

### 如何解决这个问题

解决方案是基于存储在 cookie 或 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 中的共享状态，或者基于自定义事件来实现自定义消息传递层。可以想象，实现这一点需要一定的成本，并且很快会变得复杂和难以处理。此外，要考虑到通信会带来开销。因此，你必须确保你所构建的东西会带来真正的好处，并且不会进一步降低你的应用程序的速度。

## 5.团队之间的沟通问题

大型团队中的沟通可能是个问题，但没有什么比几个团队之间的沟通更糟糕的了。这是因为让多个团队在不同的代码库上工作意味着找到可重用的特性、功能和实用程序变得更加困难。从代码可发现性和可重用性的角度来看，这是很糟糕的。换句话说，您可能很容易在不同的微前端之间重复实现相同的组件。

### 如何解决这个问题

解决方案是从一开始就支持团队之间的交流逻辑。如上所述，这涉及到为每项采用的技术准备一个具有可重用资源的项目。但是拥有这样一个项目而不保持它的最新状态会使它变得毫无用处。

因此，您必须允许每个团队向其中添加组件和库。此外，有一个专门的团队可以使整个过程更容易。事实上，对于一个独立和孤立的团队来说，理解哪些元素将被多个微前端共享可能并不容易。

而且，不要把技术独立想成几个孤立的团队。相反，让团队互相交流并保持更新是项目成功的关键。因此，在采用微前端架构时，培养沟通文化必须是关键要素之一。

## 结论

在本文中，我们研究了微前端架构方法的五个最大的陷阱，这是由我的团队在两年的日常工作中积累的经验支持的。虽然微前端方法允许开发人员将前端应用程序分成更小的独立部分，但这并不意味着每个团队也应该被隔离。相反，共享解决方案、组件、资源和知识是成功的关键。

不幸的是，作为一个团队，我们不知道这一点。因此，我们被迫放弃我们的微前端之旅。但是我们从这次冒险中学到了很多，我希望分享导致我们失败的主要原因以及如何避免或抵消它们是有用的。

感谢阅读！如有任何问题、意见或建议，请随时联系我。

## 分享这篇文章