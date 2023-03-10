# 在 5 分钟内与 Poole 一起创建一个 Jekyll 博客

> 原文：<https://www.sitepoint.com/set-jekyll-blog-5-minutes-poole/>

最近，从复杂的、数据库驱动的网站转向更简单、更静态的网站——当然，在可行的情况下，比如简单的作品集网站或博客。

然而，这并不是说人们正在回到昔日的网站。您仍然希望能够有效地管理模板和站点范围的代码，这就是静态站点生成器的用武之地。

静态网站生成器的目的是把你的各种静态文件，并把它们变成一个网站。有[批次](https://staticsitegenerators.net/)和[批次](http://www.modernstatic.com/)可供选择，SitePoint 上也有最近关于它们的文章，涵盖了它们使用的[不同语言](https://www.sitepoint.com/6-static-blog-generators-arent-jekyll/)、基于节点的选项、它们相对于 WordPress 的潜在优势[，甚至还有](https://www.sitepoint.com/wordpress-vs-jekyll-might-want-make-switch/)[如何使用 Grunt](https://www.sitepoint.com/building-static-site-generator-grunt-plugin/) 滚动自己的页面。

在这篇文章中，我们将看看 Jekyll，其中最受欢迎和广泛使用。我将描述你如何用最少的代码创建一个博客，然后在 [GitHub 页面](https://pages.github.com/)上免费发布。我们还将利用 [Poole](http://getpoole.com/) ，一种框架——或者“基础设置”——来构建 Jekyll 站点。

## 简而言之，哲基尔

杰基尔是用红宝石写的。它作为红宝石安装在你的电脑上。它不是一个 CMS，它不是博客软件，它没有模板，内容等等。比那简单多了。可以把它看作是一个简单的程序，它提供了处理文件的简便方法。

安装 Jekyll 后，您可以从命令行让它工作。它可以做像 process Markdown 这样的事情，并且因为它生成一个静态站点，所以你可以很容易地在本地查看进度，并且将你的站点托管在一个静态 web 服务器上，比如 GitHub Pages。

## 安装 Jekyll

首先你需要在你的系统上安装 Jekyll。对于 Mac，在终端中运行以下命令来安装 Jekyll:

```
$ gem install jekyll
```

这将自动安装所有 Jekyll 的 gem 依赖项。如果在安装过程中遇到卡顿，可以查看 Jekyll 官网上的[故障排除页面](http://jekyllrb.com/docs/troubleshooting/)或者[在 GitHub 上提出问题](https://github.com/jekyll/jekyll/issues/new)。

虽然 Jekyll 在 Windows 上没有得到官方支持，但您仍然可以通过一些调整来运行它。请参考 Jekyll 网站上的 [Jekyll on Windows](http://jekyllrb.com/docs/windows/) 页面或朱利安·蒂洛的[本指南](http://jekyll-windows.juthilo.com/)。

## 获得 Poole 和您的第一台服务器

让我们使用 Poole，而不是从原始的 Jekyll 主题开始。它是由马克·奥托创造的，他是这样描述的:

> Poole 是静态站点生成器 Jekyll 的管家。它是由 [@mdo](https://twitter.com/mdo) 设计和开发的，为任何 Jekyll 站点提供一个清晰简洁的基础设置。它通过提供一个完整的带有示例模板、页面、帖子和样式的标准 Jekyll 安装来做到这一点。

这里可以看到基础版[。虽然它对于一个极简的博客来说非常好，但我更喜欢建立在它之上的两个主题:](http://demo.getpoole.com/)[海德](http://hyde.getpoole.com/)和[兰宁](http://lanyon.getpoole.com/)。让我们用海德来构建我们的演示博客。

要获得 Hyde 的设置，请访问其 GitHub 页面并下载存储库。把它放到你选择的文件夹里，然后去终端。让我们假设你把它放在桌面上。运行以下命令移动到该目录并启动服务器:

```
$ cd Desktop/hyde-master/

$ jekyll serve
```

`jekyll serve`命令将启动一个开发服务器，允许您在本地浏览器中预览生成的站点。

为了理解我的意思，在你的浏览器中打开一个新标签，输入:`http://localhost:4000`。如果你正确地跟踪了每件事，你会在那里找到你的新博客。

## 添加新帖子

您刚刚下载的`hyde-master`库包含[许多文件夹](http://jekyllrb.com/docs/structure/)，如`_layouts`、`_posts`和`_includes`。但是如果你想管理你博客的文章，你只需要关心一个文件夹:`_posts`。它以[降价格式](http://daringfireball.net/projects/markdown/syntax)包含了你所有的帖子。

要添加新的博客文章，只需将你的 Markdown 文件放在`_posts`文件夹中。确保你的减价文件在顶部包含以下内容(被称为 [YAML 前线事宜](http://jekyllrb.com/docs/frontmatter/)):

```
---
layout: post
title: Your Post Title
---
```

这会告诉 Jekyll 你正在添加一个帖子。(如果您有任何疑问，请查看`_posts`文件夹中的样本帖子。)

在你的终端中再次运行`jekyll serve`，如果你打开`http://localhost:4000`，你会在你的博客中找到这篇新文章。

为了确保正确构建您的博客，Jekyll 需要以下命名约定:

```
YEAR-MONTH-DAY-title.MARKUP
```

`YEAR`是一个四位数，`MONTH`和`DAY`都是两位数，`MARKUP`是代表文件中使用的格式的文件扩展名(在我们的例子中是`md`)。比如你的第一篇帖子可以命名为`2015-03-29-my-first-post.md`。

## 发布您的博客

一旦你完成了上面的步骤，并添加了一些你自己的帖子，你就可以向全世界展示你的新博客了。你可以把你的文件发布到许多网络主机上，但是 GitHub 让它变得非常容易(而且是免费的！)通过 GitHub 页面托管静态 Jekyll 站点。你只需要一个 GitHub 账户。

在推给 GitHub 之前，我们先做一些改动:

在`config.yml`文件中:

*   将`title`、`tagline`、`description`、`github`和`author`更改为您的个人详细信息。
*   将`url`改为`your_github_username.github.io`。

在 CNAME，提及`your_github_username.github.io`而不是`hyde.getpoole.com`。并编辑`about.md`以包含您的详细信息。

一旦你做了上面的更改，创建一个库`your_github_username.github.io`并将所有文件从你的桌面移动到这个库。

尝试在浏览器中打开`your_github_username.github.io`，你会在那里找到你的新博客。

**需要注意的重要一点**:您需要了解一点 Git，以便能够将您的新帖子添加到存储库中。如果你不喜欢命令行，有很多工具可以让你轻松使用 Git，比如 [GitHub for Mac](https://mac.github.com/) 。无论哪种方式，一旦你把文章添加到库中，GitHub Pages 会处理剩下的事情。

## 进一步阅读

这篇文章是关于让你开始使用 Jekyll 的，但是如果你愿意花一些时间学习，你可以做很多事情。您会发现以下资源有助于更好地理解 Jekyll 和 Git:

*   Jekyll 文档——非常简洁和中肯的文档
*   杰基尔简介——约翰·朗西的精彩解释
*   [Git 和 GitHub 的基础知识](https://www.youtube.com/watch?v=U8GBXvdmHT4)——来自官方 GitHub YouTube 频道
*   [15 分钟学会 Git 基础知识](https://try.github.io/)—Code School 的互动教程。

我希望这篇文章对你有用。如果你正试图用上面提到的步骤来建立一个博客，但是在任何地方都停滞不前，请在下面的评论中提出问题。我会在那里很活跃。

## 分享这篇文章