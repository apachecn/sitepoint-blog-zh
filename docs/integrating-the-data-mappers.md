# 集成数据映射器

> 原文：<https://www.sitepoint.com/integrating-the-data-mappers/>

虽然仍然有大量的案例表明[领域模型](http://martinfowler.com/eaaCatalog/domainModel.html)被认为是一个过分的“企业”解决方案，不符合 PHP 世界中普遍存在的自然实用主义，但是它们逐渐打破了许多开发人员的思维，甚至是那些像沉船的最后一件救生衣一样坚持数据库模型范例的人。

有一些理由可以在很大程度上证明这种反应的合理性。毕竟，即使是构建最简单的领域模型，也需要定义约束、规则和构建对象之间的关系，它们在给定的上下文中的行为，以及它们在生命周期中将携带什么类型的数据。此外，将模型数据传入和传出存储的过程可能需要在某个时候丢弃一组[数据映射器](http://martinfowler.com/eaaCatalog/dataMapper.html)，这一事实凸显了为什么领域模型经常被一团欺凌包围。

然而，急切的预先判断往往会产生误导。一个丰富的领域模型的框架当然更适合放在一个大型应用程序的边界内，但是也有可能缩小它们的规模，并在较小的环境中充分利用它们。为了证明这一点，在我的上一篇文章中，我向您展示了如何实现一个简单的博客域模型，该模型由一些帖子、评论和用户对象组成。

前一篇缺乏真正的大团圆结局；它仅仅展示了模型的机制，而不是如何让它与“真正的”持久层同步工作。所以在你因为这种不礼貌的态度把我扔到狮子面前之前，在接下来的文章中，我们将开发一个基本的映射模块，它将允许你在博客的模型和 MySQL 数据库之间轻松地移动数据，同时保持它们彼此之间的整洁隔离。

## 构建一个天真的 DAL(或者为什么我的 PDO 适配器比你的好)

我知道，这句话听起来像是廉价的陈词滥调，但我并不特别喜欢每次解决软件问题时都重新发明轮子(当然，除非我需要一个更好更快的轮子)。在这种情况下，考虑到我们将尝试将一批映射类连接到一个博客的域模型，这种情况确实需要一些额外的努力。考虑到这项工作的重要性，我们的想法是从零开始建立一个基本的数据访问层(DAL ),这样域对象就可以很容易地保存在 MySQL 数据库中，然后根据请求通过一些通用的查找器进行检索。

正在讨论的 DAL 将只由几个组件组成:第一个组件将是一个简单的数据库适配器接口，其契约如下所示:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{
    public function connect();
    public function disconnect();

    public function prepare($sql, array $options = array());
    public function execute(array $parameters = array());

    public function fetch($fetchStyle = null, 
        $cursorOrientation = null, $cursorOffset = null);
    public function fetchAll($fetchStyle = null, $column = 0);

    public function select($table, array $bind, 
        $boolOperator = "AND");
    public function insert($table, array $bind);
    public function update($table, array $bind, $where = "");
    public function delete($table, $where = "");
}
```

不可否认，上面的`DatabaseAdapterInterface`是一种可以驯服的生物。它的契约允许我们在运行时创建不同的数据库适配器，并执行一些常见的任务，如连接到数据库和运行 CRUD 操作，而没有太多麻烦。

现在我们至少需要一个接口实现者来完成所有这些很酷的事情。将承担这一责任的骄傲的骑士将是一个非规范的 PDO 适配器，它看起来如下:

```
<?php
namespace LibraryDatabase;

class PdoAdapter implements DatabaseAdapterInterface
{
    protected $config = array();
    protected $connection;
    protected $statement;
    protected $fetchMode = PDO::FETCH_ASSOC;   

    public function __construct($dsn, $username = null,
        $password = null, array $driverOptions = array()) {
        $this->config = compact("dsn", "username", "password", 
            "driverOptions");
    }

    public function getStatement() {
        if ($this->statement === null) {
            throw new PDOException(
              "There is no PDOStatement object for use.");
        } 
        return $this->statement;
    }

    public function connect() {
        // if there is a PDO object already, return early
        if ($this->connection) {
            return;
        }

        try {
            $this->connection = new PDO(
                $this->config["dsn"],
                $this->config["username"],
                $this->config["password"],
                $this->config["driverOptions"]);
            $this->connection->setAttribute(PDO::ATTR_ERRMODE,
                PDO::ERRMODE_EXCEPTION);
            $this->connection->setAttribute(
                PDO::ATTR_EMULATE_PREPARES, false); 
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function disconnect() {
        $this->connection = null;
    }

    public function prepare($sql, array $options = array() {
        $this->connect();
        try {
            $this->statement = $this->connection->prepare($sql, 
                $options);
            return $this;
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function execute(array $parameters = array()) {
        try {
            $this->getStatement()->execute($parameters);
            return $this;
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function countAffectedRows() {
        try {
            return $this->getStatement()->rowCount();
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function getLastInsertId($name = null) {
        $this->connect();
        return $this->connection->lastInsertId($name);
    }

    public function fetch($fetchStyle = null,
        $cursorOrientation = null, $cursorOffset = null) {
        if ($fetchStyle === null) {
            $fetchStyle = $this->fetchMode;
        }

        try {
            return $this->getStatement()->fetch($fetchStyle, 
                $cursorOrientation, $cursorOffset);
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function fetchAll($fetchStyle = null, $column = 0) {
        if ($fetchStyle === null) {
            $fetchStyle = $this->fetchMode;
        }

        try {
            return $fetchStyle === PDO::FETCH_COLUMN
               ? $this->getStatement()->fetchAll($fetchStyle, $column)
               : $this->getStatement()->fetchAll($fetchStyle);
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function select($table, array $bind = array(), 
        $boolOperator = "AND") {
        if ($bind) {
            $where = array();
            foreach ($bind as $col => $value) {
                unset($bind[$col]);
                $bind[":" . $col] = $value;
                $where[] = $col . " = :" . $col;
            }
        }

        $sql = "SELECT * FROM " . $table
            . (($bind) ? " WHERE "
            . implode(" " . $boolOperator . " ", $where) : " ");
        $this->prepare($sql)
            ->execute($bind);
        return $this;
    }

    public function insert($table, array $bind) {
        $cols = implode(", ", array_keys($bind));
        $values = implode(", :", array_keys($bind));
        foreach ($bind as $col => $value) {
            unset($bind[$col]);
            $bind[":" . $col] = $value;
        }

        $sql = "INSERT INTO " . $table
            . " (" . $cols . ")  VALUES (:" . $values . ")";
        return (int) $this->prepare($sql)
            ->execute($bind)
            ->getLastInsertId();
    }

    public function update($table, array $bind, $where = "") {
        $set = array();
        foreach ($bind as $col => $value) {
            unset($bind[$col]);
            $bind[":" . $col] = $value;
            $set[] = $col . " = :" . $col;
        }

        $sql = "UPDATE " . $table . " SET " . implode(", ", $set)
            . (($where) ? " WHERE " . $where : " ");
        return $this->prepare($sql)
            ->execute($bind)
            ->countAffectedRows();
    }

    public function delete($table, $where = "") {
        $sql = "DELETE FROM " . $table . (($where) ? " WHERE " . $where : " ");
        return $this->prepare($sql)
            ->execute()
            ->countAffectedRows();
    }
}
```

你可以随意诅咒我向你扔出这么厚的代码片段，但这是必要的邪恶。此外，尽管`PdoAdapter`类看起来有些复杂，但它实际上是一个简单的包装器，利用了 PDO 开箱即用的大部分功能，而没有将客户端代码暴露给冗长的 API。

现在`PdoAdapter`正在为我们做数据库的脏工作，让我们创建几个 MySQL 表来存储对应于博客文章、评论和用户的数据:

```
CREATE TABLE posts (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  title VARCHAR(100) DEFAULT NULL,
  content TEXT,

  PRIMARY KEY (id)
);

CREATE TABLE users (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  name VARCHAR(45) DEFAULT NULL,
  email VARCHAR(45) DEFAULT NULL,

  PRIMARY KEY (id)
);

CREATE TABLE comments (
  id INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
  content TEXT,
  user_id INTEGER DEFAULT NULL,
  post_id INTEGER DEFAULT NULL,

  PRIMARY KEY (id),
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREGIN KEY (post_id) REFERENCES posts(id)
);
```

上面的数据库模式定义了帖子和评论之间的一对多关系，以及评论和用户(博客的评论员)之间的一对一关系。如果您喜欢冒险，您可以随意调整模式以满足您的特定需求。然而，为了简洁起见，我一直保持简单。

此时，我们已经实现了一个简单的 DAL，我们可以用它在 MySQL 中持久化博客的域模型，而不需要在这个过程中付出太多。现在我们需要添加中间人，也就是前面提到的数据映射器，这样任何阻抗不匹配都可以在幕后悄悄处理。

## 实现双向映射层

当然，这取决于上下文，但是大多数时候构建一个映射层(特别是一个双向关系层)并不容易。这个过程不能简单归结为…嘿，我会在喝咖啡休息的时候让这些关系映射器运行起来。这就是为什么像[主义](http://www.doctrine-project.org/)这样的 ORM 库终究是要生存和呼吸的。然而，在这种情况下，我们希望利用我们内心的大胆编码者，创建我们自己的一套映射器来按摩博客的域对象，而不用面对第三方包的学习曲线。

让我们从在抽象类中封装尽可能多的映射逻辑[开始，如下所示:](http://martinfowler.com/refactoring/catalog/extractSuperclass.html)

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface;

abstract class AbstractDataMapper
{
    protected $adapter;
    protected $entityTable;

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function getAdapter() {
        return $this->adapter;
    }

    public function findById($id)
    {
        $this->adapter->select($this->entityTable,
            array('id' => $id));

        if (!$row = $this->adapter->fetch()) {
            return null;
        }

        return $this->createEntity($row);
    }

    public function findAll(array $conditions = array())
    {
        $entities = array();
        $this->adapter->select($this->entityTable, $conditions);
        $rows = $this->adapter->fetchAll();

        if ($rows) {
            foreach ($rows as $row) {
                $entities[] = $this->createEntity($row);
            }
        }

        return $entities;
    }

    // Create an entity (implementation delegated to concrete mappers)
    abstract protected function createEntity(array $row);
}
```

该类在两个通用查找器后面抽象出了从指定表中提取数据所需的所有逻辑，这些逻辑随后用于以有效状态重新构造域对象。因为重构应该被委托给细化的实现，所以`createEntity()`方法被声明为抽象的。

现在让我们定义一组具体的映射器，它们将处理博客帖子、评论和用户。下面是第一个，以及它实现的接口:

```
<?php
namespace ModelMapper;
use ModelPostInterface;

interface PostMapperInterface
{
   public function findById($id);
   public function findAll(array $conditions = array());

   public function insert(PostInterface $post);
   public function delete($id);
}
```

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelPostInterface,
    ModelPost;

class PostMapper extends AbstractDataMapper implements PostMapperInterface
{
    protected $commentMapper;
    protected $entityTable = "posts";

    public function __construct(DatabaseAdapterInterface $adapter,
        CommentMapperInterface $commenMapper) {
        $this->commentMapper = $commenMapper;
        parent::__construct($adapter);
    }

    public function insert(PostInterface $post) {
        $post->id = $this->adapter->insert($this->entityTable,
            array("title"   => $post->title,
                  "content" => $post->content));
        return $post->id;
    }

    public function delete($id) {
        if ($id instanceof PostInterface) {
            $id = $id->id;
        }

        $this->adapter->delete($this->entityTable, "id = $id");
        return $this->commentMapper->delete("post_id = $id");
    }

    protected function createEntity(array $row) {
        $comments = $this->commentMapper->findAll(
            array("post_id" => $row["id"]));
        return new Post($row["title"], $row["content"], $comments);
    }
}
```

请注意，`PostMapper`的实现遵循了一条相当符合逻辑的路径。简单地说，它不仅扩展了它的抽象父对象，还在构造函数中注入了一个注释映射器(仍未定义)，以便同步处理帖子和注释，而不会向外界暴露创建整个对象图的复杂性。当然，我们不应该否认自己看到仍然隐藏的注释映射器的喜悦，因此这里是它的源代码，以及相应的接口:

```
<?php
namespace ModelMapper;
use ModelCommentInterface;

interface CommentMapperInterface
{
    public function findById($id);
    public function findAll(array $conditions = array());

    public function insert(CommentInterface $comment, $postId,
        $userId);
    public function delete($id);
}
```

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelCommentInterface,
    ModelComment;

class CommentMapper extends AbstractDataMapper implements CommentMapperInterface
{
    protected $userMapper;
    protected $entityTable = "comments";

    public function __construct(DatabaseAdapterInterface $adapter,
        UserMapperInterface $userMapper) {
        $this->userMapper = $userMapper;
        parent::__construct($adapter);
    }

    public function insert(CommentInterface $comment, $postId, 
        $userId) {
        $comment->id = $this->adapter->insert($this->entityTable, 
            array("content" => $comment->content,
                  "post_id" => $postId,
                  "user_id" => $userId));
        return $comment->id;
    }

    public function delete($id) {
        if ($id instanceof CommentInterface) {
            $id = $id->id;
        }

        return $this->adapter->delete($this->entityTable,
            "id = $id");
    }

    protected function createEntity(array $row) {
        $user = $this->userMapper->findById($row["user_id"]);
        return new Comment($row["content"], $user);
    }
}
```

`CommentMapper`类的行为与其兄弟`PostMapper`非常相似。简而言之，它要求在构造函数中有一个用户映射器，这样特定的评论就可以绑定到相应的评论者。考虑到`CommentMapper`的随和本质，让我们做最后的努力，定义另一个将处理用户的:

```
<?php
namespace ModelMapper;
use ModelUserInterface;

interface UserMapperInterface
{
    public function findById($id);    
    public function findAll(array $conditions = array());

    public function insert(UserInterface $user);
    public function delete($id);
}
```

```
<?php
namespace ModelMapper;
use ModelUserInterface,
    ModelUser;

class UserMapper extends AbstractDataMapper implements UserMapperInterface
{    
    protected $entityTable = "users";

    public function insert(UserInterface $user) {
        $user->id = $this->adapter->insert($this->entityTable,
            array("name"  => $user->name,
                  "email" => $user->email));
        return $user->id;
    }

    public function delete($id) {
        if ($id instanceof UserInterface) {
            $id = $id->id;
        }

        return $this->adapter->delete($this->entityTable,
            array("id = $id"));
    }

    protected function createEntity(array $row) {
        return new User($row["name"], $row["email"]);
    }
}
```

既然已经设置了`UserMapper`类，我们终于达到了我们从一开始就承诺的目标:从头开始构建一个易于按摩的映射层，能够在简单的博客域模型和 MySQL 之间来回移动数据。但是让我们先不要沾沾自喜，因为最好的方法是通过例子来看看地图绘制者是否像他们第一眼看到的那样有用。

## 将博客的域对象映射到 DAL 和从 DAL 映射

如您所料，以高效的方式使用 blog domain 模型非常简单，因为 mappers 的 API 完成了实际的艰苦工作，并对模型本身隐藏了底层数据库。不过，从应用层的角度来看，这种能力最值得欣赏。让我们将所有映射图连接在一起:

```
<?php
use LibraryLoaderAutoloader;
require_once __DIR__ . "/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();

// create a PDO adapter
$adapter = new PdoAdapter("mysql:dbname=blog", "myfancyusername",
    "myhardtoguesspassword");

// create the mappers
$userMapper = new UserMapper($adapter);
$commentMapper = new CommentMapper($adapter, $userMapper);
$postMapper = new PostMapper($adapter, $commentMapper);
```

到目前为止，一切顺利。此时，映射器已经通过将它们的合作者放到相应的构造函数中而被初始化。考虑到他们已经准备好采取一些实际行动，现在让我们使用帖子映射器，将一些琐碎的帖子插入到数据库中:

```
<?php
$postMapper->insert(
    new Post(
        "Welcome to SitePoint",
        "To become yourself a true PHP master, you must first master PHP."));

$postMapper->insert(
    new Post(
        "Welcome to SitePoint (Reprise)",
        "To become yourself a PHP Master, you must first master... Wait! Did I post that already?"));
```

如果一切按预期进行，那么`posts`表应该很好地填充了以前的条目。但是，是只有我还是他们看起来有点孤独？让我们添加一些注释来解决这个问题:

```
<?php
$user = new User("Everchanging Joe", "joe@example.com");
$userMapper->insert($user);

// Joe's comments for the first post (post ID = 1, user ID = 1)
$commentMapper->insert(
    new Comment(
        "I just love this post! Looking forward to seeing more of this stuff.",
        $user),
    1, $user->id);

$commentMapper->insert(
    new Comment(
        "I just changed my mind and dislike this post! Hope not seeing more of this stuff.",
        $user),
    1, $user->id);

// Joe's comment for the second post (post ID = 2, user ID = 1)
$commentMapper->insert(
    new Comment(
        "Not quite sure if I like this post or not, so I cannot say anything for now.", 
        $user),
    2, $user->id);
```

感谢 Joe 非凡的口才，第一篇博文现在应该有两条评论，第二篇应该有一条。注意，用于维持注释和用户之间的绑定的外键是在运行时获得的。然而，在生产中，它们最有可能被收集在用户界面中。

现在，博客的数据库终于被一些帖子、评论和闲聊用户的信息所充实，我们最不应该做的事情就是把所有的数据拉进来，并把它们转储到屏幕上。我们开始吧:

```
<?php
$posts = $postMapper->findAll();
```

即使这个一行程序只是一行程序，但它实际上是根据数据库的请求创建博客域对象图并将它们放在内存中以供进一步处理的主要工具。另一方面，讨论中的图表可以通过框架视图轻松地分解回来，如下所示:

```
<!doctype html>
<html>
<head>
 <meta charset="utf-8">
 <title>Building a Domain Model in PHP</title>
</head>
<body>
 <header>
  <h1>SitePoint.com</h1>
 </header>
 <section>
  <ul>
<?php
foreach ($posts as $post) {
?>
   <li>
    <h2><?php echo $post->title;?></h2>
    <p><?php echo $post->content;?></p>
<?php
	if ($post->comments) {
?>
    <ul>  
<?php
		foreach ($post->comments as $comment) {
?>
     <li>
      <h3><?php echo $comment->user->name;?> says:</h3>
      <p><?php echo $comment->content;?></p>
     </li>
<?php
		}
?>
    </ul>
<?php
	}
?>
    </li>
<?php
}
?>
   </ul>
  </section>
 </body>
</html>
```

事实上，浏览一些帖子、评论和用户是一项无聊的任务，不值得进一步解释。当然，如果你是一个有洞察力的鹰眼观察者，你可能已经注意到这个看似无害的视图实际上是一个贪婪者，它明目张胆地把整个数据库拉进来，扔进用户界面的中心。我们先不要急着下判断，因为有一些常见的技巧可以用来解决这个问题，包括缓存(多种形式中的任何一种)、[延迟加载](http://martinfowler.com/eaaCatalog/lazyLoad.html)等等。

用这些好东西给数据地图增添趣味将会成为你的家庭作业，这肯定会让你开心很长时间。

## 结束语

很少有人会不同意富域模型的实现远不是一夜之间就能完成的任务，即使使用 PHP 这样简单的语言也是如此。虽然可以肯定地说，在许多情况下，向 DAL 转发模型数据和从 DAL 转发模型数据可以委托给交钥匙映射库或框架(假设存在这样的东西)，但是定义域对象之间的关系以及它们自己的规则、数据和行为取决于开发人员。

尽管如此，额外的努力通常会产生有益的波动效应，因为它有助于积极推动多层设计的使用以及良好的 OOP 实践，其中所涉及的对象只有几个，[定义良好的职责](http://en.wikipedia.org/wiki/Single_responsibility_principle)，并且模型不会使其原始的生态系统受到数据库逻辑的污染。除此之外，将模型从一种基础设施转移到另一种基础设施可以相当轻松地完成，您将会看到为什么这种方法在开发必须具有良好伸缩性的应用程序时非常有吸引力。

图片通过[肯托](http://www.shutterstock.com/gallery-168430p1.html) / [快门](http://shutterstock.com)

## 分享这篇文章