# 用 Flow 编写更好的 JavaScript

> 原文：<https://www.sitepoint.com/writing-better-javascript-with-flow/>

你有多少次发现自己在跟踪某个代码中的 bug，却发现这个错误很简单，本来是可以避免的？也许你把参数传递给了一个顺序错误的函数，或者你试图传递一个字符串而不是一个数字？JavaScript 的弱类型系统和试图将变量强制转换为不同类型的意愿可能是静态类型语言中不存在的一类错误的来源。

> **2017 年 3 月 30 日**:文章已更新，以反映流库的变化。

[Flow](https://flow.org/en/) 是脸书在 2014 年[规模会议](http://atscaleconference.com/)上首次推出的 JavaScript 静态类型检查器。它的目标是发现 JavaScript 代码中的类型错误，通常不需要修改我们的实际代码，因此只需要程序员付出很少的努力。同时，它还向 JavaScript 添加了额外的语法，为开发人员提供了更多的控制。

在本文中，我将向您介绍 Flow 及其主要特性。我们将看看如何设置它，如何向代码中添加类型注释，以及如何在运行代码时自动去掉这些注释。

## 装置

Flow 目前可以在 Mac OS X、Linux (64 位)和 Windows (64 位)上运行。最简单的安装方法是通过 npm:

```
npm install --save-dev flow-bin 
```

并将其添加到项目的`package.json`文件中，在`scripts`部分下:

```
"scripts": {
  "flow": "flow"
} 
```

一旦完成，我们就可以开始探索它的特性了。

## 入门指南

名为`.flowconfig`的配置文件必须位于项目文件夹的根目录下。我们可以通过运行以下命令创建一个空的配置文件:

```
npm run flow init 
```

一旦配置文件存在，您就可以在终端上运行以下命令，对项目文件夹及其子文件夹中的代码进行特别检查:

```
npm run flow check 
```

然而，这并不是使用 Flow 最有效的方式，因为它会导致 Flow 每次都重新检查整个项目的文件结构。相反，我们可以使用流服务器。

流服务器递增地检查文件，这意味着它只检查已经改变的部分。可以通过在终端上运行命令`npm run flow`来启动服务器。

第一次运行该命令时，服务器将启动并显示初始测试结果。这允许更快和增量的工作流程。每次想知道测试结果，在终端上运行`flow`。完成编码会话后，您可以使用`npm run flow stop`停止服务器。

流的类型检查是**选择加入**。这意味着你不需要一次检查所有的代码。您可以选择想要检查的文件，Flow 会为您完成这项工作。这种选择是通过在您希望由流检查的任何 JavaScript 文件的顶部添加`@flow`作为注释来完成的:

```
/*@flow*/ 
```

当您试图将 Flow 集成到现有项目中时，这非常有帮助，因为您可以选择想要逐个检查的文件并解决任何错误。

## 类型推理

通常，类型检查可以通过两种方式完成:

*   **Via annotations** :我们指定我们期望作为代码一部分的类型，类型检查器根据这些期望评估代码
*   **通过代码推断**:该工具足够智能，可以通过查看使用变量的上下文来推断预期的类型，并基于此检查代码

对于注释，我们必须编写一些额外的代码，这些代码只在开发过程中有用，并从浏览器将加载的最终 JavaScript 构建中剥离出来。这需要预先做一些额外的工作，通过添加额外的类型注释来使代码可检查。

在第二种情况下，代码已经准备好进行测试，无需任何修改，因此最大限度地减少了程序员的工作量。它不会强迫你改变编码方式，因为它会自动推导出表达式的数据类型。这被称为**类型推理**，并且是流的最重要的特征之一。

为了说明这个特性，我们可以以下面的代码为例:

```
/*@flow*/

function foo(x) {
  return x.split(' ');
}

foo(34); 
```

当您运行`npm run flow`命令时，这段代码将在终端上给出一个错误，因为函数`foo()`需要一个字符串，而我们传递了一个数字作为参数。

该错误类似于以下内容:

```
index.js:4
  4:   return x.split(' ');
                ^^^^^ property `split`. Property not found in
  4:   return x.split(' ');
              ^ Number 
```

它清楚地说明了错误的位置和原因。只要我们将参数从数字更改为任意字符串，如下面的代码片段所示，错误就会消失。

```
/*@flow*/

function foo(x) {
  return x.split(' ');
};

foo('Hello World!'); 
```

正如我所说的，上面的代码不会给出任何错误。我们在这里可以看到，Flow 理解`split()`方法只适用于一个`string`，因此它期望`x`是一个`string`。

### 可空类型

与其他类型的系统相比，Flow 以不同的方式处理`null`。它没有忽略`null`，因此它防止了可能导致应用程序崩溃的错误，其中传递的是`null`，而不是其他有效类型。

考虑以下代码:

```
/*@flow*/

function stringLength (str) {
  return str.length;
}

var length = stringLength(null); 
```

在上面的例子中，Flow 将抛出一个错误。要解决这个问题，我们必须单独处理`null`,如下所示:

```
/*@flow*/

function stringLength (str) {
  if (str !== null) {
    return str.length;
  }

  return 0;
}

var length = stringLength(null); 
```

我们引入了对`null`的检查，以确保代码在所有情况下都能正确运行。流会将最后一个代码片段视为有效代码。

## 键入注释

正如我上面提到的，类型推断是 Flow 最好的特性之一，因为我们可以获得有用的反馈，而不必编写类型注释。然而，在某些情况下，向代码添加注释对于提供更好的检查和消除歧义是必要的。

请考虑以下情况:

```
/*@flow*/

function foo(x, y){
  return x + y;
}

foo('Hello', 42); 
```

Flow 不会在上面的代码中发现任何错误，因为`+`(加号)运算符可以用在字符串和数字上，我们没有指定`add()`的参数必须是数字。

在这种情况下，我们可以使用类型注释来指定所需的行为。类型注释以`:`(冒号)为前缀，可以放在函数参数、返回类型和变量声明上。

如果我们在上面的代码中添加类型注释，它将变成如下所示:

```
/*@flow*/

function foo(x : number, y : number) : number {
  return x + y;
}

foo('Hello', 42); 
```

这段代码显示了一个错误，因为函数需要数字作为参数，而我们提供的是一个字符串。

终端上显示的错误如下所示:

```
index.js:7
  7: foo('Hello', 42);
         ^^^^^^^ string. This type is incompatible with the expected param type of
  3: function foo(x : number, y : number) : number{
                      ^^^^^^ number 
```

如果我们传递一个数字而不是`'Hello'`，就不会有任何错误。在大型复杂的 JavaScript 文件中，类型注释对于指定所需的行为也很有用。

记住前面的例子，让我们看看 Flow 支持的各种其他类型的注释。

### 功能

```
/*@flow*/

/*--------- Type annotating a function --------*/
function add(x : number, y : number) : number {
  return x + y;
}

add(3, 4); 
```

上面的代码显示了一个变量和一个函数的注释。`add()`函数的参数以及返回值应该是数字。如果我们传递任何其他数据类型，Flow 将抛出一个错误。

### 数组

```
/*-------- Type annotating an array ----------*/
var foo : Array<number> = [1,2,3]; 
```

数组注释采用`Array<T>`的形式，其中`T`表示数组中各个元素的数据类型。在上面的代码中，`foo`是一个数组，它的元素应该是数字。

### 班级

下面给出了类和对象的示例模式。需要记住的唯一一点是，我们可以使用`|`符号在两种类型之间执行 OR 运算。变量`bar1`是根据`Bar`类的模式进行注释的。

```
/*-------- Type annotating a Class ---------*/
class Bar{
  x:string;           // x should be string 
  y:string | number;  // y can be either a string or a number
  constructor(x,y){
    this.x=x;
    this.y=y;
  }
}

var bar1 : Bar = new Bar("hello",4); 
```

### 对象文字

我们可以用与类相似的方式注释对象文字，指定对象属性的类型。

```
/*--------- Type annonating an object ---------*/

var obj : {a : string, b : number, c: Array<string>, d : Bar} = {
  a : "hello",
  b : 42,
  c : ["hello", "world"],
  d : new Bar("hello",3)
} 
```

### 空

任何类型的`T`都可以通过写`?T`而不是`T`来包含`null` / `undefined`，如下所示:

```
/*@flow*/

var foo : ?string = null; 
```

在这种情况下，`foo`可以是字符串，也可以是`null`。

在这里，我们只是触及了 Flow 的类型注释系统的表面。一旦你习惯了使用这些基本类型，我建议你深入研究一下 Flow 网站上的类型文档。

## 库定义

我们经常面临这样的情况，我们不得不在代码中使用第三方库中的方法。在这种情况下，Flow 会抛出一个错误，但是通常，我们不希望看到这些错误，因为它们会分散我们检查自己代码的注意力。

谢天谢地，我们不需要接触库代码来防止这些错误。相反，我们可以创建一个库定义(libdef)。libdef 只是 JavaScript 文件的一个花哨术语，它包含第三方代码提供的函数或方法的声明。

让我们看一个例子来更好地理解我们正在讨论的内容:

```
/* @flow */

var users = [
  { name: 'John', designation: 'developer' },
  { name: 'Doe', designation: 'designer' }
];

function getDeveloper() {
  return _.findWhere(users, {designation: 'developer'});
} 
```

该代码将给出以下错误:

```
interfaces/app.js:9
  9:   return _.findWhere(users, {designation: 'developer'});
              ^ identifier `_`. Could not resolve name 
```

这个错误是因为 Flow 不知道任何关于`_`变量的事情而产生的。要解决这个问题，我们需要为下划线引入一个 libdef。

### 使用流式

幸运的是，有一个名为[流类型](https://github.com/flowtype/flow-typed)的存储库，其中包含许多流行的第三方库的 libdef 文件。要使用它们，您只需将相关定义下载到项目根目录下名为`flow-typed`的文件夹中。

为了进一步简化这个过程，有一个命令行工具可用于获取和安装 libdef 文件。它是通过 npm 安装的:

```
npm install -g flow-typed 
```

一旦安装完毕，运行`flow-typed install`将检查您的项目的`package.json`文件，并下载 libdefs 以查找它找到的任何依赖项。

### 创建自定义库

如果您使用的库在流类型库中没有可用的 libdef，那么可以创建自己的库。我不会在这里详述，因为这是你不应该经常需要做的事情，但是如果你感兴趣，你可以查看文档。

## 剥离类型注释

由于类型注释不是有效的 JavaScript 语法，我们需要在浏览器中执行之前将它们从代码中剥离出来。这可以通过使用[流移除类型工具](https://github.com/flowtype/flow-remove-types)或者作为[巴别塔预置](https://babeljs.io/docs/plugins/preset-flow/)来完成，如果你已经在使用巴别塔来传输你的代码的话。在本文中，我们将只讨论第一种方法。

首先，我们需要安装 flow-remove-types 作为项目依赖项:

```
npm install --save-dev flow-remove-types 
```

然后我们可以向我们的`package.json`文件添加另一个`script`条目:

```
"scripts": {
  "flow": "flow",
  "build": "flow-remove-types src/ -D dest/",
} 
```

该命令将从`src`文件夹中的文件中删除所有类型注释，并将编译后的版本存储在`dist`文件夹中。编译后的文件可以像其他 JavaScript 文件一样加载到浏览器中。

作为构建过程的一部分，[的几个模块捆绑器](https://github.com/flowtype/flow-remove-types#use-in-build-systems)可以使用插件来去除注释。

## 结论

在本文中，我们讨论了 Flow 的各种类型检查特性，以及它们如何帮助我们捕捉错误并提高代码质量。我们还看到了 Flow 如何通过在每个文件的基础上“选择”并进行类型推断来使开始变得非常容易，这样我们就可以开始获得有用的反馈，而不必在整个代码中添加注释，

你觉得 JavaScript 的静态类型检查怎么样？这是有用的东西，还是给现代 JavaScript 带来更多复杂性的另一个不必要的工具？这篇文章是否鼓励你自己去体验心流？欢迎在下面分享你的想法、疑问或评论。

## 分享这篇文章