# ES2015 中最佳新功能的概述

> 原文：<https://www.sitepoint.com/es2015-useful-parts-rundown-best-new-features/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

arrow 和 spread 操作符、新的类语法、`let`和`const`关键字是 JavaScript 中非常明显的新增内容，它们的使用已经像野火一样蔓延开来。这些新的语言特性减少了开发人员编写的样板代码的数量，简化了 JavaScript 代码，并使开发人员的意图更加清晰。而且[浏览器支持特别好](https://dev.windows.com/en-us/microsoft-edge/platform/status/symbolses6/?utm_source=SitePoint&utm_medium=article73&utm_campaign=SitePoint/?filter=f3f0000bf&search=es6)包括大部分现代浏览器[包括微软 Edge](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16537-DEV-sitepoint-article73) 。本文将向您简要介绍 ES2015 中的最佳新功能。

ECMAScript 是由标准制定机构 ECMA 国际标准化的脚本语言，称为 ECMA-262。这个组织肩负着为 JavaScript 实现设定标准的艰巨任务。ECMAScript 标准已经经历了几个版本。该标准的新版本已于今年夏天发布。你可能听说过它叫 ECMAScript 6 (ES6)，ECMAScript 2015(ES2015)，Harmony 甚至 JavaScript 2015。在本文的其余部分，我将使用术语 ES2015。

ES2015 中引入的新语言功能改进了 JavaScript，使其更简单、可读性更强、不易出错。本文将介绍块级作用域和新的`let`和`const`关键字，它们简化了作用域，使代码更容易理解，新的类语法减少了创建面向对象风格继承所需编写的样板代码的数量，新的箭头和展开操作符减少了开发人员必须进行的击键次数。

## 数据块范围界定—我们来谈谈 ES2015 之前的范围界定

新的甚至经验丰富的 JavaScript 开发人员已经成为 JavaScript 中作用域系统的牺牲品。甚至来自其他语言的专业开发人员也成为了牺牲品。直到最近，JavaScript 变量作用域还只能在函数级绑定。换句话说，每当你声明一个变量时，不管它是在 if 语句还是 for 循环中声明的，变量的作用域都被设置为函数级。

```
 var foo = 'Canadian Developer Connection';
console.log(foo); // Prints 'Canadian Developer Connection'
if(true){
  var foo = 'BAR';
  console.log(foo); // Prints 'BAR'
}
console.log(foo); // Prints 'BAR' 
```

如果你已经养成了重用变量名的习惯，那么函数级变量的作用域就特别麻烦。如果你是一名 Java、C#或 C++开发人员，这可能已经在你的编码习惯中根深蒂固了，尤其是当涉及到占位符变量时，比如 for 循环中的索引变量，或者在函数的不同部分重新实例化的对象的名称。

JavaScript 将变量提升到函数的顶部，不管它们是在函数中的什么地方声明的，强制执行函数级变量作用域。这种现象被称为“提升”，经常是前端 web 开发人员职位候选人的面试问题。以下代码:

```
 var foo = 'JS';
if(!bar){
  console.log(foo +' '+ bar);
  // Prints 'JS undefined'
}
var bar = '2015';
console.log(foo +' '+ bar);
// Prints ‘JS 2015' 
```

实际上是这样执行的:

```
 var foo, bar;
foo = 'JS';
if(!bar){
  console.log(foo +' '+ bar);
  // Prints 'JS undefined'
}
bar = '2015';
console.log(foo +' '+ bar);
// Prints 'JS 2015' 
```

ES2015 使用两个新的语法关键字引入了块级范围；`let` & `const`。

## 块范围 let & const 简介

ES2015 允许您使用两个新关键词在块级别确定范围。在不同的块中声明的变量，例如 if 块、for 循环块、while 循环块，任何用花括号`{}`括起来的东西，甚至裸花括号`{}`，都将只绑定到那个范围。要声明一个作用于块级的变量，可以使用`let`关键字而不是`var`。

```
 let foo = 'JS';
console.log(foo); // Prints 'JS'
if(true){
  let foo = 'BAR';
  console.log(foo); // Prints 'BAR'
}
console.log(foo); // Prints 'JS' 
```

ES2015 还引入了块级单赋值变量的构造。这是通过新的`const`关键字完成的。用`const`声明的变量只能赋值一次，这对于声明“神奇”变量非常有用，如 Pi 或 Boltzsmann 常数或其他在初始赋值后不能修改的变量。

```
 const foo = 'JS';
console.log(foo); // Prints 'JS'
if(true){
  let foo = 'BAR';
  console.log(foo); // Prints 'BAR'
}
// foo = 'BAR';
// The above line causes an error due to the variable being const.
console.log(foo); // Prints 'JS' 
```

在 ES2015 中引入`let`和`const`可以让开发人员更加明确地声明变量，并降低范围错误的可能性。在大多数情况下，特别是如果你已经知道“提升”和函数级范围，你可以用`let`代替`var`的用法，而不用对你的代码做任何其他的重大改变。您可以省去很多麻烦，并且更容易引进懂其他语言的开发人员。

## 类 JavaScript 中的继承

对象继承是学习 JavaScript 的开发人员的一个常见困惑点。特别是，有 Java、C#或 C++等面向对象语言经验的开发人员会尝试开发类似的继承链。JavaScript 对象继承基于[原型继承](https://en.wikipedia.org/wiki/Prototype-based_programming)。

在 JavaScript 中，对象可以被描述为键值属性的动态集合。您可以在 JavaScript 中添加、更改和删除任何对象的属性，因此对象是可变的。为了实现 JavaScript 对象的继承，我们使用原型法，即使用一个现有的对象并克隆它来创建一个新的对象。[道格拉斯·克洛克福特](http://www.crockford.com/)写了[这篇文章](http://javascript.crockford.com/prototypal.html):

> 不是创建类，而是创建原型对象，然后使用 object 函数创建新的实例。对象在 JavaScript 中是可变的，所以我们可以增加新的实例，给它们新的字段和方法。然后，这些可以作为新对象的原型。我们不需要类来制造许多相似的对象。

原型继承模型比基于类的继承模型更强大。此外，您可以使用原型继承来重新创建一种基于类的继承模型。如果您试图在对象之间继承和共享“方法”，您继承的函数就像克隆到新对象的另一个属性一样。克隆函数中的“ **this** ”将指向新的克隆对象，而不是克隆它的对象。熟悉 Java 和 C++的开发人员肯定会对这一点感到困惑，如果你没有意识到编程范例中的这一主要差异，你肯定会写出错误的代码。

ES2015 增加了**语法糖**，让习惯基于类的对象继承模式的开发者更容易使用。

## 类——引入语法糖类

ES2015 引入了语法糖，使开发人员更容易理解。新引入的关键词有`class`、`constructor`、`static`、`extends`、`super`。但是不要忘记 JavaScript 仍然是基于原型的。

类实际上只是函数。您可以像函数一样，使用声明和表达式来声明一个类。

```
 class GameState {
  …
}
var GameState = class {
  …
} 
```

ES2015 还引入了`constructor`关键字。这是一个特殊的函数，用于初始化用类创建的对象。如果你没有定义你自己的构造函数，默认的构造函数什么都不做或者传递参数给父构造函数(见下一段)。

```
 classGameState {
  constructor(players){
    …
  }
} 
```

ES2015 还引入了`extends`和`super`关键词。`Extends`用来告诉一个对象继承另一个对象，形成父子关系。`Super`用于从子对象中调用父对象中的函数(如果确实有父对象)。请注意调用父构造函数与调用父函数有什么不同。

```
 class Dog {
  constructor(name) {}
  bark() {
    console.log("bark");
  }
}

class Poodle extends Dog {
  constructor(color, name) {
    super(name);
  }
  bark() {
    super.bark();
    console.log("wooooof");
  }
} 
```

最后，ES2015 中还引入了一个关键词，那就是`static`关键词。`static`关键字允许您定义一个静态函数，无需创建对象的新实例即可调用该函数。它在实例化时也不能从类中调用。您通常可以使用这个关键字来创建实用函数。

## 箭头运算符

你多久编写一次执行一个语句或返回一个值的短函数？似乎每个实用程序库(例如下划线、连字符、jQuery)都让你编写简短的回调函数。引入箭头操作符`=>`是为了减少用 JavaScript 编写的无用样板代码的数量。箭头操作符`=>`是函数的简写。示例:

```
 function(x) { return x + x; } becomes x => x + x;
function(x) { return x.y; } becomes x => x.y;
function(x, y) { return x + y; } becomes (x, y) => x + y;
function() { doSomething(); } becomes () => { doSomething(); } 
```

编写函数和使用箭头操作符有一个显著的区别。箭头操作符中的代码体共享相同的词法`this`，而不是新的函数作用域。如果你需要一本关于 JavaScript 块范围的入门书，你可以阅读这篇[文章](http://blogs.msdn.com/b/cdndevs/archive/2015/09/16/future-of-javascript-ecmascript-6-es2015-block-scoping.aspx?WT.mc_id=16537-DEV-sitepoint-article73)。例如，它的作用域是父函数:

```
 function dog(){
  this.name = "boo";
  setInterval(
    () => {
      console.log(this.name + " woofed!");
    }, 1000);
} 
```

其他语言如 C#、Java 和 CoffeeScript 启发了新的箭头操作符。

## 扩展运算符

spread 运算符是扩展表达式的一种简便方法。spread 运算符可用于将可迭代对象扩展为函数的参数。您可以将一个 iterable 对象扩展到另一个数组中，从而用语言语法执行一个`splice`、`concat`或`push`。在当前规范 ES2015 中，它不支持对象和其他东西。

这是一个将数组扩展为函数参数的示例，相当于对该函数调用`apply()`:

```
 var args = ["arg1","arg2"];
doSomething(…args); 
```

这是在另一个数组中间展开一个数组的示例，相当于该数组上的一个`splice()`:

```
 var missing_numbers = [3, 4];
var numbers = [1, 2, ...missing_numbers, 5, 6]; 
```

这是一个`push()`和`concat()`的例子:

```
 var zero = [0];
var three = [3];
var numbers = [...zero, 1, 2, ...three]; 
```

## ES2015 现在在哪里？

大多数浏览器和 JavaScript 引擎已经实现了 ES2015 中的功能。你会在 [Kangax](http://kangax.github.io/compat-table/es6/) 上找到非常详细的状态表。微软 Edge，Firefox，Chrome 在支持大多数 ES2015 功能方面都表现得很好。

Node.js 开发者也可以尝试 ES2015。从版本 5 开始，Node.js 几乎完全支持 ES2015。甚至版本 4 也部分支持 ES2015。如果您停留在 Node.js 的旧版本上，您可以通过使用–V8-options 标志获得 ES2015 的一些功能。

## 结论

ES2105 引入了许多新的语言功能，使开发人员能够更轻松地创建易读、简单的代码。您不再需要使用新的类语法从其他语言如 C#、Java 或 C++中重新创建一些编程模式。然而，不要忘记，这些新的关键字只是语法上的糖，旨在使开发更容易，但不会改变底层的原型继承模型。新的`let`和`const`关键字以及 arrow 和 spread 操作符允许您减少大量样板代码，从而提高程序员的生产率。有了 Edge、Chrome、Firefox、Node.js 的最新版本，ES2015 现已上市！

## 其他资源

*   [关于 JavaScript 作用域你需要知道的事情](http://www.smashingmagazine.com/2009/08/what-you-need-to-know-about-javascript-scope/)
*   [你想知道的关于 JavaScript 作用域的一切](http://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)
*   [解释 JavaScript 范围和闭包](http://robertnyman.com/2008/10/09/explaining-javascript-scope-and-closures/)
*   [JavaScript 中的原型继承](http://javascript.crockford.com/prototypal.html)
*   [基于原型的编程](https://en.wikipedia.org/wiki/Prototype-based_programming)
*   [继承和原型链](https://developer.mozilla.org/en/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
*   [ECMAScript 6 特性:类](https://github.com/lukehoban/es6features#classes)
*   [ES6 特性–箭头](https://github.com/lukehoban/es6features#arrows)
*   [箭头功能–MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
*   [扩展运算符–MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16537-DEV-sitepoint-article73) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16537-DEV-sitepoint-article73)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article73&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article73&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article73&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article73&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16537-DEV-sitepoint-article73)):

*   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16537-DEV-sitepoint-article73)
*   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes/?WT.mc_id=16537-DEV-sitepoint-article73)
*   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries/?WT.mc_id=16537-DEV-sitepoint-article73)
*   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins/?WT.mc_id=16537-DEV-sitepoint-article73)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development/?WT.mc_id=16537-DEV-sitepoint-article73)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=16537-DEV-sitepoint-article73)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=16537-DEV-sitepoint-article73)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16537-DEV-sitepoint-article73) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16537-DEV-sitepoint-article73) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16537-DEV-sitepoint-article73)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx/?WT.mc_id=16537-DEV-sitepoint-article73)(基于订阅的免费培训和云优势)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=16537-DEV-sitepoint-article73) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16537-DEV-sitepoint-article73)

## 分享这篇文章