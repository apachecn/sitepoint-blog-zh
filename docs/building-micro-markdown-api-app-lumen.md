# 使用 Lumen 构建微型 Markdown API 应用程序

> 原文：<https://www.sitepoint.com/building-micro-markdown-api-app-lumen/>

如果您使用 Laravel 已经有一段时间了，您会知道对于一个小的应用程序或服务来说，它有时感觉有点沉重。出于同样的目的，Laravel 的创造者泰勒建造了 Lumen。一个小的[微框架](http://en.wikipedia.org/wiki/Microframework)建立在 Laravel 之上，照亮组件，默认情况下它不会加载所有的组件，如雄辩、刀片、中间件等，在它启动时保持光亮。我们将在这个简短的教程中探索所有这些。

![Lumen Header](img/e179d1aa3c3f1db6b5b573fa1d26d935.png)

## 为什么是卢蒙？

如果你已经是 Laravel 的用户，你可能会爱上 Laravel 的语法和快捷方式。但是，当处理一个小的应用程序时，你不希望有一个完整的栈框架，把所有的包都连接起来。Lumen 为您的小应用程序提供了一个很好的起点，去掉了您可能不需要的所有部分。

## 我们正在建造的东西

为了说明微框架的实际用例，我们将创建一个 Markdown 解析器 API 应用程序，用户可以在其中提交一个 Markdown 文本，并以 JSON 的形式获取解析后的内容。我将使用 PHP 联盟的 [league/commonmark](https://github.com/thephpleague/commonmark) 包。你可以在 [Github](https://github.com/Whyounes/LumenDemoApp) 上查看最终结果。

## 装置

如果您经常创建新的应用程序，您可以使用全局安装程序(`composer global require "laravel/lumen-installer=~1.0"`)并利用像`lumen new my-project`这样的短语法，或者您可以使用 composer 通过`composer create-project laravel/lumen my-project --prefer-dist`创建一个新项目。你可以在[文档](http://lumen.laravel.com/docs/installation#install-lumen)中了解更多关于安装过程的信息。

安装好 Lumen 之后，您可以继续使用`composer require league/commonmark`请求`commonmark`包。访问[文档](http://commonmark.thephpleague.com/)获取更多信息。

## 文件夹结构

安装 Lumen 后，您首先会注意到的是文件夹结构。很多东西都被去掉了，比如`config`、`database`和`resources`文件夹，在需要的时候可以使用一些脚手架命令或者手动地恢复。

Lumen 为您提供了`php artisan make`命令来帮助您搭建丢失的文件夹。如果你想使用视图模板，你可以运行`php artisan make views`为你的模板创建`resources/views`文件夹。可用的命令可以在`Laravel\Lumen\Console\Commands\MakeCommand`类中找到。

*   `php artisan make foundation`:搭建`resources`和`database`文件夹。
*   `php artisan make resources`:搭建`resources`文件夹。
*   `php artisan make database`:搭建`database`文件夹。
*   `php artisan make lang`:搭建`resources/lang`文件夹。
*   `php artisan make views`:搭建`resources/views`文件夹。

## 解析降价

```
// app/Http/routes.php

$app->get('/parse', 'App\Http\Controllers\MarkdownController@parse');
```

```
// app/Http/Controllers/MarkdownController.php

public function parse(Request $request, CommonMarkConverter $parser)
{
    $text = $request->get('text');
    $result = $parser->convertToHtml($text);

    return ['html' => $result];
}
```

在用户向我们的`/parse`端点提交 Markdown 文本之后，我们将从请求对象中检索内容并解析它。您注意到我们利用服务容器类型提示来解析 markdown 和 request 对象。如果你决定使用 Facades ( `Request::get('text')`)，确保告诉你的应用程序加载它们(见下一节)。

![HTML result](img/dc1fc878676e9f5e7508d17c1b122375.png)

## 门面，雄辩和中间件

因为 Facades、雄辩的 ORM 和中间件通常在 Laravel 应用程序中使用，我们在默认情况下将它们添加到我们的`bootstrap/app.php`文件中，我们只需要在开始使用它们之前取消对它们的注释。

```
// bootstrap/app.php

$app->withFacades();
$app->withEloquent();
$app->middleware([
    //...
]);
```

如果您的应用程序需要包含其他服务，您可以使用`$app->register('AppServiceProvider');`语法来注册提供者列表。

一般来说，大多数 Lumen 部件的工作方式与 Laravel 中的相同，它们要么默认不加载，要么需要安装并连接到应用程序中。例如，您可以照常使用 Laravel 文件系统包(`Storage::disk('local')->put('file.txt', 'Content here');`)，它将按预期工作。但是，如果您想使用 Rackspace，您需要使用 Composer 来提取`league/flysystem-rackspace`包，并在`.env`文件中指定您的凭证。

## 使用 API

在我们的服务启动并运行之后，我们可以使用 [Guzzle](http://guzzle.readthedocs.org/en/latest/) 或任何其他您喜欢的包或方法来消费我们的 API。

```
$client = new GuzzleHttp\Client();
$response = $client->get('http://vaprobash.dev/parse', [
    'query' => ['text' => file_get_contents('myFile.md')]
])->json();
$html = $response->html;
```

如果您的服务的一部分流量很大，那么使用轻量级微框架来处理应用程序的这一部分是有意义的，而不必加载所有的域登录和包来完成一个简单的任务。

## 结论

Lumen 将带回一些 Laravel 的粉丝，他们不喜欢使用完整的框架来完成小任务。您可以查看[文档](http://lumen.laravel.com/docs/introduction)了解更多信息。如果你已经试用过 Lumen，我很想知道你对它的第一印象，以及你认为它比其他微框架有什么优势？

## 分享这篇文章