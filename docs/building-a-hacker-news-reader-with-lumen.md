# 用 Lumen 构建一个黑客新闻阅读器

> 原文：<https://www.sitepoint.com/building-a-hacker-news-reader-with-lumen/>

在本教程中，我们将为黑客新闻构建一个阅读器。我们将使用[黑客新闻 API](https://github.com/HackerNews/API) 和 [Lumen 框架](https://lumen.laravel.com/)来实现这一点。

最终输出如下所示:

![Working Hacker News Reader](img/79c37dea6834852155294e3c13e95ea6.png)

如果你很兴奋，那就让我们开始吧。

## 安装和配置 Lumen

你需要做的第一件事是安装 Lumen。您可以使用以下命令来完成，其中`hnreader`是您希望项目安装到的文件夹，而`--prefer-dist`只是加快所需 Composer 包的下载速度:

```
composer create-project laravel/lumen hnreader --prefer-dist 
```

创建一个包含以下内容的`.env`文件:

```
APP_DEBUG=true

APP_TITLE=HnReader

DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=hnreader
DB_USERNAME=homestead
DB_PASSWORD=secret 
```

`APP_DEBUG`允许我们在 Lumen 中打开调试，以便我们可以看到应用程序中的错误。而`DB_*`用于数据库配置。我们将使用 MySQL 数据库来存储我们将从黑客新闻 API 获得的项目。这样，我们就不需要在每次用户访问应用程序时发出单独的 HTTP 请求。如果你使用的是[家园改良版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，你可能会保留`DB_CONNECTION`、`DB_HOST`、`DB_PORT`的值。当然，我们也需要创建数据库。

```
mysql -u homestead -psecret
CREATE DATABASE hnreader; 
```

接下来，让我们打开`bootstrap/app.php`文件并取消对以下行的注释:

```
Dotenv::load(__DIR__.'/../'); 
```

这一行从之前创建的`.env`文件中加载配置选项。

另外，取消对下面一行的注释，以便您可以使用 facades，例如`DB`:

```
$app->withFacades(); 
```

## 数据库ˌ资料库

对于这个应用程序，我们只需要一个表来存储我们从 API 获取的项目。您可以通过使用以下命令创建新迁移来创建该表:

```
php artisan make:migration create_items_table 
```

这将在`database/migrations`目录中创建新的迁移。打开文件，将`up`和`down`方法的内容更新如下:

```
public function up()
{
    Schema::create('items', function(Blueprint $table){
        $table->integer('id')->primary();
        $table->string('title');
        $table->text('description');
        $table->string('username');
        $table->char('item_type', 20);
        $table->string('url');
        $table->integer('time_stamp');
        $table->integer('score');
        $table->boolean('is_top');
        $table->boolean('is_show');
        $table->boolean('is_ask');
        $table->boolean('is_job');
        $table->boolean('is_new');
    });
}

public function down()
{
    Schema::drop('items');
} 
```

`up`方法所做的是创建*项*表。以下是每个字段的简要描述:

*   **id**–来自 API 的唯一项目 ID。
*   **标题**–项目的标题。这是我们稍后将在新闻页面中显示的字段。
*   **描述**–物品的简要描述。这将显示在悬停在工具提示。
*   **用户名**–在黑客新闻上提交文章的用户的用户名。
*   **item _ type**–项目的类型。这可以是*的故事*，也可以是*的工作*。
*   **url**–指向该项目的完整详细信息的 URL。这通常是添加的项目的网站，但也可以是空的，在这种情况下，该项目的完整描述可在黑客新闻网站上找到。
*   **time _ stamp**–提交时间的 unix 时间戳。
*   **分数**–该物品的当前排名。

以下是代表该项目是否属于头条新闻、显示 HN、询问 HN、黑客新闻招聘或是否是新发布的标志。

*   **is_top**
*   **is_show**
*   **is_ask**
*   **is_job**
*   **是 _ 新**

在`down`方法下，我们简单地删除表格。

```
Schema::drop('items'); 
```

要运行迁移，请使用以下命令:

```
php artisan migrate 
```

这将在数据库中创建表。

## 添加路线

打开`app/routes.php`文件并添加以下内容:

```
$app->get('/{type}', 'HomeController@index');
$app->get('/', 'HomeController@index'); 
```

第一种途径允许我们响应对特定类型项目的请求。比如工作、新故事、头条新闻等等。第二条路线是主页。

## 新闻更新者

为了添加或更新数据库中的新闻条目，我们使用 [Laravel 的任务调度器](https://laravel.com/docs/master/scheduling)。Lumen 基本上是 Laravel 的轻量级版本，因此任务调度器在 Lumen 中也是可用的。这允许我们在特定的时间更新数据库。例如，每天晚上 8 点。

要使用任务调度程序，我们首先需要创建一个任务。您可以通过在`app/Console/Commands`目录中创建一个新文件来做到这一点。将文件命名为`UpdateNewsItems.php`:

```
<?php
namespace App\Console\Commands;

use Illuminate\Console\Command;

use DB;
use GuzzleHttp\Client;

class UpdateNewsItems extends Command
{

    protected $name = 'update:news_items';

    public function fire()
    {

        $client = new Client(array(
            'base_uri' => 'https://hacker-news.firebaseio.com'
        ));

        $endpoints = array(
            'top' => '/v0/topstories.json',
            'ask' => '/v0/askstories.json',
            'job' => '/v0/jobstories.json',
            'show' => '/v0/showstories.json',
            'new' => '/v0/newstories.json'
        );

        foreach($endpoints as $type => $endpoint){

            $response = $client->get($endpoint);
            $result = $response->getBody();

            $items = json_decode($result, true);

            foreach($items as $id){
                $item_res = $client->get("/v0/item/" . $id . ".json");
                $item_data = json_decode($item_res->getBody(), true);

                if(!empty($item_data)){

                    $item = array(  
                        'id' => $id,
                        'title' => $item_data['title'],
                        'item_type' => $item_data['type'],
                        'username' => $item_data['by'],
                        'score' => $item_data['score'],
                        'time_stamp' => $item_data['time'],
                    );

                    $item['is_' . $type] = true;

                    if(!empty($item_data['text'])){
                        $item['description'] = strip_tags($item_data['text']);
                    }

                    if(!empty($item_data['url'])){
                        $item['url'] = $item_data['url'];
                    }

                    $db_item = DB::table('items')
                        ->where('id', '=', $id)
                        ->first();

                    if(empty($db_item)){

                        DB::table('items')->insert($item);

                    }else{

                        DB::table('items')->where('id', $id)
                            ->update($item);
                    }
                }
            }
        }
        return 'ok';

    }
} 
```

这个类继承自 Illuminate 的`Command`类，所以我们需要导入它。

```
use Illuminate\Console\Command; 
```

导入数据库类和 Guzzle，这样我们就可以使用数据库并使用 Guzzle HTTP 客户端发出 HTTP 请求。

```
use DB;
use GuzzleHttp\Client; 
```

指定命令的名称:

```
protected $name = 'update:news_items'; 
```

这允许我们使用 artisan 从终端执行该命令，如下所示:

```
php artisan update:news_items 
```

在`fire`方法下，创建一个新的 Guzzle 客户端实例，并声明一个包含 Hacker News API 中各种端点的数组。在这个应用程序的情况下，我们将只使用头条新闻的端点，问 HN，乔布斯，显示 HN 和新的故事。

```
$client = new Client(array(
    'base_uri' => 'https://hacker-news.firebaseio.com'
));

$endpoints = array(
    'top' => '/v0/topstories.json',
    'ask' => '/v0/askstories.json',
    'job' => '/v0/jobstories.json',
    'show' => '/v0/showstories.json',
    'new' => '/v0/newstories.json'
); 
```

遍历所有端点，以便我们可以添加或更新每个端点可用的新闻条目。

```
foreach($endpoints as $type => $endpoint){
    ...
} 
```

在循环内部，我们向 Hacker News API 发出请求，并将响应体的内容转换为一个数组。该数组包含为当前端点返回的新闻项目的 id。

```
$response = $client->get($endpoint);
$result = $response->getBody();

$items = json_decode($result, true); 
```

遍历所有这些 id，并向 API 发出一个单独的请求，以获得关于每一项的更多信息。这里我们使用的是*项端点* ( `/v0/item/{ITEM_ID}.json`)。一旦我们得到了响应，我们就根据 API 返回的数据构造将保存在数据库中的数据。请注意，我们正在检查响应是否确实有一些内容。这是因为有时从特定端点(例如，top stories)返回的 id 实际上并不指向实际的项目。

```
foreach($items as $id){
    $item_res = $client->get("/v0/item/" . $id . ".json");
    $item_data = json_decode($item_res->getBody(), true);

    if(!empty($item_data)){

        $item = array(  
            'id' => $id,
            'title' => $item_data['title'],
            'item_type' => $item_data['type'],
            'username' => $item_data['by'],
            'score' => $item_data['score'],
            'time_stamp' => $item_data['time'],
        );
    }

    ... 
```

为了能够根据用户稍后访问的路线轻松获取项目，我们将特定类型的值设置为`true`。

```
$item['is_' . $type] = true; 
```

如果项目中有描述和 URL，请设置它们。

```
if(!empty($item_data['text'])){
    $item['description'] = strip_tags($item_data['text']);
}

if(!empty($item_data['url'])){
    $item['url'] = $item_data['url'];
} 
```

如果数据库中的项目不存在，则为其创建一个新行，如果已经存在，则更新该行。

```
$db_item = DB::table('items')
    ->where('id', '=', $id)
    ->first();

if(empty($db_item)){

    DB::table('items')->insert($item);

}else{

    DB::table('items')->where('id', $id)
        ->update($item);
} 
```

在函数的末尾返回一些东西，表示函数在这里结束。

```
return 'ok'; 
```

现在我们已经创建了一个新任务，是时候将它添加到控制台内核中了。打开`app/Console/Kernel.php`文件即可。在 commands 数组下，添加我们刚刚创建的任务的路径。

```
protected $commands = [
     'App\Console\Commands\UpdateNewsItems',
]; 
```

在`schedule`功能下，添加命令并指定它将运行的时间。`19:57`在这里意味着命令`update:news_items`应该在每天晚上 7:57 运行。

```
protected function schedule(Schedule $schedule)
{
    $schedule->command('update:news_items')->dailyAt('19:57');
} 
```

接下来，您需要确保应用程序使用与服务器相同的时区。打开`.env`文件并添加时区配置:

```
APP_TIMEZONE=Asia/Manila 
```

只需将`Asia/Manila`更改为适用于您的服务器的有效 PHP 时区。您可以在[支持时区列表](http://php.net/manual/en/timezones.php)页面中获得有效时区列表。

最后，通过执行以下命令向 cron 添加一个新项目:

```
sudo crontab -e 
```

并在最后一行添加以下内容:

```
* * * * * php /path/to/hn-reader/artisan schedule:run >> /dev/null 2>&1 
```

确保将`/path/to/hn-reader/`更改为应用程序在文件系统中的实际路径。您可以通过在应用程序的根目录中执行以下命令来进行测试。

```
php artisan schedule:run 
```

当然，如果调度程序中指定的时间与您运行命令的时间不匹配，这将不会有任何影响，所以要进行真正的测试。

## 新闻页面

对于新闻页面，创建`app/Http/controllers/HomeController.php`:

```
<?php

namespace App\Http\Controllers;

use Laravel\Lumen\Routing\Controller as BaseController;
use DB;

class HomeController extends BaseController {

    private $types;

    public function __construct(){
        $this->types = array(
            'top',
            'ask',
            'job',
            'new',
            'show'
        );
    }

    public function index($type = 'top'){

        $items = DB::table('items')
            ->where('is_' . $type, true)
            ->get();

        $page_data = array(
            'title' => $type,
            'types' => $this->types,
            'items' => $items
        );

        return view('home', $page_data);
    }

} 
```

在类内部，我们声明了一个名为`$types`的私有变量。这是我们存储可以在新闻页面中查看的项目类型的地方。请注意，这些键与我们之前在更新新闻条目的任务中的`$endpoints`数组中使用的键相同。

在`index`函数中，我们接受类型作为参数，默认为`top`。这样，默认情况下，主页会显示头条新闻。

然后，我们根据设置为`true`的`type`获取项目。每个项目可以启用多种类型。例如，一个招聘启事也可能是头条新闻之一，因此表中的`is_job`和`is_top`字段都有一个值`true`，这意味着有些条目在不同的页面上重复出现。

一旦我们获取了项目，我们现在可以将它们添加到一个数组中，作为新闻页面的数据。这里我们还传入了不同的类型，以便用户可以选择他们想要查看的 HN 项目。页面标题设置为当前类型。

```
$page_data = array(
    'title' => $type,
    'types' => $this->types,
    'items' => $items
);

return view('home', $page_data); 
```

用于显示新闻的视图(`resources/views/home.blade.php`)包含以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ env('APP_TITLE') }}</title>
    <link rel="stylesheet" href="{{ url('assets/css/hint.min.css') }}">
    <link rel="stylesheet" href="{{ url('assets/css/style.css') }}">
</head>
<body>
    <div id="sidebar">
        <h3>{{ env('APP_TITLE') }}</h3>
        <ul id="types">
            @foreach($types as $type)
            <li>
                <a href="/{{ $type }}">{{ ucwords($type) }}</a>
            </li>
            @endforeach
        </ul>
    </div>

    <div id="items-container">
        <h1>{{ $title }}</h1>
        <ul id="items">
            @foreach($items as $item)
            <li class="item">
                <span class="item-score">{{ $item->score }}</span>
                <a href="{{ URLHelper::getUrl($item->id, $item->url) }}">
                    <span class="item-title hint--bottom" data-hint="{{ str_limit(strip_tags($item->description), 160) }}">{{ $item->title }}</span>
                    <span class="item-info">posted {{ \Carbon\Carbon::createFromTimestamp($item->time_stamp)->diffForHumans() }} by {{ $item->username }}</span>
                </a>
            </li>
            @endforeach 
        </ul>
    </div>
</body>
</html> 
```

我们通过调用`env`函数从`.env`文件加载`APP_TITLE`。

然后，我们使用`url`助手为 [hint.css](http://kushagragour.in/lab/hint/) 和主样式表生成 URL。接下来，我们遍历所有类型，并相应地格式化无序列表。

下一部分显示了正在查看的当前类型，并遍历了从数据库中获取的所有项目。这里我们使用一个名为`URLHelper`的自定义帮助器类来返回链接到实际项目的正确 URL。这是必要的，因为有些项目实际上没有网站，所以 URL 将是分配给该特定项目的黑客新闻页面。所有的[问 HN 的事项](https://news.ycombinator.com/ask)都是如此。我们很快就会看到这个助手的代码。现在，只要记住我们将 ID 和 URL 传递给该类的`getURL`函数。

对于项目发布的时间，我们将 unix 时间戳转换为人类友好的时间，比如`4 seconds ago`。这是在碳 T2 的帮助下完成的。

对于项目描述，我们使用 [hint.css](http://kushagragour.in/lab/hint/) 来显示它。在包含项目标题的 span 中，我们添加了`hint--bottom`类来控制工具提示的位置，而`data-hint`包含描述文本，通过使用`str_limit`助手函数，该文本被限制为 160 个字符。

在`URLHelper` ( `app/Helpers/URLHelper.php`)中，`getURL`函数检查 URL 是否为空。如果不是，那么它返回 URL。否则，它返回指向分配给该项目的页面的 HN URL。

```
<?php

class URLHelper {

    public static function getURL($id, $url = ''){
        if(!empty($url)){
            return $url;
        }
        return "https://news.ycombinator.com/item?id={$id}";
    }
} 
```

在我们可以使用这个助手之前，我们需要在`composer.json`中再做一个修改。在`autoload`对象下，寻找`classmap`。这是一个包含自动加载文件的目录路径的数组。因为我们已经将`URLHelper`保存在了`app/Helpers`目录下，所以我们将`app/Helpers`添加到了`classmap`数组中。

```
"autoload": {
    "psr-4": {
        "App\\": "app/"
    },
    "classmap": [
        "database/",
        "app/Helpers"
    ]
}, 
```

最后，我们添加样式表(`public/assets/css/style.css`)。

```
body {
    font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
    padding: 0;
    margin: 0;
}

h1 {
    padding-left: 40px;
}

#sidebar {
    width: 20%;
    float: left;
    background-color: #EBEBEB;
    position: fixed;
    height: 100%;
}

#items-container {
    width: 80%;
    float: left;
    position: relative;
    margin-left: 20%;
    background-color: #F7F7F7;
}

ul li {
    list-style: none;
}

#sidebar h3 {
    border-bottom: 3px solid;
    padding: 0;
    padding-left: 30px;
}

#types li {
    padding: 10px 30px;
}

ul#types {
    padding: 0;
}

#types li a {
    text-decoration: none;
    color: #575757;
}

#items {
    padding: 0 20px;
}

#items li a {
    text-decoration: none;
    color: #3A3A3A;
    display: inline-block;
}

#items li {
    padding: 20px;
}

#items li:hover {
    background-color: #DFDFDF;
}

.item-score {
    font-weight: bold;
    display: inline-block;
    width: 50px;
    border-radius: 50%;
    background-color: #ccc;
    height: 50px;
    text-align: center;
    line-height: 50px;
    margin-right: 10px;
}

.item-info {
    display: inline-block;
    width: 100%;
    font-size: 15px;
    color: #8A8A8A;
    margin-top: 5px;
} 
```

## 结论

就是这样！在本教程中，您已经学习了如何使用 Hacker News API 来构建新闻阅读器。我们在本教程中只使用了 API 的一部分，所以如果你想使用它的更多特性，请务必查看[文档](https://github.com/HackerNews/API)。本教程中使用的所有源代码都可以在这个 [Github repo](https://github.com/sitepoint-editors/hn-reader) 中获得。有问题吗？评论？建议？把它们留在下面！

## 分享这篇文章