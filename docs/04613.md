# Chruby 和 Rbenv 提示和技巧

> 原文：<https://www.sitepoint.com/chruby-rbenv-tips-tricks/>

Rbenv 和 [chruby](https://github.com/postmodern/chruby) 现在是街区里最酷的孩子。显然，我们希望从我们最喜欢的工具中获得最多的东西。所以这篇文章不是关于更喜欢哪一个，也不是对任何一个版本管理器的[介绍](https://www.sitepoint.com/up-and-running-with-rbenv/)。相反，我们将介绍一些高级的 rbenv 和 chruby 用法，以最大化您的生产力。

## 设置

如果你没有使用 rbenv 或 chruby 的经验，你应该先阅读它们的`README`文件，但是这里有一个快速的开始:

**关于[家酿](http://brew.sh/)安装的说明。**我喜欢自制软件，但是一直说`brew install whatever`是多余的，因为我也将展示手工(git)方法。换句话说，如果你碰到什么东西，不想通过 git 安装，就`brew install`它！

### rbenv

首先，下载 rbenv。

```
$ git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
```

在您的 shell 配置文件(您应该知道您的是什么:`~\.(bash|zsh)(rc|_profile)`)中，附加以下内容:

(如果您是通过自制软件安装的，可以跳过这一步。)

```
# rbenv
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

### chruby

与 rbenv 的流程相同，但略有不同。

```
$ git clone git://github.com/postmodern/chruby.git ~/.chruby
$ cd ~/.chruby
$ sudo make install
```

*或*

```
$ wget -O chruby-0.3.8.tar.gz https://github.com/postmodern/chruby/archive/v0.3.8.tar.gz
$ tar -xzvf chruby-0.3.8.tar.gz
$ cd chruby-0.3.8/
$ sudo make install
```

比起后者，我更喜欢前者，因为它既容易记忆又简明扼要。你挑吧。

将它附加到您的 shell 配置中:

```
# chruby
source /usr/local/share/chruby/chruby.sh
```

重新加载外壳，您就可以开始了！试着运行`rbenv`或`chruby`看看是否有效。如果你还没有安装任何 ruby 版本，那也没关系。看看 [ruby-install](https://github.com/postmodern/ruby-install) 或者 [ruby-build](https://github.com/sstephenson/ruby-build) 。

## 情节

现在你已经设置好了，我将运行几个真实的场景，以及如何使用 rbenv 和 chruby 来解决它们。*走吧！*

### 我想让我的 Ruby 版本自动改变。

Rbenv 会自动这样做。不需要插件。

Chruby 也能做到这一点，但是您必须在您的 shell 配置中添加`source /usr/local/share/chruby/auto.sh`。就是这样。

### 我把我的 Ruby 版本放在别的地方。

这在从 rbenv 切换到 chruby 时非常有用，反之亦然。用你碰巧存放红宝石的目录替换给定的目录。

Rbenv 需要一点技巧，但它只是一个简单的符号链接。

```
$ ln -s /opt/rubies/2.1.5 ~/.rbenv/versions/2.1.5
```

**Chruby** 有一个数组(`$RUBIES`)就是为了这个目的。将它附加到您的 shell 配置中:

```
RUBIES+=(~/.rbenv/versions/*)
```

### 我想要一个默认的全局 Ruby 版本。

**两个**都允许一个全局`~/.ruby-version`文件，你可以把它放在你的[点文件](http://dotfiles.github.io/)中。(例如`echo "2.1.5" > ~/.ruby-version` ) **不过这是假设你启用了 chruby 自动版本切换。**

**Rbenv** 有`rbenv global 2.1.5`。

Chruby 允许您在 shell 配置中调用自己，如下所示:

```
# below other chruby config
chruby 2.1.5
```

### 我的项目使用了不同的 Ruby 版本名。

偶尔一个项目在实际版本前会有一个奇怪的前缀(像 RVM 的`ruby-`)。如果 chruby 或 rbenv 似乎找不到给定的版本，这只是一个简单的符号链接。在这个例子中，我们的项目有一个指定了`ruby-2.1.5`的 Ruby 版本文件，但是我们的版本被命名为`2.1.5`。

```
$ cd ~/.
$ ln -s 2.1.5 ruby-2.1.5
```

## 酷酷的 Rbenv 插件

rbenv 的一个优点是没有像 RVM 那样臃肿的特性。然而，有时我们想要额外的东西。让我们来看看几个针对 rbenv 的插件。要安装这些，您需要检查每个插件的 GitHub 库中的安装部分。通常只是简单的`git clone`，但你也可能会用家酿。

你可以在这里看到可用插件的完整列表。

### rbenv-gem-rehash

[https://github.com/sstephenson/rbenv-gem-rehash](https://github.com/sstephenson/rbenv-gem-rehash)

Rbenv 使用可执行垫片来加载 Ruby 及其 gem。运行`rbenv rehash`刷新这些垫片。在每次安装 gem 之后，运行`rbenv rehash`。幸运的是，这个插件会自动刷新垫片。没有任何配置，只需安装它，你就再也不用重写 rbenv 了。

### rbenv-使用

[https://github.com/rkh/rbenv-use](https://github.com/rkh/rbenv-use)

所以，你仍然偏爱一些 RVM 的命令，嗯？其中一个命令是`rvm use VERSION`。这个插件添加了`rbenv use VERSION`命令来简化这个转换。然而，*你的项目不应该依赖这个插件。*`.ruby-version`文件应该在你的大多数环境中设置你的版本。

### rbenv-别名

[https://github.com/tpope/rbenv-aliases](https://github.com/tpope/rbenv-aliases)

你可能会注意到我们之前用一个简单的符号链接解决了这个问题，但是这个插件做了同样的事情。简直就是`rbenv alias VERSION\_ALIAS REAL\_VERSION`。

### rbenv-每个

[https://github.com/chriseppstein/rbenv-each](https://github.com/chriseppstein/rbenv-each)

这个插件在你系统上的每个 Ruby 版本上运行一个命令。我认为最有可能的情况是测试多个版本。语法与`bundle exec`类似，只需运行`rbenv each <a command>`对每个 Ruby 运行命令。

### rbenv-sudo

[https://github.com/dcarley/rbenv-sudo](https://github.com/dcarley/rbenv-sudo)

有时候有必要以根用户的身份运行 Ruby，但这通常是一个非常痛苦和混乱的过程。幸运的是这个插件做到了！只需运行`rbenv sudo <a command>`来运行命令作为一个根 Ruby 进程。

## 最后

Rbenv 和 chruby 都是优秀的 ruby 版本管理器。我自己使用过每一个都有很好的效果。每个都是高度可扩展和易于使用的。上面的场景旨在模拟现实生活，但如果你发现自己处于上面没有描述的场景中，请随意发表评论。

## 分享这篇文章