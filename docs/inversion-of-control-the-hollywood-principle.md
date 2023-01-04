# 控制倒置——好莱坞原则

> 原文：<https://www.sitepoint.com/inversion-of-control-the-hollywood-principle/>

程序员之间有一个共识(包括我自己，所以这是我自己的公开道歉),控制反转(IoC)只不过是普通的依赖注入(DI)的同义词。有一个非常直观的原因支持这种思维模式:如果 DI 背后的动机是促进类的设计，而这些类的外部合作者是由其周围的上下文提供的，而不是反向查找它们，那么这个过程可以有效地被视为 IoC 的一种形式。

但是，虽然 DI = IoC 方程可以被认为是普遍有效的，但控制反转本身的概念实际上要宽泛得多。事实上，可以说 DI 是利用 IoC 优势的一个特定用例，但它远不是唯一的一个。这让我们回到起点；如果直接投资只是一种依靠国际奥委会力量的模式，那么国际奥委会到底是什么？

传统上，应用程序组件被设计为在执行环境上操作和控制执行环境，这种方法在某种程度上交付得很好。例如，日志模块可以被实现为将数据记录到文件中，并且如何以及何时记录数据将完全在该模块的控制之下。日志文件(在这种情况下是环境的一部分)只是一个外部的、被动的元素，对模块的工作方式没有影响。但是，假设我们需要扩展该模块的功能，使其能够将数据额外记录到数据库中，或者最终甚至通过电子邮件。升级模块以公开额外的功能将使其复杂性增加，变得更加臃肿，因为处理这些额外任务所需的逻辑被打包在同一个 API 后面。这种方法可行，但根本无法扩展。

这种错综复杂的局面可以用一种相当简单的方式来解决。我们可以将责任直接转移到外部环境，而不是让模块完全负责将数据记录到多个端点。该模块的实现仍然简单得可笑，仅限于充当简单的事件调度程序。另一方面，环境将负责实现记录数据所需的所有逻辑，完全独立于所讨论的模块。

毫不奇怪，在组件和环境之间转换这些职责的过程正式地被称为控制反转(或者用更轻松的行话来说，[好莱坞原则](http://en.wikipedia.org/wiki/Hollywood_principle "Hollywood principle - Wikipedia, the free encyclopedia"))，并且当涉及到开发可扩展的、高度解耦的程序模块时，它的实现可以是一个真正的推动。

当然，IoC 是一个与语言无关的范例，因此可以在 PHP 世界中轻松使用它。

## 实现控制反转——观察领域对象

IoC 确实无处不在，所以很容易找到它的实现。我想到的第一个用例是依赖注入，但是还有很多其他的用例同样具有示范性，尤其是当涉及到事件驱动设计的时候。如果您想知道 IoC 在什么样的平行宇宙中与事件处理机制相处，请考虑 GoF 剧目中的一个经典:观察者模式。

几乎无处不在，甚至通过 JavaScript 在客户端使用，观察者是 IoC 概念在实践中的一个光辉例子；有一个高度解耦的主题，专注于在不污染周围环境的情况下完成几个狭窄的任务，而一个或多个外部观察者负责实现处理主题触发的事件所需的逻辑。如何处理事件，甚至处理新的事件，完全是观察者的责任，而不是主体的责任。

举个例子可能是让我之前的胡言乱语更清楚一点的好方法。因此，假设我们已经实现了一个原始的领域模型，它定义了博客帖子和评论之间的一对多关系。在这种情况下，我们将故意雄心勃勃，让模型能够在帖子中添加新评论时发送电子邮件通知系统管理员。

老实说，实现这样一个特性而不求助于 IoC 实际上会是一团乱麻，因为我们会要求域对象做一些超出它们范围的事情。相反，我们可以采用基于 IoC 的方法，并将域类定义如下:

```
<?php
namespace Model;

interface PostInterface
{
    public function setTitle($title);
    public function getTitle();

    public function setContent($content);
    public function getContent();

    public function setComment(CommentInterface $comment);
    public function getComments();
}
```

```
<?php
namespace Model;

class Post implements PostInterface, SplSubject
{
    private $title;
    private $content;
    private $comments  = [];
    private $observers = [];

    public function __construct($title, $content) {
        $this->setTitle($title);
        $this->setContent($content);
    }

    public function setTitle($title) {
        if (!is_string($title) 
            || strlen($title) < 2
            || strlen($title) > 100) {
            throw new InvalidArgumentException(
                "The post title is invalid.");
        }
        $this->title = $title;
        return $this;
    }

    public function getTitle() {
        return $this->title;
    }

    public function setContent($content) {
        if (!is_string($content) || strlen($content) < 10) {
            throw new InvalidArgumentException(
                "The post content is invalid.");
        }
        $this->content = $content;
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setComment(CommentInterface $comment) {
        $this->comments[] = $comment;
        $this->notify();
    }

    public function getComments() {
        return $this->comments;
    }

    public function attach(SplObserver $observer) { 
        $id = spl_object_hash($observer);
        if (!isset($this->observers[$id])) {
            $this->observers[$id] = $observer;
        }
        return $this;
    }

    public function detach(SplObserver $observer) {
        $id = spl_object_hash($observer);
        if (!isset($this->observers[$id])) {
            throw new RuntimeException(
                "Unable to detach the requested observer.");
        }
        unset($this->observers[$id]); 
        return $this;
    }

    public function notify() {
        foreach ($this->observers as $observer) {    
            $observer->update($this);
        }
    }
}
```

```
<?php
namespace Model;

interface CommentInterface
{
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
    private $content;
    private $author;

    public function __construct($content, $author) {
       $this->setContent($content);
       $this->setAuthor($author);
    }

    public function setContent($content) {
        if (!is_string($content) || strlen($content) < 10) {
            throw new InvalidArgumentException(
                "The comment is invalid.");
        }
        $this->content = $content;
        return $this;
    }

    public function getContent() {
        return $this->content;
    }

    public function setAuthor($author) {
        if (!is_string($author) 
            || strlen($author) < 2
            || strlen($author) > 50) {
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

`Post`和`Comment`类之间的交互是琐碎的，但是`Post`类值得深入研究。实际上，它被设计成一个“经典”主题，因此提供了典型的 API，允许随意附加/分离和通知观察者。

这个过程最有趣的方面是`setComment()`的实现，在这里发生了实际的控制反转。每当添加注释时，该方法只向所有注册的观察者发送一个“拉”更新。这意味着发送电子邮件通知所需的所有逻辑都被委托给一个或多个外部观察者，从而将脏活从`Post`身上卸下，使其专注于自己的业务逻辑。

有了这个简单但有效的控制反转模式，唯一需要添加的结构是至少一个观察者，他应该负责发送上述电子邮件。为了使事情易于理解，我将把观察者实现为一个在服务层中生活和呼吸的瘦实体。

## 将控制权委托给外部环境——实现注释通知服务

构建一个能够在博客文章中添加新评论时触发电子邮件通知的 observer 服务是一个简单的过程，只需定义一个实现附属`update()`方法的类。如果你很好奇，想看看有问题的服务是什么样的，这里是:

```
<?php
namespace Service;

class CommentService implements SplObserver
{
    public function update(SplSubject $post) {
        $subject = "New comment posted!";
        $message = "A comment has been made on a post entitled " .
            $post->getTitle();
        $headers = "From: "Notification System" <notify@example.com>rnMIME-Version: 1.0rn";
        if (!@mail("admin@example.com", $subject, $message, $headers)) {
            throw new RuntimeException("Unable to send the update.");
        }
    }
}
```

`CommentService`类做了它应该做的事情；每当用户发表与给定帖子相关的评论时，它调用它的`update()`方法向系统管理员发送一封电子邮件。

如果我向您展示一个让所有示例类都工作的脚本，那么在这种情况下看到控制反转带来的好处会容易得多，所以下面是一些代码:

```
<?php
use LibraryLoaderAutoloader,
    ModelPost,
    ModelComment,
    ServiceCommentService;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();

$post = new Post(
    "A sample post",
    "This is the content of the sample post"
);

$post->attach(new CommentService());

$comment = new Comment(
    "A sample comment",
    "Just commenting on the previous post"
);

$post->setComment($comment);
```

很有可能，这只是我个人的偏好，我觉得将服务注入域对象的内部是不太合适的。那些一直宣称领域层必须与服务层无关(除非你求助于分离的接口)，并且这一层应该位于与多个客户交互操作的上层。但是在这种情况下，考虑到`Post`类只是一个注册观察者的虚拟容器，只有在触发事件更新时才会被使用，这种方法并没有那么罪恶。此外，考虑到有问题的服务和 Post 类之间的责任是多么清晰地颠倒了，我的抱怨应该被认为是非常挑剔的突发奇想。

## 结束语

经常被认为是一个晦涩难懂的概念，特别是在 PHP 中，许多开发人员倾向于直观地将这个概念与简单的旧依赖注入联系起来，控制反转是一种简单而杀手级的编程方法，当正确实现时，它是一种创建解耦的正交系统的极好方法，这些系统的组件可以很容易地被隔离测试。

如果您在应用程序中使用依赖注入(您正在使用，对吗？)那么你应该感觉到你的程序员的本能得到了很好的满足，因为你已经利用了控制反转提供的好处。然而，正如我之前试图演示的那样，除了以正确的方式管理类依赖之外，这种方法还适用于很多情况。事件驱动设计无疑是一个很好的例子。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章