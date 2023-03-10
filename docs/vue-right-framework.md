# 如何判断 Vue.js 是否是你下一个项目的正确框架

> 原文：<https://www.sitepoint.com/vue-right-framework/>

*想从头学起 Vue.js？使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具的 Vue 书籍的完整收藏&。[现在就加入，每月仅需 9 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzA3?utm_source=blog&utm_medium=articles)或[试试我们的 7 天免费试用](https://www.sitepoint.com/premium/sign-up?step=0?utm_source=blog&utm_medium=articles)。*

Vue.js 从一个人的项目成长为人人都在谈论的 JavaScript 框架。您已经从您的前端同事和会议中听说过它。你可能已经看过 Vue，React，Angular 之间的多次比较。你可能也注意到了 Vue 在 GitHub stars 上的排名。

所有这些让你想知道 Vue.js 是否是你下一个项目的正确框架？好吧，让我们来探索 Vue 的可能性和局限性，让您从高层次上了解这个框架，并让您的决策变得更容易。

*请注意，这篇文章将强烈借鉴 [Monterail](https://www.monterail.com?utm_medium=guestpost&utm_source=sitepoint&utm_campaign=vue.js) 在 Vue 和其他 JavaScript 框架方面的经验。作为一家软件开发公司，我们已经交付了大约 30 个基于 Vue 的项目，并且我们通过像 [State of Vue.js](https://www.monterail.com/state-of-vuejs-report?utm_medium=guestpost&utm_source=sitepoint&utm_campaign=vue.js) 和 [Vue Newsletter](https://www.getrevue.co/profile/vuenewsletter) 这样的项目在开发者和企业中大力宣传它。*

让我们开始吧。

## vue . js 概述

回到 2014 年，Vue.js 的第一个公版发布。它的模板语法——类似于 angular JS——和基于组件的架构——类似于 React 提供的——使它在当时很容易被 JS 开发人员接受。Vue.js 真正起飞仅仅是在一年之后，当时它被 [Laravel](https://laravel.com/) (流行的 PHP 框架)社区发现。

几年后，根据 JS State 的数据，它现在记录了所有 JS 框架中最高的满意度 (91.2%)。越来越多的开发者报告说听说过它，并希望在将来使用它。像 IBM、GitLab 和 Adobe 这样的公司已经在其产品中采用了 Vue。

[根据 Vue 的创造者尤雨溪](https://www.monterail.com/state-of-vuejs-report):

> 最初的目标是“挠自己的痒”，创建一个我喜欢使用的前端库。

显然，他和整个社区成功地完成了这项任务。但是 Vue 到底有什么特别的地方让程序员想用呢？

该项目的官方网站是这样说的:

> Vue(读作/vjuː/，类似视图)是一个用于构建用户界面的渐进式框架。与其他整体框架不同，Vue 从一开始就被设计成可增量采用的。核心库只关注视图层，很容易获取并与其他库或现有项目集成。

在那里！循序渐进，易于掌握和整合。但这足以让它成为你的首选吗？

## Vue 的商业视角

在 Monterail，我们认为对于一个 JavaScript 程序员来说，选择哪一个框架或库作为应用程序的前端应该没有太大的区别。构建非常快速、漂亮且可维护的产品一点也不简单，但是如果某些东西是可行的，那么它对于任何现代 JS 框架都是可行的。

然而，[我们热爱 Vue.js](https://www.monterail.com/blog/why-we-use-vuejs?utm_medium=guestpost&utm_source=sitepoint&utm_campaign=vue.js) 并在大多数情况下向我们的客户推荐它。这是因为 Vue 有很多优点。让我们来探索它们。

### 当你的应用充满了动画和互动元素

Vue 提供了一个真正优雅和灵活的 API——不仅用于前端的可组合架构，还用于视图之间的无缝转换。过渡和动画增强了用户体验，平滑了状态之间的变化。人类的大脑喜欢运动，所以它是现代网站和应用程序的重要组成部分。当我们需要抓住用户的注意力时，当我们想让用户在我们的网站上停留更长时间时，或者只是为了让我们的产品更有趣时，动画是关键。

Vue 2.0 的发布在过渡方面引入了更多的灵活性。我们现在可以更精确地访问过渡挂钩，这反过来使得利用第三方库和交付复杂动画成为可能，同时仍然在核心使用 Vue。这意味着在 Vue 中有很多不同的方式来制作动画。你所需要做的就是应用一个自定义属性并添加一些 CSS 魔法。Vue 为我们提供了已经内置并基于 CSS 动画的`<transition>`和`<transition-group>`组件，允许 CSS 和 JS 挂钩。它还可以很容易地与非 HTML 元素集成，比如 SVG。

在我们的投资组合中，我们有一个非常好的例子，这个项目有许多不同的过渡，其中[我们使用 Vue.js 和 Nuxt](https://www.monterail.com/blog/from-angularjs-to-vuejs?utm_medium=guestpost&utm_source=sitepoint&utm_campaign=vue.js) 协同工作。我们设法为 [Easyship](https://www.easyship.com/) 提供了一个漂亮的用户界面，比他们的 AngularJS 版本高 37%。Vue 在过渡方面令人难以置信的可能性也使它成为营销活动网站的一个好选择。 [Airbnb 的“直到我们都属于”](https://www.awwwards.com/sites/airbnb-until-we-all-belong)是另一个很好的例子——一个在六周内交付的获奖活动，完全用 Vue.js 编写

### 当您需要与多个应用程序无缝集成时

在 Vue.js 2017 年状态调查中，81%的开发人员强调易于集成是 Vue 的一个关键优势。Vue 在构建 SPAs(单页应用程序)和与现有的服务器渲染(多页)应用程序集成方面都非常出色。Vue 可以很容易地“放入”呈现在服务器上的页面中——例如，通过 Rails、Laravel 或 Express 之类的框架——以增加某种程度的交互性。想要一些快速的客户端表单验证吗？没问题！动态获取的内容？再简单不过了！这样的例子不胜枚举…

除了能够从头开始构建成熟的单页面应用程序之外，由于其 webpack 集成，Vue 还提供了一系列现代功能，如捆绑、代码分割或树抖动。在很多情况下，我们使用 Vue 只是为了在现有页面上增加一些交互性——这也同样有效。我们需要做的就是将它加载到一个`<script>`标签中，并将`Vue({ el: "#app" })`添加到 JS 文件中。这是我们的基础。然后我们可以添加 HTML 模板，插入数据，并呈现组件。

### 当你想在没有高级技能的情况下制作原型时

Vue 很酷的一点是组件可以用纯 HTML、CSS 和 JavaScript 编写。这使得它很容易开始。另一方面，我们有 React，其中 HTML 结构用 JSX 表示(Vue 也支持)。HTML 模板是一种更简单的选择，用它来阅读和构建代码感觉更自然。

这种方法使得经验较少的开发人员或设计人员更容易编写代码，甚至准备一些工作原型。Vue 备受赞誉的文档也让它对初学者或不太懂技术的企业家非常友好。即使你在某个时候陷入困境，它也会帮你走出困境。

Vue 应用程序不需要太多的设置工作。使用 [Vue CLI](https://cli.vuejs.org/) (命令行界面)可以轻松创建生产就绪的应用程序设置。不太喜欢命令行？没问题！开箱即用，Vue 在其 CLI 之上提供了一个令人惊叹的图形用户界面，称为 [Vue UI](https://morningstar.engineering/vue-ui-a-first-look-916600d9a918) 。只需几次点击，您就可以设置您的应用程序，将其配置为使用最流行的功能(如 Vuex、Vue 路由器或 TypeScript)等。除了初始应用设置的灵活性，还有越来越多的插件，让你可以轻松地将流行的库集成到你的应用中。

在视图中正确反映数据变化可能是一项艰难而棘手的任务。令人欣慰的是，Vue 通过其内置的反应系统来拯救我们——例如，React 很遗憾地缺少的东西(尽管有这个名字！).该系统在小型应用程序中非常有用，在这些应用程序中，状态相对简单，不一定需要更复杂的状态管理解决方案。因此，我们可以很容易地跟踪通过用户输入提供的数据的任何变化。

### 当你想快速运送你的 MVP 时

Vue 模板的简单性也意味着极快的编码速度——尤其是与 Angular 相比——无论是在 API 还是设计方面。使用 Vue 设置项目环境可能只需要一天时间。这对于 Angular 来说并不完全正确，它主要是为大型应用程序而设计的。

有了 Vue，您不必每次都重新发明轮子；您可以重用现有的组件来加速开发。据 Livestorm 的 Gilles Bertaux(他决定将 Vue 用于所有的前端组件，并在一个月内交付了他们的第一个 MVP):

> 我们不需要花一个月的时间像 React 一样设置好一切。Vue 让我们在一周内开始运作。如果没有 Vue，我们永远不会走到现在这一步。我百分百确定。

## 其他让 Vue 成为爆炸的事情

我要说，上面概述的案例是一种不需要动脑筋的事情，当客户向我们提出类似的问题时，我们总是推荐 Vue.js。但是这并不一定意味着 Vue 只在那些情况下有效(尽管它涵盖了我们遇到的大多数项目)。

Vue 的一个关键“卖点”是它的先进性。这意味着您可以逐步选择加入框架，在需要的地方添加元素。它使你的应用程序升级和降级更加平滑，让你在特性、团队设置和工具方面保持灵活性。这在缩放或重写应用程序时变得非常有用。

这里我想提到的最后一件事是组成 Vue 背后这个不可思议的社区的人们。多亏了他们，Vue 的堆栈变得更加一致和有序。GitHub 上只有一个组织，许多 Vue 库——包括 Vuex、Vue-Router 或 Vue press——都是由组成 Vue 核心团队的同一批人创作的。他们只有一个目标:做出最好的、使用起来令人愉快的框架。然而，React 的情况有所不同，因为脸书的利益和社区的利益并不总是完全一致的。

## 那么问题出在哪里呢？

像所有事物一样，Vue 也有我们认为的缺点。例如，它的灵活性:虽然一些开发人员享受 Vue 带来的自由，但其他人更喜欢只有一种正确的方式来构建新组件。

另一件事是类型脚本支持。虽然已经做了很多工作来改善体验，但仍有一些工作要做。[Vue 3.0 也将带来变化](https://medium.com/the-vue-point/plans-for-the-next-iteration-of-vue-js-777ffea6fabf):

> 此外，API 的设计考虑到了 TypeScript 类型推断。3.x 代码库本身将用 TypeScript 编写，并提供改进的 TypeScript 支持。(也就是说，在应用程序中使用 TypeScript 仍然是完全可选的。)

最后，许多企业决定使用 React 而不是 Vue，因为可用的 Vue.js 程序员较少(西欧和美国)。React 开发人员有更多的工作机会，市场上自然会有更多的 React 开发人员。公司可能希望通过选择更受欢迎的图书馆来缓解潜在的招聘问题。另一方面，Vue 的学习曲线真的很平滑，任何有 JavaScript 经验的开发者都可以很快跳入一个基于 Vue 的项目。我们看到没有 Vue 经验的初级员工在几个小时内就变得高效。因此，我认为这不应该成为任何业务的障碍。

## 结论

有很多来自不同形状和大小的公司的案例研究，我很肯定这个数字在未来几个月还会增加。如果你想看一些 Vue.js 在现实世界商业案例中的应用，这里有一个我最喜欢的列表:

*   [我们如何做 Vue:一年后](https://about.gitlab.com/2017/11/09/gitlab-vue-one-year-later/)git lab

*   考虑在你的下一个 Web 项目中使用 VueJS

*   [为什么我们从 Angular 2 转到 Vue.js(以及为什么我们没有选择 React)](https://medium.com/reverdev/why-we-moved-from-angular-2-to-vue-js-and-why-we-didnt-choose-react-ef807d9f4163) 作者雷佛

*   ASC Mexico 的《好的、坏的和丑陋的》

*   【Adobe、Behance、Livestorm 的更多案例研究，以及 Monterail 的 Vue.js 中的更多内容

我希望这篇文章很好地解释了为什么以及何时 Vue 是一个项目的可行选择。显然还有其他很好的框架可以很好地适应你的产品。请记住，一项技术有多时髦或受欢迎并不重要。做你的研究，因为这是做出真正明智决定的唯一途径。

## 分享这篇文章