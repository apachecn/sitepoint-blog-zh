# OSQuery:使用 SQL 探索您的操作系统

> 原文：<https://www.sitepoint.com/osquery-explore-os-sql/>

如果这个标题听起来像一个令人困惑的骗局，这是可以理解的——但它非常非常真实。在 10 月 30 日的[公告中，脸书发布了](https://code.facebook.com/posts/844436395567983/introducing-osquery/)[OSQuery](http://osquery.io/)——一种通过编写 SQL 查询来检查你的 OS X 或 Linux 操作系统当前状态的新方法。

![](img/92c3fe9ed52cce76746ccbb86bde7ce8.png)

起初，这听起来可能很奇怪，你的直觉反应可能是“为什么？!"，但进一步检查，有用的方面变得明显。让我们看看怎么做。在这篇文章中，我将告诉你为什么它可能对你有用，告诉你如何安装它，并指导你在一个准备好的流浪者盒子上做一些示例查询，如果你目前没有运行 OS X 或 Linux，你可以使用它。

## 这是什么？

![](img/a317b397632ebad07af1369b4034fcd0.png)

我不会重复他们的公告帖子——关于实施细节[见那里](https://code.facebook.com/posts/844436395567983/introducing-osquery/)。简而言之，OSQuery 假装是一个关系数据库，并包含一些“表”(用引号括起来的表，因为它们实际上并不像您在 MySQL 中习惯的表那样存在)，这些表以一种方式公开操作系统数据，使其可以被 SQL 语句查询(是的，包括连接和全部内容！).

如果你曾经遇到过这样的情况，你不能运行 Apache，因为一个端口已经被占用，你不得不去 grep 进程列表，却发现一个死的 Skype 实例正在占用端口 80，你就会知道欣赏 OSQuery 的简单性。

OSQuery 在 CentOS、Ubuntu 和 OS X 上运行，因此支持您的生产服务器、开发 playbox 以及您可以访问的任何其他机器的操作系统，如您的孩子或员工的操作系统，允许您使用它来监控整个生态系统的操作系统状态。它是完全开源的，甚至有一个关于创建你自己的表格的[指南](https://github.com/facebook/osquery/wiki/creating-a-new-table)，以防某些表格丢失，而你需要它们。团队会定期添加新表，所以即使你不想贡献，但仍然想使用一些丢失的表，如果你给它一些时间，它们很有可能会出现。

该软件是通过(目前)所有支持的操作系统的自建包安装的，并带有`osqueryi`——一个用于处理查询的交互式控制台——和`osqueryd`——一个守护程序，例如，你可以安排它定期运行并在受监控的机器上聚合数据。[文档](https://github.com/facebook/osquery/wiki)非常好，所以征服 OSQuery 的每一个方面就像花一个下午在上面一样简单。

## 安装和使用 OSQuery

OSQuery 为您提供了一个默认的浮动配置，您可以使用它来构建这个包，您最终会将这个包分发到您希望安装它的所有其他机器上。如果你不熟悉流浪者，你真的应该熟悉，在这里看我们关于主题[的帖子。](https://www.sitepoint.com/blog/)

如果您从未使用过虚拟机，安装过程会有些复杂，所以让我们来分解一下。假设我们有一台 Ubuntu 14.04 机器，我们想在上面安装 OSQuery。通常情况下，你可以通过 Aptitude 这样的包管理器发出类似于`apt-get install`的命令来安装软件。然而，由于 OSQuery 还没有出现在这些发行版的官方回购中，我们需要*手动构建*包，然后从本地位置安装它(通过将`.deb`文件复制到目标机器上)，而不是像往常一样从远程仓库安装。这听起来可能比实际更复杂，所以让我们一步一步来。

### 1.克隆并启动 OSQuery 框

确保您的主机上安装了 Git、vagger 和 Virtualbox，并执行以下命令:

```
git clone https://github.com/facebook/osquery
cd osquery
vagrant up ubuntu14
```

如果你的《流浪者》有之前下载的 Ubuntu14 镜像，你应该最多一分钟就可以运行了。否则，它将下载映像，这可能需要一段时间，然后创建虚拟机。

### 2.在虚拟环境中构建

使用`vagrant ssh`SSH 到您的虚拟机。在我们的情况下，这将是

```
vagrant ssh ubuntu14
```

一旦进入，执行:

```
sudo su
cd /vagrant
./tools/provision.sh
```

注意，如果你在 Windows 上，著名的符号链接错误会再次出现。在它无法完成后，只需重新运行配置脚本，它应该可以工作。这是一个奇怪的小问题，需要进一步的调查，如果我找到任何真正的解决方法或者问题得到解决，我会发帖子回来。

这将更新 Ubuntu 实例，并下载 OSQuery 自我构建所需的所有内容。然后，我们告诉它把自己包装成一个可安装包。

```
make
make package
```

然后您应该能够在`/vagrant/build/linux/osquery-0.0.1-trusty.amd64.deb`中看到这个包。

### 3.安装 OSQuery

要安装它，我们可以使用默认的 Debian 包管理系统:

```
sudo dpkg -i osquery-0.0.1-trusty.amd64.deb
```

现在把它安装到你的任何一台 Ubuntu 14.04 机器上就像复制`.deb`文件并运行上面的命令一样简单。我们甚至可以将它安装到构建它的操作系统中。

如果你需要其他操作系统的软件包，程序是完全一样的，只需稍作改动——只需按照[的说明](https://github.com/facebook/osquery/wiki)操作。

### 5.使用 OSQuery

让我们看看它是否有效。通过执行`osqueryi`进入交互控制台。您应该会看到类似这样的内容:

![](img/02189ddf9523f876ee788021460fe00a.png)

让我们看一个测试查询。将以下内容粘贴到控制台中并执行它:

```
SELECT * FROM users;
```

您应该会看到这样的事情发生:

![](img/593ec191865864c067ea2971a865009b.png)

您可以通过执行`.tables`列出所有可用的表格，使用`.help`执行所有命令，并使用`.exit`退出。

#### 恶意行为者示例

根据他们的公告，查询:

```
SELECT name, path, pid FROM processes WHERE on_disk = 0;
```

列出启动它们的二进制文件不再存在于磁盘上的所有进程。运行一个进程然后消失是恶意行为者的常用手段，如果你的系统没有受到损害，它不应该返回任何东西。

#### 具有组的所有用户示例

```
SELECT u.uid, u.gid, u.username, g.name, u.description FROM users u LEFT JOIN groups g ON (u.gid = g. gid);
```

上面的查询将输出操作系统的所有用户，包括他们的 id、他们的组和组名以及他们的描述。

#### 查找所有空组

```
SELECT groups.gid, groups.name FROM groups LEFT JOIN users ON (groups.gid = users.gid) WHERE users.ui d IS NULL;
```

该查询查找所有空的操作系统用户组，即没有用户属于的用户组。

这些都是非常简单的例子，但是您已经可以看到表之间的交互如何快速有效地揭示有趣的信息。

## 结论

OSQuery 是脸书最新的开源奇迹——一种用类似关系数据库的 API 来公开系统级数据的方法，让我们可以像查询一堆关系数据一样查询我们的操作系统。虽然对监控服务器或服务器集群很有用，但它肯定也有其他应用——从恶意软件检测到僵尸进程杀死，应有尽有。

你想到什么独特的用途了吗？想写写他们吗？取得联系！

## 分享这篇文章