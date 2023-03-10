# Spider:一个令人兴奋的 JavaScript 替代品

> 原文：<https://www.sitepoint.com/spider-exciting-alternative-javascript/>

Spider 是一种新的语言，它试图通过提供更多的可靠性来改进我们的代码。有些人肯定会把它描述成带有 JavaScript 语法的 CoffeeScript，但是这种描述并没有强调 Spider 的真正好处。

Spider 包含了比 CoffeeScript 等大多数替代品更多的独特和有趣的概念。虽然后者肯定比 Spider 更成熟，但通过选择以八条腿的节肢动物命名的语言，我们得到了一些不错的选择。如果我们只是想尝试另一种语言，寻找一种值得信赖的 JavaScript 替代语言，或者尝试少写多做， [Spider](http://spiderlang.org) 似乎是一个不错的选择。

## 基本概念

蜘蛛是围绕它的口号设计的，<q>它只是 JavaScript，但更好。这意味着我们不会得到任何类型的编译类型系统或类型检查器。我们也不会错过我们钟爱的 C 风格语法，块用花括号，函数调用用圆括号，数组用方括号。最后，我们也没有看到基于 JavaScript 或其他任何东西的定制 VM 会破坏与现有 JavaScript 代码的兼容性。没错，这真的是 JavaScript。</q>

Spider 的创建者意识到争论静态语言和动态语言是没有意义的。每一种都有其优点和缺点。使用 Spider 选择完全动态的原因很简单:JavaScript 已经是动态的，当语言包含动态类型系统时，与其他动态代码的交互变得简单得多。

这里应该提到两件更重要的事情:

1.  Spider 被编译成 JavaScript(即 transpiled)
2.  一些特性的灵感来自于像 Go、C#和 CoffeeScript 这样的语言

这些文件不会传输到旧版本的 JavaScript，而是最新的标准 ECMAScript 6。为了保证跨大多数浏览器的支持，Spider 使用 Google 的 Traceur 来生成 ECMAScript 5 兼容文件。这意味着 Spider 已经在利用未来的改进，当前的输出是向后兼容的。

### 句法

Spider 包含了用于访问全局范围的`::`操作符。这可以防止我们在没有意识到的情况下做一些愚蠢的事情。然而，这也意味着我们需要多写一点来访问，例如，`console`对象。下面的语句显示了一个使用`::`运算符的例子:

```
::console.log("Hello world!");
```

一种可能的解决方法是使用`use`语句。它允许我们引用局部未声明的符号。

```
use console;

console.log("Hello world!");
```

Spider 提供了某些宏来解锁一些众所周知的全局对象。根据您正在开发的应用程序的类型，这些宏可能或多或少有用。下面是一个例子:

```
use :browser;

console.log(document.title, window.screen);
```

`:browser`宏允许我们直接使用诸如`document`、`console`、`window`、`location`等对象。对于 DOM 密集型应用程序来说，这是一个非常有用的特性。

没有保留所有以前的逻辑运算符，而是替换了一些。例如，等式和不等式操作符(`==`和`!=`)现在扮演严格等式和严格不等式的角色(JavaScript 中的`===`和`!==`)。“与”(`&&`)和“或”(`||`)运算符也转换值，并分别被重命名为`and`和`or`。这里有一个例子:

```
// x == true;
x = false or 5;

// x == true;
x = 5 and 4;

// x == false;
x = 1 == "1";
```

现在你们中的一些人会尖叫，停止阅读这篇文章，也关闭页面。但是等等…不要走得那么快！

逻辑“与”和逻辑“或”操作符也被滥用于控制流和放置默认值。虽然前者没那么有趣，但后者可以真正节省时间。该语言使用 C#中的空合并运算符`??`来覆盖默认值:

```
x = options.name ?? 'default name';
```

至此，我们已经准备好了解函数了。函数是 JavaScript 如此有趣的原因。蜘蛛不会带走任何东西，除了几个字符:

```
var square = fn (x) {
return x * x;
};
```

在 Spider 中，我们可以写`fn`，而不是写`function`。这使我们不用敲几下键盘，同时保持相同的结构。和 JavaScript 一样，我们可以在函数语句或函数表达式中使用函数。函数语句仅限于命名函数，就像在 JavaScript 中一样。

此外，我们可以在 Java lambda 表达式中使用[函数箭头](https://www.sitepoint.com/javascript-arrow-functions/) `->`(类似于 JavaScript 中的箭头函数)。前面的例子可以表述如下:

```
var square = (x) -> x * x;
```

如果不写块，函数会立即返回提供的表达式。相反，如果你有一个语句块，你需要使用一个`return`语句来返回值。

但是简单的函数箭头是不够的。在 TypeScript 语言中(ECMAScript 6 中也是如此),我们也有粗箭头`=>`操作符。这是一个上下文保持函数箭头。如果你想了解更多关于 JavaScript 中的箭头函数，我建议你阅读文章[准备 ECMAScript 6:新函数语法](https://www.sitepoint.com/preparing-ecmascript-6-new-function-syntax/)。

以下是 Spider 中该运算符的一个示例:

```
fn Animal(name) {
this.name = name;

this.printNameLater = () => {
::setTimeout(() => {
::console.log(this.name);
}, 1000);
};
}
```

函数的另一个特点是能够指定默认参数，并像 ECMAScript 6 中那样使用 rest 参数。前者自动生成代码来检查和修复缺失的(即`undefined`)参数。后者类似于变量参数列表。它基本上将所有附加的未命名参数分组到一个命名数组中:

```
fn format(text, parameters...) {
for parameter, index in parameters
text = text.replace('{' + index + '}', parameter);
return text;
}

format("Hi {0}! My name is {1}.", "World", "Florian");
```

在前面的例子中，我们也看到了 Spider 编写循环的一种优雅方式。我们使用了一个经典的`foreach`循环和一个额外的迭代计数器。Spider 还包含更多这样的特性，我们将在下一节中看到。

### 特征

通过引入更多的一致性，Spider 为 JavaScript 带来了更多的安全性。更一致的方法的例子可以在类型的名称中找到。

```
// "object"
typeof { a: 4 };
// "array"
typeof [1, 2, 3];
// "date"
typeof new Date;
// "number"
typeof new Number(4);
// "string"
typeof new String("abc");
// "boolean"
typeof new Boolean(true);
```

如您所见，数组和日期的类型不同于 JavaScript，它更接近于您(或大多数人)的预期。另一项安全检查可通过存在操作员`?`找到。它将任何表达式转换为对`null`或`undefined`的检查。这非常方便:

```
if game? {
play();
}
```

还有其他变体，即调用属性的`?.`(也叫 Elvis 运算符)或调用函数的`?()`。因此，以下可能是有意义的:

```
game?.play?();
```

这里，如果定义了`game`，我们只访问`play`属性。如果`play`不是一个函数，那么什么都不会被调用。

### 蒸发

我已经提到过，Spider 实际上将文件传输到 ECMAScript 6。作为一个积极的副作用，Spider 是面向未来的，并且使用了今天可以使用的 JavaScript 特性。然而，针对 ES6 也有一个缺点:我们仍然需要另一个 transpiler 来将输出转换为 ES5 或更低版本，它可以被所有现代浏览器(包括旧版本的 Internet Explorer)解释。

为了翻译，我们需要 Spider 编译器。最佳解决方案是安装 [npm 软件包蜘蛛脚本](https://www.npmjs.com/package/spider-script):

```
npm install -g spider-script
```

这还会安装 Traceur、PEG.js 和其他一些依赖项。使用 Traceur 的主要缺点是额外的运行时依赖性。

此时，我们可以访问 Spider 编译器，它被称为`spider`。默认情况下，编译器在 ES5 模式下传输并运行代码，而不会碰到磁盘。但是，有几个选项可以改变这种行为，并使用可选的源映射来编写输出文件。

## 简短的演示

与其停留在理论层面，我希望您通过创建一个小的演示应用程序来实践一下 Spider。我们的目标是尽可能多地使用 Spider 的功能。除此之外，演示还应该使用起来很有趣，所以我们将创建一个简单的游戏。通过创建这个项目，您还将看到 Spider 惊人的继承特性。

### 基本概念

我们将创建一个简单的太空射击游戏，我们的船被描绘成一个三角形，对手被描绘成圆形。任何碰撞都会导致玩家的毁灭。该游戏将使用 HTML5 画布和 2D 绘图上下文来绘制。

我们不会关注图形，因为我们的注意力和兴趣应该集中在代码上。我们将创建一个名为`GameObject()`的构造函数，它也是构造函数`PlayerShip()`和`Asteroid()`的`prototype`。一个对象`game`将集合游戏中的所有对象。

首先，你需要为我们的游戏下载一些资源。我们需要一个很好的背景图像和声音播放，以防发生碰撞。游戏是通过键盘的箭头键控制的。

### 在 Spider 中实现

每个游戏都需要一种资源加载器。如果通过网络装载资源，需求甚至会更高。下面的方法封装了从 promise 中的给定 URL 加载图像的过程:

```
fn loadImage(url) {
return new Promise(fn (fulfill, reject) {
var img = document.createElement('img');
img.src = url;
img.onload = () -> {
fulfill(img);
};
img.onerror = () -> {
reject(img);
};
});
}
```

有趣的是，我们可以简单地在启动例程中使用它，就像我们处理经典的顺序代码一样:

```
background.image = await loadImage('http://i.ytimg.com/vi/qbzFSfWwp-w/maxresdefault.jpg');
```

`background`对象是一种特殊的虚拟游戏对象。构造函数使用一个`GameObject`作为它的原型:

```
fn Background(game)
extends GameObject(game) {
this.draw = () => {
if this.image? {
var ctx = this.game.ctx;
var img = this.image;
var w = ctx.canvas.width;
var h = ctx.canvas.height;
ctx.drawImage(img, 0, 0, img.naturalWidth, img.naturalHeight, -0.5 * w, -0.5 * h, w, h);
}
};
}
```

我们不需要直接指定`prototype`。我们必须表达我们的基本意图，那就是用一个更专门化的函数来扩展`GameObject`构造函数。

游戏还包含其他伪对象。例如，我们可能有一个小行星发电机。在这里，内联循环和范围等特性就派上了用场。我们只想在随机时间和随机位置创造一颗小行星。

获取三个随机数(这里称为`a`、`b`和`c`)可以在一行中完成:

```
fn AsteroidGenerator(game)
extends GameObject(game) {
this.move = () => {
if Math.random() > 0.95 {
var [a, b, c] = [Math.random() for i in [1..3]];
// ...
game.items <- new Asteroid(game, location, velocity, radius);
}
};
}
```

最后，我们还将雇用助手，比如一个简单的`Point`构造函数。举个例子，我们总是可以为任何参数设置一个默认值。这将减少只检查`undefined`并应用默认值的样板代码:

```
fn Point(x = 0, y = 0) {
this.x = x;
this.y = y;
// ...
this.dist = (that) => {
return Math.sqrt(Math.pow(this.x - that.x, 2) + Math.pow(this.y - that.y, 2));
};
}
```

完成的演示应用可在[html5.florian-rappl.de/Spider/](http://html5.florian-rappl.de/Spider/)查看。[原始源代码可以通过 GitHub](https://github.com/sitepoint-editors/SpiderSample) 获得。

### 主要观察结果

让我们回顾一下我们已经看到的功能:

*   `async`和`await`解决回调地狱
*   原型继承变得更加简单
*   简写方法名称使代码更加优雅
*   在许多情况下，范围是很大的
*   默认值很有帮助
*   整个代码更容易阅读

在我看来，阅读蜘蛛代码对你来说并不感到陌生或完全陌生。事实上，这看起来更像是 JavaScript 的自然扩展，而不是一种新语言。

## 结论

Spider 附带了一些方便的新特性，也为表带来了一些一致性。它拥抱 JavaScript 的动态本质，而不是与之对抗。当其他语言试图通过引入编译时特性来应对错误时，Spider 建立在改进的语言规范之上。很多 bug 肯定会永远消失。

在本文中，我们看到了 Spider 的一些独特卖点。我们还构建了一个非常小的示例应用程序，它利用了 Spider 的新特性。使用 Spider 可以实现比我在本文中介绍的更多的功能。出于这个原因，我建议你去 spiderlang.org 查看官方文件。

你对蜘蛛有什么看法？它有一些吸引人的功能吗？或者你对你目前的工作流程完全满意吗？

## 分享这篇文章