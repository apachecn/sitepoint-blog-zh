# Ruby 社区:简介

> 原文：<https://www.sitepoint.com/ruby-community-introduction/>

![Human arms around colorful and next.](img/c94219f2eff804a60b80af47148c619c.png)

大约一年前，我在 Ruby 生态系统中面临许多挑战和陌生的概念。诸如测试、gems、社区名人录、rake、rdoc、Sinatra 和 Rails 之类的网络内容都非常混乱。这是一次有意义的经历，但这一探索包含了大量的尝试和错误。这篇文章希望能澄清一点，对 Ruby、它的社区、它的生态系统以及 Ruby 爱好者使用的工具给出一个清晰而全面的介绍。

## Ruby 的想法

> Ruby 是一种动态的开源编程语言，专注于简单和高效。它有一个优雅的语法，读起来很自然，写起来很容易。–**ruby-lang.org**

Ruby 是一种强调简单的语言。它试图消除冗长和混乱。Rubyists 倾向于支持简洁明了的表达意图的代码。

例如:

```
5.times do
  puts "This is beautiful and readable"
end
```

或者:

```
puts "car".upcase
```

你可以读一下。我敢用我最后一份薪水打赌，你能阅读并理解它的功能。没有杂乱无章，没有不必要的行话。没有不必要的括号或符号。它简单、清晰、简洁。

这是 Ruby 社区本质的一部分。冗长和杂乱是 ruby 爱好者所厌恶的(尽管有些人仍然不明白)。也有普遍接受的风格指南。这个社区已经形成了一种非常精致的审美，他们不仅关注意图和生产力，甚至关注 Ruby 代码在屏幕上的布局。

全面了解惯用的 Ruby 以及应该如何编写 Ruby 代码。拉斯·奥尔森的《雄辩的鲁比》是一个很好的参考。

## 初级人群

让我们看看一些主要的 Ruby 社区成员。

### 松本幸弘:红宝石的创造者

松本幸宏在 1990 年代中期创建了 Ruby，他是 Ruby 今天的大部分决策的幕后人。Matz 仍然领导着该语言的参考实现 MRI(用于 Matz 的 Ruby 解释器)的开发。

你可以在 Twitter 上关注他，地址是 [@yukihiro_matz](http://twitter.com/yukihiro_matz) 。

### 大卫·海涅梅尔·汉森:`Ruby on Rails`的创造者

David Heinemeier Hansson 从他在 Basecamp 的工作中创建了 Ruby on Rails (Ruby 最流行的 web 框架)。他在 2004 年 7 月将该框架作为开源软件发布。他很果断，直言不讳地表达自己的观点。DHH(他在社区中的名字)的想法是 Rails 成功的重要原因。

你可以在 Twitter 上关注他，地址是 [@dhh](http://twitter.com/dhh) 。

### Aaron Patterson: Ruby 和 Rails 核心撰稿人

> 白天，Aaron Patterson 是 Red Hat 的一名温和的程序员，他的 Ruby 代码几乎和他一样好看。晚上，他穿上他的粉色战士服，这让他有能力为许多开源项目做出贡献，比如 nokogiri、Ruby 和 Rails。如果你看到有人在会议上亲吻马茨或 DHH，那很可能就是他。–[Ruby on Rails 内核](http://rubyonrails.org/core/)

我可能无法更好地解释亚伦的酷怪。

你可以在推特 [@tenderlove](http://twitter.com/tenderlove) 和 http://tenderlovemaking.com[的博客](http://tenderlovemaking.com)上找到他。

在 Ruby 社区中有更多了不起的人。你会在旅途中遇到他们。作为一项家庭作业，去读一读桑迪·梅斯，她的精彩演讲，以及她令人惊叹的书。

## Gems 和依赖性管理

依赖项管理器提供了一种简单的方法来管理您使用的库。大多数现代编程语言都有专门的依赖管理器。比如 PHP 有 Composer，Node。JS 有 npm，Ruby 有 [RubyGems](http://rubygems.org) 。

Ruby 库被称为`gem`。你可以在你的系统上安装一个 gem(如果你已经安装了 Ruby ):

```
sudo gem install <gemname>
```

(*你可能不需要`sudo`* )

这个命令将把库安装到全局 Ruby 安装中，但是如果您想为一个项目安装特定的 gem 呢？邦德来了。

Bundler 通过跟踪和安装所需的 gem 和版本，为 Ruby 项目提供了一个一致的环境。

要使用 Bundler，在您的项目根目录中创建一个`Gemfile`并列出您需要的 gems(`bundle init`将为您创建文件)。

这里有一个例子`Gemfile`:

```
source "https://rubygems.org"

gem "rails"
gem "nokogiri"
gem "odyssey"
```

要安装 gem 文件中的所有 gem，请运行:

```
bundle install
```

从项目目录中。

你甚至可以用 bundler 轻松制作宝石。要了解更多信息，请查看 [RubyGems](http://guides.rubygems.org/rubygems-basics/)

## 测试

与许多其他语言不同，Ruby 社区非常重视测试。自动化测试允许你运行一个命令，并且**知道**，你的应用程序中的一切都在工作。测试改进你的设计，并给你一个简单的“它工作”的检查。如果你不知道如何用 Ruby 编写测试，看看这个[网站的截屏](https://www.sitepoint.com/simple-testing-ruby-using-minitest/)。

如果你想成为一名合格的 Ruby 开发者，并向全世界发布你的代码，你就必须编写测试。相信我，测试会让你和你同事的生活变得更好。

## Rails、Sinatra 和 Web

如果你还没有注意到，Ruby 是一种通用编程语言，而不仅仅是像 PHP 一样的脚本语言。所以，Ruby 几乎可以用于任何事情。但是 Ruby 最受欢迎的用途之一是 web 开发，部分原因是`Ruby On Rails`使得完成这项工作变得极其容易。

Rails 是一个全栈的 web 应用框架。它鼓励约定胜于配置，这意味着它提供了架构，并期望某些事情以某种方式进行。这个惯例带来了很大的权力。由于非凡的领导力和清晰的愿景，Rails 成为世界上最受欢迎的 web 应用程序框架之一。

但是如果你不想要一个巨型的全栈框架呢？如果您只想创建几条路线，或者不需要 Rails 提供的功能，请使用 Sinatra。

Sinatra 是一个微框架，它给了你很大的灵活性，但没有 Rails 的所有功能和约定。

还有很多其他的 Ruby web 框架，比如 Volt、Padrino 和 Lotus，它们正在获得关注，但是 Rails 和 Sinatra 必须在您的工具包中。

## 使用 Rake 实现自动化

在编写软件时，有许多事情你必须一遍又一遍地做，比如:

*   清理您的 Gemfile
*   初始化 Git 存储库
*   为你的工具箱设置宝石，比如 Bootstrap

使用 Rake，您可以创建一个名为<something>的任务，例如，运行您的所有测试。耙任务用`rake <something></something>`执行。你可以传入参数，用 Rake 做很多很酷的事情。</something>

像 Rails 这样的框架提供了默认的 Rakefiles，可以自动完成很多与 web 开发相关的任务。例如，在 Rails 项目中，您可以运行:

```
rake routes
```

要在 Rails 应用程序中查看所有已声明的路线，或者

```
rake db:migrate
```

将迁移(创建您的表和其他数据库对象)您的数据库。

要了解更多关于 Rake 的信息，请查看 SitePoint 上的[Rake:Automate the All Things](https://www.sitepoint.com/rake-automate-things/)。

## 包装东西

我希望你现在更熟悉 Ruby 社区和生态系统。这应该给你一个 Ruby 世界的介绍。到目前为止，你只是在大海中跋涉。既然你知道水很诱人，那就开始吧。

## 分享这篇文章