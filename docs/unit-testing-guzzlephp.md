# 使用 GuzzlePHP 进行单元测试

> 原文：<https://www.sitepoint.com/unit-testing-guzzlephp/>

一月份，米格尔·罗梅罗[写了一篇很棒的文章](https://www.sitepoint.com/guzzle-php-http-client/)展示如何开始狂饮。如果你错过了这篇文章，或者不熟悉狂饮:

> Guzzle 是一个 PHP HTTP 客户端&用于构建 RESTful web 服务客户端的框架。

在 Miguel 的文章中，他介绍了该库的一系列特性，包括 GET 和 POST 请求的基本配置、插件(如使用 monolog 进行日志记录),并以与 OAuth 服务(特别是 GitHub 的 API)的交互结束。

在本教程中，我想从不同的角度向您展示如何使用 Guzzle，特别是如何用它进行单元测试。为此，我们将研究三种具体方法:

1.  手工制作定制响应
2.  使用带有模拟响应文件的 ServiceClient
3.  用模拟响应对服务器进行排队

## 正在设置

注意:本文的源代码可以在 Github 上的[处获得。](https://github.com/sitepoint-examples/GuzzleTesting)

像所有的事情一样，我们需要先走，然后才能跑。在这种情况下，我们需要设置我们的测试环境和测试类。为了让一切就绪，我们将使用 Composer。如果您不熟悉 Composer，请在继续之前阅读 SitePoint 上的 [Alexander 的文章](https://www.sitepoint.com/php-dependency-management-with-composer/)。

我们的 composer.json 文件将如下所示:

```
{
    "require": {
        "php": ">=5.3.3",
    }
    "require-dev": {
        "phpunit/phpunit": "4.0.*",
        "guzzle/guzzle": "~3.7"
    }
}
```

我规定了 PHP 的最低版本是 5.3.3。公平地说，它可能应该更高，但这是一个好的开始。我们唯一的其他要求是 PHPUnit 和 Guzzle。在将这些添加到 composer.json 之后，在您的项目运行`composer install`中，在短暂的等待之后，依赖项将准备就绪。

## 准备 PHPUnit

在运行单元测试之前，我们也需要做一些准备工作。首先，在项目中创建一个名为`tests`的目录。在那里，创建两个文件:`bootstrap.php`和`phpunit.xml.dist`。

`bootstrap.php`很简单:

```
<?php
error_reporting(E_ALL | E_STRICT);
require dirname(__DIR__) . '/vendor/autoload.php';
```

这包括 Composer 创建的来自供应商目录的自动生成的`autoload.php`文件。它确保我们可以访问 PHPUnit 和 Guzzle。接下来，我们来看看`phpunit.xml.dist`。

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="./bootstrap.php" colors="true">
    <testsuites>
        <testsuite name="importer-tests">
            <directory suffix="Test.php">./</directory>
        </testsuite>
    </testsuites>
</phpunit>
```

这是一个相当基本的配置。它的作用是:

*   告诉 PHPUnit 使用`bootstrap.php`来引导测试环境
*   在测试输出中使用颜色(便于辨别输入方面)
*   建立一个名为`tests`的测试套件。这将在位于当前目录下的所有以`Test.php`结尾的文件中查找测试

有相当多的可用选项和配置，但这符合我们的需求。如果你感兴趣，查看配置文档以了解更多细节。

完成之后，让我们开始构建我们的测试类。在 tests 目录中，创建一个名为`SitePointGuzzleTest.php`的新文件。在其中，添加以下内容:

```
<?php
use Guzzle\Tests\GuzzleTestCase,
    Guzzle\Plugin\Mock\MockPlugin,
    Guzzle\Http\Message\Response,
    Guzzle\Http\Client as HttpClient,
    Guzzle\Service\Client as ServiceClient,
    Guzzle\Http\EntityBody;

class SitePointGuzzleTest extends GuzzleTestCase 
{
    protected $_client;
}
```

这里，我们已经导入了测试类需要的关键类。我们的类从`GuzzleTestCase`扩展而来，让它可以访问我们将要使用的一些特定于 Guzzle 的测试功能。到目前为止，一切顺利。让我们看看自定义响应。

## 手工制作定制响应

从 Miguel 的文章中，您将熟悉如何初始化客户机以发出请求，可能会传入参数，然后检查响应。让我们假设您已经创建了一个类，它要么使用或包装了一个 Guzzle 客户端，要么使用了一个 Guzzle 响应对象。

假设你正在使用免费代理 API 并且你想测试检索发票数据的代码。

如果出现问题或 API 发生变化，您需要确保代码按照要求做出反应。让我们通过查看一些带注释的代码来看看如何模拟响应。

```
public function testAnotherRequest() {
    $mockResponse = new Response(200);
    $mockResponseBody = EntityBody::factory(fopen(
        './mock/bodies/body1.txt', 'r+')
    );
    $mockResponse->setBody($mockResponseBody);
    // ...
}
```

这里我们首先实例化一个新的响应对象。然后我们使用工厂方法`Guzzle\Http\EntityBody`用文件`./mock/bodies/body1.txt`的内容设置响应体。

这使得将配置从代码中分离出来变得容易，并且使得测试维护更加简单。`body1.txt`可在本文的源代码中找到。

```
$mockResponse->setHeaders(array(
    "Host" => "httpbin.org",
    "User-Agent" => "curl/7.19.7 (universal-apple-darwin10.0) libcurl/7.19.7 OpenSSL/0.9.8l zlib/1.2.3",
    "Accept" => "application/json",
    "Content-Type" => "application/json"
));
```

然后，我们将一个关联数组传递给 setHeaders，它将在我们的响应中设置四个自定义头。

```
$plugin = new MockPlugin();
$plugin->addResponse($mockResponse);
$client = new HttpClient();
$client->addSubscriber($plugin);
```

这里我们创建了一个新的 MockPlugin 对象，并向它传递了响应对象。一旦我们实例化了新的`HttpClient`，我们就将 MockPlugin 添加为订阅者，这反过来将确保用它发出的请求使用我们刚刚创建的模拟响应。

```
$request = $client->get(
    'https://api.freeagent.com/v2/invoices'
);
$response = $request->send();
```

现在，和 Miguel 的文章一样，我们在客户机上调用 get，并发送返回的请求来获得一个响应对象。这样，我们可以运行一系列测试断言。

```
$this->assertEquals(200, $response->getStatusCode());
$this->assertTrue(in_array(
    'Host', array_keys($response->getHeaders()->toArray())
));
$this->assertTrue($response->hasHeader("User-Agent"));
$this->assertCount(4, $response->getHeaders());
$this->assertSame(
    $mockResponseBody->getSize(), 
    $response->getBody()->getSize()
);
$this->assertSame(
    1, 
    count(json_decode($response->getBody(true))->invoices
));
```

在断言中，您可以看到我已经检查了响应代码，如果`Host`和`User-Agent`在响应头中，发送的头的数量，主体的大小与我们伪造的响应主体的大小相匹配，并且在收到的响应中有一张发票。

这只是可以运行的测试种类中的一个小例子，但是它显示了模仿一个定制响应和在它被检索时测试它是多么容易。你还会做什么样的测试？

## 使用带有模拟响应文件的 ServiceClient

这是模仿回应的漫长道路。如果你还记得，我在开始时强调我们的测试类扩展了 GuzzleTestCase，以获得一些优秀的测试功能。让我们看看如何通过使用它来跳过我们刚刚做的许多工作。

这一次，让我们覆盖`setUp`如下:

```
public function setUp()
{
    $this->_client = new ServiceClient();
    $this->setMockBasePath('./mock/responses');
    $this->setMockResponse(
        $this->_client, array('response1')
    );
}
```

这里，我们将类变量`$_client`实例化为一个新的`ServiceClient`。然后我们使用`setMockBasePath`，调用`./mock/responses`，并调用`setMockResponse`，传入我们的客户端对象和一个数组。

数组列出了'中的文件名。/mock/responses。这些文件的内容将用于建立一系列的响应，客户端将在每次连续调用`send`时收到这些响应。

在本例中，我只添加了一个，但是您可以轻松地添加任意多个。您可以在`./mock/responses/response1`中看到，它列出了 HTTP 版本、状态代码、报头和响应体。同样，它将代码和配置巧妙地分开。

现在，让我们看看使用它的函数。

```
public function testRequests() {
    $request = $this->_client->get(
        'https://api.freeagent.com/v2/invoices'
    );
    $request->getQuery()->set(
        'view', 'recent_open_or_overdue'
    );
    $response = $request->send();

    $this->assertContainsOnly(
        $request, $this->getMockedRequests()
    );
    $this->assertEquals(200, $response->getStatusCode());
    $this->assertEquals(
        'AmazonS3', $response->getServer()
    );
    $this->assertEquals(
        'application/xml', $response->getContentType()
    );
}
```

您可以看到，我所要做的就是调用客户端对象上的`get`,并发送返回的请求对象，就像我们通常所做的那样。我添加了查询参数只是为了更好地衡量。

和以前一样，我可以运行一系列断言，检查被模仿的请求、返回的状态代码、服务器和内容类型头。

值得一提的是，它是一个先进先出队列；这意味着添加的第一个响应是最先发送的。不要让这个绊倒你。

## 用模拟响应对服务器进行排队

最后，让我们看看 Guzzle 的一个更酷的方面。如果我们调用`$this->getServer()->enqueue(array());`，Guzzle 在后台透明地启动一个 node.js 服务器。然后，我们可以使用它向发送请求，就好像它是我们真正的服务器端点一样。让我们来看看如何使用它。

```
public function testWithRemoteServer() {
    $mockProperties = array(
        array(
            'header' => './mock/headers/header1.txt',
            'body' => './mock/bodies/body1.txt',
            'status' => 200
        )
    );
}
```

这里我创建了一个数组来存储模拟请求的头、体和状态信息，指定状态代码为 200，以及包含头和体响应数据的文件。

```
$mockResponses = array();

foreach($mockProperties as $property) {
    $mockResponse = new Response($property['status']);
    $mockResponseBody = EntityBody::factory(
        fopen($property['body'], 'r+')
    );
    $mockResponse->setBody($mockResponseBody);
    $headers = explode(
        "\n", 
        file_get_contents($property['header'], true)
    );
    foreach($headers as $header) {
        list($key, $value) = explode(': ', $header);
        $mockResponse->addHeader($key, $value);
    }
    $mockResponses[] = $mockResponse;
}
```

然后我创建了一个新的响应对象，设置了状态代码，并再次使用了`EntityBody`类的工厂方法来设置主体。头文件有点麻烦，所以我遍历了文件的内容，为每个检索到的键/值对调用`addHeader`。

```
$this->getServer()->enqueue($mockResponses);
```

创建的每个模拟响应对象都被添加到一个数组中，然后传递给`enqueue`。现在，它已经有了一组准备发送给我们的客户端请求的响应。

```
$client = new HttpClient();
$client->setBaseUrl($this->getServer()->getUrl());
$request = $client->get();
$request->getQuery()->set(
    'view', 'recent_open_or_overdue'
);
$response = $request->send();
```

这里，和以前一样，我们已经初始化了我们的客户机对象，对返回的请求调用 get 和 send。需要注意的一点是，要使用节点。JS 服务器，我们需要将`$this->getServer()->getUrl()`传递给`$client->setBaseUrl()`，否则无法工作。

```
$this->assertCount(5, $response->getHeaders());
$this->assertEmpty($response->getContentDisposition());
$this->assertSame('HTTP', $response->getProtocol());
```

接下来，我们的代码像以前一样工作，我在返回的响应对象中添加了消息头、内容处理消息头和协议的断言。

## 包扎

你觉得怎么样？虽然这确实需要一些工作，但我相信您可以看到设置不同的响应方法是多么简单。我相信只要做一点工作，你也可以简化我所采用的方法。

我知道这仅仅触及了 Guzzle 测试的表面，但是我希望我激起了你的好奇心，向你展示了大量的方法来确保你的客户端代码被完全测试覆盖。

你有什么经验？请在评论中分享你的想法。

## 分享这篇文章