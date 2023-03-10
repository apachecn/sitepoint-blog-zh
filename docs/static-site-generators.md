# 静态站点生成器:初学者指南

> 原文：<https://www.sitepoint.com/static-site-generators/>

**[jam stack](https://www.sitepoint.com/learn-jamstack/)(JavaScript、API 和标记)越来越成为 Web 上首选的开发栈。Jamstack 网站上的标题暗示 Jamstack 是“构建网站和应用的现代方式”，并且它“提供了更好的性能”。**

性能当然是 Jamstack 带来的好处之一，还有更好的安全性、可伸缩性和开发人员体验。建立在这种架构上的网站利用通过 cdn 提供的预渲染静态页面，可以从多个来源获取数据，并用微服务 API 取代传统服务器及其数据库。

静态站点生成器(SSG)使得快速且相对容易地创建静态站点成为可能。

在一系列编程语言中有大量的静态站点生成器，比如 JavaScript、Ruby、Go 等等。你会在[staticsitegenerators.net](https://staticsitegenerators.net/)上找到一个详尽的、未过滤的列表，但是如果你想要更容易管理的东西，查看一下 [Jamstack 网站的列表](https://jamstack.org/generators/)，在那里你可以通过名字或者 GitHub 星的数量来过滤静态站点生成器。

在本文中，我将列出七个流行的静态站点生成器及其主要特性，这样您就可以更好地了解其中哪一个更适合您的项目。

## 什么是静态站点生成器？

一个常见的 CMS(内容管理系统)——比如 WordPress——在客户端请求时动态构建网页:它从数据库中收集所有数据，并通过模板引擎处理内容。相比之下，虽然静态站点生成器也通过模板引擎处理页面，但是它们在客户端请求页面之前处理构建过程，这意味着它们可以在客户端请求页面时提供服务。所有在网上托管的都是静态资产，这使得网站更加轻量级，服务速度更快。

要了解更多关于传统 CMS 和静态站点生成器之间的区别，以及使用 SSG 的好处，请查看 Craig Buckler 的文章“ [7 个使用静态站点生成器的理由](https://www.sitepoint.com/7-reasons-use-static-site-generator/)”。

但是，CMSs 带来的所有好东西呢，比如非开发人员的内容创建和更新，围绕内容的团队协作等等？输入无头 CMS。

无头 CMS 是只有后端的 CMS。没有显示内容的前端。它唯一的工作是管理内容，但它也提供了一个 API，另一个前端可以使用它来检索输入其中的数据。

这样，例如，编辑团队可以继续在他们熟悉的、用户友好的管理界面上工作，他们生成或更新的内容只是静态站点生成器可以通过公开的 API 访问的数据源之一。流行的无头 CMS 软件有 [Strapi](https://strapi.io/) 、 [Sanity](https://www.sanity.io/) 、[contentiful](https://www.contentful.com/)。此外， [WordPress 有一个 REST API，允许开发者将它用作无头 CMS](https://www.sitepoint.com/wordpress-headless-cms/)。

因此，现代的 Jamstack 工具使得构建静态服务的网站成为可能，并且仍然可以获得内容管理系统的好处。

现在，让我们来看看一些静态站点生成器选项。

## 1.盖茨比（姓）

![Gatsby](img/55c5c4df07ec6ae5f780ffbf52b2cc8e.png)

Gatsby 是一个用于构建静态网站和应用的成熟框架。它内置在 React 中，利用 [GraphQL](https://graphql.org/) 来操作数据。如果你很好奇，想要更深入地研究，请查看 SitePoint 上的“[盖茨比入门:建立你的第一个静态网站](https://www.sitepoint.com/gatsby-guide/)”和 Gatsby 网站上的[文档](https://www.gatsbyjs.org/docs/)。

以下是盖茨比的一些优点:

*   有了 Gatsby，你可以接触到最新的网络技术 React、webpack、modern JS、CSS 等等都为你准备好了，你只需开始编写你的站点代码。
*   Gatsby 丰富的插件生态系统允许您使用来自一个或多个来源的任何类型的数据。
*   易于部署和可伸缩性，这主要是因为 Gatsby 构建了不需要复杂设置的静态页面。
*   SBY IS A Progressive Web Apps (PWA) Generator:

    > You can get code and data partitioning out of the box. Gatsby only loads critical HTML, CSS, data and JavaScript, so the faster your site loads, the better. Once it's loaded, Gatsby prefetches resources of other pages, so clicking on the website is incredibly fast. — [Gatsby website](https://www.gatsbyjs.org/)

*   [gatsby-image](https://www.gatsbyjs.com/plugins/gatsby-image/) 将 gatsby 的原生图像处理能力与高级图像加载技术相结合，轻松全面地优化您网站的图像加载。
*   大量的入门网站可供你自由选择和定制。

## 2.Next.js

![Next.js](img/3785a61611c365ac95244ce60ad88070.png)

[Next](https://nextjs.org/) 是一个通用框架，用于创建服务器渲染或静态导出的 JavaScript 应用。它建立在 React 之上，由 [Vercel](https://vercel.com/) 创建。

要创建下一个应用程序，请在终端中运行以下命令:

```
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/learn-starter" 
```

`cd`进入`nextjs-blog`，你新创建的目录，输入命令在端口 3000 上打开你下一个 JS app 的开发服务器:

```
npm run dev 
```

要检查一切是否正常，请在浏览器中打开 [http://localhost:3000](http://localhost:3000) 。

Next.js 有很棒的文档，你可以从中了解更多关于构建和定制基于 Next 的应用的信息。

以下是 Next 的一些最佳特性:

*   默认情况下，Next 在服务器上渲染，这对性能非常有利。关于服务器端渲染的利与弊的讨论，请看亚历克斯·格里戈良在 Medium 上的文章。
*   无需设置:自动代码分割、路由和开箱即用热重装。
*   图像优化、国际化和分析。
*   伟大的[文档](https://nextjs.org/docs/getting-started)、[教程、测验和例子](https://nextjs.org/learn/basics/create-nextjs-app)让你从初学者到高级用户都可以使用。
*   内置 CSS 支持。
*   大量的[示例应用](https://github.com/zeit/next.js/tree/canary/examples)帮助你开始。

## 3.雨果

![Hugo - static site generators](img/09d2bbb837b47b1bc5fa2c9272ec75e8.png)

Hugo 是一个非常受欢迎的静态站点生成器，目前在 GitHub 上有超过 49k 颗星星。它是用 Go 编写的，并标榜自己是构建网站最快的框架。事实上，Hugo 有一个快速的构建过程，这使得构建静态网站变得轻而易举，并且非常适合有很多帖子的博客。

这些文档非常棒，在该平台的网站上，您可以找到一份精彩的[快速入门指南](https://gohugo.io/getting-started/quick-start/)，让您立即开始使用该软件。

以下是雨果最喜欢的一些特征:

*   针对速度进行了设计和优化:根据经验，每段内容大约在 1 毫秒内呈现。
*   不需要为分页、重定向、多种内容类型等等安装额外的插件。
*   丰富的主题系统。
*   短代码可作为使用降价的替代方法。
*   自 2020 年 12 月以来，Hugo 提供了 [Dart Sass](https://gohugo.io/hugo-pipes/scss-sass/#options) 支持，以及一个新的滤镜来将一个图像叠加在另一个图像之上——[Hugo 0.80:2020 年的最后一次发布！](https://gohugo.io/news/0.80.0-relnotes/)

## 4.Nuxt.js

![Nuxt.js](img/99ff7c9ef4810b9f7b902ee6127bc318.png)

Nuxt.js 是一个用 [Vue.js](https://vuejs.org/) 构建的高层框架，它让你可以创建生产就绪的 web 应用。使用 Nuxt，您可以:

*   服务器端为你的网站渲染，也叫*通用*或*同构*模式。Nuxt 使用一个节点服务器来交付基于 Vue 组件的 HTML。
*   静态站点生成。有了 Nuxt，你可以基于你的 Vue 应用建立静态网站。
*   单页应用程序。Nuxt 为您提供了构建基于 Vue 的 SPA 的配置和框架。

创建基于 Nuxt 的网站可以非常快速地完成。下面是 Nuxt 网站上的 [Hello World 示例](https://nuxtjs.org/examples)。您可以在您的机器上下载它，或者在 Codesandbox 上试用它来开始。

以下是 Nuxt.js 的一些特性:

*   卓越的性能:基于 Nuxt 的应用程序已经过开箱优化。
*   模块化:Nuxt 使用强大的模块化结构构建。有 50 多个模块可供您选择，以加快您的开发体验。
*   相对容易的学习曲线。Nuxt 是基于 Vue 的，Vue 是一个框架，可以让你快速、轻松地上手。
*   使用 Vuex 进行集成状态管理。
*   自动代码分割。
*   前沿的 JavaScript 代码翻译。
*   JS 和 CSS 的捆绑和缩小。
*   管理`<head>`元素(`<title>`、`<meta>`等)。).
*   前置处理器:Sass，Less，Stylus 等。

## 5.吉基尔博士

![Jekyll](img/8e967de3e0b7f86a060da7396fe782ec.png)

[Jekyll](https://jekyllrb.com/) 的简单性和浅的学习曲线使其成为撰写本文时 GitHub 上 42k+星的热门选择。它从 2008 年就已经存在了，所以它是一个成熟的、得到很好支持的软件。

Jekyll 是用 Ruby 打造的。你用 Markdown 写你的内容，模板引擎是流动的。它非常适合博客和其他大量文本的网站。Jekyll 也是 GitHub 页面的引擎，这意味着你可以在 GitHub 页面上免费托管你的 Jekyll 站点，“自定义域名和所有”。

Jekyll 提供的强大功能包括:

*   简单
*   GitHub 页面上的免费托管
*   出色的社区负责维护和创建主题、插件、教程和其他资源

## 6.电梯，电梯

![Eleventy JS](img/1becdf6d894de5c520bc59a8505af646.png)

Eleventy ，通常被认为是 Jekyll 的 JavaScript 替代品，介绍自己是“一个更简单的静态站点生成器”。Eleventy 构建在原生 JavaScript 上，没有框架(尽管您可以选择使用自己喜欢的框架)，具有默认的零配置设置方法，并允许您使用自己喜欢的模板引擎。

要快速使用 Eleventy，请查看 Craig Buckler 的"[Getting with Eleventy](https://www.sitepoint.com/getting-started-with-eleventy/)"，Raymond Camden 的"[11ty Tutorial:Cranking Your jam stack Blog Up to 11](https://snipcart.com/blog/11ty-tutorial)"，以及 Tatiana Mac 的"[Eleventy 初学者指南](https://tatianamac.com/posts/beginner-eleventy-tutorial-parti/)，或者前往 Eleventy 网站上的 [getting started docs](https://www.11ty.dev/docs/getting-started/) 页面。

一些不错的功能包括:

*   简单性和性能
*   社区驱动
*   灵活的模板系统
*   快速构建时间

## 7.VuePress

![VuePress](img/037b33d32347da8451947ffb4f76970d.png)

VuePress 是一款 Vue 供电的静态现场发电机。它的默认主题是为编写技术文档而优化的，所以它开箱即用，非常适合这种类型的网站。在撰写本文时，它的当前稳定版本是 1.8.0，但如果你对正在进行的突破性变化感到好奇，请查看 GitHub 上的[版本 2 alpha。](https://github.com/vuepress/vuepress-next)

VuePress 站点就像一个 SPA，它利用了 Vue、Vue 路由器和 webpack 的强大功能。

要开始使用 VuePress，您需要 Node.js v.10+和可选的 [Yarn Classic](https://classic.yarnpkg.com/en/) 。

要快速设置 VuePress，请使用 [create-vuepress-site](https://github.com/vuepress/create-vuepress-site) 生成器，在您选择的目录中打开您的终端并运行以下命令之一，具体取决于您使用的是 Npm 还是 Yarn:

Npm:

```
npx create-vuepress-site [optionalDirectoryName] 
```

纱线:

```
yarn create vuepress-site [optionalDirectoryName] 
```

回答了几个配置问题后，您应该会在您选择的文件夹中看到新的网站文件结构。

有关更多详情，请访问 [VuePress 指南](https://vuepress.vuejs.org/guide/)。

以下是 VuePress 提供的一些出色功能:

*   设置基于 VuePress 的网站很快，您可以使用 Markdown 编写内容。
*   VuePress 基于 Vue 构建，这意味着您可以享受 Vue、webpack 的 web 体验，可以在 Markdown 文件中使用 Vue 组件，还可以使用 Vue 开发自定义主题。
*   快速加载体验:VuePress 静态网站由预先渲染的静态 HTML 组成，一旦加载到浏览器中，就可以作为 SPA 运行。
*   [多语言支持](https://vuepress.vuejs.org/guide/i18n.html#site-level-i18n-config)默认用 i18n。

### Nuxt.js 还是 VuePress？

Nuxt.js 和 VuePress 都是建立在 Vue.js 之上的，可以让你创建静态网站。那么，哪一个比另一个更好呢？

假设 Nuxt.js 可以做 VuePress 做的所有事情。然而，本质上，Nuxt 最适合构建应用程序。另一方面，VuePress 是创建静态文档网站的理想选择，这些网站显示用 Markdown 编写的内容。

简而言之，如果你需要的只是一个文档网站或 Vue.js 中的一个非常简单的博客，可以考虑使用 VuePress，因为 Nuxt 可能有些过头了。

## 如何选择一个静态站点生成器

有了所有可用的选项，当选择一个符合要求的静态站点生成器时，很容易感到麻痹。有一些考虑因素可以帮助你筛选出所提供的东西。

你的项目需求应该对你应该在你的 SSG 中寻找的特性有所启发。

如果您的项目需要大量现成的动态功能，那么 Hugo 和 Gatsby 可能是一个不错的选择。至于构建和部署时间，上面列出的所有 SSG 都表现得非常好，尽管 Hugo 似乎是最受欢迎的，尤其是如果您的网站有很多内容的话。

你的项目是博客还是个人网站？在这种情况下，Jekyll 和 Eleventy 可能是很好的选择，而对于一个简单的文档网站，VuePress 将是一个很好的选择。如果你正在计划一个电子商务网站，你可能要考虑哪个 SSG 适合商店管理的无头 CMS。在这种情况下，Gatsby 和 Nuxt 可以很好地工作。

你可能要考虑的另一件事是你对每一种 SSG 语言的熟悉程度。如果你用 Go 编程，那么 Hugo 很可能是你的首选。另一方面，如果 JavaScript 是你最喜欢的编程语言，你会有很多选择:Eleventy 是用纯 JS 构建的，Next 和 Gatsby 是基于 React 构建的，而 Nuxt 和 VuePress 是用 Vue 构建的。

关于优秀的文档、强大的社区和支持，我列出的所有静态站点生成器都是最受欢迎的。

## 分享这篇文章