# Firefox OS 命令行界面简介

> 原文：<https://www.sitepoint.com/introduction-firefox-os-command-line-interface/>

火狐操作系统(我们称之为 FFOS)是一个新的移动操作系统，充满潜力，越来越受用户和开发者的欢迎。

在开发了几个 FFOS 应用程序之后，我认为这个过程可以更加自动化，减少重复性。必须有一种比手动创建整个目录树和每个 FFOS 应用程序所需的文件更快的方法来创建项目。

受 Git 的启发，我决定创建一个*命令行界面*来自动化这个过程，并帮助其他喜欢开发 FFOS 应用的开发者。

## ffos-cli 是什么？

`ffos-cli`是一个简单的工具，用 C 编程语言开发。它可以帮助您生成 FFOS 应用程序等所需的整个目录树。

它帮助开发人员:

*   用应用程序的名称创建一个新的项目文件夹
*   从命令行编辑清单文件
*   将源文件添加到项目中

## 入门指南

ffos-cli 使用一个名为 **jansson** 的 C 库来处理 JSON 文件，比如任何 Firefox OS 应用程序项目中的`manifest.webapp`文件。为了得到`ffos-cli`并正确使用，你应该下载 **jansson** 。如果使用 Ubuntu，请执行以下命令:

```
sudo apt-get install libjansson-dev
```

如果使用 Mac，您可以使用自制软件并键入:

```
brew install jansson
```

这将在你的机器上安装**詹森**。现在你已经准备好获取`ffos-cli`源代码并自己编译了。你可以从 [GitHub](https://github.com/aziflaj/ffos-cli/) 克隆源代码。

要将其克隆到您的机器上，请执行:

```
git clone https://github.com/aziflaj/ffos-cli.git
```

要安装，请执行:

```
cd ffos-cli
make
sudo make install
```

如果您执行`ffos`，您将看到默认的帮助页面:

```
ffos-cli 0.1 to help developing Firefox OS applications easier

commands:
    create - Creates a new Firefox OS application folder with necessary files
    add - Adds new source files to the working project
    set - Changes data stored in manifest file

For more help check the README or type ffos help [command].
```

如果你看到这个帮助页面(我相信你会的)，你已经成功地在你的机器上安装了`ffos-cli`。现在，您可以使用它来创建 Firefox OS 应用程序了。

## ffos-cli 正在运行

现在让我们使用这个工具来创建并管理一个 Firefox OS 应用程序。

要创建应用程序，请执行:

```
ffos create MagnApp
```

该命令将创建以下目录树:

```
[MagnApp]
    |------ css/
    |------ img/
    |------ js/
    |------ index.html
    |------ manifest.webapp
```

移动到 MagnApp 目录并设置 manifest.webapp 文件。

默认情况下，`manifest.webapp`文件是:

```
{
    "name" : "MagnApp",
    "launch_path" : "index.html",
    "description" : "App description here",
    "version" : "1.0",
    "developer" : {
        "name" : "Your name here",
        "url" : "Your url here"
    }
}
```

如果您想将您的姓名或您公司的名称设置为开发人员的姓名，或者将您的 URL 设置为开发人员的 URL，您可以通过执行:

```
ffos set dev.name John Doe                  # or your company name
ffos set dev.url https://www.johndoe.com    # or your company url
```

现在，让我们更改正在开发的应用程序的描述。这可以通过执行以下命令来实现:

```
ffos set descr "This application does magnificient things"
```

也许你没有发布这个应用程序的 1.0 版本。要更改版本号，请执行以下命令:

```
ffos set version 0.3    # or whatever version this is
```

执行完所有这些`ffos`命令后，`manifest.webapp`变为:

```
{
    "name" : "MagnApp",
    "launch_path" : "index.html",
    "description" : "This application does magnificient things",
    "version" : "0.3",
    "developer" : {
        "name" : "John Doe",
        "url" : "https://www.johndoe.com"
    }
}
```

如果需要在项目中添加 CSS 和 JavaScript 文件。您可以手动创建它们，或者执行:

```
ffos add css main   # this creates main.css
ffos add js app     # this creates app.js
```

执行这些操作后，目录树变成:

```
[MagnApp]
    |------ css/
    |          |------ main.css
    |
    |------ img/
    |
    |------ js/
    |          |------ app.js
    |
    |------ index.html
    |------ manifest.webapp
```

您可以通过执行来添加更多的 html 文件(您现在可能已经明白了！):

```
ffos add html anotherfile   # this creates anotherfile.html
```

如果您想将`anotherfile.html`设置为启动路径，请执行:

```
ffos set launch_path anotherfile.html
```

在应用程序的开发过程中，您可能需要添加权限。权限是清单中的字段，包含应用程序使用的特定 API 的信息。[这是 Mozilla 在`manifest.webapp`文件中接受的](https://developer.mozilla.org/en-US/Apps/Build/App_permissions)所有权限的列表。要使用`ffos-cli`添加权限，请执行:

```
ffos set permission alarms "This application will use alarms"
```

这个命令添加一个名为`"alarms"`的权限，描述为`"This application will use alarms"`。如果名为`"alarms"`的权限已经存在于`manifest.webapp`中，它将改变权限的描述。

因此，假设您已经执行了上面的所有命令，您将得到这个目录树:

```
[MagnApp]
    |------ css/
    |          |------ main.css
    |
    |------ img/
    |
    |------ js/
    |          |------ app.js
    |
    |------ anotherfile.html
    |------ index.html
    |------ manifest.webapp
```

和`manifest.webapp`文件:

```
{
    "name" : "MagnApp",
    "launch_path" : "anotherfile.html",
    "description" : "This application does magnificient things",
    "version" : "0.3",
    "developer" : {
        "name" : "John Doe",
        "url" : "https://www.johndoe.com"
    },
    "permissions" : {
        "alarms" : {
            "description" : "This magnificient application will use alarms"
        }
    }
}
```

当我设计和开发 ffos-cli 时，简单是我想到的第一件事，我希望您会发现它很有用。随意叉在 [GitHub](https://github.com/aziflaj/ffos-cli/) 上。目前，我认为权限过滤和应用程序打包功能会有所帮助，并希望您能帮助添加这些或任何其他您认为需要的功能。

## 分享这篇文章