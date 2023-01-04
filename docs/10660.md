# PHP Twitter 应用程序的 OAuth，第 1 部分

> 原文：<https://www.sitepoint.com/oauth-for-php-twitter-apps-part-1/>

截至 8 月 31 日，Twitter 的 HTTP 基本认证 API 服务已被完全禁用。现在，任何与 API 的接口都需要使用基于令牌的身份验证方法，即 OAuth。

这是什么意思？任何人如果编写了一个基于服务器的应用程序，通过它的 API 与 Twitter 对话，都需要花一些时间来升级它。在本文中，我们将重点关注自动化后端类型的应用程序，如当网站上有新交易时自动发布推文的应用程序、体育比分推文或其他类似的应用程序。在第二部分中，我们将看看更传统的 OAuth 模型，其中个人用户授权您的应用程序通过 web 接口访问他们的 Twitter 帐户。

乍一看，实现 OAuth 的任务似乎令人生畏，如果不熟悉的话，OAuth 的令牌交换系统可能会令人困惑。幸运的是，有一些相当聪明的人已经为我们做了基础工作，所以让我们开始吧。

## 注册你的应用

以前，Twitter 不要求你注册应用程序。但是，根据 OAuth，您必须这样做。当您这样做时，Twitter 将为您提供一组将您的应用程序连接到 API 所需的密钥。

要注册你的应用，请访问[http://dev.twitter.com/apps](http://dev.twitter.com/apps)并完成表格，其中大部分都很简单——尽管有几个字段可能会让你困惑:

*   如果您的应用程序不需要通过 web 界面验证用户，回调 URL 可以留空。在我上面提到的例子中(网站的自动推文功能)，没有登录过程，所以不需要提供一个 URL 来重定向用户。

*   默认访问类型指的是你的应用程序对 Twitter 的访问。只读取消了发布推文或添加/删除好友的功能，而读&写允许完全访问。这几乎可以肯定是您的应用程序所需要的。

*   应用程序类型应更改为客户端，即使该应用程序可能由模拟 web 浏览器点击的 cron 作业运行，或者通过您网站的幕后触发器运行，用户也不会通过浏览器直接与之交互。

一旦你的应用被注册，你将会得到一个 OAuth 消费者密钥和消费者秘密。这两个字符串是我们 OAuth 连接的基础。

## 下载 TwitterOAuth PHP 类

最初由 [Abraham Williams](http://abrah.am/) 、[开发的 Twitter auth](http://github.com/abraham/twitteroauth)处理与 Twitter 接口的 OAuth 请求的生成、注册和认证。多亏了这个类，不需要理解 OAuth 密钥和令牌操作的所有细节。继续[下载库的副本](http://github.com/abraham/twitteroauth/downloads)。对于本文，我将使用版本 0.2.0-beta3。

## 修改该类以允许基于 PIN 的 OAuth 注册

通常，基于网络的 Twitter 应用程序会通过将用户转到 Twitter 来验证 Twitter，用户可以点击授权按钮。然而，对于没有用户交互的应用程序(例如上面给出的自动 Tweet 应用程序)，我们宁愿避免这样做。幸运的是，Twitter 已经为桌面应用程序提供了一个基于 PIN 的认证选项，但同样适用于我们基于 web 的自动化应用程序。

最初的请求由 Twitter 授权，会给你一个七位数的 PIN。然后，您的应用程序将使用该 PIN 发出请求，这将为它提供一个访问令牌，以便在将来的请求中使用。

然而，我们上面提到的 TwitterOAuth 类缺乏对基于 PIN 的注册的本地支持，因此我们需要对代码进行快速更改，以使一切正常工作。这段代码是基于康斯坦丁·科夫谢宁的博客文章[做了一些修改。编辑文件`twitteroauth.php`,用以下内容替换从第 111 行开始的函数`getAccessToken`:](http://kovshenin.com/archives/twitter-api-pin-based-oauth-php/)

```
function getAccessToken($token = NULL, $pin = NULL) {  if($pin) {    $request = $this->oAuthRequest($this->accessTokenURL(), 'POST', array('oauth_verifier' => $pin));  } else {    $request = $this->oAuthRequest($this->accessTokenURL());  }  $token = OAuthUtil::parse_parameters($request);  $this->token = new OAuthConsumer($token['oauth_token'], $token['oauth_token_secret']);  return $token;}
```

## 创建请求令牌和注册 URL

现在您需要为您的应用程序生成一些访问令牌。这分两步进行:首先，生成一些请求令牌和一个注册 URL，然后使用它们生成访问令牌。您将编写的代码只是为了获取这些令牌，可以与您的应用程序分开。因此，您可以从开发服务器或本地服务器运行它。

在您的服务器的 web 根目录下创建一个文件夹，并将您下载(并修改)的`twitteroauth`文件夹放入其中。

同样在该文件夹中，创建一个名为`register.php`的文件，包含以下代码:

**例 1。`register.php`**

```
<?phprequire_once('twitteroauth/twitteroauth/twitteroauth.php');$oauth = new TwitterOAuth('*`consumer_key`*','*`consumer_secret`*');$request = $oauth->getRequestToken();$requestToken = $request['oauth_token'];$requestTokenSecret = $request['oauth_token_secret'];// place the generated request token/secret into local filesfile_put_contents('request_token', $requestToken);file_put_contents('request_token_secret', $requestTokenSecret);// display Twitter generated registration URL$registerURL = $oauth->getAuthorizeURL($request);echo '<a href="' . $registerURL . '">Register with Twitter</a>';?>
```

记得添加您的消费者密钥和秘密。

一旦你创建了注册脚本，你需要运行它来请求 Twitter 的访问:

1.  使用您想要应用程序使用的 Twitter 帐户登录 Twitter(通过网站)

2.  在浏览器中加载`register.php`文件

3.  该脚本将在其目录中创建两个文件，包含您的请求令牌和请求令牌密码

4.  屏幕上将显示一个链接；这是您注册 URL 的链接。

5.  点击该链接，Twitter 将询问您是否愿意授权该应用程序访问您的帐户(您在步骤 1 中登录的帐户)。

6.  最后，Twitter 会显示一个七位数的 PIN。抄下来。

## 生成访问令牌

既然 Twitter 已经授权了我们的访问请求，我们需要传回所提供的 PIN，以便生成一个访问令牌及其秘密。一旦完成，我们的应用程序将永远不会再经历这个认证过程，并将永久访问链接的 Twitter 帐户。

在您的开发文件夹中创建一个新文件`validate.php`:

**例二。`validate.php`**

```
<?php// Retrieve our previously generated request token & secret$requestToken = file_get_contents("request_token");$requestTokenSecret = file_get_contents("request_token_secret");// Include class file & create object passing request token/secret alsorequire_once("twitteroauth/twitteroauth/twitteroauth.php");$oauth = new TwitterOAuth('*`consumer_key`*', '*`consumer_secret`*', $requestToken, $requestTokenSecret);// Generate access token by providing PIN for Twitter$request = $oauth->getAccessToken(NULL, $_GET["pin"]);$accessToken = $request['oauth_token'];$accessTokenSecret = $request['oauth_token_secret'];// Save our access token/secretfile_put_contents("access_token", $accessToken);file_put_contents("access_token_secret", $accessTokenSecret);?>
```

这个脚本希望您将 PIN 作为一个`GET`参数传递(例如，`http://dev.machine/validate.php?pin=1234567`)，并将名为`access_token`和`access_token_secret`的文件保存到文件夹中。在您的浏览器中访问它，传递 PIN。

**warning:** Time is of the Essence

我们在上一步中生成的请求令牌和注册 URL 并不是无限期的:Twitter 会在给定的时间过后让它们过期。如果您在访问`validate.php`时收到错误，或者发现生成的文件是空的，这可能是因为您启动脚本的时间太长了。

如果发生这种情况，只需再次点击`register.php`即可生成一个新的 PIN。

## 发微博去吧！

所有 OAuth 标记都已完成，是时候实际使用 API 了。让我们尝试一下最简单的测试，从授权账户发出“hello world ”:

**例 3。`test.php`**

```
<?php// Read in our saved access token/secret$accessToken = file_get_contents("access_token");$accessTokenSecret = file_get_contents("access_token_secret");// Create our twitter API objectrequire_once("twitteroauth/twitteroauth/twitteroauth.php");$oauth = new TwitterOAuth('*`consumer_key`*', '*`consumer_secret`*', $accessToken, $accessTokenSecret);// Send an API request to verify credentials$credentials = $oauth->get("account/verify_credentials");echo "Connected as @" . $credentials->screen_name;// Post our new "hello world" status$oauth->post('statuses/update', array('status' => "hello world"));?>
```

将这个文件放在您的测试目录中，并通过您的浏览器访问它(同样，您需要粘贴您自己的消费者密钥和秘密)。你会看到确认你的帐户已连接，如果你现在加载该帐户的 Twitter 个人资料，你应该会看到一个“hello world”推文！

## 最后，一次清理和一些安全措施

现在你已经启动并运行了，你的应用程序不再需要`register.php`、`validate.php`、`request_token`和`request_token_secret`文件。已经生成的访问令牌现在可以处理应用程序和 Twitter 之间的所有来回访问。你还会注意到，我们写的任何代码，或者任何生成的文件，都没有提到链接的 Twitter 账户的用户名或密码；这是 OAuth 方法的主要安全优势。

也就是说，如果你的`access_token`和`access_token_secret`文件在一个可公开访问的网络目录中，账户的安全性仍然会受到威胁。一旦生成，它们应该被转移到服务器的 web 文件夹之外，并通过 PHP 的文件操作加载进来。如果出于某种原因，您无法选择，请将它们移动到无法通过 Web 访问的子文件夹中。

## 总结

虽然预先设置一个可以访问 Twitter 帐户的应用程序变得有点复杂，但是 OAuth 方法确实有安全方面的好处。一旦你掌握了诀窍，用 Twitter 账户设置新的应用程序就非常简单了。

在本系列的第二部分中，我们将把注意力转向 Twitter 应用程序，这些应用程序涉及到您的用户通过 Web 单独授权他们的帐户。

图片来源: [drdjo](http://www.kweeper.com/drdjo/image/12449)

## 分享这篇文章