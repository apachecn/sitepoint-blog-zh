# 使用 Python 的 SQLite 简介

> 原文：<https://www.sitepoint.com/sqlite-python/>

**在本文中，我们将探讨 SQLite。我们将通过一个名为 [sqlite3](https://docs.python.org/3/library/sqlite3.html) 的 Python 库来学习如何使用 SQLite。最后，我们将探索由`sqlite3`提供的一些更高级的特性，以使我们的工作更容易。**

*注意:入门之前，熟悉 SQL 就好了。如果你不是，你可能想检查一下* [简单的 SQL](https://www.sitepoint.com/premium/books/simply-sql/) 。

## 什么是 SQLite？

[SQLite](https://www.sqlite.org/index.html) 的座右铭是:“小。很快。可靠。任选三个。”

SQLite 是一个用 c 编写的嵌入式数据库库。您可能熟悉 MySQL 或 PostgreSQL 等其他数据库技术。这些使用客户机-服务器方法:数据库作为服务器安装，然后使用客户机连接到它。SQLite 是不同的:它被称为一个**嵌入式**数据库，因为它作为一个库包含在一个程序中。所有的数据都存储在一个文件中——通常带有一个`.db`扩展名——并且您拥有允许您运行 SQL 语句或对数据库进行任何其他操作的函数。

基于文件的存储解决方案还提供了**并发访问**，这意味着多个进程或线程可以访问同一个数据库。好吧，那么 SQLite 有哪些用法呢？它适合任何类型的应用吗？

SQLite 在一些情况下表现出色:

*   如果你想要一个**独立的**和**无服务器的**数据存储解决方案，SQLite 可能是一个完美的选择。

*   不使用巨大的 CSV 文件，您可以利用 **SQL** 的能力，将所有数据放入一个 SQLite 数据库中。

*   SQLite 可用于存储应用程序的配置数据。事实上，SQLite 比基于文件的系统(如配置文件)快 35%。

另一方面，有哪些不选择 SQLite 的理由？

*   与 MySQL 或 PostgreSQL 不同，SQLite 缺乏多用户功能。

*   SQLite 仍然是基于文件的数据存储解决方案，而不是服务。您不能将它作为一个进程来管理，不能启动或停止它，也不能管理资源的使用。

### SQLite 的 Python 接口

正如我在介绍中所说，SQLite 是一个 C 库。尽管有很多语言编写的接口，包括 Python。`sqlite3`模块提供了一个 SQL 接口，至少需要 SQLite 3.7.15。

牛逼的是`sqlite3`自带 Python，不需要安装任何东西。

## sqlite3 入门

是时候编码了！在第一部分中，我们将创建一个基本的数据库。首先要做的是创建一个数据库并连接到它:

```
import sqlite3
dbName = 'database.db'

try:
  conn = sqlite3.connect(dbName)
  cursor = conn.cursor()
  print("Database created!")

except Exception as e:
  print("Something bad happened: ", e)
  if conn:
    conn.close() 
```

在第 1 行，我们导入了`sqlite3`库。然后，在一个`try/except`代码块中，我们调用`sqlite3.connect()`来初始化到数据库的连接。如果一切顺利，`conn`将成为`Connection`对象的一个实例。如果`try`失败，我们打印收到的异常并关闭与数据库的连接。正如官方文档中所述，每个打开的 SQLite 数据库都由一个`Connection`对象表示。每次我们必须执行 SQL 命令时，`Connection`对象都有一个名为`cursor()`的方法。在数据库技术中，游标是一种控制结构，可以遍历数据库中的记录。

现在，如果我们执行这段代码，应该会得到以下输出:

```
> Database created! 
```

如果我们查看 Python 脚本所在的文件夹，应该会看到一个名为`database.db`的新文件。该文件是由`sqlite3`自动创建的。

### 创建、读取和修改记录

此时，我们准备创建一个新表，添加第一个条目并执行 SQL 命令，如`SELECT`、`UPDATE`或`DROP`。

要创建一个表，我们只需要执行一个简单的 SQL 语句。在本例中，我们将创建一个包含以下数据的学生表:

| 身份证明（identification） | 名字 | 姓 |
| --- | --- | --- |
| one | 约翰 | 史密斯（姓氏） |
| Two | 露西 | 雅各布斯 |
| three | 斯蒂芬 | 泰勒 |

在`print("Database created!")`行之后，添加以下内容:

```
# Create operation
create_query = '''CREATE TABLE IF NOT EXISTS student(
  id INTEGER PRIMARY KEY,
  name TEXT NOT NULL,
  surname TEXT NOT NULL);
  '''
cursor.execute(create_query)
print("Table created!")

# Insert and Read operation
cursor.execute("INSERT INTO student VALUES (1, 'John', 'Smith')")
print("Insert #1 done!")
cursor.execute("INSERT INTO student VALUES (2, 'Lucy', 'Jacobs')")
print("Insert #2 done!")
cursor.execute("INSERT INTO student VALUES (3, 'Stephan', 'Taylor')")
print("Insert #3 done!")
conn.commit()
conn.close() 
```

我们创建一个表并调用`cursor.execute()`方法，当我们想要执行一条 SQL 语句时就使用这个方法。

然后，我们为想要添加的每一行做一个`INSERT`。在我们所有的更改都完成之后，我们调用`conn.commit()`将挂起的事务提交给数据库。如果不调用`commit()`方法，对数据库的任何挂起的更改都将丢失。最后，我们通过调用`conn.close()`方法来关闭到数据库的连接。

好，现在让我们查询我们的数据库！我们需要一个变量来保存我们的查询结果，所以让我们将`cursor.execute()`的结果保存到一个名为`records`的变量中:

```
records = cursor.execute("SELECT * FROM student")
for row in findrecords:
  print(row) 
```

执行此操作后，我们将看到所有记录到`stdout`:

```
(1, 'John', 'Smith')
(2, 'Lucy', 'Jacobs')
(3, 'Stephan', 'Taylor') 
```

此时，您可能已经注意到，在`cursor.execute()`方法中，我们放置了必须执行的 SQL 命令。如果我们想执行另一个 SQL 命令，如`UPDATE`或`DROP`，Python 语法没有任何变化。

## 占位符

`cursor.execute()`方法需要一个字符串作为参数。在上一节中，我们看到了如何将数据插入到数据库中，但是一切都是硬编码的。如果我们需要在数据库中存储一些变量中的东西呢？为此，`sqlite3`有一些花哨的东西叫做占位符。**占位符**允许我们使用参数替换，这将使得在查询中插入变量更加容易。

让我们看看这个例子:

```
def insert_command(conn, student_id, name, surname):
  command = 'INSERT INTO student VALUES (?, ?, ?)'
  cur = conn.cursor()
  cur.execute(command, (student_id, name, surname, ))
  conn.commit() 
```

我们创建了一个叫做`insert_command()`的方法。这个方法有四个参数:第一个是一个`Connection`实例，另外三个将在我们的 SQL 命令中使用。

`command`变量中的每个`?`代表一个占位符。这意味着，如果你用`student_id=1`、`name='Jason'`和`surname='Green'`调用`insert_command`函数，`INSERT`语句将变成`INSERT INTO student VALUES(1, 'Jason', 'Green')`。

当我们调用`execute()`函数时，我们将命令和所有将被替换的变量传递给占位符。从现在开始，每次我们需要在 student 表中插入一行时，我们调用带有所需参数的`insert_command()`方法。

## 处理

即使您对交易的定义并不陌生，让我快速回顾一下它的重要性。**事务**是在逻辑上被视为一个单元的数据库上执行的一系列操作。

事务最重要的好处是确保数据的完整性。在我们上面介绍的例子中，这可能没有用，但是当我们处理存储在多个表中的更多数据时，事务确实有所不同。

Python 的`sqlite3`模块在 [execute()](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.execute) 和 [executemany()](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.executemany) 执行`INSERT`、`UPDATE`、`DELETE`或`REPLACE`语句之前启动一个事务。这意味着两件事:

*   我们必须注意调用`commit()`方法。如果我们在没有执行`commit()`的情况下调用`Connection.close()`，我们在事务期间所做的所有更改都将丢失。
*   我们不能使用`BEGIN`在同一个流程中打开一个事务。

解决办法？[明确处理交易](https://docs.python.org/3/library/sqlite3.html#transaction-control)。

怎么会？通过使用函数调用`sqlite3.connect(dbName, isolation_level=None)`而不是`sqlite3.connect(dbName)`。通过将`isolation_level`设置为`None`，我们强制`sqlite3`永远不要隐式打开事务。

下面的代码重写了前面的代码，但是明确使用了事务:

```
import sqlite3
dbName = 'database.db'

def insert_command(conn, student_id, name, surname):
  command = 'INSERT INTO student VALUES (?, ?, ?)'
  cur = conn.cursor()
  cur.execute("BEGIN")
  try:
    cur.execute(command, (student_id, name, surname, ))
    cur.execute("COMMIT")
  except conn.Error as e:
    print("Got an error: ", e)
    print("Aborting...")
    cur.execute("ROLLBACK")

conn = sqlite3.connect(dbName, isolation_level=None)
cursor = conn.cursor()
print("Database created!")

# Create operation
create_query = '''CREATE TABLE IF NOT EXISTS student(
  id INTEGER PRIMARY KEY,
  name TEXT NOT NULL,
  surname TEXT NOT NULL);
  '''
cursor.execute(create_query)
print("Table created!")

# Insert and Read operation
insert_command(conn , 1, 'John', 'Smith')
insert_command(conn , 2, 'Lucy', 'Jacobs')
insert_command(conn , 3, 'Stephan', 'Taylor')
insert_command(conn , 4, 'Joseph', 'Random')
findRecords = cursor.execute("SELECT * FROM student")
for row in findRecords:
  print(row)

conn.close() 
```

## 结论

我希望您现在已经很好地理解了什么是 SQLite，如何在您的 Python 项目中使用它，以及它的一些高级特性是如何工作的。事务的显式管理一开始可能有点棘手，但它肯定能帮助您充分利用`sqlite3`。

**相关阅读:**

*   【SQLite3 入门:基本命令
*   [使用 unittest 和 pytest 进行 Python 单元测试的介绍](https://www.sitepoint.com/python-unit-testing-unittest-pytest/)
*   [使用 SQLite 管理 iOS 应用中的数据](https://www.sitepoint.com/managing-data-in-ios-apps-with-sqlite/)
*   [HTTP Python 请求初学者指南](https://www.sitepoint.com/http-python-requests/)
*   [SQL 与 NoSQL:区别](https://www.sitepoint.com/sql-vs-nosql-differences/)

## 分享这篇文章