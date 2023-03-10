# Laravel 4 到 Laravel 5——简单的升级指南

> 原文：<https://www.sitepoint.com/laravel-4-laravel-5-simple-upgrade-guide/>

Laravel 5 已经出来了，但对变化的恐惧正在带走每个人。我们不断听到人们抱怨一些激进的变革。比如，为什么是这种新的文件夹结构？如果我做一个`composer update`，我的应用程序会崩溃吗？

在本文中，我们将研究如何将您现有的 Laravel 4 应用程序迁移到 Laravel 5，并理解新的文件夹结构。

![Laravel Logo](img/b24fd9db18c1157e55823b1b5d7e1e96.png)

## 装置

我现有的 Laravel 4 应用程序是以前一篇关于使用谷歌分析 API 的文章中的一个[演示](https://github.com/sitepoint-editors/laravel_google_analytics)。这个应用程序没有太多的代码，但是它可以完成我们教程中的工作。

让我们首先在我们的计算机上安装 Laravel 5，并创建一个临时文件夹来保存我们的 Laravel 4 版本的应用程序。

```
composer create-project laravel/laravel --prefer-dist
```

我更喜欢通过 composer 安装 Laravel，但是您可以访问[文档](http://laravel.com/docs/5.0)来阅读更多关于 Laravel 安装程序的信息。

你可以使用储存库中的流浪者盒子，或者使用[家园强化](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。如果一切顺利，您应该会看到 Laravel 5 的欢迎页面。

## 配置文件

旧的`app/config`文件夹现在位于您的应用程序的根目录下，因此我们必须将我们的`app/config/analytics.php`移动到`config/analytics.php`。凭证直接粘贴到文件中，所以为什么不使用环境变量呢？

```
// config/analytics.php

return [
  'app_name'          => env('app_name'),
  'client_id'         => env('client_id'),
  'client_secret'     => env('client_secret'),
  'api_key'           => env('api_key')
];
```

```
// .env

app_name='YOUR APP NAME'
client_id='YOUR CLIENT ID'
client_secret='CLIENT SECRET'
api_key='API KEY'
```

`.env`文件是自动加载的，可以用来将您的本地环境配置与生产、测试等分开。

## 按指定路线发送

Laravel 4 路线在`app/routes.php`内注册。在 Laravel 5 中，所有与 HTTP 相关的东西都被分组到`app/Http`文件夹下，包括路由，所以让我们把`app/routes.php`移到`app/Http/routes.php`。

### 过滤

Laravel 5 已经从过滤器转移到了中间件，所以如果你的路线中有任何过滤器，请确保你换到了中间件。

```
Route::get(‘/report’, [‘middleware’ => ‘auth’, function()
{
    //
}]);
```

如果你有一个定制的过滤器，你可以把它移植成一个中间件。我有一个`GoogleLogin`中间件，我正在我的路线中使用，实现如下。

```
// app/Http/Middleware/GoogleLogin.php

class GoogleLogin
{
  public function handle($request, Closure $next)
  {
    $ga = \App::make('\App\Services\GoogleLogin');
    if (!$ga->isLoggedIn()) {
      return redirect('login');
    }

    return $next($request);
  }

}
```

```
// app/Http/Kernel.php

protected $routeMiddleware = [
        'google_login'  => 'App\Http\Middleware\GoogleLogin',
];
```

```
// app/Http/routes.php

Route::any('/search', ['middleware' => 'google_login', 'as' => 'search', 'uses' => 'GoogleController@search']);
```

默认情况下会添加 CRSF 保护中间件。如果您想删除它，您可以转到`app/Http/Kernel.php`文件并注释掉适当的行。

## 控制器

因为我们的控制器被认为是 Http 逻辑的一部分，我们需要将我们的`app/controllers/*`移动到`app/Http/Controllers`，并用`App\Http\Controllers`命名它们。您需要解决的最后一个问题是将`BaseController`改为`Controller`类。

如果您不喜欢`App`根名称空间，您可以使用下面的 artisan 命令对其进行全局更改。

```
php artisan app:name MyApp
```

## 迁移

我们的 Google Analytics 应用程序没有任何本地数据库交互，但是升级过程值得一提。

`app/database`目录现在位于`/database`文件夹中，您只需要将文件移动到那里。该目录已经包含一个`users`和一个`password_resets`表，您可以根据需要删除或更新它们。

## 模型

Laravel 4 的 models 文件夹不见了，Laravel 5 把`User`模型直接放在了`app`文件夹里面作为例子。你也可以在那里复制你的模型，并用`App`命名它们。

然而，如果你不喜欢把你的模型放在那里，你可以在`app`目录下创建一个名为`Models`的新文件夹，但是不要忘记用`App\Models`命名空间来命名你的类。

```
namespace App\Models;

class User extends Eloquent {
	…
}
```

## 应用服务程序

我们的`src`文件夹包含一个`GA_Service`和一个`GA_Utils`类。如果我们认为它们是服务，我们可以把它们放在`app/Services`里面。否则，我们可以创建一个名为`app/GA`的新文件夹来存储我们的服务类。这将导致一个问题，因为我们一开始没有使用 PSR-4 自动加载，所以我们需要用正确的新名称空间更新控制器中的类引用。

## 视图

应用视图从`app/views`文件夹移动到`resources/views`文件夹。

`resources`文件夹还包含用于应用程序本地化的`lang`文件夹，以及用于前端资产的`assets`文件夹。Laravel 5 引入了[药剂](http://laravel.com/docs/5.0/elixir)，它使[大口](http://gulpjs.com/)任务运行器适应 Laravel 的开发环境。

## 设计者

确保复制应用程序的 composer 依赖项，并进行任何必要的升级。在我们的演示中，我将把我的`"google/apiclient": "1.1.*"`移动到新的`composer.json`，并做一个`composer update`来反映这些变化。

## 表单和 HTML

Laravel 5 上的默认安装中删除了`illuminate/html`包，您需要单独安装它。

要将 HTML 助手带回您的项目，您需要将`"illuminate/html": "5.0.*"`包添加到您的`composer.json`并运行`composer update`，然后您需要将`'Illuminate\Html\HtmlServiceProvider'`添加到您的`config/app.php`提供者数组。如果您想在您的刀片模板中使用`Html`和`Form`外观，您可以将以下外观添加到您的`config/app.php`外观数组中。

```
// config/app.php

'aliases' => [

	'Form'=> 'Illuminate\Html\FormFacade', 
	'HTML'=> 'Illuminate\Html\HtmlFacade',
],
```

## 结论

升级到 Laravel 5 的过程的复杂性和持续时间总是取决于您的应用程序的大小，对于您的特殊情况，这个过程可能会比这个例子长得多。在这篇文章中，我们试图解释通用过程，它应该处理大多数(如果不是所有)需要改变的事情。

您不会被强制升级到新的文件夹结构，您可以保留旧的文件夹结构，只更新您的 composer 依赖项，但这不是推荐的方法。如果您有任何问题或意见，请务必在下面发表。更多信息，请参见[完整版升级指南](http://laravel.com/docs/master/upgrade)。

## 分享这篇文章