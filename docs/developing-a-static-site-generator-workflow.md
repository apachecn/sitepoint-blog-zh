# 开发静态站点生成器工作流

> 原文：<https://www.sitepoint.com/developing-a-static-site-generator-workflow/>

在这篇文章中， [Usersnap](https://usersnap.com/) 的技术营销人员 Thomas Peham 解释了静态网站生成器如何以及为什么可以帮助你的登陆页面工作流程。

![static site debugging workflow](img/c1a338ba744b20ed9eb2529a6adda984.png)

没人喜欢找虫子。更糟糕的是，没有人喜欢在购买数码产品时寻找漏洞。这就是为什么，作为一家向成千上万的用户提供基于网络的错误跟踪工具的公司，我们必须确保一个没有错误的环境。

随着我们网页数量的增加——因此代码行也越来越多——我们被激励去寻找改进我们内部工作流程的方法。

在这篇文章中，我想给你一些关于我们在过去几个月中改变我们的工具堆栈以及我们制作和部署新的登录页面的方式的旅程的见解。

## usersnap.com 的新工具堆栈

一旦我们决定改进 usersnap.com 登陆页面的内部工作流程，就有很多问题需要回答。使用哪种内容管理系统？哪些软件工具？以及更多的问题。

我们最终使用 Hugo 作为我们在 usersnap.com 的主要网站框架。这个决定为团队带来了全新的协作和发展方式。

![new tool stack for usersnap.com](img/428cc5bcdf6d03388f729e3c874a9506.png)

## 静态站点生成器的好处

那么 Hugo 在我们的网站设置中起到什么作用呢？Hugo 是一个开源的静态站点生成器。静态站点生成器在你创建新内容或编辑它的时候建立一个网页。

相比之下，像 WordPress 这样的内容管理系统在访问者每次请求页面时都会构建一个页面(尽管有各种缓存技术可用)。

比起 WordPress 这样的系统，选择 Hugo 这样的静态站点生成器有很多好处。这些优势不仅仅在于性能。

用 Hugo 创建的静态网页可以让你 100%控制你的内容和网页设计。如果你计划发布一个有不同布局和内容的网站，考虑一个静态的网站生成器比内容管理系统更有意义，因为你会更快更灵活。此外，你不需要理解给定 CMS 的所有特性，只需要知道 HTML 和 CSS 的基础知识。

最初，雨果是由史蒂夫·法兰克王国做的一个兼职项目。今天，Hugo 社区有超过 165 个贡献者，35 个主题和数千名用户。

我们已经将 Hugo 用于我们的附带项目 [bugtrackers.io](https://www.bugtrackers.io/) ，我们知道它也是我们想用于 usersnap.com 的框架。

## Netlify 上的静态托管

在认定像 WordPress 这样的 CMS 增加了更多的复杂性而不是好处之后，我们开始寻找静态主机。这些要求简单明了。static hoster 必须与 GitHub 配合得很好，我们需要能够建立一个部署流程，让我们公司的每个人都能够执行。

当 Divshot(我们 bugtrackers.io 的主机)被谷歌收购后关闭，我们转而使用 [Netlify](https://www.netlify.com/) 。我们对 Netlify 非常满意，所以选择它作为我们的静态登录页面也是一个自然而简单的决定。

## 使用 GitHub 进行版本控制

GitHub 已经为其他几个项目准备好了，我们知道 GitHub 将是我们管理源代码的地方。为我们的新登录页面建立一个私有存储库，并将 GitHub 与 Netlify 连接起来是非常简单的。

![github for version control](img/6bb2940718832bb583137da5ce5a781e.png)

尤其是有了 Hugo 这样的静态站点生成器，这个工作流变得更加强大。

使用命令`hugo`你可以在 Netlify 上配置 Hugo。您只需选择应该如何构建您的项目，以及应该公开托管哪个目录。

每当您将构建命令推送到 GitHub 时，Netlify 都会运行它并部署结果。

在我们的例子中，配置如下所示:

```
Repository: usersnap/landing-pages
Branch: master

Build cmd: cd src && npm install &&node_modules/bower install && node_modules/brunch/bin/brunch 
build —production && cd .. && hugo

Public folder: /public
```

(我们确实运行了额外的 JavaScript 和 CSS 预处理工具。)

## 使用 Codeship 和 Usersnap 进行测试

当谈到测试和 QA 时，大多数人似乎都在忙于回避这个话题。；)

有了 [Codeship](https://codeship.com/) (用于自动化测试)和 [Usersnap](https://usersnap.com) (用于手动测试)，我认为我们已经找到了完美的工具链，允许我们在运行中报告错误，同时拥有自动化测试的安全网。

因此，在我们的代码托管在 Netlify 之前，它将与 Codeship 一起进行测试、准备和部署。这样，我们就为静态网站建立了一个登台和生产环境。

部署完成后，我们使用 Usersnap(是的，我们吃自己的狗粮)进行人工测试和 QA，或者只是讨论新想法或收集对任何内容的反馈。

总而言之，开发工作流程看起来是这样的:

![web development workflow at usersnap](img/41ca36a55583d9bc3b159651579a30ff.png)

为了让我们团队的每个人都参与进来，我们用 Slack 连接了 Codeship 和 Usersnap。新的推送出现在专用的松弛通道中。此外，Slack 会通知开发人员新的 bug 报告和反馈。

## 管理 bug 并完成工作

获得关于新漏洞和错误的警报和通知是一回事。对它们进行优先排序、分配和修复是另外一回事。

我们的产品和开发团队依赖于产品路线图，我们称之为[特性矩阵](http://usersnap.com/blog/how-we-implement-new-product-features-backed-by-customer-feedback/)。这个特性矩阵包括我们团队正在开发的所有特性、错误修复和变更请求。

除了特性矩阵之外，我们还使用了一些内部工具来使沟通和生活变得更加容易。我们喜欢把 Slack 称为我们的家，因为它使我们能够在整个公司内相互沟通。

为了管理新项目和完成工作，我们依靠产品管理工具 [Blossom](https://www.blossom.co/) ，它让我们能够更好地了解全局。例如，我们的产品有一个 Blossom 板，它让我们对新产品需求的当前状态有一个很好的概述。我们也有针对所有静态网站项目的公告板。

## 下一步是什么？

作为一家成长中的初创公司，我们一直在想办法提高效率。在我们的开发团队中加入新人也需要我们重新思考我们的工作方式，并确定必须以不同方式完成的工作。

借助我们的新工作流程，我们找到了一种更高效地部署新登录页面的方法。

展望未来，我们认为发展是一个对成长中的公司越来越重要的话题。

将基础设施外包给像 AWS 或 Azure 这样的服务已经成为构建和扩展软件的事实标准。

因此，基础设施运营变得越来越不重要——尽管我们看到对资源、技能和工具的需求越来越多，以满足您的软件运营和服务。幸运的是，已经有各种服务可以帮助您加快开发速度。

## 分享这篇文章