# Bower 浏览器的包管理

> 原文：<https://www.sitepoint.com/package-management-for-the-browser-with-bower/>

Bower 是浏览器的包管理器。它由 Twitter 开发，可通过 npm 获得，这意味着手动管理客户端 JavaScript 包的日子可能很快就会成为过去。

要安装 [Bower](http://twitter.github.com/bower/) ，您需要安装 Node 和 NPM。你可以从 nodejs.org 的[下载安装 Node。](http://nodejs.org) [NPM](https://npmjs.org/) 与每个二进制包捆绑在一起，所以当你安装 Node 时，NPM 应该会自动同时安装。您还需要安装 Git，因为 Bower 使用 Git 端点来定位包。如果你没有安装 Git，你可以从 git-scm.com/downloads 下载。一旦安装了 Node、npm 和 Git，现在您需要做的就是打开您的终端并键入:

```
npm install bower -g
```

如果你是 NPM 的新用户，末尾的`-g`意味着 Bower 将在全球范围内安装，这意味着我们可以在任何项目目录的任何地方运行它。现在让我们运行 Bower 并向它传递`--help`命令，看看它到底能为我们提供什么:

```
bower --help
```

如果您继续操作，您将看到安装、卸载和更新软件包的命令，列出您当前已经安装的软件包，并搜索新的软件包。我们还可以通过将命令传递给`help`来获得任何特定命令的帮助:

```
bower help list
```

简单地列出我们已经安装的软件包。如果我们现在运行它，我们不会看到太多，因为我们还没有安装任何软件包。让我们通过使用以下命令安装 jQuery 来解决这个问题。

```
bower install jquery
```

当您运行这个程序时，您将看到 Bower 克隆了这个包的 Git 存储库，将这个包缓存在`~/bower`目录中，然后将这个包复制到我们当前项目的`components`目录中(如果这个目录不存在，Bower 将创建它)。下载完成后，将 jQuery 包含在项目中的最简单方法是通过一个常规的`<script>`标签，如下所示。

```
 <script src="components/jquery/jquery.js"></script> 
```

当然，对于更复杂的项目，您可以自由地将 Bower 与您首选的捆绑/缩小工具集成在一起。

您会注意到 jQuery 安装在它自己的目录中，该目录本身包含了`jquery.js`和一个`components.json`文件。`components.json`文件很像 NPM 模块中使用的`package.json`文件。它存储关于包的元数据，以及包需要的任何依赖项。jQuery 包没有任何依赖项，但是如果我们安装一个*有*依赖项的包，Bower 会同时安装这些依赖项。jquery-Mustache 依赖于 Mustache，所以让我们安装它，看看会发生什么:

```
bower install jquery-Mustache
```

您将看到 Bower 首先下载 jquery-Mustache，然后下载它的依赖项。我们可以通过查看组件目录来验证这一点:

```
ls components
jquery  jquery-Mustache  mustache
```

jquery-Mustache 是我们期望看到的，jquery 之前就已经有了，但是，正如你所看到的，Bower 自动为我们下载了 Mustache，我们不必担心。如果我们查看 jquery-Mustache `components.json`文件，我们可以看到依赖关系是如何指定的:

```
"dependencies": {
    "jquery": ">=1.5",
    "mustache": ">=0.4"
}
```

当 Bower 读到这里时，它知道必须下载至少 1.5 版的 jQuery，以及至少 0.4 版的 mustache。如果您现在运行`bower list`，您将看到 Bower 列出了您已安装的软件包，它们的依赖项嵌套在它们下面。

## 安装多个软件包

您不必一次安装一个软件包:您可以将一个空格分隔的列表传递给`bower install`，它将一个接一个地安装每个软件包。让我们使用下面的命令安装更多的 jQuery 插件。

```
bower install jquery.validation jquery.colorbox jquery.loadfeed
```

## 卸载软件包

卸载软件包和安装软件包一样简单。在这种情况下，我们使用`bower uninstall`命令。

```
bower uninstall jquery.colorbox
```

如果你错误地卸载了一个依赖于另一个包的包，Bower 会警告你已经在卸载了之后移除了一个依赖*。例如，您可以使用以下命令卸载 jQuery。*

```
bower uninstall jquery
```

当你卸载 jQuery 时，Bower 警告你`jquery.loadfeed depends on jquery`。幸运的是，重新安装 jQuery 很容易。

## 管理包版本

更新软件包也很容易。要将 jQuery 更新到最新版本，我们需要输入:

```
bower update jquery
```

并且，为了查看包的哪些版本是可用的，我们可以运行`bower info`命令:

```
bower info jquery
```

## 发布包

最后，让我们快速看一下用 Bower 发布自己的包是多么容易。只需遵循以下三个步骤:

1.  创建您的包的`component.json`文件
2.  将您的包推送到 Git 端点，例如 GitHub。
3.  运行`Bower register yourpackagename git://github.com/yourusername/repositoryname`，适当更换`yourpackagename`、`yourusername`和`repositoryname`。

这就是全部了。你不必创建一个帐户，或设置任何认证。套餐名称按照先到先得的原则分配。所以，你只需要为你的包取一个唯一的名字，然后其他开发者就可以通过运行`bower install yourpackagename`来安装它。

## 结论

在本文中，我们已经了解了由 Twitter 开发的浏览器包管理器 Bower。我们已经介绍了如何通过 npm 安装 Bower，如何安装、更新和删除包，以及如何发布自己的包。

## 更多资源:

*   [鲍尔](http://twitter.github.com/bower/)
*   [GitHub 库](https://github.com/twitter/bower)
*   亚历克斯·麦克考和雅各布·桑顿的 JavaScript Jabber 029:bower . js
*   [安德](https://github.com/ender-js/Ender)
*   [堵塞](http://jamjs.org)

## 分享这篇文章