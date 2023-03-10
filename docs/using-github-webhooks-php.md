# 在 PHP 中使用 Github Webhooks

> 原文：<https://www.sitepoint.com/using-github-webhooks-php/>

在我们系列的第一部分中，我们谈到了 Github API，并在此过程中构建了一个演示。在这一部分中，我们将探索 Webhooks API，并构建一个演示来展示 API 的用法。让我们开始吧。

![Github logo](img/a0d396a6fb51a46fba664d443ca4b376.png)

## 我们正在建造的东西

在本文中，我们将建立一个演示程序，接收来自 Github 的每次推送，并将其保存到数据库中。我们还将创建一个页面来显示我们的存储库提交者的列表，该列表根据提交的数量进行排序。你可以在 [Github](https://github.com/sitepoint-editors/GithubAPI_Demo) 上查看最终结果。

## 设置环境

我们将使用 [Laravel 5](http://laravel.com) 作为我们的框架，因为我们的应用程序必须可以从互联网访问才能与 Github Webhooks 一起工作，所以我们可以使用 [Heroku](https://devcenter.heroku.com/articles/getting-started-with-php) 来托管我们的代码。你可以[查看这个帖子](https://www.sitepoint.com/8-heroku-addons-production-ready-apps/)开始学习 Heroku。

## 配置

### 添加 Mysql

在 Heroku 上创建我们的应用程序后，我们需要添加 MySQL 来存储接收到的数据。

```
heroku addons:add cleardb
```

您可以转到您的[仪表板](https://dashboard.heroku.com/apps)获取您的数据库连接凭证。在您的[配置](https://devcenter.heroku.com/articles/config-vars)中设置必要的变量，如下例所示。

```
MYSQL_HOST=‘HOST’
MYSQL_DATABASE=‘DB’
MYSQL_USERNAME=‘USERNAME’
MYSQL_PASSWORD=‘PASSWORD’
```

### 注册 Github 挂钩

要注册一个新的挂钩，请导航到您帐户内的存储库，然后转到设置选项卡并选择`Webhooks & Services`。

![Register Webhook](img/11e320815054ee67a8409611ff0f1642.png)

在设置应用程序 URL 端点以接收有效负载之后，您可以设置一个密钥与有效负载一起发送，以验证请求来源。默认情况下，Github 只会向您发送推送事件，但是您可以从列表中选择单个事件。对于我们的演示，推送事件就足够了。

## 构建演示

### 创建数据库表

```
php artisan make:migration create_hooks_table
```

```
// database/migrations/XXX_create_hooks_table.php

class CreateHooksTable extends Migration
{

  public function up()
  {
    Schema::create('hooks', function (Blueprint $table) {
      $table->increments('id');
      $table->string('event_name', 100);
      //you can separate the sender, repo...etc. but let's just keep it simple
      $table->text('payload');
      $table->timestamps();
    });
  }

  public function down()
  {
    Schema::drop('hooks');
  }

}
```

我们只需要一个`hooks`表来存储传入的请求。如果我们计划跟踪其他事件，注册`event_name`将在将来对我们有所帮助，并且它将使我们更容易查找数据库。
有效载荷字段包含请求体。您可以将该字段分成多个部分，并创建发送者、存储库、提交列表等。

在使用`php artisan migrate`运行迁移之后，我们将创建我们的`Hook`模型类。

```
// app/Hook.php

class Hook extends Model
{

  protected $table = ‘hooks’;

}
```

### 创建路线

我们的演示应用程序只有两条路线。

*   一个`events`路由，它将接收 Github 事件负载。
*   一条`/report/contributions`路线，它将显示项目参与者的图表。

```
// app/Http/routes.php

Route::post(‘/events’, [‘uses’ => ‘GithubController@storeEvents’]);
```

```
// app/Http/Controllers/GithubController.php

public function storeEvents(Request $request) {
  $event_name = $request->header(‘X-Github-Event’);
  $body = json_encode(Input::all());

  $hook = new Hook;
  $hook->event_name = $event_name;
  $hook->payload = $body;

  $hook->save();

  return ‘’;// 200 OK
}
```

`X-Github-Event`头值包含发生的事件名称。在这种情况下，它将等于`push`。如果您想使用之前在 Github 上提供的密钥来测试请求源，您可以使用`X-Hub-Signature`头值。你可以在[文档](https://developer.github.com/webhooks/#payloads)中了解更多信息。

Github 会向你发送一个几乎实时的请求。如果您使用 Heroku 进行部署，那么您可以在测试时使用 logger 来监控请求。你可以在[文档](https://devcenter.heroku.com/articles/logging)中读到更多。您也可以在 Github 上导航到您的 Webhook 配置，并滚动到底部的`Recent Deliveries`选项卡。

```
heroku logs —tail -n 0 —ps router
```

![Test push](img/27131182080b764f77031eae49d7c7b1.png)

为了显示贡献者列表，我将使用 [ChartJs](http://www.chartjs.org) JavaScript 库。你可以看看这个[入门指南](https://www.sitepoint.com/creating-beautiful-charts-chart-js/)来获得一个很好的介绍。

```
// app/Http/routes.php

Route::get(‘/reports/contributions.json’, [‘uses’ => ‘GithubController@contributionsJson’]);
```

```
// app/Http/Controllers/GithubController.php
public function contributionsJson()
{
  $hooks = Hook::where('event_name', '=', 'push')->get(['payload']);

  $users = [];
  $hooks->each(function ($item) use (&$users) {
    $item = json_decode($item['payload']);

    $pusherName = $item->pusher->name;
    $commitsCount = count($item->commits);

    $users[$pusherName] = array_pull($users, $pusherName, 0) + $commitsCount;
  });

  return [
      'users'   => array_keys($users),
      'commits' => array_values($users)
  ];
}
```

`/reports/contributions.json`路由将从我们的数据库中检索推送事件的列表，并通过它循环检索提交的用户列表。然后我们返回用户列表，并分别提交以满足库的需要。但是，您也可以选择在前端使用一些 Javascript 来完成。

```
// app/Http/routes.php

Route::get(‘/reports/contributions’, [‘uses’ => ‘GithubController@contributions’]);
```

```
// app//Http/Contrllers/GithubController.php

public function contributions()
{

  return View::make('reports.contributions');
}
```

```
// resources/views/reports/contributions.blade.php

<canvas id=“contributions” width=“400” height=“400”></canvas>

<script>
    var ctx = document.getElementById(“contributions”).getContext(“2d”);

    $.ajax({
        url: “/reports/contributions.json”,
        dataType: “json”,
        type: “GET”,
        success: function(response){
            var data = {
                labels: response.users,
                datasets: [
                {
                    data: response.commits
                }]
            };

            new Chart(ctx).Bar(data, {});
        }
    });
</script>
```

从 JSON 端点获得数据后，我们创建一个新的`Chart`对象。您可以在[文档](http://www.chartjs.org/docs/#bar-chart)中了解更多可用选项。

![Contributors chart](img/3967cd393272065284379f37633c68bf.png)

## 结论

这里有一个真实世界的例子——如果你曾经在 Github 上参与过一个 [Symfony](https://github.com/symfony/symfony) 项目，你会注意到当你发送一个 pull 请求时，你的代码被一个名为 [fabbot.io](http://fabbot.io) 的外部服务检查。该服务正在监视每个新的 pull 请求的存储库，并对您的代码进行一些检查。您可以访问网站了解更多信息，也可以注册自己的存储库进行检查。

Github API 提供了很多东西，文档也让入门变得非常容易。我建议你去看看。另外，你可以在 [Github](https://github.com/sitepoint-editors/GithubAPI_Demo/tree/hooks) 上查看最终结果。和往常一样，如果你有任何问题，不要犹豫，把它们贴在下面。

## 分享这篇文章