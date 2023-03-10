# 引入 Bugsnag–您需要的最后一个错误监视器

> 原文：<https://www.sitepoint.com/laravel-with-bugsnag-the-last-error-monitor-youll-need/>

构建无错误应用程序的追求仍在继续。但与此同时，我们需要监控应用程序的错误并迅速采取行动。许多公司都试图解决这个问题，目前市场上最好的解决方案之一是 [Bugsnag](http://bugsnag.com/) 。

他们关注于提供一个错误通知系统的问题，这个系统能够理解错误并帮助你以各种方式采取行动。在本文中，我们将发现 Bugsnag 并将其集成到现有的 Laravel 应用程序中。你可以从 Github 中克隆[演示应用程序来跟进。](https://github.com/Whyounes/bugsnag_demo)

![Bugsnag Logo](img/ac3047dc258ac969ab1f805413e3b187.png)

## 项目设置

您可以按照下面的说明来设置演示应用程序(假设您有一个类似于[这个](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)的 PHP 工作环境)。

```
// clone the repo
git clone git@github.com:Whyounes/bugsnag_demo.git

cd bugsnag_demo

// update dependencies
composer update

// migrate and seed the database
php artisan migrate && php artisan db:seed 
```

## 在 BugSnag 上创建帐户

在开始使用 Bugsnag 之前，我们需要创建一个新帐户并创建我们的新项目。你可以从 30 天的免费试用开始，然后根据你的应用程序的大小切换到付费计划。注册后，你必须创建一个新项目，并指定你的项目类型。在我们的例子中，那将是 PHP。

### 给我们的项目增加了障碍

Bugsnag 为不同的应用程序类型提供了一个通知程序列表。我们将安装并配置 [PHP 通知包](https://bugsnag.com/docs/notifiers/php)。或者，因为演示应用程序是使用 Laravel 4 构建的，所以您可能希望使用 [Laravel 包](http://github.com/bugsnag/bugsnag-laravel)并遵循 [Laravel 集成指南](https://bugsnag.com/docs/notifiers/laravel#laravel-5-0)来轻松设置您的项目，但是在这种情况下，我们将采用一种更加独立于框架的方法。

*   要求`bugsnag/bugsnag:2.*`使用 Composer。
*   在 Bugsnag 的控制面板中从`project dashboard > settings`获取您的项目 API 密钥。根据您的应用程序，您可以将 API 密钥存储在环境变量或配置文件中。在这种情况下，我们将把它添加到我们的`app/config/app.php`中。

```
// app/config/app.php

return array(
    // ...
    'bugsnag_api_key'   => 'YOUR KEY'
    // ...
); 
```

在`app/start/global.php`中，我们绑定并配置我们的 Bugsnag 客户端。

```
// app/start/global.php

App::singleton('bugsnag', function() {
    $bugsnag = new Bugsnag_Client( Config::get('app.bugsnag_api_key') );

    return $bugsnag;
});

// ... 
```

根据您的应用程序，容器依赖注入设置会有所不同。对于 Laravel 应用程序来说，上述情况很常见。

此外，您可以选择使用以下内容设置发布阶段:

```
$bugsnag->setReleaseStage(App::environment()); 
```

这只会在应用程序的特定发布阶段产生 Bugsnag 日志错误。

在创建 Bugsnag 客户端之后，您应该将它附加到您的错误处理程序。这将取决于您如何管理应用程序内部的错误和异常。Laravel 提供了接受回调处理程序的`fatal`和`error`方法，但是你也可以使用 PHP 的 [set_exception_handler](http://php.net/manual/en/function.set-exception-handler.php) 和 [set_error_handler](http://php.net/manual/en/function.set-error-handler.php) 。

```
// app/start/global.php

App::error(function($exception) {
    App::make('bugsnag')->notifyException($exception, null, "error");
});

App::fatal(function($exception) {
    App::make('bugsnag')->notifyException($exception, null, "error");
}); 
```

`notifyException`方法接受额外的元数据作为第二个参数，错误级别作为第三个参数，默认为`warning`。但是，如果您想将它直接附加到 PHP 错误和异常处理程序。

```
$bugsnag = App::make('bugsnag');

set_error_handler(array($bugsnag, 'errorHandler'));
set_exception_handler(array($bugsnag, 'exceptionHandler')); 
```

### 配置缺陷

*   将用户详细信息添加到错误:
    默认情况下，Bugsnag 发送带有错误的登录用户的 ID，但是您可以通过更新容器上的 Bugsnag 客户端实例来更改这一点。

    ```
    // app/start/global.php

    App::make('bugsnag')->setUser([
        'email' => Auth::check() ? Auth::user()->email : 'Guest user'
    ]); 
    ```

    现在，通过选择`user`选项卡，我们可以在错误详情中看到用户的用户名。

    ![User details](img/28879160cb7f09f14350187045b3cd76.png)

*   您还可以通过使用接受一个可调用函数作为参数的`setBeforeNotifyFunction`方法来改变发送到服务器的错误对象。

    ```
    // app/start/global.php

    App::make('bugsnag')->setBeforeNotifyFunction(function($error) {
        $error->setMetaData([
            "user" => [
                "username" => Auth::check() ? Auth::user()->username : 'Guest user'
            ],
            "metrics" => [
                "Metric 1" => "Some data here"
            ]
        ]);
    }); 
    ```

    如果你在上面的截图中注意到了，我们有一个标签列表来分组关于错误的有意义的细节。我们可以更新现有的标签，如`USER`标签，并为指标等添加新的标签。

    ![Before notif](img/28879160cb7f09f14350187045b3cd76.png)

*   为了避免关于您的客户或应用程序的敏感细节泄露出去，您可以添加一个您想要过滤到 Bugsnag 客户机实例的属性列表。该值将在您的仪表板上显示为`[FILTERED]`。

    ```
    // app/start/global.php

    App::singleton('bugsnag', function() {
        $bugsnag = new Bugsnag_Client( Config::get('app.bugsnag_api_key') );
        $bugsnag->setFilters([
            'password'
        ]);

        return $bugsnag;
    });

    // ... 
    ```

    ![Filter keys](img/9476883d5a728004fd40e85392f574f1.png)

## Bugsnag 仪表板

在创建了 Bugsnag 帐户并将其附加到应用程序之后，我们需要访问仪表板来探索不同的可用组件。我们可以在`Inbox`视图中进行选择，该视图将错误列表显示为我们可以过滤的列表，或者通过单击页面顶部的`timeline`链接切换到时间线图表页面。您可以查看[文档](https://bugsnag.com/docs/dashboard/investigating_errors)了解更多关于时间线图表仪表板的详细信息。

![Timeline Graph](img/9adda23cf8d34bbf2a8333991ac3c371.png)

### 让我们生成一些错误

在我们的问题视图模板中，我们显示了问题所有者的用户名。被删除的用户会导致问题，并抛出一个错误。(`Trying to get property of non-object`)。如果我们有很多用户访问问题页面(您可以通过多次请求同一个页面来模拟)，这可能会使我们的错误指示板出现相同的错误。如果您使用 artisan `db:seed`命令植入数据库，您将有一个问题分配给一个 id 为 10 的用户，该用户在数据库中不存在。

![Grouping errors](img/095a2132e2d9e72ee13dbda003b53bde.png)

![Error details](img/095a2132e2d9e72ee13dbda003b53bde.png)

我们不必担心仪表板上显示重复的错误，也不必对它们应用任何分组。解析您的错误后，Bugsnag 将决定我们是在处理重复错误还是新错误。这是我们应用程序中的一个例子。

我们可以通过在访问对象的 username 属性之前添加一个简单的测试来修复这个错误。

```
// app/views/user/questions.blade.php

// ...
{{ isset($question->user) ? $question->user->username : 'User deleted' }}
//... 
```

### 过滤

为了调查我们的错误，Bugsnag 为我们提供了一组过滤器来深入研究数据并收集关于问题来源的特定细节。

您可以使用顶部过滤栏上的日期范围选择器按时间过滤错误，或者通过选择`all`过滤器查看所有错误。

一个更好的过滤错误的方法是通过数据——比如用户、错误状态、严重性、分配给 X 等等。您可以查看[文档](https://bugsnag.com/docs/dashboard/filter_keys_and_date_formats)中支持的过滤器列表。因为 Bugsnag 会在您发送错误消息时对其进行解析，所以它将帮助您自动完成主机名、发布阶段等搜索条件。

![Filters](img/921842e31f9569df967e43a2341715bf.png)

### 添加协作者

很有可能你不是一个人在做你的项目，或者你是项目经理。您可以点击右上角的`collaborators`链接来添加项目合作者。

![Collaborators](img/8af91f10ae8b58348415d9aec3051a2d.png)

Bugsnag 解析错误，并相应地对它们进行分组，以避免冗长的重复列表。选择错误后，我们可以将其分配给现有的协作者或邀请新的协作者。

![Assign to collaborator](img/6bf562bed346f188ff98efaa0c6b2563.png)

### 错误状态

仪表板的左侧栏显示了按状态分组的错误列表，(*打开、进行中、已修复、暂停、忽略*)。选择错误后，我们可以在左上角的栏中更改其状态。

![Error status](img/5b990e8837d559c838f4511a91d9f49b.png)

默认情况下，Bugsnag 只会显示`status:open`错误来帮助我们识别活动错误，但是我们可以使用前面提到的过滤器来改变这种情况。

### 打盹和忽略错误

如果您有一个经常发生的错误，并且您的团队目前正在修复它，您可以暂停它一段特定的时间或使用一个条件。

![Snoozing errors](img/9f7680a38ff42ddab9cf1aa5dc66226f.png)

如果您想忽略此错误(不建议在生产中使用),可以使用“操作”菜单上“暂停”选项旁边的“忽略”按钮。

## 第三方通知

团队经常在他们的开发过程中使用多种应用和服务。Bugsnag 提供了一个[通知插件](https://bugsnag.com/docs/notification-plugins)列表，我们可以配置这些插件来发送不同类型错误的通知。你可以在 Slack 上通知你的团队，在 Github 上创建问题，发布到你服务器的 webhook 等等。

### 时差通知

导航到我们的项目设置，点击`Team Notifications`链接显示集成列表，然后选择 Slack。

我们需要指定它应该通知我们的时间和频率。然后，我们需要通过点击输入上方的链接来获取我们的 Slack webhook URL，粘贴 URL，并发送一个测试通知。

![Slack notification](img/069a6592c0497c557e085aa54298af88.png)

### 制造 Github 问题

另一种处理错误通知的方法是创建一个新的 Github 问题。导航到我们的项目设置，点击`Issue Tracker`链接显示集成列表，然后选择 Github issues。

![Github Issue](img/99b0db6d28f740637b32a7f64994a9ce.png)

![Github Issue Test](img/d7b53ac3174ddbbfa01d61b7ec202f8d.png)

## 结论

Bugsnag 为自动化错误跟踪系统提供了一个很好的平台，节省了浏览日志、创建监控脚本等的时间。您可以专注于构建您的应用程序，同时密切关注您的产品发布中发生的事情。

Bugsnag 已经吸引了一些大公司，如 Github、思科、LinkedIn 等。你可以从创建 30 天试用版开始，探索可用的功能，如果你真的喜欢它，就切换到付费计划。如果您正在使用另一个错误监控平台，或者您已经尝试过 Bugsnag，请让我们知道您的想法！

## 分享这篇文章