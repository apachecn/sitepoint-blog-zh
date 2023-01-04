# Rails 深度剖析:应用程序设置，位置

> 原文：<https://www.sitepoint.com/rails-deep-dive-application-setup-loccasions/>

到目前为止，在 rails 深入探讨系列中，我们已经将重点放在深入挖掘框架的内部，试图揭示 Rails 实现其魔力的一些方式。展望未来，我想创建一个 Rails 3.1 应用程序，重点是如何设置应用程序、执行开发和部署应用程序。我认为在头脑中有一个明确的目标会让这个系列受益。

我们的应用程序将被称为**locations**。Loccasions 的目的是允许用户创建*事件*和*场合*。一个事件可能是“我打扫了我的房间”或者“下雨了”或者“看见了一颗彗星”。事件包含时机，标记事件发生的时间和地点。该应用程序将在地图上呈现事件，允许用户查看事件发生的频率和地点。这个想法很简单，用例也很具体，所以创建这个应用应该很容易(他说，知道自己会遇到障碍…)

## 用户故事

当创建一个新的 Rails 应用程序(或者任何应用程序)时，最好有一些[用户故事](http://en.wikipedia.org/wiki/User_story "User Stories")来指导应用程序，并确保我们在执行任务。通常，您会与客户会面，并一起生成高级用户故事。用户故事的关键是捕捉足够多的细节来开始工作，避免“分析瘫痪”,这会阻碍进展。对于临时情况，我们将保持用户故事相当高的水平，随着我们的发展增加更多。我们的第一个故事是:

*   作为未注册用户，我想看看主页/登录页面
*   作为一名管理员，我希望能够邀请用户参加临时会议
*   作为受邀用户，我希望能够创建一个帐户
*   作为注册用户，我希望能够创建活动
*   作为注册用户，我希望能够创造机会
*   作为一个注册用户，我想在地图上看到场合

我认为这是一个良好的开端。

## 宝石

下一个决定是关于我们将要利用的宝石，以满足我们的一些功能需求。显然，临时代理需要某种认证，社区在这方面有很好的资源。可能最著名的认证宝石是由 Jose Valim 和 Plataformatec 的不可思议的人们写的[device](https://github.com/plataformatec/devise "Devise")。我认为使用 Devise 给了我们一个久经考验的宝石和一个奇妙的支持社区。

我为临时事件所做的决定之一是如何处理持久性。我选择 MongoDB 作为后端持久性存储，而不是像 PostGIS 或 MySQL 那样走标准的关系数据库路线。首先，我认为事件== >场合模型是一个很好的文档数据库模型。其次，我相对确定 Loccasions 将使用 MongoDB 提供的[空间功能](http://www.mongodb.org/display/DOCS/Geospatial+Indexing "MongoDB Spatial")。此外，如果我是诚实的，我真的想在一个“现实”的 Rails 应用程序中使用 MongoDB，这是机会敲门。

MongoDB 的使用引出了 gems 可以提供帮助的另一个领域。在这种情况下，我查看了 [MongoMapper](http://mongomapper.com/ "MongoMapper") 和 [Mongoid](http://mongoid.org/ "Mongoid") 并选定了 Mongoid，因为它似乎对 MongoDB 的空间部分提供了更好的支持，并且存在[Mongoid _ spatial](https://github.com/ryanong/mongoid_spacial "mongoid_spacial")

值得注意的是，这个结论是基于对两组文档的几分钟观察，所以它可能是错误的。然而，这就是在启动一个应用程序时，有时做出决策的方式。挑一个方向走。此外，我们很可能会遇到 gem 依赖项之间的版本问题。如果/当这种情况发生时，我们可能不得不牺牲一颗宝石或叉起它，自己解决这个问题。

## 客户端的东西

我相对确定我们会在各种场合使用大量的 javascript。我最初的设想是将每个事件页面视为一个单独的页面应用程序，允许用户创建事件并将它们添加到地图中。地图和事件列表将保持同步，这意味着我们有两个“视图与我们的视图”。这个愿景将我推向客户端框架，我目前最喜欢的是 [Backbone.js](http://documentcloud.github.com/backbone/ "Backbone.js") 。 [rails-backbone gem](https://github.com/codebrew/backbone-rails "rails-backbone gem") 简化了 rails 对 backbone 的使用，所以我们也将这个 gem 放在列表中。(注意:有两个名称非常接近的主干 gem，请确保您使用的是 codebrew 的 gem)

此外，我已经成为了 Haml 的粉丝，所以我认为我们将使用 Haml 而不是 ERB 作为我们的视图模板。

## 测试

我们将尽可能地采用测试驱动的方法来创建临时位置。本质上，这意味着我们将首先编写测试来驱动应用程序的设计和实现。考虑到这一点，我们需要选择一种测试方法，我已经决定使用 RSpec 和 Capybara。我喜欢 RSpec，并认为像黄瓜这样的东西比我在这个系列中需要的要多一些。此外，有一个集成了 Mongoid 和 Rspec 的 gem(Mongoid-Rspec)将简化我们的测试。

测试驱动方法也扩展到了应用程序的客户端，使用类似于 [Jasmine](http://pivotal.github.com/jasmine/ "Jasmine") 的东西可以保持规范方法的一致性。

## 源代码控制

我将为 Loccasions 源代码创建一个 Github 存储库。在开始任何开发过程之前，您应该有一个源代码控制计划。Git 使得使用 SCM 变得非常容易，所以没有借口。

## 其他资源

你的 Rails 工具箱中最好的工具之一就是互联网，站在前人的肩膀上。例如，我对 Devise 和 Mongoid 设置的灵感来自丹尼尔·凯霍的[精彩教程](https://github.com/RailsApps/rails3-devise-rspec-cucumber "Rails_Apps Tutorial")。我确信我们会在网上寻找帮助和资源，我希望强调我们的发现。

## 起跑线

好吧，我认为这是足够的计划。是时候停止把脚趾浸在水里，跳到脖子上了。当然，我们需要 MongoDB 在本地运行。去你的平台上安装 MongoDB…我会等的。

完成了吗？希望您已经准备好了默认的 MongoDB 设置。在我们生成应用程序之前要做的最后一件事是为我们的应用程序创建一个 [github 库](http://learn.github.com/p/setup.html "Setup Github")(我的是这里的)。)

我们越来越近了。我用的是 Rails 3.1 RC5 和 Ruby 1.9.2。此外，我使用 [RVM](https://www.sitepoint.com/rails-development-101-rvm/ "RVM Article") 和我*强烈*建议你在继续之前设置 RVM 和宝石。

```
rvm use 1.9.2@loccasions -- create # will create and switch to loccasions gemset and Ruby 1.9.2
```

我们有一个干净的 gemset，所以我们需要在访问 Rails 之前安装一些 gem。

```
gem install bundler
    gem install rails --pre # We want 3.1
```

记住，我们使用的是 MongoDB，所以我们不需要任何 ActiveRecord 片段(-O)(我们不会使用迁移)，而且，我们使用的是 RSPec，所以不需要生成 Test::Unit 文件(-T)。

```
rails new loccasions -O -T
    cd loccasions
```

现在我们终于有了一个应用程序结构，我们需要引入前面提到的宝石。在你最喜欢的编辑器中打开“Gemfile ”(我用 vim，b/c 它是 fantasmic ),让它看起来像:

```
source 'http://rubygems.org'

gem 'rails', '3.1.0.rc5'
gem 'devise', "~> 1.4.2"
gem 'mongoid', "~> 2.1.8"
gem 'mongoid_spacial', "~> 0.2.13"
gem 'haml', '~> 3.1.2'
gem 'bson_ext', '~> 1.3.1'

gem 'rails-backbone', "~> 0.5.3"

# Gems used only for assets and not required
# in production environments by default.
group :assets do
  gem 'sass-rails', "~> 3.1.0.rc"
  gem 'coffee-rails', "~> 3.1.0.rc"
  gem 'uglifier'
end

gem 'jquery-rails'

group :test, :development do
  gem 'rspec-rails', '~> 2.6.1'
  gem 'mongoid-rspec', '~> 1.4.4'
  gem 'capybara', '~> 1.0.1'
  gem 'factory_girl_rails', '~> 1.1.0'
  gem 'database_cleaner', '~> 0.6.7'
  gem 'jasmine', '~> 1.0.2.1'
end
```

很快`bundle install`我们就准备开始我们的第一个用户故事了。在此之前，让我们对 git 存储库进行初始提交，并将其推送到 github。首先，编辑。gitignore 并确保它有意义:

```
.bundle
db/*.sqlite3
log/*.log
tmp/
.sass-cache/
*.swp
.DS_Store
```

我添加了`*.swp`和`.DS_Store`行，这样我的 vim 缓冲区和 Mac 工件就不会被添加到存储库中。

```
git add . 
git commit -m "Initial commit"
```

现在，将您的 github 远程资源库添加为“origin”。

```
git remote add origin https://ruprict@github.com/ruprict/loccasions.git
git push origin master
```

在我这样做的那一刻，我意识到我忘记了创建一个. rvmrc 文件，所以让我们也这样做并把它推上来。

```
rvm --rvmrc --create ruby-1.9.2-p290@loccasions
```

现在，`cd ..`然后是`cd loccasions`来生成。rvmrc 文件可信。它将提示您检查文件，然后键入“是”。最后，添加。rvmrc 到 git，推送到 github。

```
git add .rvmrc
    git commit -m "Added .rvmrc"
    git push origin master
```

在本系列的下一篇文章中，我们将从“未注册用户”的故事开始，这将引导我们决定如何布局应用程序。如果你对设置有任何问题，请在评论中告诉我。

## 分享这篇文章