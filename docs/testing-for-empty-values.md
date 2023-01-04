# 测试 Javascript 中的空值

> 原文：<https://www.sitepoint.com/testing-for-empty-values/>

本系列的第二篇文章涵盖了简短但有趣的函数，讨论了测试空值的问题。这个函数叫做`empty()`。类似于同名的 PHP 函数，它接受一个变量或属性，并告诉您值是否为空。空 T2 的定义取决于所讨论的值。

PHP 的`empty()`函数非常松散。例如，`0`和`false`都将返回`true`，这让许多粗心的开发者感到惊讶！我们的 JavaScript 函数对于哪些类型的数据可以被认为是空的要精确得多:

*   `undefined`或`null`
*   零长度字符串
*   没有成员的数组
*   没有可枚举属性的对象

不管它们的值是多少，布尔值和数字永远不会*为空*。下面是`empty()`函数的代码:

```
function empty(data)
{
  if(typeof(data) == 'number' || typeof(data) == 'boolean')
  { 
    return false; 
  }
  if(typeof(data) == 'undefined' || data === null)
  {
    return true; 
  }
  if(typeof(data.length) != 'undefined')
  {
    return data.length == 0;
  }
  var count = 0;
  for(var i in data)
  {
    if(data.hasOwnProperty(i))
    {
      count ++;
    }
  }
  return count == 0;
}
```

## 该功能如何工作

您可以看到有四个条件用于测试不同的数据类型。第一个函数处理布尔值和数字，并且总是返回`false`。可能有人认为这个条件是不必要的，因为，好吧，只是不要传递布尔值和数字！但是提供这种灵活性意味着您可以传入任意数据。第二个条件针对`undefined`和`null`测试值。这对于设置可选函数参数的默认值特别有用，如下例所示。

```
function doSomething(foo, bar)
{
  if(empty(bar))
  {
    bar = 'default value';
  }
}
```

虽然`null`显然与`undefined`不同，但是对于许多意图和目的来说，它们等同于同一件事(即这段数据没有想要的值)。例如，如果您使用对象语法查询一个 [web 存储](https://www.w3.org/TR/webstorage/)对象，一个未定义的值将是`undefined`，然而`getItem()`方法将返回`null`。

`empty()`中的第三个条件处理任何带有`length`属性的东西。这将检测没有成员的空字符串和数组。我确实考虑了一个额外的条件来检查为空或者只有空格的字符串。该测试如下所示。

```
if(typeof(data.length) != 'undefined')
{
  if(/^[\s]*$/.test(data.toString()))
  {
    return true;
  }
  return data.length == 0;
}
```

我决定不包括之前的检查，因为它引入了歧义。只有空白的字符串是否可以被认为是空的是一个观点或应用程序特定的问题。相反，我选择让函数尽可能清晰(毕竟我们不是在写 PHP)。

最后一个条件处理对象，遍历它们并计算它们的可枚举成员。如果计数器是零，那么该对象被认为是空。

## 忽略不相关的

由于位于末尾，final 条件处理通过的任何其他内容，包括函数、正则表达式、元素和文档。函数和正则表达式将被视为空的，因为它们通常没有可枚举的属性。元素和文档不会是空的，因为它们有很多属性。

这是一种可能值得避免的歧义，但在某些时候你必须问自己，“这真的值得吗？”考虑您可能要测试的数据类型。它通常是简单的变量、数组、字典、函数参数、表单值、Ajax 响应、属性和文本节点；它不太可能是函数、正则表达式或 <abbr title="Document Object Model">DOM</abbr> 子树。`empty()`函数在精确评估和不浪费时间测试每一种可能的可能性之间找到了一个平衡点。

## 分享这篇文章