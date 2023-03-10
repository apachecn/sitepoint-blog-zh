# WebStorm 和 Angular 的 12 大生产力技巧:第 2 部分

> 原文：<https://www.sitepoint.com/productivity-tips-for-webstorm-and-angular-part-2/>

*这篇关于 WebStorm 和 Angular 的文章由 [JetBrains](https://www.jetbrains.com/) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在这个关于 WebStorm 和 Angular 的两部分系列文章中，谷歌开发专家 Jurgen Van de Moere 和 Todd Motto 分享了他们使用 WebStorm 开发 Angular 应用程序时最喜欢的 12 个生产率技巧。

你可以在这里查看第一部分。在第二部分中，Todd 分享了他个人的七大 WebStorm 特性，这些特性让他每天都能提高自己的 WebStorm 和 Angular productivity:

*   使用导入路径计算
*   实时模板
*   在 IDE 中运行测试
*   穿越时空
*   使用 TypeScript 参数提示
*   使用面包屑导航
*   并使用 WebStorm 查找角度文档

在 WebStorm 中开发 Angular 应用程序时，每个 WebStorm 和 Angular tip 都将提高您的工作效率。让我们来探索这些技巧。

在我们开始之前:当对设置进行更改时，请记住 WebStorm 允许您分别在 IDE 范围和项目范围内更改设置/首选项。

## 网络风暴和角度提示 6:导入路径计算

默认情况下，WebStorm 将解析相对于文件的导入路径。此设置将满足大多数项目，并避免不必要的路径编辑。这也是使用 Angular CLI 生成的项目所使用的方法。

WebStorm 的奇妙之处在于，您不必亲自键入这些导入语句！当您需要使用通常会被导入的构造时，只需在需要的地方键入它。WebStorm 会建议通过自动完成上下文菜单导入该构造，或者高亮显示该构造，并通过按`option` + `enter`来选择导入它。

WebStorm 将在文档顶部为您创建一个新的 import 语句，或者将该构造添加到使用相同源库的现有导入组中。

[https://gfycat.com/ifr/HappygoluckyFantasticAnchovy](https://gfycat.com/ifr/HappygoluckyFantasticAnchovy)

WebStorm 为您提供了其他专门的选项来处理您的导入。对于需要它的项目，您可以指示 WebStorm 计算相对于`tsconfig.json`文件位置的导入路径。如果您决定使用一个木桶`index.ts`文件来导入您的组件(阅读更多关于[木桶技术](https://angular.io/guide/glossary#barrel)的内容)，那么您可以使用*使用目录导入(节点样式模块解析)*。这将使用 Node.js 模块解析策略，而不是 TypeScript 的经典模块解析策略。

当导入不需要导入整个模块的子模块时，将该模块添加到*不从*列表中精确导入。WebStorm 将在自动导入过程中跳过指定的路径。例如，不是拥有:

```
import {Observable} from 'rxjs' 
```

…将 **rxjs** 添加到列表中会产生:

```
import {Observable} from 'rxjs/Observable' 
```

WebStorm 跳过 RxJS 模块，自动为您导入可观察子模块！

![WebStorm and Angular: Import Path Calculation](img/9f1cbd744fd2e65a7909620640489314.png)

*额外提示:格式化输入以在首选项>编辑器>代码样式>TypeScript–Spaces–Within–ES6 导入/导出括号*中使用花括号内的空格。

![WebStorm and Angular: Import Path Calculation](img/cf8e236d844503282ecb96dffe602812.png)

## WebStorm 和 Angular 技巧 7:使用实时模板

当您发现自己重复编写某些代码模式时，创建一个动态模板来快速搭建代码块。WebStorm 已经提供了一些预定义的实时模板，您可以根据自己的开发风格进行修改。

要创建实时模板，请导航到以下位置:

*   【MAC OS】*WebStorm>偏好>编辑>直播模板*
*   【Windows/Linux】*文件>设置>编辑> Live 模板*

您会看到 WebStorm 已经将预定义的模板捆绑到类别中。我创建了一个类别来捆绑我的 ngrx Live 模板，方法是点击 *+* 符号并选择*模板组*。然后，我再次点击 *+* 符号，创建了一个新的实时模板，但这次选择了实时模板。

让我向您简要介绍一下使实时模板成为生产力成功的要素:

*   **缩写:**您将在编辑器中键入的快捷方式，用于调用您的模板。
*   **描述:**告诉你模板被调用时做什么。
*   **模板文本:**这是调用时要搭建的代码片段。利用强大的实时模板变量，允许您在搭建时用您想要的文本替换它们。
*   **上下文:**选择 WebStorm 应该对模板敏感的语言或代码片段。
*   **选项:**根据*WebStorm>Preferences>Editor>Code Style*上定义的样式设置，定义哪个键允许你展开模板并重新格式化。

您已经准备好试用您的模板了。打开一个支持您定义的上下文的文件，键入您的快捷方式，按下定义的扩展键，然后看着您的模板为您出现！如果您定义了任何变量，光标将放在应该输入第一个变量的位置。如果定义了其他变量，您可以使用 tab 导航到它们，而无需单击。

![WebStorm and Angular: Use Live Templates](img/d6693ee3e73c95edfd5d500cd7cfb774.png)

[https://gfycat.com/ifr/TinyKeenGermanshorthairedpointer](https://gfycat.com/ifr/TinyKeenGermanshorthairedpointer)

## WebStorm 和 Angular 技巧 8:运行测试

WebStorm 是一个优秀的测试工具。只要在计算机上安装了 Node.js 运行时环境并启用了 NodeJS 插件，就可以直接从 IDE 中运行各种 JavaScript 测试。这里有一些运行测试时的生产率提示。

您可以运行单个 Karma 测试，而不是运行所有测试。在编辑器中点击测试旁边的图标，选择*运行*或*调试*。图标将显示测试状态。

直接从 IDE 中用量角器运行和调试测试。确保量角器在全球范围内安装。测试结果将显示在工具窗口中。您可以过滤测试结果，并选择只显示失败的测试。

[https://gfycat.com/ifr/CleverAfraidArawana](https://gfycat.com/ifr/CleverAfraidArawana)

作为额外的提示，您可以直接从 IDE 中测试 RESTful 服务！转到*工具>测试 RESTful Web 服务*。

[https://gfycat.com/ifr/VigilantPlayfulFirefly](https://gfycat.com/ifr/VigilantPlayfulFirefly)

## 网络风暴和角度提示 9:穿越时间

这是一个网络风暴可以扭转乾坤的领域。您可能已经不停地编写了许多新代码，并且忘记了定期将代码提交给版本控制——或者根本就没有启动它。它发生了。无论背景如何，有些情况下我们需要回到过去。

我们可以使用“撤销”，但是 WebStorm 的默认限制是 100 次撤销。这个限制可以增加，但是有一个更好的方法来恢复以前的状态:使用本地历史。

使用本地历史，您可以浏览代码的快照，以直观地找到您想要到达的代码库状态。这类似于版本控制，并排对比当前状态和以前的状态。(还提供统一的查看器。)然而，本地历史独立于版本控制:例如，即使您没有启动 git，也可以使用它。这些快照将易于浏览，因为它们是按时间排序的，从最新的到最早的。

![WebStorm and Angular: Travel Through Time](img/9a5ac36e2e6ed794db71f092ff180ffb.png)

![WebStorm and Angular: Travel Through Time](img/b153d3f4b0b132de65ca327c7d65dd0a.png)

## WebStorm 和 Angular 提示 10: TypeScript 参数提示

TypeScript 参数提示显示方法和函数中的参数名称，以使您的代码更易于阅读。默认情况下，只有某些参数提示基于它们的类型显示，而一些常见方法的提示是隐藏的。

![WebStorm and Angular: TypeScript Parameter Hints](img/c385c2bbf90eb9b5e32525ece4988881.png)

![WebStorm and Angular: TypeScript Parameter Hints](img/1de68a9f4d313f171bd1d79bbd92aab8.png)

为了避免干扰，您可以将参数提示列入黑名单，这样它们就不会在项目中再次出现。您也可以启用所有参数或完全禁用它们。要显示所有参数的参数提示:

*   在 *WebStorm >首选项>编辑器>通用>外观*中打开*外观*页面。
*   点击“显示参数名提示”复选框旁边的*配置*。
*   在*选项*框中，您可以选择“显示所有参数的名称”。*

**注意:在同一个对话框中，您可以修改参数提示黑名单。*

[https://gfycat.com/ifr/UnlinedPleasedHammerkop](https://gfycat.com/ifr/UnlinedPleasedHammerkop)

## WebStorm 和 Angular Tip 11:使用面包屑导航

查看您的`.ts`文件的底部，您可以通过面包屑看到您在当前文件中的位置。面包屑显示类、变量、函数和方法的名称。单击面包屑的名称可跳转到父对象。您可以通过右键单击面包屑，然后单击*面包屑>顶部*或*面包屑>不显示*，将它们配置为显示在编辑器顶部，或者根本不显示。

[https://gfycat.com/ifr/SnivelingThoughtfulEuropeanpolecat](https://gfycat.com/ifr/SnivelingThoughtfulEuropeanpolecat)

或者，您可以使用文件结构弹出窗口导航文件。可以通过*导航>文件结构*或者在 macOS 上按`CMD` + `F12`和在 Windows/Linux 上按`Ctrl` + `F12`来访问。

![WebStorm and Angular: Navigate using Breadcrumbs](img/d483d3293dade40b145fa50aa9a7bf63.png)

## WebStorm 和 Angular 额外提示 12:文档查找

没有必要离开 IDE 去获得 Angular 正在做什么的更深入的信息。

只要 WebStorm 有正在讨论的 Angular 构造的文档，您就可以将插入符号放在 Angular 方法或函数上，并按下`F1`来快速查看它的文档。这也适用于模块和其他角度结构。

[https://gfycat.com/ifr/WelldocumentedRegalElkhound](https://gfycat.com/ifr/WelldocumentedRegalElkhound)

另一种调用文档的快捷方式是将鼠标停留在一个构造上。在*首选项>编辑器>常规*中，勾选“鼠标移动时显示快速文档”框。您可以在调用快速文档之前调整时间延迟。

![WebStorm and Angular: Documentation Look Up](img/18c1abf6274af26d11434d0fa47f7677.png)

[https://gfycat.com/ifr/FarflungHugeHuemul](https://gfycat.com/ifr/FarflungHugeHuemul)

最后一句话:不要只是遵循这些提示！衡量他们的结果。WebStorm 允许你在*帮助>生产力指南*中获得生产力报告。您可以看到键入代码完成为您节省了多少！

## 摘要

让我们回顾一下 Todd 关于在 WebStorm 中提高角度开发效率的个人建议:

*   使用高效的导入技术
*   使用动态模板快速构建代码模式
*   在 ide 中运行和监控各种测试
*   使用本地历史作为独立的、个人的、实时的版本控制系统
*   通过使用 TypeScript 参数提示提高代码的可读性
*   使用面包屑或文件结构工具快速导航到文件
*   从编辑器中查找角度文档。

这就结束了我们的 WebStorm 和 Angular productivity 技巧，由 Google 开发者专家使用 WebStorm 开发 Angular 应用程序。现在，您可以简化开发工作流程，充分利用键盘上的时间。

## 分享这篇文章