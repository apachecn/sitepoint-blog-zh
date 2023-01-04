# 处理聚集根的集合——存储库模式

> 原文：<https://www.sitepoint.com/handling-collections-of-aggregate-roots/>

传统[领域驱动设计](http://en.wikipedia.org/wiki/Domain-driven_design) (DDD)架构的一个最典型的方面是由[领域模型](http://en.wikipedia.org/wiki/Domain_model)暴露的强制性持久性不可知论。在更保守的设计中，包括几个基于[活动记录](http://martinfowler.com/eaaCatalog/activeRecord.html)或[数据表网关](http://martinfowler.com/eaaCatalog/tableDataGateway.html)的实现(这些实现追求相当欺骗性的简单性，最终往往会损害基础设施的领域逻辑)，总是有一个明确的底层存储机制的概念，通常是关系数据库。另一方面，域模型从一开始就在概念上被设计成严格的“不知道存储”的性质，因此将任何持久性逻辑移出了它们的边界。

即使考虑到 DDD 在直接引用“数据库”时有些难以捉摸，但在现实世界中，很可能至少有一个人坐在幕后，因为领域模型最终必须以某种形式持久化。因此，在模型和数据访问层之间部署一个映射层是很常见的。这不仅积极地推动了各层之间保持适当的隔离级别，而且还屏蔽了在客户端代码中来回移动域对象所涉及的每个复杂细节。

撇开过失不谈，公平地说，处理一层奇怪的[数据映射器](http://martinfowler.com/eaaCatalog/dataMapper.html)是相当大的负担，经常陷入“一次编码/永远使用”的策略。尽管如此，上面的模式在相当简单的情况下表现得相当好，在这种情况下，只有少数映射器处理少数几个域类。然而，当模型开始膨胀并增加复杂性时，情况会变得更加尴尬，因为随着时间的推移，肯定会添加额外的映射器。

简而言之，这表明在使用由几个复杂的聚合根组成的丰富的领域模型时，打开持久性无知的大门在实践中是非常困难的，至少在不需要在多个地方创建昂贵的对象图或重复实现的情况下是如此。更糟糕的是，在需要从数据库中提取匹配不同标准的[聚合根](http://en.wikipedia.org/wiki/Domain-driven_design)的昂贵集合的大型系统中，当没有通过单个入口点适当地集中时，整个查询过程本身可能是这种有缺陷的复制的活跃、多产的推动者。

在这种复杂的用例中，实现一个额外的抽象层，在 DDD 的说法中通常称为[存储库](http://martinfowler.com/eaaCatalog/repository.html)，它在数据映射器和域模型之间起中介作用，可以有效地帮助将查询逻辑重复减少到最小，同时向模型公开真实内存集合的语义。

然而，与作为基础设施一部分的制图者不同，存储库将自己描述为说模型的语言，因为它与模型紧密相关。由于它对映射器的隐式依赖，它还保留了对持久性的无知，因此提供了更高级别的数据抽象，更接近于域对象。

可悲但真实的是，存储库给桌面带来的好处并不能为可能存在的每一个应用程序轻易实现，因此只有在情况允许时才值得实现。无论如何，从零开始构建一个小的存储库是非常有益的，这样您就可以看到它的内部工作方式，并揭示其相当深奥的外壳下的实际内容。

## 做一些初步的基础工作

实现存储库的过程可能相当复杂，因为它实际上将注入和处理数据映射器的所有细节都隐藏在一个简化的类似集合的 API 后面，该 API 反过来还会注入某种持久性适配器，等等。这种依赖关系的连续注入，加上大量逻辑的隐藏，解释了为什么一个存储库经常被认为是一个简单的[外观](http://en.wikipedia.org/wiki/Facade_pattern)，即使当前有些观点偏离了这个概念。

在这两种情况下，我们应该采取的第一个步骤是创建一个基本的领域模型，以建立并运行一个功能库。我计划在这里使用的这个将负责建模一般用户的任务，它的基本结构如下所示:

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

    public function setRole($role);
    public function getRole();
}
```

```
<?php
namespace Model;

class User implements UserInterface
{
    const ADMINISTRATOR_ROLE = "Administrator";
    const GUEST_ROLE         = "Guest";

    protected $id;
    protected $name;
    protected $email;
    protected $role;

    public function __construct($name, $email, $role = self::GUEST_ROLE) {
        $this->setName($name);
        $this->setEmail($email);
        $this->setRole($role);
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this user has been set already.");
        }
        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
                "The user ID is invalid.");
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
        $this->name = htmlspecialchars(trim($name), ENT_QUOTES);
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

    public function setRole($role) {
        if ($role !== self::ADMINISTRATOR_ROLE
            && $role !== self::GUEST_ROLE) {
            throw new InvalidArgumentException(
                "The user role is invalid.");
        }
        $this->role = $role;
        return $this;
    }

    public function getRole() {
        return $this->role;
    }
}
```

特别是在这种情况下，域模型是一个非常骨架的层，仅仅位于能够验证自身的普通数据持有者之上，它仅仅通过一个隔离的接口和一个平庸的实现者来定义一些虚构用户的数据和行为。为了保持事物的整洁和易于理解，我将保持模型很薄。

模型已经在宽松的隔离状态下运行了，让我们通过添加一个额外的类来使它更丰富一点，这个类负责处理用户对象的集合。这个“附录”组件只是一个实现了 [`Countable`](http://php.net/manual/en/class.countable.php) 、 [`ArrayAccess`](http://www.php.net/manual/en/class.arrayaccess.php) 和 [`IteratorAggregate`](http://www.php.net/manual/en/class.iteratoraggregate.php) SPL 接口的经典数组包装器:

```
<?php
namespace ModelCollection;
use MapperUserCollectionInterface,
    ModelUserInterface;

class UserCollection implements UserCollectionInterface
{
    protected $users = array();

    public function add(UserInterface $user) {
        $this->offsetSet($user);
    }

    public function remove(UserInterface $user) {
        $this->offsetUnset($user);
    }

    public function get($key) {
        return $this->offsetGet($key);
    }

    public function exists($key) {
        return $this->offsetExists($key);
    }

    public function clear() {
        $this->users = array();
    }

    public function toArray() {
        return $this->users;
    }

    public function count() {
        return count($this->users);
    }

    public function offsetSet($key, $value) {
        if (!$value instanceof UserInterface) {
            throw new InvalidArgumentException(
                "Could not add the user to the collection.");
        }
        if (!isset($key)) {
            $this->users[] = $value;
        }
        else {
            $this->users[$key] = $value;
        }
    }

    public function offsetUnset($key) {
        if ($key instanceof UserInterface) {
            $this->users = array_filter($this->users,
                function ($v) use ($key) {
                    return $v !== $key;
                });
        }
        else if (isset($this->users[$key])) {
            unset($this->users[$key]);
        }
    }

    public function offsetGet($key) {
        if (isset($this->users[$key])) {
            return $this->users[$key];
        }
    }

    public function offsetExists($key) {
        return ($key instanceof UserInterface)
            ? array_search($key, $this->users)
            : isset($this->users[$key]);
    }

    public function getIterator() {
        return new ArrayIterator($this->users);
    }
}
```

事实上，将这个数组集合放在模型的边界内是完全可选的，因为使用普通数组可以产生几乎相同的结果。然而，在这种情况下，通过依赖独立的集合类，可以更容易地访问通过面向对象的 API 从数据库中获取的用户对象集。

此外，考虑到域模型必须完全不了解基础设施中设置的底层存储，我们应该采取的下一个逻辑步骤是实现一个映射层，使其与数据库保持良好的分离。以下是构成这一层的元素:

```
<?php
namespace Mapper;
use ModelUserInterface;

interface UserCollectionInterface extends Countable, ArrayAccess, IteratorAggregate 
{
    public function add(UserInterface $user);
    public function remove(UserInterface $user);
    public function get($key);
    public function exists($key);
    public function clear();
    public function toArray();
}
```

```
<?php
namespace Mapper;
use ModelRepositoryUserMapperInterface,  
    ModelUser;

class UserMapper implements UserMapperInterface
{    
    protected $entityTable = "users";
    protected $collection;

    public function __construct(DatabaseAdapterInterface $adapter, UserCollectionInterface $collection) {
        $this->adapter = $adapter;
        $this->collection = $collection;
    }

    public function fetchById($id) {
        $this->adapter->select($this->entityTable,
            array("id" => $id));
        if (!$row = $this->adapter->fetch()) {
            return null;
        }
        return $this->createUser($row);
    }

    public function fetchAll(array $conditions = array()) {
        $this->adapter->select($this->entityTable, $conditions);
        $rows = $this->adapter->fetchAll();
        return $this->createUserCollection($rows);

    }

    protected function createUser(array $row) {
        $user = new User($row["name"], $row["email"],
            $row["role"]);
        $user->setId($row["id"]);
        return $user;
    }

    protected function createUserCollection(array $rows) {
        $this->collection->clear();
        if ($rows) {
            foreach ($rows as $row) {
                $this->collection[] = $this->createUser($row);
            }
        }
        return $this->collection;
    }
}
```

开箱即用，`UserMapper`执行的任务相当简单，仅限于公开几个通用查找器，它们负责从数据库中提取用户，并通过`createUser()`方法重建相应的实体。此外，如果你以前已经埋头研究过一些地图绘制工具，或者甚至写过自己的地图杰作，那么上述内容应该很容易理解。很可能唯一值得强调的细微细节是`UserCollectionInterface`被放置在贴图层，而不是模型层。在这种情况下，我决定特意这样做，因为用户集合所依赖的抽象(协议)是由更高级别的`UserMapper`明确声明和拥有的，与[依赖倒置原则](http://en.wikipedia.org/wiki/Dependency_inversion_principle)所提倡的指导方针一致。

设置好映射器后，我们可以直接使用它，并从存储中提取一些用户对象，让模型瞬间水化。虽然乍一看这似乎是正确的选择，但实际上我们会不必要地污染基础设施的应用程序逻辑，因为映射器实际上是基础设施的一部分。如果将来有必要根据更精炼的、特定于领域的条件来查询用户实体，而不仅仅是映射器的查找器所公开的总括条件，那该怎么办？

在这种情况下，确实需要在映射层之上放置一个额外的层，这不仅可以提供更高级别的数据访问，而且可以通过一个单点传送大量的查询逻辑。在最后一个实例中，这就是我们期望从一个存储库中获得的大量好处。

## 实现用户存储库

在生产中，存储库可以在其表面下实现几乎所有你能想到的东西，以便在模型上展示内存中聚集根集合的假象。然而，在这种情况下，我们不能如此天真地期望免费享受如此昂贵的奢侈品，因为我们将要构建的存储库将是一个非常人造的结构，负责从数据库中获取用户:

```
<?php
namespace ModelRepository;

interface UserMapperInterface
{
    public function fetchById($id);
    public function fetchAll(array $conditions = array());
}
```

```
<?php
namespace ModelRepository;

interface UserRepositoryInterface
{
    public function fetchById($id);
    public function fetchByName($name);
    public function fetchbyEmail($email);
    public function fetchByRole($role);
}
```

```
<?php
namespace ModelRepository;

class UserRepository implements UserRepositoryInterface
{
    protected $userMapper;

    public function __construct(UserMapperInterface $userMapper) {
        $this->userMapper = $userMapper;
    }

    public function fetchById($id) {
        return $this->userMapper->fetchById($id);
    }

    public function fetchByName($name) {
        return $this->fetch(array("name" => $name));
    }

    public function fetchByEmail($email) {
        return $this->fetch(array("email" => $email));
    }

    public function fetchByRole($role) {
        return $this->fetch(array("role" => $role));
    }

    protected function fetch(array $conditions) {
        return $this->userMapper->fetchAll($conditions);
    }
}
```

尽管是建立在一个轻量级的结构之上，但是考虑到它的 API 允许它从存储中提取用户对象的集合，这些对象符合与模型语言密切相关的精炼谓词，所以`UserRepository`的实现是非常直观的。此外，在当前状态下，存储库只向客户端代码公开了一些简单的查找器，这些查找器反过来利用数据映射器的功能来访问存储。

在一个更现实的环境中，一个存储库也应该具有持久化聚合根的能力。如果你有心情向`UserRepository`推销一个`insert()`方法或其他类似的东西，请随意。

在这两种情况下，获取使用存储库的实际优势的一种有效方式是通过例子。

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter,
    MapperUserMapper,
    ModelCollectionUserCollection,
    ModelRepositoryUserRepository;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=users", "myfancyusername", "mysecretpassword");
$userRepository = new UserRepository(new UserMapper($adapter, 
    new UserCollection()));

$users = $userRepository->fetchByName("Rachel");
foreach ($users as $user) {
    echo $user->getName() . " " . $user->getEmail() . "<br>";
}

$users = $userRepository->fetchByEmail("username@domain.com");
foreach ($users as $user) {
    echo $user->getName() . " " . $user->getEmail() . "<br>";
}

$administrators = $userRepository->fetchByRole("administrator");
foreach ($administrators as $administrator) {
    echo $administrator->getName() . " " . 
        $administrator->getEmail() . "<br>";
}

$guests = $userRepository->fetchByRole("guest");
foreach ($guests as $guest) {
    echo $guest->getName() . " " . $guest->getEmail() . "<br>";
}
```

如前所述，存储库有效地将业务术语与客户端代码(所谓的[通用语言](http://martinfowler.com/bliki/UbiquitousLanguage.html)由 Eric Evans 在他的书[领域驱动设计](http://www.amazon.com/gp/product/0321125215?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321125215)中创造)进行交换，而不是一种较低级的技术术语。与数据映射器的查找器中存在的模糊性不同，另一方面，存储库的方法用“名称”、“电子邮件”和“角色”来描述自己，这些当然是对用户实体建模的属性的一部分。

这种经过提炼的更高层次的数据抽象，以及在复杂系统中封装查询逻辑所需的一系列成熟的功能，无疑是在多层设计中使用存储库的最有吸引力的原因。当然，大多数情况下，在预先获得这些好处和经历部署额外抽象层的麻烦之间有一个隐含的权衡，在更适度的应用程序中，这可能是多余的。

## 结束语

作为领域驱动设计的核心概念之一，存储库可以在用其他语言编写的应用程序中找到，比如 Java 和 C#，等等。然而，在 PHP 中，它们仍然相对不为人知，只是在这个世界上迈出了它们害羞的第一步。尽管如此，还是有一些值得信赖的框架，比如 [FLOW3](http://flow3.typo3.org/) ，当然还有 [Doctrine 2.x](http://www.doctrine-project.org/) ，它们将帮助你拥抱 DDD 范式。

与任何开发方法一样，您不必在应用程序中使用存储库，甚至不必用 DDD 背后的一堆概念来破坏它们。运用常识，只有当你认为它们会符合你的需求时，才选择它们。真的就这么简单。

图片 via[Chance Agrella](http://freerangestock.com/view_photog.php?photogid=2 "freerangestock.com | Free Stock Photos - Totally Free Commercial Photography and Textures")/[Freerangestock.com](http://freerangestock.com "freerangestock.com | Free Stock Photos - Totally Free Commercial Photography and Textures")

## 分享这篇文章