# 2015 年需要关注的 3 个 JavaScript 库

> 原文：<https://www.sitepoint.com/3-javascript-libraries-keep-eye-2015/>

享受用 JavaScript 创造不可思议的东西？你可能会喜欢我们关于如何在我们的学习平台上[构建你的第一个流星应用](https://learnable.com/courses/your-first-meteor-application-2886?utm_source=sitepoint&utm_medium=sitepointarticle&utm_term=javascriptlibraries2015&utm_campaign=learnablerelated "Build Your First Meteor Application")，[可学习](http://learnable.com?utm_source=sitepoint&utm_medium=sitepointarticle&utm_campaign=learnablerelated "Learnable")的课程。

作为开发人员，我们都知道我们的行业发展速度非常快。确实如此之快，以至于经常很难跟上我们日常使用的所有新的库、框架和工具的新版本。尽管如此，尽可能保持最新仍然很重要。这样做可以确保我们保持高效，符合老板和客户的期望。

一个多月前新的一年开始了，一些趋势已经开始形成。在本文中，我将介绍 2015 年值得关注的三个库和框架。

## React.js

![react-logo](img/17fa3efcb7f01095e5c7f5856c7e4edd.png)

React.js ，有时简称 React，是一个用于创建用户界面的 JavaScript 库，由脸书和 Instagram 合作创建。目前，它由这两家公司在其他开发者的帮助下维护，并被雅虎、Airbnb、索尼(当然还有脸书和 Instagram)等公司使用。

React.js 不是一个完整的框架，所以它没有提供你在其他项目中可以找到的所有组件，比如 [Ember](http://emberjs.com/) 或 [AngularJS](https://angularjs.org/) 。它鼓励创建可重用的 UI 组件，这些组件呈现随时间变化的数据。许多人喜欢把 React 称为 MVC 中的 V。与使用双向数据绑定模型的 AngularJS 等框架的一个重要区别是 [React 采用单向数据绑定模型](http://facebook.github.io/react/docs/thinking-in-react.html)。

这个项目最重要的概念之一是虚拟 DOM。您可以将它视为一组可以用数据修改的元素，最终，这些元素将修改页面的真正 DOM。虚拟 DOM 用于通过使用 diff 算法来高效地重新呈现 DOM，该算法仅重新呈现改变的组件。这反过来又使库能够超快。

另一个很棒的特性是，它还可以使用 Node.js 在服务器上呈现。因此，您可以使用在客户端和服务器上获得的相同知识。这有很大的性能和 SEO 好处。许多开发人员使用 React.js 在服务器上呈现页面的第一个静态版本，这比在客户端上更快，也是 SEO 友好的。然后，他们通过在客户端使用 React.js 实现快速的用户交互和 UI 更新。

这只是对这个库的简单介绍，我真的鼓励你通过访问 [React.js 网站](http://facebook.github.io/react/)来了解更多。它包含了许多有用的教程，将在学习过程中帮助你。

## 流星

![unnamed](img/04c92f740aec9334c33628742625d810.png)

Meteor 是一个基于 Node.js 的开源 JavaScript 框架，专注于实时网络应用。它不是很新，因为它已经有了一个稳定的版本(1.0)，但最近我看到越来越多的人讨论并采用它来创建他们的应用程序。

Meteor 的主要优点之一是它将著名的 Java 格言“编写一次，到处运行”带入了 JavaScript 世界。使用 Meteor，您可以编写在客户端和服务器上运行的代码，您甚至可以通过在幕后使用 [Cordova](http://cordova.apache.org/) 将您的 web 应用程序转换为移动应用程序。这种应用程序被称为**同构应用程序**——这意味着应用程序可以同时运行客户端和服务器端。后端和前端共享相同的代码。如果你喜欢把一个流星应用程序变成一个移动应用程序，你可能想读一读大卫·特恩布尔写的文章[一个用流星进行移动开发的初学者指南](https://www.sitepoint.com/beginners-guide-mobile-development-meteor/)。

当您安装框架时，您获得了开发应用程序的客户端和服务器端所需的一切，因此启动和运行起来真的很快。Meteor 自带的 <abbr title="command line interface">CLI</abbr> 可以让你加快工作流程。它还允许您通过键入以下命令在本地计算机上创建演示应用程序:

`meteor create --example todos`

我最喜欢这个框架的一个特点是它是*反应式的*，这意味着你的数据的任何变化都会自动反映在整个应用程序的任何地方，而不需要回调。此外，正如我之前提到的，Meteor 专注于实时应用。因此，您或其他用户所做的更改会立即反映在 UI 中。

如果这篇介绍对你有吸引力，我建议你访问官方的[流星网站](https://www.meteor.com)，或者阅读文章[用流星开发你的下一个网络应用的 7 个理由](https://www.sitepoint.com/7-reasons-develop-next-web-app-meteor/)和[你需要知道的关于流星 1.0 的事情](https://www.sitepoint.com/need-know-meteor-1-0/)，这两篇文章都发表在 SitePoint 上。

## Rendr

![rendr logo](img/b6a6402e37ac7caee673fda1967c16a1.png)

最近几个月，我读了很多关于同构应用程序的文章，而 [Rendr](http://rendrjs.github.io/rendr/) 是另一个采用这一概念的库。这是一个由 Airbnb 开发的小型库[，允许你在客户端和服务器上运行 Backbone.js 应用程序。优点和 React.js 部分提到的一样:快速提供静态和 SEO 友好的 HTML 页面。](http://nerds.airbnb.com/weve-launched-our-first-nodejs-app-to-product)

轻量化是这个项目的目标之一。本文中提到的[:](http://nerds.airbnb.com/weve-open-sourced-rendr-run-your-backbonejs-a/)

> 在真正的主干风格中，Rendr 努力成为一个库，而不是一个框架。

Rendr 还试图通过最小化像`if (server) {…} else {…}`这样的代码来对环境保持不可知论，这是你会在像 Meteor 这样的框架中发现的。

不幸的是，我不知道使用这个框架构建的真实世界的应用程序(除了在链接的 Airbnb 帖子中提到的那个)，所以我不确定它是否足够成熟，可以用于生产。

## 结论

在本文中，我描述了三个非常有趣的项目，它们吸引了大量的注意力，并加入了同构应用程序的迷人潮流。

最近几周我一直在玩 React.js，我必须承认我很喜欢它。我还在学习这个库的过程中，所以我对它还没有很强的看法，需要在一个重大项目中使用它来适当地测试它。但如果它能服务于 Instagram 用户群，我相信它能在中小型应用程序中可靠地运行。我觉得有趣的是，虽然谷歌没有在 Gmail 等应用程序中使用自己的 AngularJS，但脸书却在产品中采用了 React。

您曾经使用过这些库和框架中的一个或多个吗？如果是的话，你对他们有什么体验？如果你没用过，你愿意试一试吗？

## 分享这篇文章