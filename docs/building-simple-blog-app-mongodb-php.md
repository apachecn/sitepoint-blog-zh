# 用 MongoDB 和 PHP 构建一个简单的博客应用程序

> 原文：<https://www.sitepoint.com/building-simple-blog-app-mongodb-php/>

## 介绍

如果你想用 MongoDB 和 PHP 创建一个博客，这篇文章将教你:

*   连接到 MongoDB 数据库
*   将文档保存在收藏中
*   查询集合中的文档
*   执行范围查询
*   对文档进行排序、更新文档、从集合中删除一个或多个文档

我选择构建一个博客应用程序的原因是因为它是一个基本的 CRUD 应用程序，非常适合于 PHP 和 MongoDB web 开发。我们将建立一个简单的用户界面使用引导简单的文本框和按钮。MongoDB 数据库将存储所有内容。你可以从 [github](https://github.com/duythien/php-mongodb) 下载完整的源代码，在这里看一个演示前端[，用用户名和密码 *duythien* 试试演示应用的](http://phpmongodb-duythien.rhcloud.com)[后端](http://phpmongodb-duythien.rhcloud.com/admin)。

## 什么是 MongoDB

据[官网](http://www.mongodb.org/about/introduction/)介绍，MongoDB 是一个文档数据库，提供高性能、高可用性和易伸缩性。MongoDB 属于面向文档的 NoSQL 数据库。NoSQL 数据库的其他子类型，见[这里](http://rebelic.nl/2011/05/28/the-four-categories-of-nosql-databases/)。

### MongoDB 概念:数据库、集合和文档

1.  数据库:MongoDB 按照与大多数关系数据库完全相同的方式将数据分组到数据库中。如果您有任何使用关系数据库的经验，您应该以同样的方式考虑这些。在 RDBMS 中，数据库是一组表、存储过程、视图等等。在 MongoDB 中，数据库是一组集合。MongoDB 数据库包含一个或多个集合。例如，一个名为 **blog** 的博客应用程序的数据库通常包含文章、作者、评论、类别等集合。

2.  集合:集合相当于 RDBMS 表。集合存在于单个数据库中。集合不强制架构。集合中的文档可以有不同的字段。通常，集合中的所有文档都有相似或相关的目的。

3.  文档:MongoDB 集合中的记录和 MongoDB 中的基本数据单元。文档类似于 JSON 对象，但是以一种更丰富类型的格式存在于数据库中，称为 [BSON](http://bsonspec.org/) 。文档包含一组字段或键值对。将文档视为多维数组的最佳方式。在数组中，有一组映射到值 **(Document == Array)** 的键。参见[文件](http://docs.mongodb.org/manual/core/document/)。

### 安装 MongoDB

MongoDB 运行在大多数平台上，支持 32 位和 64 位架构。MongoDB 可以作为二进制文件或软件包使用。在生产环境中，使用 64 位 MongoDB 二进制文件。本节将介绍在 Ubuntu Linux 和 Windows 上的安装。对于其他操作系统，请参阅它们的文档。

这就是 Mongo 在 Ubuntu Linux 上的安装方式。打开终端并执行以下操作:

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
#
echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | sudo tee /etc/apt/sources.list.d/mongodb.list 
```

现在发出以下命令来更新您的存储库并安装 MongoDB 的最新稳定版本:

```
sudo apt-get update
sudo apt-get install mongodb-10gen 
```

完成，您已经成功安装了 MongoDB。现在通过下面的命令行启动和停止服务 MongoDB。

```
sudo service mongodb start
sudo service mongodb stop 
```

如果出现启动错误，请尝试运行以下命令:

```
sudo mongod --fork --logpath /var/log/mongodb/mongodb.log
#or
sudo mongod -f /etc/mongodb.conf 
```

下面介绍了如何在 Windows 上安装它:

前往 MongoDB 官方网站上的[下载](http://www.mongodb.org/downloads)页面。单击 Windows 下最新稳定版本的下载链接。

下载完成后，提取并移入`C:\`。MongoDB 需要一个数据文件夹来存储它的文件。MongoDB 数据目录的默认位置是 C:\data\db。如果不存在，就创建它。

要启动 MongoDB，请在命令提示符下执行

```
C:\> cd \mongodb\bin
C:\mongodb\bin> mongod 
```

完成，您已经成功安装了 MongoDB。现在通过下面的命令行启动和停止服务 MongoDB。

```
net start MongoDB
net stop  MongoDB 
```

### 为 MongoDB 安装 PHP 驱动程序

MongoDB 服务器已经可以与您当前的 web 服务器协同工作，但不能与 PHP 协同工作。为了让 PHP 与 MongoDB 服务器对话，我们需要 PHP-MongoDB 驱动程序。这是一个 PHP 扩展库。

如果您使用 Linux，可以通过以下方式轻松安装:

```
sudo pecl install mongo 
```

将行`extension=mongo.so`添加到 php.ini 配置中，就可以开始了:

```
sudo -i
echo 'extension=mongo.so' >> /etc/php5/apache2/php.ini 
```

重新启动 web 服务器，并通过命令行进行验证:

```
php -i |grep "mongo"
php --re mongo 
```

### 在 Windows 上安装驱动程序

让我们尝试在 Apache (XAMPP)上运行 PHP 5.4 的 Windows 机器上安装驱动程序:

1.  在你的机器上下载 https://github.com/mongodb/mongo-php-driver/downloads 的压缩文件并解压。
2.  将 php_mongo.dll 文件从解压文件夹复制到 php 扩展目录(C:\xampp\php\ext)。
3.  打开 php 安装中的 php.ini 文件，添加下面一行:extension=php_mongo.dll
4.  保存文件并关闭它。重新启动 XAMP。
5.  打开文本编辑器，将下面的代码添加到一个新文件中:`<?php phpinfo();?>`，将文件保存为 Apache web 服务器的 document root(htdocs)中的 phpinfo.php，并在浏览器中打开 PHP 脚本。如果你在 PHP 信息中看到 mongo，说明安装成功了。

## 用 PHP 开发 Mongo Web

### 连接到 MongoDB 数据库服务器

从 PHP 连接到 MongoDB 与连接到任何其他数据库非常相似。默认主机是 localhost，默认端口是 27017。

```
$connection = new Mongo(); 
```

使用可选的自定义端口和身份验证连接到远程主机:

```
$connecting_string =  sprintf('mongodb://%s:%d/%s', $hosts, $port,$database),
$connection=  new Mongo($connecting_string,array('username'=>$username,'password'=>$password)); 
```

### 选择数据库

一旦建立了数据库服务器连接，我们将使用它来访问数据库。定义的方法是:

```
$dbname = $connection->selectDB('dbname'); 
```

### 基础知识(CRUD 操作)

MongoDB 为读取和操作数据提供了丰富的语义。CRUD 代表创建、读取、更新和删除。这些术语是所有与数据库交互的基础。

#### 创建/选择收藏

选择和创建集合与访问和创建数据库非常相似。如果集合不存在，则创建该集合:

```
$collection = $dbname->collection;
//or
$collection = $dbname->selectCollection('collection'); 
```

例如，这会在我的博客中创建“帖子”集合:

```
$posts = $dbname->posts 
```

#### 创建文档

在 MongoDB 中创建文档再简单不过了。创建一个数组。将其传递给集合对象的 insert 方法

```
$post = array(
        'title'     => 'What is MongoDB',
        'content'   => 'MongoDB is a document database that provides high performance...',
        'saved_at'  => new MongoDate() 
    );
    $posts->insert($post); 
```

`insert()`方法将数据存储在集合中。`$post`数组自动接收一个名为`_id`的字段，它是插入的 BSON 文档的自动生成的惟一 ObjectId。您还可以使用`save()`方法，该方法会更新现有记录，或者在记录不存在的情况下创建一个新记录。

#### 阅读文件

为了从集合中获取数据，我使用了`find()`方法，该方法获取集合中的所有数据。`findOne()`只返回一个满足指定查询条件的文档。以下示例将向您展示如何查询一个或多个记录。

```
 // all records $result = $posts::find();  // one record $id =  '52d68c93cf5dc944128b4567'; $results = $posts::findOne(array('_id'  =>  new  MongoId($id)));
```

#### 更新文档

修改集合中的一个或多个现有文档。默认情况下，`update()`方法更新单个文档。如果 multi 选项设置为 true，该方法将更新所有符合查询条件的文档。

```
 $id =  '52d68c93cf5dc944128b4567'; $posts->update( array('_id'  =>  new  MongoId($id)), array('$set'  => array(title'   => 'What  is phalcon'))
    );
```

update()方法有两个参数。第一个是描述要更新的对象的标准，第二个是用来更新匹配记录的对象。还有第三个可选参数，您可以通过它传入一组选项。

### 迷你博客

我们将要建立的项目结构:

```
 |-------admin |------index.php # This is dashboard  |------auth.php |-------view |------admin |-------create.view.php #form create article  |-------dashbroad.view.php |------index.view.php |------layout.php #This is main layout it will be shown for every  action executed within the app  |------single.view.php # This is view for a single article  |-------config.php # Define paramater database, url  |-------app.php # Include multi-php files  |-------db.php # Container class db  |-------index.php |-------single.php |-------static  # Container css,js...  |-----css |-----js |-------vendor |----markdown # Library converts markdown into html
```

在我们开始实际的 PHP 代码之前，我们需要像上面一样创建我们的文件和文件夹。

#### config.php

这是您的配置文件，告诉我们的应用程序如何连接到数据库。这是您定义数据库名称、用户名和用户密码以访问该数据库的地方:

```
 <?php
        define('URL',  'http://duythien.dev/sitepoint/blog-mongodb'); define('UserAuth',  'duythien'); define('PasswordAuth',  'duythien'); $config = array(  'username'  =>  'duythien',  'password'  =>  'qazwsx2013@',  'dbname'  =>  'blog',  'connection_string'=> sprintf('mongodb://%s:%d/%s','127.0.0.1','27017','blog')  );
```

其中我们定义了参数 UserAuth 和 PasswordAuth 来通过 HTTP 身份验证保护 admin 文件夹。为了简单起见，我们在这里使用 HTTP auth，因为本文的中心主题是连接到 MongoDB——您通常会使用某种带有 ACL 的体面框架来构建访问控制。

app.php:

```
 <?php
    include 'config.php'; include 'layout.php'; include 'db.php';  use  Blog\DB,  Blog\Layout;  // Constructor to the db $db =  new DB\DB($config);  // Constructor to layout view $layout =  new  Layout\Layout();
```

### 管理

这是包含 CRUD 代码的文件夹。

```
<?php
require_once 'auth.php'; require_once '../app.php'; require_once '../vendor/markdown/Markdown.inc.php';  use \Michelf\MarkdownExtra, \Michelf\Markdown;  if  ((is_array($_SESSION)  && $_SESSION['username']  ==  UserAuth))  { $data = array(); $status =  (empty($_GET['status']))  ?  'dashboard'  : $_GET['status'];  switch  ($status)  {  //create post  case  'create':  break;  //edit post  case  'edit':  break;  //delete post  case  'delete':  //display all post in dashboard  default: $currentPage =  (isset($_GET['page']))  ?  (int)$_GET['page']  :  1; $data = $db->get($currentPage,  'posts'); $layout->view('admin/dashboard', array(  'currentPage'  => $data[0],  'totalPages'  => $data[1],  'cursor'  => $data[2]  ));  break;  }  }
```

index.php 的完整档案见[这里](https://github.com/duythien/php-mongodb/blob/master/admin/index.php)。上面我使用了类`layout.php`中的视图函数，它将自动加载`dashboard.view.php`。

```
<?php namespace  Blog\Layout;  Class  Layout  {  /**
     * @var array
     */  public $data;  public  function view($path, $data)  {  if  (isset($data))  { extract($data);  } $path .=  '.view.php'; include "views/layout.php";  }  }
```

GET 参数`status`对应于一个 CRUD 动作。例如，当状态为“创建”时:

```
if  ($_SERVER['REQUEST_METHOD']  ===  'POST')  { $article = array(); $article['title']  = $_POST['title']; $article['content']  =  Markdown::defaultTransform($_POST['content']); $article['saved_at']  =  new  MongoDate();  if  (empty($article['title'])  || empty($article['content']))  { $data['status']  =  'Please fill out both inputs.';  }  else  {  // then create a new row in the collection posts $db->create('posts', $article); $data['status']  =  'Row has successfully been inserted.';  }  } $layout->view('admin/create', $data);
```

函数`view(‘admin/create’, $data)`显示了一个 HTML 表单，用户可以在其中编写新博客文章的标题/内容，或者将用户提交的数据保存到 MongoDB。默认情况下，该脚本显示以下 HTML 表单:

```
<form  action=""  method="post">  <div><label  for="Title">Title</label>  <input  type="text"  name="title"  id="title"  required="required"/>  </div>  <label  for="content">Content</label>  <p><textarea  name="content"  id="content"  cols="40"  rows="8"  class="span10"></textarea></p>  <div  class="submit"><input  type="submit"  name="btn_submit"  value="Save"/></div>  </form>
```

![create post](img/29b4bd5535ef213d8593bfe50f735851.png)

接下来我们来看看`db.php`，这里可以找到完整的

```
<?php namespace  Blog\DB;  class DB {  /**
     * Return db
     * @var object
     */  private $db;  /**
     * Results limit.
     * @var integer
     */  public $limit =  5;  public  function __construct($config)  { $this->connect($config);  }  //connecting mongodb  private  function connect($config)  {  }  //get all data  public  function  get($page, $collection)  {  }  //get one data by id  public  function getById($id, $collection)  {  }  //create article  public  function create($collection, $article)  {  }  //delete article by id  public  function  delete($id, $collection)  {  }  //update article  public  function update($id, $collection, $acticle)  {  }  //create and update comment  public  function commentId($id, $collection, $comment)  {  }  }
```

MongoDB 游标使分页变得容易。这些游标方法可以链接到查找返回和相互查找的游标对象。将 limit 和 skip 结合起来使分页变得容易。这些也可以和订单结合。比如说。

```
 public  function  get($page,$collection){ $currentPage = $page; $articlesPerPage = $this->limit;  //number of article to skip from beginning $skip =  ($currentPage -  1)  * $articlesPerPage; $table = $this->db->selectCollection($collection); $cursor = $table->find();  //total number of articles in database $totalArticles = $cursor->count();  //total number of pages to display $totalPages =  (int) ceil($totalArticles / $articlesPerPage); $cursor->sort(array('saved_at'  =>  -1))->skip($skip)->limit($articlesPerPage); $data=array($currentPage,$totalPages,$cursor);  return $data;  }
```

`index.php`:模板文件可以在视图文件夹中找到；比如`index.view.php`。这里有一个`index.php`的例子:

```
<?php require  'app.php';  try  { $currentPage =  (isset($_GET['page']))  ?  (int)$_GET['page']  :  1;  //current page number $data = $db->get($currentPage,  'posts'); $layout->view('index', array(  'currentPage'  => $data[0],  'totalPages'  => $data[1],  'cursor'  => $data[2],  'name'  =>  'Duy Thien'  ));  }  catch  (Exception $e)  { echo 'Caught exception: ', $e->getMessage(),  "\n";  }
```

打开浏览器并导航至`http://duythien.dev/sitepoint/blog-mongodb`。它列出了博客中当前的所有文章:

![index all posts ](img/dc4975f5e8222ba634e3959f62ef51f3.png)

`single.php`:当你查看一个帖子页面时(点击阅读更多帖子)，你看到的是视图文件夹中的`single.view.php`。这里是`single.php`的逻辑:

```
<?php require  'app.php';  // Fetch the entire post $post = $db->getById($_GET['id'],  'posts');  if  ($post)  { $layout->view('single', array(  'article'  => $post ));  } 
```

这个文件接收文章的`_id`作为 HTTP GET 参数。我们调用文章集合上的`findOne()`方法，将`_id`值作为参数发送给该方法。`findOne()`方法用于检索单个文档。参见文件`db.php`中的功能`getById()`

![single posts](img/53ce009ef74c88030dafee4e583fdd35.png)

在评论部分的输入框中输入一个任意的名字和电子邮件，同时在文本区输入一些文本。然后点击“保存”按钮，页面将重新载入您刚才发布的评论。这是`comment.php`的样子:

```
<?php require  'app.php';  if  ($_SERVER['REQUEST_METHOD']  ===  'POST')  { $id = $_POST['article_id']; $comment = array(  'name'  => $_POST['fName'],  'email'  => $_POST['fEmail'],  'comment'  => $_POST['fComment'],  'posted_at'  =>  new  MongoDate()  ); $status = $db->commentId($id,  'posts', $comment);  if  ($status == TRUE)  { header('Location: single.php?id='  . $id);  }  }
```

文章的评论存储在文档名`comments`的数组字段中。注释的每个元素都是包含几个字段的嵌入式文档。

## 结论

在本文中，我们介绍了使用 MongoDB 对 PhP 进行基本的 CRUD 介绍。我们甚至在这个过程中创建了一种非常原始的 MVC(参见 Github 上的完整应用程序)。使用一个合适的框架，实现这里使用的简单 HTTP auth 之外的身份验证，并添加更多的功能，这取决于您，但是基础已经就绪，您可以随心所欲地编写这个演示应用程序。

有关 MongoDB 的更多信息，请查看在线文档。你喜欢这篇文章吗？让我们知道你的想法！

## 分享这篇文章