# 用 Slim 编写 RESTful Web 服务

> 原文：<https://www.sitepoint.com/writing-a-restful-web-service-with-slim/>

通过 SitePoint 的一系列文章,您已经了解了什么是 REST 以及它是如何工作的。在本文中，我将向您展示如何使用 Slim 创建一个 RESTful web 服务，Slim 是一个 PHP 微框架，灵感来自于一个 Ruby 框架 Sinatra。它非常适合创建简单的 RESTful web 服务，并且附带了一些最少的组件，如路由器、请求、响应和视图。它非常简单，易于理解和使用。

## 向苗条世界问好

要开始使用 Slim，您需要先下载它。它需要 PHP 5.2+并允许你以过程式或 5.3+式编写应用程序，例如使用匿名函数。在本文中，我将对所有示例使用 5.3 风格的编码，假设您已经熟悉它。

要创建 Hello World 应用程序，请删除应用程序目录中的默认`index.php`文件，并使用以下代码创建一个新的`index.php`文件:

```
<?php
require "Slim/Slim.php";

// create new Slim instance
$app = new Slim();

// add new Route 
$app->get("/", function () {
    echo "<h1>Hello Slim World</h1>";
});

// run the Slim app
$app->run();
```

您的第一个 Slim 应用程序现在已经准备好了。如果你通过浏览器访问`index.php`，你应该会看到一个大大的“Hello Slim World”

要在您的应用程序中使用 Slim，您需要包含`Slim.php`，Slim 将自动加载它需要的所有其他文件。然后，您可以创建一个或多个`Slim`对象的实例，并添加您的路线。

Slim 构造函数接受一组应用程序配置值。`MODE`、`TEMPLATES.PATH`、`VIEW`是我们经常使用的一些重要配置。使用`MODE`设置要使用的应用环境，如开发或生产。`TEMPLATES.PATH`设置模板文件的位置。默认情况下，Slim 使用`Slim_View`来呈现视图，但是您可以编写定制的视图处理程序，并通过使用`VIEW`值将它们附加到 Slim。下面的例子展示了如何使用自定义的`TEMPLATES.PATH`创建一个新的 Slim 实例，并将环境设置为开发模式。

```
<?php
$app = new Slim(array(
    "MODE" => "development",
    "TEMPLATES.PATH' => "./templates"
));
```

使用 Slim 创建应用程序最重要的部分是创建路线。路由有助于将 URI 映射到特定请求方法的回调函数。Slim 提供了一种简单直观的方法来将相同的 URIs 映射到不同的请求方法。它将调用与当前 URI 和请求方法匹配的第一个回调函数，如果无法匹配，将产生 404 错误。添加路线后，您需要调用`Slim`实例上的`run()`方法来运行应用程序。

## 编写图书馆服务

作为更深入的练习，让我们使用 Slim 创建一个简单的图书馆管理 web 服务应用程序。在这个应用程序中，我们将能够使用 web 服务调用来列出、添加、删除和更新图书详细信息。

下表列出了 web 服务将支持的端点:

对于数据库交互，我将使用 [NotORM](http://www.notorm.com/ "NotORM - PHP library for simple working with data in the database") ，这是一个由 [Jakub Vrána](http://www.vrana.cz/ "Jakub Vrána") 编写的 PHP 库，作为 ORM 的替代，它提供了一个简单直观的 API 来处理数据库中的数据。NotORM 使用 PHP 的 PDO 扩展来访问数据库，因此一个`PDO`实例被传递给`NotORM`的构造函数。

```
<?php
require "NotORM.php";

$pdo = new PDO($dsn, $username, $password);
$db = new NotORM($pdo);
```

## 列出书籍

第一个端点列出了图书馆中的所有书籍，所以让我们使用 Slim 来创建端点并返回以 JSON 格式编码的数据。

```
<?php
...
$app = new Slim(
    "MODE" => "developement",
    "TEMPLATES.PATH' => "./templates"
);

$app->get("/books", function () use ($app, $db) {
    $books = array();
    foreach ($db->books() as $book) {
        $books[]  = array(
            "id" => $book["id"],
            "title" => $book["title"],
            "author" => $book["author"],
            "summary" => $book["summary"]
        );
    }
    $app->response()->header("Content-Type", "application/json");
    echo json_encode($books);
});
```

是一个在给定 URI 上路由到 GET 请求的 Slim 方法。它的第一个参数是 URI，最后一个参数是回调函数。`use`关键字使我们能够从匿名函数的范围内访问外部变量。

在这个函数中，我们通过遍历数据库返回的每个记录来创建一个书籍数组(`$db->books()`返回一个对`books`表的可遍历引用)。响应的 Content-Type 头作为“application/json”发送，我们发出图书数据的编码数组。

现在让我们用给定的 ID 编写获取一本书的细节的端点:

```
<?php
...
$app->get("/book/:id", function ($id) use ($app, $db) {
    $app->response()->header("Content-Type", "application/json");
    $book = $db->books()->where("id", $id);
    if ($data = $book->fetch()) {
        echo json_encode(array(
            "id" => $data["id"],
            "title" => $data["title"],
            "author" => $data["author"],
            "summary" => $data["summary"]
            ));
    }
    else{
        echo json_encode(array(
            "status" => false,
            "message" => "Book ID $id does not exist"
            ));
    }
});
```

这里我们给`Route`添加了一个参数，用于传递书的 ID。在执行这个路由时，Slim 将调用回调函数，并将参数值作为参数。

请注意，该参数是必需的。你可以把它放在一个圆括号里，使它成为可选的，比如:`/book(/:id)`但是，如果你把一个参数设为可选的，你将不能为回调函数指定参数。在这种情况下，您可以利用`func_get_args()`函数将任何参数传递给回调函数。

## 添加和编辑图书

现在让我们来处理负责添加和更新图书信息的端点。我们将使用`post()`方法添加新数据，使用`put()`更新现有数据。

```
<?php
...
$app->post("/book", function () use($app, $db) {
    $app->response()->header("Content-Type", "application/json");
    $book = $app->request()->post();
    $result = $db->books->insert($book);
    echo json_encode(array("id" => $result["id"]));
});

$app->put("/book/:id", function ($id) use ($app, $db) {
    $app->response()->header("Content-Type", "application/json");
    $book = $db->books()->where("id", $id);
    if ($book->fetch()) {
        $post = $app->request()->put();
        $result = $book->update($post);
        echo json_encode(array(
            "status" => (bool)$result,
            "message" => "Book updated successfully"
            ));
    }
    else{
        echo json_encode(array(
            "status" => false,
            "message" => "Book id $id does not exist"
        ));
    }
});
```

`$app->request()`返回带有 POST 或 PUT 数据的当前请求对象(`Slim_Http_Request`)。您可以使用该对象的`post()`方法获取 POST 值，使用`put()`方法获取 PUT 值。这里我们假设 POST 和 PUT 数据都是信息的键/值对，表列名作为键。在现实世界的应用程序中，您将需要添加一些验证和错误处理，但是为了简单起见，我在这里省略了它。

如果您计划从浏览器访问您的 Slim 应用程序，您将无法轻松地发出 PUT 请求，因为浏览器通常不会通过 HTML 公开该方法。为了克服这个问题，Slim 提供了一个条款，允许您通过在表单中放置一个隐藏字段来覆盖 POST 请求。字段名应该是“_METHOD”，值应该设置为“PUT”。

```
<form action="#" method="post">
 <input type="hidden" name="_METHOD" value="PUT">
 Title: <input type="text" name="title"><br>
 Author: <input type="text" name="author"><br>
 Summary: <textarea name="summary"></textarea>
 <br>
 <input type="submit" value="Submit">
</form>
```

## 删除书籍

既然我们已经有了在 web 服务中添加、编辑和列出图书的端点，接下来我们需要的显然是删除图书的端点。它应该接受要删除的图书的 ID，并从数据库中删除相应的记录。

```
<?php
...
$app->delete("/book/:id", function ($id) use($app, $db) {
    $app->response()->header("Content-Type", "application/json");
    $book = $db->books()->where("id", $id);
    if ($book->fetch()) {
        $result = $book->delete();
        echo json_encode(array(
            "status" => true,
            "message" => "Book deleted successfully"
        ));
    }
    else{
        echo json_encode(array(
            "status" => false,
            "message" => "Book id $id does not exist"
        ));
    }
});
```

这里的一切都很简单。首先，我们从数据库中获取对应于给定 ID 的行，就像我们之前获取图书详细信息时所做的那样。对 row 对象调用`delete()`方法将从数据库中删除该记录。

我们已经创建了所有与书籍相关的必要端点。在某些情况下，您可能希望有一个单独的路由来响应多个请求方法。它可以使用 Slim 的`map()`方法实现，这里的[解释的是](http://www.slimframework.com/documentation/stable#routing-generic "Stable Branch Documentation - Slim Framework for PHP 5")。

## 摘要

在本文中，我们讨论了使用 Slim 框架创建 RESTful web 服务。现在，您应该能够毫不费力地创建自己的 web 服务应用程序了。

当然，除了这里讨论的简单事情，你还可以做更多的事情。您可以拥有包含许多参数、数据验证等的路线。所以深入研究它，使用像 Slim 和 NoORM 这样的工具来帮助你实现你的目标。

你可以从 GitHub 下载这篇文章的[源代码。](https://github.com/phpmasterdotcom/WritingARESTfulWebServiceWithSlim "phpmasterdotcom/WritingARESTfulWebServiceWithSlim")

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章