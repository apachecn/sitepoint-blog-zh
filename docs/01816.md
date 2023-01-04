# JavaScript 变量和数据类型初学者指南

> 原文：<https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/>

*一本 JavaScript 变量和数据类型的初学者指南由[斯科特·莫利纳里](https://www.sitepoint.com/author/smolinari)和[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)和[克里斯·佩里](https://www.sitepoint.com/author/cperry/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![A set of dominoes with symbols representing JavaScript variables and datatypes](img/9e08bb15318bc2f76b911b1eb8e5422f.png)

所以你决定学习 JavaScript，一种网络编程语言。如果这看起来是一段令人生畏的旅程，你不知道从哪里开始，这里有一个小秘密:学习如何编程不需要任何特殊技能，每个人都从零开始。一步一步来，你会成功的。

## 这是给我的指南吗？

如果其中任何一条适用于你，你将从阅读本指南中受益:

*   你以前从未使用过编程语言。
*   你以前从未使用过 JavaScript。
*   您以前尝试过学习 JavaScript，但是发现缺少资源或者很难跟上。
*   你知道一点 JavaScript，但想温习一下基础知识。

在本文中，我们将关注基础:语法、变量、注释和数据类型。美妙之处在于，您可以将这里学到的关于 JavaScript 的概念应用到将来学习另一种编程语言中。

> **免责声明:**本指南面向 JavaScript 和编程的完全初学者。因此，许多概念将以简化的方式呈现，并且将使用严格的 ES5 语法。

准备好了吗？我们开始吧！

## JavaScript 是什么？

JavaScript 是一种编程语言，用于使网站具有动态性和交互性。这是一种**客户端**编程语言，这意味着代码在用户的网络浏览器中执行。随着 [Node.js](https://nodejs.org/en/) 等技术的出现，它还可以作为**服务器端**语言，具有极强的通用性。JavaScript 主要用于**前端 web 开发**，与 HTML 和 CSS 紧密配合。

> **注意:** Java 不是 JavaScript。这是一种不同的语言，有着令人困惑的相似名字。

### 要求

您已经具备了开始编写和使用 JavaScript 的先决条件。你只需要一个**网络浏览器**来查看代码，一个**文本编辑器**来编写代码。你目前使用的浏览器很完美(Chrome、Firefox、Safari、Edge 等)。你的电脑预装了记事本(Windows)或文本编辑(Mac)，但我会推荐安装 [Atom](https://atom.io/) 或[括号](http://brackets.io/)，这是专门为编码设计的免费程序。

CodePen 是一个允许你写代码和制作现场演示的网站，这将是开始跟随和练习的最简单的方法。

### 基本术语

程序员写程序，就像作者写书一样。

一个**程序**只是一组计算机可以读取并用来执行任务的指令。每条单独的指令都是一行代码，称为**语句**，类似于书中的一句话。英语句子以句号结尾，而 JavaScript 语句通常以分号结尾。**语法**是指定义语言结构的符号和规则，类似于语法和标点符号，结束一条 JavaScript 语句的分号就是语法的一部分。

## 评论

一个**注释**是用代码写的人类可读的注释。

注释是用简单的英语写的，目的是解释代码。虽然注释在技术上并不执行程序中的任何功能，但是养成正确记录文档的习惯以帮助你或未来的合作者理解你的代码的意图是至关重要的。

JavaScript 中有两种类型的注释:

*   **单行注释**，写成两个正斜杠`//`后跟注释。

```
// This is a single line comment. 
```

*   **多行注释**，夹在`/*`和`*/`之间，可以跨多行。

```
/* This is a comment.
It's a multi-line comment.
Also a haiku. */ 
```

## 变量

**变量**是存储数据值的容器。

你知道一个[变量](https://www.sitepoint.com/how-to-declare-variables-javascript/)是可以改变的东西。在基础代数中，它是一个代表数字的字母。`x`是一个常见的变量名，但是也可以用`y`、`z`或其他名字来表示。

最初`x`没有值或意义，但是您可以对它应用一个值。

```
x = 5 
```

现在，`x`代表`5`。你可以把`x`想象成一个存储`5`的容器，T3 是一个数字。

在 JavaScript 中，变量的工作方式是一样的，除了它们可以包含不仅仅是数字的值——它们可以包含所有类型的数据值，我们将在本文的结尾了解这一点。

使用关键字`var`创建和声明变量。我们可以使用上面的代数例子来创建一个 JavaScript 语句。

```
var x = 5; // the variable x contains the numeric value of 5. 
```

基于我们目前所学的内容，您可以看到我们有一个 JavaScript 语句，它声明了一个变量(`x`)，用一个等号(`=`)指定了数字数据类型(`5`)，并用一个注释(`//`)用简单的英语解释了它。该语句以分号(`;`)结束。

变量只需要在第一次使用时用`var`声明，顾名思义，变量可以改变。

```
var x = 5; // x was worth 5
x = 6; // now it's worth 6 
```

一个变量一次只能包含一个值，由于程序是从上到下执行的，`x`的值现在是`6`。

下面是一个具有不同数据类型的变量的示例。

```
var greeting = "Oh hi, Mark!"; 
```

现在`greeting`变量包含了字符串`Oh hi, Mark!`。

关于变量需要知道的几件重要事情:

*   变量名可以包含字母、数字、美元符号(`$`)和下划线(`_`)，但不能以数字开头。
*   变量不能是[保留关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Reserved_keywords_as_of_ECMAScript_2015)列表中的任何单词。
*   变量区分大小写。
*   命名约定是 **camelCase** ，其中一个变量总是以小写字母开头，但是后面的每个单词都以大写字母开头。

> 提示:虽然变量可以有任何名称，但是选择描述性的且简洁的名称很重要。

## 数据类型

现在我们知道了变量，我们可以了解变量可以保存的数据类型。

**数据类型**是数据的分类。编程语言需要不同的数据类型来正确地与值交互。你可以用一个数字做数学，但不能用一个句子，所以计算机对它们进行了不同的分类。有六种**原语**(基本)数据类型:字符串、数字、[布尔](https://www.sitepoint.com/boolean-data-type/)、空、未定义和符号(ES6 中新增)。原语只能保存一个值。不是这些原语之一的任何东西都是一个**对象**。对象可以包含多个值。

JavaScript 被称为**弱类型语言**(或*松散类型*)，这意味着该语言将根据您使用的语法自动确定数据类型。

## 测试

`alert()`和`console.log()`是在 JavaScript 中打印值的两种简单方法。

```
var x = 5;

alert(x);
console.log(x); 
```

[这里的](https://codepen.io/taniarascia/pen/yVgYvx)是这些方法的演示。一个`alert`是一个弹出窗口，`console.log`将打印到检查器，你可以在浏览器中右击并选择**检查**来找到它。我不会在整篇文章中再次提到这些，所以你可以选择最适合你的来练习。我建议避开`alert`，因为它很烦人。

你总是可以使用`typeof`找到变量的类型。

```
var answer = 42;

typeof answer // returns number 
```

### 用线串

一个**字符串**是一系列字符。

任何包含文本的数据都将由字符串表示。名称 *string* 可能源自早期的程序员，他们被提醒字符串上的珠子。

*   一个字符串可以用**双引号** ( `" "`)括起来:

    ```
    "Pull the string, and it will follow wherever you wish."; // double   quoted string 
    ```

*   或者**单引号** ( `' '`):

    ```
    'Push it, and it will go nowhere at all.'; // single quoted string 
    ```

> **注意:**单引号通常被称为撇号，不要与反撇号混淆，反撇号位于键盘的最左侧。

字符串的两端必须匹配，但除此之外，两者没有区别——它们只是编写字符串的不同方式。

但是如果我想在单引号字符串中写 *I'm* ，或者在双引号字符串中引用某人，该怎么办呢？那不会把线弄断吗？

会的，有两个选择来对抗它。您可以安全地使用相反类型的引号:

```
"Damn it, man, I'm a doctor, not a torpedo technician!"; // using single quotes within a double quoted string
'"Do. Or do not. There is no try." - Yoda'; // using double quotes in a single quoted string 
```

在整个项目中，为字符串选择一种风格以保持一致性是非常重要的。你可以用一个反斜杠(`\`)来对*字符串进行转义。*

```
'Damn it, man, I\'m a doctor, not a torpedo technician!'; 
"\"Do. Or do not. There is no try.\" - Yoda"; 
```

我们可以将字符串应用于变量。让我们用我的`greeting`例子。

```
var greeting = "Oh hi, Mark!"; 
```

字符串也可以通过称为**串联**的过程连接在一起。变量和字符串可以用加号(`+`)连接在一起。

```
var greeting = "Oh hi, " + "Mark" + "!"; // returns Oh hi, Mark! 
```

注意，空格也是字符串中的一个字符。在下面的例子中，我们将看到串联是如何有用的。

```
var message = "Oh hi, ";
var firstName = "Mark";
var bam = "!";

var greeting = message + firstName + bam; // returns Oh hi, Mark! 
```

现在所有的字符串都用可以改变的变量来表示。如果你曾经登录一个应用程序(比如你的电子邮件)并收到你的名字，你可以看到你的名字只是他们系统中变量的一个字符串。

### 数字

一个**号**是一个数值。

与字符串不同，数字没有任何特殊的相关语法。如果你把一个数字放在引号(`"5"`)中，它不会被认为是一个数字，而是一个字符串中的一个字符。数字可以是整数，也可以是小数(称为浮点数)，可以有正值或负值。

```
var x = 5; // whole integer
var y = 1.2; // float
var z = -76; // negative whole integer 
```

一个号码最多可以有 15 个数字。

```
var largeNumber = 999999999999999; // a valid number 
```

你可以用数字做常规数学运算——加法(`+`)、减法(`-`)、除法(`/`)和乘法(`+`)。

```
var sum = 2 + 5; // returns 7
var difference = 6 - 4; // returns 2 
```

你可以用变量做数学。

```
var elves = 3;
var dwarves = 7;
var men = 9;
var sauron = 1;

var ringsOfPower = elves + dwarves + men + sauron; // returns 20 
```

还有另外两种特殊的号码类型。

#### 圆盘烤饼

`NaN`表示不是一个数字，尽管它在技术上是一个数字类型。`NaN`是试图用其他数据类型做不可能的数学运算的结果。

```
var nope = 1 / "One"; // returns NaN 
```

例如，试图将一个数字除以一个字符串会导致`NaN`。

#### 无穷

`Infinity`在技术上也是一个数，要么是除以`0`的乘积，要么是计算一个太大的数。

```
var beyond = 1 / 0; // returns Infinity 
```

### 布尔代数学体系的

一个布尔型值是一个或者真或者假的值。

布尔值在编程中非常常用，它可以在是与否、开与关、真与假之间切换。布尔可以表示可能改变的事物的当前状态。

例如，我们用一个布尔值来表示一个复选框是否被选中。

```
var isChecked = true; 
```

通常，布尔用于检查两个事物是否相等，或者数学等式的结果是真还是假。

```
/* Check if 7 is greater than 8 */
7 > 8; // returns false

/* Check if a variable is equal to a string */
var color = "Blue";

color === "Blue"; // returns true 
```

> **注意:**一个等号(`=`)将一个值应用于另一个值。双(`==`)或三等号(`===`)检查两个事物是否相等。

### 不明确的

一个未定义的变量没有值。

使用关键字`var`，我们将*声明为*变量，但是在给它赋值之前，它是未定义的。

```
var thing; // returns undefined 
```

如果没有用`var`声明变量，它仍然是未定义的。

```
typeof anotherThing; // returns undefined 
```

### 空

**Null** 是不代表任何东西的值。

Null 是指*有意*缺少任何值。它表示不存在的东西，并且不是任何其他数据类型。

```
var empty = null; 
```

null 和 undefined 之间的区别相当微妙，但主要区别在于 null 是一个有意为空的值。

### 标志

**符号**是唯一且不可变的数据类型。

符号是一种新的原始数据类型，出现在最新的 JavaScript 版本中( [ES6](http://www.ecma-international.org/ecma-262/6.0/) )。它的主要特点是每个符号都是一个完全唯一的标记，不像其他数据类型可以被覆盖。

因为它是一种高级的、很少使用的数据类型，所以我不会深入讨论，但是知道它的存在是很有用的。

```
var sym = Symbol(); 
```

### 目标

一个**对象**是名称/值对的集合。

任何不是简单的字符串、数字、布尔、空、未定义或符号的值都是一个[对象](https://www.sitepoint.com/javascript-object-creation-patterns-best-practises/)。

你可以用一对花括号(`{}`)创建一个对象。

```
var batman = {}; 
```

对象由**属性**和**方法**组成。属性是对象*的*，方法是对象*做的*。回到类比这本书，属性就像名词和形容词，方法就像动词。

| **属性** | **方法** |
| --- | --- |
| 蝙蝠侠，名字 | 蝙蝠侠.战斗() |
| 蝙蝠侠.地点 | 蝙蝠侠. jump() |

我们可以使用名称/值对(有时称为*键/值*对)对`batman`对象应用一些属性。它们将以逗号分隔，并写成`propertyName: propertyValue`。

```
var batman {
  firstName: "Bruce", // string
  lastName: "Wayne", 
  location: "Gotham", 
  introduced: 1939, // number
  billionaire: true, // Boolean
  weakness: null // null
}; 
```

> **注意:**对象属性列表中的最后一项不以逗号结尾。

如您所见，我们可以将任何原始数据类型作为值应用到对象中。我们可以用一个点(`.`)来检索一个单独的值。

```
batman.firstName; // returns Bruce, a string 
```

我们也可以用括号符号来检索值。

```
batman["firstName"]; // returns Bruce 
```

JavaScript 属性名必须是有效的 JavaScript 字符串或数字文字。如果名称以数字开头或包含空格，则必须使用括号符号进行访问。[阅读:MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects#Objects_and_properties)

```
batman.secret identity; // invalid
batman["Secret Identity"]; // valid 
```

一种方法执行一个动作。这里有一个简单的例子。

```
var batman {
  firstName: "Bruce",
  lastName: "Wayne", 
  secretIdentity: function() { // method
    return this.firstName + " " + this.lastName;
  }
}; 
```

我有一个`function`，而不是简单的数据类型作为值。`function`连接`this`对象的`firstName`和`lastName`，并返回结果。在本例中，`this`与`batman`相同，因为它在对象(`{}`)内部。

```
batman.secretIdentity(); // returns Bruce Wayne, a concatenation of two properties 
```

圆括号表示方法。(`()`)。

### 数组

一个**数组**将一个列表存储到一个变量中。

因为一个[数组](https://www.sitepoint.com/quick-tip-create-manipulate-arrays-in-javascript/)包含不止一个值，所以它是一种对象类型。

你可以用一对直括号(`[]`)创建一个数组。

```
var ninjaTurtles = []; 
```

数组不包含名称/值对。

```
var ninjaTurtles = [
  "Leonardo",
  "Michelangelo",
  "Raphael",
  "Donatello"
]; 
```

你可以通过引用数组的`index`得到一个单独的值。在编程中，计数从零开始，所以列表中的第一项总是`[0]`。

```
var leader = ninjaTurtles[0]; // assigns Leonardo to the leader variable 
```

您可以使用`length`属性查看数组中有多少项。

```
ninjaTurtles.length; // returns 4 
```

## 概述

我们在这篇文章中讨论了很多。现在，您应该对常见的编程概念、术语、语法和基础有了更好的理解。至此，您已经学习了所有关于 JavaScript 变量和数据类型的知识，现在您可以继续操作这些数据和构建程序了！* 

## *分享这篇文章*