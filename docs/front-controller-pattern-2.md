# 前端控制器模式介绍，第 2 部分

> 原文：<https://www.sitepoint.com/front-controller-pattern-2/>

前端控制器就像应用程序中的集中式代理，其主要关注的领域是静态或动态地将命令分派给预定义的处理程序，如页面控制器、REST 资源或任何想到的东西。

构建至少一个简单的前端控制器对于理解它们的本质是一个很有指导意义的经历，为了从实用的角度推广这个想法，我在[介绍文章](https://www.sitepoint.com/front-controller-pattern-1 "An Introduction to the Front Controller Pattern, Part 1")中介绍了一个人造前端控制器的实现，它打包了在单个类的边界内路由和分派请求所需的所有逻辑。

前端控制器最好的一点是，您可以让它们作为紧密的结构运行，只是路由和调度传入的请求，或者您可以让您的另一面显示和实现一个成熟的 RESTful 控制器，它能够解析 HTTP 动词，适应前/后调度挂钩等，所有这些都在一个统一的 API 后面。虽然这种方法很吸引人，但它打破了单一责任原则(SRP ),违背了 OOP 本身的本质，即主动将不同的任务委托给几个细粒度的对象。

那么这是否意味着我只是另一个敢于违背 SRP 戒律的罪恶灵魂？嗯，从某种意义上说我是。因此，我想通过向您展示部署一个小型但可扩展的 HTTP 框架是多么容易来洗刷我的罪过，该框架能够将前端控制器与独立路由器和调度程序一起工作。此外，整个请求/响应周期将由几个可重用的类独立处理，当然您可以随意调整。

随着打包了全功能组件的 HTTP 框架的大量涌现，从头开始实现一个通过几个模块化类路由和调度请求的前端控制器似乎是荒谬的，即使这些类保留了 SRP 的本质。为了避免被认为是重复发明轮子，我的一些定制实现将受到 Lars Strojny 编写的漂亮的 EPHPMVC 库的启发。

## 剖析请求/路线/调度/响应周期

我们应该处理的第一个任务是定义几个负责建模典型 HTTP 请求/响应周期的数据和行为的类。这是第一个，与它实现的接口相关联:

```
class Request {

  public function __construct($uri, $params) { 
    $this->uri = $uri;
    $this->params = $params;
  }

  public function getUri() {
    return $this->uri;
  }

  public function setParam($key, $value) {
    $this->params[$key] = $value;
    return $this;
  }

  public function getParam($key) {
    if (!isset($this->params[$key])) {
      throw new \InvalidArgumentException("The request parameter with key '$key' is invalid."); 
    }
    return $this->params[$key];
  }

  public function getParams() {
    return $this->params;
  }
}
```

Request 类封装了一个传入的 URI 和一组参数，并模拟了一个极其骨架化的 HTTP 请求。为了简洁起见，额外的数据成员，比如与正在讨论的请求相关联的方法集，被有意地留在了图片之外。如果你有心情把他们丢进课堂，那就去做吧。

拥有一个简单的 HTTP 请求包装器当然很好，但是如果不与模拟典型 HTTP 响应的数据和行为的包装器结合起来，最终将毫无用处。让我们修复并构建这个补充组件:

```
class Response {
  public function __construct($version) {
    $this->version = $version;
  }

  public function getVersion() {
    return $this->version;
  }

  public function addHeader($header) {
    $this->headers[] = $header;
    return $this;
  }

  public function addHeaders(array $headers) {
    foreach ($headers as $header) {
      $this->addHeader($header);
    }
    return $this;
  }

  public function getHeaders() {
    return $this->headers;
  }

  public function send() {
    if (!headers_sent()) {
      foreach($this->headers as $header) {
        header("$this->version $header", true);
      }
    } 
  }
}
```

毫无疑问，响应类比它的伙伴请求更主动。它的作用就像一个基本的容器，允许你随意堆叠 HTTP 头，并且也能够将它们发送给客户端。

这些类独立地做着它们的事情，现在是时候处理前端控制器构造的下一部分了。在一个典型的实现中，路由/分派过程大部分时间都封装在同一个方法中，坦率地说，这一点也不差。然而，在这种情况下，最好将正在讨论的过程分解，并将它们委托给不同的类。这样，他们的责任就更加平等了。

下面是启动并运行路由模块的一批类:

```
class Route {

  public function __construct($path, $controllerClass) {
    $this->path = $path;
    $this->controllerClass = $controllerClass;
  }

  public function match(RequestInterface $request) {
    return $this->path === $request->getUri();
  }

  public function createController() {
   return new $this->controllerClass;
  }
}

class Router {
  public function __construct($routes) {
    $this->addRoutes($routes);
  }

  public function addRoute(RouteInterface $route) {
    $this->routes[] = $route;
    return $this;
  }

  public function addRoutes(array $routes) {
    foreach ($routes as $route) {
      $this->addRoute($route);
    }
    return $this;
  }

  public function getRoutes() {
    return $this->routes;
  }

  public function route(RequestInterface $request, ResponseInterface $response) {
    foreach ($this->routes as $route) {
      if ($route->match($request)) {
        return $route;
      }
    }
    $response->addHeader("404 Page Not Found")->send();
    throw new \OutOfRangeException("No route matched the given URI.");
  }
}
```

正如所料，在实现功能路由机制时，有太多的选项可供选择。以上，至少在我看来，揭示了一个务实和简单的解决方案。它定义了一个独立的路由类，将路径与给定的动作控制器联系起来，还定义了一个简单的路由器，其职责仅限于检查存储的路由是否匹配与特定请求对象相关联的 URI。

为了最终解决问题，我们需要设置一个 swift dispatcher，它可以与前面的类一起工作。下面的类正是这样做的:

```
class Dispatcher {

  public function dispatch($route, $request, $response)
    $controller = $route->createController();
    $controller->execute($request, $response);
  }
}
```

浏览 Dispatcher 时，您会注意到两件事。首先，它不携带任何状态。其次，它隐含地假设每个动作控制器将在 execute()方法的表面下运行。

如果您愿意的话，这可以重构为稍微灵活一点的模式(我首先想到的是调整 Route 类的实现)，但是为了简单起见，我将保持 dispatcher 不变。

到现在为止，你可能想知道如何以及在哪里放置一个能够将所有以前的类结合在一起的前端控制器。不要着急，因为那是下一个！

## 实现可定制的前端控制器

我们已经到了从一开始就期待的时刻，实现了期待已久的前端控制器。但是如果你期望实现是某种史诗般的任务，考虑到我们前面放弃的类的数量，恐怕你会失望。事实上，创建控制器可以归结为定义一个类，在一个简单得可笑的 API 后面屏蔽路由器和调度程序的功能:

```
class FrontController {

  public function __construct($router, $dispatcher) {
    $this->router = $router;
    $this->dispatcher = $dispatcher;
  }

  public function run(RequestInterface $request, ResponseInterface $response) {
    $route = $this->router-&gt;route($request, $response);
    $this->dispatcher->dispatch($route, $request, $response);
  }
}
```

FrontController 类所做的只是使用它的 run()方法，通过使用其协作者的幕后功能，将给定的请求路由和分派到相应的动作控制器。如果你愿意的话，这个方法可以更丰富一些，并封装一些额外的实现，比如前/后调度挂钩等等。我会把这个作为家庭作业留给你，以防你想在你的开发者腰带上增加一个新的刻痕。

为了查看前端控制器的设置是否真的像看上去的那样起作用，让我们创建两个实现 execute()方法的普通动作控制器:

在这种情况下，示例动作控制器非常简单，除了向屏幕输出几条无关紧要的消息之外，它们不做任何特别有用的事情。这里的要点是演示如何通过前面的前端控制器调用它们，并传递请求和响应对象，以便进行一些最终的进一步处理。

下面的代码片段简单地展示了如何实现这一点:

```
$request = new Request("http://example.com/test/");

$response = new Response;

$route1 = new Route("http://example.com/test/", "Acme\\Library\\Controller\\TestController");

$route2 = new Route("http://example.com/error/", "Acme\\Library\\Controller\\ErrorController");

$router = new Router(array($route1, $route2));

$dispatcher = new Dispatcher;

$frontController = new FrontController($router, $dispatcher);

$frontController->run($request, $response);
```

尽管这个脚本看起来有些拥挤，因为它首先分解了几个传递到前端控制器内部的路由，但它演示了如何以一种非常简单的方式让事情运转起来并调用动作控制器。此外，在这个例子中，TestController 的一个实例将在运行时被调用，因为它有效地匹配了第一个路由。不用说，路由可以自顶向下定制，因为调用其他动作控制器只是将不同的 URI 传递给请求对象，当然还有匹配的路由。

尽管在实现过程中需要所有的预先设置，但是这种方法的真正优点在于请求/路由/分派/响应周期中涉及的每个类所暴露的模块化。不再需要处理单片前端控制器的奇怪之处，更不用说整个事务中包含的大多数对象，如请求/响应 duet，可以在不同的上下文中轻松重用或由更健壮的实现替换。

## 结束语

虽然前端控制器的学术定义似乎非常严格，因为它将该模式描述为一种集中的、基于命令的机制，仅限于分派请求，但事实是，在现实世界中，可用于实现功能的方法非常少。

在这篇快速综述中，我的目标只是从实用的角度演示如何创建至少两个可定制的前端控制器，首先是一个紧凑、紧密的实现，其中路由和调度流程被打包在一个单一的、包罗万象的类的范围内，其次是一个更细粒度的解决方案，其中所讨论的流程的执行被分解并委托给几个细粒度的类。

除了这里展示的选项之外，还有很多其他选项值得一看，每一个都有自己的优点和缺点。像往常一样，你应该挑选哪一种完全取决于你挑剔的口味。

## 分享这篇文章