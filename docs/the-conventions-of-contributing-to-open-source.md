# 为开源做贡献的惯例

> 原文：<https://www.sitepoint.com/the-conventions-of-contributing-to-open-source/>

![Open Source Week](img/31a22f613e5e7d0c22f753b2e51b5549.png)

SitePoint 的开源周到了！整个星期我们都在发布关于开源、自由软件和社区的文章，所以请继续关注最新的更新。

![open source](img/f067ed4336643b159a79c730259d6a2e.png)

*这篇文章由[汤姆·帕金](https://www.sitepoint.com/author/tparkin)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

### 目录 

*   [导游](#h-guide)
*   [行为准则](#h-coc)
*   [报告错误/问题](#h-bugs)
*   [安全问题](#h-sec)
*   [所有其他问题](#h-otheriss)
*   [功能请求](#h-feat)
*   [帮助解决现有问题](#h-exis)
*   [撰写 Rails 文档](#h-cont)
*   [翻译文档](#h-tran)
*   [贡献源代码](#h-src)
*   [设置本地环境](#h-loc)
*   [编写代码](#h-writ)
*   [源代码控制约定](#h-scm)
*   [编码约定](#h-conv)
*   [测试代码](#h-test)
*   [记录变更](#h-docs)
*   [依赖关系管理](#h-dm)
*   [提交变更](#h-commit)
*   [分叉和拉取请求](#h-fork)
*   [讨论和迭代](#h-disc)
*   [向后兼容和向后移植](#h-back)
*   [结论](#h-end)
*   [评论](#comments)

我们都喜欢使用开源软件，对吗？我已经为开源做出了自己的贡献，主要是通过一些小的贡献。我过去曾经尝试过开源一些库，成功和失败的程度各不相同。我会说我在贡献者的光谱中间的某个地方。有些人做得多，有些人做得少。

如果你对成为 OSS 贡献者感兴趣，关于如何做到这一点的文章不在少数。事实上，[几年前我自己也写了一本](https://www.sitepoint.com/8-simple-steps-for-contributing-to-open-source/)。我将把一步一步的贡献留给那些有能力的文章。

今天，我想重点谈谈开源贡献的一些惯例，从开源创造者的角度来看。我这次旅行的工具是 Rails 贡献指南。众所周知，Rails 是一个非常非常成熟的框架，也是一项巨大的开源事业，由许多模块和成千上万的贡献者组成。所有这些活动都围绕着创建和维护 Rails，贡献者使用的文档和约定必须是全面的和格式良好的。

换句话说，当谈到开源软件如何创建贡献指南时，我觉得 Rails 是一个很好的研究。让我们看看我是不是对的。

## 指南

正如我提到的，我们将基本上浏览一下[“为 Ruby on Rails 做贡献”指南](http://edgeguides.rubyonrails.org/contributing_to_ruby_on_rails.html)，抽出并讨论 Rails 对贡献所采取的方法。以下是本指南开头的要点:

*   如何使用 GitHub 报告问题？
*   如何克隆 master 并运行测试套件。
*   如何帮助解决现有问题。
*   如何撰写 Ruby on Rails 文档？
*   如何贡献 Ruby on Rails 代码？

这是指南的作者认为对贡献者来说最重要、高水平的项目。让我们看看他们是如何解决每一个问题的。

## 行动守则

不过，在此之前，我想指出 Rails 确实有一个官方的[行为准则(CoC)](http://rubyonrails.org/conduct/) 。无论您是否同意这种文档的存在或需要，Rails 都有。因此，贡献者应该遵守 CoC。当你决定成为一名开源贡献者时，这给我带来了一个重要的考虑。

如果你打算开源你自己的作品，你应该仔细考虑 Rails CoC 中提出的问题。COC 通常是一个两极分化的问题，所以你是否选择有一个 COC 以及它说了什么是很重要的。你将如何处理投诉？如果你觉得有人骚扰或不尊重你是你贡献的一部分，你能做什么？确保你理解期望:你的和社区的期望。

## 报告错误/问题

Rails 选择 Github 问题跟踪来报告新的错误和问题。这是一个很好的选择，因为几乎每个人都会熟悉 Github，而且代码本身就托管在那里。Rails 团队区分基于安全的问题和不基于安全的问题。

### 安全问题

安全问题通过[不同的站点](http://rubyonrails.org/security/)报告，我喜欢把它看作是核心团队的一种蝙蝠电话。此外，还有一个非常不同的安全问题协议。

显然，对于像 Rails 这样的框架来说，安全性是一个非常重要的考虑因素。拥有不同的协议是有意义的，如果你的库有重要的安全考虑，你应该确保有一个好的方法来报告它们。

### 所有其他问题

如果问题与安全无关，那么可以使用 Github 问题。核心团队是令人敬畏的人，他们已经创建了一堆模板来帮助记者为给定的问题创建可执行的测试用例。以下是活动记录问题的模板:

```
begin
  require "bundler/inline"
rescue LoadError => e
  $stderr.puts "Bundler version 1.10 or later is required. Please update your Bundler"
  raise e
end

gemfile(true) do
  source "https://rubygems.org"
  # Activate the gem you are reporting the issue against.
  gem "activerecord", "5.0.0"
  gem "sqlite3"
end

require "active_record"
require "minitest/autorun"
require "logger"

# Ensure backward compatibility with Minitest 4
Minitest::Test = MiniTest::Unit::TestCase unless defined?(Minitest::Test)

# This connection will do for database-independent bug reports.
ActiveRecord::Base.establish_connection(adapter: "sqlite3", database: ":memory:")
ActiveRecord::Base.logger = Logger.new(STDOUT)

ActiveRecord::Schema.define do
  create_table :posts, force: true do |t|
  end

  create_table :comments, force: true do |t|
    t.integer :post_id
  end
end

class Post < ActiveRecord::Base
  has_many :comments
end

class Comment < ActiveRecord::Base
  belongs_to :post
end

class BugTest < Minitest::Test
  def test_association_stuff
    post = Post.create!
    post.comments << Comment.create!

    assert_equal 1, post.comments.count
    assert_equal 1, Comment.count
    assert_equal post.id, Comment.first.post.id
  end
end 
```

这是一个很好的主意，也是真正帮助人们报告问题的方法。它允许其他人快速重现问题，从而更快地解决问题。

### 功能请求

特性请求不会被报告为 Rails 的问题，相反，请求者应该为特性编写代码并作为贡献提交。这完全有道理，因此请求新特性属于新贡献指南的范畴。

## 帮助解决现有问题

对包括 Rails 在内的任何库做出贡献的一个很好的方式就是仔细查看未解决的问题，并尝试推动它们找到解决方案。例如，选择一个问题并尝试重现它。如果贡献者遵循了惯例，那么应该有一个可执行的测试用例。

如果有人通过拉请求(PR)提交了修复，那么您可以检查 PR 并验证修复。

在这两种情况下，您都是在帮助核心团队，验证事情是否如所报告的那样，允许他们跳过问题的分类并开始修复。

如果你打算提供一个开源项目，考虑一下除了新代码之外你可以使用的帮助。人们可以测试新功能和报告的错误吗？我打赌他们可以，而且这对于很多人来说是一个较低的准入门槛。

## 为 Rails 文档做贡献

Rails 有一整节专门讨论框架的文档。同样，这也是新的开源者需要学习的东西。你的图书馆是如何记录的？是否清晰易找？另外，其他人如何改进你的文档？确保你回答了这些重要的问题。

在大多数情况下，您不需要 Rails 提供的文档。它规模庞大，被全球成千上万(如果不是更多)的人使用。但是，这里的要点是为您的文档提供一个好的答案。我可以告诉你，我曾经试图发布一个非常薄的 README 作为 docs 的库，但进展并不顺利。

### 翻译文档

如果你期望说不同语言的人会使用你的开源库，那么用一种简单的方法来翻译文档可能是个好主意。Rails 通过要求翻译人员创建一个以语言命名的新目录，复制现有的文档，并翻译它们。

## 为源代码做贡献

我们到达了大多数人在为开源做贡献时考虑的核心:代码。正如您所料，指南的这一部分是迄今为止最长的。

### 设置本地环境

任何贡献者想要做的第一件事就是弄清楚如何在本地运行你的代码。如果他们不能很容易地做到这一点，他们就不能很容易地做出贡献，而你正在拒绝帮助者。

Rails 提供了两种方式来设置您的环境:“简单”方式和“困难”方式。最简单的方法是运行“Rails 开发盒”,这是一个随时可以运行的虚拟机(VM)。如果你有 VirtualBox 和 vagger，你可以启动它并开始写代码。那*就是*容易。

所以，还有一个困难的方法，这意味着你基本上在本地安装所有的东西。Rails 开发的“一切”包括:

*   饭桶
*   SQLite(带开发库)
*   Memcached
*   关系型数据库
*   一种数据库系统
*   雷迪斯

其中许多都有设置子任务。呀！这是一条艰难的道路。

无论如何，这里的要点是 Rails 已经记录了如何设置本地环境并获得编码。所有开源产品都应该这样做。顺便说一下，我想知道为什么没有基于 Docker 的 Rails 开发环境/指令。嗯…也许有人可以贡献一个…

## 写代码

在为 Rails 这样的开源库编写代码时，有几个事项需要考虑。

### 源代码管理约定

首先是如何将您的代码与主分支隔离开来。换句话说，贡献者应该如何使用 Git。

Rails 让它非常简单，只要求人们创建一个分支并在其中工作。哦，他们提供了一个很酷的方法来生成一个虚拟 Rails 应用程序，它将使用你的本地分支。

### 编码约定

Rails 确实列出了一组它希望人们遵守的代码约定，这是一个好主意。有一些宝石可以帮助执行这些事情，比如 [Rubucop](https://github.com/bbatsov/rubocop) 。事实上，Rails GitHub 存储库中有一个 Rubucop 配置文件(`.rubocop.yml`)，因此贡献者可以确保他们的代码符合约定。

### 测试代码

显然，你会希望贡献者在他们的代码上编写和运行测试。你应该对他们如何做有明确的指示。让 Rake 任务或者类似的东西运行你的所有任务可能是个好主意。

如前所述，Rails 由许多许多模块组成。因此，不需要对整个 Rails 代码库运行测试。Rails 记录了在给定模块上运行测试的方法，以允许贡献者只测试他们更改的内容。很好。

此外，Rails 使用 Travis CI(一个持续集成服务器)来运行所有测试。这是一个安全网，这意味着不强迫每个人为每一个贡献运行所有的测试是可以的。同样，让测试运行起来容易，否则没人会运行测试。

### 记录变更

为您的库保留一个运行中的变更日志是一个好主意。Rails 要求贡献者在 Changelog 中放置一个新条目，记录一个条目应该是什么样子。

### 依赖性管理

毫无疑问，你的库将依赖于其他 Ruby gems。如果一个贡献者需要改变/升级一个以使他们的代码工作，会发生什么？你应该考虑清楚。

Rails 让它变得简单，因为 Bundler 让它变得简单。只需更新 Gemfile.lock 并运行您的测试。

### 提交更改

好的提交信息就像一件安全背心:有时它们很难穿上，但是，如果你需要它们，它们是无价的。我强烈建议您为提交消息制定一个标准。Rails 有，我在这里展示了:

```
Short summary (ideally 50 characters or less)

More detailed description, if necessary. It should be wrapped to
72 characters. Try to be as descriptive as you can. Even if you
think that the commit content is obvious, it may not be obvious
to others. Add any description that is already present in the
relevant issues; it should not be necessary to visit a web page
to check the history.

The description section can have multiple paragraphs.

Code examples can be embedded by indenting them with 4 spaces:

    class ArticlesController
      def index
        render json: Article.limit(10)
      end
    end

You can also add bullet points:

- make a bullet point by starting a line with either a dash (-)
  or an asterisk (*)

- wrap lines at 72 characters, and indent any additional lines
  with 2 spaces for readability 
```

同样，它并不太复杂，但它有它的价值。

需要注意的一点是，需要使用来自存储库的最新源代码来更新您的特性/bug 分支，以避免与他们所做的工作发生冲突。Rails 指南详细介绍了这一点，这是记录您的期望的另一个好例子。

哦，还有一件事，鼓励贡献者放弃他们的提交是一个好主意。你不想要一个有 1023 次提交的公关，因为只有一次就够了。

### 分叉和拉取请求

记录您希望如何在源代码控制中接受代码是非常重要的。如果你使用 Github，有一条很好的路可以走，那就是派生存储库并发布 PRs。如果您的开源存储库很小，您可以从主存储库中的分支发出 PRs。不管你的选择是什么，记录下你想要的。

### 讨论和迭代

Rails 指南更进一步，详细说明了贡献者在讨论一个提议的变更时可以期待什么。Rails 非常庞大，所以在人们收到回复时设置一个非正式的 SLA 是个好主意。

此外，关于如何更新公关几句话是聪明的。就像最初的 PR 一样，在这里取消提交可能是您想要的。

## 向后兼容性和向后移植

我知道我听起来有点像破唱片，但是 Rails 是巨大的，全世界的人都依赖它工作和谋生。在任何时候，世界上都有各种版本的 Rails 在生产。对于那些仍然受支持的版本，您可能需要反向移植修复程序。

Rails 指南记录了如何基于旧版本的框架创建分支。这使得贡献者很容易移植和测试以前版本的 Rails 代码。您可能不需要这种程度的准备，但是您应该仔细考虑并交流您的库版本将如何更新，如果有的话。

## 结论

Rails 已经存在了足够长的时间，已经创建了一个值得效仿的贡献指南。我认为最重要的主题是:让人们更容易做出贡献。如果你正在考虑开源一个项目，你可能会比在 Rails 上建模你的贡献策略更糟糕。请务必思考:

*   贡献者如何设置本地环境？
*   除了写代码，人们还能如何贡献*？*
*   如何管理我的依赖关系？
*   进行测试容易吗？
*   我如何保持我的文档是最新的？

我希望你觉得这篇 Rails 贡献指南很有价值。我知道我做到了。现在，我要去设置我的本地 Rails 环境…也许我会使用 Docker。开源快乐！

## 分享这篇文章