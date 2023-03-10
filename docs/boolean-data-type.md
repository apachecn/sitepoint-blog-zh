# 什么是布尔数据类型，有哪些用途？

> 原文：<https://www.sitepoint.com/boolean-data-type/>

了解什么是布尔数据类型，以及如何在编程中使用它。

本文详细介绍了布尔数据类型的定义，并解释了它在 Python 等编程语言中的用法。还有一些布尔运算符的例子，在理解布尔逻辑和条件语句时会很有用。

## 什么是布尔数据类型？

在计算机程序中，有三种类型的数据:文本、数字和布尔。布尔数据类型是只能为真或假的值。

真布尔值可能表示对象是有效的(例如，电子邮件地址键入正确)。假布尔值表示对象无效，并且没有正确完成(例如，您忘记填写必填字段)。

### 布尔值是真值和假值

布尔值有两种可能的状态:真和假。在二进制中，这些用 1 和 0 来表示。

布尔代数是一种处理逻辑值运算的数学，包括二进制变量。它是程序中决策的基础，所以理解布尔值如何工作是很重要的。

### 布尔值在计算机编程中是如何使用的？

布尔值用于创建条件，并控制程序在某些事情发生时的行为(例如，如果条件为真，则执行某些操作)。它们只能有两个可能的值:0 或 1。你不能加或减它们。布尔*变量*是计算机中一种特殊类型的内存，只能存储两个值:真或假。

### 文本、数字和布尔有什么区别？

当在编程中使用布尔时，理解布尔变量和其他类型的数据之间的区别是很重要的。您需要知道它们是如何存储的，以及可以对它们执行哪些操作。

#### 布尔运算

布尔值通常使用一个字节的内存来存储，而文本变量使用不止一个字节(例如，ASCII 使用两个字节，Unicode 使用四个字节)。

#### 文本

一些编程语言用字符数组表示文本(例如 ASCII 或 Unicode)。文本数据类型的大小有限(通常为 256 个字符)，并且不对自身执行操作。

#### 数字

数字可以是负数、正数或零，这取决于它们的属性。它们存储为一个位数组，该数组确定它们是否为正(即:00000000)。这同样适用于负数(-1，-2 等)。

这些类型的值可用于数学运算，如加法和减法。布尔值不能相加或相减，因为它们只有两种状态:真和假。

## 真值和假值

![Clouds of various data types depicting whether they evaluate to truthy or falsy when evaluated with a Boolean operator](img/778276aad593ad92d8817870d1af8e76.png)

编程语言中有一些特殊的值，它们既可以被视为文本，也可以被视为布尔值。这些值被称为“真”或“假”值，取决于它们分别评估为真还是假。

例如，0 是一个假值，因为它的计算结果为假，但“0”是一个定义字符串的真值。

阅读我们的 JavaScript 中的 [truthy 和 falsy 指南，了解各种变量状态和数据类型如何评估为真或假。](https://www.sitepoint.com/javascript-truthy-falsy/)

## 布尔值运算符

当在程序中使用布尔时，理解布尔运算符是很重要的。这些用于控制程序行为的条件和条件语句中。运算符示例包括 AND ( `&&`)、OR ( `||`)和 NOT ( `~`)。

### 布尔运算符示例

以下是编程中布尔值运算符的示例:

*   `>=`–如果一个数大于或等于另一个数，则为真。
*   `<=`–如果一个数小于或等于另一个数，则为真。
*   `==`–如果两个值相等，则为真。
*   `!=`–如果两个值不相等，则为真。
*   `&&`–如果两个值都为真，则为真。
*   `||`–如果任一值为真，则为真。
*   `!`–如果值为假，则为真。
*   `~`–反转变量中的所有位(例如:0000000000000000 变为 11111111111111)。这在必要时很有用，因为它允许您在不影响值或其他类型的数据的情况下更改布尔值。它还可以用于恢复前一条语句所做的更改(例如:if(！idspnonenote)。k)那么 k = 1)。

布尔运算符用于在程序中做出决策，并指示程序应该如何运行。比如 p 为真，q 也为真，那就做点什么。

### 布尔用例示例

布尔值用于条件测试，如下所述。

*   检查电子邮件地址是否有效。
*   检查密码长度是否至少为 6 个字符。
*   检查两个字段是否填写正确。

### 实践中布尔数据类型的一个例子是什么？

以下是 C++代码中的一个布尔值示例(注意，没有表示变量的类型，可以使用任何变量类型进行存储):

如果两个数相加为零，该函数返回 true，否则返回 false。

`bool NumberCheck(int x, int y) { return x+y == 0; };`

布尔数据类型也可以用作关系运算符(例如:“大于”)和等式运算符(例如:“等于”或“不等于”)中的操作数，以执行比较目录内容和从网站检查文件大小等任务，如下面的 JavaScript 所示:

```
if (document.getElementById("files").value) {
    if (parseInt(document.getElementById("files").value) > 1048576) {
      alert("You have selected a file larger than 1MB.");
    } else {
      alert("You have selected a file smaller or equal to 1MB.");
    }
  } else {
    alert("Please select at least one file.");
  }
```

## 布尔数据类型的历史和起源

布尔以 19 世纪数学家乔治·布尔的名字命名。他在 1854 年首次发展了布尔代数。

布尔数据类型是在 19 世纪早期发明的。乔治·布尔创建了一个逻辑系统，可以用来描述计算机中的真值(即:1)和假值(即:0)。这发展成了我们现在所说的布尔代数，它被用作大多数计算机编程语言的基础。

1854 年，乔治·布尔写了《对逻辑和概率的数学理论所基于的思维规律的研究》，这为许多计算机语言奠定了数学基础。

1951 年，贝尔实验室的一名工程师首次将布尔运算用于商业用途，作为美国空军使用的大型计算机安/FSQ-7 的一部分。

## 常见问题

**What are Boolean variables in programming?**

布尔数据类型用于存储值 true 和 false。这种数据类型可用于存储允许存储开或关两种状态之一的信息。

**What is a nullable data type?**

可空类型用于表示可以设置为空(无值)状态的值。

**What is a null value?**

null 是用于表示对象(如变量、记录等)的数据类型。)当前不存在的、未完成的/未使用的或被丢弃的，以便释放资源。当表达式的计算结果为空并且需要给它赋值时，也可以使用它。它返回 false。

**What is a non-zero value?**

非零值是非零的值。它返回 true。

**Can a Boolean data type have the value of null?**

不，它不能，但是你可以使用一个未定义的布尔值来表示一个空值。如果对照运算符进行检查，则返回 false。

**What is a Boolean data type in a database?**

布尔数据类型可用于在数据库中存储值 true 和 false。

布尔在数据库中最常用来表示是/否、开/关或其他相关状态。例如，如果帐户已关闭，则其状态栏可能包含 false。如果它当前处于打开状态，那么 true 将存储在帐户状态布尔值中。另一个常见的用法是标志，它允许你存储发生的事情的信息(例如:“电子邮件已发送”，“文件已下载”)。

布尔也可以用于显示文件是否存在，或者动作是否已经执行(例如:表单已提交)。

**What are some programming languages that support Boolean data types?**

大多数编程语言中都有布尔值。如果你使用 JavaScript、Java、PHP、Python、C、C++或 Swift，那么你应该可以访问布尔数据类型。如果你的编程语言允许 null，那么 true 和 false 也是允许的。

**What are some applications that manipulate Boolean data types?**

应用软件可以使用布尔值来管理标志、是/否选项、开/关状态和其他相关信息。它们还可以用于跟踪资源或执行各种任务(例如:删除文件)。

## 摘要

在这篇博文中，您了解了什么是布尔值，以及它们与其他类型的数据有何不同。您还了解了理解布尔运算的重要性，以及如何在编程中使用布尔运算符。

## 分享这篇文章