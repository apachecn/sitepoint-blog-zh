# 不使用静态站点生成器的 7 个理由

> 原文：<https://www.sitepoint.com/7-reasons-not-use-static-site-generator/>

静态站点生成器(SSG)很受欢迎，并且提供了许多好处，但是本文讨论了为什么它们不适合替代内容管理系统(CMS)的原因。

在我之前的文章中，我们讨论了您的站点如何从使用静态站点生成器中获益:

*   静态站点是包含在基本 HTML 文件中的页面集合。您可以在文本编辑器中手写这些内容，但是管理资产和重复的元素(如导航)可能会有问题。
*   一个**内容管理系统**在数据库中存储页面内容，并提供编辑和应用主题的工具。管理变得更加容易，但代价是灵活性、性能、服务器要求、安全性和备份。
*   一个 [**静态站点生成器**](https://www.sitepoint.com/static-site-generators/) 是使用手工编码的静态站点和完整 CMS 之间的折衷。您可以使用原始数据(如降价文件)和模板一次性生成完整的网站。生成的文件集被传输到您的实时服务器。
*   术语“jam stack”(JavaScript、API 和标记)用于静态站点。它指的是框架、无服务器功能和相关工具的兴起，这些工具生成静态文件，但允许添加复杂的交互性。

流行的静态站点生成器有[杰基尔](https://www.sitepoint.com/set-jekyll-blog-5-minutes-poole/)、[十一](https://www.sitepoint.com/getting-started-with-eleventy/)、[盖茨比](https://www.sitepoint.com/gatsby-guide/)、[雨果](https://www.sitepoint.com/premium/books/a-beginner-s-guide-to-creating-a-static-website-with-hugo/read/1)和[金属匠](https://www.sitepoint.com/create-static-site-metalsmith/)。SSG 适用于大多数语言；更多信息见 [StaticGen](http://www.staticgen.com/) 。

SSG 似乎提供了 CMS 和静态世界的优点，但是它们可能不适合每个项目…

## 1.你只能靠自己了

如果没有一些开发专业知识，使用静态站点生成器不会有什么进展。这个过程比使用 CMS 更困难，资源更少，你可能很难找到预建的插件和模板。

对比一下 WordPress。非技术用户可能需要安装帮助，但是一旦安装完成，他们就可以编辑一个站点并安装成千上万个可用主题和插件中的一个。他们可能没有最好的定制网站，但他们的运营受到了最少的干预。

## 2.选择麻痹

有许多静态站点生成器，但是即使是最流行的工具也只有很少一部分网络社区在使用。你需要时间来研究、调查和评估这些选择。第一批 SSG 之一是基于 Ruby 的 [Jekyll](http://jekyllrb.com/) ，但是，虽然你不一定需要 Ruby 专业知识，如果你以前使用过这种语言，它会有所帮助。

有很多 CMS，但是有一个明显的选择: *WordPress* 。它为 T2 超过 40%的网络提供能量，所以帮助是充足的。同样，如果你有一些 PHP 经验，这将有所帮助，但即使是非开发人员也可以使用现成的主题和插件创建一个合理的网站。

## 3.初始设置时间

创建你的第一个静态网站需要时间。您需要学习构建过程，并且需要开发大部分模板代码。部署脚本可能也是必要的。

开发一个定制的 CMS 主题也可能是复杂的，但是预建的模板是可用的，并且更容易找到帮助。初次安装后，可能不需要进一步开发。

## 4.没有管理界面

面对复杂的 CMS 界面时，客户可能会非常谨慎。让他们创建和编辑一套降价文件可能会吓到很多人。或许你可以通过以下方式简化这一过程:

*   使用他们现有的 CMS 作为 SSG 数据源，或
*   提供更简单的工作流，比如在 [StackEdit](https://stackedit.io/) 或者 [Hackmd.io](https://hackmd.io/) 中编辑基于 Git 的文件。

但是这将进一步影响您的初始开发时间。

## 5.网站一致性

静态站点非常灵活:源内容中包含的任何内容都可以呈现在网页上。用户可能会包含脚本、小部件或许多不需要的项目。

可以配置 CMS 来约束用户。内容通常绑定到具有特定字段的数据库，因此管理面板会提示用户输入标题、正文内容、摘录、特色图片等。即使用户在意想不到的字段中输入了内容，它也不会出现在网站上，除非它是在主题模板中实现的。

## 6.管理大型网站

假设一个网站有数千个页面、每日内容发布、实时突发新闻，并且有几十个作者分布在多个地点。可以使用静态站点生成器来管理内容，但是:

*   内容编辑和发布可能会更尴尬。编辑可能需要访问 Git 存储库或共享文件夹，而不是简单的 web 或应用程序界面。
*   实时更新被延迟，因为站点必须被重建、测试和部署。
*   构建时间可能会迅速增加，部署可能会变得繁琐。

静态站点生成器可能最适合包含不超过几百个页面且每周有几个新帖子的站点。自动化的构建和部署过程将是必需的，您可能会发现 CMS 成为了一个更实用的选择。

## 7.服务器端功能

静态站点非常适合内容页面，但是当您需要用户登录、表单填写、搜索工具、论坛或其他服务器和数据库交互时，情况就变得更具挑战性了。选项包括:

1.  添加第三方客户端组件，如 [Algolia search](https://www.algolia.com/) 或 [Disqus comments](https://publishers.disqus.com/) 。
2.  创建您自己的服务器(或无服务器)API，客户端 JavaScript 可以使用这些 API 来添加所需的功能。
3.  生成包含`<?php ... ?>`或类似服务器端代码块的页面。
4.  切换到像 [Next.js](https://www.sitepoint.com/premium/books/build-a-blog-with-react-and-next-js/read/1) 这样的框架，它尽可能呈现静态内容，但也允许服务器端处理。

然而，开发时间、构建复杂性、安全隐患、测试工作量和费用都会增加。相比之下，安装一个合适的 WordPress 插件可以在几分钟内实现客户端或服务器端的功能。

## 静态网站适合你吗？

在做出任何决定之前，请检查:

*   您的项目需求、规模、复杂性、更新频率等。
*   你的用户，他们的位置，期望等等。
*   您团队的开发技能，以及
*   任何托管和/或部署因素。

绝大多数网站很少超过几十个页面，很少接收更新，并且依赖于开发者来做出这些改变。CMS 通常是多余的，所以静态站点生成器可以简化开发并降低成本。说服你的客户放弃他们的内容管理面板可能是一项更艰巨的任务！

有关使用静态场地生成器构建场地的实际演示，请参见:

*   [如何使用 Metalsmith 创建静态站点](https://www.sitepoint.com/create-static-site-metalsmith/)
*   [十一月入门](https://www.sitepoint.com/getting-started-with-eleventy/)
*   [如何使用 WordPress 作为 Eleventy 的无头 CMS](https://www.sitepoint.com/wordpress-headless-cms-eleventy/)
*   [使用 Cloudflare Workers 处理 Jamstack 站点的表单](https://www.sitepoint.com/jamstack-form-handling-cloudflare-workers/)

## 分享这篇文章