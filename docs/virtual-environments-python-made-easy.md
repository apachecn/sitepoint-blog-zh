# Python 中的虚拟环境变得简单

> 原文：<https://www.sitepoint.com/virtual-environments-python-made-easy/>

大多数 Python 新手不知道如何建立一个遵循专业程序员使用的最新标准的开发环境。本教程将教您如何使用行业公认的最佳实践来创建一个完全正常工作的 Python 开发环境。

## 虚拟环境

虚拟环境通过创建隔离的环境来帮助我们解决项目依赖性冲突。这些“隔离环境”包含了 Python 程序员开发项目可能需要的所有好东西。

虚拟环境包括 Python 二进制文件的全新副本，以及整个 Python 标准库的独立副本。这就是它能自己工作的原因。

使用虚拟环境给我们带来了以下优势:

*   我们能够保持我们的本地机器包完好无损
*   我们可以用一个`requirements.txt`文件与其他人共享依赖关系
*   我们可以在专用服务器上部署一个 Python 应用程序(PythonAnyWhere、Heroku 等等)

### 对虚拟环境的需求

我在我的项目中使用了很多库。其中有三个 web 应用程序开发框架，还有其他我想在未来探索的库。这是 Python 中的严肃项目依赖于其他开发人员编写的其他包的主要论点。

如果您是 Django 开发人员，我相信您会使用 **Django rest 框架**来创建强大的 rest APIs， **Django 调试工具栏**来收集关于当前请求/响应的各种调试信息，**芹菜**来处理实时操作和调度，等等。

例如，我的一些项目严重依赖于`requests`包，我目前正在开发的 Django web 应用程序依赖于 2.3.0 版本。根据官方文档，在撰写本文时，该软件包的最新版本是 3.2 版。

让我们假设我在我的 Ubuntu 机器上安装了最新版本的库，因为我需要它用于另一个项目。一切看起来都很好，直到我尝试使用我的旧项目，它在 2.3.0 中运行良好。突然间，一切都破碎了。

发生了什么事？可能 Django 最新版本的 API 从 2.3.0 版本开始就有变化了？原因在这一点上并不重要，因为我的老项目已经坏了，不再工作了。

两个项目之间产生了冲突。他们使用同一个库，但是他们需要不同版本的库。

各种包解决了这个问题。让我们看看一些突出的。

## 开始前

在本教程中，我们将使用 Python 3，所以让我们从检查您的 Python 安装开始。

为此，请打开一个终端 Windows 上的 cmd/PowerShell—并键入以下命令:

```
python --version

Python 3.9.5 # My result 
```

*注意:大部分 macOS 和 Linux 系统都安装了 Python。如果你用的是 Windows，你可以查看一下 [Python 安装指南](https://docs.python.org/3/using/windows.html)。*

如果你没有得到表格`Python 3.x`的结果，有两种选择:

*   如果这个命令返回了一个`Python 2.x`版本，那么您将需要在本教程中使用`python3`
*   如果您得到一个`Unknown command`错误，尝试运行`python3`，如果您得到另一个错误，遵循 Python 安装指南

您可以通过检查版本来证明`python3`二进制文件的存在:

```
python3 --version

Python 3.9.5 
```

*注意:如果上面的命令有效，你需要运行`python3`而不是`python`。*

现在您已经知道了在您的机器上运行的 Python 命令，让我们进入虚拟环境。

## 内置`venv`模块

让我们使用内置的 Python [venv 模块](https://docs.python.org/3/library/venv.html)来创建您的第一个虚拟环境。

*注意:要使用此模块，您需要在系统中安装 Python 3.3 或更高版本。*

要使用`venv`创建 Python 虚拟环境，请键入以下命令:

```
python -m venv virt1 
```

*注意:`-m`标志意味着 Python 将内置的`venv`模块作为脚本运行。*

这将创建一个名为`virt1`的虚拟环境，但这只是一个参数。你可以用任何你想要的名字创建虚拟环境。

安装在`virt1`目录中的所有东西都不会影响全局包或系统范围的安装，因此避免了依赖冲突。

### 激活虚拟环境

重要的是要知道，每次我们想要使用一个创建的虚拟环境时，我们需要用下面的命令激活它:

```
source virt1/bin/activate 
```

这并不是在每个系统中都有效，所以您可以查看下表，清楚地知道应该使用哪个命令:

| 平台 | 壳 | 激活虚拟环境的命令 |
| --- | --- | --- |
| 可移植性操作系统接口 | bash/zsh | $ source(venv-name)/bin/activate |
|  | 鱼 | $ source(venv-name)/bin/activate . fish |
|  | csh/tcsh | $ source(venv-name)/bin/activate . csh |
|  | PowerShell 核心 | $ (venv-name)/bin/Activate.ps1 |
| Windows 操作系统 | cmd.exe | c:>(venv-name)\ Scripts \ activate . bat |
|  | PowerShell | PS C:>(venv-name)\ Scripts \ activate . PS1 |

*注意:POSIX 上的`$`标志和 Windows 上的`C:>`、`PS C:>`标志不是命令的一部分。*

您可能注意到了，我在 POSIX (macOS 和 Linux)中使用 bash shell，这就是我运行上面命令的原因。

### 在创建环境之后

一旦虚拟环境被激活，终端提示符会有一些变化。

![Activated environment](img/618047bb6a0a7fbd1cea92a7e2cfe9be.png)

以下命令允许您停用虚拟环境:

```
deactivate 
```

请注意，您的终端提示符再次发生了变化。

![Deactivated environment](img/7c1fd3a6a3e202f219a3198cd8b8a82a.png)

现在再次激活您的虚拟环境，并使用`which`命令检查正在使用的 Python 二进制文件:

```
source virt1/bin/activate
which python 
```

如果一切正常，您应该得到类似于下面的输出:

```
/home/daniel/tests/python-tests/venvs/virt1/bin/python 
```

如果您再次选择`deactivate`和`which`，您应该会得到不同的输出:

```
deactivate
/usr/bin/python 
```

这是因为，当在虚拟环境中工作时，会使用放置在该环境中的二进制副本。这同样适用于包。

### 虚拟环境中的 Pip

虽然这不是 pip 指南，但展示 pip 和虚拟环境之间的工作流程是很重要的。

pip 是一个用来安装和管理 Python 包的包管理器。

当您想要将您的项目分发给其他人时，这是非常有用的，因为它允许其他开发人员—以及最终用户—一目了然地安装您的项目的所有依赖项。

例如，开发人员可以激活一个虚拟环境，然后运行以下命令来安装项目的依赖项:

```
pip install -r requirements.txt 
```

这里，`requirements.txt`是包含所有项目依赖项的文件——包的特定版本。

要生成项目的依赖项文件，可以运行下面的命令:

```
pip freeze > requirements.txt 
```

如果你想安装一个特定版本的包，你可以运行`pip install`，后跟包名，双等号(`==`，以及它的版本:

```
pip install package==version 
```

在其他情况下，我们也可以从我们的机器(或虚拟环境)中卸载软件包:

```
pip uninstall some-package-name 
```

## Virtualenv(虚拟环境)

Virtualenv 是一个用于创建虚拟环境的外部包。实际上，Python 内置的`venv`是它的一个子集，所以`virtualenv`比我们看到的第一个选项拥有更多的特性。你可以在[官方文档](https://virtualenv.pypa.io/en/latest/)中了解更多关于`virtualenv`优于`venv`的地方。

现在，让我们使用下面的命令安装带有 pip 的`virtualenv`(确保您已经停用了之前的`venv`):

```
pip install virtualenv 
```

这个工具的工作方式类似于`venv`，所以让我们通过创建另一个虚拟环境来测试它:

```
virtualenv virt2 
```

*注意:在运行上述命令之前，请确保您已经进入了另一个环境。*

与`venv`一样，我们必须在使用之前激活虚拟环境:

```
source virt2/bin/activate 
```

如果我现在安装最新版本的 requests，它将只安装在虚拟环境`venv2`:

```
pip install requests 
```

上述命令会产生以下输出:

```
Collecting requests
 ...
Installing collected packages: urllib3, idna, chardet, certifi, requests
Successfully installed certifi-2021.5.30 chardet-4.0.0 idna-2.10 requests-2.25.1 urllib3-1.26.5 
```

如果我运行`pip freeze`命令，打印所有已安装软件包的列表，我会得到:

```
certifi==2021.5.30
chardet==4.0.0
idna==2.10
requests==2.25.1
urllib3==1.26.5 
```

正如您所看到的，我得到的唯一的包是最新版本的`requests`——在我写作的时候——以及它的依赖项。

### 其他虚拟新功能

我们可以在使用`virtualenv`的同时使用`-p`标志，以使用全局安装在机器上的特定版本的 Python。

例如，如果您的机器上安装了 Python3，可以使用下面的命令来创建包含 Python3 的虚拟环境`virt2`:

```
virtualenv -p /usr/bin/python3 virt2 
```

要删除一个虚拟环境，您可以使用`rm -r`命令，就像您要删除任何其他目录一样:

```
rm -r virt2 
```

您可以在[官方文档](https://virtualenv.pypa.io/en/latest/cli_interface.html)中了解更多关于 virtualenv CLI 界面的高级用法。

## Virtualenvwrapper(虚拟包装器)

Virtualenvwrapper 提供了非常有用的命令，通过将它们组织在一个简单的地方，使虚拟环境的工作变得更加容易。

与 virtualenv 一样，它可以通过 pip 轻松安装。

```
pip install virtualenvwrapper 
```

这将在您的`~/.local/bin/`目录下创建一个 shell 文件`virtualenvwrapper.sh`。这个文件夹用来存储包的二进制文件，让你可以直接从终端使用 Python 包。

在使用 virtualenvwrapper 之前，您需要编辑您的 shell 配置文件。由于我使用的是 bash shell，我将把以下内容添加到位于我的主目录中的`.bashrc`文件中:

```
cat <<EOT>> ~/.bashrc
# Virtualenwrapper settings
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
export VIRTUALENVWRAPPER_VIRTUALENV=~/.local/bin/virtualenv
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/Documents
source ~/.local/bin/virtualenvwrapper.sh
EOT 
```

这将把上述内容添加到文件的末尾。如果上面的命令不起作用，请打开文件并手动修改。

`VIRTUALENVWRAPPER_PYTHON`指向你机器的 Python 二进制文件。您可以使用以下命令检查它(不激活任何虚拟环境):

```
which python

/usr/bin/python # My result 
```

确保根据您的 Python 二进制路径修改`.bashrc`文件。

*注意:如果你使用的是 Windows，你可以使用 [virtualenvwrapper-win](https://pypi.org/project/virtualenvwrapper-win/) 。*

然后，我们通过运行以下命令，用我们对`.bashrc`文件所做的更改重新加载 bash shell:

```
source ~/.bashrc 
```

现在，可以使用`mkvirtualenv`命令轻松地将新环境默认放置在该文件夹中:

```
mkvirtualenv sitepoint 
```

您可以通过进入`WORKON_HOME`路径看到`sitepoint`虚拟环境文件夹，我们在上面将其定义为`$HOME/.virtualenvs`:

```
ls ~/.virtualenvs

sitepoint # Virtual env folder 
```

要获得 virtualenvwrapper 创建的所有虚拟环境的列表，可以运行不带参数的`workon`命令:

```
workon

sitepoint # My result 
```

借助`workon`命令，我们可以轻松激活虚拟环境:

```
workon sitepoint 
```

停用虚拟环境的命令与我们之前使用的命令相同:

```
deactivate 
```

在不同的虚拟环境之间切换非常容易。例如，到`workon`另一个虚拟环境:

```
workon another_virtualenv 
```

要删除虚拟环境，应使用命令`rmvirtualenv`:

```
rmvirtualenv sitepoint 
```

## 结论

在本教程中，您已经了解了每个 Python 开发人员都应该掌握的基本工作流。虚拟环境是任何协作 [Python 项目](https://www.sitepoint.com/django-photo-sharing-app/)的关键部分，你可以通过使用它们立即提高你的生产力。

工具就在外面。现在是时候将它们融入到具有挑战性的个人项目中了。在您的 Python 开发工作流程中，您是否使用了其他有趣的方法？在 [Python 论坛](https://www.sitepoint.com/community/c/python-perl-and-golang/python/)让我们知道！

## 分享这篇文章