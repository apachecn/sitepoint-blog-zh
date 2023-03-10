# 使用 Solaris 和 SOLR 进行搜索–Solaris 和 GUI

> 原文：<https://www.sitepoint.com/using-solarium-solr-search-solarium-gui/>

这是关于结合 Apache 的 SOLR 搜索实现使用 Solarium 的四篇系列文章中的第二篇。

在[的第一部分](https://www.sitepoint.com/using-solarium-solr-search-setup/)，我介绍了关键概念，我们安装并设置了 SOLR。在第二部分中，我们将安装 Solarium，开始构建一个示例应用程序，填充搜索索引，并进入可以开始运行搜索的位置。

## 创建应用程序

通过 Composer 创建新的 Laravel 应用程序:

```
composer create-project laravel/laravel movie-search --prefer-dist 
```

使存储目录`app/storage`可写。

## 安装日光浴室

您将从第一部分回忆起，[Solaris 项目](http://www.solarium-project.org)提供了对底层 SOLR 请求的抽象级别，这使您能够编写代码，就像 SOLR 是本地运行的本地搜索实现一样，而不用担心发出 HTTP 请求或解析响应。

到目前为止，安装 Solarium 的最佳方式是使用 Composer:

```
"solarium/solarium": "dev-develop" 
```

或者你可以从 Github 下载或者克隆它[。](https://github.com/basdenooijer/solarium)

如果你继续开发电影搜索应用程序，它将会像其他第三方包一样，被放入你新创建的项目的`composer.json`文件中；在这种情况下，没有 Laravel 服务提供商。

## 配置日光浴室

Solarium 客户机的构造函数接受一组连接细节，因此创建一个配置文件–`app/config/solr.php`,如下所示:

```
return array(
    'host'      => '127.0.0.1',
    'port'      => 8983,
    'path'      => '/solr/',
): 
```

如果您已经按照第一部分中的说明安装了 SOLR，这些默认值应该就可以了，尽管在某些情况下您可能需要更改端口号。

为简单起见，我们将创建一个 Solarium 客户机实例作为控制器的属性(`app/controllers/HomeController.php`):

```
 /**
     * @var The SOLR client.
     */
    protected $client;

    /**
     * Constructor
     **/
    public function __construct()
    {
        // create a client instance 
        $this->client = new \Solarium\Client(Config::get('solr'));
    }
```

通常在 Laravel 中，您会在一个绑定到 IoC 容器的服务提供者中创建一个实例，但是这种方式对于一个非常简单的应用程序来说也很好。

## Ping 查询

ping 查询对于检查 SOLR 服务器是否正在运行和可访问非常有用，因此是一个很好的起点。使用 Solarium 很简单，因此您可能希望通过使用以下命令来测试一切是否正常:

```
// create a ping query
$ping = $client->createPing();

// execute the ping query
try {
    $result = $client->ping($ping);
} catch (Solarium\Exception $e) {
    // the SOLR server is inaccessible, do something
}
```

如示例所示，不可访问的 SOLR 实例会抛出异常，因此您可以通过在此时捕捉它来采取相应的措施。

## 抽样资料

出于本教程的目的，我们将建立一个简单的电影搜索。我已经创建了一个 CSV 文件，包含大约 2000 部围绕一堆关键词(例如空间、夜晚和房子)的电影，你可以下载，或者如果你想创建自己的文件，你可能想查看一下[烂番茄 API](http://developer.rottentomatoes.com/) 。(顺便说一句，但这是相关的，IMDB 使他们的数据可用，但分布在许多 CSV 文件中——其中一些文件很大——并且只通过网站或 FTP 提供。)

在我们编写导入这些数据的命令之前，让我们看看使用 Solarium 的 SOLR 搜索实现的基本**创建**、**更新**和**删除**操作。

## 向搜索索引添加文档

要将文档添加到搜索索引中，首先需要创建一个更新查询实例:

```
$update = $client->createUpdate(); 
```

然后创建一个文档:

```
$doc = $update->createDocument(); 
```

现在，您可以将文档(`$doc`)视为一个`stdClass`，并简单地将数据指定为公共属性:

```
$doc->id = 123;
$doc->title = 'Some Movie';
$doc->cast = array('Sylvester Stallone', 'Marylin Monroe', 'Macauley Culkin'); 
```

…依此类推，然后将文档添加到更新查询中:

```
$update->addDocument($doc); 
```

然后提交更新:

```
$update->addCommit(); 
```

最后，要在客户机上实际运行您调用的查询`update()`:

```
$result = $client->update($update); 
```

如果您希望验证您已经成功地索引了一些文档，请在您的浏览器中访问 SOLR 管理面板——通常是`http://localhost:8983/solr`,然后单击核心管理，您将在`Index`部分中找到作为`numDocs`列出的索引中的文档数量。

## 更新文档

如果你需要更新索引中的一个文档，你只需要“重新添加”它——假设它有相同的唯一标识符——SOLR 会聪明地更新它，而不是添加一个新的。

## 删除文档

您可以使用更新查询从索引中删除文档，使用的语法与 SQL 中的 WHERE 子句没有太大的不同。例如，要删除由 ID 123 唯一标识的文档:

```
// get an update query instance
$update = $client->createUpdate();

// add the delete query and a commit command to the update query
$update->addDeleteQuery('id:123');
$update->addCommit();

// this executes the query and returns the result
$result = $client->update($update); 
```

或者你可以不那么具体:

```
// get an update query instance
$update = $client->createUpdate();

// add the delete query and a commit command to the update query
$update->addDeleteQuery('title:test*');
$update->addCommit();

// this executes the query and returns the result
$result = $client->update($update); 
```

注意通配符的使用——换句话说:“删除标题以 test 开头的所有文档”。

## 用电影填充搜索索引

现在我们已经了解了索引文档的基本原理，让我们将一些数据放入示例应用程序的索引中。

Laravel 使构建控制台命令变得很容易，所以让我们创建一个来导入我们的电影 CSV 文件的内容并对它们进行索引。我们也可以在这一点上创建相应的数据库记录，但是出于本练习的目的，索引文档将包含我们需要的所有内容。

要创建命令，请在命令行中输入以下内容:

```
php artisan command:make PopulateSearchIndexCommand 
```

在新生成的文件中–`app/commands/PopulateSearchIndexCommand.php`–设置命令的名称(即，您在命令行上输入来运行它的内容)和描述:

```
/**
 * The console command name.
 *
 * @var string
 */
protected $name = 'search:populate';

/**
 * The console command description.
 *
 * @var string
 */
protected $description = 'Populates the search index with some sample movie data.'; 
```

现在我们将使用`fire()`方法创建 Solarium 客户机，打开 CSV，遍历它并为每部电影建立索引:

```
/**
 * Execute the console command.
 *
 * @return void
 */
public function fire()
{       
    $client = new \Solarium\Client(Config::get('solr'));

    // open up the CSV
    $csv_filepath = storage_path() . '/movies.csv';

    $fp = fopen($csv_filepath, 'r');

    // Now let's start importing
    while (($row = fgetcsv($fp, 1000, ";")) !== FALSE) {

        // get an update query instance
    $update = $client->createUpdate();

    // Create a document
        $doc = $update->createDocument();    

        // set the ID
    $doc->id = $row[0];

    // ..and the title
        $doc->title = $row[1];

        // The year, rating and runtime columns don't always have data
        if (strlen($row[2])) {
            $doc->year = $row[2];
        }
        if (strlen($row[3])) {
            $doc->rating = $row[3];
        }
        if (strlen($row[4])) {
            $doc->runtime = $row[4];
        }

        // set the synopsis
        $doc->synopsis = $row[5];

        // We need to create an array of cast members
        $cast = array();

        // Rows 6 through 10 contain (or don't contain) cast members' names
        for ($i = 6; $i <= 10; $i++) {
            if ((isset($row[$i])) && (strlen($row[$i]))) {
                $cast[] = $row[$i];
            }
        }

        // ...then we can assign the cast member array to the document
        $doc->cast = $cast;

        // Let's simply add and commit straight away.
        $update->addDocument($doc);
    $update->addCommit();

    // this executes the query and returns the result
    $result = $client->update($update);

    }

    fclose($fp);
} 
```

虽然这不是最巧妙或最灵活的代码，但这是一个相当学术性的练习，我们只打算运行一次。确保 CSV 文件位于正确的位置—`app/storage/movies.csv`并运行它:

```
php artisan search:populate 
```

一切顺利的话，索引现在应该包含大约 2300 部电影。您可以通过管理界面检查这一点。

在下一节中，我们将开始在中构建基本搜索。

### 搜索表单

让我们使用 Laravel 的[刀片模板引擎](http://laravel.com/docs/templates)和[表单生成器](http://laravel.com/docs/html)来创建搜索表单。所以，在`app/views/home/index.blade.php`中:

```
@extends('layouts.default')

@section('content')

<header>
    {{ Form::open(array('method' => 'GET')) }}
    <div class="input-group">
        {{ Form::text('q', Input::get('q'), array('class' => 'form-control input-lg', 'placeholder' => 'Enter your search term')) }}        
        <span class="input-group-btn">
            {{ Form::submit('Search', array('class' => 'btn btn-primary btn-lg')) }}            
        </span>
    </div>
    {{ Form::close() }}
</header>

@endsection 
```

`app/views/layouts/default.blade.php`中的基本页面布局:

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Movie Search</title>

    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.3/css/bootstrap.min.css">

</head>
<body>
    <div class="container">
        <header id="header" class="row">        
            <div class="col-sm-12 col-md-12 col-lg-12">
                <h1>Movie Search</h1>
            </div>      
        </header>

        <div class="row">
            <div class="col-sm-12 col-md-12 col-lg-12">
                @yield('content')
            </div>
        </div>

        <hr />

        <footer id="footer">

        </footer>

    </div>

</body>
</html> 
```

在`app/controllers/HomeController.php`中:

```
public function getIndex()
{
    return View::make('home.index');
} 
```

最后，用以下内容替换`app/routes.php`的内容，告诉 Laravel 使用`HomeController`:

```
<?php
/*
|--------------------------------------------------------------------------
| Application Routes
|--------------------------------------------------------------------------
*/
Route::controller('/', 'HomeController'); 
```

现在我们已经准备好实现基本的搜索机制，这将是本系列下一部分的主题。

## 摘要

在这个由四部分组成的系列文章的第二部分中，我们安装了 Solarium，目的是将 Apache 的 SOLR 与 PHP 应用程序集成在一起。我们已经建立了一个示例应用程序并索引了一些数据。在下一部分中，我们将开始实现搜索机制。

## 分享这篇文章