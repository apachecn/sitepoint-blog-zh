# 使用 Angular 5 片段和 VS 代码提升您的工作流程

> 原文：<https://www.sitepoint.com/boosting-workflow-angular-5-snippets-vs-code/>

在本文中，我们将关注如何在 Visual Studio 代码中使用 Angular 5 片段来改进我们的工作流程。我们将首先从使用和创建代码片段的基础开始。我们将看看如何在 Angular 项目中使用 VS 代码的 [Angular 片段。然后，我们将在后面看看如何创建我们自己的片段，并与他人分享。](https://github.com/johnpapa/vscode-angular-snippets)

对于任何一个精通编程语言的人来说，你应该知道一遍又一遍地输入相同的代码是多么的无聊。你最终开始复制和粘贴你的代码片段，以避免你的手指再写另一个`for`循环的痛苦。

如果您的编辑器可以在您键入时自动帮您插入这些常用代码，会怎么样？那太棒了，对吧？

嗯，你可能知道它们是**片段**。这是当前所有现代 IDE 中的一个常见特性。甚至 Notepad++也支持它们(尽管默认情况下没有启用)。

## 先决条件

在开始之前，你需要在你的机器上安装最新版本的 Visual Studio 代码。我们还将看到 Angular 5 片段。因此，至少有一个角度的基本知识将是有帮助的，但不是必要的。

您需要使用现有的或新的 Angular 5 项目来试验代码片段。我假设您有 Node.js 的最新版本，或者至少没有比 Node.js 6 旧的版本。如果您还没有安装 Angular CLI 工具，以下是安装该工具的命令:

```
npm install -g @angular/cli

# or
yarn global add @angular/cli 
```

## 片段解释

代码片段是允许您轻松插入重复代码的模板。当您第一次安装 VS Code 时，它预装了 JavaScript 代码片段。要检查它们，只需打开一个现有的 JavaScript 文件或在您的工作区中创建一个新文件，并尝试键入这些前缀:

*   `log`
*   `if`
*   `ifelse`
*   `forof`
*   `settimeout`

当您键入时，会出现一个弹出列表，为您提供自动完成代码的选项。一旦正确的代码片段被突出显示，只需按`enter`即可插入代码片段。在某些情况下，比如`log`和`for`，你可能需要按下向下键来选择正确的代码片段。

您可能想知道这些 JavaScript 代码片段到底是从哪里来的。你可以在以下位置找到确切的定义:

*   windows—`C:\Program Files\Microsoft VS Code\resources\app\extensions\javascript\snippets\javascript.json`
*   Linux—`/usr/share/code/resources/app/extensions/javascript/snippets/javascript.json`

我们将研究如何创建我们自己的代码片段，但首先让我们探索一些第三方代码片段。

## 如何使用片段

在撰写本文时， [Visual Studio Marketplace](https://marketplace.visualstudio.com/search?target=VSCode&category=Snippets&sortBy=Downloads) 在 snippet 类别中列出了 934 个扩展。你会发现每一种编程语言的代码片段，包括 Pascal！这意味着你可能应该在创建自己的代码片段之前查看一下市场。如前所述，我们将看看 Angular 5 片段。在您的工作空间中找到现有的 Angular 5 项目，或者创建一个新项目:

```
ng new snippets-demo 
```

一旦建立了项目，就在 VS 代码中打开它。接下来，点击活动栏上的扩展图标，打开*扩展*面板。搜索`Angular 5`。搜索结果应该列出几个角度延伸。安装有作者**‘约翰爸爸’**的那个。安装完成后，单击 reload 按钮重启 VS 代码。这个有角度的 5 片段扩展附带了 50 多个片段。大约 70%的代码片段是为 TypeScript 编写的，其余的大部分是为 HTML 编写的。里面也有一些 Docker 片段。

![Angular 5 Snippets](img/40bdf1c88985702cc31d73f402aa9302.png)

让我们试试这些有棱角的片段。在 app 文件夹中创建一个名为`app.service.ts`的新文件。打开文件，开始输入“a-service”。当您键入时，会出现一个弹出列表。甚至在您完成输入之前，您应该突出显示正确的选项。按下`enter`插入模板。输入代码片段后，请注意，生成的类名会突出显示，供您更改。

![angular-service](img/bd78fdfebe7ede33ea86edd332d72d47.png)

只需输入“App”，整个类名就会读作“AppService”。很方便，对吧？让我们尝试一些不同的东西。删除`app-service.ts`中的整个代码，并键入以下前缀:

```
a-service-httpclient 
```

您应该得到一个服务类定义，包括类构造函数中的导入和`HttpClient`注入。就像之前一样，将类名重命名为`AppService`。

![http-client-service](img/ebd737916c16f4604d993a2276d33ac8.png)

接下来，让我们使用另一个代码片段来定义一个 HTTP GET 请求。让我们定义一个空的 GET 方法。对于这段代码，你要打字；不要复制粘贴:

```
getPosts(): Observable<any[]> {

} 
```

当你开始输入“Observable”时，会出现一个片段选项。只需按下`enter`键，可观察类就会自动为您导入。

![Observable Snippet](img/eeb7943ed868c241cfbdc0e514fa43c8.png)

接下来，让我们使用另一个代码片段来完成该函数。开始输入这个前缀:“a-httpclient-get”。按下`enter`将为您插入以下代码片段:

```
return this.httpClient.get('url'); 
```

将 URL 更改为一个假想的路径—假设为`/posts`。显然我们的代码不会运行，因为我们还没有设置任何 API。通过在`get`后添加`<any>`来修复它。完整的方法现在看起来像这样。

```
getPosts(): Observable<any[]> {
  return this.httpClient.get<any[]>('/posts');
} 
```

现在我们知道了一点如何使用 Angular 5 片段，让我们看看它们是如何创建的。

## 如何创建角度 5 片段

Visual Studio 代码中使用的语法与 [TextMate](https://manual.macromates.com/en/snippets) 中定义的语法相同。事实上，你可以从 TextMate 中复制一个片段，它将在 VS 代码中工作。但是，请记住，VS 代码不支持“插入外壳代码”特性。

创建代码片段最简单的方法是通过命令面板(`ctrl` + `shift` + `p`)打开`Preferences:Configure User Snippet`。选择它，然后选取您想要为其创建语法的语言。现在让我们为 TypeScript 创建一个。打开`typescript.json`后，将这个代码片段模板放在左括号和右括号中:

```
"Print Hello World": {
  "prefix" : "hello",
  "body": [
    "console.log('Hello World!');"
  ],
  "description": "Print Hello World to console"
} 
```

让我看一下模板结构:

*   “打印 Hello World”—代码片段模板的标题。你可以放任何你想要的标题。
*   前缀-代码片段的触发关键字。
*   body —代码段将插入的代码。
*   描述—不需要解释这个。

这基本上是您可以编写的最简单的代码片段模板。保存文件后，创建一个空白的`typescript`并测试前缀`hello`。当您键入时，会出现一个弹出列表。

![hello-snippet-prefix](img/40f367624d949a2e29e845e4714e4380.png)

突出显示您的片段后，只需点击`enter`。应该为您插入以下代码:

```
console.log('Hello World'); 
```

太棒了，对吧？现在让我们通过插入一些**制表位**来使我们的代码片段模板具有交互性。

```
"Print Hello World": {
  "prefix" : "hello",
  "body": [
    "console.log('Hello $1!');",
    "$2"
  ],
  "description": "Print Hello World to console"
} 
```

现在再试试你的前缀。这个版本允许你插入你的名字。一旦你完成输入，只要按 tab 键，你的光标就会停留在新的一行。`$0`表示最后的制表位。您可以使用`$1`和更大的来创建多个制表位。但是如果我们想要插入一个默认值呢？我们可以使用如下的占位符:`${1:World}`。以下是完整的模板:

```
"Print Hello World": {
  "prefix" : "hello",
  "body": [
    "console.log('Hello ${1:World}!');",
    "$0"
  ],
  "description": "Print Hello World to console"
} 
```

现在，请再次尝试该片段。您可以更改默认值，也可以按 tab 键退出以保持不变。现在，让我们给开发人员一个选择列表:

![snippet-choice](img/3abfddcb4520531c847c78395a561ea9.png)

要实现选择列表，只需像这样替换`console.log`行:

```
...
"console.log('Hello ${1|World,Earth,Terra|}!');",
... 
```

这是足够的例子了。我还没有介绍变量名和转换。要了解更多，请查看 GitHub 上的[约翰爸爸的棱角片段](https://github.com/johnpapa/vscode-angular-snippets)。这里有一个预览:

```
"Angular HttpClient Service": {
    "prefix": "a-service-httpclient",
    "description": "Angular service with HttpClient",
    "body": [
      "import { Injectable } from '@angular/core';",
      "import { HttpClient } from '@angular/common/http';",
      "import { catchError } from 'rxjs/operators';",
      "",
      "@Injectable()",
      "export class ${1:ServiceName}Service {",
      "\tconstructor(private httpClient: HttpClient) { }",
      "\t$0",
      "}"
    ]
  } 
```

这是我们之前用来创建`HttpClient`服务的模板。我建议你浏览一下这个项目，因为它包含了很多非常有用的模板，你可以从中学习。

## 摘要

既然您已经学会了如何创建片段，您可能希望与他们的团队成员、朋友或公众分享它们。与您的团队成员共享代码片段的最简单方法是在您的项目根目录下创建一个`.vscode`文件夹。创建一个名为`snippets`的子文件夹，将所有模板放在那里，如下所示:

![team-share-snippets](img/289b5aa3cf2bd29ab674fc50f7110bbc.png)

确保提交此文件夹，以便每个人都可以在存储库中找到它。要与您的朋友和公众分享，您有多种选择:

*   你可以把你的代码片段上传到一个公共服务器上，比如 Google Drive、Dropbox 甚至 Pastebin。
*   您可以将代码片段作为扩展发布在 [VS 代码市场](https://marketplace.visualstudio.com/vscode)上。

我希望这篇关于使用代码片段的简要介绍能让你的编程生活变得更容易一些！

## 分享这篇文章