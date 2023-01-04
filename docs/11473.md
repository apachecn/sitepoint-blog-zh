# Jekyll:网站变得简单

> 原文：<https://www.sitepoint.com/jekyll-sites-made-simple/>

就在去年八月，我决定重新启动我的网站；我选择轻击 WordPress，并尝试使用基于 Ruby 的静态站点生成器 [Jekyll](http://wiki.github.com/mojombo/jekyll) 。哲基尔？在文档中是这样描述的:

> Jekyll 的核心是一个文本转换引擎。该系统背后的概念是这样的:你给它用你最喜欢的标记语言编写的文本，可以是 Markdown、Textile，或者只是普通的 HTML，它通过一个布局或一系列布局文件搅动它。在整个过程中，你可以调整你想要的站点 URL 的外观，在布局上显示什么数据，等等。这都是通过严格的文件编辑完成的，web 界面就是最终产品。

Jekyll 是一个博客感知的静态站点生成器。它使用一组模板或布局文件、相关的 CSS 文件和用于文章的纯文本文件。帖子可以使用 Markdown 格式或另一种类似的标记语言。Jekyll 获取 post 标记并将其插入到布局中，生成标准的 HTML 标记以及链接文件(任何 CSS、JavaScript、图像等)，供您上传到 web 服务器。没有内容管理系统，没有数据库，也不需要在 web 服务器上提供特定的语言支持——您的站点是一个简单的旧静态 HTML 文件的集合。

切换回静态标记初看起来像是回到 20 世纪 90 年代末。然而，Jekyll 是一个简单而优雅的系统，它模仿了动态网站的特征——从数据库中提取内容，并通过 CMS 将其插入模板中——没有那么复杂。作为 KISS 原则的坚定信徒(保持简单，愚蠢)，我总是试图找到适合这项工作的正确工具，Jekyll 是一个常见问题的简单解决方案:“我想要我自己的定制博客。”它也足够灵活，可以很容易地用于其他任务。比如 GitHub 用它来驱动 GitHub 页面。

这篇文章描述了使用 Jekyll 构建一个网站:从下载它，建立一个开发环境，并破解模板文件。我将用一句话来总结维护开发实例之间的更新(例如，新的博客条目)并将它们同步到 web 服务器。注意，我是从一个设计师的角度来写这篇文章的。虽然我有许多技术障碍要跨越，但 Jekyll 仍然相当简单；例如，使用 Jekyll 的模板系统和 Liquid 标签比将一个设计作为 WordPress 的主题更容易。别担心，我打算让这篇文章尽可能地对设计者友好。

创建 Jekyll 博客相对来说比较容易，可以归结为几个简单的步骤。

## 设置您的开发环境

Jekyll 是用 Ruby 写的，所以你需要安装它。就像[为你的平台下载最新的 Ruby 版本](http://www.ruby-lang.org/en/downloads/)并遵循安装说明一样简单。Ruby 安装还包括 RubyGems 打包系统，并允许您下载和安装其他 Ruby 库和程序。如果你使用的是 Windows，选择一键安装程序，并确保在运行安装程序时勾选启用 RubyGems 选项。

我们将从 Gemcutter RubyGem 托管库获取 Jekyll，但是我们首先需要安装 Gemcutter 程序。

如果您使用的是 Mac OS X 或 Linux，请打开命令提示符并输入以下命令:

```
$ `sudo gem install gemcutter`⋮ Successfully installed gemcutter-0.1.71 gem installedInstalling ri documentation for gemcutter-0.1.7...Installing RDoc documentation for gemcutter-0.1.7...$ `sudo gem tumble`
```

在下载 RubyGem 包时， **tumble** 命令将简单地让 RubyGems 安装程序首先查询 Gemcutter 库。这确保了 Jekyll 是从 Gemcutter 库下载的。

现在我们可以下载并安装 Jekyll:

```
$ `sudo gem install jekyll`
```

如果你在 Windows 上，你将在 Windows 命令提示符下输入命令(开始 > 运行… 并输入 **`cmd`** ):

```
C:> `gem install gemcutter`C:> `gem tumble`C:> `gem install jekyll`
```

Jekyll 像所有好的软件一样，是模块化的，可以很好地完成一项任务，并依赖其他程序和库来获得额外的功能——毕竟，为什么要重新发明轮子呢？这种模块化也给了我们选择的余地:根据您的喜好，您可以从各种执行类似任务的略有不同的工具中挑选来扩展 Jekyll。例如，如果你宁愿在[纺织](http://www.textism.com/tools/textile/)而不是[减价](http://daringfireball.net/projects/markdown/)写博客，你可以。然而，对我来说，缺省值(Markdown)就足够了，Gem 只需要少量其他的[依赖项](http://gemcutter.org/gems/jekyll)，这些都是自动获取的。

关于扩展 Jekyll 的可用 gem 的完整列表，请参见 GitHub 上的 Jekyll 安装说明中的列表。

## 分享这篇文章