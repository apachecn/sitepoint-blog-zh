# 建立 Twitter 标签竞赛——注册应用程序

> 原文：<https://www.sitepoint.com/building-twitter-hashtag-contest-register-twitter-app/>

## 介绍

社交网站在增加网站或应用程序的受欢迎程度方面起着至关重要的作用。绝大多数网站都提供了通过点击按钮在流行的社交媒体网站上分享内容的能力。在这些网站上组织一场竞赛是推广你的申请的另一个好方法。

我们将在 Twitter 上建立一个竞赛，以增强用户与 web 应用程序的交互。这是一个由两部分组成的系列，第一部分重点介绍 Twitter 竞赛和配置 Twitter 应用程序，第二部分重点介绍竞赛的实现。让我们开始吧。

### Twitter 竞赛简介

Twitter 是一种社交网络和微博服务，利用即时消息、SMS 或 web 界面。Twitter 被认为是 web 的 SMS，因此 Twitter 上的竞赛通常与其他流行的社交网站上的竞赛不同。

Twitter 竞赛的主要目标是让人们就某个话题创建尽可能多的推文，以提高知名度。让我们来看看一些最流行的使用 Twitter 的竞赛类型。

*   抽奖活动——这是最简单的推特竞赛。用户可以通过创建一条带有给定的**#标签**和他们的**用户名**的推文来参加比赛。获胜者将通过幸运抽奖产生。

*   **创意答案**——在这个比赛中，组织者通过创建一条推文或在其网站上发布问题来提问。用户必须创建带有给定**#标签**的推文来回答这个问题。最有创意的答案将由组织者手动选择。

*   **转发赢**–组织者发布一条推文，用户必须转发。获胜者将从所有转发者中随机选出。

*   **关注并赢得**–组织者提供公司简介或他们想要推广的简介。用户必须是给定配置文件的追随者。通常在比赛结束时随机选出获胜者。

我们不会完全依赖运气，我们将创建一个标签竞赛，根据用户的努力和表现来产生获胜者。

### 策划一场推特标签大赛

竞赛的步骤如下:

*   **用户需要验证他们的 Twitter 账户**–通常，这是通过提供一个 Twitter 登录按钮来完成的，用户可以通过他们的 Twitter 账户授权访问应用程序。

*   **通过我们的应用程序**创建带有标签的推文——我们将为每条推文使用两个标签。第一个标签用于推广品牌。如果我们的品牌是 SitePoint，我们将有一个名为`#SitePoint`的标签来推广我们的品牌。第二个标签用于在其他带有第一个标签的 tweets 中识别应用程序。

*   **选择获胜者**——在这种比赛中，有许多方法可以自动选出获胜者。在这种情况下，我们将根据转发次数来评估获胜者。获胜者将根据用户创建的推文的转发总数选出。

## 建筑物

首先，你需要在 Twitter 上注册一个应用程序。访问 [Twitter 开发者网站](https://dev.twitter.com/)，创建一个具有写权限的应用程序。一旦您有了一个包含`consumer_key`和`consumer_secret`的 Twitter 应用程序，请按照逐步指南中的说明完成实现。

### 步骤 1–配置 OAuth 库

OAuth 是 Twitter 使用的标准技术，用于认证对其 API 的访问。有许多现有的库可以自动化 Twitter 的认证过程。我们将使用一个名为 [tmhOAuth](https://github.com/themattharris/tmhOAuth) 的库。从 Github 下载并解压 zip 文件，或者克隆它。

创建一个包含`index.php`文件的应用程序文件夹，并将 **tmhOAuth** 库的内容复制到一个名为`twitter`的文件夹中。然后将`tmhOAuth.php`文件包含在`index.php`文件中(见下文)。

### 第二步——用 Twitter 认证用户

首先，用户必须使用他们的 Twitter 账户授权该应用。我们需要一个按钮或链接将用户重定向到授权过程。我们将使用一个单独的类来管理这个应用程序的逻辑。在应用程序文件夹中创建一个名为`twitter_hashtag_contest.php`的文件。现在我们来看看更新后的`index.php`文件。

```
<?php
    require 'twitter/tmhOAuth.php';
    require 'twitter_hashtag_contest.php';

    session_start();

    $contest = new TwitterHashTagContest();

    if(isset($_GET['action']) && $_GET['action'] == 'twitter'){

      $contest->getRequestToken();

    } else {
      echo "<a href='?action=twitter'>LogIn with Twitter</a>";
    }
```

我们可以默认显示登录链接。点击登录链接后，我们必须将用户重定向到 Twitter 进行应用程序授权并检索请求令牌。在我们进入`getRequestToken`函数的实现之前，我们必须设置`TwitterHashTagContest`类的初始细节。

### 步骤 3–初始化 TwitterHashTagContest 类

我们使用`TwitterHashTagContest`类来处理所有应用程序特定的细节，所以让我们来看看这个类的初始化和配置。

```
class TwitterHashTagContest{

    private $config;
    private $twitter_auth;
    private $app_details;
    private $result_tweets;

    public function __construct(){
        $this->config['consumer_key'] = 'consumer_key';
        $this->config['consumer_secret'] = 'consumer_secret';
        $this->config['OAUTH_CALLBACK']='URL to index.php file';

        $this->twitter_auth = new tmhOAuth($this->config);

        $this->result_tweets = array();
    }
}
```

我们可以通过在一个数组中定义`consumer_key`、`consumer_secret`和回调 URL 来开始配置。然后我们可以使用配置数组初始化`tmhOAuth`库。最后，我们初始化一个数组，用于在比赛结束时存储结果。

完成配置细节后，现在我们可以进入 Twitter 授权流程。

### 步骤 4–生成请求令牌

首先，我们必须获得请求令牌来初始化用户授权过程。Twitter 提供 API URL `oauth/request_token`来生成请求令牌。让我们来看看前面定义的`getRequestToken`函数的实现。

```
public function getRequestToken() {
    $this->twitter_auth->request("POST", $this->twitter_auth->url("oauth/request_token", ""), array(
        'oauth_callback' => $this->config['OAUTH_CALLBACK']
    ));

    if ($this->twitter_auth->response["code"] == 200) {

        // get and store the request token
        $response = $this->twitter_auth->extract_params($this->twitter_auth->response["response"]);
        $_SESSION["authtoken"] = $response["oauth_token"];
        $_SESSION["authsecret"] = $response["oauth_token_secret"];

        // redirect the user to Twitter to authorize
        $url = $this->twitter_auth->url("oauth/authenticate", "") . '?oauth_token=' . $response["oauth_token"];
        header("Location: " . $url);
        exit;
    }
    return false;
}
```

我们已经有了一个`tmhOAuth`类的实例，在`TwitterHashTagContest`的构造函数中初始化。我们必须使用`tmhOAuth`的请求方法来访问 Twitter 的 API。这个函数有 3 个参数，其中第一个参数定义了请求类型(POST 或 GET ),接下来的两个参数分别定义了 API 端点 URL 和参数。

我们使用`oauth/request_token` API URL 来生成令牌。响应将包含请求令牌以及请求的状态。代码 200 表示请求成功完成，我们使用`extract_params`函数将响应中的所有参数提取到一个数组中。接下来，我们在当前会话中存储`oauth_token`和`oauth_token_secret`。

最后，我们使用`oauth/authenticate`或`oauth/authorize`重定向用户进行授权。

我们可以使用`oauth/authenticate`或`oauth/authorize`来处理授权过程。`oauth/authorize`要求用户对每个请求授权应用程序，即使它已经被授权给用户。`oauth/authenticate`与`oauth/authorize`不同，它避免对每个请求进行授权。我们必须在 Twitter 应用程序的 Settings 选项卡上勾选**Allow this application to Sign in with Twitter**复选框，以允许这样做。

重定向后，用户可以登录并授权应用程序，然后 Twitter 会自动将请求重定向到回调 URL。接下来，我们必须通过验证请求令牌来生成用户特定的请求凭证(访问令牌)。

### 步骤 5–生成访问令牌

首先，我们必须过滤授权应用程序后检索到的响应。让我们看看更新后的代码。

```
if(isset($_GET['action']) && $_GET['action'] == 'twitter'){
    $contest->getRequestToken();
} else if(isset($_GET['oauth_token'])) {
    $oauth_token = isset($_GET['oauth_token']) ? $_GET['oauth_token'] : '';
    $oauth_verifier = isset($_GET['oauth_verifier']) ? $_GET['oauth_verifier'] : '';
    $response = $contest->getRequestCredentials($oauth_verifier,$oauth_token);
    $contest->createUserSession($response);
} else {
    echo "<a href='?action=twitter'>LogIn with Twitter</a>";
}
```

Twitter 的响应包含作为 URL 参数的`oauth_token`和`oauth_verifier`。我们可以使用这些参数过滤请求，并调用`getRequestCredentials`函数来生成访问令牌。下面的代码说明了`getRequestCredentials`的实现。

```
 public function getRequestCredentials($oauth_verifier,$oauth_token){
        $this->twitter_auth->request("POST", $this->twitter_auth->url("oauth/access_token", ""), array(  
            // pass a variable to set the callback 
            'oauth_verifier'    => $oauth_verifier,
            'oauth_token' => $oauth_token  
        ));

        $response = array();

        if($this->twitter_auth->response["code"] == 200) {  
            $response = $this->twitter_auth->extract_params($this->twitter_auth->response["response"]);  

        }

        return $response;
    }
```

正如我们前面所做的，POST 请求是在`$this->twitter_auth`对象上创建的，以访问`oauth/access_token` API。这里，我们传递从先前请求中检索的`oauth_verifier`和`oauth_token`来生成访问令牌。最后，我们检查响应代码以匹配 200，并返回响应对象的提取组件。

> 请记住，考虑到本教程的范围，我们只是在实现成功之路。在实际的实现中，我们还必须实现错误处理部分来过滤其他响应代码。

之后，我们可以使用访问令牌并为用户启用 tweet 功能。我们将响应传递给一个名为`createUserSession`的函数，用于将细节添加到浏览器会话中，并初始化 tweeting 屏幕。

> 在实际实现中，我们需要在数据库中保存访问令牌和登录用户的详细信息，以便检索令牌。这里，我们使用浏览器会话来简化教程。

## 结论

通过社交媒体竞赛推广应用程序是一个很好的方法。Twitter 是最容易举办竞赛的平台之一，所以我们的目标是在 Twitter 上建立一个标签竞赛。

到目前为止，我们已经实现了创建 Twitter 应用程序和验证用户进入系统的初始步骤。在下一部分，我们将通过为用户创建 tweet 功能并生成结果来完成这场比赛的实现。

在此之前，请告诉我们您对此部分的想法！

## 分享这篇文章