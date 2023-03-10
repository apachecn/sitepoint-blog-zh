# 灵活视图处理的模式，第 2 部分——使用装饰器

> 原文：<https://www.sitepoint.com/flexible-view-manipulation-2/>

多年来，我们一直忙于讨论模型/控制器串联的优点和缺点，以至于忽略了视图的作用，将其贬低为笨拙的 HTML 模板。虽然 [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller "Model view controller - Wikipedia, the free encyclopedia") 的起源并不根植于 [OOP 范例](http://en.wikipedia.org/wiki/Object-oriented_programminghttp://en.wikipedia.org/wiki/Object-oriented_programming "Object-oriented Programming - Wikipedia, the free encyclopedia") *本身*，但是目前大多数可用的实现都依赖于类的使用。简而言之，这意味着如果我们自己傲慢的天性使我们相信“视图= HTML 模板”等式是有效的，我们将无法看到将视图视为承载状态和行为的丰富对象的好处，这些状态和行为可以通过几种设计模式的逻辑来操纵。

为了避免因为胡言乱语而被误判，在第一部分中，我从头实现了一个可定制的视图模块，其中视图在概念上被预先设计为 POPOs，并且该模块被赋予了通过相同的 API 递归解析多个视图对象的能力，因此支持一个典型的[复合模式](http://en.wikipedia.org/wiki/Composite_pattern "Composite pattern - Wikipedia, the free encyclopedia")的实现。

事实上，通过统一的 API 处理单个视图和复合视图提供了很大的灵活性，因为它允许我们呈现树形结构(在大多数情况下是 HTML 块，但也可以是浏览器可以消化的任何东西)，而不必用难闻的条件不必要地污染客户端代码，这是违反[开放/封闭原则](http://en.wikipedia.org/wiki/Open/closed_principle "Open/closed principle - Wikipedia, the free encyclopedia")的不祥迹象。

通过利用其他一些模式的细微之处，以相当灵活的方式操纵视图也是可行的，包括被低估的装饰者。如果你想知道装饰者在什么样的平行宇宙中与视图相处，在这一部分中，我将展示如何将它们在尼斯编排中并排工作，以产生另一个视图模块。

## 运行时的流水线功能——将装饰者带到桌面上

这里有一个你可以在你的同事身上尝试的快速测试:问他们在 PHP 中哪里可以使用 Decorators，让他们措手不及。他们可能会说这样的话:“嗯，Zend Framework 实现了表单装饰器，PHPUnit 也使用它们 Symfony 曾经把它们放在某个地方，但我不太确定实际上在哪里。”事实上，这可能是我们中的许多人在这种情况下会嘀咕的，因为装饰者在 PHP 世界中并不是多产的存在。

不管怎样，它们提供的好处是非常显著的。它们允许我们在运行时将功能附加到一个给定的组件上，方法是将它注入到其他几个共享相同接口的包装器中。

这听起来像是一个模糊的概念？让我们尝试一个例子，其中 HTML 元素被建模为实现以下契约的可呈现结构:

```
<?php
namespace LibraryHtml;

interface HtmlElementInterface
{
    public function getText();
    public function render();
}
```

```
<?php
namespace LibraryHtml;

class Span implements HtmlElementInterface
{
    protected $text;

    public function __construct($text) {
        if (!is_string($text) || empty($text)) {
            throw new InvalidArgumentException(
                "The text of the element must be a non-empty string.");
        }
        $this->text = $text;
    }

    public function getText() {
        return $this->text;
    }

    public function render() {
        return "<span>" . $this->text . "</span>";
    }
}
```

构建一个转储单个元素的类不会增加我的开发人员水平，但是它对于展示如何利用 decorators 的好处很有用。

假设我们需要向呈现元素的对象添加一些额外的功能，或者在更广的范围内，向任何其他的`HtmlElementInterface`接口实现者添加一些额外的功能。为此，我们应该首先将公共实现放入抽象装饰器的边界内，如下所示:

```
<?php
namespace LibraryHtml;

abstract class AbstractHtmlDecorator implements HtmlElementInterface
{
    protected $element;

    public function __construct(HtmlElementInterface $element) {
        $this->element = $element;
    }

    public function getText() {
        return $this->element->getText();
    }

    public function render() {
        return $this->element->render();
    }
}
```

有了利用委托的`AbstractHtmlDecorator`类，很容易子类化一些具体的 decoratorss，这些 decorator 在幕后对注入的 HTML 元素执行一些额外的任务。此外，下面的方法依靠这个概念将元素的标记包装在一些< p >、< div >和< section >标签中:

```
<?php
namespace LibraryHtml;

class DivDecorator extends AbstractHtmlDecorator
{
    public function render() {
        return "<div>" . $this->element->render() . "</div>";
    }
}
```

```
<?php
namespace LibraryHtml;

class ParagraphDecorator extends AbstractHtmlDecorator
{
    public function render() {
        return "<p>" . $this->element->render() . "</p>";
    }
}
```

```
<?php
namespace LibraryHtml;

class SectionDecorator extends AbstractHtmlDecorator
{
    public function render() {
        return "<section>" . $this->element->render() . "</section>";
    }
}
```

诚然，每个装饰者扮演的角色都相当平庸。不过，让我们看看它们是如何工作的:

```
<?php
use LibraryLoaderAutoloader,
    LibraryHtmlSpan,
    LibraryHtmlParagraphDecorator,
    LibraryHtmlDivDecorator,
    LibraryHtmlSectionDecorator;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader();
$autoloader->register();

$div = new DivDecorator(
    new ParagraphDecorator(new Span("Welcome to SitePoint!"))
);
echo $div->render();

$section = new SectionDecorator(
    new DivDecorator(
        new ParagraphDecorator(new Span("Welcome to SitePoint!"))
    )
);
echo $section->render();
```

在运行时构建多个 decorators 管道以呈现多个标记结构是一个简单的过程。每个装饰器的组装顺序可以随意改变，并且可以利用这种技巧动态生成 HTML 的不同部分，每个部分显示不同的语义。

虽然前面的例子是说明性的，但当涉及到生产时，它就显得不够了，因为用这种方法以编程方式生成大块的标记是没有意义的。您可能想知道除了呈现一些简单的 HTML 元素之外，我们如何以更有用的方式使用装饰器。

## 以更务实的方式使用装饰器——建立一个可扩展的视图模块

我们可以建立一个基本视图类的模型，反过来，将一堆实现相同接口的装饰器附加到它上面，这将让我们动态地生成漂亮的网页页眉、页脚，甚至外部布局。实现一个利用 decorator 的可定制视图模块比人们想象的要容易得多，因为整个过程可以归结为创建前面提到的基本视图类，然后根据更精确的需求通过一个或多个 decorator 扩展它的功能。

为了让模块工作，我将使用我在第一部分中编写的相同的`View`类，它实现了一个分离的`ViewInterface`接口。有了这个已经编码的组件，我们可以专注于几个装饰器，它们将负责呈现定制的页眉、页脚和布局。下面是执行这些任务的装饰者的层次结构:

```
<?php
namespace LibraryView;

abstract class AbstractViewDecorator implements ViewInterface
{
    const DEFAULT_TEMPLATE = "default.php";
    protected $template = self::DEFAULT_TEMPLATE;
    protected $view;

    public function __construct(ViewInterface $view) {
        $this->view = $view;
    }

    public function render() {
        return $this->view->render();
    }

    protected function renderTemplate(array $data = array()) {
        extract($data);
        ob_start();
        include $this->template;
        return ob_get_clean();
    }
}
```

```
<?php
namespace LibraryView;

class OuterViewDecorator extends AbstractViewDecorator
{
    const DEFAULT_TEMPLATE = "layout.php";

    public function render() {
        $data["innerview"] = $this->view->render();
        return $this->renderTemplate($data);
    }
}
```

```
<?php
namespace LibraryView;

class HeaderViewDecorator extends AbstractViewDecorator
{
    const DEFAULT_TEMPLATE = "header.php";

    public function render() {
        return $this->renderTemplate() . $this->view->render();
    }
}
```

```
<?php
namespace LibraryView;

class FooterViewDecorator extends AbstractViewDecorator
{
    const DEFAULT_TEMPLATE = "footer.php";

    public function render() {
        return $this->view->render() . $this->renderTemplate();
    }
}
```

每个装饰器都是注入视图对象的普通包装器，这些对象反过来覆盖基本的`render()`方法，以便向对象的模板添加一些特定的 web 页面部分。如果我给出一些可测试的例子，渲染过程应该更容易理解。所以，假设我们已经定义了两个骨架模板，叫做`partial.php`和`layout.php`，看起来如下:

```
<h2><?php echo $this->heading;?></h2>
<p><?php echo $this->content;?></p>
```

```
<!doctype html>
<html>
 <head>
  <meta charset="utf-8">
  <title>My fancy web page</title>
 </head>
 <body>
  <header>
   <h1>A sample header</h1>
  </header>
  <section>
   <?php echo $innerview;?>
  </section>
  <footer>
   <p>A sample footer</p>
  </footer>
 </body>
</html>
```

现在，如果我们需要将部分直线嵌入到外部布局中，该过程将简化为编写以下代码片段:

```
<?php
$view = new View("partial");
$view->heading = "This is the sample heading line";
$view->content = "This is the sample content";

$page = new OuterViewDecorator($view);
echo $page->render();
```

那确实是轻而易举的事。但是我们还没有完成。还有一些更好的东西可以玩。如何呈现一个类似的 HTML5 文档，这次是在运行时附加页眉和页脚？那么，让我们定义绑定到相应装饰器的模板，即`header.php`和`footer.php`:

```
<!doctype html>
<html>
 <head>
  <meta charset="utf-8">
  <title>My fancy web page</title>
 </head>
 <body>
  <header>
   <h1>A sample header</h1>
  </header>
```

```
<footer>
   <p>A sample footer</p>
  </footer>
 </body>
</html>
```

有了模板，我们现在可以生成一个包含分部的视图对象，然后按照所需的顺序放置装饰器，如下所示:

```
<?php
$view = new View("partial");
$view->heading = "This is the sample heading line";
$view->content = "This is the sample content";

$page = new FooterViewDecorator(new HeaderViewDecorator($view));
echo $page->render();
```

虽然我不得不承认，在这种情况下，呈现一个格式良好的 HTML 文档的过程比处理一个带有分散的 PHP 代码的单个模板要麻烦和乏味一些，但简单地说，它展示了如何在可定制视图模块的实现中利用几个简单的装饰器提供的功能。此外，考虑到模块固有的灵活性，通过在内部加入一些额外的装饰器，而不是刚刚实现的装饰器，可以很容易地使它更加丰富。

如果你觉得自己很大胆，想要应对这个挑战，你将会免费获得一次提高编码技能的机会。

## 最后的想法

很少有人会不同意复合和装饰模式是“偏爱复合胜过继承”的最明显的例子。尽管如此，它们仍然是 PHP 中被忽视的范例，仅限于少数流行框架的黑暗角落。

尽管如此，利用它们的能力并以灵活的方式使用它们来操作视图对象并不复杂。在这种情况下，本综述中展示的模块只是基本的例子，离生产就绪还差得很远；希望它们足以说明一个有效的观点，并鼓励以多方面的方式使用模式。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章