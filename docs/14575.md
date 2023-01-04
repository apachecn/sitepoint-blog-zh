# ColdFusion 中的用户定义函数

> 原文：<https://www.sitepoint.com/defined-functions-coldfusion/>

用户定义的函数允许开发者将经常使用的代码保存在可重用的单元中。例如，您可能经常发现自己在检查特定文件的大小或计算数据库表中的记录数。您可能会发现需要确定字符串是大写还是小写，或者将字符串转换为单个字符的数组。

无论您需要什么，用户定义函数(UDF)都可以帮助您实现目标。在本文中，我将向您展示如何使用 cfscript 创建用户定义的函数，以便您的函数与 ColdFusion 5 和 ColdFusion MX 兼容。

##### `cfscript`功能

cfscript 从[开始就存在了，至少版本 4.01](http://www.macromedia.com/support/coldfusion/ts/documents/cfmlhistory.htm) 。它基本上允许开发人员以 C 风格的语法编写 ColdFusion 代码。然而,`cfscript`也有局限性。例如，您不能从`cfscript`内部调用 ColdFusion 标签。你必须使用可用的功能。`cfscript`已经被证明在某些情况下比使用标签提供了一些性能上的好处，并且它经常会导致你使用比你原本可能需要的更少的代码。此外，在 ColdFusion 5 中，您只能通过使用`cfscript`来创建自己的函数。正是在这种背景下，我们将使用`cfscript`来构建我们自己的函数，这些函数可以一直兼容到版本 5。

对于我们的第一个示例，我们将使用我在四月份的 ColdFusion 博客中发布的几个函数，这些函数允许我们判断字符是大写还是小写。然后，我们将添加几个函数来增加脚本的功能，这些功能在将来可能会有用。

##### 大写函数

首先，让我们构建一个函数来告诉我们一个字符是否是大写的。然后我们将构建一个来告诉我们一个字符是否是小写的。下面是该函数的代码:

```
<cfscript> 

   function isUpperCase(character) { 

      if (Asc(character) gte 65 and Asc(character) lte 90) 

         return true; 

      return false; 

   } 

</cfscript>
```

首先，我们打开我们的`cfscript`块。接下来，我们使用 function 关键字创建一个函数。我们给它一个名字，然后在括号中指定它需要的参数。你觉得这个眼熟吗？如果你曾经使用过 JavaScript，应该可以。这正是 JavaScript 函数的声明方式(除了`cfscript`块)。但是，关于 ColdFusion UDFs，有一些事情需要注意:

*   不能重载或重新声明函数。一旦创建了函数，就不能再创建同名的函数。
*   您不能为 ColdFusion UDF 中的参数指定默认值。
*   不能为 UDF 指定可选参数。声明中使用的每个参数都必须在函数调用中传递。

既然我已经告诉了您不能用 UDF 做什么，下面是您可以做的事情:您可以指定未声明的参数。例如，我只为上面的函数声明了一个参数，但是我可以传入三个、四个或更多的参数。因此，一个普通的函数调用应该是这样的:

```
<cfoutput> 

   #isUpperCase("A")# 

</cfoutput>
```

这将显示值“真”。注意，如果我传入字符串“Aaaa ”,它仍然是真的，因为`Asc`函数使用传入字符串的第一个字符，忽略其余的字符。

然而，如果我用几个额外的参数调用它，它仍然可以工作:

```
<cfoutput> 

   #isUpperCase("A", "a", "b")# 

</cfoutput>
```

这将仍然显示“真”。它不使用那些额外的参数。但是如果我们想使用传递给函数的附加参数呢？你会问，我们如何接近他们？真的很简单。在函数内部，我们有一个名为“arguments”的数组。因此，如果我们想使用第二个和第三个参数，我们可以简单地通过这个数组访问它们。

```
<cfscript> 

   function argsTest() { 

      for (i = 1; i lte ArrayLen(arguments); i = i + 1) { 

         WriteOutput(arguments[i] & "<br />"); 

      } 

   } 

</cfscript>
```

这将写出我们传入的所有参数。我们这样调用这个函数:

```
<cfset a = argsTest("I", "am", "ready", "to", "code")>
```

使用上面的代码，我们将把每个参数打印在单独的一行上。注意我们函数的一些事情:

*   我们使用`"lte"`进行比较。这是因为，尽管它是不同的语法，它仍然是 ColdFusion。我们必须使用普通的 CF 运算符进行比较和逻辑运算。
*   我们用`i = i + 1`代替`i++`。这是因为，我们仍然在使用 ColdFusion，它不支持`++`操作符。
*   我们使用`WriteOutput()`函数向页面显示变量和其他信息。这有点像在 JavaScript 函数中使用`document.write()`。
*   就像在我们的大写函数中一样，注意我们可以使用普通的 ColdFusion 函数，比如`Asc(), Chr(), Len(), ArrayLen(), ArrayNew()`等。，也是。
*   注意我们大写函数中的 return 语句。在这个函数中，我们返回一个简单的布尔值，然后可以将它赋给我们选择的任何变量。但是，我们也可以返回复杂的值，比如结构和数组。我们马上就会看到这一点。

##### 小写函数

现在，让我们编写小写函数。小写字符的 ASCII 值是 97 到 122，所以我们基本上复制粘贴大写函数并修改这两个值。

```
<cfscript> 

   function isLowerCase(character) { 

      if (Asc(character) lte 97 and Asc(character) gte 122) 

         return true; 

      return false; 

   } 

</cfscript>
```

这些功能就够了。现在，让我们构建一个函数来决定它是大写还是小写，并提醒我们这一点。我们将根据传递的字符返回值“upper”、“lower”或“false”。

```
<cfscript> 

   function isUpperOrLowerCase(character) { 

      if (Asc(character) gte 65 and Asc(character) lte 90) 

         return "upper"; 

      else if (Asc(character) gte 97 and Asc(character) lte 122) 

         return "lower"; 

      return false; 

   } 

</cfscript>
```

我们可以这样使用函数:

```
<cfif not isUpperOrLowerCase("a")> 

   This is not a character. 

<cfelseif isUpperOrLowerCase("a") eq "upper"> 

   This is an uppercase character. 

<cfelse> 

   This is a lowercase character. 

</cfif>
```

这演示了从 UDF 返回除布尔值之外的数据类型的能力。现在，让我们返回更复杂的东西:一个数组。我们将创建一个函数，将字符串转换为字符数组。

```
<cfscript> 

   function toCharacterArray(str) { 

      var CharArray = ArrayNew(1); 

      for (i = 1; i lte Len(str); i = i + 1) { 

         CharArray[i] = Mid(str, i, 1); 

      } 

      return CharArray; 

   } 

</cfscript>
```

它接受一个字符串作为参数，创建一个新的一维数组，从 1 开始循环到字符串的长度，取出每个字符，将其填充到数组的当前索引处，由变量`i`表示。然后我们返回字符数组。

```
<cfset a = toCharacterArray('This is my array")> 

<cfdump var="#a#">
```

上面的脚本显示了一个整洁的小表，其中显示了数组中的每个值。在 ColdFusion 中创建用户定义的函数真的很容易。现在您已经知道如何使用 cfscript 在 ColdFusion 中创建用户定义的函数，下面是您的任务:

浏览当前的项目或您之前在 ColdFusion 中构建的网站，并确定该项目的哪些部分可以内置到功能中。您可能在几个地方使用了相同的功能，或者您可以通过将代码从模板体中移除并放入模板中来清理和简化代码。然后，构建您的函数，将它们存储在一个文件中(如果没有太多的话),并简单地将它们包含在您需要的地方。您还可以在您的站点中创建一个名为“udfs”的目录，或者类似的目录，并将每个函数放在一个同名的文件中。然后，您只需包含您需要的功能。

如果你想要免费的用户定义函数，你可能想看看 [CFLib](http://www.cflib.org/) 你会发现那里有大量非常方便的函数。

## 分享这篇文章