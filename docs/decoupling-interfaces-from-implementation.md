# 从实现中分离接口——使用分离的接口

> 原文：<https://www.sitepoint.com/decoupling-interfaces-from-implementation/>

如果你曾经面临过同样的困境，你肯定会理解下面描述的含义:[内聚](http://en.wikipedia.org/wiki/Cohesion_(computer_science))和[解耦](http://en.wikipedia.org/wiki/Decoupling)，这两个看似天真但却是基本的 OOP 基石，负责不断击碎大多数软件开发人员的思想，经常跨越有形目标的边界进入乌托邦，远离实用主义和现实。

我不想听起来过于不祥，但是公平地说，设计内聚、松耦合的系统绝不是一件简单的事情。在大多数情况下，实现这样的原始特性需要在初始阶段进行大量的规划，并在随后随着系统的发展和成长经历适当的重构周期。尽管如此，OOP 范例*本身*提供了一套开箱即用的值得信赖的方法和原则，它们共同促进了内聚的、高度解耦的组件的实现，即使它们在各自的生命周期中最终不得不被重构。

例如，考虑几个[隔离接口](http://en.wikipedia.org/wiki/Interface_segregation_principle)和命名空间类的使用。当同步工作时，它们有助于将语义上相互关联的组件连接在一起，从而增加系统公开的整体内聚级别。另一方面，接口本身的定义，无论是接口构造还是虚拟/抽象类的形式，都使得具体的实现依赖于抽象，因此以这样一种方式促进了组件的解耦，即它们可以在运行时以不同的方式“重新连接”，通常是通过[依赖注入](http://martinfowler.com/articles/injection.html)。当从鸟瞰的角度进行分析时，很明显，良好的 OOP 实践和设计模式的适当混合的合力在大多数情况下是一个有效的解决方案，它允许我们达到一个体面的内聚和解耦水平。

然而，当需要明确地“重新定义”多层设计中的一个组件，甚至整个层必须与其他组件相互作用的方式时，可能会出现问题。在传统的系统设计中，其解耦组件之间的依赖关系是通过各自的接口来定义的，这些接口通常位于包含具体实现的同一个组件中。可能会有一些特殊的情况，在这些情况下，依赖关系必须被颠倒，以响应特定的需求。在这种情况下，通过将接口放到一个组件中，并将相应的实现放到另一个组件中，就可以很容易地实现上述反转。

考虑到这个概念一开始可能听起来相当混乱和扭曲，这种组件之间的协议转换生活和呼吸在被称为[分离接口](http://martinfowler.com/eaaCatalog/separatedInterface.html)的基本设计模式的保护伞下，这至少在某种程度上与[依赖倒置原则](http://www.objectmentor.com/resources/articles/dip.pdf)的戒律携手并进。此外，由于该模式的易用性，用 PHP 实现它也是可行的，因此在本文中，我将向您展示如何以一种相当平易近人的方式利用分离接口带来的好处。

## 侵入禁区——从域模型中调用数据映射器逻辑

虽然分离的接口背后的逻辑很容易理解，但在所有情况下，都必须有一个令人信服的理由来证明它们的实现，因为反转应用程序中组件之间的依赖关系的过程远远不是一个任意的、异想天开的决定。

这也提出了一个有趣的问题:毕竟，如果组件在当前状态下工作得很好，为什么要反转组件依赖关系呢？在某些情况下，反转可以用作提高相关组件之间解耦水平的机制。简单地说，将一个接口放在一个模块中，而将实现放在另一个模块中，这意味着要明确定义在外部世界访问所讨论的模块时应该使用的协议。

查看分离接口的效果的一个好方法是使用一些可测试的例子。因此，让我们假设我们有一个简单的领域模型，它需要访问映射层中的一些逻辑。在一个经典的实现中，这很可能被视为一个大罪，因为根据定义，[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)必须不知道任何形式的持久性。然而，当使用分离的接口时，我们可以实现这一点，并且仍然保持模型完全不可知。

为了更好地理解这个过程的内部工作，让我们开始实现一个基本模型。这是它的第一个模块的样子:

```
<?php
namespace Model;

class AbstractEntity
{
    protected $id;

    //set values for protected/private fields via the corresponding mutators
    public function __set($field, $value) {
        $this->checkField($field);
        $mutator = "set" . ucfirst(strtolower($field));
        method_exists($this, $mutator) && 
            is_callable(array($this, $mutator)) 
            ? $this->$mutator($value)
            : $this->$field = $value;
        return $this;                 
    }

    // get values from protected/private fields via the corresponding accessors 
    public function __get($field) {
        $this->checkField($field);
        $accessor = "get" . ucfirst(strtolower($field));
        return method_exists($this, $accessor) && 
            is_callable(array($this, $accessor))
            ? $this->$accessor() 
            : $this->$field;
    }

    protected function checkField($field) {
        if (!property_exists($this, $field)) {
            throw new InvalidArgumentException(
                "Setting or getting the field '$field'j is not valid for this entity.");
        }
    }

    // sanitize strings assigned to the fields of the entity
    protected function sanitizeString($value, $min = 2, $max = null) {
        if (!is_string($value) 
            || strlen($value) < (integer) $min 
            || ($max) ? strlen($value) > (integer) $max : false) {
            throw new InvalidArgumentException(
                "The value of the field accessed must be a valid string.");
        }
        return htmlspecialchars(trim($value), ENT_QUOTES);
    }

    // handle IDs
    public function setId($id)
    {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this entity has been set already.");
        }
        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
              "The ID of this entity is invalid.");
        }
        $this->id = $id;
        return $this;
    }

    public function getId() {
        return $this->id;
    }
}
```

考虑到这里的目标是实现一个相当典型的模型，它的核心功能可以很容易地随意扩展，上面的`AbstractEntity`类的行为非常像一个[层超类型](http://martinfowler.com/eaaCatalog/layerSupertype.html)，它封装了所有领域子类共有的逻辑，这些子类可能是以后偶然得到的。这包括执行一些基本任务，比如处理 id、净化字符串，以及通过`__set()`和`__get()`将调用映射到赋值函数/访问函数。

有了基类，现在创建定义具体域对象的数据和行为的附加子类就相当简单了。设置几个负责建模经典博客帖子/评论关系的人怎么样？

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

     public function getComments();
}
```

```
<?php
namespace Model;

interface CommentFinderInterface {
    public function findById($id);
    public function findAll(array $conditions = array());
}
```

```
<?php
namespace Model;

class Post extends AbstractEntity implements PostInterface
{
    protected $title;
    protected $content;
    protected $comments;
    protected $commentFinder;

    public function __construct($title, $content, CommentFinderInterface $commentFinder)  {
        $this->setTitle($title);
        $this->setContent($content);
        $this->commentFinder = $commentFinder;
    }

    public function setTitle($title) {
        $this->title = $this->sanitizeString($title);
        return $this;
    }

    public function getTitle() {
        return $this->title;
    }

    public function setContent($content) {
        $this->content = $this->sanitizeString($content);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function getComments() {
        if ($this->comments === null) {
            $this->comments = $this->commentFinder->findAll(
                array("post_id" => $this->id));
        }
        return $this->comments;
    }
}
```

虽然不可否认驱动`Post`类的逻辑是人为的，归结起来只是验证和分配一些值给 blog post 对象的字段，但是它暴露了一个值得强调的细节:它被注入到它的构造函数中，一个以前的`CommentFinderInterface`的实现用于为持久层拉取相关的注释。这简单地展示了如何使用一个分离的接口来反转模型(至少是它的一部分)和任何其他必须与之交互的层之间的依赖关系。换句话说，这个模型现在是一个独立的客户端模块，负责定义它自己的访问协议的抽象。

这个简单的改变让我们可以做各种各样聪明的事情，而不会感到一丝内疚，包括那些在任何其他情况下都会被认为是亵渎神明的事情。例如，我们可以让模型调用映射逻辑并从数据库中检索注释，同时仍然让它不知道映射器本身。模型总是看到接口，而不是实现者，不管是映射器还是其他什么。

当然，在我们看到如何在数据映射器中获得模型调用逻辑之前，我们应该创建负责建模上述注释的子类。这是额外的衍生工具，以及它实现的接口:

```
<?php
namespace Model;

interface CommentInterface
{
     public function setId($id);
     public function getId();

     public function setContent($content);
     public function getContent();

     public function setAuthor($author);
     public function getAuthor();
}
```

```
<?php
namespace Model;

class Comment extends AbstractEntity implements CommentInterface
{
    protected $content;
    protected $author;

    public function __construct($content, $author) {
        $this->setContent($content);
        $this->setAuthor($author);
    }

    public function setContent($content) {
        $this->content = $this->sanitizeString($content);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setAuthor($author) {
        $this->author = $this->sanitizeString($author);
        return $this;
    }

    public function getAuthor() {
        return $this->author;
    }
}
```

没有必要为了掌握它的底层逻辑而过于深入地研究早期 Comment 类的 API，因为它的职责非常狭窄。它只是在有效状态下创建 comment 对象，这些对象通过一些标量字段(比如评论的内容和相应的作者)进行合并。

## 通过数据映射器从存储中提取注释

现在是展示一下的时候了，因为我们已经成功实现了一个功能域模型，它依赖于一个独立的接口，并显式声明了应该用于从持久层获取注释的契约。虽然这一切都很好，但在这个难题中仍有一块缺失；为了让事情最终运转起来，我们需要定义这个接口的至少一个实现者，以便可以将注释注入到几个 post 对象的内部。

特别是在这种情况下，实现者将是一个注释映射器，因为它反映了生产中可能存在的大多数用例。不用说，可以将任何其他形式的中介切换到持久层，比如 web 服务，甚至是内存缓存系统。不过，为了让代码示例更清晰、更容易理解，我将坚持使用基本的数据映射器实现。

在领域模型和数据库之间建立一个像样的数据映射器，并且至少能够对一些领域对象执行 CRUD 操作，这是一项相当繁重的任务，因为映射器不仅必须剖析对象本身的骨架以便持久化它们，而且还必须根据请求反转过程并重新组合它们。这种角色的双重性使得数据映射器的设置非常复杂，并且在大多数情况下，以精神健全的名义，委托给第三方库的职责。

即使如此，在实现数据映射器时也没有必要如此紧张，因为我们现在需要做的只是创建一个实现前面的`CommentFinderInterface`的简单的映射器，这样相应的注释就可以从存储器中取出并注入到相关的 post 对象的内部。下面的例子就是这样做的:

```
<?php
namespace Mapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelCommentFinderInterface,
    ModelNullComment,
    ModelComment;   

class CommentMapper implements CommentFinderInterface
{
    protected $adapter;
    protected $entityTable = "comments";

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function findById($id) {
        $this->adapter->select($this->entityTable, 
            array("id" => $id));
        if (!$row = $this->adapter->fetch()) {
            return null;    
        }
        return $this->loadComment($row);
    }

    public function findAll(array $conditions = array()) {
        $this->adapter->select($this->entityTable, $conditions);
        $rows = $this->adapter->fetchAll();
        return $this->loadComments($rows);
    }

    protected function loadComment(array $row) {
        return new Comment($row["content"], $row["author"]);
    }

    protected function loadComments(array $rows) {
        $comments = array();
        foreach ($rows as $row) {
            $comments[] = $this->loadComment($row);
        }
        return $comments;
    }
}
```

为了简洁起见，我决定将`CommentMapper`的实现保持为围绕`findById()`和`findAll()`方法的简单包装。但是，如果您喜欢冒险，想要添加一些额外的方法，允许它从关联的表中插入、更新和删除注释，那么就尽情发挥您非凡的编码技能吧。

当然，映射器最吸引人的方面是在从存储中检索注释时，它对模型所声明的协议的表达性(即使不是显式的)依赖性。这种模式的真正美妙之处在于，尽管依赖性是规定性的，但它依赖于接口的抽象。这意味着模型不知道映射器，映射器也不了解模型的整体情况。考虑到仅仅通过使用一个简单的抽象就已经很好地增强了这两个组件之间的解耦程度，这是非常令人振奋的。

当然，如果我没有一个具体的例子来支持的话，所有这些话听起来可能只是一堆废话。

```
<?php
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter,
    MapperCommentMapper,
    ModelPost,
    ModelComment;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=test", "fancyusername",
    "hardtoguesspassword");

$commentMapper = new CommentMapper($adapter);

$post = new Post("A naive sample post", 
    "This is the content of the sample post", 
    $commentMapper);
$post->id = 1;

echo $post->title . " " . $post->content . "<br>";

foreach ($post->comments as $comment) {
    echo $comment->content . " " . $comment->author . "<br>";
}
```

仅仅是我，还是将数据映射器传递到 post 对象看起来会让我畏缩？没有理由恐慌；这两个元素已经通过分离接口的契约被适当地相互屏蔽。此外，由于映射器仅仅是一个利用多态性的元素，所以很容易替换掉其他的`CommentFinderInterface`实现。

无论在哪种情况下，这个例子都应该足以说明独立接口的强大屏障背后是什么，以及它在一些特定用例中的实现如何在逆转一个或多个组件之间的依赖关系时非常有用，同时在整个系统中保持整洁的解耦水平。

## 结束语

如果你和我一样，可能第一次听说分离接口时，你会认为它们很像另一种扭曲的、难以理解的模式，在现实世界中几乎没有真正的应用。尽管有这样的膝跳反应，驱动分离接口的逻辑比人们想象的要容易理解得多。另外，分离接口带来的好处实在太多了，因为它们允许您以一种简单的方式建立松散耦合的客户端模块，这些模块定义了其他包应该使用的契约。

由于存在大量的设计模式，盲目崇拜分离的接口是没有意义的，因此应该谨慎使用它们，并且只有当情况允许逆转应用程序组件之间的依赖关系时才使用。诉诸你的常识，不要异想天开，当你真的认为它们会对你的设计产生积极影响时，就使用它们。

<small>图片 via [库帕](http://www.shutterstock.com/gallery-664216p1.html)/[Shutterstock](http://shutterstock.com)</small>

## 分享这篇文章