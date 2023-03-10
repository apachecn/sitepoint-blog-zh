# 使用 nvm 安装 Node.js 的多个版本

> 原文：<https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/>

使用 Node.js 时，您可能会遇到需要安装多个运行时版本的情况。

例如，也许您的机器上安装了最新版本的 Node，但是您将要开始工作的项目需要一个旧版本。或者，您可能正在将一个旧的 Node 项目升级到一个更现代的版本，当您进行转换时，能够在这两个版本之间切换会很方便。

如果没有好的工具，这将意味着花费大量的时间和精力来手动卸载和重新安装节点版本及其全局包。还好有更好的办法！

## nvm 简介

[nvm](https://github.com/creationix/nvm) 代表节点版本管理器。顾名思义，它可以帮助您轻松管理不同的节点版本并在它们之间切换。它提供了一个命令行界面，你可以用一个命令安装不同的版本，设置默认值，在它们之间切换等等。

## 操作系统支持

nvm 同时支持 Linux 和 macOS，但这并不是说 Windows 用户就一定要错过。还有一个名为 [nvm-windows](https://github.com/coreybutler/nvm-windows) 的项目，为 windows 用户提供了轻松管理节点环境的选项。尽管名字如此，nvm-windows 并不是 nvm 的克隆，也不隶属于它。但是，下面列出的基本命令(用于安装、列出和在版本之间切换)应该适用于 nvm 和 nvm-windows。

## 装置

让我们首先介绍 Windows、macOS 和 Linux 的安装。

### Windows 操作系统

首先，我们需要做一点准备:

*   卸载 Node.js 的任何现有版本
*   删除任何现有的 Node.js 安装目录(如`C:\Program Files\nodejs`)
*   删除现有的 npm 安装位置(如`C:\Users\<user>\AppData\Roaming\npm`)

在这之后，下载并运行[最新的稳定安装程序](https://github.com/coreybutler/nvm-windows/releases)，你就可以开始了！

### macOS/Linux

与 Windows 不同，在 macOS 和 Linux 中删除以前的节点和 npm 安装是可选的。如果这是你想做的事情，网上有很多好的资源。例如，下面是如何在 macOS 和 Linux 上移除节点。以下是你如何[移除你可能有的任何先前的 npm 安装](https://docs.npmjs.com/misc/removing-npm)。

您可以使用 cURL 或 Wget 安装 nvm。在您的终端上，运行以下命令:

带[卷曲](https://curl.haxx.se/):

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.35.2/install.sh | bash 
```

或者用 [Wget](https://www.gnu.org/software/wget/) :

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.35.2/install.sh | bash 
```

请注意，版本号(`v0.35.2`)会随着项目的发展而改变，因此值得查看项目主页的[相关部分以找到最新版本。](https://github.com/creationix/nvm#install-script)

这将把 nvm 存储库克隆到`~/.nvm`，并对 bash 配置文件进行必要的修改，这样`nvm`就可以在终端的任何地方使用了。

就是这样！重新加载(或重启)您的终端，nvm 就可以使用了。

## 使用 nvm

如果安装正确，`nvm`命令在终端的任何地方都可用。让我们看看如何使用它来管理 Node.js 版本。

### 安装多个版本的 Node.js

nvm 最重要的部分之一当然是安装不同版本的 Node.js，为此，nvm 提供了`nvm install`命令。您可以通过运行此命令，后跟您想要的版本来安装特定的版本。例如:

```
nvm install 12.14.1 
```

通过在终端中运行上述程序，nvm 将安装 Node.js 版本`12.14.1`。

*提示:nvm-windows 用户在安装后必须运行`nvm use 12.14.1`。*

nvm 遵循 [SemVer](http://semver.org/) ，所以如果你想安装，例如，最新的`12.14`补丁，你可以通过运行:

```
nvm install 12.14 
```

nvm 随后将安装 Node.js 版本`12.14.X`，其中`X`是可用的最高版本。在撰写本文时，这个值是 1，所以您的系统上已经安装了`12.14.1`版本。

您可以通过运行以下命令来查看可用版本的完整列表:

```
nvm ls-remote 
```

对于 nvm-windows，这是:

```
nvm ls available 
```

*提示:列出所有可用的节点版本会产生大量输出。Linux 用户可能想把它通过管道传输到他们想要的版本。比如`nvm ls-remote | less`，或者`nvm ls-remote | grep v12`。*

### npm

安装 Node.js 实例时，nvm 也会安装兼容的 [npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 版本。每个节点版本可能会带来不同的 npm 版本，您可以运行`npm -v`来检查您当前使用的是哪个版本。全局安装的 npm 软件包不会在不同的 Node.js 版本之间共享，因为这可能会导致不兼容。相反，它们安装在当前版本的节点旁边。这有一个额外的好处，用户不需要`sudo`特权来安装全局软件包。

幸运的是，在安装新的 Node.js 版本时，您可以从特定版本重新安装 npm 全局软件包。例如:

```
nvm install v12.14.1 --reinstall-packages-from=10.18.1 
```

通过运行上述程序，nvm 将安装 Node.js 版本`12.14.1`，即相应的 npm 版本，并重新安装您为`10.18.1`版本安装的全局 npm 软件包。

如果您不确定最新版本是什么，您可以使用`node`别名:

```
nvm install node 
```

这将当前拉入版本`13.6.0`。

或者您可以安装最新的 [LTS 版本](https://en.wikipedia.org/wiki/Long-term_support)，使用:

```
nvm install --lts 
```

这将当前拉入版本`12.14.1`。

您还可以通过运行以下命令卸载任何您认为不再有用的实例:

```
nvm uninstall 13.6.0 
```

### 在版本之间切换

到目前为止，我们已经看到了如何安装不同的节点版本。现在我们来看看如何在它们之间切换。让我首先指出，当一个新版本被安装，它会自动投入使用。因此，如果您安装了最新的 Node.js 版本，然后立即运行`node -v`，您将看到最新版本的输出。

为了切换已安装的版本，nvm 提供了`nvm use`命令。这类似于 install 命令。因此，您需要在它后面加上一个版本号或别名。

切换到 Node.js 版本`13.6.0`:

```
nvm use 13.6.0 
```

切换到 Node.js 版本`12.14.1`:

```
nvm use 12.14.1 
```

切换到最新的 Node.js 版本:

```
nvm use node 
```

切换到最新的 LTS 版本:

```
nvm use --lts 
```

当切换到不同的版本时，nvm 将使终端符号中的`node`实例链接到正确的 Node.js 实例。

### 自定义别名

除了 nvm 自带的别名之外，您还可以创建自定义别名。例如，通过运行:

```
nvm alias awesome-version 13.6.0 
```

您正在为 Node.js 版本`13.6.0`设置一个名为“awesome-version”的别名。所以，如果你现在跑:

```
nvm use awesome-version 
```

nvm 会将`node`切换到版本`13.6.0`。您可以通过运行以下命令来删除别名:

```
nvm unalias awesome-version 
```

您还可以通过将某个版本定位到“默认”别名来设置在任何 shell 中使用的默认实例，如下所示:

```
nvm alias default 12.14.1 
```

### 列出已安装的实例

您可以随时通过运行以下命令来检查您安装了哪些版本:

```
nvm ls 
```

这将显示类似如下的内容:

![nvm versions list](img/d554b6e96a6ca495263365fcb3ab0ae4.png)

左侧带箭头的绿色条目是正在使用的当前版本。在已安装的版本下面，有一个可用别名的列表。现在尝试执行以下命令:

```
nvm use node
nvm ls 
```

它将显示如下:

![nvm use and versions list](img/2677b10ce95592c8db5ec1dd30035075.png)

您还可以使用以下命令检查当前使用的版本:

```
nvm current 
```

### 基于每个项目指定一个节点版本

像 [rbenv](https://github.com/rbenv/rbenv) 这样的版本管理器允许你在每个项目的基础上指定一个 Ruby 版本(通过把那个版本写到你当前目录下的一个`.ruby-version`文件中)。这在 nvm 中是可能的，如果你在一个项目中创建一个`.nvmrc`文件并指定一个版本号，你可以 cd 进入项目目录并输入`nvm use`。然后，nvm 将读取`.nvmrc`文件的内容，并使用您指定的任何版本的节点。

如果这种自动发生对你来说很重要，那么在项目主页上有几个片段供你添加到你的`.bashrc`或`.zshrc`文件中来实现这一点。

这是 ZSH 的片段。将它放在您的 nvm 配置下面:

```
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc 
```

现在，当您切换到一个带有`.nvmrc`文件的目录时，您的 shell 将自动更改节点版本。

![Automatically apply nvm use](img/8d87f461cc9609c512ad31677d8a54cd.png)

### 其他 nvm 命令

nvm 提供了一些更高级或不常用的命令。

您可以直接运行已安装版本的命令，而无需切换`node`变量:

```
nvm run 13.6.0 --version 
```

您可以针对特定版本在子 shell 上运行命令:

```
nvm exec 13.6.0 node --version 
```

您可以获取特定安装版本的 Node.js 可执行文件的路径:

```
nvm which 13.6.0 
```

当配置一个文本编辑器插件需要知道你当前的 Node 版本在哪里时，这可能是有用的。

## 结论

nvm 对于任何 Node.js 开发者来说都是一个很棒的工具。它支持无忧安装和不同版本之间的轻松切换，为真正重要的事情节省时间。

感谢 nvm 的创造者[蒂姆·卡斯维尔](https://github.com/creationix)，感谢 nvm 对 Windows 的支持[科里·巴特勒](https://github.com/coreybutler)，当然还有那些为这些伟大项目做出贡献的人们。Node.js 社区非常赞赏您的工作。

你呢？你用 nvm 吗？你的体验如何？如果你想聊天，可以在 Twitter 上点击迈克尔或 T2 或詹姆斯。

## 分享这篇文章