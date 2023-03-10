# Sql Server 全文搜索保护第 1 部分:安装

> 原文：<https://www.sitepoint.com/sql-server-full-text-search-protips-part-1-setup/>

正如我之前提到的那样，Sql Server 的全文搜索可以让开发人员轻松地创建一些令人不安的功能。而且，与许多其他全文实现不同，它并不局限于纯文本字段。通过适当的设置，它还可以在二进制字段中进行搜索。不用说，让全文索引工作并利用它们有几个好处。在这篇文章中，我将告诉你如何得到全特克斯

**入门**

首先，您需要一份 Sql Server 2000 或 2005 标准版。MSDE 或 Sql Express 没有全文功能。为了启用全文搜索，您必须做一些事情。

*   对数据库启用全文搜索。
*   创建全文目录。
*   对数据中的特定列启用全文搜索。

为此，我们可以运行一些系统存储过程。我应该注意的是，这些在 2005 年因为支持 DDL 语句而被取消了，但是它们仍然可以工作，并且更容易理解。以下是在数据库中创建全文索引所需的 SQL 示例:

```
 exec sp_fulltext_database 'enable'
exec sp_fulltext_catalog 'Catalog_Name', 'create' 

```

**表格设计考虑事项**

一旦有了全文索引，就可以开始索引列了。但是我们不要想得太多。首先，使用全文索引需要表上有一个唯一的键。其次，对于任何使用全文索引的表，都应该有一个时间戳列。这是一个小的二进制列，每当行发生变化时，它都会自动更新。这是必要的，因为它充当索引引擎的标志，允许它对表进行增量索引。

对二进制字段(如存储在数据库中的 word 文档)使用全文索引还有另外一个注意事项。需要考虑的是，您必须有一个带有文件扩展名的文本字段(例如，word 的“doc”)。这是因为索引需要知道它正在处理什么类型的文件，以便解析文件和找到数据。现成的，Sql Server 可以索引文本文件，html 文件和 Word 文档。但是可以使用 [IFilters](http://msdn2.microsoft.com/en-us/library/ms691105.aspx) 进行扩展。例如， [Adobe 提供了一个 PDF IFilter](http://www.adobe.com/support/downloads/detail.jsp?ftpID=2611) 供您使用。

**启用表的全文索引&列**

假设我们有一个相当简单的表，我们希望索引，文档附件。它有六个字段:

*   Id(整数标识不为空，主键)
*   文件名(varchar(255)不为空)
*   file description(varchar(1000)NULL)
*   FileExtension (varchar(10)不为空)
*   保存戳(时间戳)
*   档案资料(影像)

为了允许对表进行全文查询，必须首先启用表的全文索引，然后在特定列上启用全文索引。如果我们希望在 FileDescription 和 FileData 字段上创建全文索引，我们将使用下面的 T-SQL:

```
 exec sp_fulltext_table @tabname='document_attachments', @action='create', @ftcat=[CatalogName], @keyname=[Name of Primary Key index]
exec sp_fulltext_column @tabname='document_attachments', @colname='FileName', @action='add'
exec sp_fulltext_column @tabname='document_attachments', @colname='FileData', @action='add', @type_colname='FileExtension' 

```

**步进注意事项**

现在，在使用任何全文目录之前，必须对其进行索引。这里有两种不同的情况。对于“普通字段”，即不是图像或文本的字段，Sql Server 能够自动跟踪对列的更改。而且，如果你正在使用 Sql 2005，你真的应该使用 VARCHAR(MAX)和 VARBINARY(MAX)，所以这对某些人来说不是问题。但是我们这些仍然在 Sql 2000 上工作的人将被要求在任何老式的长文本或二进制列上建立索引时间表，以便保持新鲜。索引的确切频率取决于应用程序和您的需求。请记住，这是一个有点昂贵的过程，所以你不希望每隔 15 秒就发射一次，以保持“新鲜”

在任何情况下，要对表启用更改跟踪(自动索引)，请使用以下 T-SQL:

```
 exec sp_fulltext_table 'document_attachments', 'start_change_tracking' 

```

至于设置时间表，最好的方法是启动企业管理器，找到您的全文目录，右键单击它并选择 **Schedules。然后制定你觉得合适的时间表。**

但是等一下，怀亚特！不是还有吗！

为什么是的，还有更多。请继续关注 Sql Server 全文保护的下一期:使用全文查询的乐趣和益处。

谢谢，如果你喜欢就踢吧。

## 分享这篇文章