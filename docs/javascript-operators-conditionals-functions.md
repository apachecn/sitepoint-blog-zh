# 回到基础:JavaScript 操作符、条件和函数

> 原文：<https://www.sitepoint.com/javascript-operators-conditionals-functions/>

在钻研用 JavaScript 创建程序之前，牢固掌握基础知识是非常重要的。在本文中，我们将回顾 JavaScript 的一些最重要的基本概念，这些概念将允许您开始编写自己的程序:运算符、条件语句和函数。

![JavaScript operators, conditionals and functions](img/31ef0a0d6d08783c40ffa7af8dbae885.png)

### 目录 

*   [JavaScript 运算符](#javascriptoperators)
*   [赋值运算符](#assignmentoperators)
*   [算术运算符](#arithmeticoperators)
*   [加法](#addition)
*   [减法](#subtraction)
*   [乘法运算](#multiplication)
*   [分部](#division)
*   [模数](#modulus)
*   [增量](#increment)
*   [减量](#decrement)
*   [比较运算符](#comparisonoperators)
*   [等于](#equal)
*   [严格相等](#strictequal)
*   [不等于](#notequal)
*   [严格不等于](#strictnotequal)
*   [小于](#lessthan)
*   [小于或等于](#lessthanorequalto)
*   [大于](#greaterthan)
*   [大于或等于](#greaterthanorequalto)
*   [逻辑运算符](#logicaloperators)
*   [和](#and)
*   [或](#or)
*   [不是](#not)
*   [运算符优先级](#operatorprecedence)
*   [条件语句](#conditionalstatements)
*   [如果/否则](#ifelse)
*   [如果](#if)
*   [否则](#else)
*   [否则如果](#elseif)
*   [开关](#switch)
*   [功能](#functions)
*   [申报](#declaration)
*   [调用](#invocation)
*   [参数和自变量](#parametersandarguments)
*   [结论](#conclusion)
*   [评论](#comments)

在我们开始之前，您应该了解基本的 JavaScript 语法、注释、数据类型以及给变量赋值。您可以在[JavaScript 变量和数据类型初学者指南](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/)中了解或回顾所有这些信息。

> **免责声明:**本指南面向 JavaScript 和编程的完全初学者。因此，许多概念将以简化的方式呈现，并且将使用严格的 ES5 语法。

准备好了吗？我们开始吧！

## JavaScript 运算符

JavaScript **操作符**是用来对数据执行不同操作的符号。JavaScript 中有几种类型的运算符，在这一课中我们将学习最常见的运算符:赋值运算符、算术运算符、比较运算符和逻辑运算符。

### 赋值运算符

**赋值运算符**，以其最基本的形式，将数据应用于变量。在这个例子中，我将把字符串`"Europe"`赋给变量`continent`。

```
var continent = "Europe"; 
```

赋值用等号(`=`)表示。虽然还有其他类型的赋值操作符，你可以在这里查看，但这是最常见的。

您可以通过使用`console.log()`函数，或者通过使用控制台的[来测试本文中的所有示例。](https://developers.google.com/web/tools/chrome-devtools/console/)

### 算术运算符

与所有编程语言一样，JavaScript 具有内置的数学能力，就像计算器一样。**算术运算符**对数字或代表数字的变量进行数学计算。你已经知道其中最常见的——加法、减法、乘法和除法。

#### 添加

由加号(`+`)表示的加法运算符将两个值相加并返回总和。

```
var x = 2 + 2; // x returns 4 
```

#### 减法

由减号(`-`)表示的**减法**运算符将两个值相减并返回差值。

```
var x = 10 - 7; // x returns 3 
```

#### 增加

由星号(`*`)表示的**乘法**运算符将两个值相乘并返回乘积。

```
var x = 4 * 5; // x returns 20 
```

#### 分开

由正斜杠(`/`)表示的**除法**运算符将两个值相除并返回商。

```
var x = 20 / 2; // x returns 10 
```

#### 系数

稍微不太熟悉的是**模数**运算符，它返回除法运算后的余数，用百分号(`%`)表示。

```
var x = 10 % 3; // returns 1 
```

`3`进入`10`三次，余数为`1`。

#### 增量

用**增量**操作符将一个数字加 1，用一个双加号(`++`)表示。

```
var x = 10;
x++; // x returns 11 
```

这发生在分配任务之后。也可以在赋值之前写`++x;`。比较:

```
var x = 10;
var y = x++;
// y is 10, x is 11 
```

并且:

```
var x = 10;
var y = ++x;
// y is 11, x is 11 
```

#### 减量

用**递减**操作符将一个数字递减 1，用一个双减号(`--`)表示。

```
var x = 10;
x--; // x returns 9 
```

如上，也可以写成`--x;`。

### 比较运算符

**比较运算符**将计算两个值的相等或不同，并返回`true`或`false`。它们通常用在逻辑语句中。

#### 平等的

两个等号(`==`)在 JavaScript 中表示**等于**。当你第一次学习时，很容易混淆单个、双个和三个等号，但是请记住，单个等号将值应用于变量，并且从不计算相等。

```
var x = 8;
var y = 8;

x == y; // true 
```

这是一种松散类型的等式，即使使用字符串而不是数字，也将返回`true`。

```
var x = 8;
var y = "8";

x == y; // true 
```

#### 严格相等

三个等号(`===`)在 JavaScript 中表示**严格等于**。

```
var x = 8;
var y = 8;

x === y; // true 
```

这是一种比常规等号(`==`)更常用、更准确的确定相等的形式，因为它要求类型和值都相同才能返回`true`。

```
var x = 8;
var y = "8";

x === y; // false 
```

#### 不相等

在 JavaScript 中，感叹号后跟等号(`!=`)意味着**不等于**。这和`==`正好相反，只会测试值，不会测试类型。

```
var x = 50;
var y = 50;

x != y; // false 
```

它会将这个字符串和数字视为相等。

```
var x = 50;
var y = "50";

x != y; // false 
```

#### 严格不等于

在 JavaScript 中，一个感叹号后跟两个等号(`!==`)意味着**严格来说不等于**。这和`===`正好相反，会同时测试值和类型。

```
var x = 50;
var y = 50;

x !== y; // false 
```

它会将这个字符串和数字视为不相等。

```
var x = 50;
var y = "50";

x !== y; // true 
```

#### 不到

另一个熟悉的符号，**小于** ( `<`)将测试左边的值是否小于右边的值。

```
var x = 99;
var y = 100;

x < y; // true 
```

#### 小于或等于

**小于等于** ( `<=`)同上，但等于也会求值为`true`。

```
var x = 100;
var y = 100;

x <= y; // true 
```

#### 大于

**大于** ( `>`)将测试左边的值是否大于右边的值。

```
var x = 99;
var y = 100;

x > y; // false 
```

#### 大于或等于

**大于等于** ( `>=`)同上，但等于也会算到`true`。

```
var x = 100;
var y = 100;

x >= y; // true 
```

### 逻辑运算符

逻辑语句通常会使用我们刚刚学过的比较运算符来确定一个`true`或`false`值。在这些语句中可以使用另外三个运算符来测试`true`或`false`。

在讨论条件语句之前，理解这些操作符是很重要的。

#### 和

**和**用两个&符号(`&&`)表示。如果`&&`左右两边的语句都计算为`true`，则整个语句返回`true`。

```
var x = 5;

x > 1 && x < 10; // true 
```

在上面的例子中，`x`等于`5`。用我的逻辑语句，我在测试`x`是否大于`1`并且小于`10`，事实就是如此。

```
var x = 5;

x > 1 && x < 4; // false 
```

上面的例子返回`false`，因为即使`x`大于`1`，但是`x`不小于`4`。

#### 或者

**或**由两根管子(`||`)表示。如果`||`左侧和右侧的任一条语句的计算结果为`true`，该语句将返回`true`。

```
var x = 5;

x > 1 || x < 4; // true 
```

`x`不小于`4`，但大于`1`，所以语句返回`true`。

#### 不

最后一个逻辑运算符是**不是**，用感叹号(`!`)表示，如果语句是`true`则返回`false`，如果语句是`false`则返回`true`。如果存在一个值，它也返回`false`(不等于`false`)。花点时间消化一下…

```
var x = 99;

!x // false 
```

由于`x`存在并且有值，`!x`将返回`false`。我们还可以测试一个布尔值——如果值是`false`，我们可以使用`!`操作符来测试它，它将返回`true`。

```
var x = false;

!x // true 
```

这个操作符现在看起来可能有点混乱，但是当我们进入下一节——条件语句时，它就有意义了。

### 运算符优先级

当你在学校学数学的时候，你可能已经学会了 PEMDAS ( *请原谅我亲爱的萨莉阿姨*)的首字母缩略词来学习运算的顺序。这代表“括号、指数、乘法、除法、加法、减法”——数学运算必须执行的顺序。

同样的概念也适用于 JavaScript，只是它包含了更多类型的操作符。欲查看操作员优先级的完整表格，[查看 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence) 上的参考。

在我们学习的运算符中，以下是正确的运算顺序，从最高到最低优先级。

*   分组(`()`)
*   不(`!`)
*   乘法(`*`)
*   除法(`/`)
*   模数(`%`)
*   加法(`+`)
*   减法(`-`)
*   小于(`<`)
*   小于或等于(`<=`)
*   大于(`>`)
*   大于或等于(`>=`)
*   相等(`=`)
*   不相等(`!=`)
*   严格相等(`===`)
*   严格不等于(`!==`)
*   和(`&&`)
*   或者(`||`)
*   赋值(`=`)

举例来说，您希望下面代码片段中的`x`的值是什么？

```
var x = 15 - 5 * 10; 
```

如果你说`-35`，那就做得好。产生这个结果的原因是乘法运算符优先于减法运算符，JavaScript 引擎在从`15`中减去结果之前首先计算`5 * 10`。

要改变运算符优先级，可以使用括号。

```
var x = (15 - 5) * 10;
// x is 100 
```

## 条件语句

如果你曾经遇到过 JavaScript 代码块，你很可能会注意到熟悉的英文单词`if`和`else`。这些是**条件语句**，或者根据条件是`true`还是`false`来执行的代码块。

我们刚刚学习的所有比较和逻辑运算符在计算这些语句时都会派上用场。

条件语句可以被认为是基于不同结果产生不同结果的流程图。

### If / Else

#### 如果

一个 **if 语句**将总是用关键字`if`编写，后跟一个括号中的条件(`()`)，以及花括号中要执行的代码(`{}`)。这将被写成`if () {}`。由于`if`语句通常包含大量的代码，它们被写在多行上，并带有缩进。

```
if () {
} 
```

在`if`语句中，只有括号中的语句为`true`时，条件才会运行。如果是`false`，整个代码块将被忽略。

```
if (condition) {
  // execute code
} 
```

首先，它可以用来测试变量的存在。

```
var age = 21;

if (age) {
  console.log("Your age is " + age + ".");
} 
```

在上面的例子中，存在一个`age`变量，因此代码将打印到控制台。`if (age)`是`if (age === true)`的简写，因为默认情况下`if`语句的计算结果为`true`。

我们可以使用前面学过的比较运算符来增强这个条件的功能。如果你曾经看过一个酒类产品的网站，他们通常有一个年龄限制，你必须输入才能查看该网站。在美国，年龄是 21 岁。他们可能会使用`if`语句来测试用户的年龄是否大于或等于 21 岁。

```
var age = 21;

if (age >= 21) {
  console.log("Congratulations, you can view this site.");
} 
```

#### 其他

如果想为不符合条件的用户显示不同的消息，可以使用 **else 语句**。如果第一个条件不成立，第一个代码块将被忽略，而`else`代码块将被执行。

```
if (condition) {
  // execute code
} else {
  // execute other code
} 
```

这里有一个年轻用户的例子。由于用户不满足条件，第二个代码块将运行。

```
var age = 18;

if (age >= 21) {
  console.log("Congratulations, you can view this site.");
} else {
  console.log("You must be 21 to view this site.");
} 
```

#### 否则如果

如果有两个以上的选项，可以使用一个 **else if 语句**根据多个条件执行代码。

```
var country = "Spain";

if (country === "England") {
  console.log("Hello");
} else if (country === "France") {
  console.log("Bonjour");
} else if (country === "Spain") {
  console.log("Buenos días");
} else {
  console.log("Please enter your country.");
} 
```

在上面的例子中，输出将是`"Buenos Días"`，因为`country`的值被设置为`"Spain"`。

### 转换

还有另一种类型的条件语句，称为 **switch 语句**。它非常类似于一个`if`语句，执行相同的功能，但是编写方式不同。

当评估许多可能的结果时，`switch`语句是有用的，并且通常比使用许多`else if`语句更可取。

一个 switch 语句被写成`switch () {}`。

```
switch (expression) {
  case x:
    // execute code
    break;
  case y:
    // execute code
    break;
  default:
    // execute code
} 
```

在语句中，您会看到`case`、`break`和`default`关键字。为了更好地理解，我们将使用与`else if`相同的带有 switch 语句的例子。

```
var country = "Spain";

switch (country) {
  case "England":
    console.log("Hello");
    break;
  case "France":
    console.log("Bonjour");
    break;
  case "Spain":
    console.log("Buenos días");
    break;
  default:
    console.log("Please enter your country.");
} 
```

在这个例子中，我们正在计算某个字符串的变量，一个代码块将基于每个`case`执行。一旦找到匹配，关键字`break`将阻止进一步的代码运行。如果没有找到匹配，将执行`default`代码块，类似于`else`语句。

## 功能

JavaScript **函数**是一个包含的代码块。它可以执行任务或计算，并接受参数。使用函数的主要原因之一是编写可重用的代码，这些代码可以在每次运行时产生不同的结果(取决于传递给它的值)。

### 申报

在使用函数之前，必须声明(或定义)它。函数是用关键字`function`声明的，并且遵循与变量相同的命名规则。

一个函数被写成`function() {}`。下面是一句简单的“你好，世界！”在函数中。

```
function greeting() {
  return "Hello, World!";
} 
```

### 祈祷

为了调用(使用)该函数，请键入名称，后跟括号。

```
greeting(); // returns "Hello, World!" 
```

### 参数和自变量

函数也可以接受参数并执行计算。**参数**是传递给函数的一个值。**参数**是函数接受并执行的局部变量。

> 局部变量是只在特定代码块中起作用的变量。

在本例中，我们创建了一个名为`addTwoNumbers`的函数，它将两个数字相加(说真的，好的命名很重要)。我们将发送数字`7`和`3`作为参数，它们将作为参数`x`和`y`被函数接受。

```
function addTwoNumbers(x, y) {
  return x + y;
}

addTwoNumbers(7, 3); // returns 10 
```

由于`7` + `3` = `10`，函数将返回`10`。下面，您将看到函数是如何重用的，因为我们将把不同的参数传递给完全相同的函数，以产生不同的输出。

```
function addTwoNumbers(x, y) {
  return x + y;
}

addTwoNumbers(100, 5); // returns 105 
```

在 JavaScript 中还有其他几种声明函数的方法。你可以在这篇文章中读到更多的内容:[快速提示:函数表达式 vs 函数声明](https://www.sitepoint.com/function-expressions-vs-declarations/)。

## 结论

在本文中，我们学习了 JavaScript 的三个非常重要的基本概念:操作符、条件语句和函数。运算符是对数据执行运算的符号，我们学习了赋值、算术、比较和逻辑运算符。条件语句是基于真或假结果执行的代码块，函数是执行任务的可重用代码块。

有了这些知识，您就可以继续学习 JavaScript 的中级概念了。如果您对所展示的材料有任何问题或意见，我很乐意在下面的评论中听到(尤其是如果您刚刚接触 JavaScript 的话)。

*这篇文章由[詹姆斯·科尔斯](https://www.sitepoint.com/author/jkolce)和[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章