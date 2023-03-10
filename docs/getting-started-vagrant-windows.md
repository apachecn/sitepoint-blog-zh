# Windows 上的流浪者入门

> 原文：<https://www.sitepoint.com/getting-started-vagrant-windows/>

![setting up a vm on the fly](img/a8bcae4704599121106fda571681cfbb.png)

*这篇热门文章在 2017 年全面更新。更改包括公钥信息、故障排除提示以及 Windows 10 和其他相关软件的更新。*

在 Mac、Windows 和 Linux 操作系统上，vagger 已经迅速成为无处不在的本地开发工具。

通过一组可重复使用的配置文件，帮助您动态创建虚拟机。开发人员可以通过 GitHub 和其他地方共享他们的配置和脚本，这样其他开发人员就可以构建相同的环境和工具。

如果您想为测试程序启动服务器，学习如何使用 Linux 工具，或者在生产系统上应用更改之前在测试环境中工作，这是一个很好的工具。想在 Ubuntu 上从头开始学习安装 PHP/Apache/MySQL 栈吗？还是在 Apache 前面玩设置 Varnish 这样的缓存服务器？在 Nginx 一试身手？学 PHP 7？与单独使用 VirtualBox 相比，流浪者使事情变得简单了许多。

![Vagrant logo](img/b04d7932371b0c4c63c67a714fcd8184.png)

让我们来看看如何在 Windows 环境中设置流浪者。

## 安装兆位和部件

要开始使用，请安装这些核心工具:

*   [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (创建虚拟机的软件)
*   [流浪者](https://www.vagrantup.com/)(我们的英雄，部署虚拟机和运行配置脚本的软件)
*   [PuTTY 和 PuTTYGen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (SSH 客户端和安全密钥生成器)

VirtualBox 和流浪汉安装像任何其他 Windows 程序。流浪者将安装自己到你的全球路径，所以你可以从任何地方运行它。

### 创建您的项目

现在您已经设置好了，您可以通过创建一个项目文件夹来启动您的第一个流浪者项目，该项目文件夹将包含您的每个虚拟机的各种配置。您将使用命令行从这些文件夹的中的*运行漫游命令。*

创建一个项目文件夹，我用的是`E:\Vagrant\sitepoint`。

> 提示:作为 Windows 用户，您可以通过按住 shift 键并右键单击文件夹或文件夹中的空白区域，然后选择“在此打开命令窗口”，从资源管理器中快速打开命令提示符。

## 使用流浪者

开始使用的主要命令是`vagrant init`和`vagrant up`。

打开命令行，将目录更改为测试项目文件夹。运行`vagrant init`,在当前目录下会创建一个新的名为“vagger file ”(无扩展名)的文件，其中包含一个基本的启动程序配置。

在 Notepad++或 Sublime 这样的文本编辑器中打开 Vagrantfile，查看一下，研究一下每个配置值前的注释。您可以设置来宾和主机之间的共享文件夹、打开端口转发、设置主机名等等。请注意，除了一行之外，所有的行都被注释掉了:即使这里没有更改任何设置，Vagrant 也将使用默认配置。

这个浮动文件还没有指向任何类型的虚拟机，所以继续操作并**删除您刚刚创建的浮动文件**,让我们找到一个“基础箱”来使用。

浏览[流浪云](https://atlas.hashicorp.com/boxes/search)寻找你可能会用到的基本盒子。对于这个例子，我们将使用“ubuntu/trusty64 ”,这是 Ubuntu Server 14.04 LTS(Trusty Tahr)的“官方”基础盒。要让 vagger 自动使用此基本框，请键入以下内容:

```
vagrant init ubuntu/trusty64 
```

这一次，如果您打开 lavong file，您会注意到它列出了“config.vm.box”的基本框。其他设置仍然被注释掉。

![Vagrant init](img/5fee486e86e26d1575ad0a977603f675.png)

现在您已经有了一个配置了基本盒的浮动文件，您可以使用这个惊人的命令启动 VM:

```
vagrant up 
```

![Vagrant up](img/7f860ef35980ed06c111a32b2bc6f3cc.png)

为了解释这里发生的事情，如果你还没有基础盒(我已经有了，否则它会先下载)，那么流浪者首先导入基础盒。然后它会检查您的盒子是否是最新的。

> 注意:这些基础框位于您的`%userprofile%/.vagrant.d/boxes`文件夹中。您可以通过键入`vagrant box list`列出所有已安装的盒子。可以用`vagrant box remove box/name`删除方框。

您可以看到它将 SSH 转发端口设置为“2222”:您将需要它来使用像 PuTTY 这样的 SSH 客户端。

注意用户名是“流浪者”，它将永远是。你还会注意到它生成了一组新的密钥，这是在以前的版本中没有的。公钥会自动复制到虚拟机中，并替换其中的默认密钥。它还将私钥放在一个名为。流浪”在你的项目文件夹里。如果您深入到该文件夹，您会发现“private_key ”,稍后在 PuTTY 中使用它会很有用。

接下来，它会检查来宾操作系统添加的内容，并装载主机和来宾操作系统之间的共享文件夹。这可以根据您的意愿进行更改，或者添加额外的共享文件夹。第一个文件夹“/vagger”在虚拟机上；它将共享到我的本地项目文件夹。当您工作时，文件将在文件夹之间保持同步。

## 现在我可以访问我的服务器了，对吧？

是啊！算是吧！

你看，流浪汉*有了*就可以登录新的服务器，进行额外的调配，比如设置配置好的 IP，端口转发，文件夹共享，VirtualBox 来宾添加等。为了做到这一点，所有的 base boxes 都需要已经安装了 have 公钥，以及一个名为“have”的用户，该用户具有无密码的 sudo 特权。

一旦创建了 VM，vagger 将提供一些输出，您可以扫描这些输出来查找错误。见前面截图。大多数基本盒安装不会有问题，但是高级脚本配置有时会遇到麻烦，并且在配置过程中会有数百行文本输出。

我们现在面临的问题是 Windows 没有附带 SSH 命令行客户端。这意味着，对于我们这些 Windows 用户来说，流浪者的内置 SSH 功能不一定管用。但这就是为什么我们有油灰。键入以下内容，以便在需要时获取您的 SSH 信息:

```
vagrant ssh-config 
```

它会告诉您要使用的 IP 和 SSH 端口以及其他一些信息，以防您忘记。

> 尽管许多基础机器都是准系统操作系统，但它们确实需要安装一个 SSH 服务器，这也是被 vagger 使用的要求之一。许多基础机器除了安装了 OpenSSH 之外什么也没有安装。

打开 PuTTY，输入 IP 和端口，为连接命名，然后保存它。现在，您可以使用 PuTTY 通过 SSH 连接到新服务器。用户名是“流浪者”,密码也应该是“流浪者”,但在特殊情况下，这可以个性化到基本框。

![Vagrant with PuTTY](img/b4733d6f3ab02c828f0090cfb43cb817.png)

如果使用了默认的 2222 端口，taken 将自动选择另一个端口。您的所有虚拟机都将获得一个新端口，因此请务必注意哪个虚拟机是哪个虚拟机！

> 旁注:你可以安装一个 Windows SSH 客户端，比如 [Cygwin](https://www.cygwin.com/) 或者 [MinGW](http://www.mingw.org/) 。甚至 [Git](https://git-scm.com/downloads) 也带有 SSH 客户端。键入`vagrant ssh`看看会发生什么！Linux 和 Mac 用户应该已经有了 SSH 客户端。如果你没有客户，流浪者会给你看一些选项。

## 使用公钥和私钥

您可以使用新创建的私钥 vacator，因为该私钥存储在您的本地机器上。但是，PuTTY 不能使用 OpenSSH 样式密钥。这就是我们下载 PuTTYGen 的原因，现在就打开吧。

点击“加载”并浏览到您的项目文件夹，一直到`**\\.vagrant\machines\default\virtualbox**`。切换到查看“所有文件”，选择“private_key”，点击打开。

您可以立即单击“保存私钥”按钮，选择“是”保存，无需密码。在同一文件夹中，将其命名为类似于“private_key_putty”的名称。这将创建一个扩展名为`.PPK`的文件。关闭 PuTTYGen，现在回到 PuTTY。

![Save private key](img/079e77e498a8e9a57f2d3cd5d9d53558.png)

在 PuTTY 中，将您的连接加载到新服务器，如果没有保存，请再次键入。然后进入侧边栏的*连接- > SSH- > Auth* 点击“浏览”找到私钥。您将看到 PPK 版本，但不会看到另一个版本，因为 PuTTY 不使用该类型。现在，请确保保存您的配置，以便您可以在以后继续使用它。

![Private key in PuTTY](img/a926c41de0b93bbda04b93ba70269c58.png)

完成后，下一次你打开这个连接并以用户身份输入“流浪者”,它将自动使用密钥进行验证，你将无需输入密码即可登录。

您可以更进一步，让 PuTTY 自动输入用户名。在你的 PuTTY 配置中进入*连接>数据*，并在“自动登录用户名”字段中输入“流浪者”。现在，当您打开保存的连接时，PuTTY 会自动键入用户名，并使用私钥为您登录！

## 附加注释

流浪者文件是所有配置开始的地方。该文件旨在由版本控制系统管理，并且是您与团队共享开发设置的方式。如果每个人都使用相同的流浪者文件，他们将得到相同的虚拟机设置，具有相同的盒子和供应。请注意，脚本可以在这个文件外部调用，这意味着一些复杂的浮动项目可能包含许多文件和文件夹。

你可以使用 VirtualBox 来查看你创建的任何虚拟机，但你实际上根本不需要打开或使用 VirtualBox 来使用 vagger。如果您从 VirtualBox 对虚拟机进行某些更改，则有可能会失去与虚拟机的关联。

在你的项目文件夹和 VM 中的一个文件夹之间创建一个共享文件夹，默认情况下这个文件夹是`/vagrant`。通常机器被用作开发机器，所以一个共享的公共文件夹是 Apache 或 Nginx www 或 public_html 文件夹。

> 注意:流浪者自动转发端口 22，所以你可以进入，但它不会自动转发一个 web 端口，如 80，以防你做 web 开发工作。您必须打开您的流浪者文件，并取消注释转发端口 80 的行，以启用它。只需阅读文件中的说明。编辑之后，你会想要做一个`vagrant reload`。

您可以使用`vagrant halt`关闭 VM，或者使用`vagrant suspend`暂停它。然后随时用`vagrant up`再次打开。键入`vagrant status`查看虚拟机的当前状态。

所有的命令都可以在这里找到[。](https://www.vagrantup.com/docs/cli/index.html)

> 提示:查看[流浪者管理器](http://vagrantmanager.com/windows/)的可视化系统托盘工具来管理您的本地虚拟机。也适用于 Mac 用户。

## 附加安全性

因为任何人都可以创建基本盒，所以流浪者规则规定他们应该创建一个用户和密码流浪者/流浪者。它还声明它不期望 root 用户有任何密码—也就是说，它不使用 root 或期望它有密码。如果您觉得有必要，了解您选择安装的机器上的默认用户和密码是很好的做法。

这些规则还规定，travel 需要在 travel 用户上使用无密码的 sudo，这样它就可以执行设置，所以如果您想提高安全性，应该注意这一点。

vagger 将 SSH 端口设置为绑定到本地主机，因此它不会接受来自“外部”的连接。如果您想从其他地方的其他主机访问您的虚拟机，这可以更改。为此，您需要额外的配置。

## 潜在的问题

根据反馈，使用流浪者有一些问题。简而言之，如果您有问题，可以考虑以下几点:

*   确保流浪者可以通过 https 与安全网站通信。唯一可能的障碍是防火墙或有限的用户账户。
*   您电脑的 BIOS 可能有启用虚拟化的设置，必须打开。你必须找到你的主板手册，看看这些设置在哪里。许多年来，大多数现代计算机都将默认设置为启用虚拟化。
*   我个人最近遇到了一个问题，即最新版本的 VirtualBox 不能与最新的流浪者一起工作。我必须安装以前版本的 VirtualBox。如果其他版本都不起作用，您可以在必要时尝试以前的版本。
*   如果 vagger 无法与 Atlas for base boxes 通信，您可能需要 [Microsoft Visual C++ 2010 可再发行包](https://www.microsoft.com/en-us/download/confirmation.aspx?id=5555)。这个问题比较老，现在可能已经被修复了，但是你可以在 GitHub [这里](https://github.com/mitchellh/vagrant/issues/6764)读到它。
*   高级 Linux 文件系统属性可能无法正常工作，例如符号链接和某些权限。共享文件夹和长文件路径(如 NPM 创建的)有问题，但这个问题在较新版本的流浪者中得到解决。在此阅读相关内容[。](https://github.com/mitchellh/vagrant/pull/5495)

## 下一步？

现在你已经知道了流浪者是如何工作的，你会想要更进一步。你可以在流浪云之外找到额外的盒子，比如[流浪盒. es](http://www.vagrantbox.es) 。使用另一个来源的框就像输入`vagrant init box/name url`一样简单，这里会为您提供“框/名称”和“URL”。

乔治费科特讲述了如何创建自己的基础盒子。

你甚至可以使用 [DigitalOcean 作为提供者](https://www.digitalocean.com/community/tutorials/how-to-use-digitalocean-as-your-provider-in-vagrant-on-an-ubuntu-12-10-vps)，使用 vagger 部署水滴。

像 [PuPHPeT](https://puphpet.com/) 和 [Protobox](http://getprotobox.com/) 这样的自动化工具可以帮助您创建用于部署更多全功能开发虚拟机的配置脚本。

您可能听说过流行的流浪者盒子 [Laravel Homestead](https://laravel.com/docs/5.3/homestead) ，它可以用于为 Laravel 项目部署开发服务器。SitePoint 的布鲁诺·什科沃茨在 Homestead 的基础上创建了他所谓的 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) ，并为 web 开发提供了一些替代配置。此框经常在 SitePoint 上的教程中使用。

请阅读 Bruno 的[重新介绍 vacant](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php)文章，了解关于 web 开发工具和使用 vacant 的 LAMP 堆栈的更多信息。

有一个叫做 [VVV](https://github.com/Varying-Vagrant-Vagrants/VVV) 的盒子，它不仅为 WordPress 开发者创建了一个开发盒子，而且实际上设置了它，所以你可以在一个虚拟机上托管多个 WordPress 站点。Homestead 也做同样的事情，所以你不用为你的每个项目使用一个完整的 VM。亚历山大·科科[也写了 VVV](https://www.sitepoint.com/wordpress-meets-vagrant-vvv/) 。

天空是无限的，所以赶快行动起来，四处寻找酷酷的盒子、配置脚本，或者创建自己的脚本！

我希望这能帮助你开始！请在评论中告诉我们你是否最终采取了行动，以及它是否对你有用。

## 分享这篇文章