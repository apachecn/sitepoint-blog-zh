# 用 RAML 测试 API

> 原文：<https://www.sitepoint.com/testing-apis-raml/>

在最近的一篇文章中，我研究了 RESTful API 建模语言(RAML)。我概述了 RAML 是什么，如何编写它以及它的一些用途。

这一次，我将介绍一些使用 RAML 进行测试的方法。我们将从使用 RAML 来验证来自 API 的响应开始。然后，我们将研究一种模拟 API 服务器的方法，使用一个 RAML 文件来创建模拟 HTTP 响应。

## 验证 API 响应

首先，让我们为虚构的 API 定义一个简单的 RAML 文件。我已经省略了一些路线，但它将足以展示原则。

```
#%RAML 0.8
title: Albums
version: v1
baseUri: http://localhost:8000
traits:
  - secured:
      description: Some requests require authentication
      queryParameters:
        accessToken:
          displayName: Access Token
          description: An access token is required for secure routes
          required: true
  - unsecured:
      description: This is not secured
/account:
  displayName: Account
  get:
    description: Get the currently authenticated user's account details.    
    is: [secured]
    responses:
      200:
        body:
          application/json: 
            schema: |
              { "$schema": "http://json-schema.org/schema#",
                "type": "object",
                "description": "A user",
                "properties": {
                  "id":  { 
                    "description": "Unique numeric ID for this user",
                    "type": "integer" 
                  },
                  "username":  { 
                    "description": "The user's username",
                    "type": "string" 
                  },                  
                  "email":  { 
                    "description": "The user's e-mail address",
                    "type": "string",
                    "format": "email" 
                  },
                  "twitter": {
                    "description": "User's Twitter screen name (without the leading @)",
                    "type": "string",
										"maxLength": 15
                  }
                },
                "required": [ "id", "username" ]
              }
            example: |
              {
                "id": 12345678,
                "username": "joebloggs",
                "email": "joebloggs@example.com",                                
                "twitter": "joebloggs"
              }
  put:
    description: Update the current user's account
/albums:
  displayName: Albums
  /{id}:      
    displayName: Album
    uriParameters: 
      id:
        description: Numeric ID which represents the album
    /tracks:
      displayName: Album Tracklisting
      get:
        responses:
          200:
            body:
              application/json: 
                schema: |
                  { "$schema": "http://json-schema.org/schema#",
                    "type": "array",
                    "description": "An array of tracks",
                    "items": {
                      "id":  { 
                        "description": "Unique numeric ID for this track",
                        "type": "integer" 
                      },
                      "name":  { 
                        "description": "The name of the track",
                        "type": "string" 
                      }
                    },
                    "required": [ "id", "name" ]
                  }
                example: |
                  [                    
                    {
                      "id": 12345,
                      "name": "Dark & Long"
                    },
                    {
                      "id": 12346,
                      "name": "Mmm Skyscraper I Love You"
                    }
                  ]
```

请注意，我们已经为安全路由定义了一个特征，它需要一个访问令牌作为查询参数。我们已经定义了一些可用的路由，并定义了一些 JSON 模式来指定结果的格式。我们还包括了一些示例响应；这些是我们将要用来生成模拟响应的内容。

让我们创建一个我们将在本教程的两个部分中使用的应用程序。你会在 Github 上找到它[。](https://github.com/sitepoint-editors/raml-test-example)

在第一部分中，我将展示如何解析一个 RAML 文件，提取给定路由的模式，然后使用它进行测试。

创建一个项目目录，用上面的内容创建文件`test/fixture/api.raml`。

我们将使用 [Guzzle](https://github.com/guzzle/guzzle) 访问 API， [PHPUnit](https://phpunit.de/) 作为测试框架，以及这个[基于 PHP 的 RAML 解析器](https://github.com/alecsammon/php-raml-parser)。因此，创建一个`composer.json`来定义这些依赖关系:

```
{
    "name": "sitepoint/raml-testing",
    "description": "A simple example of testing APIs against RAML definitions",
    "require": {
        "alecsammon/php-raml-parser": "dev-master",
        "guzzle/guzzle": "~3.9@dev",				
        "phpunit/phpunit": "~4.6@dev"
    },
    "authors": [
        {
            "name": "lukaswhite",
            "email": "hello@lukaswhite.com"
        }
    ],
		"autoload": {
        "psr-0": {
          "Sitepoint\\": "src/"
        }
      },
    "minimum-stability": "dev"
}
```

运行`composer install`下载所需的软件包。

现在，让我们创建一个简单的测试来验证来自 API 的响应。我们将从一个`phpunit.xml`文件开始:

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit
    colors="true"
    convertErrorsToExceptions="false"
    convertNoticesToExceptions="false"
    convertWarningsToExceptions="false"
    stopOnFailure="false"
    bootstrap="vendor/autoload.php"
    strict="true"
    verbose="true"
    syntaxCheck="true">

    <testsuites>
        <testsuite name="PHP Raml Parser">
            <directory>./test</directory>
        </testsuite>
    </testsuites>

    <filter>
        <whitelist>
            <directory suffix=".php">./src</directory>
        </whitelist>
    </filter>

</phpunit>
```

*PHP RAML 解析器当前显示一个弃用错误。为了解决这个问题，我们将`convertErrorsToExceptions`、`convertNoticesToExceptions`和`convertWarningsToExceptions`设置为`false`。*

让我们创建一个框架测试类。将此命名为`test/AccountTest.php`，并从定义`setUp()`方法开始:

```
<?php

class AccountTest extends PHPUnit_Framework_TestCase
{
    /**
     * @var \Raml\Parser
     */
    private $parser;

    public function setUp()
    {
        parent::setUp();
        $parser = new \Raml\Parser();
        $this->api = $parser->parse(__DIR__.'/fixture/api.raml');

        $routes = $this->api->getResourcesAsUri()->getRoutes();

        $response = $routes['GET /account']['response']->getResponse(200);

        $this->schema = $response->getSchemaByType('application/json');
    }

}
```

这里我们解析 RAML 文件，然后提取所有定义的路由。接下来，我们将拉出由字符串`GET /account`标识的路线。从那里，我们提取成功响应的定义，从那里，我们获取定义 JSON 响应的预期结构的 JSON 模式。

现在，我们可以创建一个简单的测试来调用我们的端点，检查我们是否获得了 200 状态，响应格式是否是 JSON **和**，并根据模式进行验证。

```
/** @test */
public function shouldBeExpectedFormat()
{    
	$accessToken = 'some-secret-token';

	$client = new \Guzzle\Http\Client();

	$request = $client->get($this->api->getBaseUri() . '/account', [
		'query' => [
			'accessToken' => $accessToken,
		]
	]);

	$response = $client->send($request);

	// Check that we got a 200 status code
	$this->assertEquals( 200, $response->getStatusCode() );

	// Check that the response is JSON 
	$this->assertEquals( 'application/json', $response->getHeader('Content-Type')->__toString());

	// Check the JSON against the schema
	$this->assertTrue($this->schema->validate($response->getBody()));

}
```

就这么简单；RAML 解析器为我们定义的 JSON 模式提供了一个验证器。

使用 RAML 测试 API 的方法有很多。和 JSON 模式一样，RAML 也支持 XML 模式——所以用 XML 格式检查结果的原理大体上是相似的。您可以测试是否返回了适当的状态代码，RAML 中定义的路由是否都存在，等等。

在下一节中，我们将看看如何使用 RAML 来模拟 API 响应。

## 使用 RAML 模拟 API

我们可以用 RAML 做的另一件有趣的事情是用它来模拟 API。不同的 API 之间可能有太多的差异，无法创建一个通用的模拟类，所以让我们构建一个可以根据您的需求进行调整的类。

我们要做的是创造三样东西:

*   一个“响应”类，它封装了标准的 HTTP 响应数据，比如状态代码和主体
*   一个使用 RAML 来响应“URL”的类
*   可以在 web 服务器上运行的简单“服务器”

为了简单起见，我们将使用与上一节相同的代码。我们只需要添加一个额外的依赖项； [FastRoute](https://github.com/nikic/FastRoute) ，一个简单快速的路由组件，我们将使用它来确定我们将要响应的路由。将其添加到您的`composer.json`文件的`require`部分:

```
"nikic/fast-route": "~0.3.0"
```

现在，让我们创建一个真正简单的响应类；在`src/Sitepoint/Response.php`中创建:

```
<?php namespace Sitepoint;

class Response {

	/**
	 * The HTTP status code
	 * @var integer
	 */
	public $status;

	/**
	 * The body of the response
	 * @var string
	 */
	public $body;

	/**
	 * An array of response headers
	 * @var array
	 */
	public $headers;

	/**
	 * Constructor
	 * 
	 * @param integer $status The HTTP status code
	 */
	public function __construct($status = 200)
	{
		$this->status = $status;

		$this->headers = [
			'Content-Type' => 'application/json'
		];
	}

	/**
	 * Sets the response body
	 * 
	 * @param string $body
	 */
	public function setBody($body)
	{
		$this->body = $body;
		$this->headers['Content-Length'] = strlen($body);
	}

}
```

这里没什么太复杂的。注意，我们将模拟一个只“说”JSON 的 API，所以我们将`Content-Type`强制转换为`application/json`。

现在让我们开始一个类，它给定一个 HTTP 动词和路径，将通过一个 RAML 文件查找匹配的路由，并返回一个适当的响应。我们将通过从适当类型的响应中抽出`example`来实现这一点。对于这个组件来说，这将始终是一个成功的(状态代码`200` ) JSON 响应。

创建文件`src/Sitepoint/RamlApiMock.php`，从以下内容开始课程:

```
<?php namespace Sitepoint;

class RamlApiMock {

	/**
	 * Constructor
	 * 
	 * @param string $ramlFilepath Path to the RAML file to use
	 */
	public function __construct($ramlFilepath)
	{
		// Create the RAML parser and parse the RAML file
		$parser = new \Raml\Parser();
		$api = $parser->parse($ramlFilepath);

		// Extract the routes
		$routes = $api->getResourcesAsUri()->getRoutes();
		$this->routes = $routes;

		// Iterate through the available routes and add them to the Router
		$this->dispatcher = \FastRoute\simpleDispatcher(function(\FastRoute\RouteCollector $r) use ($routes) {
			foreach ($routes as $route) {
				$r->addRoute($route['method'], $route['path'], $route['path']);
			}
		});

	}

}
```

让我们看看我们在这里做什么。

和以前一样，我们正在解析一个 RAML 文件。然后我们提取所有可用的路线。接下来，我们遍历它们，并将它们添加到与 FastRoute 组件兼容的集合中。幸运的是，它使用相同的格式，所以它能够翻译以下内容:

```
/albums/123/tracks
```

对此:

```
/albums/{id}/tracks
```

`addRoute()`方法的第三个参数是用来处理路由的函数名。通常，您需要定义单独的函数来相应地处理每条路由。然而，由于我们将使用相同的代码处理所有的路由，我们稍微覆盖了这种行为，并且我们第二次添加了路径，而不是函数名。这样，我们可以提取处理程序中的路径来确定我们需要发送什么响应。

让我们创建一个`dispatch()`方法。

```
/**
	 * Dispatch a route
	 * 
	 * @param  string $method  The HTTP verb (GET, POST etc)
	 * @param  string $url     The URL
	 * @param  array  $data    An array of data (Note, not currently used)
	 * @param  array  $headers An array of headers (Note, not currently used)
	 * @return Response
	 */
	public function dispatch($method, $url, $data = array(), $headers = array())
	{
		// Parse the URL
		$parsedUrl = parse_url($url);
		$path = $parsedUrl['path'];

		// Attempt to obtain a matching route
		$routeInfo = $this->dispatcher->dispatch($method, $path);

		// Analyse the route
		switch ($routeInfo[0]) {
			case \FastRoute\Dispatcher::NOT_FOUND:
				// Return a 404
				return new Response(404);				
				break;

			case \FastRoute\Dispatcher::METHOD_NOT_ALLOWED:
				// Method not allows (405)
				$allowedMethods = $routeInfo[1];				
				// Create the response...
				$response = new Response(405);
				// ...and set the Allow header
				$response->headers['Allow'] = implode(', ', $allowedMethods);
				return $response;
				break;

			case \FastRoute\Dispatcher::FOUND:
				$handler = $routeInfo[1];
				$vars = $routeInfo[2];
				$signature = sprintf('%s %s', $method, $handler);
				$route = $this->routes[$signature];

				// Get any query parameters
				if (isset($parsedUrl['query'])) {					
					parse_str($parsedUrl['query'], $queryParams);							
				} else {
					$queryParams = [];
				}

				// Check the query parameters
				$errors = $this->checkQueryParameters($route, $queryParams);
				if (count($errors)) {
					$response = new Response(400);
					$response->setBody(json_encode(['errors' => $errors]));
					return $response;
				}				

				// If we get this far, is a successful response
				return $this->handleRoute($route, $vars);

				break;
		}

	}
```

这是怎么回事？

我们首先解析 URL 并提取路径，然后使用 FastRoute 尝试找到匹配的路由。

`RouteCollection`的`dispatch()`方法返回一个数组，它的第一个元素告诉我们它是否是一个有效的路由，它是否是一个有效的路由但是无效的方法，或者只是没有找到。

如果我们找不到匹配的路线，我们就生成一个`404 Not Found`。如果该方法不受支持，我们生成一个`405 Method Not Allowed`，将允许的方法弹出到适当的头中。

第三种情况很有趣。我们通过连接方法和路径来生成一个“签名”，所以它看起来像这样:

```
GET /account
```

或者:

```
GET /albums/{id}/tracks
```

然后，我们可以使用它从`$routes`属性中获取路由定义，您会记得我们是从 RAML 文件中提取的。

下一步是创建一个查询参数数组，然后调用一个函数来检查这些参数——我们一会儿将讨论这个特定的函数。因为不同的 API 处理错误的方式非常不同，所以您可能希望修改它以适合您的 API——在本例中，我只是返回一个`400 Bad Request`,其主体包含特定验证错误的 JSON 表示。

此时，您可能希望添加一些额外的检查或验证。例如，您可以检查请求是否提供了适当的安全凭证。我们将通过所需的`accessToken`查询参数来实现这一点，我们已经在 RAML 文件中将它定义为一个特征。

最后，我们调用`handleRoute()`方法，传递路由定义和任何 URI 参数。在我们看之前，让我们回到我们的查询参数验证。

```
/**
	 * Checks any query parameters
	 * @param  array 	$route  The current route definition, taken from RAML
	 * @param  array 	$params The query parameters
	 * @return boolean
	 */
	public function checkQueryParameters($route, $params)
	{
		// Get this route's available query parameters
		$queryParameters = $route['response']->getQueryParameters();

		// Create an array to hold the errors
		$errors = [];

		if (count($queryParameters)) {

			foreach($queryParameters as $name => $param) {				

				// If the display name is set then great, we'll use that - otherwise we'll use
				// the name
				$displayName = (strlen($param->getDisplayName())) ? $param->getDisplayName() : $name;

				// If the parameter is required but not supplied, add an error
				if ($param->isRequired() && !isset($params[$name])) {
					$errors[$name] = sprintf('%s is required', $displayName);
				}

				// Now check the format
				if (isset($params[$name])) {

					switch ($param->getType()) {
						case 'string':
							if (!is_string($params[$name])) {
								$errors[$name] = sprintf('%s must be a string');
							}
							break;
						case 'number':
							if (!is_numeric($params[$name])) {
								$errors[$name] = sprintf('%s must be a number');
							}
							break;
						case 'integer':
							if (!is_int($params[$name])) {
								$errors[$name] = sprintf('%s must be an integer');
							}
							break;
						case 'boolean':
							if (!is_bool($params[$name])) {
								$errors[$name] = sprintf('%s must be a boolean');
							}
							break;
						// date and file are omitted for brevity
					}

				}

			}
		}

		// Finally, return the errors
		return $errors;
	}
```

这是非常简单的事情——注意，为了保持简单，我省略了某些参数类型——但是这可以用来处理查询参数，并拒绝那些参数不符合我们的 RAML 文件中的规范的请求。

最后，`handleRoute()`功能:

```
/** 
	 * Return a response for the given route
	 * 
	 * @param  array 	$route  The current route definition, taken from RAML
	 * @param  array 	$vars   An optional array of URI parameters
	 * @return Response
	 */
	public function handleRoute($route, $vars)
	{
		// Create a reponse
		$response = new Response(200);	

		// Return an example response, from the RAML
		$response->setBody($route['response']->getResponse(200)->getExampleByType('application/json'));

		// And return the result
		return $response;

	}
```

我们在这里做的是从适当的路由中提取示例，然后将它作为状态代码为 200 的响应返回。

此时，您可以在您的单元测试中使用`RamlApiMock`。然而，通过一个简单的添加，我们也可以将这个模仿组件作为 web 服务提供，只需用一些简单的路由逻辑包装对它的调用。

为此，创建一个包含以下内容的`index.php`文件:

```
<?php
require_once 'vendor/autoload.php';

use Sitepoint\RamlApiMock;

// The RAML library is currently showing a deprecated error, so ignore it
error_reporting(E_ERROR | E_WARNING | E_PARSE | E_NOTICE);

// Create the router
$router = new RamlApiMock('./test/fixture/api.raml');

// Handle the route
$response = $router->dispatch($_SERVER['REQUEST_METHOD'], $_SERVER['REQUEST_URI']);

// Set the HTTP response code
http_response_code($response->status);

// Optionally set some response headers
if (count($response->headers)) {
	foreach ($response->headers as $name => $value) {
		header(sprintf('%s: %s', $name, $value));
	}
}

// Print out the body of the response
print $response->body;
```

希望这是不言自明的；我们正在实例化我们的“路由器”，处理请求的 URL 和方法组合，然后发送回带有适当状态代码和任何头的响应。

现在，使用以下命令运行服务器:

```
php -S localhost:8000
```

因为 API 差别很大，所以您可能需要修改这个示例以适合您的实现。希望它能给你足够的时间开始。

## 摘要

在本文中，我从测试和模仿 API 的角度研究了 RAML。

因为 RAML 提供了一个关于 API 应该如何运行的明确而全面的声明，所以它对于测试和提供模拟响应都非常有用。

使用 RAML 还可以做更多的事情，这些例子只是触及了 RAML 如何用于测试的表面，但是希望我已经为您提供了一些想法。

## 分享这篇文章