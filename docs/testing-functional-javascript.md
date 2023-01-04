# 测试函数式 JavaScript 的初学者指南

> 原文：<https://www.sitepoint.com/testing-functional-javascript/>

[功能编程](https://www.sitepoint.com/premium/courses/functional-javascript-programming-2922/)和测试。也许你已经单独尝试过了，但是不知何故你从来没有把它们作为你常规练习的一部分。它们本身听起来可能是无辜的，但是测试和函数式编程结合在一起会产生一种不可抗拒的诱惑，几乎会迫使您编写更干净、更紧凑、更易维护的代码。

![Testing Functional JavaScript — A crash test dummy sits at a computer with a mug of coffee](img/de6d2be55636052e4a83cf4f99c0b2ef.png)

好消息是，将这两种技术结合使用可以带来一些真正的优势。事实上，一旦你尝过这种结合有多甜，你可能会发现自己和我一样沉迷于此，我敢打赌你会回来吃更多。

在本文中，我将向您介绍测试函数式 JavaScript 的原则。我将向您展示如何启动并运行 Jasmine 框架，并使用测试驱动的方法构建一个纯粹的功能。

## 为什么要测试？

测试就是要确保你的应用程序中的代码做你期望它做的事情，并且在你做改变的时候继续做你期望它做的事情，这样当你完成的时候你就有了一个工作产品。您编写一个测试，在一组定义的环境下定义您的预期功能，对代码运行该测试，如果结果不是测试所说的那样，您会得到一个警告。你会一直收到这个警告，直到你修改了你的代码。

然后你得到奖励。

是的，它会让你感觉很好。

测试有很多种方式，对于边界在哪里进行健康的辩论也有空间，但简单来说:

*   单元测试验证隔离代码的功能
*   集成测试验证数据流和组件的交互
*   功能测试着眼于整个应用程序的行为

注意:不要因为有一种叫做功能测试的测试而分心。这不是我们在这篇关于测试函数式 JavaScript 的文章中要关注的。事实上，无论您是否在 JavaScript 中使用函数式编程技术，您将用来对应用程序的整体行为进行功能测试的方法可能都不会改变太多。函数式编程真正有帮助的地方是在你构建单元测试的时候。

您可以在编码过程中的任何时候编写测试，但是我总是发现在编写您计划测试的函数之前编写单元测试是最有效的。这种被称为[测试驱动开发(TDD)](https://www.sitepoint.com/learning-javascript-test-driven-development-by-example/) 的实践，鼓励你在开始编写之前分解应用程序的功能，并确定你想从每一段代码中得到什么结果，首先编写测试，然后编码以产生结果。

一个附带的好处是，TDD 经常迫使你与那些付钱给你写程序的人进行详细的交谈，以确保你写的东西确实是他们想要的。毕竟单考过关很容易。困难的是决定如何处理你将会遇到的所有可能的输入，并在不破坏东西的情况下正确处理它们。

## 为什么功能性？

可以想象，您编写代码的方式与测试的难易程度有很大关系。有一些代码模式，比如将一个函数的行为与另一个函数的行为紧密耦合，或者严重依赖全局变量，会使代码更加难以进行单元测试。有时，为了建立可测试的参数和结果，您可能不得不使用不方便的技术，例如“模仿”外部数据库的行为或模拟复杂的运行时环境。这些情况不是总能避免的，但是通常可以隔离出代码中需要它们的地方，这样就可以更容易地测试代码的其余部分。

函数式编程允许您独立处理应用程序中的数据和行为。通过创建一组独立的函数来构建应用程序，每个函数都独立工作，不依赖于外部状态。因此，您的代码变得几乎是自文档化的，将定义明确的小函数捆绑在一起，以一致和可理解的方式运行。

函数式编程经常与命令式编程和面向对象编程形成对比。JavaScript 可以支持所有这些技术，甚至可以混合使用它们。对于创建命令性代码序列来说，函数式编程是一种很有价值的替代方法，它可以跨多个步骤跟踪应用程序的状态，直到返回一个结果。或者利用复杂对象之间的交互来构建应用程序，这些复杂对象封装了应用于特定数据结构的所有方法。

## 纯函数如何工作

函数式编程鼓励您使用微小的、可重用的、可组合的函数来构建应用程序，这些函数只做一件特定的事情，并且每次都为相同的输入返回相同的值。像这样的函数叫做[纯函数](https://www.sitepoint.com/an-introduction-to-reasonably-pure-functional-programming/)。纯函数是函数式编程的基础，它们都有以下三个特点:

*   不要依赖外部状态或变量
*   不要引起副作用或改变外部变量
*   对于相同的输入，总是返回相同的结果

编写函数代码的另一个好处是，它使得单元测试变得更加容易。您可以进行单元测试的代码越多，您就越能放心地依赖您在未来重构代码而不破坏基本功能的能力。

## 是什么让功能代码易于测试？

如果你思考我们刚刚讨论的概念，你可能已经明白为什么功能代码更容易测试了。为一个纯函数编写测试是微不足道的，因为每一个输入都有一致的输出。你所要做的就是设定期望值，并对照代码运行它们。不需要建立上下文，不需要跟踪功能间的依赖关系，不需要模拟功能外的变化状态，也不需要模拟可变的外部数据源。

有很多测试选项，从成熟的框架到实用程序库和简单的测试工具。其中包括[茉莉](http://jasmine.github.io/)、[摩卡](https://mochajs.org/)、[酵素](http://airbnb.io/enzyme/)、 [Jest](https://facebook.github.io/jest/) 等等一大堆。每一个都有不同的优点和缺点，最佳用例，以及[忠实的追随者](http://stateofjs.com/2016/testing/)。Jasmine 是一个健壮的框架，可以在各种各样的环境中使用，所以这里有一个快速的演示，说明如何使用 Jasmine 和 TDD 在浏览器中开发一个纯函数。

您可以创建一个 HTML 文档，从本地或者从 CDN 获取 Jasmine 测试库。包含 Jasmine 库和测试运行程序的页面示例可能如下所示:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Jasmine Test</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.6.1/jasmine.min.css">
  </head>
  <body>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.6.1/jasmine.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.6.1/jasmine-html.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jasmine/2.6.1/boot.min.js"></script>
  </body>
</html> 
```

这带来了 Jasmine 库，以及 Jasmine HTML 引导脚本和样式。在这种情况下，文档主体是空的，等待您的 JavaScript 和 Jasmine 测试。

## 测试功能 JavaScript——我们的第一个测试

首先，让我们编写第一个测试。我们可以在一个单独的文档中这样做，或者将它包含在页面上的一个`<script>`元素中。我们将使用 Jasmine 库定义的 [describe](https://jasmine.github.io/api/2.6/global.html#describe) 函数来描述我们尚未编写的新函数的预期行为。

我们要写的新函数将被称为`isPalindrome`，如果传入的字符串前后相同，它将返回`true`，否则返回`false`。测试将如下所示:

```
describe("isPalindrome", () => {
  it("returns true if the string is a palindrome", () => {
    expect(isPalindrome("abba")).toEqual(true);
  });
}); 
```

当我们将它添加到页面的脚本中并加载到浏览器中时，我们会得到一个显示错误的 Jasmine 报告页面。这就是我们现在想要的。我们想知道测试正在运行，以及它失败了。这样一来，我们渴望得到认可的大脑就知道我们有东西需要修正。

因此，让我们用 JavaScript 写一个简单的函数，逻辑足够让我们的测试通过。在这种情况下，它只是一个函数，通过返回预期的值来使我们的测试通过。

```
const isPalindrome = (str) => true; 
```

是的，真的。我知道这看起来很可笑，但和我一起坚持下去。

当测试者再次运行时，它通过了。当然了。但是很明显，这个简单的代码没有做我们期望回文测试器做的事情。我们已经编写了通过测试的最少代码。但是我们知道我们的代码不能有效地计算回文。此时我们需要的是额外的期望。因此，让我们为 describe 函数添加另一个断言:

```
describe("isPalindrome", () => {
  it("returns true if the string is a palindrome", () => {
    expect(isPalindrome("abba")).toEqual(true);
  });
  it("returns false if the string isn't a palindrome", () => {
    expect(isPalindrome("Bubba")).toEqual(false);
  });
}); 
```

现在重新加载我们的页面会使测试输出变成红色并失败。我们会收到一条消息，说明问题是什么，测试结果会变成红色。

红色！

我们的大脑感觉到有问题。

当然有。现在，我们简单的`isPalindrome`函数，每次都返回 true，已经被证明在这个新的测试中不能有效地工作。所以让我们更新一下`isPalindrome`,增加比较向前和向后传递的字符串的能力。

```
const isPalindrome = (str) => {
  return str
    .split("")
    .reverse()
    .join("") === str;
}; 
```

## 测试会上瘾

又变绿了。现在那是令人满意的。当你重新加载页面的时候，你有没有产生多巴胺的冲动？

有了这些变化，我们的测试又通过了。我们的新代码有效地比较了向前和向后的字符串，当字符串向前和向后相同时返回`true`，否则返回`false`。

这段代码是一个纯粹的函数，因为它只做一件事，并且在给定一致的输入值的情况下始终如一地做这件事，而不会产生任何副作用，不会对自身之外的变量进行任何更改，也不依赖于应用程序的状态。每当您向该函数传递一个字符串时，它都会对向前和向后的字符串进行比较，并返回结果，而不管它何时或如何被调用。

你可以看到这种一致性使得这个函数很容易进行单元测试。事实上，编写测试驱动的代码可以鼓励你编写纯函数，因为它们更容易测试和修改。

你想要通过测试的满足感。你知道你有。

## 重构一个纯函数

在这一点上，添加额外的功能是微不足道的，比如处理非字符串输入，忽略大写和小写字母之间的差异，等等。只要问产品负责人他们希望程序如何运行。因为我们已经有了测试来验证字符串将被一致地处理，我们现在可以为非字符串值添加错误检查或字符串强制或任何我们喜欢的行为。

例如，让我们看看如果我们为一个像 1001 这样的数字添加一个测试会发生什么，如果它是一个字符串，可能会被解释为一个回文:

```
describe("isPalindrome", () => {
  it("returns true if the string is a palindrome", () => {
    expect(isPalindrome("abba")).toEqual(true);
  });
  it("returns false if the string isn't a palindrome", () => {
    expect(isPalindrome("Bubba")).toEqual(false);
  });
  it("returns true if a number is a palindrome", () => {
    expect(isPalindrome(1001)).toEqual(true);
  });
}); 
```

这样做给了我们一个红色屏幕和一个失败的测试，因为我们当前的 is `isPalindrome`函数不知道如何处理非字符串输入。

恐慌开始了。我们看到红色。测试失败了。

但是现在我们可以安全地更新它来处理非字符串输入，将它们强制转换成字符串并以这种方式检查它们。我们可能会想出一个看起来更像这样的函数:

```
const isPalindrome = (str) => {
  return str
    .toString()
    .split("")
    .reverse()
    .join("") === str.toString();
}; 
```

现在我们所有的测试都通过了，我们看到了绿色，甜蜜的多巴胺涌入了我们受测试驱动的大脑。

通过将`toString()`添加到评估链中，我们能够适应非字符串输入，并在测试前将它们转换成字符串。最重要的是，因为我们的其他测试仍然每次都在运行，所以我们可以确信，我们没有因为将这个新功能添加到我们的 pure 函数中而破坏我们之前获得的功能。这是我们最终得到的结果:

参见 [CodePen](https://codepen.io) 上的 [@SitePoint](https://codepen.io/SitePoint) )测试功能 JavaScript 的 Pen [初学者指南。](https://codepen.io/SitePoint/pen/zzZwjQ/)