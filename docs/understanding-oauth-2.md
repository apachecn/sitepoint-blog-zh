# 理解 OAuth——从零开始发推特，第 2 部分

> 原文：<https://www.sitepoint.com/understanding-oauth-2/>

欢迎回到理解 OAuth——从零开始发推特。这是由两部分组成的系列文章的第 2 部分，从我们在第 1 部分中停止的地方继续讨论您返回的访问凭证。由于获取凭证是这个过程中最艰难的部分，所以除了代表用户发布一条 tweet 之外，没有什么可以做的了。希望你会发现最后的步骤更容易遵循，执行起来也更有趣。

首先，您至少应该将第 1 部分中的用户详细信息和凭证存储在`$_SESSION`中，以便以后使用，尽管您很可能希望将信息存储在数据库中，以便在您想代表他人发送 tweets 时可以检索它们。

```
<?php
//...
$response = file_get_contents($requestUrl);
parse_str($response, $values);

$_SESSION["accessToken"] = $values["oauth_token"];
$_SESSION["accessTokenSecret"] = $values["oauth_token_secret"];
$_SESSION["twitterUserId"] = $values["user_id"];
$_SESSION["twitterUsername"] = $values["screen_name"];

// Redirect the user to the application's form 
header("Location: /postTweet.php");
```

因为您的应用程序有必要的访问凭证，并且您已经安全地将它们保存起来以备将来使用，所以您可以将用户重定向到一个表单，在那里他可以输入他的 tweet 文本。

```
<form action="/postTweet.php" method="post">
 <textarea name="tweet" rows="3" cols="50"></textarea>
 <br>
 <input type="submit" value="Send">
</form>
```

## 发布到 Twitter

一旦你有了访问凭证，你就可以用 Twitter API 做很多事情(完整的 API 文档可以在 dev.twitter.com/docs/api 的[找到)。在这篇文章中，我将展示如何使用状态/更新来发布推文。只需在 URL 的末尾添加`.xml`或`.json`，就可以请求许多 API 调用用 XML 或 JSON 格式的数据进行响应。我个人认为 JSON 比 XML 更容易处理。](http://dev.twitter.com/docs/api "Twitter Developers")

`statuses/update`需要一个 HTTP `POST`来发送 HTTP 头中的 OAuth 参数。以前你只在和推特的对话中使用`GET`。

所有 OAuth v1 Twitter API 资源 URL 都以“http://api.twitter.com/1/”开头，以资源名称后跟`.xml`或`.json`结尾。从现在起，您需要的唯一凭据是您的消费者凭据和访问凭据；请求凭证被丢弃，因为它们只是授权过程所必需的。

构建签名的执行与之前相同，只是现在在`$sigKey`中包含了访问令牌秘密。

```
<?php
$oauthVersion = "1.0";
$apiResourceUrl = "http://api.twitter.com/1/statuses/update.json"; 
$nonce = md5(mt_rand()); 
$oauthSignatureMethod = "HMAC-SHA1"; 
$oauthTimestamp = time();
$accessToken = $_SESSION["accessToken"];
$accessTokenSecret = $_SESSION["accessTokenSecret"];
$tweetText = trim($_POST["tweet"]);

$sigBase = "POST&" . rawurlencode($apiResourceUrl) . "&"
    . rawurlencode("oauth_consumer_key=" . rawurlencode($consumerKey)
    . "&oauth_nonce=" . rawurlencode($nonce)
    . "&oauth_signature_method=" . rawurlencode($oauthSignatureMethod)
    . "&oauth_timestamp=" . $oauthTimestamp
    . "&oauth_token=" . rawurlencode($accessToken)
    . "&oauth_version=" . rawurlencode($oauthVersion)
    . "&status=" . rawurlencode($tweetText));
$sigKey = rawurlencode($consumerSecret) . "&" . rawurlencode($accessTokenSecret); 
$oauthSig = base64_encode(hash_hmac("sha1", $sigBase, $sigKey, true));
```

OAuth `POST`事务可以(并且 Twitter 要求)将 OAuth 参数包含在一个特殊的`Authorization` HTTP 头中。值得注意的是，除了典型的 OAuth 参数外，`$sigBase`还包括上面的 API 资源参数状态。签名中需要非 OAuth 参数，但这些参数不包含在 HTTP 头中。它们被用在了`POST`的身上。

```
<?php
$authHeader = "OAuth oauth_consumer_key=" . rawurlencode($consumerKey) . ","
    . "oauth_nonce=" . rawurlencode($nonce) . ","
    . "oauth_signature_method=" . rawurlencode($oauthSignatureMethod) . ","
    . "oauth_signature=" . rawurlencode($oauthSig) . ","
    . "oauth_timestamp=". rawurlencode($oauthTimestamp) . ","
    . "oauth_token=" . rawurlencode($accessToken) . ","
    . "oauth_version=" . rawurlencode($oauthVersion); 

$httpPostDataUrl = "status=" . $tweetText; 

$context = stream_context_create(array("http" => array(
    "method" => "POST", 
    "header" => "Content-Type: application/x-www-form-urlencodedrnAuthorization: " . $authHeader . "rn", 
    "content" => $httpPostDataUrl)));

$result = file_get_contents($apiResourceUrl, false, $context);
```

如果一切顺利，你应该代表授权用户发布到 twitter。Twitter 发送成功或失败状态以及大量关于交易的信息，如果你愿意，你可以用`print_r(json_decode($result))`发送。

## 摘要

既然您已经对 OAuth 的工作原理有了相当的了解，那么实现第三方库或者对 Oauth 进行一般性的故障排除应该会容易得多。总之，您已经学会了如何:

*   创建一个新的 Twitter 应用程序并获取消费者凭证
*   获取请求访问凭据所需的请求凭据
*   向用户授权您的应用程序，并获取访问凭据
*   使用访问凭证代表另一个用户发布推文

今天，一些最流行的网络应用程序提供了对 API 的访问——[Flickr](http://www.flickr.com/services/api/ "Flickr Services")、[脸书](http://developers.facebook.com/ "Home - Facebook Developers")、 [foursquare](https://developer.foursquare.com/ "foursquare for Developers - foursquarefoursquare") 、[网飞](http://developer.netflix.com/ "Netflix API - Welcome to the Netflix Developer Network")、 [Last.fm](http://www.last.fm/api "API - Last.fm") 和 [GetGlue](http://getglue.com/api "API | GetGlue") 等等。深入了解如何与这些应用程序无缝通信是一项非常需要的技能！您在这里学到的一些关于签名散列和令牌交换的知识也可以很好地应用于非 Oauth v1.0 APIs，例如 Flickr 的预 Oauth 接口和更容易理解的 [OAuth v2](http://oauth.net/2/ "OAuth 2.0 - OAuth") 。祝你好运，玩得开心！

这个系列的代码可以在 GitHub 上找到。

图片通过[Photosani](http://www.shutterstock.com/gallery-54706p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章