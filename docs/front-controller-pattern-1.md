# 前端控制器模式介绍，第 1 部分

> 原文：<https://www.sitepoint.com/front-controller-pattern-1/>

有趣的是看到人们是如何以拟人化的方式对几个软件编程概念进行评判的。肥胖的模特都是甜美的，臃肿的控制器是邪恶的，单身者充满了不可逾越的陷阱。但是批评家们并没有就此止步。一些人现在抱怨最近侵入日常 PHP 开发的新概念，说[前端控制器](http://martinfowler.com/eaaCatalog/frontController.html "P of EAA: Front Controller")是多余的“重新发明轮子”,事实上应该被抛弃。句号。

不时指责单身人士，甚至对肥胖控制者有点暴躁，在某种程度上，这是一种相当可预见的健康反应。但是有一个漂亮的前端控制器运行起来有什么问题呢？

有一个反对前控制器的相当充分的论点。在基于 web 的应用程序中，前端控制器的主要作用是将典型的请求/路由/调度/响应周期封装在易于使用的 API 的边界内，这正是 web 服务器所做的。事实上，乍一看，这个过程似乎是多余的，但是当耦合到 MVC 实现时，我们实际上是在试图构建一个控制器，进而控制其他控制器。

尽管角色明显重复，但前端控制器的实现是对现代 web 应用程序开发日益复杂的响应。通过单个入口点将所有请求隧道化无疑是实现基于命令的机制的有效方式，这不仅允许您将命令路由和分派给适当的处理程序，还公开了一个灵活的结构，可以轻松地进行处理和扩展。

坦率地说，前方控制者是容易驯服的生物。在最简单的场景中，我们只需要简单地将 [URL 重写](http://httpd.apache.org/docs/2.0/misc/rewriteguide.html "URL Rewriting Guide - Apache HTTP Server")和一些 switch 语句结合起来，就可以路由和调度请求，尽管在生产中，可能有必要求助于更复杂和更细粒度的实现，特别是如果我们希望通过具有良好分离的职责的更细粒度的对象来提取路由和调度流程。

在这篇由两部分组成的文章中，我将深入探讨两种您可能会觉得有吸引力的简单方法，特别是如果您试图从头实现一个可扩展的前端控制器，而在这个过程中不需要付出过多的努力，也不需要处理臃肿的框架的负担。

## 以简单明了的方式路由和调度

在现实中，有这么多漂亮的选项可以用来构建一个功能性的前端控制器，但我将首先展示我务实的一面(是的，我有一个)。我将介绍的第一个前端控制器实现将负责路由/调度符合以下格式的 URIs:

```
basepath/controllername/actionname/param1/param2/.../paramN
```

如果你曾经接触过使用参数化动作控制器概念的框架，上面的 URI 应该很熟悉。这是一种相当普遍的模式。当然，这里最具挑战性的任务是设计一种灵活的机制，能够毫不费力地解析所讨论的 URIs。这可以通过各种创造性的方式来实现，要么通过简单的过程代码，要么借助面向对象的代码。我将把路由/调度逻辑的具体细节封装在一个类的外壳下:

```
<?php
namespace LibraryController;

interface FrontControllerInterface
{
    public function setController($controller);
    public function setAction($action);
    public function setParams(array $params);
    public function run();
}
```

```
<?php
namespace LibraryController;

class FrontController implements FrontControllerInterface
{
    const DEFAULT_CONTROLLER = "IndexController";
    const DEFAULT_ACTION     = "index";

    protected $controller    = self::DEFAULT_CONTROLLER;
    protected $action        = self::DEFAULT_ACTION;
    protected $params        = array();
    protected $basePath      = "mybasepath/";

    public function __construct(array $options = array()) {
        if (empty($options)) {
           $this->parseUri();
        }
        else {
            if (isset($options["controller"])) {
                $this->setController($options["controller"]);
            }
            if (isset($options["action"])) {
                $this->setAction($options["action"]);     
            }
            if (isset($options["params"])) {
                $this->setParams($options["params"]);
            }
        }
    }

    protected function parseUri() {
        $path = trim(parse_url($_SERVER["REQUEST_URI"], PHP_URL_PATH), "/");
        $path = preg_replace('/[^a-zA-Z0-9]//', "", $path);
        if (strpos($path, $this->basePath) === 0) {
            $path = substr($path, strlen($this->basePath));
        }
        @list($controller, $action, $params) = explode("/", $path, 3);
        if (isset($controller)) {
            $this->setController($controller);
        }
        if (isset($action)) {
            $this->setAction($action);
        }
        if (isset($params)) {
            $this->setParams(explode("/", $params));
        }
    }

    public function setController($controller) {
        $controller = ucfirst(strtolower($controller)) . "Controller";
        if (!class_exists($controller)) {
            throw new InvalidArgumentException(
                "The action controller '$controller' has not been defined.");
        }
        $this->controller = $controller;
        return $this;
    }

    public function setAction($action) {
        $reflector = new ReflectionClass($this->controller);
        if (!$reflector->hasMethod($action)) {
            throw new InvalidArgumentException(
                "The controller action '$action' has been not defined.");
        }
        $this->action = $action;
        return $this;
    }

    public function setParams(array $params) {
        $this->params = $params;
        return $this;
    }

    public function run() {
        call_user_func_array(array(new $this->controller, $this->action), $this->params);
    }
}
```

`FrontController`类的职责归结为解析请求 URI，或者最终通过一些基本的变异器从零开始组装一个全新的。一旦执行了这个任务，`run()`方法就将请求和提供的参数(如果有的话)一起发送给适当的动作控制器。

考虑到它的最小 API，使用这个类是一个简单的两步过程。首先，将一个典型的`.htaccess`文件放入 web 根目录，如下所示:

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.php
```

其次，将下面的代码片段设置为`index.php`:

```
<?php
use LibraryLoaderAutoloader,
    LibraryControllerFrontController;

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$frontController = new FrontController();
$frontController->run();
```

在这种情况下，控制器只是解析请求 URI，并将其提供给给定的动作控制器，顺便说一下，这是它的默认行为。不过，通过调用相应的 setters 来显式提供 URI 是可行的，如下所示:

```
<?php
$frontController = new FrontController(array(
    "controller" => "test", 
    "action"     => "show", 
    "params"     => array(1)
));

$frontController->run();
```

这里的前端控制器具有很强的延展性，很容易配置，既可以在内部解析请求，也可以路由/分派直接由客户端代码提供的定制请求。此外，前面的例子简单地展示了如何调用一个假想的`TestController`类的`show()`方法，并向它传递一个数字参数。当然，你可以随意使用不同的 URIs，并随意发挥操控者的能力。因此，如果你觉得无聊，想免费找点乐子，那就去吧。

尽管我不得不承认我精心制作的`FrontController`类的功能非常有限，但它本身就有一个声明。它证明了构建一个可定制的前端控制器实际上是一个简单的过程，不需要使用晦涩难懂的编程原则就可以成功地完成。

另一方面，坏消息是这个班有很多责任要监督。如果你持怀疑态度，就看看它的`run()`方法。当然，它的实现是干净简洁的，甚至可以用来拦截前/后调度钩子。但它同时做多件事，表现得很像路由和调度的总括点。最好将前端控制器分解成更细粒度的类，其职责被缩小到执行离散任务。

不用说，让这样一个经过提炼的前端控制器按预期启动并运行需要大量的类和接口。我不愿意让这一部分过于冗长，所以我将在下一篇文章中深入讨论整个实现过程的细节。这样你可以有一些时间来扭曲和弯曲我的样品前控制器，使它适合你的味觉。

## 结束语

无论这种方法是使用过程代码还是面向对象的代码，前端控制器从头实现起来都简单得可笑。由于其随和的本质，很容易扩展一个天真、原始的前端控制器，并在它的肩膀上投掷处理后台 RESTful 资源所需的全部东西。

很有可能，编写前端控制器最令人困惑的方面是，在决定请求是必须静态路由还是动态路由并分派给适当的处理程序时，要解决隐含的两难问题。没有正式的原则规定所有的路由/调度逻辑都应该封装在控制器的边界内，或者分解成可以独立重用的独立模块。

确切地说，后者是我将在下一篇文章中讨论的实现形式。所以，敬请期待！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章