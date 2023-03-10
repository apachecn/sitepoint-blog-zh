# 10 个 Zsh 提示和技巧:配置、定制和使用

> 原文：<https://www.sitepoint.com/zsh-tips-tricks/>

作为 web 开发人员，命令行正成为我们工作流程中越来越重要的一部分。我们用它从 npm 安装软件包，测试 API 端点，将提交推送到 GitHub，等等。

我选择的 shell 是 [zsh](https://en.wikipedia.org/wiki/Z_shell) 。这是一个高度可定制的 Unix shell，它包含了一些非常强大的功能，如杀手级制表符补全、巧妙的历史、远程文件扩展和[等等](https://github.com/hmml/awesome-zsh)。

在本文中，我将向您展示如何安装 zsh，然后提供十个提示和技巧，让您在使用它时更有效率。

这是一个初学者级别的指南，任何人都可以遵循(甚至 Windows 用户，感谢 Linux 的 Windows 子系统)。然而，鉴于苹果宣布 zsh 现在是 macOS Catalina 的标准外壳，Mac 用户可能会发现它特别有用。

让我们开始吧。

## 装置

我不想为每个操作系统提供深入的安装说明，而是提供一些通用指南。如果你在安装 zsh 时遇到困难，网上有很多帮助。

在撰写本文时，当前的 zsh 版本是 5.7.1。

### 马科斯

大多数版本的 macOS 都预装了 zsh。您可以使用命令`zsh --version`检查是否是这种情况，如果是，您运行的是哪个版本。如果版本是 4.3.9 或更高，你应该可以开始了(我们稍后至少需要这个版本来安装 Oh My Zsh)。如果没有，你可以按照这个指南用自制软件安装一个新版本的 zsh。

安装后，您可以使用`chsh -s $(which zsh)`将 zsh 设置为默认 shell。发出这个命令后，您需要注销，然后再次登录以使更改生效。

如果在任何时候您决定不喜欢 zsh，您可以使用:`chsh -s $(which bash)`返回 Bash。

### Linux 操作系统

在基于 Ubuntu 的发行版上，可以使用:`sudo apt-get install zsh`安装 zsh。一旦安装完成，您可以使用`zsh --version`检查版本，然后使用`chsh -s $(which zsh)`将 zsh 设为您的默认 shell。您需要注销，然后重新登录以使更改生效。

与 macOS 一样，您可以使用:`chsh -s $(which bash)`恢复到 Bash。

如果你运行的不是基于 Ubuntu 的发行版，那么查看其他发行版的[说明。](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH#ubuntu-debian--derivatives-windows-10-wsl--native-linux-kernel-with-windows-10-build-1903)

### Windows 操作系统

不幸的是，这就是事情开始变得有点复杂的地方。Zsh 是一个 Unix shell，要让它在 Windows 上工作，你需要激活[Windows Subsystem for Linux(WSL)](https://www.sitepoint.com/wsl2/)，这是 Windows 10 和 11 中运行 Linux 二进制文件的环境。

网上有各种教程解释如何在 Window 10s 中使用 zsh。我发现这两个是最新的，容易跟随:

*   [如何在 Windows 10 上安装和使用 Linux Bash Shell](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/)——首先按照这个安装 WSL
*   [如何在 Windows 10 中使用 zsh(或另一个 Shell)](https://www.howtogeek.com/258518/how-to-use-zsh-or-another-shell-in-windows-10/)——按照这一秒安装 Zsh

注意，也可以让 zsh 与 [Cygwin](https://www.cygwin.com/) 一起运行。这里是[做那件事的说明](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH#cygwin)。

## 首轮放映

当你第一次打开 zsh 时，你会看到下面的菜单。

![Zsh first run menu](img/239c596da051d75cdfaa654134aaaaa0.png)

如果你选择`(1)`，你会被带到一个菜单，允许你配置历史记录、按键绑定和其他一些东西。然而，我建议选择`(2)`，这将创建一个具有推荐默认设置的配置文件。

### 定位配置文件

现在让我们看看 zsh 刚刚创建的文件。进入您的主目录，在您选择的编辑器中打开`.zshrc`文件。这个文件在您启动 zsh 时运行，并且是任何定制配置的所在。

> **注意**:在大多数操作系统中，以点开头的文件名默认是隐藏的，所以你需要在编辑它之前使它可见。如果你不知道怎么做，搜索“显示隐藏的点文件 mac/Linux 等”。

我们可以通过创建一个别名来演示这个文件是如何工作的。最简单的形式是，别名是常规命令的终端快捷方式。将以下内容添加到`.zshrc`的底部并保存文件:

```
alias myip="curl http://ipecho.net/plain; echo" 
```

重启您的 shell(例如，关闭它然后再次打开它)，然后键入`myip`。假设你已经安装了 [cURL 程序](https://en.wikipedia.org/wiki/CURL)，你应该看到你当前的公共 IP 地址输出到终端。如果你想尝试更多的别名，看看 [7 个超级有用的别名，让你的开发生活更容易](https://codeburst.io/7-super-useful-aliases-to-make-your-development-life-easier-fef1ee7f9b73)。

通过键入`exit`关闭终端，然后找到`.zsh_history`文件，它也在您的主目录中。这是 zsh 存储您以前的终端命令列表的地方。目前它应该包含两条线路，即`myip`和`exit`。

> **注意:**在整个教程中，我们将多次重新加载我们的外壳。您也可以使用命令:`source ~/.zshrc`来完成此操作。

### 检查你的进度

至此，您应该已经安装了 zsh，并将其设置为默认 shell。如果您遇到任何问题，请尝试在 [SitePoint 的论坛](https://www.sitepoint.com/community/)上创建一个主题，并在那里寻求帮助。

现在让我们进入一些提示。

## 1.安装哦我的 Zsh

如果你只遵循这些建议中的一条，那就是这条。

Oh My Zsh 是一个社区驱动的框架，用于管理您的 Zsh 配置，捆绑了数千个有用的功能、助手、插件和主题。下面的许多提示都依赖于你安装了这个。

正如项目主页上所解释的，你可以使用 cURL 或 wget 来安装它。

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)" 
```

这假设您已经安装了 git，希望您已经安装了。如果没有，可以[从项目首页](https://git-scm.com/downloads)抓取。


正如你所看到的，在安装后的消息中，Oh My Zsh 创建了你的`.zshrc`文件的备份，然后用它自己的版本替换它。这意味着您需要将任何定制配置(比如我们的`myip`别名)复制到新的`.zshrc`文件中。

## 2.选择一个主题

如果你花了很多时间在终端，值得投资一些努力，使它在视觉上有吸引力。幸运的是，我的 Zsh 附带了一大堆主题。

如果您在`~/.zshrc`文件中查找`ZSH_THEME`变量，您会发现它被设置为`robbyrussel`。这是 Oh My Zsh 的创作者使用的默认主题。

要安装一个不同的主题，在重启你的 shell 之前，从上面的链接中选择一个并改变变量`ZSH_THEME`的值。例如，设置`ZSH_THEME="avit"`将导致 Oh My Zsh 使用 [avit 主题](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#avit)。

您可能还想改变终端的配色方案。在我的 Linux 机器上，我可以通过*编辑* > *偏好* > *颜色*来做到这一点。去掉*中的勾号，使用系统主题*中的颜色，然后选择*日晒深色*作为方案，很快就给了我一个非常吸引人的结果。

![Avit theme on Oh My Zsh](img/1759c977c0e0ff82e4544ba46d9e0b3b.png)

注意主题如何识别它在 git repo 中，告诉我我在哪个分支上，并显示一个绿色勾号让我知道一切都是最新的。

我鼓励你浏览现有的主题，找到一个适合你的。

### 解决纷争

当我在 WSL 上测试这个的时候，我一安装 Oh My Zsh，就在我的终端上看到了乱码。为了解决这个问题，我必须安装[电力线字体](https://github.com/powerline/fonts)，并告诉我的终端使用它们。

```
git clone https://github.com/powerline/fonts.git 
```

然后打开管理 PowerShell，导航到 repo 的根目录并运行:

```
.\install.ps1 
```

如果 PowerShell 阻止您运行脚本，那么尝试按照这个 [StackOverflow 答案](https://stackoverflow.com/a/4038991)设置`ExecutionPolicy`。一旦安装了字体，一定要重新设置。

最后，右击终端的标题栏，选择*属性* > *字体*，并确保选择了电力线字体。在测试中，*的《似曾相识》对我来说效果很好。*

还要注意的是，一些主题需要电力线字体，与操作系统无关。我们将在文章的最后安装其中一个主题( [Agnoster](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#agnoster) )。

## 3.利用历史

我最喜欢的“哦，我的 Zsh”功能之一是它可以让你搜索你的历史。想象一下`.zsh_history`的内容是这样的:

```
cd Desktop
mkdir my-project
cd my-project
npm init -y
mkdir {src,build}
touch index.js 
```

如果你在终端中按下`↑`键，你会期望一个接一个地循环浏览这些历史条目，对吗？这并不奇怪。

但是，Oh My Zsh 允许您输入一个命令的开头，然后按下`↑`键来循环匹配的条目。因此，在上面的例子中，如果您输入“mkdir”然后按下`↑`键，您将看到显示“mkdir {src，build}”。如果你再次按下它，你会看到“mkdir my-project”。

我一直在使用这个功能。例如，当我想通过 ssh 连接到一个服务器，但不记得确切的凭证时，我只需输入“ssh ”,就可以在最近的连接中循环，直到找到正确的连接。或者，当我不记得命令的确切语法时，我可以只输入前几个字符，然后循环查找任何匹配项。

## 4.自动暗示 FTW！

Oh My Zsh 的另一大特色是*插件*。这些为您的 shell 添加了新的特性，增强了它的功能。

哦，我的 Zsh 附带了数百个插件,用于各种可能的用途，我们将在本文后面看到其中的一些。然而在这一节，我想向你介绍一个叫做[自动建议](https://github.com/zsh-users/zsh-autosuggestions)的第三方插件。这将在您键入命令时根据历史记录给出建议。

要安装，您需要将其存储库克隆到`$ZSH_CUSTOM/plugins`中。默认情况下，这是`~/.oh-my-zsh/custom/plugins`。

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions 
```

一旦完成，要激活它，跳转到`~/.zshrc`文件并取消对下面一行的注释:

```
# plugins=(git) 
```

然后用`zsh-autosuggestions`替换`git`

```
plugins=(zsh-autosuggestions) 
```

重启你的 shell，你会发现当你输入一个命令时，我的 Zsh 会根据你的历史给出建议。如果建议是您正在寻找的建议，请按`→`键接受它。一个真正的时间节省者！

![Autosuggestions with Oh My Zsh](img/5a90f8454e4cf38627a8dbca109dac52.png)

## 5.文件导航与哦我的 Zsh

Oh My Zsh 的另一个很棒的地方是它让你在你的机器上导航变得很容易。

例如，当你想改变目录时，不需要输入`cd`命令，即`cd Desktop`就变成了`Desktop`。这也适用于路径——你可以输入`/home/jim/Desktop`,到达你想去的地方。

哦，我的 Zsh 也有很好的自动完成功能。一旦你开始输入一个路径，你可以按下`TAB`让它展开一个你可以导航到的所有可能文件夹的列表。如果继续使用 tab 键，您可以在列表中移动，直到通过按下`Return`选择一个文件夹。之后再次按下`TAB`将重新开始该过程。

![Oh My Zsh file navigation with TAB completion](img/a92406a8d6ec893d42c561906510de82.png)

最后，值得一提的是，Oh My Zsh 提供了许多用于操作和导航目录的[命令和别名。例如，键入`...`将使您上移两个目录，键入`take <dir-name>`将创建一个新目录并导航到该目录。哦，我的 Zsh 还支持动态路径完成，所以输入(例如)`/h/j/De`并按下`Tab`会将路径扩展到`/home/jim/Desktop`。酷吧。](https://github.com/robbyrussell/oh-my-zsh/wiki/Cheatsheet#commands)

> **额外提示**:如果你觉得很懒，你只需将一个文件夹拖放到终端窗口，路径就会为你展开。

## 6.使用 z 导航到任何内容

z 是一个方便的插件，它建立了一个你最常用和最近文件夹的列表(它称之为“frecent”)，并允许你通过一个命令跳转到它们。这通常比在嵌套的文件夹结构中切换要方便得多。

要安装这个插件，你所要做的就是把它添加到你的`.zshrc`文件中，就像这样:

```
plugins=(z zsh-autosuggestions) 
```

然后重启你的终端。

一旦安装，z 将有一个短暂的学习阶段，因为它观察你用终端在你的电脑上导航。然而，过一会儿，你将能够键入`z`，后跟你想要的目录路径中的任何单词。该插件将使用模糊匹配来找出你想去哪个文件夹，并相应地扩展路径。如果有多种可能性，您可以像上一篇技巧文章中描述的那样在选项中切换。

这听起来没什么大不了的，但是您将能够打开一个 shell，键入`z my-project`并让它扩展到`/home/jim/files/some/deeply/nested/directory/or/other/my-project`的路径。然后当你在那个目录中时，你可以输入`z my-other-project`，让它展开路径到`/var/www/html/projects/top/secret/my-other.project`。

> **注:**第一次运行 z，并试图改变目录，得到错误
> `_z_dirs:2: no such file or directory: /home/jim/.z`。这是一次性的，因为 z 显然需要在我的主目录中创建一个`.z`文件。如果你遇到任何问题，[这篇文章有一些建议](https://www.smashingmagazine.com/2015/07/become-command-line-power-user-oh-my-zsh-z/#using-z-to-jump-to-frecent-folders)。

## 7.使用 git

Zsh 包括一些基本的 git 集成。例如，如果您键入`git`并按下`Tab`键，zsh 会给出一长串可能的 git 命令。如果您键入`git`，后面跟着您要查找的命令的前几个字母，这会变得稍微有用一些。例如`git a` + `Tab`生产:

```
add        -- add file contents to index
am         -- apply patches from a mailbox
apply      -- apply patch to files and/or to index
archimport -- import an Arch repository into git
archive    -- create archive of files from named tree 
```

当你安装了 [Oh My Zsh git 插件](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/git/)时，事情开始变得更有趣了。

```
plugins=(git z zsh-autosuggestions) 
```

这提供了一大堆别名，以及一些使用 git 的有用函数。我经常使用的一些方法是:

*   `gaa`–`git add all`
*   `gdca`–`git diff --cached`
*   `gp`–`git push`
*   `gpf!`–`git push --force`
*   `grhh`–`git reset --hard`
*   `gst`–`git status`

您也可以通过将以下内容添加到您的`.zshrc`文件中，一次为几个命令创建一个别名:

```
function acp() {
  git add .
  git commit -m "$1"
  git push
} 
```

现在，当您在 git repo 中时，键入`acp "a commit message"`将添加所有更改的文件，用您指定的任何提交消息提交它们，然后将它们推送到您的遥控器。这个想法归功于阿里·斯皮特尔。

## 8.获得关于 git 回购状态的即时反馈

在文章的顶部，我们激活了一个名为 [avit](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#avit) 的主题。在 git repo 中，该主题将显示分支名称，以及一个叉号或勾号，指示分支的所有内容是否都是最新的。我喜欢能够一目了然地看到这些信息，这无疑是从不得不一直键入`git status`中迈出的一大步。

但是，有几个主题以更直观的方式显示了这些信息。我最喜欢的是 [Agnoster](https://github.com/agnoster/agnoster-zsh-theme) 。让我们现在安装它。

为了让 Agnoster 正确渲染箭头和 git 图标，你可能需要安装一个[电力线补丁字体](https://github.com/Lokaltog/powerline-fonts)。WSL 用户可以按照上述[故障排除部分](#troubleshooting)中的说明进行操作。对于 Mac 和 Linux 用户，[的安装说明可以在项目的 repo](https://github.com/powerline/fonts#installation) 中找到。

High Sierra 或更高版本的 Mac 用户可能需要执行一些额外的工作才能显示这些字体。除非你是卡特琳娜，[这个方法推荐](https://github.com/gabrielelana/awesome-terminal-fonts/wiki/OS-X)。卡特琳娜用户需要安装一个[补丁字体](https://github.com/ryanoasis/nerd-fonts)，并在他们的终端设置中选择它。

测试您的终端是否可以显示所有必要的字符。运行以下命令:

```
echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699" 
```

结果应该是这样的:

![Powerline fonts required for the Agnoster theme](img/69312ddd6028fe3ad5ab253bc6e8872a.png)

之后，通过更改您的`.zshrc`文件中的以下行来启用主题:

```
ZSH_THEME="agnoster" 
```

以及重启终端。一旦 Agnoster 启动并运行，您将对任何 git 存储库的状态有一个更直观的了解。您还将收到关于上一个命令是成功还是失败的视觉反馈，以及您是否正在使用提升的权限工作。

![Up and running with the Agnoster theme](img/3bed5f2c6b0624a4316911b604869738.png)

> **提示**如果你不喜欢提示的`user@host`部分，你可以通过在`.zshrc`文件中添加`DEFAULT_USER=your_user_name`使其消失。

## 9.插件，插件，插件

如前所述， [Oh My Zsh 搭载了*很多*的插件](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins)。你真的应该看看这些，花些时间学习那些对你的工作流程有帮助的东西。为了让你开始，这里有一些流行的要注意的:

*   [cloudapp](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#cloudapp)–上传文件和管道内容到 cloud app 服务。将结果 URL 输出并复制到剪贴板。
*   [command-not-found](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#command-not-found)–如果某个命令在`$PATH`中未被识别，它将使用 Ubuntu 的 command-not-found 包来查找该命令或提示拼写错误(仅适用于 Ubuntu 和 openSUSE)。
*   [节点](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#node)–在浏览器中打开当前版本的节点 API。
*   [npm](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#npm)–为 NPM 添加自动完成功能。
*   [sudo](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#sudo)–按两次`ESC`将 sudo 放在当前命令的前面，如果命令行为空，则放在最后一个命令的前面。
*   [vs code](https://github.com/robbyrussell/oh-my-zsh/wiki/plugins#vscode)–使命令行和代码编辑器之间的交互更加容易。

## 10.组合键

这不是一个 zsh 特有的技巧，但是总结一下，我想让你知道这五个节省时间的组合键。

*   `Ctrl`+`U`–从光标到行首删除。
*   `Ctrl`+`K`–从光标到行尾删除。
*   `Ctrl`+`W`–从光标处开始删除前一个单词。
*   `Alt`+`D`–从光标处删除到下一个单词的末尾。
*   `Ctrl`+`L`–清除终端。

如果你能记住这些，你会惊讶地发现它们经常派上用场。

## 结论

在这篇文章中，我演示了如何安装 zsh 并将其设置为默认 shell。我还展示了如何使用 Oh My Zsh 框架充分利用您的新终端。我希望，如果你花几周时间尝试一下这些，你会想知道没有它们你是怎么过的。

别忘了，如果你对此有任何问题，你可以跳到 [SitePoint 论坛](https://www.sitepoint.com/community/)寻求帮助。如果我错过了什么，我也会很高兴听到——例如，你最喜欢的主题、插件、别名或生产力技巧。

你可以在论坛或推特上联系我。

## 分享这篇文章