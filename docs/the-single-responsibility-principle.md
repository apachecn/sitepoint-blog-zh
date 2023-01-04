# 单一责任原则

> 原文：<https://www.sitepoint.com/the-single-responsibility-principle/>

健壮的软件系统应该建立在一个相互关联的对象的网络上，这些对象的职责是紧密的、整齐的结合在一起的，可以归结为仅仅执行一些狭窄的和定义良好的任务。然而，不可否认的是，设计这样的系统是相当困难的，至少在第一步。大多数时候，我们倾向于按照与我们人类思维的本性相一致的特定语义来对所讨论的任务进行分组。

这种理性关联过程最臭名昭著的后果之一是，在某些时候，我们实际上最终创建了做得太多的类。所谓的“上帝类”很可能是一种结构的最极端和最粗糙的例子，这种结构将大量不相关的操作打包在同一个 API 的栅栏后面，但是还有其他更微妙、更隐蔽的情况，其中将多个角色分配给同一个类更难跟踪。一个很好的例子就是单例。

我们都知道，多年来，Singletons 一直受到谴责，因为它们遭受了各种讨厌的实现问题，其中经典的可变全局状态是最臭名昭著的。他们也可能因为同时做两件语义上不相关的事情而受到指责:除了扮演他们的主要角色，不管这可能是什么，他们还负责控制原始类应该如何被实例化。单身者是不可避免地被诅咒要执行至少两个不同的任务的实体，而这两个任务彼此之间甚至没有一点联系。

远离单身者而不感到一丝内疚是很容易的。但是，在更一般的、日常的情况下，以及设计那些关注点相当一致的课程时，我们怎么能做到务实呢？即使这个问题没有直接的答案，也可以遵循[单一责任原则](http://www.objectmentor.com/resources/articles/srp.pdf "SRP: The Single Responsibility Principle")的规则，其正式定义如下:

> 一个类的改变不应该有一个以上的原因。

该原则试图提倡的是，类必须总是被设计为只公开一个关注领域，并且它们定义和实现的操作集必须旨在实现该关注领域，而不是其他。换句话说，一个类应该只响应那些语义相关的操作的执行而改变。如果它需要改变来响应另一个完全不相关的操作，那么很明显这个类有不止一个责任。

让我们继续编写一些易于理解的例子，这样我们就可以看到如何利用这个原则的好处。

## 单一责任原则的典型违反

由于显而易见的原因，在很多情况下，分配给一个类的一组看似内聚的操作实际上作用于不同的不相关的职责，因此违反了原则。我发现一个特别有启发性的是[活跃记录模式](http://martinfowler.com/eaaCatalog/activeRecord.html "P of EAA: Active Record")，因为它在过去几年中获得了发展势头。

让我们假设我们是模式和所谓的数据库模型的盲目崇拜者，并希望利用它的细微之处来创建一个基本的实体类，它应该对一般用户的数据和行为进行建模。这样一个类，连同它实现的契约，可以定义如下:

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
    public function getGravatar();

    public function findById($id);
    public function insert();
    public function update();
    public function delete();
}
```

```
<?php
namespace Model;
use LibraryDatabaseDatabaseAdapterInterface;

class User implements UserInterface
{
    private $id;
    private $name;
    private $email;
    private $db;
    private $table = "users";

    public function __construct(DatabaseAdapterInterface $db) {
        $this->db = $db;
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The user ID has been set already.");
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
        $this->name = $name;
        return $this;
    }

    public function getName() {
        if ($this->name === null) {
            throw new UnexpectedValueException(
                "The user name has not been set.");
        }
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
        if ($this->email === null) {
            throw new UnexpectedValueException(
                "The user email has not been set.");
        }
        return $this->email;
    }

    public function getGravatar($size = 70, $default = "monsterid") {
        return "http://www.gravatar.com/avatar/" .
            md5(strtolower($this->getEmail())) .
            "?s=" . (integer) $size .
            "&d=" . urlencode($default) .
            "&r=G";
    }

    public function findById($id) {
        $this->db->select($this->table,
            ["id" => $id]);
        if (!$row = $this->db->fetch()) {
            return null;
        }
        $user = new User($this->db);
        $user->setId($row["id"])
             ->setName($row["name"])
             ->setEmail($row["email"]);
        return $user;
    }

    public function insert() {
        $this->db->insert($this->table, [
            "name"  => $this->getName(), 
            "email" => $this->getEmail()
        ]);
    }

    public function update() {
        $this->db->update($this->table, [
                "name"  => $this->getName(), 
                "email" => $this->getEmail()], 
            "id = {$this->id}");
    }

    public function delete() {
        $this->db->delete($this->table,
            "id = {$this->id}");
    }
}
```

正如人们可能从活动记录的典型实现中所期望的那样，`User`类是一个非常混乱的结构，它混合了大量的业务逻辑，比如设置/获取用户名和电子邮件地址，甚至动态生成一些漂亮的[gravatar](https://en.gravatar.com/ "Gravatar - Global Recognized Avatars")，并带有数据访问。这是否违反了单一责任原则？嗯，毫无疑问是这样的，因为这个类向外部世界公开了两种不同的职责，这两种职责之间没有真正的语义关系。

即使不浏览类的实现，只浏览它的接口，也可以清楚地看到 CRUD 方法应该放在数据访问层中，与 mutator/accessor 的生存和发展完全隔离。在这种情况下，执行`findById()`方法的结果会改变类的状态，而对 setters 的任何调用也会影响 CRUD 操作。这意味着这里有两个重叠的职责共存，这使得类响应不同的需求而变化。

当然，如果你和我一样，你会想知道如何在重构过程中没有太多麻烦的情况下将`User`变成一个单一的符合责任原则的结构。应该引入的第一个修改是将所有的域逻辑保留在类的边界内，同时将处理数据访问的逻辑转移到数据访问层。有一些漂亮的方法可以实现这一点，但是考虑到责任应该分散在多个层上，使用数据映射器是一种有效的方法，允许以一种相当无痛的方式实现这一点。

## 将数据访问逻辑放入数据映射器

将类的职责(当然是与域相关的职责)与处理数据访问的职责隔离开来的最好方法是通过一个基本的数据映射器。下面这一条在划分责任方面做得不错:

```
<?php
namespace Mapper;
use ModelUserInterface;

interface UserMapperInterface
{
    public function findById($id);
    public function insert(UserInterface $user);
    public function update(UserInterface $user);
    public function delete($id);
}
```

```
<?php
namespace Mapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelUserInterface,
    ModelUser;

class UserMapper implements UserMapperInterface
{
    private $db;
    private $table = "users";

    public function __construct(DatabaseAdapterInterface $db) {
        $this->db = $db;
    }

    public function findById($id) {
        $this->db->select($this->table, ["id" => $id]);
        if (!$row = $this->db->fetch()) {
            return null;
        }
        return $this->loadUser($row);
    }

    public function insert(UserInterface $user) {
        return $this->db->insert($this->table, [
            "name"  => $user->getName(), 
            "email" => $user->getEmail()
        ]);
    }

    public function update(UserInterface $user) {
        return $this->db->update($this->table, [
            "name"  => $user->getName(), 
            "email" => $user->getEmail()
        ], 
        "id = {$user->getId()}");
    }

    public function delete($id) {
        if ($id instanceof UserInterface) {
            $id = $id->getId();
        }
        return $this->db->delete($this->table, "id = $id");
    }

    private function loadUser(array $row) {
        $user = new User($row["name"], $row["email"]);
        $user->setId($row["id"]);
        return $user;
    }
}
```

查看映射器的合同，很容易看到以前污染了`User`类生态系统的 CRUD 操作被放置在一个内聚集内是多么好，该集合现在是原始基础设施的一部分，而不是域层。这个简单的修改应该让我们重构域类，并把它变成一个更干净、更精炼的结构，它符合以下原则:

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
    public function getGravatar();
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
                "The user ID has been set already.");
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

    public function getGravatar($size = 70, $default = "monsterid") {
        return "http://www.gravatar.com/avatar/" .
            md5(strtolower($this->email)) .
            "?s=" . (integer) $size .
            "&d=" . urlencode($default) .
            "&r=G";
    }
}
```

可以说,`User`现在有了一个更好的设计实现，因为它执行的批量操作不仅是纯领域逻辑，而且它们在语义上相互绑定。按照单一责任原则的说法，这个类只有一个明确定义的责任，这个责任与处理用户数据密切相关。不多不少。

当然，如果我不展示如何让映射器做所有的数据访问工作，同时保持`User`类的持久性不可知论，这个例子看起来就不完整了:

```
<?php
$db = new PdoAdapter("mysql:dbname=test", "myusername",
    "mypassword");
$userMapper = new UserMapper($db);

// Display user data
$user = $userMapper->findById(1);
echo $user->getName() . ' ' . $user->getEmail() .
    '<img src="' . $user->getGravatar() . '">';

// Insert a new user
$user = new User("John Doe", "john@example.com");
$userMapper->insert($user);

// Update a user
$user = $userMapper->findById(2);
$user->setName("Jack");
$userMapper->update($user);

// Delete a user    
$userMapper->delete(3);
```

虽然这个例子毫无疑问是琐碎的，但是它非常清楚地显示了将执行 CRUD 操作的责任委托给数据映射器这一事实是如何允许我们处理那些唯一关心的领域是专门处理域逻辑的用户对象的。在这一点上，对象的任务是符合原则的，经过很好的提炼，并缩小到设置/检索用户数据和呈现相关的 gravatars，而不是额外关注在存储中持久存储数据。

## 结束语

也许这只是基于我自己作为开发人员的经验的偏见(所以从表面上看)，我敢说单一责任原则最糟糕的诅咒，当然它在实践中被如此公然忽视的原因是现实的实用主义。显然，盲目地将一堆角色分配给一个类，而不考虑它们在语义上是否相互关联，会更容易“完成工作”并与紧迫的期限斗争。

即使在企业环境中，使用契约来明确概述应用程序组件的行为不仅是一种奢侈，而且是必须的，也很难弄清楚如何将一组操作紧密地组合在一起。尽管如此，花点时间设计更干净的类，不要把不相关的职责混在一起，这也没什么坏处。从这个意义上说，这个原则只是一个指导方针，在这个过程中会帮助你，但肯定是一个非常有价值的原则。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章