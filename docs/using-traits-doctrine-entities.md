# 在原则实体中使用特征

> 原文：<https://www.sitepoint.com/using-traits-doctrine-entities/>

从 PHP 5.4.0 开始，PHP 支持一种很好的重用代码的方式，称为“Traits”——一组可以包含在另一个类中以避免重复的方法。你可以在之前发布的 SitePoint 帖子中阅读更多关于特质的内容:这里，这里，这里。

![Copy - Flat icon for web and mobile apps](img/c11577e4577e578572668d39b5493c7f.png)

今天，我将向您展示如何在 Symfony 环境中使用它们。

## 特质基础

```
<?php
trait ExampleTrait {
    public function sayHello() {
        echo "Hello";
    }
}

class A {
    use ExampleTrait;
}

class B {
    use ExampleTrait;
}

$one = new A();
$one->sayHello();    /* return `Hello` */

$two = new B();
$two->sayHello();    /* return `Hello`, too */
```

正如我们所看到的，一个基本方法`sayHello()`在一个特征中被声明，这个特征由`A`和`B`类用一个`use`语句实现。很简单，对吧？这个例子很短，但是它应该给你一些处理特征的基本知识。

如果你对特质感兴趣，我建议你阅读一下[官方文档](http://php.net/manual/en/language.oop5.traits.php)和之前发表的 SitePoint 帖子[这里](https://www.sitepoint.com/using-traits-in-php-5-4/)、[这里](https://www.sitepoint.com/lesser-known-features-of-phps-oo-model/)和[这里](https://www.sitepoint.com/php-traits-good-or-bad/)，以全面掌握概念。请允许我警告你一个事实，许多人往往看不到特征和接口之间的区别。这里有一个实用的解释:

> 接口是一个契约，说“这个对象能够做这件事”，而特征是给对象做这件事的能力。

要获得更深入的解释，请随意查看菲利普·布朗的这篇颇有见地的文章,前面的引用来自该文章。

在组织数据库架构时，面临代码重复的情况并不少见。举个例子，假设我们必须开发普通的博客应用程序。在某个时候，我们可能会创建一个基本的`Article`实体，也可能会创建一个`Comment`实体。

这两个实体都将受益于拥有`created_at`和`updated_at`字段(这样我们可以稍后在这些列上对结果进行排序)。但在深入研究特征之前，让我们看看如何在没有特征的情况下，在教义中构建这些实体。

## 步骤 1:创建实体

### 物品实体

```
<?php
namespace Blog\AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Table(name="article")
 * @ORM\Entity(repositoryClass="Blog\AppBundle\Entity\ArticleRepository")
 */
class Article
{
    /**
     * @ORM\Column(name="idArticle" type="integer")
     * @ORM\Id()
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    private $id;

    /* Other properties you need in your entity: $title, $content, $author...  */

    /** @ORM\Column(name="created_at" type="datetime") */
    private $createdAt;

    /** @ORM\Column(name="updated_at" type="datetime") */
    private $updatedAt;

   /* Getters & Setters */
}
```

### 注释实体

```
<?php
namespace Blog\AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Table(name="comment")
 * @ORM\Entity(repositoryClass="Blog\AppBundle\Entity\CommentRepository")
 */
class Comment
{
    /**
     * @ORM\Column(name="idComment" type="integer")
     * @ORM\Id()
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    private $id;

    /* Other properties you need in your entity */

    /** @ORM\Column(name="created_at" type="datetime") */
    private $createdAt;

    /** @ORM\Column(name="updated_at" type="datetime") */
    private $updatedAt;

    /* Getters & Setters */
}
```

相同的属性`$createdAt`和`$updatedAt`包含在两个类中。这还远远没有干。特征能帮助我们清理这些代码吗？

## 第二步:创造特质

```
<?php
// src/Blog/AppBundle/Entity/Traits/TimestampableTrait.php

namespace Blog\AppBundle\Entity\Traits;

use Doctrine\ORM\Mapping as ORM;

trait TimestampableTrait
{
    /**
     * @var datetime $createdAt
     *
     * @ORM\Column(name="created_at", type="datetime")
     */
    private $createdAt;

    /**
     * @var datetime $updatedAt
     *
     * @ORM\Column(name="updated_at", type="datetime")
     */
    private $updatedAt;

    /**
     * Get createdAt
     *
     * @return datetime
     */
    public function getCreatedAt()
    {
        return $this->createdAt;
    }

    /**
     * Set createdAt
     *
     * @param datetime $createdAt
     */
    public function setCreatedAt($createdAt)
    {
        $this->createdAt = $createdAt;

        return $this;
    }

    /**
     * Get updatedAt
     *
     * @return datetime
     */
    public function getUpdatedAt()
    {
        return $this->updatedAt;
    }

    /**
     * Set updatedAt
     *
     * @param datetime $updatedAt
     */
    public function setUpdatedAt($updatedAt)
    {
        $this->updatedAt = $updatedAt;

        return $this;
    }
}
```

这是一个非常有特色的文件，我们已经将最初的重复代码移入其中。`$createdAt`和`$updatedAt`以及所有相关的方法现在都从实体中分离出来了。因此，在其他地方使用它们会容易得多。记住介绍部分的关键词`use`。

## 步骤 3:重构实体

### 物品实体

```
<?php
// src/Blog/AppBundle/Entity/Article.php

namespace Blog\AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;
use Blog\AppBundle\Entity\Traits\TimestampableTrait;

class Article
{
    use TimestampableTrait;

    /**
     * @ORM\Column(name="idArticle" type="integer")
     * @ORM\Id()
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    private $id;

    /* Other properties you need in your entity */

    /* Getters & Setters */
}
```

### 注释实体

```
<?php
// src/Blog/AppBundle/Entity/Comment.php

namespace Blog\AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;
use Blog\AppBundle\Entity\Traits\TimestampableTrait;

/**
 * @ORM\Table(name="comment")
 * @ORM\Entity(repositoryClass="Blog\AppBundle\Entity\CommentRepository")
 */
class Comment
{
    use TimestampableTrait;

    /**
     * @ORM\Column(name="idComment" type="integer")
     * @ORM\Id()
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    private $id;

    /* Other properties you need in your entity */

    /* Getters & Setters */
}
```

搞定了。让我们使用命令行。首先，让我们在数据库中创建实体:

```
php app/console doctrine:schema:create
```

该命令将产生:

```
`Article Entity`

	| idArticle | *All our other fields...* | created_at | updated_at |
	|-----------|---------------------------|------------|------------|

	`Comment Entity`

	| idComment | *All our other fields...* | created_at | updated_at |
	|-----------|---------------------------|------------|------------|
```

现在，如果您想从这些类创建新的对象，您会发现它们都有可用的公共方法:

```
<?php
    $article = new Article();
    $article->setUpdatedAt(new \Datetime('now'));

    $comment = new Comment();
    $comment->setUpdatedAt(new \Datetime('now'));
?>
```

显然，我们现在已经准备好持久化数据了。

## 更进一步

目前，在 Symfony 领域，许多包和扩展倾向于坚持这种做事方式。来自 [KNPLabs](http://knplabs.com) 的 [DoctrineBehaviors](http://knplabs.com/blog/2012/03/30/knp-doctrine-orm-behaviors/) 库为实体和存储库提供了大量特征。本着同样的想法，我建议你深入研究一下众所周知的 [DoctrineExtensions](https://github.com/Atlantic18/DoctrineExtensions) 包，尤其是关于 [Timestampable 行为扩展](https://github.com/Atlantic18/DoctrineExtensions/blob/master/doc/timestampable.md)的一切。

## 最后的想法

特质不难吸收。它们是生成更轻便、更灵活的代码的绝佳方式。注意不要滥用它们:有时，构造一个独特的类实现更好。我怎么强调都不为过，花足够的时间来正确设计你的应用程序是多么重要。如果你认为他们能帮到你，就给他们一个机会。创建你的，测试它们，告诉我们你是如何使用它们的！

## 分享这篇文章