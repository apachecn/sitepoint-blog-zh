# 空对象模式——领域模型中的多态性

> 原文：<https://www.sitepoint.com/the-null-object-pattern-polymorphism-in-domain-models/>

虽然肯定与规范相去甚远，但可以说[正交性](http://en.wikipedia.org/wiki/Orthogonality_%28programming%29 "Orthognonality (programming) - Wikipedia, the free encyclopedia")是建立在“良好设计”基础上的软件系统的精髓，其中组成模块彼此之间巧妙地解耦，使系统不容易受到刚性和脆弱性问题的影响。当然，谈论正交系统的好处比在生产中建立并运行一个实际的系统要容易得多；这个过程往往是对乌托邦的追求。

尽管如此，在一个系统中实现高度解耦的组件绝对不是一个乌托邦式的概念。几个编程概念的使用，例如[多态性](http://en.wikipedia.org/wiki/Polymorphism_%28computer_science%29 "Polymorphism_(computer science) - Wikipedia, the free encyclopedia")，允许人们设计灵活的程序，其部分可以在运行时切换，其依赖性可以以抽象的形式而不是具体的实现来表达。我敢说，随着时间的推移，旧的“编程到接口”的咒语已经得到了无处不在的采用，不管我们是在谈论实现基础设施还是应用程序逻辑。

然而，当踏上[领域模型](https://www.sitepoint.com/building-a-domain-model/ "Building a Domain Model – An Introduction to Persistence Agnosticism")的领域时，事情就完全不同了。坦率地说，这是一个可以预见的情况。毕竟，一个由相互连接的对象组成的网络，其数据和行为被约束到定义良好的业务规则，为什么应该是多态的呢？本身没多大意义。不过，这条规则也有一些例外，可能最终会特别适用于这种情况。

第一个例外是使用了虚拟代理，它有效地共享了与实际域对象实现的接口相同的接口。另一个例外是所谓的“空情况”，这是一种特殊的情况，操作可能最终分配或返回空值，而不是一个很好填充的实体。在传统的非多态方法中，模型的消费者必须检查这些“邪恶的”空值，并优雅地处理条件，从而在代码中产生大量的条件。

幸运的是，这种看似复杂的情况可以简单地通过创建域对象的多态实现来解决，它将实现与所讨论的对象相同的接口，只是它的方法不会做任何事情，因此在执行操作时，客户端代码不必重复检查难看的空值。毫不奇怪，这种方法是一种叫做 [Null Object](http://en.wikipedia.org/wiki/Null_Object_pattern "Null Object pattern - Wikipedia, the free encyclopedia") 的设计模式，它将多态性的优势发挥到了极致。

在本文中，我将在几种情况下展示这种模式的好处，并向您展示它们对多态方法的依赖有多好。

## 处理非多态条件

正如所料，在展示空对象模式的优点时，有几条路可以走。我发现一个特别简单的方法是实现一个数据映射器，它可能最终从一个通用的查找器返回一个空值。假设我们已经成功地创建了一个仅由一个用户实体组成的框架域模型。该接口及其类如下所示:

```
<?php
namespace Model;

interface UserInterface
{
    public function setId($id);
    public function getId();

    public function setName($name);
    public function getName();

    public function setEmail($email);
    public function getEmail();
}
```

```
<?php
namespace Model;

class User implements UserInterface
{
    private $id;
    private $name;
    private $email;

    public function __construct($name, $email) {
        $this->setName($name);
        $this->setEmail($email);
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this user has been set already.");
        }
        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
              "The ID for this user is invalid.");
        }
        $this->id = $id;
        return $this;
    }

    public function getId() {
        return $this->id;
    }

    public function setName($name) {
        if (strlen($name) < 2 || strlen($name) > 30) {
            throw new InvalidArgumentException(
                "The user name is invalid.");
        }
        $this->name = $name;
        return $this;
    }

    public function getName() {
        return $this->name;
    }

    public function setEmail($email) {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException(
                "The user email is invalid.");
        }
        $this->email = $email;
        return $this;
    }

    public function getEmail() {
        return $this->email;
    }
}
```

`User`类是一个反应式结构，它实现了一些赋值函数/访问函数，以便定义一些用户的数据和行为。

有了这个设计好的域类，现在我们可以更进一步，定义一个基本的[数据映射器](http://martinfowler.com/eaaCatalog/dataMapper.html "P of EAA: Data Mapper")，它将保持我们的域模型和数据访问层相互隔离。

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelUser;

class UserMapper implements UserMapperInterface
{   
    private $adapter;

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function fetchById($id) {
        $this->adapter->select("users", array("id" => $id));
        if (!$row = $this->adapter->fetch()) {
            return null;
        }
        return $this->createUser($row);
    }

    private function createUser(array $row) {
        $user = new User($row["name"], $row["email"]);
        $user->setId($row["id"]);
        return $user;
    }
}
```

首先应该指出的是，映射器的`fetchById()`方法是块中的淘气男孩，因为如果数据库中没有用户匹配给定的 ID，它将有效地返回 null。由于显而易见的原因，这种笨拙的条件使得客户端代码每次调用映射器的 finder 时都要经历检查 null 值的麻烦。

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter,
    ModelMapperUserMapper;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=test", "myusername", "mypassword");

$userMapper = new UserMapper($adapter);

$user = $userMapper->fetchById(1);

if ($user !== null) {
    echo $user->getName() . " " . $user->getEmail();
}
```

乍一看，这不是什么大问题，只要检查是在一个地方完成的。但是，如果相同的行出现在多个页面控制器中或一个服务层中，难道你不会去碰壁吗？不知不觉中，映射器返回的看似无辜的 null 产生了重复条件的瘟疫，这是糟糕设计的不祥之兆。

## 从客户端代码中移除条件

然而，没有必要感到痛苦，因为这正是空对象模式说明为什么多态是天赐之物的那种情况。如果我们想一劳永逸地摆脱那些讨厌的条件，我们可以实现前面的`User`类的多态版本。

```
<?php
namespace Model;

class NullUser implements UserInterface
{
    public function setId($id) { }
    public function getId() { }

    public function setName($name) { }
    public function getName() { }

    public function setEmail($email) { }
    public function getEmail() { }
}
```

如果你期望一个完全成熟的实体类包装各种各样的花里胡哨，恐怕你会大失所望。实体的“空”版本符合相应的接口，但是方法是没有实际实现的空包装器。

虽然`NullUser`类的存在显然没有给我们带来任何值得称赞的有用的东西，但它是一个整洁的生物，允许我们将所有以前的条件扔进垃圾桶。想看看怎么样吗？

首先，我们应该做一些前期工作，重构数据映射器，使其查找器返回一个空用户对象，而不是空值。

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelUser,
    ModelNullUser;

class UserMapper implements UserMapperInterface
{   
    private $adapter;

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function fetchById($id) {
        $this->adapter->select("users", array("id" => $id));
        return $this->createUser($this->adapter->fetch());
    }

    private function createUser($row) {
        if (!$row) {
            return new NullUser;
        }
        $user = new User($row["name"], $row["email"]);
        $user->setId($row["id"]);
        return $user; 
    }
}
```

mapper 的`createUser()`方法隐藏了一个微小的条件，因为现在它负责在传递给 finder 的 ID 没有返回有效用户时创建一个空用户。即便如此，这种微妙的惩罚不仅使客户端代码免于做大量的重复检查，而且将它变成了一个宽容的消费者，当它不得不处理一个空用户时，它不会抱怨。

```
<?php
$user = $userMapper->fetchById("This ID is invalid...");

echo $user->getName() . " " . $user->getEmail();
```

这种多态方法的主要缺陷是任何使用它的应用程序都会变得过于宽容，因为它在处理无效实体时不会崩溃。在最糟糕的情况下，用户界面会显示一些空白行，但没有什么真正的噪音让我们畏缩。当扫描早期`NullUser`类的当前实现时，这一点尤其明显。

尽管如此，在保持逻辑的多态本质不变的情况下，将逻辑封装在空对象中是可行的，甚至是值得推荐的。我甚至可以说，空对象是封装默认数据和行为的绝佳选择，这些数据和行为只应在少数特殊情况下向客户端代码公开。

如果你有足够的雄心，并且想使用天真的空用户对象来尝试这个概念，那么当前的`NullUser`类可以按以下方式重构:

```
<?php
namespace Model;

class NullUser implements UserInterface
{
    public function setId($id) { }
    public function getId() {
        return "The requested ID does not correspond to a valid user.";
    }

    public function setName($name) { }
    public function getName() {
        return "The requested name does not correspond to a valid user.";
    }

    public function setEmail($email) { }
    public function getEmail() {
        return "The requested email does not correspond to a valid user.";
    }
}
```

增强版的`NullUser`比它安静的前身表达能力稍强，因为它的 getters 提供了一些基本的实现，可以在无效用户被请求时返回一些默认消息。

虽然这种变化微不足道，但它对客户端代码处理空用户的方式有积极的影响，因为这一次消费者至少清楚地知道，当他们试图从存储中提取一个不存在的用户时，出现了问题。这是一个很好的突破，它不仅展示了如何实现实际上根本不为空的空对象，还展示了根据特定需求在相关对象内部移动逻辑是多么容易。

## 结束语

有些人可能会说，经历实现空对象的麻烦是多余的，特别是在 PHP 中，核心 OOP 概念，如多态性，被公然低估了。他们在某种程度上是对的。

尽管如此，逐渐采用值得信赖的编程原则和设计模式，以及该语言的对象模型目前达到的成熟水平，为坚定地向前迈进并开始使用一些不久前还被认为是混乱、不切实际的概念的小“奢侈品”提供了所有必要的基础。Null Object 模式就属于这一类，但是它的实现如此简单优雅，以至于在清理客户机代码中重复的 Null 值检查时，很难不发现它的吸引力。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章