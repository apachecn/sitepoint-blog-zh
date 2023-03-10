# Symfony 微框架 Silex 简介

> 原文：<https://www.sitepoint.com/introduction-silex-symfony-micro-framework/>

Silex 是一个基于 Symfony 组件的 PHP 微框架，灵感来自于 [Sinatra](http://www.sinatrarb.com/) Ruby 框架。在本文中，我们将从这个框架开始，看看它如何满足我们的需求。

![Logo](img/c7272807c336d86be5aa7968d0196972.png)

## 装置

安装 Silex 的最佳和推荐方式是通过 composer:

```
// composer.json
{
    "require": {
        "silex/silex": "1.3.*@dev",
        "twig/twig": "1.17.*@dev"
    },
    "require-dev": {
        "symfony/var-dumper": "dev-master"
    }
}
```

运行`composer update --dev`加载依赖项并生成自动加载程序。我们还需要`twig`,因为我们想用它作为我们的模板引擎，并将 Symfony 的新`var-dumper`作为开发依赖——在这里阅读更多关于它的[。](https://www.sitepoint.com/var-dump-introducing-symfony-vardumper/)

## 创建文件夹结构

我喜欢 Silex 的一点是，它给了你一个基本框架，你可以用任何你想要的方式组织它。

```
|-app/
|----config/
|-resources/
|----views/
|----logs/
|-src/
|----MyApp/
|-public/
|----index.php
|----.htaccess
|-vendor/
|-composer.json
```

例如，我不喜欢我的根应用程序文件夹被称为`web`，我更喜欢普通的`public`文件夹。`src`目录是我们放置应用程序特定代码的地方，而其他文件夹是不言自明的。

我们的`public/index.php`文件将创建一个新的`Silex\Application`，它是我们的应用程序容器实例，这是我们将要连接组件的地方。

```
// public/index.php
<?php

require_once __DIR__ . '/../vendor/autoload.php';

$app = new Silex\Application();

$app->run();
```

现在，如果您点击您的根应用程序 URL，您应该会看到一个*页面未找到*错误。您可以通过将容器中的`debug`模式设置为`true`来打开调试。

如果您在访问路由时遇到问题，请确保您的服务器文档根目录指向公用文件夹。你可以查看[文档](http://silex.sensiolabs.org/doc/web_servers.html)以获得更多关于配置你的网络服务器的信息。

```
// public/index.php

//...

$app['debug'] = true;

$app->run();
```

现在，如果我们再次尝试，我们会得到一个描述性的`NotFoundHttpException`，因为我们没有注册任何路由。

## 按指定路线发送

注册路由很简单，只需将 URL 模式映射到控制器函数。

```
// public/index.php
$app->get('/', function(){
	return "Hello world";
});
```

我们可以处理`get`、`post`、`put`和`delete`，或者我们可以使用`match`方法来处理任何请求方法。句柄函数必须返回一个字符串或一个`Symfony\Component\HttpFoundation\Response`实例。

```
// public/index.php
$app->get('/', function(){
	return new Symfony\Component\HttpFoundation\Response("Hello world");
});
```

### 路由参数

```
$app->get("/users/{id}", function($id){
   return "User - {$id}";
});
```

我们可以向 URL 模式添加多个参数，唯一的约定是 URL 模式参数的名称必须与函数中使用的名称相匹配，否则您将得到一个`RuntimeException`。您还可以使用`assert`方法指定一个 URL 模式，并使用`value`方法指定一个默认值。

```
$app->get("/users/{id}", function($id){
   return "User - {$id}";
})
    ->value("id", 0) //set a default value
    ->assert("id", "\d+"); // make sure the id is numeric
```

我最喜欢的路由方法之一是`convert`:它允许我们拦截请求，并在将请求传递给回调函数之前更改参数值。

```
$app->get("/users/{user}", function($user){
    // return the user profile

    return "User {$user}";
})->convert("user", function($id){
        $userRepo = new User();
        $user = $userRepo->find($id);

        if(!$user){
            return new Response("User #{$id} not found.", 404);
        }

        return $user;
    });
```

在这个例子中，`convert`方法获取一个用户 id，查找数据库并返回用户。如果找不到用户，则返回 404 响应。

如果你是 Laravel 的粉丝，你习惯了`auth`、`csrf`、`guest`这样的滤镜。然而，在 Silex 中，您可以提供一个回调函数来像过滤器一样工作。

```
$app->get("/users/{user}", function($user){
    // return the user profile

    return "User {$user}";
})->before(function($request, $app){
    // redirect if the user is not logged in
})
->after(function($request, $response){
    // log request events
})
->finish(function(){
    // log request event
});
```

同样，您可以使用`after`和`finish`方法。但是请记住，`finish`方法不能访问`request`和`response`，因为响应已经发送给用户了。

### 命名路线

当处理多个路由时，用描述性的方式命名它们是有意义的。这在更新 URL 格式或生成模板链接时会很有帮助。

```
$app->get("/users/list", function(Silex\Application $app){
    return "List of users";
})->bind('users');
```

如果您使用的是 [URL 生成器服务提供商](http://silex.sensiolabs.org/doc/providers/url_generator.html)，您可以生成一个直接指向该路线的链接。

```
<a href="{{ app.url_generator.generate('users') }}">Users</a>
```

## 控制器

在实际应用中，我们不使用闭包进行路由，而是创建单独的控制器类来处理请求。

```
$app->get("/", "MyApp\Controller\HomeController::index");
```

### 分组控制器

使用类控制器的主要好处之一是能够对它们进行分组。当创建 RESTful API 时，URL 应该是这样的:

*   `/users`
*   `/users/id`【上传，删除】
*   `/users/id/edit`

处理这个问题的一个非常干净的方法是将控制器分组到一个叫做控制器提供者的东西中。我们的`User`控制器提供者必须实现`ControllerProviderInterface`并定义`connect`方法。

```
// src/MyApp/Controller/Provider/User.php

class User implements ControllerProviderInterface{

    public function connect(Application $app)
    {
        $users = $app["controllers_factory"];

        $users->get("/", "MyApp\\Controller\\UserController::index");

        $users->post("/", "MyApp\\Controller\\UserController::store");

        $users->get("/{id}", "MyApp\\Controller\\UserController::show");

        $users->get("/edit/{id}", "MyApp\\Controller\\UserController::edit");

        $users->put("/{id}", "MyApp\\Controller\\UserController::update");

        $users->delete("/{id}", "MyApp\\Controller\\UserController::destroy");

        return $users;
    }

}
```

`$app['controllers_factory']`返回一个新的`Silex\ControllerCollection`来保存我们的路由集合。`UserController`将处理注册的请求。

```
// src/MyApp/Controller/UserController.php

class UserController{

    public function index(){
        // show the list of users
    }

    public function edit($id){
        // show edit form
    }

    public function show($id){
        // show the user #id
    }

    public function store(){
        // create a new user, using POST method
    }

    public function update($id){
        // update the user #id, using PUT method
    }

    public function destroy($id){
        // delete the user #id, using DELETE method
    }
}
```

唯一剩下的部分是将我们的控制器集合附加到我们的应用程序。当处理路由时，我更喜欢 Laravel 的方法，将它们注册在一个单独的文件中并包含它们。

```
// app/routes.php

$app->mount("/users", new \MyApp\Controller\Provider\User());
```

`mount`方法将前缀和我们的`User`提供者类作为参数。

此外，使用控制器集合的好处之一是能够使用`before`、`after`和`finish`过滤器，而不必在每条路线上调用它们。

```
// src/MyApp/Controller/Provider/User.php

class User implements ControllerProviderInterface{

    public function connect(Application $app)
    {
        //...
        $users->before(function(){
		// check for something here
	});
    }
}
```

## 提供者

我们之前提到过这个术语，但它只是代表了一个将组件绑定到 Silex 应用程序的小类。有一个预先包含的提供者列表，要使用它，您只需将它注册到应用程序实例。

```
// app/providers.php
$app->register(new Silex\Provider\TwigServiceProvider(), array(
    'twig.path' => __DIR__.'/../views',
));
```

当然，这应该在您的 composer 中需要 Twig 并更新依赖关系之后进行。现在，如果你访问`TwigServiceProvider::register`方法，你可以看到我们可以从容器中访问`Twig_Environment`。

```
$app->get(function(){
   return $app['twig']->render('home.twig');
});
```

要为 Silex 创建自己的提供者，您需要实现`Silex\ServiceProviderInterface`并定义`register`方法。你可以在[文档](http://silex.sensiolabs.org/doc/providers.html#creating-a-provider)中了解更多。

## 结论

Silex 是 Symfony 粉丝的一个小而快的框架。这篇介绍的目的是让你开始尝试这个框架。我们没有涵盖所有内容，但希望我可以让你出去试一试。如果你有任何问题或意见，请在评论中告诉我！

## 分享这篇文章