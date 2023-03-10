# SQLite3 入门:基本命令

> 原文：<https://www.sitepoint.com/getting-started-sqlite3-basic-commands/>

**[SQLite](http://www.sqlite.org/) 是一个实现 SQL 数据库引擎的 C 库。它是一个关系数据库管理系统(或 RDBMS)。大多数 SQL 数据库使用客户机/服务器模型。以 [MySQL](http://www.mysql.com/) 为例。要从 MySQL 数据库中输入和接收数据，您需要向 MySQL 服务器发送一个请求，服务器会在收到请求后为您提供适当的响应。与 MySQL 相反，SQLite 数据库是直接从磁盘操作的。不需要创建对服务器的请求。**

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## 装置

我们将使用**sqlite3**1 命令行界面(CLI)对我们的数据库进行操作。我们将在 Linux 平台下工作。**用于 MAC OS 和 Windows 的 sqlite3** CLI 是一样的，但是为了一致起见，建议你安装[Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)——一个轻量级的虚拟机，可以在 5 分钟内运行，可以让你的电脑清除多余的不需要的软件。

要在 Homestead Improved(或任何基于 Debian 的操作系统，如 Ubuntu)上安装它，运行以下命令:

```
sudo apt-get install sqlite3 libsqlite3-dev
```

这将安装 **sqlite3** 。要在其他平台上安装，请遵循他们的[官方指示](http://www.sqlite.org/download.html)。安装后，我们可以开始会话。打开终端/命令行，输入命令`sqlite3`。你一定会看到这样的东西:

![CLI sqlite3](img/1bc91ae2452957574cd9c03384faa94d.png)

第二行告诉您输入`.help`以获取指令。有一个提示等待您键入命令。所以继续前进，输入`.help`。这将给你一个**元命令**及其描述的列表。

## 元命令

**元命令**用于定义表格的输出格式，检查数据库和其他管理操作。他们总是以一个*点*开始。甚至`.help`都是元命令。你可以浏览一下名单。这里有一些会经常派上用场:

| 命令 | 描述 |
| --- | --- |
| 。显示 | 显示各种参数的当前设置 |
| 。数据库 | 提供数据库名称和文件 |
| 。放弃 | 退出 sqlite3 程序 |
| 。桌子 | 显示当前表格 |
| 。 (计划或理论的)纲要 | 显示表的模式 |
| 。页眉 | 显示或隐藏输出表格标题 |
| 。方式 | 选择输出表的模式 |
| 。倾销 | 以 SQL 文本格式转储数据库 |

## 标准命令

让我们看一下 sqlite3 中的标准命令。发布元命令来检查数据库。发出标准的 SQL 命令来操作数据库。**标准命令**可以分为三组:

*   **数据定义语言**:提供从数据库系统中访问数据的存储结构和方法。

    *   [创建](https://www.sqlite.org/lang_createtable.html)
    *   [改变](https://www.sqlite.org/lang_altertable.html)
    *   [下降](https://www.sqlite.org/lang_droptable.html)
*   **数据操作语言**:用户可以操作(添加/修改/删除)数据。

    *   [插入](https://www.sqlite.org/lang_insert.html)
    *   [更新](https://www.sqlite.org/lang_update.html)
    *   [删除](https://www.sqlite.org/lang_delete.html)
*   **数据查询语言**:让用户从数据库中检索所需数据。

    *   [选择](https://www.sqlite.org/lang_select.html)

**注意:** SQLite 理解许多其他标准命令，可以在这里阅读[的列表。由于本教程是初级介绍，我们将只涵盖上述命令。](https://www.sqlite.org/lang.html)

SQLite 数据库是*跨平台的可移植文件*。它们可以存储在各种存储设备上，并可以在不同的计算机之间传输。

我们将学习使用 **sqlite3** 和我们的*评论区*数据库。如今几乎每个网站都有评论区。要发布评论，用户必须输入以下详细信息:

*   名字
*   电子邮件
*   网站(全球资讯网的主机站)
*   评论

在这四个选项中，只有网址是可选的。我们还必须定义一个对注释进行编号的列。姑且称之为 *post_id* 。

我们可以如下定义列(属性)的数据类型:

| 属性 | 需要数据类型 |
| --- | --- |
| post_id | 整数 |
| 名字 | 文本 |
| 电子邮件 | 文本 |
| 网站 _ 网址 | 文本 |
| 评论 | 文本 |

您可以查看 SQLite3 中提供的不同[数据类型和存储类](https://www.sqlite.org/datatype3.html)的文档。

SQLite3 使用**清单类型** [2](#fn:manifesttyping "See footnote") 。包括 MySQL 在内的大多数其他数据库引擎都使用静态类型。

现在让我们创建一个数据库。如果您仍在 sqlite3 程序中，请在 sqlite 提示符下输入`.quit`退出该程序。然后，发出命令:

```
sqlite3 comment_section.db
```

这将在当前工作目录下创建一个数据库文件 *comment_section.db* 。

**注意:**如果命令后面没有文件名，sqlite3 会自动创建一个临时数据库。这就是我们第一次输入单个命令 sqlite3 时发生的情况。

### [创建表格](https://www.sqlite.org/lang_createtable.html)

为了存储注释，我们必须定义一个表。我们把它命名为`comments`。要创建它，请输入语句:

```
CREATE TABLE comments ( 
	post_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, 
	name TEXT NOT NULL, 
	email TEXT NOT NULL, 
	website_url TEXT NULL, 
	comment TEXT NOT NULL );
```

确保记录中的特定值不为空。`PRIMARY KEY` [3](#fn:primarykey "See footnote") 和`AUTOINCREMENT`4 阐述 *post_id* 属性。

要检查表是否已经创建，发出 meta 命令`.tables`。它将显示表名`comments`。

![Create Table comments](img/a13d55bb5ed9398dc20e1199fc090f53.png)

**注意:**要获得表的模式，输入`.schema comments`

现在已经创建了表，我们需要输入数据。

### [插入行](https://www.sqlite.org/lang_insert.html)

假设一个用户输入了一条注释，其中包含以下详细信息:

```
Name    : Shivam Mamgain
Email   : xyz@gmail.com
Website : shivammg.blogspot.com
Comment : Great tutorial for beginners. 
```

为了插入它，我们使用**插入**命令。

```
INSERT INTO comments ( name, email, website_url, comment )
VALUES ( 'Shivam Mamgain', 'xyz@gmail.com',
'shivammg.blogspot.com', 'Great tutorial for beginners.' );
```

我没有提供 *post_id* 的值，尽管它被定义为`NOT NULL`。问题是，它不是必需的，因为它是一个`AUTOINCREMENT`字段。

在表格中输入更多的行进行练习。

### [选择](https://www.sqlite.org/lang_select.html)

我们已经学会了向表中插入数据。为了检索数据，我们使用**选择**命令。

```
SELECT post_id, name, email, website_url, comment
FROM comments;
```

上述语句也可以写成:

```
SELECT *
FROM comments;
```

这将从表中检索所有行。没有标题和适当的列间分隔，结果表可能看起来很混乱。为了克服这一点，我们必须改变一些参数。

输入`.show`

![show](img/5dd2831a00ecc432dc363f5a85ac8394.png)

要显示列属性，输入`.headers ON`。
要以列样式显示行，输入`.mode column`。
进入**再次选择**语句。

![Column Display](img/d68613a70b11671c4982ebf772965eb5.png)

**注意:**选择适合自己的显示模式。输入`.help`，搜索`.mode`。你会看到`.mode`可以拥有的各种价值。

### [更新](https://www.sqlite.org/lang_update.html)

假设“Shivam Mamgain”的*电子邮件*更改为“zyx@email.com”。我们需要更新该行。发布以下声明:

```
UPDATE comments
SET email = 'zyx@email.com'
WHERE name = 'Shivam Mamgain';
```

这将把*名称*‘Shivam mam gain’的*电子邮件*属性更改为‘zyx @ email . com’。

**注意:***名称*属性不是唯一的，因此上述语句可能会影响多行。对于每个*姓名*=‘Shivam mam gain’，*电子邮件*将被设置为‘zyx @ email . com’。要更新特定的行，请使用 *post_id* 属性。它被定义为一个`PRIMARY KEY`，并且永远是唯一的。

### [删除](https://www.sqlite.org/lang_delete.html)

为了从表中删除行，我们可以使用 **WHERE** 条件，就像在 **UPDATE** 中一样。

假设我们需要删除一个带有 *post_id* 9 的行。我们可以输入命令:

```
DELETE FROM comments
WHERE post_id = 9;
```

我们需要删除“巴特·辛普森”和“荷马·辛普森”发布的所有评论。我们可以运行以下查询:

```
DELETE FROM comments
WHERE name = 'Bart Simpson' OR name = 'Homer Simpson';
```

### [改变](https://www.sqlite.org/lang_altertable.html)

可以使用 **ALTER** 将新列添加到表中。在网络上，大多数人不一定使用他们的真实姓名。他们通常用化名工作，一个 T2 用户名 T3。我们需要在表中添加一列*用户名*。以下查询完成了任务:

```
ALTER TABLE comments
ADD COLUMN username TEXT;
```

这将在`comments`中创建一列*用户名*，并将其数据类型设置为文本。对于已经插入的行，*用户名*的值将被设置为空。

**ALTER** 命令也用于重命名表格。让我们将`comments`表改为`Coms`。

```
ALTER TABLE comments
RENAME TO Coms;
```

### [下降](https://www.sqlite.org/lang_droptable.html)

删除一个表意味着删除整个表。可以使用以下查询删除`Coms`表:

```
DROP TABLE Coms;
```

## 结论

与其他关系数据库相比，SQLite3 提供了许多优势。它的一些与众不同的特点可以在这里读到[。大多数 PHP 框架和许多其他 web 框架，包括](http://www.sqlite.org/different.html) [Django](https://www.djangoproject.com/) 、 [Ruby on Rails](http://rubyonrails.org/) 和 [web2py](http://www.web2py.com/) 都将 SQLite3 作为默认数据库。轻量级使得它更适合 web 浏览器中的本地存储。它也被用作许多操作系统的客户端存储，包括 [Android](http://en.wikipedia.org/wiki/Android_%28operating_system%29) 和 [Windows Phone 8](http://en.wikipedia.org/wiki/Windows_Phone_8) 。它是部署最广泛的数据库引擎之一。

在教程中，我们与 SQLite 数据库系统进行了交互。有 GUI 应用程序来做同样的事情，而不需要学习命令。两个这样的应用是用于 SQLite 的 DB 浏览器和 T2 SQLiteStudio。你也可以在[SQL diddle](http://sqlfiddle.com/)在线练习 SQL。

本指南涵盖了 SQLite3 入门的基础知识。我希望它能诱使你走得更远。你可以使用通用的 [PDO 类](http://php.net/manual/en/class.pdo.php)轻松地将它与 PHP 集成。

* * *

1.  [sqlite 版本 3](http://www.sqlite.org/version3.html)↩
2.  **清单类型**解除了对特定字段可以输入的值的类型的许多限制。这允许您向列中输入任何数据类型的任何值，而不管列的声明类型(除了`INTEGER PRIMARY KEY`)。您甚至不需要提供数据类型的最大大小。在用户输入数据后，SQLite 确定它在磁盘上需要的大小。要获得更深入的见解，请参考 SQLite3 中的[数据类型。](https://www.sqlite.org/datatype3.html) [↩](#fnref:manifesttyping "Return to article")
3.  一个`PRIMARY KEY`可以唯一地定义一个特定的行。这样的属性必须始终定义为`NOT NULL`。 [↩](#fnref:primarykey "Return to article")
4.  `AUTOINCREMENT`自动将当前插入记录的相应属性设置为一个数字加上`AUTOINCREMENT`的值(除非指定)。`AUTOINCREMENT`定义的属性必须是`INTEGERS`。 [↩](#fnref:autoincrement "Return to article")

## 分享这篇文章