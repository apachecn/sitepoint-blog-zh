# 用 Symfony 2 构建 Web 应用程序:引导

> 原文：<https://www.sitepoint.com/building-a-web-app-with-symfony-2-bootstrapping/>

### 介绍

Symfony PHP 框架功能强大、可伸缩且灵活。然而，许多人，尤其是那些刚接触框架的人，认为它有一个非常陡峭的学习曲线。

这在一定程度上是真的。乍一看，模型、视图、控制器、实体、存储库、路由、模板等等，总的来说会显得*非常*可怕和混乱。

然而，如果你已经掌握了 PHP 和 HTML 的基础，对现代网站开发有了基本的了解(特别是，漂亮的 URIs，MVC)，并且知道如何 CRUD 一个数据库/表格，你就离开发一个相当好的网站不远了，无论是为你的个人使用还是商业应用。

在本系列中，我将捕获一些关键步骤和一些高级技术(图像操作、分页、动态内容、NativeQuery 等)，以帮助任何考虑使用 Symfony(注意，为了避免将来的混淆，Symfony 在这里指的是 Symfony 2，而不是过时的 Symfony 1)作为他们的 PHP 框架来开发网站的人。

我将以我的个人藏书网站为起点。它已经开始运行了，所以我在这里使用的项目不是一个演示，而是一个真实的运行站点(托管在[http://www.rsywx.net](http://www.rsywx.net)，纯中文)。

这部分教程的最终源代码可以在 [Github 仓库](https://github.com/phpmasterdotcom/TaylorRen-SymfonyApp_01)中找到。有任何意见和疑问，请随时[联系我](mailto:taylor.ren@gmail.com)。

### 快速设置

设置 Symfony 相当容易。我最喜欢的方式是下载 Symfony 标准无供应商包。访问官方下载网站并确保你选择了正确的软件包。将归档文件解压缩到您计划的 web 根目录，您就差不多完成了(我的本系列项目将位于 f:\www\rsywx_test)。解压缩 Symfony 包后，您将看到一个与此类似的目录结构:

![](img/7431ca854b59f7df080c0aadd2ebb41e.png)

确保上述目录是正确的(您可能看不到。hg 目录，因为这仅用于版本控制)。

下一步是下载名为 [Composer](http://getcomposer.org/) 的 PHP 打包系统。大多数现代 PHP 框架使用 Composer 进行包管理。

如果您安装了 [cURL](http://curl.haxx.se/docs/install.html) 实用程序，您可以发出以下命令:

```
curl -S https://getcomposer.org/installer | php
```

或者，如果没有(但是您真的应该安装 cURL)，键入以下内容:

```
php -r "eval('?>'.file_get_contents('https://getcomposer.org/installer'));"
```

将会下载一个新文件，名为 composer.phar。这个文件是我们 PHP 包管理的入口点。

正如档案库的名字所暗示的，我们现在拥有的文件不包含让 Symfony 真正工作的库(或包)。为此，您需要运行:

```
php composer.phar update
```

以上将安装通常所需的最新和必要的软件包。如果您遇到任何错误(缺少实用程序、无法满足关键依赖关系..)作曲家会善意地让你知道。根据您的连接速度，安装过程应该最多在几分钟内完成。

如果您已经正确设置了您的 web 服务器(我使用的是 Apache ),您已经可以访问该站点了:

![](img/6c6619d4a77d514726fe1295e79f6cfc.png)

使用 Symfony，在开发过程中使用`app_dev.php`作为入口页面是常见的做法。在生产环境中，`app_dev.php`将被省略。

您可以随意浏览，感受一下这个框架，熟悉它提供的工具栏和调试消息。

详细的设置说明可以在这里找到[，以防你需要更多的帮助。](http://symfony.com/doc/current/book/installation.html)

> 定期运行`php composer.phar selfupdate`来获得最新的 composer.phar 发行版总是一个好主意。

**注意:**Symfony 包总是有一个名为 AcmeDemoBundle 的内置包。该捆绑包是一个演示应用程序，证明 Symfony 已经启动并运行，并作为一个示例，您可以使用它来构建自己的应用程序。要从您的网站设置中删除这个包，请遵循这篇[文章](http://symfony.com/doc/current/cookbook/bundles/remove.html)中的说明。

删除后，如果您再次访问该网站，您将会看到一个 404 页面(没有为“GET /”找到路由)，因为我们还没有开始创建自己的应用程序。

### 束、控制器、视图、模型(实体)

简而言之，Symfony 依赖于 bundles(在其他框架中通常称为模块)。一个包可以被看作是一个文件的集合——它作为一个容器，用于数据检索、逻辑控制和网站中特定功能集的呈现。我的网站只有一个包，用来列出我所有的书，一本书的细节，我读完一本书后写的文章，我最喜欢的 NBA 球队(湖人队)的得分，等等。这些函数(或者在 Symfony 中反直觉地称为模块)都封装在一个包(trrsywxBundle)中。

一个包将包含控制器、视图和实体文件(模型)。这些构成了 MVC 架构网站的基础。

要生成一个包并开始我们的应用程序开发，发出下面的命令:

```
php app/console generate:bundle
```

在创建包之前，控制台会询问您几个问题:

*   Bundle 名称空间:在这个例子中，我使用 tr\rsywxBundle。结尾一定要有“捆绑”。
*   包名:使用从名称空间派生的建议名称。在本例中为“trrsywxBundle”。
*   目标目录:使用建议的 l 选项(本例中为 F:/www/rsywx_test/src)。
*   配置格式:有 4 个选项可用:PHP、YAML、XML 和 annotation。选择一个你喜欢的，感觉舒服的。如果你想跟着做，我将使用 YAML，所以键入“yml”作为你的选择。
*   要不要生成整个目录结构:没必要。
*   确认生成，内核更新和路由生成。

关于此过程的更多详细说明可在[这里](http://symfony.com/doc/current/book/page_creation.html)找到。

#### 按指定路线发送

考虑将路由作为从 HTTP 请求到真正处理它的模块/函数的映射机制(处理请求、获取数据并向 web 浏览器返回正确的响应)。

像大多数其他框架一样，Symfony 有一个路由特性来支持[漂亮的 URIs](http://en.wikipedia.org/wiki/Clean_URL) 。这是通过在 routing.yml 中创建路由来实现的。htaccess 配置，它位于您的 Symfony 设置的 web/目录中)。

为了使封装更加强大，我建议您在包的 routing.yml 文件中添加自己的路由(位于*path-to-your-site-root*/src/*tr/rsywxBundle/*Resources/config 下)。当您最终想要将整个包移植到另一个站点时，这很方便。请注意包的名称空间是如何反映在目录结构中的。

定义路由时的一个常见缺陷是路由有其优先级。路由出现的越早，匹配的越早。换句话说，如果路线安排不当，就会出现奇怪的、难以调试的问题。

例如，在我的站点的早期版本中，我有两条如下所示的路线:

```
tag_list:
  pattern: /books/{tag}/{page}
  defaults: {_controller: trrsywxBundle:Book:taglist, page:1}   
...  
reading_list:
  pattern: /books/readings/{page}
  defaults: {_controller: trrsywxBundle:Reading:list, page:1} 
```

看到问题了吗？`/books/readings`实际上将被映射到`tag_list`路线，其中参数`tag`将把`readings`作为其值，并将我们导向一个完全不同的控制器。我不能简单地改变这两条路线的顺序(这将解决大多数问题，但当有人真正在寻找包含“阅读”作为标签的书籍时，就会崩溃)。解决这个问题的唯一方法是改变第一个问题的路由模式:

```
tag_list:
  pattern: /books/tag/{page}/{tag}
  defaults: {_controller: trrsywxBundle:Book:taglist, page:1} 
```

因为路由出现得越早，匹配得越早，所以需要将最常用的路由放在 routing.yml 文件的开头。这样做的额外好处是速度更快，因为框架在匹配之前要检查的路由更少。

我在这里并没有涵盖 Symfony 中路由的所有方面。[官方文件](http://symfony.com/doc/current/book/routing.html)说明了一切。

#### 数据库ˌ资料库

在这个项目中，使用的数据库相对简单。创建了几个表来反映书的信息(标题、作者、出版商、购买日期、地点等)、我的阅读想法、湖人队的得分、当天的报价等。大多数表(书籍、读物、出版商、购买地点等)都是以一对多的关系相互关联的。“标签”表和“书籍”表通过多对多关系链接在一起。

项目的`misc`文件夹中的`rsywx.sql`文件将允许您导入数据库(MariaDB 或 MySQL)——只需通过 MySQL 命令行工具，或者通过 PhpMyAdmin 之类的 web GUI 导入即可。尽管 Symfony 官方文档建议在 Symfony 框架内创建数据库/表格，但我强烈建议使用另一个第三方工具。使用第三方工具的优势包括:

*   数据库的定义更加直观和简单；
*   在定义数据类型、关系、索引、主键等时减少输入。

我们现在可以通过配置`app/config/parameters.yml`链接数据库:

```
parameters:
  database_driver: pdo_mysql
  database_host: 127.0.0.1
  database_port: null
  database_name: symfony
  database_user: root
  database_password: null
```

通常，您只需更改数据库名称、用户和密码，以反映您自己的选择。

创建数据库/表格后，很容易将这些数据库结构导入 Symfony:

```
php app\console doctrine:mapping:import
```

然后通过以下方式创建相应的实体:

```
php app\console doctrines:generate:entity tr
```

(其中 tr 是包的名称空间)。

注: Symfony 目前支持两种 ORM:教条和推进。我在这个应用中使用原则。

一般来说，导入会成功，几乎没有问题。然而，至少有两件事需要注意:

*   该工具不擅长映射具有复合主键(即，具有 2 个或更多字段的主键)的表，同时，其中一个字段也是另一个表的外键
*   您可以编辑生成的实体文件(位于*path-to-your-site-root*/src/*tr/rsywxBundle/*Entity/)下)。但是如果您修改了字段名、类型和其他信息，请确保通过`php app/console doctrine:database:create`命令将这些更改“反映”回数据库。别担心，西弗莫尼现在更强更聪明了。该命令不会破坏您之前加载的数据。

### 结论

在教程的这一部分，我们设置了项目和 Symfony 框架，将数据库放在适当的位置，并做了清理工作(删除内置的 AcmeDemoBundle)。

为适当的进一步开发准备好框架通常是开始一个项目最困难的部分，尤其是在处理一个你以前从未处理过的框架时。到目前为止，我希望设置仍然足够简单明了。Symfony 是我见过的文档记录最完整的框架，所以如果你还有问题，一定要访问官方文档和 T2 的食谱来寻求帮助，或者直接联系。同样，这部分教程的最终代码可以在 [Github](https://github.com/phpmasterdotcom/TaylorRen-SymfonyApp_01) 上获得。

下一次，我们将创建路由、控制器(指导应用程序流和逻辑)、实体/存储库(检索数据)和模板(用于表示)来使站点起作用。

## 分享这篇文章