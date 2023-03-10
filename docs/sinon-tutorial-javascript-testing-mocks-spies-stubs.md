# Sinon 教程:用模仿、间谍和存根进行 JavaScript 测试

> 原文：<https://www.sitepoint.com/sinon-tutorial-javascript-testing-mocks-spies-stubs/>

这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)和[马克托勒](https://twitter.com/MarcTowler)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

编写单元测试时最大的绊脚石之一是，当你有重要的代码时该怎么办。

在现实生活的项目中，代码经常做各种各样的事情，使得测试变得困难。Ajax 请求、定时器、日期、访问其他浏览器特性……或者如果你使用 Node.js，数据库总是很有趣，网络或文件访问也是如此。

所有这些都很难测试，因为你不能在代码中控制它们。如果你使用 Ajax，你需要一个服务器来响应请求，这样你的测试才能通过。如果您使用`setTimeout`，您的测试将不得不等待。对于数据库或网络，情况是一样的——您需要一个包含正确数据的数据库，或者一个网络服务器。

现实生活并不像许多测试教程看起来那么简单。但是你知道有一个解决方法吗？

通过使用 Sinon，我们可以让测试不平凡的代码变得平凡！

让我们找出方法。

## 是什么让西农如此重要和有用？

简单地说， [Sinon](http://sinonjs.org/) 允许你用一些使测试变得简单的东西来代替测试中的困难部分。

当测试一段代码时，您不希望它受到测试之外的任何事情的影响。如果某个外部因素影响了测试，测试就会变得更加复杂，并且可能会随机失败。

如果您想测试 Ajax 调用的代码，该如何做呢？您需要运行一个服务器，并确保它给出测试所需的准确响应。设置起来很复杂，并且使得编写和运行单元测试很困难。

如果你的代码依赖于时间呢？假设它在做某件事之前等待了一秒钟。现在怎么办？您可以在测试中使用一个`setTimeout`来等待一秒钟，但是这会使测试变慢。想象一下如果间隔更长，例如五分钟。我猜你可能不想每次运行测试都等五分钟。

通过使用 Sinon，我们可以解决这两个问题(以及许多其他问题)，并消除复杂性。

## 西蒙是如何工作的？

Sinon 允许您轻松创建所谓的*测试替身*，从而帮助消除测试中的复杂性。

顾名思义，测试替身是测试中使用的代码片段的替代品。回头看看 Ajax 示例，我们将使用 test-double 替换 Ajax 调用，而不是设置服务器。以时间为例，我们将使用测试加倍来允许我们“在时间中向前旅行”。

这听起来可能有点奇怪，但基本概念很简单。因为 JavaScript 是非常动态的，所以我们可以用任何函数替换它。测试替身只是将这个想法进一步发展了一点。使用 Sinon，我们可以用 test-double 替换任何 JavaScript 函数，然后可以配置 test-double 来做各种事情，使复杂的事情变得简单。

西农将双打分为三种类型:

*   [间谍](http://sinonjs.org/docs/#spies)，提供关于函数调用的信息，而不影响它们的行为
*   [存根](http://sinonjs.org/docs/#stubs)，类似间谍，但完全替代了功能。这使得存根函数可以为所欲为——抛出异常、返回特定值等
*   通过结合间谍和存根，模仿使替换整个对象变得更加容易

此外，Sinon 还提供了一些其他的助手，尽管这些超出了本文的范围:

*   [假计时器](http://sinonjs.org/docs/#clock)，可用于时间向前旅行，例如触发`setTimeout`
*   [伪造 XMLHttpRequest 和 server](http://sinonjs.org/docs/#server) ，可用于伪造 Ajax 请求和响应

有了这些特性，Sinon 允许你解决测试中外部依赖引起的所有困难问题。如果你学会了有效使用 Sinon 的技巧，你就不需要任何其他工具了。

## 安装否则

首先我们需要安装 Sinon。

**对于 Node.js 测试:**

1.  使用`npm install sinon`通过 npm 安装 Sinon
2.  使用`var sinon = require('sinon');`在测试中要求 Sinon

**对于基于浏览器的测试:**

1.  你可以用`npm install sinon`通过 npm 安装 Sinon，使用 [CDN](https://cdnjs.com/libraries/sinon.js) ，或者从 [Sinon 的网站](http://sinonjs.org/download/)下载
2.  在您的测试运行程序页面中包含`sinon.js`。

## 入门指南

Sinon 有很多功能，但大部分都是建立在自身之上的。你学了一部分，你已经知道下一部分了。一旦你学会了基本知识，知道了每个不同部分的作用，这使得 Sinon 很容易使用。

当我们的代码调用一个给我们带来麻烦的函数时，我们通常需要 Sinon。

对于 Ajax，可能是`$.get`或`XMLHttpRequest`。随着时间的推移，这个功能可能会成为`setTimeout`。对于数据库，可能是`mongodb.findOne`。

为了便于讨论这个函数，我将把它称为*依赖关系*。我们正在测试的函数*依赖于另一个函数的结果*。

我们可以说，Sinon 的基本使用模式是**用一个双测试**替换有问题的依赖。

*   当测试 Ajax 时，我们用一个假装发出 Ajax 请求的 test-double 替换`XMLHttpRequest`
*   当测试时间时，我们用一个假想的计时器代替`setTimeout`
*   当测试数据库访问时，我们可以用 test-double 替换`mongodb.findOne`,它会立即返回一些假数据

让我们看看这在实践中是如何工作的。

## 间谍

间谍是 Sinon 最简单的部分，其他功能都建立在它们之上。

间谍的主要用途是收集关于函数调用的信息。您还可以使用它们来帮助验证一些事情，比如某个函数是否被调用。

```
var spy = sinon.spy();

//We can call a spy like a function
spy('Hello', 'World');

//Now we can get information about the call
console.log(spy.firstCall.args); //output: ['Hello', 'World'] 
```

函数`sinon.spy`返回一个`Spy`对象，它可以像函数一样被调用，但也包含带有对它的任何调用信息的属性。在上面的例子中，`firstCall`属性有关于第一次调用的信息，比如`firstCall.args`，它是传递的参数列表。

尽管您可以通过不带参数调用`sinon.spy`来创建匿名间谍，但更常见的模式是用一个间谍替换另一个函数。

```
var user = {
  ...
  setName: function(name){
    this.name = name;
  }
}

//Create a spy for the setName function
var setNameSpy = sinon.spy(user, 'setName');

//Now, any time we call the function, the spy logs information about it
user.setName('Darth Vader');

//Which we can see by looking at the spy object
console.log(setNameSpy.callCount); //output: 1

//Important final step - remove the spy
setNameSpy.restore(); 
```

用 spy 替换另一个函数的工作方式与上一个示例类似，但有一个重要的区别:当您使用完 spy 后，一定要记住恢复原来的函数，如上面示例的最后一行所示。没有这个，你的测试可能会出错。

间谍有很多不同的属性，这些属性提供了关于他们如何被利用的不同信息。兴农的间谍文件列出了所有可用选项的完整列表。

实际上，你可能不会经常使用间谍。你更可能需要一个存根，但间谍可以很方便，例如验证一个回调被调用:

```
function myFunction(condition, callback){
  if(condition){
    callback();
  }
}

describe('myFunction', function() {
  it('should call the callback function', function() {
    var callback = sinon.spy();

    myFunction(true, callback);

    assert(callback.calledOnce);
  });
}); 
```

在这个例子中，我使用 [Mocha](https://mochajs.org/) 作为测试框架，使用 [Chai](http://chaijs.com/) 作为断言库。如果您想了解更多关于这两个方面的内容，请参考我以前的文章:[使用 Mocha 和 Chai](https://www.sitepoint.com/unit-test-javascript-mocha-chai/) 对您的 JavaScript 进行单元测试。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Sinon 教程:用 Mocks 进行 JavaScript 测试，Spies & Stubs](http://codepen.io/SitePoint/pen/XdbYzb/) 。