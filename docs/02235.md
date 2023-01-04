# 使用 ProxyManager 的 RESTful 远程对象代理

> 原文：<https://www.sitepoint.com/restful-remote-object-proxies-with-proxymanager/>

这篇文章由[德姬·阿卡拉](https://www.sitepoint.com/author/dakala/)和[马可·皮韦塔](https://twitter.com/ocramius)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

*代理模式*是[软件开发中另一个](https://www.sitepoint.com/book-review-practical-design-patterns-php/)酷的设计模式。代理是作为另一个类或 web 服务的*接口*的类。为了简单起见，在本文的其余部分，我们将代理类称为*主题*。代理通常实现与主题相同的接口，所以看起来我们是直接在主题上调用方法。

![](img/752c124be7987b8ff86c9fab5e71e50c.png)

虽然本文不是关于代理模式的概念，但是我们将讨论一些基础知识，作为开始。

有多种用于不同目的的代理类型:

1.  **虚拟代理**–用于延迟加载资源饥渴的对象，防止它们在需要之前占用内存。

2.  **保护代理**–用于通过一组规则限制对对象的属性或方法的访问。

3.  **智能引用**——用于在主题上调用方法时添加额外的行为——适合面向方面的编程。

4.  **远程对象**–用于访问远程对象，隐藏它们实际上位于单独的地址空间的事实。本文将主要讨论远程对象代理。

让我们从一个**虚拟代理**的基本例子开始:

```
class MyObjectProxy
{
    protected $subject;

    public function someMethod()
    {
        $this->init();

        return $this->subject->someMethod();
    }

    private function init()
    {
        if (null === $this->subject) {
            $this->subject = new Subject();
        }
    }
} 
```

在上面的代码中，我们保留了对主题的引用；每次对代理调用一个方法时，`init()`被调用，检查主体是否已经被实例化，如果没有，它将实例化它。

最后，关于该主题的相应方法被称为:

```
// ...
return $this->subject->someMethod();
// ... 
```

这是一个非常基本的*值持有者虚拟代理*的例子，它始终保持对主题的引用。虽然前面的例子工作得很好，但这不是我们在这个世界中创建代理的方式。相反，我们将做任何其他优秀开发人员都会做的事情:使用一个众所周知的、经过良好测试的第三方库。

ProxyManager 是一个 PHP 库，用于通过一组工厂类创建各种代理。

[Proxy manager 的作者 Marco Pivetta](https://twitter.com/Ocramius) 有一个全面的[演示](http://ocramius.github.io/presentations/proxy-pattern-in-php/#/)，这是一个简单易懂的代理模式介绍。我还建议在开始之前快速浏览一下 ProxyManager 的[官方文档](http://ocramius.github.io/ProxyManager/)。

## 切入正题

这篇文章致力于讨论较少的代理类型之一，称为*远程对象代理*。我们将了解它们是什么以及它们是如何工作的。然后，我们将继续创建一个远程对象代理，它能够与一个 *RESTful API* 交互。

简单地说，远程对象代理用于与远程主体进行交互(使用 HTTP 作为传输机制)，并将它们伪装成本地对象！

每当针对代理对象调用一个方法时，它都会尝试将被调用的方法名映射到它的**远程对应物**。然后，它将请求编码并转发给远程方法。所有这一切都发生在几毫秒之内。

我们通过使用 ProxyManager 的`RemoteObjectFactory`类来创建我们的远程对象代理。这个工厂需要一个*适配器*来创建一个远程对象代理，能够与某个远程主体交互。另一方面，适配器负责以远程主体可以处理的方式转换请求。

适配器根据远程主体实现的协议而变化；无论主题是基于 XML-RPC、基于 SOAP 还是 RESTful JSON API，都应该使用合适的适配器。

接下来的代码将使用 ProxyManager 的`RemoteObjectFactory`。

目前，ProxyManager 提供了三种现成的适配器:`XmlRpc`、`JsonRpc`和`Soap`。这些适配器使用`Zend\Server\Client`接口的实现作为传输机制。

要查看如何做到这一点的示例，[官方文档](https://ocramius.github.io/ProxyManager/docs/remote-object.html)是最佳位置。

在下一节中，我们将创建目前缺少的东西:一个适合 RESTful APIs 的定制适配器！

## 准备

首先，我们将创建一个伪 JSON API 作为我们的远程主题——使用 Silex 作为我们的框架。然后，我们将安装 ProxyManager 来为它创建一个代理。

由于 ProxyManager 没有为 RESTful APIs 提供适配器，我们需要创建自己的适配器。我们的定制适配器将使用 [Guzzle](https://github.com/guzzle/guzzle) 作为它的 HTTP 客户端。

让我们首先创建一个目录来保存我们的文件:

```
mkdir rest-proxy && cd rest-proxy 
```

现在，我们安装 Silex、ProxyManager 和 Guzzle(暂时):

```
composer require silex/silex ocramius/proxy-manager guzzlehttp/guzzle 
```

## 目录结构

我们将在同一个项目中实现测试 API 和代理相关的代码，假设一个是本地的，另一个是远程的。为了可读性，我们将这两个组件放在不同的名称空间下。

也就是说，我们需要更改我们的`composer.json`文件的`autoload`键:

**文件名:** `composer.json`

```
{
    ...

    "autoload": {
        "psr-4": {
            "": "src/"
        }
    }
} 
```

然后我们跑`composer dump-autoload`。

因此，我们可以有两个不同的名称空间，映射到`src/`目录中的两个不同目录。让我们分别命名为`RemoteProxy`和`API`。此外，我们在项目的根目录下创建另一个名为`web`的目录来保存我们的`index.php`文件。目录结构现在应该是这样的:

```
remote-proxy
├── src
│   ├── Api
│   └── RemoteProxy
└── web 
```

## 创建 API

该 API 将有三个端点，用于返回图书列表、图书细节和图书作者。

为此，我们在`src/Api/Controller`下创建一个 Silex 控制器提供者，名为`BookControllerProvider.php`:

要了解 Silex 控制器提供者是如何工作的，你可能想看一看[这一页](http://silex.sensiolabs.org/doc/master/providers.html#controller-providers)。

**文件名:** `src/Api/Controller/ApiControllerProvider.php`

```
<?php

namespace Api\Controller;

use Silex\Application;
use Silex\Api\ControllerProviderInterface;
use Symfony\Component\HttpFoundation\JsonResponse as Json;

class ApiControllerProvider implements ControllerProviderInterface
{

    public function connect(Application $app)
    {
        $controllers = $app['controllers_factory'];

        /**
         * Return a list of the books
         */
        $controllers->get('books', function (Application $app) {

            return new Json([
                'books' => 'List of books...'
            ]);
        });

        /**
         * Return book details by Id
         */
        $controllers->get('books/{id}', function (Application $app, $id) {

            return new Json([
                'details' => 'Details of book with id ' . $id,
            ]);
        });

        /**
         * Return the author(s) of the book
         */
        $controllers->get('books/{id}/authors', function (Application $app, $id) {

            return new Json([
                'authors' => 'Authors of book with id ' . $id,
            ]);
        });

        return $controllers;
    }
} 
```

我们有三条带有控制器的路线，每条路线都返回一个带有虚拟消息作为内容的`JsonResponse`实例。

## 创建应用程序

接下来，我们创建我们的`src/app.php`文件，在这里我们创建应用程序并挂载我们的控制器提供程序:

**文件名:** `src/app.php`

```
<?php

use Silex\Application;
use Api\Controller\ApiControllerProvider;

$app = new Application();
$app->mount('/api/v1', new ApiControllerProvider());

return $app; 
```

## 前端控制器

最后，我们创建前端控制器，这将是我们的 API 的入口点。我们将该文件保存为`web/index.php`,内容如下:

**文件名:** `web/index.php`

```
<?php
require_once __DIR__.'/../vendor/autoload.php'; 
$app = require __DIR__ . '/../src/app.php';
$app->run(); 
```

在上面的代码中，我们包含了 composer autoloader 和我们的`app.php`文件(它返回一个 Silex 应用程序对象)。最后，我们通过调用`run()`方法来运行应用程序。

要测试代码，请参考您的环境的服务器设置(如果使用类似于 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 的东西，则为自动设置),或者为了简洁起见，使用 PHP 的内置 web 服务器:

```
php -S localhost:9000 -t web 
```

作为后一种选择的结果，API 将可以从浏览器的`http://localhost:9000/api/v1`下访问。

## 创建代理

现在我们已经在“远程”端设置好了一切，是时候创建一些代理了！

### 代理适配器

ProxyManager 中的适配器扩展了抽象类`BaseAdapter`，它实现了`AdapterInterface`。

所有适配器都接受一个 HTTP 客户端对象(通过它们的构造函数)来与远程主体交互，以及一个包含*方法到端点*映射信息的*数组*。通过使用这个数组，适配器将知道应该将请求转发到哪个远程端点。

在我们的例子中，数组如下所示:

```
<?php
// ...

$array = [
    'getBooks'   => ['path' => 'books',             'method' => 'get'],
    'getBook'    => ['path' => 'books/:id',         'method' => 'get'],
    'getAuthors' => ['path' => 'books/:id/authors', 'method' => 'get'],
];

// ... 
```

每个键是一个方法名(在代理对象上),值(是一个数组)有远程*路径*和*方法*来访问它。我们将在后面看到如何使用这个数组。

为了创建我们的适配器，与其他适配器不同，我们不会扩展`BaseAdapter`抽象类。相反，我们将从头开始创建它。为什么要这么做？因为抽象类迫使我们使用`Zend\Server\Client`的实现作为 HTTP 客户端，而我们想要使用 Guzzle。

适配器(实现`AdapterInterface`)必须实现`call()`，每当调用一个方法时，代理就会调用它。这个方法有三个参数:代理类本身、被调用方法的名称和被调用方法的参数。

下面是我们的 RESTful 适配器的代码:

**文件名:** `src/RemoteProxy/Adapter/RestAdapter.php`

```
<?php

namespace RemoteProxy\Adapter;

use GuzzleHttp\ClientInterface;
use ProxyManager\Factory\RemoteObject\AdapterInterface;

class RestAdapter implements AdapterInterface
{

    /**
     * Adapter client
     *
     * @var GuzzleHttp\ClientInterface
     */
    protected $client;

    /**
     * Mapping information
     *
     * @var array
     */
    protected $map;

    /**
     * Constructor
     *
     * @param Client $client
     * @param array  $map    Map of service names to their aliases
     */
    public function __construct(ClientInterface $client, $map = [])
    {
        $this->client  = $client;
        $this->map     = $map;
    }

    /**
     * {@inheritDoc}
     */
    public function call($wrappedClass, $method, array $parameters = [])
    {
        if (!isset($this->map[$method])) {
            throw new \RuntimeException('No endpoint has been mapped to this method.');
        }

        $endpoint =  $this->map[$method];
        $path     = $this->compilePath($endpoint['path'], $parameters);

        $response = $this->client->request($endpoint['method'], $path);

        return (string) $response->getBody();
    }

    /**
     * Compile URL with its values
     *
     * @param string $path
     * @param array $parameters
     *
     * @return string
     */
    protected function compilePath($path, $parameters)
    {
        return preg_replace_callback('|:\w+|', function ($matches) use (&$parameters) {
            return array_shift($parameters);
        }, $path);
    }
} 
```

在前面的代码中，首先，我们在类构造函数中设置属性`$map`和`$client`。在`call()`方法中，我们检查被调用的方法名是否存在于`$map`属性中；如果是，我们调用`compilePath()`，将被调用方法的参数放入 URL 中它们各自的占位符中。

例如，如果我们调用`getBook(12)`，URL 可能类似于`http://localhost:9000/api/v1/book/12`。

最后，我们根据*指定的 HTTP 动词*发送一个请求——使用我们的 Guzzle 客户端的`request()`方法:

```
// ...
$response = $this->client->request($endpoint['method'], $path);
// ... 
```

## 创建代理对象

现在，任何时候我们需要创建一个远程对象代理，我们创建一个`ProxyManager\Factory\RemoteObjectFactory`的实例，传递给它一个我们的**自定义适配器**和**映射数组**的实例。

这个过程应该在我们需要使用代理的任何地方重复。

**使用示例:**

```
<?php

// ...

use ProxyManager\Factory\RemoteObjectFactory;
use RemoteProxy\Adapter\RestAdapter;

$base_uri = 'http://localhost';
// Creating the factory
$factory = new RemoteObjectFactory(
            // Passing our custom adapter in
            new RestAdapter(
                // Passing our HTTP client to the adapter
                new \GuzzleHttp\Client([
                    'base_uri' => $base_uri,
                ]),
                // Along with the arraym which contain the method-to-endpoint mapping
                $array = [
                  'getBooks'   => ['path' => 'books',             'method' => 'get'],
                  'getBook'    => ['path' => 'books/:id',         'method' => 'get'],
                  'getAuthors' => ['path' => 'books/:id/authors', 'method' => 'get'],
                ];
            )
        );

$factory->createProxy('interfaceName');

// ... 
```

请注意，我们在实例化时传递给 Guzzle 的基本 URI 是我们的 API 的基本 URI。

现在，我们通过在工厂上调用`createProxy()`来创建代理对象。此方法需要一个接口，该接口的方法由远程主体实现。也就是说，我们也需要界面。让我们把这个文件放在`RemoteProxy`名称空间下:

**文件:** `src/RemoteProxy/LibraryInterface.php`

```
<?php

namespace RemoteProxy;

interface LibraryInterface
{    
    /**
     * Return the books
     */
    public function getBooks();

    /**
     * Return book's details
     *
     * @param  int $id
     * @return  mixed
     */
    public function getBook($id);

    /**
     * Return author of a book
     *
     * @param  int $id
     * @return mixed
     */
    public function getAuthors($id);
} 
```

现在我们有了接口，我们可以创建代理了:

```
<?php

// ...

$library = $factory->createProxy(RemoteProxy\LibraryInterface::class);

// calling the methods:

var_dump($library->getBooks());
var_dump($library->getAuthors(1));

// ... 
```

## 用接口保存映射信息

尽管我们可以在实例化适配器时准备映射数组，但有一种更简洁的方法可以做到这一点。我们通过使用一个**自定义注释**来定义接口中每个方法的端点。

类似于:

```
<?php

interface LibraryInterface
{
    /**
     * Return all books
     * @Endpoint(path="/books", method="get")
     */
    public function getBooks();

    // ...
} 
```

使用了名为`Endpoint`的自定义注释，指定了访问它的路径和方法。

创建自定义注释是一项非常简单的任务。每个注释都是一个同名的类，注释参数是它的公共属性。我们用注释解析器将这些信息解析成一个数组，该数组可以发送给我们的适配器。

为了创建和解析注释，我们使用了`Doctrine Annotations`库。

```
composer require doctrine/annotations 
```

每个注释类都应该用`@Annotation`进行注释，这样 Doctrine 的注释解析器就会知道它是一个有效的注释类。

让我们将注释类放在`src/RemoteProxy/Annotation`中，这样我们可以将它放在`Annotation`名称空间下:

**文件:** `src/RemoteProxy/Annotation/Endpoint.php`

```
<?php

namespace RemoteProxy\Annotation;

/**
 * @Annotation
 */
class Endpoint
{
    public $path;
    public $method;

    public function __construct($parameters)
    {
        $this->path = $parameters['path'];
        $this->method = isset($parameters['method']) ? $parameters['method'] : 'get';
    }
} 
```

在上面的代码中，我们为注释的每个参数创建了一个属性。

在我们的例子中，这些是`$path`和`$method`。在构造函数中，我们接收注释参数作为关联数组(`$parameters`)。最后，我们将`$parameters`的元素分配给它们在类中各自的属性。

现在，我们更新界面:

**文件名:** `src/RemoteProxy/LibraryInterface.php`

```
<?php

namespace RemoteProxy;

use RemoteProxy\Annotation\Endpoint;

interface LibraryInterface
{    
    /**
     * Return all books
     *
     * @Endpoint(path="books")
     */
    public function getBooks();

    /**
     * Return book's details
     *
     *
     * @Endpoint(path="books/:id")
     * @param  int $id
     * @return mixed
     */
    public function getBook($id);

    /**
     * Return author of a book
     *
     * @Endpoint(path="books/:id/authors")
     * @param  int $id
     * @return mixed
     */
    public function getAuthors($id);
} 
```

到目前为止，我们已经有了注释和接口，但是我们应该解析它们并将其传递给我们的适配器，适配器需要一组映射信息。

为此，我们创建一个 helper 类，它接受接口，在 PHP 的*反射 API* 和 Doctrine 的注释解析器的帮助下提取信息，并将结果作为数组返回。然后我们可以将这个数组传递给我们的适配器——就像我们在前一个例子中硬编码的数组一样。

让我们把这个类命名为`UriResolver`:

**文件名:** `src/RemoteProxy/UriResolver.php`

```
<?php

namespace RemoteProxy;

use Doctrine\Common\Annotations\AnnotationReader;
use RemoteProxy\Annotation\Endpoint;

class UriResolver
{

    /**
     * Annotation reader instance
     *
     * @var Doctrine\Common\Annotation\AnnotationReader
     */
    protected $annotationReader;

    /**
     * Instantiate the URI resolver
     */
    public function __construct()
    {
        $this->annotationReader = new AnnotationReader();
    }

    /**
     * Return an array of mapping information
     *
     * @param string $interface
     *
     * @return array
     */
    public function getMappings($interface)
    {

        $mappings   = [];
        $methods    = (new \ReflectionClass($interface))->getMethods();

        foreach ($methods as $method) {
            $annotations = $this->annotationReader->getMethodAnnotations($method);
            foreach ($annotations as $annotation) {
                if ($annotation instanceof Endpoint) {
                    $mappings[$method->name] = ['path' => $annotation->path, 'method' => $annotation->method];
                }
            }
        }
        return $mappings;
    }
} 
```

在上面的代码中，首先，我们实例化`Doctrine\Common\Annotation\AnnotationReader`并将其存储在`$annotationReader`属性中。

在`getMappings()`中，我们使用反射 API 获得所有接口的方法。然后，我们遍历它们来解析文档块。在每次迭代中，我们检查方法是否有`Endpoint`注释。如果是这样，我们向`$mappings`数组中添加一个新元素，用方法名作为键，用`path`和`method`作为值。

这是我们使用这个新类的方式:

**使用示例:**

```
<?php

// ...

use ProxyManager\Factory\RemoteObjectFactory;
use RemoteProxy\Adapter\RestAdapter;

$base_uri = 'http://localhost';
AnnotationRegistry::registerLoader('class_exists');
$factory = new RemoteObjectFactory(
            new RestAdapter(
                new \GuzzleHttp\Client([
                    'base_uri' => $base_uri,
                ]),
                // Array
                (new UriResolver())->getMappings(RemoteProxy\LibraryInterface::class)
            )
        );
// ... 
```

您可能想知道对`AnnotationRegistry::registerLoader('class_exists')`的调用。由于 Doctrine 注释不是由定义的 PHP 自动加载器加载的，我们需要使用 Doctrine 内置的[静默自动加载机制](http://docs.doctrine-project.org/projects/doctrine-common/en/latest/reference/annotations.html#registering-annotations):

```
<?php
AnnotationRegistry::registerLoader('class_exists'); 
```

## 创建一个特殊的工厂

每当我们想在代码中创建一个远程对象代理时，我们都需要重复上述步骤，这当然是不方便的。此外，它还会在我们的控制器中产生大量的实例。作为解决方案，我们将上述过程抽象成一个方便的工厂类。

我们将其命名为`src/RemoteProxy`目录下的`RestProxyFactory`:

**文件名** : `src/RemoteProxy/RestProxyFactory.php`

```
<?php

namespace RemoteProxy;

use Doctrine\Common\Annotations\AnnotationRegistry;
use RemoteProxy\Adapter\RestAdapter;

class RestProxyFactory
{
    public static function create($interface, $base_uri)
    {
        // Registering a silent autoloader for the annotation
        AnnotationRegistry::registerLoader('class_exists');

        $factory = new \ProxyManager\Factory\RemoteObjectFactory(
            new RestAdapter(
                new \GuzzleHttp\Client([
                    'base_uri' => rtrim($base_uri, '/') . '/',
                ]),
                (new UriResolver())->getMappings($interface)
            )
        );

        return $factory->createProxy($interface);
    }
} 
```

在上面的类中，我们接受了一个**接口**和 API 的**基本 URL** (我们需要它来实例化 Guzzle)。然后，我们将前面所有的实例化步骤放在那里。

## 使用

现在，通过使用我们特殊的工厂类，我们可以随时轻松地创建一个 REST 友好的远程对象代理。

为了测试代理，让我们在我们的`src/app.php`文件中定义一个路由:

```
<?php
// src/app.php
// ...

$app->get('test-proxy', function (Application $app) {            

    $proxy = new RestProxyFactory(LibraryInterface::class, 'localhost:9000/api/v1');
    return new JsonResponse([
        'books' => $proxy->getBooks(),
    ]);
});

// ... 
```

在上面的代码中，针对`$proxy`调用`getBooks()`，将向`http://localhost:9000/api/v1/books`发出一个 HTTP 请求。因此，我们应该得到以下响应:

```
{
resp: "{"books":"List of books..."}"
} 
```

这就是全部了。我希望你喜欢这篇文章。如果你想亲自尝试，可以在 Github 上找到完整的代码。

如果您只想在项目中使用它，可以使用 composer 安装它:

```
composer require lavary/rest-remote-proxy 
```

## 包扎

我们了解了远程代理如何工作，以及它们如何使用不同的协议与不同的远程主体进行交互。我们还创建了一个适用于 RESTful APIs 的适配器。

通过代理远程主题，我们甚至可以在不知道它们位于不同地址空间的情况下使用它们。我们只需要请求，代理人就会打电话。就这么简单。

你是如何使用代理的？你认为这种方法会在你自己的项目中派上用场吗？

## 分享这篇文章