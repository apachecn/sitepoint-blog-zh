# 升级到 Rails 2.0。食谱

> 原文：<https://www.sitepoint.com/upgrading-to-rails-20-a-recipe/>

在以前的帖子中，我介绍了 Rails 2.0 的一些更新以及如何为 Rails 2.0 做准备，但没有真正介绍如何升级到 Rails 2.0 的机制。因此，作为今年 Rails】系列的 [6 件事的一部分，我编制了一个适合我的快速食谱。这可能不是唯一的方法，但确实有效。](https://www.sitepoint.com/6-things-to-try-in-rails-this-year/)

## 1.修复所有不推荐使用的警告

最简单的方法是下载我之前提到的 Geoffrey Grosenbach 的 [rake task](http://topfunky.net/svn/plugins/deprecated/tasks/deprecated.rake) 。将其复制到要升级和运行的应用程序的 lib/tasks 目录中

 `rake deprecated` 

这将为您指出任何不推荐使用的方法的方向。找到他们并解决他们。

## 2.清理您的环境

我曾经在 enviroment.rb 文件中放了很多代码，这确实有点不方便。为了方便起见，Rails 团队引入了 initializers 目录，您可以在其中添加在运行时自动加载的自定义代码。您现在需要创建目录，所以运行

 `mkdir config/initializers` 

在该目录下创建一个新的 ruby 文件，并从 enviroment.rb 文件中移除任何自定义代码、mime 类型和变形器。

## 3.更新引擎

尽管 Rails gems 提供了很多 Rails 引擎，但每个应用程序都需要一些模板代码。这是在运行 rails /path/to/app 命令时创建的。嗯，为了确保一切都是最新的，我们将再次做同样的事情。rails 命令足够智能，可以在覆盖文件之前比较它们之间的差异。

 `rails /path/to/your/app` 

现在应该会问你“覆盖[文件]？(输入“h”获得帮助)[Ynaqdh]”以获得一大堆文件。在点击“是”之前仔细检查每个文件名——一些你不想覆盖的文件(例如 404.html、500.html、database.yml 等)。如果它是一个配置文件，您可能不想覆盖它。它还将更新 JavaScript 库，所以如果你依赖这些库，你可能也想在那里说不。

我发现这是确保一切都是最新的最好方法。

## 4.更新视图

虽然没有严格要求，但我发现了一个 rake 任务。rhtml 文件转换为. html.erb，这样有利于一致性。

对于一个不使用任何废弃插件的标准 Rails 安装，应该就是这样了！如果你发现任何其他的智慧，请告诉我。

## 分享这篇文章