# 使用服务器端 Git 挂钩的一键式应用部署

> 原文：<https://www.sitepoint.com/one-click-app-deployment-server-side-git-hooks/>

## 摘要

这篇文章向用户展示了如何使用 git post-receive 钩子来部署一个简单的网站。它涵盖了足够的背景信息，以便读者可以在时间允许的情况下进一步扩展他们的知识。

## 一键部署

我们经常说宣扬**一键部署**的咒语吗？每个项目？每天？*每犯？*如果我们有任何相似之处，你会很容易认同其中的一个或多个。我们都有过这样的经历，或者有过这样的项目，我们不得不跨越许多障碍，将我们的应用程序部署在一个或多个环境中。

我们知道这最终会分散我们最擅长的事情的注意力:**应用程序设计和开发**。我们知道我们应该专注于其他领域，例如*架构*、*测试*，以及*设计*。然而，我们却反复陷入复制文件、运行脚本等细枝末节中。我们反复陷入任务中，而这些任务**应该总是自动化的**。

也许您对以下场景之一很熟悉:

*   遵循部署清单
*   SSH 连接到远程机器并运行一组脚本
*   手动将文件 FTP 到远程服务器

我希望你不是在做最后一个…

多年来，我只是说说而已，并没有付诸行动。但是最近，我的生活，我的时间，我的家人和朋友变得太重要了，不能再这样下去了。他们变得太重要了，不能浪费时间，花费无数的时间，部署代码。也许你也一样。如果你是，这个系列文章就是为你准备的。

最近我在 Sitepoint 上看了 Tim Boronczyk 的 [Git Hooks for Fun and Profit](https://www.sitepoint.com/git-hooks-fun-profit/) 帖子，我想*如果*会怎样。我阅读了关于使用 git 钩子的手册，并从 Tim 的帖子中获得了灵感，认为一定有一种方法可以用它们自动部署远程 web 应用程序。

我给自己设定了一个简单明确的目标:

> 我只需要运行 git push，部署就会得到处理，不再需要我的参与。

当我谈到这件事时，朋友们有点怀疑地看着我；但是我受到了启发。无可非议的是，在这一点上，你可能想知道，为什么不使用持续集成(CI)服务器，比如 Jenkins 或 Hudson，或者像 [codeship.io](http://codeship.io) 这样的服务？这是一个很好的问题，如果你认为它，这样做的要点。**许多不**。

为什么不用一个？正如 Beth Tucker-Long 去年在 PHPUK 的演讲中所说的那样，并非所有的需求都如此复杂或精密，或者能够证明实施成本的合理性。

所以在今天的文章中，我们开始了一个系列，看看 git 钩子如何帮助你实现简单有效的部署过程。今天的是最简单的。

## 它将如何工作？

它由 3 部分组成:

1.  工作知识库
2.  一个偏僻的、空的仓库
3.  工作目录(您部署的站点或测试分支)

我们将像往常一样在本地工作目录中开发我们的应用程序，并像往常一样推送到远程位置(例如 *Github* 、 *BitBucket* 等)。尽管远程存储库不是标准的开发存储库。它必须是所谓的**裸库**，git scm 书[将](http://git-scm.com/book/ch4-2.html)描述为:

> 不包含工作目录的存储库

如果这看起来有点混乱，那是完全正常的。我做了大量的阅读，因为我开始了解这个设置。在研究过程中，我找到了 bitflop.com 的这句话，它简洁地解释了这个问题。

> 在 Git 中，你应该只使用一个“空的”存储库来克隆、提取和推送。它没有一个签出树，所以它只是做“服务器”在集中式 VCS 中名义上做的事情——当你推它时,**记录提交、分支**等，当你克隆或拉它时,**给你最新的版本**。

因此，在我们的裸存储库中，我们将无法编辑文件和进行更改。它纯粹是为了让我们将最新的变更发布到工作目录，以及将这些变更从工作目录部署到工作目录。如果有帮助，用下面的图片来概念化。

![](img/0cd5701cdab59285ee801ebfe8c19863.png)

## 设置远程存储库

出于本文的目的，我将假设您使用的是 Linux (Debian)服务器，并且远程存储库将存储在`/opt/git`中。我们就叫它`simpleapp.git`。我在结尾处加上了`.git`，因为这似乎是公认的惯例。

利用这些假设，我们运行以下命令来设置远程存储库:

```
$ mkdir -p /opt/git
$ cd /opt/git
$ git clone --bare simpleapp simpleapp.git
Initialized empty Git repository in /opt/git/simpleapp.git/
```

完成后，如果您`ls`这个目录，您会注意到它包含了通常存储在`.git`目录中的内容。这很好，因为在这种情况下，工作目录实际上是部署目录。

## 接收后挂钩

对于远程存储库设置，我们接下来添加一个 git 接收后挂钩。我已经在`post-receive.sh`提供了一个样品:

```
#!/bin/sh
# 
## store the arguments given to the script
read oldrev newrev refname

## Where to store the log information about the updates
LOGFILE=./post-receive.log

# The deployed directory (the running site)
DEPLOYDIR=/var/www/html/simpleapp

##  Record the fact that the push has been received
echo -e "Received Push Request at $( date +%F )" >> $LOGFILE
echo " - Old SHA: $oldrev New SHA: $newrev Branch Name: $refname" >> $LOGFILE

## Update the deployed copy
echo "Starting Deploy" >> $LOGFILE

echo " - Starting code update"
GIT_WORK_TREE="$DEPLOYDIR" git checkout -f
echo " - Finished code update"

echo " - Starting composer update"
cd "$DEPLOYDIR"; composer update; cd -
echo " - Finished composer update"

echo "Finished Deploy" >> $LOGFILE
```

这个用 bash 编写的脚本将在远程存储库接收到推送后启动。它为我们管理整个部署流程，包括以下步骤:

1.  将部署目录中的代码更新到最新版本
2.  运行`composer install`，确保我们拥有所有的依赖项

让我们一点一点来看:

```
read oldrev newrev refname
```

在这里，我们存储在调用接收后脚本时传递给它的三个参数。这些是:

*   上一次提交 SHA1 哈希
*   最新提交的 SHA1 哈希
*   [refname](https://www.kernel.org/pub/software/scm/git/docs/git-show-ref.html) (包含分行信息)

这有助于跟踪正在部署的内容，并在需要时进行回滚。然而，这个脚本不处理回滚。

```
LOGFILE=./post-receive.log
DEPLOYDIR=/var/www/html/simpleapp
```

接下来，我们设置一个变量来记录部署和部署目录的输出。

```
echo -e "Received Push Request at $( date +%F )" >> $LOGFILE
echo " - Old SHA: $oldrev New SHA: $newrev Branch Name: $refname" >> $LOGFILE
echo "Starting Deploy" >> $LOGFILE
```

这里我们记录了何时收到推送请求及其详细信息。这样，如果我们需要调试，我们就有了相关的信息和一条快速消息，告诉我们正在开始部署过程。

```
echo " - Starting code update"
GIT_WORK_TREE="$DEPLOYDIR" git checkout -f
echo " - Finished code update"
```

在这里，我们告诉 git 工作树在哪里，并指示 git 将代码的最新副本签出到该目录，删除任何可能已经手动进行的更改。

```
echo " - Starting composer update"
cd "$DEPLOYDIR"; composer update; cd -
echo " - Finished composer update"
```

既然代码已经更新，当我使用 composer 进行依赖项管理时，我们运行`composer update`,因为`composer.json`可能已经改变了应用程序的依赖项。

```
echo "Finished Deploy" >> $LOGFILE
```

最后一步是表明部署现在已经完成。在一个标准项目中，您可能会做得更多，包括:

*   清除和预热缓存目录
*   运行数据库迁移
*   运行单元和回归测试
*   检查关键目录的权限

然而，这是一个简单的例子，我不想让你偏离主要目标。在`hooks`目录下，创建一个名为`post-receive`的新文件，并添加`post-receive.sh`的内容。您应该只需要更改一个变量，`DEPLOYDIR`。

根据自己的需要进行更改，并且不要忘记使文件可执行，否则它将无法运行。如果您不熟悉如何操作，如下所示:

```
chmod +x post-receive
```

## Git 用户

在远程服务器上，还剩下一步，git 用户。这是将管理流程的用户，也是我们将进行身份验证的用户。因此，如果它还不存在，运行以下命令来创建它:

```
sudo adduser git
```

之后，您需要将您的公钥添加到。git 用户主目录中的 ssh 目录。

**注意:**如果你没有或者不熟悉它们，[GitHub 的这个优秀的简短指南](https://help.github.com/articles/generating-ssh-keys)会带你创建一个。

复制公钥最简单的方法可能是在终端会话之间复制内容。所以复制~/的内容。ssh/id_rsa.pub 通过 scp 或终端会话连接到您的远程服务器，并将它们添加到远程 authorized_keys 文件中。

然后在本地机器上，通过运行下面的命令，测试现在可以 ssh 到远程机器，适当地替换掉`remoteserver`。

```
ssh git@remoteserver 
```

一切正常，不会提示您输入用户名和密码。接下来，确保 git 用户对`/opt/git/simpleapp`目录和部署目录都有适当的权限。

## 添加远程存储库

之后，我们就差不多完成了。在您的工作目录中，运行以下命令添加新的远程存储库:

```
git remote add test git://remoteserver/opt/git/simpleapp.git
```

然后，使用以下内容验证它是否就位:

```
git remote
```

有了这些，我们需要做的就是开始开发我们的应用程序，并根据需要进行推送。

## 最后

我知道这是一个很复杂的过程，至少在开始的时候需要接受很多。然而，就像 Linux 一样，一旦你把一切都准备好并运行起来，大部分工作就结束了。

你怎么想呢?告诉我你还想看什么。我希望本文向您展示了自动部署的可能性和直接性。

你已经这样做了吗？你有没有用不同的方式来处理它？你能做得更好吗？请在评论中给我你的想法。

## 分享这篇文章