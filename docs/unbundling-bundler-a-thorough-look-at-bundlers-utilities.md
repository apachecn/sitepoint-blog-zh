# 解开捆绑:彻底看看捆绑的公用事业

> 原文：<https://www.sitepoint.com/unbundling-bundler-a-thorough-look-at-bundlers-utilities/>

![Bundler](img/5b0e8b2c532a703bff8dd69405d3bea6.png)

在我之前的文章中，我详细介绍了 Bundler 的 Gemfile 和它包含的所有魔法。然而，Gemfile 只是 Bundler 生态系统的一部分，所以我回到文档中挖掘其他领域，并在 Bundler 的另一部分着陆，我并不知道我应该知道的。

本文将关注 [Bundler 实用程序](http://bundler.io/v1.12/man/bundle.1.html#UTILITIES)，它们是 Bundler 附带的命令，不被视为**主要**命令。顺便提一下，这里是所有的 Bundler 命令，按照它们是主要命令还是实用程序来分类:

*   **主要**:安装、更新、打包、执行、配置、帮助
*   **工具**:检查，列表，显示，过期，控制台，打开，锁定，即，初始化，gem，平台，清理，binstubs，注入

今天，我将介绍第二类工具，以引起人们对 Bundler 提供的工具的更多关注。我猜你至少会从*T2 那里学到一件关于 Bundler 的新东西。我知道我做到了。如果我错了，请直接向`/dev/null`投诉。*

让我们开始吧。对于本文，我将从一个空的目录(和一个空的 gemset)开始，浏览各种实用程序，看看它们是做什么的。

## 捆绑初始化

如果您发现自己在创建一次性的 Ruby 项目，并手动写出一个新的 Gemfile，那么您应该利用`bundle init`。我做了相当多的工作，创建了一个 Gemfile 并手动添加了`source "https://rubygems.org"`等。这样做似乎很傻，因为我可以直接输入:

```
$ bundle init
Writing new Gemfile to /path/Gemfile 
```

这将创建一个如下所示的 Gemfile:

```
# A sample Gemfile
source "https://rubygems.org"

# gem "rails" 
```

很好。现在我可以不用担心在`https:/roobygemmms.org`中是否有打印错误，只需开始将我的宝石添加到文件中。

此外，如果您有一个包含 gem 依赖项的`.gemspec`,您可以指定`--gemspec=<name of gem>.gemspec`,生成的 gem 文件将包含来自所述 gemspec 的 gem。它甚至会为您的开发依赖项创建一个`development`组。

好了，让我们取消对那一行的注释，这样我们就有东西可以玩了。

## 捆绑列表

想知道当前包中有什么，但又懒得打开 Gemfile？那么，输入`bundle list`,你会得到这样的结果:

```
$ bundle list
Gems included by the bundle:
  * actioncable (5.0.0)
  * actionmailer (5.0.0)
  * actionpack (5.0.0)
  * actionview (5.0.0)
  * activejob (5.0.0)
  * activemodel (5.0.0)
  .... 
```

在您认为`bundle list`微不足道之前，您应该知道它使用 Gemfile.lock 来告诉您什么是什么。然而，如果您删除该文件并键入`bundle list`,您将得到:

```
$ rm Gemfile.lock
$ bundle list
Starting resolution (2016-07-27 12:56:04 -0400)
Creating possibility state for rails (1 remaining)
  Attempting to activate rails (5.0.0)
  Activated rails at rails (5.0.0)
  Requiring nested dependencies (activesupport (= 5.0.0), actionpack (= 5.0.0), actionview (= 5.0.0), activemodel (= 5.0.0), activerecord (= 5.0.0), actionmailer (= 5.0.0), activejob (= 5.0.0), actioncable (= 5.0.0), railties (= 5.0.0), bundler (< 2.0, >= 1.3.0), sprocket
s-rails (>= 2.0.0))
  Creating possibility state for sprockets-rails (>= 2.0.0) (1 remaining)
    Attempting to activate sprockets-rails (3.1.1)
    Activated sprockets-rails at sprockets-rails (3.1.1)
    Requiring nested dependencies (sprockets (>= 3.0.0), actionpack (>= 4.0), activesupport (>= 4.0))
    Creating possibility state for activesupport (>= 4.0) (1 remai
    .... 
```

因此，Bundler 从头开始解决问题，*为*建立了一个新的 Gemfile.lock. Sassy。顺便说一下，在最新的 Bundler 文档中没有关于`bundler list`的内容，所以我认为它可能会被淘汰…

## 捆绑检查

有时当你返回到一个应用程序去做一些工作时，关于`bundle install`是否已经完成以及你所有的依赖关系是否正常，这是一个谜。要解开这个谜，只需输入:

```
$ bundle check
The following gems are missing
 * rake (11.2.2)
 * i18n (0.7.0)
 * minitest (4.7.5)
 * multi_json (1.12.1)
 * thread_safe (0.3.5)
 * tzinfo (0.3.51)
 * activesupport (4.0.2)
 * builder (3.1.4)
 * erubis (2.7.0)
 * rack (1.5.5)
 * rack-test (0.6.3)
 * actionpack (4.0.2)
 * mime-types (1.25.1)
 * polyglot (0.3.5)
 * treetop (1.4.15)
 * mail (2.5.4)
 * actionmailer (4.0.2)
 * activemodel (4.0.2)
 * activerecord-deprecated_finders (1.0.4)
 * arel (4.0.2)
 * activerecord (4.0.2)
 * hike (1.2.3)
 * thor (0.19.1)
 * railties (4.0.2)
 * tilt (1.4.1)
 * sprockets (2.12.4)
 * sprockets-rails (2.0.1)
 * rails (4.0.2)
Install missing gems with `bundle install` 
```

给你。它不仅告诉我们没有安装依赖项，还告诉我们缺少哪些依赖项。经过一个`bundle install`，我们看到:

```
$ bundle check
The Gemfile's dependencies are satisfied 
```

酷毙了。通过查看`$GEM_HOME/specifications`目录中的各种 gemspecs 进行检查。因此，完全有可能从 gem 缓存中移除一个已安装的 gem，但是让`bundle check`说一切正常。当然，你必须做一些愚蠢的事情来达到那个目的，但是你已经做到了。

如果缺少 Gemfile.lock，还会构建它。以下是可用的选项:

*   `--dry-run`:告诉您缺少什么，但不构建 Gemfile.lock
*   使用不同的路径寻找宝石。这是一个“记忆选项”(稍后会有更多相关内容。)
*   `--gemfile=FILE`:使用`FILE`代替 Gemfile。

### sidemote:记忆选项

在某些情况下，公用事业公司将改变 Bundler 的配置。在上面的情况下，它告诉 Bundler 在这个项目中总是使用 gems 的那个路径，直到配置被重置或被另一个记忆的选项改变。所以，当我跑步的时候

```
$ bundle check --path=./gems
The following gems are missing
 * rake (11.2.2)
 * concurrent-ruby (1.0.2)
 * i18n (0.7.0)
 * minitest (5.9.0)
 ...
 Install missing gems with `bundle install` 
```

它创建`./gems`目录并创建(或修改)`./.bundle/config`，将`BUNDLE_PATH`设置为`./gems`:

```
$ bundle config
...usual...
bin
Set for your local app (/path/.bundle/config): "./bin"

path
Set for your local app (/path/.bundle/config): "/path/gems" 
```

同样，如果你:

```
$ cat ./.bundle/config 
---
BUNDLE_BIN: "./bin"
BUNDLE_PATH: "/Users/ggoodrich/projects/bundler/gems"
BUNDLE_DISABLE_SHARED_GEMS: true 
```

你可以看到**本地**配置基于我用于`--path`的值而改变。这可以用`bundle install --system`复位。从现在开始，我将用 **(RO)** 来表示记忆的选项。

## 捆绑展示

您可能知道也可能不知道，Bundler 会(默认)将当前的包安装到`$GEM_HOME`中。当然，除非你告诉它不要这样做。如果你不确定特定的宝石在哪里，做如下工作:

```
$ bundle show tzinfo
<YOUR $GEM_HOME>/gems/tzinfo-1.2.2 
```

如果你想仔细研究某个宝石或其他什么东西的来源，这是很有用的。同样，如果你不提供一个 gem 名称，你将得到与`bundle list`相同的输出。然而，如果你添加了`--paths`选项，它会显示所有宝石的路径。

```
$ bundle show --paths
<YOUR $GEM_HOME>/gems/ruby-2.3.0@bundler-test/gems/actioncable-5.0.0
<YOUR $GEM_HOME>/gems/ruby-2.3.0@bundler-test/gems/actionmailer-5.0.0
<YOUR $GEM_HOME>/gems/ruby-2.3.0@bundler-test/gems/actionpack-5.0.0
<YOUR $GEM_HOME>/gems/ruby-2.3.0@bundler-test/gems/actionview-5.0.0
<YOUR $GEM_HOME>/gems/ruby-2.3.0@bundler-test/gems/activejob-5.0.0
..... 
```

我不知道`--paths`选项。

## 捆绑打开

是我最喜欢的 Bundler 工具之一。它在你最喜欢的编辑器中打开宝石。我说的“最喜欢的编辑器”是指你在`$EDITOR`或`$BUNDLER_EDITOR`环境变量中设置的编辑器。

```
$ export EDITOR=vim
$ bundle open rake
...opens to the rake gem directory in vim... 
```

当我想挖掘一颗宝石，并把`puts`放进去，看看宝石里究竟发生了什么时，这非常有用。

## 捆绑包过期

我看到过几个关于`bundle outdated`的帖子，他的工作是浏览宝石，告诉你是否有更新版本的宝石发布。在我的一个老项目中运行这个程序，花了几分钟时间，看起来像这样:

```
$ bundle outdated
Fetching gem metadata from https://rubygems.org/...........
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies.............................................(lots more dots)
Outdated gems included in the bundle:
  * active_model_serializers (newest 0.10.2 4ff33a7, installed 0.10.0.rc5 63e9337) in group "default"
  * byebug (newest 9.0.5, installed 8.2.4) in groups "development, test"
  * committee (newest 1.14.1, installed 1.14.0) in group "default"
  * database_cleaner (newest 1.5.3, installed 1.5.1) in group "test"
  ....lots more outdated gems... 
```

它列出了 gem、最新版本、已安装版本以及 gem 所在的组。你也可以传递一个特定宝石的名字，只是为了检查那一个宝石:

```
$ bundle outdated rails
Resolving dependencies... (again, takes a really long time) 
```

有三个命令行选项:

*   `--local`将检查 gem 缓存，而不连接到远程源(即 Rubygems)。
*   `--pre`将包含预发布的 gems，默认排除。
*   对照“特定来源”进行检查，我假设您将该来源传递到选项中。文档并没有说，我也没有定制的 Rubygems 服务器。

## 捆绑控制台

这是我总是忘记存在的一个工具。运行`bundle console`将启动一个 IRB 会话，包中的所有 gem 都已经`require` d 了(除非您指定在 gem 文件中不需要它们)。

```
$ bundle console
irb> Rails
=> Rails 
```

这是非常有用的，特别是如果你正在开发一个 gem，并且想要一个加载了你的 gem 及其所有依赖项的快速 REPL。顺便说一句，如果你是 Pry 的爱好者(还有，谁不是呢？)您可以使用`bundle console config pry`让 Bundler 使用 Pry 作为控制台，假设您已经安装了 Pry。

## 捆绑锁

您知道几个命令`bundle list`和`bundle show`将如何重建 Gemfile.lock 文件吗？嗯，这正是`bundle lock`所做的。它重建锁文件，但不下载/安装任何 gem:

```
$ bundle lock
Fetching gem metadata from https://rubygems.org/...........
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Starting resolution (2016-07-27 13:58:05 -0400)
Resolving dependencies...Creating possibility state for rails (167 remaining)
  Attempting to activate rails (5.0.0)
  Activated rails at rails (5.0.0)
  Requiring nested dependencies (activesupport (= 5.0.0), actionpack (= 5.0.0), actionview (= 5.0.0), activemodel (= 5.0.0), activerecord (= 5.0.0), actionmailer (= 5.0.0), activejob (= 5.0.0), actioncable (= 5.0.0), railties (= 5.0.0), bundler (< 2.0, >= 1.3.0), sprocket
s-rails (>= 2.0.0))
  Creating possibility state for bundler (< 2.0, >= 1.3.0) (1 remaining)
    Attempting to activate bundler (1.11.2)
    Activated bundler at bundler (1.11.2)
    Requiring nested dependencies ()
    ...
    Writing lockfile to /path/Gemfile.lock 
```

`bundle lock`有几个选项:

*   `--lockfile=FILE`:将锁文件写入`FILE`。顺便说一句，我不能让这个工作。
*   `--local`:不连接 Rubygems，只使用本地 gem 缓存。
*   `--print`:将锁文件打印到 STDOUT。
*   `--update=[gems]`:这将更新提供的`gems`列表，如果没有指定，则更新所有宝石。这是对 gems 的积极更新。

## 捆绑即

这是另一个我从未使用过的工具*。`bundle viz`创建 Gemfile 的依赖图。让我们来看看:*

```
$ bundle viz
#<LoadError: cannot load such file -- graphviz>
Make sure you have the graphviz ruby gem. You can install it with:
`gem install ruby-graphviz` 
```

哦，我想我需要`gem install ruby-graphviz`(我确实需要):

```
$ bundle viz
/<path>/gem_graph.png 
```

如果我们打开该文件，它看起来像:

![bundle viz](img/f1c826b1bd01d599503dee49eb695988.png)

这是一个普通的 Rails 应用程序……还不错。我在我的一个部署项目中运行了它，委婉地说，它看起来更复杂了。尽管如此，它也向我展示了我们编写的 gem 可能做了太多基于它作为依赖项引入的东西。既有用又丢人，一次搞定。

有几个选项:

*   `-f`或`--file`允许您指定输出文件的名称。
*   `--format`或`-F`是如何选择图像格式(png、jpg、svg 等。)
*   `--requirements`显示每个必需依赖项的版本，它们是图的边。
*   `--version`显示 gem 的版本，它是图形的节点。
*   当然，`--without`允许你从图像中排除宝石组。

## 捆绑宝石

在所有的实用程序中，`bundle gem`可能是最大的。它的要点是生成开始开发一个合适的 gem 所需的所有文件。事实上，使用`bundle gem`是*启动新宝石的*方式:

```
$ bundle gem
 bundler bundle gem my-new-gem
Creating gem 'my-new-gem'...
      create  my-new-gem/Gemfile
      create  my-new-gem/.gitignore
      create  my-new-gem/lib/my/new/gem.rb
      create  my-new-gem/lib/my/new/gem/version.rb
      create  my-new-gem/my-new-gem.gemspec
      create  my-new-gem/Rakefile
      create  my-new-gem/README.md
      create  my-new-gem/bin/console
      create  my-new-gem/bin/setup
      create  my-new-gem/.travis.yml
      create  my-new-gem/.rspec
      create  my-new-gem/spec/spec_helper.rb
      create  my-new-gem/spec/my/new/gem_spec.rb
Initializing git repo in /path/my-new-gem 
```

您可以看到该命令创建的所有内容:

*   当然，我们得到了一个 gem 文件
*   我们还得到一个初始化的 git 存储库，用`.gitignore`完成。哦，所有的新文件都被索引了。
*   标准 gem 文件，包括 gemspec、Rakefile、版本文件、`console`和`setup`的二进制文件。
*   在我的例子中，它生成了用于测试的 RSpec 文件。这意味着，在过去的某个时候，我运行了`bundle gem`，被提示选择哪个测试框架(RSpec 或 Minitest)并选择了`rspec`。
*   一个 Travis CI 配置文件，坦率地说，我不知道为什么它是默认`bundle gem`输出的一部分。我觉得我一定做了什么让这一切发生。

此外，还有几个项目没有在上面显示:行为准则和许可证。同样，我必须在过去的某个时候选择退出。

从期权的角度来看，大部分期权都是围绕推翻创业板的创建(或省略)的。存在创建或省略以下内容的选项:用于 gem 的可执行文件(`--exe`或`--no-exe`)、行为准则 **(RO)** ( `--coc`、`--no-coc`)、C 扩展样板文件(`--ext`、`--no-ext`)、mit 许可证 **(RO)** ( `--mit`、`--no-mit`)。

另外仅有的两个选项是`-e`打开你`$EDITOR`中的`.gemspec`和`--test=TEST`T6(RO)指定`rspec`或`minitest`。

## 捆绑平台

会告诉你你指定了什么 Ruby 平台或者你的 gem 使用了什么平台:

```
$ bundle platform
Your platform is: x86_64-darwin15

Your app has gems that work on these platforms:
* ruby

Your Gemfile does not specify a Ruby version requirement. 
```

所以，我在苹果电脑上运行，使用核磁共振成像。如果我将`ruby "2.3.0"`添加到我的 gem 文件中，结果是这样的:

```
$ bundle platform
our platform is: x86_64-darwin15

Your app has gems that work on these platforms:
* ruby

Your Gemfile specifies a Ruby version requirement:
* ruby 2.3.0

Your current platform satisfies the Ruby version requirement. 
```

有一个`--ruby`选项可以简单地返回 Gemfile 中指定的 Ruby 版本，如果有的话。

## 捆绑清洁

有时，您安装了您应用程序不使用的 gem(无论是无意的还是有意的)。此外，如果你使用 RVM，我知道在某一点上，未使用的宝石，一旦安装，永远不会从一个宝石。所以，`bundle clean`会处理好这个:

```
$ bundle clean
Cleaning all the gems on your system is dangerous! If you're sure you want to remove every system gem not in this bundle, run `bundle clean --force`. 
```

哇，这是一个可怕的信息。*闭上眼睛*

```
$ bundle clean --force
Removing ruby-graphviz (1.2.2) 
```

啊，那只是从我目前的宝石组中移除了`ruby-graphviz`宝石，我安装它是为了给你看`bundle viz`。

## 捆绑 bin 存根

Binstubs 是用于包装可执行文件以便为该可执行文件设置环境的脚本。这类似于装饰模式，但是是针对可执行文件的。如果你曾经跑过`bundle exec <some command>`，那几乎是同样的事情。Bundler 很友好地提供了`bundler binstub <a gem in the bundle>`来生成一个文件，您可以根据需要调用它来包装内容:

```
$ bundle binstubs rack 
```

这将创建一个`bin`目录，并为 rack gem 中的所有可执行文件编写 binstubs。在这种情况下，只有一个(`rackup`)。所以，现在我们可以调用`bin/rackup`，它将在我们的 bundle 的上下文中运行。

顺便说一下，如果你运行`bundle install --binstubs` **(RO)** ，那么 Bundler 将在每个`bundle install`为每个带有可执行文件的 gem 生成 binstubs。

## 捆绑注入

这个很奇怪。`bundle inject`获取一个 gem 名称和版本，并将它们注入到 Gemfile 和 Gemfile.lock:

```
$ bundle inject rspec ">0"
...performs resolution...
Added to Gemfile:
  rspec (> 0) 
```

在`bundle install`运行之前，不会安装`rspec` gem。我不知道为什么有人会用这个，但我打赌有很好的理由(如果你知道它是什么，请在评论中告诉我们)

## 捆绑版本

为了完整起见，这里有`bundle version`:

```
$ bundle version
Bundler version 1.12.5 
```

顺便问一下，`1.12.5`是本文写作时最新的 Bundler 版本吗？您运行的是什么版本？我打赌你们中的一些人已经很久没有更新 Bundler 了…

## 完成的

简而言之，这就是 Bundler 公用事业公司。好吧，如果我说的“简单”是指“比我预期的博客帖子要长”尽管如此，我还是学到了不少东西，其中一些可能是错的。所以，如果你看到一个错误，让我知道。去打包吧。

## 分享这篇文章*