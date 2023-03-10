# 查看 Rails 3 Beta，第一部分:Bundler

> 原文：<https://www.sitepoint.com/checking-out-the-rails-3-beta-part-i-bundler/>

Ruby on Rails 3.0 的测试版在二月份发布。这是 Rails 项目与 Merb(另一个基于 Ruby 的 MVC 框架)合并的结果，也是大量错误修复、性能增强和新特性的结果。

我一直在为我正在做的一个个人项目玩它，所以我想我应该与你分享一些很酷的新功能。很多关于 Rails 3 的帖子都是针对铁杆 Rails 爱好者的，所以它们不太可能吸引那些以前没有使用过这个框架的人(或者只是涉猎过这个框架的人)。在这里，我将采用一种不同的方法，尝试向您展示现在是进入 Rails 并开始尝试的最佳时机。如果您在 1.x 或 2.x 分支中尝试过 Rails，也许我将概述的一个新特性会说服您再试一次。

所以，事不宜迟，让我们看看新开发人员可能会遇到的第一个 Rails 3 特性。Rails 3 引入了 Bundler，这是一种管理应用程序依赖性的非常简单的方法。Rails 应用程序通常依赖于许多 Ruby gem(Ruby 称之为库)。例如，您可能使用[回形针](http://github.com/thoughtbot/paperclip)来处理带有附件的模型，[设计](http://github.com/plataformatec/devise)来验证用户，或者[黄瓜](http://cukes.info/)来编写集成测试。在 Rails 2.1 之前，您基本上必须自己管理应用程序的任何依赖项。如果您将应用程序部署到缺少 gem 的服务器上，或者有不同的版本，它可能会在没有警告的情况下中断。Rails 2.1(和 2.2)以`gem.config`的形式引入了一些依赖管理，但是它的用法有点晦涩难懂，容易出问题。

Rails 3 的 Bundler 让 gem 管理变得如你所能想象的那样简单。您将一个名为`Gemfile`的文件放在您的应用程序的根目录中，在这里您以下面的格式指定您的应用程序需要哪些 gem:

```
gem 'paperclip', '2.3.0' 
```

版本字符串是可选的；如果没有，Bundler 将从 Gemcutter 上获取最新版本，Gem cutter 是主要的 Ruby Gem 库。一旦您满意地设置了 Gemfile，您需要做的就是从应用程序的目录中运行这个命令:

```
bundle install
```

然后，Bundler 会将所有需要的 gem(以及它们可能有的任何依赖项)下载到您的机器上。

仅这些特性就让 Bundler 成为 Rails 的一大亮点，但它的意义远不止于此。Bundler 还无缝地安装来自 GitHub 的宝石。您需要做的只是指定一个 URL 和一个分支，剩下的事情会在您运行`bundle install`时自动处理。例如，当前版本的回形针尚未更新为支持 Rails 3。然而，[杰森·金](http://github.com/JasonKing)已经在 GitHub 上完成了这个项目，并对其进行了修补，使其能够正常工作。因此，要将打了补丁的版本导入我的应用程序，我需要做的就是:

```
gem 'paperclip', :git => 'git://github.com/JasonKing/paperclip.git', :branch => 'rails3'
```

我的应用程序现在将获取该修补版本。当官方回形针宝石更新后，我需要做的就是删除`:git`和`:branch`选项，并重新运行`bundle install`。我现在运行 Gemcutter 的版本。

Bundler 的另一个重要特性是，您可以通过将 gem 语句分组来指定需要给定 gem 的环境:

```
group :test do
  gem 'webrat'
  gem 'shoulda'
end
```

有了我的 Gemfile 中的这段代码，我可以在我的生产机器上运行以下代码:

```
bundle install --without test
```

这很有用，因为我宁愿避免在一台永远不会用到 Webrat 的机器上管理编译 Webrat 及其依赖项所需的库。

Bundler 只是 Rails 3 众多新特性中的一个；这恰好是您在设置新应用程序时遇到的第一个问题。下周，我将关注一些更直接影响开发的特性。在此之前，请查看[发布公告](http://weblog.rubyonrails.org/2010/2/5/rails-3-0-beta-release/)了解安装测试版的说明，以及官方[发布说明](http://guides.rails.info/3_0_release_notes.html)了解新版本中的所有内容。

## 分享这篇文章