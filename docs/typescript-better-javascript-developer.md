# TypeScript 如何让你成为更好的 JavaScript 开发人员

> 原文：<https://www.sitepoint.com/typescript-better-javascript-developer/>

[Airbnb](https://www.reddit.com/r/typescript/comments/aofcik/38_of_bugs_at_airbnb_could_have_been_prevented_by/) 、 [Google](http://neugierig.org/software/blog/2018/09/typescript-at-google.html) 、 [Lyft](https://eng.lyft.com/typescript-at-lyft-64f0702346ea) 和 [Asana](https://blog.asana.com/2014/11/asana-switching-typescript/) 有什么共同点？他们都将几个代码库迁移到了 TypeScript。

无论是吃得更健康，锻炼身体，还是睡得更多，我们人类都热爱自我提升。这同样适用于我们的职业生涯。如果有人分享了提高程序员水平的技巧，你的耳朵会竖起来。

在这篇文章中，我们的目标是成为那个人。我们知道 [TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 会让你成为更好的 JavaScript 开发者，原因有几个。写代码的时候你会觉得很自信。生产代码中出现的错误会更少。重构代码会更容易。您将编写更少的测试(耶！).总的来说，您将在编辑器中获得更好的编码体验。

## 什么是打字稿？

TypeScript 是一种编译语言。你写 TypeScript，它编译成 JavaScript。本质上，你是在写 JavaScript，但是用的是类型系统。JavaScript 开发人员应该有一个无缝的过渡，因为语言是相同的，除了一些怪癖。

以下是 JavaScript 和 TypeScript 中函数的一个基本示例:

```
function helloFromSitePoint(name) {
    return `Hello, ${name} from SitePoint!`
} 
```

```
function helloFromSitePoint(name: string) {
    return `Hello, ${name} from SitePoint!`
} 
```

请注意这两者几乎是一样的。不同之处在于 TypeScript 中“name”参数上的类型批注。这告诉编译器，“嘿，确保当有人调用这个函数时，他们只传入一个字符串。”我们不会深入探讨，但是这个例子应该说明了最基本的 TypeScript。

## TypeScript 将如何让我变得更好？

TypeScript 将通过以下方式提高您作为 JavaScript 开发人员的技能:

*   给你更多的自信，
*   在错误进入生产之前捕捉它们，
*   使重构代码变得更加容易，
*   节省您编写测试的时间，
*   为您提供更好的编码体验。

让我们更深入地探讨一下每一个问题。

### 更加自信

在不熟悉的代码库和大型团队中工作时，TypeScript 会增强你的信心。如果您了解 TypeScript，并且您加入了一个使用 TypeScript 的新团队或新项目，您会感到不那么担心。你知道打字稿会帮你一把。这种语言为代码提供了更多的可读性和可预测性，因为您可以看到一些东西并立即推断出它是如何工作的。这是类型系统的直接结果。

函数参数是带注释的，因此 TypeScript 知道您传递的值的有效类型。

```
type Color = "red" | "blue" | "green"

// Here, you know color must be of type "Color", meaning one of the three options
function printColor(color: Color) {
  console.log(`The color you chose was: ${color})
} 
```

函数返回类型要么被推断，要么被注释。

```
function sum(a: number, b: number) { // TS infers the return type as number
  return a + b
}

function minus(a: number, b: number): number { // We annotate the return type as number
  return a - b
} 
```

通常使用 TypeScript，您的队友的代码是不言自明的。他们不需要向你解释，因为类型给代码添加了上下文。这些特性让你更加信任团队。你在更高的水平上运作，因为你花更少的时间担心愚蠢的错误。对您的代码来说也是如此。TypeScript 强制您编写显式代码。副作用是代码质量的即时提高。最后，作为一名 JavaScript 开发人员，您会发现自己对使用 TypeScript 更有信心。

### 更少的生产错误

TypeScript 将在编译时而不是运行时捕捉可能的生产错误。当你写代码时，如果做错了，TypeScript 会对你大喊大叫。例如，看看这个例子:

![](img/3292a5f3fdccdd408579e9946226aa14.png)

注意到`colors`的红色曲线了吗？那是因为我们在上面调用了`.forEach`，但它可能是`undefined`。这可能会导致生产出错。幸运的是，TypeScript 在我们编写代码时告诉我们，除非我们修复它，否则不会编译。作为一名开发人员，你应该是抓住这一点的人，而不是你的用户。TypeScript 几乎总是会消除这些类型的错误，因为您在代码编译时会看到它们。

### 更容易重构

使用 TypeScript 重构代码变得更加容易，因为它会为您捕捉错误。如果你重命名一个函数，如果你忘记在某个地方使用新名字，它会让你知道。当您更改接口或类型的形状并移除您认为没有使用的属性时，TypeScript 会纠正您。你对你的代码做的任何改变，TypeScript 都会被你背后的人说，“嘿。你忘了改第 142 行的名字。”我听说有人称之为“持续重构”，因为你可以快速重构代码库的大部分。这是一个美丽的东西，并证明更易于维护的未来。

### 更少的单元测试

TypeScript 不再需要某些单元测试，如函数签名测试。以这个函数为例:

```
interface User {
  name: string;
  age: number;
}

function getAge(user: User) {
  return user.age
} 
```

我们不再需要单元测试来确保用适当类型的值调用`getAge`。如果开发人员试图用一个数字调用`getAge`，TypeScript 将抛出一个错误，告诉我们类型不匹配。因此，这允许我们花更少的时间编写简单的单元测试，花更多的时间编写我们更喜欢的东西。

### 编辑器中更好的编码体验

TypeScript 将使您受益最大的领域之一是通过自动完成和“未来”JavaScript 提高生产率。包括 Atom、Emacs、Vim、VSCode、Sublime Text 和 Webstorm 在内的大多数主流 ide 和编辑器都有用于类型脚本工具的插件。在本节中，我们将参考 VScode 中的一些可用特性。

第一个能提高你工作效率的特性是自动完成。这是当你在寻找一个类或对象的方法或属性的时候。如果 TypeScript 知道形状，它可以自动完成名称。这里有一个例子:

![](img/e28d80bcfec916290b85c28f7b06a358.png)

请注意，我还没有输入完`myFriend`的属性。这里，您看到 TypeScript 开始建议属性名，因为它知道形状匹配`User`。

我在写一个叫`printUser`的函数。我想将用户的全名记录到控制台。我去定义`lastName`，看到一条红色的曲线。在我的编辑器中将鼠标悬停在它上面时，TypeScript 告诉我，“属性‘last name’在类型‘User’上不存在。这个超级有帮助！它替我抓住了我愚蠢的错误。很漂亮，对吧？

改善我们体验的第二个特性是 TypeScript 能够让您编写“未来”的 JavaScript。通常，我们需要多个巴别塔插件来做到这一点。另一方面，TypeScript 提供了同样的功能，但代价是一个依赖项。TypeScript 团队在遵循 ECMAScript 规范方面做得非常好，添加了 Stage 3 及以上的语言特性。这意味着您可以利用新添加到 JavaScript 中的内容，而不会有过多的依赖或配置。这样做会让你领先于你的 JavaScript 同行。这两个特性结合起来将会提高 JavaScript 开发人员的效率。

## 我从哪里开始？

如果您对开始使用 TypeScript 感兴趣，可以从几个地方开始，这取决于您如何学习得最好。

*   [**5 分钟内打字完毕**](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html) 。TypeScript 手册快速入门指南将为您提供该语言的实践经验。它会带你了解这门语言的基本特征。你所需要的只是五分钟，一个编辑和学习的意愿。
*   [**打字稿简介**](https://www.sitepoint.com/introduction-to-typescript/) 。如果您想更进一步，我们推荐这篇初学者友好的文章，它将涵盖一些基本概念并让 TypeScript 在本地运行。
*   [**鲍里斯·车尔尼**的编程打字稿](https://www.amazon.com/Programming-TypeScript-Making-JavaScript-Applications/dp/1492037656)。对于那些喜欢深入的人来说，我们指的是深入，看看鲍里斯·切尔尼的这本奥莱利的书。它涵盖了从基础到高级语言功能。如果你想让你的 JavaScript 技能更上一层楼，我们强烈推荐你这么做。

## 自己出去试试吧！

听取他人的意见很重要，但没有什么比根据经验形成自己的观点更重要了。我们知道 TypeScript 将提高您的信心，帮助您更快地捕捉错误和重构代码，并提高您的整体生产率。现在出去，试着自己打字，让我们知道你的想法！

## 更多的打字稿即将推出！

如果你喜欢这篇文章，你会很高兴听到我们有更多的打字稿文章。在接下来的几个月里保持警惕。我们将讨论诸如开始使用 TypeScript 和在 React 等技术中使用它这样的主题。在那之前，编码快乐！

## 分享这篇文章