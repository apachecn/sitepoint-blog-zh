# 开发 WordPress REST API 应用程序:入门

> 原文：<https://www.sitepoint.com/developing-a-wordpress-rest-api-app/>

*这篇关于开发 WordPress REST API 应用程序的文章最初是由 Torque 杂志发布的[，经允许在此转载。](https://torquemag.io/2016/06/developing-rest-api-app-part-1/)*

对 WordPress [REST API](http://v2.wp-api.org/) 的兴奋似乎已经持续了很长时间，但是一旦它最终登陆，对于网站所有者和开发者来说，它实际上意味着什么，我们仍然处在起跑线上。

虽然我已经写了很多关于 REST API 的 [*潜在*含义](http://torquemag.io/2016/06/4-exciting-revenue-routes-rest-api-opens/)的文章，但是到目前为止，我一直没有真正投入进去，亲自动手。随着 REST API 终于[在跑道上滑行](http://torquemag.io/2016/05/rest-api-ready/)，现在看起来是时候迎难而上，真正开始挖掘细节了。

在本系列的整个过程中，我将通过组装一个简单的 JavaScript 应用程序，使用 REST API 为其内容提供支持，来体验一下这种新的时尚。这有望成为一次紧张的学习经历，但也有望为来自非技术背景的其他人提供服务。

## 简单介绍一下我的背景

虽然 WordPress 一直是我的激情所在，但我的职业是作家和企业家。我不能假装有任何严肃的开发背景。“知道的足够危险”将是我迄今为止对我的编码经验的仁慈分类。

所以，这个系列不会像你所期望的那样，从杰克·鲁诺这样的主题专家，或者拉姆齐·拉尼尔这样的高级网页开发者[那里进行深入的探讨。我不是从一家老牌顶级数字机构的角度来看待这个问题，也不应该感到惊讶，这家机构希望](https://medium.com/front-end-developers/wordpress-with-node-react-and-graphql-part-1-introduction-ee0fc491730e#.p6mxsdjfw)[尝试最新技术](http://tri.be/blog/redux-react-and-the-wordpress-rest-api-v2)。

![Major agencies such as Modern Tribe are already working with the REST API](img/e231082e2fcc47c9b59b2f5cd4c9aa86.png)Major agencies such as [Modern Tribe](http://tri.be/) are already [working with the REST API](http://tri.be/blog/redux-react-and-the-wordpress-rest-api-v2)

不，这个系列将更符合普通 WordPress 用户的想法，他们希望通过一个实际的、探索性的项目来掌握下一代平台。

希望这种方法也能引起其他网站所有者的共鸣。这么说吧——如果你的眼睛立刻开始呆滞在其他(公认的优秀)教程的介绍上，比如下面的[，这是给你的系列:](https://medium.com/front-end-developers/wordpress-with-node-react-and-graphql-part-1-introduction-ee0fc491730e)

> 在接下来的几周，通过一系列文章，我将解释我如何在后端使用 Node.js 和 Express，一个 GraphQL 服务器连接到一个 MYSQL WordPress 数据库，该数据库使用 Apollo 获取数据并将其传输到 React 组件。不要担心，我仍然会使用可靠的 WordPress 管理界面。

有了这些警告，让我们简要回顾一下为什么现在是真正卷起袖子认真使用 REST API 的好时机。

## 为什么现在是拥抱 REST API 的时候了

早在 2015 年末，Calypso T1 的发布和马特·莫楞威格 T3 在 T2 的演讲就清楚地表明了 WordPress 世界的风向。简而言之，REST API 将成为该平台未来下一阶段的核心，开发人员必须尽快使用 JavaScript。

![The arrival of Calypso points the way to the future of WordPress](img/b30f3068c42362b65554829da552464f.png)

我们已经看到[整个会议](https://wptavern.com/a-day-of-rest-conference-successful-81-would-attend-again)致力于探索 REST API 的含义，以及[越来越大的基于它的现实世界项目](https://wptavern.com/guggenheim-org-relaunches-on-wordpress-using-the-wp-rest-api)，尽管它来的晚。从[微软到*纽约时报*](http://torquemag.io/2015/12/use-cases-wp-rest-api-wordcamp-us-edition/) ，全球的蓝筹股公司都在迫不及待地探索它的力量。

如果你是一个主题或插件开发者，你可以放心，你的绝大多数竞争对手至少已经在积极研究这个主题了。如果你是一个网站的所有者，你可以预期你的网站在未来五年或更长时间的发展将会被 REST API 所带来的可能性所显著定义。不管你怎么看，现在是登上这列特殊列车的时候了。

## 在这个系列中，我们要做的是

在这个系列中，我们将从本地安装开始，并使用 WordPress 来保存一个伟大的美国原创者的语录集——亨利·大卫·梭罗先生。随着我们的智慧之词安全地存储在 WordPress 后端，我们将通过 REST API 与它们交互，并构建一个简单的 JavaScript 驱动的前端，使用脸书的 [React 库](https://facebook.github.io/react/)以各种方式显示它们。

![We’ll be using the current plugin version of the REST API](img/ce9848407acbc69a500cad595ff421fe.png)

在这一过程中，我们将涉及替代前端解决方案、与移动应用的集成、设计技巧和调整，以及尝试第三方 API 以增加功能等主题。当我们完成时，您应该对 REST API 的实际内容有了一个更加基础和实用的认识。

我们将使用在本地开发环境上运行的 [WordPress 4.5.3](https://codex.wordpress.org/Version_4.5.3) 、作为插件的 REST API 的[版本 2、](http://v2.wp-api.org/) [React](https://facebook.github.io/react/) ，以及大量的耐心和坚持来完成我们的项目。坚持乘车，你一定会得到一大堆有用的信息！

## 结论

破解 REST API 的工具已经存在，它被世界范围内的几个主要网站用于生产，不久它将真正进入主流 WordPress 世界。这是学习它的最佳时机。

我们关于从头开发 REST API 应用程序的系列文章针对的是相对非技术型的读者，它将带你从零开始，一步到位。你不需要计算机科学学位就能跟上——只需要一点时间、耐心和毅力。

请继续关注第二部分，在这一部分中，我们将启动我们的本地站点，将一些数据放入其中，并开始研究通过 REST API 的基本读写功能。

你可以在这里阅读本系列的其他文章[。](https://torquemag.io/tag/rest-api-app/)

## 分享这篇文章