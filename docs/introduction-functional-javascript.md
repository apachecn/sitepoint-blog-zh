# 函数式 JavaScript 简介

> 原文：<https://www.sitepoint.com/introduction-functional-javascript/>

> 本文收录于我们的文集[现代 JavaScript](https://www.sitepoint.com/premium/books/modern-javascript) 。如果你想在一个地方获得所有现代 JavaScript 的速度，注册 SitePoint Premium 并下载一份。

您听说过 JavaScript 是一种函数式语言，或者至少它能够支持函数式编程。但是什么是函数式编程呢？就此而言，如果你要开始比较一般的编程范式，函数式方法与你一直编写的 JavaScript 有什么不同？

好消息是 JavaScript 在范例方面并不挑剔。你可以随意混合命令式的、面向对象的、原型的和功能性的代码，并且仍然可以完成工作。但是坏消息是这对你的代码意味着什么。JavaScript 可以在同一代码库中同时支持多种编程风格，因此在可维护性、可读性和性能方面做出正确的选择取决于您。

为了增加价值，函数式 JavaScript 不必接管整个项目。了解一点关于函数式方法的知识有助于指导您在构建项目时所做的一些决定，不管您更喜欢用什么方式来构建代码。学习一些函数模式和技术可以让你在编写更干净、更优雅的 JavaScript 的道路上走得更好，不管你更喜欢哪种方法。

## 命令式 JavaScript

JavaScript 最初作为浏览器内语言流行起来，主要用于为网页上的元素添加简单的悬停和点击效果。多年来，这就是人们对它的大部分了解，这也是 JavaScript 早期名声不佳的原因之一。

由于开发人员努力将 JavaScript 的灵活性与浏览器文档对象模型(DOM)的复杂性相匹配，实际的 JavaScript 代码在现实世界中通常是这样的:

```
var result;
function getText() {
  var someText = prompt("Give me something to capitalize");
  capWords(someText);
  alert(result.join(" "));
};
function capWords(input) {
  var counter;
  var inputArray = input.split(" ");
  var transformed = "";
  result = [];
  for (counter = 0; counter < inputArray.length; counter++) {
    transformed = [
      inputArray[counter].charAt(0).toUpperCase(), 
      inputArray[counter].substring(1)
    ].join("");
    result.push(transformed);
  }
};
document.getElementById("main_button").onclick = getText;
```

在这一小段代码中，发生了很多事情。变量在全局范围内被定义。值被函数传递和修改。DOM 方法正与原生 JavaScript 混合在一起。函数名不太具有描述性，部分原因是整个事情依赖于一个可能存在也可能不存在的上下文。但是如果您碰巧在浏览器中运行定义了`<button id="main_button">`的 HTML 文档，您可能会被提示输入一些要处理的文本，然后看到文本中每个单词的第一个字母都大写的 an `alert`。

像这样的命令式代码被编写为从上到下读取和执行(给予或接受一点点[变量提升](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/))。但是我们可以做一些改进，通过利用 JavaScript 面向对象的特性来清理它，使它更具可读性。

## 面向对象的 JavaScript

几年后，开发人员开始注意到在像浏览器这样的共享环境中命令式编码的问题。一段 JavaScript 代码中的全局变量破坏了另一段代码中的全局变量。调用代码的顺序会以不可预知的方式影响结果，尤其是考虑到网络连接和渲染时间带来的延迟。

最终，出现了一些更好的实践来帮助封装 JavaScript 代码，并使其更好地与 DOM 兼容。上面相同代码的更新版本，按照面向对象的标准编写，可能如下所示:

```
(function() {
  "use strict";
  var SomeText = function(text) {
    this.text = text;
  };
  SomeText.prototype.capify = function(str) {
    var firstLetter = str.charAt(0);
    var remainder = str.substring(1);
    return [firstLetter.toUpperCase(), remainder].join("");
  };
  SomeText.prototype.capifyWords = function() {
    var result = [];
    var textArray = this.text.split(" ");
    for (var counter = 0; counter < textArray.length; counter++) {
      result.push(this.capify(textArray[counter]));
    }
    return result.join(" ");
  };

  document.getElementById("main_button").addEventListener("click", function(e) {
    var something = prompt("Give me something to capitalize");
    var newText = new SomeText(something);
    alert(newText.capifyWords());
  });
}());
```

在这个面向对象的版本中，构造函数模拟一个类来模拟我们想要的对象。方法依靠新对象的原型来保持低内存使用。所有的代码都被隔离在一个匿名的立即调用的函数表达式中，这样就不会影响全局范围。甚至还有一个利用最新 JavaScript 引擎的`"use strict"`指令，老式的`onclick`方法已经被崭新的`addEventListener`所取代，因为谁还会使用 IE8 或更早的版本呢？像这样的脚本可能会被插入到 HTML 文档中的`<body>`元素的末尾，以确保所有的 DOM 在被处理之前都已被加载，这样它所依赖的`<button>`就可用了。

但是，尽管所有这些重新配置，仍然有许多相同的命令式风格的工件将我们带到了这里。构造函数中的方法依赖于父对象范围内的变量。有一个循环结构来遍历字符串数组的所有成员。有一个`counter`变量，除了通过`for`循环增加进度之外，它没有任何作用。还有一些方法会产生副作用，修改存在于它们自己定义之外的变量。所有这些都使得代码更加脆弱，可移植性更差，并且使得在这个狭窄的上下文之外测试方法更加困难。

## 函数 JavaScript

面向对象的方法比我们开始时的命令式方法更干净，更模块化，但是让我们看看是否可以通过解决我们讨论过的一些缺点来改进它。如果我们能够找到方法利用 JavaScript 的内置能力将函数视为一级对象，这样我们的代码就可以更干净、更稳定、更易于重用，那就太好了。

```
(function() {
  "use strict";
  var capify = function(str) {
    return [str.charAt(0).toUpperCase(), str.substring(1)].join("");
  };
  var processWords = function(fn, str) {
    return str.split(" ").map(fn).join(" ");
  };
  document.getElementById("main_button").addEventListener("click", function(e) {
    var something = prompt("Give me something to capitalize");
    alert(processWords(capify, something));
  });
}());
```

你注意到这个版本有多短了吗？我们只定义了两个函数:`capify`和`processWords`。这些函数都是纯函数，这意味着它们不依赖于调用它们的代码的状态。这些函数不会产生改变外部变量的副作用。对于给定的一组参数，函数返回的结果只有一个。由于这些改进，新的函数非常容易测试，并且可以直接从这段代码中截取出来，不需要任何修改就可以用在其他地方。

这里可能有一个关键字，除非你以前看过一些函数代码，否则你不会认出来。我们利用了`Array`上新的[映射方法](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/map)来对我们拆分字符串时创建的临时数组的每个元素应用一个函数。`Map`只是我们在现代浏览器和服务器端 JavaScript 解释器实现 ECMAscript 5 标准时得到的少数便利方法之一。在这里使用`map`代替`for`循环，消除了`counter`变量，有助于使我们的代码更加清晰易读。

## 开始功能性思考

您不必放弃您所知道的一切来利用函数范式。当您编写下一个程序时，可以通过考虑几个问题来开始以函数的方式考虑您的 JavaScript:

*   我的函数依赖于它们被调用的上下文吗，或者它们是纯粹的和独立的？
*   我能不能以这样一种方式来编写这些函数，使得我可以相信它们对于给定的输入总是返回相同的结果？
*   我能确定我的函数不会修改自身以外的任何东西吗？
*   如果我想在另一个程序中使用这些函数，我需要对它们进行修改吗？

这篇介绍仅仅触及了函数式 JavaScript 的皮毛，但是我希望它能激起你学习更多的兴趣。

## 分享这篇文章