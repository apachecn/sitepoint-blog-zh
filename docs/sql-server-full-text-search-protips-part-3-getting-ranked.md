# Sql Server 全文搜索保护第 3 部分:排名

> 原文：<https://www.sitepoint.com/sql-server-full-text-search-protips-part-3-getting-ranked/>

在本系列的第 1 部分中，我们探讨了为 Sql Server 数据库启用全文索引和在特定表上启用全文索引的方法。在本系列的[第 2 部分](https://www.sitepoint.com/sql-server-full-text-search-protips-part-2-contains-vs-freetext/)中，我们探索了使用 [CONTAINS](http://msdn2.microsoft.com/en-us/library/ms187787.aspx) 和 [FREETEXT](http://msdn2.microsoft.com/en-us/library/ms176078.aspx) 函数访问这些全文索引的方法。在本系列的最后一部分，我们将研究如何使用 [CONTAINSTABLE](http://msdn2.microsoft.com/en-us/library/ms189760.aspx) 和 [FREETEXTTABLE](http://msdn2.microsoft.com/en-us/library/ms177652.aspx) 函数向调用应用程序提供排序的搜索结果。

如前所述，CONTAINSTABLE 和 FREETEXTTABLE 函数为所提供的搜索词查询所选的全文索引。这些函数与它们的非表兄弟(即 CONTAINS 和 FREETEXT)之间的区别在于，这些函数采用的参数略有不同，最重要的是，它们返回一个值表，而不是一个简单的真/假答案。

这些表函数采用的参数集与它们的表亲稍有不同。这些参数是:

1.  要查询的自由文本索引表。
2.  要查询的表中的列。
3.  搜索词。对于 FREETEXTTABLE，这通常只是您希望查找的术语。对于 CONTAINSTABLE，这个术语可以是第 2 部分中描述的任何术语。
4.  前 N 名结果。可选整数，用于设置要获取的结果数的限制。这个字段非常重要——限制您实际希望处理的结果数量可以大大减轻数据库服务器的负载，尤其是在处理大型结果集时。

这些函数返回的表包含两列:

1.  KEY:它包含索引表中行的主键。显然，但重要的是，它的数据类型与那个表中的主键相同。
2.  RANK:这是一个正整数，表示与您的搜索词匹配的相对强度。这个数字不是一个绝对值——它不是从某个固定值开始，一直到接近零。与自由文本查询的其他结果相比，特定值只是相对的。比较不同全文查询之间的排名值不一定会得到有意义的结果。

现在，这个两个字段的表可能听起来非常简单，但是有了它，您可以使用一个非常简单的技术完成许多非常奇特的技巧——只需将从关键字字段的自由文本函数返回的表内部连接到您想要的结果，然后根据排序效果的 RANK 列进行排序。或者甚至得到你在一些网站搜索引擎上看到的那些整齐的百分比匹配。

对于下面的例子，我们将使用 Microsoft Pubs 数据库，就像我们在本系列的第 2 部分中设置的一样。

首先，让我们研究如何使用 FREETEXTTABLE 获得排名搜索结果:

```
 SELECT 
	ftt.RANK,
	titles.title_id, 
	titles.title
FROM Titles
INNER JOIN 
FREETEXTTABLE(titles, notes, 'recipe cuisine') as ftt
ON
ftt.[KEY]=titles.title_id
ORDER BY ftt.RANK DESC 

```

正如您所看到的，这里没有什么太复杂的地方——只是对 FREETEXTTABLE 的一个简单的内部连接，并根据 FREETEXTTABLE 的等级进行排序。

现在，为了得到百分比，你需要变得更好一点。对于匹配百分比，您需要将排名除以排名最高的值。不幸的是，您不能只通过一个自由文本查询来做到这一点；它需要选择最大值，然后选择所需的结果，并为百分比创建计算字段。困惑了吗？好吧，让我们用 SQL 来看看。

```
 DECLARE @topRank int

set @topRank=(SELECT MAX(RANK) FROM
FREETEXTTABLE(titles, notes, 'recipe cuisine', 1))

SELECT 
	ftt.RANK, 
	(CAST(ftt.RANK as DECIMAL)/@topRank) as matchpercent, 
	titles.title_id, 
	titles.title
FROM Titles
INNER JOIN 
FREETEXTTABLE(titles, notes, 'recipe cuisine') as ftt
ON
ftt.[KEY]=titles.title_id
ORDER BY ftt.RANK DESC 

```

需要注意的是，我在第一次调用 FREETEXTTABLE 时特意使用了可选的 Top_N_Results 参数来限制返回的结果数量。这样做的原因是为了降低数据库服务器上的查询成本。

最后一个关键技巧:正如我在第 2 部分中提到的，人们可以非常喜欢包含和搜索术语。这真的真的适用于 CONTAINSTABLE。例如，使用加权搜索词加上 RANK 列可以为最终用户获得正确的结果。例如，假设我们想要查找关于烹饪的书籍，但特别是烹饪:

```
 SELECT 
	ftt.RANK,
	titles.title_id, 
	titles.title,
	notes
FROM Titles
INNER JOIN 
CONTAINSTABLE
	(
	titles, 
	notes, 
	'ISABOUT("recipe*" weight (.7), "cuisine*" weight (.2))'
	) 
as ftt
ON
ftt.[KEY]=titles.title_id
ORDER BY ftt.RANK DESC 

```

在上面的例子中需要注意的一点是，因为我们使用的是 CONTAINS 类型的查询，所以搜索词(ISABOUT。。。)需要包含其他形式来捕捉复数等等。在这种情况下，我使用“SearchTerm*”结构来告诉全文引擎查找以“SearchTerm”开头的所有术语。

最后，回顾一下:

1.  在第 1 部分中，我们研究了如何创建全文索引以及维护这些索引的注意事项。
2.  在第 2 部分中，我们研究了如何使用 CONTAINS 和 FREETEXT 谓词查询全文索引，以及这些函数之间的区别。
3.  在这一部分中，我们研究了如何使用 CONTAINSTABLE 和 FREETEXTTABLE 函数从自由文本索引中获取排名搜索结果。此外，我们研究了使用自由文本查询的一些限制和解决方法。

我希望你们都喜欢，并请张贴任何意见或问题。最后，如果你喜欢的话，可以随意踢它。

## 分享这篇文章