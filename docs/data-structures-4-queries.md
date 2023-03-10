# Coldfusion 数据结构简介第四部分-查询

> 原文：<https://www.sitepoint.com/data-structures-4-queries/>

好吧，这绝对是我最后一次告诉你数据结构。然而，我把一个最有用的数据结构留到了最后。无论您是用 ASP、JSP、PHP 还是 Coldfusion 编程，查询都是 Web 开发的重要部分。有时，它们被称为结果集、记录集，或者在 Coldfusion 的情况下，被称为查询。

在整篇文章中，我们将探讨一些与查询相关的不同主题，包括数据库查询、文件系统查询、手动构建查询、遍历查询，以及如何针对现有查询运行查询。所以，事不宜迟，我们开始吧。

##### 数据库查询

获取查询对象的最常见方式是使用结构化查询语言(SQL)查询数据库。为此，我们使用`CFQUERY`标签来检索我们的结果集。这非常简单，但是您首先需要[使用参考文章中的示例数据库建立一个数据源](http://www.kodefusion.com/article/index.cfm?Coldfusion_Data_Sources&ArticleID=10)。一旦设置好数据源，您所要做的就是使用一个带有一点 SQL 的`CFQUERY`命令:

```
<cfquery name="GetUsers" datasource="MyDSN"> 

  SELECT * 

  FROM Users 

</cfquery>
```

现在，该查询将指示 Coldfusion 访问数据源名称`"MyDSN"`所引用的数据库，并打开表`"Users"`。然后，它将检索该表中的所有行和所有列。现在，我们有了查询对象。是时候探索我们可以利用它的方法了。

##### 使用查询

使用查询最常见的方式是循环查询或输出查询。您可以使用`CFOUTPUT`或`CFLOOP`标签的`QUERY`属性来实现。例如，假设我们想在页面上显示表格中的所有内容。使用 Coldfusion 非常简单。我们要做的就是:

```
<cfoutput query="GetUsers"> 

  #Name#<br> 

</cfoutput>
```

这将遍历查询中的每一行，并在单独的行上显示每个名称。当您使用`QUERY`属性时，可以为`CFOUTPUT`标签指定几个不同的选项。您可以轻松地指定要从哪一行开始，以及要显示多少行。因此，如果我们只想显示第一行，我们可以这样做:

```
<cfoutput query="GetUsers" maxrows="1"> 

  #Name#<br> 

</cfoutput>
```

或者，我们可以从第二行开始:

```
<cfoutput query="GetUsers" startrow="2"> 

  #Name# 

</cfoutput>
```

这两个属性可以一起使用，一次显示查询的一小部分。这使得查询分页变得极其简单，只需要一点点远见和创造力。

现在，`CFOUTPUT`标签可以很好地显示信息，但是如果您只是对查询中的行执行某种逻辑，您可能希望使用`CFLOOP`来获得更好的性能:

```
<cfloop query="GetUsers"> 

  <cfif Name eq "John Doe"> 

    <cfquery name="UpdateUser" datasource="MyDSN"> 

  UPDATE Users SET 

  State = 'TX' 

  WHERE ID = #ID# 

    </cfquery> 

  </cfif> 

</cfloop>
```

这将循环查询并将 John Doe 移动到 Texas，但将其他人留在原地。

现在，当您运行查询时，Coldfusion 会在查询中附加几个您可以访问的变量。首先是 RecordCount 变量。这将为您提供查询中所有记录的计数。您可以在逻辑中使用它，例如:

```
<cfif GetUsers.RecordCount gt 0> 

  <!--- Do some logic here with your users query ---> 

</cfif>
```

还有一个 CurrentRow 变量。CurrentRow 非常方便，在大多数情况下，它让您不必创建自己的计数器。例如，假设我在一个表中输出我的用户，我想改变行的颜色以提高可读性。我通常是这样做的:

```
<table> 

  <cfoutput query="GetUsers"> 

    <cfset bgcolor = "##FFFFF"> 

    <cfif CurrentRow MOD 2> 

        <cfset bgcolor = "##CCCCCC"> 

    </cfif> 

    <tr bgcolor="#bgcolor#"> 

      <td>#Name#</td> 

      <td>#State#</td> 

    </tr> 

  </cfoutput> 

</table>
```

这里，我们在每次迭代循环时设置变量，使背景颜色为白色。然后，如果当前行除以 2 的模数为 1，我们将背景颜色设置为灰色。第一次通过循环时，`CurrentRow`将等于 1，所以背景色将是灰色。

您可能会发现另一个有用的内置变量是`ColumnList`变量。这是给定查询返回的所有列的逗号分隔列表。因此，我可以运行一个查询并动态输出一个包含如下列的表:

```
<table border="1"> 

  <tr> 

    <cfloop list="#GetUsers.ColumnName#" index="column"> 

      <cfoutput><th>#column#</th></cfoutput> 

    </cfloop> 

  </tr> 

  <cfoutput query="GetUsers"> 

    <tr> 

      <cfloop list="#GetUsers.ColumnName#" index="column"> 

        <td>#Evaluate(column)#</td> 

      </cfloop> 

    </tr> 

  </cfoutput> 

</table>
```

这将为您提供一个漂亮、整洁的小表，其中列名出现在表的顶部，数据显示在相应的列中。令人惊叹的是，您可以使用它来显示任何您想要的查询。只需更改查询名，就可以了。

这些是 Coldfusion 的查询对象的一些最常见的用法。你可能会发现自己大部分时间都在使用上述方法之一。但是现在，让我们看看获取和使用查询对象的其他几种方法。

##### 目录列表和查询对象

这是一个很短的部分，但仍然很有价值。我遇到的每一种编程语言都有一些访问文件系统的方法。这包括创建、写入、删除、移动、复制和读取文件，以及创建、删除、重命名和列出目录。

嗯，当我们想在 Coldfusion 中获得一个目录列表时，我们不必太费力就能完成这项工作！首先，我们简单地得到一个目录列表:

```
<cfdirectory action="list" directory="#GetDirectoryFromPath(GetTemplatePath())#" name="CurrentDir">
```

这为我们提供了当前模板目录中所有文件的列表。name 属性指定将返回的查询对象的名称。因此，我们可以简单地通过循环查询来输出目录列表:

```
<cfoutput query="CurrentDir"> 

  #Name#<br> 

</cfoutput>
```

现在，这个查询对象包含几个列。如果你愿意，你可以用`ColumnList`变量来访问它们，但是我会告诉你它们是什么列。

*   名字
*   大小
*   类型
*   DateLastModified
*   属性
*   模式(仅适用于*IX 系统，即“755”、“466”等。)

还有另一种方法可以获得在应用程序中使用的查询对象。但是，您不必访问文件系统或数据库来获取查询对象。如果你愿意，你可以自己造！

##### 手动构建查询

如果您真的想手动构建一个查询，您可以使用几个函数的组合来实现。首先，我们需要讨论 Coldfusion 中内置的一些用于操作查询的功能。

首先，我们要创建一个查询对象。这可以通过`QueryNew()`功能轻松实现。该函数采用逗号分隔的列列表，这些列需要包含在查询中。

```
<cfset myQuery = QueryNew("id, name, username, password")>
```

现在，我们有一个空查询。它列出了函数参数中的列，但没有列出任何行。没有数据的查询对我们来说没有多大用处，所以我们将使用`QuerySetCell()`函数添加一些数据。该函数接受查询的名称、列的名称、列的值以及要插入该信息的行号。我们可以像这样添加我们的第一个用户:

```
<cfset temp = QuerySetCell(myQuery, "id", "1", 1)>  

<cfset temp = QuerySetCell(myQuery, "name", "John Doe", 1)>  

<cfset temp = QuerySetCell(myQuery, "username", "JDoe", 1)>  

<cfset temp = QuerySetCell(myQuery, "password", "test", 1)>
```

之后，我们可以将查询输出到屏幕上，并看到它确实是一个包含我们输入的数据的查询对象。

```
<cfoutput query="myQuery">  

  #id# : #name# : #username# : #password#<br>  

</cfoutput>
```

现在，您可能认为创建自己的查询没有多大意义，事实上，在大多数情况下，您不需要这样做。但是，这里有一个场景，您可能会发现此功能的一些用途。我不会为这种特殊的情况提供任何代码，因为它实际上相当复杂，但是我相信您可以想出各种方法来应用这种技术。

想象一下，你正在为一个通过网络服务提供产品流的代销商建立一个在线商店([想到一个非常大的代销商](http://www.amazon.com))。当您调用 Web 服务时，会向您返回一个 XML 文档。当 XML 文档被解析后，它们作为嵌套在数组中的结构返回，数组嵌套在结构中，等等，等等。

使用上述方法，我们可以创建一个比一系列复杂的结构和数组更容易操作的查询对象。事实上，我只是碰巧找到了[这个教程](http://tutorial195.easycfm.com/)，它将向你展示如何完成这个特定的任务(它没有使用亚马逊的 Web 服务，但概念是相同的，并且易于移植)。

您还可以使用`QueryAddColumn()`函数向查询中添加一列。对于此函数，您可以指定查询名称、列名称以及包含要用来填充列的数据的数组的名称。为了证明这确实发生在现实生活中，看看[这个论坛的帖子](http://www.sitepointforums.com/showthread.php?t=145592)。这是一个真实的情况，其中`QueryAddColumn()`函数可以派上用场(忽略其中的任何语法错误。它是在没有测试的情况下匆忙完成的……)。所以，在现实生活中，你肯定需要用到这些函数。

##### 查询查询

有时，您可能需要从数据库中提取大量详细数据，然后根据这些详细信息检索汇总数据。在这种情况下，Coldfusion 使用查询作为数据源的能力非常方便。现在，正如论坛成员 mrhatch 在之前引用的论坛帖子中向我指出的那样，这个功能被称为`CFSQL`(我之前只是称之为查询的查询)。

让我们来看看这可能有用的情况。作为程序员，我们经常发现自己在创建报告。是的，我们讨厌这样做，但如果我们想付账，这是一种不可避免的罪恶。在许多情况下，用户希望能够查看汇总数据和详细信息。在这种情况下，我们有几个选择。

1.  我们可以得到一个包含细节的查询，在输出查询时，我们可以使用细节查询中包含的标识符从数据库中提取聚合数据。

3.  我们可以输出查询并手动编译我们的聚合结果，在最终输出到屏幕之前执行任何需要的计算。

5.  我们可以使用`CFSQL`来避免上述两种情况，同时检索所需的摘要信息。

对于我们的教程，我们将使用第三个选项，因为它在这里演示了这一点。我们假设我们有一个包含以下数据的查询:

![1286_coldfusion](img/9ef8d9ac8201a849f44004b54229f61e.png)

这个数据在一个名为`GetDetails`的查询中，它显示了我们公司的销售人员、他们完成的订单以及订单完成的日期和客户的详细列表。我们希望看到的是一个更漂亮的版本，其中每个销售人员的名字是一个标题，在标题下面，显示他们的销售列表。然后，在每个部分的底部，我们希望看到销售人员的总销售额。

*挑战*:尝试只使用`QueryNew`、`QueryAddRow`和`QuerySetCell`函数创建上面的查询。这样，您就不必创建一个数据库、一个数据源和一个表来执行其他示例。

让我们从输出查询开始。这将为我们提供销售人员表现的详细列表:

```
<cfset curUserID = GetDetails.UserID>  

<cfoutput query="GetDetails">  

   <cfif CurrentRow lt RecordCount>  

      <cfset nextUser = GetDetails.UserID[CurrentRow + 1]>  

   <cfelse>  

      <cfset nextUser = "">  

   </cfif>  

   <cfif curUserID neq UserID or CurrentRow eq 1>  

      <hr>  

      #UserID# #UserName# (#FullName#) : <br>  

   </cfif>  

   <blockquote>  

      #OrderID# - #CustomerName# was sent on #DateFormat(SentDate, "mm/dd/yyyy")# #TimeFormat(SentDate, "hh:mm:ss tt")#<br>  

   </blockquote>  

   <cfif NextUserID neq UserID>  

      <cfquery name="GetCount" dbtype="query">  

         SELECT Count(OrderID) AS OrderCount  

         FROM GetDetails  

         WHERE UserID = #UserID#  

      </cfquery>  

      <blockquote>  

         <strong>User Total: #GetCount.OrderCount#</strong>  

      </blockquote>  

   </cfif>  

   <cfset curUserID = UserID>  

</cfoutput>
```

好了，这里有很多代码要介绍，所以让我们开始吧。首先，我们只想输出每个销售人员的姓名和 ID 一次。因此，我们必须跟踪当前用户 ID 是什么，以及下一个用户 ID 是什么。我们用`curUserID`变量跟踪当前用户。然后我们一头扎进我们的查询。

第一个`CFIF`语句查看我们是否在查询中的最后一条记录上。如果是的话，我们的下一个用户就是一个空字符串。如果不是，我们将把`UserID`列视为一个数组，并获取下一行中`UserID`列的值。

现在，我们想知道是否该显示用户名了。我们知道，如果这是查询中的第一行，我们需要。但是，如果`curUserID`变量与`UserID`变量(直接来自查询)不同，这意味着我们已经前进到另一个用户的记录。在这一点上，我应该强调根据标识符对列进行排序的重要性，在本例中是`UserID`。如果我们在另一个用户的记录上，我们输出那个人的名字、用户名和用户 ID。

现在，我们只显示订单 ID、客户名称和订单发送日期。请注意，无论我们在查询中处于什么位置，都会显示这些数据。

接下来，我们进入有趣的部分:我们查看这是否是该用户的最后一行。如果是，我们使用魔法`CFSQL`。注意这个查询有几个奇怪的地方。首先，我们不指定“datasource”属性；我们指定“dbtype”属性，并为其分配“query”值。第二，我们不是从表中选择；我们从查询中选择。但是，仔细想想，表和查询之间并没有太大的区别。查询的结构与表相同，有行和列。

现在，我们可以限制来自`CFSQL`查询的记录集，就像我们可以限制来自`MySQL`数据库或`SQL`服务器数据库的记录集一样。我们使用 WHERE 子句来指定我们只想获取当前用户的记录。然后，我们输出查询的记录计数。最后，我们将查询中的`curUserID`变量设置为当前的`UserID`。

在使用 Coldfusion 5 时，我个人曾在`CFSQL`查询中结合使用 COUNT 等聚合函数和 WHERE 子句得到不稳定的结果。Coldfusion MX 似乎已经纠正了这个问题，因为我在`CFMX 6.1`上测试了所有这些代码。如果您使用 Coldfusion 5，您可以从查询中选择所有或单个字段，并使用`(QueryName)`。`RecordCount`变量来计算这个用户有多少销售额，或者任何你想要计算的东西。

##### 结论

查询不仅仅是从数据库中得到的结果。在 Coldfusion 中，查询提供了显示和操作数据的非常简单的方法。您可以将其他数据结构(如 XML 文档)转换成查询，并以更简单的方式显示它们。您可以使用`CFSQL`从详细的查询中收集聚合数据，而不需要额外的数据库点击。通过将查询视为包含一系列数组的结构，可以访问查询中的特定行和列。您可以使用附加到每个查询的`ColumnList`变量来显示查询转储。您可以获得目录和文件的列表，并像查询一样访问它。

我们对 Coldfusion 数据结构系列的介绍到此结束。当然，有各种各样的数据结构可以被有创造力的开发人员很好地利用，我希望这一系列文章已经帮助您了解了 Coldfusion 可以被如此轻松地操作以应对当今业务挑战的一些方法。

## 分享这篇文章