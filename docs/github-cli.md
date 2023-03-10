# GitHub CLI:GitHub 命令行指南

> 原文：<https://www.sitepoint.com/github-cli/>

在本快速入门指南中，您将学习 [GitHub CLI](https://cli.github.com/) 。了解 GitHub CLI 工具的用途、设置和使用方法。

如果你已经熟悉了 [Git 命令](https://www.sitepoint.com/premium/books/jump-start-git-2nd-edition/read/1/utm-preview/read/1/?preview=true&utm_source=blog&utm_medium=articles)，你显然意识到你需要切换到 web 浏览器，以便在你的 GitHub 库上执行各种操作。使用新的 GitHub CLI 工具，您可以在不离开命令行界面的情况下执行这些操作。

## GitHub CLI 设置

要开始，只需访问[安装页面](https://cli.github.com/manual/installation)并找到如何为您的操作系统安装 GitHub CLI 的说明。对于 Windows 和 macOS，您可以使用包管理器来安装 GitHub CLI 并使其保持最新。对于 Linux 用户，你需要从[最新版本](http://github.com/cli/cli/releases/latest)页面下载这个包。也有针对 Windows 用户的签名 MSI 安装程序，但请注意，如果您选择这种方式，您必须重新下载并手动更新该工具。对 Windows 用户来说，最简单的方法是使用[独家新闻](https://scoop.sh/)软件包管理器。

以下是每个受支持平台的安装说明快照:

*   窗口:

    ```
    scoop bucket add github-gh https://github.com/cli/scoop-gh.git
    scoop install gh 
    ```

*   macOS:

    ```
    brew install github 
    ```

*   Debian/Ubuntu Linux:

    ```
    sudo apt install git &amp;&amp; sudo dpkg -i gh_*_linux_amd64.deb 
    ```

*   Fedora/Centos Linux:

    ```
    sudo yum localinstall gh_*_linux_amd64.rpm 
    ```

*   Arch Linux:

    ```
    yay -S github-cli 
    ```

在 Windows 上，我推荐使用 [Git Bash](https://gitforwindows.org/) 终端。这个接口将允许您访问常见的 Linux 命令和 Bash 特性，比如自动完成。它还通过终端集成得到了 Visual Studio 代码的官方支持。

安装 GitHub CLI 后，您需要验证您的帐户。运行任何命令都会触发此身份验证过程。比如试试`gh repo view cli/cli`。对于首次使用的用户，系统会提示您以下信息:

![GitHub authentication](img/d9f48074038c49bd23ffea8f9422a14e.png)

只需按下`Enter`键即可启动该过程，如下图所示:

![Gitub authorization](img/8d014947f5f65da812540697742deb8c.png)

一旦您提供了您的密码，您将得到一个“成功认证 GitHub CLI”的消息。现在，您可以通过命令行终端与 GitHub 平台进行交互。下一步是实现自动完成，这是可选的。只需将这一行添加到您的`~/.bash_profile`:

```
eval "$(gh completion -s bash)" 
```

您也可以在当前终端中运行上述命令，无需重启终端即可获得自动完成功能。要确认它正在工作，请键入`gh repo`，然后按两次`tab`。它应该显示四个不同的命令，你可以添加到当前的`repo`命令中。

## GitHub CLI 命令结构

`gh`命令结构是树状的，容易记忆。基本上有两个级别的命令。第一级仅包含六个命令:

*   `config`
*   `repo`
*   `issue`
*   `pr`
*   `gist`
*   `credits`

每个命令都有第二级命令，您可以在其中指定想要执行的操作，例如`gh repo view`或`gh pr list`。然而,`credits`司令部没有二级司令部。当执行时，它只是列出一个库的贡献者的名字。这里有一个简单的例子，你可以自己尝试一下:

```
$ gh credits cli/cli 
```

在接下来的章节中，我们将更详细地研究其余的命令。

## GitHub 存储库命令

使用`gh`命令克隆存储库比使用`git`命令更容易。要进行克隆，您只需执行以下格式的命令:

```
gh repo clone OWNER/REPO 
```

这种格式使得从内存中克隆更加容易。您不再需要键入或复制粘贴长的 Git URL 来进行克隆。下面是一个例子:

```
$ gh repo clone tailwindcss/tailwindcss 
```

您还可以从命令行轻松地将现有的存储库分支到您的帐户。尝试:

```
$ gh repo fork cli/cli 
```

在分叉过程中，该工具会询问您是否也要克隆。如果您说“是”，它将进行克隆，设置远程上游分支并自动为您执行更新。那很方便。您可以通过命令行`git config -e`检查存储库的配置来确认这一点。下面是我的输出:

```
[core]
 repositoryformatversion = 0
 filemode = false
 bare = false
 logallrefupdates = true
 symlinks = false
 ignorecase = true
[remote "origin"]
 url = https://github.com/brandiqa/cli.git
 fetch = +refs/heads/*:refs/remotes/origin/*
[branch "trunk"]
 remote = origin
 merge = refs/heads/trunk
[remote "upstream"]
 url = https://github.com/cli/cli.git
 fetch = +refs/heads/*:refs/remotes/upstream/* 
```

对于这个项目，`trunk`是默认的分支。你需要像往常一样使用`git`命令[同步你的分叉回购](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)。在`cli`回购的情况下:

```
# Make sure you’re in the default branch
$ git checkout trunk

# Fetch updates
$ git fetch upstream

# Merge
$ git merge upstream/trunk

# Update your remote repo
$ git push 
```

您还可以使用`gh repo view`命令查看 GitHub 上托管的项目的描述和自述文件。尝试以下命令:

```
$ gh repo view facebook/react 
```

到目前为止，你已经学会了如何使用`gh repo`命令来`clone`、`fork`和`view`。让我们从命令行创建一个新的 GitHub 存储库。首先，我们需要创建一个新项目。让我们快速生成一个 Next.js 项目。询问时，使用“默认入门应用程序”模板:

```
$ npx create-next-app example-next
...
# navigate to project folder after creation process is completed
$ cd example-next 
```

您会发现项目的本地 Git repo 已经被初始化。要从命令行创建 repo，只需运行以下命令:

```
$ gh repo create --public
✓ Created repository brandiqa/example-next on GitHub
✓ Added remote https://github.com/brandiqa/example-next.git

# Push your project code to your new remote repository
$ git push -u origin master 
```

如果不指定`--public`选项，默认情况下将创建一个私有存储库。以下是您可以指定的标志的完整列表:

```
 -d, --description string    Description of repository
      --enable-issues        Enable issues in the new repository (default true)
      --enable-wiki          Enable wiki in the new repository (default true)
  -h, --homepage string      Repository home page URL
      --public               Make the new repository public (default: private)
  -t, --team string          The name of the organization team to be granted access 
```

如果您想在不同的组织下创建回购协议，您需要使用以下语法来创建回购协议:`gh repo create org/repo`。

## 拉请求命令

现在可以在终端上创建一个拉动请求(PR) [供您的团队评审](https://www.sitepoint.com/git-techniques-to-know-before-you-join-a-team/)。在你提交了一个特性或者 bug 分支之后，你可以执行`gh pr create`。这将交互地创建拉请求:

![Creating a pull request](img/2a03f4f517fd840bfce67a220ed55088.png)

这是完整的输出:

```
$ gh pr create
Creating pull request for feature-1 into master in brandiqa/example-next
? Title Updated Home Page Title
? Body &lt;Received&gt;
? What is  next? Submit
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 398 bytes | 398.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
remote:
remote: Create a pull request for 'feature-1' on GitHub by visiting:
remote:      https://github.com/brandiqa/example-next/pull/new/feature-1
remote:
To https://github.com/brandiqa/example-next.git
* [new branch]      HEAD -&gt; feature-1
Branch 'feature-1' set up to track
remote branch 'feature-1' from 'origin'.
https://github.com/brandiqa/example-next/pull/1 
```

您也可以运行带有如下标志的 create 命令:

```
gh pr create --title "Pull request title" --body "Pull request body" 
```

为了确认已经在 GitHub 平台上创建了 pull 请求，您可以执行命令`gh pr list`来列出所有的 pull 请求。以下是输出:

```
$ gh pr list
Showing 1 of 1 pull request in brandiqa/example-next
#1  Updated Home Page Title  feature-1 
```

对于更活跃的项目，如 [TailwindCSS](https://github.com/tailwindcss/tailwindcss) ，您将获得一个更长的活跃 PRs 列表:

![PR list](img/42284dd0f754164fe0d5deff4799d272.png)

如上所示的列是:

*   请购单编号
*   公关描述
*   公关部门

您可以使用命令`gh pr checkout <number>`来检查一个拉取请求。该命令将提取远程特征分支并切换到该分支。在那里，您还可以执行`gh pr diff`来查看发生了什么变化:

![checkout diff](img/e590d1ae60bf9f8c168daa2202ab967e.png)

让我们来看看`gh pr merge`命令。你可能知道，GitHub 会检查你的代码中的漏洞，并通过 bump pull 请求提供解决方案。这里有一个例子:

![GitHub security bumps](img/ebe0be46c808db88b2008b8296e7c63c.png)

逐一合并这些 PRs 对您来说可能很麻烦，尤其是在慢速互联网连接上。但是，您可以通过使用终端的`gh pr merge`命令来加速这个过程。下面是运行中的命令的屏幕截图:

![pr merge](img/9ee2d220fb781158e22f1d9fefbe9221.png)

完成合并后，最好立即执行拉操作。如果您希望在一个步骤中完成所有这些操作，而不是以交互方式运行命令，可以使用标志:

```
gh pr merge -m -d &lt;number&gt; &amp;&amp; git pull 
```

还有其他非常有用的`gh pr`命令，我们不会在这里讨论，但我会很快提到它们:

*   [`gh pr view`](https://cli.github.com/manual/gh_pr_view) :显示拉取请求的标题、正文等信息。
*   [`gh pr status`](https://cli.github.com/manual/gh_pr_status) :显示相关拉式请求的状态。
*   [`gh pr ready`](https://cli.github.com/manual/gh_pr_ready) :提出一个拉式请求，以备审核。
*   [`gh pr review`](https://cli.github.com/manual/gh_pr_review) :给拉式请求添加审核。
*   [`gh pr close`](https://cli.github.com/manual/gh_pr_close) :关闭拉式请求。
*   [`gh pr reopen`](https://cli.github.com/manual/gh_pr_reopen) :重新打开拉式请求。

## 发布命令

[问题](https://guides.github.com/features/issues/)是我们如何在 GitHub 项目库中跟踪 bug、任务和特性请求。我们可以使用命令`gh issue create`通过终端创建一个新问题:

![Creating an issue](img/79780aea5612c245753b8255b03f0bb1.png)

还有一个列出所有问题的`gh issue list`命令。下面是正在使用的命令的屏幕截图示例:

![GitHub issue list](img/8b678adfb428162d0d28abccadb4961c.png)

其余的`gh issue`命令与`gh pr`命令非常相似。下面是一个快速总结:

*   [`gh issue view`](https://cli.github.com/manual/gh_issue_view) :显示一期的标题、正文等信息。
*   [`gh issue status`](https://cli.github.com/manual/gh_issue_status) :显示相关问题的状态。
*   [`gh issue close`](https://cli.github.com/manual/gh_issue_close) :关闭一个问题。
*   [`gh issue reopen`](https://cli.github.com/manual/gh_issue_reopen) :重提旧事。

## 要点命令

在 GitHub 上创建一个[要点](https://help.github.com/en/github/writing-on-github/creating-gists)现在可以通过控制台终端使用命令`gh gist create <file>`轻松完成。下面是带有`--public`标志选项的命令示例:

![Creating a gist](img/92a0107584596b37161e7c9ffa147ca8.png)

默认情况下，如果不指定`--public`标志，将会创建一个私有 gist。这里有一个[链接](https://gist.github.com/brandiqa/daa98001c23baa14eaa7cbfacf45b9d3)到我刚刚创建的要点。它包含了我经常使用的常见 Linux 命令别名的列表。

没有其他的 gist 命令，但是还有一个标志我没有提到:

*   `-d`、`--desc`字符串:对此要点的描述。

## GitHub CLI 摘要

我希望你已经学会了如何使用新的`gh`命令行工具。我建议您将该工具集成到您的工作流程中，并使之成为一种习惯，因为它确实节省了一些管理您的存储库的时间。请注意，该工具仍在开发中，在本文发表后可能会出现新的命令。您可以参考[手册](https://cli.github.com/manual)了解新功能和使用现有工具的信息。

我没有提到的一个命令是`gh config`。这是因为在撰写本文时没有太多关于使用它的文档。关注手册和[发布](https://github.com/cli/cli/releases)页面，获取最新更新。

## 分享这篇文章