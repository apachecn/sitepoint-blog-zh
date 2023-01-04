# guzzle–PHP HTTP 客户端

> 原文：<https://www.sitepoint.com/guzzle-php-http-client/>

你可能知道，网站开发可以分为两个主要领域:

*   前端(最终用户看到的)
*   后端(服务器为了提供请求的数据必须做的事情)

虽然前端开发经常使用几个数据源来显示一个页面，但简单的动态站点只依赖于来自同一服务器上的数据库的数据。作为今天的后端开发人员，我们需要从不同主机上的数据库服务器中检索数据，或者使用第三方提供商提供的 API，同时让一切看起来像是在我们的服务器中发生的。

PHP 自带了本地 cURL 客户端(如果启用的话)，这可能会让新手感到害怕，或者很难用来消费 SOAP 服务。其他框架在某种程度上实现了它们的 HTTP、REST 和 SOAP 客户端版本。然而，如果你不希望使用 cURL，你就没有框架或者你不喜欢你的框架解决方案，[狂饮](http://guzzlephp.org)来拯救。

Guzzle 是 PHP 的独立 HTTP 客户端。安装 Guzzle 非常简单:你需要先安装 composer([https://www . site point . com/PHP-dependency-management-with-composer/](https://www.sitepoint.com/php-dependency-management-with-composer/))。安装好 composer 并准备好之后，创建基本的`composer.json`文件，如下所示:

```
 {
        "name": "jd/guzzle demo",
        "authors": [
            {
                "name": "John Doe",
                "email": "john@doe.tst"
            }
        ],
        "require": {

        }
    }
```

现在只需将 guzzle 库添加到`require`部分的`composer.json`文件中，如下所示:

```
 {
        "name": "jd/guzzle demo",
        "authors": [
            {
                "name": "John Doe",
                "email": "john@doe.tst"
            }
        ],
        "require": {
            "guzzle/guzzle": "~3.7"
        }
    }
```

保存文件后，您只需要运行`composer install`命令，就可以开始了！

## 基础

作为一个密码学爱好者，寻找用于密钥生成的熵数据的来源是我的一个爱好。一个很好的来源是位于 https://qrng.anu.edu.au 的 ANU 量子随机数服务器。据推测，他们的 RNG 是基于量子物理学的，所以当生成具有良好熵水平的随机数据时，值得一看。

幸运的是，他们有一个我们可以使用的公共 API，所以这就是 Guzzle 派上用场的地方:

```
 <?php
    chdir(dirname(__DIR__));

    require_once 'vendor/autoload.php';

    use Guzzle\Http\Client;
    use Guzzle\Http\EntityBody;
    use Guzzle\Http\Message\Request;
    use Guzzle\Http\Message\Response;

    /** @var $client Client */
    $client = new Client("https://qrng.anu.edu.au");
```

使用 composer 及其自动加载机制的人应该对前两行很熟悉。当我们实例化一个客户端对象时，我们应该将我们想要连接的 URL 作为一个构造函数参数来传递。请注意，它不使用路径和/或 URL 参数-这将在一分钟内实现。

```
 /** @var $request Request */
    $request = $client->get('/API/jsonI.php?length=10&type=uint8');

    /** @var $response Response */
    $response = $request->send();

    /** @var $body EntityBody */
    $body = $response->getBody(true);
```

最后，我们向客户端指示路径(`/API/jsonI.php`)和 URL 参数(`length=10&type=uint8`)，通过`$client->get()`方法构建一个 HTTP GET 请求。如果我们想构建一个 POST 请求，客户机对象有`post()`和许多其他 HTTP 请求方法。为了发送请求，应该调用`$request->send()`方法，在`$response`变量中捕捉响应。最后，如果我们想看看远程服务器的响应是什么，我们执行`$response->getBody()`方法。TRUE 可以作为参数发送给`getBody()`方法以获得字符串形式的结果。

## 路径和 URL 参数操作

在前面的例子中，我们使用一个字符串来表示构建请求的路径和 URL 参数…这可能不是最优雅的解决方案。我们可能决定传递某些 URL 参数，或者根据某些条件使用不同的路径。通过对代码进行一些调整，我们可以很容易地完成对路径和 URL 参数的正确处理。

```
 /** @var $request Request */
    $request = $client->createRequest();

    $request->setPath('/API/jsonI.php');

    $request->getQuery()
        ->set('length', 10)
        ->set('type', 'uint8');
```

首先，我们用`$client->getRequest()`方法从 Guzzle 客户端创建一个空请求。请求对象有几个方法；在这种情况下，我们将使用`setPath()`和`getQuery()`方法。`setPath()`应该挺直白的；至于`getQuery`，我们使用 Guzzle 的流畅界面来逐个设置 URL 参数。

## 记录

当我们开发通过网络使用第三方服务的应用程序时，经常会出现通信问题，因此记录所有请求和响应非常重要，至少在开发阶段如此，以便我们可以调试正在发生的事情。

Guzzle 提供了几个插件。对于日志插件，我们有几个适配器可供我们使用。对于这个例子，我们将使用[独白](https://github.com/Seldaek/monolog)

要让 monolog 在您的项目中可用，您必须修改 composer.json 文件并运行 composer update 命令。您的 composer.json 文件应该如下所示:

```
 {
        "name": "jd/guzzle demo",
        "authors": [
            {
                "name": "John Doe",
                "email": "john@doe.tst"
            }
        ],
        "require": {
            "guzzle/guzzle": "~3.7",
            "monolog/monolog": "1.6.0"
        }
    }
```

为了让 Guzzle 使用 monolog 并编写我们发送和接收的所有请求和响应，我们需要对代码进行一些调整:

```
 <?php

    chdir(dirname(__DIR__));

    require_once 'vendor/autoload.php';

    //...
    use Guzzle\Log\MessageFormatter;
    use Guzzle\Log\MonologLogAdapter;
    use Guzzle\Plugin\Log\LogPlugin;
    use Monolog\Handler\StreamHandler;
    use Monolog\Logger;

    $logger = new Logger('client');
    $logger->pushHandler(new StreamHandler('guzzle.log'));

    $logAdapter = new MonologLogAdapter($logger);

    $logPlugin = new LogPlugin($logAdapter, MessageFormatter::DEBUG_FORMAT);

    /** @var $client Client */
    $client = new Client("https://qrng.anu.edu.au");

    $client->addSubscriber($logPlugin);
```

首先，我们用`$logger = new Logger('client');`创建一个独白实例。Monolog 拥有几个处理程序，在这种情况下，我们将使用 StreamHandler 写入一个名为`guzzle.log`的文件。然后我们通过实例化一个 MonologLogAdapter 对象来创建 guzzle 的 monolog 适配器。接下来，我们使用 Guzzle 的日志插件管理器 log plugin 创建一个日志插件对象。请注意`MessageFormatter::DEBUG_FORMAT`常量，它告诉 Guzzle 记录完整的请求和响应消息。请参考 Guzzle 的文档，看看哪些消息格式化程序是可用的。最后，我们通过调用`$client->addSubscriber($logPlugin);`将日志插件添加到 guzzle 客户端

## 与 Github 的 API 交互

让我们看一下这段代码，然后我将逐行解释它

```
 <?php
    chdir(dirname(__DIR__));

    require_once 'vendor/autoload.php';

    use Guzzle\http\Client;
    use Guzzle\Log\MonologLogAdapter;
    use Guzzle\Plugin\Log\LogPlugin;
    use Guzzle\Log\MessageFormatter;
    use Monolog\Logger;
    use Monolog\Handler\StreamHandler;

    $client = new Client('https://api.github.com');

    $log = new Logger('log');
    $log->pushHandler(new StreamHandler('requests.log'));

    $adapter = new MonologLogAdapter($log);

    $logPlugin = new LogPlugin($adapter, MessageFormatter::DEBUG_FORMAT);

    $client->addSubscriber($logPlugin);

    $request = $client->post('authorizations', array(), 
            json_encode(
                    array(
                            'scopes' => array(
                                    'public_repo',
                                    'user',
                                    'repo',
                                    'gist'
                            ),
                            'note' => 'auto client' . uniqid()
                    )));

    $request->setAuth('john@doe.tst', 'yourpasswordhere');

    $request->getCurlOptions()->set(CURLOPT_SSL_VERIFYPEER, false);

    $response = $request->send();

    $body = json_decode($response->getBody(true));

    $oauthToken = $body->token;

    $request = $client->get('user/keys');

    $query = $request->getQuery();

    $query->add('access_token', $oauthToken);

    $request->getCurlOptions()->set(CURLOPT_SSL_VERIFYPEER, false);

    try {

        $response = $request->send();

        $body = $response->getBody(true);

        echo $body;
    } catch (Exception $e) {
        echo $request->getResponse()->getRawHeaders();
    }
```

详细解释如下。

```
$client = new Client('https://api.github.com');
```

我们创建一个客户端，它将使用 Github 的 API 基 URL。

```
 $log = new Logger('log');
    $log->pushHandler(new StreamHandler('requests.log'));

    $adapter = new MonologLogAdapter($log);    

    $logPlugin = new LogPlugin($adapter, MessageFormatter::DEBUG_FORMAT);

    $client->addSubscriber($logPlugin);
```

正如我们之前看到的，我们设置了日志记录功能，因此我们能够调试通信问题。

```
 $request = $client->post('authorizations', array(), 
            json_encode(
                    array(
                            'scopes' => array(
                                    'public_repo',
                                    'user',
                                    'repo',
                                    'gist'
                            ),
                            'note' => 'auto client' . uniqid()
                    )));
```

首先，我们想请求授权使用 Github 的 API([http://developer.github.com/v3/oauth/](http://developer.github.com/v3/oauth/))，而不必每次都使用我们的用户名和密码，所以我们需要执行一个 HTTP POST 动词 to `https://api.github.com/authorizations`。请记住，我们在创建客户端时设置了基本 URL，因此我们只需要设置路径。要查看可以发送给 Github 的 API 的可能参数列表，请参考它们的文档。

```
 $request->setAuth('john@doe.tst', 'yourpasswordhere');

    $request->getCurlOptions()->set(CURLOPT_SSL_VERIFYPEER, false);
```

这一次，我们将使用 HTTP 基本身份验证。请记住，阻止以纯文本形式发送您的凭据的唯一机制是 HTTPS(如 API url 所示)——通过像 HTTP 这样不安全的方式执行请求可能会导致有人拦截您的数据。在第二行中，我们将 cURL 配置为不验证 SSL 证书，因为这可以解决几个通信问题。

```
 $response = $request->send();
    $body = json_decode($response->getBody(true));
```

最后，我们发送请求。响应是通过 getBody()方法获得的，TRUE 标志用于配置 Guzzle 返回一个普通字符串。请记住，Github 的 API 是 RESTful 的，所以所有内容都将被 JSON 编码，因此我们将使用 json_decode()将其解码为一个对象。

```
 $oauthToken = $body->token;
```

Oauth 令牌将包含在“token”属性中。关于响应格式的详细解释，请参考 Github 的 API 文档。

```
 $request = $client->get('user/keys');
    $query = $request->getQuery();
    $query->add('access_token', $oauthToken);
```

我们将执行一个新的请求。这一次，我们想要列出在我们的帐户中配置的授权密钥，主要用于通过 SSH 访问我们的存储库。这一次必须发送一个 HTTP GET 请求，但是我们将使用给定的 Oauth 令牌，而不是使用明文形式的凭证；这个令牌必须作为查询参数添加到我们请求的 URL 中。

```
 $response = $request->send();    
    $body = $response->getBody(true);
    echo $body;
```

最后，我们发送请求并获得响应体，准备好被解析并用于您喜欢的任何目的。

## 结论

这个小小的演示只是 Guzzle 能力的冰山一角。有了一个结构良好的插件系统、请求和响应处理机制，你最终可以驯服互联网必须提供的大量 API。

如果你有一个具体的使用案例，你想证明或只是一些一般的反馈，让我们知道！

## 分享这篇文章