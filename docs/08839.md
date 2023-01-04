# 在 Ubuntu 上安装带有 RVM 的 Ruby

> 原文：<https://www.sitepoint.com/installing-ruby-with-rvm-on-ubuntu/>

你可能已经注意到最近 SitePoint 上的一些文章和它的属性讨论了 Heroku 和他们的产品；他们无疑正在颠覆云服务领域！事实上，[我最近写了一篇文章](https://www.sitepoint.com/heroku-postgresql/)讨论 Heroku 的数据库即服务产品， [Heroku Postgres](https://postgres.heroku.com/) 。但是作为一个将近 10 年的 PHP 开发人员，当我探索他们的[脸书集成产品](http://www.heroku.com/facebook)时，我被一个令人沮丧的障碍绊倒了……我必须安装 Ruby！

嗯，不，不完全是这样。你看，我的系统上没有安装 Ruby，所以我首先尝试使用系统的包管理工具来安装它。我运行的是 Ubuntu 11.10，所以用了 apt-get。一切似乎都很顺利，但我在尝试使用 heroku gem 时遇到了问题。在研究了所有东西之后，我最终用 RVM 这个神奇的工具自己安装了 Ruby。

我想和 RVM 分享在 Ubuntu 上安装 Ruby 的过程，因为我看到其他几个人也在 heroku gem 上遇到了类似的问题，格伦很乐意帮忙。如果你是一个 Ruby 专家，我很抱歉…把这篇文章看作是一个无知的 PHP 开发人员的漫谈。但是如果你是一个 Ruby 新手或者经历过 Ubuntu 上的 gem 问题，希望这篇简短的文章会有所帮助。

## 安装 RVM

你可以自己从源代码中安装 Ruby，但是我建议你使用 [Ruby 版本管理器](http://beginrescueend.com/) (RVM)来安装。RVM 实际上是一套巧妙的脚本，可以让你轻松地在系统上安装多个版本的 Ruby，并保持它们的环境相互隔离；你不必担心不兼容的 gem 版本之间的冲突以及其他可能发生的潜在危险。

首先，安装 RVM 自己使用的依赖项(为此使用默认包是安全的)。

```
sudo apt-get install curl git-core
```

然后，按照 RVM 网站上的[说明安装 RVM。相信我，你真的想手动安装它，而不是使用打包版本。并不是所有你以后需要的依赖项都会被 Ubuntu 包安装，在 libreadline 上有一些冲突，在安装了打包版本的依赖项之后，我仍然无法让事情正常运行。](http://beginrescueend.com/rvm/install/)

```
bash < <(curl -sk https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"' >> ~/.bashrc
source ~/.bashrc
```

请注意安装输出中列出的其他依赖项。您需要马上安装它们。但是首先，简单解释一下上面的命令是做什么的。

从 GitHub 获取最新版本的 RVM 安装程序脚本。如果 GitHub 的 SSL 证书有问题,`-s`选项抑制命令的正常输出(进度条、错误消息等),`-k`选项允许 curl 建立不安全的 HTTPS 连接。就我个人而言，我不同意使用`-k`的建议，所以当我在自己的系统上运行该命令时，我省略了它，并且没有不良影响。下载的输出然后被重定向到 Bash shell，在那里执行安装程序脚本。

RVM 安装在本地您的主目录下的隐藏目录`.rvm`。第二个命令向您的`.bashrc`文件添加一个测试，以检查包含重要 shell 命令的`rvm`文件是否可用，如果可用，则将它们加载到您的终端会话中。`[[ ]]`是内置的 Bash 操作符，检查表达式是否为真，`-s`是测试文件是否存在。`source`读取并执行文件中的命令来更新您的 Bash 环境。

## 安装 Ruby

Heroku 的快速入门指南推荐使用 Ruby 1.9.2，这听起来是个不错的数字，所以我没有反驳。使用 RVM 安装多个版本的 Ruby 很简单。为了确保编译顺利进行，请在终端会话中向后滚动，查看应该安装的必备软件包的列表(最好剪切并粘贴所有的名称，以免输入错误)。

```
sudo apt-get install build-essential openssl libreadline6 libreadline6-dev zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev autoconf libc6-dev libncurses5-dev automake libtool bison subversion
```

现在，您可以使用 rvm 来下载、编译和安装 Ruby。

```
rvm install 1.9.2
rvm use 1.9.2 --default
```

第一个命令(`rvm install`)触发下载/编译/安装过程，并在`~/.rvm`下安全地安装 Ruby 及其 gems，远离系统上的其他任何东西。第二个命令(`rvm use`)将 Ruby 的 1.9.2 版本设置为当前默认版本。

您可以通过以下方式验证 1.9.2 现在是否可用:

```
ruby -v
```

## 摘要

在 RVM 的帮助下，当发行版软件包安装失败时，我能够快速、轻松地安装 Ruby。这种方法的好处还在于 Ruby 环境是沙箱化的，所以我不必担心不同版本之间的冲突。我不仅可以并排放置不同版本的 gem，还可以轻松安装其他 Ruby 实现(MRI、REE、JRuby 等。).

说实话，如果 PHP 有这样的工具，我不会介意，尽管考虑到 PHP 如何与 Apache 这样的服务器交互，我认为管理起来有点棘手。

一旦安装了 RVM，您可能会喜欢使用它，看看它还能做些什么来帮助您更轻松地管理您的环境。我偶然发现了[如何使用 RVM](http://screencasts.org/episodes/how-to-use-rvm) 的截屏，该截屏在 Ruby 环境之间切换，然后使用`.rvmrc`文件基于您的工作项目切换环境。

## 分享这篇文章