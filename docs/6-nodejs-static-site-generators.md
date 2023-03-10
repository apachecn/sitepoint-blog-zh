# 6 Node.js 静态站点生成器

> 原文：<https://www.sitepoint.com/6-nodejs-static-site-generators/>

随着 Node.js 越来越受欢迎，并成为小型和大型 web 应用程序的可行解决方案，许多有才华的开发人员正在创建基于节点的系统，以取代我们许多人都知道并喜欢的一些老式软件。

在内容管理系统(CMSs)中，毫无疑问最流行的是 WordPress。这是一个可靠而稳固的系统，有一个非常活跃的社区。它允许几乎没有技术技能的人创建任何类型的网站，包括但不限于博客。在静态站点生成器中，Jekyll 和基于 Jekyll 的系统受到了开发人员的青睐，他们正在寻找创建博客或信息网站的轻量级解决方案。在本文中，我将向所有 JavaScript 爱好者概述 6 个 Node.js 静态站点生成器。

## 温特史密斯

![wintersmith](img/d5739a0f44ebe9fab36c1a543e133b41.png)

[Wintersmith](http://wintersmith.io/) 号称是基于 Node.js 构建的<q>灵活、极简、多平台静态站点生成器</q>当然，<q>极简</q>是关键词！Wintersmith 不适合初学者，但对于希望轻松上手并运行的中级或高级开发人员来说，它非常合适。温特史密斯的方法并不是唯一最小的东西。它在 [CoffeeScript](https://coffeescript.org/) 中进行了编码，并与 [Jade](http://jade-lang.com) 和 [Markdown](http://daringfireball.net/projects/markdown/) 一起准备就绪。

这个静态站点生成器在网站上托管它的 API 文档，但是使用 [GitHub](https://github.com/) 托管 wiki。信息简单明了，但很少。然而，一个相当活跃的 [StackOverflow 标签](http://stackoverflow.com/questions/tagged/wintersmith)和 IRC 频道(#wintersmith)意味着帮助不会太远。

## 装配

![assemble](img/0208d4654bd64fc095c706993799fbf2.png)

Assemble 是一个强大的工具，它将 [Grunt](http://gruntjs.com) 和 [Yeoman](http://yeoman.io) 集成到其工作流程中。就像本文后面讨论的 Punch 和 DocPad 一样，Assemble 试图<q>让设计人员和开发人员达成一致。</q>例如，Assemble 预装了[手柄](http://handlebarsjs.com/)，这是目前使用最广泛、对初学者友好的模板系统之一。

虽然 Assemble 附带了一个插件系统，但它主要是为了<q>扩展 Assemble 的核心功能，</q>而不一定是为了添加前端框架、jQuery 插件或其他您可能想要使用的 JavaScript 库。

## 金属史密斯

![metalsmith](img/ff59a8e4e71013b618f16cb4e6a028d0.png)

[Metalsmith](http://www.metalsmith.io/) 立即引起了我的注意，这不仅是因为它漂亮而简约的设计，还因为下面的标语:<q>一个极其简单的，*可插拔*静态站点生成器。</q>第一篇导语继续解释说<q>metal Smith 中的所有逻辑都是由插件处理的。你只要把它们连在一起。</q>这使得 Metalsmith 成为这里介绍的所有系统中最通用的。

但是伴随着巨大的可插拔性而来的是巨大的责任。Metalsmith 是为中级和高级开发人员设计的，他们希望获得令人满意的灵活性和对内容的控制。他们的网站提供了一些静态站点生成器可以帮助你的内容的例子:项目架子工、电子书生成器、构建工具和技术文档。Metalsmith 不仅仅是博客和简单的网站！

## Hexo

![hexo](img/49770dcbebf14f51db3fd93c20701303.png)

在本文列出的所有 Node.js 静态站点生成器中， [Hexo](http://hexo.io/) 是目前为止 GitHub 上最受欢迎的。Hexo 的文档简单明了，非常漂亮，并且在每一页的底部都有一个评论区。此外，在 GitHub 上你可以找到很多极简主题，其中很多是[幽灵](https://ghost.org/)主题的派生或衍生。

Hexo 在很大程度上是 Ghost 的后代，专注于用户体验和极简主义。它还试图让您尽可能快地启动和运行。我相信这使得 Hexo 成为创建轻量级博客的最适合初学者/设计者的系统。

## 打孔

![punch](img/2e9ad6d863bb18d8c8f5ccba5415b7f8.png)

[Punch](http://laktek.github.io/punch/) 是本文中两个基于 Node.js 的静态站点生成器之一，据称对设计人员和开发人员来说都很容易。设置 Punch 很容易，但是当你开始使用它时，它给你的只是一个空白页。一个基本的开始主题是可用的，但其余的取决于你。

与其他静态网站不同，Punch 不提供定制的插件系统。它依赖于 [npm](https://www.npmjs.com/) 来安装替代的 CSS 预处理器、JavaScript 库等等。

## DocPad

![docpad](img/a81a3b0013c2983934eee14e952c6169.png)

即使粗略地浏览一下 DocPad 的网站，也可以看出 DocPad 是一个雄心勃勃的项目。如果你正在寻找一个逐点说明 DocPad 与 Jekyll 不同的地方，你不用找太远。在 [DocPad 的介绍页面](https://docpad.org/docs/intro)的底部，你可以找到一个显示每个系统所能提供的比较表。当然，这些标准是基于 DocPad 认为全功能 CMS 所必需的。

DocPad 文档是广泛和详尽的；考虑到系统提供的无数特征，必须如此。虽然文档记录得非常好，但我不会说 DocPad 非常适合初学者。抛出像*抽象*这样的术语可能会拒绝一个初学者或者一个几乎没有编码经验的设计师。

像本文中描述的其他软件一样，DocPad 自带插件系统，使得切换模板系统、预处理器或在必要时包含 JavaScript 库变得简单。插件系统是广泛的，你不太可能想要或需要使用 DocPad 没有提供的语言。

但是，嘿，[蜘蛛](http://spiderlang.org)，一种新的编译成 JavaScript 的语言*看起来很有趣。以至于你可能想在你的下一个宠物项目中使用它。在这种情况下，DocPad 为[扩展平台](https://docpad.org/docs/extend)提供指令，包括编写自定义插件和开始使用*框架*项目。*

 *## 结论

当谈到前端工具、库和一般软件时，永远不会缺少选项。你在寻求最大限度的控制吗？或者即插即用？本文中列出的每个 Node.js 静态站点生成器都有适合您的 web 开发个性的东西！* 

## *分享这篇文章*