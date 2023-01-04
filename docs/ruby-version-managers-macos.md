# 用于 macOS 的 Ruby 版本管理器的比较

> 原文：<https://www.sitepoint.com/ruby-version-managers-macos/>

在这篇文章中，我将比较 Mac 上最流行的 Ruby 版本管理器:Chruby、Rbenv 和 RVM，以及 Asdf，一个用于多种语言的版本管理器，和 Frum，一个用 Rust 编写的全新版本管理器。我会根据你最可能需要的东西提出建议。

## 预装的 Ruby 不适合开发者

不熟悉 Ruby 的人很高兴地发现，Ruby 是预装在 macOS 上的。当一个更有经验的开发人员告诉他们，“不要使用 Mac 系统 Ruby”时，他们经常感到失望我已经在别处写了[关于原因。简而言之，系统 Ruby 是给 macOS 用的，不是给你用的。这对于运行实用程序脚本来说很好，但是对于开发来说，系统 Ruby 已经过时了，如果不小心的话，gems 的安装可能会导致令人头疼的问题(包括系统安全违规)。](https://www.freecodecamp.org/news/do-not-use-mac-system-ruby-do-this-instead/)

## 自制的一个红宝石问题

家酿是软件包管理器，它已经成为向 macOS 添加开发者软件的近乎标准的工具。家酿使得安装新的 Ruby 版本变得容易，避免了对系统 Ruby 的依赖。但是自制有两个问题。第一，家酿的 Ruby 版本可以在安装了其他家酿包的情况下自动意外地被更新的版本替换(这个问题由`brew pin ruby`解决)。更重要的是，Homebrew 只让你安装一个版本的 Ruby。如果你用 Ruby 开发或维护不止一个项目，你可能需要在 Ruby 版本之间切换。这就是你需要版本管理器的地方。

## Asdf 支持多种语言

Asdf 是一个支持多种语言的版本管理器。如果您使用 Ruby 通过 Rails 6 开发 web 应用程序，您需要安装 Node 和 Yarn 来构建一个 Rails starter 应用程序。(Rails 7 可能会消除对 Node 的需求，但目前还不能确定。)

因此，对于 Rails 开发人员来说，Asdf 是一个理想的版本管理器。如果您使用 Python、Elixir 或许多其他语言开发应用程序，它也会为您提供很好的服务(参见支持的语言列表)。使用 Asdf 有负面影响吗？只有两个。它使用[垫片](https://en.wikipedia.org/wiki/Shim_(computing))拦截常见命令，这可能会使故障排除变得困难。此外，对于某些操作，它可能会有点慢。如果你正在用多种语言开发，我建议你试试 Asdf，看看你是否喜欢它。如果您发现 Asdf 降低了您的速度，请切换到 Frum，这是用 Rust 编写的只支持 Ruby 的版本管理器。

## 仅限 Ruby 的版本管理器

如果你只想用 Ruby 开发应用程序，可以从四个只支持 Ruby 的版本管理器中选择:Chruby、Frum、Rbenv 和 RVM。这四个版本的区别在于速度、复杂性以及如何切换 Ruby 版本。让我们先看看最古老的:RVM。

### 延髓头端腹内侧核群

RVM 是第一个受欢迎的 Ruby 版本经理。至今仍被维护和广泛使用。当您更改目录时，RVM 修改系统`cd`命令来设置环境变量。为了覆盖`cd`命令，RVM 安装脚本更改了 shell 配置文件(较新 MAC 上的`~/.zshrc`文件)。

RVM 恰当地显示了环境变量，揭示了 Ruby 和 gem 的版本和位置，这有助于诊断问题。然而，RVM 包括额外的功能，如宝石。不再需要 Gemsets，因为 Bundler 被添加到核心 Ruby 中来管理 gem 依赖项。额外的复杂性是不需要的，所以许多开发人员已经转向较新的版本管理器，如 Rbenv、Chruby 或 Frum。

### Rbenv

和 RVM 一起， [Rbenv](https://github.com/rbenv/rbenv) 一直是 Ruby 最受欢迎的版本管理器。Rbenv 使用[垫片](https://en.wikipedia.org/wiki/Shim_(computing))来拦截常见的 Ruby 命令。(Asdf 也使用垫片。)

在用 Homebrew 安装了 Rbenv 之后，您必须修改您的`~/.zshrc`文件，以便 Rbenv 填充程序优先于普通的 Ruby 命令。shim 调用`rbenv exec`命令，该命令决定在运行任何 Ruby 命令之前使用哪个版本的 Ruby。

垫片有两个缺点。首先，垫片通过隐藏实际命令使故障排除更加困难。例如，`which ruby`命令显示的是 Rbenv shim，而不是实际的 Ruby 版本(如果想看到实际的 Ruby 命令，必须设置`RBENV_DEBUG`环境变量)。其次，填充程序会给 Ruby 命令的执行增加几微秒的延迟。(每次运行 Ruby 命令时，Rbenv 都会进行干预，这与 RVM 不同，后者只在您更改目录时进行干预。)对于注意到延迟的开发人员，或者不喜欢 shims 的想法的开发人员，有 Chruby 或 Frum。

### Chruby

Chruby 不像 RVM 或 Rbenv 那么知名，但是它受到了一些杰出的 ruby 开发者的支持。与 RVM 不同，它不覆盖`cd`命令。与 Rbenv 或 Asdf 不同，它不使用垫片拦截命令。您需要修改您的`~/.zshrc`文件，以便 Chruby 在您的本地 shell 环境中运行。没有必要修改你的`$PATH`，因为 Chruby 已经根据你选择的 ruby 版本为你做了。

Chruby 还为 ruby 设置了某些环境变量。这就是 Chruby 在 ruby 版本之间切换所需要的全部内容。使用`which`进行故障排除时效果很好。我用了几年 Chruby，没有什么问题。Chruby 是一个很好的选择，但是你可能想试试 Frum，最新的 ruby 版本管理器。

### 鼓

Frum 是用 Rust 编写的，Rust 是一种很好的语言，用于系统软件的快速命令行执行。Frum 是新的(2021 年初发布)。与 Asdf、Chruby 或 Rbenv 不同，它包含一个内置的 ruby installer 命令，因此安装 Ruby 不需要额外的程序。安装程序很快，Frum 不需要任何依赖(它是一个一体化的 Rust 可执行文件，可以用 Homebrew 安装)。

安装 Frum 之后，您必须修改您的`~/.zshrc`文件，以便 Frum 在您的本地 shell 环境中运行。像其他版本管理器一样，它检查项目目录中的`.ruby-version`文件，如果没有为项目指定版本，它将默认为全局 Ruby 版本。没有垫片(不像 Asdf 或 Rbenv ),它不覆盖`cd`命令(不像 RVM)。虽然它是新的，但我推荐它，因为它是多功能的，而且速度很快。

## 为 Ruby 安装版本管理器

我已经写了一个完整的在 Mac 上安装 Ruby 的指南。在本指南中，我提供了以下说明:

*   [用 Asdf 安装 Ruby】](https://mac.install.guide/ruby/6.html)
*   [用 Frum 安装 Ruby】](https://mac.install.guide/ruby/14.html)
*   [用 Chruby 安装 ruby](https://mac.install.guide/ruby/12.html)
*   [用自制软件安装 Ruby】](https://mac.install.guide/ruby/13.html)

如果您想探索 RVM 或 Rbenv，请访问:

*   [RVM](https://rvm.io/)
*   [Rbenv](https://github.com/rbenv/rbenv)

## 关于 Docker 的一个注记

如果你提到 Ruby 版本管理器，有人可能会说，“我使用 Docker。”Docker 是一个容器化工具，一些开发者用它来进行版本管理。Docker 的主要用例是创建一个可复制的虚拟服务器，其中包含运行应用程序所需的任何软件依赖项的配置版本(语言、数据库、消息队列)。因此，它非常适合创建开发环境的“冻结”版本，以便部署到服务器上。

您也可以在容器中进行本地开发，但是与版本管理器相比，它运行速度较慢，需要更多的内存，并且增加了配置的复杂性。为了简单起见，我避免使用 Docker 进行本地开发，除非应用程序非常复杂。

## 结论

如果你在 Ruby 中处理多个项目，你就不仅仅是一个普通的 Ruby 用户。确保您在本地开发环境中安装了一个很好的版本管理器。

我喜欢 Asdf，因为我用 Ruby 和 JavaScript 开发，Asdf 为我提供了一个两种语言的版本管理器。然而，Asdf 使用了垫片(比如 Rbenv ),所以偶尔会比我喜欢的要慢。我还发现 Asdf 隐藏了实际的命令，无法用`which`命令进行故障排除，这很烦人。如果这些缺点超过了管理多种语言的便利，我推荐使用 Frum 在您的 Ruby 版本之间切换。版本管理器应该简单，在你工作的时候不要碍事。

## 分享这篇文章