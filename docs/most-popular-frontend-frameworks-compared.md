# 比较了 5 种最流行的前端框架

> 原文：<https://www.sitepoint.com/most-popular-frontend-frameworks-compared/>

**现在有大量的前端框架可用，每一个都有不同的优点和缺点。这使得你很难决定你应该花时间学习哪一个，或者哪一个最适合你的下一个项目。**

在本文中，我将比较五种最流行的前端 JavaScript 框架。我将对每一个都提供一个高层次的概述，检查它们的主要特性、工具、学习曲线以及其他要考虑的优缺点。

当然，我不能告诉你哪一个是最好的框架:这是主观的，取决于你当前的 JavaScript 经验水平和你正在开发的应用程序类型。不过，我要做的是让您快速了解主要参与者，并帮助您做出明智的决定，即您应该开始深入研究哪个框架。

## 我是如何衡量受欢迎程度的？

流行度是根据 JavaScript Survey 2020 的[状态由框架使用决定的。该调查由 23，765 名受访者完成，调查结果如下:](https://2020.stateofjs.com/en-US/technologies/front-end-frameworks/)

1.  反应:80%
2.  角度:56%
3.  vista .联署材料:49%
4.  苗条:15%
5.  预测:13%

我还考虑了来自同一调查的“框架意识”:

1.  反应:100%
2.  角度:100%
3.  vista . js:99%
4.  烬:88%
5.  苗条:86%

根据 2020 年的[堆栈溢出开发者调查，我将这些结果与框架使用进行了交叉引用。这是由 65，000 名受访者完成的，它很大程度上证实了 JavaScript 的发现——尽管不幸的是，它没有区分前端和后端框架。](https://insights.stackoverflow.com/survey/2020#technology-web-frameworks-all-respondents2)

当然，还有许多其他指标可以利用，比如工作可用性、GitHub 星级、npm 下载量、GitHub“使用者”等等。如果你想了解其中的一些(至少对三大巨头而言)，请[查阅 GitHub 要点](https://gist.github.com/tkrotoff/b1caa4c3a185629299ec234d2314e190)。

对于框架如何相互堆叠的交互式视图，您可以参考 npmtrends 的图表。

## 我们如何定义前端框架？

房间里的大象是列表中最流行的框架(React)将自己定义为“库”。

我不想深入这个兔子洞，因为有[篇完整的文章](https://betterprogramming.pub/libraries-vs-frameworks-whats-the-difference-5f28c53dcffe)致力于解释框架和库之间的区别。出于本文的目的，我将使用马丁·福勒提供的以下定义[:](https://martinfowler.com/bliki/InversionOfControl.html)

> 库本质上是一组你可以调用的函数，现在通常被组织成类。每个调用都做一些工作，并将控制权返回给客户端。
> 
> 框架体现了一些抽象的设计，内置了更多的行为。为了使用它，你需要通过子类化或者插入你自己的类，把你的行为插入到框架的不同地方。然后框架的代码在这些点上调用你的代码。

在我看来，React 更符合框架的行为，而不是库的行为。虽然从技术上来说它不是一个，但开发者通常会从它的生态系统中采用一些工具和软件包来使它像这样工作。

## 1.反应

![](img/ae2e75216e189dfee2aae958f259683b.png)![](img/13e7579800ecfbfbf824eb7fe3df0d58.png)![](img/d78b20fd01077c134bc4c69fc385f07b.png)![](img/fa9dc56e2ea225757ce0f9d95879d4af.png)

*   网址:[https://reactjs.org/](https://reactjs.org/)
*   GitHub:[https://github.com/facebook/react/](https://github.com/facebook/react/)

React 最初由脸书在 2013 年发布，是目前最受欢迎的前端 JavaScript 框架。React 被脸书、网飞和 Airbnb 等公司用于生产，它拥有大量的开发者——这意味着很容易在网上找到帮助和资源。

React 的主要目的是用可重用的组件组成交互式用户界面。它使用[JSX](https://reactjs.org/docs/introducing-jsx.html)(JavaScript 的语法扩展)进行模板化，并实现了单向数据流模型来用数据填充组件。每当组件数据发生变化时，React 使用其[虚拟 DOM](https://reactjs.org/docs/faq-internals.html) 快速有效地更新页面。

开发者工具是好的。React 团队已经构建并维护了一个 CLI ( [Create React App](https://create-react-app.dev/) )来快速轻松地搭建一个新项目，以及一个针对 Chrome 和 Firefox 的[开发者工具扩展](https://www.npmjs.com/package/react-devtools)。有很多第三方包可以用来完成各种各样的任务(如路由、处理表单和动画)，还有几个基于 React 的框架，如 [Next.js](https://nextjs.org/) 和 [Gatsby](https://www.gatsbyjs.com/) 。

React 信奉“一次学习，随处书写”的理念。它可以使用 [React Native](https://reactnative.dev/) 为移动应用提供支持，并且可以使用 Node 在服务器上进行渲染。这意味着出色的 SEO 支持，随着所谓的[服务器组件](https://reactjs.org/blog/2020/12/21/data-fetching-with-react-server-components.html)的出现，这种支持只会变得更好。

对 React 的一个主要批评是，它太不独立了:它只关心应用程序的视图层，而把其他事情都留给了开发人员。有些人喜欢这种自由，但其他人——尤其是新开发人员——可能会被这种鼓励 React 应用程序编码的非结构化方法所淹没。

React 有一个适中的学习曲线。它鼓励使用各种函数式编程范例(例如不变性和纯函数)，这意味着开发人员在尝试构建任何严肃的东西之前，最好对这些概念有一个基本的了解。

如果你对 React 的非个人化方法感到满意，并且它将开发过程的相当一部分留给了开发人员，那么对于任何规模的数据驱动应用程序来说，它都是一个极好的选择。

## 2.有角的

![](img/9dd36cde0b6257c89705759737b470ac.png)![](img/c2f512e6716bde469c385c0c9a18c53e.png)![](img/aea34d13273203aa306a7036723443c5.png)![](img/0af6e6b7ee87360e0c5cf041822b2abd.png)

*   网址:[https://angular.io/](https://angular.io/)
*   github:https://github . com/angular/angular/

Angular 是 Google 在前端框架领域提供的产品。它在 2010 年以 AngularJS(或 Angular 1)的名字诞生，并立即获得了成功，主要是因为它是第一个允许开发人员构建我们现在称为单页应用程序的框架。

为了解决性能问题和构建大规模 JavaScript 应用程序的挑战，谷歌从头重写了 AngularJS，并在 2016 年发布了 Angular 2(现在只是 Angular)。两者之间没有简单的移植路径，因此 AngularJS 和 Angular 成为两个独立的框架。AngularJS 现已寿终正寝，*不应用于新项目。*

至于 Angular，它在前端框架世界中算是重量级的了。它被谷歌和微软等公司用于生产，所以肯定是久经考验的。网上也有很多资源(比如优秀的[英雄之旅教程](https://angular.io/tutorial/))还有很多关于栈溢出的[角度相关问题](https://stackoverflow.com/questions/tagged/angular)。

与只处理视图层的 React 不同，Angular 为构建单页面客户端应用程序提供了完整的解决方案。角度组件可以实现双向数据绑定，这允许它们在父组件和子组件之间同时监听事件和更新值。模板是 HTML 的块，允许使用特殊的语法来利用 Angular 的许多特性。TypeScript 是 Angular 开发的主要语言，这使得该框架特别适合企业级应用程序。

明面是好的。Angular 提供了一个高度完善的 CLI 来初始化、开发、搭建和维护 Angular 应用程序。还有 [Chrome 和 Firefox 开发工具扩展](https://augury.rangle.io/)可用于调试 Angular 应用程序。Angular 有一个现成的解决方案来处理许多常见的任务，如表单和路由，但仍然有一个丰富的第三方库的[生态系统。](https://angular.io/resources?category=development)

在我看来，Angular 是这里列出的所有框架中学习曲线最陡的。开发人员需要熟悉 TypeScript，以及 decorators 和依赖注入等概念，以便能够有效地使用框架。正因如此，对于新开发人员来说，这不是一个好的选择。相反，它更适合作为团队的一部分来构建大型应用程序。

如果你想全面了解 React 和 Angular 之间的区别，请参见“ [React vs Angular:深入比较](https://www.sitepoint.com/react-vs-angular/)”。

## 3.view . js-检视. js

![](img/c0b31d5258aaae5bb771ceb55cbf7df5.png)![](img/9d4bb7218f8a8bb2d587e3a1092fe4b7.png)![](img/e1cb7d60fed8cf470b65b6bc6a3f163c.png)![](img/3603dfcf024db673b06b534c595943f3.png)

*请注意，这些数据是针对 Vue v2 的。版本 3 可用，但必须作为`vue@next`安装。*

*   网址:[https://vuejs.org/](https://vuejs.org/)
*   GitHub:[https://github.com/vuejs/vue](https://github.com/vuejs/vue)

第三个是 Vue.js，这是一个[模型-视图-视图模型(MVVM)](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel) 前端框架，用于构建用户界面和单页应用程序。它由[尤雨溪](https://twitter.com/youyuxi)创作，并于 2014 年首次发布。Vue 有一群非常忠实的开发者(例如，它的 GitHub 明星比 React 多)，这可能是因为当它被重写为 AngularJS 时，它非常好地填补了 Angular js 留下的空白。

Vue 由大约 20 名开发人员组成的核心团队开发和维护，尽管它没有得到互联网巨头的直接支持，但它被阿里巴巴、Gitlab 和 Adobe 等公司用于生产。Vue 可以说是列表中所有框架中最好的文档，它的[论坛](https://forum.vuejs.org/)是获得编码问题帮助的绝佳资源。Vue 在 PHP 世界也很流行，并作为 Laravel 框架的一部分发布。

Vue 的一个卖点是，它是从头开始设计的，可以逐步采用。这意味着你可以在普通网页中加入 Vue 来增强它们的功能，或者你可以全力以赴，用它来构建一个成熟的单页应用。Vue.js 使用一种基于 HTML 的模板语法，它允许您毫不费力地将属性绑定到底层数据模型。它还提供了单个文件组件，将模板、JavaScript 代码和作用域 CSS 保存在同一个文件中。

围绕 Vue 的工具是极好的。有一个官方的 CLI 来移植和开发 Vue 应用，还有一个 T2 的 devtools 扩展来帮助 Chrome 和 Firefox 进行调试。与 React 形成鲜明对比的是，Vue 为[路由](https://github.com/vuejs/vue-router)和[状态管理](https://github.com/vuejs/vuex)提供了官方包，这呈现了一种令人愉快的标准化做事方式。还有各种各样的第三方工具，以及基于 Vue 的框架，比如 [Nuxt.js](https://nuxtjs.org/) 和 [Gridsome](https://gridsome.org/) (Vue 对 React 的 Next.js 和 Gatsby 的回答)。

使用 Vue 的门槛很低，部分原因是它构建应用程序的渐进方法，部分原因是它基于 HTML、CSS 和 JavaScript——任何开发人员都应该熟悉这些技术。

Vue 是各种规模应用的绝佳选择。它适合于经验不足的开发人员，以及那些喜欢从他们的框架中得到更多结构和指导的人。

## 4.苗条的

![](img/72b2f3ff4fda97235188979ff754c2b6.png)![](img/15b202f99979eef6ed43390669337f89.png)![](img/a2633554f762f147f8d6ec57e63896f5.png)![](img/ed3e53a44f55febddb20f138d6a94527.png)

*   网址:[https://svelte.dev/](https://svelte.dev/)
*   GitHub:[https://github.com/sveltejs/svelte/](https://github.com/sveltejs/svelte/)

Rich Harris 在 2016 年发布的 Svelte 相对来说是框架领域的新人，它采用了一种不同的方法来构建 web 应用程序。其[网站声明](https://svelte.dev/tutorial/basics):

> Svelte 在构建时将您的应用程序转换成理想的 JavaScript，而不是在运行时解释您的应用程序代码。这意味着你不用为框架的抽象付出性能代价，当你的应用首次加载时也不会招致损失。

换句话说，它避开了虚拟 DOM 的概念，而倾向于在构建期间将代码编译成小型的普通 JavaScript 模块，这将在应用程序的状态发生变化时更新 DOM。正如你所想象的，这使得应用程序运行速度更快，占用空间更小。Svelte 还处理本机状态管理，并提供开箱即用的反应。

不幸的是，工具目前有点棘手。最初， [Sapper](https://sapper.svelte.dev/) (一个建立在 Svelte 之上的 app 框架)被用来构建具有预定义结构的 Svelte 应用，并为其配备稍微高级一些的功能，如路由和服务器端渲染。然而，在 2020 年 11 月，Svelte 的创造者[宣布](https://www.youtube.com/watch?v=qSfdtmcZ4d0)Sapper 的 1.0 版本将永远不会发布，并且 [SvelteKit](https://kit.svelte.dev/) 现在是开始用 Svelte 构建应用程序的唯一推荐方式。也有适用于 Chrome 和 Firefox 的浏览器开发工具扩展，以及各种第三方模块，尽管远不及那些更成熟的框架。

尽管 Svelte 的学习曲线非常低，但社区仍然很小，而且它还没有获得与这里提到的前三个框架相同的牵引力。但它正被 IBM 和《纽约时报》等公司用于生产，这绝对是一个值得在未来几个月或几年关注的框架。

对于较小的项目来说，Svelte 是一个很好的选择，主要是因为它还不成熟。不过，这种情况正在改变。SvelteKit 正处于公开测试阶段，这个社区正在继续发展壮大。虽然苗条目前是一个新人，你应该看看这个空间…

## 5.Ember.js

![](img/ab7f2166e897eb89463d3edf99946c0d.png)![](img/6ca581be51697125efb10086c7db0a58.png)![](img/e8ffdabb879597a5931cb65644e30484.png)![](img/4d9a1277cffbfd6074efa60be5f8c964.png)

*   网址:[https://emberjs.com/](https://emberjs.com/)
*   GitHub:[https://github.com/emberjs/ember.js/](https://github.com/emberjs/ember.js/)

我将 Ember 作为本文的最终框架，因为它在前端框架的早期就已经出现了。它最初于 2011 年发布，但在开发人员中保持了持续的流行:

> 它已经有将近十年的历史了，可以追溯到 React、Vue、Svelte 和所有其他品牌出现之前。该框架从未站在前端宣传列车的最前沿，但却悄悄地让团队稳步、可持续地发展——其中包括 2020 年欧洲 50 大金融科技公司中的两家 [Qonto](https://qonto.com/) 和 [CLARK](https://clark.de/)

与 Angular 类似，Ember 采取了更多的电池内置应用程序开发方法，并提供了构建现代前端 JavaScript 应用程序所需的一切。这包括从路由解决方案到[数据层](https://guides.emberjs.com/release/models/)，再到默认情况下内置于每个应用中的全功能现代测试工具。它遵循六周的发布周期(当一个新的次要版本发布时),并且对稳定性有很强的承诺。对于那些负担不起不断重写应用程序以避免被更快发展的框架甩在后面的开发者来说，这可能是一个真正的加分点。

各种各样的工具围绕 Ember 涌现，从[Ember CLI](https://cli.emberjs.com/release/)——创建、构建、测试和服务 Ember 应用程序的官方方式——到 [Ember Inspector](https://github.com/emberjs/ember-inspector) ，官方支持的浏览器插件，使您能够检查应用程序中的 Ember 对象。还有许多第三方库可用，CLI 提供了一种通用格式(也称为 Ember Addons)来分发它们。

Ember 的社区不像 React 和其他人的社区那么大，但是它的成员非常积极，有[论坛](https://discuss.emberjs.com/)和[不和谐服务器](https://discord.gg/emberjs)，你可以在那里寻求编码问题的帮助。Ember 是这里列出的框架中最固执己见的，它采用了“约定胜于配置”的方法。这一点，再加上开发人员需要熟悉更高级的概念——比如序列化器和适配器——给了它一个适中到陡峭的学习曲线。

Ember 可能不是初学者或小型项目的最佳选择。它有许多移动的部分，并且在如何构建事物方面没有提供太多的灵活性。但是当作为团队的一部分用来构建丰富、复杂的前端应用程序时，它确实会大放异彩。

## 结论

这就是我对当今市场上最流行的五种前端框架的比较。虽然这并不是对每个框架特性的详尽介绍，但我希望它能让您深入了解哪些大玩家可能非常适合您的下一个项目，或者是您进一步探索的好人选。

如果您有任何问题或意见，欢迎加入我们的 [SitePoint 论坛](https://www.sitepoint.com/community/)进行讨论。

## 分享这篇文章