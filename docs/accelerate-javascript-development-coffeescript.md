# 使用 CoffeeScript 加速您的 JavaScript 开发

> 原文：<https://www.sitepoint.com/accelerate-javascript-development-coffeescript/>

![CoffeeScript Logo](img/adb73e52e7b9cbf44b7cdb7179a11298.png)

CoffeeScript 是一种编译成 JavaScript 的微型语言。其富于表现力而又简洁的语法极大地增加了代码的可读性，从而使代码更容易维护，并且不太可能包含错误。用它的创造者[杰瑞米·阿什肯纳斯](https://twitter.com/jashkenas)的话说，CoffeeScript 允许你“写你想写的东西，而不是在历史偶然的限制下写作”。

编写 CoffeeScript 的另一个好处是，它编译的 JavaScript 可以在旧版本的 Internet Explorer 中运行。CoffeeScript 还可以让您忘记常见的 JS 陷阱，比如尾随逗号和自动分号插入。

而且越来越受欢迎！受 Rails 社区采用 CoffeeScript(Rails 3.1+提供内置 coffee script 支持)的推动， [CoffeeScript 最近进入了 Tiobe 排名前 100 的编程语言索引](http://www.infoworld.com/article/2894714/javascript/survey-developers-want-javascript-alternatives.html)，在那里它被排在第 64 位。这排在 Dart(第 66 位)和 TypeScript(未列出)之前，这两者都编译成 JavaScript。

那么你准备好尝试 CoffeeScript 了吗？在这篇文章中，我将演示如何安装它，以及它的基本概念。

## 装置

您可以使用节点包管理器(npm)全局安装 CoffeeScript，方法是在终端中键入以下命令:

```
npm install coffee-script -g
```

您应该在全局范围内安装它，以便以后可以在终端中用命令`coffee`访问它。

如果你需要使用 npm 的入门知识，那么请参考最近发表的 SitePoint 文章。

### 汇编

CoffeeScript 文件的扩展名为`.coffee`。这些文件要么是手动编译的，要么是您设置了一个监视程序，它将在每次保存不同内容的脚本时编译您的脚本。

要手动编译，请转到脚本所在的目录:

```
cd E:\apps\something\logic
```

并运行以下命令:

```
coffee  -c app.coffee
```

这将在同一个目录中创建一个`app.js`文件，然后您可以将它包含在您的项目中。

然而，您很可能希望每次保存文件时都刷新`app.js`。因此，您可以编译它，并通过键入以下命令添加一个观察器:

```
coffee -cw app.coffee
```

请注意，在最新版本的 CoffeeScript (1.9.1)中有一个[bug 导致观察器无法工作](https://github.com/jashkenas/coffeescript/issues/3863)。以下所有示例都是使用 CoffeeScript v 1.9.0 测试的。

## CoffeeScript 基础

在 CoffeeScript 中，您不必像在 JavaScript 中那样声明变量，尽管通常您需要设置一个初始值。我们也不必在行尾键入分号(`;`)。

这意味着你要写:

```
hasBody = true
```

而不是:

```
var hasBody = true;
```

您也可以在不使用括号的情况下调用函数，但这仅适用于最外层的函数调用。因此，您可以执行以下操作:

```
$(".messages") .show 'slow'
```

而不是:

```
$(".messages").show('slow');
```

缩进在 CoffeeScript 中非常重要。您应该缩进两个空格或一个制表符:

```
if hasBody
  alert "Hello Body"
else
  alert "No Body"
```

## 布尔和条件

在 CoffeeScript 中，关键字`on`、`yes`和`true`都是等价的，指的是布尔值`true`，而`off`、`no`和`false`也是等价的，指的是布尔值`false`。

您可以使用`is`和`isnt`来检查是否相等(`===`和`!==`)。你可以使用`then`来制作单行条件语句。
可以用`and`和`or`分别指代`&&`和`||`。
所有这些都意味着你可以将一个值与另外两个值进行比较，而无需重复自己。

这些概念的一个例子是:

```
x = 53
y = 40
z = 33
b = true
if b is on and x is 53 and z < y > 11 then alert "ALRIGHT!"
```

最后的语句编译为:

```
if (b === true && x === 53 && (z < y && y > 11)) {
  alert('ALRIGHT!');
}
```

## 迭代、过滤器和范围

CoffeeScript 中的`for .. in`语法用于迭代数组，而`for .. of`循环用于迭代对象的属性。

```
arr = [1, 2, 3]

for val in arr
  console.log(val);

spartacus =
  type: "cat"
  legs: 4
  fur: yes

for key, value of spartacus
  console.log "#{key}: #{value}"
```

注意最后一条语句中的字符串插值。这是使用`#{variableName}`语法实现的。

这将输出:

```
1
2
3
type: cat
legs: 4
fur: true
```

您可以结合 CoffeeScript 的`when`关键字来过滤数组中的项目:

```
spartacus =
  type: "cat"
  legs: 4
  fur: yes

shrimpy =
  type: "fish"
  legs: 0
  fur: no

pets = [spartacus, shrimpy]
myPet = pet for pet in pets when pet.type is "cat"
console.log myPet
```

产出:

```
Object {type: "cat", legs: 4, fur: true}
```

请注意，您可以在编写循环之前指定要在循环中执行的语句。这在编写单行循环时很有用。

这也可以写成:

```
for pet in pets when pet.type is "cat"
  myPet = pet
  console.log myPet
```

CoffeeScript 的一个非常有用的特性是创建数值范围的能力。这些可以是包容性的，也可以是排他性的:

```
someValues = [0..10]
sameValues = [0...11]
```

当编译成 JavaScript 时，数组如下所示:

```
someValues = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
sameValues = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
```

如果创建一个包含 20 个以上元素的范围，CoffeScript 会变得更聪明，这可以从生成的 JavaScript 中看出。注意，执行发生在匿名函数中，以防止范围泄漏和变量冲突。

```
var someValues, _i, _results;

someValues = (function() {
  _results = [];
  for (_i = 0; _i <= 21; _i++){ _results.push(_i); }
  return _results;
}).apply(this);
```

## 函数和“this”关键字

要创建函数，您可以使用`->`后跟定义。如果您必须添加参数，您可以在`->`之前的括号()中添加它们

您可以通过将参数设置为等于某个值来添加默认值。

CoffeeScript 的函数示例:

```
myCoffee = ->
  console.log "C'est un cafe"

makeCoffee = (brand = "Hogwarts") ->
  console.log "Making a coffee #{brand}-style"

myCoffee()
makeCoffee()
```

记录到控制台:

```
C'est un cafe
Making a coffee Hogwarts-style
```

您可以使用`@`代替`this`关键字:

```
$("a").click ->
  $(@).hide 'slow'
```

这将编译为:

```
$("a").click(function() {
  return $(this).hide('slow');
});
```

## ES6 风格的类和 OOP

CoffeeScript 也促进了面向对象的编程和继承。您可以像这样定义类:

```
class Animal
  constructor: (@name, @breed) ->
    @.introduce = ->
      console.log "Hi, I am #{@.name}, one hell of a #{@.breed}"

husky = new Animal("Johnny", "Siberian Husky")
husky.introduce()
```

控制台中的输出:

```
Hi, I am Johnny, one hell of a Siberian Husky
```

在构造函数中的参数名称前键入`@`，会导致参数立即在构造函数中设置。或者，你可以只在构造函数中写`@.name = name`。

您也可以扩展类:

```
class Dog extends Animal

Dog.prototype.bark = ->
      console.log "#{@name} barks!"

newHusky = new Dog("Lassy", "Labrador Husky")
newHusky.introduce()
newHusky.bark()
```

此代码输出:

```
Hi, I am Lassy, one hell of a Labrador Husky
Lassy barks!
```

如果在参数后添加`...`(省略号)，您的函数可以接受无限数量的参数。在这种情况下，该参数之后的所有值(包括该参数)都以数组的形式添加。

以下是你如何实现这一点:

```
showAwards = (awards...) ->
  console.log ("Awards collected : #{awards.join ', '}")

showAwards "Award 1", "Award 2", "Award 3"
```

上面的代码输出:

```
Awards collected : Award 1, Award 2, Award 3
```

这里我要提到的最后一点是，当你在任何函数 CoffeeScript 中时，它会自动返回最后一条执行的语句的结果。因此，您的所有函数都有一个隐式返回值(正如您在上面处理锚点击的匿名函数中看到的)。

## 结论

在本文中，我展示了许多使 CoffeeScript 如此令人愉快的特性。我将在以后的文章中利用这些知识，使用 CoffeeScript 创建著名的游戏 TicTacToe。在那之前，让我知道你的想法:你已经在使用 CoffeeScript 了吗？这篇文章有没有让你好奇去尝试一下？还是 CoffeeScript 只提供了一层不必要的复杂性？

## 分享这篇文章