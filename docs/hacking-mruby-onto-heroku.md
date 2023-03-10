# 把 mruby 黑进 Heroku

> 原文：<https://www.sitepoint.com/hacking-mruby-onto-heroku/>

![](img/659fe7a7062a00960928343ae46cec1e.png "heorku_mruby")除非你一直生活在红宝石色的岩石下，否则你很可能听说过 [mruby](https://github.com/mruby/mruby) ， [Matz 的](http://en.wikipedia.org/wiki/Yukihiro_Matsumoto)最新的实验性 Ruby 实现。我打赌你不知道的是你现在可以在 Heroku 上运行 mruby。事实上，你可以在 Heroku 上运行任何东西，只要它能在 Linux 机器上编译成二进制文件。

如果你是 mruby 的新手，或者是编译二进制文件的新手，看看我的上一篇文章[今天就试试 mruby](https://www.sitepoint.com/try-mruby-today/)。我将介绍如何在您的本地机器上启动和运行 mruby。如果你已经掌握了速度，那么就跟随我们使用 [vulcan](https://github.com/heroku/vulcan) 将 mruby 打包成二进制文件，打包到一个定制的 [buildpack](http://blog.heroku.com/archives/2012/7/17/buildpacks/) 中，然后启动一个应用程序在 Heroku cloud 上使用 mruby。

## 什么是 Buildpack？

当你在 Heroku 上部署 [python](https://github.com/heroku/heroku-buildpack-python) 、 [ruby](https://github.com/heroku/heroku-buildpack-ruby) 或其他任何东西时，我们会检测语言，然后运行相应的构建包。简单地说，构建包使我们能够在 Heroku 上运行任何语言或框架。该系统的目标是最小化锁定和最大化透明度。

构建包是部署过程中所有奇迹发生的地方。在 [ruby buildpack](https://github.com/heroku/heroku-buildpack-ruby) Heroku [中检测并配置你的 ruby 版本](https://devcenter.heroku.com/articles/ruby-versions)，安装 gems，并运行`rake assets:precompile`。所有的构建包都是开源的，这意味着你可以派生一个或者从头开始构建你自己的。

如果你想了解更多，有一篇关于 build packs[build packs:Heroku for Everything](http://blog.heroku.com/archives/2012/7/17/buildpacks/)的博文。

在我们将一个新的软件(比如 mruby)放入 buildpack 之前，我们需要对它进行编译，这样它就可以使用构建服务器在 Heroku 上运行。

## 你的第一个 Heroku 构建服务器

如果你在本地编译了 mruby，你可以直接把它送到 Heroku，对吗？没那么快，Heroku 的机器可能与你的个人设置不同。为了确保软件能够运行，我们需要在 Heroku 机器上编译 mruby，但是我们怎么做呢？

为了帮助这个过程，Heroku 发布了名为 [vulcan](https://github.com/heroku/vulcan) 的开源工具，你可以用它在 Heroku 上[打包你的二进制文件。首先，我们需要安装工具:](https://devcenter.heroku.com/articles/buildpack-binaries)

```
$ gem install vulcan 
```

**注意:**你可能也需要安装 heroku gem，你可以运行`$ gem install heroku`来获得它。Vulcan 应该会很快更新，不再依赖它。

现在您需要在 Heroku 机器上提供一个地方来编译您的代码。我们称之为构建服务器，它实际上是一个运行在标准 Heroku dyno 上的应用程序:

```
$ vulcan create vulcan-schneems 
```

**注意:**你需要选择一个不同于`vulcan-schneems`的名字。

现在您已经获得了一个构建服务器，让我们克隆一个 mruby 源代码的新副本

## 克隆 mruby

转到 [mruby Github 页面](https://github.com/mruby/mruby)。从这里，我们可以获取 git url，将源代码克隆到我们的计算机上。

```
$ git clone git://github.com/mruby/mruby.git $ cd mruby 
```

**注意:** mruby 处于 Alpha 版本，这些说明可能会有所不同。欲了解最新信息，请访问 https://github.com/mruby/mruby/blob/master/INSTALL

在你试图编译 Heroku 上的任何东西之前，确保`bin/`目录是空的，否则编译可能会失败，但看起来仍然有效，因为你在你的机器上编译了旧文件。

## 用 Vulcan 编译

现在你在 mruby 目录中，当你尝试构建它时，vulcan 将把目录的内容上传到 Heroku 构建服务器。默认情况下，vulcan 会尝试运行[。/configure & & make 将](https://github.com/heroku/vulcan/blob/master/lib/vulcan/cli.rb)安装在你所在的当前目录下。如果您需要指定一组不同的命令，您可以使用`--command=`标志来完成。因为我们不需要`./configure`，让我们用`make`来运行它。

**Protip:** 通过`--verbose`可以得到完整的输出，帮助诊断错误。我强烈推荐这个选项。

我们需要的最后一项配置是`--prefix`这是服务器上的路径，一旦构建完您的软件，它将被克隆。传统上，构建的输出可以在运行带有`--prefix`标志的`./config`时指定。例如，如果您希望输出到`/tmp/mydirectory`，您可以运行

```
$ ./configure --prefix /tmp/mydirectory $ make $ make install 
```

因为 mruby 没有./configure 命令，所以这不起作用，相反，我们必须告诉 vulcan 我们的可执行文件在什么目录下。我们可以只说抓取由`.`(一个句号)指定的当前目录，但是这个下载会非常大。相反，我们只获取构建的输出。当运行一个构建时，vulcan 将上传的文件(从当前目录)放到一个`input`文件夹中，所以我们构建的结果将在`./input/bin`中。所以让我们使用命令`--prefix=./input/bin`来抓取这些文件。我们可以把所有这些放在一起组成我们的瓦肯人建造指挥部:

```
$ vulcan build --command=make --prefix=./input/bin --verbose # ... >> Downloading build artifacts to: /tmp/mruby.tgz 
```

(可从 http://Vulcan-schneems . heroku app . com/output/4e 584d 28-6451-4ff 3-a72f-1d0d 509d 639d 获取)

**提示:**关于 vulcan CLI 的更多文档，请查看 github 上的 [vulcan 源代码](https://github.com/heroku/vulcan)。

一旦命令完成，该文件将在 vulcan 输出的 url 中可用，服务器将重新启动。您的构建服务器运行在一个带有临时硬盘的 dyno 上。虽然它们可以被读取和写入，但在服务器重新启动时会被清除。这有助于防止失控的进程填满磁盘，并加强良好的可伸缩应用程序设计。保证每 24 小时至少重启一次服务器。该文件的副本也应该复制到您的`/tmp`目录中。

解压缩文件以验证它包含了我们想要的所有内容:

```
$ cd /tmp $ tar xvf mruby.tgz $ ls mirb mrbc mruby # ... 
```

这会解压内容，现在你应该有二进制文件`mirb`、`mruby`和`mrbc`。如果你想知道`mrbc`二进制文件，它可以用来将 mruby 程序编译成字节码。如果你试图在你的计算机上执行这些文件，你可能会得到一个错误，因为它们不是在你的计算机上建立的。我们可以仔细检查他们是否使用了另一个 Heroku 应用程序。

## 验证编译的文件

对于这一部分，您需要一个 Heroku 应用程序来验证文件编译是否正确。在安装了 [Heroku 工具带](http://toolbelt.heroku.com)的情况下运行:

```
$ heroku create Creating fast-sierra-6912... done, stack is cedar http://fast-sierra-6912.herokuapp.com/ | git@heroku.com:fast-sierra-6912.git 
```

**注意:**您的应用程序名称将与我的不同，请根据需要替换以下命令。

现在，您将希望能够在您的应用程序中运行命令。您可以使用`run`命令来做到这一点，或者您可以像这样使用`heroku run bash`打开一个 bash 会话:

```
$ heroku run bash -a fast-sierra-6912 
```

**注意:**用你的 app 名称替换`fast-sierra-6912`。

命令`heroku run bash`旋转一个额外的 dyno，并给你一个 ssh 会话。这不同于 ssh-ing 到常规服务器，因为它与任何其他 dynos 或 web 请求完全隔离。你可以在`heroku run bash`中删除整个应用的驱动器，这不会影响任何其他的 dynos。

现在我们在 Heroku bash 会话中，我们需要在机器上获得二进制文件。我们可以用`curl`命令和`-O`选项(Oscar 中的大写 O)来实现。使用这个命令和我们之前从 vulcan 获得的二进制 url 来下载二进制数据。

```
# on heroku bash $ curl -O http://vulcan-schneems.herokuapp.com/output/4e584d28-6451-4ff3-a72f-1d0d509d639d 
```

**注意:**你需要在这里使用你自己的网址。

然后解压缩文件

```
# on heroku bash $ tar xvf 4e584d28-6451-4ff3-a72f-1d0d509d639d x mirb x mrbc x mruby 
```

现在你已经将二进制文件复制到 Heroku，执行它们，就像你在我上一篇文章中所做的一样[今天试试 mruby】:](https://www.sitepoint.com/try-mruby-today/)

```
# on heroku bash ~ $ ./mirb mirb - Embeddable Interactive Ruby Shell This is a very early version, please test and report errors. Thanks :) > 
```

成功了！我们能够编译一个可以在 Heroku 上运行的二进制文件。我们可以使用同样的技术来编译任何你能想到的库。但是我们还没有完成。现在我们有了一个二进制文件，让我们把它放到一个 buildpack 中，这样我们就可以在任何应用程序上重用这个二进制文件。让我们开始吧。

## 派生一个空白的构建包

虽然我们可以从一个有更多优势的构建包开始，但从 Heroku 工程师 Ryan Smith 创建的一个空白构建包开始会更容易看到发生了什么。分叉“null”build pack，然后将名称更改为更合适的名称，如“mruby-buildpack”。为此，您可以进入管理，选择“重命名”,然后输入“mruby-buildpack”。完成此操作后，将您个人的 github repo 克隆到本地机器上:

```
$ git clone git://github.com/schneems/mruby-buildpack.git $ cd mruby-buildpack 
```

**注意:**您的 url 将会不同(不是 schneems/mruby-buildpack)

一旦你有了本地回购，打开它，看看它。这里真的不多总共 4 个文件和一个文件夹。如果你查看`bin`目录，你会看到 3 个文件:`detect`、`compile`和`release`。这是构建过程的三个阶段，这些文件将在部署期间由运行时调用。稍后我们将对此进行更深入的探讨。

## 构建一个测试应用程序

让我们验证一下我们非常简单的空构建包是否有效。我们将制作一个新的 Heroku“app ”,并使用我们的定制空构建包进行部署:

首先创建一个新目录(首先确保您不在 buildpack 目录中)。

```
$ mkdir null $ cd null $ touch README.md 
```

然后将该目录初始化为 git repo

```
$ git init $ git add . $ git commit -m "initial commit" 
```

现在我们想在 Heroku 上创建一个应用程序，并告诉它使用我们新开发的 buildpack 来构建。我们可以通过在创建应用程序时指定 buildpack url 或者通过修改配置中的`BUILDPACK_URL`来做到这一点。运行这个命令，但是记住指向您的 buildpack url，而不是我的:

```
$ heroku create --buildpack http://github.com/schneems/mruby-buildpack.git Creating still-mountain-4026... done, stack is cedar BUILDPACK_URL=http://github.com/schneems/mruby-buildpack.git http://still-mountain-4026.herokuapp.com/ | git@heroku.com:still-mountain-4026.git Git remote heroku added 
```

**记住:**使用你的 github 回购，而不是`schneems/mruby-buildpack`

完成后，您可以验证 buildpack 配置变量是否已设置:

```
$ heroku config === still-mountain-4026 Config Vars BUILDPACK_URL: http://github.com/schneems/mruby-buildpack.git 
```

太好了！将来您可以随时更改这个 url，例如，如果您想使用某个构建包的特定分支。您可以使用`#`(一个散列符号)和分支的名称来指定它。如果您有一个名为`total_re_write`的分支，您可以使用 url `http://github.com/schneems/mruby-buildpack.git#total_re_write`和`heroku config:add`命令来指定它；但是我们现在先不要担心这个。

部署您的项目并确保一切正常:

```
$ git push heroku master Counting objects: 3, done. Writing objects: 100% (3/3), 212 bytes, done. Total 3 (delta 0), reused 0 (delta 0) -----> Heroku receiving push -----> Fetching custom git buildpack... done -----> Null app detected -----> Nothing to do. -----> Discovering process types Procfile declares types -> (none) -----> Compiled slug size: 4K -----> Launching... done, v4 http://still-mountain-4026.herokuapp.com deployed to Heroku To git@heroku.com:still-mountain-4026.git * [new branch] HEAD -> master 
```

注意，输出`Nothing to do.`直接来自 repo 中的编译命令。

在我们将 mruby 添加到我们的 buildpack 之前，让我们看看构建的每个阶段都做了什么。

## 发现

当 Heroku 在 buildpack 上调用 detect 命令时，它会针对您推送的任何源代码运行。这是一个像 ruby 这样的应用程序可以检查一个`Gemfile`或者一个`config.ru`是否存在的地方，以再次检查它是否可以实际运行给定的项目。如果 buildpack 可以基于这些文件进行构建，它会向 Heroku 返回一个`0`，在*nix 中解释为“一切都按预期运行”。如果 buildpack 没有找到它需要的文件，它应该返回一个非零状态，通常是`1`。如果发生这种情况，Heroku 将取消部署过程。

因为我们是手动指定 buildpack url，所以总是返回 0 是安全的。

## 编制

这是大部分构建发生的地方，一旦应用类型被确定，就可以运行不同的设置脚本来配置系统，例如安装二进制文件或运行`assets:precompile`。

关于编译阶段需要注意的最重要的事情是，在编译步骤中，配置环境变量是不可用的。这是有意为之的，因为无论配置如何，一个架构良好的应用程序都应该编译相同的内容。Heroku labs 有一个特性可以在编译过程中启用环境变量，尽管我建议不要使用它。通过使用这个特性，我看到了太多不可复制的“它在登台时工作，为什么在生产时坏了”的问题。编译没有配置的应用程序被认为是最佳实践。

在编译阶段，缓存目录是可用的。放置在缓存目录中的任何内容都将在部署之间可用。在这里存储下载的项目(比如 gems)是个好主意，但是我们现在还不需要缓存任何东西。

在编译阶段，我们需要下载预编译的二进制文件，解压缩，并把它放在我们以后可以找到的地方。

那么什么时候将配置添加到项目中呢？我们将它不是检测或编译，所以它可能是…

## 释放；排放；发布

发布版是编译后的应用程序与它们的环境变量配对的地方。在这里，您不应该对磁盘进行任何更改，只能对环境变量进行更改。Heroku 期望以 YAML 格式返回 3 个键`addons`，如果有任何默认插件，`config_vars`提供一组默认的配置环境变量，`default_process_types`告诉 Heroku 默认运行什么命令(即`web`)。

您可以通过 [buildpack 文档](https://devcenter.heroku.com/articles/buildpack-api)获得关于这三个步骤的更多信息。

## 装运它

现在你已经知道 Heroku 如何运行 buildpack，让我们打开你的分叉 build pack，添加 mruby 到里面，然后做一些事情。

打开`mruby-buildpack/bin/detect`，将`"Null"`改为`"mruby"`。那是容易的部分。把你下载的二进制文件放到你的本地机器上，然后把它放到像 S3 这样可以公开访问的地方。这需要放在 Heroku 能拿到的地方。你可以在`https://s3.amazonaws.com/schneems-heroku/mruby.tgz`找到我的副本。如果你没有 S3 账户，任何地方的 public 都可以，包括 dropbox。

打开`mruby-buildpack/bin/compile`。因为这个文件是一个 bash 文件，所以我们可以直接向其中写入命令行命令。如果您喜欢用 ruby 编写脚本，我们可以向 buildpack 添加额外的文件，并让编译步骤调用它们，但是对于这个练习，我们将坚持使用 shell 脚本。首先，我们要确保我们在正确的构建目录中，这是作为 bash 脚本的第一个参数传入的，所以我们可以使用`$1`来获取它

```
 # change to the the BUILD_DIR ($1) cd $1 
```

然后，我们想像这样使用`curl -O`从公共位置下载文件:

```
 # download the mruby binary (-O) silently (-s) curl https://s3.amazonaws.com/schneems-heroku/mruby.tgz -s -O 
```

**注意:*记得点你的网址而不是我的。此外，`curl`中的`-s`标志使下载静音，如果你想看到它发生，就把它取出来。

现在我们需要创建一个目录来存储我们的二进制文件，让我们把它放在`vendor/mruby_bin`中。我们可以运行`mkdir`来做到这一点:

```
# make a directory to untar (like unzip) the binary mkdir -p vendor/mruby_bin 
```

现在我们想使用`tar xvf`命令将文件解压到正确的目录，其中`-C`用于指定文件的输出目录:

```
# untar the binary to the directory we want tar -C vendor/mruby_bin -xvf mruby.tgz 
```

最后清理所有未使用的文件

```
# clean up the unused files rm mruby.tgz 
```

作为一个不错的尝试，你可以替换这一行:

```
echo "-----> Nothing to do." 
```

有了这个:

```
echo "-----> Installing mruby like a boss" 
```

对我来说，最终的编译文件如下所示

```
 #!/usr/bin/env bash # bin/compile <build-dir> <cache-dir> echo "-----> Installing mruby like a boss" # change to the the BUILD_DIR ($1) cd $1 # download the mruby binary (-O) silently (-s) curl https://s3.amazonaws.com/schneems-heroku/mruby.tgz -s -O # make a directory to untar (like unzip) the binary mkdir -p vendor/mruby_bin # untar the binary to the directory we want tar -C vendor/mruby_bin -xvf mruby.tgz # clean up the unused files rm mruby.tgz 
```

这就结束了编译部分，但是我们还没有完全完成。如果你还记得当我们让 [mruby 在本地工作的时候](https://www.sitepoint.com/try-mruby-today/)我们修改了路径，这样我们可以从任何地方调用`mruby`命令。要在 Heroku 应用上启用此功能，我们需要修改路径以包含`vendor/mruby_bin`。

在我们调用 try pushing 之前，我们需要将`vendor/mruby_bin`中的文件添加到我们的路径中，这样我们就可以从任何目录中访问它们。为此，我们需要设置我们的路径，它是环境配置变量的一部分。因为这些在编译阶段对我们来说是不可用的，所以我们必须在发布时添加它们。您需要将路径追加到现有的`PATH`中，以确保我们不会破坏任何东西。

在您的`bin/release`中，您可以像这样以 [YAML](http://en.wikipedia.org/wiki/YAML) 格式输出默认配置变量:

```
 #!/usr/bin/env bash # add our mruby_bin folder to the path on first deploy echo "---" echo "addons:" echo "config_vars:" echo " PATH: $PATH:/app/vendor/mruby_bin" echo "default_process_types:" 
```

注意，我们添加了完整路径`/app/vendor/mruby_bin`，这是一个绝对文件路径(它以`/`开头)。当你的应用程序被部署后，它将驻留在`/app`。因为我们将文件添加到了`vendor/mruby_bin`，所以我们需要将完整的位置添加到路径中。冒号字符`:`是一个分隔符。该路径可以有多条路径，并按顺序进行搜索。

在 buildpack 中改变环境变量的一个重要警告是，它们被认为是“缺省值”,不会覆盖现有的配置变量。如果你在一个应用程序上设置了 config_var，你需要创建一个新的应用程序(`heroku create #...`)来测试所有的改变。

现在我们已经把编译和发布都整理好了，我们需要把我们的修改提交到 buildpack，并把它推回到 github，这样 Heroku 就可以访问它了。

```
$ git add . $ git commit -m "adding mruby like a boss" $ git push origin master 
```

再次检查更改是否显示在 github repo 上，再次检查应用程序中的 buildpack url 是否指向同一个 github repo。当你完成后，我们都准备好部署！

## mruby 构建包部署

回到您的空应用程序(不是 buildpack 文件夹，是我们创建的带有空 readme 的应用程序)。进行新的提交

```
$ git commit -m "empty" --allow-empty 
```

然后部署，您应该会看到您的应用程序在运行:

```
$ git push heroku master ----> Heroku receiving push -----> Fetching custom git buildpack... done -----> mruby app detected -----> Installing mruby like a boss -----> Discovering process types Procfile declares types -> (none) -----> Compiled slug size: 2.9MB -----> Launching... done, v4 http://morning-taiga-3831.herokuapp.com deployed to Heroku 
```

让我们快速验证我们的路径是否正确:

```
$ heroku config PATH: /usr/local/bin:/usr/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/app/vendor/mruby_bin 
```

你应该看看里面的`/app/vendor/mruby_bin`。我觉得不错，让我们试一试，看看是否一切正常。当然，没有 hello world 的语言教程是不完整的，现在让我们在 Heroku 应用程序上运行一个:

```
$ heroku run "mruby -e "puts 'hello world' " " Running `mruby -e "puts 'hello world' " ` attached to terminal... up, run.1 hello world 
```

恭喜你。您刚刚构建了一个功能完整的 buildpack，它运行的语言还没有版本号。你应该借此机会庆祝一下，告诉你所有的编程伙伴你有多棒。当你幸灾乐祸的时候，也不要忘记把它们链接到这篇文章上(当然)。

现在 mruby 在系统上可用了，你可以通过`$ heroku run bash`或者直接通过`run`命令使用它，比如`$ heroku run mirb`。

## 为什么在 Heroku 上运行 mruby？

除了显而易见的“因为你可以”，mruby 是我最喜欢的语言的新实现。这意味着它有不同的优势和劣势。它应该在小目标上运行，并且是轻量级的。我对 mruby 和标准 MRI ruby (cruby)进行了多次测试比较。如果有相当多的浮点计算，那么 mruby 将击败 cruby，但在大量其他操作上，它将远远落后。再次强调，重点不是要快，而是要轻。

在运行测试时，我检查了 ram 的使用情况，发现 cruby 使用的内存大约是 mruby 的 4 倍。因此，如果我们可以用 mruby 重新编写我们的 web 应用程序，理论上我们可以在一个 dyno 中挤进 4 倍数量的进程，这将是非常令人印象深刻的。

重要的是要记住，mruby 并不是 cruby 的替代品，它仍处于初级阶段。它目前没有标准库，也不具备使用`require`的能力，所以不要指望很快就能在上面运行 rails 应用。

由于它的占用空间相对较轻，我们可以在一个 dyno 上同时运行许多进程，并且由于它具有出色的浮点性能:mruby 非常适合在 Heroku 的可伸缩架构上并行化 map-reduce 或机器学习作业。

随着该语言的成熟和在现实世界中得到更多的使用，看看 mruby 嵌入到什么类型的设备中，什么游戏选择使用它作为脚本语言，以及它在云中承担什么样的疯狂任务将是令人兴奋的。

## 鳍状物

哇，那是相当多的工作。你能想象从头开始编写一个 buildpack 来处理任何 Rack/Rails 应用程序吗？幸运的是，我们会为你处理的。如果您确实需要定制一个构建包，从一个现有的(非空的)构建包开始会容易得多。

如果您在系统上需要一个二进制文件，但如果您想将 mruby 二进制文件放在 git repo 中并更改配置路径以指向该目录，则不希望维护一个 buildpack 或实例的分支。

现在，您对 Heroku 构建过程的内容以及编译和打包二进制文件在 Heroku 上的工作方式有了更多的了解。如果你用 mruby 或 Heroku 上的定制 buildpack 做了一些有趣的事情，请告诉我: [@schneems](http://twitter.com/schneems) 。在一天结束的时候，你对你的系统如何工作了解得越多，你就能更好地使用它。

## 分享这篇文章