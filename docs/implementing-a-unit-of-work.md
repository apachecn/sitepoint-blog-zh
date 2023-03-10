# 实现工作单元——通过事务模型处理域对象

> 原文：<https://www.sitepoint.com/implementing-a-unit-of-work/>

即使在您可以想象的最基本的场景中，应用程序核心的逻辑可以归结为只是从数据库中提取一些记录，赋予一些域对象生命，然后通过一些基本呈现机制的 API 将它们转储到屏幕上，在幕后总有一个正在进行的事务，其最昂贵的方面往往在用户界面吸引人的外部影响下变得模糊不清。

如果您仔细考虑了这一点，您会注意到问题的症结在于事务毫不奇怪地存在于数据库访问的堆中，尽管它们可以通过巧妙的缓存策略得到很大程度的缓解。在相对较小的应用程序中，每个事务中只涉及几个基本的域对象，而且大多数情况下访问数据库只是为了检索数据，在适当的位置放置一个简单的缓存系统肯定有助于提高效率。

虽然悲伤但真实，现实是一个无情的生物，总是对我们大喊大叫，根本不同于我们宁愿听到的甜言蜜语。在大多数情况下，由于域对象固有的、不可避免的可变性(当域类的依赖关系围绕不可变的[值对象](http://martinfowler.com/bliki/ValueObject.html)的概念建模时，很少有例外)，一些对象可能需要跨多个请求进行修改，甚至新的对象也会被放入内存以响应一些用户相关的事件。

简而言之，这意味着即使是没有封装大量额外业务逻辑的虚拟 CRUD 应用程序，在执行多个数据库写入时也会很快变得臃肿，并产生大量开销。如果它们达到了必须处理大量域对象的地步，这些对象必须同步持久化和删除，而又不损害美国编程人员松散地称之为数据完整性，那该怎么办？

让我们诚实面对自己(至少一次)。无论是我们一路走来可以学到的所有高级数据源架构模式，还是我们可能一夜之间发现的酷的新方法，都不能令人满意地处理像从存储中写出和删除多组数据这样可预测和平凡的事情。有鉴于此，我们应该就此放弃，称这个问题几乎是一个失败的事业吗？

诚然，这个问题是反问。事实上，将域对象的集合包装在一个相当灵活的业务事务模型中并一次性执行几次数据库写入/删除是可行的，因此避免了将该过程分解成更多原子的和昂贵的数据库调用，这总是导致每个操作一个会话的反模式。此外，这种基于事务的机制依赖于通常被称为工作单元(UOW)的设计模式的学术形式，它在几个流行的企业级包中的实现非常丰富和成功，比如 T2 Hibernate T3。

另一方面，由于显而易见的原因，PHP 仍然难以在生产中运行各种 uow，除了一些值得信赖的库，如 [Doctrine](http://www.doctrine-project.org/) 和 [RedBeanPHP](http://redbeanphp.com/) ，它们在不同的层次上使用模式的力量来处理和协调实体上的操作。尽管如此，仔细看看 UOW 提供的好处肯定是很有教育意义的，这样你可以看到它们是否符合你的要求。

## 向工作单元注册域对象

在 Martin Fowler 的《企业应用架构的模式》一书中，他讨论了在实现 UOW 时可以遵循的两种主流方法:第一种方法是让 UOW 直接负责注册或排队域对象以进行插入、更新或删除，第二种方法是将这种责任转移给域对象本身。

在这种情况下，由于我想让领域模型只封装我的业务逻辑，并对任何可能存在于其他层中的持久性形式保持不可知，所以我将坚持第一个选项的原则。无论哪种情况，你都可以自由选择你认为最符合要求的方法。

UOW 的轻量级实现可能如下所示:

```
<?php
namespace ModelRepository;
use ModelEntityInterface;

interface UnitOfWorkInterface
{
    public function fetchById($id);
    public function registerNew(EntityInterface $entity);
    public function registerClean(EntityInterface $entity);
    public function registerDirty(EntityInterface $entity);
    public function registerDeleted(EntityInterface $entity);
    public function commit();
    public function rollback();
    public function clear();
}
```

```
<?php
namespace ModelRepository;
use MapperDataMapperInterface,
    LibraryStorageObjectStorageInterface,
    ModelEntityInterface;

class UnitOfWork implements UnitOfWorkInterface
{
    const STATE_NEW     = "NEW";
    const STATE_CLEAN   = "CLEAN";
    const STATE_DIRTY   = "DIRTY";
    const STATE_REMOVED = "REMOVED";

    protected $dataMapper;
    protected $storage;

    public function __construct(DataMapperInterface $dataMapper, ObjectStorageInterface $storage) {
        $this->dataMapper = $dataMapper;
        $this->storage = $storage;
    }

    public function getDataMapper() {
        return $this->dataMapper;
    }

    public function getObjectStorage() {
        return $this->storage;
    }

    public function fetchById($id) {
        $entity = $this->dataMapper->fetchById($id);
        $this->registerClean($entity);
        return $entity;
    }

    public function registerNew(EntityInterface $entity) {
        $this->registerEntity($entity, self::STATE_NEW);
        return $this;
    }

    public function registerClean(EntityInterface $entity) {
        $this->registerEntity($entity, self::STATE_CLEAN);
        return $this;
    }

    public function registerDirty(EntityInterface $entity) {
        $this->registerEntity($entity, self::STATE_DIRTY);
        return $this;
    }

    public function registerDeleted(EntityInterface $entity) {
        $this->registerEntity($entity, self::STATE_REMOVED);
        return $this;
    }

    protected function registerEntity($entity, $state = self::STATE_CLEAN) {
        $this->storage->attach($entity, $state);
    }

    public function commit() {
        foreach ($this->storage as $entity) {
            switch ($this->storage[$entity]) {
                case self::STATE_NEW:
                case self::STATE_DIRTY: 
                    $this->dataMapper->save($entity);
                    break;
                case self::STATE_REMOVED:
                    $this->dataMapper->delete($entity);
            }
        }
        $this->clear();
    }

    public function rollback() {
        // your custom rollback implementation goes here
    }

    public function clear() {
        $this->storage->clear();
        return $this;
    }  
}
```

应该清楚地看到，UOW 只不过是简单的、内存中的对象存储，它跟踪哪些域对象应该被安排插入、更新和删除。简而言之，这个约定可以归结为以下几个方面:需要添加到存储中的域对象将被注册为“新的”；那些正在更新的将被标记为“脏”；那些标有“移除”的将会…是的，从数据库中删除。此外，任何注册为“干净”的对象都将在内存中保持冻结和安全，直到客户端代码明确请求修改其关联状态。

当然，在一个事务中执行这些与持久性相关的操作的方法是`commit()`，它利用一个尚未定义的数据映射器的功能来访问持久层。如果我向您展示在其构造函数中注入的协作者的实现，您会更容易理解 UOW 的内部工作方式，下面是组成对象存储模块的组件:

```
<?php
namespace LibraryStorage;

interface ObjectStorageInterface extends Countable, Iterator, ArrayAccess
{
    public function attach($object, $data = null);
    public function detach($object);
    public function clear();
}
```

```
<?php
namespace LibraryStorage;

class ObjectStorage extends SplObjectStorage implements ObjectStorageInterface
{
    public function clear() {
        $tempStorage = clone $this;
        $this->addAll($tempStorage);
        $this->removeAll($tempStorage);
        $tempStorage = null;
    } 
}
```

特别是在这种情况下，我决定使用稍微定制的 [SplObjectStorage](http://www.php.net/manual/en/class.splobjectstorage.php) 类的实现来注册域对象，而不需要太多麻烦以及它们与 UOW 的相关状态，即使使用普通数组也可以达到几乎相同的效果。同样，由您决定是否使用最适合您需求的方法注册域对象。

定制的`ObjectStorage`类就绪后，让我们看看前面提到的数据映射器的实现:

```
<?php
namespace Mapper;
use ModelEntityInterface;

interface DataMapperInterface
{
    public function fetchById($id);
    public function fetchAll(array $conditions = array());
    public function insert(EntityInterface $entity);
    public function update(EntityInterface $entity);
    public function save(EntityInterface $entity);
    public function delete(EntityInterface $entity);
}
```

```
<?php
namespace Mapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelCollectionEntityCollectionInterface,   
    ModelEntityInterface;

abstract class AbstractDataMapper implements DataMapperInterface
{
    protected $adapter;
    protected $collection;
    protected $entityTable;

    public function __construct(DatabaseAdapterInterface $adapter, EntityCollectionInterface $collection, $entityTable = null) {
        $this->adapter = $adapter;
        $this->collection = $collection;
        if ($entityTable !== null) {
            $this->setEntityTable($entityTable);
        }
    }

    public function setEntityTable($entityTable) {
        if (!is_string($table) || empty($entityTable)) {
            throw new InvalidArgumentException(
                "The entity table is invalid.");
        }
        $this->entityTable = $entityTable;
        return $this;
    }

    public function fetchById($id) {
        $this->adapter->select($this->entityTable, 
            array("id" => $id));
        if (!$row = $this->adapter->fetch()) {
            return null; 
        }
        return $this->loadEntity($row);
    }

    public function fetchAll(array $conditions = array()) {
        $this->adapter->select($this->entityTable, $conditions);
        $rows = $this->adapter->fetchAll();
        return $this->loadEntityCollection($rows);
    }

    public function insert(EntityInterface $entity) {
        return $this->adapter->insert($this->entityTable,
            $entity->toArray());
    }

    public function update(EntityInterface $entity) {
        return $this->adapter->update($this->entityTable,
            $entity->toArray(), "id = $entity->id");
    }

    public function save(EntityInterface $entity) {
        return !isset($entity->id) 
            ? $this->adapter->insert($this->entityTable,
                $entity->toArray()) 
            : $this->adapter->update($this->entityTable,
                $entity->toArray(), "id = $entity->id");   
    }

    public function delete(EntityInterface $entity) {
        return $this->adapter->delete($this->entityTable,
            "id = $entity->id");
    }

    protected function loadEntityCollection(array $rows) {
        $this->collection->clear();
        foreach ($rows as $row) {
            $this->collection[] = $this->loadEntity($row);
        }
        return $this->collection;
    }

    abstract protected function loadEntity(array $row);
}
```

`AbstractDataMapper`在一个非常标准的 API 后面放置了将域对象拉入和拉出数据库所需的大量逻辑。为了使事情变得更简单，派生出它的一个改进的实现也是很好的，这样我们就可以很容易地用几个样本用户对象测试 UOW。这个额外的映射子类如下所示:

```
<?php
namespace Mapper;
use ModelUser;

class UserMapper extends AbstractDataMapper
{
    protected $entityTable = "users";

    protected function loadEntity(array $row) {
        return new User(array(
            "id"    => $row["id"], 
            "name"  => $row["name"], 
            "email" => $row["email"],
            "role"  => $row["role"]));
    }
}
```

在这一点上，我们可以把我们的手放在 UOW 上，看看它的事务模式是否交付了它所承诺的东西。但是在此之前，首先我们确实应该在内存中删除一些域对象。那样的话，我们可以让他们在 UOW 注册。所以现在让我们定义一个原语[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)，它将负责提供所讨论的对象。

## 定义基本的领域模型

坦率地说，有几种方法可以实现功能域模型(很可能每个开发人员都有一个功能域模型)。因为在这种情况下，我希望过程既简单又轻松，所以我将用于测试 UOW 的模型将只由一个原型实体类和一个派生实体组成，派生实体将负责生成基本的用户对象:

```
<?php
namespace Model;

interface EntityInterface
{
    public function setField($name, $value);
    public function getField($name);
    public function fieldExists($name);
    public function removeField($name);
    public function toArray();      
}
```

```
<?php
namespace Model;

abstract class AbstractEntity implements EntityInterface
{
    protected $fields = array(); 
    protected $allowedFields = array(); 

    public function __construct(array $fields = array()) {
        if (!empty($fields)) {
            foreach ($fields as $name => $value) {
                $this->$name = $value;
            } 
        }
    }

    public function setField($name, $value) {
        return $this->__set($name, $value);
    }

    public function getField($name) {
        return $this->__get($name);
    }

    public function fieldExists($name) {
        return $this->__isset($name);
    }

    public function removeField($name) {
        return $this->__unset($name);
    }

    public function toArray() {
        return $this->fields;
    }

    public function __set($name, $value) {
        $this->checkAllowedFields($name);
        $mutator = "set" . ucfirst(strtolower($name));
        if (method_exists($this, $mutator) && 
            is_callable(array($this, $mutator))) {
            $this->$mutator($value);
        }
        else {
            $this->fields[$name] = $value;
        }
        return $this;                 
    }

    public function __get($name) {
        $this->checkAllowedFields($name);
        $accessor = "get" . ucfirst($name);
        if (method_exists($this, $accessor) &&
            is_callable(array($this, $accessor))) {
            return $this->$accessor();
        }
        if (!$this->__isset($name)) {
            throw new InvalidArgumentException(
                "The field '$name' has not been set for this entity yet.");
        }
        return $this->fields[$name];
    }

    public function __isset($name) {
        $this->checkAllowedFields($name);
        return isset($this->fields[$name]);
    }

    public function __unset($name) {
        $this->checkAllowedFields($name);
        if (!$this->__isset($name)) {
            throw new InvalidArgumentException(
                "The field "$name" has not been set for this entity yet.");
        }
        unset($this->fields[$name]);
        return $this;
    }

    protected function checkAllowedFields($field) {
        if (!in_array($field, $this->allowedFields)) {
            throw new InvalidArgumentException(
                "The requested operation on the field '$field' is not allowed for this entity.");
        }
    }
}
```

```
<?php
namespace Model;

class User extends AbstractEntity
{
    const ADMINISTRATOR_ROLE = "Administrator";
    const GUEST_ROLE         = "Guest";

    protected $allowedFields = array("id", "name", "email", "role");

    public function setId($id) {
        if (isset($this->fields["id"])) {
            throw new BadMethodCallException(
                "The ID for this user has been set already.");
        }
        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
                "The user ID is invalid.");
        }
        $this->fields["id"] = $id;
        return $this;
    }

    public function setName($name) {
        if (strlen($name) < 2 || strlen($name) > 30) {
            throw new InvalidArgumentException(
                "The user name is invalid.");
        }
        $this->fields["name"] = htmlspecialchars(trim($name),
            ENT_QUOTES);
        return $this;
    }

    public function setEmail($email) {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException(
                "The user email is invalid.");
        }
        $this->fields["email"] = $email;
        return $this;
    }

    public function setRole($role) {
        if ($role !== self::ADMINISTRATOR_ROLE &&
            $role !== self::GUEST_ROLE) {
            throw new InvalidArgumentException(
                "The user role is invalid.");
        }
        $this->fields["role"] = $role;
        return $this;
    }
}
```

虽然`AbstractEntity`和`User`类的实现乍看起来可能很复杂，但我保证这只是一个模糊的印象。事实上，前者是一些典型 PHP 魔术方法的框架包装器，而后者封装了一些简单的赋值函数，以便为通用用户对象的字段分配适当的值。

这些域类已经在宽松的隔离中完成了它们的工作，现在让我们来做模型的最后一个构建块。实际上，这是一个可选的类，如果情况允许，可以跳过它，它的职责只是包装实体的集合。其实现如下:

```
<?php
namespace ModelCollection;
use ModelEntityInterface;

interface EntityCollectionInterface extends Countable, ArrayAccess, IteratorAggregate 
{
    public function add(EntityInterface $entity);
    public function remove(EntityInterface $entity);
    public function get($key);
    public function exists($key);
    public function clear();
    public function toArray();
}
```

```
<?php
namespace ModelCollection;
use ModelEntityInterface;

class EntityCollection implements EntityCollectionInterface
{
    protected $entities = array();

    public function __construct(array $entities = array()) 
    {
        if (!empty($entities)) {
            $this->entities = $entities;
        }
    }

    public function add(EntityInterface $entity) {
        $this->offsetSet($entity);
    }

    public function remove(EntityInterface $entity) {
        $this->offsetUnset($entity);
    }

    public function get($key) {
        $this->offsetGet($key);

    }

    public function exists($key) {
        return $this->offsetExists($key);
    }

    public function clear() {
        $this->entities = array();
    }

    public function toArray() {
        return $this->entities;
    }

    public function count() {
        return count($this->entities);
    }

    public function offsetSet($key, $entity)
    {
        if (!$entity instanceof EntityInterface) {
            throw new InvalidArgumentException(
                "Could not add the entity to the collection.");
        }
        if (!isset($key)) {
            $this->entities[] = $entity;
        }
        else {
            $this->entities[$key] = $entity;
        }
    }

    public function offsetUnset($key) {
        if ($key instanceof EntityInterface) {
            $this->entities = array_filter($this->entities, 
                function ($v) use ($key) {
                    return $v !== $key;
                });
        }
        else if (isset($this->entities[$key])) {
            unset($this->entities[$key]);
        }
    }

    public function offsetGet($key) {
        if (isset($this->entities[$key])) {
            return $this->entities[$key];
        }
    }

    public function offsetExists($key) {
        return $key instanceof EntityInterface 
            ? array_search($key, $this->entities) 
            : isset($this->entities[$key]);
    }

    public function getIterator() {
        return new ArrayIterator($this->entities);
    }
}
```

在这一点上，我们已经成功地创建了一个原始的域模型，我们当然可以用它来生成用户对象，而不会有什么大的麻烦。在这样做的时候，我们有一个真正的机会来看看 UOW 是否真的是它看起来的功能组件，当它将数据库中的多个实体作为单个事务来持久化时。

## 对 UOW 进行测试

如果您已经读到本文的这一点，您可能会觉得自己被拉向了相反的方向，不知道编写一堆接口和类所需的所有艰苦的前期工作是否真的值得。事实上，的确如此。此外，如果您仍然持怀疑态度，请确保查看下面的代码片段，它显示了如何让 UOW 与一些天真的用户对象进行良好的同步:

```
<?php    
require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=test", "myfancyusername",
    "myhardtoguesspassword");

$unitOfWork = new UnitOfWork(new UserMapper($adapter,
    new EntityCollection), new ObjectStorage);

$user1 = new User(array("name" => "John Doe", 
    "email" => "john@example.com"));
$unitOfWork->registerNew($user1);

$user2 = $unitOfWork->fetchById(1);
$user2->name = "Joe";
$unitOfWork->registerDirty($user2);

$user3 = $unitOfWork->fetchById(2);
$unitOfWork->registerDeleted($user3);

$user4 = $unitOfWork->fetchById(3);
$user4->name = "Julie";

$unitOfWork->commit();
```

抛开一些不相关的细节，比如假设某个地方实际上存在一个 PDO 适配器，早期脚本的驱动逻辑应该很容易理解。简单地说，它展示了如何使用 UOW 进行操作，UOW 从数据库中拖入一些用户对象，并通过使用相应的注册方法对它们进行排队以便插入、更新和删除。在过程的最后，`commit()`只是在注册的对象上进行内部循环，并一次性执行适当的操作。

虽然在标准实现中，UOW 确实公开了我们期望看到的一组典型的注册方法，但是它的正式定义没有提供任何类型的查找器。然而，在这种情况下，我决定有意实现一个通用的，这样您就可以更清楚地看到如何从存储中获取对象，然后将它们注册到 UOW，而不必纠结于一个独立的、更接近于域的奇怪结构，比如一个[存储库](http://martinfowler.com/eaaCatalog/repository.html)或者甚至一个过度的[服务](http://martinfowler.com/eaaCatalog/serviceLayer.html)。

## 结束语

既然您已经在幕后窥视了一个 UOW，并从零开始学习了如何实现一个幼稚的 UOW，那么就让您狂野的一面展现出来，并随心所欲地调整它。

请记住，尽管这种模式有很多好处，但它远不是解决与对持久层的大规模访问相关的所有问题的灵丹妙药。但是，在必须跨多个位置执行昂贵的数据库写入的企业级应用程序中，UOW 提供了一种有效的、类似于事务的方法来减少底层开销，因此当与缓存机制适当耦合时，它成为一种可靠的、多方面的解决方案。

图片 via [朱可夫奥列格](http://www.shutterstock.com/gallery-66737p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章