# 使用 rbenv 启动和运行

> 原文：<https://www.sitepoint.com/up-and-running-with-rbenv/>

rbenv 是一个可以让你并行安装和运行多个版本的 Ruby 的工具。这是一个简单、轻量级的替代方案，代替了只专注于管理多个 Ruby 环境的 T2 RVM T3。

每个版本的 Ruby 都安装在`~/.rbenv/versions`目录下。它使用垫片在版本之间切换，这些垫片位于`~/.rbenv/shims`中。垫片的工作是抓取你想要的 Ruby 版本的目录，并把它放在你的`$PATH`的开头，然后执行相应的二进制文件。

rbenv 要求你在`~/.rbenv/versions`目录下安装每个版本的 Ruby。它使用垫片在版本之间切换，这些垫片位于`~/.rbenv/shims`中。垫片的工作是抓取你想要的 Ruby 版本的目录，并把它放在你的`$PATH`的开头，然后执行相应的二进制文件。

rbenv 和 RVM 在管理多个 Ruby 环境方面都做得很好。一会儿我们将看看如何安装 rbenv 并设置几个 Ruby 版本。在此之前，让我们看看你在决定是否选择 rbenv 或 RVM 时可能要考虑的其他一些因素。

## 我为什么要用 RVM？

像 rbenv 一样，RVM 允许你并行安装和运行多个 Ruby 环境。但它也提供了:

*   用于安装红宝石的内置支架，例如`rvm install 1.9.2`；使用 rbenv，你必须自己手工制作红宝石
*   一个灵活的 gem 管理系统，称为命名 Gem 集，简化了跨多个环境的 Gem 管理
*   拥有完全相同的独立环境的能力，例如开发、CI、试运行和生产
*   能够同时测试多个 Ruby 版本，例如从 1.8.x 升级到 1.9.x、JRuby 等。

## 为什么我会选择 rbenv？

rbenv 建立在做一件事并把它做好的 UNIX 传统上。它的唯一重点是管理多个 Ruby 环境，并提供简单的方法来做到这一点:

*   您可以选择在全局和每个项目级别上使用哪个版本的 Ruby。
*   也可以用环境变量覆盖这些设置。

与 RVM 相反，rbenv 没有:

*   覆盖任何内置的 shell 命令
*   附带任何管理宝石的方式(推荐的方式是使用 [Bundler](http://gembundler.com/) 或 [rbenv-gemset](https://github.com/jamis/rbenv-gemset)
*   附带任何安装 Ruby 版本的机制

ruby-build 可以与 rbenv 集成，以简化 ruby 版本的安装。另一种方法是自己下载并构建 Ruby 的每个版本，这就是我们在本文余下部分要做的。

让我们看看如何安装`rbenv`和设置一些 Ruby 环境。

## 装置

**NB**rbenv*不*兼容 RVM。如果你已经安装了 RVM，在尝试 rbenv 之前，你需要[删除所有对它的引用](http://stackoverflow.com/questions/3558656/how-to-remove-rvm-ruby-version-manager-from-my-system/3558763#3558763)。如果你不这样做，那么 rbenv 可能看起来工作正常，但是当你试图安装一个 gem 时，它就会崩溃。根据 [rbenv 自述文件](https://github.com/sstephenson/rbenv/blob/master/README.md#section_2)，这是因为 RVM 用一个 shell 函数覆盖了`gem`命令。

rbenv 目前住在 https://github.com/sstephenson/rbenv。[自述文件](https://github.com/sstephenson/rbenv/blob/master/README.md)包含了在我们的系统上安装它需要遵循的步骤。如果你在 OS X，你可以选择更简单的方式[通过自制软件](https://github.com/sstephenson/rbenv#section_2.2)安装 rbenv。但是我们将看看如何下载源代码并自己编译它。

在我们开始之前，请注意以下几点:

*   我正在 Ubuntu 上安装这个。如果你在 OS X 上关注此事，你需要用`.bash_profile`替换下面所有对`.bashrc`的引用
*   在 OS X 上，你需要安装 Xcode 才能编译源代码。你可以在 http://developer.apple.com/technologies/tools/的 OS X 安装 DVD 上找到 Xcode，或者从那里下载
*   当我们从 GitHub 获取源代码时，如果你安装了 git，那会很有用:)

让我们开始吧:

1.  在将 GitHub 库克隆到`~/.rbenv`之前，打开终端并确保您在您的主目录中:

    ```
    $ cd
    $ git clone git://github.com/sstephenson/rbenv.git .rbenv
    ```

2.  为了能够在命令行上访问`rbenv`，我们需要将它添加到我们的`$PATH`:

    ```
    $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    ```

3.  然后，我们需要将`rbenv init`添加到我们的 shell 中，以启用填充和自动完成。如果我们重新启动 shell，我们将立即启动并运行:

    ```
    $ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
    ```

4.  如果我们重启我们的 shell，我们将可以立即使用 rbenv 和自动完成功能:

    ```
    $ exec $SHELL
    ```

5.  为了保持整洁，你可能需要在$HOME 目录下创建一个文件夹来保存每个 Ruby 版本的源代码:

    ```
    $ cd
    $ mkdir rubysrc
    ```

6.  接下来下载最新版本的 Ruby 源代码:

    ```
    $ curl -O http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p290.tar.gz
    ```

7.  解压下载文件，然后`cd`放入新解压的文件夹:

    ```
    $ tar xzvf ruby-1.9.2-p290.tar.gz
    $ cd ruby-1.9.2-p290
    ```

8.  然后运行`configure`、`make`和`make install`，确保在`./configure --prefix`路径中输入了正确的版本号:

    ```
    $ ./configure --prefix=$HOME/.rbenv/versions/1.9.2-p290
    $ make
    $ make install
    ```

    你现在应该发现你已经在`~/.rbenv/versions/1.9.2-p290`文件夹中安装了 Ruby 1.9.2

9.  最后一步是运行`rbend rehash`,这样 shim 二进制文件就会被重新构建。建议你在安装新的 Ruby 二进制文件时这样做，不管是 Ruby 本身的新版本还是二进制文件附带的 gem。

## 安装另一个版本的 Ruby

好了，我们已经安装了最新版本的 Ruby。现在让我们下载并安装候选版本，然后看看我们如何使用`rbenv`在两个版本之间切换:

1.  从(http://www.ruby-lang.org/)下载发布候选代码:

    ```
    $ cd ~/rubysrc
    $ curl -0 http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-rc1.tar.gz
    ```

2.  提取源代码:

    ```
    $ tar xzvf ruby-1.9.3-rc1.tar.gz
    $ cd ruby-1.9.3-rc1.tar.gz
    ```

3.  像以前一样运行`configure`和`make`。同样，我们需要小心 T2 之路。这一次它应该指向 RC:

    ```
    $ ./configure --prefix=$HOME/.rbenv/versions/1.9.3-rc1
    $ make
    $ make install
    $ rbenv rehash
    ```

4.  查看一下，我们确实有两个版本的 Ruby 供我们使用:

    ```
    $ rbenv versions
        1.9.2-p290
        1.9.3-rc1
    ```

## 版本控制

现在我们已经安装了两个版本的 Ruby，我们如何控制使用哪一个呢？简单。我们可以用`rbenv global`、`rbenv local`或者`rbenv shell`

1.  `rbenv global`设置默认使用的 Ruby 全局版本。我们将把版本 1.9.2 设置为全局版本，而不是默认使用 RC:

    ```
    $ rbenv global 1.9.2-p290
    ```

这将把我们选择作为全局变量的版本写到`~/.rbenv/version`

```
$ cat ~/.rbenv/version
    1.9.2-p290 (set by /home/ianoxley/.rbenv/version)
```

1.  `rbenv local`基于每个项目设置使用哪个 Ruby。例如，如果我们想在当前项目`foobar`中使用 RC1，我们需要在命令行中输入以下内容:

    ```
    $ cd ~/projects/foobar
    $ rbenv local 1.9.3-rc1
    ```

这会将版本写入当前目录中的文件`.rbenv-version`(这就是为什么我们首先将目录更改为项目的根文件夹):

```
$ cat .rbenv-version
    1.9.3-rc1
```

1.  也可以使用独立于全局或项目设置的特定于 shell 的 Ruby 版本。也许我们想使用最新的 Ruby 夜间快照来尝试一些东西？

    ```
    $ cd ~/rubysrc
    $ curl -O http://ftp.ruby-lang.org/pub/ruby/snapshot.tar.gz
    $ tar xzvf snapshot-20111004
    $ cd snapshot-20111004
    $ ./configure --prefix=$HOME/.rbenv/versions/snapshot-20111004
    $ make
    $ make install
    $ rbenv rehash
    ```

    然后，我们可以将它设置为在当前的 shell 中使用，如下所示:

    ```
    $ rbenv shell snapshot-20111004
    ```

在幕后，这将设置`RBENV_VERSION`环境变量指向我们告诉它的 Ruby 版本:

```
$ echo $RBENV_VERSION
    snapshot-20111004
```

它可以通过以下方式取消设置:

```
$ rbenv shell --unset
```

要查看您已经安装并且对`rbenv`可用的所有 Ruby 版本，有一个`rbenv versions`命令。这列出了`rbenv`知道的所有 Ruby 版本，并根据当前上下文在当前活动版本旁边添加了一个星号，例如 shell、本地或全局。让我们看看当我们改变我们所处的上下文时，这个命令的输出是如何变化的:

```
$ cd
    $ rbenv versions
    * 1.9.2-p290 (set by /home/ianoxley/.rbenv/version)
        1.9.3-rc1
        snapshot-20111004

    $ cd projects/foobar
    $ rbenv versions
        1.9.2-p290
    *   1.9.3-rc1 (set by /home/ianoxley/projects/foobar/.rbenv-version)
        snapshot-20111004

    $ rbenv shell snapshot-20111004
    $ rbenv versions
        1.9.2-p290
        1.9.3-rc1
    *   snapshot-20111004 (set by RBENV_VERSION environment variable)
```

希望一切顺利？如果你有任何问题，请在评论中告诉我，我会尽力帮助你。

## 分享这篇文章