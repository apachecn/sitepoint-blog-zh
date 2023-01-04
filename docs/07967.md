# 使用 NotORM 简化数据库交互

> 原文：<https://www.sitepoint.com/database-interaction-made-easy-with-notorm/>

对象关系映射器(ORM)很酷。它们帮助您快速创建应用程序，而不必担心编写原始 SQL 查询。这样做的目的是简化数据库交互，避免编写复杂查询时可能出现的错误。事实上，现代 ORM 可以从数据库中生成模型/实体，反之亦然。

但是使用任何 ORM 的现实是，只有当你已经有了使用它的经验时，使用它才是简单的。为了充分利用它，你应该对这些概念有深刻的理解。任何 ORM 都有一个陡峭的学习曲线。

如果你正在开发的只是一个简单的应用程序，使用一个成熟的 ORM 可能是多余的。在这种情况下，您可能需要考虑使用 NotORM。NotORM 易学易用，因为它提供了一个直观的 API 来与数据库交互。在这篇文章中，我将教你如何使用 NotORM。

在我们开始之前，这里是我将在整篇文章中使用的数据库布局。

```
Table: author
+----+------------------------+
| id | name                   |
+----+------------------------+
|  1 | Khalil Gibran          |
|  2 | Sir Arthur Conan Doyle |
|  3 | Paulo Coelho           |
+----+------------------------+

Table: book
+----+-----------------+-----------+
| id | title           | author_id |
+----+-----------------+-----------+
|  1 | The Prophet     |         1 |
|  3 | Sherlock Holmes |         2 |
|  4 | The Alchemist   |         3 |
+----+-----------------+-----------+

Table: category
+----+------------+
| id | category   |
+----+------------+
|  1 | poem       |
|  2 | article    |
|  3 | tutorials  |
|  4 | philosophy |
|  5 | essays     |
|  6 | story      |
+----+------------+

Table: book_category
+----+---------+-------------+
| id | book_id | category_id |
+----+---------+-------------+
|  1 |       1 |           4 |
|  3 |       3 |           6 |
|  4 |       4 |           4 |
+----+---------+-------------+
```

## 连接到数据库

使用 NotORM 的第一步是创建一个`NotORM`对象的实例，它使用一个活动的 PDO 连接与数据库接口。

```
<?php
$dsn = "mysql:dbname=library;host=127.0.0.1";
$pdo = new PDO($dsn, "dbuser", "dbpassword");
$library = new NotORM($pdo);
```

数据源名称(DSN)是描述数据库连接的常用方式。它包含数据库驱动程序使用的名称、数据库名称和主机地址。PDO 构造函数接受 DSN 和数据库用户名和密码进行连接。一旦连接上，PDO 对象就被传递给 NotORM。我们将在整篇文章中使用这个 NotORM 实例。

## 基本检索

现在我们通过 NotORM 连接起来了，让我们列出数据库中的所有书籍。

```
<?php
$books = $library->book();
foreach ($books as $book) {
	echo $book["id"] . " " . $book["title"] . "<br>";
}
```

结果是:

```
1 The Prophet
3 Sherlock Holmes
4 The Alchemist
```

就这么简单！`$library`是 NotORM 对象，`book`是存储我们的图书信息的表名。`book()`方法返回一个多维数组，表的主键列作为一级索引。在`foreach`，`$book`是单个图书记录的表示，这是一个带有以表的列名命名的键的数组。`$book["title"]`返回该记录的标题列的值。

在大多数情况下，您不会对检索表中的所有列感兴趣。您可以通过`select()`方法指定您感兴趣的列。例如，如果您只需要标题，您可以将示例改写为:

```
<?php
$books = $library->book()->select("title");
foreach ($books as $book) {
	echo $book["title"] . "<br>";
}
```

对于有大量列的表，检索特定的列是特别理想的，这样您就不必浪费时间和内存来检索和存储您不使用的值。

为了使用主键从表中获取单个记录，我们在编写 SQL 时在查询的`WHERE`子句中引用主键。通常，我们有许多方法可以完成这项任务；最简单的方法是使用主键作为表属性的索引。

```
<?php
$book = $library->book[1]; 
echo $book["title"];
```

这将简单地从 ID 为 1 的记录中检索获取图书的详细信息。

## 对列值进行筛选

NotORM 允许使用`where()`方法在查询的`WHERE`子句中指定的条件下过滤结果。为了举例说明，让我们在表中搜索标题包含“炼金术士”的书籍。

```
<?php
$books = $library->book->where("title LIKE ?", "%Alchemist%");
foreach ($books as $book) {
	echo $book["id"] . " " . $book["title"] . "<br>";
}
```

结果是:

```
4 The Alchemist
```

如果你不熟悉准备好的陈述，你可能会想知道那个问号是什么意思。这是一种将参数绑定到 PDO 执行的查询的方法，这样您就可以通过更改值来多次执行相同的查询。一个问号，或者一个像`:title`这样的变量，就像一个值占位符。你可以在 PHP 手册中读到更多关于 PDO 的语句。

NotORM 还允许你链接`where()`方法来应用多个条件。

```
<?php
$books = $library->book->where("title LIKE ?", "%The%")
				   ->where("id < ?", 5);
foreach ($books as $book) {
	echo $book["id"] . " " . $book["title"] . "<br>";
}
```

```
1 The Prophet
4 The Alchemist
```

对于上面的示例，NotORM 发出的语句相当于下面的 SQL 查询:

```
SELECT * FROM book WHERE title LIKE "%The%" AND id < 5
```

## 排序结果

在您的整个应用程序中，很少有直接使用单个表进行查询的情况。在实际应用程序中，您需要连接许多表，根据不同列中的值对记录进行排序，限制检索的记录数量，等等。

您可以基于一列或多列对结果进行排序，升序或降序。下面给出的例子将按照 id 的降序返回书籍。

```
<?php
$books = $library->book->order("id desc");
foreach ($books as $id => $book) {
	echo $id . " " . $book["title"] . "<br>";
}
```

```
4 The Alchemist
3 Sherlock Holmes
1 The Prophet
```

如果要根据多列对结果进行排序，可以用逗号分隔它们。

```
<?php
$books = $library->book->order("id desc, title asc");
foreach ($books as $id => $book) {
	echo $id . " " . $book["title"] . "<br>";
}
```

结果记录将按照 ID 的降序返回，如果有多个记录具有相同的 ID(当然不会有),将按照标题的升序返回。

NotORM 也支持限制结果。让我们将结果集限制为两条记录，从偏移量 0 开始:

```
<?php
$books = $library->book->limit(2, 0);
foreach ($books as $id => $book) {
	echo $id . " " . $book["title"] . "<br>";
}
```

```
1 The Prophet
3 Sherlock Holmes
```

## 连接表格

到目前为止，我们一直在讨论如何用一张桌子列出书籍或进行不寻常的工作。现在我们想更进一步，比如找出这本书的作者等等。

让我们试着列出这些书及其作者。在我们的图书馆数据库中，book 表有一个外键`author_id`，它引用了`author`表(在这个设置中，每本书只能有一个作者)。

```
<table>
 <tr><th>Book</th><th>Author</th></tr>
<?php
$books = $library->book();
foreach ($books as $book) {
    echo "<tr>";
    echo "<td>" . $book["title"] . "</td>";
    echo "<td>" . $book->author["name"] . "</td>";
    echo "</tr>";
}
?>
</table>
```

上述代码的输出是:

```
Book             Author
The Prophet      Khalil Gibran
Sherlock Holmes  Sir Arthur Conan Doyle
The Alchemist    Paulo Coelho
```

当您调用`$book->author["name"]`时，NotORM 使用`author_id`列自动将`book`表与`author`表链接起来，并检索图书记录的作者详细信息。这是一对一关系(父表中的一条记录将只链接到子表中的一条记录)的情况。

在一对多关系的情况下，辅助表将有多条记录对应于主表中的一行。例如，假设我们可以为一本书写评论，那么对于每本书，将有零个或多个评论存储在另一个表中。对于每本书，你将需要另一个循环来显示它的评论，如果有的话。

对于多对多关系，将有第三个表链接主表和辅助表。我们有一个`category`表来保存图书类别，一本书可以有零个或多个相关的类别。使用`book_category`表维护链接。

```
<table>
 <tr><th>Book</th><th>Author</th><th>Categories</th></tr>
<?php
foreach ($books as $book) {
    echo "<tr>";
    echo "<td>" . $book["title"] . "</td>";
    echo "<td>" . $book["author"] . "</td>";
    // book_category table joins book and category
    $categories = array();
    foreach ($book->book_category() as $book_category) {
        $categories[] = $book_category->category["category"];
    }
    echo "<td>" . join(", ", $categories) . "</td>";
    echo "</tr>";
}
?>
 </tr>
</table>
```

```
Book             Author                 Categories
The Prophet      Khalil Gibran           philosophy
Sherlock Holmes  Sir Arthur Conan Doyle  story
The Alchemist    Paulo Coelho            philosophy, story
```

当您调用`book_category()`方法时，它将从`book_category`表中获取记录，然后使用`$book_category->category["category"]`连接到`category`表。

表名和列名有一些默认的约定，如果遵循这些约定，可以使在 NotORM 中处理表关系更加容易。

*   表名应该是单数，即使用 *book* 作为表名来存储图书详细信息。
*   使用 *id* 作为表的主键。没有必要让所有的表都有主键，但这是个好主意。
*   表中的外键应该命名为 *table_id* 。

如我所说，这些只是默认约定。如果你愿意，你可以遵循不同的约定，但是你需要告诉 NotORM 它应该遵循的约定。这个库有一个用于定义命名约定的类`NotORM_Structure_Convention`。这里有一个如何使用的简单例子，是我从 NotORM 网站上复制的。

```
<?php
$structure = new NotORM_Structure_Convention(
    $primary = "id_%s", // id_$table
    $foreign = "id_%s", // id_$table
    $table = "%ss", // {$table}s
    $prefix = "wp_" // wp_$table
);
$db = new NotORM($pdo, $structure);
```

该示例将所有表名改为复数，并以“wp_”为前缀。主键和外键已更改为 *id_table* 。

## 数据持久性

到目前为止，我们已经讨论了检索已经在数据库中的数据。接下来，我们还需要考虑存储和更新数据。

插入和更新非常简单直接。您只需要创建一个以列名作为键的关联数组，并将其作为参数传递给表的`insert()`或`update()`方法。

```
<?php
$book = $library->book();
$data = array(
    "title" => "Beginning PHP 5.3",
    "author_id" => 88
);
$result = $book->insert($data);
```

如果插入成功，将返回记录；如果插入失败，将返回 false。从那里，您可以使用`$result["id"]`获得插入记录的 ID。

要更新一本书，使用它的主键从数据库中获取 book 记录，然后将应该更新的值作为数组传递给`update()`。

```
<?php
$book = $library->book[1];
if ($book) {
    $data = array(
        "title" => "Pro PHP Patterns, Frameworks, Testing and More"
    );
    $result = $book->update($data);
}
```

`update()`如果成功将返回 true，如果更新失败将返回 false。

类似地，您可以通过在 book 对象上调用`delete()`来删除一本书。

```
<?php
$book = $library->book[10];
if ($book && $book->delete()) {
    echo "Book deleted successfully.";
}
```

重要的是要记住，当您更新或删除一行时，您首先要确保它存在于数据库中，否则您的脚本将抛出一个致命错误。

## 摘要

通过本文，您已经熟悉了一个用于与数据库交互的简单库。事实是，随着应用程序的增长，使用 NotORM 的代码将变得不那么容易管理，因此您需要根据应用程序的预期大小和其他因素来决定 NotORM 是否合适。但是当使用 NotORM 时，您不需要担心编写原始 SQL 查询或创建具有复杂关系的实体类。相反，您可以使用熟悉的面向对象符号来直接处理表和列。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章