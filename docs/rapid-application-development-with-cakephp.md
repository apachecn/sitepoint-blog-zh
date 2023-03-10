# 用 CakePHP 快速开发应用程序

> 原文：<https://www.sitepoint.com/rapid-application-development-with-cakephp/>

CakePHP 是一个为 PHP 开发提供坚实基础的框架。它允许任何技能水平的用户快速开发健壮的 web 应用程序。该框架遵循 MVC 模式(模型-视图-控制器)的原则，该模式将代码分为三个部分:

*   **模型**–处理数据的代码(即数据库、文件系统和其他数据提供者)
*   **视图**–显示数据和处理界面(网页、RSS 提要等)的代码。)
*   **控制器**–通过收集协调模型和视图代码的输入来提供应用程序主要功能的代码

CakePHP 还通过添加 Dispatcher 组件来帮助将请求路由到适当的控制器，从而扩展了这种模式。因此，CakePHP 应用程序中的流程如下所示:

![](img/476fe5ca30e7d680d85d61a87dee0268.png)

你可以在 [CakePHP 图书网站](http://book.cakephp.org/2.0/en/cakephp-overview/understanding-model-view-controller.html)上找到更多关于 MVC 模式的信息。

就个人而言，我更喜欢 CakePHP 而不是其他 PHP 框架的原因是它对控制台应用程序的更好支持。CakePHP 有一个强大的控制台工具，可以定制为 web 和控制台世界构建应用程序。

在本文中，我将向您介绍 CakePHP 最有用的两个特性:

*   使用控制台工具 [Bake](http://book.cakephp.org/2.0/en/console-and-shells/code-generation-with-bake.html) 自动生成代码
*   动态[脚手架](http://book.cakephp.org/2.0/en/controllers/scaffolding.html)

我将使用 CakePHP 的这两个特性，通过重新创建 Stephen Thorpe 在他的文章[中提出的例子来实现。Thorpe 的示例应用程序提供了一个收集电子邮件地址的表单，验证该地址，并将其存储到数据库中。](https://www.sitepoint.com/untangling-mvc-with-codeigniter/)

## 安装和配置

在你开始写任何代码之前，你需要从 [CakePHP 网站](http://www.cakephp.org/)下载并安装最新版本的 CakePHP。确保您的 web 服务器支持 URL 重写(例如 Apache 通过 mod_rewrite 来支持),因为 CakePHP 大量使用它。还有其他回退方法，但它们超出了本文的范围。

获得 CakePHP 代码后:

1.  解压缩 web 服务器的文档根目录中的归档文件，并将目录重命名为`Subscribers`。
2.  使目录`Subscribers/app/tmp`对运行 web 服务器的用户帐户可写。
3.  打开浏览器并导航至`Subscribers`目录(例如`http://localhost/Subscribers/`)。

您应该会看到类似如下的页面:

![](img/b13e68ce156188d14e426efaed89527f.png "cake-vito-2")

CakePHP 的哲学是“约定胜于配置”如果您遵循 CakePHP 对类、变量、文件名和路径的基本命名约定，您可以将配置文件最小化为基本信息。

目录`app/Config`是 CakePHP 的惯例之一。在这个目录中，您可以找到默认的配置文件，并且可以根据需要添加您的特定文件。CakePHP 在`core.php`文件中提供了一些基本设置，您可以通过编辑专用于您的应用程序的`bootstrap.php`文件来覆盖这些设置。CakePHP 还提供了一个名为`database.php.default`的数据库配置示例文件。

### 安全性更改

在最初的浏览器输出中，您应该看到两个红色通知，要求您编辑参数`Security.salt`和`Security.cipherSeed`。框架的所有加密例程都使用这两个设置，因此显然最好使用不同于出厂默认值的值。

打开`app/Config/bootstrap.php`文件，插入这两行:

```
<?php
Configure::write('Security.salt', 'SomeSuperSecretLongStringHere');
Configure::write('Security.cipherSeed', 'SomeSuperSecretLongNumberHere');
```

CakePHP 使用它的`Configure`类来管理这样的设置。通过这几行代码，您可以使用`Configure`的`write()`静态方法来设置新值。这些值将存储在配置对象的内存中。您经常使用的另一种方法是`Configure::read()`来获取应用程序的设置。使用`Section.variableName`命名设置的惯例被称为*点符号*，它被 CakePHP 内部转换成一个关联数组。

现在，如果你重新加载页面，两个红色的通知消失。

### 数据库配置

黄色通知建议如何继续配置数据库连接:将`app/Config/database.php.default`重命名(或复制)为`app/Config/database.php`，并在您的编辑器中打开它。

`DATABASE_CONFIG`类在其公共属性中存储所有数据库连接的详细信息。starter 文件为您提供了`$default`和`$test`属性(应用程序默认使用前者，PHPUnit 测试可选使用后者)。

每个变量都是您应该熟悉的设置的关联数组:`hostname`、`login`、`password`、`database`和`prefix`。datasource 键设置使用哪个数据库驱动程序，缺省值是 MySQL ( `Database/Mysql`)。CakePHP 使用 PDO，因此它也支持 Postgres、SQLite 和 MS SQL Server。此外，您可以添加自定义数据源或覆盖现有的数据源。我将继续用默认的 MySQL 驱动程序进行演示。

在您的服务器上创建一个新的数据库(即`cake_subscribers`)并将连接细节插入到您的`$default`连接中。重新加载页面，您应该会看到一个绿色的通知，上面写着“Cake 能够连接到数据库”

您还需要一个数据库表来保存订户信息，因此使用以下代码来创建订户表:

```
CREATE TABLE subscribers (
    id INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(128) NOT NULL,
    last_name VARCHAR(128) NOT NULL,
    email VARCHAR(128) NOT NULL UNIQUE,
    created DATETIME,
    modified DATETIME
)
```

### 其他有用的设置

查看`core.php`文件内部，您可以看到可以用`bootstrap.php`文件覆盖的其他有趣的设置:会话和日志记录特性、错误和异常处理等等。一个有用的设置是调试级别。默认值为 2，这意味着显示错误和警告，缓存总是刷新，并且完整的 SQL 输出显示在每页的底部。值 1 将抑制 SQL 输出。值为 0 适合生产；只记录错误，CakePHP 特定的异常被优雅地显示为 404 或 501 错误。

## 带烘焙的代码生成

一旦你有了基本的环境设置，你就可以开始编码或者…让 CakePHP 为你写一些代码！烘焙控制台工具非常强大。它可以生成任何基本组件(模型、视图和控制器)和其他有用的东西，比如单元测试。

打开一个终端窗口，导航到您的项目目录(例如，`/Users/Shared/WebServer/Documents/Subscribers`)并运行如下所示的 Bake 命令，为模型生成代码:

```
Vitos-Laptop:Subscribers vito$ lib/Cake/Console/cake/bake model
Welcome to CakePHP v2.0.5 Console
---------------------------------------------------------------
App : app
Path: /Users/Shared/WebServer/Documents/Subscribers/app/
---------------------------------------------------------------
---------------------------------------------------------------
Bake Model
Path: /Users/Shared/WebServer/Documents/Subscribers/app/Model/
---------------------------------------------------------------
Use Database Config: (default/test) 
[default] >
```

按 enter 确认您的默认数据库连接，您将看到:

```
Possible Models based on your current database:
1\. Subscriber
Enter a number from the list above,
type in the name of another model, or 'q' to exit  
[q] >
```

输入 1，然后 Bake 将询问您是否要为此模型指定验证标准。输入 y。

对于每个字段，Bake 会要求您选择一个或多个验证规则，并向您推荐最合适的规则。为`name`和`last_name`字段选择`notempty`，为电子邮件字段选择`email`。

最后一个问题是关于模型关联，现在输入 N 是安全的。您应该会看到一个摘要:

```
---------------------------------------------------------------
The following Model will be created:
---------------------------------------------------------------
Name:       Subscriber
DB Table:   `subscribers`
Validation: Array
(
    [name] => Array
        (
            [notempty] => notempty
        )

    [last_name] => Array
        (
            [notempty] => notempty
        )

    [email] => Array
        (
            [email] => email
        )

)
---------------------------------------------------------------
Look okay? (y/n) 
```

如果没问题，输入 Y，让它工作。不到一分钟，我们就有了一个模型类，包括验证和测试功能。在你的编辑器里看一下。厉害！

现在让我们对控制器做同样的事情:

```
Vitos-Laptop:Subscribers vito$ lib/Cake/Console/cake/bake controller
```

按 enter 键接受默认的数据库配置，然后选择`Subscribers`控制器，然后输入 Y 以交互方式构建控制器，然后输入 Y 以启用动态搭建。最终输出应该如下所示:

```
---------------------------------------------------------------
The following controller will be created:
---------------------------------------------------------------
Controller Name:
    Subscribers
public $scaffold;
---------------------------------------------------------------
Look okay? (y/n) 
[y] >
```

## 带有脚手架的自动用户界面

如果你打开控制器文件`app/Controller/SubscribersController.php`，你会看到它几乎是空的；有一个类定义和一个名为`$scaffold`的公共属性。Bake 可以生成控制器的完整代码，也可以生成视图(我鼓励你去尝试)，但是我们之前选择了使用动态搭建特性。

脚手架是一种技术，它允许你定义和创建一个可以创建、检索、更新和删除对象的基本应用程序。使用脚手架需要的只是一个模型和一个控制器。怀疑？将您的浏览器指向`http://localhost/Subscribers/subscribers`，您应该会看到:

![](img/15df2bfc64c0d406a5b1ca51ac1a3d94.png "cake-vito-3")

界面功能齐全！尝试插入并编辑一些条目；您将看到实际的错误处理和完整的分页。

但是，如果你试图插入一个重复的电子邮件地址，你应该看到一个非常糟糕(但优雅)的错误信息。这是因为我将`UNIQUE`索引应用于数据库中的`email`字段，而 CakePHP 不知道这一点。打开模型文件`app/Model/Subscriber.php`，编辑其电子邮件验证规则，如下所示:

```
<?php
...
'email' => array(
    'email' => array(
        'rule' => array('email'),
        //'message' => 'Your custom message here',
        //'allowEmpty' => false,
        //'required' => false,
        //'last' => false, // Stop validation after this rule
        //'on' => 'create', // Limit validation to 'create' or 'update' operations
    ),
    'unique' => array(
        'rule' => array('isUnique'),
        'message' => 'This email address is already registered',
        //'allowEmpty' => false,
        //'required' => false,
        //'last' => false, // Stop validation after this rule
        //'on' => 'create', // Limit validation to 'create' or 'update' operations
    ),
),
```

现在再次尝试添加一个重复的地址，这一次您应该会看到一个更友好的错误消息。

脚手架也可以重定向到特定的路径，例如`/admin`。您可以构建和定制应用程序的公共页面，并使用私有管理面板的脚手架特性，最好使用 CakePHP 的身份验证、授权和安全特性。我将向您展示第一步，然后让您探索其他步骤。

将这一行添加到您的`bootstrap.php`文件:

```
<?php
Configure::write('Routing.prefixes', array('admin'));
```

这告诉 CakePHP 将`admin`解析为 URL 前缀(您可以使用任意多的前缀)。然后在`SubscribersController.php`文件中编辑脚手架属性，如下所示:

```
<?php
...
public $scaffold = 'admin';
```

如果你重新打开订阅者索引 URL，你会看到一个错误，但是如果你指向`http://localhost/Subscribers/admin/subscribers`，你会看到你的管理面板。

## 摘要

在这篇介绍性文章中，您只看到了 CakePHP 功能的冰山一角。CakePHP 有很多特性，几乎每一个都是可扩展和可定制的，以满足你的需求。此外，你还可以指望一个非常有用的[文档](http://book.cakephp.org/)，一个完整的 [API 参考](http://api20.cakephp.org/)，以及一个名为[面包店](http://bakery.cakephp.org/)的活跃社区。建议你先从[教程](http://book.cakephp.org/2.0/en/tutorials-and-examples.html)和……祝烘焙快乐！

图片通过[【susaiy】](http://www.shutterstock.com/gallery-603064p1.html)/[快门闭锁](http://shutterstock.com)

## 分享这篇文章