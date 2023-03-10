# 探索基于 Node.js 构建的不同 CMS 解决方案

> 原文：<https://www.sitepoint.com/exploring-different-cms-solutions-built-on-node-js/>

为了让 web 应用程序保持相关性，必须有某种适当的过程来确保一切都可以尽快更新和修改。为了满足这种需求，诸如 WordPress 和 Drupal 这样的内容管理系统(或 CMS)被创造出来，并取得了令人难以置信的成功。也就是说，这两个 CMS 解决方案都是使用 PHP 开发的，这并不奇怪。毕竟，这些项目都是在 21 世纪初建立的，当时 PHP 被视为 web 编程语言中最重要的东西。由于 Node.js 速度极快，过去几年服务器端 JavaScript 的兴起导致了基于 node . js 构建的不同 CMS 解决方案。

在本文中，我将向您概述这种类型的三种 CMS 解决方案:

*   KeystoneJS
*   铅笔蓝
*   撇号

为了给你最好的理解，我将讨论这些平台使用的一些关键技术，以及它们之间的不同之处。因此，您可以选择您认为最适合您正在开发的项目的平台。

## KeystoneJS

[KeystoneJS](http://keystonejs.com) 是基于 Node.js 构建的最受欢迎的 CMS 之一，其使命是为用户提供一种借助 MongoDB 轻松创建数据库驱动网站的方式。使用 KeystoneJS 启动和运行非常容易。一种方法是安装一个名为`generator-keystone`的 [Yo 生成器](http://yeoman.io/generators/)，然后用它初始化一个新项目。为此，您可以在项目目录中运行以下命令:

```
npm install -g generator-keystone
yo keystone
```

Keystone generator 允许用户根据自己的需求定制应用程序。它为开发者提供了很多不同的选择，这总是一个优点。默认情况下，Keystone 应用程序使用 [Express](http://expressjs.com/) 框架和 [MongoDB](https://docs.mongodb.org/manual/) 。Keystone 背后的人分享了一个非常可靠的指南，重点是如何开始使用这个框架。[你可以在这里阅读](http://keystonejs.com/docs/getting-started/)。

一旦生成器完成了 Keystone 应用程序的配置，它将为用户提供一个带有身份验证模板和博客系统的样板应用程序。KeystoneJS 还为用户提供了一个用户界面，可以方便地添加新页面、创建 SEO 标签、定义 URL 等等。

![keystone](img/3e5380f9dbdb35203c56e55b60d31362.png)

KeystoneJS 允许您开箱配置的其他一些技术有 [embed.ly](http://embed.ly/) 、 [Cloudinary](http://cloudinary.com/) 和 [Mandrill](http://mandrill.com/) 。[你可以参考这个链接获得完整的文档](http://keystonejs.com/docs/configuration/#services)。绝对值得注意的是，Keystone 的文档很好地解释了每个规范，并且非常详细。

## 铅笔蓝

名单上的下一个是 [Pencilblue](https://pencilblue.org/) ，一个为高流量网络应用服务的 CMS。像 KeystoneJS 一样，Pencilblue 提供了一个可靠的用户界面，可以轻松地创建新页面和博客文章，定义 SEO 标签等等。使用 Pencilblue 的一个优势是它内置了对[引导](http://getbootstrap.com/)、 [AngularJS](https://angularjs.org/) 和 [jQuery UI](https://jqueryui.com/) 的支持。因此，开发人员可以立即开始在他们的 Pencilblue 应用程序中实现自定义代码。在后端，Pencilblue 提供了对 MongoDB 和 [Redis](http://redis.io/) 的内置支持，并允许开发人员轻松启用数据库项的缓存。

下面是开始使用 Pencilblue 命令行的代码:

```
npm install -g pencilblue-cli
pbctrl install [appName]
```

Pencilblue 使用了一个插件系统，这个系统与 WordPress 中的主题非常相似。下面是默认 Pencilblue 主题的模板示例；它展示了如何使用 carets 将模板加载到文件中。除了这个模板系统，控制器是这个 CMS 的另一个关键部分，因为它们允许开发人员向它添加功能。由于 Pencilblue 是建立在 AngularJS 这样的框架之上的，所以使用它的人可以利用他们对不同库的了解来为主题添加一些功能。

```
<!-- Loads template at admin/head.html -->
^tmp_admin=head^
<div class="container">
    <!-- Retrieves the HELLO_WORLD localized text -->
    ^loc_HELLO_WORLD^
</div>
<!-- Loads analytics code -->
^analytics^
<!-- Loads template at admin/footer.html -->
^tmp_admin=footer^
```

此外，该 CMS 允许开发者创建他们自己的模板和控制器系统以及他们自己的主题。对于那些对深化这个主题感兴趣的人，我建议你看一下[这个开发主题](https://github.com/pencilblue/pencilblue/wiki/Quickstart:-Developing-Themes)的快速入门指南。

综上所述，Pencilblue 为开发者提供了一个真正强大的 CMS 解决方案。它充分利用了 Node.js 引擎，并支持在其 CMS 中使用集群。由于这种能力，许多开发人员已经考虑了从 WordPress 或 Drupal 迁移的可能性，转而使用 Node.js 多核处理器。

## 撇号

我们名单上的下一个是撇号。Keystone 是为创建数据库驱动的网站而构建的，而 CMS 是为创建设计驱动的网站而构建的。我已经包括了撇号的演示网站的 gif。正如您所看到的，它为开发人员提供了丰富的界面，并使添加新内容、构建新页面等变得极其容易。

![demo.apostrophenow.com](img/33a1979a44904802547ecbecfefc795d.png)

Apostrophe 使用了不同的技术，如 [ImageMagick](http://www.imagemagick.org) 和 MongoDB。假设您的计算机上已经安装了 Node.js、ImageMagick 和 MongoDB，您可以通过运行以下代码开始使用[撇号沙盒应用程序](https://github.com/punkave/apostrophe-sandbox):

```
git clone https://github.com/punkave/apostrophe-sandbox
cd apostrophe-sandbox && npm install
mkdir data && cp local.example.js data/local.js
node app apostrophe:reset
node app
```

如果您打开浏览器并导航到端口 3000，您将看到沙盒应用程序和一个用户界面，该用户界面包含几个小部件，允许开发人员添加文本或图像、创建新页面、编辑页面的现有区域等等。

Apostrophe 使用 [Nunjucks 模板引擎](https://mozilla.github.io/nunjucks/)向应用程序添加自定义逻辑，并使用前面提到的 ImageMagick 来确保添加到项目中的所有图像都经过优化，以获得更好的性能和查看效果。当一个图像被渲染时，撇号会自动根据它在页面上占据的区域来缩放图像。该 CMS 还为用户提供了工具来轻松创建 SEO 描述，为页面添加自定义标签等等。

撇号的开发者提供了一份详细的文档，向用户展示了平台是如何配置的，以及应该如何使用。如果你想开始使用撇号，这里有一个教程，它很好地概述了我上面提到的沙盒应用程序。

## 结论

希望到现在为止，您已经对这些平台及其提供的特性有了一些了解。如果你需要一个 CMS，我绝对鼓励你使用上面讨论的三个中的一个，因为它们提供了如此多的功能。一如既往，如果你有任何问题，请在下面分享你的评论，我会尽快回复你的！

## 分享这篇文章