# MySQL 性能提升与索引和解释

> 原文：<https://www.sitepoint.com/mysql-performance-indexes-explain/>

提高应用程序性能的技术可以来自许多不同的地方，但通常我们首先关注的是数据库，这也是最常见的瓶颈。还能改进吗？我们如何衡量和理解什么是需要和可以改进的？

一个非常简单但非常有用的工具是查询概要分析。启用概要分析是获得运行查询的更准确时间估计的简单方法。这是一个两步过程。首先，我们必须启用概要分析。然后，我们调用`show profiles`来实际获取查询运行时间。

假设我们的数据库中有以下插入内容(并假设已经创建了用户 1 和图库 1):

```
INSERT INTO `homestead`.`images` (`id`, `gallery_id`, `original_filename`, `filename`, `description`) VALUES
(1, 1, 'me.jpg', 'me.jpg', 'A photo of me walking down the street'),
(2, 1, 'dog.jpg', 'dog.jpg', 'A photo of my dog on the street'),
(3, 1, 'cat.jpg', 'cat.jpg', 'A photo of my cat walking down the street'),
(4, 1, 'purr.jpg', 'purr.jpg', 'A photo of my cat purring'); 
```

显然，这么大的数据量不会造成任何麻烦，但是让我们用它来做一个简单的侧写。让我们考虑以下查询:

```
SELECT * FROM `homestead`.`images` AS i
WHERE i.description LIKE '%street%'; 
```

这个查询是一个很好的例子，如果我们得到很多照片条目，将来可能会出现问题。

为了获得该查询的准确运行时间，我们将使用以下 SQL:

```
set profiling = 1;
SELECT * FROM `homestead`.`images` AS i
WHERE i.description LIKE '%street%';
show profiles; 
```

结果将如下所示:

| 查询标识 | 持续时间 | 询问 |
| --- | --- | --- |
| one | 0.00016950 | 显示警告 |
| Two | 0.00039200 | 从`homestead`中选择*。`images`AS I \ n where I . description LIKE \ ' % street % \ ' \ n 限制为 0，1000 |
| three | 0.00037600 | 显示来自`homestead`的按键。`images` |
| four | 0.00034625 | 显示类似“家园”的数据库 |
| five | 0.00027600 | 显示来自`homestead`的表格，如“图像” |
| six | 0.00024950 | 从`homestead`中选择*。`images`其中 0=1 |
| seven | 0.00104300 | 显示从`homestead`开始的所有列。`images`喜欢\'id\ ' |

正如我们所看到的，`show profiles;`命令不仅给出了原始查询的时间，还给出所有其他查询的时间。这样，我们可以准确地分析我们的查询。

但是我们怎样才能真正改善它们呢？

我们可以依靠我们的 SQL 知识并随机应变，或者我们可以依靠 MySQL `explain`命令并基于实际信息提高我们的查询性能。

**解释**用于获取查询执行计划，或者说 MySQL 将如何执行我们的查询。它使用`SELECT`、`DELETE`、`INSERT`、`REPLACE`和`UPDATE`语句，并显示来自优化器的关于语句执行计划的信息。官方文档[很好地描述了`explain`如何帮助我们:](https://dev.mysql.com/doc/refman/5.7/en/explain.html)

> 在 EXPLAIN 的帮助下，您可以看到应该在表的什么位置添加索引，以便通过使用索引查找行来更快地执行语句。您还可以使用 EXPLAIN 来检查优化器是否以最佳顺序连接表。

为了举例说明`explain`的用法，我们将使用`UserManager.php`发出的查询通过电子邮件查找用户:

```
SELECT * FROM `homestead`.`users` WHERE email = 'claudio.ribeiro@examplemail.com'; 
```

要使用`explain`命令，我们只需在选择类型查询之前添加它:

```
EXPLAIN SELECT * FROM `homestead`.`users` WHERE email = 'claudio.ribeiro@examplemail.com'; 
```

这是结果(向右滚动查看全部):

| 身份证明（identification） | 选择类型 | 桌子 | 划分 | 类型 | 可能的键 | 键 | keygen | 裁判员 | 行 | 走漏 | 额外的 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 简单的 | 用户的 | 空 | 常数 | UNIQ_1483A5E9E7927C74 ' | UNIQ_1483A5E9E7927C74 ' | ‘182’ | 常数 | One hundred | 空 |  |

这些结果乍一看并不容易理解，所以让我们仔细看看每一个结果:

*   `id`:这只是 SELECT 中每个查询的顺序标识符。

*   `select_type`:选择查询的类型。该字段可以采用许多不同的值，因此我们将重点关注最重要的值:

    *   `SIMPLE`:没有子查询或联合的简单查询
    *   `PRIMARY`:select 位于连接中最外层的查询中
    *   `DERIVED`:select 是 from 中的子查询的一部分
    *   `SUBQUERY`:子查询中的第一个选择
    *   `UNION`:select 是 union 的第二个或后面的语句。

    可以在`select_type`字段中出现的值的完整列表可以在[这里](https://dev.mysql.com/doc/refman/5.5/en/explain-output.html#explain_select_type)找到。

*   `table`:行引用的表。

*   这个字段是 MySQL 如何连接所使用的表。这可能是解释输出中最重要的字段。它可以指示缺少的索引，还可以显示应该如何重写查询。该字段的可能值如下(从最佳类型到最差类型排序):

    *   `system`:表格有零行或一行。
    *   `const`:该表只有一个匹配的索引行。是最快的联接类型。
    *   `eq_ref`:索引的所有部分都被连接使用，并且索引或者是 PRIMARY_KEY 或者是 UNIQUE NOT NULL。
    *   `ref`:从上一个表中读取每个行组合的索引列的所有匹配行。与`=`或`<=>`操作符相比，这种类型的连接通常出现在索引列中。
    *   `fulltext`:连接使用表全文索引。
    *   `ref_or_null`:与 ref 相同，但也包含列中值为空的行。
    *   `index_merge`:连接使用一个索引列表来产生结果集。`explain`的密钥列将包含所使用的密钥。
    *   `unique_subquery`:IN 子查询只从表中返回一个结果，并使用主键。
    *   `range`:索引用于查找特定范围内匹配的行。
    *   `index`:扫描整个索引树以找到匹配的行。
    *   `all`:扫描整个表，寻找连接的匹配行。这是最糟糕的连接类型，通常表示表上缺少适当的索引。
*   `possible_keys`:显示了 MySQL 可以用来从表中查找行的键。这些密钥在实践中可能会使用，也可能不会使用。

*   `keys`:表示 MySQL 使用的实际索引。MySQL 总是寻找可用于查询的最佳键。当连接许多表时，它可能会找出一些其他键，这些键没有在`possible_keys`中列出，但是更优化。

*   `key_len`:表示查询优化器选择使用的索引的长度。

*   `ref`:显示与 key 列中指定的索引进行比较的列或常量。

*   `rows`:列出为产生输出而检查的记录数。这是一个非常重要的指标；检查的记录越少越好。

*   `Extra`:包含附加信息。该列中的值如`Using filesort`或`Using temporary`可能表示查询有问题。

关于`explain`输出格式的完整文档可以在官方 MySQL 页面上找到[。](https://dev.mysql.com/doc/refman/5.5/en/explain-output.html)

回到我们的简单查询:这是一个带有常量类型连接的`SIMPLE`类型的 select。这是我们可能拥有的最好的查询情况。但是当我们需要更大更复杂的查询时会发生什么呢？

回到我们的应用程序模式，我们可能希望获得所有图库图像。我们还可能希望在描述中只包含单词“cat”的照片。这绝对是我们可以在项目需求中找到的案例。让我们来看看这个查询:

```
SELECT gal.name, gal.description, img.filename, img.description FROM `homestead`.`users` AS users
LEFT JOIN `homestead`.`galleries` AS gal ON users.id = gal.user_id
LEFT JOIN `homestead`.`images` AS img on img.gallery_id = gal.id
WHERE img.description LIKE '%dog%'; 
```

在这个更复杂的案例中，我们应该有更多的信息来分析我们的`explain`:

```
EXPLAIN SELECT gal.name, gal.description, img.filename, img.description FROM `homestead`.`users` AS users
LEFT JOIN `homestead`.`galleries` AS gal ON users.id = gal.user_id
LEFT JOIN `homestead`.`images` AS img on img.gallery_id = gal.id
WHERE img.description LIKE '%dog%'; 
```

这会产生以下结果(向右滚动以查看所有单元格):

| 身份证明（identification） | 选择类型 | 桌子 | 划分 | 类型 | 可能的键 | 键 | keygen | 裁判员 | 行 | 走漏 | 额外的 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 简单的 | 用户的 | 空 | '索引' | 主要，UNIQ_1483A5E9BF396750 ' | unil _ 1483 a5 和 9BF396750 " | ‘108’ | 空 | One hundred | '使用索引' |  |
| one | 简单的 | 加尔 | 空 | '参考' | '主要，UNIQ_F70E6EB7BF396750，IDX_F70E6EB7A76ED395 ' | unil _ 1483 a5 和 9BF396750 " | ‘108’ | homestead.users.id ' | One hundred | 空 |  |
| one | 简单的 | ' img ' | 空 | '参考' | IDX_E01FBE6A4E7AF8F ' | IDX_E01FBE6A4E7AF8F ' | ‘109’ | ' homestead.gal.id ' | ‘25.00’ | “使用位置” |  |

让我们仔细看看，看看我们可以在查询中改进什么。

正如我们前面看到的，我们应该首先查看的主要列是`type`列和`rows`列。目标应该是在`type`栏中获得一个更好的值，并在`rows`栏中尽可能减少。

我们第一次查询的结果是`index`，这根本不是一个好结果。这意味着我们有可能改进它。

查看我们的查询，有两种方法可以处理它。首先，`Users`表没有被使用。我们要么扩展查询以确保我们以用户为目标，要么我们应该完全删除查询的`users`部分。这只会增加我们整体表现的复杂性和时间。

```
SELECT gal.name, gal.description, img.filename, img.description FROM `homestead`.`galleries` AS gal
LEFT JOIN `homestead`.`images` AS img on img.gallery_id = gal.id
WHERE img.description LIKE '%dog%'; 
```

所以现在我们有了完全相同的结果。我们来看看`explain`:

| 身份证明（identification） | 选择类型 | 桌子 | 划分 | 类型 | 可能的键 | 键 | keygen | 裁判员 | 行 | 走漏 | 额外的 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 简单的 | 加尔 | 空 | '全部' | 主要，UNIQ_1483A5E9BF396750 ' | 空 | 空 | 空 | One hundred | 空 |  |
| one | 简单的 | ' img ' | 空 | '参考' | IDX_E01FBE6A4E7AF8F ' | IDX_E01FBE6A4E7AF8F ' | ‘109’ | ' homestead.gal.id ' | ‘25.00’ | “使用位置” |  |

我们只剩下一个`ALL`在打字。虽然`ALL`可能是最糟糕的连接类型，但有时它是唯一的选择。根据我们的要求，我们需要所有图库图像，因此我们需要搜索整个图库表。虽然索引在试图查找表中的特定信息时非常有用，但当我们需要表中的所有信息时，它却帮不了我们。当我们遇到这种情况时，我们必须求助于不同的方法，比如缓存。

由于我们正在处理一个`LIKE`，我们可以做的最后一个改进是向我们的描述字段添加一个全文索引。这样，我们可以将`LIKE`改为`match()`，提高性能。关于全文索引[的更多内容可以在这里找到](https://dev.mysql.com/doc/refman/5.6/en/innodb-fulltext-index.html)。

还有两个非常有趣的案例我们必须看看:我们应用程序中的`newest`和`related`功能。这些适用于画廊，并触及一些我们应该注意的角落案例:

```
EXPLAIN SELECT * FROM `homestead`.`galleries` AS gal
LEFT JOIN `homestead`.`users` AS u ON u.id = gal.user_id
WHERE u.id = 1
ORDER BY gal.created_at DESC
LIMIT 5; 
```

以上为相关图库。

```
EXPLAIN SELECT * FROM `homestead`.`galleries` AS gal
ORDER BY gal.created_at DESC
LIMIT 5; 
```

以上是最新的画廊。

乍一看，这些查询应该非常快，因为它们使用了`LIMIT`。大多数使用`LIMIT`的查询都是这种情况。对我们和我们的应用程序来说不幸的是，这些查询也使用了`ORDER BY`。因为我们需要在限制查询之前对所有结果进行排序，所以我们失去了使用`LIMIT`的优势。

因为我们知道`ORDER BY`可能很棘手，所以让我们应用我们可信赖的`explain`。

| 身份证明（identification） | 选择类型 | 桌子 | 划分 | 类型 | 可能的键 | 键 | keygen | 裁判员 | 行 | 走漏 | 额外的 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 简单的 | 加尔 | 空 | '全部' | IDX_F70E6EB7A76ED395 ' | 空 | 空 | 空 | One hundred | 使用 where'使用文件排序' |  |
| one | 简单的 | 你好 | 空 | ' eq_ref ' | 主要，UNIQ_1483A5E9BF396750 ' | 主要的 | ‘108’ | ' homestead.gal.id ' | ‘100.00’ | 空 |  |

而且，

| 身份证明（identification） | 选择类型 | 桌子 | 划分 | 类型 | 可能的键 | 键 | keygen | 裁判员 | 行 | 走漏 | 额外的 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | 简单的 | 加尔 | 空 | '全部' | 空 | 空 | 空 | 空 | One hundred | '使用文件排序' |  |

正如我们所看到的，我们的两个查询都有最坏情况的连接类型:`ALL`。

从历史上看，MySQL 的`ORDER BY`实现，尤其是与`LIMIT`一起使用时，常常是导致 MySQL 性能问题的原因。这种组合也用于大多数具有大型数据集的交互式应用程序中。像新注册用户和顶部标签这样的功能通常使用这种组合。

因为这是一个常见的问题，所以也有一些我们应该应用的常见解决方案来处理性能问题。

*   **确保我们使用了索引**。在我们的例子中，`created_at`是一个很好的候选，因为它是我们排序的字段。这样，我们既执行了`ORDER BY`又执行了`LIMIT`，而无需扫描和排序整个结果集。
*   **按前导表中的一列排序**。通常，如果`ORDER BY`是表中的字段，而不是连接顺序中的第一个，那么索引就不能使用。
*   **不按表情排序**。表达式和函数不允许通过`ORDER BY`使用索引。
*   **小心大的`LIMIT`值**。大的`LIMIT`值将迫使`ORDER BY`对更多的行进行排序。这会影响性能。

当我们同时拥有`LIMIT`和`ORDER BY`时，这些是我们应该采取的一些措施，以便最小化性能问题。

## 结论

正如我们所见，`explain`对于在早期发现查询中的问题非常有用。有很多问题，只有当我们的应用程序处于生产状态，并且有大量数据或大量访问者访问数据库时，我们才会注意到。如果可以在使用`explain`的早期发现这些问题，那么将来出现性能问题的可能性就会小得多。

我们的应用程序拥有它所需要的所有索引，而且速度非常快，但是我们现在知道，每当我们需要检查性能提升时，我们总是可以求助于`explain`和索引。

## 分享这篇文章