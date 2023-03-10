# 用于填充字符串和数字的实用函数

> 原文：<https://www.sitepoint.com/a-utility-function-fo-padding-strings-and-numbers/>

**这篇文章关注于用前导字符填充值的函数。这种类型的函数对于格式化整数日期和时间特别有用。**

## 该函数的作用

`pad`函数以一个字符串或数字开始，用前导字符填充输入，直到指定的字符串长度。虽然该函数可以用任何其他值填充任何值，但最常见的用法可能是用前导零填充数字，例如，将 24 小时制中的小时、分钟和秒标准化。`pad`功能的代码如下所示。

```
function pad(input, length, padding)
{
  while((input = input.toString()).length + (padding = padding.toString()).length < length)
  {
    padding += padding;
  }
  return padding.substr(0, length - input.length) + input;
}
```

从代码中可以看出，该函数有三个参数。第一个参数`input`是要填充的值。第二个参数`length`是填充字符串的期望长度。第三个参数`padding`是填充值，通常只有一个字符。

## 使用函数

举例来说，`Date.getHours()`函数根据当地时间返回一个数字，表示指定日期中的小时。要将小时规范化为两位数的数字字符串，您可以像这样通过`pad`传递它:

```
var hours = pad(new Date().getHours(), 2, 0);
```

进一步看前面的例子，您可以创建一个完整的 24 小时时间字符串，如下所示:

```
var date = new Date(), time = [
  pad(date.getHours(), 2, 0),
  pad(date.getMinutes(), 2, 0),
  pad(date.getSeconds(), 2, 0)
];
alert( time.join(':') );
```

## 该功能的按键

首先，我们必须将输入值和填充值都转换为字符串，这样操作的结果总是连接，而不是数字相加，即使两个值都是数字。我们可以坚持输入必须是字符串，但这会使函数使用起来不太方便。例如，如果不自己将`Date.getHours()`的输出转换成一个字符串，就无法传递给它。编写这样一个函数最显而易见的方法是简单地将填充字符串添加到输入字符串的开头，直到达到所需的长度，如下所示。

```
while(input.length < length)
{
  input = padding + input;
}
return input;
```

这种方法的问题是，只有当填充字符串是单个字符，或者输出长度减去输入长度能被填充长度整除时，它才能正确工作。例如，如果我们传递一个输入值`"7"`，一个输出长度`6`，以及填充字符串`"0x"`，结果将是`"0x0x0x7"`。这显然长于指定的输出长度。

为了解决这个问题，我们首先预编译填充符——根据需要连接尽可能多的填充符实例，以达到或超过输出长度。然后，我们向输入字符串中添加一个精确的子字符串，以创建指定的精确输出长度:

```
while(input.length + padding.length < length)
{
  padding += padding;
}
return padding.substr(0, length - input.length) + input;
```

使用与前面相同的示例，改进后的代码的结果将是`"0x0x07"`，因为实际上只使用了最终填充实例的一个字符。

## 结论

现在你有了它，一个简短而可爱的函数，它执行一个简单但非常有用的任务。我们已经创建了一个函数来帮助格式化日期、时间、货币和 rgb-hex 值。请务必在接下来的几周内查看更多简短而甜蜜的函数。

## 分享这篇文章