# 使用现代 JavaScript 语法的最佳实践

> 原文：<https://www.sitepoint.com/modern-javascript-best-practices/>

现代 JavaScript 正在快速发展，以满足新框架和环境不断变化的需求。了解如何利用这些变化可以节省您的时间，提高您的技能，并区分好代码和优秀代码。

了解现代 JavaScript 试图做什么可以帮助您决定什么时候使用新语法对您最有利，什么时候使用传统技术仍然有意义。

## 可以依附的固体

我不知道有谁对 JavaScript 的现状不感到困惑，不管你是 JavaScript 新手，还是已经用它编写代码有一段时间了。如此多的新框架，如此多的语言变化，如此多的上下文需要考虑。奇怪的是，每个月我们都要学习新的东西，却有人能完成任何工作。

我相信任何编程语言的成功秘诀，不管应用程序有多复杂，都是回归基础。如果你想理解 Rails，从努力提高你的 Ruby 技能开始，如果你想使用[中的](https://www.sitepoint.com/url-parsing-isomorphic-javascript/)[不变量](https://www.sitepoint.com/immutable-data-functional-javascript-mori/)和[单向数据流](https://www.sitepoint.com/video-introducing-one-way-data-flow/)同构 [反应](https://www.sitepoint.com/universal-react-rendering-sitepoint/)与 [webpack](https://www.sitepoint.com/bundle-static-site-webpack/) (或者任何酷的书呆子最近在做的事情)从了解你的核心 JavaScript 开始。

理解语言本身是如何工作的比让自己熟悉最新的框架和环境要实际得多。这些变化比天气还快。对于 JavaScript，我们在网上有关于 JavaScript 是如何创建的以及如何有效使用它的丰富信息。

问题是 JavaScript 最新版本中的一些新技术使得一些旧规则过时了。但不是全部！有时，一种新的语法可能会取代一种陈旧的语法来完成同样的任务。其他时候，新方法可能看起来像是我们过去做事方式的一种更简单的替代方式，但存在细微的差异，了解这些差异很重要。

## 一勺句法糖

近年来 JavaScript 中的许多变化被描述为现有语法的语法糖。在许多情况下，语法糖可以帮助 Java 程序员学习如何使用 JavaScript，或者对于我们其他人来说，我们只是想要一种更干净、更简单的方法来完成我们已经知道如何做的事情。其他变化似乎引入了神奇的新功能。

但是，如果您试图使用现代语法来重新创建一种熟悉的旧技术，或者在不了解其实际行为的情况下坚持使用它，您将面临以下风险:

*   不得不调试以前运行良好的代码
*   引入可能在运行时捕捉到的细微错误
*   创建在您最不期望的时候无声地失败的代码。

事实上，一些看起来是对现有技术的替代的变化实际上与它们应该替代的代码行为不同。在许多情况下，使用原始的、旧的风格来完成你想做的事情可能更有意义。认识到这一点，并知道如何做出选择，对于编写有效的现代 JavaScript 至关重要。

## 当你的`const`不一致时

现代 JavaScript 引入了两个新的关键字，`let`和`const`，它们在大多数情况下声明变量时有效地取代了对`var`的需要。但是它们的行为方式与`var`并不完全相同。

在传统的 JavaScript 中，在使用变量之前用关键字`var`声明它们是一种干净的编码实践。如果做不到这一点，就意味着您声明的变量可能会被运行在相同上下文中的任何脚本在全局范围内访问。由于传统的 JavaScript 经常在网页上运行，多个脚本可能会同时加载，这意味着一个脚本中声明的变量可能会泄漏到另一个脚本中。

现代 JavaScript 中对`var`最干净的替代是`let`。但是`let`有一些与`var`不同的特质。默认情况下，带有`var`的变量声明总是被提升到包含它们的[作用域](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)的顶部，不管它们被放在该作用域内的什么位置。这意味着，即使是深度嵌套的变量，也可以从其包含范围的开始就被认为是声明的和可用的。而`let`或`const`则不然。

```
console.log(usingVar); // undefined
var usingVar = "defined";
console.log(usingVar); // "defined"

console.log(usingLet); // error
let usingLet = "defined"; // never gets executed
console.log(usingLet); // never gets executed 
```

当使用`let`或`const`声明一个变量时，该变量的作用域被限制在声明它的局部块中。JavaScript 中的块由一组花括号`{}`来区分，比如函数体或循环中的可执行代码。

这对于在块范围内使用变量(如迭代器和循环)非常方便。以前，循环中声明的变量可用于包含范围，当多个计数器可能使用相同的变量名时，会导致潜在的混乱。然而，如果您希望在脚本的某个块中声明的变量在其他地方可用，那么`let`可能会让您大吃一惊。

```
for (var count = 0; count < 5; count++) {
  console.log(count);
} // outputs the numbers 0 - 4 to the console
console.log(count); // 5

for (let otherCount = 0; otherCount < 5; otherCount++) {
  console.log(otherCount);
} // outputs the numbers 0 - 4 to the console
console.log(otherCount); // error, otherCount is undefined 
```

另一个可选声明是`const`，它应该表示一个常量。但它不是完全不变的。

与`var`或`let`变量不同，`const`不能在没有值的情况下声明。

```
var x; // valid
let y; //valid
const z; // error 
```

如果您试图在声明后将它设置为新值，那么`const`也会抛出一个错误:

```
const z = 3; // valid
z = 4; // error 
```

但是，如果您希望您的`const`在所有情况下都是不可变的，那么当声明为`const`的对象或数组允许您更改其内容时，您可能会大吃一惊。

```
const z = []; // valid
z.push(1); // valid, and z is now [1]
z = [2] // error 
```

出于这个原因，当人们建议在所有变量声明中不断地使用`const`来代替`var`时，我仍然持怀疑态度，即使你完全打算在声明之后永远不要改变它们。

虽然将变量视为不可变的是一个好习惯，但是如果没有外部脚本的帮助，JavaScript 不会对引用变量的内容强制这样做，比如用`const`声明的数组和对象。因此，`const`关键字可能会让普通读者和 JavaScript 新手期望得到比它实际提供的更多的保护。

我倾向于将`const`用于简单的数字或字符串变量，我希望对它们进行初始化并且永远不要改变，或者用于命名函数和类，我希望定义一次，然后关闭以进行修改。否则，我对大多数变量声明使用`let`——尤其是那些我希望被定义它们的作用域所约束的变量声明。我最近没有发现使用`var`的必要，但是如果我想要一个声明来打破范围并被提升到我的脚本的顶部，我会这样做。

## 限制函数的范围

使用`function`关键字定义的传统函数可以被调用，以对传入的任何参数执行一系列在块中定义的语句，并可选地返回值:

```
function doSomething(param) {
  return(`Did it: ${param}`);
}
console.log(doSomething("Hello")); // "Did it: Hello" 
```

它们也可以和关键字`new`一起使用来构造具有原型继承的对象，并且该定义可以放在它们可能被调用的范围内的任何地方:

```
function Animal(name) {
  this.name = name;
}
let cat = new Animal("Fluffy");
console.log(`My cat's name is ${cat.name}.`); // "My cat's name is Fluffy." 
```

以这两种方式使用的函数可以在调用之前或之后定义。对 JavaScript 来说无所谓。

```
console.log(doSomething("Hello")); // "Did it: Hello"

let cat = new Animal("Fluffy");
console.log(`My cat's name is ${cat.name}.`); // "My cat's name is Fluffy."

function doSomething(param) {
  return(`Did it: ${param}`);
}
function Animal(name) {
  this.name = name;
} 
```

传统函数也会创建自己的上下文，为`this`定义一个只存在于语句体范围内的值。其中定义的任何语句或子函数都在执行，并允许我们在调用函数时绑定一个值给`this`。

这对于关键字来说是很大的工作量，通常比程序员在任何地方需要的都多。所以现代 JavaScript 将传统函数的行为分成了[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)和类。

### 准时去上课

传统的`function`的一部分已经被`class`关键词接管了。这允许程序员选择他们是更喜欢遵循带有可调用箭头函数的函数式编程范例，还是使用带有类的更面向对象的方法来替代传统函数的原型继承。

JavaScript 中的类在外观和行为上很像其他面向对象语言中的简单类，并且可能是 Java 和 C++开发人员在 JavaScript 扩展到服务器时扩展到 JavaScript 的一个简单的跳板。

在用 JavaScript 进行面向对象编程时，函数和类的一个区别是 JavaScript 中的类需要前向声明，就像在 C++中一样(尽管不是在 Java 中)。也就是说，`class`需要在用`new`关键字实例化之前在脚本中声明。使用`function`关键字的原型继承在 JavaScript 中也有效，即使它是在脚本的后面定义的，因为`function`声明会自动提升到顶部，不像`class`。

```
// Using a function to declare and instantiate an object (hoisted)
let aProto = new Proto("Myra");
aProto.greet(); // "Hi Myra"

function Proto(name) {
  this.name = name;
  this.greet = function() {
    console.log(`Hi ${this.name}`);
  };
};

// Using a class to declare and instantiate an object (not hoisted)
class Classy {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hi ${this.name}`);
  }
};

let aClassy = new Classy("Sonja");
aClassy.greet(); // "Hi Sonja" 
```

### 箭头功能的显著差异

传统函数的另一个方面现在可以使用箭头函数来访问，这是一种新的语法，允许您更简洁地编写可调用的函数，以便更好地适应回调。事实上，arrow 函数最简单的语法是完全去掉花括号的一行，并自动返回所执行语句的结果:

```
const traditional = function(data) {
  return (`${data} from a traditional function`);
}
const arrow = data => `${data} from an arrow function`;

console.log(traditional("Hi")); // "Hi from a traditional function"
console.log(arrow("Hi"));  // "Hi from an arrow function" 
```

Arrow 函数封装了几个特性，使得调用它们更加方便，并且省略了调用函数时其他不太有用的行为。它们不是更通用的传统关键字`function`的替代物。

例如，一个箭头函数从调用它的上下文中继承了`this`和`arguments`。这对于像事件处理或`setTimeout`这样的情况非常有用，因为程序员经常希望被调用的行为应用到被请求的上下文中。传统函数迫使程序员编写复杂的代码，通过使用`.bind(this)`将函数绑定到现有的`this`。对于箭头函数，这些都是不必要的。

```
class GreeterTraditional {
  constructor() {
    this.name = "Joe";
  }
  greet() {
    setTimeout(function () {
      console.log(`Hello ${this.name}`);
    }, 1000); // inner function has its own this with no name
  }
}
let greeterTraditional = new GreeterTraditional();
greeterTraditional.greet(); // "Hello "

class GreeterBound {
  constructor() {
    this.name = "Steven";
  }
  greet() {
    setTimeout(function () {
      console.log(`Hello ${this.name}`);
    }.bind(this), 1000); // passing this from the outside context
  }
}
let greeterBound = new GreeterBound(); // "Hello Steven"
greeterBound.greet();

class GreeterArrow {
  constructor() {
    this.name = "Ravi";
  }
  greet() {
    setTimeout(() => {
      console.log(`Hello ${this.name}`);
    }, 1000); // arrow function inherits this by default
  }
}
let greeterArrow = new GreeterArrow();
greeterArrow.greet(); // "Hello Ravi" 
```

## 了解你得到了什么

这不仅仅是语法上的甜言蜜语。因为需要新的功能，JavaScript 中引入了许多新的变化。但这并不意味着 JavaScript 传统语法的老理由已经消失。通常，继续使用传统的 JavaScript 语法是有意义的，有时使用新的语法可以使您的代码编写得更快，更容易理解。

查看你正在关注的在线教程。如果作者使用`var`来初始化所有的变量，忽略类以支持原型继承，或者依赖回调中的`function`语句，您可以预期其余的语法将基于更老的传统 JavaScript。这很好。今天，我们仍然可以从传统的 JavaScript 教学和使用方式中学习和应用很多东西。但是如果你看到初始化中的`let`和`const`，回调中的箭头函数，以及作为面向对象模式基础的类，你可能也会在例子中看到其他现代 JavaScript 代码。

现代 JavaScript 的最佳实践是关注语言实际在做什么。取决于你习惯了什么，它可能并不总是显而易见的。但是想想你写的代码想要完成什么，你需要把它部署在哪里，下一个修改它的人是谁。然后自己决定什么是最好的方法。

## 分享这篇文章