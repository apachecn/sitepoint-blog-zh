# 虚拟代理介绍，第 2 部分

> 原文：<https://www.sitepoint.com/intro-to-virtual-proxies-2/>

站在一个相当花哨的名字后面，虚拟代理很可能是为什么“编程到接口”的咒语不仅仅是一个枯燥的教条原则的最明显的例子之一。基于多态性(动态多态性，而不是通常通过简单方法覆盖实现的特定多态性)，虚拟代理是一个简单而可靠的概念，它允许您推迟构建/加载昂贵的对象图，而不必修改客户端代码。

代理的一个伟大之处在于，它们可以在概念上被设计为与单个对象或它们的集合一起工作(或者与两者一起工作，尽管这样做会危及关注点的分离并随着时间的推移变得难以管理)。为了从实践的角度演示如何利用虚拟代理提供的功能，在本系列的第一部分中，我开发了几个例子，展示了如何使用一个基本的代理从数据库中提取一个集合，以实现一个简单的领域模型。

尽管体验很有教育意义，运气也很有趣，但它的另一面有些欺骗性，因为它展示了虚拟代理的具体细节，但没有展示如何在更现实的场景中实现它们。当从存储中延迟加载域对象集合时，代理是一个难以击败的舰队。要了解这个概念，只需考虑一批博客文章，其中每组相关评论都可以根据需要从数据库中获取；当然，你会明白为什么代理人可以在这种情况下很好地实现他们的承诺。

像往常一样，实践是最好的老师。在这一部分中，我将展示如何将一个代理连接到一个特定的域对象集合。我将在一个非常基本的水平上重新创建这个典型的场景，这样你就可以以一种相当无痛的方式看到它的驱动逻辑。

## 创建域对象的集合

正如我所解释的，当从持久层获取绑定到底层域对象集合的聚合根时，虚拟代理通常被赋予生命。由于集合固有的多产性，在许多情况下，预先设置集合的成本很高，这使得它们很容易被请求拖入到数据库的开销中。

此外，考虑到博客帖子和相应的评论之间的“一对多”关联在这个用例中反映得相当真实，在处理具体的代理之前，首先通过一些简单的领域类对这种关系进行建模是非常有益的。

为了使事情易于理解，我将在测试阶段添加的前两个角色将是一个分离的接口，以及一个基本实现者。这两者的结合将定义通用博客文章对象的契约和实现:

```
<?php
namespace Model;
use ModelCollectionCommentCollectionInterface;

interface PostInterface
{
    public function setId($id);
    public function getId();

    public function setTitle($title);
    public function getTitle();

    public function setContent($content);
    public function getContent();

    public function setComments(CommentCollectionInterface $comments);
    public function getComments();
}
```

```
<?php
namespace Model;
    use ModelCollectionCommentCollectionInterface;

class Post implements PostInterface
{
    protected $id;
    protected $title;
    protected $content;
    protected $comments;

    public function __construct($title, $content, CommentCollectionInterface $comments = null)  {
        $this->setTitle($title);
        $this->setContent($content);
        $this->comments = $comments;
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

    public function setComments(CommentCollectionInterface $comments) {
        $this->comments = $comments;
        return $this;
    }

    public function getComments() {
        return $this->comments;
    }
}
```

理解上面的`Post`类背后的逻辑是一个不需要解释的琐碎过程。不过，这里有一个相关的细节值得注意:该类在构造函数中显式声明了对一个仍未定义的注释集合的依赖。现在让我们创建产生帖子评论的类:

```
<?php
namespace Model;

interface CommentInterface
{
     public function setId($id);
     public function getId();

     public function setContent($content);
     public function getContent();

     public function setPoster($poster);
     public function getPoster();
}
```

```
<?php
namespace Model;

class Comment implements CommentInterface
{
    protected $id;
    protected $content;
    protected $poster;

    public function __construct($content, $poster) {
        $this->setContent($content);
        $this->setPoster($poster);
    }

    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this comment has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
                "The comment ID is invalid.");
        }

        $this->id = $id;
        return $this;
    }

    public function getId() {
        return $this->id;
    }

    public function setContent($content) {
        if (!is_string($content) || strlen($content) < 2) {
            throw new InvalidArgumentException(
                "The content of the comment is invalid.");
        }

        $this->content = htmlspecialchars(trim($content), ENT_QUOTES);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setPoster($poster) {
        if (!is_string($poster) 
            || strlen($poster) < 2
            || strlen($poster) > 30) {
            throw new InvalidArgumentException(
                "The poster is invalid.");
        }

        $this->poster = htmlspecialchars(trim($poster), ENT_QUOTES);
        return $this;
    }

    public function getPoster() {
        return $this->poster;
    }
}
```

到目前为止，事情进展顺利。除了它们是一个基本的[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)的小块之外，上面的域类没有什么可说的，其中每个 blog post 对象公开了与相关评论的“一对多”关联。如果你愿意，可以随意称我为纯粹主义者，但在我看来，如果没有一系列评论的加入，这个模型当前的实现看起来不完整而且笨拙。让我们通过添加这个额外组件的逻辑来使模型更丰富一些:

```
<?php
namespace ModelCollection;

interface CommentCollectionInterface extends Countable, IteratorAggregate
{
    public function getComments();
}
```

```
<?php
namespace ModelCollection;
use ModelCommentInterface;

class CommentCollection implements CommentCollectionInterface
{
    protected $comments = array();

    public function __construct(array $comments = array()) {
        if ($comments) {
            foreach($comments as $comment) {
                $this->addComment($comment);
            } 
        }

    }

    public function addComment(CommentInterface $comment) {
        $this->comments[] = $comment;
        return $this;
    }

    public function getComments() {
        return $this->comments;
    }

    public function count() {
        return count($this->comments);
    }

    public function getIterator() {
        return new ArrayIterator($this->comments);
    }
}
```

如果你观察仔细，浏览一下`CommentCollection`类，你首先会注意到的事实是，它只不过是一个隐藏在花哨伪装后面的可迭代、可数数组包装器。事实上，数组集合有不同的形式和风格，但大多数时候它们只是`[Iterator](http://www.php.net/manual/en/class.iterator.php)`和`[ArrayAccess](http://www.php.net/manual/en/class.arrayaccess.php)` SPL 类的简单用法。在这种情况下，我想把我自己(和你)从处理如此无聊的任务中解救出来，并让这个类成为 [`IteratorAggregate`](http://www.php.net/manual/en/class.iteratoraggregate.php) 的实现者。

有了评论集合，我们可以更进一步，让领域模型做它应该做的事情——到处按摩一些博客文章对象，甚至将它们与从数据库中急切获取的一批评论相互连接。但这样做，我们只是在欺骗自己，而不是充分利用虚拟代理提供的功能。

考虑到在一个典型的实现中，代理暴露与实际域对象相同的 API，一个与前面的`CommentCollection`类交互的代理也应该实现`CommentCollectionInterface`来实现与客户端代码的约定，而不会在某个地方出现一堆糟糕的条件。

## 通过虚拟代理与域对象集合接口

坦率地说，像前面那样的数组包装集合可以独立存在，而不必依赖任何其他依赖。(如果你持怀疑态度，请随意查看教义中的收藏如何在幕后完成它们的工作。)尽管如此，请记住，我正试图实现一个代理来模拟真实的帖子评论集合的行为，但这实际上是一个轻量级的替身。

人们不禁要问:如何将评论从数据库中取出并放入早期的集合中？有几种方法可以做到这一点，但我发现最吸引人的是通过数据映射器，因为它促进了持久性不可知论。

下面的映射器很好地完成了从存储中获取帖子评论集合的工作。看看这个:

```
<?php
namespace ModelMapper;

interface CommentMapperInterface
{
    public function fetchById($id);

    public function fetchAll(array $conditions = array());
}
```

```
<?php
namespace ModelMapper;
use LibraryDatabaseDatabaseAdapterInterface,
    ModelCollectionCommentCollection,
    ModelComment;

class CommentMapper implements CommentMapperInterface
{  
    protected $adapter;
    protected $entityTable = "comments";

    public function __construct(DatabaseAdapterInterface $adapter) {
        $this->adapter = $adapter;
    }

    public function fetchById($id) {
        $this->adapter->select($this->entityTable,
            array("id" => $id));

        if (!$row = $this->adapter->fetch()) {
            return null;
        }

        return $this->createComment($row);
    }

    public function fetchAll(array $conditions = array()) {
        $collection = new CommentCollection;
        $this->adapter->select($this->entityTable, $conditions);
        $rows = $this->adapter->fetchAll();

        if ($rows) {
            foreach ($rows as $row) {
                $collection->addComment($this->createComment($row));
            }
        }

        return $collection;
    }

    protected function createComment(array $row) {
        return new Comment($row["content"], $row["poster"]);
    }
}
```

虽然由`CommentMapper`类公开的 finders 通常会坚持使用标准数据映射器实现中可能会有的 API，但是到目前为止,`fetchAll()`方法是这个模块中最出色的。它首先从存储中获取所有的博客文章评论，并将其放入集合中，最终返回给客户端代码。如果你和我一样，你的脑海中可能会响起警铃，因为集合是直接在方法中实例化的。

事实上，没有必要为害羞地生活在工厂外的`new`操作人员而疯狂，至少在这种情况下，因为该集合实际上是一个通用结构，属于“可更新”类别，而不是“可注入”类别。无论如何，如果您觉得在映射器的构造函数中注入集合没有那么内疚，请随意这样做。

有了注释映射器，是时候经历真正的顿悟时刻，并构建代理类来协调前面的集合了:

```
<?php
namespace ModelProxy;
use ModelCollectionCommentCollectionInterface,
    ModelMapperCommentMapperInterface;

class CommentCollectionProxy implements CommentCollectionInterface
{
    protected $comments;
    protected $postId;
    protected $commentMapper;

    public function __construct($postId, CommentMapperInterface $commentMapper) {
        $this->postId = $postId;
        $this->commentMapper = $commentMapper;
    }

    public function getComments() {
        if ($this->comments === null) {

            if(!$this->comments = 
                $this->commentMapper->fetchAll(
                    array("post_id" => $this->postId))) {
                throw new UnexpectedValueException(
                    "Unable to fetch the comments.");
            }
        }

        return $this->comments;
    }

    public function count() {
        return count($this->getComments());
    }

    public function getIterator() {
        return $this->getComments();
    }
}
```

正如您所料，`CommentCollectionProxy`实现了与真实注释集合相同的接口。然而，它的`getComments()`方法在幕后执行真正的工作，并通过构造函数中传递的映射器从数据库中延迟加载注释。

这个简单而有效的技巧可以让你用评论做各种聪明的事情，而不会有过多的腋下出汗。你想看哪些？好吧，假设您需要从数据库中获取绑定到特定博客文章的所有评论。下面的代码片段完成了这项工作:

```
<?php	
use LibraryLoaderAutoloader,
    LibraryDatabasePdoAdapter,
    ModelMapperCommentMapper,
    ModelProxyCommentCollectionProxy,
    ModelComment,  
    ModelPost;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$adapter = new PdoAdapter("mysql:dbname=blog", "myfancyusername", "mysecretpassword");

$commentMapper = new CommentMapper($adapter);
$comments = $commentMapper->fetchAll(array("post_id" => 1));

$post = new Post("The post title", "This is just a sample post.",
    $comments);

echo $post->getTitle() . " " .  $post->getContent() . "<br />";

foreach ($post->getComments() as $comment) {
    echo $comment->getContent() . " " . $comment->getPoster() .
        "<br />";
}
```

这种方法的缺点是注释首先从存储器中取出，然后注入 post 对象的内部。不如反过来做，但是这次用代理“愚弄”客户机代码。

```
<?php
$comments = new CommentCollectionProxy(1, new CommentMapper($adapter)); 

$post = new Post("The post title", "This is just a sample post.",
    $comments);

echo $post->getTitle() . " " .  $post->getContent() . "<br />";

foreach ($post->getComments() as $comment) {
    echo $comment->getContent() . " " . $comment->getPoster() .
        "<br />";
}
```

在代理被放入`foreach`循环后，不仅注释集从数据库中被透明地延迟加载，而且暴露给客户机代码的 API 始终保持其原始结构不变。我们还敢要求更好的东西吗？除非你极其贪婪，否则我不这么认为。

无论是哪种情况，此时您都应该知道虚拟代理下实际发生了什么，以及如何充分利用这些功能来提高域对象和底层持久层之间的操作效率。

## 结束语

尽管很琐碎，特别是如果您足够大胆地在生产中使用它，前面的例子简单地展示了一些有趣的概念。首先，虚拟代理不仅易于设置和使用，而且在运行时混合不同的实现以推迟昂贵任务的执行时，例如从存储层延迟加载大块数据，或者创建重量级对象图，虚拟代理是无与伦比的。

第二，它们是一个经典的例子，说明了多态性的使用是一种有效的疫苗，可以减少许多面向对象应用程序遭受的常见的[刚性和脆弱性](http://www.objectmentor.com/resources/articles/Principles_and_Patterns.pdf)问题。此外，由于 PHP 的对象模型简单，并且支持[闭包](http://www.php.net/manual/en/functions.anonymous.php)，因此可以将这些特性很好地混合起来，构建底层逻辑由闭包驱动的代理。如果你想独自应对这个挑战，那么我提前祝福你。

图片 via[imredesiuk/](http://www.shutterstock.com/gallery-496894p1.html)Shutterstock

## 分享这篇文章