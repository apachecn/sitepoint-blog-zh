# 使用搜索和替换练习正则表达式

> 原文：<https://www.sitepoint.com/practicing-regular-expressions/>

如果您刚刚开始使用正则表达式(regex)，语法一开始可能会有点令人费解(我推荐 Jason Pasnikowski 的文章作为一个很好的起点)。一开始很难掌握正则表达式的一个原因是你在代码中使用它们的次数很少，这反过来限制了你使用它们的次数。无论从事何种职业，无论是体育、娱乐还是发展，专业人士总是在练习——有些人比其他人练习得更多。

那么，如果您仅限于在代码中使用正则表达式，那么您如何练习使用正则表达式呢？答案是使用一个实用程序，这个实用程序有很多，它使用正则表达式来执行搜索和替换。我相信每个人都熟悉标准的“查找 *x* 并用 *y* 替换”类型的搜索和替换。大多数 ide 和文本编辑器都内置了正则表达式引擎来处理搜索和替换。在本文中，我将通过一系列练习来帮助您练习使用 regex。

在本文中，我将使用 NetBeans。有些编辑器的 regex 行为可能与您在这里看到的略有不同，所以如果您使用的是 NetBeans 之外的东西，并且它的工作不像您预期的那样，请务必阅读特定编辑器的文档。

## 单词边界

让我们使用下面的代码开始我们的例子；我特意制作了它来说明搜索和替换过程中的特殊注意事项。

```
<div id="navigation">
 <a href="divebomb.php" title="All About Divebombs">Divebombs</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="endives.php" title="All About Endives">Endives</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="indivisible.php" title="Indivisible by Zero">Indivisible Numbers</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="division.php" title="All About Division">Divison</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="skydiving.php" title="All About Skydiving">Skydiving</a>&nbsp;&nbsp;|&nbsp;&nbsp;
</div>
```

理想情况下，这个导航代码应该是一个无序列表，而不是`div`标签中的自由锚。但是你不能用 ul 代替 div，因为 divebomb 会变成 ulebomb，endives 会变成 enules，等等。你也不能使用"< div ",因为它会错过结束的`div`标签。您可以手动将`div`标签替换为`ul`标签，或者您可以使用表示单词边界的特殊序列`b`。

在搜索字段中，键入:`bdivb`
在替换字段中，键入:`ul`

这只替换由单词边界分隔的文本“div”。单词边界允许你执行全词搜索，所以

和中的单词“div”都被匹配，而锚点中的子字符串被保留。

稍后您还会看到`w`，它用于匹配非空白的“单词”字符。

## 分组和反向引用

继续第一个例子中修改过的代码，让我们继续重构列表。现在，您的代码应该如下所示:

```
<ul id="navigation">
 <a href="divebomb.php" title="All About Divebombs">Divebombs</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="endives.php" title="All About Endives">Endives</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="indivisible.php" title="Indivisible by Zero">Indivisible Numbers</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="division.php" title="All About Division">Divison</a>&nbsp;&nbsp;|&nbsp;&nbsp;
 <a href="skydiving.php" title="All About Skydiving">Skydiving</a>&nbsp;&nbsp;|&nbsp;&nbsp;
</ul>
```

您可以很容易地在锚标记上进行标准的搜索和替换，而不会遇到任何妨碍您使用 div 这样做的问题，但是这有什么意思呢？本着实践的精神，让我们使用 regex 将锚点包装在`li`标签中。

要选择锚点，请在搜索字段中键入以下内容:`(<a.*>)`
在替换字段中，键入:`<li>$1</li>`

暂时忽略搜索模式中的括号，让我们分解这个模式并讨论它的每一部分。第一块是 

搜索模式中的括号执行一个特殊的功能；它们将单个匹配项分组，供您以后访问。通过添加括号，您告诉 regex 引擎存储匹配结果，因为您以后会需要它们。您可以通过号码访问这些组。

替换模式告诉引擎用开始的`li`标记替换搜索模式，后面是第一个分组中的内容，以及结束的`li`标记。在这个例子中只有一个组(因为只有一组括号)，所以在`li`标签中间的`$1`表示这是您想要使用的组。(有些编辑可能会用`1`代替`$1`。如果`$1`起作用，那么撤销你的替换，尝试另一个变体。)

你可以有多个组，组可以嵌套，你马上就会看到。您将修改刚才用来添加`li`标签的模式，以便创建更健壮的导航。撤消您刚才所做的替换。通常类似于`Ctrl` + `Z`的东西工作得很好，但是如果不行，这里有搜索和替换模式来恢复代码:

在搜索字段中，键入:`<li>(<a.*>)</li>`
在替换字段中，键入:`$1`

## 多重分组

好了，现在让我们将锚标签包装在带有`class`和`id`属性的`li`标签中，以便用于 CSS。为此，您将使用以下内容:

搜索:`(<a.*>(w+).*</a>)`
替换:`<li class="navEntry" id="$2">$1</li>`

在第二个例子的搜索模式中，<a.>匹配锚标签。您要求 regex 引擎查找一个字符串，该字符串以一个大于号开始，后面是字母 *a* ，后面是以小于号结束的一系列零个或多个字符。使用`w+`,您还要求引擎查找不包含任何空白或符号字符且长度大于零的字符序列。`w+`周围的括号表示您想要将匹配作为一个组存储。接下来你补充道。*添加到模式中，以匹配锚点标记结束前可能出现的任何其他字符。结果是，`$1`将拥有匹配的锚点字符串，而`$2`将拥有链接文本的第一个单词。</a.>

分解替换，首先是`li`，一个`class`属性及其值，然后是`id`属性。然而，您没有提供 id 值，而是使用了`$2`。这告诉 regex 引擎您想要使用存储在搜索模式的第二个组中的内容，在本例中是`w+`。然后你打开`li`标签，告诉 regex 引擎你想使用第一个分组(`$1`是整个锚标签)，最后关闭`li`标签。

确定要替换哪些组时要小心。考虑下面这个假设的例子(我用组名而不是模式来说明分组是如何工作的):

```
(group1(group2))(group3)
```

使用上述方法会得到以下结果:

```
$1 = group1group2
$2 = group2
$3 = group3
```

`$1`包含*组 1* 和*组 2* ，因为圆括号将它们都括了起来。即使*组 2* 本身是一个组，也是如此。当然*组 3* 本身就是一个组。

为了完成清理工作，您可以删除行尾的不间断空格实体和管道字符，并用一个空字符串替换它们(在表达式中管道前面需要有一个反斜杠，因为它对引擎有特殊的意义)。

搜索:`&nbsp;&nbsp;|&nbsp;&nbsp;`
将替换字段留空。

您的代码现在看起来应该是这样的——一个漂亮、整洁、结构良好的列表，您可以使用 CSS 来设置样式:

```
<ul id="navigation">
 <li class="navEntry" id="Divebombs"><a href="divebomb.php" title="All About Divebombs">Divebombs</a></li>
 <li class="navEntry" id="Endives"><a href="endives.php" title="All About Endives">Endives</a></li>
 <li class="navEntry" id="Indivisible"><a href="indivisible.php" title="Indivisible by Zero">Indivisible Numbers</a></li>
 <li class="navEntry" id="Divison"><a href="division.php" title="All About Division">Divison</a></li>
 <li class="navEntry" id="Skydiving"><a href="skydiving.php" title="All About Skydiving">Skydiving</a></li>
</ul>
```

## 摘要

感谢您花时间学习更多关于正则表达式的知识，并使用搜索和替换来练习它们。我鼓励那些正在努力理解这些概念的人在他们的编辑器中练习使用搜索和替换，因为它很方便，通常能提供即时的视觉反馈。如有必要，您可以将正在处理的内容拷贝并粘贴到一个空白文件中，并尝试使用它，运行替换和撤销它们，直到您得到您喜欢的内容。

## 分享这篇文章