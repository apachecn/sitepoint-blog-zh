# 超越基础的 SQLite 3

> 原文：<https://www.sitepoint.com/sqlite-3-beyond-basics/>

在 SQLite 3 的[介绍文章中，我们介绍了一些基本的命令、数据库和表的创建等等。在这一篇中，我们将更深入地探讨其他数据库中的常见特性以及使 SQLite 脱颖而出的特性。](https://www.sitepoint.com/getting-started-sqlite3-basic-commands/)

![](img/9b4e3de79f205de5f655d73ad32e4474.png)

假设您在继续阅读本文之前已经阅读了介绍性文章，或者您已经熟悉了 SQLite 3 的基础知识。

## 选择查询

SELECT 是一个标准的 SQL 命令，属于数据查询语言(或 DQL)。它允许您在数据库上执行查询并获取所需的记录。可以使用 SQLite 提供的各种子句进一步过滤结果。

打开终端，输入:`sqlite3 Library.db`

这将在当前目录中创建一个名为 **Library.db** 的数据库。现在，让我们创建一个存储图书馆用户信息的表。图书馆用户必须有一个名字，一个由图书馆提供的唯一的 ID，一个用户的年龄和他们的加入日期。以下 SQL 查询将创建该表:

```
CREATE TABLE Users ( 
  SerialNo INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
  Name TEXT NOT NULL,
  Id TEXT NOT NULL UNIQUE,
  Age INTEGER NOT NULL,
  DOJ TEXT NOT NULL );
```

序列号是主键。

> **记住:**一个表中可以有任意数量的`UNIQUE`列，但是必须只有一个`PRIMARY KEY`。

使用以下查询将记录插入表中:

```
INSERT INTO Users ( Name, Id, Age, DOJ)
VALUES ( 'Shivam', 'U123', 19, '2015-01-31' );
```

> **注意:** SQLite 对于日期没有不同的数据类型。要在 SQLite 中存储日期，您必须以`YYYY-MM-DD`格式输入它们，否则它们将被视为字符串。

您可以使用上面的命令插入更多的记录，但是对于大量的数据来说，这将是非常繁琐的。我们将使用`.read`元命令。它从指定的文件执行 SQL 查询。将以下内容保存到名为`newusers.sql`的文件中:

```
BEGIN TRANSACTION;

CREATE TABLE NewUsers (
    SerialNo INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    Name TEXT NOT NULL,
    Id TEXT NOT NULL UNIQUE,
    Age INTEGER NOT NULL,
    DOJ TEXT NOT NULL );

INSERT INTO NewUsers VALUES (1,  'Abraham',     'U123', 19, '2015-02-28');
INSERT INTO NewUsers VALUES (2,  'Michael',     'U124', 23, '2014-12-14');
INSERT INTO NewUsers VALUES (3,  'Steve',       'U125', 35, '2012-11-20');
INSERT INTO NewUsers VALUES (4,  'Ben',         'U126', 15, '2013-05-12');
INSERT INTO NewUsers VALUES (5,  'Alice',       'U127', 17, '2013-03-01');
INSERT INTO NewUsers VALUES (6,  'Christopher', 'U128', 19, '2014-05-03');
INSERT INTO NewUsers VALUES (7,  'Elena',       'U129', 27, '2011-07-14');
INSERT INTO NewUsers VALUES (8,  'Daniel',      'U130', 43, '2010-08-14');
INSERT INTO NewUsers VALUES (9,  'Candice',     'U131', 18, '2014-02-05');
INSERT INTO NewUsers VALUES (10, 'Billy',       'U132', 20, '2015-01-24');
INSERT INTO NewUsers VALUES (11, 'Michael',     'U133', 19, '2013-02-12');
INSERT INTO NewUsers VALUES (12, 'Alice',       'U134', 25, '2010-07-14');
INSERT INTO NewUsers VALUES (13, 'Richard',     'U135', 40, '2011-12-14');
INSERT INTO NewUsers VALUES (14, 'Lester',      'U136', 18, '2014-09-05');
INSERT INTO NewUsers VALUES (15, 'Malvo',       'U137', 21, '2015-01-04');
INSERT INTO NewUsers VALUES (16, 'Alice',       'U138', 19, '2013-02-02');
INSERT INTO NewUsers VALUES (17, 'Drako',       'U139', 24, '2010-12-02');

COMMIT;
```

并使用以下命令从 sqlite 命令行读取它:

`.read newusers.sql`

如果一切顺利，您将拥有一个包含新记录的 **NewUsers** 表。使用选择查询来显示它们。

```
SELECT Id, Name, Age, DOJ
FROM NewUsers;
```

默认情况下，不显示列标题，显示模式设置为行。通过输入`.header ON`打开标题显示，并使用`.mode column`将模式样式更改为列。进入**再次选择**查询。

**提示:**使用`.show`命令显示各种设置值。您也可以使用`.width` [1](#fn:width "See footnote") 元命令手动指定列宽。

您可以更改要显示的列数，并为标题指定另一个名称(别名)。例如:

```
SELECT Id AS 'User ID', Name, DOJ AS 'Date of Joining'
FROM NewUsers;
```

您可以使用`.schema`命令来显示用于创建数据库的所有 SQL 语句。您可以选择为它提供一个表名，以显示特定表的模式。

### WHERE 子句

WHERE 子句指定了获取结果的条件。例如，要获取年龄在 20 岁或以上的用户 id 和姓名，我们可以输入以下查询。

```
SELECT Id, Name FROM NewUsers
WHERE Age >= 20;
```

### 经营者

SQLite3 提供了许多运算符来优化 WHERE 子句。二元运算符按优先级降序排列如下:

```
||
*   /   %
+   -
<<  >>  &   |
<   <=  >   >=
=   ==  !=  <>  IS  IS NOT  IN  LIKE  GLOB  MATCH  REGEXP
AND   
OR
```

SQLite3 支持四个一元前缀运算符，`- + ~ NOT`。

除了这些，还有其他一些类似`BETWEEN`和`EXISTS`的操作符。

#### 在...之间

提供要操作的值范围(最小值到最大值)。例如，要选择加入日期在`12-30-2011`和`12-30-2014`之间并且年龄在 17 到 27 岁之间的用户，我们可以输入以下查询:

```
SELECT * FROM NewUsers
WHERE DOJ BETWEEN '2011-12-30' AND '2014-12-30' 
AND Age BETWEEN 17 AND 27;
```

#### 与，或与非

**和**操作符提供两个条件的交集，而**或**操作符提供两个条件的并集。例如，要选择年龄大于 20 且序列号不大于 5 的用户，请输入以下查询:

```
SELECT * FROM NewUsers
WHERE Age > 20 AND SerialNo > 5;
```

现在，要选择年龄大于 20 岁的用户或序列号大于 5 的用户，请输入:

```
SELECT * FROM NewUsers
WHERE Age > 20 OR SerialNo > 5;
```

**NOT** 运算符只是相应条件的否定。年龄不在 20 到 30 岁之间的用户可以通过以下方式提取:

```
SELECT * FROM NewUsers
WHERE Age NOT BETWEEN 20 AND 30;
```

#### 是和不是

**是****不是**工作与 **=** (等于)和**相同！=** (不等于)分别。

#### 在和不在

运算符中的**在文字列表中搜索一个值。例如，要搜索库 id 为 U124、U127 和 U129 的用户:**

```
SELECT * FROM NewUsers
WHERE Id IN ('U124', 'U127', 'U129');
```

#### 喜欢和全球

**像**一样使用通配符`%`和`_`进行模式匹配比较。`%`通配符匹配零个或多个字符，`_`匹配任何单个字符。要选择名称以“B”开头、以“y”结尾且倒数第三个字符为“l”的用户，我们可以输入:

```
SELECT * FROM NewUsers
WHERE Name LIKE "B%l_y";
```

> **注意:** LIKE 是不区分大小写的，所以“B%”和“b%”在和 LIKE 一起使用时作用是一样的。

**GLOB** 类似于 **LIKE** ，除了它区分大小写并使用 [Unix 文件 Globbing](http://en.wikipedia.org/wiki/Glob_%28programming%29) 语法。

#### 存在

**存在**运算符评估为 **0** 或 **1** 。如果 EXISTS 右侧的查询不返回任何行，则 EXISTS 返回零，如果查询返回一行或多行，则 EXISTS 返回一。

```
SELECT EXISTS ( SELECT * FROM NewUsers WHERE Age < 10 );
```

这将返回零，因为没有年龄小于 10 岁的用户。

#### `||`操作员

`||`运算符连接指定的字符串。以下查询将给出结果:“sitepoint.com”。

```
SELECT 'sitepoint' || '.' || 'com';
```

### ORDER BY 子句

顾名思义， **ORDER BY** 子句按升序或降序对记录进行排序。它对特定的列进行操作，接受`ASC`进行升序排序，接受`DESC`进行降序排序。

例如，按姓名的升序对所有用户记录进行排序。

```
SELECT * FROM NewUsers
ORDER BY Name ASC;
```

要按年龄和加入日期在 *2013-05-12* 之后的降序对用户记录进行排序，您必须输入以下查询:

```
SELECT * FROM NewUsers
WHERE DOJ > '2013-05-12' ORDER BY Age DESC;
```

### 限制条款

**LIMIT** 子句将查询结果的数量限制在指定的数量。它带有一个可选的**偏移**标志，声明要跳过的记录数。例如，要选择第六、第七和第八条记录，我们需要设置限制为 3，偏移量为 5。

```
SELECT * FROM NewUsers
LIMIT 3 OFFSET 5;
```

> **注:**上面的查询也可以写成:
> `SELECT * FROM NewUsers LIMIT 5, 3;`

### GROUP BY 子句

**GROUP BY** 组合多个记录，并使用一列或多列对它们进行分组。例如，要统计具有特定名称的用户数量，我们可以输入以下查询:

```
SELECT Name, COUNT(Name) FROM NewUsers
GROUP BY Name;
```

### HAVING 子句

如果我们需要在组上指定条件，我们不能使用 WHERE 子句，因为 WHERE 子句在列上指定条件。为了提供关于组的条件，使用了具有子句的**。在上面的例子中，让我们提取同名的用户。**

```
SELECT Name, COUNT(Name) FROM NewUsers
GROUP BY Name HAVING COUNT(Name) > 1;
```

### 明显的

**DISTINCT** 关键字检索唯一的记录。要获取唯一名称，请输入以下查询:

```
SELECT DISTINCT Name
FROM NewUsers ORDER BY Name;
```

## 附加和分离数据库

当操作多个数据库时，可以为每个数据库附加一个别名。`ATTACH DATABASE`命令为数据库附加一个名称。输入`.databases`。您将获得数据库名称及其各自文件的列表。**主**数据库是主数据库。除了**主**，还有一个临时的，隐藏的数据库**临时**。这两者既不能附加到别名，也不能从别名中分离。让我们将 **Library.db** 附加到别名 **LIB** 上。

```
ATTACH DATABASE 'Library.db' AS 'LIB';
```

考虑另一个数据库 **Students.db** 。把它附在**斯图**身上。

```
ATTACH DATABASE 'Students.db' AS 'STU';
```

输入`.databases`。你会得到这样的东西:

![Attach Database](img/69d67e7fdbacb887425703f5914bd1f9.png)

`seq`列没有介于 0 和 2 之间的记录 1。它实际上是隐藏的数据库 **temp** 。

现在，您可以在同一个 sqlite 会话中对两个数据库执行查询。要从 **LIB** 中选择前十条记录，我们可以输入以下内容:

```
SELECT * FROM LIB.NewUsers
WHERE 1 LIMIT 10;
```

假设 STU 包含一个包含学生姓名的学生表。我们需要找出作为学生的图书馆用户的详细信息(在这个例子中，将姓名视为 students 和 NewUsers 中的一列)。我们可以输入以下查询:

```
SELECT LIB.NewUsers.Id, LIB.NewUsers.Name, LIB.NewUsers.Age
FROM LIB.NewUsers
INNER JOIN STU.Students
ON STU.Students.Name = LIB.NewUsers.Name;
```

要将 **Library.db** 与其别名 **LIB** 分离，请输入以下内容:

```
DETACH DATABASE 'LIB';
```

## 处理

一旦创建了数据库，我们就可以执行查询来创建和修改表中的记录。这些改变数据库先前状态的查询中的一个或多个会产生一个**事务**。所以一个事务可以包括插入、更新和删除记录。它还包括表的创建、修改和删除。一个事务内部可以有几个较小的执行——考虑一个银行的数据库。用户取钱，他们的帐户状态被更新，银行的状态被更新，日志数据库得到一个新的记录，等等——所有这些都需要完美地发生，否则钱将在一个表中丢失，给每个人都带来一个问题。

当您以事务的形式执行查询时，更改不会直接添加到数据库中。你必须自己去做。这有助于防止在交易过程中出现错误或系统故障。在这种情况下，数据库不会改变其状态，也不会留下部分更改。

SQLite 是一个事务数据库，即它符合 [**ACID** 标准](http://en.wikipedia.org/wiki/ACID)。酸是原子性、一致性、隔离性和持久性的缩写。这些术语将在下面讨论。

Atomicity

原子性使得事务不可分割。要么一次提交所有更改，要么整个事务失败。

Consistency

每个事务都是一致的，即事务后达到的最终状态也将是有效状态。

Isolation

事务以这样一种方式相互分离，即在并发执行期间，一个事务的失败不会影响其他事务的执行。

Durability

一旦事务成功提交，数据库中的更改将是永久的，不会受到任何系统故障的影响。

SQLite 提供了控制事务的命令。下表列出了控制命令。

| 命令 | 描述 |
| --- | --- |
| 开始交易 | 标志着交易的开始。 |
| 反转 | 删除当前交易。如果与 TO 关键字一起使用，它将回滚到特定的保存点。 |
| 犯罪 | 向数据库提交事务。这是结束事务的另一个别名。 |

除此之外，还有两个命令:保存点和释放。SAVEPOINT 类似于 BEGIN，只是您必须为开始的事务提供一个名称，从而允许您嵌套事务。RELEASE 删除预定义的保存点，并将命名事务合并到其父事务中。

让我们继续 **Library.db** 。输入`BEGIN TRANSACTION;`开始交易。在表中插入一些有效记录:

```
INSERT INTO NewUsers VALUES (18, 'Frank', 'U140', 45, '2009-11-02');
INSERT INTO NewUsers VALUES (19, 'Claire', 'U141', 43, '2009-11-01');
```

要保存这些更改，您可以输入`COMMIT;`。或者如果您想丢弃它们，请输入`ROLLBACK;`。

SAVEPOINT 语句用一个名称启动事务。创建一个名为 SPT1 的保存点，并添加一条新记录。

```
SAVEPOINT SPT1;
INSERT INTO NewUsers VALUES (20, 'Ricky', 'U142', 26, '2011-07-22');
```

创建另一个保存点 SPT2 并添加另一条记录。

```
SAVEPOINT SPT2;
INSERT INTO NewUsers VALUES (21, 'Sundar', 'U143', 31, '2011-09-21');
```

这两个保存点都是嵌套的(SPT1 中的 SPT2)。您可以通过输入以下命令来提交这两个命令:

```
RELEASE SAVEPOINT SPT1;
```

当 RELEASE 应用于嵌套保存点的最外层保存点时，它会从最近的保存点开始提交所有保存点。因此 SPT2 和 SPT1 都被提交。

> `RELEASE SAVEPOINT SPT2;`将释放 SPT2。但是因为它是父事务 SPT1 的子事务，所以它的命运取决于它。如果 SPT1 回滚，SPT2 也会回滚。

要放弃这两个保存点，您可以回滚到最外面的保存点，即 SPT1。

```
ROLLBACK TO SAVEPOINT SPT1;
```

### 自动提交模式

如果在没有显式定义事务的情况下执行查询，每个查询都被包装在自己的事务中，然后执行。您可以将它想象成带有特定 BEGIN 事务和 COMMIT 语句的每个查询。这是自动提交模式。默认情况下，自动提交模式是打开的。

如果显式启动一个事务，自动提交模式将被关闭。在提交(或回滚)到同一个事务后，自动提交模式再次打开。

## 导出数据库

在本教程的开始，我们使用`.read`命令将一个表导入数据库。为了导出数据库或表格，我们使用了`.dump`命令。`.dump`以 SQL 文本格式显示数据库。您可以在命令行中使用重定向操作符将转储保存到文件中。

```
sqlite3 Library.db '.dump' > LibBackup.sql
```

如果文件 **LibBackup.sql** 已经存在，它将被覆盖。上述命令会将整个数据库转储到文件中。您可以选择提供一个表名作为`.dump`的参数。它将导出那个特定的表。要转储新用户，请输入:

```
sqlite3 Library.db '.dump NewUsers' > LibBackup.sql
```

## 输出结果

默认情况下，通过查询生成的输出写在标准输出上。SQLite 提供了`.output`命令来将一个或多个查询的输出写入文件。

输入`.output output.txt`和一些查询。结果将被写入存储在当前工作目录中的`output.txt`。您可以退出 sqlite 会话并查看该文件。

![.output](img/e6e1937e35b44bc86a6a738a3c74fddc.png)

> `.output stdout`显示输出到标准输出。

## 结论

我们已经学习了 SQLite 操作符和表达式。本教程还包括事务和关于 SQLite 事务性质的简短讨论。希望你觉得有用；如果你有任何问题，欢迎在评论中提问，我会尽力回答。

* * *

1.  `.width`设置列模式的列宽。以逗号分隔的值提供字符宽度。例如`.width 10, 20, 15` [↩](#fnref:width "Return to article")

## 分享这篇文章