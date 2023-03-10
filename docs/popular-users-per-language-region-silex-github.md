# 使用 Silex 和 Github 的每个语言和地区的受欢迎用户

> 原文：<https://www.sitepoint.com/popular-users-per-language-region-silex-github/>

Github API V3 是一组非常强大的 API 端点，您可以使用它来检索关于您的存储库、活动和事件的有用信息。不仅如此，它还允许您访问他人的公共信息。事实上，它有许多语言和平台的库，这也是一大优势；它可以让你很快开始写代码。

您可以通过 GitHub API V3 获得的数据大多是您可以在 GitHub web 界面上看到的数据，除了您可以在您的应用程序中使用这些数据来得出有趣的结论。这就是我们在这篇文章中要做的。

[Younes Rafie](https://www.sitepoint.com/author/yrafie/) 写了一篇名为[如何在 PHP 中使用 Github 的 API](https://www.sitepoint.com/use-githubs-api-php/)的文章，他在文章中介绍了很多我们在这里讨论的内容，尽管是以不同的方式。如果你没有读过那篇文章，不要害怕！我们掩护你。

![Github logo](img/a0d396a6fb51a46fba664d443ca4b376.png)

## 这个概念

正如 Brandon Savage 在他的文章[中提到的，为开源做贡献的权威指南](http://www.brandonsavage.net/the-definitive-guide-to-contributing-to-open-source/):

> 有数以百万计的 PHP 开发人员，但是只有少数开源软件的贡献者、作者和维护者。据 Packagist 报道，PHP 生态系统中有将近 50，000 个软件包。这是一个巨大数量的开源包，但与世界上的 PHP 开发者相比是个小数目。

现在，如果有一个 web 服务，我们可以指定一个位置和一种语言(例如 PHP ),并获得开源贡献者的排序列表，会怎么样？这肯定会让布兰登非常高兴。

在我们的 web 服务中，我们将有一个端点(即动作、URL)显示最流行的十个`creators`。最受欢迎的`creators`是在一个地方拥有最多观星者的人。为此，我们将检索每个人的资料库，并统计每个人被加星的次数。

GitHub Awards 项目做了一些类似的事情，尽管方式更加复杂。它使用了诸如 [GitHub 存档](https://www.githubarchive.org/)之类的工具，这超出了本文的范围。尤尼斯·拉菲在[的这篇文章](https://www.sitepoint.com/exploring-githubs-public-events-php-google-bigquery/)中也提到了这种方法。然而，我们在这里要做的只是简单地根据用户的追随者数量对他们进行分类，因为这也是受欢迎程度的一个很好的指标。要查看这款应用的完整源代码，请查看其 Github repo 。

## 加速

### 设置开发机器

最简单的开始方式是使用[拉勒维尔家园](http://laravel.com/docs/5.0/homestead)，或者我们自己的[家园改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。不管你使用哪一个，如果你打开你的`homestead.yaml`配置文件，你会看到默认的配置，那就是有一个名为`homestead.app`的站点从`~/Code`加载你的`php`文件。

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public
```

### 获取 Silex 并建立家园. yaml

作为一个微框架，Silex 没有太多的规则或惯例。然而，阅读[这篇文章](https://www.sitepoint.com/introduction-silex-symfony-micro-framework/)有助于吸收一些基础知识。如果你不想在文章之间切换，我们将在本教程中链接到 Silex 文档的相关部分。

正如您可能知道的，按照 Symfony 惯例，Silex 将其`index.php`文件放在`web`目录中。所以，你的`webroot`实际上会是`~/Code/silex/web`，而不是`~/Code/Laravel/public`。让我们进入`homestead.yaml`的`sites`部分并修复它:

```
sites:
    - map: homestead.app
      to: /home/vagrant/Code/silex/web
```

然后，点击`vagrant up`启动虚拟机，一旦完成，使用`vagrant ssh`SSH 进入虚拟机。在内部，我们执行以下操作来启动一个新的工作 Silex 实例:

```
cd Code
composer create-project silex/silex
```

一旦该过程完成(可能需要一段时间),在`silex`文件夹中创建一个名为`web`的子文件夹，并在其中创建一个文件`index.php`,内容如下:

```
<?php

require_once __DIR__.'/../vendor/autoload.php';

$app = new Silex\Application();

$app->get('/', function(){
    return "Hello world";
});

$app->run();
```

如果你现在在你的浏览器中打开`homestead.app:8000`，你应该会看到“Hello World”(假设`homestead.app`在你的`hosts`文件中，按照指令为[)。](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)

恭喜你！你现在可以开始了！

## GitHub API V3 入门

我们将使用 KNPLabs 的 [PHP GitHub API](https://github.com/KnpLabs/php-github-api) 库，通过其 [GitHub API V3](https://developer.github.com/v3/) 从 GitHub 中检索数据。让我们通过执行命令:`composer require knplabs/github-api ~1.4`将它添加到我们的`composer.json`文件中。

让我们通过将`$app['debug']`设置为`true`来打开调试模式，并向 GitHub API V3 发出请求，以获得一个名为`parhamdoustdar`的用户的详细信息。更新`index.php`。

```
<?php
require_once __DIR__.'/../vendor/autoload.php';

use Github\Client;
use Silex\Application;

$app = new Application();
$app['debug'] = true;

$app->get('/', function (Application $app) {
    $client = new Client();
    $parham = $client->user()->show('parhamdoustdar');

    return $app->json($parham);
});

$app->run();
```

如果你去`http://homestead.app:8000`，你应该会在 json 里看到`parhamdoustdar`的详细信息:

```
{
    "login":"parhamdoustdar",
    "id":352539,
    "avatar_url":"https:\/\/avatars.githubusercontent.com\/u\/352539?v=3",
    "gravatar_id":"","url":"https:\/\/api.github.com\/users\/parhamdoustdar",
    "html_url":"https:\/\/github.com\/parhamdoustdar",
    "followers_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/followers",
    "following_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/following{\/other_user}",
    "gists_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/gists{\/gist_id}",
    "starred_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/starred{\/owner}{\/repo}",
    "subscriptions_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/subscriptions",
    "organizations_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/orgs",
    "repos_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/repos",
    "events_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/events{\/privacy}",
    "received_events_url":"https:\/\/api.github.com\/users\/parhamdoustdar\/received_events",
    "type":"User",
    "site_admin":false,
    "name":"Parham Doustdar",
    "company":"TENA",
    "blog":"http:\/\/www.parhamdoustdar.com",
    "location":"Tehran",
    "email":"parham90@gmail.com",
    "hireable":true,
    "bio":null,
    "public_repos":9,
    "public_gists":2,
    "followers":3,
    "following":0,
    "created_at":"2010-08-03T07:56:17Z",
    "updated_at":"2015-03-01T20:01:26Z"}
```

如果你不明白我们的控制器到底在做什么，不要烦恼！我们会一起经历的。

在第 8 行，我们打开了前面提到的调试模式。

在访问根路由(`/`)时将被调用的匿名函数中，我们创建了一个`Github\Client`的实例。请注意，我们添加了一个类型为`Silex\Application`的参数。如 Silex 手册的[用法](http://silex.sensiolabs.org/doc/usage.html)部分所述:

> 由于类型提示，当前应用程序被 Silex 自动注入到闭包中。

在下一行中，我们调用`client`对象上的`user()`方法来获取类型为`Github\Api\User`的对象，该对象用于获取用户信息。然后我们简单地调用这个对象上的`show()`，传入我们想要获取信息的用户名。

最后一行使用`Silex\Application`对象上的`json()` helper 方法来呈现我们的示例 json 响应。

## 应用程序目录

您可能已经注意到，我们的项目没有放置源代码的位置。当你正在编写一小段代码来检查一切是否顺利时，拥有一个`index.php`文件是很好的，但是现在是时候让我们创建一个文件夹来存放我们应用程序的源代码了。

为此，在您的`silex`目录中创建一个文件夹，紧挨着您的`web`文件夹，并将其命名为`App`。解决了这个问题，让我们编辑`composer.json`并添加自动加载信息:

```
{
    ...
    "autoload": {
        "psr-0": { "Silex": "src/" },
        "psr-4": {
            "App\\": "App/"
        }
    },

}
```

接下来，运行`composer dump-autoload`来重新生成你的自动加载文件。我们现在准备开始真正的工作了！

## 某个位置的 GitHub 用户

对于 toplist，我们需要做的第一件事是获取特定位置的用户名列表，这些用户名拥有指定语言的存储库。为此，我们可以使用[搜索用户 API](https://developer.github.com/v3/search/#search-users) 。

如果您查看该页面，您会发现我们可以在搜索用户时指定多个参数。然而，我们感兴趣的是`location`和`language`，它们可以在`q`参数中指定。如果您以前使用过 GitHub 搜索框，您会注意到`q`参数就是您要输入到该文本字段中的内容。

让我们继续创建一个服务，搜索居住在我们想要的位置的用户，并拥有我们需要的编程语言的存储库。我们称之为`UserSearcher`，放入`App/Services`。

```
<?php
namespace App\Services;

use Github\Client;
class UserSearcher
{

    /**
     * @var Client
     */
    protected $client;

    public function __construct(Client $client)
    {
        $this->client = $client;
    }

    public function retrieveByLocationAndLanguage($location, $language)
    {
        $searchString = "location:${location} language:${language}";
        $results = $this->fetchSearchResults($searchString, 10);

        return $this->extractUsernames($results);
    }

    protected function fetchSearchResults($searchString, $count)
    {
        $results = $this->client
            ->search()
            ->setPerPage($count)
            ->users($searchString, 'followers')
        ;

        return $results['items'];
    }

    protected function extractUsernames(array $results)
    {
        $usernames = [];

        foreach ($results as $result) {
            $usernames[] = $result['login'];
        }

        return $usernames;
    }

}
```

如您所见，`UserSearcher::retrieveByLocationAndLanguage()`方法首先构建一个搜索字符串。如果将`Tehran`和`PHP`分别作为第一个和第二个参数发送给这个函数，那么字符串将是`location:Tehran language:PHP`。

然后，它使用`Github\Client`检索前十个搜索结果的列表。您可能已经猜到了，它首先从客户端获取搜索 API 对象(`Github\Api\Search`)，然后将`perPage`设置为 10，这意味着它将只请求十个搜索结果。然后，它将搜索字符串发送到 GitHub 并返回结果。注意，搜索的结果是按照追随者的数量排序的，并且在返回数组的`items`索引中。

方法`extractUserNames()`简单地做了您所期望的事情:它遍历结果并将每个用户的`login`添加到一个数组中，然后返回它。

在这一点上我们有什么？十个用户名。十个用户名，我们可以查询他们的存储库，然后加总该用户名下所有存储库的观星者数量。

准备好了吗？

## 仓库的天文学家

下面是我们类的代码，`App\Services\StarGazerCalculator`:

```
<?php
namespace App\Services;

use Github\Client;
class StarGazerCalculator
{

    /**
     * @var Client
     */
    protected $client;

    public function __construct(Client $client)
    {
        $this->client = $client;
    }

    public function calculateStarGazersByUsername($username)
    {
        $repositories = $this->client->user()->repositories($username);
        $repositories = $this->filterForkedRepositories($repositories);

        return $this->addStarGazersFromRepositories($repositories);
    }

    protected function filterForkedRepositories(array $repositories)
    {
        return array_filter($repositories, function($repository) {
            return $repository['fork'] === false;
        });
    }

    protected function addStarGazersFromRepositories(array $repositories)
    {
        return array_reduce($repositories, function($starGazersSoFar, $repository) {
            return $starGazersSoFar + $repository['stargazers_count'];
        });
    }

}
```

## 检索顶级创作者

现在该开始写我们的最后一课了，`TopCreatorsRetriever`。这个类包装了我们想要的功能——它使用`UserSearcher`类获得最流行的十个用户名，然后遍历这些用户，使用`StarGazerCalculator`获得每个用户的观星者数量。

```
<?php
namespace App\Services;

class TopCreatorsRetriever
{

    /**
     * @var UserSearcher
     */
    protected $searcher;

    /**
     * @var StarGazerCalculator
     */
    protected $calculator;

    public function __construct(UserSearcher $searcher, StarGazerCalculator $calculator)
    {
        $this->searcher = $searcher;
        $this->calculator = $calculator;
    }

    public function retrieve($location, $language)
    {
        $usernames = $this->searcher->retrieveByLocationAndLanguage($location, $language);
        $map = $this->createUserStarGazerMap($usernames);

        return $this->sortMapByStarGazers($map);
    }

    protected function createuserStarGazerMap(array $usernames)
    {
        $results = [];

        foreach ($usernames as $username) {
            $results[$username] = $this->calculator->calculateStarGazersByUsername($username);
        }

        return $results;
    }

    protected function sortMapByStarGazers(array $map)
    {
        arsort($map, SORT_NUMERIC);

        return $map;
    }

}
```

## 服务提供商

您可能知道，为了将我们的类添加到 Silex 提供的依赖注入容器中，我们需要[创建一个服务提供者](http://silex.sensiolabs.org/doc/providers.html#creating-a-provider)，然后[在应用程序](http://silex.sensiolabs.org/doc/providers.html#loading-providers)上注册它。这意味着我们需要创建一个服务提供者来使用我们新编写的类`TopCreatorsRetriever`。但是我们不需要注册另外两个类，因为这两个类供我们的高级类`TopCreatorsRetriever`内部使用。

在`App/`下新建一个文件夹，命名为`Providers`。然后，创建一个名为`TopCreatorsRetrieverServiceProvider.php`的文件，它将存放我们类的代码。

```
<?php
namespace App\Providers;

use Silex\ServiceProviderInterface;
use Silex\Application;
use Github\Client;
use App\Services\UserSearcher;
use App\Services\StarGazerCalculator;
use App\Services\TopCreatorsRetriever;
class TopCreatorsRetrieverServiceProvider implements ServiceProviderInterface
{

    public function register(Application $app)
    {
        $app['topCreatorsRetriever'] = $app->share(function() {
            $client = new Client();
            $searcher = new UserSearcher($client);
            $calculator = new StarGazerCalculator($client);

            return new TopCreatorsRetriever($searcher, $calculator);
        });
    }

    public function boot(Application $app)
    {

    }
}
```

> **注意**:不要去掉空的`boot()`功能。它必须存在，即使是空的，这样我们的类才能符合`ServiceProviderInterface`接口。

## 在我们的控制器中使用 TopCreatorsRetriever

终于到了看我们所有工作的时候了。我们将在`index.php`中定义一个名为`creators`的路由，它获取`location`和`language`作为参数，然后返回一个 JSON 数组，用户名作为键，观星者的数量作为值。不要忘记，如前所述，我们还需要注册我们之前创建的服务提供者。

```
<?php
require_once __DIR__.'/../vendor/autoload.php';

use Silex\Application;
use App\Providers\TopCreatorsRetrieverServiceProvider;

$app = new Application();
$app['debug'] = true;
$app->register(new TopCreatorsRetrieverServiceProvider());

$app->get('/creators/{location}/{language}', function (Application $app, $location, $language) {
    $data = $app['topCreatorsRetriever']->retrieve($location, $language);
    return $app->json($data);
});

$app->run();
```

很简单。唯一改变的是我们的服务提供商的注册和我们的路线，它只包含两条线路:

1.  第一行从`TopCreatorsRetriever`类获取数据，并且
2.  第二行将数据转换成 json。

现在，如果你向`http://homestead.app:8000/creators/Tehran/PHP`发送一个请求，你会看到居住在德黑兰的观星者数量最多的所有软件包创建者:

```
{
    "sallar":198,
    "sepehr":86,
    "reshadman":49,
    "moein7tl":49,
    "alijmlzd":13,
    "atkrad":4,
    "AmirHossein":3,
    "Yousha":0,
    "voltan":0,
    "zoli":0
}
```

恭喜你！你现在知道在任何领域谁是最受欢迎的软件包创建者了！

## 结论

我们现在有一个应用程序，它通过一个非常基本的机制从 GitHub 收集有意义的数据:我们检索一个区域中拥有我们想要的语言的存储库的顶级用户，并为他们的所有存储库添加观星者。

希望这篇教程已经教会了你一些新的有用的东西。请在下面的评论中留下您的反馈，不要忘记您可以在这里查看完整的源代码！

## 分享这篇文章