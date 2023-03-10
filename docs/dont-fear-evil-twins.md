# 不要害怕邪恶的双胞胎。=运算符)

> 原文：<https://www.sitepoint.com/dont-fear-evil-twins/>

JavaScript 开发者道格拉斯·克洛克福特称 JavaScript 的`==`和`!=`操作符为应当避免的邪恶双胞胎[。然而，一旦你理解了它们，这些操作符并不是那么糟糕，实际上可能是有用的。这篇文章考察了`==`和`!=`，解释了它们是如何工作的，并帮助你更好地了解它们。](http://oreilly.com/javascript/excerpts/javascript-good-parts/bad-parts.html)

## 有问题的`==`和`!=`操作符

JavaScript 语言包含两组等式操作符:`===`和`!==`，以及
`==`和`!=`。理解为什么有两组等式操作符，并弄清楚在什么情况下使用哪一组操作符，一直是许多困惑的来源。

`===`和`!==`操作符不难理解。当两个操作数类型相同且值相同时，`===`返回`true`，而`!==`返回`false`。但是，当值或类型不同时，`===`返回`false`，而`!==`返回`true`。

当两个操作数具有相同的类型时，`==`和`!=`操作符的行为相同。然而，当类型不同时，JavaScript *将一个操作数强制*为另一种类型，以使操作数在比较前兼容。结果往往令人困惑，如下所示:

```
"this_is_true" == false // false
"this_is_true" == true  // false
```

因为只有两个可能的布尔值，您可能会认为其中一个表达式应该计算为`true`。但是，他们都评估为`false`。当您假设传递关系(如果 a 等于 b，b 等于 c，那么 a 等于 c)适用时，会出现额外的混淆:

```
'' == 0   // true
0 == '0' // true
'' == '0' // false
```

这个例子揭示了`==`缺乏传递性。如果空字符串等于数字 0，并且数字 0 等于由字符 0 组成的字符串，则空字符串应该等于由 0 组成的字符串。但事实并非如此。

当通过`==`或`!=`比较操作数时遇到不兼容的类型时，JavaScript 会将一种类型强制转换为另一种类型，以使它们具有可比性。相比之下，当使用`===`和`!==`时，它从不执行类型强制(这会带来更好的性能)。由于类型不同，`===`在第二个例子中总是返回`false`。

理解 JavaScript 如何将一个操作数强制转换为不同类型的规则，以便在应用`==`和`!=`之前两个操作数都是类型兼容的，可以帮助您确定什么时候使用`==`和`!=`更合适，并有信心使用这些操作符。在下一节中，我们将探索与`==`和`!=`操作符一起使用的强制规则。

## `==`和`!=`是怎么工作的？

了解`==`和`!=`如何工作的最好方法是学习 ECMAScript 语言规范。本节重点介绍 [ECMAScript 262](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 。规范的第 11.9 节介绍了等式运算符。

`==`和`!=`操作符出现在语法产生式`EqualityExpression`和`EqualityExpressionNoIn`中。(与第一次生产不同，第二次生产避免了`in`操作符。)让我们检查一下`EqualityExpression`的生产，如下所示。

```
EqualityExpression :
   RelationalExpression
   EqualityExpression == RelationalExpression
   EqualityExpression != RelationalExpression
   EqualityExpression === RelationalExpression
   EqualityExpression !== RelationalExpression
```

根据这个结果，等式表达式或者是关系表达式，或者是通过`==`等于关系表达式的等式表达式，或者是通过`!=`不等于关系表达式的等式表达式，等等。(我忽略`===`和`!==`，它们与本文无关。)

第 11.9.1 节介绍了以下关于`==`如何工作的信息:

> 生产等式表达式:*等式表达式* == *关系表达式*计算如下:
> 
> 1.  设 *lref* 为评估 *EqualityExpression* 的结果。
> 2.  让 *lval* 成为数值(*LRF*。
> 3.  设 *rref* 为评估*关系表达式*的结果。
> 4.  让 *rval* 得到数值(*参考*。
> 5.  返回执行抽象等式比较 *rval* == *lval* 的结果。(参见 11.9.3。)

第 11.9.2 节介绍了关于`!=`如何工作的类似信息:

> 生产等式表达式:*等式表达式*！= *关系表达式*计算如下:
> 
> 1.  设 *lref* 为评估 *EqualityExpression* 的结果。
> 2.  让 *lval* 成为数值(*LRF*。
> 3.  设 *rref* 为评估*关系表达式*的结果。
> 4.  让 *rval* 得到数值(*参考*。
> 5.  设 *r* 为执行抽象等式比较 *rval* 的结果！= *lval* 。(参见 11.9.3。)
> 6.  如果 *r* 为**真**，则返回**假**。否则，返回**真**。

`lref`和`rref`是对`==`和`!=`操作符左右两侧的引用。每个引用都被传递给内部函数`GetValue()`以返回相应的值。

抽象等式比较算法规定了`==`和`!=`如何工作的核心，在第 11.9.3 节中有介绍:

> 比较`*x* == *y*`，其中 *`x`* 和 *`y`* 为数值，产生
> **真**或**假**。这种比较按如下方式进行:
> 
> 1.  如果类型( *`x`* )与类型( *`y`* )相同，那么
>     1.  如果类型( *`x`* )未定义，则返回**真**。
>     2.  如果类型( *`x`* )为空，则返回**真**。
>     3.  如果类型( *`x`* )是数字，那么
>         1.  如果 *`x`* 是**南**，返回**假**。
>         2.  如果 *`y`* 是**南**，返回**假**。
>         3.  如果 *x* 与 *y* 的数值相同，则返回 **true** 。
>         4.  如果 *x* 为 **+0** 且 *y* 为 **-0** ，则返回**真**。
>         5.  如果 *x* 为 **-0** 且 *y* 为 **+0** ，则返回**真**。
>         6.  返回**假**。
>     4.  如果 Type( *`x`* )是 String，那么如果 *`x`* 和 *`y`* 是完全相同的字符序列(长度相同，对应位置的字符相同)，则返回 **true** 。否则，返回**假**。
>     5.  如果 Type( *`x`* )为布尔值，则返回**真**如果 *`x`* 和 *`y`* 都为**真**或都为**假**。否则，返回**假**。
>     6.  返回**真**如果 *`x`* 和 *`y`* 指的是同一个对象。否则，返回**假**。
> 2.  如果 *`x`* 为**空**且 *`y`* 为**未定义**，则返回**真**。
> 3.  如果 *`x`* 是**未定义**而 *`y`* 是 **null** ，返回 true。
> 4.  如果 Type( *`x`* )是数字，Type( *`y`* )是字符串，则返回比较结果*`x`*= = to Number(*`y`*)。
> 5.  如果 Type( *`x`* )是 String，Type( *`y`* )是 Number，返回比较的结果 to Number(*`x`*)= =*`y`*。
> 6.  如果类型( *`x`)是布尔型，则返回比较的结果为数字(*`x`*)=*=`y`*。*
> 7.  如果 Type( *`y`* )为布尔值，则返回比较结果*`x`*= = to number(*`y`*)。
> 8.  如果 Type( *`x`* )为字符串或数字，Type( *`y`* )为对象，则返回比较结果*`x`*= = top rimitive(*`y`*)。
> 9.  如果 Type( *`x`* )是 Object，Type( *`y`* )是 String 或 Number，则返回比较结果给 primitive(*`x`*)= =*`y`*。
> 10.  返回**假**。

当操作数类型相同时，执行该算法中的步骤 1。显示`undefined`等于`undefined`，`null`等于`null`。它还说明了没有什么等于`NaN`(不是数字)，两个相同的数值相等，+0 等于-0，两个长度和字符序列相同的字符串相等，`true`等于`true`和`false`等于`false`，对同一个对象的两个引用相等。

步骤 2 和 3 显示了为什么`null != undefined`返回`false`。JavaScript 认为这些值是相同的。

从步骤 4 开始，算法变得有趣了。这一步主要关注数字和字符串值之间的相等性。当第一个操作数是一个数字，第二个操作数是一个字符串时，第二个操作数通过`ToNumber()`内部函数转换成一个数字。表达式*`x`*= = to number(*`y`*)表示递归；重新应用第 11.9.1 节中开始的算法。

第 5 步与第 4 步等效，但第一个操作数是字符串类型，必须转换为数字类型。

步骤 6 和 7 将布尔操作数转换为数字类型并递归。如果另一个操作数是布尔值，它将在下一次执行该算法时被转换为一个数字，该算法将再递归一次。从性能角度来看，您可能希望确保两个操作数都是布尔类型，以避免两个递归步骤。

步骤 9 揭示了如果任一操作数是对象类型，则该操作数通过
`ToPrimitive()`内部函数被转换为原始值，并且算法递归。

最后，算法认为两个操作数不相等，并在步骤 10 中返回`false`。

虽然很详细，但是抽象的等式比较算法很容易理解。但是，它指的是一对内部函数，`ToNumber()`和`ToPrimitive()`，需要暴露它们的内部工作方式，才能对算法有一个完整的理解。

`ToNumber()`函数将其参数转换成一个数字，在 9.3 节中有描述。下面的列表总结了可能的非数字参数和等效返回值:

*   如果参数未定义，则返回 **NaN** 。
*   如果参数为空，则返回 **+0** 。
*   如果参数为布尔值 true，则返回 **1** 。如果参数为布尔值 false，则返回 **+0** 。
*   如果参数是数字类型，则返回输入参数，没有转换。
*   如果参数具有字符串类型，则第 9.3.1 节“应用于字符串类型的数字”适用。返回对应于语法所指示的字符串参数的数值。如果参数不符合指定的语法，则返回 NaN。例如，参数`"xyz"`导致返回 NaN。同样，参数`"29"`导致返回 29。
*   如果参数具有对象类型，则应用以下步骤:
    1.  设 *primValue* 为本原(*输入自变量*，提示数)。
    2.  返回到 Number( *初始值*)。

`ToPrimitive()`函数接受一个输入参数和一个可选的 PreferredType 参数。输入参数被转换为非对象类型。如果一个对象能够转换成多种基本类型，`ToPrimitive()`使用可选的 PreferredType 提示来支持首选类型。转换过程如下:

1.  如果输入参数未定义，则返回输入参数(未定义)-没有转换。
2.  如果输入参数为 Null，则返回输入参数(Null )-没有转换。
3.  如果输入参数具有布尔类型，则返回输入参数—没有转换。
4.  如果输入参数是数字类型，则返回输入参数，没有转换。
5.  如果输入参数是字符串类型，则返回输入参数—没有转换。
6.  如果输入参数具有对象类型，则返回对应于输入参数的默认值。通过调用对象的`[[DefaultValue]]`内部方法，传递可选的 PreferredType 提示，检索对象的默认值。在 8.12.8 节中为所有本地 ECMAScript 对象定义了`[[DefaultValue]]`的行为。

这一节介绍了大量的理论。在下一节中，我们将通过展示涉及`==`和`!=`的各种表达式并遍历算法步骤来评估它们，从而转向实际。

## 了解邪恶的双胞胎

现在我们已经知道了`==`和`!=`是如何根据 ECMAScript 规范工作的，让我们通过研究涉及这些操作符的各种表达式来很好地利用这些知识。我们将逐步了解这些表达式是如何计算的，并发现它们为什么是`true`或`false`。

对于我的第一个例子，考虑在文章开头附近出现的以下一对或表达式:

```
"this_is_true" == false // false
"this_is_true" == true  // false
```

按照以下步骤，根据抽象等式比较算法计算这些表达式:

1.  由于类型不同，跳过步骤 1:`typeof "this_is_true"` 返回`"string"`，`typeof false`或`typeof true`返回`"boolean"`。
2.  跳过第 2 步到第 6 步，它们不适用，因为它们与操作数类型不匹配。但是，第 7 步适用，因为右边的参数是 Boolean 类型。表达式被转换为`"this_is_true" == ToNumber(false)`和`"this_is_true" == ToNumber(true)`。
3.  `ToNumber(false)`返回+0，`ToNumber(true)`返回 1，分别将表达式简化为`"this_is_true" == +0`和`"this_is_true" == 1`。在这一点上，算法递归。
4.  跳过不适用的步骤 1 至 4。但是，第 5 步适用，因为左操作数是字符串类型，右操作数是数字类型。表达式被转换为`ToNumber("this_is_true") == +0`和`ToNumber("this_is_true") == 1`。
5.  `ToNumber("this_is_true")`返回 NaN，这将表达式分别简化为`NaN == +0`和`NaN == 1`。在这一点上，算法递归。
6.  进入步骤 1 是因为 NaN、+0 和 1 都是 Number 类型。跳过步骤 1.a 和 1.b，因为它们不适用。但是，步骤 1.c.i 适用，因为左操作数是 NaN。该算法现在返回 false (NaN 不等于包括其自身在内的任何值)作为每个原始表达式的值，并倒带堆栈以完全退出递归。

我的第二个例子(根据[银河系漫游指南](http://en.wikipedia.org/wiki/The_Hitchhiker's_Guide_to_the_Galaxy)基于生命的意义)通过`==`比较一个物体和一个数字，返回一个值`true`:

```
var lifeAnswer = {
  toString: function() {
    return "42";
  }
};

alert(lifeAnswer == 42);
```

以下步骤显示了 JavaScript 如何使用抽象等式比较算法来获得 true 作为表达式的值:

1.  跳过第 1 步到第 8 步，它们不适用，因为它们与操作数类型不匹配。但是，第 9 步适用，因为左操作数是 Object 类型，右操作数是 Number 类型。该表达式被转换为`ToPrimitive(lifeAnswer) == 42`。
2.  `ToPrimitive()`在没有提示的情况下调用`lifeAnswer`的`[[DefaultValue]]`内部方法。根据 ECMAScript 262 规范的 8.12.8 节，`[[DefaultValue]]`调用`toString()`方法，该方法返回`"42"`。表达式被转换成`"42" == 42`并且算法递归。
3.  跳过第 1 步到第 4 步，它们不适用，因为它们与操作数类型不匹配。但是，第 5 步适用，因为左操作数是字符串类型，右操作数是数字类型。该表达式被转换为`ToNumber("42") == 42`。
4.  `ToNumber("42")`返回 42，表达式转换为 42 == 42。算法递归并执行步骤 1.c.iii。因为数字是相同的，所以返回`true`并且递归展开。

对于我的最后一个例子，让我们弄清楚为什么下面的序列没有证明传递性，其中第三个比较将返回`true`而不是`false`:

```
'' == 0   // true
0 == '0' // true
'' == '0' // false
```

下面的步骤展示了 JavaScript 如何使用抽象等式比较算法来得出`true`作为`'' == 0`的值。

1.  第 5 步执行产生`ToNumber('') == 0`，它转换为`0 == 0`，算法递归。(规范中 9.3.1 节说明*StringNumericLiteral:::[empty]的 MV[数学值]为 0。*换句话说，空字符串的数值是 0。)
2.  Step 1.c.iii 执行，比较 0 和 0 并返回`true`(并展开递归)。

下面的步骤展示了 JavaScript 如何使用抽象等式比较算法得出`true`作为`0 == '0'`的值:

1.  第 4 步执行产生`0 == ToNumber('0')`，它转换为`0 == 0`，算法递归。
2.  Step 1.c.iii 执行，比较 0 和 0 并返回`true`(并展开递归)。

最后，JavaScript 执行抽象等式比较算法中的步骤 1.d，得到`true`作为`'' == '0'`的值。因为两个字符串的长度不同(0 和 1)，所以返回`false`。

## 结论

也许你想知道为什么你要为`==`和`!=`而烦恼。毕竟，前面的例子已经表明，由于类型强制和递归，这些操作符可能比它们的`===`和`!==`对应的操作符要慢。你可能想要使用`==`和`!=`，因为在某些情况下`===`和`!==`没有任何优势。考虑下面的例子:

```
typeof lifeAnswer === "object"
typeof lifeAnswer == "object"
```

`typeof`运算符返回一个字符串值。因为一个字符串值正在与另一个字符串值(`"object"`)进行比较，所以不会发生类型强制，`==`与`===`一样有效。也许从未接触过`===`的 JavaScript 新手会发现这样的代码更清晰。类似地，下面的代码片段不需要类型强制(两个操作数都是数字类型)，因此`!=`的效率不亚于`!==`:

```
array.length !== 3
array.length != 3
```

这些例子表明`==`和`!=`在不需要强迫的比较中是合适的。当操作数类型不同时，`===`和`!==`是可行的，因为它们返回`false`而不是意外值(例如，`false == ""`返回`true`)。如果操作数类型相同，没有理由不使用`==`和`!=`。也许是时候停止害怕这对邪恶的双胞胎了，在你了解他们之后，他们并不那么邪恶。

## 分享这篇文章