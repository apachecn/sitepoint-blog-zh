# 使用 Babushka 轻松设置服务器

> 原文：<https://www.sitepoint.com/painless-server-setup-with-babushka/>

Web 开发很有趣。至少，应该是。然而，它涉及到一些任务——用所有必需的软件包、配置文件等设置一个新的服务器或虚拟机——这些任务有点繁琐。输入[头巾](http://babushka.me)。Babushka 是由 Ben Hoskings 编写的一个小 Ruby 软件。它可以让你编写简单的小程序(Babushka 称之为“deps”)在你的服务器上安装特定的软件。在其最基本的形式中，每个 dep 由两部分组成:如果已经满足依赖关系，则确定*的方法，如果不满足依赖关系，则确定满足依赖关系的方法。通过这种方式，您可以维护一个 dep 库，使您可以快速地让您微调过的服务器按照您喜欢的方式运行，而无需花费两个小时来尝试整理缺失的依赖项和构建错误。例如，假设作为我的设置的一部分，我想确保服务器的时间是准确的。这里有一个快速的 dep 来检查苹果时间服务器的系统时间:*

```
dep 'time' do  server = 'time.apple.com'  met? {    log_shell("Checking clock against #{server}",        "ntpdate -q #{server} | tail -n1") =~ /offset -?(d+)(.d+)? sec/    $1.to_i < 10  }  meet {    sudo 'service ntp stop'    log_shell "Setting time from #{server}", "ntpdate #{server}", :sudo => true    sudo 'service ntp start'  }end
```

正如您可能猜到的，运行`met?`块是为了确定是否满足依赖关系。它应该总是返回一个布尔值；在这种情况下，我们检查系统时间和服务器时间之间的偏差是否小于 10 秒。如果`met?`返回`false`，Babushka 将运行`meet`中的代码。这是您放置满足依赖性所需的所有代码的地方。一旦执行完毕，它将再次运行`met?`来看看是否有效。你可能会想，“嘿，太酷了！”但是等等，还有更多。Babushka 将透明地处理软件安装，或者通过系统的软件包管理器，或者从源代码。假设我想安装 memcached。这需要 libevent 作为依赖项，所以我需要通过包管理器来获取它。这里有一个用头巾做这些事情的简单方法:

```
dep 'memcached.src' do  requires 'libevent.managed'  source 'http://memcached.googlecode.com/files/memcached-1.4.5.tar.gz'  provides 'memcached'enddep 'libevent.managed' do  installs {    via :apt, 'libevent-dev'    via :brew, 'libevent'  }  provides []end
```

那里发生了不少有趣的事情。首先，您会注意到，我只是简单地将 Babushka 指向 memcached 源代码，其余的都被透明地处理了。Babushka 将下载 tarball，提取它，然后运行`./configure`、`make`和`make install`。这要感谢 dep 名称末尾的那个`.src`位；这告诉 Babushka 我想使用源**模板**来满足这种依赖性。Babushka 附带了从源代码安装、通过系统包管理器安装以及其他一些模板，但是您也可以定义自己的模板并在 dep 中使用它们。你可以向你的任何一个部门提供多种选择。例如，如果我想提供一个`./configure`参数，我可以用这样一行代码:

```
configure_args "--enable-sasl"
```

另一个有趣的语法是`requires`行。这使得您的 dep 依赖于另一个 dep。如果我从命令行运行`babushka memcached.managed`，它会知道需要先检查 libevent。libevent dep 很简单:`installs`声明让我根据系统上使用的包管理器指定可选的包名(在本例中，我为 Linux 指定了 apt，为 Mac OS X 指定了 brew)。这些都是非常简单的例子。Babushka 有一系列惊人的小助手来与文件系统和外壳交互，并且能够定义自己的模板，这使它非常灵活。目前文档还相当有限，但是 Ben 自己在 GitHub 上的 [babushka-deps](http://github.com/benhoskings/babushka-deps) 库有很多真实的例子(虽然它们主要是针对部署 Rails/Passenger/Nginx 栈，但是它们仍然可以让你很好地了解所有不同的助手和选项是如何工作的)。要安装 Babushka，请从 Linux 命令行运行以下命令:

```
bash -c "`wget -O - babushka.me/up`"
```

在麦克·OS X 身上，那是:

```
bash -c "`curl babushka.me/up`"
```

虽然想出一套您满意的 dep 可能需要做一些工作和大量的尝试和错误，但启动一个新的 VM 并使用几个快速命令完全配置您的完美服务器设置是一种非常令人满意的体验。

**note:**Want more?

如果你想阅读更多关于 Louis 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章