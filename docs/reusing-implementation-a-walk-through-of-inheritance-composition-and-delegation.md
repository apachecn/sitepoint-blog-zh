# 重用实现——继承、组合和委托的演练

> 原文：<https://www.sitepoint.com/reusing-implementation-a-walk-through-of-inheritance-composition-and-delegation/>

如果必须给出一个令人信服的理由来解释为什么大多数开发人员都是如此奇特的生物，我会说，每当你说他们懒惰时，他们都会为自己感到无比自豪，并露出一个大大的“满口牙齿”的微笑。诚然，事实本身并没有超出仅仅是好奇和轶事的范围，但它表明“懒惰”一词并不被认为是冒犯性的。当然，作为一名开发人员，我要说我们没有那么疯狂(或者至少我们的疯狂通常是无害的)。只是我们把“懒惰”这个词当成了“一个聪明地重复使用东西的真正干巴巴的人”(因此做的工作更少)的特别同义词。

虽然拥有这种被描述为最省力原则的态度本身并没有错，但有时这只不过是虚张声势。我们知道，利用 framework A 和 library B 提供的丰富功能，在处理紧迫的最后期限和创造收入时会非常有效，但最终我们真正想要的是避免遭受代码重复的痛苦，并享受可重用实现的好处。老实说，我们真的知道这些框架或库是否像他们声称的那样枯燥吗？更糟糕的是，假设它们是，我们真的有能力在这样的代码基础上编写干代码吗？

普遍的看法是，重用实现，从而产生更干燥的代码，归结为利用继承提供的好处，对吗？嗯，我希望有那么简单！很少有 cargo cult 编程的实践者也会大声宣称[组合](http://en.wikipedia.org/wiki/Object_composition "Object composition - Wikipedia, the free encyclopedia")实际上是代码重用之王，继承应该被抛弃。或者，我们为什么不停止转动曲柄，通过选择普通的[代表团](http://en.wikipedia.org/wiki/Delegation_(programming) "Delegation (programming) - Wikipedia, the free encyclopedia")来摆脱这两者呢？这确实是一个棘手的两难问题，但最终毫无意义。

正如人们所料，每种方法都有自己的舒适区，哪种方法最符合要求取决于要解决的问题类型及其周围的环境。长期以来，继承一直被高估，尤其是在 PHP 世界中，像 [Liskov 替换原则](https://www.sitepoint.com/liskov-substitution-principle/ "The Liskov Substitution Principle") (LSP)这样的核心 OOP 概念在现实世界中几乎被视为混乱、晦涩的范例，几乎没有或根本没有应用价值。另一方面，组合和委托在语言中找到了相当多产的位置，在过去几年中通过[依赖注入](http://en.wikipedia.org/wiki/Dependency_injection "Dependency Injection - Wikipedia, the free encyclopedia")获得的势头加强了这一点。这就好像对立的力量在不断地相互推动，以维持一种纯粹虚构的平衡。

如果您不知道在重用实现方面该走哪条路，那么在本文中，我将对继承/组合/委托三者进行一次简单的介绍，试图并排展示它们最吸引人的优点和笨拙的缺点。

## 踏上继承之路——简单性与基本类型/子类型问题

我计划首先提出的第一个代码重用方法是所有方法中被滥用和误用最多的，这不应该是新闻。但是我不想成为一个虐待狂，并让你厌烦到流泪(当然还有我自己),解释如何通过继承动物来获得一个好的狗子类(哎哟！).相反，我会更现实一点，我们将致力于一个天真的，但功能， PDO 适配器。我们开始吧:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{
    public function executeQuery($sql, array $parameters = array());
}
```

```
<?php
namespace LibraryDatabase;

class PdoAdapter extends PDO implements DatabaseAdapterInterface
{
    protected $statement;

    public function __construct($dsn, $username = null, $password = null, array $options = array()) 
    {
        // fail early if the PDO extension is not loaded
        if (!extension_loaded("pdo")) {
            throw new InvalidArgumentException(
                "This adapter needs the PDO extension to be loaded.");
        }
        // check if a valid DSN has been passed in
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException(
                "The DSN must be a non-empty string.");
        }
        // try to create a valid PDO object
        try {
            parent::__construct($dsn, $username, $password,
                $options);
            $this->setAttribute(PDO::ATTR_ERRMODE,
                PDO::ERRMODE_EXCEPTION);
            $this->setAttribute(PDO::ATTR_EMULATE_PREPARES,
                false);

        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function executeQuery($sql, array $parameters = array())
    {
        try {
           $this->statement = $this->prepare($sql);
           $this->statement->execute($parameters);
           return $this->statement->fetchAll(PDO::FETCH_CLASS,
               "stdClass"); 

        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }
}
```

当创建一个快速而肮脏的适配器时，它只是在由`PDO`类提供的功能上增加了一些额外的功能，继承确实是一个非常诱人的途径。在这种情况下，适配器对输入的数据库参数执行一些基本的验证，它的`executeQuery()`方法允许我们通过一个容易使用的 API 运行参数化查询。下面的代码片段演示了如何将适配器放到客户端代码中，以便从数据库中提取一些用户:

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=mydatabase",
    "myfancyusername", "myhardtoguesspassword");

$guests = $adapter->executeQuery("SELECT * FROM users WHERE role = :role", array(":role" => "Guest"));

foreach($guests as $guest) {
    echo $guest->name . " " . $guest->email . "<br>";
}
```

没有任何“偶然”破坏 LSP 的迹象，这意味着任何`PdoAdapter`的实例都可以在运行时被一个基本的`PDO`对象安全地换出，而不会受到客户端代码的抱怨，并且适配器已经被其父级封装了所有遗留功能。我们敢要求更好的交易吗？

不可否认，这里有一个陷阱。虽然没有明确说明，`PdoAdapter`实际上向外界公开了整个冗长的 PDO API，加上它自己实现的 API。可能有些时候我们想要避免这种情况，即使`PdoAdapter`和`PDO`之间的“是-A”关系被巧妙地保持着。

在这种情况下，我们可以诉诸“偏爱组合胜过继承”这句箴言的智慧，并有效地转移到组合的海洋中。这样做，我们将有效地重用`PDO`的实现，但不必处理它的整个 API。更好的是，由于在相应的适配器和`PDO`类之间没有“是-A”关系，而是“有-A”关系，我们也将满足我们纯粹的 OOP 本能。[封装](http://en.wikipedia.org/wiki/Encapsulation_(object-oriented_programming) "Encapsulation (object-oriented programming) - Wikipedia, the free encyclopedia")会保持其原始外壳完整无损。

当然，理解这种方法内部工作原理的一个好方法是设置一个具体的例子。让我们调整一下之前的`PdoAdapter`类来遵守写作的戒律。

## 继承之上的组合——适配器、被适配器和其他有趣的事实

与流行的观点相反，实现一个基于组合的 PDO 适配器非常容易。此外，整个重构将仅限于创建一个简单的包装器，该包装器将一个本机`PDO`对象注入到构造函数中。再说一遍，在理解流程的驱动逻辑时，实践示例是最好的老师:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{
    public function executeQuery($sql, array $parameters = array());
}
```

```
<?php
namespace LibraryDatabase;

class PdoAdapter implements DatabaseAdapterInterface
{
    protected $pdo;
    protected $statement;

    public function __construct(PDO $pdo) 
    {
        $this->pdo = $pdo;
        $this->pdo->setAttribute(PDO::ATTR_ERRMODE,
            PDO::ERRMODE_EXCEPTION);
        $this->pdo->setAttribute(PDO::ATTR_EMULATE_PREPARES,
            false);
    }

    public function executeQuery($sql, array $parameters = array())
    {
        try {
           $this->statement = $this->pdo->prepare($sql);
           $this->statement->execute($parameters);
           return $this->statement->fetchAll(PDO::FETCH_CLASS,
               "stdClass"); 
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }
}
```

虽然适配器的重构版本通常遵循[适配器模式](http://en.wikipedia.org/wiki/Adapter_pattern "Adapter pattern - Wikipedia, the free encyclopedia")的正式定义，但是在这种情况下，传递到适配器内部的被适配器是一个具体的实现，而不是抽象。考虑到我们正试图为一个`PDO`对象设计一个精简的 API，务实一点，直接注入有问题的对象，而不必经历定义一个额外接口的麻烦，是没有坏处的。如果你觉得使用基于接口的方法更舒服一点，那就一直坚持下去。

抛开那些微妙的实现细节不谈，到目前为止要指出的最相关的事情是`PdoAdapter`类现在是一个不那么冗长的东西。使用组合，它通过只向客户端代码公开其`executeQuery()`方法，对外界隐藏了整个 PDO API。尽管这个例子很幼稚，但它提出了几个值得注意的问题。首先，处理潜在危险的类层次结构的负担已经悄悄地消失在空气中，在这种层次结构中，子类型的行为可能与它们的基类型截然不同，从而吹走应用程序流。第二，不仅现在适配器的 API 不那么臃肿，它还声明了对`PDO`实现的显式依赖，这使得从外部更容易看到它需要做它的事情的合作者。

然而，这些好处也伴随着不那么隐蔽的代价:在更现实的情况下，创建“组合的”适配器可能比创建“继承的”适配器更难。正确的道路将因情况而异；理智一点，选择最符合自己要求的。

如果你想知道如何使用重构后的`PdoAdapter`类，下面的例子应该是有启发性的:

```
<?php
$pdo = new PDO("mysql:dbname=mydatabase",
    "myfancyusername", "myhardtoguesspassword");
$adapter = new PdoAdapter($pdo);

$guests = $adapter->executeQuery("SELECT * FROM users WHERE role = :role", array(":role" => "Guest"));

foreach($guests as $guest) {
    echo $guest->name . " " . $guest->email . "<br>";
}
```

到目前为止，我们已经在一个非常宽泛的层面上看到了两种可用于重用实现的常见方法。那么，接下来是什么？嗯，我也答应过要深入挖掘授权的细节。诚然，组合是一种隐式的委托形式，但在前面的示例中，委托链接是通过在构造函数中声明对 PDO 实现的依赖来设置的。

然而，通过使用一个或多个工厂方法来建立并运行一个体面的委托机制是可行的，尽管这错过了将接口与实现解耦的许多优点。无论如何，这种方法值得一看，因为它有助于构建能够与数据库进行惰性连接的 PDO 适配器。

## 通过委派推迟数据库访问

还有什么比将昂贵的数据库之旅推迟到最后一刻更令人愉快的呢？完全避免它当然会更好，这是缓存系统存在的主要原因。但不幸的是，我们有时不能拥有一切，我们需要通过设计 PDO 适配器来解决自己的问题，这些适配器将这种漂亮的能力封装在工厂方法的边界内。

委托是一个简单而强大的模式，它允许实现这个特性而没有太多的麻烦。如果我们将注意力转向前面的`PdoAdapter`类，并让它利用委托的好处，它将看起来如下:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{
    public function connect();

    public function disconnect();

    public function executeQuery($sql, array $parameters = array());
}
```

```
<?php
namespace LibraryDatabase;

class PdoAdapter implements DatabaseAdapterInterface
{
    protected $config = array();
    protected $connection;
    protected $statement;

    public function __construct($dsn, $username = null, $password = null, array $options = array())
    {
        // fail early if the PDO extension is not loaded
        if (!extension_loaded("pdo")) {
            throw new InvalidArgumentException(
                "This adapter needs the PDO extension to be loaded.");
        }
        // check if a valid DSN has been passed in
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException(
                "The DSN must be a non-empty string.");
        }
        $this->config = compact("dsn", "username",
            "password", "options");
    }

    public function connect()
    {
        if ($this->connection) {
            return;
        }
        try {
            $this->connection = new PDO(
                $this->config["dsn"], 
                $this->config["username"], 
                $this->config["password"], 
                $this->config["options"]
            );
            $this->connection->setAttribute(PDO::ATTR_ERRMODE,
                PDO::ERRMODE_EXCEPTION);
            $this->connection->setAttribute(
                PDO::ATTR_EMULATE_PREPARES, false);
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function disconnect()
    {
        $this->connection = null;
    }

    public function executeQuery($sql, array $parameters = array())
    {
        $this->connect();
        try {
           $this->statement = $this->connection->prepare($sql);
           $this->statement->execute($parameters);
           return $this->statement->fetchAll(PDO::FETCH_CLASS,
               "stdClass"); 
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }
}
```

如果您曾经有机会从头开始制作自己的 PDO 杰作，或者如果您刚刚重用了一个可用的堆，那么上面的适配器的底层逻辑应该很容易理解。毫无疑问，它最吸引人的方面是实现了`connect()`方法，因为它的职责是根据请求创建一个`PDO`对象，然后使用该对象对数据库进行查询。

虽然不可否认这很枯燥，但它有助于展示如何在适配器的实现中使用一种“硬编码”的委托，这种委托足够聪明，可以触发到数据库的惰性连接。此外，下面的示例演示了如何使用适配器:

```
<?php
$adapter = new PdoAdapter("mysql:dbname=mydatabase",
    "myfancyusername", "myhardtoguespassword");

$guests = $adapter->executeQuery("SELECT * FROM users WHERE role = :role", array(":role" => "Guest"));

foreach($guests as $guest) {
    echo $guest->name . " " . $guest->email . "<br>";
}
```

如果您愿意，可以说我是一个令人难以忍受的学究，但是使用适配器实际上是一个简单的过程，尤其是从客户机代码的角度分析时。主要的缺陷是适配器隐藏了依赖关系。这引入了一个讨厌的耦合，因此使整个实现有点脏。另一方面，适配器最吸引人的优点在于它只有在情况允许时才调用数据库的能力。自然，通过在适配器中注入一个工厂，这种权衡可以很容易地解决，它将负责在需要时创建一个`PDO`对象。

尽管这种方法实际上是两全其美的，因为它积极地促进了依赖注入的使用，同时仍然采用了委托，但在我看来，这有点矫枉过正，至少在这种情况下是这样。

## 结束语

我不得不承认，在不陷入浅薄、害羞的分析的情况下，对继承、组成和授权所暴露的缺陷和细微之处做出判断是相当具有挑战性的。即便如此，考虑到每种方法都是 OOP 范例的核心支柱，冒险一试肯定是值得的。

此外，虽然在这种情况下，我有意地做了一个孤立的“按方法”评估，希望最终有所启发，但有可能将所有方法连接在一起，以实现更有效的代码重用策略。这里没有互相排斥。

不用说，仅仅因为可以就盲目的把它们堆起来，没有太大的意义。在所有情况下，要有意识，并确保将它们的使用范围缩小到你正在处理的上下文中。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章