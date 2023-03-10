# Lithium 框架:入门

> 原文：<https://www.sitepoint.com/lithium-framework-getting-started/>

[Lithium](https://github.com/UnionOfRAD/lithium) 是一个精干但刻薄的 PHP 框架(机器？)专为 PHP 5.3 及以上版本打造。它旨在为启动您的 web 应用程序提供一个好的工具集，但又不太局限。

Lithium 使用[模型-视图-控制器(MVC)](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 架构，这就是我们在本文中要讨论的。我将向您展示它是如何工作的，以及如何使用这个框架定义应用程序的一些业务和表示逻辑。在这方面，我们将做几件事。

我们将设置一个控制器来路由 URL 请求。该控制器将在数据模型的帮助下从数据库中获取并处理一些信息。然后，这些信息将使用视图显示在浏览器中。所有标准的 MVC 的东西，但真正的乐趣与锂执行。

我假设您已经在服务器上设置了框架，如果您导航到 URL，至少可以看到默认的应用程序起始页。此外，您还需要一个填充了一些信息的数据库。我将使用 MySQL，但 Lithium 支持许多其他存储系统，如 MongoDB 或 CouchDB。

我已经建立了一个 [Git 库](https://bitbucket.org/upchuk/lithium-framework/)，如果你想跟进的话，你可以克隆它。`master`分支包含普通的 Lithium 框架，而`MVC`分支包含本文的代码。别忘了还有`init`和`update`锂子模块。要连接您的数据库，复制一份位于`app/config/bootstrap`文件夹中的`connections_default.php`文件，并将其重命名为`connections.php`。然后在该文件中添加您的凭据。

我们开始吧。

## 数据

在开始有趣的 MVC 内容之前，让我们用一些信息在数据库中创建一个表。我将使用虚拟页面数据，因此我的表(名为`pages`)将包含一个`id`列(INT、自动增量和主键)、一个`title`列(varchar 255)、一个`content`列(text)和一个`created`列(INT)。在这个表中，我有 2 个样本行。如果您想准确地理解，下面是表创建语句:

```
CREATE TABLE `pages` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `title` varchar(255) DEFAULT NULL,
  `content` text,
  `created` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8; 
```

这是我的虚拟行:

```
INSERT INTO `pages` (`id`, `title`, `content`, `created`)
VALUES
    (1, 'My awesome page title', 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.', 1397158745),
    (2, 'Some other page title', 'Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.', 1397158768); 
```

当然也可以用别的。

## c 代表控制器

控制器可能是任何 MVC 框架中最重要的部分。它们的角色是处理由应用程序的路由系统路由到它的请求。

如果你查看你的应用程序的`app/controllers/`文件夹，你会发现这是我们必须放我们的文件夹的地方。让我们在那里创建一个名为`SiteController.php`的新文件(每个控制器类都驻留在自己的文件中)，并粘贴以下类声明开始:

```
<?php

namespace app\controllers;

class SiteController extends \lithium\action\Controller {

} 
```

如你所见，我们正在将 Lithium base 控制器类扩展到我们自己的名为`SiteController`的类中。在这个类中，您创建了一些方法，这些方法在被 URL 请求时执行所需的逻辑。我们一会儿将看到它的实际运行，但首先，让我们了解路由是如何工作的。

默认情况下，在构造 URL 时，我们使用映射到控制器类名称(在我们的例子中是`site`)、方法和参数的参数。如果没有传递方法名，Lithium 将自己假设一个名为`index()`的方法。所以如果你导航到`http://example.com/site/`，Lithium 会寻找这个方法并调用它。现在假设我们有一个名为`view()`的方法，它有一个参数(`$id`)。调用控制器方法的 URL 是`http://example.com/site/view/1`，其中`view`是方法的名称，1 是传递给该函数的参数。如果该方法获得更多参数，您只需在 URL 中传递它们，用斜杠(`/`)分隔。

然而，就像我提到的，这是默认行为。为了更好地控制，您可以在`/app/config/routes.php`文件中自己定义路线。我不会深入细节，但你可以在相应的[文档页面](http://li3.me/docs/app/config/routes)上找到更多信息。

现在让我们继续创建一个`page()`方法，它将负责显示我的虚拟数据库中的各个页面:

```
public function page() {

    // Mock page info.
    $title = 'My awesome page title';
    $content = 'My awesome page content. Yes indeed.';
    $created = '10 April 2014';

    // Prepare page info for passing to the View.
    $data = array(
      'title' => $title,
      'content' => $content,
      'created' => $created,
    );

    // Pass the data to the View.
    $this->set($data);

} 
```

上面，我们模拟了数据库页面信息，并将其存储在一个数组中。然后，我们将这个数组传递给控制器类的`set()`方法(我们继承了这个方法),并将其发送给视图。或者，我们也可以返回`$data`数组，而不是使用`set()`方法。但是在这两种情况下，数组的键代表变量名，我们可以从视图文件中访问这些变量名。让我们看看它是如何工作的。

## v 代表观点

视图是 MVC 框架的表示层。它们用于保持应用程序的业务逻辑独立，并允许对显示给浏览器的内容进行简单的主题化。

让我们创建一个视图来显示我们的页面信息。在`app/views/`文件夹中，您需要创建另一个以使用它的控制器类命名的文件夹(在我们的例子中是`site`)。在这个文件夹中，您必须创建一个以方法本身命名的文件，并附加上`.html.php`扩展名。这是 Lithium 对视图的命名约定，使得我们很容易将它们连接到控制器。

因此，对于我们的页面示例，新文件将位于`app/views/site/page.html.php`。

在该文件中，粘贴以下内容:

```
<!-- Title -->
<h1><?=$title ?></h1>

<!-- Created date -->
<p><?=$created ?></p>

<!-- Main content -->
<div class="content">
  <?=$content ?>
</div> 
```

您可能已经猜到了，这是一些基本的标记，我们在其中打印以控制器传递的数组键命名的变量。Lithium 使用这种语法来打印变量，因为它还通过负责净化 HTML 的`$h()`函数来运行变量。然而，这仅适用于打印变量，而不是`$this`对象的属性。

为了测试我们目前所拥有的，导航到`http://example.com/site/page`，您应该会看到一个显示模拟信息的漂亮页面。您还会注意到，我们的简单视图呈现在一个更复杂的布局中(框架自带的默认布局)。

Lithium 中的布局用于用常用的标记(如页眉和页脚)包装内容。它们位于`app/layouts`文件夹中，它们使用`$this->content()`来渲染里面的视图。默认情况下，我们的视图呈现在`default.html.php`布局中，但是如果你愿意，你可以指定另一个。您可以从呈现视图的控制器中执行此操作，或者作为应用于该控制器的所有方法的类属性，或者在方法本身内部执行，如下所示:

```
public function page() {

    // Method logic ...

    $this->_render['layout'] = 'yourLayout';   
} 
```

我们将坚持使用默认的，因为它看起来很适合我们的演示目的。

## m 代表型号

既然已经处理了请求和表示逻辑，是时候用我们的虚拟数据库内容替换模拟页面数据了。我们将使用一个模型来抽象和方便地访问这些信息。

模型类是 MVC 框架中非常重要的一部分，因为它们定义并处理数据库中的内容。它们还使得应用程序可以轻松地对这些数据执行 CRUD(创建、读取、更新、删除)操作。让我们看看它们在锂中是如何工作的。

您需要做的第一件事是在名为`Pages.php`的`app/models`文件夹中创建一个类文件，并在其中粘贴以下内容:

```
<?php

namespace app\models;

class Pages extends \lithium\data\Model {

} 
```

我们只是扩展了基本模型类，并利用了它的所有方法。我们的模型类的名称需要匹配包含相关记录的数据库表。所以，如果你的名字不是`pages`，确保你相应地适应，因为 Lithium 会自动选择这个名字，让我们更容易。

接下来，我们需要将该文件包含在控制器类文件中，因此将以下内容粘贴到名称空间声明的下方:

```
use app\models\Pages; 
```

接下来从`page()`方法中移除模拟内容，并确保该函数传递了一个`$id`参数，这样我们就知道需要检索哪个页面。我们只剩下查询页面记录并将结果传递给视图的简单任务。所以修改后的`page()`方法看起来会是这样的:

```
public function page($id) {

    // Look for the first record that matches the passed criteria
    $record = Pages::first(array('conditions' => array('id' => $id)));
    $page = $record->data();

    // Prepare page info for passing to the View.
    $data = array(
      'title' => $page['title'],
      'content' => $page['content'],
      'created' => date('F j Y', $page['created']),
    );

    // Pass the data to the View.
    $this->set($data);

} 
```

我们使用模型父类的`first()`方法来使用条件进行查询。结果是一个对象，我们使用`data()`方法从中检索记录数据。这是以表列名为关键字的数组形式。除了我们使用 PHP `date()`函数格式化的`created`字段之外，其余部分和以前一样，因为我们从数据库中获得了一个 UNIX 时间戳。差不多就是这样。

如果我们导航到`http:example.com/site/page/1`，我们应该会看到 ID 为 1 的页面。如果我们将最后一个 URL 参数切换为 2，页面应该加载第二条记录。干净利落。

## 结论

在本教程中，我们看到了理解和使用 Lithium MVC 框架是多么容易。我们已经学习了如何定义控制器、视图和模型，以及如何一起使用它们来创建一个整洁分离的应用程序流程。我们也看到了锂公约是如何帮助我们起步的。甚至在不知道的情况下，我们提取了数据库内容，并将其公开，以便于访问。

我希望您学到了一些东西，并有足够的好奇心更深入地了解一下 Lithium 提供的其他强大特性。有哪些内置的 CRUD 方法，如何扩展它们？如何定义自己的自定义路线？如何使用多种布局，甚至在视图中呈现更小的`elements`？这些都是 Lithium 为我们的 web 应用程序提供的优秀特性，值得一试。

我让你好奇了吗？想了解这个伟大框架的更多信息吗？

## 分享这篇文章