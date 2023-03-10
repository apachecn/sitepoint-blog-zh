# Laravel 4 的新增功能

> 原文：<https://www.sitepoint.com/whats-new-in-laravel-4/>

Laravel 是一个使用 MVC 模式的 PHP 框架。当然，有许多这样的项目，我不打算在本文中将 Laravel 与其他框架进行比较。相反，我将与您分享 Laravel 的最新版本——Laravel 4 的新功能。

## 去耦组件

正如你在 GitHub 页面上看到的那样，Laravel 4 并没有包含太多的代码。实际上，app 项目只是注册了它使用的服务。这是 Laravel 4 最大的变化；不是将所有代码打包在一个存储库中，而是将功能分离在所谓的 Illuminate 组件中。app 项目只是使用 Composer 来管理我们从 retro Laravel 中熟悉的依赖项:控制器/路由系统、名为雄辩的对象关系映射器，以及使用刀片模板引擎的视图管理。它们现在都被组织成独立的组件，并使用[服务定位器](https://www.sitepoint.com/managing-class-dependencies-2/)进行解耦。

也许你没有被打动，也有可能你在问自己:“哇，就这样吗？有什么大不了的？”重要的是，通过真正的解耦，我们可以挑选出我们需要的照明组件，并在我们自己的项目中使用它们。所以，你只是在寻找一个好的 ORM，但是你不想要路由系统、视图、分页和邮件组件？没问题，你想用什么就用什么。你只需要你需要的，而不是整个“健壮”的框架。

## 创建一个“普通”的项目

对于想一如既往地使用完整框架的用户，只需检索这个库:【github.com/illuminate/app.git】T2。之后，[安装 Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) ，进入代码所在的目录，运行`composer update`。这将获得所有的 Laravel 依赖项。在`app`目录中，一切都与旧的 Laravel 相似——目录结构和文件——所以将 Laravel 3 项目更新到 Laravel 4 不会花费太多精力。

## 路线

就像以前一样，路线是在`routes.php`文件中定义的。您可以返回您想要的任何内容，尽管注意，对于控制器，Laravel 不再使用神奇的重命名。在 Laravel 3 中，您可以像这样调用控制器方法:

```
<?php
Route::get('/', 'home@index');
```

控制者的名字是`HomeController`。但是现在你必须使用:

```
<?php
Route::get('/', 'HomeController@index');
```

虽然这看起来不像是一个功能增强，但它确实是。例如，如果您将 route 组件用作独立的库，您就不会受到 Laravel 的命名约定的约束。

## 通过测试获得更好的代码

Laravel 以前版本的批评者认为它的代码永远无法达到高质量的状态。通过使用大量的静态方法来产生漂亮干净的代码，开发人员不得不牺牲项目的可测试性。主要开发人员(Taylor Otwell)在平衡简单代码和可测试性方面做得很好，但是代码永远不可能被完全测试。

那个时代现在结束了。通过使用 IoC 容器来注册所有的组件，一切都可以得到充分的测试。每个组件都有针对其大部分特性的测试，每次提交时，Travis CI 都会对这些测试进行验证。为了保持调用组件的简洁方式，Laravel 使用了[外观模式](http://en.wikipedia.org/wiki/Facade_pattern)。

## 容器及其绑定

当您创建一个 Laravel 项目时，您使用一个 IoC 容器来解析所有的依赖项。`Application`类是从`IlluminateContainer`扩展而来的。当您使用 facades 调用组件方法时，您仍然可以将东西绑定到容器，以使它们在整个应用程序中可用。例如:

```
<?php
App::bind('hello', function()
{
return "Hello to you, sir";
});

Route::get('/', function()
{
return App::make('hello');
});
```

这里我们绑定了一个函数，它将字符串“您好，先生”返回到容器中的“您好”。

该容器还允许我们在不查看其构造函数参数的情况下延迟绑定对象。这可以通过[反射](http://php.net/manual/en/book.reflection.php)来完成。例如:

```
<?php
Class writer
{
public function write()
{
return 'A good day to you, sir';
}
}

Class helloWorld
{
protected $writer;

public function __construct (Writer $writer)
{
$this->writer = $writer;
}

public function write()
{
return $this->writer->write();
}
}

Route::get('/', function()
{
return App::make('helloWorld')->write();
});
```

您可以看到，Laravel 通过创建一个新的`Writer`类并将其用作`helloWorld`类构造函数中的参数来自动解析我们的依赖关系。如果希望将特定的 writer 实例用作参数，也可以使用上一个示例中所示的方法将该对象绑定到容器。

## 改进的 CLI

Laravel 著名的 CLI(名为 Artisan)也得到了一些重大改进。使用 Symfony 的命令行组件，Artisan 现在表现出更友好的行为。例如，您可以通过执行`artisan list`获得所有可用的命令，并且可以获得关于这些命令及其作用的额外信息。

## 新的数据库特性

对于大多数 Laravel 用户将在他们的项目中使用的数据库组件，最显著的更新是数据库播种。如果你去你的`app/database`目录，你会看到一个名为`seeds`的目录。

为了向您展示这个特性，我们首先需要一个测试数据库表。因此，在`app/config/database.php`中设置您的连接细节，导航到根目录(在`app`之上)并执行`php artisan migrate:install`来安装迁移表。然后，运行`php artisan migrate:make create_comments_table --create="comments"`，创建一个名为`comments`的测试表。

查看`app/database/migrations`目录，您将在那里看到您的迁移。我们可以使用模式构建器，这样就不必使用原始的 SQL 查询。在`up()`方法中，填写:

```
<?php
public function up()
{
Schema::create('comments', function($table)
{
$table->increments('id');
$table->string('title');
$table->text('body');
$table->integer('author');
});
}
```

并且在`down()`方法中，放入:

```
<?php
Public function down()
{
Schema::drop('comments');
}
```

保存您的文件并运行`php artisan migrate`,您应该会收到一条成功的确认消息。

现在让我们制作种子。种子是您想要插入到数据库中用于测试应用程序的某种测试数据。在`database/seed` s 目录下创建一个名为`comments.php`的新文件，内容如下:

```
<?php
return [
[
'title' => 'hello there',
'body' => 'I wish you a warm welcome',
'author' => '5'
],
[
'title' => 'Another hello',
'body' => 'I want to greet you another time',
'author' => '1'
]
];
```

该代码显示了一个多维数组，其中每个成员数组都是一条记录。现在执行`php artisan db:seed`。现在您的数据库已经被植入(包含测试数据)！每次进行迁移时，您都可以这样做。

## 使用独立组件

正如我前面所说，新框架的最大更新是独立组件的可用性。Laravel 使用一个`IlluminateFoundationApplication`实例作为 IoC 容器，因此我们不必担心依赖关系，并且我们能够使用自己的定制组件。最后一个可以在你的`config`目录下的`app.php`中编辑。

我相信将来会有很多项目可以包装 Laravel 组件，并在您的项目中使用它们。一个例子是[胶囊](https://github.com/dandoescode/capsule)，它为我们提供了一些访问组件的方法。目前只支持数据库组件，但将来会有所改变。例如，可以通过以下代码建立数据库连接:

```
<?php
CapsuleDatabaseConnection::make('main', [
//database credentials
], true);
```

现在您可以通过使用`CapsuleDB`类的静态方法来执行[流畅的查询](http://laravel.com/docs/database/fluent)。有关文档，请查阅 GitHub 上的项目。

## 干得好！

我认为 Laravel 开发人员再次做了很好的工作，对框架做了一些重大的改进。可测试性不再是问题，解耦也不再是问题。由于所有开发人员的辛勤工作，Laravel 4 很可能是目前最伟大的 PHP 开发框架之一。

## 分享这篇文章