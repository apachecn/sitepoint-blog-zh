# 理解 OAuth——从零开始发推特，第 1 部分

> 原文：<https://www.sitepoint.com/understanding-oauth-1/>

关于 OAuth 的一个常见抱怨是很难理解，但也许这种混淆是因为期望由第三方库提供的抽象将消除理解 OAuth 事务步骤的需要——事实并非如此。这篇由两部分组成的文章通过解释将一个 PHP 应用程序连接到 [Twitter API](https://dev.twitter.com/ "Twitter Developers") 的过程，展示了【OAuth v1 是如何工作的，该过程仅使用几个内置函数向用户的 Twitter 流发送消息。

本文中提供的示例代码仅用于教育目的。它缺乏很多实际用途和错误处理，任何真正的开发都应该利用现有的 OAuth 库，但是在阅读本文之后，您将对 OAuth 如何工作有更好的理解，并且能够更好地解决可能出现的任何问题。

## 获取消费者凭据

每次代表用户向 Twitter 发帖而不要求用户名和密码组合，需要一种“代客钥匙”…这个钥匙就是 OAuth。在 OAuth 术语中，您的客户端应用程序被称为*消费者*，用户被称为*资源所有者*，Twitter 是*服务器*或*服务提供者*。

在 Twitter 接受来自你的应用程序的帖子之前，你需要获得你自己的客户端凭证:消费者密钥和消费者秘密。像大多数提供公共 API 的服务一样，Twitter 会在你完成一份注册表格后授予你一个密钥和秘密(可在 dev.twitter.com/apps[获得)。使用 Twitter，您需要在授权过程中向用户提供一些识别您的应用程序的信息，特别是它的名称、描述和网站 URL。您还需要输入一个回调 URL，稍后我会解释这一点。](https://dev.twitter.com/apps "Twitter Developers")

提交“创建申请”表单后，您将进入新申请的详细信息页面。向下滚动找到您的消费者密钥和密码。您将需要这些以及列出的一些端点 URL。请注意，默认情况下，应用程序被授予只读访问权限；由于它将发布推文，您需要单击设置选项卡，并将访问权限更改为“读写”。

## 授权应用程序

消费者密钥和消费者秘密允许您的应用程序与 Twitter API 对话，但仅凭这两者是不会让它代表另一个用户发布 tweets 的。你需要访问凭证:一个*访问令牌*和*访问秘密*。通过在一个简短的对话中向服务提供商(Twitter)发出请求来获取这些信息，对话中使用了涉及 Twitter 和最终用户的消费者凭证。获取访问凭证可能相当痛苦，但幸运的是，您只需要为每个用户做一次。您可以无限期地保存凭据以供以后使用，而不需要用户对您的应用程序重新授权。请求访问凭证的对话需要它自己的一组凭证:请求令牌和请求秘密*。*

### 授权步骤 1:请求凭据

授权过程通常从将用户定向到授权 Twitter 页面开始。这是您创建的一个页面，它从 Twitter 发起请求令牌的请求，并开始 OAuth 授权过程。它必须生成一个用于获取请求凭证的 URL，一旦获得凭证，就可以将用户重定向到 Twitter，以授予应用程序发布的权限。

请求请求凭证需要一个签名的请求，这意味着您需要发送一个 OAuth 签名以及请求中的其他重要参数。签名是请求参数的 base64 编码哈希列表。在 Twitter 的例子中，哈希算法是 HMAC-SHA1 算法。签名过程防止任何人用您的凭证冒充您的应用程序，即使消费者密钥是以明文传输的。签名只能由您(消费者)和服务器(Twitter)复制，因为您和服务器是唯一应该知道散列签名的消费者秘密的实体。

让我们构建一个签名所基于的字符串:

```
<?php
$requestTokenUrl = "http://api.twitter.com/oauth/request_token"; 
$authorizeUrl = "http://api.twitter.com/oauth/authorize";
$oauthTimestamp = time();
$nonce = md5(mt_rand()); 
$oauthSignatureMethod = "HMAC-SHA1"; 
$oauthVersion = "1.0";

$sigBase = "GET&" . rawurlencode($requestTokenUrl) . "&"
    . rawurlencode("oauth_consumer_key=" . rawurlencode($consumerKey)
    . "&oauth_nonce=" . rawurlencode($nonce)
    . "&oauth_signature_method=" . rawurlencode($oauthSignatureMethod)
    . "&oauth_timestamp=" . $oauthTimestamp
    . "&oauth_version=" . $oauthVersion);
```

上面的一些变量可能相当明显——`$requestTokenUrl`是在您被授予消费者凭证时从 Twitter 获得的，而`$oauthTimestamp`是当前的 UNIX 纪元时间。不太明显的项目是`$nonce`，它只不过是一个只使用一次的随机字符串(每个事务使用一个不同的 nonce)。通常情况下，MD5 哈希随机数可以很好地用作随机数。此外，对于 Twitter 来说，`$oauthSignatureMethod`始终是 HMAC-SHA1，而`$oauthVersion`目前是 Twitter 的 1.0 版本。

接下来，签名的字符串被构造为`$sigBase`。OAuth 表示，签名基础必须是 HTTP 方法(在本例中为`GET`)，后跟一个&，接着是 URL 编码的请求 URL ( `$requestTokenUrl`)，再接着是另一个&，最后是一个 URL 编码的、按字母顺序排列的参数键/值对列表(其值也必须进行编码)，由“&”分隔。注意，当 OAuth 需要 URL 编码的内容时，它意味着 [RFC-3986](http://tools.ietf.org/rfc/rfc3986.txt "Uniform Resource Identifier (URI): Generic Syntax
") 。PHP 的`rawurlencode()`函数可以工作，因为它将空格编码为“%20”，而不是像`urlencode()`那样编码为“+”。

您还需要一个签名密钥。密钥始终是消费者密码，后跟一个“&”和 OAuth 令牌密码(这是您还没有的令牌凭证的一部分)，或者 2)什么也没有。然后可以使用 PHP 内置的`hash_hmac()`函数生成最终的签名。

```
<?php
$sigKey = $consumerSecret . "&"; 
$oauthSig = base64_encode(hash_hmac("sha1", $sigBase, $sigKey, true));
```

您将所有部分放在一起，构建一个 URL 来请求请求凭证:

```
<?php
$requestUrl = $requestTokenUrl . "?"
    . "oauth_consumer_key=" . rawurlencode($consumerKey)
    . "&oauth_nonce=" . rawurlencode($nonce)
    . "&oauth_signature_method=" . rawurlencode($oauthSignatureMethod)
    . "&oauth_timestamp=" . rawurlencode($oauthTimestamp)
    . "&oauth_version=" . rawurlencode($oauthVersion)
    . "&oauth_signature=" . rawurlencode($oauthSig); 

$response = file_get_contents($requestUrl);
```

除了这个简单的演示之外，您还需要更多的错误处理，但是现在我假设不会出错，并且您能够在`$response`中接收临时请求凭证。

Twitter 发回的回复如下:

```
oauth_token=mjeaYNdNYrvLBag6xJNWkxCbgL5DV6yPZl6j4palETU&oauth_token_secret=W45dnBz917gsdMqDu4bWNmShQq5A8pRwoLnJVm6kvzs&oauth_callback_confirmed=true
```

`oauth_token`和`oauth_token_secret`值是从响应中提取的，用于构建用户进入授权过程第二步的下一个链接。将请求凭证存储在用户的会话中是一个好主意，这样当用户从 Twitter 的授权页面返回时就可以使用它们。向 Twitter 注册应用程序后，详细信息页面上会提供授权 URL。

```
<?php
parse_str($response, $values);
$_SESSION["requestToken"] = $values["oauth_token"];
$_SESSION["requestTokenSecret"] = $values["oauth_token_secret"];

$redirectUrl = $authorizeUrl . "?oauth_token=" . $_SESSION["requestToken"]; 
header("Location: " . $redirectUrl);
```

既然应用程序可以将用户发送到 Twitter 进行授权，现在是添加回调 URL 的好时机，这样 Twitter 就可以将用户发送回应用程序！回调 URL 只是一个地址，在用户授权您的应用程序代表他发送 tweets 后，Twitter 会将用户指引到这个地址，它是在详细页面的 Settings 选项卡上指定的。

当 Twitter 将用户重定向到回调 URL 时，它会附加两个额外的参数:来自初始请求的可用于验证的 your `oauth_token`,以及用于获取授权凭证的`oauth_verifier`。

在发布 tweets 所需的三组凭证中，现在有两组了——消费者凭证和请求凭证。接下来:访问凭证！

### 授权步骤 2:访问凭据

要获得访问凭证，您需要`oauth_token`、`oauth_token_secret`和新获得的`oauth_verifier`。这一步需要另一个签名请求，这次是对显示在详细信息页面上的访问令牌 URL 的请求。

```
<?php
$oauthVersion = "1.0";
$oauthSignatureMethod = "HMAC-SHA1"; 
$accessTokenUrl = "http://api.twitter.com/oauth/access_token"; 
$nonce = md5(mt_rand()); 
$oauthTimestamp = time();
$oauthVerifier = $_GET["oauth_verifier"];
```

`$accessTokenUrl`是从细节页面获得的下一个端点。生成新的`$oauthTimestamp`和`$nonce`，并从 Twitter 授权页面发回`$oauthVerifier`。没有列出，但是在`$_SESSION`数组中，是上一步中的请求凭证，也是必需的。

授权过程的这一步需要另一个签名的请求。一旦建立了签名，它就与访问凭证的请求一起使用。

```
<?php
$sigBase = "GET&" . rawurlencode($accessTokenUrl) . "&"
    . rawurlencode("oauth_consumer_key=" . rawurlencode($consumerKey)
    . "&oauth_nonce=" . rawurlencode($nonce)
    . "&oauth_signature_method=" . rawurlencode($oauthSignatureMethod)
    . "&oauth_timestamp=" . rawurlencode($oauthTimestamp)
    . "&oauth_token=" . rawurlencode($_SESSION["requestToken"])
    . "&oauth_verifier=" . rawurlencode($oauthVerifier)
    . "&oauth_version=" . rawurlencode($oauthVersion)); 
$sigKey = $consumerSecret . "&"; 
$oauthSig = base64_encode(hash_hmac("sha1", $sigBase, $sigKey, true));

$requestUrl = $accessTokenUrl . "?"
    . "oauth_consumer_key=" . rawurlencode($consumerKey)
    . "&oauth_nonce=" . rawurlencode($nonce)
    . "&oauth_signature_method=" . rawurlencode($oauthSignatureMethod)
    . "&oauth_timestamp=" . rawurlencode($oauthTimestamp)
    . "&oauth_token=" . rawurlencode($_SESSION["requestToken"])
    . "&oauth_verifier=" . rawurlencode($oauthVerifier)
    . "&oauth_version=". rawurlencode($oauthVersion)
    . "&oauth_signature=" . rawurlencode($oauthSig); 

$response = file_get_contents($requestUrl);
```

这次`$response`有非常有用的`screen_name`、`user_id`和期待已久的访问凭证！

## 摘要

本文的授权部分到此结束。到目前为止，您已经学习了如何创建一个新的 Twitter 应用程序，并使用所提供的消费者凭证逐步通过 OAuth“舞蹈”来获得访问凭证。在本系列的第二部分也是最后一部分，我将讨论使用访问凭证向用户的 Twitter 流发布一条 tweet。

图片 via[Quin](http://www.shutterstock.com/gallery-151012p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章