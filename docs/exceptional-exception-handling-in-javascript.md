# JavaScript 中的异常处理

> 原文：<https://www.sitepoint.com/exceptional-exception-handling-in-javascript/>

任何可能出错的事情都会出错。墨菲定律甚至适用于软件开发。对于非琐碎的程序来说，不是*如果*的问题，而是什么时候*会出错的问题。不符合标准、不受支持的特性和浏览器怪癖只是 web 开发人员面临的潜在问题的几个来源。考虑到所有可能出错的事情，JavaScript 有一个令人惊讶的简单方法来处理错误――它只是默默放弃和失败。至少，用户看到的是这种行为。事实上，在引擎盖下发生了很多事情。*

当 JavaScript 语句产生错误时，据说*抛出*一个*异常*。JavaScript 解释器检查[异常处理](http://en.wikipedia.org/wiki/Exception_handling "Exception handling - Wikipedia")代码，而不是继续下一条语句。如果没有异常处理程序，那么程序从抛出异常的函数中返回。对[调用堆栈](http://en.wikipedia.org/wiki/Call_stack "Call stack - Wikipedia")上的每个函数重复这一过程，直到找到异常处理程序或到达顶层函数，导致程序终止。

# 错误对象

当异常发生时，会创建并引发一个表示错误的对象。JavaScript 语言定义了七种类型的内置错误对象。这些错误类型是异常处理的基础。下面详细描述了每种错误类型。

## 错误

“错误”类型用于表示一般异常。这种类型的异常最常用于实现用户定义的异常。创建用户定义的异常的主题将在本文的后面重新讨论。“Error”对象通过调用它们的构造函数来实例化，如下例所示。

```
var error = new Error("error message");
```

“错误”对象包含两个属性，“名称”和“消息”。“name”属性指定异常的类型(在本例中为“Error”)。“消息”属性提供了对异常的更详细的描述。“消息”从传递给异常构造函数的字符串中获取其值。其余的异常类型代表更具体的错误类型，但它们都以与一般“错误”类型相同的方式使用。

## 范围误差

“RangeError”异常由超出指定范围的数字生成。例如，JavaScript numbers 有一个 toFixed()方法，它采用一个“digits”参数来表示小数点后出现的位数。该参数应该在 0 到 20 之间(尽管有些浏览器支持更大的范围)。如果“digits”的值超出了这个范围，那么就会抛出“RangeError”。下面的示例显示了这种情况。

```
var pi = 3.14159;

pi.toFixed(100000);  // RangeError
```

## 参考错误

当访问不存在的变量时，会引发“ReferenceError”异常。这些异常通常发生在现有变量名拼写错误时。在下面的示例中，当访问“bar”时会出现“ReferenceError”。请注意，此示例假设在尝试增量操作时，“bar”不存在于任何活动范围中。

```
function foo() {
  bar++;  // ReferenceError
}
```

## 句法误差

当 JavaScript 语言的规则被破坏时，就会抛出“SyntaxError”。熟悉 C 和 Java 等语言的开发人员已经习惯了在编译过程中遇到语法错误。但是，因为 JavaScript 是一种解释型语言，所以语法错误直到代码执行后才会被识别出来。语法错误是独一无二的，因为它们是唯一无法恢复的异常类型。由于“if”语句缺少右大括号，下面的示例将生成语法错误。

```
if (foo) {  // SyntaxError
  // the closing curly brace is missing
```

## TypeError

当值不是预期的类型时，会发生“TypeError”异常。试图调用不存在的对象方法是这类异常的常见原因。下面的示例创建一个名为“foo”的空对象，然后尝试调用它的 bar()方法。因为 bar()没有定义，所以在尝试调用时会抛出“TypeError”。

```
var foo = {};

foo.bar(); // TypeError
```

## URIError

当 encodeURI()和 decodeURI()等方法遇到格式错误的 URI 时，它们会抛出“URIError”异常。下面的示例在尝试解码字符串“%”时生成“URIError”。“%”字符表示 URI 转义序列的开始。因为在本例中“%”后面没有任何内容，所以该字符串是一个无效的转义序列，因此是一个格式错误的 URI 组件。

```
decodeURIComponent("%"); // URIError
```

## 评估错误

当 eval()函数使用不当时，会引发“EvalError”异常。最新版本的 EcmaScript 标准中没有使用这些异常。但是，为了保持与旧版本标准的向后兼容性，仍然支持它们。

# 处理异常

现在我们知道了什么是异常，是时候学习如何阻止它们破坏我们的程序了。JavaScript 通过“try…catch…finally”语句处理异常。下面显示了一个通用示例语句。

```
try {
  // attempt to execute this code
} catch (exception) {
  // this code handles exceptions
} finally {
  // this code always gets executed
}
```

“try…catch…finally”语句的第一部分是“try”子句。“try”子句是强制性的，用于分隔程序员怀疑可能产生异常的代码块。“try”子句后面必须跟有“catch”和“finally”子句中的一个或两个。

# “附带条件”条款

“try…catch…finally”的第二部分是“catch”从句。“catch”子句是一段代码，只有在“try”子句中出现异常时才会执行。尽管“catch”子句是可选的，但是没有它就不可能真正处理异常。这是因为“catch”子句阻止异常通过调用堆栈传播，从而允许程序恢复。如果在“try”块中出现异常，则控制会立即传递给“catch”子句。发生的异常也被传递到“catch”块进行处理。以下示例显示了如何使用“catch”子句来处理“ReferenceError”。请注意，“ReferenceError”对象通过“exception”变量在“catch”子句中可用。

```
try {
  foo++;  // ReferenceError
} catch (exception) {
  var message = exception.message;

  // handle the exception
}
```

复杂的应用程序会产生各种异常。在这种情况下，可以使用“instanceof”运算符来区分各种类型的异常。在下面的示例中，假设“try”子句可以生成几种类型的异常。相应的“catch”子句使用“instanceof”来处理“TypeError”和“ReferenceError”异常，与所有其他类型的错误分开处理。

```
try {
  // assume an exception occurs
} catch (exception) {
  if (exception instanceof TypeError) {
    // Handle TypeError exceptions
  } else if (exception instanceof ReferenceError) {
    // Handle ReferenceError exceptions
  } else {
    // Handle all other types of exceptions
  }
}
```

# “最后”条款

“try…catch…finally”语句的最后一部分是可选的“finally”子句。“finally”子句是在“try”和“catch”子句之后执行的代码块，不管是否有错误。“finally”子句对于包含清理代码(关闭文件等)非常有用。)那是无论如何都需要执行的。请注意，即使发生未被捕获的异常，也会执行“finally”子句。在这种情况下，执行“finally”子句，然后抛出的异常正常进行。

关于“finally”子句的一个有趣的注意事项是，即使“try”或“catch”子句执行“return”语句，它也会被执行。例如，下面的函数返回 false，因为“finally”子句是最后执行的。

```
function foo() {
  try {
    return true;
  } finally {
    return false;
  }
}
```

# 抛出异常

JavaScript 允许程序员通过适当命名的“throw”语句抛出他们自己的异常。对于没有经验的开发人员来说，这个概念可能会有些混乱。毕竟，开发人员努力编写没有错误的代码，然而“throw”语句故意引入了这些错误。然而，故意抛出异常实际上会导致代码更容易调试和维护。例如，通过创建有意义的错误消息，识别和解决问题变得更加容易。

“throw”语句的几个例子如下所示。对于可以作为异常抛出的数据类型没有限制。同样的数据被捕获和抛出的次数也没有限制。换句话说，异常可以被抛出、捕获，然后再次抛出。

```
throw true;
throw 5;
throw "error message";
throw null;
throw undefined;
throw {};
throw new SyntaxError("useful error message");
```

虽然“throw”语句可以用于任何数据类型，但是使用内置异常类型还是有一些好处的。例如，Firefox 通过添加调试信息(如发生异常的文件名和行号)来对这些对象进行特殊处理。

作为一个示例场景，假设除法运算发生在应用程序的某个地方。除法可能很麻烦，因为有可能被零除。在 JavaScript 中，这样的操作会产生“NaN”。这可能会导致难以调试的混乱结果。如果应用程序大声抱怨被零除，事情会简单得多。下面的“if”语句通过抛出一个异常来完成这个任务。

```
if (denominator === 0)
  throw new Error("Attempted division by zero!");
```

当然，使用如下所示的“RangeError”可能更合适。

```
if (denominator === 0)
  throw new RangeError("Attempted division by zero!");
```

# 自定义异常对象

我们刚刚学习了如何使用内置的异常类型生成定制的错误消息。然而，另一种方法是通过扩展现有的“错误”类型来创建新的异常类型。因为新类型继承自“Error ”,所以它可以像其他内置异常类型一样使用。虽然 JavaScript 中的继承主题超出了本文的范围，但是这里介绍了一种简单的技术。

下面的例子回到处理被零除的问题。我们将创建自己的异常类型，而不是像前面那样使用“Error”或“RangeError”对象。在本例中，我们创建了“DivisionByZeroError”异常类型。示例中的函数充当新类型的构造函数。构造函数负责分配“名称”和“消息”属性。该示例的最后两行导致新类型从“Error”对象继承。

```
function DivisionByZeroError(message) {
  this.name = "DivisionByZeroError";
  this.message = (message || "");
}

DivisionByZeroError.prototype = new Error();
DivisionByZeroError.prototype.constructor = DivisionByZeroError;
```

# 要记住的事情

*   “try…catch…finally”语句用于处理异常。
*   “try”子句标识可能会生成异常的代码。
*   “catch”子句仅在发生异常时执行。
*   “finally”子句总是要执行的，不管是什么。
*   “throw”语句用于生成异常。
*   自定义异常对象应该从现有的“错误”类型继承。

图片来自 [Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm_campaign=sitepoint)

## 分享这篇文章