# Disco 与设计模式:依赖注入的新观点

> 原文：<https://www.sitepoint.com/disco-with-frameworks-and-design-patterns-a-fresh-look-at-dependency-injection/>

依赖注入是关于代码可重用性的。这是一种设计模式，旨在通过将对象创建/配置与使用分离，使高级代码可重用。

![Illustration of people's outlines dancing in a disco](img/e818cf2ab21c54db48d523a7632d8cff.png)

考虑以下代码:

```
<?php

class Test {

    protected $dbh;

    public function __construct(\PDO $dbh)
    {
        $this->dbh = $dbh;
    }

}

$dbh  = new PDO('mysql:host=localhost;dbname=test', 'username', 'password');
$test = new Test($dbh) 
```

正如你所看到的，我们没有在类内部创建 PDO 对象，而是在类外部创建它，并通过构造函数方法将它作为一个依赖项传入。这样，我们可以使用我们选择的驱动程序，而不是必须使用类中定义的驱动程序。

我们自己的 Alejandro Gervasio 已经[精彩地解释了 DI 概念](https://www.sitepoint.com/inversion-of-control-the-hollywood-principle/)，[杨奇煜·波特尼尔](https://twitter.com/fabpot)也在[系列](http://fabien.potencier.org/what-is-dependency-injection.html)中涉及了它。

但是，这种模式有一个缺点:当依赖关系的数量增加时，在传递给依赖对象之前，需要创建/配置许多对象。我们最终会得到一堆样板代码，以及构造函数方法中的一长串参数。输入依赖注入容器！

依赖注入容器——或简称为阿迪容器——是一个确切知道如何创建服务和处理其依赖关系的对象。

在本文中，我们将通过这个领域的一个新人进一步演示这个概念: [Disco](https://github.com/bitExpert/disco) 。

有关依赖注入容器的更多信息，请参见我们关于主题[的其他帖子。](https://www.sitepoint.com/how-to-build-your-own-dependency-injection-container/)

由于框架是部署 DI 容器的很好的例子，我们将在 Disco 和一些 Symfony 组件的帮助下创建一个基本的基于 HTTP 的框架来结束本文。

## 装置

要安装 Disco，我们像往常一样使用 Composer:

```
composer require bitexpert/disco 
```

为了测试代码，我们将使用 PHP 的[内置 web 服务器](http://php.net/manual/en/features.commandline.webserver.php):

```
php -S localhost:8000 -t web 
```

因此，可以从浏览器的`http://localhost:8000`下访问该应用程序。最后一个参数`-t`选项定义了文档根——`index.php`文件所在的位置。

## 入门指南

Disco 是一个兼容的 DI 容器。有些争议的是，Disco 是一个基于注释的 DI 容器。

*注意包`container_interop`由一组接口组成，用来标准化容器对象的特性。要了解这是如何工作的，请看[教程](https://www.sitepoint.com/how-to-build-your-own-dependency-injection-container/)，其中我们构建了自己的 SitePoint 依赖注入容器，也是基于容器互操作的。*

为了向容器添加服务，我们需要创建一个**配置类**。这个类应该用`@Configuration`标注:

```
<?php
/**
 * @Configuration
 */
 class Services {
    // ...
 } 
```

每个容器服务应该被定义为配置类中的一个**公共**或**保护的**方法。Disco 将每个服务称为一个 **Bean** ，这源于 Java 文化。

在每个方法中，我们**定义了**应该如何创建服务。每个方法**必须**用`@Bean`标记，暗示这是一个服务，还有`@return`注释，说明返回对象的类型。

这是一个带有一个“Bean”的 Disco 配置类的简单示例:

```
<?php
/**
 * @Configuration
 */
class Configuration {

    /**
     * @Bean
     * @return SampleService
     */
    public function getSampleService()
    {
        // Instantiation
        $service  = new SampleService();

        // Configuration
        $service->setParameter('key', 'value');
        return $service; 
    }
} 
```

`@Bean`注释接受一些配置参数来指定服务的**性质**。它是否应该是一个**单独的**对象、**惰性加载的**(如果对象是资源饥渴的)，或者甚至是它的状态**在会话的生命周期**期间保持，都由这些参数指定。

默认情况下，所有服务都被定义为**单例**服务。

例如，下面的 Bean 创建了一个单独的延迟加载服务:

```
<?php

// ...

/**
 * @Bean({"singleton"=true, "lazy"=true})
 * @return \Acme\SampleService
 */
 public function getSampleService()
 {
     return new SampleService();
 }

// ... 
```

Disco 使用[代理管理器](https://github.com/Ocramius/ProxyManager)来进行服务的延迟加载。它还使用它将附加行为(由注释定义)注入到配置类的方法中。

在我们创建配置类之后，我们需要创建一个`AnnotationBeanFactory`的实例，将配置类传递给它。**这将是我们的容器。**

最后，我们用`BeanFactoryRegistry`注册容器:

```
<?php

// ...

use \bitExpert\Disco\AnnotationBeanFactory;
use \bitExpert\Disco\BeanFactoryRegistry;

// ...

// Setting up the container
$container = new AnnotationBeanFactory(Services::class, $config);
BeanFactoryRegistry::register($container); 
```

### 如何从容器中获取服务

由于 Disco 与`container/interop`兼容，我们可以在容器对象上使用`get()`和`has()`方法:

```
// ...

$sampleService = $container->get('sampleService');
$sampleService->callSomeMethod(); 
```

## 服务范围

HTTP 是一种无状态协议，这意味着在每次请求时，整个应用程序都被引导，所有对象都被重新创建。然而，我们可以通过向`@Bean`注释传递适当的参数来影响服务的生命周期。其中一个参数是`scope`。范围可以是`request`或`session`。

如果作用域是`session`，服务状态将在会话生存期内保持不变。换句话说，在后续的 HTTP 请求中，将从会话中检索对象的最后状态。

让我们用一个例子来阐明这一点。考虑下面的类:

```
<?php

class sample {

    public $counter = 0;

    public function add()
    {
        $this->counter++;
        return $this;
    } 
} 
```

在上面的类中，`$counter`的值在每次调用`add()`方法时递增；现在，让我们将它添加到容器中，范围设置为`session`:

```
// ...
/**
 * @Bean({"scope"="session"})
 * @return Sample
 */
public function getSample()
{
    return new Sample();
}
// ... 
```

如果我们这样使用它:

```
// ...
$sample = $container->get('getSample');
$sample->add()
       ->add()
       ->add();

echo $sample->counter; // output: 3
// ... 
```

在第一次运行中，输出将是 3。如果我们再次运行这个脚本(发出另一个**请求**，这个值将是 6(而不是 3)。这就是对象状态跨请求保持的方式。

如果范围设置为`request`，那么在后续的 HTTP 请求中，该值将始终为 3。

## 容器参数

容器通常接受来自外界的参数。使用 Disco，我们可以将参数作为关联数组传递到容器中，如下所示:

```
// ...
$parameters = [

    // Database configuration
    'database' => [        
        'dbms' => 'mysql',
        'host' => 'localhost',
        'user' => 'username',
        'pass' => 'password',
    ],
];

// Setting up the container
$container = new AnnotationBeanFactory(Services::class, $parameters);
BeanFactoryRegistry::register($container); 
```

为了在配置类的每个方法中使用这些值，我们使用了`@Parameters`和`@parameter`注释:

```
<?php
// ...

/**
 * @Bean
 * @Parameters({
 *    @parameter({"name"= "database"})
 * })
 *
*/
public function sampleService($database = null)
{
    // ...
} 
```

## 迪斯科在行动

在这一节中，我们将创建一个基本的基于 HTTP 的框架。框架将基于从**请求**接收的信息创建**响应**。

为了构建我们框架的核心，我们将使用一些 [Symfony 组件](http://symfony.com/components)。

**HTTP 内核**

我们框架的核心。提供请求/响应基础知识。

**Http 基金会**

围绕 PHP 的 HTTP 超级全局变量的一个很好的面向对象的层。

**路由器**

根据官方网站:将一个 HTTP 请求映射到一组配置变量——下面会有更多的介绍。

**事件调度器**

这个库提供了一种方法，使用侦听器和订阅者来挂钩请求/响应生命周期的不同阶段。

要安装所有这些组件:

```
composer require symfony/http-foundation symfony/routing symfony/http-kernel symfony/event-dispatcher 
```

按照惯例，我们将把框架的代码放在`Framework`名称空间下。

让我们也注册一个 [PSR-4 自动装载机](https://www.sitepoint.com/battle-autoloaders-psr-0-vs-psr-4/)。为此，我们在`composer.json`中的`psr-4`键下添加以下名称空间到路径的映射:

```
// ...
 "autoload": {
        "psr-4": {
            "": "src/"
        }
    }
// ... 
```

因此，将在`src/`目录中查找所有名称空间。现在，我们运行`composer dump-autoload`让这个更改生效。

在本文的其余部分，我们将编写框架代码和代码片段，以使一些概念变得清晰。

### 内核

任何框架的基础都是它的内核。这是一个**请求**被处理成一个**响应**的地方。

我们不打算在这里从头开始创建一个内核。相反，我们将扩展刚刚安装的 **HttpKernel** 组件的`Kernel`类。

```
<?php
// src/Framework/Kernel.php

namespace Framework;

use Symfony\Component\HttpKernel\HttpKernel;
use Symfony\Component\HttpKernel\HttpKernelInterface;

class Kernel extends HttpKernel implements HttpKernelInterface {
} 
```

因为基本实现对我们来说很好，所以我们不会重新实现任何方法，而是依赖于继承的实现。

## 按指定路线发送

一个`Route`对象包含一个**路径**和一个**回调**，每次匹配路由时(由[控制器解析器](http://api.symfony.com/2.7/Symfony/Component/HttpKernel/Controller/ControllerResolver.html)调用)(由 [URL 匹配器](http://api.symfony.com/3.1/Symfony/Component/Routing/Matcher/UrlMatcher.html))。

URL matcher 是一个类，它接受一组路由(我们稍后将讨论)和一个用于查找活动路由的实例 [RequestContext](http://api.symfony.com/3.1/Symfony/Component/Routing/RequestContext.html) 。

请求上下文对象包含关于当前请求的信息。

下面是如何使用**路由**组件定义路由的:

```
<?php

// ...

use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;

$routes = new RouteCollection();

$routes->add('route_alias', new Route('path/to/match', ['_controller' => function(){
    // Do something here...
}]
)); 
```

要创建路线，我们需要创建一个`RouteCollection`的实例(它也是`Routing`组件的一部分)，然后**将**我们的路线添加到其中。

为了使路由语法更有表现力，我们将围绕`RouteCollection`创建一个路由构建器类。

```
<?php
// src/Framework/RouteBuilder.php

namespace Framework;

use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;

class RouteBuilder {

    protected $routes;

    public function __construct(RouteCollection $routes)
    {
        $this->routes = $routes;
    }

    public function get($name, $path, $controller)
    {
        return $this->add($name, $path, $controller, 'GET');
    }

    public function post($name, $path, $controller)
    {
        return $this->add($name, $path, $controller, 'POST');
    }

    public function put($name, $path, $controller)
    {
        return $this->add($name, $path, $controller, 'PUT');
    }

    public function delete($name, $path, $controller)
    {
        return $this->add($name, $path, $controller, 'DELETE');
    }

    protected function add($name, $path, $controller, $method)
    {
        $this->routes->add($name, new Route($path, ['_controller' => $controller], ['_method' => $method]));

        return $this;
    }

} 
```

这个类保存了一个`RouteCollection`的实例。在`RouteBuilder`中，对于每个 HTTP 动词，都有一个调用`add()`的方法。我们将把路线定义保存在`src/routes.php`文件中:

```
<?php
// src/routes.php

use Symfony\Component\Routing\RouteCollection;
use Framework\RouteBuilder;

$routeBuilder = new RouteBuilder(new RouteCollection());

$routeBuilder

->get('home', '/', function() {
            return new Response('It Works!');
})

->get('welcome', '/welcome', function() {
            return new Response('Welcome!');
}); 
```

## 前端控制器

任何现代 web 应用程序的入口点都是它的前端控制器。它是一个 PHP 文件，通常命名为`index.php`。这是包含类自动加载器的地方，应用程序是引导的。

所有的请求都经过这个文件，并从这里被分派到适当的控制器。因为这是我们将向公众公开的唯一文件，所以我们将它放在 web 根目录中，将其余的代码放在外面。

```
<?php
//web/index.php

require_once __DIR__ . '/../vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\RequestStack;
use Symfony\Component\HttpKernel\EventListener\RouterListener;
use Symfony\Component\HttpKernel\Controller\ControllerResolver;

// Create a request object from PHP's global variables
$request = Request::createFromGlobals();

$routes = include __DIR__.'/../src/routes.php';
$UrlMatcher = new Routing\Matcher\UrlMatcher($routes, new Routing\RequestContext());

// Event dispatcher & subscribers
$dispatcher = new EventDispatcher();
// Add a subscriber for matching the correct route. We pass UrlMatcher to this class
$dispatcher->addSubscriber(new RouterListener($UrlMatcher, new RequestStack()));

$kernel = new Framework\Kernel($dispatcher, new ControllerResolver());
$response  = $kernel->handle($request);

// Sending the response
$response->send(); 
```

在上面的代码中，我们基于 PHP 的全局变量实例化了一个`Request`对象。

```
<?php
// ...
$request = Request::createFromGlobals();
// ... 
```

接下来，我们将`routes.php`文件加载到`$routes`中。检测正确的路线是`UrlMatcher`类的责任，所以我们创建它，将路线集合和一个`RequestContext`对象一起传递。

```
<?php
// ...
$routes = include __DIR__.'/../src/routes.php';
$UrlMatcher = new Routing\Matcher\UrlMatcher($routes, new Routing\RequestContext());
// ... 
```

为了使用`UrlMatcher`实例，我们将它传递给`RouteListener`事件订阅者。

```
<?php
// ...
// Event dispatcher & subscribers
$dispatcher = new EventDispatcher();
// Add a subscriber for matching the correct route. We pass UrlMatcher to this class
$dispatcher->addSubscriber(new RouterListener($UrlMatcher, new RequestStack()));
// ... 
```

每当一个请求到达应用程序时，事件被触发，相应的监听器被调用，监听器通过使用传递给它的`UrlMatcher`来检测正确的路由。

最后，我们实例化内核，通过其构造函数传入**调度器**和**控制器解析器**的一个实例:

```
<?php
// ...

$kernel    = new Framework\Kernel($dispatcher, new ControllerResolver());
$response  = $kernel->handle($request);

// Sending the response
$response->send();
// ... 
```

### 迪斯科时间

到目前为止，我们必须在前端控制器中进行大量的实例化(和配置),包括创建请求上下文对象、URL 匹配器、事件调度器及其订户，当然还有内核本身。

现在是时候让 Disco 为我们将所有这些片段连接起来了。

和以前一样，我们使用 Composer 安装它:

```
composer require bitexpert/Disco; 
```

然后，我们创建配置类，并定义前端控制器中需要的服务:

```
<?php
// src/Framework/Services.php

use bitExpert\Disco\Annotations\Bean;
use bitExpert\Disco\Annotations\Configuration;
use bitExpert\Disco\Annotations\Parameters;
use bitExpert\Disco\Annotations\Parameter;

/**
 * @Configuration
 */
class Services {

    /**
     * @Bean
     * @return \Symfony\Component\Routing\RequestContext 
     */
    public function context()
    {
        return new \Symfony\Component\Routing\RequestContext();
    }

    /**
     * @Bean
     *
     * @return \Symfony\Component\Routing\Matcher\UrlMatcher
     */
    public function matcher()
    {
        return new \Symfony\Component\Routing\Matcher\UrlMatcher($this->routeCollection(), $this->context());
    }

    /**
     * @Bean
     * @return \Symfony\Component\HttpFoundation\RequestStack
     */
    public function requestStack()
    {
        return new \Symfony\Component\HttpFoundation\RequestStack();
    }

    /**
     * @Bean
     * @return \Symfony\Component\Routing\RouteCollection
     */
    public function routeCollection()
    {
        return new \Symfony\Component\Routing\RouteCollection();
    }

    /**
     * @Bean
     * @return \Framework\RouteBuilder
     */
    public function routeBuilder()
    {
        return new \Framework\RouteBuilder($this->routeCollection());
    }

    /**
     * @Bean
     * @return \Symfony\Component\HttpKernel\Controller\ControllerResolver
     */
    public function resolver()
    {
        return new \Symfony\Component\HttpKernel\Controller\ControllerResolver();
    }

    /**
     * @Bean
     * @return \Symfony\Component\HttpKernel\EventListener\RouterListener
     */
    protected function listenerRouter()
    {
        return new \Symfony\Component\HttpKernel\EventListener\RouterListener(
            $this->matcher(),
            $this->requestStack()
        );
    }

    /**
     * @Bean
     * @return \Symfony\Component\EventDispatcher\EventDispatcher
     */
    public function dispatcher()
    {
        $dispatcher = new \Symfony\Component\EventDispatcher\EventDispatcher();

        $dispatcher->addSubscriber($this->listenerRouter());

        return $dispatcher;
    }

    /**
     * @Bean
     * @return Kernel
     */
    public function framework()
    {
        return new Kernel($this->dispatcher(), $this->resolver());
    }

} 
```

看起来有很多代码；但事实上，它与之前驻留在前端控制器中的代码是相同的。

在使用这个类之前，我们需要确保它已经被自动加载，方法是将它添加到我们的`composer.json`文件中的`files`键下:

```
// ...
 "autoload": {
        "psr-4": {
            "": "src/"
        },
        "files": [
            "src/Services.php"
        ]
    }
// ... 
```

现在来看看我们的前端控制器。

```
<?php

//web/index.php

require_once __DIR__ . '/../vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;

$request   = Request::createFromGlobals();

$container = new \bitExpert\Disco\AnnotationBeanFactory(Services::class);
\bitExpert\Disco\BeanFactoryRegistry::register($container);

$routes   = include __DIR__.'/../src/routes.php';

$kernel   = $container->get('framework')
$response = $kernel->handle($request);
$response->send(); 
```

现在我们的前控制器可以呼吸了！当我们请求服务时，所有的实例化都是由 Disco 完成的。

## 但是配置怎么样呢？

如前所述，我们可以将参数作为关联数组传递给`AnnotationBeanFactory`类。

为了在我们的框架中管理配置，我们创建了两个配置文件，一个用于**开发**，另一个用于**生产**环境。

每个文件返回一个关联数组，我们可以将它加载到一个变量中。

让我们将它们保存在`Config`目录中:

```
// Config/dev.php

return [
    'debug' => true;
]; 
```

对于生产:

```
// Config/prod.php

return [
    'debug' => false;
]; 
```

为了检测环境，我们将在一个特殊的纯文本文件中指定环境，就像我们定义一个环境变量一样:

```
ENV=dev 
```

为了解析文件，我们使用了 **PHP dotenv** ，这是一个将环境变量从一个文件(默认文件名是`.env`)加载到 PHP 的`$_ENV`超级全局变量中的包。这意味着我们可以通过使用 PHP 的 [getenv()](http://php.net/manual/en/function.getenv.php) 函数来获取这些值。

要安装软件包，请执行以下操作:

```
composer require vlucas/phpdotenv 
```

接下来，我们在`Config/`目录中创建我们的`.env`文件。

**配置/。环境**

```
ENV=dev 
```

在前端控制器中，我们使用 PHP dotenv 加载环境变量:

```
<?php
//web/index.php

// ...

// Loading environment variables stored .env into $_ENV
$dotenv = new Dotenv\Dotenv(__DIR__ . '/../Config');
$dotenv->load();

// Load the proper configuration file based on the environment
$parameters = require __DIR__ . '/../config/' . getenv('ENV') . '.php';

$container = new \bitExpert\Disco\AnnotationBeanFactory(Services::class, $parameters);       \bitExpert\Disco\BeanFactoryRegistry::register($container);

// ... 
```

在前面的代码中，我们首先指定了我们的`.env`文件所在的目录，然后我们调用`load()`将环境变量加载到`$_ENV`中。最后，我们使用`getenv()`来获得正确的配置文件名。

## 创建容器生成器

当前状态下的代码仍然有一个问题:每当我们想要创建一个新的应用程序时，我们必须在前端控制器(`index.php`)中实例化`AnnotationBeanFactory`。作为一个解决方案，我们可以创建一个工厂，在任何需要的时候创建容器。

```
<?php
// src/Factory.php

namespace Framework;

class Factory {

    /**
     * Create an instance of Disco container
     *
     * @param  array $parameters
     * @return \bitExpert\Disco\AnnotationBeanFactory
     */
    public static function buildContainer($parameters = [])
    {
        $container = new \bitExpert\Disco\AnnotationBeanFactory(Services::class, $parameters);
        \bitExpert\Disco\BeanFactoryRegistry::register($container);

        return $container;
    }

} 
```

这个工厂有一个名为`buildContainer()`的静态方法，它创建并注册了一个 Disco 容器。

这就是它如何改进我们的前端控制器:

```
<?php
//web/index.php

require_once __DIR__ . '/../vendor/autoload.php';

use Symfony\Component\HttpFoundation\Request;

// Getting the environment
$dotenv = new Dotenv\Dotenv(__DIR__ . '/../config');
$dotenv->load();

// Load the proper configuration file based on the environment
$parameters = require __DIR__ . '/../config/' . getenv('ENV') . '.php';

$request   = Request::createFromGlobals();
$container = Framework\Factory::buildContainer($parameters);
$routes    = include __DIR__.'/../src/routes.php';

$kernel   = $container->get('framework')
$response = $kernel->handle($request);
$response->send(); 
```

现在看起来整洁多了，不是吗？

### 应用程序类别

我们可以在可用性方面更进一步，将剩余的操作(在前端控制器中)抽象到另一个类中。让我们称这个类为`Application`:

```
<?php

namespace Framework;

use Symfony\Component\HttpKernel\HttpKernelInterface;
use Symfony\Component\HttpFoundation\Request;

class Application {

    protected $kernel;

    public function __construct(HttpKernelInterface $kernel)
    {
        $this->kernel = $kernel;
    }

    public function run()
    {
        $request = Request::createFromGlobals();

        $response = $this->kernel->handle($request);
        $response->send();
    }
} 
```

依赖于内核，并作为它的包装器。我们创建一个名为`run()`的方法，它填充请求对象，并将其传递给内核以获得响应。

为了让它更酷，让我们也将这个类添加到容器中:

```
<?php

// src/Framework/Services.php

// ...

    /**
     * @Bean
     * @return \Framework\Application
     */
    public function application()
    {
        return new \Framework\Application($this->kernel());
    }

// ... 
```

这是我们前端控制器的新外观:

```
<?php

require_once __DIR__ . '/../vendor/autoload.php';

// Getting the environment
$dotenv = new Dotenv\Dotenv(__DIR__ . '/../config');
$dotenv->load();

// Load the proper configuration file based on the environment
$parameters = require __DIR__ . '/../config/' . getenv('ENV') . '.php';

// Build a Disco container using the Factory class
$container = Framework\Factory::buildContainer($parameters);

// Including the routes
require __DIR__ . '/../src/routes.php';

// Running the application to handle the response
$app = $container->get('application')
          ->run(); 
```

## 创建响应监听器

我们现在可以使用该框架，但仍有改进的空间。目前，我们必须在每个控制器中返回一个`Response`的**实例**，否则内核会抛出一个异常:

```
<?php

// ...

$routeBuilder

->get('home', '/', function() {
            return new Response('It Works!');
});

->get('welcome', '/welcome', function() {
            return new Response('Welcome!');
});

// ... 
```

然而，我们可以让它可选，并允许发送回纯字符串。为此，我们创建了一个特殊的 subscriber 类，如果返回值是一个字符串，它会自动创建一个`Response`对象。

订户必须实现`Symfony\Component\EventDispatcher\EventSubscriberInterface`接口。他们应该实现`getSubscribedMethods()`方法，在该方法中，我们定义我们感兴趣的订阅事件，以及它们的事件侦听器。

在我们的例子中，我们对`KernelEvents::VIEW`事件感兴趣。当要返回响应时，事件发生。

这是我们的订户类别:

```
<?php
// src/Framework/StringResponseListener
namespace Framework;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpKernel\Event\GetResponseForControllerResultEvent;
use Symfony\Component\HttpKernel\KernelEvents;

class StringResponseListener implements EventSubscriberInterface
{
    public function onView(GetResponseForControllerResultEvent $event)
    {
        $response = $event->getControllerResult();

        if (is_string($response)) {
            $event->setResponse(new Response($response));
        }
    }

    public static function getSubscribedEvents()
    {
        return array(KernelEvents::VIEW => 'onView');
    }
} 
```

在监听器方法`onView`中，我们首先检查响应是否是一个字符串(并且还不是一个`Response`对象)，然后在需要时创建一个响应对象。

要使用订户，我们需要将其作为受保护的服务添加到容器中:

```
<?php

// ...

/**
 * @Bean
 * @return \Framework\StringResponseListener
 */
protected function ListenerStringResponse()
{
    return new \Framework\StringResponseListener();
}

// ... 
```

然后，我们将它添加到 dispatcher 服务:

```
<?php

// ...

/**
 * @Bean
 * @return \Symfony\Component\EventDispatcher\EventDispatcher
 */
public function dispatcher()
{
    $dispatcher = new \Symfony\Component\EventDispatcher\EventDispatcher();

    $dispatcher->addSubscriber($this->listenerRouter());
    $dispatcher->addSubscriber($this->ListenerStringResponse());

    return $dispatcher;
}

// ... 
```

从现在开始，我们可以简单地在控制器函数中返回一个字符串:

```
<?php

// ...

$routeBuilder

->get('home', '/', function() {
            return 'It Works!';
})

->get('welcome', '/welcome', function() {
            return 'Welcome!';
});

// ... 
```

框架现在已经准备好了。

## 结论

我们在 Symfony 组件和 Disco 的帮助下创建了一个基本的基于 HTTP 的框架。这只是一个基本的请求/响应框架，缺少任何其他 MVC 概念，如模型和视图，但允许实现我们可能需要的任何其他架构模式。

Github 上的[提供了完整的代码。](https://github.com/sitepoint-editors/disco-framework)

Disco 是 DI-container 游戏的新成员，如果与老的游戏相比，它缺乏全面的文档。本文试图为那些对这种新型 DI 容器感兴趣的人提供一个平稳的开端。

你用 DI 容器把你的应用组件粘在一起了吗？如果有，是哪些？你试过迪斯科吗？让我们知道！

## 分享这篇文章