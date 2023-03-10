# JavaScript 中的变量赋值和变异指南

> 原文：<https://www.sitepoint.com/variable-assignment-mutation-javascript/>

在 JavaScript 的世界里，突变是你经常听到的事情，但是它们到底是什么，它们有想象中那么邪恶吗？

在本文中，我们将介绍变量赋值和变异的概念，并看看为什么它们会让开发人员感到痛苦。我们将研究如何管理它们以避免问题，如何尽可能少地使用它们，以及如何保持代码的可预测性。

如果你想更详细地探索这个话题，或者了解现代 JavaScript，请免费阅读我的新书 [**的第一章【学习用 JavaScript 编码**](https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/read/) 。

让我们从回到值类型的基础开始…

## 数据类型

JavaScript 中的每个值要么是原始值，要么是对象。有七种不同的原始数据类型:

*   数字，如`3`、`0`、`-4`、`0.625`
*   字符串，如`'Hello'`、`"World"`、``Hi``、`''`
*   布尔，`true`和`false`
*   `null`
*   `undefined`
*   符号——保证永远不会与另一个符号冲突的独特标记
*   `BigInt` —用于处理大整数值

任何不是原始值的东西都是一个**对象**，包括数组、日期、正则表达式，当然还有对象文字。函数是一种特殊类型的对象。它们肯定是对象，因为它们有属性和方法，但是它们也可以被调用。

## 变量赋值

变量赋值是你在编码中首先要学习的事情之一。例如，这就是我们将数字`3`赋给变量`bears`的方式:

```
const bears = 3; 
```

变量的一个常见比喻是一个带有标签的盒子，标签内有值。上面的例子将被描绘成一个包含标签“bears”的盒子，其中放置了值 3。

![variables like a box](img/39e43831a55cd763fd8f8da624b7afa5.png)

思考发生什么的另一种方式是作为引用，将标签`bears`映射到值`3`:

![variables like a reference](img/7d83931d581330be87c2d5bfb168ed92.png)

如果我把数字`3`赋给另一个变量，它引用的值和熊一样:

```
let musketeers = 3; 
```

![variables referencing the same value](img/16f51d7a603fbff297060f8dd27faa0f.png)

变量`bears`和`musketeers`都引用相同的原始值 3。我们可以使用严格的等式运算符`===`来验证这一点:

```
bears === musketeers
<< true 
```

如果两个变量引用相同的值，相等运算符返回`true`。

### 使用对象时的一些问题

前面的例子显示了原始值被分配给变量。分配对象时使用相同的过程:

```
const ghostbusters = { number: 4 }; 
```

这种赋值意味着变量`ghostbusters`引用一个对象:

![variables referencing different objects](img/2fe2e794a30da981bc4b688535c970f7.png)

然而，将对象赋给变量的一个很大的区别是，如果您将另一个对象文字赋给另一个变量，它将引用一个完全不同的对象——即使两个对象文字看起来完全一样！例如，下面的赋值看起来像变量`tmnt`(忍者神龟)引用了与变量`ghostbusters`相同的对象:

```
let tmnt = { number: 4 }; 
```

尽管变量`ghostbusters`和`tmnt`看起来引用了同一个对象，但实际上它们都引用了一个完全不同的对象，如果我们使用严格的等式运算符，就可以看到这一点:

```
ghostbusters === tmnt
<< false 
```

![variables referencing different objects](img/d08a3af59290817c1aa2890cee685da0.png)

## 变量再分配

当 ES6 中引入`const`关键字时，许多人误以为 JavaScript 中引入了常量，但事实并非如此。这个关键词的名字有点误导。

任何用`const`声明的变量都不能被重新赋值。这适用于原始值和对象。例如，变量`bears`在上一节中是用`const`声明的，所以它不能被赋予其他值。如果我们试图将数字 2 赋给变量`bears`，我们会得到一个错误:

```
bears = 2;
<< TypeError: Attempted to assign to readonly property. 
```

对数字 3 的引用是固定的，不能给变量`bears`重新赋值。

这同样适用于对象。如果我们试图给变量`ghostbusters`分配一个不同的对象，我们会得到同样的错误:

```
ghostbusters = {number: 5};
TypeError: Attempted to assign to readonly property. 
```

### 使用`let`重新分配变量

当关键字`let`被用来声明一个变量时，它可以在我们代码的后面被重新分配来引用一个不同的值。例如，我们使用`let`声明了变量`musketeers`，因此我们可以更改`musketeers`引用的值。如果达达尼昂加入火枪手，他们的人数会增加到 4:

```
musketeers = 4; 
```

![variables referencing different values](img/e5cf645a8bebd24a55bc689f02a43dd5.png)

这是可以做到的，因为使用了`let`来声明变量。我们可以随意多次修改`musketeers`引用的值。

变量`tmnt`也是用`let`声明的，所以它也可以被重新分配来引用另一个对象(或者完全不同的类型，如果我们愿意的话):

```
tmnt = {number: 5}; 
```

注意，变量`tmnt`现在引用了一个*完全不同的对象*；我们不只是将`number`属性更改为 5。

**总的来说**，如果你使用`const`声明一个变量，它的值不能被重新赋值，并且总是引用它最初被赋值的原始值或对象。如果你使用`let`声明一个变量，它的值可以在程序中根据需要多次重新赋值。

尽可能频繁地使用`const`通常被认为是良好的实践，因为这意味着变量的值保持不变，代码更加一致和可预测，从而更不容易出错。

## 引用变量赋值

在原生 JavaScript 中，只能给变量赋值。你不能给变量赋值来引用另一个变量，即使看起来你可以。例如，活宝的数量与火枪手的数量相同，因此我们可以使用下面的方法将变量`stooges`指定为引用与变量`musketeers`相同的值:

```
const stooges = musketeers; 
```

看起来变量`stooges`引用了变量`musketeers`，如下图所示:

![variables cannot reference another variable](img/e60cbecf925c1bae290fbbc5528ef815.png)

然而这在原生 JavaScript 中是不可能的:**一个变量只能引用一个实际值；它不能引用另一个变量**。当你像这样赋值时，实际发生的是，赋值左边的变量将引用右边变量引用的值，所以变量`stooges`将引用与变量`musketeers`相同的值，即数字 3。一旦赋值完成，`stooges`变量就不再与`musketeers`变量相连。

![variables referencing values](img/ad1ed2d4762c72dd26cf1b04bb92e463.png)

这意味着如果达达尼昂加入火枪手，我们将`musketeers`的值设置为 4，`stooges`的值将保持为 3。事实上，因为我们使用`const`声明了`stooges`变量，所以不能将其设置为任何新值；永远都是 3。

**总之**:如果你用`const`声明一个变量，并把它设置为一个原始值，即使是通过引用另一个变量，它的值也不能改变。这对你的代码有好处，因为这意味着它将更加一致和可预测。

## 突变

如果一个值可以被改变，那么这个值就是可变的。这就是全部内容:一个*突变*是改变一个值的属性的行为。

JavaScript 中所有原始值都是*不可变的*:你永远不能改变它们的属性。例如，如果我们将字符串`"cake"`赋给变量`food`，我们可以看到我们不能改变它的任何属性:

```
const food = "cake"; 
```

如果我们试着把第一个字母改成“f”，看起来好像变了:

```
food[0] = "f";
<< "f" 
```

但是如果我们看一下变量的值，我们会发现实际上什么都没有改变:

```
food
<< "cake" 
```

如果我们尝试更改 length 属性，也会发生同样的情况:

```
food.length = 10;
<< 10 
```

尽管返回值暗示 length 属性已经更改，但快速检查表明它并没有更改:

```
food.length
<< 4 
```

注意，这与使用`const`而不是`let`声明变量无关。如果我们使用了`let`，我们可以设置`food`来引用另一个字符串，但是我们不能改变它的任何属性。不可能改变原始数据类型的任何属性，因为它们是*不可变的*。

### JavaScript 中的可变性和对象

相反，JavaScript 中的所有对象都是可变的，这意味着它们的属性可以改变，即使它们是用`const`声明的(记住`let`和`const`只控制变量是否可以被重新赋值，与可变性无关)。例如，我们可以使用以下代码更改数组的第一项:

```
const food = ['🍏','🍌','🥕','🍩'];
food[0] = '🍎';
food
<< ['🍎','🍌','🥕','🍩'] 
```

请注意，尽管我们使用`const`声明了变量`food`，但这种变化仍然发生了。这说明使用`const`T3 并不能阻止物体变异。

我们也可以改变数组的长度属性，即使它是用`const`声明的:

```
food.length = 2;
<< 2
food
<< ['🍎','🍌'] 
```

## 通过引用复制

请记住，当我们将变量赋给对象文字时，变量将引用完全不同的对象，即使它们看起来相同:

```
const ghostbusters = {number: 4};
const tmnt = {number: 4}; 
```

![variables referencing different objects](img/d08a3af59290817c1aa2890cee685da0.png)

但是如果我们将一个变量`fantastic4`赋给另一个变量，它们都将引用*同一个*对象:

```
const fantastic4 = tmnt; 
```

这将变量`fantastic4`分配给引用变量`tmnt`所引用的同一个对象的*，而不是一个完全不同的对象。*

![variables referencing the same object](img/18069173705fd4b69e5ed5707df6ced2.png)

这通常被称为通过引用复制**，因为两个变量都被指定引用同一个对象*。***

这很重要，因为对这个对象的任何改变都会在两个变量中看到。

因此，如果蜘蛛侠加入了神奇四侠，我们可能会更新对象中的`number`值:

```
fantastic4.number = 5; 
```

这是一个突变，因为我们已经改变了`number`属性，而不是设置`fantastic4`来引用一个新对象。

这给我们带来了一个问题，因为`tmnt`的`number`属性也会改变，可能我们甚至没有意识到:

```
tmnt.number
<< 5 
```

这是因为`tmnt`和`fantastic4`都引用同一个对象，所以对`tmnt`或`fantastic4`的任何改变都会影响它们。

这突出了 JavaScript 中的一个重要概念:当对象被引用复制并随后变异时，这种变异将影响引用该对象的任何其他变量。这可能会导致意想不到的副作用和难以追踪的错误。

## 传播运营商来救援！

那么，如何在不创建对原始对象的引用的情况下复制一个对象呢？答案是使用[扩散运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)！

在 ES2015 中为数组和字符串引入了扩展运算符，在 ES2018 中为对象引入了扩展运算符。它允许您轻松地创建对象的浅层副本，而无需创建对原始对象的引用。

下面的例子显示了我们如何设置变量`fantastic4`来引用对象`tmnt`的副本。这个副本将与`tmnt`对象完全相同，但是`fantastic4`将引用一个全新的对象。这是通过将要复制的变量的名称放在对象文字中，并在它的前面加上 spread 运算符来实现的:

```
const tmnt = {number: 4};
const fantastic4 = {...tmnt}; 
```

我们在这里实际做的是将变量`fantastic4`赋给一个新的对象文字，然后使用 spread 操作符复制由`tmnt`变量引用的对象的所有可枚举属性。因为这些属性是值，所以它们通过值而不是引用被复制到`fantastic4`对象中。

![variables referencing different objects](img/fd08c8ebf38e701f1b4e10f9e53207ca.png)

现在，对任何一个对象所做的任何更改都不会影响另一个对象。例如，如果我们将`fantastic4`变量的`number`属性更新为 5，它不会影响`tmnt`变量:

```
fantastic4.number = 5;
fantastic4.number
<< 5
tmnt.number
<< 4 
```

![Changes don't affect the other object](img/87396d5bac39e04b2c81ba09541bf072.png)

spread 操作符还有一个有用的快捷符号，可以用来复制一个对象，然后在一行代码中对新对象进行一些更改。

例如，假设我们想要创建一个对象来模拟忍者神龟。我们可以创建第一个 turtle 对象，并将变量`leonardo`赋给它:

```
const leonardo = {
  animal: 'turtle',
  color: 'blue',
  shell: true,
  ninja: true,
  weapon: 'katana'
} 
```

其他海龟都有相同的属性，除了`weapon`和`color`属性，每个海龟的属性都不一样。使用 spread 操作符复制一个`leonardo`引用的对象，然后更改`weapon`和`color`的属性是有意义的，如下所示:

```
const michaelangelo = {...leonardo};
michaelangelo.weapon = 'nunchuks';
michaelangelo.color = 'orange'; 
```

我们可以在一行中通过在对 spread 对象的引用之后添加我们想要更改的属性来实现这一点。下面是为变量`donatello`和`raphael`创建新对象的代码:

```
const donatello = {...leonardo, weapon: 'bo staff', color: 'purpple'}
const raphael = {...leonardo, weapon: 'sai', color: 'purple'} 
```

注意，以这种方式使用 spread 操作符只会产生一个对象的[浅拷贝](https://stackoverflow.com/q/184710/1136887)。要进行深层复制，你必须递归地进行，或者使用一个库。就我个人而言，我建议你尽量保持你的对象尽可能的浅。

## 突变是坏事吗？

在本文中，我们已经讨论了变量赋值和变异的概念，并了解了为什么它们会让开发人员感到头疼。

突变名声不好，但它们本身不一定是坏的。事实上，如果你正在构建一个动态的 web 应用程序，它必须在某个时候改变。这就是“动态”这个词的字面意思！这意味着你的代码中会有一些突变。话虽如此，突变越少，代码就越容易预测，更容易维护，也不太可能出现任何错误。

一个特别有害的组合是引用复制和突变。这可能会导致你甚至没有意识到已经发生的副作用和错误。如果你改变了一个被代码中的另一个变量引用的对象，这可能会导致很多难以追踪的问题。关键是尽量减少使用必要的突变，并跟踪哪些对象发生了突变。

在函数式编程中，纯函数是不会引起任何副作用的函数，而突变是产生副作用的最大原因之一。

黄金法则是避免通过引用复制任何对象。如果要复制另一个对象，请使用 spread 运算符，然后在复制后立即进行任何变异。

接下来，我们将研究 JavaScript 中的[数组突变。](https://www.sitepoint.com/immutable-array-methods-javascript/)

如果你想了解现代 JavaScript，别忘了看看我的新书 [**学习用 JavaScript 编码**](https://www.sitepoint.com/premium/books/learn-to-code-with-javascript/read/1) 。你可以免费阅读第一章。如果您有任何问题或意见，请联系 [Twitter](https://twitter.com/daz4126) ！

## 分享这篇文章