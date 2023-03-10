# Java 语言基础文章

> 原文：<https://www.sitepoint.com/java-language-basics-4/>

##### 经营者

正如我上面所说的，任何有用的程序都可能必须执行某种计算，或者以某种方式操纵信息。到目前为止，我们已经看到了如何定义信息片段并将它们存储在变量中，为将要完成的工作做准备。在这一节中，我将介绍一些 Java 的**操作符**。使用运算符，您可以获取简单的值(如数字和文本字符串)并对它们执行运算(如加、减或连接)。

让我给你看几个简单的例子，这样你就知道我的意思了:

```
int a, b, c; // Three integer variables    

a = 10;      // Assigns value 10 to a    

b = 10 + 1;  // Assigns value 11 to b (+ is addition)    

c = b - a;   // Assigns value 1 to c (- is subtraction)    

a = b * 3;   // Assigns value 33 to a (* is multiplication)    

c = a / b;   // Assigns value 3 to c (/ is division)
```

顺便提一下，您可能已经注意到，我在上述代码块的第一行使用了另一个快捷方式，在一条语句中创建了三个整数变量(`a`、`b`和`c`)。这一行可以扩展为以下内容，其工作方式完全相同:

```
int a;

int b;

int c;
```

回到这些操作符，注意我是如何使用符号+、-、*和/来分别执行加法、减法、乘法和除法的。这些符号是**算术运算符**。还要注意，运算符可以用于文字值(`10 + 1`)、变量(`b â€“ a`)或两者(`b * 3`)。

另一种不常用的算术运算符是模数运算符:

```
c = b % 3;   // Assigns value 2 to c (% is modulus op.)
```

模数运算符给出整数除法的余数。在这个例子中，存储在`b` (11)中的值除以 3 是 9，余数是 2——这个余数是由`%`运算符计算的值。

如果愿意，可以在一条语句中执行多个操作。请注意，算术运算遵守与科学计算器相同的优先规则:首先是乘法和除法，然后是加法和减法。当然，如果我们愿意，我们可以使用括号来改变它:

```
a = 1 + 2 * 3;   // a = 7    

b = (1 + 2) * 3; // b = 9
```

回想一下，变量不一定要包含数字。它们还可以保存文本字符串…也可以对字符串执行操作:

```
String name = "Kevin";

System.out.println("Hi " + name + ", how are you today?");
```

上面的代码显示消息`Hi Kevin, how are you today?`注意，用于将数字相加的`+`操作符也可以用于将文本字符串连接在一起(换句话说，连接字符串)。在这个上下文中，`+`被称为**字符串连接操作符**。还要注意，我们使用了表达式`"Hi " + name + ", how are you today?"`的结果作为由`println`命令输出的值。没有说我们必须将算术计算或其他操作的结果存储在变量中；如上例所示，如果我们只需要一个地方的值，我们可以简单地在需要的地方输入表达式。

另一类操作符叫做**比较操作符**。顾名思义，这些运算符用于比较不同的值。算术运算符产生的结果是数字(通常是`int`或`double`，这取决于运算中使用的数据类型)，字符串连接运算符产生`String`，比较运算符产生布尔值(即真或假)。如前所述，一些例子可以有效地说明这一点:

```
// From above: a = 7, b = 9, and c = 2    

boolean d, e, f; // Three boolean variables    

d = (a == 7);    // d = true (== tests equality)    

e = (a == b);    // e = false (a doesn't equal b)    

f = (a != b);    // f = true (!= tests inequality)    

d = (a > 0);     // d = true (a is greater than 0)    

e = (b < 6);     // e = false (b is not less than 6)    

f = (c >= 2);    // f = true (c is greater than or equal to 2)    

d = (c <= 1);    // d = false (c is not less than or equal to 1)
```

如下图所示，比较运算符有`==`、`!=`、`>`、`<`、`>=`和`<=`。大多数操作符只有在应用于数字(或包含数字的变量)时才有意义。然而，人们可能期望`==`(测试相等性)和`!=`(测试不相等性)会对`String`值起作用，但事实上它们不会。如果您尝试使用这些操作符来比较字符串，您不会得到错误，但是您会很快发现`==`将总是变成 false(不管字符串是否相同)，而`!=`将总是给出 true 结果。然而，这样做的原因将不得不等到我们的下一篇文章，在那里我将解释对象和类的所有优点。

您可能想知道这些比较运算符的实际用途。事实上，当要让你的程序在不同的条件下做不同的事情时，它们是必不可少的。然而，要完成这样的壮举，你还需要一个工具:**控制结构**。

**Go to page:** [1](https://sitepoint.com/java-language-basics) | [2](https://sitepoint.com/java-language-basics-2/) | [3](https://sitepoint.com/java-language-basics-3/) | [4](https://sitepoint.com/java-language-basics-4/) | [5](https://sitepoint.com/java-language-basics-5/) | [6](https://sitepoint.com/java-language-basics-6/) | [7](https://sitepoint.com/java-language-basics-7/)

## 分享这篇文章