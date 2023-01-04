# PHP Twitter 应用程序的 OAuth，第 2 部分

> 原文：<https://www.sitepoint.com/oauth-for-php-twitter-apps-part-2/>

在[第 1 部分](https://www.sitepoint.com/article/oauth-for-php-twitter-apps-part-1)中，我们看了如何实现一个基于服务器的应用程序，该应用程序将使用 [OAuth](http://oauth.net/) 自动运行，这是 Twitter 目前唯一被接受的认证方法。在第 2 部分中，我们将介绍如何让用户使用 Twitter 的[“用 Twitter 登录”](http://dev.twitter.com/pages/sign_in_with_twitter)认证技术登录您的站点。这反过来会让我们访问 Twitter 的 API。

你可能已经注意到“使用 *x* 登录”按钮变得越来越流行，因为我们需要记住的用户名和密码组合的数量似乎在以指数速度增长。

单点登录的想法并不新鲜。最成功的实现之一是 [OpenID](http://openid.net/) ，它提供了一个单一身份来登录多个站点。正是在 OpenID 中对这一功能的讨论直接导致了 OAuth 的开发，OAuth 是一个对用户站点具有有限和明确访问权限的外部站点。

在 Twitter 和脸书等流行的大型应用程序中使用单点登录方法有助于推广单点登录，使其成为用户登录网站的一种简化方式。您可以让其他人去担心相关的安全流程！

让我们看看它是怎么做的。

## 注册你的应用

就像第 1 部分一样，如果你想以任何形式使用推特的应用编程接口，你必须向他们的开发者服务注册一个应用程序。前往[http://dev.twitter.com/apps](http://dev.twitter.com/apps)填写表格，特别注意以下字段:

*   应用类型必须是浏览器。用户和 Twitter 之间的交互只发生在浏览器中。

*   回调 URL 指的是服务器上的 PHP 文件，Twitter 将调用该文件来处理登录请求的结果。我们将创建这个文件，并将其命名为`callback.php`。记住还要包括服务器名称；比如`http://yourserver.com/twitter/callback.php`。

*   默认访问类型默认为只读，这对于本练习来说就足够了。如果你以后希望允许你的用户在登录你的网站时向 Twitter 发帖，你需要选择读写来代替。

注册后，Twitter 将为你提供一个 OAuth 消费者密钥和消费者秘密，你需要记录下来，以便在开发你的登录流程时使用。

## 下载 TwitterOAuth PHP 类

我们将再次使用奇妙的 Twitter/OAuth 包装类， [Abraham Williams](http://abrah.am/) 编写的[twitterouth](http://github.com/abraham/twitteroauth)。与第 1 部分一样，我们将使用版本 0 . 2 . 0-beta 3；然而，这一次不需要修改。首先，下载库的[副本。](http://github.com/abraham/twitteroauth/downloads)

## 设置您的登录页面

我们的第一个任务是创建一个页面，在您的服务器上启动整个登录过程。它本身需要做一些 OAuth 工作，创建一对请求令牌来附加到我们的登录链接。

创建包含以下代码的文件`login.php`:

**例 1。`login.php`**

```
<?php session_start();    // Include class & create require_once("consumer-keys.php");require_once("twitteroauth/twitteroauth.php");    // Create TwitterOAuth object with our Twitter provided keys $tOAuth = new TwitterOAuth($consumerKey, $consumerSecret);    // Generate request tokens$requestToken = $tOAuth->getRequestToken();$_SESSION["oauth_token"] = $requestToken["oauth_token"];$_SESSION["oauth_token_secret"] = $requestToken["oauth_token_secret"];    // Display Twitter log in button with encoded link echo "<a href="". $tOAuth->getAuthorizeURL($requestToken["oauth_token"]) . ""><img src="http://a0.twimg.cimg/dev/buttons/sign-in-with-twitter-d.png"></a>";?>
```

现在用下面的代码创建文件`consumer-keys.php`:

**例二。`consumer-keys.php`**

```
<?php   // The Twitter provided key & secret for your registered application $consumerKey = "[paste your Twitter provided consumer key]"; $consumerSecret = "[paste your Twitter provided consumer secret]"; ?>
```

信不信由你，你已经成功了一半。转到登录页面，点击登录按钮，登陆 Twitter 的网站。它会要求你允许或拒绝【你的应用名称】”访问你的 Twitter 账户。

如果您在此时单击任何一个选项，您都会生成一个 404 错误。Twitter 正在尝试加载您的回拨 URL ，我们尚未创建该 URL。让我们现在做那件事。

## 创建您的回调处理程序

接下来，您将创建在 Twitter 上注册应用程序时指定的回调文件。该文件是服务器上身份验证过程的控制器。根据用户的选择(允许或拒绝)，您现在必须执行适当的功能。

首先，我们将处理拒绝用户访问的问题。Twitter 将显示一个“拒绝”页面，其中有一个链接将用户弹回你的回拨 URL。它还将附加一个`GET`变量`denied`，该变量的值将等于上面您的登录页面上生成的请求令牌。

如果你想非常谨慎，你可以测试`$_GET["denied"]`是否等于`$_SESSION["oauth_token"]`，但是在下面的例子中我还没有做到这一步。如何处理拒绝完全取决于你自己。在本例中，我只是提供了一个返回登录页面的链接。

创建包含以下代码的文件`callback.php`:

**例 3。`callback.php`**

```
<?php session_start();    // Include class & create require_once("consumer-keys.php");require_once("twitteroauth/twitteroauth.php");    // User has selected to DENY access if(!empty($_GET["denied"])) {  // could re-direct or display cancelled view/template  // we're just echoing out a message  echo "No deal! <a href='login.php'>Try again?</a>";  die();}?>
```

接下来，我们需要处理更可取的选项:允许您的用户登录您的网站。这一次，初始请求令牌在`GET`变量`oauth_token`中被发送回来，在继续之前，检查它是否与我们的会话中存储的令牌相匹配。

此时，必须对这些访问令牌的存储做出决定。您可能希望将它们保存到 cookie 中(以类似于正常登录系统上的“记住我”复选框的方式使用)，或者保存到数据库中，以便与本地用户相关记录进行匹配。为了便于演示，我只是将它们放入会话的存储中。

将访问令牌保存到会话后，您可以取消注册以前的请求令牌，因为不再需要它们，并重定向用户:

**例 4。`callback.php`(续)**

```
<?php // User has selected to ALLOW access for given token if($_GET["oauth_token"] == $_SESSION["oauth_token"]) {  // Use generated request tokens (from session) to construct object  $tOAuth = new TwitterOAuth($consumerKey, $consumerSecret, $_SESSION["oauth_token"], $_SESSION["oauth_token_secret"]);  // Retrieve access token from Twitter  $accessToken = $tOAuth->getAccessToken();  // Check we have valid response  if(is_numeric($accessToken["user_id"]))  {    // Save the access tokens to a DB (we're using a session)    $_SESSION["access_token"] = $accessToken;    // Remove request token session variables    unset($_SESSION["oauth_token"]);    unset($_SESSION["oauth_token_secret"]);    // Redirect to main page    header("location: welcome.php");  } else {    header("location: login.php");  }}?>
```

## 恭喜你！您的用户已经登录

既然 Twitter 已经给了我们通往这个王国的钥匙(存储在我们会话中的访问令牌)，那么用户被定向到的页面只需要检查这些令牌，并用 Twitter 验证它们。之后，您的用户就可以像使用自定义的本地登录过程登录到您的站点一样继续操作。额外的好处是，您现在能够对 Twitter 执行 API 调用。

创建包含以下代码的文件`welcome.php`:

**例 5。`welcome.php`**

```
<?php session_start();      // Include class & createrequire_once("consumer-keys.php");require_once("twitteroauth/twitteroauth.php");      // Check we have active session with access tokens from Twitterif(!empty($_SESSION["access_token"]) && !empty($_SESSION["access_token"]["oauth_token"])) {  // Create new TwitterOAuth object with access tokens  $tOAuth = new TwitterOAuth($consumerKey, $consumerSecret, $_SESSION["access_token"]["oauth_token"], $_SESSION["access_token"]["oauth_token_secret"]);  // Perform an API request  $credentials = $tOAuth->get('account/verify_credentials');  echo "Logged in as @" . $credentials->screen_name;  echo "<br>YAY! You've logged in using Twitter via OAuth!";}?>
```

## 总结

使用 OAuth 和用 Twitter 登录来提供单点登录功能是让你的网站访问者生活更轻松的有效方法。您还可以获得一些强大的交互选项，同时保护用户的安全。

## 分享这篇文章