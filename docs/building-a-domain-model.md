# 构建领域模型——持久性不可知论简介

> 原文：<https://www.sitepoint.com/building-a-domain-model/>

在过去的几年中，有如此多的水在[域模型](http://martinfowler.com/eaaCatalog/domainModel.html)桥下流动，很难深入挖掘关键概念而不在已经动荡不安的小溪中激起更多的混乱。此外，随着成吨的 MVC 实现像饥饿的蚂蚁一样激增，首字母缩写词“M”继续遭受 ad-hoc 层(通常称为数据库模型)的症状，它可以完全不受惩罚地用数据库访问代码(因此是基础设施)或其他类型的底层存储污染域逻辑。

在许多情况下，数据库模型之所以如此吸引人，是因为从客户端代码的角度来看，它表现得相当好。毕竟，它很容易被消费，因为它在一个看似无害的 API 后面隐藏了很多复杂性(例如，`$user->save()`之类的东西)。不利的一面是，当坚持良好的面向对象设计实践时，它会产生很大的冲突，更不用说最终浮出水面的过多的可伸缩性和可测试性问题了。

从这个角度来看，似乎流行的数据源架构模式，如[活动记录](http://martinfowler.com/eaaCatalog/activeRecord.html)和[表数据网关](http://martinfowler.com/eaaCatalog/tableDataGateway.html)，当它们与域逻辑耦合时，应该被认为是潜在的有害入侵者。但是指责模式的初衷只是一个无力的借口，因为它没有按照最初设想的目的拥抱领域模型:*一个独立的、与持久性无关的层，负责通过数据和行为清楚地定义系统实体之间的交互。*

当然，上述定义与正式的定义相去甚远。尽管如此，它强调了一些重要的想法。首先，创建一个丰富的域模型，其中多个具有良好定义的约束和规则的域对象相互作用，可能是一项艰巨的任务。其次，不仅需要自顶向下定义模型本身，还需要从头开始实现或重用映射层，以便在持久层和模型之间来回移动数据。这个过程需要至少一个额外的层次来理清事情，因此远离了更务实的方法。

不过，最终的回报可能会令人意外地满意，尤其是考虑到该模型可以毫无争议地从一个基础设施移植到另一个基础设施时。确实是一大笔奖金。

您可能想知道域模型是否能与 PHP 和谐相处。事实上确实如此，尽管代价是必须解决上述问题。但是，像这样的主张必须始终有一个像样的概念证明来支持，所以，请允许我提供几个例子。

## 构建一个基本的博客领域模型

毫无疑问，一个领域模型的基本要素是把重点放在数据和领域对象的行为之间的关系上，同时把任何基础设施的痕迹都排除在外。这种方法的美妙之处(也是我非常喜欢它的原因)在于，这个目标是以优雅和简单的方式实现的。通常，简单的 PHP 域模型由几个[Popo](http://en.wikipedia.org/wiki/Plain_Old_Java_Object)(普通的旧 PHP 对象)组成，它们封装了丰富的业务逻辑，比如验证和策略，隐藏在干净的 API 后面。

也就是说，让我们看看如何将概念性的东西转化为有形的 PHP 代码。因此，假设我们需要构建一个非常复杂的博客程序，它必须能够以标准的方式处理帖子、评论和用户。实现这一点的良好开端是对博客的领域对象建模，如 POPOs。

这是第一个:

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

 	public function setComments(array $comments);
 	public function getComments();
}
```

上面的界面一点也不复杂。事实上，可以清楚地看到,`PostInterface`的目的是为通用 post 对象定义一个狭义的契约，它应该与相关评论有一对多的关系。

由于接口的代码不言自明，所以让我们更进一步，创建另一个接口来为相应的注释指定契约:

```
<?php
namespace Model;

interface CommentInterface
{
    public function setId($id);
    public function getId();

    public function setContent($content);
    public function getContent();

    public function setUser(UserInterface $user);
    public function getUser();
}
```

和`PostInterface`差不多，`CommentInterface`就不多说了。它在模型中加入了博客评论对象的简单契约。很可能在这种情况下唯一值得注意的细节是它的`setUser()`方法的签名，该方法调用[接口注入](http://martinfowler.com/articles/injection.html)的鞭子将用户绑定到特定的注释。

我们几乎完成了模型界面的创建。但是在我们举办一个受之无愧的聚会并开始祝酒之前，有必要再创建一个必须概述博客用户行为的聚会。这是最终界面的样子:

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

    public function setUrl($url);
    public function getUrl();
}
```

有了这批接口，我们已经设法在一瞬间定义了一个简单而有效的粒度契约集，允许您交换具体的域对象实现，就像我们在使用乐高积木一样。

接下来，我们需要用一些接口实现者来充实我们的模型；现在让我们看看如何以一种相当轻松的方式来完成这个任务。

## 用 POPOs 对博客帖子、评论和用户建模

因为我们是相当懒惰的开发人员，每次需要处理域对象的字段时，我们都想省去调用赋值函数和访问函数的麻烦，所以我们可以使用一些样板 PHP 魔法将客户机代码引用从不存在的属性映射到相应的域对象方法。这是一种非常常见和实用的方法，通常封装在抽象类的边界内，如下所示:

```
<?php
namespace Model;

abstract class AbstractEntity
{
    /**
     * Map the setting of non-existing fields to a mutator when
     * possible, otherwise use the matching field
     */
    public function __set($name, $value) {
        $field = "_" . strtolower($name);

        if (!property_exists($this, $field)) {
            throw new InvalidArgumentException(
                "Setting the field '$field' is not valid for this entity.");
        }

        $mutator = "set" . ucfirst(strtolower($name));
        if (method_exists($this, $mutator) &&
            is_callable(array($this, $mutator))) {
            $this->$mutator($value)
        }
        else {
            $this->$field = $value;
        }

        return $this;
    }

    /**
     * Map the getting of non-existing properties to an accessor when 
     * possible, otherwise use the matching field
     */
    public function __get($name) {
        $field = "_" . strtolower($name);

        if (!property_exists($this, $field)) {
            throw new InvalidArgumentException(
                "Getting the field '$field' is not valid for this entity.");
        }

        $accessor = "get" . ucfirst(strtolower($name));
        return (method_exists($this, $accessor) &&
            is_callable(array($this, $accessor)))
            ? $this->$accessor() : $this->field;
    }

    /**
     * Get the entity fields
     */
    public function toArray() {
        return get_object_vars($this);
    }
}
```

我并不强烈主张严重依赖 PHP 的神奇方法，但是在这种情况下，`__set()`和`__get()`方法可以方便地缩短对 setters 和 getters 的调用，而不会过多地扰乱模型的 API。当使用域对象字段时，前面的父类在幕后做一些工作，博客文章、评论和用户对象的具体实现的创建归结为创建父类的子类，如下所示:

```
<?php
namespace Model;

class Post extends AbstractEntity implements PostInterface
{
    protected $_id;
    protected $_title;
    protected $_content;
    protected $_comments;

    public function __construct($title, $content, array $comments = array()) {
        // map post fields to the corresponding mutators
        $this->setTitle($title);
        $this->setContent($content);

        if ($comments) {
            $this->setComments($comments);
        }
    }

    public function setId($id) {
        if ($this->_id !== null) {
            throw new BadMethodCallException(
                "The ID for this post has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException("The post ID is invalid.");
        }

        $this->_id = $id;
        return $this;
    }

    public function setTitle($title) {
        if (!is_string($title)
            || strlen($title) < 2
            || strlen($title) > 100) {
            throw new InvalidArgumentException("The post title is invalid.");
        }

        $this->_title = htmlspecialchars(trim($title), ENT_QUOTES);
        return $this;
    }

    public function getTitle() {
        return $this->_title;
    }

    public function setContent($content)
    {
        if (!is_string($content) || strlen($content) < 2) {
            throw new InvalidArgumentException("The post content is invalid.");
        }

        $this->_content = htmlspecialchars(trim($content), ENT_QUOTES);
        return $this;
    }

    public function getContent() {
        return $this->_content;
    }

    public function setComments(array $comments) {
        foreach ($comments as $comment) {
            if (!$comment instanceof CommentInterface) {
                throw new InvalidArgumentException(
                    "One or more comments are invalid.");
            }
        }

        $this->_comments = $comments;
        return $this;
    }

    public function getComments() {
        return $this->_comments;
    }
}
```

正如您所料，将博客文章建模为 POPO 是一个非常简单的过程，可以简化为 1)实现由相关接口定义的方法，以及 2)可选地扩展基本实体类的功能。更重要的是，由于在这种情况下 post 能够通过它的 mutators 来验证自己，从而携带数据和行为，所以没有必要用分散的验证块来污染应用程序逻辑。这给整个模型接种了疫苗，防止贫血问题，使它更加干净和干燥。

考虑到前面的方法提供了它承诺的表面价值，让我们重用它来建模博客评论和用户。以下是包装这些附加域对象的子类:

```
<?php
namespace Model;

class Comment extends AbstractEntity implements CommentInterface
{
    protected $_id;
    protected $_content;
    protected $_user;

    public function __construct($content, UserInterface $user) {
        $this->setContent($content);
        $this->setUser($user);
    }

    public function setId($id) {
        if ($this->_id !== null) {
            throw new BadMethodCallException(
                "The ID for this comment has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException("The comment ID is invalid.");
        }

        $this->_id = $id;
        return $this;
    }

    public function getId() {
        return $this->_id;
    }

    public function setContent($content) {
        if (!is_string($content) || strlen($content) < 2) {
            throw new InvalidArgumentException(
                "The content of the comment is invalid.");
        }

        $this->_content = htmlspecialchars(trim($content), 
            ENT_QUOTES);
        return $this;
    }

    public function getContent() {
            return $this->_content;
    }

    public function setUser(UserInterface $user) {
        $this->_user = $user;
        return $this;
    }

    public function getuser() {
        return $this->_user;
    }
}
```

```
<?php

namespace Model;

class User extends AbstractEntity implements UserInterface
{
    protected $_id;
    protected $_name;
    protected $_email;
    protected $_url;

    public function __construct($name, $email, $url = null) {
        // map user fields to the corresponding mutators
        $this->setName($name);
        $this->setEmail($email);

        if ($url !== null) {
            $this->setUrl($url);
        }
    }

    public function setId($id) {
        if ($this->_id !== null) {
            throw new BadMethodCallException(
                "The ID for this user has been set already.");
        }

        if (!is_int($id) || $id < 1) {
            throw new InvalidArgumentException("The user ID is invalid.");
        }

        $this->_id = $id;
        return $this;
    }

    public function getId() {
        return $this->_id;
    }

    public function setName($name) {
        if (strlen($name) < 2 || strlen($name) > 30) {
            throw new InvalidArgumentException("The user name is invalid.");
        }

        $this->_name = htmlspecialchars(trim($name), ENT_QUOTES);
        return $this;
    }

    public function getName() {
        return $this->_name;
    }

    public function setEmail($email) {
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("The user email is invalid.");
        }

        $this->_email = $email;
        return $this;
    }

    public function getEmail() {
        return $this->_email;
    }

    public function setUrl($url) {
        if (!filter_var($url, FILTER_VALIDATE_URL)) {
            throw new InvalidArgumentException("The user URL is invalid.");
        }

        $this->_url = $url;
        return $this;
    }

    public function getUrl() {
        return $this->_url;
    }
}
```

任务完成！

即使冒着听起来有点啰嗦的风险，此时此刻可以肯定地说，博客领域模型最终已经确定。底层接口和类就在那里，幸福地生活在对任何类型的持久化机制的存在的无知中，这些持久化机制可能会被实现，无论是数据库、web 服务还是其他任何东西。

此外，它们不仅定义了一个规则网络和丰富的相互关系，而且当前的域对象实现可以毫不费力地替换为自定义的实现。

现在问题变成了如何消费模型。嗯，这确实是一个简单的问题！如果我们要构建一个简单的博客应用程序，其中所有的对象图都一直驻留在内存中，不需要持久化，那么这个过程将是轻而易举的，因为根本不需要创建映射层。然而，为了进一步阐述这个概念，让我们尝试一些具体的代码示例。

## 让领域模型发挥作用

如果我们计划在博客的设计中使用多层视角，应用程序可以很容易地分解成定义良好的层。第一层是典型的引导程序，它将简单地包含并初始化一个自动加载程序，如下所示:

```
<?php
require_once __DIR__ . "/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();
```

引导程序不会做任何奇怪的事情；它只是加载和注册一个 PSR-0 兼容的自动加载器。

第二层将是我们的领域模型的基础，其最基本的实现如下:

```
<?php
use ModelPost,
    ModelComment,
    ModelUser;

// create some posts
$postOne = new Post(
    "Welcome to SitePoint",
    "To become yourself a true PHP master, yeap you must first master PHP.");

$postTwo = new Post(
    "Welcome to SitePoint (Reprise)",
    "To become yourself a PHP Master, yeap you must first master... Wait! Did I post that already?");

// create a user
$user = new User(
    "Everchanging Joe",
    "joe@hisdomain.com");

// add some comments to the first post
$postOne->comments = array(
    new Comment(
        "I just love this post! Looking forward to seeing more of this stuff.",
        $user),
    new Comment(
        "I just changed my mind and dislike this post! Hope not seeing more of this stuff.",
        $user));

// add another comment to the second post
$postTwo->comments = array(
    new Comment(
        "Not quite sure if I like this post or not, so I cannot say anything for now.",
        $user));
```

尽管 model 只执行几个简单的任务，比如创建几个帖子和绑定一个犹豫不决的人的一些评论，但它对于展示如何将域对象连接在一起并让它们工作是很方便的。在这种情况下，每个对象图都是通过使用简单的依赖注入生成的，这对于演示目的来说已经足够了。

但是，如果情况允许，对象图的创建应该委托给更通用的结构，比如依赖注入容器或服务定位器。无论是哪种情况，在这一点上，模型已经按照预期完成了它的业务。

让我们继续创建博客的应用层(MVC 栈中的控制器)，它负责拉入模型数据并将其传递给表示层。

这一层是这样的:

```
<?php
$posts = array($postOne, $postTwo);
```

这一层还能更简单或更短吗？我不这么认为。

抛开所有的嘲讽不谈，简单地说，它证明了一个领域模型很可能是胖模型/瘦控制器咒语的最耀眼的例子。由于所有的重点都放在了业务逻辑上，控制器自然就被简化为模型和用户界面之间的简单中介。

既然我们的博客的应用层运行顺利，让我们创建将以前的博客文章转储到屏幕的层。正如您所料，这是一个枯燥的 HTML 模板，只包含几个 PHP 循环:

```
<!doctype html>
<html>
 <head>
  <meta charset="utf-8">
  <title>Building a Domain Model in PHP</title>
 </head>
 <body>
  <header>
   <h1>SitePoint.com</h1>
  </header>
  <section>
   <ul>
<?php
foreach ($posts as $post) {
?>
    <li>
     <h2><?php echo $post->title;?></h2>
     <p><?php echo $post->content;?></p>
<?php
    if ($post->comments) {
?>
     <ul>  
<?php
        foreach ($post->comments as $comment) {
?>
      <li>
       <h3><?php echo $comment->user->name;?> says:</h3>
       <p><?php echo $comment->content;?></p>
      </li>
<?
        }
?>
     </ul>
<?php
    }
?>
    </li>
<?php
}
?>
   </ul>
  </section>
 </body>
</html>
```

鉴于博客表示层的平庸职责，我决定只使用 PHP 的本地模板功能来实现它。不管怎样，这里要强调的最相关的事情是，将我们的领域模型连接到不同的层确实是轻而易举的事情。最重要的是，整个实现不需要将所讨论的模型绑定到任何形式的持久性基础设施上，因此将它变成了一个易于移植和可伸缩的产品！

这是否意味着领域模型是解决数据库模型在幕后暴露的所有缺陷的灵丹妙药？嗯，从某种意义上来说是这样的，尽管有一些警告。正如在开始时提到的，最大的警告是必须将域对象来回映射到持久层的麻烦，除非我们求助于第三方 ORM 的好处，如[教条](http://www.doctrine-project.org/)、 [RedBeanPHP](http://www.redbeanphp.com/) 或类似的东西，否则这不可能在瞬间完成。

和往常一样，选择预打包的数据映射器还是定制的数据映射器只是个人需求和喜好的问题。正如法国哲学家让·保罗·萨特曾经说过的，“人注定是自由的”。所以，有意识地利用你的自由，挑选最适合你需求的映射库。

## 最后的想法

随着大量 HTTP 框架在 PHP 世界获得发展势头(像 [Symfony 2.x](http://symfony.com/) 、 [Aura](http://auraphp.github.com/) ，甚至 [Zend Framework](http://framework.zend.com/) )，它们没有预先为用户提供基本模型(或者更糟，提供臭名昭著的数据库模型)，希望我们在不久的将来会看到更多富域模型的倡导者。在此期间，深入研究它们，看看如何从零开始实现一个小的，就像我们之前做的那样，是非常有益的。

在以后的文章中，我希望更深入地挖掘领域模型的本质，并开发一个自定义映射层来演示如何在以前的模型和 MySQL 之间传输数据。敬请期待！

图片通过[肯托](http://www.shutterstock.com/gallery-168430p1.html) / [快门](http://shutterstock.com)

## 分享这篇文章