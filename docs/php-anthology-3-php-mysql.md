# PHP 选集第一卷第三章——PHP 和 MySQL

> 原文：<https://www.sitepoint.com/php-anthology-3-php-mysql/>

 **在今天的网络上，内容才是王道。

凯文·杨克

在互联网的“旧时代”，大多数网页只不过是包含 HTML 的文本文件。当人们浏览您的网站时，您的 Web 服务器只是将文件提供给他们的浏览器，浏览器解析内容并呈现人类可以阅读的内容。这种方法在开始时很好，但是随着网站的增长，设计和导航等问题变得越来越重要，开发人员意识到维护数百个 HTML 文件将是一件非常头疼的事情。为了解决这个问题，将可变内容(文章、新闻等)分开变得流行起来。)从网站的静态元素——它的设计和布局。

使用数据库作为存储可变内容的仓库，PHP 等服务器端语言执行获取数据并将其放入统一“框架”中的任务，设计和布局元素被重用。这意味着修改网站的整体外观和感觉可以作为一项独立于添加或维护内容的任务来处理。突然间，运营一个网站不再是一项耗费开发人员所有时间的任务。

PHP 支持所有值得一提的关系数据库，包括大公司常用的数据库，如 Oracle、IBM 的 DB2 和微软的 SQL Server。两个最值得注意的开源替代方案是 PostgreSQL 和 MySQL。尽管 PostgreSQL 可以说是更好的数据库，因为它支持更多关系数据库的常见特性，但 MySQL 在 Windows 上得到更好的支持，并且是支持 PHP 的 Web 主机中的流行选择。这些因素结合在一起，使得 PHP 和 MySQL 成为非常受欢迎的组合。这本书适合在 PHP 中使用 MySQL，但重要的是要记住，还有完全支持存储过程、触发器和约束等功能的替代方案，其中许多功能对于具有复杂数据结构的应用程序非常重要。

本章涵盖了 PHP 开发人员在使用 MySQL 时必须执行的所有常见操作，从检索和修改数据，到搜索和备份数据库。这些示例主要使用单个表，因此这里不讨论表关系。关于表关系的完整讨论，请参阅 Kevin Yank 的《使用 PHP & MySQL 构建你自己的数据库驱动的网站》( ISBN 0-9579218-1-0 ),或者在《第 1 章，访问控制》中讨论用户组时，看看它们的实际例子。

此处使用的示例使用名为 sitepoint 的示例数据库，该数据库包含以下单个表:

```
Example 3.1\. articles.sql 

CREATE TABLE articles ( 

  article_id INT(11)       NOT NULL AUTO_INCREMENT, 

  title      VARCHAR(255)  NOT NULL DEFAULT '', 

  intro      TEXT          NOT NULL, 

  body       TEXT          NOT NULL, 

  author     VARCHAR(255)  NOT NULL DEFAULT '', 

  published  VARCHAR(11)   DEFAULT NULL, 

  public     ENUM('0','1') NOT NULL DEFAULT '0', 

  PRIMARY KEY (article_id), 

  FULLTEXT KEY art_search (title, body, author) 

)
```

在 sql/articles.sql 文件中的代码档案中提供了构建该表的查询以及一些示例数据。该表将在本书后面的章节中用作示例。

##### 如何访问 MySQL 数据库？

用 PHP 连接 MySQL 再简单不过了。它本质上是一个两步过程；首先连接 MySQL 数据库服务器本身，然后通知 MySQL 你要连接的数据库。

***基本连接***

下面是一个最简单的 MySQL 数据库连接:

```
Example 3.2\. 1.php  

<?php  

$host   = 'localhost'; // Hostname of MySQL server  

$dbUser = 'harryf';    // Username for MySQL  

$dbPass = 'secret';    // Password for user  

$dbName = 'sitepoint'; // Database name  

// Make connection to MySQL server  

if (!$dbConn = mysql_connect($host, $dbUser, $dbPass)) {  

  die('Could not connect to server');  

}  

// Select the database  

if (!mysql_select_db($dbName, $dbConn)) {  

  die('Could not select database');  

}  

echo 'Connection successful!';  

// ... some code here using MySQL  

// Close the connection when finished  

mysql_close($dbConn);  

?>
```

重要的是要记住 MySQL 是一个独立的服务器程序，很像 Apache。两台服务器可能运行在同一台物理计算机上(因此我们在上面的例子中使用了`$host = 'localhost';`),但是也有可能连接到远程计算机上的 MySQL，例如`$host = 'anothercomputer.com';`。更有趣的是，MySQL 也有自己的端口号，默认为 3306。PHP 假定 3306 将是端口号，但是如果你需要使用不同的端口号，你所需要的就是`$host = 'anothercomputer.com:4321';`。

另一个概念上的障碍在于理解一个 MySQL 服务器可能提供对许多数据库的访问，这就是为什么在连接到服务器后需要在 PHP 中选择数据库。

回到上面的代码，有一些事情需要注意。首先，我将连接 MySQL 所需的值放入变量中。这只是让我们的生活更容易；将这类信息存储在每个 PHP 脚本中包含的单独文件中是很常见的，这使得一次更改许多脚本成为可能。一会儿我们将会看到更多让生活变得更简单的技巧。

`mysql_connect`函数负责连接 MySQL 服务器。它返回的值要么是链接标识符(PHP 提供的用于标识连接的值)，要么是`FALSE`，表示连接失败。

```
if (!$dbConn = mysql_connect($host, $dbUser, $dbPass)) {  

  die('Could not connect to server');  

}
```

这个 if 语句提出了一个问题“我成功地连接到 MySQL 服务器了吗？”如果没有，它使用`die`来终止脚本。

接下来，我们使用相同的 if 语句技术，用`mysql_select_db`选择了我们想要的数据库:

```
if (!mysql_select_db($dbName, $dbConn)) {
```

注意，我们将包含链接标识符的变量作为第二个参数提供给了`mysql_select_db`。我们通常不需要这样做(参数是可选的)，但是当一个复杂的脚本处理多个数据库连接时，这个方法可以帮助确保 PHP 知道你指的是哪个。

最后，我们使用`mysql_close`再次断开与服务器的连接:

```
mysql_close($dbConn);
```

一旦我们运行了一些使用该连接的假想 PHP 代码，这就会发生在脚本的底部。关闭连接通常是可选的——PHP 会在脚本完成后自动关闭所有连接。

注意用`mysql_pconnect`做的连接是不同的。这个函数建立了到数据库的持久连接，供多个 PHP 脚本重用。使用持久连接会使您的脚本稍微快一点，因为 PHP 不再需要每次都重新连接，但是速度是有代价的:如果您的网站运行在共享服务器上，持久连接可能会独占该服务器，导致其他站点有时无法连接。在这样的环境中，通常要么避免使用`mysql_pconnect`，要么使用一个短的连接超时值来配置 MySQL，使连接在它们停止做任何事情时被终止。

***可重用代码***

您已经看到了连接 MySQL 的最简单的方法。然而，将上述代码“打包”到一个函数或类中以便重用通常更有用。

作为一个函数，我们可以:

```
Example 3.3\. 2.php   

<?php   

function &connectToDb($host, $dbUser, $dbPass, $dbName)   

{   

  // Make connection to MySQL server   

  if (!$dbConn = @mysql_connect($host, $dbUser, $dbPass)) {   

    return false;   

  }   

  // Select the database   

  if (!@mysql_select_db($dbName)) {   

    return false;   

  }   

  return $dbConn;   

}   

$host   = 'localhost'; // Hostname of MySQL server   

$dbUser = 'harryf';    // Username for MySQL   

$dbPass = 'secret';    // Password for user   

$dbName = 'sitepoint'; // Database name   

$dbConn = &connectToDb($host, $dbUser, $dbPass, $dbName);   

?>
```

这将连接到 MySQL 和选择数据库的过程减少到一行(如果算上 include 语句的话是两行，它将指向一个包含`connectToDb`函数的单独文件):

```
$dbConn = &connectToDb($host, $dbUser, $dbPass, $dbName);
```

注意，我们使用了引用操作符`&`。这个操作符和它所扮演的角色在第 2 章“面向对象的 PHP”中有详细介绍。

懒惰:写好代码！

科学家现在已经最终证明，PHP 知识与空闲时间成反比，但与脱发成正比。防止这些影响的唯一方法是尽早学习如何编写可伸缩、可维护和可重用的代码。利用 PHP 中的类和面向对象是朝着正确方向迈出的一大步。作为一个 PHP 开发者，懒惰是一种美德。

更进一步，我们可以将这段代码包装在一个类中:

```
Example 3.4\. Database/MySQL.php (in SPLIB) (excerpt)   

/**   

 * MySQL Database Connection Class   

 * @access public   

 * @package SPLIB   

 */   

class MySQL {   

  /**   

   * MySQL server hostname   

   * @access private   

   * @var string   

   */   

  var $host;   

  /**   

   * MySQL username   

   * @access private   

   * @var string   

   */   

  var $dbUser;   

  /**   

   * MySQL user's password   

   * @access private   

   * @var string   

   */   

  var $dbPass;   

  /**   

   * Name of database to use   

   * @access private   

   * @var string   

   */   

  var $dbName;   

  /**   

   * MySQL Resource link identifier stored here   

   * @access private   

   * @var string   

   */   

  var $dbConn;   

  /**   

   * Stores error messages for connection errors   

   * @access private   

   * @var string   

   */   

  var $connectError;   

  /**   

   * MySQL constructor   

   * @param string host (MySQL server hostname)   

   * @param string dbUser (MySQL User Name)   

   * @param string dbPass (MySQL User Password)   

   * @param string dbName (Database to select)   

   * @access public   

   */   

  function MySQL($host, $dbUser, $dbPass, $dbName)   

  {   

    $this->host = $host;   

    $this->dbUser = $dbUser;   

    $this->dbPass = $dbPass;   

    $this->dbName = $dbName;   

    $this->connectToDb();   

  }   

  /**   

   * Establishes connection to MySQL and selects a database   

   * @return void   

   * @access private   

   */   

  function connectToDb()   

  {   

    // Make connection to MySQL server   

    if (!$this->dbConn = @mysql_connect($this->host,   

        $this->dbUser, $this->dbPass)) {   

      trigger_error('Could not connect to server');   

      $this->connectError = true;   

    // Select database   

    } else if (!@mysql_select_db($this->dbName,$this->dbConn)) {   

      trigger_error('Could not select database');   

      $this->connectError = true;   

    }   

  }   

  /**   

   * Checks for MySQL errors   

   * @return boolean   

   * @access public   

   */   

  function isError()   

  {   

    if ($this->connectError) {   

      return true;   

    }   

    $error = mysql_error($this->dbConn);   

    if (empty($error)) {   

      return false;   

    } else {   

      return true;   

    }   

  }
```

现在这看起来似乎很难理解，但是最重要的不是类本身是如何编码的(`trigger_error`函数将在“我如何解决 SQL 查询中的错误”一节中讨论)本章后面)，而是你如何使用它。

最重要的是，连接 MySQL 的任务现在简化为:

```
Example 3.5\. 3.php   

<?php   

// Include the MySQL class   

require_once 'Database/MySQL.php';   

$host   = 'localhost'; // Hostname of MySQL server   

$dbUser = 'harryf';    // Username for MySQL   

$dbPass = 'secret';    // Password for user   

$dbName = 'sitepoint'; // Database name   

// Connect to MySQL   

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);   

?>
```

在这里使用类的目的是获得一些使用 PHP 的对象模型来处理常见任务的实践。如果你是 PHP 面向对象编程的新手，在这个阶段要记住的最重要的事情是，你不需要理解你在一个类中找到的所有代码就能在你的代码中使用它。

我们将在整本书中利用这个类和其他类来说明面向对象编程是如何帮助重用代码的，并且在开发应用程序时可以节省时间。

##### 如何从表中获取数据？

当然，连接到数据库是件好事。但是如果我们不能从中得到任何东西，那又有什么好处呢？

从 MySQL 获取数据的方法有很多，但是最广泛使用的可能是结合使用`mysql_fetch_array`和`mysql_query`。

我们只需要在“我如何访问 MySQL 数据库”一节中看到的`connectToDb`函数上增加一点点内容要从此表中提取数据:

```
Example 3.6\. 4.php    

// Connect to MySQL    

$dbConn = &connectToDb($host, $dbUser, $dbPass, $dbName);    

// A query to select all articles    

$sql = "SELECT * FROM articles ORDER BY title";    

// Run the query, identifying the connection    

$queryResource = mysql_query($sql, $dbConn);    

// Fetch rows from MySQL one at a time    

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {    

  echo 'Title: '  . $row['title']  . '<br />';    

  echo 'Author: ' . $row['author'] . '<br />';    

  echo 'Body: '   . $row['body']   . '<br />';    

}
```

基本上，获取数据有三个步骤:

1.  首先，将必要的 SQL 查询放在一个字符串中(如果您不熟悉结构化查询语言(SQL)，我将在本章中介绍基础知识)。然而，要获得更完整的处理，请参考使用 PHP 和 MySQL 构建自己的数据库驱动网站，第二版(ISBN 0-9579218-1-0))，如下所示:

```
$sql = "SELECT * FROM articles ORDER BY title";
```

将它保存在一个单独的变量中是很方便的，因为当我们开始编写更复杂的查询并且出现问题时，我们可以用这个一行程序仔细检查我们的查询:

```
echo $sql;
```

*   接下来，告诉 MySQL 执行查询:

```
$queryResource = mysql_query($sql, $dbConn);
```

这一开始可能会令人困惑。当你告诉 MySQL 执行一个查询时，它不会立即返回结果。相反，它会将结果保存在内存中，直到您告诉它下一步该做什么。PHP 用资源标识符跟踪结果，这是从`mysql_query`函数得到的。在上面的代码中，我们将标识符存储在了`$queryResource`中。

*   最后，使用`mysql_fetch_array`从结果集中一次获取一行:

```
while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC))
```

这将结果的每一行依次放入变量`$row`。这些行中的每一行都将由一个数组表示。通过使用带有`mysql_fetch_array`的额外参数`MYSQL_ASSOC`，我们已经告诉函数给我们一个数组，其中的键对应于表中的列名。如果省略`MYSQL_ASSOC`参数，每列将在数组中出现两次:一次是数字索引(例如`$row[0]`、`$row[1]`等)。)，并且一次用一个字符串索引(即`$row['title']`、`$row['author']`等)。).虽然这通常不会引起问题，但是指定您想要的数组值的类型会稍微加快速度。

如上所示，使用 while 循环是依次处理结果集每一行的常用方法。该循环实际上是在说，“继续从 MySQL 获取行，直到我无法再获取更多的行为止”，循环体在获取行时处理这些行。

*放弃大型查询的缓冲*

*对于大型查询(即产生大型结果集的查询)，您可以通过告诉 PHP 不要缓冲查询结果来显著提高性能。当一个查询被缓冲时，整个结果集从 MySQL 中检索出来并存储在内存中，然后才允许脚本继续执行。另一方面，无缓冲查询让 MySQL 保存结果，直到您请求它们，一次一行(例如用`mysql_fetch_array`)。这不仅允许您的脚本在 MySQL 执行查询时继续运行，还让 PHP 不必一次将所有行存储在内存中。*

PHP 允许您使用`mysql_unbuffered_query`执行无缓冲查询:

```
$queryResource = **mysql_unbuffered_query**($sql, $dbConn);
```

当然，所有好东西都是有代价的——对于无缓冲查询，您不能再使用`mysql_num_rows`函数来计算行数。显然，由于 PHP 没有保存完整结果集的副本，所以无法计算它包含的行数！在进行另一个查询之前，您还必须从 MySQL 获取结果集中的所有行。

尽管有其他函数可以从查询结果中获取行和单元格，比如`mysql_fetch_object`和`mysql_result`，但你只需使用`mysql_fetch_array`就可以实现或多或少相同的功能，这种一致性有助于保持代码的简单。

***用类抓取***

既然您已经对从 MySQL 获取数据的基础感到满意，那么是时候在上一个解决方案的 MySQL 类的基础上构建更多内容了。

首先，让我们添加一个从类中运行查询的方法:

```
Example 3.7\. Database/MySQL.php (in SPLIB) (excerpt)     

  /**     

   * Returns an instance of MySQLResult to fetch rows with     

   * @param $sql string the database query to run     

   * @return MySQLResult     

   * @access public     

   */     

  function &query($sql)     

  {     

    if (!$queryResource = mysql_query($sql, $this->dbConn)) {     

      trigger_error('Query failed: ' . mysql_error($this->dbConn)     

                    . ' SQL: ' . $sql);     

      return new MySQLResult($this, $queryResource);     

    }     

  }
```

这个新方法所做的是接受一个包含 SQL 语句的变量，运行它，然后从另一个类`MySQLResult`(如下所述)构建一个新对象。然后，它将该对象返回到调用 query 的位置。

下面是这个新类的代码，`MySQLResult`:

```
Example 3.8\. Database/MySQL.php (in SPLIB) (excerpt)     

/**     

 * MySQLResult Data Fetching Class     

 * @access public     

 * @package SPLIB     

 */     

class MySQLResult {     

  /**     

   * Instance of MySQL providing database connection     

   * @access private     

   * @var MySQL     

   */     

  var $mysql;     

  /**     

   * Query resource     

   * @access private     

   * @var resource     

   */     

  var $query;     

  /**     

   * MySQLResult constructor     

   * @param object mysql   (instance of MySQL class)     

   * @param resource query (MySQL query resource)     

   * @access public     

   */     

  function MySQLResult(&$mysql, $query)     

  {     

    $this->mysql = &$mysql;     

    $this->query = $query;     

  }     

  /**     

   * Fetches a row from the result     

   * @return array     

   * @access public     

   */     

  function fetch()     

  {     

    if ($row = mysql_fetch_array($this->query, MYSQL_ASSOC)) {     

      return $row;     

    } else if ( $this->size() > 0 ) {     

      mysql_data_seek($this->query, 0);     

      return false;     

    } else {     

      return false;     

    }     

  }     

  /**     

   * Checks for MySQL errors     

   * @return boolean     

   * @access public     

   */     

  function isError()     

  {     

    return $this->mysql->isError();     

  }     

}
```

现在，再屏住呼吸一会儿，直到你看到使用这些类是什么样子:

```
Example 3.9\. 5.php     

<?php     

// Include the MySQL class     

require_once 'Database/MySQL.php';     

$host   = 'localhost'; // Hostname of MySQL server     

$dbUser = 'harryf';    // Username for MySQL     

$dbPass = 'secret';    // Password for user     

$dbName = 'sitepoint'; // Database name     

// Connect to MySQL     

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);     

$sql = "SELECT * FROM articles ORDER BY title";     

// Perform a query getting back a MySQLResult object     

$result = $db->query($sql);     

// Iterate through the results     

while ($row = $result->fetch()) {     

  echo 'Title: '  . $row['title']  . '<br />';     

  echo 'Author: ' . $row['author'] . '<br />';     

  echo 'Body: '   . $row['body']   . '<br />';     

}     

?>
```

如果你不习惯面向对象编程，这可能看起来很混乱，但是最重要的是集中在你如何使用这些类，而不是隐藏在它们里面的细节。一旦你习惯了，这就是面向对象编程的乐趣之一。代码在幕后可能会变得非常复杂，但是您需要关心的只是代码使用该类的简单“接口”(API)。

*关于 API*

经常听到在班级周围提到 API 这个术语。API 代表应用程序编程接口。它所指的是一组方法，作为一个类中包含的功能的“门”。一个设计良好的 API 将允许该类的开发人员在不破坏任何使用该类的代码的情况下在幕后进行彻底的更改。

将使用 MySQL 类与早期的过程代码进行比较；应该很容易看出相似之处。既然如此相似，你可能会问，“为什么不坚持使用简单的、过程化的 PHP 呢？”在这种情况下，它隐藏了许多与执行查询相关的细节。管理连接、捕捉错误以及决定以何种格式获取查询结果等任务都由类在幕后处理。类还使得全局修改的实现(比如从 MySQL 切换到 PostgreSQL)相对容易(也就是说，您可以切换到提供相同 API 的`PostgreSQL`类)。

##### 如何解决 SQL 查询中的错误？

如果当您试图一起处理 PHP 和 SQL 时出现问题，通常很难找到原因。诀窍是让 PHP 告诉你问题出在哪里，记住，当网站上线时，你必须能够对访问者隐藏这些信息。

PHP 提供了`mysql_error`函数，该函数返回上次执行 MySQL 操作的详细错误信息。

最好和`trigger_error`函数一起使用(这将在第 10 章，错误处理中详细讨论)，它允许你控制错误信息的输出。让我们修改前面看到的基本连接代码:

```
Example 3.10\. 6.php (excerpt)      

// Make connection to MySQL server      

if (!$dbConn = mysql_connect($host, $dbUser, $dbPass)) {      

  trigger_error('Could not connect to server: ' **. mysql_error());**      

  die();      

}      

// Select the database      

if (!mysql_select_db($dbName)) {      

  trigger_error('Could not select database: ' **. mysql_error());**      

  die();      

}
```

同样的方法也可以用于查询:

```
Example 3.11\. 6.php (excerpt)      

// A query to select all articles      

$sql = "SELECT * FROM articles ORDER BY title";      

// Run the query, identifying the connection      

if (!$queryResource = mysql_query($sql, $dbConn)) {      

  trigger_error('Query error ' . mysql_error() . ' SQL: ' . $sql);      

}
```

正如我们在上面的例子中所做的那样，返回完整的查询本身是一个好主意，特别是当您使用 PHP 变量构建查询时。这允许您确切地看到执行了什么查询，并且如果必要的话，直接针对 MySQL 执行它，以准确地确定哪里出错了。

上面讨论的 MySQL 类在遇到问题时会自动使用`mysql_error`和`trigger_error`。

##### 如何添加或修改数据库中的数据？

能够从数据库中获取数据是一个开始，但是如何将数据放在第一位呢？

同样，对于 PHP 来说，答案很简单:使用结合了 SQL 命令 INSERT 和 UPDATE 的`mysql_query`函数。INSERT 用于在表中创建新行，而 UPDATE 用于修改现有行。

***插入一行***

一个简单的插入，使用本章开头定义的文章表，如下所示:

```
Example 3.12\. 7.php (excerpt)      

// A query to INSERT data      

$sql = "INSERT INTO      

          articles      

        SET      

          title  = '$title',      

          body   = '$body',      

          author = '$author'";      

// Run the query, identifying the connection      

if (!$queryResource = mysql_query($sql, $dbConn)) {      

  trigger_error('Query error ' . mysql_error() . ' SQL: ' . $sql);      

}
```

***更新一行***

在使用更新查询之前，您需要能够识别表中要更新的行。在本例中，我使用了一个选择查询来获取标题为“如何插入数据”的文章的唯一的`article_id`值:

```
Example 3.13\. 8.php (excerpt)      

// A query to select an article      

$sql = "SELECT article_id FROM articles      

        WHERE title='How to insert data'";      

if (!$queryResource = mysql_query($sql, $dbConn)) {      

  trigger_error('Query error ' . mysql_error() . ' SQL: ' . $sql);      

}      

// Fetch a single row from the result      

$row = mysql_fetch_array($queryResource, MYSQL_ASSOC);      

// A new title      

$title = 'How to update data';      

$sql = "UPDATE      

          articles      

        SET      

          title='$title'      

        WHERE      

          article_id='" . $row['article_id'] . "'";      

if (!$queryResource = mysql_query($sql, $dbConn)) {      

  trigger_error('Query error ' . mysql_error() . ' SQL: ' . $sql);      

}
```

在上面的例子中，我们使用 SELECT 查询来查找我们想要更新的行的 ID。

在实际的 Web 应用程序中，更新可能发生在依赖于来自 Web 浏览器的输入的页面上，例如，在用户使用 HTML 表单输入值之后。该数据中的字符串可能包含撇号，这会破坏 SQL，并影响安全性。有鉴于此，请务必阅读“如何解决由引号/撇号引起的数据库错误？”，报道 SQL 注入袭击事件。

*注意全局更新
注意更新，并记住使用 WHERE 子句来指示要更改哪些行。*

例如，考虑以下查询:

```
UPDATE articles SET title = 'How NOT to update data'
```

这将更新表格的每一行！

***另一个集体诉讼***

使用上次在“如何从表中提取数据”一节中看到的 MySQL 类，我们可以执行插入和更新查询，而无需任何进一步的修改。使用类重复上面的例子，我们可以首先像这样插入:

```
Example 3.14\. 9.php (excerpt)      

// Connect to MySQL      

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);      

$title  = 'How to insert data';      

$body   = 'This is the body of the article';      

$author = 'HarryF';      

// A query to INSERT data      

$sql = "INSERT INTO      

          articles      

        SET      

          title  = '$title',      

          body   = '$body',      

          author = '$author'";      

$db->query($sql);      

if (!$db->isError()) {      

  echo 'INSERT successful';      

} else {      

  echo 'INSERT failed';      

}
```

我们可以更新如下:

```
Example 3.15\. 10.php (excerpt)      

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);      

// A query to select an article      

$sql = "SELECT article_id FROM articles      

        WHERE title='How to insert data'";      

$result = $db->query($sql);      

$row = $result->fetch();      

// A new title      

$title = 'How to update data';      

$sql = "UPDATE      

          articles      

        SET      

          title='" . $title. "'      

        WHERE      

          article_id='" . $row['article_id'] . "'";      

$db->query($sql);      

if (!$db->isError()) {      

  echo 'UPDATE successful';      

} else {      

  echo 'UPDATE failed';      

}
```

##### 如何解决由引号/撇号引起的数据库错误？

考虑以下 SQL 语句:

```
INSERT INTO articles SET title='The PHP Anthology';
```

也许生成这个查询的 PHP 脚本包含类似这样的内容:

```
<?php       

$title = "The PHP Anthology";       

$sql = "INSERT INTO articles SET title='$title';";       

$result = mysql_query($sql, $dbConn);       

?>
```

到目前为止没有问题，但是看看如果我们改变标题会发生什么:

```
$title = "PHP's Greatest Hits";
```

注意到标题中的撇号了吗？当我们将它放入 SQL 语句中时，MySQL 收到的查询如下:

```
INSERT INTO articles SET title='PHP's Greatest Hits';
```

看到问题了吗？当 MySQL 读到这条语句时，它只能做到这一步:

```
INSERT INTO articles SET title='PHP'
```

语句的其余部分将导致语法错误，查询将失败。当您自己编写标题时，很容易避免这个问题，但是当您的脚本从用户输入中获取值时会发生什么呢？

***大逃杀***

解决方法是通过在撇号前添加一个反斜杠来转义撇号字符。例如，以下查询将起作用:

```
INSERT INTO articles SET title='PHP's Greatest Hits';
```

*反斜杠和 ANSI SQL 标准*

注意，使用反斜杠作为转义字符不是标准的 ANSI SQL。如果 MySQL 是您将使用的唯一数据库，反斜杠可能是可以接受的，但是在另一个数据库上运行相同的 SQL 语句很可能会失败。根据 ANSI SQL，我们应该用另一个撇号来转义撇号:

```
INSERT INTO articles SET title='PHP''s Greatest Hits';
```

问题是，当我们在 PHP 中动态构建查询时，如何确保所有的撇号都被转义？由于备选解决方案的数量，处理这种情况变得相当混乱。

首先我们有 php.ini 设置`magic_quotes_gpc`。Magic quotes 是 PHP 的一个特性，当它打开时，通过在字符串中添加反斜杠，可以自动转义单引号和双引号，以及传入的 GET、POST 和 cookie 变量中的反斜杠和空字符。这听起来可能很棒，但实际上很快就会带来麻烦，特别是在涉及表单的地方。

假设您有一个用于编辑文章的表单。您的脚本获取用户输入的文本，并将其插入 MySQL。现在，如果用户没有完成一些重要的字段，您可能希望重新显示到目前为止已经在表单中输入的详细信息。有了神奇的引号，你必须去掉所有加在值上的斜线(用 PHP 的 stripslashes 函数)！

那么，如果您想在禁用了`magic_quotes_gpc`的服务器上运行代码呢？然后，您的代码必须检查 magic quotes 是否打开，并绕过条纹斜线的使用。头痛是不可避免的，如果您犯了一个错误，并以存储在数据库中的虚假反斜杠结束，您可能会有一个痛苦的清理过程。我一直很惊讶有多少专业设计的网站不能正确处理字符转义！留意你自己的网络旅行中意想不到的反弹。参见第 1 章，PHP 基础，我的建议是如何在你自己的网站上最好地避免这种情况。

事实上，魔术报价在第 1 章，PHP 基础知识中有详细的讨论。如果你按照我的建议关掉电源，你应该意识到潜在的安全风险。请参阅下面的“SQL 注入攻击”一节和附录 C，安全检查表。

接下来，我们有 PHP 函数`addslashes`。应用于任何字符串，`addslashes`将使用反斜杠对单引号、双引号、反斜杠和空字符进行转义。这使它成为转义字符串以便在查询中使用的有效方法。

当然，如果 magic quotes 打开，你一定不能使用`addslashes`，否则字符会被转义两次！为了解决这个冲突，您可以使用函数`get_magic_quotes_gpc`检查 magic quotes 是否被启用，如果 magic quotes 被启用，它将返回 TRUE，否则返回 FALSE。您可以将该测试与如下函数捆绑在一起:

```
<?php       

function safeAddSlashes($string)       

{       

  if (get_magic_quotes_gpc()) {       

    return $string;       

  } else {       

    return addslashes($string);       

  }       

}       

?>
```

第三种方式与`addslashes`非常相似，使用函数`mysql_escape_string`或`mysql_real_escape_string`(后者是 PHP 4.3.0 中添加的)。这些函数使用底层的 MySQL C++ API(由 MySQL 的开发人员提供，而不是 PHP 的开发人员)来转义特殊字符。

这些函数对空字符、换行符、回车符、反斜杠、单引号、双引号和文件尾字符进行转义。从 PHP 4.3.0 开始，这两个函数都使用当前的字符集来连接 PHP 和 MySQL。因此，在最新的 PHP 版本中，这两个函数之间没有区别，所以如果您的服务器是最新的，请随意使用这两个函数中较短的那个`mysql_escape_string`。

由于这种方法实际上是 MySQL 为转义字符串提供的内置机制，我推荐它而不是`addslashes`或`magic_quotes_gpc`。当然，如果您希望您的 SQL 很好地移植到其他数据库，您可能希望考虑将函数调用“隐藏”在一个类方法中，这允许您在移动到不同的数据库时交换出该类(包括转义机制)。

同样，如果你不处理魔术引号的问题，你必须检查`magic_quotes_gpc`是否开启:

```
<?php       

function safeEscapeString($string)       

{       

  if (get_magic_quotes_gpc()) {       

    return $string;       

  } else {       

    return mysql_real_escape_string($string);       

  }       

}       

?>
```

本书中的脚本使用了第 1 章 php 基础中介绍的 MagicQuotes/strip _ quotes . PHP include 文件，该文件包含在代码归档中，可以有效地关闭启用了 magic quotes 的服务器上的 magic quotes，因此后面的解决方案将可以自由地使用`mysql_real_escape_string`。我鼓励你在自己的脚本中做同样的事情，如果你有信心自己避开引号和其他特殊字符的话。

***SQL 注入攻击***

当攻击者利用您站点上的合法用户输入机制发送 SQL 代码时，就会发生 SQL 注入攻击，您毫不怀疑的脚本会将这些代码传递给数据库来执行。黄金法则:在让数据靠近数据库之前，避开所有来自外部来源的数据。该规则不仅适用于插入和更新查询，也适用于选择查询。

毫无疑问，由于 MySQL 的局限性，许多 PHP 开发人员已经从最糟糕的 SQL 注入攻击中幸免于难，MySQL 每次调用`mysql_query`时只允许执行一条 SQL 语句。在其他数据库上，SQL 注入的影响可能是灾难性的，因为攻击者可以发送第二个查询，例如，删除表的全部内容。然而，使用 MySQL 仍然会出现问题，如以下代码所示:

```
$sql = "SELECT * FROM users        

        WHERE username='" . $_POST['username'] . "'       

        AND password='" . $_POST['password'] . "'";       

echo 'Query: ' . $sql . '<br />';       

$result = mysql_query($sql);       

$rows = mysql_num_rows($result);       

if ($rows > 0) {       

  echo 'You are logged in!<br />';       

} else {       

  echo 'You are not allowed here!<br />';       

}       

?>       

<form method="post" action="<?php echo $_SERVER['PHP_SELF']; ?>">       

<input type="text" name="username" /><br />       

<input type="text" name="password" /><br />       

<input type="submit" />       

</form>
```

精明的攻击者只需在表单的密码字段中输入以下内容:

```
' OR username LIKE '%
```

假设在您的服务器上禁用了 magic quotes，并且您没有其他措施来防止它，这种巧妙的攻击改变了查询的含义:

```
SELECT * FROM users       

WHERE username='' AND password='' OR username LIKE '%'
```

修改后的查询将选择用户表中的所有记录！当脚本检查是否有任何用户匹配提供的用户名和密码组合时，它将看到这个大的结果集并授予对站点的访问权限！

如果我们对传入的变量进行转义，这是可以避免的:

```
$sql = "SELECT * FROM users        

    WHERE username='" . safeEscapeString($_POST['username']) . "'       

    AND password='" . safeEscapeString($_POST['password']) . "'";
```

在某些情况下，根据具体情况，这可能是不必要的。但是如果你重视你的睡眠，记住黄金法则:避开所有来自外部来源的数据。

##### 如何创建灵活的 SQL 语句？

SQL 是处理数据的强大语言。使用 PHP，我们可以用变量构造 SQL 语句，这对于按单个列对表进行排序或者跨多个页面显示大型结果集非常有用。

下面是一个简单的示例，让我们按表列对查询结果进行排序:

```
Example 3.16\. 11.php (excerpt)        

// A query to select all articles        

$sql = "SELECT * FROM articles";        

// Initialize $_GET['order'] if it doesn't exist        

if (!isset($_GET['order']))        

  $_GET['order'] = FALSE;        

// Use a conditional switch to determine the order        

switch ($_GET['order']) {        

  case 'author':        

    // Add to the $sql string        

    $sql .= " ORDER BY author";        

    break;        

  default:        

    // Default sort by title        

    $sql .= " ORDER BY title";        

    break;        

}        

// Run the query, identifying the connection        

if (!$queryResource = mysql_query($sql, $dbConn)) {        

  trigger_error('Query error ' . mysql_error() . ' SQL: ' . $sql);        

}        

?>        

<table>        

<tr>        

<th><a href="<?php echo $_SERVER['PHP_SELF']; ?>?order=title"        

  >Title</a></th>        

<th><a href="<?php echo $_SERVER['PHP_SELF']; ?>?order=author"        

  >Author</a></th>        

</tr>        

<?php        

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {        

  echo "<tr>n";        

  echo "<td>" . $row['title'] . "</td>";        

  echo "<td>" . $row['author'] . "</td>";        

  echo "</tr>n";        

}        

?>        

</table>
```

在 switch 语句中，我根据脚本从浏览器接收的 GET 变量“动态”生成了 SQL 语句的一部分。

这种通用方法可以扩展到 WHERE 子句、LIMIT 子句以及任何您想用 SQL 考虑的内容。当我们在《第 9 章，网页元素》中构造一个分页结果集时，我们将更详细地讨论这个问题。

***持久层:没有 SQL 的数据库交互***

持久层变得越来越流行，并且在今天的 PHP 中得到了很好的支持。持久层是表示数据库中的表的类的集合，为您提供了一个 API，数据库和 PHP 应用程序之间交换的所有数据都通过它传递。这通常消除了您手动编写 SQL 语句的需要，因为查询是由表示数据的 PHP 类自动生成和执行的。

因为 SQL 是一个定义良好的标准，所以自动生成持久层也成为可能。一个程序可以检查你的数据库模式并产生自动读取和更新它的类。这可以节省大量时间；简单地设计你的数据库，运行代码生成工具，剩下的“只是”格式化一点点(X)HTML 的问题。

持久层的一个主要例子是 PEAR::DB_DataObject，它构建在 PEAR::DB 数据库抽象库之上，并自动生成一个类层，用于访问表。

持久层，特别是 PEAR::DB_DataObject，将在“我真的需要编写 SQL 吗？”一节中讨论。

##### 如何知道我选择了多少行？

在对查询进行任何操作之前，能够计算查询返回的行数通常是很有用的，例如在跨页面拆分结果或生成统计信息时。选择结果时，可以使用 PHP 或 MySQL 来计算行数。

***用 PHP 计算行数***

在 PHP 中，函数`mysql_num_rows`返回选中的行数，但是当使用无缓冲查询时，它的应用可能会受到限制(参见“如何从表中提取数据”一节)).下面的代码说明了`mysql_num_rows`的用法:

```
Example 3.17\. 12.php (excerpt)        

// A query to select all articles        

$sql = "SELECT * FROM articles ORDER BY title";        

// Run the query, identifying the connection        

$queryResource = mysql_query($sql, $dbConn);        

// Fetch the number of rows selected        

$numRows = mysql_num_rows($queryResource);        

echo $numRows . ' rows selected<br />';        

// Fetch rows from MySQL one at a time        

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {        

    echo 'Title: '  . $row['title']  . '<br />';        

    echo 'Author: ' . $row['author'] . '<br />';        

    echo 'Body: '   . $row['body']   . '<br />';        

}
```

上例中演示的`mysql_num_rows`函数获取一个结果集资源标识符，并返回该结果集中的行数。

请注意，相关函数`mysql_num_fields`可用于找出选择了多少列。当您使用像 SELECT * FROM table 这样的查询，但是您不知道已经选择了多少列时，这是很方便的。

***用 MySQL 计算行数***

另一种方法是在查询中使用 MySQL 的 COUNT 函数。这要求您执行两个查询——一个用于计算结果，另一个用于实际获取结果——这将会在性能方面有所损失。

以下是如何使用 MySQL `COUNT`函数:

```
Example 3.18\. 13.php (excerpt)         

// A query to select all articles         

$sql = "SELECT COUNT(*) AS numrows FROM articles";         

// Query to count the rows returned         

$queryResource = mysql_query($sql, $dbConn);         

$row = mysql_fetch_array($queryResource, MYSQL_ASSOC);         

echo $row['numrows'] . " rows selected<br />";         

// A query to select all articles         

$sql = "SELECT * FROM articles ORDER BY title";         

// Run the query, identifying the connection         

$queryResource = mysql_query($sql, $dbConn);         

// Fetch rows from MySQL one at a time         

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {         

  echo 'Title: '  . $row['title']  . '<br />';         

  echo 'Author: ' . $row['author'] . '<br />';         

  echo 'Body: '   . $row['body']   . '<br />';         

}
```

注意我们使用了一个别名来放置`COUNT`函数的结果。

```
SELECT COUNT(*) AS numrows FROM articles
```

我们这样做是为了以后可以使用`$row['numrows']`识别行数。另一种方法是省略别名:

```
SELECT COUNT(*) FROM articles
```

这将要求我们以`$row['COUNT(*)']/#epc#/, which can make the code confusing to read.`的身份访问信息

当我们使用`COUNT`函数时，动态构造查询变得很重要，正如我们在“如何创建灵活的 SQL 语句”一节中看到的那样。您需要确保您的`COUNT`查询包含您在“真实”查询中使用的相同的`WHERE`或`LIMIT`子句。例如，如果我们实际用来获取数据的查询是:

```
SELECT * FROM articles WHERE author='HarryF'
```

在 PHP 中，我们可能需要这样的东西:

```
Example 3.19\. 14.php (excerpt)         

// Define reusable "chunks" of SQL         

$table = " FROM articles";         

$where = " WHERE author='HarryF'";         

$order = " ORDER BY title";         

// Query to count the rows returned         

$sql = "SELECT COUNT(*) as numrows" . $table . $where;         

// Run the query, identifying the connection         

$queryResource = mysql_query($sql, $dbConn);         

$row = mysql_fetch_array($queryResource, MYSQL_ASSOC);         

echo $row['numrows'] . " rows selected<br />";         

// A query to fetch the rows         

$sql = "SELECT * " . $table . $where . $order;         

// Run the query, identifying the connection         

$queryResource = mysql_query($sql, $dbConn);         

// Fetch rows from MySQL one at a time         

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {         

    echo 'Title: '  . $row['title']  . '<br />';         

    echo 'Author: ' . $row['author'] . '<br />';         

    echo 'Body: '   . $row['body']   . '<br />';         

}
```

***行计数与类***

让我们再来看看我们在本节中开发的类。通过向 MySQLResult 类引入以下方法，我们可以添加找出所选行数的功能:

```
Example 3.20\. Database/MySQL.php (in SPLIB) (excerpt)         

  /**         

   * Returns the number of rows selected         

   * @return int         

   * @access public         

   */         

  function size()         

  {         

    return mysql_num_rows($this->query);         

  }
```

下面是它的使用方法:

```
Example 3.21\. 15.php (excerpt)         

// Connect to MySQL         

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);         

// Select all results for a particular author         

$sql = "SELECT * FROM articles WHERE author='HarryF'";         

$result = $db->query($sql);         

echo "Found " . $result->size() . " rows";
```

***统计受影响的行***

使用 PHP 函数`mysql_affected_rows`，还可以找出有多少行受到了`UPDATE`、`INSERT`或`DELETE`查询的影响。在典型的 PHP 应用程序中使用`mysql_affected_rows`并不常见，但这可能是通知用户“您刚刚从 Customers 表中删除了 1854 条记录”的好方法。祝你愉快！”

与将结果集资源标识符作为参数的`mysql_num_rows`不同，`mysql_affected_rows`将数据库连接标识符作为参数。对于指定的连接，它返回受上次修改数据库的查询影响的行数。

下面是如何使用`mysql_affected_rows`:

```
Example 3.22\. 16.php (excerpt)          

// Connect to MySQL          

$dbConn = &connectToDb($host, $dbUser, $dbPass, $dbName);          

// A query which updates the database          

$sql = "UPDATE           

          articles          

        SET          

          author='The Artist Formerly Known as...'          

        WHERE          

          author='HarryF'";          

// Run the query, identifying the connection          

$queryResource = mysql_query($sql, $dbConn);          

// Fetch the number of rows affected          

$changedRows = mysql_affected_rows($dbConn);          

echo $changedRows . ' rows changed<br />';
```

由于需要`mysql_affected_rows`的情况并不常见，为了简单起见，我将从`MySQLResult`类中省略它。

##### 插入一行后，如何找出它的行号？

当您处理数据库表中的`AUTO_INCREMENT`列时，能够找出您刚刚插入的行的 ID 通常是有用的，这样其他表就可以用该信息进行更新。毕竟，表之间的关系就是这样建立的。PHP 提供了函数`mysql_insert_id`，当给定一个链接标识符时，该函数返回通过该连接执行的最后一次插入所生成的 ID。下面是如何使用`mysql_insert_id`:

```
Example 3.23\. 17.php (excerpt)          

// A query to insert a row          

$sql = "INSERT INTO           

          articles          

        SET          

          title='How to use mysql_insert_id()',          

          body='This is an example',          

          author='HarryF'";          

// Run the query, identifying the connection          

$queryResource = mysql_query($sql, $dbConn);          

// Fetch the inserted ID          

$insertID = mysql_insert_id($dbConn);          

echo 'The new row has ID: ' . $insertID;
```

***班级插入 ID***

要在我们的`MySQLResult`类中使用这个功能，添加下面的方法:

```
Example 3.24\. Database/MySQL.php (in SPLIB) (excerpt)          

  /**          

   * Returns the ID of the last row inserted          

   * @return int          

   * @access public          

   */          

  function insertID()          

  {          

    return mysql_insert_id($this->mysql->dbConn);          

  }
```

正如您可能猜到的，使用这种方法非常简单:

```
Example 3.25\. 18.php (excerpt)          

// Include the MySQL class          

require_once 'Database/MySQL.php';          

$host   = 'localhost';   // Hostname of MySQL server          

$dbUser = 'harryf';    // Username for MySQL          

$dbPass = 'secret';    // Password for user          

$dbName = 'sitepoint'; // Database name          

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);          

// A query to insert a row          

$sql="INSERT INTO           

        articles          

      SET          

        title='How to use mysql_insert_id()',          

        body='This is an example',          

        author='HarryF'";          

$result = $db->query($sql);          

echo 'The new row as ID: ' . $result->insertID();
```

##### 如何搜索我的表？

有些人只是不耐烦；他们不是用你提供的友好的导航系统搜索你的网站，而是现在就要求信息！因此，像你我这样的 PHP 开发人员需要实现搜索功能，为访问者提供找到他们想要的信息的“捷径”。在以 HTML 文件的形式存储所有内容的时代，这可能是一个相当大的问题，但是现在你使用数据库来存储内容，搜索变得容易得多。

***选择你喜欢的***

最基本的搜索形式是针对单个列，使用 LIKE 运算符:

```
SELECT * FROM articles WHERE title LIKE 'How %'
```

`%`是一个通配符。上述语句将选择标题以单词“How”开头的所有文章 MySQL 也支持 POSIX 正则表达式(和 PHP 的 ereg 函数一样)。使用`RLIKE`操作符，我们可以使用一个正则表达式来比较一个列:

```
SELECT * FROM articles WHERE title RLIKE '^How '

The above statement also selects every article in which the title begins with "How" followed by a space.
```

通过一些工作，这些操作符提供了探索数据所需的一切。上述方法成为负担的地方在于对多个列执行搜索。举个例子，

```
SELECT * FROM articles           

WHERE title LIKE '%how%' OR body LIKE '%how%'
```

对于较大的表，这可能需要您编写一些非常复杂和令人不快的查询。

***全文搜索***

MySQL 提供了一个替代方案来为您完成大部分工作——`FULLTEXT`索引。数据库中的索引很像一本书的索引；它们提供了一种从有组织的列表中快速定位数据库中的信息的方法。一个`FULLTEXT`索引允许你在一个表格中搜索特定的单词。

```
FULLTEXT indexes were introduced to MySQL with version 3.23\. The implementation at this point was fairly limited but still useful for basic searching, which is what I'll demonstrate here. In MySQL version 4.0.1, this functionality was extended to provide a full Boolean search mechanism that gives you the ability to build something like Googleâ„¢'s advanced search features. FULLTEXT indexes also allow each result to be returned with a "relevance" value so that, for example, the results of multiple word searches can be displayed in terms of how well each result matches that user's particular search.
```

为了利用`FULLTEXT`索引，您首先需要指示 MySQL 开始构建您想要搜索的列的索引:

```
ALTER TABLE articles ADD FULLTEXT art_search (title, body, author)
```

完成后，您需要插入一条新记录(或修改现有记录)来让 MySQL 构建索引。您还需要数据库中至少有三条记录才能使`FULLTEXT`搜索起作用，因为非布尔搜索只有在搜索字符串出现在表中少于 50%的行中时才会返回结果(如果表中只有两行，并且您的搜索匹配一行，则匹配 50%)。需要注意的最后一件事是`FULLTEXT`搜索只匹配超过三个字母的搜索；索引机制忽略三个字符或更少的单词，以避免必须建立大量索引。这很像一本书的索引；在一本书的索引中，你会非常惊讶地发现单词“the”出现在了哪几页上！

这里有一个基本的`FULLTEXT`搜索:

```
SELECT * FROM articles           

WHERE MATCH (title,body,author) AGAINST ('MySQL');
```

该搜索将返回标题、正文或作者包含单词“MySQL”的所有行

`FULLTEXT`索引的另一个用途是在搜索中返回每个结果的相关性。例如:

```
Example 3.26\. 19.php (excerpt)           

// Select all rows but display relvance           

$sql = "SELECT           

          *, MATCH (title, body, author)           

        AGAINST           

          ('The PHP Anthology Released Long Word Matching')           

        AS           

          score           

        FROM           

          articles           

        ORDER BY score DESC";           

// Run the query, identifying the connection           

$queryResource = mysql_query($sql, $dbConn);           

// Fetch rows from MySQL one at a time           

while ($row = mysql_fetch_array($queryResource, MYSQL_ASSOC)) {           

  echo 'Title: '  . $row['title']  . '<br />';           

  echo 'Author: ' . $row['author'] . '<br />';           

  echo 'Body: '   . $row['body']   . '<br />';           

  echo 'Score: '  . $row['score']  . '<br />';           

}
```

别名分数现在包含一个值，用于标识该行与搜索的相关程度。该值不是一个百分比，而仅仅是一个度量；0 表示根本没有匹配。匹配单个单词将产生大约 1 的值。匹配的单词越多，数字就越大，因此五个单词的匹配排名将产生大约 13 的相关性分数。MySQL 的关联算法是为大型表设计的，所以你拥有的数据越多，关联值就变得越有用。

总的来说，MySQL 的`FULLTEXT`搜索功能提供了一种易于实现并提供有用结果的机制。

##### 如何备份我的数据库？

数据库变得越大，没有包含数据的备份就越让人头疼。如果你的服务器崩溃了，所有东西都丢失了怎么办？幸运的是，MySQL 提供了两种选择:一个名为`mysqldump`的命令行实用程序，和一个用于备份表的查询语法。

下面是如何使用`mysqldump`从命令行导出数据库的内容:

```
mysqldump -uharryf -psecret sitepoint > sitepoint.sql
```

该命令将以用户“harryf”(`-uharryf`)的身份使用密码“secret”(`-psecret`)登录 mysql，并将 sitepoint 数据库的内容输出到一个名为 sitepoint.sql 的文件中。sitepoint.sql 的内容将是一系列可以针对 MySQL 运行的查询，可能使用 MySQL 实用程序从命令行执行相反的操作:

```
mysql -uharryf -psecret sitepoint < sitepoint.sql
```

使用 PHP 函数系统，您可以从 PHP 脚本中执行上述命令(这需要您登录并能够从命令行执行 PHP 脚本)。下面的类将所有这些放在一个方便的 PHP 表单中，您可以用它来定期备份您的站点。

```
Example 3.27\. Database/MySQLDump.php (in SPLIB)            

/**            

 * MySQLDump Class<br />            

 * Backs up a database, creating a file for each day of the week,            

 * using the mysqldump utility.<br />            

 * Can compress backup file with gzip of bzip2<br />            

 * Intended for command line execution in conjunction with            

 * cron<br />            

 * Requires the user executing the script has permission to execute            

 * mysqldump.            

 * <code>            

 * $mysqlDump = new MySQLDump('harryf', 'secret', 'sitepoint',            

 *                            '/backups');            

 * $mysqlDump->backup();            

 * </code>            

 * @access public            

 * @package SPLIB            

 */            

class MySQLDump {            

  /**            

   * The backup command to execute            

   * @private            

   * @var string            

   */            

  var $cmd;            

  /**            

   * MySQLDump constructor            

   * @param string dbUser (MySQL User Name)            

   * @param string dbPass (MySQL User Password)            

   * @param string dbName (Database to select)            

   * @param string dest (Full dest. directory for backup file)            

   * @param string zip (Zip type; gz - gzip [default], bz2 - bzip)            

   * @access public            

   */            

  function MySQLDump($dbUser, $dbPass, $dbName, $dest,            

                     $zip = 'gz')            

  {            

    $zip_util = array('gz'=>'gzip','bz2'=>'bzip2');            

    if (array_key_exists($zip, $zip_util)) {            

      $fname = $dbName . '.' . date("w") . '.sql.' . $zip;             

      $this->cmd = 'mysqldump -u' . $dbUser . ' -p' . $dbPass .            

                   ' ' . $dbName . '| ' . $zip_util[$zip] . ' >' .            

                   $dest . '/' . $fname;            

    } else {            

      $fname = $dbName . '.' . date("w") . '.sql';             

      $this->cmd = 'mysqldump -u' . $dbUser . ' -p' . $dbPass .            

                   ' ' . $dbName . ' >' . $dest . '/' . $fname;            

    }            

  }            

  /**            

   * Runs the constructed command            

   * @access public            

   * @return void            

   */            

  function backup()            

  {            

    system($this->cmd, $error);            

    if ($error) {            

      trigger_error('Backup failed: ' . $error);            

    }            

  }            

}
```

*注*

`MySQLDump`类对你的操作系统配置做了一些假设。它假设 mysqldump 实用程序位于执行该脚本的用户的路径中。如果使用了`gzip`或`bzip2`工具，它们也需要出现在执行这个脚本的用户的路径中。`bzip2`提供比`gzip`更好的压缩，有助于节省磁盘空间。

下面的代码演示了如何使用该类:

```
Example 3.28\. 20.php            

<?php            

// Include the MySQLDump class            

require_once 'Database/MySQLDump.php';            

$dbUser = 'harryf';               // db User            

$dbPass = 'secret';               // db User Password            

$dbName = 'sitepoint';            // db name            

$dest   = '/home/harryf/backups'; // Path to directory            

$zip    = 'bz2';                  // ZIP utility to compress with            

// Instantiate MySQLDump            

$mysqlDump = new MySQLDump($dbUser, $dbPass, $dbName, $dest,            

                           $zip);            

// Perform the backup            

$mysqlDump->backup();            

?>
```

`$dest`变量指定放置备份文件的目录路径。创建的文件名将采用以下格式:

```
*databaseName.dayOfWeek.sql.zipExtension*
```

例如:

```
sitepoint.1.sql.bz2
```

`*dayOfWeek*`元素可以是从 0 到 6 的任意数字(0 是星期天，6 是星期六)。这提供了每周“滚动”备份，下周的文件覆盖上周的文件。这应该提供足够的备份，给你一周的时间来发现任何严重的问题，并且不需要过多的磁盘空间来存储文件。

ZIP 实用程序的使用是可选的。如果`$zip`变量的值不是`gz`或`bz2`中的一个，那么就不会进行压缩，尽管对于大型数据库来说，使用压缩工具来最小化所需的磁盘空间显然是个好主意。

该类用于 crontab 实用程序，这是一个 Unix 特性，允许您定期(例如每天)执行脚本。

MySQL 还提供了 SQL 语句 BACKUP TABLE 和 RESTORE TABLE，允许您将表中的内容复制到文件系统上的另一个位置。与`mysqldump`实用程序不同，以这种方式备份的表保留了它们的原始格式(不可读),但是这种机制不需要访问命令行实用程序，因此可以通过网页执行。

这些语句的一般语法如下:

```
BACKUP TABLE tbl_name[, tbl_name ...]            

TO '/path/to/backup/directory'            

RESTORE TABLE tbl_name[, tbl_name ...]            

FROM '/path/to/backup/directory'
```

请注意，在 Windows 系统上，最好使用正斜杠(例如`C:/backups`)来指定路径。

通过将这些与 MySQL 提供的一些“自省”语句结合起来，我们可以使用本章中构建的`MySQL`类来备份我们的数据库。首先，我们需要获得数据库中的表列表，这可以使用 SHOW TABLES 查询语法快速实现:

```
Example 3.29\. 21.php (excerpt)            

<?php            

// Include the MySQL class            

require_once 'Database/MySQL.php';            

$host   = 'localhost'; // Hostname of MySQL server            

$dbUser = 'harryf';    // Username for MySQL            

$dbPass = 'secret';    // Password for user            

$dbName = 'sitepoint'; // Database name            

$db = &new MySQL($host, $dbUser, $dbPass, $dbName);            

// A query to show the tables in the database            

$sql = "SHOW TABLES FROM sitepoint";            

// Execute query            

$result = $db->query($sql);
```

我们还存储该查询返回的行数，以帮助我们格式化将用于构建备份查询的字符串:

```
Example 3.30\. 21.php (excerpt)            

// Get the number of tables found            

$numTables = $result->size();
```

接下来，我们遍历结果，构建一个要备份的逗号分隔的表列表:

```
Example 3.31\. 21.php (excerpt)            

// Build a string of table names            

$tables = '';            

$i = 1;            

while ($table = $result->fetch()) {            

  $tables .= $table['Tables_in_sitepoint'];            

  if ($i < $numTables) {            

    $tables .= ', ';            

  }            

  $i++;            

}
```

最后，我们使用备份表查询语法将表复制到我们选择的目录中(当然，执行该查询的脚本需要对该目录进行写操作的权限):

```
Example 3.32\. 21.php (excerpt)            

// Build the backup query            

$sql = "BACKUP TABLE $tables TO '/home/harryf/backup'";            

// Perform the query            

$db->query($sql);            

if (!$db->isError()) {            

  echo 'Backup succeeded';            

} else {            

  echo 'Backup failed';            

}            

?>
```

##### 如何修复损坏的表？

虽然这不应该发生，但存储在 MySQL 中的数据偶尔会被破坏。这种情况可能发生在一些(罕见的)情况下；Windows 特别容易受到影响，因为它没有基于 Unix 的系统的强大的文件锁定机制。负载较重的服务器(在这些服务器上，INSERT 和 UPDATE 查询与 SELECTs 一起很常见)也可能偶尔会出现损坏。假设您正在使用 MyISAM 表类型(除非您另外指定，否则您将使用它)，有一个好消息；通常，您应该能够恢复损坏的表中的所有数据。

请注意，此处提供的信息是在您需要帮助时的快速参考。非常值得一读 MySQL 关于灾难预防和恢复的手册，这样你就能清楚地知道你在做什么。

MySQL 提供了两个重要的实用程序来处理损坏的表，并为那些可以使用 MySQL 命令行的人提供了一个方便的 SQL 语法。

首先，perror 实用程序可以从命令行运行，让您大致了解 MySQL 错误代码的含义。该实用程序应该可以从 MySQL 安装的`bin`子目录中获得。例如，输入`perror 145`会告诉您:

```
145 = Table was marked as crashed and should be repaired
```

从命令行，您可以使用实用程序`myisamchk`来检查数据库文件本身:

```
myisamchk /path/to/mysql/data/table_name
```

要使用`myisamchk`修复损坏的表，语法如下:

```
myisamchk -r /path/to/mysql/data/table_name
```

使用 SQL，您还可以使用如下查询来检查和修复表:

```
CHECK TABLE articles
```

还有这个:

```
REPAIR TABLE articles
```

幸运的话，您只需要使用这些命令一次或两次，但是提前做好准备是值得的，这样您就可以有效地做出反应(在您的行动中甚至没有一丝恐慌)。

##### 我真的需要写 SQL 吗？

作为一个程序员，一个好的品质就是懒惰——用最少的努力做尽可能多的事情。虽然你可能不想在面试中把它作为你的一个优势，但是在开发一个设计良好的应用程序时，积极地让自己的生活变得更容易是一个重要的优势。

既然你已经阅读了关于 PHP 和 MySQL 的这一章，我想这是一个很好的时机来揭示我讨厌 SQL，不是因为它本身有什么问题，而是因为它总是让我感到悲伤。例如，如果我的 PHP 中有语法错误，PHP 会为我找到它。但是 PHP 不会发现 SQL 语句中的错误，MySQL 的错误消息也不会暴露出来。如果我在一个应用程序中手工编写 SQL 代码，我会花相当多的时间来调试它——这些时间我本可以用来放松的！

如果您可以完全避免编写 SQL 语句会怎么样？如果您回想一下“我如何创建灵活的 SQL 语句？”，其中我们基于传入的变量“动态地”构造 SQL 字符串，您可能已经有所暗示，会有某种通用的解决方案使生成 SQL 变得更加容易。嗯，有！叫做 [PEAR::DB_DataObject](http://pear.php.net/DB_DataObject) 。

```
DB_DataObject is a class that encapsulates the process of writing SQL statements in a simple API. It takes advantage of the native "grammar" of SQL and presents you with a mechanism that removes almost any need to write any SQL yourself. As an approach to dealing with databases, it's usually described as a database persistence layer, or, alternatively, as using the Data Access Objects (DAO) design pattern. You'll find further discussion of the general techniques used by DB_DataObject at the end of this chapter.
```

在这里，我将提供一个关于`DB_DataObject`的简短介绍来帮助你开始，因为如果深入研究的话，这是一个很容易吸收整个章节的主题。PEAR 网站上的 [DB_DataObject 文档应该会为您提供更多的帮助。我们在这里使用的版本是 1.1；注意，它要求您已经安装了`PEAR::DB`数据库抽象库(参见附录 D，使用 PEAR 获得更多关于安装 PEAR 库的信息)。](http://pear.php.net/manual/en/package.database.db-dataobject.php)

开始使用`DB_DataObject`的第一步是将它指向您的数据库，并告诉它生成`DataObject`类，这些类将构成您与表的接口。`DB_DataObject`使用 MySQL 的自省功能自动检查您的数据库，并为数据库中的每个表生成一个类，以及一个包含表定义的列的详细信息的配置文件。为了让`DB_DataObject`知道在哪里可以找到您的数据库，您需要向它提供一个配置文件，如下所示:

```
Example 3.33\. db_dataobject.ini             

[DB_DataObject]             

; PEAR::DB DSN             

database        = mysql://harryf:secret@localhost/sitepoint             

; Location where sitepoint.ini schema file should be created             

schema_location = /htdocs/phpanth/SPLIB/ExampleApps/DataObject             

; Location where DataObject classes should be created             

class_location  = /htdocs/phpanth/SPLIB/ExampleApps/DataObject             

; Prefix for including files from your code             

require_prefix  = ExampleApps/DataObject             

; Classes should be prefixed with this string e.g. DataObject_User             

class_prefix    = DataObject_             

; Debugging information: 0=off, 1=display sql, 2=display results,             

; 3=everything             

debug = 0             

; Prevent SQL INSERT, UPDATE or DELETE from being performed             

debug_ignore_updates = false             

; Whether to die of error with a PEAR_ERROR_DIE or not             

dont_die = false
```

上面的`ini`文件遵循与 php.ini 相同的格式规则，最重要的是第一行，这是一个 PEAR::DB DSN 字符串，它定义了连接数据库所需的变量。这个文件既用于生成`DataObject`类，也用于执行查询。

有了这些，我们可以使用这个脚本(必须从命令行运行)来生成类:

```
Example 3.34\. 22.php              

<?php              

// Builds the DataObjects classes              

$_SERVER['argv'][1] = 'db_dataobject.ini';              

require_once 'DB/DataObject/createTables.php';              

?>
```

这个脚本自动创建我们访问数据库所需的类文件。下面是为文章表开发的一个示例:

```
Example 3.35\. ExampleApps/DataObject/Articles.php (in SPLIB)              

<?php              

/**              

 * Table Definition for articles              

 */              

require_once 'DB/DataObject.php';              

class DataObject_Articles extends DB_DataObject               

{              

  ###START_AUTOCODE              

  /* the code below is auto generated do not remove the above tag              

*/              

  var $__table = 'articles'; // table name              

  var $article_id; // int(11)  not_null primary_key auto_increment              

  var $title; // string(255)  not_null multiple_key              

  var $intro; // blob(65535)  not_null blob              

  var $body;  // blob(65535)  not_null blob              

  var $author; // string(255)  not_null              

  var $published; // string(11)                

  var $public; // string(1)  not_null enum              

  /* ZE2 compatibility trick*/              

  function __clone() { return $this;}              

  /* Static get */              

  function staticGet($k,$v=NULL) {              

    return DB_DataObject::staticGet('DataObject_Articles',$k,$v); }              

  /* the code above is auto generated do not remove the tag below */              

  ###END_AUTOCODE              

}              

?>
```

现在让我们使用这个类来访问 articles 表:

```
Example 3.36\. 23.php              

<?php              

// Include the DataObjects_Articles class              

require_once 'ExampleApps/DataObject/Articles.php';              

// Parse the database ini file              

$dbconfig = parse_ini_file('db_dataobject.ini', true);              

// Load Database Settings              

// (note main PEAR class is loaded by Articles.php)              

foreach ($dbconfig as $class => $values) {              

  $options = &PEAR::getStaticProperty($class, 'options');              

  $options = $values;              

}              

// Instantiate the DataObject_Articles class              

$articles = new DataObject_Articles();              

// Assign a value to use to search the 'Author' column              

$articles->author = 'Kevin Yank';              

// Perform the query              

$articles->find();              

echo 'Kevin has written the following articles:<br />';              

// Loop through the articles              

while ($articles->fetch()) {              

  echo ' - ' . $articles->title . ', published: ' .              

       date('jS M Y', $articles->published) . '<br />';              

}              

?>
```

首先，SQL 在哪里？没有——太好了！PHP 提供了`parse_ini_file`函数(详见第 4 章，文件),负责从我们的`db_dataobject.ini`配置文件中获取变量。当我们实例化自动生成的子类`DataObject_Articles`时，foreach 循环使所需的变量对`DB_DataObject`可用。通过给`$articles`对象的 author 属性赋值，我们准备了一个`DataObject_Articles`查询数据库时应该使用的`WHERE`条件。该查询实际上是通过调用 find 方法来执行的(参见`DB_DataObject`文档以获得完整的细节)，该方法反过来执行以下查询:

```
SELECT * FROM articles WHERE articles.author = 'Kevin Yank'
```

为了遍历结果，我们使用了`fetch`方法。当调用它时，`fetch`用当前行结果填充`$articles`对象的属性。这允许我们通过属性名再次访问它们，就像`$articles->title`一样。

提供了进一步的方法使查询更加复杂，例如，`whereAdd`方法:

```
Example 3.37\. 24.php (excerpt)              

// Instantiate the DataObject_Articles class              

$articles = new DataObject_Articles();              

// Assign a value to use to search the 'Author' column              

$articles->author = 'Kevin Yank';              

// Add a where clause              

$articles->whereAdd('published > ' . mktime(0, 0, 0, 5, 1, 2002));              

// Perform the query              

$articles->find();
```

这允许我们向`WHERE`子句添加进一步的条件:

```
SELECT * FROM articles              

WHERE published > 1020204000 AND articles.author = 'Kevin Yank'
```

还有其他类似的方法，所以如果这些方法不能提供您所需要的，您可以使用 query 方法来执行手工编码的查询。注意，如果您发现自己需要使用查询方法，那么创建生成的`DataObject`类的子类，并将查询包装在一个准确描述它的有用方法名称中，这可能是一个好主意。

```
DB_DataObject also deals effectively with table joins, which, although slightly more detailed than the example above, is certainly a time saver when compared with writing complex join queries by hand.
```

我们对`DB_DataObject`的简短介绍到此结束，但是这一节应该已经让您领略了它能为您做什么。最大的好处是，它使得用 SQL 查询数据库变得不那么累人，也不容易出错。此外，通过将对特定表的访问集中在一个类中，有助于简化对表结构变化的处理。

##### 进一步阅读

*   [Beginning MySQL](http://www.devshed.com/Server_Side/MySQL/Intro/)

    这篇文章提供了关于如何在 MySQL 中使用 SQL 的可靠摘要。

*   [Give me back my MySQL Command Line!](https://www.sitepoint.com/blog/)

    Kevin Yank 展示了如何编写一个 PHP 脚本，用于通过网页模拟 MySQL 命令行。

*   [Optimizing your MySQL Application](https://www.sitepoint.com/blog/)

    这个方便的教程讨论了索引在 MySQL 中的使用，以及如何使用它们来提高性能。

*   [Generating PHP Database Access Layers](http://freshmeat.net/articles/view/843/)

    本文概述了持久层和相关的代码生成，并提供了一些有用工具的链接。

*   [Zend Tutorial on Fulltext Searches](http://www.zend.com/zend/tut/tutorial-Ferrara.php)

    本教程详细介绍了全文搜索。

*   [Getting Started with MySQL Fulltext Searches](http://www.devarticles.com/art/1/195)

    这篇优秀的教程详细介绍了全文搜索。

*   [Backing Up with MySQLDump](https://www.sitepoint.com/blog/)

    这篇教程探究了 mysqldump 实用程序的来龙去脉。

在接下来的几周里，请在 SitePoint 上关注 PHP 选集的更多章节！如果你等不及了，[下载样本章节](https://www.sitepoint.com/books/phpant1/signoff.php)，或者[现在就订购你自己的副本](https://www.sitepoint.com/blog/)！

## 分享这篇文章**