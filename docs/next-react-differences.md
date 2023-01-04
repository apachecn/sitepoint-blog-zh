# Next.js vs React:它们的区别，以及选择哪一个

> 原文：<https://www.sitepoint.com/next-react-differences/>

在本文中，我们将从受欢迎程度、文档和性能方面比较 React 和 Next.js。您将获得一个有用的视角，了解如何根据您正在构建的应用程序的规模和预期目的在它们之间进行选择。

Web 技术在不断发展和壮大。尽管 JavaScript 生态系统在变化，但 [React](https://reactjs.org/) 和 [Next.js](https://nextjs.org/) 仍然是有价值的选择，已经存在了很长一段时间。

作为一名 JavaScript 开发人员，您可能最近才开始使用 React，或者已经使用了一段时间，并且您也在考虑 Next.js。了解每个选项的优缺点对于做出正确的选择至关重要。

我们与 [ThemeSelection](https://themeselection.com/) 合作创作了这篇文章。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

## 什么是反应？

根据官方 [React 文档](https://reactjs.org/tutorial/tutorial.html):

> React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面。它允许你用称为“组件”的小而孤立的代码片段组成复杂的 ui。

它由 Meta 和一个由个人开发者和公司组成的社区维护。它的目标是帮助开发者轻松地为网站和应用程序创建快速的用户界面。React 的主要概念是虚拟 DOM，其中 UI 的理想或“虚拟”表示保存在内存中，并通过 ReactDOM 等库与“真实”DOM 同步。可以使用 React 开发单页面、移动和服务器呈现的应用程序。

然而，React 只关心状态管理和将状态呈现给 DOM，因此创建 React 应用程序通常需要使用额外的库进行路由，以及某些客户端功能。

## Next.js 是什么？

这就是[维基百科](https://en.wikipedia.org/wiki/Next.js)介绍 Next.js 的方式:

> Next.js 是一个开源的 web 开发框架，由 Vercel 创建，支持基于 React 的 web 应用程序在服务器端呈现和生成静态网站。

Next.js 通过生产所需的所有功能为您提供最佳的开发人员体验:混合静态和服务器渲染、类型脚本支持、智能绑定、路径预取等等。不需要配置。React 文档将 Next.js 列在“推荐的工具链”中，推荐它用 Node.js 构建一个服务器渲染的网站。

Next.js 的主要特性是使用服务器端渲染来减轻 web 浏览器的负担并提供增强的安全性。为了方便开发人员，它使用基于页面的路由，并支持动态路由。其他功能包括热模块替换，以便可以实时替换模块，自动代码分割，只包括加载页面所需的代码，以及页面预取以减少加载时间。

Next.js 还支持增量静态再生和静态站点生成；网站的编译版本通常在构建期间构建，并保存为一个`.next`文件夹。当用户发出请求时，预构建的版本(静态 HTML 页面)被缓存并发送给他们。这使得加载时间非常快，但它并不适合每个网站——例如经常变化并利用大量用户输入的交互式网站。

为了制作专业且响应迅速的 web 应用，建议使用现成的 [Next.js 管理模板](https://themeselection.com/item/category/next-js-admin-template/)，尤其是在使用新框架或库时。当从头开始时，这些模板可以极大地减少开发成本和时间。

## [Materio: MUI React NextJS 管理模板](https://themeselection.com/item/materio-mui-react-nextjs-admin-template/)

Materio 构建在 Next.js 和 [MUI](https://mui.com/) 之上。除此之外，它还有 TypeScript 和 JavaScript 版本。它对开发者友好，功能丰富，有一个高度可定制的管理仪表板，用于构建优质、高性能的 web 应用程序，如 SaaS 应用程序、电子商务应用程序、健身应用程序、CRM 项目等。

为了更好地了解，请查看 [MUI 演示](https://demos.themeselection.com/materio-mui-react-nextjs-admin-template/demo-1/dashboards/crm/)。

![Screenshot of the Materio admin template](img/1a7021891c49d7e8a8328d8cddd3a69e.png)

**特点**:

*   用 Next.js 构建
*   基于 React
*   使用 MUI core v5 稳定版构建
*   100%反应钩子和功能部件
*   Redux 工具包和 React 上下文 API
*   反应钩形式加上是
*   埃斯林特和更漂亮
*   RTL(从右到左)支持
*   JWT 认证
*   深色和浅色布局
*   还有更多

## Next.js 和 React 的区别

现在，你已经对 Next.js 和 React 有了足够的了解。让我们并排比较两者，并列出所有的差异。

| Next.js | 反应 |
| --- | --- |
| 接下来是 React 的框架 | React 是一个库，不是框架 |
| 您几乎可以配置所有内容 | 不太可配置 |
| Next 以服务器端渲染和网站静态生成而闻名 | React 不支持开箱即用的服务器端渲染 |
| 使用 Next.js 构建的 web 应用非常快 | 与 Next.js 相比，使用 React 构建的 web 应用程序速度较慢 |
| 如果你知道反应会更容易 | 陡峭的学习曲线 |
| 较小但非常专注的社区 | 一个庞大的用户群体 |
| 搜索引擎优化友好的开箱即用 | 需要一些设置，使其搜索引擎优化友好 |
| 不需要离线支持 | 需要离线支持 |
| 接下来是固执己见 | React 是不独立的 |

## React 和 Next.js 的优缺点

看了上面的并列比较后，您大概就知道应该选择哪个框架了。但是让我们更详细地看看每个框架的优缺点。

### React 的优点和缺点

**React 的优势**:

*   易学易用
*   使用虚拟 DOM
*   具有可重复使用的组件
*   搜索引擎优化友好
*   提供可扩展性
*   有清晰的抽象
*   拥有一个大而有益的社区
*   拥有丰富的插件生态系统
*   提供出色的开发工具

**React 的缺点**:

*   发展速度很快
*   缺乏良好的文档
*   有滞后的 SDK 更新

### Next.js 的优缺点

**next . js 的优势**:

*   提供图像优化
*   提供国际化
*   具有零配置
*   具有快速刷新
*   混合:SSG(静态站点生成)和 SSR(服务器端呈现)
*   有 API 路线
*   提供内置 CSS 支持
*   支持类型脚本
*   提供增强的用户体验
*   是搜索引擎友好的

**next . js 的缺点**:

*   插件生态系统很差
*   没有内置的状态管理器
*   这是一个固执己见的框架
*   具有基于文件系统的路由

## Next.js 和 React 哪个好？

这并不是一种非此即彼的情况，因为 React 是一个用于构建 ui 的库，而 Next.js 是一个基于 React 构建整个应用的框架。

这个问题应该总是在应用程序/项目需求的背景下提出。

React 有时可能比 Next.js 更合适，反之亦然。以下用例将帮助您决定何时使用哪一个。

什么时候应该使用 React？:

*   当您需要高度动态的路由时
*   当你已经熟悉 JSX 的时候
*   当您需要离线支持时

**什么时候应该使用 Next.js？**:

*   当你需要一个包罗万象的框架时
*   当您需要服务器端渲染时
*   当您需要后端 API 端点时

## 结论

尽管 React 很受欢迎，但 Next.js 提供了服务器端渲染、快速加载速度、SEO 功能、基于文件的路由、API 路由和许多独特的开箱即用功能，使其在许多情况下都是非常方便的选择。您可以使用 React 实现同样的功能，它提供了更多的控制和可定制性，但是需要手动配置。

感谢阅读我们对 Next.js 和 React 的对比。我们希望您已经有了更好的理解，并会发现为您的下一个应用选择正确的技术更容易。

## 分享这篇文章