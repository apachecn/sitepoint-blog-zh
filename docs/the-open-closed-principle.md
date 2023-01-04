# 开放/封闭原则

> 原文：<https://www.sitepoint.com/the-open-closed-principle/>

我必须承认，当我第一次偷看[开/闭原则](http://en.wikipedia.org/wiki/Open/closed_principle "Open/Closed principle - Wikipedia, the free encyclopedia")的学术定义时，它的谓词对我来说出奇的清晰。当然，抛开所有的技术术语，它的指令几乎就是我们以前听过很多次的口头禅:“不要黑掉内核”。

好吧，不可否认有一点模糊，因为至少有两种常见的方法来保持“核心”整洁，同时扩展其功能。第一个(这也是我为什么故意使用“扩展”这个术语)是对继承的诉求。继承可能是最被高估的重用实现的野兽；这很容易实现，但是有建模脆弱的层次结构的风险。第二种方法是作文。它不像继承那样简单，但是它仍然是一种在保持模块不变的情况下扩展程序模块的好方法。

问题不在于哪个选项对扩展功能本身是最好的。继承与组合的争论是如此的古老和无聊，以至于把它带到桌面上会是浪费时间。实际的问题是选择一种编程方法，它允许目标模块有一个合适的闭包级别，但同时又为它提供一个“钩子”来打开它进行扩展。理论上，只要继承和组合依赖于抽象而不是具体、严格的实现所提供的内在好处，它们都是实现这一点的良好候选。

也可以说，具体实施是该原则最大的困扰。它们不仅严重违背了开放/封闭原则的戒律，而且使得创建不经过难看的重构或糟糕的条件就能被客户端代码使用的多态模块变得不可能。在最后一种情况下，原则关注的主要领域是支持创建高度解耦的组件，这些组件的依赖关系和模块本身在概念上以抽象契约或接口的形式建模，从而促进了多态性的普遍使用，即使这些组件存在于不同的应用层。

通常，一个很好的方法是通过例子来理解原则背后的东西，以及如何利用它的好处。在这篇文章中，我将建立几个可接近的，试图揭示该原则的驱动力。

## 实现非多态 HTML 呈现器

虽然这个想法主要源于经验的实用主义，但有时通过首先展示做某事的错误方式更容易理解一个概念。在这种情况下，我将坚持这种务实的方法，并展示为什么系统模块的实现不是围绕由开放/封闭原则陈述的“修改闭包”的思想设计的，可能会导致脆弱性/刚性工件的爆炸。

假设我们需要构建一个快速而肮脏的 HTML 渲染器模块，它能够转储一些建模 div、段落等 HTML 对象。按照一种非常草率和非多态的方法，相应的 HTML 类和消费者模块可以编写如下:

```
<?php
namespace LibraryView;

class HtmlDiv
{
    private $text;
    private $id;
    private $class;

    public function __construct($text, $id = null, $class = null) {
        $this->setText($text);
        if ($id !== null) {
            $this->setId($id);
        }
        if ($class !== null) {
            $this->setClass($class);
        }
    }

    public function setText($text) {
        if (!is_string($text) || empty($text)) {
            throw new InvalidArgumentException(
                "The text of the element is invalid.");
        }
        $this->text = $text;
        return $this;
    }

    public function setId($id) {
        if (!preg_match('/^[a-z0-9_-]+$/', $id)) {
            throw new InvalidArgumentException(
                 "The attribute value is invalid.");
        }
        $this->id = $id;
        return $this;     
    }

    public function setClass($class) {
        if (!preg_match('/^[a-z0-9_-]+$/', $id)) {
            throw new InvalidArgumentException(
                 "The attribute value is invalid.");
        }
        $this->class = $class;
        return $this;     
    }

    public function renderDiv() {
        return '<div' .
            ($this->id ? ' id="' . $this->id . '"' : '') .
            ($this->class ? ' class="' . $this->class . '"' : '') .
            '>' . $this->text . '</div>';
    }
}
```

```
<?php
namespace LibraryView;

class HtmlParagraph
{
    private $text;
    private $id;
    private $class;

    public function __construct($text, $id = null, $class = null) {
        $this->setText($text);
        if ($id !== null) {
            $this->setId($id);
        }
        if ($class !== null) {
            $this->setClass($class);
        }
    }

    public function setText($text) {
        if (!is_string($text) || empty($text)) {
            throw new InvalidArgumentException(
                "The text of the element is invalid.");
        }
        $this->text = $text;
        return $this;
    }

    public function setId($id) {
        if (!preg_match('/^[a-z0-9_-]+$/', $id)) {
            throw new InvalidArgumentException(
                 "The attribute value is invalid.");
        }
        $this->id = $id;
        return $this;     
    }

    public function setClass($class) {
        if (!preg_match('/^[a-z0-9_-]+$/', $id)) {
            throw new InvalidArgumentException(
                 "The attribute value is invalid.");
        }
        $this->class = $class;
        return $this;     
    }

    public function renderParagraph() {
        return '<p' .
            ($this->id ? ' id="' . $this->id . '"' : '') .
            ($this->class ? ' class="' . $this->class . '"' : '') .
            '>' . $this->text . '</p>';
    }
}
```

```
<?php
namespace LibraryView;

class HtmlRenderer
{
    private $elements = array();

    public function __construct(array $elements = array()) {
        if (!empty($elements)) {
            $this->addElements($elements);
        }
    }

    public function addElement($element) {
        $this->elements[] = $element;
        return $this;
    }

    public function addElements(array $elements) {
        foreach ($elements as $element) {
            $this->addElement($element);
        }
        return $this;
    }

    public function render() {
        $html = "";
        foreach ($this->elements as $element) {
            if ($element instanceof HtmlDiv) {
                $html .= $element->renderDiv();
            }
            else if ($element instanceof HtmlParagraph) {
                $html .= $element->renderParagraph();
            }
        }
        return $html;
    }
}
```

`HtmlDiv`和`HtmlParagraph`的职责仅限于根据一些常见的输入参数呈现相应的 HTML 元素，例如内部文本以及“id”和“class”属性。

需要被置于聚光灯下的类是 artificated`HtmlRenderer`，它当然可以通过继承或组合进一步扩展。向外界展示这样的美德，那么渲染器会有什么问题呢？它最笨拙的一面在于，在这种情况下，它对修改的封闭级别只是虚张声势，因为它处理的 HTML 对象是非多态的。在它当前的状态下，这个类只能渲染一批`HtmlDiv`和`HtmlParagraph`对象……抱歉，没有别的了。如果我们想要添加一个新的对象类型到它的清单中，那么`render()`方法需要被重构并被更多的条件所污染。用开放/封闭原则的术语来表达这一点，这个类决不是封闭的，不能修改。

## 吸引人们注意多态性的好处

实现一个有效的解决方案需要两个步骤:首先，负责生成 HTML 对象的类应该被转换成遵循共享契约的多态结构。然后应该重构`HtmlRenderer`类来处理契约的任何实现者，而不用显式检查它是否属于某种类型。

能够很好地完成工作的隔离界面是这样的:

```
<?php
namespace LibraryView;

interface HtmlElementInterface
{
    public function render();
}
```

有了这个契约，现在是时候做一些快速清理，并将 HTML 类共享的所有逻辑封装在一个[抽象层超类型](https://www.sitepoint.com/the-layer-supertype-pattern-encapsulating-common-implementation-in-multi-tiered-systems/ "The Layer Supertype Pattern: Encapsulating Common Implementation in Multi-Tiered Systems")的边界内。

```
<?php
namespace LibraryView;

abstract class AbstractHtmlElement implements HtmlElementInterface
{
    protected $text;
    protected $id;
    protected $class;

    public function __construct($text, $id = null, $class = null) {
        $this->setText($text);
        if ($id !== null) {
            $this->setId($id);
        }
        if ($class !== null) {
            $this->setClass($class);
        }
    }

    public function setText($text) {
        if (!is_string($text) || empty($text)) {
            throw new InvalidArgumentException(
                "The text of the element is invalid.");
        }
        $this->text = $text;
        return $this;
    }

    public function setId($id) {
        $this->checkAttribute($id);
        $this->id = $id;
        return $this;     
    }

    public function setClass($class) {
        $this->checkAttribute($class);
        $this->class = $class;
        return $this;     
    }

    protected function checkAttribute($value) {
        if (!preg_match('/^[a-z0-9_-]+$/', $value)) {
            throw new InvalidArgumentException(
                "The attribute value is invalid.");
        }
    }
}
```

现在事情看起来更有吸引力了，因为我们已经成功地将所有 HTML 对象通用的实现放在了超类型的外壳下。尽管过于简单，但这种变化自动将修改后的`HtmlDiv`和`HtmlParagraph`类变成了相同接口的精简实现者:

```
<?php
namespace LibraryView;

class HtmlDiv extends AbstractHtmlElement
{    
    public function render() {
        return '<div' .
            ($this->id ? ' id="' . $this->id . '"' : '') .
            ($this->class ? ' class="' . $this->class . '"' : '') .
            '>' . $this->text . '</div>';
    }
}
```

```
<?php
namespace LibraryView;

class HtmlParagraph extends AbstractHtmlElement
{    
    public function render() {
        return '<p' .
            ($this->id ? ' id="' . $this->id . '"' : '') .
            ($this->class ? ' class="' . $this->class . '"' : '') .
            '>' . $this->text . '</p>';
    }
}
```

考虑到`HtmlDiv`和`HtmlParagraph`现在是漂亮的多态结构，遵守公共契约，很容易将相关的 HTML 渲染器重构为任何`HtmlElementInterface`接口实现者的消费者:

```
<?php
namespace LibraryView;

class HtmlRenderer
{
    private $elements = array();

    public function __construct(array $elements = array()) {
        if (!empty($elements)) {
            $this->addElements($elements);
        }
    }

    public function addElement(HtmlElementInterface $element) {
        $this->elements[] = $element;
        return $this;
    }

    public function addElements(array $elements) {
        foreach ($elements as $element) {
            $this->addElement($element);
        }
        return $this;
    }

    public function render() {
        $html = "";
        foreach ($this->elements as $element) {
            $html .= $element->render();
        }
        return $html;
    }
}
```

我知道`HtmlRenderer`类的新实现离令人兴奋还差得很远，但它仍然是一个遵循开放/封闭原则谓词的可靠模块。此外，它不仅为修改提供了一个很好的封闭级别，因为在运行时用多个接口实现它而不用修改它的任何部分是可行的，而且这个特性本身也证明了它对扩展是完全开放的。请随意表扬自己，因为我们已经在两条战线上取得了胜利！

下面是如何使用该模块来呈现几个 HTML 对象:

```
<?php
use LibraryLoaderAutoloader,
    LibraryViewHtmlDiv,
    LibraryViewHtmlParagraph,
    LibraryViewHtmlRenderer;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();

$div = new HtmlDiv("This is the text of the div.", "dID", "dClass");

$p = new HtmlParagraph("This is the text of the paragraph.", "pID", "pClass");

$renderer = new HtmlRenderer(array($div, $p));
echo $renderer->render();
```

## 结束语

在本文中，我向您展示了如何创建忠实遵守开放/封闭原则的功能性软件组件的示例。作为最相关(如果不是最可靠)的原则之一，该原则当然是 OOP 的一个基本支柱，其好处如此显著，以至于它被不公平地低估了，这是一个耻辱。但是不用说，实现这样一个目标的关键需要将组件设计成多态元素，其行为通过抽象契约明确定义。从那时起，什么方法是扩展它们最有效的(是的，继承/复合二重奏再次出现)，以及它们必须向外部世界公开什么级别的闭包，完全取决于您。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章