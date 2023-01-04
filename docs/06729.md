# 光环。Web: Aura 的 MVC 页面控制器

> 原文：<https://www.sitepoint.com/aura-web-auras-page-controller-for-mvc/>

MVC 是一个首字母缩写词，意思是模型-视图-控制器。在本教程中，我想向你介绍光环。Web，Aura 库的控制器组件。在这里，我将向您展示如何使用它来构建您自己的控制器，以及如何使用一个响应传输对象来发出 HTTP 响应头，并集成一个像 Mustache 这样的模板引擎来呈现视图。

## 体系结构

控制器是页面的入口点，与视图和模型组件通信。光环。Web 帮助我们轻松地构建遵循页面控制器设计模式的控制器。

一个应用程序的每一页通常都是独一无二的。Web 提供了`AuraWebControllerAbstractPage`类，它提供了我们可以扩展和使用的基本功能。但是为了创建一个扩展`AbstractPage`类的对象，我们需要提供一些依赖，通常是通过构造函数。依赖关系是:

1.  代表环境的`AuraWebContext`类的实例。
2.  帮助检索接受头的一个`AuraWebAccept`类的实例。
3.  创建响应传输对象的`AuraWebResponse`的实例。
4.  `AuraWebSignalInterface`的一个实现，它是一个执行钩子的信号管理器(类似于一个观察器/事件处理器)。
5.  结合我们的渲染系统的一个实现。
6.  我们可以在其中传递动作方法的参数数组。

`Context`类接受一组全局值，在大多数情况下是`$GLOBALS`。从控制器内部，我们可以使用`Context`对象作为`$this->context`，并且可以分别使用对象的方法`getQuery()`、`getPost()`、`getFiles()`、`getInput()`和`getJsonInput()`来访问`$_GET`、`$_POST`、`$_FILES`、原始`php://input`和 JSON 解码值。我们还可以检查请求是通过`isGet()`的 GET、通过`isPut()`的 PUT 还是通过`isXhr()`的 Ajax 调用发出的。

让我们假设一个对 *http://localhost/？name=Hello* 制作完成。从控制器中获取 name 参数值的一个例子是:

```
<?php
$this->context->getQuery('name', 'default value');
```

`getQuery()`的第二个参数是可选的；它指定在实际值为空时返回的默认值。

`Accept`类接受一组`$_SERVER`信息。之所以不只是在构造函数中硬编码，是为了让我们可以灵活地通过我们喜欢的测试等等。该对象也可以在我们的控制器中使用`$this->accept`。它的方法通过`getContentType()`将接受的媒体类型作为数组提供给我们，通过`getCharset()`提供给我们字符集，通过`getEncoding()`提供给我们编码，通过`getLanguage()`提供给我们语言。该操作的一个基本示例是:

```
<?php
$this->accept->getContentType();
```

请注意，将返回一个键/值数组，类似于:

```
Array
(
    [text/html] => 1
    [application/xhtml+xml] => 1
    [application/xml] => 0.9
    [*/*] => 0.8
)
```

您可能熟悉使用 PHP 的`header()`函数向发送回客户端的 HTTP 响应添加值。相反，一个`Response`对象被用作 web 响应传输对象。该对象保存我们可以传递的值，稍后使用 Aura.Http 之类的工具将其转换为正确的 HTTP 响应。

`Response`对象也通过`$this->getResponse()`在控制器中可用。该对象让我们通过`setContent()`设置响应的主体内容，通过`setHeader()`设置 HTTP 头值，通过`setCookie()`设置 cookies，通过`setRedirect()`设置重定向头。我们还可以通过`setStatusCode()`设置 HTTP 状态代码，通过`setStatusText()`方法设置状态文本。

下面是扩展和使用一个`AbstractPage`对象的样子:

```
<?php
namespace SitePointTutorialWebController;
use AuraWebControllerAbstractPage;

class Index extends AbstractPage
{
    public function actionGreet() {
        $this->response->setContent(
            '<html>' . 
            '<head><title>Aura web controller</title></head>' .
            '<body>Hello World!</body>' . 
            '</html>'
        );
    }
}
```

```
<?php
use AuraWebContext;
use AuraWebAccept;
use AuraWebResponse;
use AuraWebSignal;
use AuraWebRendererNone as Renderer;
use SitePointTutorialWebControllerIndex;

$page = new Index(
    new Context($GLOBALS),
    new Accept($_SERVER),
    new Response(),
    new Signal(),
    new Renderer(),
    [
        'action' => 'greet',
    ]
);
$response = $page->exec();
echo $response->getContent();
```

作为最后一个参数传递给扩展的`AbstractPage`的参数数组指定了需要调用哪些动作，需要将哪种格式传递给呈现策略，以及动作方法的任何其他参数。

在由`exec()`启动的执行周期中，调用以下内容:

1.  `pre_exec`，一个调用页面的`preExec()`方法的钩子。
2.  `pre_action`，一个调用`preAction()`方法的钩子。
3.  `action()`找到并调用 action 方法(它实际上创建了一个反射类来检索方法的参数，然后调用它)。
4.  `post_action`，一个调用`postAction()`方法的钩子。
5.  `pre_render`，一个调用`preRender()`方法的钩子。
6.  `render()`渲染视图。
7.  `post_render`，一个调用`postRender()`方法的钩子。
8.  `post_exec`，一个调用`postExec()`方法的钩子。

## 翻译

在上面的例子中，我们在控制器中显式地设置了内容，但是这并不是组织代码的最佳方式。视图应该是分开的。光环。Web 默认不提供渲染策略，所以很容易集成我们喜欢的任何渲染策略。这里我会用小胡子。

为了创建一个渲染策略，我们需要扩展`AuraWebRendererAbstractRenderer`类，其中我们定义了`exec()`方法。通过`$this->controller`，我们可以在渲染策略中使用控制器。

```
<?php
namespace SitePointFrameworkWebRenderer;
use AuraWebRendererAbstractRenderer;

class Mustache extends AbstractRenderer
{
    protected $mustache;

    public function __construct($mustache) {
        $this->mustache = $mustache;
    }

    public function exec() {
        $format = $this->controller->getFormat();
        if (! $format) {
            $format = '.html';
        }
        $response = $this->controller->getResponse();
        if (!$response->getContent()) {
            $data    = (array)$this->controller->getData();
            $view    = strtolower($this->controller->getAction());
            $lastval = basename(
                str_replace('\', '/', strtolower(
                    get_class($this->controller)
                ))
            );
            $file =  $lastval . '/' . $view . $format;
            $response->setContent(
                $this->mustache->render($file, $data)
            );
        }
        $response->setContentType($this->getContentType($format));
    }

    public function getContentType($format) {
        $mimetypes = [
            '.json' => 'application/json',
            '.xml'  => 'application/xml',
            '.html' => 'text/html',
            '.htm'  => 'text/html'
        ];
        return array_search($format, $mimetypes);
    }
}
```

我假设我们使用惯例`*<controller name>*/*<action name>*.*<format>*`保存所有的小胡子模板，其中文件夹镜像`*<controller name>*`并且`*<action name>*.*<format>*`是模板的文件名。例如，带有 hello 动作的控制器类示例会在`example/hello.*<format>*`中找到它的模板。

## 构建 HTTP 响应

我们仍然没有构建一个合适的 HTTP 响应，所以现在让我们看看如何做。一旦我们用它的`exec()`方法调用控制器的执行周期，我们将得到一个响应传输对象。

该对象包含 HTTP 状态代码、状态文本、cookies 和头值。我们可以用类似下面给出的代码从它构建 HTTP 响应:

```
<?php
$response = $page->exec();

// header('Status: 200 OK');
$statusCode = $response->getStatusCode();
$statusText = $response->getStatusText();

$response->getVersion();
$headers = $response->getHeaders();
foreach ($headers as $header => $value) {
    // header('Content-Type: text/html; charset=utf-8');
    header($header . ': ' . $value);
}

$cookies = $response->getCookies();
foreach ($cookies as $name => $cookie) {
    setcookie(
        $name,
        $cookie['value'],
        $cookie['expire'],
        $cookie['path'],
        $cookie['domain'],
        $cookie['secure'],
        $cookie['httponly']
    );
}

$contentType = $response->getContentType();
if (!$contentType) {
    $contentType = 'text/html; charset=utf-8';
}
header('Content-Type: ' . $contentType);
echo $response->getContent();
```

我只演示了一个纯 PHP 实现，所以每个人都很容易理解，但理想情况下，我们会使用类似 Aura 的东西。Http 或其他为我们提供必要功能的库。

## 结论

在这篇文章中，我介绍了 Aura 的基本工作原理。还展示了我们如何集成呈现策略以及如何构建适当的 HTTP 响应。你可以使用 Aura 这样的路由库。我前面讨论过的路由器来动态调用控制器。也许在以后的文章中，我会展示如何整合所有这些，并从 Aura 组件构建您自己的框架。敬请期待！

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章