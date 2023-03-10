# ColdFusion 教程第三部分

> 原文：<https://www.sitepoint.com/cold-fusion-tutorial-iii/>

 ****##### 简介:** 

欢迎阅读本文的最后一章。如果你已经走了这么远，你应该已经牢牢掌握了如何使用 ColdFusion 来创建一个动态站点。在这一部分中，我将介绍在< CFQUERY >标签中可以找到的代码，以及更高级的 SQL。

##### 1.添加数据

要向数据库中添加数据，您将使用 INSERT SQL 语句。

```
Figure 1\. The INSERT Statement

INSERT INTO Friends(Name, Sex, FavMovie)

VALUES (Chris, Male, Braveheart)
```

它是如何工作的，您指定想要插入的表，在我们的例子中是“朋友”，然后是括号中的列。在下一行中，您以大致相同的方式指定值。值的排列顺序必须与列的排列顺序相匹配。您还可以使这个 insert 语句成为动态的。

```
Figure 2\. A Dynamic INSERT Statement

INSERT INTO Friends(Name, Sex, FavMovie)

VALUES (#name#, #sex#, #movie#)
```

如本文第一部分所示，SQL 语句的任何方面都可以替换为表单输入，INSERT 语句也是如此。大多数数据库驱动的网站都有一个管理部分，使用像这样的动态语句来添加新内容。

##### 2.更改数据

要修改现有数据，必须使用 UPDATE 语句。

```
Figure 3\. The UPDATE Statement

UPDATE Friends

SET Age = '21'

WHERE Name like "Chris"
```

其工作原理是，首先指定要更新的表，在我们的例子中是“Friends ”,然后使用 SET 关键字选择值。如果你想更新多个值，你可以用逗号分隔，例如 SET Age = '21 '，FavMovie = 'The Patriot '。最后，我们包括 where 子句，如果没有它，所有行都将被更新。UPDATE 语句非常有用，例如在我们的朋友数据库中，当某人过生日时，我们会用它来更改他的年龄。

##### 3.删除数据

删除数据时应该非常小心。一个想法的失误或一个打字错误，你可能有一个空的数据库。

```
Figure 4\. The DELETE Statement

DELETE FROM Friends

WHERE Name = "Tim"
```

假设你和一个朋友闹翻了。你可以使用上面的语句从你的朋友数据库中删除他们的名字。同样，指定的第一件事是表名，后跟可选的 WHERE 语句。但是请记住，虽然 where 语句是可选的，但是如果没有它，表中的所有内容都将被删除。

##### 4.独特的关键字

有时，当使用数据库时，您不希望返回重复的值。例如，当使用二手车数据库时，我想在网站上动态生成一个车辆制造的选择表单。如果我没有使用 distinct 关键字，选择列表会很长，数据库中的每辆车都会有一个选项。在我的 SELECT 语句中使用 distinct 关键字将输出限制为数据库中的每个 distinct make。

```
Figure 5\. The DISTINCT Keyword

Select DISTINCT Make

FROM Used
```

如您所见，它看起来就像一个常规的 SELECT 语句，只是添加了 DISTINCT 关键字来消除重复值。

##### 5.列别名

有时您可能希望在引用您的列时使用别名。别名只是您为一个列指定的名称，以便于引用，它不会改变您的数据库，它只是改变 CF 解释器如何看待该列。

```
Figure 6\. Aliases

<CFQUERY DATASOURCE = "Friends" name = "Query1">

Select DISTINCT FavMovie as Movie

From Friends

</CFQUERY>

.

.

.

<CFOUTPUT QUERY = "Query1">

#Movie#

</CFOUTPUT>
```

上面所做的是返回你朋友最喜欢的电影的列表。它与普通 select 语句的唯一区别是，在页面中，我们可以将 FavMovie 列称为 Movie，因为我们建立了别名。

##### 6.聚合函数

聚合函数用于汇总查询结果。最常见的用途是显示搜索返回的结果数量。为此，您必须使用 COUNT 函数。

```
Figure 7\. Count Function

Select COUNT(*) as Friends

From Friends 
```

上面会返回你拥有的好友数量。注意 SQL 中的星号，这是因为我们想简单地计算记录的总数。星号可以替换为列名，以便计算特定列中的记录数。

当然，还有更多聚合函数，它们都以相同的方式使用，如下所示:

count()–计算结果的数量。
Sum()–计算返回值的总和。
Avg()–计算返回值的平均值。
Min()–按字母顺序计算最小值、最早日期或第一个条目。
Max()–按字母顺序计算最大值、最新日期或最后一个条目。

##### 7.排序结果

当您运行 select 语句时，结果将按照它们在表中的输入顺序返回。这并不总是您想要的，有时您可能想要按照一组标准对结果进行排序。为此，可以使用 Order by 子句。

```
Figure 8\. Ordering Results

Select Firstname, Lastname

From Friends

Order by Lastname
```

上面所做的是返回你的朋友的名字列表，按姓氏的字母顺序排列。如果你想从 Z 开始，一直到 A，你可以把它改成按姓氏 DESC 排序。DESC 是降序的缩写，它将颠倒记录的排列顺序。

但是，如果您希望按多列排序，该怎么办呢？让我们假设你有一个叫约翰·史密斯的朋友和一个叫彼得·史密斯的朋友。他们将按姓氏排列在正确的位置，但约翰是否在彼得之上取决于你将他们输入数据库的顺序。

要解决这个问题，只需在 Order 子句中传递两个列名。

```
Figure 9\. Ordering by Two Columns

Select Firstname, Lastname

From Friends

Order by Lastname, Firstname
```

##### 8.极限结果

通常情况下，希望一次只显示 10 条记录，例如大多数搜索引擎就是这样工作的。为此，您可以向查询标记添加一些参数。

```
Figure 10\. Limiting Results

<CFQUERY DATASOURCE = "Friends" Name = "Query1" Maxrows = "10" Startrow = "0">

..

..

..

</CFQUERY>
```

请注意，起始行设置为 0。请记住，计算机从 0 开始编号，而不是从 1 开始。所以数据库中的第一条记录是 0 号记录。

##### 结论:

如果你已经做到这一步，那么你应该能够很容易地使用 ColdFusion 创建一个数据库驱动的网站。我已经介绍了实现数据库驱动的网站的最常见的期望结果需要什么，但是我仅仅触及了表面。ColdFusion 和关系数据库还有很多内容，我无法在一个简短的教程中介绍。如果您想了解更多信息，我推荐 Ben Forta 开发的 ColdFusion Web 应用程序构建工具包 4.0。毫无疑问，这是我所拥有的最全面的书，它应该能够帮助你所有的 ColdFusion 工作。

## 分享这篇文章**