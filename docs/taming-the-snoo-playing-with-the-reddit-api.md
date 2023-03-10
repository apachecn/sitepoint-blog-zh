# 驯服史努比:使用 Reddit API

> 原文：<https://www.sitepoint.com/taming-the-snoo-playing-with-the-reddit-api/>

Reddit 是一个社交网络、娱乐和新闻网站，内容几乎全部由用户提交。根据[的报道](http://expandedramblings.com/index.php/reddit-stats/)，2016 年 2 月，Reddit 拥有 3600 万用户账户，2.31 亿独立月访问量，11464 个活跃社区。最近的一项研究还显示，80%的 Reddit 用户从那里获得他们的[新闻。](http://www.journalism.org/2016/02/25/nearly-eight-in-ten-reddit-users-get-news-on-the-site/)

Reddit 也提供了自己的 API。这样，我们可以使用 Reddit 上所有可用的信息来丰富我们自己的网站或构建我们自己的 Reddit 客户端。在本文中，我们将通过 PHP 解决一些基本的 Reddit API 用法。

![Realistic Reddit Alien Painting](img/c49e79cd00e717ddd64ae8a221539a35.png)

## Reddit API

从只能通过认证访问的私有方法(Reddit 使用 OAuth2)到我们可以通过基本 HTTP 调用使用的公共方法，Reddit API 非常广泛，并且有很好的文档记录。

在本文中，我们将首先关注`search`方法。虽然这是一个公共调用(它不需要认证)，但它也是最强大的调用之一，因为它允许我们访问每个子编辑中 Reddit 帖子的所有历史。

### 搜索方法

`search`方法可以通过一个基本的 HTTP 请求获得，它有很多属性。[查看文档](https://www.reddit.com/dev/api/oauth#GET_search)，我们可以看到它支持`HTTP GET`方法，并且可以通过

```
https://www.reddit.com/[/r/subreddit]/search 
```

我们还有以下可用的参数:`after`、`before`、`count`、`include_facets`、`limit`、`q`、`restrict_sr`、`show`、`sort`、`sr_detail`、`syntax`、`t`和`type`。下表可以在文档中找到，它更详细地显示了每个参数。

| 争吵 | 收到 |
| --- | --- |
| 在...之后 | 事物的全称 |
| 以前 | 事物的全称 |
| 数数 | 正整数(默认值:0) |
| 包含 _ 方面 | 布尔值 |
| 限制 | 所需项目的最大数量(默认值:25，最大值:100) |
| q | 长度不超过 512 个字符的字符串 |
| 限制 _ 服务请求 | 布尔值 |
| 显示 | (可选)字符串 all |
| 分类 | (相关、热门、热门、新、评论)之一 |
| 服务请求 _ 详细信息 | (可选)展开子编辑 |
| 句法 | (cloudsearch、lucene、plain)之一 |
| t | (小时、天、周、月、年、全部)之一 |
| 类型 | (可选)逗号分隔的结果类型列表(sr，link) |

我们将集中讨论`q`、`limit`、`sort`和`restrict_sr`论点。

`q`参数是最重要的一个，它表示我们将在所讨论的子编辑中搜索的查询。用法示例如下:

```
https://www.reddit.com/r/php/search.json?q=oop 
```

这个特定的调用将在`php`子编辑中搜索`oop`表达式。如果您尝试使用浏览器进行呼叫，您将看到结果(只需在浏览器中复制并粘贴链接)。

`limit`参数限制了返回列表中的文章数量。用法示例如下:

```
https://www.reddit.com/r/php/search.json?q=oop&limit=5 
```

这个特定的搜索将返回在`php`子编辑中搜索`oop`表达式的前 5 个结果。

`sort`参数将使用五个 Reddit 订单属性之一对搜索到的帖子进行排序:`hot`、`new`、`comments`、`relevance`和`top`。因此，如果我们想在`php`子编辑中搜索更新的`oop`文章，我们可以进行如下调用:

```
https://www.reddit.com/r/php/search.json?q=oop&sort=new 
```

`restrict_sr`是一个布尔值，它表明我们是否希望将搜索限制在当前的子编辑中。如果我们传递 0，我们将搜索整个 Reddit。

```
https://www.reddit.com/r/php/search.json?q=oop&sort=new&restrict_sr=1 
```

可以组合所有属性来进行更精确的搜索。

### 添加 PHP

能够通过我们的浏览器调用 API 很有趣，但是我们想要更强大的东西。我们希望能够以多种不同的方式解析和使用我们获得的信息。

对于这个在 PHP 中使用 Reddit API 的例子，我们可以使用 [cURL](https://www.sitepoint.com/using-curl-for-remote-requests/) ，但是虽然拥有`cURL`技能是有用的，但是现在它是一个相当过时的工具。我们将使用一个名为 [Guzzle](https://www.sitepoint.com/guzzle-php-http-client/) 的库，并安装 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 。

```
composer require guzzlehttp/guzzle 
```

对于项目的这一部分，我们不仅会使用 Guzzle，还会使用我们之前讨论过的其他论点。

```
<?php

namespace ApiReddit;

require_once './vendor/autoload.php';

class Searcher
{

    /**
     * This method queries the reddit API for searches
     *
     * @param $subreddit The subreddit to search
     * @param $query The term to search for
     * @param $options The filter used to search
     * @param $results The number of results to return
     *
     **/
    public function execSearch($subreddit = 'php', $query, $options, $results = 10)
    {                   
        //Executes an http request using guzzle
        $client = new \GuzzleHttp\Client([
            'headers' => ['User-Agent' => 'testing/1.0'],
            'verify' => false]);

        $response = $client->request("GET", 'https://reddit.com/r/' . $subreddit . '/search.json', ['query' => 'q=' . $query . '&sort=' . $options . '&restrict_sr=1&limit=' . $results ]);

        $body = $response->getBody(true);

        return $body;
    }
} 
```

在这次搜索中，我们添加了更多的参数来进一步完善我们的搜索。现在我们有了*子编辑*、*选项*和*结果*(默认设置为 10)。

接下来，我们将创建一个查询 Reddit API 的`index.php`文件。我们将使用[树枝](http://twig.sensiolabs.org/)来渲染我们的视图，并在表格中显示结果。然后，我们将在项目的根目录下创建一个`/templates`文件夹。这个文件夹将保存我们的树枝模板。

```
composer require twig/twig 
```

`index.php`文件:

```
<?php
require __DIR__.'/vendor/autoload.php';

use ApiReddit\Searcher;

//Executes a new search
$search = new Searcher();
$json=$search->execSearch( 'php', 'composer', 'hot', 5);
$data =  json_decode($json);

//Loads the results in Twig
$loader = new Twig_Loader_Filesystem(__DIR__.'/templates');
$twig = new Twig_Environment($loader, array());

//Renders our view
echo $twig->render('index.twig', array(
    'results' => $data->data->children,
)); 
```

在加载 Twig 之后，我们告诉它我们在哪里存储模板并渲染`index.twig`。

我们还想创建一个`resources/style.css`文件来设计我们的结果。该文件包含以下内容:

```
#posts td, #posts th {
    border: 1px solid #ddd;
    text-align: left;
    padding: 8px;
}

#posts th {
    padding-top: 11px;
    padding-bottom: 11px;
    background-color: #4CAF50;
    color: white;
} 
```

最后，我们将创建我们的模板文件，记住我们的结果和 CSS。在`/templates`文件夹中，我们创建一个`index.twig`文件:

```
<!DOCTYPE html>

<html>
<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8">
    <title>Reddit Search Table</title>

    <link rel="stylesheet" type="text/css" href="resources/style.css">
</head>
<body>

    <table id='posts'>
        <tr>
            <th>Title</th>
            <th>Ups</th>
            <th>Downs</th>
            <th>Created At</th>
            <th>Subreddit</th>
            <th>Number of Comments</th>
            <th>Link</th>
        </tr>

        {% for item in results %}

            <tr>
                <td>{{ item.data.title }}</td>
                <td>{{ item.data.ups }}</td>
                <td>{{ item.data.downs }}</td>
                <td>{{ item.data.created_utc }}</td>
                <td>{{ item.data.subreddits }}</td>
                <td>{{ item.data.num_comments }}</td>
                <td>{{ item.data.permalink }}</td>
            </tr>

        {% endfor %}

    </table>

</body>
</html> 
```

我们的最终结果在这里:

![Results table](img/b3d5972a8800aa06dca06cddfa0fb1db.png)

### 添加身份验证

虽然`search`方法可能非常强大，但 Reddit API 还有许多我们可以探索的特性，但大多数都需要认证。为了能够访问所有这些功能，我们首先需要一个 Reddit 帐户，所以在继续之前，请确保您已经有了一个帐户。

在我们拥有一个帐户之后，在我们能够访问 API 之前，有一些配置工作要做。登录你的账户，在右上角你会看到“偏好设置”按钮。点击它，转到“应用程序”标签，然后点击“创建”。

填写信息(一定要记住重定向 URI 必须是我们将在应用程序中使用的那个)。我们可以将`about url`留空。

之后点击`Create App`。这将为我们提供访问客户端 ID 和秘密令牌的权限。我们将使用这些信息通过 OAuth2 向 API 进行身份验证。对于 Oauth2 认证，我们将使用一个众所周知的包`adoy/oauth2`。这个包是 OAuth 2.0 协议的轻量级 PHP 包装器(基于 OAuth 2.0 授权协议草案-ietf-oauth-v2-15)。

```
composer require adoy/oauth2 
```

现在我们有了使用 Oauth2 的工具，让我们在项目的根目录下创建一个名为`Oauth.php`的文件，其内容如下:

```
<?php

require_once './vendor/autoload.php';

use ApiReddit\Authenticator;

$authorizeUrl = 'https://ssl.reddit.com/api/v1/authorize';
$accessTokenUrl = 'https://ssl.reddit.com/api/v1/access_token';
$clientId = 'Your Client Id';
$clientSecret = 'Your Secret';
$userAgent = 'RedditApiClient/0.1 by YourName';

$redirectUrl =  "Your redirect url";

$auth = new Authenticator( $authorizeUrl, $accessTokenUrl, $clientId, $clientSecret, $userAgent, $redirectUrl );
$auth->authenticate(); 
```

我们正在创建一个`Authenticator`的实例并调用`authenticate()`方法。我们也[通过添加到 Composer 来自动加载](https://www.sitepoint.com/battle-autoloaders-psr-0-vs-psr-4/)类。为此，让我们在`/src`文件夹中创建`Authenticator.php`类文件。

```
<?php

namespace ApiReddit;

class Authenticator
{

    public function __construct($authorizeUrl, $accessTokenUrl, $clientId, $clientSecret, $userAgent, $redirectUrl)
    {

        $this->authorizeUrl   = $authorizeUrl;
        $this->accessTokenUrl = $accessTokenUrl;
        $this->clientId       = $clientId;
        $this->clientSecret   = $clientSecret;
        $this->userAgent      = $userAgent;
        $this->redirectUrl    = $redirectUrl;
    }

    public function authenticate()
    {

        $client = new \OAuth2\Client($this->clientId, $this->clientSecret, \OAuth2\Client::AUTH_TYPE_AUTHORIZATION_BASIC);
        $client->setCurlOption(CURLOPT_USERAGENT, $this->userAgent);

        if (!isset($_GET["code"])) {
            $authUrl = $client->getAuthenticationUrl($this->authorizeUrl, $this->redirectUrl, array(
                "scope" => "identity",
                "state" => "SomeUnguessableValue"
            ));
            header("Location: " . $authUrl);
            die("Redirect");
        } else {
            //$this->getUserPreferences($client, $this->accessTokenUrl);
        }
    }
} 
```

在`Oauth.php`文件中，我们用通过 API 认证所需的所有数据初始化我们的项目变量。`Authenticator.php`是神奇发生的地方。

我们正在使用`adoy`用我们的 ID 和密码创建一个新的 OAuth2 客户端实例。接下来是基本的 OAuth 逻辑:我们使用一个身份验证 URL 来执行身份验证，并在身份验证后重定向到我们将被重定向到的位置。

需要注意的一件重要事情是在我们的呼叫中使用了`scope`。这个范围基本上就是我们想要访问的功能的范围。在这种情况下，我们使用`identity`,因为在这个例子中，我们想要获取我们自己的用户信息。API 文档中解释了所有可能的操作和各自的范围。

最后但同样重要的是，我们有一行注释。这是故意的。`getUserPreferences`方法将调用我们想要使用的 API 方法。因此，让我们取消对该行的注释，并实现下面的方法。

```
/**
* This function will request the Reddit API for the user information
*
* @param $client The client ID
* @param $accessToken The access token given
*/

public function getUserPreferences( $client, $accessToken )
{

    $params = array("code" => $_GET["code"], "redirect_uri" => $this->redirectUrl);
    $response = $client->getAccessToken($accessToken, "authorization_code", $params);

    $accessTokenResult = $response["result"];
    $client->setAccessToken($accessTokenResult["access_token"]);
    $client->setAccessTokenType(OAuth2\Client::ACCESS_TOKEN_BEARER);

    $response = $client->fetch("https://oauth.reddit.com/api/v1/me.json");

    echo('<strong>Response for fetch me.json:</strong><pre>');
    print_r($response);
    echo('</pre>');
} 
```

我们正在获取访问令牌，并在调用中使用它从`https://oauth.reddit.com/api/v1/me.json`获取信息。这个 GET 方法将返回当前通过身份验证的用户的身份。答案将是一个 json 数组。

如果我们运行它，我们将被带到一个登录页面，就像 OAuth 应该发生的那样。登录后，我们将看到以下页面提示:

![Allow access](img/b936d296bacbdbc016597a2a69be014c.png)

只需单击 allow，如果一切顺利，我们应该有一个 json 数组，其中包含我们刚刚认证的用户的所有信息。

这就是我们如何使用 Reddit API 进行身份验证。我们现在可以更深入地查看文档，并检查我们可以用它做的所有伟大的事情。

所有代码都可以在这个 github 库中找到[。](https://github.com/sitepoint-editors/RedditSearchGuzzle)

## 结论

我们学习了如何使用 Reddit API 的搜索功能，并初步了解了认证和使用需要登录用户的方法。但是，我们只是触及了表面。

可能性是巨大的，因为 Reddit API 让我们可以访问几乎无穷无尽的知识库。请务必查看整个 Reddit API 文档，因为它提供了更多内容，如果您构建了任何有趣的东西，请务必让我们知道！

## 分享这篇文章