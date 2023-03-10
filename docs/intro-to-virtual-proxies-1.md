# 虚拟代理介绍，第 1 部分

> 原文：<https://www.sitepoint.com/intro-to-virtual-proxies-1/>

[多态性](http://en.wikipedia.org/wiki/Polymorphism_(computer_science))最吸引人的方面之一是它适用于各种各样的情况。此外，尽管切换出具体实现的能力本身就是一个基本的面向对象的支柱，通常用于创建松耦合、可互换的组件，但当涉及到控制域对象时，它的用法就不那么好用了。

这种观察背后有一个有点脆弱的基本原理:在许多情况下，领域对象在概念上被预先建模，以在具体的数据集上操作，并在最有可能不会随时间改变的严格关系的边界内操作，并且大多数变化的业务逻辑被放置在独立策略类的层次结构中(这里是实际发生多态性的地方)。在这种情况下，除了模仿/测试之外，肯定没有太多的空间甚至令人信服的理由来证明在运行时拥有不同的域对象实现是正确的。

此外，很少有人会不同意对接口编程是一件坏事，但是每个域对象都有一个接口不是太过分了吗？毕竟，一个用户对象总是会被一些典型的角色记住，如果它的 login()方法需要更新，那么它将被相应地重构，只要双方的契约得到维护，客户端代码就不会抱怨。乍一看，拥有一个全新的用户实现不仅不太实用，而且简直荒谬。

当需要从数据库中提取一个集合(一个由其他对象或集合组成的域对象)时，这种方法的一个常见缺陷就显而易见了。由于对集合的每次引用都意味着要面对面地处理真实的域对象，因此这个过程最终不可避免地会将整个对象图放到客户机中。即使这些对象可以在以后被缓存，也不能完全解决热情赞扬的问题。

通常，简单的解决方案是最有效的，这也适用于上述问题。如果使用一个轻量级的替代品，而不是获取真正的胖集合，它共享相同的接口，并且知道如何从存储中获取有问题的集合，那么[延迟加载](http://martinfowler.com/eaaCatalog/lazyLoad.html)底层对象就变成了一个简单的过程。这种替代物通常被称为[虚拟代理](http://en.wikipedia.org/wiki/Proxy_pattern)，这是一种利用多态简洁并与实际域对象交互的替身。

代理对 PHP 来说并不陌生。[主义](http://www.doctrine-project.org/)和 [Zend Framework 2.x](http://framework.zend.com/) 利用了它们，虽然目的不同。然而，作为一个说教的理由，实现一些定制的代理类并使用它们从数据库中延迟加载一些基本的集合是很有启发性的，这种方式说明了虚拟代理是如何在幕后完成它们的工作的。

考虑到代理的功能可以很容易地与单个域对象或它们的集合(或两者)相协调，在这个由两部分组成的系列文章的第一部分中，我将展示前一个用例，而在第二部分中，我将深入探讨后者的复杂性。

所以，现在让我们继续，让虚拟代理最终运转起来。

## 设置域模型

正如我在介绍中指出的，代理是简单但功能强大的结构，它允许您从底层存储中获取请求的域对象。但是正如人们可能预料的那样，首先应该创建一个简单的[域模型](https://www.sitepoint.com/building-a-domain-model/)，这样至少有一个构建对象可以连接到代理。

我将添加到样本模型中的第一个域类是一个表示博客文章的类。该类及其隔离的接口如下所示:

```
<?php
namespace Model;

interface PostInterface
{
    public function setId($id);
    public function getId();

    public function setTitle($title);
    public function getTitle();

    public function setContent($content);
    public function getContent();

    public function setAuthor(AuthorInterface $author);
    public function getAuthor();
}
```

```
<?php
namespace Model;

class Post implements PostInterface
{
    protected $id;
    protected $title;
    protected $content;
    protected $author;

    public function __construct($title, $content, AuthorInterface $author) {
        $this->setTitle($title);
        $this->setContent($content);
        $this->setAuthor($author);
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this post has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
                "The post ID is invalid.");
        }

        $this->id = $id;
        return $this;
    }

    public function getId() {
        return $this->id;
    }

    public function setTitle($title) {
        if (!is_string($title) 
            || strlen($title) < 2 
            || strlen($title) > 100) {
            throw new InvalidArgumentException(
                "The post title is invalid.");
        }

        $this->title = htmlspecialchars(trim($title), ENT_QUOTES);
        return $this;
    }

    public function getTitle() {
        return $this->title;
    }

    public function setContent($content) {
        if (!is_string($content) || strlen($content) < 2) {
            throw new InvalidArgumentException(
                "The post content is invalid.");
        }

        $this->content = htmlspecialchars(trim($content), ENT_QUOTES);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setAuthor(AuthorInterface $author) {
        $this->author = $author;
        return $this;
    }

    public function getAuthor() {
        return $this->author;
    }
}
```

`Post`类的行为是微不足道的，因为它只是实现了一些带有一些基本过滤/验证的赋值函数/访问函数。但是，请注意，该类在构造函数中注入了作者的实现，这样就与相应的作者建立了一对一的关系。

要建立并运行领域模型，还必须对有问题的作者进行建模。下面是相关的类及其契约:

```
<?php
namespace Model;

interface AuthorInterface
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

class Author implements AuthorInterface
{
    protected $id;
    protected $name;
    protected $email;

    public function __construct($name, $email) {
        $this->setName($name);
        $this->setEmail($email);
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this author has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
                "The author ID is invalid.");
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
                "The name of the author is invalid.");
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
                "The email of the author is invalid.");
        }

        $this->email = $email;
        return $this;
    }

    public function getEmail() {
        return $this->email;
    }
}
```

我刚才说关于`Post`类的事情同样适用于它的合作者`Author`。这里有一个微妙的细节值得强调:如果域模型现在就投入使用，那么从数据库中提取的任何 post 对象都会得到一个 author 对象，这是一个完全符合其聚合条件的对象。

虽然这一切都很好，因为设置作者本身并不昂贵，但有时它们可能只能在请求时加载。如果发生这种情况，所有最后的检索逻辑都应该放在域对象的范围之外，但同时由理解模型的对象控制。

这里没有必要陷入痛苦之中，因为这种看似复杂的情况有一个非常简单的解决方案:如果使用一个作者代理而不是一个真正的作者，那么在不破坏与客户端代码的契约的情况下延迟加载后者是可行的，因为代理公开了相同的 API。这本身就证明了当为了优化数据库持久化/检索策略而交换域对象实现时，多态性是很难克服的。

好奇心确实是一个很痒的 bug，所以让我们满足自己的好奇心，看看如何创建上面提到的作者代理。

## 通过虚拟代理根据请求加载作者

考虑到代理应该能够从数据库中获取作者对象，一个简单的方法是通过数据映射器的 API。在这种特殊情况下，下面显示的方法可以很好地完成工作:

```
<?php namespace ModelMapper;

interface AuthorMapperInterface
{
    public function fetchById($id);
}
```

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelAuthor;

class AuthorMapper implements AuthorMapperInterface
{    
    protected $entityTable = "authors";

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function fetchById($id) {
        $this->adapter->select($this->entityTable, 
            array("id" => $id));

        if (!$row = $this->adapter->fetch()) {
            return null;
        }

        return new Author($row["name"], $row["email"]);
    }
}
```

到目前为止，一切顺利。用户映射器已经就绪，现在是处理创建作者代理的时候了。如前所述，它与真实作者实现的接口共享相同的接口，其核心实现如下:

```
<?php
namespace ModelProxy;
use ModelMapperAuthorMapperInterface,
    ModelAuthorInterface;

class AuthorProxy implements AuthorInterface
{
    protected $author;
    protected $authorId;
    protected $authorMapper;

    public function __construct($authorId, AuthorMapperInterface $authorMapper) {
        $this->authorId = $authorId;
        $this->authorMapper = $authorMapper;
    }

    public function setId($id) {
        $this->authorId = $id;
        return $this;
    }

    public function getId() {
        return $this->authorId;
    }

    public function setName($name) {
        $this->loadAuthor();
        $this->author->setName($name);
        return $this;
    }

    public function getName() {
        $this->loadAuthor();
        return $this->author->getName();
    }

    public function setEmail($email) {
        $this->loadAuthor();
        $this->author->setEmail($email);
        return $this;
    }

    public function getEmail() {
        $this->loadAuthor();
        return $this->author->getEmail();
    }

    protected function loadAuthor() {
        if ($this->author === null) {

            if(!$this->author = $this->authorMapper->fetchById($this->authorId)) {
                throw new UnexpectedValueException(
                    "Unable to fetch the author.");
            }
        }

        return $this->author;
    }
}
```

乍看之下，`AuthorProxy`类看起来像是`Author`的廉价而肮脏的复制品，几乎没有额外的功能，但是我向你保证这只是一个肤浅的印象。当进行分析时，它简单地显示了虚拟代理背后的逻辑。`loadAuthor()`方法是代理的实际工作，因为它的职责是从数据库中提取一次，并且只有一次，一个作者对象被注入到构造函数中。这一批额外的方法只是作者的 setter/getter 的包装器。

总而言之，应该清楚的是，构造一个在幕后透明地处理一些域对象的代理类比人们想象的要容易得多。即便如此，了解代理人真实实力的最佳方式还是通过举例。假设我们需要构建一个应用程序，从数据库中获取一些流行的引用，然后将它们与相应的作者一起转储到屏幕上。

在一个简单的实现中，应用程序看起来很像这样:

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter,
    ModelMapperAuthorMapper,
    ModelProxyAuthorProxy,
    ModelAuthor,  
    ModelPost;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=mydatabase", "dbuser", "dbpassword");

$authorMapper = new AuthorMapper($adapter);

$author = $authorMapper->fetchById(1);

$post = new Post(
    "About Men",
    "Men are born ignorant, not stupid; they are made stupid by education.",
    $author);

echo $post->getTitle() . $post->getContent() . " Quote from: " .
    $post->getAuthor()->getName();
```

即使为了简洁起见，引号映射器被排除在外，代码的流程仍然很容易理解:它从数据库中拉入第一个 author 对象(对 [Bertrand Russell](http://en.wikipedia.org/wiki/Bertrand_Russell) 的引用),该对象被注入到 quote 对象中。在这里，作者在有任何机会处理它之前就被急切地从存储中取出，顺便说一下，这不是一个大罪。

然而，生活在我们头脑中的纯粹主义疯子不能就这么轻易地被扔在一边，并不停地抱怨作者如果懒惰会更好。在这种情况下，我们可以切换代理，并将其放入应用程序中，如下所示:

```
<?php
$author = new AuthorProxy(1, new AuthorMapper($adapter)); 

$post = new Post(
    "About Men",
    "Men are born ignorant, not stupid; they are made stupid by education.",
    $author);

echo $post->getTitle() . $post->getContent() . " Quote from: " .
    $post->getAuthor()->getName();
```

如果仔细观察负责创建 post 对象的代码行，您会注意到它与之前的代码行完全相同，尽管它现在使用代理并从数据库中延迟加载作者。除了说明如何在常见用例中使用虚拟代理之外，这里的示例还展示了如何保持客户端代码的一致性。它坚持[开放/关闭](http://en.wikipedia.org/wiki/Open/closed_principle)原则，完全依赖于一些隔离的接口，而不是具体的实现。

总而言之:如果您仍然想知道多态性在创建可以在运行时由虚拟代理(或类似的东西)交换的域对象时是否有一席之地，那么请放心，它可以帮助您构建可伸缩的、经得起未来考验的域模型。

## 结束语

虚拟代理有各种各样的风格和形式，因此在开发其开箱即用的功能时并不缺少选择。在许多情况下，它们被用作实际域对象的占位符，而提前创建这些对象是相当昂贵的，这使得可以根据来自持久层的请求透明地加载它们，而不会用有害的条件毒害您的客户端代码。

在前面的例子中，使用了一个基本的代理从数据库中获取一个简单的集合，希望最终能有所启发，但并不完全符合现实世界。在更现实的情况下，领域模型通常要比这大得多，并且通常会受到不稳定的领域对象集合的困扰。

毫不奇怪，虚拟代理也可以被实现为与集合相互作用，而没有太多的麻烦。因此，在下一部分中，我将向您展示如何创建一个面向集合的代理类，这样您就可以自己看看它是否符合您的需要。

图片 via[imredesiuk/](http://www.shutterstock.com/gallery-496894p1.html)Shutterstock

## 分享这篇文章