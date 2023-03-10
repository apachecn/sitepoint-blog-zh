# 利斯科夫替代原理

> 原文：<https://www.sitepoint.com/liskov-substitution-principle/>

欢迎来到(被覆盖的)矩阵。嘘…不要告诉任何人！在《黑客帝国》三部曲中一个被删除的场景中，发生了以下对话:

尼奥，我现在在母体里。很抱歉告诉你这个坏消息，但是我们的代理跟踪 PHP 程序需要快速更新。它目前使用 PDO 的带有字符串的 query()方法从我们的数据库中获取所有矩阵代理的状态，但是我们需要用准备好的查询来代替。

听起来不错，莫斐斯。我能得到一份节目单吗？

**莫斐斯:**没问题。只需克隆我们的存储库并查看一下`AgentMapper.php`和`index.php`文件。

Neo 发出几个 Git 命令，很快下面的代码出现在他眼前。

```
<?php
namespace ModelMapper;

class AgentMapper
{
    protected $_adapter;
    protected $_table = "agents";

    public function __construct(PDO $adapter) {
        $this->_adapter = $adapter;
    }

    public function findAll() {
        try {
            return $this->_adapter->query("SELECT * FROM " . $this->_table, PDO::FETCH_OBJ);
        }
        catch (Exception $e) {
            return array();
        }
    }   
}
```

```
<?php
use ModelMapperAgentMapper;

// a PSR-0 compliant class loader
require_once __DIR__ . "/Autoloader.php";

$autoloader = new Autoloader();
$autoloader->register();

$adapter = new PDO("mysql:dbname=Nebuchadnezzar", "morpheus", "aa26d7c557296a4e8d49b42c8615233a3443036d");

$agentMapper = new AgentMapper($adapter);
$agents = $agentMapper->findAll();

foreach ($agents as $agent) {
    echo "Name: " . $agent->name .  " - Status: " . $agent->status . "<br>";
}
```

莫斐斯，我刚拿到文件。我将子类化`PDO`并覆盖它的`query()`方法，这样它就可以处理准备好的查询。因为我有超人的力量，我应该能很快把它修好。保持冷静。

空气中充满了电脑键盘平稳的声音。

**Neo:** Morpheus，子类准备测试。你可以随意查看。

莫斐斯在他的笔记本电脑上快速搜索，看到了下面的课程。

```
<?php
namespace LibraryDatabase;

class PdoAdapter extends PDO
{
    protected $_statement;

    public function __construct($dsn, $username = null, $password = null, array $driverOptions = array()) {
        // check if a valid DSN has been passed in
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException("The DSN must be a non-empty string.");
        }
        try {
            // attempt to create a valid PDO object and set some attributes.
            parent::__construct($dsn, $username, $password, $driverOptions);
            $this->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
            $this->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function query($sql, array $parameters = array())
    {
        try {
           $this->_statement = $this->prepare($sql);
           $this->_statement->execute($parameters);
           return $this->_statement->fetchAll(PDO::FETCH_OBJ);        
        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }
}
```

**Morpheus:** 适配器看起来不错。我马上试一试，看看我们的代理映射器是否能跟踪穿越矩阵的活动代理。祝我好运。

*Morpheus 犹豫了一会儿，运行之前的`index.php`文件，这次使用 Neo 的杰作`PdoAdapter`类。*

然后，一声尖叫！

莫斐斯:尼奥，我确定你就是那个人！只是我的脸上出现了一个可怕的致命错误，显示如下信息:

```
Catchable fatal error: Argument 2 passed to LibraryDatabasePdoAdapter::query() must be an array, integer given, called in path/to/AgentMapper on line (who cares?)
```

又一声尖叫。

**Neo:** 哪里出了问题？！哪里出了问题？！

*更多的尖叫声。*

**莫斐斯:**我真的不知道。哦，特工史密斯现在来找我了！

通讯突然中断。长时间的沉默结束了对话，暗示着莫斐斯被特工史密斯突袭并严重受伤。

## LSP 不代表疯狂的、病态的程序

不用说，上面的对话是虚构的，但问题无疑是真实的。如果尼欧像他曾经的著名黑客一样只知道一两件关于里斯科夫替代原理(LSP)的事情，[史密斯先生](http://en.wikipedia.org/wiki/Agent_Smith)很快就会被追踪到。最重要的是，莫斐斯将从特工的邪恶意图中解救出来。他真的很可怜。

然而，在许多情况下，PHP 开发人员考虑 LSP 的方式与 Neo 以前的方式非常相似:LSP 只不过是纯粹主义者的理论原则，在实践中很少或根本没有应用。但是他们走上了错误的道路。

即使当 LSP 的正式定义让人回过头来看(包括我的)，它的核心还是要避免定义不明确的类层次结构，在这种层次结构中，后代暴露的行为与使用相同契约的基本抽象完全不同。

简而言之，LSP 规定当在子类中覆盖一个方法时，它必须满足以下要求:

1.  它的签名必须与其父签名相匹配
2.  它的前提条件(接受什么)必须相同或更弱
3.  它的职位条件(期望什么)必须相同或更强
4.  异常(如果有)的类型必须与其父异常引发的类型相同

现在，请随意重读上面的列表(不要担心，我会等)，你将有希望意识到为什么这很有意义。

回到这个例子，Neo 最大的错误就是没有保持方法签名的一致性，因此破坏了与客户代码的契约。为了解决这个问题，代理映射器的`findAll()`方法可以用一些条件重写(代码味道的明显标志)，如下所示:

```
<?php
public function findAll() {
    try {
        return ($this->_adapter instanceof PdoAdapter)
            ? $this->_adapter->query("SELECT * FROM " . $this->_table)
            : $this->_adapter->query("SELECT * FROM " . $this->_table, PDO::FETCH_OBJ);
    }
    catch (Exception $e) {
        return array();
    }
}
```

如果您心情不错，尝试一下重构后的方法，无论是使用原生 PDO 对象还是 PDO 适配器的实例，它都会工作得很好。我知道这听起来可能很粗糙，但这只是一个快速而肮脏的修复，公然违反了[打开/关闭原则](http://en.wikipedia.org/wiki/Open/closed_principle)。

另一方面，重构适配器的`query()`方法以匹配其被覆盖的父方法的签名是可行的。但是这样做时，LSP 规定的所有其他条件也应该得到满足。简而言之，这意味着方法重写应该以应有的谨慎进行，并且只有非常非常充分的理由。

在许多用例中，假设不可能使用[接口](http://www.javaworld.com/javaworld/jw-08-2003/jw-0801-toolbox.html)，最好创建仅扩展(而不是覆盖)基类功能的子类。在 Neo 的 PDO 适配器的情况下，这种方法将像一个魔咒一样运行，并且肯定不会破坏任何级别的客户端代码。

正如我刚才所说的，有一个更有效的——但也是激进的——解决方案吸引了实现接口的好处。虽然早期的 PDO 适配器是通过继承创建的，并且不可否认地违反了 LSP 戒律，但是这个缺陷实际上来自于代理映射器类最初的设计方式。实际上，它从上到下依赖于具体的数据库适配器实现，而不是由接口定义的契约。而且大 OO 势力从古至今都说这永远是坏事。

那么，如何实现上述解决方案呢？

## 契约设计与反对继承的案例

首先，有必要定义一个简单的契约，这个契约应该在以后由具体的数据库适配器来实现。一个简单的界面，如下所示，就能很好地完成这个任务:

```
<?php
namespace LibraryDatabase;

interface DatabaseAdapterInterface
{
    public function connect();    
    public function disconnect();
    public function query($sql);
}
```

到目前为止，一切顺利。有了这个接口，生成一个具体的数据库适配器就像创建下面的实现者一样简单:

```
<?php
namespace LibraryDatabase;

class PdoAdapter implements DatabaseAdapterInterface
{
    protected $_config = array();
    protected $_connection;  

    public function __construct($dsn, $username = null, $password = null, array $driverOptions = array())
    {
        if (!is_string($dsn) || empty($dsn)) {
            throw new InvalidArgumentException("The DSN must be a non-empty string.");
        }
        // save connection parameters in the $_config field
        $this->_config = compact("dsn", "username", "password", "driverOptions");
    }

    public function connect()
    {
        // if there is a PDO object already, return early
        if ($this->_connection) {
            return;
        }
        // otherwise try to create a PDO object
        try {
            $this->_connection = new PDO(
                $this->_config["dsn"], 
                $this->_config["username"], 
                $this->_config["password"], 
                $this->_config["driverOptions"]);
            $this->_connection->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
            $this->_connection->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
            $this->_connection->setAttribute(PDO::ATTR_CASE, PDO::CASE_NATURAL);

        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }

    public function disconnect() {
        $this->_connection = null;
    }

    public function query($sql, $fetchStyle = PDO::FETCH_OBJ) {
        $this->connect();
        try {
            return $this->_connection->query($sql, $fetchStyle);

        }
        catch (PDOException $e) {
            throw new RunTimeException($e->getMessage());
        }
    }
}
```

完成了。即使当上面的类非常不自然时，它也整洁地遵守它实现的契约。这使得一堆 SQL 查询可以轻松运行，最重要的是，不会错误地覆盖任何相关的内容。

必须进行的最后一项更改是定义一个额外的[隔离接口](http://en.wikipedia.org/wiki/Interface_segregation_principle)，并重构初始映射器类，以便通过在运行时注入不同的数据库适配器，可以轻松地依次跟踪和击败矩阵中无情的代理。

也就是说，上面提到的界面看起来是这样的:

```
<?php
namespace ModelMapper;

interface AgentMapperInterface {
    public function findAll();
}
```

下面是代理映射器的改进版本:

```
<?php
namespace ModelMapper;
use LibraryDataBaseDatabaseAdapterInterface;

class AgentMapper implements AgentMapperInterface
{
    protected $_adapter;
    protected $_table = "agents"; 

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->_adapter = $adapter;
    }

    public function findAll() {
        try {
            return $this->_adapter->query("SELECT * FROM " . $this->_table);
        }
        catch (Exception $e) {
            return array();
        }
    }      
}
```

任务完成。考虑到现在重构的映射器期望接收一个早期数据库接口的实现者，可以有把握地说，它的`findAll()`方法不需要进行难看的检查来查看它在构造函数中注入了什么。毫无疑问，我们有一个大赢家！

此外，下面的代码片段显示了如何将前面的元素和谐地结合在一起:

```
<?php
use LibraryDatabasePdoAdapter,
    ModelMapperAgentMapper;

// a PSR-0 compliant class loader
require_once __DIR__ . "/Autoloader.php";

$autoloader = new Autoloader();
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=Nebuchadnezzar", "morpheus", "aa26d7c557296a4e8d49b42c8615233a3443036d");
$agentMapper = new AgentMapper($adapter);
$agents = $agentMapper->findAll();

foreach ($agents as $agent) {
    echo "Name: " . $agent->name .  " - Status: " . $agent->status . "<br>";
}
```

一点也不差，是吧？这种方法的缺点是整个重构过程过于激烈。在更现实的用例中，它甚至不是一个可行的解决方案(尤其是在处理大量杂乱的遗留代码时)。尽管如此，它还是简要地展示了如何通过使用组合而不是继承，以及通过契约的少量设计，来创建其衍生物不会打破 LSP 强加的条件的抽象。

当然，我们不要忘记这个故事的士气:与那些喜欢奴役人类并像普通电池一样使用他们的卑鄙机器的艰苦斗争有望以一场彻底的胜利而告终。我们相信尼奥。

*结束。*

## 结束语(矩阵外)

作为面向对象设计的一个中心点，以及[固体原则](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design))中的“L”，Liskov 替代原则多年来已经赢得了许多愤怒的批评者，很可能是因为它的学术定义充满了技术术语，这些术语蒙蔽了人们的视线，使人们难以理解其面纱背后的真正含义。此外，从表面上看，它似乎矛盾，甚至谴责不可避免的存在，继承的厄运。

但这只是一种误导性的印象，一旦人们理解了该原则的实际含义，这种印象就会消失得无影无踪。其核心是，LSP 的承诺旨在设计类的层次结构，暴露真实的相互关系，并且子类可以被它们的基本抽象替换，而不会破坏与客户代码的契约。

所以，一定要坚持这个原则的戒律，你作为 PHP 开发人员的生活(以及你的钱包鼓鼓的客户)将会更加轻松愉快。

## 分享这篇文章