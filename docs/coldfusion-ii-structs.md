# ColdFusion 中的数据结构简介:结构

> 原文：<https://www.sitepoint.com/coldfusion-ii-structs/>

在 ColdFusion 程序中，结构是组织数据的一种非常有用的方式。但是，即使是最熟练的 ColdFusion 编码人员，也经常会误解、忽略或不完全使用它们。

在本文中，我们将看看什么是结构，以及如何使用它们。我们还将看看一些最常见的结构，许多人经常会忽略这些结构。

##### 什么是结构？

首先，什么是结构(在 ColdFusion 中称为“结构”)？结构是一种存储数据的方式。如果你用 Java 做过编程，你可能会把它比作一个散列表。它也类似于 Perl 中的散列。我们可以在一个结构体中存储一系列相关的数据。每一段数据都有一个名称和一个值，我们可以通过调用结构体并向它传递我们想要的值的名称来检索特定的值。把这个过程想象成类似于在一个变量中存储几个不同名称的变量。

先说个例子。假设我们正在建立一个在线书店，我们希望在一个数据结构中存储关于一本书的信息。现在，通常情况下，我们可能会将数据存储在数据库中，并使用查询来访问和显示它。但是，对于这个例子，我们将使用结构。每本书都有标题、作者、描述、出版年份和 ISBN 号。我们将首先创建一个结构来保存这些数据。

```
<cfset myBook = StructNew()>
```

在这里，我们创建一个名为`myBook`的变量，并告诉 ColdFusion 这将是一个结构。如果我们不明确告诉 ColdFusion 这将是一个结构，它就不能正确工作。现在，让我们将数据添加到结构中:

```
<cfset a = StructInsert(myBook, \"title\", \"All About ColdFusion\", 1)>
```

在我们继续之前，让我们停下来讨论一下这个`StructInsert`函数。该函数的第一个参数是我们要添加一个键的结构的名称。下一个参数是我们想要添加的键的名称。对于这个参数，您可以使用一个变量或一个字符串文字(正如我们在这里所做的)。下一个参数是键值。正如键名一样，在这种情况下，我们可以使用变量或字符串。第四个参数是可选参数，即“允许覆盖”参数。如果这个参数被设置为`1`，它将覆盖这个结构中任何现有的名为“title”的键。如果它被设置为`0`，当你试图添加“标题”到一个已经有一个同名键的结构时，将会抛出一个错误。该参数的默认值为`0`。那么，让我们添加其余的数据:

```
<cfset a = StructInsert(myBook, "author", "David Medlock", 1)> 

<cfset a = StructInsert(myBook, "description", "Information about CF", 1)> 

<cfset a = StructInsert(myBook, "publishyear", "2004", 1)> 

<cfset a = StructInsert(myBook, "ISBN", "ABCD123456", 1)>
```

好的，我们的书包含了我们需要的所有信息。现在，我们想展示我们的书。这很简单。如果您知道您的结构的所有键的名称，您可以很容易地将它们显示如下:

```
<cfoutput> 

  Title: #myBook["title"]#<br /> 

  Author: #myBook["author"]#<br /> 

  Description: #myBook["description"]#<br /> 

  Publish Year: #myBook["publishyear"]#<br /> 

  ISBN: #myBook["ISBN"]# 

</cfoutput>
```

这里，我们简单地转储结构的内容，一次一个键。但是，如果我们不知道一个结构包含哪些键，会发生什么呢？没问题——我们可以使用 ColdFusion 的 loop 标签来输出该结构中的所有键。

```
<cfloop collection="#myBook#" item="key"> 

  <cfoutput> 

    #key#: #myBook[key]#<br /> 

  </cfoutput> 

</cfloop>
```

为了显示结构中的所有键及其值，这就是我们要做的全部工作。但是，您会注意到，这并没有为每个项目输出一个非常漂亮的标签。它只显示键名。接下来让我们解决这个问题。

##### 在数组或其他结构中存储结构

正如我们可以在数组中存储数组一样，我们也可以在结构和数组中存储结构。例如，我们可以创建一个名为 titles 的结构，为数组中的每个键存储一个用户友好的标签。我们将使用与我们在 book struct 中使用的完全相同的键。

```
<cfset titles = StructNew()>  

<cfset a = StructInsert(titles, "title", "Title", 1)>  

<cfset a = StructInsert(titles, "author", "Author", 1)>  

<cfset a = StructInsert(titles, "description", "Book Description", 1)>  

<cfset a = StructInsert(titles, "publishyear", "Publish Year", 1)>  

<cfset a = StructInsert(titles, "isbn", "ISBN Number", 1)>
```

那很容易。现在，我们的数据结构将变得稍微复杂一些。我们将创建一个名为`bookshelf`的结构，并将`myBook`和`titles`放入其中。

```
<cfset bookshelf = StructNew()>  

<cfset a = StructInsert(bookshelf, "names", titles, 1)>  

<cfset a = StructInsert(bookshelf, "values", myBook, 1)>
```

这里，我们简单地将`bookshelf`初始化为一个结构，然后将两个键放入其中。第一个键叫做“`names`”，我们将`titles`结构放入这个键中。第二个键叫做“T3”，我们将“T4”结构放入其中。我们现在有一个包含两个结构的结构，每个结构包含五个键。现在，我想根据键的名称对结构进行排序，所以我要这样做:

```
<cfset keyList = StructKeyList(bookshelf.names)>  

<cfset keyList = ListSort(keyList, "TEXT")>
```

这里我们有一个新的函数，`StructKeyList`，它将一个结构的名称作为参数。然后，它获取所有键的列表，并以逗号分隔的列表形式返回这些键。然后，我们使用`ListSort`函数按字母顺序排列结构键。不过，我确实想指出访问 bookshelf 中包含的结构的方法。如果你曾经使用过任何 C 风格的语言，你会认为这是“点符号”。您甚至可以将这些结构视为对象，并看到您正在访问它们的成员。在这种情况下，names 是 bookshelf 的成员。现在，我们将遍历列表，输出两个结构中的值。

```
<cfloop list="#keyList#" index="key">  

  <cfoutput>  

    #bookshelf.names[key]#: #bookshelf.values[key]#<br />  

  </cfoutput>  

</cfloop>
```

这为我们的书输出了一个漂亮整洁的小清单。但是，我们很少遇到只卖一本书的书店。所以，我们需要记录无限数量的书籍。为了做到这一点，我们将使用我们的`bookshelf`结构，和我们的`myBook`结构；我们还将创建一个名为`myBook2`的新结构和一个名为`myBookArray`的数组。首先，我们将创建`myBook2`:

```
<cfset myBook2 = StructNew()>  

<cfset a = StructInsert(myBook2, "title", "More ColdFusion", 1)>  

<cfset a = StructInsert(myBook2, "author", "David Medlock", 1)>  

<cfset a = StructInsert(myBook2, "description", "More ColdFusion Info", 1)>  

<cfset a = StructInsert(myBook2, "publishyear", "2005", 1)>  

<cfset a = StructInsert(myBook2, "isbn", "321654DCBA", 1)>
```

现在，我们创建一个一维数组来存储我们的书籍。

```
<cfset myBookArray = ArrayNew(1)>
```

接下来，我们将书籍添加到数组中:

```
<cfset myBookArray[1] = myBook>  

<cfset myBookArray[2] = myBook2>
```

现在，我们有一个包含两个结构的数组，每个结构包含五个键。我们将把这些书放在书架上，并按照上一个例子中的方法进行分类:

```
<cfset a = StructInsert(bookshelf, "names", titles, 1)>  

<cfset a = StructInsert(bookshelf, "values", myBookArray, 1)>  

<cfset keyList = StructKeyList(bookshelf.names)>  

<cfset keyList = ListSort(keyList, "TEXT")>
```

现在我们将使用两个循环来显示我们的每本书。首先，我们需要确保两本书都被展示出来。这意味着我们将对存储在`bookshelf.values`中的数组进行循环。然后，我们将遍历键列表，并显示当前数组元素中每个键的标签和值:

```
<cfloop from="1" to="#ArrayLen(bookshelf.values)#" index="i">  

  <cfloop list="#keyList#" index="key">  

    <cfoutput>  

      #bookshelf.names[key]#: #bookshelf.values[i][key]#<br />  

    </cfoutput>  

  </cfloop>  

  <hr>  

</cfloop>
```

注意，我们在外部循环中使用点符号来访问数组长度。在这个循环中，我们遍历数组中的键列表。当我们想要显示标题、作者和其他字段的值时，有趣的部分出现了。我们访问书架结构，然后访问`bookshelf`结构的`values`键(这是一个数组)，然后访问`values`键的索引`i`(这是一个结构)，最后，我们从该结构中获取 key 的值。

你现在彻底糊涂了吗？我第一次不得不长时间地使用结构是一次不寻常的经历。但是，如果您将任务分解成简单的步骤，您会发现使用 ColdFusion 的内置数据结构来增强您的应用程序实际上非常容易。学习 ColdFusion 应用程序设计和编程的这些元素可以很快让您领先于许多其他 CF 开发人员一步，并为您提供完成分配给您的任何任务所需的工具。

##### 内置 coldfusion struts

实际上，ColdFusion 中内置了一些您可能甚至没有意识到的结构。例如，您知道表单、URL 和 CGI 变量范围都是结构吗？没错。只需循环适当的集合，就可以很容易地发现这些结构中的变量。(我知道所有这些变量都出现在 ColdFusion 调试信息中，但有时知道如何在不借助 CF 内置调试的情况下获取这些信息会非常方便。)

让我们看一个简单的 CGI 变量的例子，它为我们提供了应用程序运行环境的基本信息。下面是执行此操作的代码:

```
<cfloop collection="#CGI#" item="key">  

  <cfoutput>#key# = #CGI[key]#<br></cfoutput>  

</cfloop>
```

就是这样！您可以运行同样的代码来获得表单、URL、CGI、客户机、应用程序和会话变量的信息。从 ColdFusion 的角度来看，这些都是结构，因此使用上述方法很容易访问它们。

这就结束了我们对 ColdFusion 中结构的介绍。如您所见，CFML 提供了一些非常强大的组织、跟踪和访问数据的方法。你可能会发现你并不经常使用这些方法，但当你急需解决一个难题时，它们往往是你的救命恩人。我发现当你有需要以非常规方式显示的复杂数据时，结构和数组非常有用。将这些小信息存储在大脑的某个角落，将这篇文章收藏在“ColdFusion Resources”文件夹中(我知道你有一个)，你的开发工具库中就多了一个工具。

## 分享这篇文章