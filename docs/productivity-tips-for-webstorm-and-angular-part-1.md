# WebStorm 和 Angular 的 12 大生产力技巧:第 1 部分

> 原文：<https://www.sitepoint.com/productivity-tips-for-webstorm-and-angular-part-1/>

*这篇关于 WebStorm 和 Angular 的文章由 [JetBrains](https://www.jetbrains.com/) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在这个关于 WebStorm 和 Angular 的两部分系列中，谷歌开发专家[尤尔根·范·德·摩尔](https://twitter.com/jvandemo)和[托德·莫托](https://twitter.com/toddmotto)分享了他们最喜欢的使用 WebStorm 开发 Angular 应用程序的生产率技巧。

在第一部分中，Jurgen 分享了他个人的五大 WebStorm 功能，这些功能使他在使用 WebStorm 和 Angular 时每天都能提高工作效率:

1.  从 [WebStorm](https://www.jetbrains.com/webstorm/) 内部使用 [Angular CLI](https://cli.angular.io/)
2.  像专业人士一样导航
3.  利用 [Angular 语言服务](https://next.angular.io/guide/language-service)
4.  自动格式化您的代码
5.  优化您的导入

每一个技巧都可以极大地提高您的开发效率，所以让我们一个接一个地深入研究它们。

## WebStorm 和 Angular 提示 1:在 WebStorm 中使用 Angular CLI

Angular CLI 是一个命令行界面(CLI ),由 Angular 团队编写和维护，帮助您自动化开发工作流程。您可以使用它来快速创建新的 Angular 项目，并向现有的 Angular 项目添加新的特性，如组件、服务和指令。

使用 Angular CLI 的 WebStorm 和 Angular 集成为您提供了 WebStorm 内部的所有 Angular 功能，而无需使用终端。

要创建新的 Angular 项目，选择*文件>新建>项目*并选择 *Angular CLI* 。

输入项目位置，点击*创建*按钮。WebStorm 使用 Angular CLI 创建新的 Angular 项目并安装依赖项。

当您的新角度应用就绪时，您可以轻松地添加新的角度特征。右键点击 *src/app* 并选择*新建> Angular CLI* 来选择您想要添加的特性类型。

选择一个特征后，可以指定名称和可选参数，就像在命令行上使用 Angular CLI 一样:

![WebStorm and Angular : Create a new Angular module](img/71a428cfca27769a7aae06ad0efa15a0.png)

*要了解更多关于角度 CLI 选项和参数的信息，请务必查看[终极角度 CLI 参考](https://www.sitepoint.com/ultimate-angular-cli-reference/)。*

真正令人惊叹的是，WebStorm 自动将组件添加到正确的角度模块中——在本例中为`AppModule`。

如果您的应用有多个角度模块，右击您想要添加特性的模块，并选择*新建>角度命令*。WebStorm 将确保在正确的位置创建新文件，并将新特征添加到正确的角度模块中。

多甜蜜啊！

## WebStorm 和 Angular Tip 2:像专业人士一样导航

使用`cmd` -click 或`cmd` + `B`轻松跳转到项目中的任何定义。

如果你是键盘用户，只需将光标放在一个术语上，然后点击`cmd` + `B`。如果你是鼠标用户，按住`cmd`按钮，你悬停的所有术语都会变成它们定义的链接。

WebStorm 自动识别 HTML 中的角度组件和指令——样式表链接、模板链接、类、接口等等。

不需要手动打开文件；只要跳到你需要的任何定义:

![WebStorm and Angular : Command-click words](img/e58bf8c0e11f4aeb726a705477552544.png)

当寻找一个你没有直接引用的文件时，按两次`shift`打开*到处搜索*对话框。您不必键入整个搜索字符串。如果你想打开`AppComponent`，只需键入每个部分的第一个字母，例如`ac`，WebStorm 会立即为你缩小结果列表，这样你就可以快速选择你想打开的建议:

![WebStorm and Angular: Search everywhere](img/69bc84612f6880e2c299aa6ad95bc1fc.png)

另一个超级有用的导航快捷方式是`cmd` + `E`，它为你提供了一个最近编辑过的文件列表，这样你就可以轻松地在它们之间来回导航。

![WebStorm and Angular: Recent files](img/ed0f78951e89579f3602fe09c0efa970.png)

知道如何快速找到你需要的代码将会为你每天节省大量的时间。

## WebStorm 和 Angular 技巧 3:利用 Angular 语言服务

默认情况下，WebStorm 已经为编写 Angular 代码提供了很大的帮助。

编辑脚本时，WebStorm 会自动导入所需的 JavaScript 模块，因此您不必手动导入它们。

如果打开 TypeScript 面板，WebStorm 会为您提供关于代码有效性的即时反馈，因此您可以在编译项目之前快速解决问题。

观察`OnInit`接口是如何自动导入的，以及实时的 TypeScript 反馈如何立即告诉您您的 TypeScript 代码是否有效:

![WebStorm and Angular: TypeScript recommendations](img/264310391d96cff3eb42d3f70cc9a1f8.png)

当您编辑模板时，WebStorm 为您提供了智能代码完成功能，可以识别组件、指令甚至输入和输出属性:

![WebStorm and Angular: Code completion in templates](img/68641bfea3fff63c93465a7f3eec8bf5.png)

你可以通过安装 [Angular 语言服务](https://next.angular.io/guide/language-service)来更进一步。这是一个由 Angular 团队设计的服务，为 ide 提供 Angular 模板中的错误检查和类型完成。

WebStorm 与 Angular Language Service 集成，以便更好地理解您的代码。要启用 Angular 语言服务，首先确保它已安装:

```
npm install @angular/language-service --save-dev 
```

如果使用 Angular CLI 生成 Angular 应用程序，Angular 语言服务会自动安装。

接下来，进入*首选项>语言&框架>打字稿*，确保*使用打字稿服务*被选中，然后点击*配置…* :

![WebStorm and Angular: TypeScript recommendations](img/62d674a6dc501b2a0a684efc9eb6bce5.png)

将弹出*服务选项*模式。启用*使用角度服务*并应用更改:

![WebStorm and Angular: TypeScript recommendations](img/b854313ed8328682ac681e238673cfc8.png)

启用 Angular 语言服务后，WebStorm 能够改进模板表达式中的代码完成:

![WebStorm and Angular: Angular Language Service Expression Error](img/ff5ef4ff6eee2b78dc6922f25f988b99.png)

…并在您的编辑器中更精确地报告模板错误:

![Highlighted errors](img/9abb3f56dad95085efbcd2a595785f60.png)

不必编译项目就能捕捉错误，这将为您节省大量时间。

## WebStorm 和 Angular 技巧 4:自动格式化代码

不要担心手动格式化代码。网络风暴已经覆盖了你。

无论你是在模板、脚本、样式表甚至是 JSON 文件中，只要点击`cmd` + `option` + `B`，WebStorm 就会自动为你格式化所有代码:

![WebStorm and Angular: Auto-format](img/6275ca13b453fcf4842bd3fe14c69ff0.png)

如果您的项目有一个`tslint.json`文件，只需打开它，WebStorm 会询问您是否要将 TSLint 中的代码样式应用到您的项目中:

![WebStorm and Angular: Import auto format options from TS Lint](img/bd386eac494bbdd40e8f67c90539ca6b.png)

如果你对自动格式化代码的风格不满意，你可以在 *Webstorm >首选项>编辑器>代码风格*中单独微调每种支持语言的格式设置:

![WebStorm and Angular: Configure code style](img/23a981f22690b7fcc60da99eb09ddbe3.png)

WebStorm 的代码格式化功能可确保您的代码根据您的项目设置正确格式化，以便您的代码林挺检查成功通过，您可以专注于编写代码。

## WebStorm 和 Angular 提示 5:优化您的导入

在处理角度脚本时，您可能会发现某些导入不再使用。

如果您不移除未使用的导入，包的大小可能会变得超出需要。然而，删除未使用的导入可能是一件很麻烦的事情。没有网络风暴！

点击`ctrl` + `alt` `O`立即优化您的导入。或者，您可以点击`cmd` + `shift` + `A`打开*查找动作*面板，键入`optim`查找*优化导入*动作，并点击`enter`键运行该动作。

优化导入时，WebStorm 将为您做以下工作:

*   在相同的`import`语句中合并来自相同模块的导入
*   移除未使用的导入
*   重新设置 import 语句的格式，使其符合您所需的行长度。

在下面的例子中，*优化导入*运行了两次。第一次，它将来自`@angular/core`的所有导入合并到一个导入语句中。

然后从代码中删除`OnInit`、`OnChanges`和`AfterViewInit`界面，再次按下`ctrl` + `alt` + `O`。

这一次， *Optimize imports* 自动从 import 语句中删除未使用的接口，因为代码中不再使用它们:

![WebStorm and Angular: Optimize imports](img/cd265c5770bf32faa1630d287365559f.png)

再也不用担心你的进口报表了。WebStorm 足够聪明，可以为您处理它们！

## 一起使用 WebStorm 和 Angular:摘要

让我们回顾一下 Jurgen 关于使用 WebStorm 和 Angular 来提高 Angular 开发效率的个人建议:

1.  使用 WebStorm 中的 Angular CLI 快速生成新的 Angular 项目和功能
2.  *像专家一样导航*立即跳转到代码定义，轻松定位您正在寻找的代码或文件
3.  *利用 Angular 语言服务*获得更好的代码完成和错误检查，而无需编译您的 Angular 项目
4.  *自动格式化您的代码*让 WebStorm 根据您的项目设置格式化您的所有代码
5.  *优化您的导入*以确保移除所有未使用的导入，并且您生成的包大小保持最佳。

在下一部分， [Todd Motto 分享了他最喜欢的与 WebStorm 和 Angular too 合作的技巧](https://www.sitepoint.com/productivity-tips-for-webstorm-and-angular-part-2/)。一定要去看看！

## 分享这篇文章