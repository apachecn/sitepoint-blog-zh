# 如何结合 SVN 或 Git 使用 Dropbox 进行云 SCM

> 原文：<https://www.sitepoint.com/how-to-use-dropbox-with-svn-or-git-for-cloud-source-control-management/>

[Dropbox](http://www.dropbox.com) 是一个流行的[基于云的](http://en.wikipedia.org/wiki/Cloud_computing)存储和备份服务。它的成功在于维持了可靠的服务，并且可以在任何平台上使用，包括移动平台。

在本文中，我将解释如何使用 Dropbox 来管理小型个人项目的源代码。我将提供使用 Git 和 Subversion 的例子。

### 概观

所以我们都把 Dropbox 当做一种虚拟驱动；这是一个存放我们想从任何地方、任何设备访问的所有资料的地方。但是我们的源代码呢？大多数开发人员都熟悉用于源代码管理和版本控制的 [Github](http://github.com) 或 [Bitbucket](https://bitbucket.org) 。

当然，我并不是说 Dropbox 可以取代这两种服务。然而，用它来照看你自己的小项目呢？您仍然希望使用版本控制，但是不需要 Github 这样的服务的那种项目？

### Dropbox 不是已经有版本控制了吗？

算是吧。但是它不适合版本控制。它更像是您存储的每个文档/文件的备份。此外，如果您仅仅依赖于该特性，您将无法提交和推送存储库。

[![Dropbox Revisions](img/3827f1e59a0903007973e80cf5a11b34.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/13.png)

Dropbox 版本

### Git 入门

我假设您有一个想要使用的编码项目。如果您还没有，请在项目文件夹中从控制台/命令行初始化 Git 存储库:

```
git init
```

将文件添加到项目中:

```
git add .
```

然后提交文件:

```
git commit -am 'initial commit'
```

到目前为止，所有内容都在您的计算机本地。接下来，将目录切换到 dropbox 文件夹，并创建一个文件夹来存放 Git 项目。出于冒险精神，我将我的项目命名为“git_projects”:

```
cd ~/Dropbox
mkdir git_projects
```

现在，您可以切换回包含实际代码的目录:

```
cd ~/sample_project
```

接下来，我们需要在 Dropbox 文件夹中克隆我们刚刚创建的项目文件夹。我们将把它克隆为一个裸项目，这样我们就可以对它进行推拉。如果您已经克隆了一个带有工作树的项目，这将导致推/拉过程复杂化:

```
git clone --bare . ~/Dropbox/git_projects/sample_project.git
```

如果您还没有向项目中添加任何代码，您将会收到一个警告，提示您克隆了一个空项目。不过没关系，你可以很快添加一些文件。接下来，我们需要添加我们的 Dropbox 项目作为远程别名，以便我们可以推送它:

```
git remote add sample_project ~/Dropbox/git_projects/sample_project.git
```

现在，您可以像平常一样添加代码并提交。当您准备好推送至 Dropbox 存储库时，您只需执行以下操作:

```
git push sample_project master
```

要使用另一台计算机上的代码，您可以克隆项目:

```
git clone ~/Dropbox/git_projects/sample_project.git
```

然后像平常一样工作，当你完成更新后，推回到 Dropbox 存储库。

### Subversion 也可以

要使用 Subversion 实现类似的功能，最简单的方法是首先创建 Dropbox 目录，将其转换为 Subversion 存储库，然后签出主干以添加代码。

```
cd ~/Dropbox
mkdir subversion_projects
cd subversion_projects
```

现在我们可以为我们的特定项目创建一个存储库:

```
svnadmin create sample_project
```

然后为 Subversion 项目创建标准布局。我们将从主干开始:

```
svn mkdir file://localhost/Users/you/Dropbox/subversion_projects/sample_project/trunk -m 'repo layout'
```

如果你使用 Windows，你会发现这篇文章很有用，尽管它现在有点过时了。你也可以看看用[龟](http://tortoisesvn.tigris.org/)让你的生活更轻松。

我们可以继续进行默认布局:

```
svn mkdir file://localhost/Users/you/Dropbox/subversion_projects/sample_project/branches -m 'repo layout'
svn mkdir file://localhost/Users/you/Dropbox/subversion_projects/sample_project/tags -m 'repo layout'
```

然后，检查行李箱:

```
svn checkout file://localhost/Users/you/Dropbox/subversion_projects/sample_project/trunk svn_project
```

注意，您可以像平常一样继续添加代码。完成更新后，使用 svn add 添加任何新文件，然后提交更改:

```
svn commit -m 'latest updates'
```

正如 git 示例一样，您可以简单地在另一台机器上签出一个工作副本，添加/编辑代码，然后提交回来。当你再次使用原来的电脑时，不要忘记更新 svn。

### 最后

对于大型团队来说，使用 Dropbox 作为 Git 和 Subversion 项目的存储库可能不是一个好主意。然而，对于你自己的项目来说，这比使用 Github 上的私有库要便宜得多。它还为您提供了一种在不同计算机上处理个人项目的方式，无论您喜欢 Git 还是 Subversion。

如果你有两个 Dropbox 帐户:一个用于工作，一个用于个人使用，你仍然可以使用上述程序。使用 Dropbox 的共享功能，在账户之间共享回购文件夹。然后，您仍然可以添加代码并提交，就像您使用 Github 这样的服务一样。

## 分享这篇文章