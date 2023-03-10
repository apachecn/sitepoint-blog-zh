# 类型脚本与 JavaScript:你应该使用哪一个，为什么

> 原文：<https://www.sitepoint.com/typescript-vs-javascript/>

本文将探讨 TypeScript 和 JavaScript:它们如何比较，以及何时使用 TypeScript 和 JavaScript。我们将探究 TypeScript 实际上是什么，它为什么存在，以及它的优点和缺点是什么。

## 什么是 TypeScript？

[TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 是微软开发和维护的开源编程语言。这是 JavaScript 的一个超集:任何 JavaScript 代码都在一个类型脚本环境中运行。

创建 TypeScript 是为了解决用 JavaScript 开发大规模应用程序时出现的问题。这些类型的应用程序包含数百个不同的文件。进行一项更改会影响多个文件的行为。由于 JavaScript 无法验证这些文件之间的连接，开发人员有很大的空间犯错误，导致生产中的错误。这就是为什么需要像 TypeScript 这样包含静态类型检查的前端语言。

TypeScript 是一种编译语言，不像 JavaScript 是一种解释语言。您需要将 TypeScript 编译成 JavaScript，以便在浏览器中运行。要将 TypeScript 编译成 JavaScript，可以使用 [TypeScript npm 包](https://www.npmjs.com/package/typescript)。

如果您来自像 C#或 Java 这样的面向对象语言，您会在 TypeScript 添加的列表中找到许多熟悉的特性。我最喜欢的包括添加枚举、接口、访问修饰符、名称空间、泛型等等。

由于许多外部 npm 库是用 JavaScript 编写的，TypeScript 需要一种方法来对它们进行类型检查。这就是类型声明文件(`.d.ts`文件)发挥作用的地方。这些类型声明为类型定义提供了有关代码的信息。编译器使用它们对类型脚本代码进行类型检查。如今，大多数 JavaScript 库都有专门为它们编写的 TypeScript 定义。即使库缺少这些定义，TypeScript 有时也可以推断出类型。或者，您可以自己快速编写定义。

TypeScript 非常适合大型 web 应用程序，因为您可以用它编写前端和后端代码。在后端，你需要安装一个名为 [ts-node](https://www.npmjs.com/package/ts-node) 的库，在 [Node.js 环境](https://www.sitepoint.com/how-to-use-typescript-to-build-a-node-api-with-express/)中执行 TypeScript 代码。

## TypeScript 相对于 JavaScript 的优势是什么？

2022 年，TypeScript 加入了全球[最常用的 5 种语言](https://survey.stackoverflow.co/2022/#technology-most-popular-technologies)。这并不令人惊讶，因为 TypeScript 不断地开发、添加新功能，并且有一个 TypeScript 的团队倾听的奇妙的开发人员社区。

与常规 JavaScript 相比，TypeScript 最显著的优势是它的编译器，它提供了类型和错误检查。它检查类型有效性并实时显示错误。这在重构代码时特别有用，因为编译器会向您展示您可能遗漏的内容。

![TypeScript vs JavaScript: A TypeScript compiler message over a code snippet](img/d616699bcac4503b4d2588c61162a3c7.png)

当与较新的 IDE(如 Visual Studio 代码)一起使用时，TypeScript 提供了强大的智能感知功能，如代码提示和代码完成。这些特性有助于提高程序的开发速度。

使用`tsconfig`文件，开发人员可以配置 TypeScript 的行为。例如，TypeScript 提供了一个将代码转换为以前版本的 JavaScript 的选项，因此代码可以在所有浏览器上运行。开发人员的另一个选择是 TypeScript 的严格模式，它增加了更多的检查来确保代码的正确性。

另一个优秀的 TypeScript 特性是它能够与 JavaScript 代码并行运行，这使得它更容易在具有许多 JavaScript 文件的遗留项目中实现。

此外，TypeScript 允许使用一些主流浏览器尚不支持的 ES6 和 ES7 候选特性。例如，可选的链接操作符和[类常量](https://timmousk.com/blog/typescript-class-constants/)早在 ECMAScript 正式支持它们之前就在 TypeScript 中可用了。

最后，TypeScript 提供了许多 JavaScript 所缺乏的特性，这使得编码变得更加有趣和愉快。诸如接口、枚举和泛型等特性极大地提高了代码的可读性。

## TypeScript vs JavaScript 的缺点是什么？

尽管 TypeScript 提供了多种优势，但它也带来了一些缺点。这些缺点都不是交易的破坏者，但是一个新的开发者需要意识到它们并且考虑它们。

TypeScript 最大的缺点是它给用户带来了虚假的安全感。事实上，当开发人员开始使用 TypeScript 时，他们经常过于依赖它的编译器，希望它能标记出每一个类型错误。你不应该期望你的类型代码是 100%可靠的。这是 TypeScript 的开发团队做出的选择:希望 TypeScript 在灵活性和正确性之间取得平衡。虽然 TypeScript 并不是防弹的，但在我看来，它仍然比普通的 JavaScript 代码好，因为它的编译器会发现您在其他情况下不会发现的错误。事实上，据估计，TypeScript 比 JavaScript 多发现 15%的错误。

TypeScript 的另一个缺点是需要额外的编译步骤。这个步骤会降低构建时间，并使捆绑器设置变得复杂。

由于 TypeScript 添加了许多前端开发人员可能不知道的新功能，它增加了代码库的学习曲线。事实上，当充分发挥其潜力时，对于未经培训的开发人员来说，理解 TypeScript 可能很困难，并且需要大量的谷歌搜索或来自年长队友的指导。

![TypeScript vs JavaScript: Complex TypeScript code](img/abba2008da346f66c9bafa3ead12aee0.png)

最后，一些开发人员抱怨说，使用 TypeScript 需要添加大量额外的代码来定义类型。虽然这是真的，但从长远来看，这可以节省时间，因为让新的项目成员加入更简单。这也使得代码库的重构更加容易。

## 什么时候应该为新项目使用 TypeScript？

采用一项你从未有过工作经验的新技术可能会令人望而生畏。然而，使用 TypeScript，它值得一试，因为它的优点大于缺点。

如果您正在独自进行一个项目，并且不打算从外部获得更多帮助，我建议您为下一个 web 应用程序项目选择 TypeScript。尽管设置和最初几个小时的开发可能很棘手，但您会很快爱上 TypeScript，并且永远不想再回到普通的 JavaScript。最初，您编写代码的速度会慢一些，但是从长远来看，TypeScript 将节省您调试和重构的时间。

如果你和队友一起做一个项目，决策会更复杂，因为这需要团队和管理层的同意。尽管最终使用 TypeScript 会对您的团队有所帮助，但它会在短期内减慢开发速度。此外，团队需要准备花一些时间学习 TypeScript、它的特性和它的[最佳实践](https://www.sitepoint.com/react-with-typescript-best-practices/)。在这种情况下，拥有一个使用 TypeScript 或其他面向对象编程语言(如 C#)的有经验的团队成员将有助于团队平稳过渡。

如果您的团队准备牺牲短期的性能损失和学习 TypeScript 的初始时间，我建议在您的项目中使用 TypeScript。你不会后悔的。

## 什么时候不应该对新项目使用 TypeScript？

即使 TypeScript 总的来说很棒，我仍然有理由不推荐使用它。

不使用 TypeScript 的最大原因是如果您或您的团队必须遵守紧迫的截止日期。截止日期已经够紧张的了，不建议增加你没有工作经验的新技术。不幸的是，[学习 TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 需要时间，这些时间用在有期限的项目上可能更好。

此外，TypeScript 对于[配置](https://www.sitepoint.com/premium/courses/setting-up-and-kick-starting-typescript-2933)可能具有挑战性，尤其是对于初学者。这可能需要安装多个 npm 库并使用一个捆绑器(如 webpack)。如果你不准备花时间学习所有这些新信息，就不要使用它。

还有一点要考虑的是，使用 TypeScript 会提高开发者的入门门槛。如果项目是开源的，这会增加开发人员参与的难度。

同样，对于经理和招聘人员来说，在项目中使用 TypeScript 意味着你雇佣的开发人员必须有使用 TypeScript 或另一种 <abbr title="object-oriented programming">OOP</abbr> 语言的经验。采用 TypeScript 提高了参与项目的最低工作资格，并且可以增加项目的预算，因为您需要雇用更有经验的开发人员。

## TypeScript vs JavaScript:遗留项目呢？

将使用常规 JavaScript 编写的遗留项目迁移到 TypeScript 会给项目开发生命周期带来很多好处。它有助于发现您没有注意到的 bug，并简化项目的维护。

对于较小的项目，从 JavaScript 迁移到 TypeScript 可以通过安装所需的库、将文件扩展名从`.js`更改为`.ts`并修复 TypeScript 编译器输出的错误来轻松完成。

对于包含数百个不同 JavaScript 文件的项目来说，这可能会更复杂。幸运的是，您可以逐步采用 TypeScript。您可以优先将一些文件迁移到 TypeScript，并与遗留的 JavaScript 代码并行运行。

另一种选择是保留遗留代码，只在 TypeScript 中编写新特性。当一个特性涉及到遗留代码的一部分时，您可以在处理它的同时迁移它。

## 类型脚本 vs JavaScript:判决

在这个 TypeScript 与 JavaScript 的比较中，您已经看到了 TypeScript 是一种优秀的面向对象语言，它将帮助您更高效地构建大型应用程序。

尽管它有一些缺点，比如增加了代码的复杂性或者增加了编译时间，但是从长远来看，它将会节省您的时间。

如果您独自工作，我强烈建议您使用 TypeScript 构建您的下一个应用程序。

如果您与团队合作，采用 TypeScript 可能需要一些说服。我鼓励你在空闲时间开始构建一个[打字项目](https://www.sitepoint.com/monorepo-nx-next-typescript/)。然后，您可以向您的队友展示它，并解释 TypeScript 的所有优点。

## 分享这篇文章