# “你想做什么？”

> 原文：<https://www.sitepoint.com/you-want-to-do-what/>

好的，我知道这不是我为普通开发者准备的 CFC 的第二部分，但是我想和你分享一些我今天做的很酷的事情。我有一个需要额外插入一行的位置查询。现在这听起来像是一件容易的事情，但是有一个陷阱，不总是有一个陷阱。我必须让查询按字母顺序对位置进行排序，但是我需要插入的新行必须在第一行。所以我的任务是:

1.  查询数据库，获得所有位置及其各自 ID 的列表，并按字母顺序排序
2.  在同一查询的第一行中插入一个 ID 为-99 的公司办公室列表

所以我的疑问是:

```
<cfquery name="getLocations" datasource="MyDSN">
     SELECT franchiseeName + ' (' + city + ', ' + stateProvince + ')' AS lookupdisplay, franchiseeid AS lookupkey
     FROM franchisee
     ORDER BY lookupdisplay
</cfquery>
```

这将返回一个很好的查询结果集，其中包含两列 **lookupDisplay** ，其中包含我的位置名称/州，以及 **lookupKey** ，其中包含这个特定位置的 ID。那么，如何在这个查询中获得另一行呢？添加一行很简单，我们可以简单地做:

`<cfset QueryAddRow(getLocations,1)>`

QueryAddRow 函数将在我的结果集 **getLocations** 中插入一个空白行(将 1 改为其他数字会插入该数量的行)。不需要。为了填充此行，我只需执行以下操作:

`<cfset QuerySetCell(getLocations, 'lookupDisplay',Corporate Posting (Waco, TX)')>
<cfset QuerySetCell(getLocations, 'lookupkey','-99')>`

使用 [QuerySetCell 函数](http://livedocs.macromedia.com/coldfusion/7/htmldocs/00000601.htm)我可以填充我的空行，但是这有一个问题。该行在我的查询的底部，我需要它在顶部。我的第一个想法是简单地做一个 A 查询的查询，然后重新排序所有的东西，但是我的附加值以 C 开始，我知道我有以 A 和 B 开始的位置(准确地说总共有 8 个)。那么开发者该怎么做呢？好吧，我开始考虑那些容易混进去的东西(特别是数据结构)。对我来说，数组是最容易排序和操作的数据结构，但是查询结果不是简单的数组。它们更像是每个位置都有结构的数组；每一行都是一个位置数组，每一列构成了这个结构。

好的，我需要写一些代码，这些代码将把我的查询转换成一个数组结构。简单对吗？当然是多亏了 www.cflib.org！如果你从未去过 CFlib，你需要现在就去那里，那里太棒了！这是一个为一些简单的和一些复杂的事情提供大量功能的网站。在[数据操作库中快速搜索](http://www.cflib.org/library.cfm?ID=5)得到了我需要的两个函数 [ArrayOfStructuresToQuery](http://www.cflib.org/udf.cfm?ID=287) (作者 David Crawford)和 [QueryToArrayOfStructures](http://www.cflib.org/udf.cfm?ID=10) (作者 Nathan Dintenfass)。

有了这两个函数，我可以把我的查询变成一个邪恶的数组(你应该试着把它转储出来，给自己取一个峰值！)然后我可以使用一些代码将我的最后一条记录冒泡排序到顶部。因此，在我的查询被创建并且我的记录被添加到其中之后，我的下一步是将我的查询转换成如下的结构数组:

`<cfset getLocationsArray = QueryToArrayOfStructures(getLocations)>`

然后，我可以对这个数组执行一个 CFLoop，并使用 ArraySwap 函数来创建我的冒泡排序，如下所示:

```
<cfloop index="j" from="1" to="#ArrayLen(getLocationsArray)#">
     <cfset ArraySwap(getLocationsArray,j,ArrayLen(getLocationsArray))>
</cfloop>
```

让我来为你分析最后三行。第一行是我的 CFLoop 标记，特别是它是一个索引循环，你们这些程序员知道这是 FOR 循环，但在 ColdFusion 中它只是一个索引循环。我将我的**索引**设置为 j，这将是一个变量，我可以在我的 CFLoop 标签中使用它。来自的**属性被设置为 1，记住在 ColdFusion 数组中总是从 1 开始，而大多数其他语言从 0 开始。**到**属性实际上是我的 ArrayLen()函数的结果，它是在 QueryToArrayOfStructures 函数的结果中传递的，这个 ArrayLen 函数将返回一个等于数组位置数的数值，以确保我不会在数组之外循环。如果我只是将**到**属性设置为 50，但只有 45 个数组位置，ColdFusion 将生成 ArrayIndexOutOfBounds 错误，基本上是告诉我我试图引用不存在的东西。**

好的，第二行使用 ArraySwap 函数来交换我当前所在的位置，也就是 j 变量，和数组底部的位置，从而使底部的位置上升到顶部。迷茫？是的，我也是，所以让我们好好谈谈。

假设我的数组只有 5 个位置，看起来像这样:

getLocationsArray[1] =结构 A
getLocationsArray[2] =结构 B
getLocationsArray[3] =结构 C
getLocationsArray[4] =结构 D
getLocationsArray[5] =结构 E

现在，这里的目标是让 Struct E 位于顶部，然后让其他所有东西都位于 A–d。因此，在我的 CFLoop 中的第一次循环结束时，我的数组看起来将如下所示:

getLocationsArray[1]= Struct E
getLocationsArray[2]= Struct B
getLocationsArray[3]= Struct C
getLocationsArray[4]= Struct D
getLocationsArray[5]= Struct A

传递#2 将把位置 2 中的内容与位置 5 中的内容进行交换

getLocationsArray[1]= Struct E
getLocationsArray[2]= Struct A
getLocationsArray[3]= Struct C
getLocationsArray[4]= Struct D
getLocationsArray[5]= Struct B

通过#3 将位置 3 与位置 5 交换

getLocationsArray[1]= Struct E
getLocationsArray[2]= Struct A
getLocationsArray[3]= Struct B
getLocationsArray[4]= Struct D
getLocationsArray[5]= Struct C

通过#4 将位置 4 与位置 5 交换

getLocationsArray[1]= Struct E
getLocationsArray[2]= Struct A
getLocationsArray[3]= Struct B
getLocationsArray[4]= Struct C
getLocationsArray[5]= Struct D

现在这一点是好的，但是我们现在编码的循环将在位置 5 和位置 5 之间进行另一次交换，结果什么也没有发生。

所以现在你可以看到我得到了我想要的。最棒的是，如果我需要它出现在位置 3，我所要做的就是将 CFLoop 中的 from 值改为 3，同样的事情也会发生，但它将从 3 工作到 5。

好了，现在一切都如我所愿了，我只需要用下面的方法将它转换回查询结果集:

`<cfset getLocations = ArrayOfStructuresToQuery(getLocationsArray)>`

使用 ArrayOfStructuresToQuery 函数会将我带回到我的查询，现在该查询中的数据完全符合我的要求。

不会太寒酸吧？你会如何处理这个问题？

## 分享这篇文章