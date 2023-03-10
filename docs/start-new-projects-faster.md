# 更快地开始新项目:10 个脚手架网络开发工具

> 原文：<https://www.sitepoint.com/start-new-projects-faster/>

在本文中，我将向您展示 10 个库的列表，以帮助您更快地开始新项目。我们 SitePoint 的大多数人发现很难抗拒新的回购，我们想看看我们是否可以为有项目倾向的开发人员缩小理想的工具包。

如果你是那种喜欢经常启动新项目的开发人员，让它们进入世界，看看它们是否浮动(验证，你的营销朋友可能会这么叫)，那么你需要一个工具包。当然，如果你每年用自己的时间做一些新的东西，那么学习这些工具可能会耗费你的时间——这个工具不适合你。但是，如果你发现自己失去了兴趣，你已经想好了主页是放在一栏还是三栏，你需要所有你能得到的帮助。

我选择这篇文章中的工具是因为它们能够在尽可能短的时间内让你从零开始变成英雄。鉴于这里的范围是快速引导您的项目，我不仅包括了库，还包括了各种工具，它们在提供节省时间的搭建和强大的速度优势方面表现出色。我选择了一个项目的不同方面——你可以将其中的几个结合起来，形成一个成功的组合。

## [流星](https://www.meteor.com/)

Meteor 是一个 JavaScript 全栈框架，用于创建 web 和移动应用程序。

说到快就不能不提了。流星是速度之王。它确实把你束缚在它的规则和做事方式上，但是如果你的目标是尽快得到一个原型，Meteor 会支持你。

![Meteor - Start New Projects Faster](img/fee48742445e6cfec6ca8078ed1370c2.png)

Meteor 的一个伟大特性是它的服务器-客户端共享代码能力。这可以节省很多时间，帮助你专注于真正重要的事情。

它还附带了开箱即用的 MongoDB 和 WebSockets 集成。

## [风帆](https://sailsjs.com/)

Sails 是一个模型-视图-控制器 web 应用程序框架。这为那些已经熟悉其他 MVC 框架的开发人员减少了摩擦，不管是什么语言或平台。

Sails.js 的独特之处在于它如何通过其简化的数据访问层(与数据库无关)和 REST API 蓝图来简化创建常规现代 web 应用程序的过程，REST API 蓝图可以根据您的应用程序设计生成路线和操作。

![Sails - Start New Projects Faster](img/22bc51c6941d5278aa17fc0e372675b3.png)

其中，它具有基本的安全性和基于角色的访问控制，以及开箱即用的 WebSockets 集成，这使它成为实时应用程序的绝佳选择。

## [MongoDB](https://www.mongodb.com/) + [猫鼬](https://mongoosejs.com/)

如果你正在使用 MongoDB，那么你应该从它的忠实伙伴 Mongoose 中获益。这个库可以帮助您轻松地将数据建模到模式中，并帮助您进行造型和验证等工作。这是一个去除大量样板文件的好方法，否则会拖垮你。

![MongoDb - Start New Projects Faster](img/a54a81d616bdb7b8969d3412f6f8e4af.png)

我们可以说这个是二合一的，因为它意味着使用 MongoDB，无论您是否使用 Mongoose，这都是一个极好的选择。MongoDB 是…嗯，允许我引用他们的网站:

> MongoDB 是一个通用的、基于文档的分布式数据库，为现代应用程序开发人员和云时代而构建。

最后，他们补充道:

> 没有数据库能让你更有效率。

给你。你还需要什么？停止拖延，开始编码吧！

## [洛达什](https://lodash.com/)

Lodash 提供了一套方便的工具，让你的日常编码生活更加舒适。它帮助您进行迭代、排序、节流、去抖动，可能还有您最终会在 utils 文件中抛出的任何函数。

![Lodash - Start New Projects Faster](img/40730a5a8fba545a1abbce43a2008144.png)

Lodash 非常酷的一点是，它的所有功能都可以导入，甚至可以单独安装。没有必要用不必要的代码把你的包弄得乱七八糟。

如果你只需要节流功能，你可以这样做:

```
npm install lodash/throttle 
```

如果您认为您可能正在使用一些 Lodash 函数，但是您不确定是哪一个，并且您不想要单独安装每一个的麻烦，您可以像这样导入它们:

```
import throttle from 'lodash/throttle' 
```

这样，图书馆的其他部分就不会在你的包裹里了，从此你就可以快乐地生活了。

## [苗条的](https://svelte.dev/)

Svelte 是一个相当新的前端框架，一段时间以来一直在大肆宣传，这是有原因的。它很容易使用，如果你让你的小侄子接触它，他们可能会偷走你的工作。我不是在开玩笑；这是一个极简的、无废话的框架，它会让你想知道为什么构建前端会变得如此复杂。

不要误解我的意思:编写前端代码仍然会很复杂，但不是因为错误的原因。如果这是 web 开发的未来将带我们去的地方，那么我迫不及待地想看看接下来会发生什么。

![Svelte](img/7571f43c3c3c54fc9d829b210c242fcb.png)

苗条默认是反应性的。不需要复杂的状态管理库或虚拟 DOM 等等。它甚至不能在浏览器中运行。它将你的代码编译成一个微小的、优化的、普通的 JavaScript 文件，这就是浏览器运行的内容。所以，你看:除了超级容易使用，它也很棒。

如果你不相信我，可以看看他们网站上的例子。

## [view . js](https://vuejs.org/)+[view CLI](https://cli.vuejs.org/)

在 Svelte 之前，Vue.js 绝对是我最直白的前端框架的首选。它仍然可以在这个舞台上与其他竞争对手抗衡。Vue.js 的优势是成熟可靠。起初，它很难获得行业的信任，但我们现在已经到了没有人可以争辩的地步，Vue.js 已经赢得了它应有的位置。

![Vue - Start New Projects Faster](img/092d533dd8e61a042352f13e58ed8aa7.png)

但我们来这里不是为了 Vue.js 本身，而是为了 Vue CLI。这是迄今为止用 Vue.js 引导项目的最快方法。它解决了设置环境的所有麻烦和复杂性，并让您直接开始编写代码。

我喜欢它的是它对 TypeScript 和 Unity 测试的现成支持，最重要的是你不需要“弹出”你的应用程序来配置环境。这是最接近的竞争对手无法提供的无与伦比的长期支持保证。

## [反应过来](https://reactjs.org/) + [CRA](https://create-react-app.dev/)

在“快速”领域，Vue CLI 的一个劲敌无疑是 Create React App，也就是众所周知的 CRA。它的入门速度非常快，如果您已经熟悉 React，这可能是您更好的选择。

![React - Start New Projects Faster](img/f49a035e4b16555d2054f266fab9cba2.png)

CRA 得到了积极维护，其重点是保持您的环境平稳更新。这样做的唯一缺点是，如果您需要定制您的环境，您通常最终不得不“退出”您的应用程序来公开 webpack 配置。一旦你这样做了，CRA 团队将不再提供支持，因为你不再有一个 CRA 应用程序。

尽管如此，除非您试图构建依赖于特定配置的东西，否则您可能永远不需要“弹出”

## [Three.js](https://threejs.org/)

如果你在浏览器中构建任何需要 3D 的东西，那么 Three.js 就是你的朋友。它超级好用，有很棒的社区，贡献者多如天上的星星。Three.js 让 WebGL 变得简单。

![Three.js - Start New Projects Faster](img/e09d722be00e71426feda9a5651bfaa1.png)

Three.js 是一个轻量级的渲染库，可以很容易地与大多数项目集成，这使它成为我对 WebGL 的首选。你应该在他们的网站上查看特色项目以获得灵感。网络上的 3D 不一定只是为了游戏。您可以为客户带来全新的体验。

让我给你看几个我最喜欢的:

*   [go.pioneer.com/cornrevolution](https://go.pioneer.com/cornrevolution#)
*   [障碍-Wolff . fr/cepas-Alsace](https://hinderer-wolff.fr/cepages-alsace)

这里有一个奖励，一个音乐视频:

*   [http://www.ro.me/](http://www.ro.me/)

## [像素](https://www.pixijs.com/)

需要一些漂亮流畅的 2D 动画吗？Pixi 是一个 HTML5 创建引擎，具有超快的 2D WebGL 渲染器。

![Pixi - Start New Projects Faster](img/a8bb83e71b679464425b7969621df6cc.png)

它使用起来超级简单，你应该去看看他们的[图库](https://www.pixijs.com/gallery)看看你能创造出多少很酷的东西。如果其中任何一个看起来接近你想要达到的目标，这个库将帮助你启动你的项目。

## [包裹](https://parceljs.org/)

不要误解我，我喜欢 webpack，但是如果你觉得你已经受够了那些复杂的配置文件，你在这个世界上并不孤单。Parcel 是一个开箱即用的捆绑器，无需配置。需要的话可以自定义，但重点是不需要。默认设置正好可以让您的项目启动并运行。

![Parcel - Start New Projects Faster](img/94d165b9d68f24cd3f37d2a5bd8e9efb.png)

## 结论

正如我们所看到的，没有放之四海而皆准的方法，但是，希望您已经在这个列表中找到了适合您的特定项目的方法。当然，我没有提到许多其他好的解决方案。但是正如我最初所说的，在这种情况下，重点不是可维护性或可伸缩性，而是它们能让您以多快的速度启动和运行。

创造非凡的东西不需要太难。有时候，只是用对工具的问题。

## 分享这篇文章