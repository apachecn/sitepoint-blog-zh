# 用 PHP 和 Google BigQuery 探索 Github 的公共事件

> 原文：<https://www.sitepoint.com/exploring-githubs-public-events-php-google-bigquery/>

如果你一直在关注我之前关于 [Github 的 API](https://www.sitepoint.com/use-githubs-api-php/) 的文章，你就会知道 Github 的开发者正在尽最大努力减轻与 Github 数据交互的痛苦。在本文中，我们将看看 Github 公共事件 API，并在此过程中构建一个小演示。

![Github Logo](img/a0d396a6fb51a46fba664d443ca4b376.png)

## 什么是 Github 公共事件？

Github 事件是用户进行的交互，如推送、合并、创建存储库等。就像存储 Github 的历史一样。然而，我们仅限于最近 300 个事件，如果你想搜索整个历史的数据，这是一个问题。

[伊利亚·格里戈利克](https://twitter.com/igrigorik)想要跟踪他的开源项目，但是由于 Github 公共事件 API 的限制，他决定创建 [GithubArchive](https://www.githubarchive.org/) 网站，在那里他查询 Github API 并存储结果。您可以使用类似于`http://data.githubarchive.org/2015-01-01-15.json.gz`的链接查询归档文件，您将获得包含 JSON 有效负载的归档文件。这种解决方案并不真正有效，也无助于查询数据的历史，所以，当 [Google BigQuery](https://cloud.google.com/bigquery/) 发布时，格里戈利克将他所有的数据都转移到了新的存储中，并公开了这些数据。

## 为什么是 BigQuery？

Google BigQuery 是为了解决查询延迟问题而创建的。当您处理大数据时，您需要有合适的硬件和基础架构来正确处理负载。现在，您有机会将仅附加的数据库移动到 Google 的基础设施，并看到数百万数据行在几秒钟内得到处理。
如果你不熟悉谷歌 API，一定要看看这个关于[开始一个新的谷歌 API 项目](https://community.sitepoint.com/t/starting-a-new-google-api-project/117282)的指南。之后，确保在您的 Google 开发者控制台上启用 BigQuery API。

## 设置环境

我将使用 Laravel 作为我选择的框架，并且为我的 VM 改进了[Homestead](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。我们将从`config/google.php`文件中获取我们的 Google 凭证，为了遵循 Laravel 的安全约定，我将这些凭证存储在`.env`文件中。

```
// config/google.php

return [
    'app_name'      => env('app_name'),
    'client_id'     => env('client_id'),
    'client_secret' => env('client_secret'),
    'api_key'       => env('api_key')
];
```

```
// .env

APP_DEBUG=true
app_name='Optional app name'
client_id='CHANGE IT'
client_secret='CHANGE IT'
api_key='CHANGE IT'
```

我还将创建一个新的[服务提供者](http://laravel.com/docs/master/providers)来保存我们的 Google 服务绑定。

```
// app/Providers/BigQueryServiceProvider.php

class BigQueryServiceProvider extends ServiceProvider 
{

	public function register()
	{
        $this->app->bind('GoogleClient', function () {
            $googleClient = new \Google_Client();
            $googleClient->setAccessToken(\Session::get("token"));

            return $googleClient;
        });

        $this->app->bind('bigquery', function () {
            $googleClient = \App::make('GoogleClient');
            $bigquery = new \Google_Service_Bigquery($googleClient);

            return $bigquery;
        });
	}
}
```

```
// config/app.php

// ...
'providers' => [
    // ...
    'App\Providers\BigQueryServiceProvider',
]
```

因为我们的应用程序需要用户授权来查询 API，所以我试图通过创建一个[中间件](http://laravel.com/docs/5.0/middleware)来保护我们的路线，从而使这个过程变得更容易。

```
// app/Http/Middleware/GoogleLogin.php

class GoogleLogin
{
  public function handle($request, Closure $next)
  {
      $ga = \App::make('\App\Services\GoogleLogin');
      if (!$ga->isLoggedIn()) {
          return redirect('login');
      }

      return $next($request);
  }
}
```

```
// app/Kernel.php

protected $routeMiddleware = [
    // ...
    'google_login' => '\App\Http\Middleware\GoogleLogin'
];
```

## Google BigQuery 控制台

在我们开始使用 PHP 库处理 BigQuery 之前，让我们先来探索一下 [BigQuery 查询控制台](https://bigquery.cloud.google.com/)。控制台有一个`Compose Query`按钮，让你写下你的查询。在下面，我们有一个显示查询历史的`Query History`链接。因为我们的查询需要一段时间才能得到响应，所以我们可以将查询作为一个作业来执行，并在一段时间后请求它的统计数据——`Job History`链接保存了当前作业的列表。

![BigQuery Console](img/52ac958a0d6e7c56e2027d9403dad79b.png)

如果您想查看表模式并探索可用的字段，您可以导航到左侧面板并选择 Github timeline 表。

![Table schema](img/7d38179fd1924aa3ed362ec6f993e2e4.png)

## Google BigQuery API 和 Github

举个例子，我们试着检索一下 Github 上的十大热门项目。我将流行度定义为分叉的数量。`Google_Service_Bigquery`类负责与 Google BigQuery API 的所有类型的交互。我之前介绍了术语`jobs`，这意味着我们可以在后台执行一个查询，并且我们可以随时获取它的状态，以查看它是否完成。

```
// app/Http/routes.php

Route::any('/topTen', ['middleware' => 'google_login', 'as' => 'topTen', 'uses' => 'BigQueryAPIController@topTen']);

// app/Http/Controllers/BigQueryAPIController.php

class BigQueryAPIController extends Controller
{
    public function __construct()
    {
        $this->bigqueryService = \App::make("bigquery");
    }

    public function topTen()
    {
        $projectID = 'modular-robot-22';
        $query_str = "SELECT repository_url, MAX(repository_forks) as max_forks" .
            " FROM githubarchive:github.timeline" .
            " GROUP EACH BY repository_url" .
            " ORDER BY max_forks DESC" .
            " LIMIT 10";

        $query = new \Google_Service_Bigquery_QueryRequest();
        $query->setQuery($query_str);

        $result = $this->bigqueryService->jobs->query($projectID, $query);

        $fields = $result->getSchema()->getFields();
        $rows = $result->getRows();

        dump($fields);
        foreach ($rows as $row) {
            dump($row->getF());
        }
    }    
}
```

项目 ID 可以在[谷歌开发者控制台](https://console.developers.google.com/project)上找到。查询字符串正在获取`respository_url`和`MAX(repository_forks)`。`getSchema`方法返回一个`Google_Service_Bigquery_TableSchema`实例，它包含从响应中选择的列名，`getRows`方法返回一个`Google_Service_Bigquery_TableRow`的可迭代列表。输出如下所示。

![Top Ten Query Dump](img/aafbc2c70fc6c705d235b0f8079e4400.png)

`githubarchive:github.timeline`数据集已被弃用，将不再更新。处理数据的新方式是通过特定的时间范围(年数据集、月数据集、日数据集)；你可以访问 [githubarchive](https://www.githubarchive.org/) 来了解更多这方面的信息。现在我们必须更新上面的查询，以便使用去年的数据。

```
// app/Http/Controllers/BigQueryAPIController.php

public function topTen()
{
    $projectID = 'modular-robot-22';
    $query_str = "SELECT repository_url, MAX(repository_forks) as max_forks" .
        " FROM githubarchive:year.2014" .
        " GROUP EACH BY repository_url" .
        " ORDER BY max_forks DESC" .
        " LIMIT 10";

    //...
}
```

## 按语言排列的十大知识库

我们将修改前面的查询来提取特定语言的存储库。我们的视图标记将包含一个表单和一组语言选项。

```
// resources/views/home.blade.php

<form action="/" method="POST" class="form-inline">
    <label for="language">Language: </label>
    <select name="language" id="language" class="form-control">
        <option value="PHP">PHP</option>
        <option value="JavaScript">JavaScript</option>
        <option value="CSS">CSS</option>
        <option value="Python">Python</option>
    </select>

    <input type="submit" class="form-control" value="Submit"/>
</form>
```

当用户提交表单时，我们检索选择的语言并在 SQL 查询中填充条件。因为我们将把查询结果打印成表格，所以我们将把表头部分从表体中分离出来。

```
// app/Http/Controllers/BigQueryAPIController.php

public function topTen()
{
    if (!\Input::has('language')) {
        return \View::make('home', ['repos' => null]);
    }

    $language = \Input::get('language', 'PHP');
    $projectID = 'modular-robot-647';
    $query_str = "SELECT repository_url, MAX(repository_forks) as max_forks" .
        " FROM githubarchive:year.2014" .
        " WHERE repository_language='$language'" .
        " GROUP EACH BY repository_url" .
        " ORDER BY max_forks DESC" .
        " LIMIT 10";

    $query = new \Google_Service_Bigquery_QueryRequest();
    $query->setQuery($query_str);

    $result = $this->bigqueryService->jobs->query($projectID, $query);
    // getting the table header
    $fields = $result->getSchema()->getFields();
    // query response rows
    $repos = $result->getRows();

    return \View::make('home', ['repos' => $repos, 'tableHeader' => $fields]);
}
```

与前一个查询唯一不同的是`where`条件，它根据用户输入过滤语言。在打印结果表时，我试图保持大多数查询的通用性。

```
// resources/views/home.blade.php

@if($repos)
    <table class="table table-hover">
        <tr>
            @foreach($tableHeader as $item)
                <th>{{$item->getName()}}</th>
            @endforeach
        </tr>
        @foreach($repos as $repo)
            <tr>
                @foreach($repo->getF() as $item)
                    <td>{{$item->getV()}}</td>
                @endforeach
            </tr>
        @endforeach
    </table>
@else
    <h2>Nothing to show</h2>
@endif
```

`getF()`方法返回行内容，而`getV()`方法返回单元格内容。您可以通过转储类名和遍历属性来检查结果值。

![PHP top ten](img/a09c306c5e7228322317e661099ca22b.png)

在我测试的时候，看起来 CodeIgniter 在上面，但是在我写这篇文章的时候，情况并非如此——Laravel 是 Github 上最流行的 PHP 框架。确保查看 Github [趋势库页面](https://github.com/trending)以查看几乎实时的数据。

如果您选择使用 post `01/01/2015`添加的数据，该模式还将包含嵌套记录和一个 JSON 编码的有效载荷字段。这也适用于日数据集——您可以使用 Google BigQuery 控制台来检查数据集模式。BigQuery 有一些有用的 [JSON 函数](https://cloud.google.com/bigquery/query-reference#jsonfunctions)来处理 JSON 编码的数据，一定要看看它们。

## 结论

在本文中，我们介绍了 Github 公共事件以及如何通过 BigQuery 使用它们。[最终演示](https://github.com/Whyounes/GithubEventsAPIDemo)包括一个基本的使用示例，但是你可能想尝试一下这个 API，并且用一些很酷的 Github 数据事实为[年度 Github 数据挑战赛](https://www.githubarchive.org/#resources)做好准备。你对 Github Events API 有什么看法？对于数据挑战，你有什么好主意吗？让我们知道！

## 分享这篇文章