# 现实世界中的自动类型转换

> 原文：<https://www.sitepoint.com/automatic-type-conversion/>

JavaScript 中有一些常见的表达式，但是一些编程纯粹主义者会告诉你这不是一个好主意。这些表达式的共同点是依赖于**自动类型转换**——这是 JavaScript 的一个核心特性，根据环境和你的观点，这既是优点也是缺点。

所以在这篇文章中，我想特别看看其中的两种表达方式，并考虑它们在什么情况下是好主意，什么情况下不是好主意。

第一个表达式是一个简单的`if()`条件:

```
if(foo)
{
}
```

第二个是变量赋值，可以选择可能的值:

```
var x = foo || bar;
```

如果那些例子中的`foo`和`bar`都是布尔值，那么表达式很简单:如果`foo`是`true`，则第一个条件通过；如果`foo`是`true`，第二个表达式将`foo`赋给`x`，否则将`bar`赋给`x`。

但是如果它们不是简单的布尔值呢——如果`foo`是一个对象、一个字符串或者未定义的呢？如果`foo`和`bar`是不同的数据类型会怎样？为了理解如何计算这些表达式，我们需要理解 JavaScript 如何在数据类型之间自动转换。

## 自动类型转换

JavaScript 是一种**“松散类型”语言**，这意味着每当一个操作符或语句需要特定的数据类型时，JavaScript 就会自动将数据转换成该类型。第一个例子中的`if()`语句期望[是一个布尔值](https://www.sitepoint.com/boolean-data-type/)，因此您在括号中定义的任何内容都将被转换为布尔值。对于`while()`和`do...while()`语句也是如此。

根据这种转换的结果(即`true`或`false`)，JavaScript 值通常被称为“真”或“假”。最简单的思考方式是这样的:**一个值为真，除非已知它为假**；而事实上只有*六个*假值:

*   `false`(当然！)
*   `undefined`
*   `null`
*   `0`(数字零)
*   `""`(空字符串)
*   `NaN`(不是数字)

值得注意的例外是`"0"`(字符串零)和所有类型的对象——这是真的——这包括*所有的*原始构造函数，这意味着`new Boolean(false)`计算为`true`！(有点令人困惑，但在实践中，您永远不需要以这种方式创建原始值。)

*注意:比较两个假值并不总是会产生您可能期望的结果，例如`(null != false)`，即使两个值都是假值。有一些相当复杂的算法决定了等式求值是如何工作的，讨论它们超出了本文的范围。但是如果你对细节感兴趣，可以看看抽象的等式比较算法，它是 [ECMAScript 5.1](http://ecma-international.org/ecma-262/5.1/) 的一部分。*

## 条件快捷方式

我在开始向您展示的`if()`示例将其表达式转换为布尔值，由于对象总是计算为`true`而`null`计算为`false`，我们可以使用类似的条件来测试 <abbr title="Document Object Model">DOM</abbr> 元素的存在:

```
var element = document.getElementById("whatever");
if(element)
{
  //the element exists
}
else
{
  //the element doesn't exist
}
```

这在处理 <abbr title="Document Object Model">DOM</abbr> 元素时总是可靠的，因为**DOM 规范要求一个不存在的元素返回`null`** 。

然而，其他情况就不那么明确了，比如这个例子:

```
function doStuff(foo)
{
  if(foo)
  {
    ...
  }
}
```

如果将`foo`参数定义为“，那么这样的条件经常被用来表示“T4”，但是在一些情况下，这样的条件会失败——也就是说，在任何情况下,`foo`都是假值。因此，例如，如果它是布尔值`false`或空字符串，那么条件代码将不会被执行，即使`foo` *被定义为*。

相反，这是我们想要的:

```
function doStuff(foo)
{
  if(typeof foo != "undefined")
  {
    ...
  }
}
```

尚未定义的参数(和其他变量)的数据类型为`"undefined"`。所以我们可以使用`typeof`比较器来测试参数的数据类型，如果`foo`被定义了，那么条件总是通过。当然，`if()`表达式仍然在计算一个布尔值，但是它计算的布尔值是*那个`typeof`表达式的结果*。

## 分配快捷方式

我在开始时向您展示的第二个示例使用了一个逻辑运算符，来确定应该将两个值中的哪一个赋给变量:

```
var x = foo || bar;
```

逻辑运算符*不会返回*布尔值，但它们仍然*期望*布尔值，因此转换和求值在内部进行。如果`foo`评估为`true`，则返回 `foo`的*值，否则返回`bar`的值。这非常有用。*

该表达式常见于事件处理函数中，用于根据支持的模型定义事件参数:

```
element.onclick = function(e)
{
  e = e || window.event;
};
```

所以`e`被评估为一个布尔值，如果支持事件参数模型，它将为 the(一个事件对象),如果不支持，它将为 falsey(未定义);如果为真，则返回`e`，否则返回`window.event`。

相同类型的表达式也通常用于分配事件属性，通过评估每种可能性来找到支持的属性:

```
var target = e.target || e.srcElement || window;
```

所以每个引用都被依次评估(从左到右)，第一个评估为`true`的引用将被返回。第一种情况处理标准模型，第二种情况用于 Internet Explorer，而第三种情况用于事件可能在`window`对象(没有`srcElement`属性)上触发时的 Internet Explorer。

但是，在数据的真实性未知的情况下，像这样的表达式同样容易失败。例如，另一个常见的用例是为可选参数定义默认值，但这并不好:

```
function doStuff(foo)
{
  foo = foo || "default value";
}
```

现在，如果你确定`foo`将*总是*或者是一个字符串或者是未定义的，并且假设一个空字符串应该被视为未定义的，那么这个表达式是安全的。但如果不是，则需要重新定义为更精确的东西，例如:

```
function doStuff(foo)
{
  if(typeof foo != "string")
  {
    foo = "default value";
  }
}
```

通过对照`"string"`测试类型，我们可以处理多种情况——其中`foo`是未定义的，也可能被错误定义为非字符串值。在这种情况下，我们也允许空字符串作为有效输入，但是如果我们想排除空字符串，我们必须添加第二个条件:

```
function doStuff(foo)
{
  if(typeof foo != "string" || foo == "")
  {
    foo = "default value";
  }
}
```

还有其他令人惊讶的微妙情况，这可能是一个陷阱。例如，我们可能有一个创建 unix 时间戳的日期函数，除非可选地定义了输入时间戳:

```
function doDateStuff(timestamp)
{
  timestamp = timestamp || new Date().getTime();
}
```

如果输入是`0`，这将会失败——因为零是一个假值，但它也是一个有效的时间戳。

## 总则

从所有这些中得到的一般教训很简单— **考虑类型转换将如何影响求值**，注意不要陷入我们已经遇到的陷阱。只要小心谨慎，你*仍然可以*利用自动类型转换，在适当的地方缩短条件和逻辑表达式。

但是这确实回避了一个问题——如果我们知道使用`typeof`进行显式测试总是安全的，而依赖自动类型转换有时并不安全——那么为什么不总是让*显式呢*？当然，如果偏爱较短语法的唯一原因是打字更快，那么这就是一个懒惰和草率的理由。

但事实是，JavaScript 通常在公共网络上运行，在这种情况下，**文件大小会产生影响**。文件越小，加载速度越快，占用的带宽越少，语法快捷方式越少。

利用更短的表达式本身并不是一种优化，它只是一种充分利用语言特性的编码风格。

## 分享这篇文章