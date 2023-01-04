# 参数:JavaScript 的古怪之处

> 原文：<https://www.sitepoint.com/arguments-a-javascript-oddity/>

**`arguments`是每个函数内部可用的局部数组对象的名称。它很古怪，经常被忽视，但却是许多编程魔法的来源；所有主要的 JavaScript 库都利用了`arguments`对象的力量。这是每个 JavaScript 程序员都应该熟悉的东西。**

在任何函数内部，您都可以通过变量:`arguments`来访问它，它包含一个数组，该数组包含调用函数时提供给函数的所有参数。它实际上不是一个 JavaScript 数组；`typeof arguments`将返回值:`"object"`。您可以通过数组索引访问各个参数值，它像其他数组一样有一个`length`属性，但是它没有像`push`和`pop`这样的标准`Array`方法。

## 创建灵活的功能

尽管看起来有限，`arguments`是一个非常有用的对象。例如，您可以创建接受可变数量参数的函数。狄恩·爱德华兹在 base2 库中发现的`format`函数展示了这种灵活性:

```
function format(string) {
  var args = arguments;
  var pattern = new RegExp("%([1-" + arguments.length + "])", "g");
  return String(string).replace(pattern, function(match, index) {
    return args[index];
  });
};
```

您提供一个模板字符串，其中您使用`%1`到`%9`为值添加占位符，然后提供最多 9 个其他参数来表示要插入的字符串。例如:

```
format("And the %1 want to know whose %2 you %3", "papers", "shirt", "wear");
```

上面的代码将返回字符串`"And the papers want to know whose shirt you wear"`。

您可能已经注意到，在`format`的函数定义中，我们只指定了一个参数:`string`。JavaScript 允许我们向函数传递任意数量的参数，而不管函数定义如何，并且`arguments`对象可以访问所有这些参数。

## 将其转换为实数组

即使`arguments`不是一个实际的 JavaScript 数组，我们也可以通过使用标准的`Array`方法、`slice`将它转换成一个数组，如下所示:

```
var args = Array.prototype.slice.call(arguments);
```

变量`args`现在将包含一个适当的 JavaScript `Array`对象，该对象包含来自`arguments`对象的所有值。

## 创建带有预设参数的函数

对象允许我们执行各种 JavaScript 技巧。下面是`makeFunc`函数的定义。此函数允许您为该函数提供一个函数引用和任意数量的参数。它将返回一个调用您指定的函数的匿名函数，并在调用匿名函数时提供预设参数和任何新参数:

```
function makeFunc() {
  var args = Array.prototype.slice.call(arguments);
  var func = args.shift();
  return function() {
    return func.apply(null, args.concat(Array.prototype.slice.call(arguments)));
  };
}
```

提供给`makeFunc`的第一个参数被认为是对您希望调用的函数的引用(是的，在这个简单的例子中没有错误检查),它被从 arguments 数组中删除。`makeFunc`然后返回一个匿名函数，该函数使用`Function`对象的`apply`方法来调用指定的函数。

`apply`的第一个参数指的是函数将被调用的范围；基本上关键字`this`在被调用的函数中引用的内容。就目前来说这有点高级，所以我们只保留它`null`。第二个参数是一个值数组，它将被转换成函数的`arguments`对象。`makeFunc`将原始值数组连接到提供给匿名函数的参数数组上，并将其提供给被调用的函数。

假设您需要输出一条消息，而模板总是相同的。为了避免每次调用`format`函数时都要引用模板，您可以使用`makeFunc`实用函数返回一个将为您调用`format`的函数，并自动填充模板参数:

```
var majorTom = makeFunc(format, "This is Major Tom to ground control. I'm %1.");
```

可以像这样反复调用`majorTom`函数:

```
majorTom("stepping through the door");
majorTom("floating in a most peculiar way");
```

每次调用`majorTom`函数时，它都会调用`format`函数，其中第一个参数，即模板，已经填充好了。上述调用返回:

```
"This is Major Tom to ground control. I'm stepping through the door."
"This is Major Tom to ground control. I'm floating in a most peculiar way."
```

## 创建自引用函数

你可能认为这很酷，但是等等，arguments 还有一个惊喜；它还有另一个有用的属性:`callee`。`arguments.callee`包含对创建`arguments`对象的函数的引用。这样的东西怎么用？`arguments.callee`是匿名函数引用自身的一种便捷方式。

```
repeat is a function that takes a function reference, and 2 numbers. The first number is how many times to call the function and the second represents the delay, in milliseconds, between each call. Here's the definition for repeat:

```

```
function repeat(fn, times, delay) {
  return function() {
    if(times-- > 0) {
      fn.apply(null, arguments);
      var args = Array.prototype.slice.call(arguments);
      var self = arguments.callee;
      setTimeout(function(){self.apply(null,args)}, delay);
    }
  };
}
```

```
repeat uses arguments.callee to get a reference, in the variable self, to the anonymous function that runs the originally supplied function. This way the anonymous function can call itself again after a delay using the standard setTimeout function.

因此，我的应用程序中有一个简单的函数，它接受一个字符串并弹出一个包含该字符串的警告框:

```

```
function comms(s) {
  alert(s);
}
```

然而，我想创建一个特殊版本的函数，重复 3 次，每次之间有 2 秒钟的延迟。用我的`repeat`函数，我可以这样做:

```
var somethingWrong = repeat(comms, 3, 2000);

somethingWrong("Can you hear me, major tom?");
```

调用`somethingWrong`函数的结果是一个重复 3 次的警告框，每次警告之间有 2 秒钟的延迟。

```
arguments is not often used, a little quirky, but full of surprises and well worth getting to know!
```

## 分享这篇文章