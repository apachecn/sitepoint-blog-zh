# ColdFusion 中的数据结构介绍第三部分–列表

> 原文：<https://www.sitepoint.com/coldfusion-iii-lists/>

在过去的两篇文章中，我们讨论了 ColdFusion 中一些更复杂的数据结构。虽然数组和结构对于非常具体的任务非常有用，但是您可能会发现自己更经常地使用列表。幸运的是，列表在 ColdFusion 中非常容易管理。

对于所有 Java、C++和 Visual Basic 开发人员，我现在要让你们知道，在阅读完这篇文章之后，你们会非常嫉妒 ColdFusion。ColdFusion 通过其内置函数使操作列表变得非常容易。一个优秀的 ColdFusion 开发人员只要有一点创造力，就一定能找到几百万种利用列表的方法。

##### 什么是列表？

首先，让我们用 ColdFusion 术语定义什么是列表。列表只是一个字符串。字符串和列表之间的唯一区别是，列表由一个特定的字符或一组字符分隔。您可以使用任何字符串作为列表，并且可以像操作任何字符串一样操作任何列表。例如，`a,b,c,d,e,f,g`是一个逗号分隔的列表。或者您可以使用`a/b/c/d/e/f/g`——一个正斜杠分隔的列表。ColdFusion 列表的默认分隔符是逗号。另外，请注意`a/b/c`与`a / b / c`并不相同。ColdFusion 不会修剪列表元素中的空白。如果列表包含空格，您可能希望对元素使用 Trim 函数。

##### 行动列表

现在我们知道了 ColdFusion 对列表的定义，我们将开始使用列表。首先，我们试着获取一个列表的长度。

```
<cfset listLength = ListLen("a,b,c,d,e")>
```

```
ListLen will return the value 5 to the variable listLength. That's pretty simple, but here's a challenge for you: what is the value of listLength after the following code is executed?

```
<cfset listLength = ListLen("a,b,,c,,d,e")>
```

如果你猜对了 5，那你就对了。原因是 ColdFusion 忽略了空列表元素。现在，假设我们想要显示列表中的每个元素。这非常简单:

```
<cfloop list="a,b,c,d,e" index="i"> 

    <cfoutput> #i# <br /></cfoutput> 

</cfloop>
```

这段代码在单独的行上显示每个列表元素。现在，如果您在变量中存储了一个列表，当您将它传递给循环的 list 属性时，不要忘记在变量名周围加上井号(`#`)。否则，它将无法正常工作。

```
<cfset list = "a,b,c,d"> 

<!--- this works fine ---> 

<cfloop list="#list#" index="i"> 

    <cfoutput> #i# <br /></cfoutput> 

</cfloop> 

<!--- this outputs "list" ---> 

<cfloop list="list" index="i"> 

    <cfoutput> #i# <br /></cfoutput> 

</cfloop>
```

只是在遍历一个列表时，一定要把变量名用井号括起来。
列表功能
在我们继续讨论更大更好的东西之前，还有一些我们必须简要介绍的列表函数。我们已经讨论过`ListLen`，它返回列表的长度。`ListLen`接受一个必需的参数，即您想要检查长度的列表，以及一个可选的参数，指定您想要使用的分隔符。所以，你可以这样做来找出一个句子中有多少个单词:

```
<cfset words = ListLen("The quick brown fox jumps over the lazy dog.", " ")>
```

请注意，第二个参数指定第一个参数中传递的字符串是一个空格分隔的列表。执行后，`words`变量包含值 9。

另一个有用的功能是`ListContains`。这将在列表中搜索子字符串，并返回一个布尔值，该值指示该子字符串是否作为列表元素找到。

```
<cfset found = ListContains("12,15,186,99,0", "18")>
```

请注意，这将返回 false，因为子字符串“18”不是我们作为第一个参数传递的列表中的元素。请注意，列表中确实有 186 个，但是因为没有完全匹配的元素。如果在 If 语句中使用`contains`操作符，它将返回 true:

```
<cfif "12,15,186,99,0" contains "18">  

    this is true.  

</cfif>
```

```
ListContains will also take an optional third parameter that specifies the delimiter to use.接下来，我们将看看`ListFirst`和`ListLast`函数。他们几乎完全按照你的预期去做。`ListFirst`返回列表的第一个元素，`ListLast`返回最后一个元素。

```
<cfoutput>  

    <!--- displays "The" --->  

#ListFirst("The quick brown fox jumps over the lazy dog.", " ")#  

<!--- displays "dog." --->  

#ListLast("The quick brown fox jumps over the lazy dog.", " ")#  

</cfoutput>
```

函数让我们在列表中搜索一个元素。它返回元素的第一个索引(整数)。请记住，该搜索区分大小写。

```
<cfset first = ListFind("1,2,3,1,5,6", "5")>
```

当我们执行这行代码时，first 的值将是 5。
最后，`ListDeleteAt`将让我们从列表中删除一个元素。我们只需将列表和想要删除的位置传递给它。

```
<cfset newList = ListDeleteAt("1,2,3,4,5", 2)>
```

当我们执行这段代码时，newList 变量包含值“1，3，4，5”，因为我们已经删除了列表的第二个元素 2。
在 HTML 表单中使用列表
我发现 ColdFusion 中列表最常见的用法是与 HTML 表单结合使用。当您创建包含多个同名元素(例如复选框)的表单时，结果将作为列表提交。这里有一个例子:

```
products.cfm:  

<form name="deleteProducts" action="delete.cfm">  

  Product 1: <input type="checkbox" name="productID" value="1"><br>  

  Product 2: <input type="checkbox" name="productID" value="2"><br>  

  Product 3: <input type="checkbox" name="productID" value="3"><br>  

Product 4: <input type="checkbox" name="productID" value="4"><br>  

<input type="Submit" value="Delete">  

</form>
```

现在，如果您选中前三个复选框并单击“删除”，它会将表单变量`productID`作为值为“1，2，3”的列表发送到 delete.cfm 页面。当我们到达删除页面时，我们将简单地遍历列表:

```
delete.cfm:  

<cfif isDefined("form.productID")>  

  <cfloop list="#form.productID#" index="i">  

    <cfquery name="delete" datasource="myDSN">  

      DELETE FROM Products  

      WHERE ProductID = #i#  

    </cfquery>  

  </cfloop>  

</cfif>
```

在这里，我们检查表单变量是否存在。这在提交复选框时是必要的。如果您没有选中任何复选框，就不会提交变量 productID。因此，当它到达 delete.cfm 时，它将不存在，并且当您尝试循环该变量时，将会引发错误。我们遍历产品 id 列表，并对每个产品 id 执行删除查询。
当你在日常编码中遇到各种情况时，你会发现你有很多机会应用这种技术。无论您是从数据库中删除产品还是使用其他 HTML 表单，您都会发现列表非常非常方便。这是一个你应该熟悉的工具。
在我们最后一次查看 ColdFusion 数据结构时，我们将检查 ColdFusion 查询。这些都非常有用，非常容易使用和应用。下次见，编码快乐！

```

## 分享这篇文章

```