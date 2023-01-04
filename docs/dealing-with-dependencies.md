# 处理依赖性

> 原文：<https://www.sitepoint.com/dealing-with-dependencies/>

## 组合编程风格

在面向对象的编程风格中，最好将功能分解给多个对象，它们可以一起工作来解决一个任务。在极端情况下，这会导致更多但更小的类，并且通常更少依赖于继承而更多依赖于组合。由于没有更好的词，我将称之为组合编程风格。这种风格通常在有经验的程序员中更流行。

对于来自命令式编程风格的人来说，这种风格可能显得抽象和混乱，但好处在于代码的灵活性。如果不同的对象通过组合相关联，则可以替换部分，而无需更改代码。通过在这里或那里提供包装器，这使得重用组件和挂钩代码变得更加容易。这在测试期间尤其有用，因为模拟外部依赖关系(比如数据库或 smtp 服务器)变得更加容易。

然而，组合也有不好的一面——依赖性。

## 那么什么是依赖呢？

为了这篇文章，我将使用一个相当天真的例子。假设我们正在构建一个地址簿应用程序。这将显示一个类型为`Person`的实体。在我们的代码中，我们可能有一个用于访问数据库中底层表的类。最初，它可能看起来像这样:

```
 class PersonGateway {
  protected $db;
  function __construct() {
    $this->db = new PDO("mysql:host=localhost;dbname=addressbook", "root", "secret");
  }
  function getPerson($id) {
    $stmt = $this->db->prepare("select * from persons where id = :id");
    $stmt->execute(array(':id' => $id));
    return $stmt->fetch();
  }
} 
```

然而，我们还需要一个类型为`Address`的实体，由于这也需要访问数据库，如果我们以同样的方式实现它，我们现在将得到两个连接。我们需要的是一个单一的数据库对象，我们的两个网关类可以共享它。这种对象称为依赖关系[1]——网关类称为依赖关系。

注释:
【1】*有人在评论中向我指出，我在这里使用依赖的含义与在 UML 中使用的略有不同。我所说的这种依赖是在对象实例级别上工作的，而不是在类级别上。[见 honeymonster 的漂亮解释。](https://www.sitepoint.com/dealing-with-dependencies/#comment-624190)*

## 你能把那个附属品递给我吗？

粗略地说，只有两种方法可以满足依赖性。*要么*依赖关系是通过一个全局符号【2】*获得的，要么*它是从外部传入的。全局符号是最不抽象的方法，因此经常吸引经验较少的程序员。然而，它们的具体性也使它们非常不灵活。全局符号通常很难或者不可能在运行时改变，所以聚合的很多好处就失去了。此外，全局变量隐藏了副作用，使它们成为将难以发现的错误引入应用程序的理想容器。出于这些原因，我们将传递依赖项，使我们的网关看起来像这样:

```
 class PersonGateway {
  protected $db;
  function __construct($db) {
    $this->db = $db;
  }
  ...
} 
```

注意:
【2】*对于全局符号，我把全局变量、常量或者任何一种静态成员(包括臭名昭著的 Singleton)*

## 泄漏依赖

一般来说，依赖分析是一个很好的工具。在上面的例子中，如果我们想测试数据库，我们可以很容易地用一个 mock 替换它。然而，这确实需要管理成本。想必我们现在会去写一些应用程序，它依赖于我们的网关。在路上的某个地方，我们发现需要给网关添加一个[身份映射](http://martinfowler.com/eaaCatalog/identityMap.html)。正如组合风格所规定的，我们将这些作为外部依赖项提供，但是为了做到这一点，我们改变了网关的构造函数。

我们现在必须返回并编辑所有地方，在那里网关被实例化并提供新的依赖关系。这并不好——用户不应该因为界面的改变而烦恼，仅仅因为我们重新定义了组件的内部行为。还有另一个问题。用户现在必须意识到神秘的声音类，这不是他真正关心的。我们缺乏封装。

## 编写容器

解决这个问题有不同的方法，不需要解析到全局符号，但是它们的共同点是，它们将构造函数隐藏在工厂中。这样做，依赖关系可以在一个地方解决，对用户来说是隐藏的，用户不会关心那些丑陋的细节。对于我们的示例，容器可能如下所示:

```
 class Container {
  function new_PDO() {
    return new PDO("mysql:host=localhost;dbname=addressbook", "root", "secret");
  }
  function new_PersonGateway() {
    return new PersonGateway($this->new_PDO(), $this->new_IdentityMap());
  }
  function new_AddressGateway() {
    return new AddressGateway($this->new_PDO(), $this->new_IdentityMap());
  }
} 
```

但是等一下。这个容器创建了两个数据库连接，而我们已经决定只需要一个。然而，我们确实需要 identymap 的两个不同实例，因为它们应该跟踪不同的类型。

因此，事实证明，基于它们在应用程序中的独特性，类可以分为两类。有共享对象，如数据库连接，也有瞬态对象，如身份映射。此外，网关本身也是共享的——我们不希望有多个副本。

为了解决这个问题，我们将把容器分成两个不同的部分:容器和工厂。容器保存共享实例并提供面向公众的接口，而工厂包含创建实例的方法。一个简单的实现可能是这样的:

```
 class Container {
  protected $factory;
  protected $instances = array();
  function __construct($factory) {
    $this->factory = $factory;
  }
  function get($classname) {
    $classname = strtolower($classname);
    if (!isset($this->instances[$classname]) {
      $this->instances[$classname] = $this->create($classname);
    }
    return $this->instances[$classname];
  }
  function create($classname) {
    return $this->factory->{'new_'.$classname}($this);
  }
}
class Factory {
  function new_PDO($container) {
    return new PDO("mysql:host=localhost;dbname=addressbook", "root", "secret");
  }
  function new_PersonGateway($container) {
    return new PersonGateway($container->get('PDO'), $container->create('IdentityMap'));
  }
  function new_AddressGateway($container) {
    return new AddressGateway($container->get('PDO'), $container->create('IdentityMap'));
  }
} 
```

## 将容器投入使用

这方面还有改进的空间，但我们已经有了一个非常坚固的容器可以使用。将工厂从容器中分离出来的一个很大的好处是，在测试过程中，删除选定的类是很容易的。假设我想为 PersonGateway 类编写一个单元测试，为此，我想用一个存根替换数据库。方法如下:

```
 class TestFactory extends Factory {
  function new_PDO($container) {
    return new MockPdo();
  }
}
class TestOfPersonGateway extends UnitTestCase {
  function setUp() {
    $this->container = new Container(new TestFactory());
  }
  function test_finder_selects_from_database() {
    $statement = new MockPdoStatement();
    $statement->expectOnce("execute");
    $this->container->get('PDO')->expectOnce("prepare");
    $this->container->get('PDO')->setReturnValue("prepare", $statement);
    $gateway = $this->container->get('PersonGateway');
    $gateway->getPerson(42);
  }
} 
```

因为测试用例使用了真实工厂的子类，所以不需要在每次我决定改变类的依赖关系时更新它。也没有隐藏的副作用；该容器包含任何共享实例，因此对于每个测试，我都得到一个全新的设置。

## 但这还不是全部——今天订购，就可以免费获得一个名称空间！

PHP 5.3 计划很快推出，最令人期待的改进之一是引入名称空间。由于只有一个全局名称空间，PHP 类倾向于让[变得非常冗长](http://framework.zend.com/manual/en/zend.cache.backends.html#zend.cache.backends.memcached)，以避免名称冲突。名称空间允许我们使用较短的名称来命名类，同时仍然避免名称冲突。

这个特性是如此的受欢迎，以至于它被推回到 PHP 5.3 中，尽管它原本计划在 PHP 6 中引入。但是我们不需要等到 PHP 5.3。有了上面的容器，很容易编写一个包装器，它为我们提供了一个名称空间:

```
 class Namespace {
  protected $prefix;
  protected $container;
  function __construct($prefix, $container) {
    $this->prefix = rtrim($prefix, '_') . '_';
    $this->container = $container;
  }
  function get($classname) {
    return $this->container->get($this->prefix . $classname);
  }
  function create($classname) {
    return $this->container->create($this->prefix . $classname);
  }
} 
```

假设我们已经编写了一个工厂，我们现在可以使用 Zend 框架，而不需要超长的名称:

```
 $container = new Namespace("Zend_Cache_Backend", $container);
$memcached = $container->get("Memcached"); 
```

当然，真正的、语言支持的名称空间也缓冲除类名之外的其他符号，但在面向对象的代码中，这种情况很少。例外是 PHP 的遗留过程库，这是无法避免的。在最近的一篇文章中，Mike Naberezny [描述了一种处理这些问题的方法](http://mikenaberezny.com/archives/76)，这种方法与我们的容器非常相似。

*这篇文章已经被翻译成[德语](http://www.tutorials.de/forum/php-tutorials/305677-mit-abhaengigkeiten-arbeiten.html)。*

## 分享这篇文章