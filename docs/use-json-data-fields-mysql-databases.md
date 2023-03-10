# 如何在 MySQL 数据库中使用 JSON 数据字段

> 原文：<https://www.sitepoint.com/use-json-data-fields-mysql-databases/>

**我的文章“ [SQL vs NoSQL:差异](https://www.sitepoint.com/sql-vs-nosql-differences/)”指出，SQL 和 NoSQL 数据库之间的界限变得越来越模糊，两个阵营都采用了对方的特性。MySQL 5.7+ InnoDB 数据库和 PostgreSQL 9.2+都在单个字段中直接支持 JSON 文档类型。在本文中，我们将更详细地研究 MySQL 8.0 JSON 实现。**

注意，任何数据库都将 JSON 文档作为单个字符串 blob 接受。然而，MySQL 和 PostgreSQL 支持真正的键/值对而不是基本字符串形式的经过验证的 JSON 数据。

## 就因为可以存储 JSON …

这不符合你的想法。

规范化是一种用于优化数据库结构的技术。第一范式(1NF)规则规定每一列应该保存一个值——存储多值 JSON 文档显然打破了这一规则。

如果您有明确的关系数据需求，请使用适当的单值字段。JSON 应该作为最后的手段谨慎使用。JSON 值字段不能被索引，所以避免在定期更新或搜索的列上使用它。此外，支持 JSON 的客户端应用程序较少，而且该技术较新，因此它可能不如其他类型稳定。

也就是说，对于稀疏填充的数据或定制属性，有很好的 JSON 用例。

## 创建一个带有 JSON 字段的表

考虑一个卖书的商店。所有的书都有 ID、ISBN、书名、出版商、页数和其他清晰的关系数据。假设您想要为每本书添加任意数量的类别标签。您可以使用 SQL 实现这一点:

1.  一个**标签**表，存储了每个标签名称和一个唯一的 ID，以及
2.  一个包含多对多记录的**标记映射**表，将图书 id 映射到标记 id

这是可行的，但是对于一个次要的特性来说，这是很麻烦和相当大的工作量。因此，您可以在 MySQL 数据库的 **book** 表中定义一个 **tags** JSON 字段:

```
CREATE TABLE `book` (
  `id` MEDIUMINT(8) UNSIGNED NOT NULL AUTO_INCREMENT,
  `title` VARCHAR(200) NOT NULL,
  `tags` JSON DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB; 
```

请注意，JSON 列不能有默认值，不能用作主键，不能用作外键，也不能有索引。您可以在生成的虚拟列上创建[二级索引，但是如果需要索引的话，在一个单独的字段中保留一个值会更加容易和实用。](https://dev.mysql.com/doc/refman/en/create-table-secondary-indexes.html)

## 添加 JSON 数据

整个 JSON 文档可以在 **INSERT** 或 **UPDATE** 语句中传递。例如，我们的图书标签可以作为数组传递(在字符串中):

```
INSERT INTO `book` (`title`, `tags`)
VALUES (
  'ECMAScript 2015: A SitePoint Anthology',
  '["JavaScript", "ES2015", "JSON"]'
); 
```

JSON 也可以用这些来创建:

*   [JSON_ARRAY()函数](https://dev.mysql.com/doc/refman/en/json-creation-functions.html#function_json-array)，创建数组。例如:

    ```
    -- returns [1, 2, "abc"]:
    SELECT JSON_ARRAY(1, 2, 'abc'); 
    ```

*   [JSON_OBJECT()函数](https://dev.mysql.com/doc/refman//en/json-creation-functions.html#function_json-object)，创建对象。例如:

    ```
    -- returns {"a": 1, "b": 2}:
    SELECT JSON_OBJECT('a', 1, 'b', 2); 
    ```

*   [JSON_QUOTE()函数](https://dev.mysql.com/doc/refman//en/json-creation-functions.html#function_json-quote)，引用一个字符串作为 JSON 值。例如:

    ```
    -- returns "[1, 2, \"abc\"]":
    SELECT JSON_QUOTE('[1, 2, "abc"]'); 
    ```

*   或者可以`(CAST anyValue AS JSON)`。

JSON_TYPE()函数允许你检查 JSON 值类型。它应该返回对象、数组、标量类型(整数、布尔等)、空值或错误。例如:

```
-- returns ARRAY:
SELECT JSON_TYPE('[1, 2, "abc"]');

-- returns OBJECT:
SELECT JSON_TYPE('{"a": 1, "b": 2}');

-- returns an error:
SELECT JSON_TYPE('{"a": 1, "b": 2'); 
```

如果 JSON 有效，JSON_VALID()函数返回 1，否则返回 0:

```
-- returns 1:
SELECT JSON_TYPE('[1, 2, "abc"]');

-- returns 1:
SELECT JSON_TYPE('{"a": 1, "b": 2}');

-- returns 0:
SELECT JSON_TYPE('{"a": 1, "b": 2'); 
```

尝试插入无效的 JSON 文档将会引发错误，并且整个记录将不会被插入/更新。

## 搜索 JSON 数据

[JSON_CONTAINS()函数](https://dev.mysql.com/doc/refman//en/json-search-functions.html#function_json-contains)接受正在搜索的 JSON 文档和另一个要比较的文档。当找到匹配时，它返回 1。例如:

```
-- all books with the 'JavaScript' tag:
SELECT * FROM `book` WHERE JSON_CONTAINS(tags, '["JavaScript"]'); 
```

类似的 [JSON_SEARCH()函数](https://dev.mysql.com/doc/refman//en/json-search-functions.html#function_json-search)返回给定匹配的路径，或者当没有匹配时返回 NULL。它传递了被搜索的 JSON 文档，`'one'`查找第一个匹配，或者`'all'`查找所有匹配，以及一个搜索字符串(其中`%`匹配任意数量的字符，`_`以与 [`LIKE`](https://dev.mysql.com/doc/refman//en/string-comparison-functions.html#operator_like) 相同的方式匹配一个字符)。例如:

```
-- all books with tags starting 'Java':
SELECT * FROM `book` WHERE JSON_SEARCH(tags, 'one', 'Java%') IS NOT NULL; 
```

## JSON 路径

JSON 路径以值为目标，可用于提取或修改 JSON 文档的各个部分。 [JSON_EXTRACT()函数](https://dev.mysql.com/doc/refman//en/json-search-functions.html#function_json-extract)通过提取一个或多个值来演示这一点:

```
-- returns "SitePoint":
SELECT JSON_EXTRACT('{"id": 1, "website": "SitePoint"}', '$.website'); 
```

所有路径定义都以一个`$`开头，后面跟着其他选择器:

*   后跟名称的句点，如`$.website`
*   其中 N 是零索引数组中的位置
*   `.[*]`通配符评估一个对象的所有成员
*   通配符`[*]`评估数组的所有成员
*   `prefix**suffix`通配符评估以指定前缀开始并以指定后缀结束的所有路径

以下示例引用了以下 JSON 文档:

```
{
  "a": 1,
  "b": 2,
  "c": [3, 4],
  "d": {
    "e": 5,
    "f": 6
  }
} 
```

示例路径:

*   `$.a`返回`1`
*   `$.c`返回`[3, 4]`
*   `$.c[1]`返回`4`
*   `$.d.e`返回`5`
*   `$**.e`返回`[5]`

## 在查询中提取 JSON 路径

您可以使用以下查询提取您的 **book** 表的名称和第一个标签:

```
SELECT
  title, tags->"$[0]" AS `tag1`
FROM `book`; 
```

对于一个更复杂的例子，假设您有一个包含 JSON 概要数据的**用户**表。例如:

| 身份证明（identification） | 名字 | 轮廓 |
| --- | --- | --- |
| one | 克雷格（m.） | { "email": ["craig@email1.com "，" craig@email2.com"]，" twitter": "@craigbuckler" } |
| Two | 站点点 | { "email": []，" twitter": "@sitepointdotcom" } |

您可以使用 JSON 路径提取 Twitter 名称。例如:

```
SELECT
  name, profile->"$.twitter" AS `twitter`
FROM `user`; 
```

您可以在 WHERE 子句中使用 JSON 路径，只返回拥有 Twitter 帐户的用户:

```
SELECT
  name, profile->"$.twitter" AS `twitter`
FROM `user`
WHERE
  profile->"$.twitter" IS NOT NULL; 
```

## 修改 JSON 文档的一部分

有几个 [MySQL 函数可以使用路径符号修改 JSON 文档](https://dev.mysql.com/doc/refman//en/json-modification-functions.html)的一部分。其中包括:

*   `JSON_SET(doc, path, val[, path, val]...)`:在文档中插入或更新数据
*   `JSON_INSERT(doc, path, val[, path, val]...)`:将数据插入文档
*   `JSON_REPLACE(doc, path, val[, path, val]...)`:替换文档中的数据
*   `JSON_MERGE(doc, doc[, doc]...)`:合并两个或多个文档
*   `JSON_ARRAY_APPEND(doc, path, val[, path, val]...)`:将值追加到数组的末尾
*   `JSON_ARRAY_INSERT(doc, path, val[, path, val]...)`:在文档中插入一个数组
*   `JSON_REMOVE(doc, path[, path]...)`:从文档中删除数据

因此，您可以为任何已经有“JavaScript”标签的书籍添加“技术”标签:

```
UPDATE `book`
  SET tags = JSON_MERGE(tags, '["technical"]')
WHERE
  JSON_SEARCH(tags, 'one', 'JavaScript') IS NOT NULL; 
```

## 进一步的信息

MySQL 手册提供了关于 [JSON 数据类型](https://dev.mysql.com/doc/refman/en/json.html)和[相关 JSON 函数](https://dev.mysql.com/doc/refman//en/json-functions.html)的更多信息。

我再次敦促您，除非绝对必要，否则不要使用 JSON。您可以在 MySQL 中模拟一个完整的面向文档的 NoSQL 数据库，但是这会抵消 SQL 的许多[好处，您还不如切换到一个真正的 NoSQL 系统！也就是说，JSON 数据类型可能会为 SQL 应用程序中更模糊的数据需求节省精力。](https://www.sitepoint.com/sql-vs-nosql-choose/)

## 分享这篇文章