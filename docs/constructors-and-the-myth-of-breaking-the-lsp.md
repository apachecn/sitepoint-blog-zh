# 构造函数和打破利斯科夫替代原则

> 原文：<https://www.sitepoint.com/constructors-and-the-myth-of-breaking-the-lsp/>

冒着被 PHP 煽动仇恨者盯上的风险，我必须承认我对 PHP 的对象模型相当满意。我并不天真地声称这是一个完整的模型，展示了其他“胖”玩家的所有功能，如 C++和 Java，但是尽管它有所有的怪癖，PHP 还是提供了它表面上承诺的东西。然而，我要说的是，考虑到在定义用户界面时所施加的宽松约束，该模型是出了名的宽松。

该模型的“宽恕”的一个问题是，有一天你可能会在一个异想天开的情绪中醒来，并在一个接口中指定一个构造函数，因为你可以这样做，而没有一丝内疚。如果你想通了这一点，作为接口的一部分，构造函数的存在是毫无意义的。首先，每次你定义一个契约，你实际上是在抽象出具体实现的行为，而忽略了它们必须如何被组装。第二，一旦实现者被实例化，如何才能让客户端代码使用构造函数呢？没有一个平行宇宙可以让你做这样的事情，除非你没有草率地用一堆新的操作符污染消费者，根据“将应用程序逻辑从对象构造中分离出来”的原则，这些操作符应该在其他地方生存和呼吸。

抛开咆哮不谈，这个故事的主旨可以归结为以下几点:“对象构造不是其实现者所遵守的契约的一部分”。

通过例子比阅读枯燥的理论更容易理解概念，所以在这篇文章中，我将从实践的角度展示如何在相同的层次结构中实现不同的构造函数并不违反 [Liskov 替换原则](https://www.sitepoint.com/liskov-substitution-principle/ "The Liskov Substitution Principle")，这也是为什么你不应该受到用构造函数玷污接口的诱惑的原因。

## LSP 破损的神话

关于在接口中避免构造函数，我想我已经提出了一个有效的观点，这个过程当然有助于生成描述和规定某些对象类型(也称为[子类型多态性](http://en.wikipedia.org/wiki/Subtype_polymorphism "Subtype polymorphism - Wikipedia, the free encyclopedia"))的行为的契约，但不包括如何创建所讨论的对象。然而，要理解为什么在子类中覆盖父构造函数，或者最终实现一个全新的构造函数不违反 LSP，这可能会更加困难。

比方说，我们需要创建一个 PDO 适配器，只需从原生 PDO 挂一个轻量级子类。适配器的契约以及相应的实现可能如下所示:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{     
    public function executeQuery($sql, array $parameters = array());
    public function select($table, array $bind, $operator = "AND");
    public function insert($table, array $bind);
    public function update($table, array $bind, $where = "");
    public function delete($table, $where = "");
}
```

```
<?php
namespace LibraryDatabase;

class PdoAdapter extends PDO implements DatabaseAdapterInterface
{
    private $statement;   

    public function __construct($dsn, $username = null, $password = null, array $options = array()) {
        if (!extension_loaded("pdo")) {
            throw new InvalidArgumentException(
                "The adapter needs the PDO extension to be loaded.");
        }
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException("The DSN is invalid.");
        }
        parent::__construct($dsn, $username, $password, $options);
        $this->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        $this->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
    }

    // Prepare and execute an SQL statement
    public function executeQuery($sql, array $parameters = array()) {
        try {
            $this->statement = $this->prepare($sql);
            $this->statement->execute($parameters);
            $this->statement->setFetchMode(PDO::FETCH_OBJ);
            return $this->statement;
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    // Prepare and execute a SELECT statement
    public function select($table, array $bind = array(), $operator = "AND") {
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
             . implode(" " . $operator . " ", $where) : " ");
        return $this->executeQuery($sql, $bind);
    }

    // Prepare and execute an INSERT statement
    public function insert($table, array $bind) {
        $cols = implode(", ", array_keys($bind));
        $values = implode(", :", array_keys($bind));
        foreach ($bind as $col => $value) {
            unset($bind[$col]);
            $bind[":" . $col] = $value;
        }
        $sql = "INSERT INTO " . $table 
             . " (" . $cols . ")  VALUES (:" . $values . ")";
        $this->executeQuery($sql, $bind);
        return $this->lastInsertId();
    }

    // Prepare and execute an UPDATE statement
    public function update($table, array $bind, $where = "") {
        $set = array();
        foreach ($bind as $col => $value) {
            unset($bind[$col]);
            $bind[":" . $col] = $value;
            $set[] = $col . " = :" . $col;
        }
        $sql = "UPDATE " . $table . " SET " . implode(", ", $set) 
             . (($where) ? " WHERE " . $where : " ");
        return $this->executeQuery($sql, $bind)->rowCount();
    }

    // Prepare and execute a DELETE statement
    public function delete($table, $where = "") {
        $sql = "DELETE FROM " . $table 
             . (($where) ? " WHERE " . $where : " ");
        return $this->executeQuery($sql)->rowCount();
    }
}
```

由`PdoAdapter`类执行的一批任务确实很简单，仅限于通过它的`executeQuery()`方法运行一些准备好的查询，并针对给定的表执行一些 CRUD 操作。这里要强调的最相关的细节是适配器如何覆盖其父类的构造函数，以便在开始使用 PDO 扩展之前做一些检查，看看是否已经安装了它。

下面是我们启动和运行适配器的方法:

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=test", "myusername", "mypassword");

$stmt = $adapter->prepare("SELECT * FROM users");

$stmt->execute();

$users = $stmt->fetchAll(PDO::FETCH_OBJ);

foreach ($users as $user) {
    echo $user->name . " " . $user->email . "<br>";
}
```

我有意避免使用适配器的`executeQuery()`方法，而是使用本机的`execute()`方法从数据库中提取一些用户。乍一看，这似乎是非常无聊的样板材料，应该跳过更有趣的东西…但请原谅我，因为这里确实有一个值得强调的小问题。即使考虑到适配器有效地实现了与 PDO 提供的默认构造函数不同的构造函数，它与客户机代码达成的契约也是自上而下整齐地维护的。简而言之，这意味着在一个基类型和一个子类型中有不同的构造函数实现决不会违反 LSP 可替换性规则，只要这两个类公开的契约得到适当的遵守。

从逻辑上讲，如果我们将前面的脚本切换到本机 PDO 实例，而不是使用适配器，这一点会看得更清楚:

```
<?php
$adapter = new PDO("mysql:dbname=test", "myusername", "mypassword");

$stmt = $adapter->prepare("SELECT * FROM users");

$stmt->execute();

$users = $stmt->fetchAll(PDO::FETCH_OBJ);

foreach ($users as $user) {
    echo $user->name . " " . $user->email . "<br>";
}
```

事实上，拥有一个类的层次结构，即使是一个非常基本的层次结构，其中子类型的构造不同于基本类型，这与打破 LSP 积极倡导的期望的可替换性没有任何关系。

你可能会说适配器和原生 PDO 的签名看起来非常相似，这就是为什么可替换性在这种情况下保存得如此之好。但是反驳这样的论点真的很容易。例如，我可以采用一种更实用的方法，重构适配器的构造函数，以便它可以接受一组命名的连接参数:

```
<?php
namespace LibraryDatabase;

class PdoAdapter extends PDO implements DatabaseAdapterInterface
{
    private $statement;   

    public function __construct(array $config = array()) {
        if (!extension_loaded("pdo")) {
            throw new InvalidArgumentException(
                "The adapter needs the PDO extension to be loaded.");
        }
        if (!isset($config["dsn"]) || !is_string($config["dsn"])) {
             throw new InvalidArgumentException(
                "The DSN has not been specified or is invalid.");
        }
        $username = $password = $options = null;
        extract($config);
        parent::__construct($dsn, $username, $password, $options);
        $this->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        $this->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
    }

    // the same implementation goes here
}
```

在这一点上，适配器的构造函数是一个非常不同的东西，它的签名和实现巧妙地容纳了一组连接参数，而不是消化普通的标量。有了这样一个彻底的重构，人们可能会想当然地认为在运行时不可能再用本地的 PDO 实例交换适配器的实例了。匆忙的判断往往是危险的，下面的片段就是证据:

```
<?php
$adapter = new PdoAdapter(array(
    "dsn"      => "mysql:dbname=test", 
    "username" => "myusername", 
    "password" => "mypassword"
));

$stmt = $adapter->prepare("SELECT * FROM users");

$stmt->execute();

$users = $stmt->fetchAll(PDO::FETCH_OBJ);

foreach ($users as $user) {
    echo $user->name . " " . $user->email . "<br>";
}
```

尽管现在实例化适配器的一个实例是一个完全不同的过程，但是客户机代码仍然完全不知道它所使用的契约中的这一变化。

尽管如此，如果您感到一丝怀疑，并认为该示例在演示给定层次结构的类型之间的可互换性时有所欠缺，请查看以下内容，其中更彻底地重构了构造函数，并通过简单的 DTO(数据传输对象)提供了连接参数:

```
<?php
namespace LibraryDatabase;

interface ConnectionDefinitionInterface
{
    public function getDsn();
    public function getUserName();
    public function getPassword();
    public function getOptions();
}
```

```
<?php
namespace LibraryDatabase;

class ConnectionDefinition implements ConnectionDefinitionInterface
{
    private $dsn;
    private $username;
    private $password;
    private $options = array();

    public function __construct($dsn, $username = null, $password = null, array $options = array()) {  
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException("The DSN is invalid.");
        }
        $this->dsn = $dsn;
        $this->username = $username;
        $this->password = $password;
        $this->options = $options;
    }

    public function getDsn() {
        return $this->dsn;
    }

    public function getUserName() {
        return $this->username;
    }

    public function getPassword() {
        return $this->password;
    }

    public function getOptions() {
        return $this->options;
    }
}
```

```
<?php
namespace LibraryDatabase;

class PdoAdapter extends PDO implements DatabaseAdapterInterface
{
    private $statement;   

    public function __construct(ConnectionDefinitionInterface $connectionDefinition) {
        if (!extension_loaded("pdo")) {
            throw new InvalidArgumentException(
                "The adapter needs the PDO extension to be loaded.");
        }
        parent::__construct(
            $connectionDefinition->getDsn(), 
            $connectionDefinition->getUserName(), 
            $connectionDefinition->getPassword(), 
            $connectionDefinition->getOptions()
        );
        $this->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        $this->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
    }

    // the same implementation goes here
}
```

虽然公平地说，d to 在 PHP 世界中并不多见(尽管一些流行的框架如 Zend Framework 2 和 Simfony 2 无处不在地使用它们)，但在这种情况下，我求助于一个简单的方法来将一组连接参数传递给适配器的构造函数。由于显而易见的原因，这种彻底的改变将会把工件推向负责实例化适配器的代码，无论是 DIC、低级工厂，还是其他任何不直接干扰应用程序逻辑的独立机制:

```
<?php
$adapter = new PdoAdapter(
    new ConnectionDefinition("mysql:dbname=test", "myusername", "mypassword")
);
```

只要适配器的契约在整个过程中保持不变，就有可能将适配器的一个实例与 PDO 的一个实例互换，并且客户端代码不会出现一个兼容的实例。

虽然这个例子在将 PDO 功能推到瘦包装器的边界内时可能有些矫枉过正，但它很好地展示了由于其他原因而容易发现的明显的 LSP 破坏，我敢说这些原因甚至与基本类型/子类型构造模式的“缺陷”无关。

## 结束语

这句口头禅已经说了一遍又一遍，但它值得再次强调:为某个编程原则或模式提供一个必须盲目实现的教条内涵，这几乎与根本不使用那些模式和原则一样有害，因此让实用主义的混乱极大地破坏了系统的质量。

除了试图揭开构造函数是潜在 LSP 违规的根源之一之外，我反对在接口中包含构造函数的论点当然不是教条。但是，仅仅因为 PHP 的对象模型在这方面是宽松的，就意识到这种“结构限制性”契约的后果可能有助于减少这种不灵活、无意义的做法的扩散。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章