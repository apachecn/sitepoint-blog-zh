# 如何在 JavaScript 中使用高阶函数

> 原文：<https://www.sitepoint.com/higher-order-functions-javascript/>

**把另一个函数作为自变量，或者把一个函数定义为返回值的函数，叫做高阶函数。**

JavaScript 可以接受高阶函数。这种处理高阶函数的能力，以及其他特性，使得 JavaScript 成为非常适合[函数式编程](https://www.sitepoint.com/what-is-functional-programming/.)的编程语言之一。

## JavaScript 将函数视为一等公民

你可能听说过 JavaScript 函数是一等公民。这意味着 JavaScript 中的函数是对象。

它们的类型是`Object`，它们可以被赋值为变量的值，它们可以像其他引用变量一样被传递和返回。

一级函数赋予 JavaScript 特殊的能力，使我们能够从高阶函数中获益。

因为函数是对象，所以 JavaScript 是支持函数式编程的自然方法的流行编程语言之一。

事实上，一级函数是 JavaScript 方法的固有特性，我敢打赌，您一直在使用它们，甚至没有考虑过它。

## 高阶函数可以接受一个函数作为参数

如果您做过很多 JavaScript web 开发，您可能会遇到使用回调的函数。

回调函数是在所有其他操作完成后，在操作结束时执行的函数。

通常，我们最后传递这个函数作为参数，在其他参数之后。它通常被内联定义为匿名函数。回调函数依赖于 JavaScript 处理高阶函数的能力。

JavaScript 是一种单线程语言。这意味着一次只能执行一个操作。

为了避免操作相互阻塞或阻塞系统的主线程(这会导致死锁)，引擎确保所有操作按顺序执行。它们沿着这个单线程排队，直到可以安全地进行另一个代码事务。

将函数作为参数传入并在父函数的其他操作完成后运行它的能力对于支持高阶函数的语言来说至关重要。

JavaScript 中的回调函数允许异步行为，因此脚本可以在等待结果的同时继续执行其他函数或操作。

当处理可能在一段不确定的时间后返回结果的资源时，传递回调函数的能力至关重要。

这种高阶函数模式在 web 开发中非常有用。脚本可能会向服务器发送请求，然后需要处理到达的响应，而不需要了解服务器的网络延迟或处理时间。

Node.js 经常使用回调函数来有效利用服务器资源。这种异步方法在应用程序执行功能之前等待用户输入的情况下也很有用。

### 示例:向元素事件侦听器传递警报函数

考虑这个简单的 JavaScript 片段，它向按钮添加了一个事件监听器。

```
So Clickable

document.getElementById("clicker").addEventListener("click", function() {
alert("you triggered " + this.id);
}); 
```

该脚本使用匿名内联函数来显示警报。

但是它也可以很容易地使用一个单独定义的函数，并将这个命名的函数传递给`addEventListener`方法:

```
var proveIt = function() {
alert("you triggered " + this.id);
};

document.getElementById("clicker").addEventListener("click", proveIt);
```

通过这样做，我们不仅仅展示了高阶函数。我们已经使我们的代码更具可读性和弹性，并将不同任务的功能分开(监听点击和警告用户)。

### 高阶函数如何支持代码重用

我们的小`proveIt()`函数在结构上独立于它周围的代码，总是返回被触发的任何元素的`id`。这种函数设计方法是函数式编程的核心。

这段代码可以存在于任何使用元素的`id`显示警告的上下文中，并且可以用任何事件监听器调用。

用单独定义和命名的函数替换内联函数的能力打开了一个可能性的世界。

在函数式编程中，我们试图开发不改变外部数据的纯函数，并且每次对相同的输入返回相同的结果。

现在，我们有了一个重要的工具来帮助我们开发一个小型的、有针对性的高阶函数库，您可以在任何应用程序中通用。

### 注意:传递函数和传递函数对象

注意，我们将`proveIt`而不是`proveIt()`传递给了我们的`addEventListener`函数。

当你通过名字传递一个没有括号的函数时，你传递的是函数对象本身。

当您用括号传递它时，您传递的是执行该函数的结果。

## 将函数作为高阶函数的结果返回

除了将函数作为参数，JavaScript 还允许函数返回其他函数作为结果。

这是有意义的，因为函数只是对象。对象(包括函数)可以定义为函数的返回值，就像字符串、数组或其他值一样。

但是返回一个函数的结果是什么意思呢？

函数是分解问题和创建可重用代码的强大方法。当我们将一个函数定义为高阶函数的返回值时，它可以作为新函数的模板！

这打开了通向另一个功能 JavaScript 魔法世界的大门。

说你读了太多关于千禧一代的文章，感到厌倦了。你决定每次都用短语*蛇人*替换*千禧一代*这个词。

您可能只想编写一个函数，对您传递给它的任何文本执行文本替换:

```
var snakify = function(text) {
return text.replace(/millenials/ig, "Snake People");
};
console.log(snakify("The Millenials are always up to something."));
// The Snake People are always up to something. 
```

这是可行的，但它只适用于这一种情况。也许你的耐心已经超过了关于婴儿潮一代的文章。您还想为它们创建一个自定义函数。

但是，即使是这样一个简单的函数，当您可以从一个更高阶的函数开始时，您也不想重复您已经编写的代码。

```
var hippify = function(text) {
return text.replace(/baby boomers/ig, "Aging Hippies");
};
console.log(hippify("The Baby Boomers just look the other way."));
// The Aging Hippies just look the other way. 
```

但是，如果您决定做一些更有趣的事情来保留原始字符串中的案例，该怎么办呢？为此，您必须修改这两个新函数。

这是一个麻烦，它使你的代码更脆弱，更难阅读。在这种情况下，我们可以使用高阶函数作为解决方案。

### 构建模板高阶函数

您真正想要的是能够在模板函数中用任何其他术语替换任何术语的灵活性，并将该行为定义为一个基本函数，您可以从该函数构建新的自定义函数。

由于能够将函数指定为返回值，JavaScript 提供了使这种情况更加方便的方法:

```
var attitude = function(original, replacement, source) {
return function(source) {
return source.replace(original, replacement);
};
};

var snakify = attitude(/millenials/ig, "Snake People");
var hippify = attitude(/baby boomers/ig, "Aging Hippies");

console.log(snakify("The Millenials are always up to something."));
// The Snake People are always up to something.
console.log(hippify("The Baby Boomers just look the other way."));
// The Aging Hippies just look the other way. 
```

我们所做的是将做实际工作的代码分离成一个通用的、可扩展的`attitude`函数。它封装了使用原始短语作为初始值来修改任何输入字符串所需的所有工作，并输出带有某种态度的替换短语。

当我们将这个新函数定义为对高阶函数`attitude`的引用，并预先填充了它的前两个参数时，我们会得到什么？它允许新函数接受您传递给它的任何文本，并在我们定义为`attitude`函数输出的返回函数中使用该参数。

JavaScript 函数不关心你传递给它们的参数的数量。

如果缺少第二个参数，它会将其视为未定义。当我们选择不提供第三个参数或任何数量的附加参数时，它也会这样做。

此外，您可以在以后传递附加参数。正如刚才演示的那样，当您定义了希望调用的高阶函数时，您可以这样做。

简单地将其定义为对一个函数的引用，一个高阶函数返回一个或多个未定义的参数。

如果有必要的话，反复几次，这样你就能完全理解发生了什么。

我们正在创建一个返回另一个函数的模板高阶函数。然后我们定义这个新返回的函数，减去一个属性，作为模板函数的自定义实现。

您以这种方式创建的所有函数都将从高阶函数继承工作代码。但是，您可以用不同的默认参数预定义它们。

## 你已经在使用高阶函数了

高阶函数是 JavaScript 工作方式的基础，你已经在使用它们了。

每次你传递一个匿名或回调函数时，你实际上是在获取被传递函数返回的值，并把它作为另一个函数的参数(比如用 [arrow 函数](https://www.sitepoint.com/javascript-arrow-functions/))。

开发人员在学习 JavaScript 的早期就熟悉高阶函数。JavaScript 的设计如此固有，以至于直到后来才需要学习驱动箭头函数或回调的概念。

分配返回其他函数的函数的能力扩展了 JavaScript 的便利性。高阶函数允许我们创建自定义命名的函数，使用一阶函数的共享模板代码来执行专门的任务。

这些函数中的每一个都可以继承高阶函数的任何改进。这有助于我们避免代码重复，并保持我们的源代码清晰易读。

如果您确保您的函数是纯的(它们不会改变外部值，并且对于任何给定的输入总是返回相同的值)，您可以创建测试来验证您的代码更改在您更新一阶函数时不会破坏任何东西。

## 结论

既然您已经知道了高阶函数是如何工作的，那么您可以开始考虑如何在您自己的项目中利用这个概念。

JavaScript 的一大优点是，您可以将函数式技术与您已经熟悉的代码混合使用。

尝试一些实验。即使你一开始只是为了使用高阶函数，你也会很快熟悉它们所提供的额外灵活性。

现在对高阶函数做一点工作可以在未来几年改进您的代码。

## 分享这篇文章