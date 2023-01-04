# 再见 CodeIgniter，你好 Laravel

> 原文：<https://www.sitepoint.com/goodbye-codeigniter-hello-laravel/>

很多 PHP 开发人员都是他们的 PHP 框架的粉丝，有相当多的框架可供你选择。其中最受欢迎的是 CodeIgniter、Zend Framework、CakePHP、Symfony、Yii，以及 block Silex、Slim、Lithium 和 Laravel 上的新成员。

在我职业生涯的开始，我偶然发现了 CodeIgniter，我喜欢它的简单性、占用空间小和良好的文档。当我开发一个中小型应用程序(通常是脸书应用程序)时，它对我来说非常有用，并且与我一起工作的新开发人员的学习曲线很浅，他们可以很容易地集成到开发过程中。

但是去年，由于 PHP 社区中一些人的 Twitter 讨论、博客帖子和一些朋友的建议，我尝试了 Laravel 3——从那以后我再也没有回头。因此，在这篇文章中，我想从我的角度对这两个框架进行比较。

## 要求

CodeIgniter 2.1.3 需要 PHP 5.1.6，但 2 之前的版本仍然支持 PHP 4。这是一个拖累，因为每个人都转而使用 PHP 5。Laravel 3 需要 PHP 5.3 和 Mcrypt 扩展。

CodeIgniter 1 不需要 Mcrypt，因为如果您没有安装扩展，那么它将使用自定义函数来编码/解码字符串。当然，这降低了应用程序的速度，我们对一些没有安装它的生产服务器感到惊讶。

```
<?php
function encode($string, $key = '') {
    $key = $this->get_key($key);
    if ($this->_mcrypt_exists === true) {
        $enc = $this->mcrypt_encode($string, $key);
    }
    else {
        $enc = $this>_xor_encode($string, $key);
    }
    return base64_encode($enc);
}
```

## 休息

使用 [RESTful 控制器](http://laravel.com/docs/controllers#restfulcontrollers "Controllers - Laravel Documentation")很容易用 Laravel 构建 REST API，只需在控制器中设置`$restful`属性为真。例如，如果你正在为一个任务管理器构建一个 API，为了更新一个任务，它会像这样使用:

```
<?php
class Tasks_Controller extends Base_Controller {
    public $restful = true;
    public function get_index()
    {
    }
    public function put_update()
    {
    }
...
}
```

要处理 PUT in CodeIgniter，您需要以下解决方法:

```
<?php
if ($_SERVER["REQUEST_METHOD"] == "PUT") {
    parse_str(file_get_contents("php://input"), $post_vars);
    $arData = json_decode(array_pop(array_keys($post_vars)));
}
```

如您所见，这很容易出错，而且不太干净。

## 代码组织

大多数时候，你正在做的项目会有前端和后端。为了在 CodeIgniter 中更好地组织代码，最好的方法是使用 [HMVC](https://github.com/Crypt/Codeigniter-HMVC "Crypt/Codeigniter-HMVC - GitHub") 。通过这种方式，你可以为后端代码和前端代码建立单独的目录，这样不同的开发人员就可以独立工作。为了用 Laravel 实现类似的东西，你可以使用[嵌套控制器](http://laravel.com/docs/controllers#nested-controllers "Controllers - Laravel Documentation")。

### 景色

CodeIgniter 没有默认的模板引擎，但是您可以轻松地集成一个像 [Smarty](https://github.com/cryode/CodeIgniter_Smarty "cryode/CodeIgniter_Smarty - GitHub") 这样的引擎。另一方面，Laravel [使用 Blade](http://laravel.com/docs/views/templating#blade-template-engine "Templating - Laravel Documentation") 作为其模板引擎，非常简单但功能强大。有了 Blade，建立一个[两步视图](http://martinfowler.com/eaaCatalog/twoStepView.html "P of EAA: Two Step View")非常容易。

你有一个主模板，通常你把基本的 HTML 结构和页眉、导航、页脚等放在里面。大多数时候这个模板被称为`layout.blade.php`或者`master.blade.php`。将从不同控制器注入的代码部分由`@yield('content')`在主模板中声明。

```
<!DOCTYPE HTML>
<html>
 <head>
  ...
 </head>
 <body>
  <div class="header">
   ...
  </div>
  @yield('content')
 </body>
</html>
```

在根据控制器的每个方法声明的自定义模板中，例如像“关于我们”这样的 CMS 页面，有一个名为`page.blade.php`的模板，看起来像这样:

```
@layout('master')
...
@section('content')
About page section
@endsection
...
```

您可以声明和重用任意多节。

关于这一点的更详细的解释可以在在线开源书籍《T2 代码快乐》中的[刀片布局](http://codehappy.daylerees.com/blade-templates "Code Happy: Blade Templates")一节中找到。

要在 CodeIgniter 中实现这一点，至少就我所知，是创建一个主控制器，所有其他控制器都扩展它并调用一个 render 方法。在我的 GitHub 帐户下可以找到一个用于实现两步视图的 [CodeIgniter 启动项目](https://github.com/gafitescu/codeigniter-2-two-step-view "gafitescu/codeigniter-2-two-step-view - GitHub")。

### 模型

Codeigniter 使用活动记录进行数据库操作，这很容易掌握，但是如果您有复杂的需求，也可以使用原始查询。Laravel 使用[口才](http://laravel.com/docs/database/eloquent "Eloquent - Laravel Documentation")作为一个 ORM，它很简单，可以与主要的数据库服务器一起工作。对于编写查询，您也可以使用[流畅的查询构建器](http://laravel.com/docs/database/fluent "Fluent Query Builder - Laravel Documentation")，它看起来很像活动记录方法。当然，您也可以编写原始查询。

## 命令行(Crons)

对于调用 cron 脚本的 Codeigniter，最好的方法是创建一个`Cron`控制器。您还需要复制`index.php`并在根目录下创建一个`cron.php`文件，覆盖下面的`$_SERVER`值:

```
<?php
$_SERVER['SERVER_NAME'] = "xxxxxx.org";
$_SERVER['REQUEST_URI'] = "cron/index";
$_SERVER['QUERY_STRING'] = "lang=en";
$_SERVER['SERVER_PORT'] = "0";
$_SERVER['REQUEST_METHOD'] = "GET"
```

然后可以用`php cron.php cron index`从命令行调用。

在拉勒维尔更容易。您可以使用 Laravel 附带的名为 Artisan 的命令行工具完成[任务](http://laravel.com/docs/artisan/tasks "Tasks - Laravel Documentation")。

## 多方面的

*   **单元测试**–Codeigniter 使用一个非常[初级的测试类](http://ellislab.com/codeigniter/user-guide/libraries/unit_testing.html "Unit Testing Class: CodeIgniter User Guide")来编写单元测试。它是有限的，但是你可以集成 PHPUnit。Laravel 自带 PHPUnit，这是最流行的 PHP 单元测试框架。
*   **认证**–Codeigniter 没有现成的认证工具，但是你可以使用第三方库，比如[离子认证](http://benedmunds.com/ion_auth/ "Ion Auth Docs")。另一方面，Larvel 有一个基本的[授权库](http://laravel.com/docs/auth/usage "Authentication Usage - Laravel Documentation")，你只需要在使用前进行配置。
*   **缓存**–使用 CodeIgniter，驱动程序支持 APC、Memcache 和文件系统。另一方面，Laravel 支持这些以及数据库表和 Redis。
*   **钩子/过滤器**——如果你想在 CodeIgniter 的不同执行点进入并运行某些代码，你应该使用[钩子](http://ellislab.com/codeigniter/user-guide/general/hooks.html "Hooks: CodeIgniter User Guide")来完成。Laravel 提供[滤镜](http://laravel.com/docs/routing#filters "Routing - Laravel Documentation")。
*   路由–Laravel 和 CodeIgniter 的路由方式基本相同，尽管我发现 Laravel 更加灵活。
*   **配置**–配置是通过存储在配置文件中的预定义数组完成的，这些配置文件支持两种框架中的多种环境。
*   **可扩展性**——扩展 CodeIgniter 的核心功能是通过库来完成的，你可以在[Sparks](http://getsparks.org/ "CodeIgniter Sparks | The Package Manager and Repository")(CodeIgniter 的一个包管理系统)上找到很多库。Laravel 使用了[包](http://laravel.com/docs/bundles "Bundles - Laravel Documentation")，如果你使用过 Symfony 2，你可能会觉得很熟悉。你可以在[bundles.laravel.com](http://bundles.laravel.com/ "Laravel Bundles")找到已经创建的检查包。
*   **迁移**–[数据库迁移](http://laravel.com/docs/database/migrations "Migrations - Laravel Documentation")特定于 Laravel，这是一个借用 Ruby on Rails 的概念，在数据库级别进行版本控制。

## 社区

CodeIgniter 曾经有一个更大的社区，但在它背后的公司 EllisLab 停止支持并且没有新功能添加后，许多人转向了不同的框架。其中一些人加入了 Laravel 社区，该社区现在非常活跃，有很多教程、书籍，甚至还有第一次国际会议。

Laravel 的未来似乎非常光明，因为 Laravel 4 处于 beta 2 阶段，其第一次发布会刚刚结束。在 CodeIgniter 的另一方面，很少有成员贡献者将补丁提交/合并到 CodeIgniter 2 中，但 CodeIgniter 3 没有发布日期。

## 结论

随着 Laravel 4 的出现，这个框架现在有了发展的势头，它背后的社区非常活跃。当我谈到 CodeIgniter 时，我有点怀旧，但它当时对我非常有用。它教会了我如何使用 MVC，简单易学，为我赚了第一笔钱，我甚至写了第一篇关于它的书评，但是随着时间的推移，它几乎没有改变。也许只是因为它非常擅长它所做的事情？但是网络发展变化很快，新技术层出不穷，所以如果你想生存就必须跟上。

就像在政治中，每个人都选择一方。大多数时候你可以坚持自己的选择，但探索新事物总是好的。对我来说，这意味着再见 CodeIgniter，你好 Laravel。

对本文的评论已经关闭。有关于 PHP 框架的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章