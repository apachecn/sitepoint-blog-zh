# 创建 CrunchBang Rails 开发环境

> 原文：<https://www.sitepoint.com/creating-a-crunchbang-rails-development-environment/>

Andy Hawthorne 的《JumpStart Rails》一书中的一个完成教程的方法是创建一个沙盒开发环境。如果您使用 Windows，以下将是一个特别有用的解决方案，尤其是如果您想尝试安装 Rails 的另一个选项，而不是书中描述的 Rails 安装程序。

我使用 Linux 已经有一段时间了，但是最近安装了 [CrunchBang 发行版](http://crunchbang.org) (#！给它的朋友)。这是基于超稳定的 Debian 发行版，并把自己描述为一个“敏捷”的操作系统。的确是这样——我发现它又快又稳定，而且还有一个充满活力和友好的社区。

我将从头到尾检查一遍安装过程，这样 Ruby、Rails 和 Postgresql 都已安装完毕，可以开始运行了。就我个人而言，我建议你安装 CrunchBang 作为你新的主要操作系统，但是如果你不想走那么远，那么我们将考虑使用 VirtualBox 在虚拟机上安装它。

## 安装 VirtualBox

如果你想跑#！在虚拟机上，你需要 VirtualBox 或其他虚拟化软件。从这里可以得到[。所有主要操作系统都包含安装说明。](https://www.virtualbox.org/wiki/Downloads)

您还需要#的 ISO 映像！你可以从 CrunchBang 网站获得一个[种子。](http://crunchbang.org/download/)

一旦你有了这两个项目，是时候安装#了！。

## 正在安装#！

启动 VirtualBox，然后从顶部菜单中选择“新建”。从这里，为您的新虚拟机选择一个名称(我将我的虚拟机命名为“#！”)，选择“Linux”作为类型，选择“Debian”作为版本，如下图所示:

![screenshot1](img/6141402a014b61590a41a3312f624f66.png)

现在继续点击“下一步”，选择默认选项，直到您到达需要为您的新虚拟硬盘选择名称的屏幕。选择一个名称(我选择了“__virtualdrive”)，然后单击“下一步”完成安装。您应该会看到与下面的屏幕截图类似的屏幕:

![screenshot2](img/c5e48f38bf5c4395e6a294309ea73561.png)

现在双击我们刚刚创建的虚拟计算机，您将被带到另一个菜单，在那里您必须选择启动盘。这是我们从 CrunchBang 网站下载的 ISO 图像。导航到保存它的位置并选择它。这应该会将您带到 CrunchBang Linux 安装屏幕，如下所示:

![screenshot3](img/05c67defc6022348cd6548743387a1ba.png)

使用箭头键向下移动到“Install”并按“Enter”开始安装过程。

前几个屏幕要求您选择语言、位置和键盘布局。使用箭头键选择适当的选项，然后按“Enter”。接下来，在加载了一些文件之后，你将被要求输入主机名，我只是使用默认的“crunchbang”。然后你需要输入你的个人信息和密码。完成后，系统会询问您如何对磁盘进行分区，选择第一个选项“引导-使用整个磁盘”。点击“继续”,然后选择我们之前设置的虚拟硬盘。在下一个屏幕上，因为这只是在一个虚拟驱动器上，所以最简单的方法是选择第一个选项“所有文件在一个分区中”。如果你在你的实际机器上安装这个，那么我建议选择第二或第三个选项来保持主目录独立。一旦你选择了这个，你会看到一个选项的摘要。单击继续，然后选择“是”进行确认，安装过程将开始。这需要 20-30 分钟，所以你有足够的时间去喝杯咖啡。

在安装快结束时，会询问你是否想使用 GRUB 安装程序。选择“是”并单击“继续”，然后等待安装完成。虚拟机将重新启动，然后#！应该加载完毕。

您可能会得到一个致命的错误，说机器将中止。如果发生这种情况，请选择“忽略”查看错误消息。如果这是“客人正试图切换到 PAE 模式，该模式目前在 VirtualBox 中是默认禁用的”，那么您需要做的就是单击“确定”，然后转到 Virtualbox 中的设置，从左侧菜单中选择“系统”，然后单击“处理器”选项卡并选中标记为“扩展功能 PAE/NX”的框。在此之后，重新启动虚拟机，它应该加载没有任何问题。

当它启动时，你应该看到相当经典的#！登录屏幕如下图所示:

![screenshot4](img/31cfcab7272de78929b50e39cb4d1487.png)

输入您的登录详细信息，您应该会看到以下屏幕:

![screenshot5](img/ffb7791fb26a837f453f3551cd20dc7f.png)

当你第一次登录时，会有一个安装脚本运行，询问你是否要安装诸如 Java 和打印机支持之类的东西。我们在这里做的事情不需要这些，但是你可以随意安装任何你认为有用的东西。如果你想跳过这一步，稍后再回到这一步，只需关闭窗口。您可以随时通过在终端窗口中输入命令`cb-welcome`来运行它。

## 寻找你周围的路#！

现在您已经成功安装了#！，你可能想花些时间四处看看，了解一下它是如何工作的。它使用 Openbox 窗口管理器，这可能需要一点时间来适应，但也是高度可定制的。一切都可以通过右键单击桌面上的任何地方进入主菜单来控制。还有许多快捷键用于执行任务。这些都显示在桌面的右边(比如 super+w 打开默认浏览器 Iceweasel，基本上是 Firefox 的无品牌版本)。

右边的那个东西是 Conky，它主要显示关于你的系统的信息。你可以在那里放很多信息——更多信息见[这个线程](http://crunchbanglinux.org/forums/topic/59/my-conky-config/ "Conky Config")。

！#使用多个桌面，默认使用 2，可以在屏幕顶部看到选项卡。这对于组织打开的不同窗口(例如工作和娱乐)非常有用。

默认的文本编辑器是 Geany，它非常强大。您可以使用 super+e 打开它，或者在桌面上单击鼠标右键，进入“附件”并选择“Geany 文本编辑器”。

![screenshot6](img/72abc8d921868856bb3cf088865f43cf.png)

如果你想安装任何软件，你可以使用 Synaptic Pakcage 管理器。只需右击桌面上的任何地方，进入“系统”,然后从菜单中选择“新立得软件包管理器”。

如果你使用 Dropbox，那么这很容易安装。您需要做的就是打开一个终端窗口并输入以下行:

```
$ wget -O dropbox.tar.gz "http://www.dropbox.com/download/?plat=lnx.x86"
```

这将下载您需要的文件。接下来，您必须使用以下命令提取它:

```
$ tar -xvzf dropbox.tar.gz
```

现在一切都应该设置好了。要进行检查，请运行以下命令:

```
$ ~/.dropbox-dist/dropboxd
```

如果安装成功，那么 Dropbox 应该开始运行，并要求您登录。你还会在右上角看到 Dropbox 图标(你可以点击它打开你的 Dropbox 文件夹)。我们实际上希望每次登录时都这样。这由一个名为“自动启动”的文件控制。我们可以为任何想要在登录时启动的程序添加命令。要编辑此文件，我们只需右键单击，转到“设置”，然后选择“打开框”并选择“编辑自动启动”，现在将以下代码行添加到文件的底部:

```
## Run dropbox
~/.dropbox-dist/dropboxd &
```

现在 Dropbox 应该会在我们每次登录的时候自动启动！

## 安装 Ruby 和 Rails

我们将使用 [Ruby 版本管理器](http://rvm.io)来安装最新版本的 Ruby 和 Rails。这可以在终端的一行中完成。打开一个终端(super+T ),输入下面一行代码:

```
$ curl -L https://get.rvm.io | bash -s stable --autolibs=3 --rails
```

这将启动 Ruby 和 Rails 的安装过程。这可能要花很长时间，所以这可能是一个再喝杯茶的好机会。

安装过程完成后，我们只需通过检查我们拥有的版本来检查一切是否按计划进行:

```
$ ruby -v
ruby 2.0.0p195 (2013-02-24 revision 39474) [i686-linux]
$ rails -v
Rails 3.2.13
```

如果一切按计划进行，您应该会看到与上面列出的版本类似的版本。我们现在需要的是一个数据库服务器！

## 安装 Postgresql

在 JumpStart Rails 中，Postgresql 数据库在本地用于持久化，因此我们接下来将安装它以及 PGadmin 程序。要做到这一点，我们需要使用`apt-get`命令从 Debian 仓库安装软件(Synaptic 只是 apt-get 的一个 GUI 前端)。打开终端并输入以下内容:

```
$ sudo apt-get install postgresql-9.4 pgadmin3
```

`sudo`用于任何需要管理员权限的命令，因此会要求您输入密码来完成安装。

要检查这是否有效，您可以尝试使用以下命令访问默认数据库:

```
$ sudo -u postgres psql template1
```

这使用默认用户“postgres”来访问“template1”数据库。如果一切正常，您应该看到以下内容:

```
template1=#
```

现在只剩下最后一件要安装的东西了！

## 安装 Image Magick

书中的项目也需要 Image Magick 来操作图像，所以我们也将使用 apt-get 安装它，代码如下:

```
$ sudo apt-get install imagemagick libmagickwand-dev
```

## 准备在轨道上滚动

现在您已经准备好进入 Rails 的世界了。我希望你喜欢使用#！将来可能会考虑在更多的项目中使用它。我很想在下面的评论中听到你对它的看法。

## 分享这篇文章