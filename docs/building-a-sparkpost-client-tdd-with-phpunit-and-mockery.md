# 构建 SparkPost 客户端:使用 PhpUnit 和 Mockery 的 TDD

> 原文：<https://www.sitepoint.com/building-a-sparkpost-client-tdd-with-phpunit-and-mockery/>

在[之前的一篇](https://www.sitepoint.com/what-is-sparkpost/)中，我们看了一下[的 SparkPost](https://www.sparkpost.com) (作为 [Mandrill](https://mandrillapp.com) 的替代)，并探索了一点官方 PHP 客户端。官方客户端处理大量的工作，但是我开始思考如何建立一个新的客户端。

我越想越觉得有道理。我可以学习 SparkPost API，同时练习测试驱动开发。所以，在这篇文章中，我们将会这样做！

![Home page screenshot](img/042b8f2bf4ef6118d0ab9d89ff7a3ce8.png)

你可以在 [Github](https://github.com/sitepoint-editors/tutorial-building-a-new-sparkpost-client) 上找到这篇文章的代码。

## 入门指南

首先，我们需要 Guzzle 向 SparkPost API 发出请求。我们可以安装它:

```
composer require guzzlehttp/guzzle 
```

此外，我们将尽早编写测试，因此我们还应该安装 PHPUnit 和 mocking:

```
composer require --dev phpunit/phpunit mockery/mockery 
```

在运行 PHPUnit 之前，我们需要创建一个配置文件:

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit
    backupGlobals="false"
    backupStaticAttributes="false"
    bootstrap="vendor/autoload.php"
    colors="true"
    convertErrorsToExceptions="true"
    convertNoticesToExceptions="true"
    convertWarningsToExceptions="false"
    processIsolation="false"
    stopOnFailure="false"
    syntaxCheck="false">
    <testsuites>
        <testsuite>
            <directory suffix="Test.php">tests</directory>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist addUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">src</directory>
        </whitelist>
    </filter>
</phpunit> 
```

这个配置文件处理许多事情:

1.  许多根节点属性都是合理、直观的默认值。我想特别注意的是`bootstrap`:它告诉 PHPUnit 加载 Composer 的自动加载代码。
2.  我们告诉 PHPUnit 在`tests`文件夹中加载所有以`Test.php`结尾的文件。它将把所有带有这个后缀的文件视为类文件，每个类都有一个类。如果它不能实例化它找到的任何类(比如抽象类)，那么它会忽略这些类。
3.  我们告诉 PHPUnit 将所有 PHP 文件(来自`src`文件夹)添加到代码覆盖率报告中。如果你不确定那是什么，不要担心。我们一会儿会看它…

我们现在可以运行:

```
vendor/bin/phpunit 
```

…我们应该看到:

![New phpunit screen](img/17ecbbd2bdf7c5bc869a80d47608d485.png)

## 设计界面

我最喜欢测试驱动开发的一点是它如何将我推向简约友好的界面。我从消费者开始，以对我来说最容易使用的实现结束。

让我们做第一个测试。我们可以做的最简单的事情就是通过 SparkPost API 发送一封电子邮件。如果您查看文档，您会发现这是通过对`https://api.sparkpost.com/api/v1/transmissions`的`POST`请求发生的，带有 JSON 主体和一些键头。我们可以用下面的代码模拟这种情况:

```
require "vendor/autoload.php";

$client = new GuzzleHttp\Client();

$method = "POST";
$endpoint = "https://api.sparkpost.com/api/v1/transmissions";
$config = require "config.php";

$response = $client->request($method, $endpoint, [
    "headers" => [
        "Content-type" => "application/json",
        "Authorization" => $config["key"],
    ],
    "body" => json_encode([
        "recipients" => [
            [
                "address" => [
                    "name" => "Christopher",
                    "email" => "cgpitt@gmail.com",
                ],
            ],
        ],
        "content" => [
            "from" => "sandbox@sparkpostbox.com",
            "subject" => "The email subject",
            "html" => "The email <strong>content</strong>",
        ],
    ]),
]); 
```

你可以使用与上一篇文章相同的发件人/收件人信息。

这里假设您有一个`config.php`文件，其中存储了您的 SparkPost API 密钥:

```
return [
    "key" => "[your SparkPost API key here]",
]; 
```

*确保将这个文件添加到`.gitignore`中，这样你就不会意外地将你的 SparkPost API 密匙提交给 Github。*

给定实现的粗略细节，我们可以开始为公共接口做一些假设(或者定义一些需求):

1.  我们不想对每个请求都重复`https://api.sparkpost.com/api/v1`。我们可以接受它作为构造函数的参数。
2.  我们不想知道具体的请求方法或端点。客户端应该解决这些问题，并针对 API 的新版本进行自我调整。
3.  我们可能想要定义一些或所有的端点参数，但是应该有一些合理的缺省值和一种让客户端重新安排它们的方法。

## 从测试开始

因为我们想要使用 mockss，所以创建一个中心的、基本的测试类来关闭 mock 是一个好主意:

```
namespace SparkPost\Api\Test;

use Mockery;
use PHPUnit_Framework_TestCase;

abstract class AbstractTest extends PHPUnit_Framework_TestCase
{
    /**
     * @inheritdoc
     */
    public function tearDown()
    {
        Mockery::close();
    }
} 
```

我们可以用它作为其他测试的基类，PHPUnit 不会尝试实例化它，因为它是抽象的。关闭嘲弄的潜在原因有点超出了这篇文章的范围。这只是一些你需要知道的关于嘲讽(和类似的库)的事情。

现在我们可以开始定义我们想要的客户端界面了:

```
namespace SparkPost\Api\Test;

use Mockery;
use Mockery\MockInterface;
use GuzzleHttp\Client as GuzzleClient;

class ClientTest extends AbstractTest
{
    /**
     * @test
     */
    public function itCreatesTransmissions()
    {
        /** @var MockInterface|GuzzleClient $mock */
        $mock = Mockery::mock(GuzzleClient::class);

        // ...what do we want to test here?
    }
} 
```

嘲弄背后的想法是，它允许我们模拟我们不想测试的类，而那些我们想测试的类却需要这些类。在这种情况下，我们不想为 Guzzle 编写测试(甚至不想用它发出真正的 HTTP 请求)，所以我们创建了一个 Guzzle\Client 类的模拟。我们可以告诉 mock 期待对`request`方法的调用，带有一个请求方法字符串、一个端点字符串和一组选项:

```
$mock
    ->shouldReceive("request")
    ->with(
        Mockery::type("string"),
        Mockery::type("string"),
        $sendThroughGuzzle
    )
    ->andReturn($mock); 
```

我们并不真正关心哪个请求方法被发送给 Guzzle，或者哪个端点。这些可能会随着 SparkPost API 的改变而改变，但重要的是为这些参数发送字符串。我们真正感兴趣的是看我们发送给客户端的参数是否格式正确。

假设我们想要测试以下数据:

```
$sendThroughGuzzle = [
    "headers" => [
        "Content-type" => "application/json",
        "Authorization" => "[fake SparkPost API key here]",
    ],
    "body" => json_encode([
        "recipients" => [
            [
                "address" => [
                    "name" => "Christopher",
                    "email" => "cgpitt@gmail.com",
                ],
            ],
        ],
        "content" => [
            "from" => "sandbox@sparkpostbox.com",
            "subject" => "The email subject",
            "html" => "The email <strong>content</strong>",
        ],
    ]),
]; 
```

…但是，如果我们调用以下方法，我们只希望该数据通过:

```
$client = new SparkPost\Api\Client(
    $mock, "[fake SparkPost API key here]"
);

$client->createTransmission([
    "recipients" => [
        [
            "address" => [
                "name" => "Christopher",
                "email" => "cgpitt@gmail.com",
            ],
        ],
    ],
    "subject" => "The email subject",
    "html" => "The email <strong>content</strong>",
]); 
```

然后，我们可能需要在记住这个结构的情况下进行剩余的测试。假设我们只对从 SparkPost 返回的`json_decode`数据感兴趣…

我们可以想象这样使用客户端:

1.  我们称之为`$client->createTransmission(...)`。
2.  客户端根据 SparkPost 的要求格式化这些参数。
3.  客户端向 SparkPost 发送一个经过身份验证的请求。
4.  响应是通过`json_decode`发送的，只是得到一个响应数据的数组。

我们可以将这些步骤构建到测试中:

```
public function itCreatesTransmissions()
{
    /** @var MockInterface|GuzzleClient $mock */
    $mock = Mockery::mock(GuzzleClient::class);

    $sendThroughGuzzle = [
        "headers" => [
            "Content-type" => "application/json",
            "Authorization" => "[fake SparkPost API key here]",
        ],
        "body" => json_encode([
            "recipients" => [
                [
                    "address" => [
                        "name" => "Christopher",
                        "email" => "cgpitt@gmail.com",
                    ],
                ],
            ],
            "content" => [
                "from" => "sandbox@sparkpostbox.com",
                "subject" => "The email subject",
                "html" => "The email <strong>content</strong>",
            ],
        ]),
    ];

    $mock
        ->shouldReceive("request")
        ->with(
            Mockery::type("string"),
            Mockery::type("string"),
            $sendThroughGuzzle
        )
        ->andReturn($mock);

    $mock
        ->shouldReceive("getBody")
        ->andReturn(
            json_encode(["foo" => "bar"])
        );

    $client = new Client(
        $mock, "[fake SparkPost API key here]"
    );

    $this->assertEquals(
        ["foo" => "bar"],
        $client->createTransmission([
            "recipients" => [
                [
                    "address" => [
                        "name" => "Christopher",
                        "email" => "cgpitt@gmail.com",
                    ],
                ],
            ],
            "subject" => "The email subject",
            "html" => "The email <strong>content</strong>",
        ])
    );
} 
```

在我们运行 PHPUnit 来查看这个测试之前，我们需要为我们的库添加 autoload 指令到`composer.json`:

```
"autoload": {
    "psr-4": {
        "SparkPost\\Api\\": "src"
    }
},
"autoload-dev": {
    "psr-4": {
        "SparkPost\\Api\\Test\\": "tests"
    }
} 
```

*你可能还得跑`composer du`才能让这些工作……*

## 实现接口

当我们运行 PHPUnit 时，我们会看到各种各样的错误。我们还没有创建类，更不用说实现它们的行为了。让我们来制作`Client`类:

```
<?php

namespace SparkPost\Api;

use GuzzleHttp\Client as GuzzleClient;

class Client
{
    /**
     * @var string
     */
    private $base = "https://api.sparkpost.com/api/v1";

    /**
     * @var GuzzleClient
     */
    private $client;

    /**
     * @var string
     */
    private $key;

    /**
     * @param GuzzleClient $client
     * @param string $key
     */
    public function __construct(GuzzleClient $client, $key)
    {
        $this->client = $client;
        $this->key = $key;
    }

    /**
     * @param array $options
     *
     * @return array
     */
    public function createTransmission(array $options = [])
    {
        $options = array_replace_recursive([
            "from" => "sandbox@sparkpostbox.com",
        ], $options);

        $send = [
            "recipients" => $options["recipients"],
            "content" => [
                "from" => $options["from"],
                "subject" => $options["subject"],
                "html" => $options["html"],
            ]
        ];

        return $this->request("POST", "transmissions", $send);
    }

    /**
     * @param string $method
     * @param string $endpoint
     * @param array $options
     *
     * @return array
     */
    private function request(
        $method, $endpoint, array $options = []
    )
    {
        // ...time to forward the request
    }
} 
```

我们在进步！当我们运行 PHPUnit 时，我们应该看到类似这样的内容:

![Invalid return type error](img/3281c87543f21c3c28202abf2561e5af.png)

最后，让我们将请求转发到 SparkPost:

```
private function request(
    $method, $endpoint, array $options = []
)
{
    $endpoint = $this->base . "/" . $endpoint;

    $response = $this->client->request($method, endpoint, [
        "headers" => [
            "Content-type" => "application/json",
            "Authorization" => $this->key,
        ],
        "body" => json_encode($options),
    ]);

    return json_decode($response->getBody(), true);
} 
```

有了这个，测试就通过了。

![Passing tests](img/6aae400acc236543dc152a8010824a33.png)

也许这也有助于我们了解如何不带任何嘲讽地消费它:

```
require "vendor/autoload.php";

$config = require "config.php";

$client = new SparkPost\Api\Client(
    new GuzzleHttp\Client(), $config["key"]
);

$reponse = $client->createTransmission([
    "recipients" => [
        [
            "address" => [
                "name" => "Christopher",
                "email" => "cgpitt@gmail.com",
            ],
        ],
    ],
    "subject" => "The email subject",
    "html" => "The email <strong>content</strong>",
]); 
```

## 新闻报道

记得我提到过代码覆盖报告吗？我们可以按以下方式运行 PHPUnit:

```
vendor/bin/phpunit --coverage-html coverage 
```

这将生成一个 HTML 文件的文件夹。您可以打开`coverage/index.html`来了解您的测试覆盖了多少库。您需要安装 XDebug 扩展，使用这个参数运行更大的测试套件可能比不使用它要慢。

![Coverage report](img/8573cd5ca095d7d137f27b3528881c92.png)

## 需要考虑的事项

1.  我们没有验证`createTransmission`的参数。这将是一件好事。

2.  我们与暴饮暴食紧密相连。我认为这没问题，但是如果你有所保留，宁愿创建一个“客户端”接口和一个巨大的适配器，或者甚至更好，采用[http ug 方法](https://www.sitepoint.com/breaking-free-from-guzzle5-with-php-http-and-httplug/)。

3.  SparkPost API 还有很多功能，在设计这样一个界面时，您可以选择一些有趣的途径。这是我最近做的一个截屏，探索了在我们今天构建的客户端之上的一个表达语法

你觉得 SparkPost 怎么样？你喜欢这种开发流程吗？请在下面的评论中告诉我们。

## 分享这篇文章