# 如何将 React 应用程序迁移到 TypeScript

> 原文：<https://www.sitepoint.com/how-to-migrate-a-react-app-to-typescript/>

当我刚开始学习 [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 的时候，我经常听到的一个建议是，“转换你现有的一个项目！是最好的学习方式！”不久之后，Twitter 上的一个朋友[提出要做](https://www.youtube.com/watch?v=nfFVygaWCuE)那样的事情——向我展示如何将 React 应用程序迁移到 TypeScript。

本文目的是成为您的朋友，帮助您将自己的项目迁移到 TypeScript。作为背景，我将使用我在经历这个过程时移植的个人项目的片段。

## 这个计划

为了让这个过程不那么令人生畏，我们将把它分成几个步骤，这样您就可以分块执行迁移。当我接受一项大任务时，我总是发现这很有帮助。以下是我们迁移项目的所有步骤:

1.  添加类型脚本
2.  添加`tsconfig.json`
3.  从简单开始
4.  转换所有文件
5.  增加严格性
6.  清理干净
7.  庆祝

注意:整个过程中最重要的一步是第 7 步。尽管我们只能通过按顺序完成它们来达到目的。

### 1.向项目中添加 TypeScript

首先，我们需要向我们的项目添加 TypeScript。假设您的 React 项目是用`create-react-app`引导的，我们可以[按照文档](https://create-react-app.dev/docs/adding-typescript/#installation)运行:

```
npm install --save typescript @types/node @types/react @types/react-dom @types/jest 
```

或者，如果您正在使用`yarn`:

```
yarn add typescript @types/node @types/react @types/react-dom @types/jest 
```

请注意，我们还没有对 TypeScript 做任何更改。如果我们运行命令在本地启动项目(在我的例子中是`yarn start`，应该没有什么不同。如果是这样，那太好了！我们准备好下一步了。

### 2.添加`tsconfig.json`

在我们能够利用 TypeScript 之前，我们需要通过`tsconfig.json`进行配置。我们开始的最简单方法是使用以下命令搭建一个:

```
npx tsc --init 
```

这为我们提供了一些基础知识。

我们还没有与 TypeScript 交互。我们只是采取了必要的行动来做好准备。我们的下一步是将文件迁移到 TypeScript。这样，我们就可以完成这一步，进入下一步。

### 3.从一个简单的组件开始

TypeScript 的美妙之处在于您可以逐步采用它。我们可以从这个迁移的第一部分的一个简单组件开始。对于我的项目，我将从一个如下所示的按钮组件开始:

```
import React from 'react'
import { buttonStyles } from './Button.styles'

const Button = ({ onClick, text }) => (
  <button onClick={onClick} className={buttonStyles}>  {text}  </button>
)

export default Button 
```

为了正确转换，我们需要做两件事:

1.  将文件扩展名更改为`.tsx`
2.  添加类型注释

因为这个组件需要两个道具，所以我们需要改变一些东西:

```
import React, { MouseEventHandler } from 'react'
import { buttonStyles } from './Button.styles'

type Props = {
  onClick: MouseEventHandler,
  text: string,
}

const Button = ({ onClick, text }: Props) => (
  <button onClick={onClick} className={buttonStyles}>
    {text}
  </button>
)

export default Button 
```

让我们通过运行项目来再次检查事情是否还在工作，以确保我们没有破坏任何东西。注意，这里的`react-scripts`会自动检测到新的变化，并为我们修改我们的`tsconfig.json`！瞧啊。那有多美？

如果一切顺利，我们的项目将保持工作状态。给自己一个鼓励！您已经成功地将第一个文件迁移到 TypeScript。如果我们想在这里停下来，我们可以，但让我们向前推进。

### 4.转换所有文件

下一步是做我们在步骤 3 中做的事情，但是是针对项目中的所有文件。如果您正在迁移的项目相当大，我建议通过多次迭代来完成。否则，你可能会累坏自己。

在此步骤中，您可能需要添加额外的包，这取决于您使用的第三方库。例如，我正在使用`moment`，所以我必须运行`yarn add -D @types/moment`来添加类型作为`devDependency`。

执行此操作时，请记住以下一些其他事项:

*   通过在错误前的行上添加`// @ts-ignore`来抑制类型脚本错误
*   如果文件使用 jsx(即`<App />`)，文件扩展名必须是`.tsx`而不是`.ts`
*   在本地运行项目，以确保一切仍在运行(它们应该是这样的)

完成这一步后，困难的事情就完成了！我们的项目将在 TypeScript 中，但我们需要增加严格性来利用这些好处。

### 5.增加`tsconfig.json`严格性

现在我们准备通过在我们的`tsconfig.json`中启用更严格的规则来增加严格性。幸运的是，`react-scripts`会在本地运行我们的项目时通知我们任何类型错误。我们将遵循这样的流程:

1.  启用规则
2.  在本地启动项目
3.  修复错误

我们将对以下规则重复这一过程:

*   `"noImplicitAny": true`
*   `"strictNullChecks": true`
*   `"noImplicitThis": true`
*   `"alwaysStrict": true`

我想分享一个小技巧。如果您发现某个东西隐式地具有类型`any`并且您不确定如何在那个时刻修复它，不要这样做。创建这个并使用它来掩盖错误:

```
export type FixMeLater = any 
```

我们的目标是快速前进，稍后再回头解决这些问题。

这将为我们的项目带来更大的类型安全性。如果你想阅读更多关于编译器选项的内容，你可以在 [TypeScript 手册](https://www.typescriptlang.org/docs/handbook/compiler-options.html)中阅读。

完成后，我们可以替换这些:

*   `"noImplicitAny": true`
*   `"strictNullChecks": true`
*   `"noImplicitThis": true`
*   `"alwaysStrict": true`

有了这个:

*   `"strict": true`

这也支持这些严格的选项:

*   strictBindCallApply
*   strictNullChecks
*   strictFunctionTypes
*   strictPropertyInitialization

在这一点上，我们已经在我们的项目中达到了标准的严格程度。如果我们想添加额外的检查，我们可以添加这些规则:

*   `"noUnusedLocals": true`
*   `"noUnusedParameters": true`
*   `"noImplicitReturns": true`
*   `"noFallthroughCasesInSwitch": true`

一旦我们达到了满意的严格程度，我们就可以进行下一步了。

### 6.清理快捷方式

如果你添加了`@ts-ignore`或者利用了`FixMeLater`类型，现在是时候回去修正它们了。我们不需要一次完成所有这些工作，但这将是确保项目中最大类型安全的最后一步。

有时，解决这些问题的努力不值得花时间，而其他时候却值得。你必须和你的团队讨论并决定什么是有意义的。

### 7.庆祝

我们做到了！我们正式将我们的项目迁移到 TypeScript。花点时间庆祝你的工作。这当然不是一项微不足道的任务。尤其是当你在一个大的代码库中工作的时候。

## 要记住的事情

当我们反思我们的努力时，在将一个项目从 React 迁移到 TypeScript 时，有一些事情需要记住。

### 从小处着手

利用 TypeScript 的能力来逐步采用它。按照你自己的速度一次处理一个文件。做对你和你的团队有意义的事情。不要试图一次解决所有问题。

### 随着时间的推移增加严格性

没必要一开始就要求最严格。这是一次旅行。有时间就提高水平。最终，你会达到一个感觉舒适的水平。如果你没有做到 100%的严格，不要难过。一些类型安全比没有类型安全要好。

### 依靠捷径

`@ts-ignore`和`FixMeLater`的提示有助于减轻迁移的负担。不是所有的事情都需要立刻改变。根据需要使用快捷方式，但不要因为使用它们而感到不舒服。还是那句话，重点是迁移，但不应该是痛苦的。随着时间的推移，您可以优先考虑用适当的类型安全替换这些东西。但是请记住，这些工具是由你支配的，所以使用它们。

这不是将 React 项目迁移到 TypeScript 的唯一方法。然而，这是对我有用的。我希望它能像帮助我一样帮助你。

### 进一步阅读

*   [与打字稿反应:最佳实践](https://www.sitepoint.com/react-with-typescript-best-practices/)
*   [提升打字技能的实用方法](https://www.sitepoint.com/advance-typescript-skills-practical-ways/)
*   【TypeScript 如何让你成为更好的 JavaScript 开发人员
*   [JavaScript:从新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/)
*   [React 和 React Native 第二版](https://www.sitepoint.com/premium/books/react-and-react-native-second-edition/)

*特别感谢[卡尔·霍基](https://twitter.com/karlhorky)，他解释说不推荐“React.FC”类型，因为它几乎没有任何好处，而且还有一些缺点。更多信息见 [GitHub 讨论](https://github.com/facebook/create-react-app/pull/8177)。*

## 分享这篇文章