# 崇高文本的顶部角度插件

> 原文：<https://www.sitepoint.com/top-angular-plugins-sublime-text/>

本文涵盖了 Sublime Text 文本编辑器的多个 Angular 插件。

无论您是 Angular(版本 2+)开发的新手，并且希望开始使用它并将其与您选择的代码编辑器更紧密地集成，或者您是 Angular 开发的老手，并且作为新的编辑器正在尝试使用 Sublime Text，集成这两者从未如此简单。

Angular 插件有很多选项，可以让你使用 Sublime Text 开发 Angular 应用程序的过程更加顺利。在开始之前，你需要设置 Sublime 文本包管理器(称为“包控制”)，然后你可以看看这里的插件列表，看看是否有符合你需要的！

## 设置包装控制

为了使用本文将要讨论的大多数 Angular 插件，你首先需要在 Sublime 文本中设置包控制。这是一个相当无痛的过程。最简单的方法是复制粘贴配置代码。

1.  使用热键`CTRL` + ```或使用*视图>显示控制台*菜单调出集成的 Sublime 文本控制台。
2.  将 Python 代码块粘贴到控制台，可以从[包控制安装页面](https://packagecontrol.io/installation)复制，并按照那里的说明进行操作。
3.  一旦完成，包控制菜单将被设置！现在，您需要做的就是找到并安装您的软件包。

### 通过包控制菜单安装 Sublime 文本包

使用包控制轻而易举。你需要打开包控制，选择*安装*菜单，然后选择并安装一个包:

1.  使用快捷键`CMD` + `Shift` + `P` ( `CTRL` + `Shift` + `P`，视操作系统而定)打开命令面板。
2.  键入`install package`，然后按下`Enter`，这将带您进入可用软件包列表。
3.  按名称搜索软件包，选择软件包，然后按`Enter`安装。

## Angular 2 HTML 包

Angular 2 HTML 插件为升华文本提供了增强的 HTML 语法。您将能够在不破坏语法高亮显示的情况下使用角度属性。此外，页面的 JavaScript 部分也会突出显示为 JavaScript。对于 Angular 开发人员来说，这是一个很小但非常有用的附加功能。

### Angular 2 HTML 包设置

在撰写本文时，这个插件不在包控制中，但是可以通过下面的步骤手动安装。

1.  关闭 Sublime Text 并通过命令行导航到应用程序安装中的 Sublime Text 3“Packages”文件夹。在 macOS 中，这是在`/Users/{user}/Library/Application Support/Sublime Text 3/Packages`。

2.  使用以下内容将存储库克隆到您的包文件夹中:

    ```
    git clone https://github.com/princemaple/ngx-html-syntax 
    ```

3.  重新打开 Sublime Text 3，检查*视图>语法*菜单，确保 Ngx HTML 是一个选项。

此外，您可以使用 Angular 2 HTML 语法让 Sublime Text 自动高亮显示`.component.html`文件:

1.  打开一个`.component.html`文件。

2.  选择*查看>语法> Ngx HTML* 。

3.  转到*首选项>设置>语法特定*。因为您的当前文件使用 Ngx HTML 语法，所以它应该打开 Ngx HTML 的特定语法设置文件。如果是这样，请在右侧的设置面板中添加一个扩展条目:

    ```
    "extensions":
    [
      "component.html"
    ] 
    ```

4.  重启 Sublime Text。现在，所有的`.component.html`文件都应该自动使用 Ngx 语法插件！

## 角度 2 片段

[Angular 2 Snippets](https://github.com/evanplaice/angular2-snippets) 是一个崇高的文本插件，旨在为用户提供 Angular 的代码片段和代码补全。它带来了您的角度发展所需的大部分自动完成功能。对于许多开发人员来说，代码的自动完成有点像是一个温床问题，他们觉得这既是福也是祸，但尽管如此，它还是非常有用。

在撰写本文时，以下代码片段和补全类别是可用的。(关于当前列表，或者关于每个类别中项目的更多细节，请参见插件的 [GitHub 自述文件](https://github.com/evanplaice/angular2-snippets)。)

### Angular 插件:代码片段类别

*   成分
*   指示的
*   服务
*   管
*   路由配置
*   途径
*   试验

### 角度插件:完成类别

*   释文
*   装修工
*   生命周期
*   按指定路线发送
*   指令
*   属性
*   管道

## 角度 CLI

Angular CLI 插件对于任何 Angular 开发人员来说都是极好的，允许 Angular CLI 命令在 Sublime 文本中运行。安装后，可以在命令选项板中找到 Angular CLI 命令。Angular CLI 可用于多种目的:

*   生成组件、类、路线等等
*   用于测试
*   为了林挺
*   用于启动开发服务器

…还有更多！

## 角度插件:类型脚本

[TypeScript](https://github.com/Microsoft/TypeScript-Sublime-Plugin) 在标准 JavaScript 之上提供了一个类型层，是许多 Angular 项目的首选语言。它编译成有效的 JavaScript，所以真正的价值在于开发人员。Sublime Text 的 TypeScript 包为编辑器中的 TypeScript 用户提供了更好的体验。

*   通过点击快捷方式为一行、一段代码或整个文档设置类型脚本格式。
*   快速导航到一个符号或一段文本，轻松查看打字错误。
*   有了插件，项目处理变得轻而易举。它允许创建新的、已配置的 TypeScript 项目。当编辑一个单独的类型脚本文件时，它还会自动创建“推断的项目”，拉进导入的文件使它们对你可用。

## 棱角分明的插件:林挺

谁不需要代码清单？当您使用 Angular 和 TypeScript 时，您会希望您的编辑器 lint 您的代码。[SublimeLinter-contrib-ts lint](https://github.com/lavrton/SublimeLinter-contrib-tslint)是一个连接`tslint`软件和 Sublime 文本的 Sublime 包。为了使用它，你需要预先安装两个东西:

1.  安装`tslint` ( `npm install -g tslint`)。
2.  通过包控制安装 SublimeLinter 3 插件。
3.  使用 [SublimeLinter 文档](http://sublimelinter.readthedocs.io/en/latest/troubleshooting.html#finding-a-linter-executable)。(按照“查找 linter 可执行文件”到“验证路径”的步骤，确保 SublimeLinter 可以看到您的 tslint 包。)
4.  最后，通过包控制安装 Sublime 插件`SublimeLinter-contrib-tslint`。

你都准备好了。现在你只需要配置你的 linter。您可以使用 [tslint 文档](https://palantir.github.io/tslint/usage/configuration/)来处理 tslint 的配置选项(您希望将它们包含在项目的`tslint.json`文件中)。也看看 [SublimeLinter 文档](http://sublimelinter.readthedocs.io/en/latest/linter_settings.html)关于设置 Linter 特定的设置。

## DocBlockr

DocBlockr 是一个升华文本的包，支持 JavaScript 和各种风格的 JavaScript，包括 TypeScript。虽然 DocBlockr 并不是 TypeScript 或 Angular 所独有的，但它肯定可以在那里使用，并且提供了一种不应该被忽视的服务。DocBlockr 是一个帮助开发者编写文档块的插件。为了节省您的宝贵时间，它自动完成了文档块的语法，甚至还自动生成了函数和变量文档块模板。这是一种既能节省时间又能确保你的代码以统一的方式被文档化的绝妙方法。

## 结论

希望这组插件至少能为你提供一些你需要的 Angular 插件，让你在 Sublime 中的 Angular 开发工作取得成功。你使用过上面的任何一个吗，或者对没有提到的插件有什么反馈吗？给我们发推特 [@sitepointjs](https://twitter.com/sitepointjs) 让我们知道！

## 分享这篇文章