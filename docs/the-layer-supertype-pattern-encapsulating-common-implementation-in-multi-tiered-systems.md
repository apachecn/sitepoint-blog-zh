# 层超类型模式:在多层系统中封装公共实现

> 原文：<https://www.sitepoint.com/the-layer-supertype-pattern-encapsulating-common-implementation-in-multi-tiered-systems/>

作为 OOP 的基石之一，继承就像一扇双面的门，危险地向两边摆动。当它向一方开放时，它暴露了一个强大的机制，允许我们快速重用实现，而不必求助于[组合](http://en.wikipedia.org/wiki/Object_composition "Object composition - Wikipedia, the free encyclopedia")。然而，每当它向另一边打开时，我们看到它令人愉快的本性迅速消失在空气中，取而代之的是一种邪恶和扭曲的野兽，能够创造各种腐败的等级制度，其中子类型的行为与其基本类型如此不同，以至于说它们之间存在“是-A”关系是亵渎神明的！

尽管所有的陷阱和奇怪都源于遗传，其中大部分可以通过合理和适度的使用来减轻，但它迷人的影响力是难以抗拒的。毕竟，代码重用是继承存在的首要原因，当涉及到在多层系统的抽象中添加样板实现时，它可能是一个真正的杀手。

继承提供了一种简单的方法，可以很容易地生成大量语义上相互关联的对象，而不需要重复的代码。这个概念简单得可笑——但功能强大:首先在一个基本类型(通常是一个抽象类，但也可能是一个具体的类)的边界内放下尽可能多的逻辑，然后开始根据更具体的需求派生细化的子类型。该过程通常是在“每层”的基础上进行的，因此为每层提供了自己的一组超类型，这些超类型的核心功能由相应的子类型依次提取和扩展。

毫不奇怪，这种重复的封装/派生循环奠定了被称为[层超类型](http://martinfowler.com/eaaCatalog/layerSupertype.html "P of EAA: Layer Supertype")的设计模式的形式(是的，虽然有些天真，但它确实有一个真正的学术名称)，在接下来的文章中，我将深入研究它的内部工作，您将能够看到将其功能与[域模型](http://martinfowler.com/eaaCatalog/domainModel.html "P of EAA: Domain Model")挂钩是多么容易。

## 对层超类型的需求——定义一个膨胀的领域模型

可以说，层超类型是“普通”基本类型的自然和选择性进化，只是后者在特定层的范围内生活和呼吸。这在多层设计中有着丰富的应用，在多层设计中，开发超类型的功能是一种迫切的需要，而不仅仅是一个轻率的决定。

通常，理解模式背后的实用主义的最有效的方法是通过几个实际例子。因此，假设我们需要从头开始构建一个简单的领域模型，负责定义一些博客帖子及其相应评论之间的一些基本交互。

粗略地看，这个模型很容易被概括为一个贫血层，只包含几个对帖子和评论建模的框架类。

第一个域类及其契约可能如下所示:

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

    public function setComment(CommentInterface $comment);
    public function setComments(array $comments);
    public function getComments();
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
    protected $comments = array();

    public function __construct($title, $content, array $comments = array()) {
        $this->setTitle($title);
        $this->setContent($content);
        if (!empty($comments)) {
           $this->setComments($comments); 
        }
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
        $this->title = htmlspecialchars(trim($title),
            ENT_QUOTES);
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
        $this->content = htmlspecialchars(trim($content),
            ENT_QUOTES);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setComment(CommentInterface $comment) {
        $this->comments[] = $comment;
        return $this;
    }

    public function setComments(array $comments) {
        foreach ($comments as $comment) {
            $this->setComment($comment);
        }
        return $this;
    }

    public function getComments() {
        return $this->comments;
    }
}
```

驱动`Post`类是老生常谈的逻辑，归结起来就是定义一些基本 post 条目的数据和行为。这应该很容易理解。

现在让我们通过添加一个产生与特定博客条目相关的评论的类来使模型变得更大一些。它的契约和实现如下所示:

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

class Comment implements CommentInterface
{
    protected $id;
    protected $content;
    protected $author;

    public function __construct($content, $author) {
        $this->setContent($content);
        $this->setAuthor($author);
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
        $this->content = htmlspecialchars(trim($content),
            ENT_QUOTES);
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setAuthor($author) {
        if (!is_string($author) || strlen($author) < 2) {
            throw new InvalidArgumentException(
                "The author is invalid.");
        }
        $this->author = $author;
        return $this;
    }

    public function getAuthor() {
        return $this->author;
    }
}
```

像`Post`一样，`Comment`类也相当幼稚。但是现在两个类都就位了，我们可以使用这个模型了。例如:

```
<?php
use LibraryLoaderAutoloader,   
    ModelPost,
    ModelComment;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$post = new Post(
    "A sample post.",
    "This is the content of the post."
);

$post->setComments(array(
    new Comment(
        "One banal comment for the previous post.",
        "A fictional commenter"),
    new Comment(
        "Yet another banal comment for the previous post.",
        "A fictional commenter")
));

echo $post->getTitle() . " " . $post->getContent() . "<br>";

foreach ($post->getComments() as $comment) {
    echo $comment->getContent() . " " . $comment->getAuthor() .
        "<br>";
}
```

那确实非常有效！消费模型是一个相当简单的过程，要求您首先创建几个`Post`对象，然后用相关的注释对它们进行水合。是的，生活是甜蜜而美好的。嗯，到目前为止是这样，但事情肯定可以好得多！

并不是我想破坏这样一个美好时刻的魔力，但是我必须承认，每当我看着`Post`和`Comment`类的实现时，我的脊柱都会轻微颤抖。虽然这本身不是一个严重的问题，但是一些方法，比如`setId()`和`setContent()`，表现出代码重复的典型症状。

由于一些逻辑上的考虑，毫不马虎地解决这个问题并不像乍看上去那样直观。首先，尽管它们彼此共享一个语义关系，但是每个类都有效地对不同类型的对象建模。第二，它们实现了完全不同的接口，这意味着很难抽象出逻辑，而不会以笨拙的层次结构结束，其中“是-A”条件永远不会成立。

特别是在这种情况下，我们可以采取一种更自由的方法，将 Post 和 Comment 视为高度通用的`AbstractEntity`超类型的子类型。这样做的话，将共享实现放在抽象类的边界内将会非常简单，从而使子类型的定义变得更细。由于整个抽象过程将只在领域层进行，假设的`AbstractEntity`将被认为是…是的，你猜对了，一个层超类型。简单但很好，是吧？

## 将共享实现移动到单个域类中——创建一个层超类型

创建模型超类型的过程，至少在这种情况下，可以被看作是一些原子和粒度重构技术的混合，例如提取超类、拉起字段和拉起方法，Martin Fowler 在他的书[Refactoring:Improving the Design of Existing Code](http://martinfowler.com/books.html "Books by Martin Fowler")(如果你还没有副本，请确保尽快获得一个)中对此进行了深入的讨论。

让我们大胆尝试，通过加入前面提到的`AbstractEntity`类来重构我们笨拙的领域模型。由于在这种特定的情况下，大量的重复代码是通过域类的方法出现的，这些域类处理 id 并对基于字符串的字段执行一些基本的验证，因此将这些职责委托给超类型并让子类型专注于执行更少、更窄的任务会很有用。

基于这个简单的概念，前面提到的模型超类型的“胖”实现可能如下所示:

```
<?php
namespace Model;

class AbstractEntity
{
    protected $id;

    // Map calls to protected/private fields to mutators when
    // defined. Otherwise, map them to the fields.
    public function __set($field, $value) {   
        $this->checkField($field);
        $mutator = "set" . ucfirst(strtolower($field));
        method_exists($this, $mutator) && is_callable(array($this, $mutator)) 
            ? $this->$mutator($value)
            : $this->$field = $value;
        return $this;                 
    }

    // Map calls to protected/private fields to accessors when
    // defined. Otherwise, map them to the fields.
    public function __get($field) {
        $this->checkField($field);
        $accessor = "get" . ucfirst(strtolower($field));
        return method_exists($this, $accessor) && is_callable(array($this, $accessor))
            ? $this->$accessor() 
            : $this->$field;
    }

    // Map calls to undefined mutators/accessors to the corresponding
    // fields
    public function __call($method, $arguments) {
        if (strlen($method) < 3) {
            throw new BadMethodCallException(
                "The mutator or accessor '$method' is not valid for this entity.");
        }
        $field = lcfirst(substr($method, 3));
        $this->checkField($field);
        if (strpos($method, "set") === 0) {
            $this->$field = array_shift($arguments);
            return $this;
        }
        if (strpos($method, "get") === 0) {
            return $this->$field;
        }
    }

    // Make sure IDs are positive integers and assigned only once
    public function setId($id) {
        if ($this->id !== null) {
            throw new BadMethodCallException(
                "The ID for this entity has been set already.");
        }
        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException(
              "The ID for this entity is invalid.");
        }
        $this->id = $id;
        return $this;
    }

    public function getId() {
        return $this->id;
    }

    // Get the entity fields as an array
    public function toArray() {
        return get_object_vars($this);
    }

    // Check if the given field exists in the entity
    protected function checkField($field) {
        if (!property_exists($this, $field)) {
            throw new InvalidArgumentException(
                "Setting or getting the field '$field' is not valid for this entity.");
        }
    }

    // Validate and sanitize a string
    protected function sanitizeString($value, $min = 2, $max = null) {
        if (!is_string($value) || empty($value)) {
            throw new InvalidArgumentException(
              "The value of the current field must be a non-empty string.");
        }
        if (strlen($value) < (integer) $min || $max ? strlen($value) > (integer) $max : false) {
            throw new InvalidArgumentException(
              "Trying to assign an invalid string to the current field.");
        }
        return htmlspecialchars(trim($value), ENT_QUOTES);
    }
}
```

与普遍的看法相反，赋予抽象超类型生命(它巧妙地封装了模型的子类型所共享的大部分逻辑)实际上是一个简单的过程，可以相对较快地完成。在这种情况下，我承认我有点过分热心了，因为超类型不仅能够处理 id 和验证/净化字符串，而且还在幕后加入了一些 PHP 技巧，尽可能将私有/受保护属性的调用映射到相应的赋值函数/访问函数。为超类型提供像这样的额外功能是完全可选的，所以你可以在自己的`AbstractEntity`类中随意包含或省略它。

下一步是重构`Post`和`Comment`类的实现，这样它们就可以容纳抽象父类现在提供的好东西。以下是重构后的版本:

```
<?php
namespace Model;

class Post extends AbstractEntity implements PostInterface
{
    protected $title;
    protected $content;
    protected $comments = array();

    public function __construct($title, $content, array $comments = array())  {
        $this->setTitle($title);
        $this->setContent($content);
        if (!empty($comments)) {
            $this->setComments($comments);
        }
    }

    public function setTitle($title) {
        try {
            $this->title = $this->sanitizeString($title);
            return $this; 
        }
        catch (InvalidArgumentException $e) {
            throw new $e("Error setting the post title: " .
                $e->getMessage());
        }
    }

    public function getTitle() {
        return $this->title;
    }

    public function setContent($content) {
        try {
            $this->content = $this->sanitizeString($content);
            return $this;
        }
        catch (InvalidArgumentException $e) {
            throw new $e("Error setting the post content: " .
                $e->getMessage());
        } 
    }

    public function getContent() {
        return $this->content;
    }

    public function setComment(CommentInterface $comment) {
        $this->comments[] = $comment;
        return $this;
    }

    public function setComments(array $comments) {
        foreach ($comments as $comment) {
            $this->setComment($comment);
        }
        return $this;
    }

    public function getComments() {
        return $this->comments;
    }
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
        try {
            $this->content = $this->sanitizeString($content);
            return $this;
        }
        catch (InvalidArgumentException $e) {
            throw new $e("Error setting the comment: " .
                $e->getMessage());
        }
    }

    public function getContent() {
        return $this->content;
    }

    public function setAuthor($author) {
        try {
            $this->author = $this->sanitizeString($author);
            return $this;
        }
        catch (InvalidArgumentException $e) {
            throw new $e("Error setting the author : " .
                $e->getMessage());
        }
    }

    public function getAuthor() {
        return $this->author;
    }
}
```

模型的子类型现在变得更加纤细，因为大部分重复代码都被放在了主导超类型中。此外，由于在`__set()` / `__get()` duet 的表面下实现，消费模型甚至是一个更容易的任务，简化为只需编写以下代码片段:

```
<?php
$post = new Post("A sample post.", "This is the content of the post.");

$post->setComments(array(
    new Comment(
        "One banal comment for the previous post.",
        "A fictional commenter"),
    new Comment(
        "Yet another banal comment for the previous post.",
        "A fictional commenter")
 ));

echo $post->title . " " . $post->content . "<br>";

foreach ($post->comments as $comment) {
    echo $comment->content . " " . $comment->author . "<br>";
}
```

这个例子可能是人为的，但是它强调了如何让模型最终启动并运行，这一次使用的是经过裁剪的`Post`和`Comment`类。有趣的事情实际上发生在幕后，而不是在华丽的前线，因为抽象超类型实体的定义允许我们在整个重构过程中删除大量重复的实现，而不用太担心。

此外，一些子类型的单独存在，就其本身而言，是经历实现层超类型的麻烦的有效理由。由于显而易见的原因，当处理分散在几个应用层的多个子类型时，该模式最吸引人的方面就暴露出来了。

## 结束语

虽然它通常被视为被高估和滥用的野兽，但我希望现在很少有人会不同意继承是一种强大的机制，当在多层系统中巧妙地使用时，它可以有效地防止代码重复。当创建子类型时，继承提供了大量现成的优点，使用诸如 Layer Supertype 之类的简单模式就是一个例子，这些子类型彼此共享样板实现的大部分。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章