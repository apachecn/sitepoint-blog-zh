# TypeScript 简介:Web 静态类型

> 原文：<https://www.sitepoint.com/introduction-to-typescript/>

TypeScript 是用 JavaScript 创造更好体验的众多尝试之一。

TypeScript 是 JavaScript 的强类型超集，这意味着它为语言增加了一些语法上的好处，同时仍然允许您编写普通的 JavaScript。它通过诸如接口和静态类型(稍后将详细介绍)之类的东西鼓励更具声明性的编程风格，提供模块和类，最重要的是，与流行的 JavaScript 库和代码集成得相当好。你可以把它看作是当前 JavaScript 上的一个强静态层，它有一些特性可以让生活(尤其是调试)变得更容易忍受。

TypeScript 在几年前获得了特别的关注，因为它被 Angular 2 和后续版本(也是用 TypeScript 本身编写的)选择为完全支持。它也是由微软开发的，这意味着它有两家主要技术公司的支持(对任何语言来说都不是一个坏地方)。从那时起，它获得了更多的追随者和主流地位。

不用说，TypeScript 绝对值得研究。

## 它是如何工作的？

TypeScript 实际上看起来很像现代的 JavaScript。在最基本的层面上，它为 JavaScript 引入了一个[静态类型](https://www.sitepoint.com/typing-versus-dynamic-typing/)范例，所以不用下面的:

```
var name = “Susan”,
    age = 25,
    hasCode = true; 
```

我们可以这样写:

```
let name: string = "Susan",
    age: number = 25,
    hasCode: boolean = true; 
```

如你所见，这里没有太大的区别。我们所做的就是明确地告诉系统每个变量是什么类型；我们从一开始就告诉它，`name`是一个字符串，`age`是一个数字。但这似乎意味着我们必须编写更多的代码。为什么要告诉系统这么具体的信息呢？因为它给了系统更多关于我们程序的信息，这反过来意味着它可以捕捉我们以后可能会犯的错误。

例如，假设您的代码中有这样的内容:

```
var age = 25;
age = "twenty-five"; 
```

像这样变异一个变量并改变它的类型很可能会破坏其他地方的东西，特别是在一个非常大的程序中，所以如果编译器能在我们把它加载到浏览器中之前发现这一点，那就太好了，我们不得不花半个小时自己寻找这个问题。基本上，它使我们的程序更加安全，不会出现错误。

不过，还有更多。这里有一个来自 TypeScript 网站介绍教程的例子(你可以在这里找到):

```
interface Person {
    firstname: string;
    lastname: string;
}

function greeter(person : Person):string {
    return "Hello, " + person.firstname + " " + person.lastname;
}

let user = {firstname: "Jane", lastname: "User"};

document.body.innerHTML = greeter(user); 
```

现在这里有一些比以前更不寻常的东西。我们有一个名为`user`的普通对象，包含名和姓，它被传递给`greeter()`，输出被插入到文档的主体中。但是在`greeter`函数的参数中有一些看起来很奇怪的东西，还有一个叫做`interface`的东西。

让我们从`greeter`函数开始:

```
function greeter(person: Person):string {
    return "Hello, " + person.firstname + " " + person.lastname;
} 
```

我们可以看到，`greeter`接受了一个`person`参数，我们希望它的类型是`Person`。这样，我们可以确信当我们询问那个人的名字时，它一定会出现，如果它失败了，我们也不会让自己头疼。函数参数后面的`:string`告诉我们，当我们调用这个函数时，我们期望它返回什么类型。

函数的主体并不复杂，但是，当然，现在你可能想知道`Person`类型究竟是什么。这就是`interface`特性的用武之地:

```
interface Person {
    firstname: string;
    lastname: string;
} 
```

接口在 TypeScript 中用于定义对象的结构(并且只定义对象)。在这个例子中，我们说任何类型为`Person`的变量必须是包含一个`firstname`和一个`lastname`属性的对象，两者都是字符串类型。我们基本上是在为我们的对象创建一个自定义类型。

这很有用，因为它告诉编译器，以及你自己和将来从事这项工作的任何开发人员，确切地知道预期的数据类型。我们基本上是在*建模*对象属性，创建一些我们以后需要调试时可以参考的东西。这通常是为什么你会在 TypeScript 文件的顶部看到接口，因为它们让我们很好地了解程序在文件的其余部分处理的数据。

在我们的例子中，如果我们在程序的任何地方使用这个带有变量的`Person`接口，而它*既不包含`firstname`也不包含`lastname`，*既包含`string`类型的*(幸运的是我们的`user`对象包含)，那么编译器将会抱怨我们，我们将被迫改正我们的做法。*

不仅如此，拥有静态类型意味着支持 TypeScript 的 IDE 或编辑器将能够为我们提供非常好的、非常具体的提示和自动完成功能，以便我们能够开发更快、更安全的代码。

TypeScript 允许我们使用更多的特性，比如泛型和名称空间，所以强烈建议至少快速阅读一下它们的[文档](http://www.typescriptlang.org/docs/)。

## 我如何设置它？

因为 TypeScript 是 JavaScript 的超集，如果我们想在浏览器中使用它，我们需要将它转换成 JavaScript。令人欣慰的是，它已经很好地集成了许多任务运行器和打包器。

如果你只是想先在本地使用它，你可以通过 [npm](http://www.typescriptlang.org/docs/tutorial.html#installing-typescript) 全局安装 TypeScript，并在命令行使用`tsc`命令，就像这样:

```
tsc your-typescript-file.ts 
```

这将输出一个 JavaScript 文件，在本例中称为`your-typescript-file.js`，然后您可以像往常一样在浏览器中使用它。然而，在项目中设置它几乎肯定需要设置一个合适的`tsconfig.json`。

该文件表示该项目是一个 TypeScript 项目，并允许我们设置许多配置选项。这里有一个从文档中截取的例子:

```
{
    "compilerOptions": {
        "module": "commonjs",
        "outFile": "./build/local/tsc.js",
        "sourceMap": true
    },
    "exclude": [
        "node_modules"
    ]
} 
```

这里我们以多种方式配置编译器。我们将指定一个模块系统来编译，当编译完成时将编译好的文件放在哪里，并包含一个源映射。我们也给了它一个`exclude`选项，它基本上告诉编译器编译任何类型脚本文件——那些以`.ts`结尾的——只要它们不在`node_modules`文件夹中，它就会找到。

从这里，我们可以将东西集成到我们最喜欢的任务运行器或捆绑器中。咕哝和吞咽[都有用于打字稿的插件，这些插件将为你的任务运行者展示编译器选项。Webpack 有一个*很棒的*](https://www.npmjs.com/package/gulp-typescript) [类型脚本加载器](https://github.com/s-panferov/awesome-typescript-loader)，对其他一些设置也有很好的支持。基本上，您可以毫不费力地将 TypeScript 集成到您当前正在进行的几乎任何工作流中。

### 外部打字

如果您在项目中使用外部库(老实说，谁不是呢？)您可能还需要一些类型定义。这些定义——用一个`.d.ts`扩展表示——让我们可以访问其他人为许多 JavaScript 库编写的接口。总的来说，这些定义可以在一个名为 DefinitelyTyped 的巨大 repo 中获得，我们就是从这个 repo 安装它们的。

要使用它们，你需要安装[类型](https://github.com/typings/typings)，这有点像 npm，但用于类型脚本类型定义。它有自己的配置文件，名为`typings.json`，您可以在其中配置类型定义安装的包和路径。

我们在这里不会涉及太多细节，但是如果我们想要使用 AngularJS 1.x 类型，例如，我们可以简单地进入`typings install angularjs --save`并让它们下载到在`typings.json`中定义的路径中。此后，您可以在项目中的任何地方使用 Angular 的类型定义，只需包含以下代码行:

```
/// <reference path="angularjs/angular.d.ts" /> 
```

现在，我们可以使用如下的角度类型定义:

```
var http: ng.IHttpService; 
```

任何开发人员在后期(或者我们自己，在我们写完代码三个月后)偶然发现我们的代码，通过查看他们，将能够对我们写的东西有更多的理解。

## 好吧，那社区呢？

随着该语言的采用，TypeScript 社区正在继续发展。也许最重要的是，Angular 2+就是用它编写的，而且框架从一开始就提供了对它的全面支持。微软 Visual Studio IDE 和 Visual Studio 代码中也对其语法提供了出色的支持，编辑器的包和插件也很容易获得，如 [Atom](https://atom.io/packages/atom-typescript) 、 [Sublime Text](https://github.com/Microsoft/TypeScript-Sublime-Plugin) 和 [Emacs](https://github.com/ananthakumaran/tide) 。

这意味着围绕 TypeScript 有大量的活动，所以这是您需要关注的事情。

## 进一步阅读

*   [官方打字稿网站](http://www.typescriptlang.org/)
*   [明确类型化](http://definitelytyped.org/) —第三方类型脚本定义

## 结论

TypeScript 引入了一个静态类型系统，通过接口和类型联合完善了 JavaScript 的缺点，这是一个有趣的改进。这有助于我们编写更安全、更易读和声明性的代码。

它与目前几乎所有主流的构建设置都集成得很好，甚至还让我们能够创建和使用定制类型。还有无数的 ide 和文本编辑器对它的语法和编译过程提供了强大的支持，因此您可以在自己选择的编码环境中使用它，而不会有什么痛苦或过程。

也许最重要的是，TypeScript 是 Angular 2+的重要组成部分，这意味着我们将在未来继续看到它。我们对它及其工作原理了解得越多，当它成为 JavaScript 的成熟主流替代品时，我们就能更好地应对它。

你有没有在下一个项目中使用 TypeScript 的灵感？强类型是 JavaScript 的未来，还是只是一种时尚？下面让我知道你的想法！

## 分享这篇文章