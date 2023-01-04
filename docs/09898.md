# 条件中的(更多)赋值

> 原文：<https://www.sitepoint.com/more-assignment-in-conditions/>

这是我几个月前写的文章的后续，在文章中，我描述了一种在同一个表达式中赋值和计算变量的技术，并演示了它在检索和引用 <abbr title="Document Object Model">DOM</abbr> 节点中的用途。

虽然你们中有相当多的人觉得语法太混乱，不值得使用；这只会产生难以阅读的代码:

```
if(summary = document.getElementById("post-summary"))
{
    return summary.innerHTML;
}
```

我对那个观点有某种程度的同情；但不是非常多，因为最终结果是表达式是**代码少**，通常是**更快** <abbr title="in around 80% of cross-browser benchmarks">*</abbr> ，能**节省内存**(优化的前三大目标！)

## 不顾一切地继续下去

所以在过去的几个月里，我一直在以一种相当特别的方式试验这种技术，寻找它可能适用和有用的情况。我想出了几个新的用例，看起来工作得相当不错；但是我也一直看到一个最终不能被忽视的问题(之前的一位读者实际上已经指出了这个问题——这个问题是**变量范围**和**不适当的全局变量创建**的问题之一。

### 避免创建全局变量

在上面的代码示例中，表达式创建了一个名为`summary`的变量；但是变量声明前面没有`var`，也不可能在那个上下文中:

```
if(var summary = document.getElementById("post-summary"))
```

那只会抛出一个无效语法的错误。

但是没有前面的`var`我们已经创建了一个*全局*变量，因为 JavaScript 的**隐含全局**行为(即任何没有用`var`显式声明的变量都被创建为全局变量)。如果我们已经在其他地方声明了这个变量，那就不是问题。否则，我们必须先声明它；但是如果我们必须在赋值条件之前单独做，那么我们就破坏了语法的一些快捷方式值！

所以我最后做的只是简单地提前声明一堆变量，这些变量可以在任何地方使用，因为它们的范围是受限制的。在哪里做并不重要*，只要它们是**封装的**和**在你需要它们的地方可用**——如果你在一个[类或模块模式](https://www.sitepoint.com/my-favorite-javascript-design-pattern)中工作，那么它的闭包的顶部看起来是一个显而易见的地方。为了使用这种语法，我在需要临时变量的地方反复使用相同的变量名；通过重用它们，而不是每次都创建一个新的，我最终能够**节省更多的内存**。*

## 包围赋值的括号

正如我们上次提到的，赋值返回一个值，**如果赋值用圆括号括起来，那么它就用一个语句**括起来。它首先被求值，就像在数学方程中一样，所以我们可以准确地使用它*，就好像它包含了文字值*。

这为我们提供了新的可能性——指定一个值，然后对该值执行任意评估:

```
if((foo = bar) > 0)
{
    //foo is greater than zero
}
```

该示例中的变量`bar`同样可以是函数的返回，保存它允许我们在条件中再次使用该值:

```
if((foo = getFooValue()) > 0)
{
    alert(foo + " is greater than zero");
}
```

这是保存长属性引用的快捷方式的一种特别有用的方法，它最终会使代码运行得更快，因为需要的对象遍历更少:

```
if((foo = this.prop.subprop.childprop.value) > 0)
{
    alert(foo + " is greater than zero");
}
```

## 保存正则表达式的执行

使用这种语法，我们可以——在一个字符串中搜索一个<abbr title="regular-expression">正则表达式</abbr>模式，保存匹配数组，并评估结果——所有这些都在同一个表达式中(注意。 [`exec`](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/RegExp/exec) 是 [`RegExp`](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/regexp) 的方法，返回匹配数组，如果没有匹配则返回`null`):

```
if(matches = /^([a-z]+)[:](.*)$/.exec(data)) 
{ 
    alert("The key is " + matches[1] + " and the value is " + matches[2]); 
}
```

在那个例子中，我们依赖于这样一个事实，即`null`计算为`false`，matches 数组计算为`true`，这是一个安全的假设。但是同样，我们不必这样做——我们可以将所有这些都用括号括起来，然后进行严格的评估:

```
if((matches = /^([a-z]+)[:](.*)$/.exec(data)) !== null) 
{ 
    alert("There are " + matches.length + " matches"); 
}
else
{
    alert("There are no matches");
}
```

## 坚持下去——不要失去理智！

我第一个承认这种语法看起来有点奇怪，因此可能会引起混淆和误解。但是，对于无数事物来说(起初)这是真的，而不是丢弃它们的理由；就像其他事情一样——随着时间的推移，它变得熟悉起来。在我看来，它所能提供的远远超过它的成本:

*   它提供的是**优化**——一个影响*我们所有用户的切实问题*
*   代价是一些可读性(T1)——一个只影响美国 T2 的抽象问题(T3)

我不知道你怎么想，但我认为这很划算！

*缩略图鸣谢:[sbwoodside](http://www.flickr.com/photos/sbwoodside/3383458339/)*

## 分享这篇文章