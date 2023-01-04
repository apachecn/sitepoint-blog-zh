# Sql Server 全文搜索保护第 2 部分:CONTAINS 与 FREETEXT

> 原文：<https://www.sitepoint.com/sql-server-full-text-search-protips-part-2-contains-vs-freetext/>

在本系列的第 1 部分中，我们探索了为 Sql Server 数据库启用全文索引和在特定表上启用全文索引的方法。但是现在我们到了精彩的部分——如何在您的应用程序中使用该索引。

有四个主要的 T-SQL 函数允许与您的全文索引进行交互:CONTAINS、FREETEXT、CONTAINSTABLE 和 FREETEXTTABLE。前两个返回一个布尔值，这意味着您可以在 WHERE 子句中直接使用它们。后两个函数返回一个两列的表——KEY 和 RANK，允许用户管理已排序的搜索。

今天我们将探讨包含和自由文本；CONTAINSTABLE 和 FREETEXTTABLE 将在以后枚举。在任何情况下，要记住的重要一点是，CONTAINS 比 FREETEXT 更具体、更可配置。前者使用它被告知的参数进行搜索，而后者将拆分单词，找到屈折形式并执行其他魔法。显然，FREETEXT 是两者中更昂贵的一个，而且它经常是多余的。

困惑了吗？举些例子怎么样？为此，我们将使用古老的 PUBS 样本数据库。(如果您没有副本，您可以[从微软](http://www.microsoft.com/downloads/details.aspx?FamilyID=06616212-0356-46A0-8DA2-EEBC53A68034&displaylang=en)下载数据库脚本。)它没有任何全文功能设置，但是我们将在它上面运行以下 SQL:

```
 --Enable database for full-text querying
exec sp_fulltext_database 'enable'
--Create catalog
exec sp_fulltext_catalog 'PubsCatalog', 'create'
--Enable titles table for fulltext indexing
exec sp_fulltext_table 'titles', 'create', 'PubsCatalog', 'UPKCL_titleidind'
--Add columns
exec sp_fulltext_column 'titles', 'title', 'add'
exec sp_fulltext_column 'titles', 'notes', 'add'
--start change tracking to force indexing
exec sp_fulltext_table 'titles', 'start_change_tracking' 

```

上面只是为全文索引设置了数据库，并在一些选择列上启用了它。关于设置全文索引的完整解释，请参见[第 1 部分](https://www.sitepoint.com/sql-server-full-text-search-protips-part-1-setup/)。

**包含 vs 自由文本**

CONTAINS 和 FREETEXT 是非常相似的函数。两者都返回一个布尔值，并且都接受两个参数:自由文本索引列名和自由文本搜索项。但是他们的行为完全不同。一个明显的例子是，运行以下查询:

```
 SELECT title_id, title FROM Titles
WHERE CONTAINS(notes, 'recipe')

SELECT title_id, title FROM Titles
WHERE FREETEXT(notes, 'recipe') 

```

请注意，第一条 SELECT 语句返回零行，而第二条 SELECT 语句返回三行。你会问为什么？因为包含是特定的。“备注”字段包含术语“收件人”，但不包含“收件人”。FREETEXT 自动将单词的屈折形式添加到搜索中，从而得到一个更宽松的结果集。

**延伸包含**

你现在可能会说“嗯，包含谓词是非常无用的。”但事实远非如此。有一个完整的自由文本查询语法，允许您使用 CONTAINS 实现一些非常巧妙的搜索。首先，可以有意识地强制它使用 FORMSOF 查找所有单词形式。将上述示例扩展到:

```
 SELECT title_id, title FROM Titles
WHERE CONTAINS(notes, 'FORMSOF(INFLECTIONAL, recipe)') 

```

返回三行结果，就像 FREETEXT 示例一样。另一个有用的技巧是寻找多个术语。假设你想捕捉关于烹饪和/或 recipies 的书籍。您可以将查询修改为:

```
 SELECT title_id, title FROM Titles
WHERE CONTAINS(notes, 'FORMSOF(INFLECTIONAL, recipe) or FORMSOF(INFLECTIONAL, cuisine)') 

```

这个查询应该返回四行，在查询结果列表中添加一本关于烹饪的书。

上面的例子仅仅触及了表面——人们可以用 CONTAINS 搜索子句得到非常非常奇特的[,以便得到特定的和定制的搜索结果集。开发人员可以指定每个搜索项的权重，以获得更具体的结果。或者询问彼此附近的单词。有了合适的解析器，你可以从一个相当开放的搜索框中得到一些非常具体的结果。然而，有一个关键的元素是自由文本所不具备的，那就是对搜索结果的价值进行某种外部排序。输入两个全文表函数—CONTAINSTABLE 和 FREETEXTTABLE。有关使用这些 T-SQL 函数的更多信息，请继续关注本系列的第 3 部分。](http://msdn2.microsoft.com/en-us/library/ms187787.aspx)

## 分享这篇文章