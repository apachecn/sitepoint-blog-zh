# 宝石挖掘:深入了解 Bundler 的宝石

> 原文：<https://www.sitepoint.com/gemfile-mining-a-dive-into-bundlers-gemfile/>

![Bundler](img/5b0e8b2c532a703bff8dd69405d3bea6.png)

Bundler 非常棒，这也是它成为 Ruby 应用程序事实上的包和依赖管理器的原因。我使用过 npm 和 golang vendoring 以及其他语言依赖管理器，但是它们都无法与 Bundler 提供的简单性相提并论。

我相信你知道，Bundler 善良的根源是 **Gemfile** 。Gemfile 列出了我们的应用程序完成工作所需的 gem。如果你和我一样，我从各种 Rails(和其他)教程中学到了如何使用 Gemfile 和 Bundler。我看到人们在打字

```
 gem "a_gem" 
```

然后，运行`bundle install`并观察所有依赖项的安装。这两个步骤是如此不言自明，以至于我从来没有真正被推动去做更多的研究。更不用说，在多年的依赖和其他令人毛骨悚然的令人沮丧的方法后，Bundler 是一个天赐之物。大声抱怨过去的日子，说你们这些自以为是的家伙现在过得有多轻松

在最初盲目跟随步骤的 Bundler 教程之后，我根据需要选择了其他 Gemfile 选项，如`group`、`path`和`github`。然而，前几天我决定通读 Bundler 文档，特别是关于 Gemfile 的文档，我对所有我没有使用过的选项感到惊讶。我学到了一些东西，所以我想其他人也可以。

在今天的帖子中，我将在 Gemfile 中挖掘选项。这里真的没有你在优秀的 Bundler 文档中看不到的东西，但是你没有读过，对吧？？？*大声抱怨为什么我们不能拥有美好的事物*

## 基础知识

总是一个好的开始，这是你可能已经知道的东西。

### 来源

所有 gem 文件的顶部都有这样一行:

```
source "https://rubygems.org" 
```

这一行告诉 Bundler 使用 [Rubygems 站点](https://rubygems.org)作为此处列出的所有宝石的唯一来源。你可能不知道:

*   您可以有多个`source`行，但不建议这样做，它们将按照列出的*反向*顺序进行搜索。我真的不喜欢这些，但它就在那里。
*   如果您使用的`source`是私有的 gem 服务器，您需要使用`bundle config`设置用户名和密码。不要把它放在 gem 文件中，因为它会出现在 Github 上，然后你的私人宝石会被公开，混乱随之而来。
*   好的，你*可以*把用户和密码放入 URL 本身(`https://user:pass@gem.server.com`)，但是不要。你无论如何都要做，不是吗？*抱怨年轻人从不倾听*

### 宝石

没错。我们都知道如何在 gem 文件中指定宝石，对吗？

```
gem "name-of-gem" 
```

那条线将拉入最新版本的 gem。显然，您应该将您的依赖项锁定到您知道可用于您的应用程序的版本。有几种方法可以指定 gem 版本:

*   使用特定版本:`gem "name-of-gem", "1.0"`。你可以通过搜索你的宝石并查看列出的“版本”在 Rubygems.org 的(假设那是你正在使用的`source`)上找到特定的版本。
*   使用一个版本运算符:`gem "name-of-gem", ">1.0"`。这将引入 1.0 之后的所有宝石。
*   使用那个奇怪的 squiggly-stabby 操作符:`gem "name-of-gem", "~>1.3"`。这个操作符，有些人称之为“旋转”,基本上是说“任何高于这个版本，但低于下一个小版本的版本”。既然我确信你知道什么是[语义版本](http://semver.org/)，我就不需要解释次要版本是第二个数字。在示例中，它表示“任何大于或等于 1.3 但小于 1.4 的版本”。它也适用于主要版本，所以`~>1`会在`2`之前获取最新版本。当你使用预发布版本时，就有点奇怪了，比如`~>1.0.rc.1`，这意味着它将匹配所有的`1.0.rc`预发布版本。
*   使用一个复合需求:`gem "name-of-gem", "~>2.2", ">=2.2.1"`。在这里，我们需要一个特定的版本，但也可以接受较新的次要版本。没人会这么做。

### 组

Bundler 允许您将依赖项分配到不同的组中。这通常用于将不同的 gem 安装到开发环境中，而不是安装到生产环境中。您可以在宝石的同一行上为宝石指定组，或者将宝石放在`group`块中:

```
gem "my-dev-tool", group: :development

group :development, :test do
  gem "my-test-tool"
end 
```

然后，您可以使用`--without`命令行选项到`bundle install`来避免将特定的组(或多个组)安装到特定的环境中。

## 雷蛇发现

让我们来谈谈指定宝石的其他选项，您可能见过也可能没见过，但可能不是 100%清楚。至少，这是我对他们的感觉。

### 平台

平台允许你告诉 Bundler 为特定版本的 Ruby 安装一个 gem。平台基本上是组，但是 Bundler 根据您当前的 Ruby 版本来处理 gems 的安装(或不安装)。这里列出了可用的平台[。](http://bundler.io/man/gemfile.5.html#PLATFORMS-platforms-)你可以这样使用它:

```
gem "my-java-gem", platform: jruby 
```

如果我`bundle`和我目前的 Ruby 是 MRI，`my-java-gem`就不会安装。

### `install_if`

另一种控制宝石安装的方法是`install_if`。传递一个计算结果为布尔值(意思是“truthy”或“falsy”)的 proc 或 lambda 将控制块中宝石的安装。从文档中:

```
install_if -> { RUBY_PLATFORM =~ /darwin/ } do
  gem "pasteboard"
end 
```

也许你有另一个环境变量来决定你是否安装一些 gem。我不能明智地解释你为什么做事情，我能做的只是向你展示工具。

### 需要

如果你曾经在一行宝石中见过`require`关键字，就像这样:

```
gem "rspec", require: "spec" 
```

这意味着宝石的名称和你要求宝石的名称是不同的。你看，当有东西调用`Bundler.require`时，Bundler 会要求所有的 gem 依赖。所以，在这个例子中，你希望它是`require "spec"`，而不是`require "rspec"`。

我们有时也会看到这样一行字:

```
gem 'rspec', require: false 
```

这告诉 Bundler 在调用`Bundler.require`时不需要`rspec`。例如，Rails 将调用`Bundler.require`作为其魔法的一部分，这就是为什么你所有的宝石都已经被需要了。

默认情况下，Bundler 将在 gem 文件中安装所有 gem，并将其加载到内存中。如果我们不想加载一个特定的 gem(可能因为我们将在代码中动态地做这件事),我们可以将 require 关键字设置为 false。

### 覆盖源

我提到了位于 gem 文件顶部的关键字`source`,但是如果您碰巧有从私有 gem 服务器提供的 gem，您可以单独覆盖这些 gem 的`source`。这可以在单个宝石的基础上完成，也可以在一个块中的多个宝石上完成:

```
gem "a-gem", source: "https://my-private-gem-server.com" 

source "https://my-private-gem-server.com" do
  gem "a-gem"
  gem "b-gem"
end 
```

### 饭桶

另一种更常见的方法是使用`git`、`github`或`path`选项，它们分别来自 git 库、Github 或文件路径。

当直接从 git 中提取 gem 时，只需要指定到存储库的路径:

```
gem "my-git-gem", git: "https://github.com/me/my-git-gem.git" 
```

你需要获得回购，所以它需要是公开的，在你的网络上，等等。如果您的 git 存储库有多个 gemspec，那么块语法也可以工作:

```
git "http://github.com/me/many_gems.git" do
  gem "a-gem"
  gem "b-gem"
end 
```

顺便说一下，那些存储库必须有`.gemspec`文件。否则，您必须提供一个版本:

```
gem "my-git-gem-with-on-spec", "0.1", git: "https://github.com/me/my-git-gem-with-no-spec.git" 
```

如果您想引入一个分支、标签或特定的提交，该怎么办呢？Bundler 允许您分别使用`branch`、`tag`和`ref`选项:

```
gem "my-git-gem", git: "https://github.com/me/my-git-gem.git", branch: "the_new_stuff"
gem "my-git-gem", git: "https://github.com/me/my-git-gem.git", tag: "1.0.rc2"
gem "my-git-gem", git: "https://github.com/me/my-git-gem.git", ref: "5acdb" 
```

顺便说一下，`ref`选项接受提交散列的前 5 个字符(或更多)。

最后，如果你正在拉一个有大量子模块的 git 存储库(人们这样做？)，有一个布尔`submodule`选项:

```
gem "my-gem-of-submodules", git: "https://github.com/me/my-gem-of-submodules.git", submodules: true 
```

#### 自定义 Git 源

事实证明，您可以定义定制的 git 源。Bundler 提供了 3 个现成的 git 源:`github`、`bitbucket`和`gist`。这提供了一些非常酷的简写:

```
gem "my-git-gem", github: "me/my-git-gem.git" 
```

只需在 Github(或 Bitbucket)上提供 repo 的路径。如果所有者(或组织)与 git 存储库同名呢？它变得更小了:

```
gem "rails", github: "rails" # Works b/c the repo is https://github.com/rails/rails.git 
```

所有适用于`git`源的选项在这里也有效。顺便说一下，`github`选项使用的是`git`协议，这是不加密的，所以要相应地管理自己。

如果您在 Github 上的存储库是私有的，您可以[创建一个个人访问令牌](https://github.com/settings/tokens)并在 gem 文件中使用它:

```
gem "my-private-gem", git: "https://<the_token>:x-oauth-basic@github.com/me/my-private-gem.git" 
```

这使得使用私有 git repos 变得很容易，但是将令牌放在 Gemfile 中却是一个灾难。为了保证令牌的安全，设置一个环境变量(`GITHUB_TOKEN`)并在`git`选项中引用它:

```
gem "my-private-gem", git: "https://#{ENV['GITHUB_TOKEN']}:x-oauth-basic@github.com/me/my-private-gem.git" 
```

非常感谢这个要点第一次给我看。不幸的是，这种方法仍然有一个问题，因为它会将令牌写入到 **Gemfile.lock** 文件，该文件可能在源代码控制之下。那么，我们能做什么呢？

还有一种方法是使用`bundle config`来设置 auth。如果将 gem 文件中的行更改为:

```
gem "my-private-gem", git: "https://github.com/me/my-private-gem.git" 
```

然后跑

```
bundle config github.com <the_token> 
```

Bundler 将使用令牌代表您访问 github。所以，你所有的私人回购都是可以访问的。顺便说一下，这将值写入`~/.bundle/config`。嗯……也许我需要再写一篇深入 Bundler CLI 的文章。*抱怨我的工作永远做不完*

#### 要点

我从未使用过一个[要点](https://gist.github.com)作为宝石来源，所以我不太清楚它是如何工作的。我[做了一个](https://gist.github.com/e0bef10af2e0f9bb2ed703c721ca9d29)算出来:

```
gem "gist-gem", gist: "e0bef10af2e0f9bb2ed703c721ca9d29" 
```

那行得通。直到这个。我不知道为什么有人会这么做。但是，我也不了解我的空间。

最后，您可以为其他 git 服务器定制 git 源，如 Github Enterprise Server、Bitbucket Server(正式名称为 Stash)或其他源。这是文件中的一个例子:

```
git_source(:stash){ |repo_name| "https://stash.corp.acme.pl/#{repo_name}.git" }
gem "rails", :stash => "forks/rails" 
```

### 小路

如果你在本地有一个正在开发的宝石，你可以用`path`选项把它拉进你的捆绑器:

```
gem "mine-mine-all-mine", path: "~/my-gems/mine-mine-all-mine" 
```

当然，还有一个块选项:

```
path "~/my-gems" do
  gem "mine-mine-all-mine"
  gem "acts-as-myspace"
end 
```

## Ruby 版本

可以指定您的应用程序需要的 Ruby 版本和解释器(或引擎)。例如，如果您绑定了 Ruby 2.1 或更高版本，但不想假定 2.2 也可以，请将它放在 Gemfile 的顶部:

```
ruby "~>2.1" 
```

默认情况下，该版本显然是指 MRI。想抢 JRuby 怎么办？

```
ruby "~>2.1", engine: "jruby", engine_version: "9.0.3.0" 
```

也就是说你想要 JRuby 9000。`ruby`关键字还提供了一个`patchlevel`选项，如果你知道你需要什么补丁级别，它可以接受所有相同的 gem 版本操作符。顺便说一下，如果我提交一个补丁，它将是补丁级别`nicotine`、`adams`或`eye`。

一些版本管理器，比如 RVM，也会阅读 Ruby 指令并安装合适的 Ruby 版本，这样 Bundler 就不会抱怨我们使用了不同的 Ruby 版本。

## 全部完成

所以，就这样了。Bundler Gemfile 蒸馏。谁知道在 Gemfile 中安装 gem 有这么多选项？我没有，所以这种努力教会了我一些新东西，并再次确认了我对 Bundler 的爱，他是世界上最好的依赖管理人。我希望你也学到了一些东西。

有个问题问你:我错过了什么吗？还有什么我们应该包括在这里的技巧吗？这些天年轻人们和邦德勒在一起做什么？

衷心感谢恩里克·冈萨雷斯和弗雷德·希斯对本文的同行评议。尽管他们很年轻，但他们是非常有用的社会成员。

## 分享这篇文章