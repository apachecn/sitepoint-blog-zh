# MVC 模式和 PHP，第 2 部分

> 原文：<https://www.sitepoint.com/the-mvc-pattern-and-php-2/>

欢迎来到讨论 MVC 和 PHP 的两部分系列的第 2 部分，在这里我们将讨论使用 MVC 架构时必须考虑的一些问题。如果您没有先阅读第 1 部分就直接阅读了本文，我鼓励您[回头仔细阅读](https://www.sitepoint.com/the-mvc-pattern-and-php-1/ "The MVC Pattern and PHP, Part 1"),因为本文假设您已经阅读并理解了它讨论的所有内容。记住这一点，让我们开始吧！

## 路由和 URL

尽管从理论上讲，MVC 应该在所有形式的计算机编程中完美地工作，但是在 web 上将 MVC 与 PHP 结合起来可能有点棘手。第一个问题是 URL 路由。URL 路由是 MVC 创建时从未考虑过的一个方面，因此随着互联网的发展和技术的扩展，我们使用的架构模式也必须如此。

那么我们有什么选择来解决 URL 路由问题呢？一种解决方案是尝试抢占您的站点或 web 应用程序需要的任何 URL，将它们与关于为每个页面或部分加载哪个模型、视图和控制器的信息一起存储在永久存储中。然后，系统获取用户请求的 URL，加载分配给该页面的特定组件并开始工作。如果你正在创建一个不依赖于动态 URL 的组合网站或者静态网站，这是一个完全可行的解决方案。例如:

```
<?php
$page = $_GET['page'];
if (!empty($page)) {

    $data = array(
        'about' => array('model' => 'AboutModel', 'view' => 'AboutView', 'controller' => 'AboutController'),
        'portfolio' => array('model' => 'PortfolioModel', 'view' => 'PortfolioView', 'controller' => 'PortfolioController')
    );

    foreach($data as $key => $components){
        if ($page == $key) {
            $model = $components['model'];
            $view = $components['view'];
            $controller = $components['controller'];
            break;
        }
    }

    if (isset($model)) {
        $m = new $model();
        $c = new $controller($model);
        $v = new $view($model);
        echo $v->output();
    }
}
```

我们的 URL 应该是这样的:

```
example.com/index.php?page=about
```

或者

```
example.com/index.php?page=portfolio
```

示例 MVC 系统为所请求的页面加载特定的模型、视图和控制器集。如果 URL 参数是“关于”，那么将显示“关于”页面。如果参数是“投资组合”，投资组合页面将改为。

是静态路由的一个基本示例，尽管设置非常简单，但也有一些缺点。一个最明显的缺点是可伸缩性变得更加困难，因为站点的宽度被硬编码的页面数组所限制。

另一个选择是打开模型、视图和控制器类的分配，让 URL 定义这些参数。在静态路由的例子中，我们简单地从一个数组中提取类的标识，这个数组反过来充当来自永久存储器的路由数据。用元素替换数组将我们的静态路由变成了动态路由。

尽管我们用 URL 变量为数组中的每个关联提取了一个键，但是与相应类的关系已经预先定义好了；我们不能用静态路由混合和匹配每个键中的值。但是我们为什么要这么做呢？首先，我们不必对系统的每个部分进行硬编码。我们可以通过创建模型、视图和控制器关系来创建部分或页面。例如:

```
<?php
$model = $_GET['model'];
$view = $_GET['view'];
$controller = $_GET['controller'];
$action = $_GET['action'];

if (!(empty($model) || empty($view) || empty($controller) || empty($action))) {
    $m = new $model();
    $c = new $controller($m, $action);
    $v = new $view($m);
    echo $v->output();
}
```

我们的新 URL 现在看起来像这样:

```
example.com/index.php?controller=controllername&map;model=modelname&view=viewname&action=actionname
```

动作 URL 变量告诉系统要执行控制器中的哪个函数。重要的是要记住，当这个函数向模型传递数据时，它向模型传递一段数据，这将依次指示加载哪个视图和视图操作。这可以是动作 URL 变量，但也可以是单独的变量，或者是由控制器收集的数据。请记住，永远不要允许控制器加载或直接向视图传递数据；它必须只与模型和用户的输入交互。

这两种方法各有利弊，静态路由更稳定，实现更快，并允许开发人员对系统进行更多的控制，而动态路由允许我们创建一个更有效的系统，在可伸缩性和可移植性方面有巨大的潜力。

然而，与静态路由相比，动态路由会给控制器带来更多的责任，这可以被视为改变了传统的 MVC 模式。然而，如果正确有效地实施动态路由，与使用静态路由相比，它可以使控制器在系统内更受欢迎。

添加一个前端控制器将允许您的系统动态地加载各个部分，这取决于您希望它加载什么。Alejandro Gervasio 写了一篇精彩的关于前端控制器模式的 2 部分文章，其中提到了使用带有 MVC 元素的前端控制器的想法。我强烈推荐任何想了解前端控制器和 MVC 的人。我还建议快速访问[汤姆·巴特勒的网站](http://r.je/ "Tom Butler on programming")，因为他有一篇文章着眼于将前端控制器实现到他的“1.1 控制器:视图”关系中，这对于那些希望将真正的动态路由解决方案开发到他们的 MVC 系统中的人来说是理想的。

## 干(不要重复自己)&模板

我支持使用 MVC 模式的一个主要理由是，目的是让你的整个系统尽可能的有组织。最显而易见的方法是减少拥有相同代码的多个实例的机会。任何开发人员都会同意，在任何应用程序中最糟糕的事情就是重复相同的代码。保持代码流线型并尽可能使用可重用组件的实践被称为 DRY 哲学——不要重复自己。

DRY 原则规定“每一项知识都必须在系统中有一个单一的、明确的、权威的表示。”DRY 的目标是增加和探索对开发人员开放的每一个可能的途径，使他们的系统尽可能地动态和优化。DRY 原则意味着，如果您需要在许多地方编写相同的代码，而不是在这些地方重复代码，那么创建一个单独的方法，并在需要的地方使用它。这使得系统变得更加优化，并引入了在我们的系统中进行缓存的可能性，以帮助改善整体运行时间。

DRY 的正确实现意味着改变系统的一个元素不会改变不相关的元素，这使得 DRY 成为使用 MVC 模式开发时的一个重要原则。

### 模板

“模板”这个词可能会给那些以前看过 MVC web 框架的人带来一些问题，因为大多数人都把模板部分和视图混为一谈。正如我们之前讨论过的，如果你想坚持传统的 MVC 模式，这是不正确的。理想情况下，从模型中收集数据后，您将拥有处理数据的视图。因此，只有视图组件选择一个模板并将数据从视图传递到该模板才有意义。这样，它就可以使用块代码布局显示，或者使用 echo、print 或任何其他 PHP 输出代码显示。无论您选择使用哪种方法，要记住的主要事情是，您的数据必须处于就绪状态，您只需要在模板中打印数据。如果您在模板中进行其他数据处理或运算，那么您的设置是错误的，最有可能的是，MVC 设置不正确。

下面是一个视图加载模板并将数据推送到模板的快速示例:

```
<?php
class Model
{
    public $tstring;

    public function __construct(){
        $this->tstring = "The string has been loaded through the template.";
        $this->template = "tpl/template.php";
    }
}
```

```
<?php
class View
{
    private $model;

    public function __construct($model) {
        $this->controller = $controller;
        $this->model = $model;
    }

    public function output(){
        $data = "<p>" . $this->model->tstring ."</p>";
        require_once($this->model->template);
    }
}
```

```
<!DOCTYPE html>
<html>
 <head>
  <meta charset="charset=utf-8">
  <title>The Template name</title>
 </head>
 <body>
  <h1><?php echo $data; ?></h1>
 </body>
</html>
```

此外，模板正在通过模型传递，这在原则上允许根据每个特定视图的意图动态分配模板。这种模板化的方法允许 MVC 系统被有效地和自信地扩展，同时给我们从前端开发中分离后端开发的选择；MVC 模式的最初目标。

## 结论

当 MVC 模式首次用于桌面编程时，它是一个游戏改变者。过去是，现在仍然是开发人员之间关于使用它开发时解释某些场景的激烈争论的话题。当您将 PHP 和 web 结合起来时，争论变得更加激烈，这是一个极好的迹象，表明越来越多的开发人员开始关注改进 PHP 的开发规程。

MVC 是个人的最爱，因为它鼓励不同处理部分之间的分离，以及前端和后端分离的机会。我恳求任何以前没有用 MVC 开发过的人，尤其是没有用 PHP 开发过的人，都要沉下心来，投入到这种奇妙的开发模式中去；你不会失望的。

对这篇文章的评论已经关闭。有一个关于 MVC 模式和 PHP 的问题？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章