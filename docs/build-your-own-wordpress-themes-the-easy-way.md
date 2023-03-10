# 用简单的方法构建你自己的 WordPress 主题

> 原文：<https://www.sitepoint.com/build-your-own-wordpress-themes-the-easy-way/>

WordPress 是迄今为止最受欢迎的博客工具之一，WordPress 主题当然很受欢迎。自定义主题是向客户推销的便捷服务，而为你的博客创建自己的主题是展示你的个性和技能的绝佳方式——比下载一些已经被 500 个其他博客使用的普通主题要好得多。

从头开始创建一个全新的主题既复杂又耗时——解释如何编写自己的主题对于一篇小小的博文来说是一项太大的工作！幸运的是，WordPress 的父主题和子主题功能是一种快速简单的开始使用主题的方式。只需抓住一个适合你需求的父主题框架，添加一个带有你自己的 CSS 和调整的子主题，你就可以控制主题了。

## 主题如何工作

如果你不熟悉 WordPress 主题的工作方式，这里有一个快速入门:一般来说，主题由 PHP 模板、样式表、图像和 JavaScript 文件的集合组成。它们都被打包在一个目录中，并上传到 WordPress 主题目录，该目录在 wp-content/themes 中。管理员从站点仪表板中选择主题。

然而，对于一个子主题，所需要的只是样式表 style.css。我们将如上定义主题的细节，并添加一行来指定要使用的父主题。然后 WordPress 将使用我们的样式表，以及来自父主题的模板、函数和脚本。我们还可以选择添加自己的模板或函数来定制主题生成的标记。

## 选择父主题

我们需要选择一个基本主题，它将作为本练习的父主题。你可以使用任何一个主题作为你的基础，尽管有几个框架主题是专门为这个目的设计的[。当然，你应该确保你选择了一个有许可版权的主题，比如知识共享或者 GPL 许可。尽管创建一个子主题并没有改变原来的主题，但是公平地说，如果你重复了一个高级主题，主题设计师会很不高兴的。](http://codex.wordpress.org/Theme_Frameworks)

在这个练习中，我将使用[主题。它相当成熟，可以编辑，并且充满了用于 CSS 的类和 id。它还有许多小部件区域——在模板中添加我自己的内容或代码的地方——而不必修改标记。](http://themeshaper.com/thematic/)

## 安装父主题

让我们假设你已经有了一个 WordPress 的副本——如果没有，你应该[现在就获取并安装它。](http://wordpress.org/download/)接下来，安装主题，下载主题，解压文件，然后上传主题文件夹到你的 WordPress 安装的 wp-content/themes 目录。

在你的管理界面中，访问外观屏幕，激活那个主题，然后去查看你的 WordPress 站点。相当稀疏，对不对？没关系，我们很快会添加到这一点。不过，现在来看看标记的内部。我们将使用这个标记来放置我们的 CSS。在 Thematic 中，所有东西上都有大量的类和 IDs 有足够的空间添加各种各样的附加功能。

## 开始设计

这是有趣的部分:让我们添加一些 CSS。我喜欢使用 WordPress themes 的 CSS，将不同种类的页面——主页、个人文章、类别列表等——保存为一系列普通的旧 HTML 文件。我局部地修改它们来引用我自己的样式表，并在放置样式时使用它们来测试外观。

您正在使用的父主题几乎肯定会有许多基本样式，您可以将其作为自己工作的参考。在主题中，有一个内置的 CSS 重置、图像、排版、插件等一系列样式。我选择保留主题中的重置、插件和图片 CSS，使用`@import`从父元素中获取它们。它们位于专题/库/样式文件夹中，我将像这样包含它们:

```
/* Browser reset */
@import url('../thematic/library/styles/reset.css');

/* Image styles for posts, etc. */
@import url('../thematic/library/styles/images.css');

/* Styles for plugins */
@import url('../thematic/library/styles/plugins.css');
```

很简单，对吧？

## 安装您的儿童主题

现在我们已经有了一些 CSS，是时候创建和安装子主题了。创建一个新文件夹，选择一个不带空格的简单名称。

在其中，创建一个新的 CSS 文件 style.css。编辑该文件，并添加主题的详细信息:

```
/*
Theme Name: My Child Theme
Theme URI: http://example.com/child
Description: My first child theme
Author: Tech Times Reader
Author URI: http://example.com
Template: thematic
Version: 1.0
Tags: your, tags, here
.
Add any comments here!
.
*/
```

重要的一行是标有`Template: thematic`的那一行。这就是我如何将主题模板定义为我的主题的基础:你在那里输入的值应该是你的父主题所在的目录的名称。根据你的心意定制这些——只有当你计划发布你的主题时才需要`Theme URI`和`Author URI`，但是其他的也应该填写。

现在，放下你一直在做的 CSS，复制任何你可能需要的图片或其他东西。将整个文件夹上传到你博客的 wp-content/themes 目录，然后在管理面板的外观部分寻找你的主题。激活你闪亮的新主题，并在真实的博客上测试它。如果你对你所看到的感到满意，是时候坐下来欣赏你的杰作了！

## 下一步是什么？

现在你已经体验了使用 WordPress 主题的一点点，你已经准备好更进一步了。这里有一些技巧可以尝试:

*   你对 PHP 没意见吗？想进入 WordPress 模板的内部吗？看一看[模板文档](http://codex.wordpress.org/Stepping_Into_Templates)并了解它是如何工作的。使用子主题，可以添加您自己的定制功能，甚至用您自己的模板覆盖它们。

*   想炫耀一下吗？将您的主题发布到野外供他人使用！[看看发布主题的指南。](http://codex.wordpress.org/Designing_Themes_for_Public_Release)

*   更想炫耀一下吗？睁大眼睛关注主题设计竞赛，并参加比赛。许多设计博客都举办主题设计竞赛，有超酷的奖品可供争夺(更不用说炫耀的权利了)——如果现金奖励更符合你的风格，在 99designs.com 的[通常会有几个主题请求！](http://99designs.com)

## 分享这篇文章