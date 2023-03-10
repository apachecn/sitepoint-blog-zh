# 智能字符串缩写

> 原文：<https://www.sitepoint.com/intelligent-string-abbreviation/>

在小而甜的函数系列的第七篇文章中，我想向您展示一个名为`abbreviate()`的函数——我相信您可以猜到它的主要用途！它将字符串缩写为指定的最大长度，但它这样做是明智的——确保单词中间不会出现拆分，并预处理字符串以删除无关的空白。

下面是`abbreviate`函数的代码:

```
function abbreviate(str, max, suffix)
{
  if((str = str.replace(/^\s+|\s+$/g, '').replace(/[\r\n]*\s*[\r\n]+/g, ' ').replace(/[ \t]+/g, ' ')).length <= max)
  {
    return str;
  }

  var 
  abbr = '',
  str = str.split(' '),
  suffix = (typeof suffix !== 'undefined' ? suffix : ' ...'),
  max = (max - suffix.length);

  for(var len = str.length, i = 0; i < len; i ++)
  {
    if((abbr + str[i]).length < max)
    {
      abbr += str[i] + ' ';
    }
    else { break; }
  }

  return abbr.replace(/[ ]$/g, '') + suffix;
}
```

该函数有三个参数**——原始输入字符串、最大输出长度和添加到缩写字符串末尾的可选后缀。如果后缀没有定义，那么它默认为`" ..."`(一个空格后跟三个点)，这是一种常见的、可识别的表示缩写的方式。**

 **## 这个功能有什么用

当您需要限制字符串的长度时，可以使用该函数，作为简单的`substr`表达式的更智能的替代方法。有许多可能的应用——比如处理表单输入、创建自定义工具提示、在基于 web 的电子邮件列表中显示消息主题，或者预处理通过 Ajax 发送的数据。

例如，要将一个字符串限制为`100`个字符并添加默认后缀，我们可以这样调用它:

```
str = abbreviate(str, 100);
```

这在概念上相当于这个`substr`表达式:

```
str = str.substr(0, 96) + " ..."
```

但是这是一个非常笨拙的工具，因为它经常会导致输出字符串在一个单词的中间被分开。而`abbreviate`函数是专门设计来不这么做的，它会在最后一个单词之前而不是中间拆分字符串*。所以由`abbreviate()`产生的输出字符串通常会比指定的最大值*短*——但是**永远不会比**长。*

该函数还考虑了缩写后缀所需的空间，即如果特定的最大 if `100`但后缀本身是 4 个字符，那么我们最多只能使用主输入字符串的 96 个字符。

您可以通过传递一个空字符串来完全不指定后缀，或者如果您想要缩写一个标记字符串，那么您可以将其定义为一个 <abbr title="HyperText Markup Language">HTML</abbr> 结束标记。例如，以下输入:

```
abbreviate("<p>One two three four five</p>", 15, "</p>");
```

会产生这样的输出:

```
<p>One two</p>
```

## 该功能如何工作

`abbreviate`函数的关键是能够将一个输入字符串分割成单个的单词，然后重新编译尽可能多的单词以适应最大长度。

为了使这一点有效，我们需要确保单词之间的拆分是可预测的，最简单的方法是通过**最小化内部空白**——将换行符和制表符转换为空格，然后减少连续的空格，这样内部空白的每一块都变成一个空格。当然，还有其他的处理方式——例如，我们可以为`split`定义一个更灵活的正则表达式，考虑到我们可能在单词之间找到的所有不同类型的字符。正则表达式甚至有一个单词边界字符(`"b"`)，所以我们可以使用它。

但是我发现空白预处理本身是有用的，尤其是在涉及到用户输入的时候。按单词边界分割不会产生想要的结果，因为破折号、点、逗号和大多数特殊字符实际上都算作单词边界。但是我认为用标点符号拆分单词是不合适的，除非这个字符后面有一个空格，这样像连字符和代码片段这样的东西就不会在中间拆分。

所以这个函数的第一项工作是做空白预处理，然后如果结果是*已经比指定的最大值*短，我们可以直接返回它:

```
if((str = str.replace(/^\s+|\s+$/g, '').replace(/[\r\n]*\s*[\r\n]+/g, ' ').replace(/[ \t]+/g, ' ')).length <= max)
{
  return str;
}
```

如果我们不这样做，那么我们可能会遇到这样的情况:字符串在不必要的时候被缩写，例如:

```
abbreviate("Already long enough", 20)
```

如果没有第一个条件，我们将得到简短的输出，因为指定的最大值必须考虑后缀的长度:

```
Already long ...
```

而添加第一个条件会产生未修改的输出:

```
Already long enough
```

因此，除非我们在这一点上返回，否则我们将继续编译缩写字符串——将输入字符串按空格分开以创建单个单词，然后迭代地将每个单词-空格对加在一起，只要缩写字符串短于指定的最大值。

一旦我们编译了所需的内容，我们就可以中断迭代，然后在添加后缀并最终返回结果之前，从缩写字符串的末尾开始修剪剩余的空间。右移剩余空格，只添加默认后缀，看起来有点浪费，但是这样做，我们允许输入后缀根本没有空格。

## 结论

这样就有了——一个简单但智能的缩写字符串的函数，它还预处理输入以删除无关的空白。根据我的经验，这两个需求经常同时存在，这就是为什么我开发了这样的函数。

## 分享这篇文章**