# 灵活视图处理的模式，第 1 部分——使用组合

> 原文：<https://www.sitepoint.com/flexible-view-manipulation-1/>

我敢说，各种形式的观点都是被低估的寻求真实身份的实体。从 MVC 开始进入 PHP 社区的那一刻起，缩写词“V”就被不公平地降到了普通模板的水平，其职责仅限于呈现几个 HTML 页面。

虽然没有人会不同意视图的作用与输出数据密切相关，但是将它们仅仅视为背后没有逻辑的模板是相当愚蠢的。视图不仅可以是携带状态的成熟对象，而且可以是模型的观察者，足够灵活以适应模型状态变化时的波动并做出相应的反应。虽然这些变化肯定会波及到花哨的用户界面，但这仅仅是它们响应性的结果，而不是它们存在的主要原因。

另一方面，这种迟来的预言也提出了一个有趣的观点。如果视图是如此漂亮的组件，向外界展示各种丰富而精彩的特性，我们可能会认为，建立一个能够以实用的方式处理它们的机制比仅仅呈现一些简单的 HTML 模板更难实现。

从某种意义上说，可能是。像往常一样，看似复杂的问题可以通过诉诸更简单的方法来解决。此外，由于在许多情况下，视图在概念上被建模为共享某种公共呈现接口的框架 PHP 对象(因此支持多态性)，因此通过统一 API 单独或联合处理它们相对简单。

如果您重新阅读前面的句子，您会发现与复合模式带来的功能有相似之处。很好，当然。不过，在不破坏与客户端代码的契约的情况下，将视图递归地注入到其他视图中会更好，这将让我们实现一些整洁的装饰器，能够在运行时组装视图管道，然后可以一次完成所有渲染。

听起来很像书呆子的诱饵？其实不是。为了克服您的疑虑，在这个由两部分组成的教程中，我将向您展示如何通过深入研究复合和装饰模式来从头实现几个可定制的视图处理模块。

## 迈出第一步–实施基本视图模块

包括在 GoF 的经典剧目中，复合模式是旧的“偏爱复合胜过继承”咒语的最明显的例子之一。简而言之，模式的力量允许您通过使用相同的 API 来操作给定组件的单个和多个实例。这种能力对于遍历由叶子和分支对象(是的，对象由其他对象提供)组成的树结构特别有吸引力，而不必用条件使代码混乱，因此部署了一个基于多态性的优雅解决方案。

虽然在理论上，当涉及到消费复合材料时，一切看起来都很甜蜜和迷人，但是要弄清楚如何将它们应用到现实世界中来从头构建一个灵活的视图模块是相当复杂的。没有必要感到痛苦，因为正在讨论的模块的实现实际上是一个相当简单的过程，首先创建一个能够对单个视图的数据和行为建模的类(所谓的叶对象)，然后定义一个适当的对应对象，负责递归地操作复合视图。

因为在这种情况下，我希望保持整个实现简洁，并且从上到下易于理解，所以我计划添加到模块中的第一个元素将是一个负责生成各个视图的类。这是这张照片的样子:

```
<?php
namespace LibraryView;

interface ViewInterface
{
    public function setTemplate($template);
    public function getTemplate();
    public function __set($field, $value);
    public function __get($field);
    public function __isset($field);
    public function __unset($field);
    public function render();
}
```

```
<?php
namespace LibraryView;

class View implements ViewInterface 
{
    const DEFAULT_TEMPLATE = "default.php";

    protected $template = self::DEFAULT_TEMPLATE;
    protected $fields = array();

    public function __construct($template = null, array $fields = array()) {
        if ($template !== null) {
            $this->setTemplate($template);
        }
        if (!empty($fields)) {
            foreach ($fields as $name => $value) {
                $this->$name = $value;
            }
        } 
    }

    public function setTemplate($template) {
        $template = $template . ".php";
        if (!is_file($template) || !is_readable($template)) {
            throw new InvalidArgumentException(
                "The template '$template' is invalid.");   
        }
        $this->template = $template;
        return $this;
    }

    public function getTemplate() {
        return $this->template;
    }

    public function __set($name, $value) {
        $this->fields[$name] = $value;
        return $this;
    }

    public function __get($name) {
        if (!isset($this->fields[$name])) {
            throw new InvalidArgumentException(
                "Unable to get the field '$field'.");
        }
        $field = $this->fields[$name];
        return $field instanceof Closure ? $field($this) : $field;
    }

    public function __isset($name) {
        return isset($this->fields[$name]);
    }

    public function __unset($name) {
        if (!isset($this->fields[$name])) {
            throw new InvalidArgumentException(
                "Unable to unset the field '$field'.");
        }
        unset($this->fields[$name]);
        return $this;
    }

    public function render() {
        extract($this->fields);
        ob_start();
        include $this->template;
        return ob_get_clean();
    }
}
```

如前所述，视图是吸引人的候选人，他们努力祈祷被模仿成 POPOs，当然这个`View`类尊重这个概念。它的功能可以归结为在幕后使用一些 PHP 魔法，以便从视图中分配和剥离字段，然后由它的`render()`方法解析这些字段。

考虑到这个类的可延展性，很容易让它运行起来而不需要太多的麻烦。要做到这一点，首先我们应该确保定义它的默认模板，在一个非常基本的级别上，它可能看起来像这样:

```
<!doctype html>
<head>
  <meta charset="utf-8">
  <title>The Default Template</title>
</head>
 <body>
  <header>
    <h1>You are viewing the default page!</h1>
<?php echo $this->header;?>
  </header>
  <section>
<?php echo $this->body;?>
  </section>
  <footer>
<?php echo $this->footer;?>
  </footer>
 </body>
</html>
```

有了这个模板，就该看看如何将它连接到`View`类，这样它就可以被填充并转储到浏览器中。下面的代码片段就是这样做的:

```
<?php
use LibraryLoaderAutoloader,
    LibraryViewView;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$view = new View();
$view->header = "This is my fancy header section";
$view->body = "This is my fancy body section";
$view->footer = "This is my fancy footer section";
echo $view->render();
```

一点也不差，对吧？尽管我不得不承认这个视图实现了比我通常习惯的更神奇的东西，但在创建可呈现的视图对象时，它表现得相当不错。此外，考虑到该类能够解析放入模板的闭包，我们可以更大胆一点，用它来生成两步视图。为了实现这一点，我们应该创建至少一个极简的部分文件，称为`partial.php`或类似的东西，类似于这个:

```
<p><?php echo $this->content;?></p>
```

将每个模板属性换成部分属性将是一个简单的过程，简化为定义几个闭包:

```
<?php
$view = new View();

$view->header = function () {
    $header = new View("partial");
    $header->content = "This is my fancy header section";
    return $header->render();
};

$view->body = function () {
    $body = new View("partial");
    $body->content = "This is my fancy body section";
    return $body->render();
};

$view->footer = function () {
    $footer = new View("partial");
    $footer->content = "This is my fancy footer section";
    return $footer->render();
};

echo $view->render();
```

我们已经实现了一个非常灵活的视图模块，可以用来渲染孤立的物体，甚至是虚拟的局部。然而，尽管有这些优点，该模块在渲染视图树时仍然有所欠缺。这可以通过嵌套闭包来实现，至少在我看来，这是一个非常草率的解决方案。不如改用复合模式吧？毕竟，它提供了开箱即用的功能，同时保留了 OOP 的本质。

## 构建复合视图——叶子和树枝是平等的

事实上，修改前面模块的结构使其能够通过统一的 API 处理复合视图是非常简单的。我们应该引入的第一个变化是使视图类成为几个独立接口的实现者，而不是它当前使用的单一接口。这些细粒度接口定义的契约集如下所示:

```
<?php
namespace LibraryView;

interface TemplateInterface
{
    public function setTemplate($template);
    public function getTemplate();
}
```

```
<?php
namespace LibraryView;

interface ContainerInterface
{
    public function __set($field, $value);
    public function __get($field);
    public function __isset($field);
    public function __unset($field);
}
```

```
<?php
namespace LibraryView;

interface ViewInterface
{
    public function render();
}
```

虽然不明确，但是定义几个粒度契约最吸引人的方面是，现在,`render()`方法可以由处理独立视图的类和处理复合视图的类同时实现。有了这个特性，我们现在应该重构`View`类的签名，这样它就可以遵守这些更改:

```
<?php
class View implements TemplateInterface, ContainerInterface, ViewInterface 
{
    // the same implementation goes here
}
```

仍然是一个完全可呈现的实体，尽管它的功能响应不同接口强加的契约。此时，您可能想知道复合模式的具体细节在哪里以及如何适合这个模式。抛开焦虑，看看下面的类，它……是的，具有一次性递归解析多个视图的能力:

```
<?php
namespace LibraryView;

class CompositeView implements ViewInterface
{
    protected $views = array();

    public function attachView(ViewInterface $view) {
        if (!in_array($view, $this->views, true)) {
            $this->views[] = $view;
        }
        return $this;
    }

    public function detachView(ViewInterface $view) {
        $this->views = array_filter($this->views, function ($value) use ($view) {
            return $value !== $view;
        });
        return $this;
    }

    public function render() {
        $output = "";
        foreach ($this->views as $view) {
            $output .= $view->render();
        }
        return $output;
    }
}
```

虽然它的`attachView()` / `detachView()`串联允许我们随意添加和删除视图，包括其他复合视图，但是`CompositeView`类只不过是一个隐藏在花哨名称后面的普通视图容器。这种漂亮的递归能力经常出现在复合模式的典型实现中，免费带来了很多灵活性。例如，考虑一个常见的场景，其中 HTML 页面由经典的页眉、正文和页脚组成。为了使这个例子更加简单，我们假设这些部分绑定了下面的`header.php`、`body.php`和`footer.php`模板:

```
<!doctype html>
<head>
  <meta charset="utf-8">
  <title>A Sample Template</title>
</head>
<body>
  <header>
    <h1>Welcome to this page, which you're accessing from <?php echo $this->ip;?></h1>
    <p><?php echo $this->content;?></p>
  </header>
```

```
<section>
  <p><?php echo $this->content;?></p>
</section>
```

```
<footer>
   <p><?php echo $this->content;?></p>
  </footer>
 </body>
</html>
```

在传统的方法中，每个部分可以很容易地组装成一个隔离的视图，并以线性方式呈现，这一切都很好。然而，我们可以使用`CompositeView`类，通过一个方法调用来呈现整个页面，如下所示:

```
<?php
use LibraryLoaderAutoloader,
    LibraryViewView,
    LibraryViewCompositeView;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$header = new View("header");
$header->content = "This is my fancy header section";
$header->ip = function () {
    return $_SERVER["REMOTE_ADDR"];
};

$body = new View("body");
$body->content = "This is my fancy body section";

$footer = new View("footer");
$footer->content = "This is my fancy footer section";

$compositeView = new CompositeView;

echo $compositeView->attachView($header)
                   ->attachView($body)
                   ->attachView($footer)
                   ->render();
```

这种方法的魅力在于复合模式隐藏在其背后的固有灵活性。考虑到普通视图和复合视图都是同一个接口的实现者，很容易递归地注入它们并创建复杂的布局，而不必修改任何客户端代码。

上面的例子通常是相当保守的，因为组合的递归注入实际上从来没有发生过。然而，将一个更自由的计划付诸实施，其中单一和复合视图只是传递给其他视图，应该是一个简单的过程。如果你觉得大胆，想给视图模块一个不同的转折，你有绿灯这样做。

## 结束语

复合模式通常被放在一个肤浅而模糊的“灵活编程”类别下，它是一个非常矛盾的例子，因为它的优雅而产生的喜悦之情很快就被复杂的实现掩盖了。然而，当被适当驯服时，它被证明是一个非常可靠的解决方案，允许我们轻松地操纵视图对象，无论是作为孤立的实体还是递归集的形式。

这并不意味着每当需要在某个地方部署视图处理机制时，就必须坚持模式的优点，因为教条是没有意义的。此外，如果组合不符合您的口味，可以另辟蹊径，通过调用装饰者带来的功能来实现灵活的视图模块。在后续文章中，我将详细介绍这种方法的内部工作原理，所以不要错过它！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章