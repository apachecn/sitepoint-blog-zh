# 使用 EXPLAIN 编写更好的 MySQL 查询

> 原文：<https://www.sitepoint.com/using-explain-to-write-better-mysql-queries/>

当您发出一个查询时，MySQL 查询优化器会尝试设计一个最佳的查询执行计划。您可以通过在查询前加上`EXPLAIN`来查看关于计划的信息。`EXPLAIN`是理解和优化复杂的 MySQL 查询的最强大的工具之一，但遗憾的是许多开发人员很少使用它。在本文中，您将了解到`EXPLAIN`的输出是什么，以及如何使用它来优化您的模式和查询。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## 理解解释的输出

使用`EXPLAIN`就像在`SELECT`查询之前预先挂起它一样简单。让我们分析一个简单查询的输出，以熟悉该命令返回的列。

```
EXPLAIN SELECT * FROM categoriesG
```

```
********************** 1\. row **********************
           id: 1
  select_type: SIMPLE
        table: categories
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
        Extra: 
1 row in set (0.00 sec)
```

看起来可能不是这样，但是这 10 个专栏包含了大量的信息！查询返回的列有:

*   `id`–查询中每个`SELECT`的连续标识符(当有嵌套子查询时)
*   `select_type`—`SELECT`查询的类型。可能的值有:
    *   `SIMPLE`–该查询是一个简单的`SELECT`查询，没有任何子查询或`UNION`
    *   PRIMARY——`SELECT`位于`JOIN`中最外层的查询中
    *   DERIVED——`SELECT`是`FROM`子句中子查询的一部分
    *   子查询–子查询中的第一个`SELECT`
    *   从属子查询–依赖于外部查询的子查询
    *   不可缓存的子查询–不可缓存的子查询(在某些情况下查询是可缓存的)
    *   UNION—`SELECT`是 UNION 的第二个或后面的语句
    *   依赖联合——第二个或后面的`UNION`依赖于外部查询
    *   联合结果——`SELECT`是`UNION`的结果
*   `table`–行引用的表
*   MySQL 如何连接所使用的表。这是输出中最有洞察力的字段之一，因为它可以表明缺少索引或者应该重新考虑如何编写查询。可能的值有:
    *   系统–表格只有零行或一行
    *   const–该表只有一个被索引的匹配行。这是最快的连接类型，因为只需读取一次表，并且在连接其他表时，列的值可以被视为常量。
    *   eq _ ref–索引的所有部分都由 join 使用，索引为`PRIMARY KEY`或`UNIQUE NOT NULL`。这是下一个最好的连接类型。
    *   ref–从上一个表中读取每个行组合的索引列的所有匹配行。这种类型的联接出现在使用`=`或`<=>`运算符进行比较的索引列中。
    *   全文–连接使用表的`FULLTEXT`索引。
    *   ref _ or _ null–这与 ref 相同，但也包含列值为 null 的行。
    *   index _ merge–该连接使用一个索引列表来生成结果集。`EXPLAIN`输出的密钥列将包含所使用的密钥。
    *   unique _ subquery–一个`IN`子查询只从表中返回一个结果，并使用主键。
    *   index _ subquery–与 unique_subquery 相同，但返回多个结果行。
    *   范围–索引用于查找特定范围内的匹配行，通常是在使用运算符`BETWEEN`、`IN`、`>`、`>=`等将键列与常量进行比较时。
    *   index–扫描整个索引树以查找匹配的行。
    *   all–扫描整个表以查找连接的匹配行。这是最差的连接类型，通常表示表上缺少适当的索引。
*   `possible_keys`–显示 MySQL 可以用来从表中查找行的键，尽管它们在实践中可能会用到，也可能不会用到。事实上，该列通常有助于优化查询，因为如果该列为空，则表明找不到相关的索引。
*   `key`–表示 MySQL 使用的实际索引。该列可能包含未在`possible_key`列中列出的索引。MySQL 优化器总是寻找可用于查询的最佳键。当连接许多表时，它可能会计算出其他一些没有在`possible_key`中列出但更优化的键。
*   `key_len`–表示查询优化器选择使用的索引长度。例如，`key_len`值为 4 意味着它需要内存来存储四个字符。查看 MySQL 的[数据类型存储要求](vhttp://dev.mysql.com/doc/refman/5.0/en/storage-requirements.html)以了解更多信息。
*   `ref`–显示与关键字列中指定的索引相比较的列或常数。MySQL 要么选择一个常数值进行比较，要么根据查询执行计划选择一个列本身。你可以在下面的例子中看到这一点。
*   `rows`–列出为产生输出而检查的记录数量。这是另一个值得关注的优化查询的重要列，特别是对于使用`JOIN`和子查询的查询。
*   `Extra`–包含有关查询执行计划的附加信息。诸如“使用临时”、“使用文件排序”等值。这一列中的可能表示一个麻烦的查询。要获得可能值及其含义的完整列表，请查看 MySQL 文档。

您还可以在查询中的`EXPLAIN`后添加关键字`EXTENDED`，MySQL 将向您显示关于它执行查询的方式的附加信息。要查看信息，请使用`SHOW WARNINGS`跟随您的`EXPLAIN`查询。这对于查看查询优化器进行任何转换后执行的查询非常有用。

```
EXPLAIN EXTENDED SELECT City.Name FROM City
JOIN Country ON (City.CountryCode = Country.Code)
WHERE City.CountryCode = 'IND' AND Country.Continent = 'Asia'G
```

```
********************** 1\. row **********************
           id: 1
  select_type: SIMPLE
        table: Country
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 3
          ref: const
         rows: 1
     filtered: 100.00
        Extra: 
********************** 2\. row **********************
           id: 1
  select_type: SIMPLE
        table: City
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4079
     filtered: 100.00
        Extra: Using where
2 rows in set, 1 warning (0.00 sec)
```

```
SHOW WARNINGSG
```

```
********************** 1\. row **********************
  Level: Note
   Code: 1003
Message: select `World`.`City`.`Name` AS `Name` from `World`.`City` join `World`.`Country` where ((`World`.`City`.`CountryCode` = 'IND'))
1 row in set (0.00 sec)
```

## 使用 EXPLAIN 排除性能故障

现在让我们看看如何通过分析`EXPLAIN`的输出来优化一个性能不佳的查询。在处理现实世界的应用程序时，无疑会有许多表，它们之间有许多关系，但有时很难预测编写查询的最佳方式。

在这里，我为一个没有任何索引或主键的电子商务应用程序创建了一个示例数据库，并将通过编写一个非常糟糕的查询来演示如此糟糕的设计的影响。您可以从 GitHub 下载[模式示例](https://github.com/phpmasterdotcom/UsingExplainToWriteBetterMySQLQueries)。

```
EXPLAIN SELECT * FROM
orderdetails d
INNER JOIN orders o ON d.orderNumber = o.orderNumber
INNER JOIN products p ON p.productCode = d.productCode
INNER JOIN productlines l ON p.productLine = l.productLine
INNER JOIN customers c on c.customerNumber = o.customerNumber
WHERE o.orderNumber = 10101G
```

```
********************** 1\. row **********************
           id: 1
  select_type: SIMPLE
        table: l
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 7
        Extra: 
********************** 2\. row **********************
           id: 1
  select_type: SIMPLE
        table: p
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 110
        Extra: Using where; Using join buffer
********************** 3\. row **********************
           id: 1
  select_type: SIMPLE
        table: c
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 122
        Extra: Using join buffer
********************** 4\. row **********************
           id: 1
  select_type: SIMPLE
        table: o
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 326
        Extra: Using where; Using join buffer
********************** 5\. row **********************
           id: 1
  select_type: SIMPLE
        table: d
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 2996
        Extra: Using where; Using join buffer
5 rows in set (0.00 sec)
```

如果你看上面的结果，你可以看到一个坏的查询的所有症状。但是，即使我编写了一个更好的查询，结果仍然是一样的，因为没有索引。连接类型显示为“ALL”(这是最糟糕的)，这意味着 MySQL 无法识别任何可以在连接中使用的键，因此`possible_keys`和`key`列为空。最重要的是，`rows`列显示 MySQL 扫描每个表的所有记录进行查询。这意味着在执行查询时，它将扫描 7 × 110 × 122 × 326 × 2996 = 91，750，822，240 条记录来查找四个匹配结果。那真的很恐怖，而且只会随着数据库的增长呈指数级增长。

现在让我们添加一些明显的索引，比如每个表的主键，并再次执行查询。根据一般经验，您可以将查询的`JOIN`子句中使用的列视为键的良好候选，因为 MySQL 总是会扫描这些列来查找匹配的记录。

```
ALTER TABLE customers
    ADD PRIMARY KEY (customerNumber);
ALTER TABLE employees
    ADD PRIMARY KEY (employeeNumber);
ALTER TABLE offices
    ADD PRIMARY KEY (officeCode);
ALTER TABLE orderdetails
    ADD PRIMARY KEY (orderNumber, productCode);
ALTER TABLE orders
    ADD PRIMARY KEY (orderNumber),
    ADD KEY (customerNumber);
ALTER TABLE payments
    ADD PRIMARY KEY (customerNumber, checkNumber);
ALTER TABLE productlines
    ADD PRIMARY KEY (productLine);
ALTER TABLE products 
    ADD PRIMARY KEY (productCode),
    ADD KEY (buyPrice),
    ADD KEY (productLine);
ALTER TABLE productvariants 
    ADD PRIMARY KEY (variantId),
    ADD KEY (buyPrice),
    ADD KEY (productCode);
```

添加索引后，让我们再次运行相同的查询，结果应该如下所示:

```
********************** 1\. row **********************
           id: 1
  select_type: SIMPLE
        table: o
         type: const
possible_keys: PRIMARY,customerNumber
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
        Extra: 
********************** 2\. row **********************
           id: 1
  select_type: SIMPLE
        table: c
         type: const
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 1
        Extra: 
********************** 3\. row **********************
           id: 1
  select_type: SIMPLE
        table: d
         type: ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: const
         rows: 4
        Extra: 
********************** 4\. row **********************
           id: 1
  select_type: SIMPLE
        table: p
         type: eq_ref
possible_keys: PRIMARY,productLine
          key: PRIMARY
      key_len: 17
          ref: classicmodels.d.productCode
         rows: 1
        Extra: 
********************** 5\. row **********************
           id: 1
  select_type: SIMPLE
        table: l
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 52
          ref: classicmodels.p.productLine
         rows: 1
        Extra: 
5 rows in set (0.00 sec)
```

添加索引后，扫描的记录数减少到 1 × 1 × 4 × 1 × 1 = 4。这意味着对于在`orderdetails`表中带有`orderNumber` 10101 的每条记录，MySQL 能够使用索引直接在所有其他表中找到匹配的记录，而不必求助于扫描整个表。

在第一行的输出中，您可以看到所使用的连接类型是“const”，对于有多条记录的表，这是最快的连接类型。MySQL 能够使用`PRIMARY`键作为索引。ref 列显示“const”，它只不过是查询的`WHERE`子句中使用的值 10101。

让我们再看一个示例查询。这里我们基本上取两个表的并集，`products`和`productvariants`，每个表都用`productline`连接。`productvariants`表格包含以`productCode`为参考关键字的不同产品变型及其价格。

```
EXPLAIN SELECT * FROM (
SELECT p.productName, p.productCode, p.buyPrice, l.productLine, p.status, l.status AS lineStatus FROM
products p
INNER JOIN productlines l ON p.productLine = l.productLine
UNION
SELECT v.variantName AS productName, v.productCode, p.buyPrice, l.productLine, p.status, l.status AS lineStatus FROM productvariants v
INNER JOIN products p ON p.productCode = v.productCode
INNER JOIN productlines l ON p.productLine = l.productLine
) products
WHERE status = 'Active' AND lineStatus = 'Active' AND buyPrice BETWEEN 30 AND 50G
```

```
********************** 1\. row **********************
           id: 1
  select_type: PRIMARY
        table: <derived2>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 219
        Extra: Using where
********************** 2\. row **********************
           id: 2
  select_type: DERIVED
        table: p
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 110
        Extra: 
********************** 3\. row **********************
           id: 2
  select_type: DERIVED
        table: l
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 52
          ref: classicmodels.p.productLine
         rows: 1
        Extra: 
********************** 4\. row **********************
           id: 3
  select_type: UNION
        table: v
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 109
        Extra: 
********************** 5\. row **********************
           id: 3
  select_type: UNION
        table: p
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 17
          ref: classicmodels.v.productCode
         rows: 1
        Extra: 
********************** 6\. row **********************
           id: 3
  select_type: UNION
        table: l
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 52
          ref: classicmodels.p.productLine
         rows: 1
        Extra: 
********************** 7\. row **********************
           id: NULL
  select_type: UNION RESULT
        table: <union2,3>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: NULL
        Extra: 
7 rows in set (0.01 sec)
```

您可以在这个查询中看到许多问题。它扫描`products`和`productvariants`表中的所有记录。由于这些表上没有针对`productLine`和`buyPrice`列的索引，输出的`possible_keys`和`key`列显示为空。在`UNION`之后检查`products`和`productlines`的状态，因此将它们移动到`UNION`内部将减少记录的数量。让我们添加一些额外的索引并重写查询。

```
CREATE INDEX idx_buyPrice ON products(buyPrice);
CREATE INDEX idx_buyPrice ON productvariants(buyPrice);
CREATE INDEX idx_productCode ON productvariants(productCode);
CREATE INDEX idx_productLine ON products(productLine);
```

```
EXPLAIN SELECT * FROM (
SELECT p.productName, p.productCode, p.buyPrice, l.productLine, p.status, l.status as lineStatus FROM products p
INNER JOIN productlines AS l ON (p.productLine = l.productLine AND p.status = 'Active' AND l.status = 'Active') 
WHERE buyPrice BETWEEN 30 AND 50
UNION
SELECT v.variantName AS productName, v.productCode, p.buyPrice, l.productLine, p.status, l.status FROM productvariants v
INNER JOIN products p ON (p.productCode = v.productCode AND p.status = 'Active') 
INNER JOIN productlines l ON (p.productLine = l.productLine AND l.status = 'Active')
WHERE
v.buyPrice BETWEEN 30 AND 50
) productG
```

```
********************** 1\. row **********************
          id: 1
  select_type: PRIMARY
        table: <derived2>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 12
        Extra: 
********************** 2\. row **********************
           id: 2
  select_type: DERIVED
        table: p
         type: range
possible_keys: idx_buyPrice,idx_productLine
          key: idx_buyPrice
      key_len: 8
          ref: NULL
         rows: 23
        Extra: Using where
********************** 3\. row **********************
           id: 2
  select_type: DERIVED
        table: l
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 52
          ref: classicmodels.p.productLine
         rows: 1
        Extra: Using where
********************** 4\. row **********************
           id: 3
  select_type: UNION
        table: v
         type: range
possible_keys: idx_buyPrice,idx_productCode
          key: idx_buyPrice
      key_len: 9
          ref: NULL
         rows: 1
        Extra: Using where
********************** 5\. row **********************
           id: 3
  select_type: UNION
        table: p
         type: eq_ref
possible_keys: PRIMARY,idx_productLine
          key: PRIMARY
      key_len: 17
          ref: classicmodels.v.productCode
         rows: 1
        Extra: Using where
********************** 6\. row **********************
           id: 3
  select_type: UNION
        table: l
         type: eq_ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 52
          ref: classicmodels.p.productLine
         rows: 1
        Extra: Using where
********************** 7\. row **********************
           id: NULL
  select_type: UNION RESULT
        table: <union2,3>
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: NULL
        Extra: 
7 rows in set (0.01 sec)
```

正如您在结果中看到的，现在扫描的近似行数从 2，625，810 (219 × 110 × 109)显著减少到 276 (12 × 23)，这是一个巨大的性能增益。如果您尝试相同的查询，没有之前的重新安排，只是在添加索引之后，您不会看到太大的减少。MySQL 不能利用索引，因为它在派生结果中有`WHERE`子句。在将这些条件转移到`UNION`中之后，它就可以使用索引了。这意味着仅仅添加一个索引并不总是足够的；除非您相应地编写查询，否则 MySQL 将无法使用它。

## 摘要

在本文中，我讨论了 MySQL `EXPLAIN`关键字，它的输出意味着什么，以及如何使用它的输出来构造更好的查询。在现实世界中，它可能比这里演示的场景更有用。通常情况下，您会将多个表连接在一起，并使用复杂的`WHERE`子句。简单地在几列上添加索引可能并不总是有帮助的，然后是时候更仔细地研究一下查询本身了。

<small>图片 via[Efman](http://www.shutterstock.com/gallery-442516p1.html)/[Shutterstock](http://www.shutterstock.com)</small>

## 分享这篇文章