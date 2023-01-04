# JWT 的 PHP 授权(JSON Web 令牌)

> 原文：<https://www.sitepoint.com/php-authorization-jwt-json-web-tokens/>

曾经有一段时间，向应用程序验证自己身份的唯一方法是提供您的凭据(通常是用户名或电子邮件地址和密码)，然后使用一个会话来维护用户状态，直到用户注销。过了一会儿，我们开始使用认证 API。最近，jwt 或 JSON Web 令牌越来越多地被用作向服务器验证请求的另一种方式。

在本文中，您将了解什么是 jwt，以及如何在 PHP 中使用它们来发出经过身份验证的用户请求。

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## jwt 与会话

但是首先，为什么会话不是这样一件好事呢？嗯，有三个主要原因:

*   **数据以纯文本形式存储在服务器**上。
    尽管数据通常不存储在公共文件夹中，但任何对服务器有足够访问权限的人都可以读取会话文件的内容。
*   它们涉及文件系统读/写请求。
    每次会话开始或其数据被修改时，服务器需要更新会话文件。每次应用程序发送一个会话 cookie 时也是如此。如果有大量用户，除非使用替代的会话存储选项，如 Memcached 和 Redis，否则最终会导致服务器运行缓慢。
*   **分布式/集群化应用**。
    由于默认情况下会话文件存储在文件系统中，因此很难为高可用性应用程序建立分布式或集群式基础设施，这些应用程序需要使用负载平衡器和集群服务器等技术。必须实施其他存储介质和特殊配置，并且在实施时要充分意识到它们的影响。

## JWT

现在，让我们开始学习 jwt。[JSON Web Token 规范(RFC 7519)](http://www.rfc-editor.org/info/rfc7519) 于 2010 年 12 月 28 日首次发布，最近一次更新是在 2015 年 5 月。

与 API 键相比，jwt 有许多优点，包括:

*   API 键是随机的字符串，而 jwt 包含信息和元数据。这些信息和元数据可以描述各种各样的事情，例如用户的身份、授权数据以及令牌在某个时间范围内或与某个域相关的有效性。
*   jwt 不需要集中的发布或撤销权限。
*   jwt 与 OAUTH2 兼容。
*   可以检查 JWT 数据。
*   jwt 有到期控制。
*   jwt 适用于空间受限的环境，比如 HTTP 授权头。
*   数据以 JavaScript 对象符号格式(JSON)传输。
*   jwt 使用 [Base64url 编码](http://www.herongyang.com/Encoding/Base64URL-Encoding-Algorithm.html)来表示

## JWT 长什么样？

这里有一个 JWT 的例子:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE0MTY5MjkxMDksImp0aSI6ImFhN2Y4ZDBhOTVjIiwic2NvcGVzIjpbInJlcG8iLCJwdWJsaWNfcmVwbyJdfQ.XCEwpBGvOLma4TCoh36FU7XhUbcskygS81HE1uHLf0E 
```

乍一看，该字符串似乎只是用句点或点字符连接起来的随机字符组。因此，它可能看起来与 API 键没有太大区别。然而，如果你更仔细地看，有三个单独的字符串。

### JWT 头球

第一个字符串是 JWT 标头。这是一个 [Base64](http://en.wikipedia.org/wiki/Base64) ，URL 编码的 JSON 字符串。它指定使用哪种加密算法来生成签名，以及令牌的类型，该类型总是被设置为`JWT`。算法可以是*对称*或者*非对称*。

**对称算法**使用单个密钥来创建和验证令牌。密钥由 JWT 的创建者和使用者共享。你必须确保只有创造者和消费者知道这个秘密。否则，*任何人*都可以创建一个有效的令牌。

一种非对称算法使用私钥对令牌进行签名，使用公钥对其进行验证。当共享秘密不切实际或者其他方只需要验证令牌的完整性时，应该使用这些算法。

### JWT 的有效载荷

第二根弦是 JWT 的有效载荷。它也是一个 Base64、URL 编码的 JSON 字符串。它包含一些标准字段，称为“声明”。债权有三种类型:*注册*、*公有*和*私有*。

**登记的权利要求**是预定义的。你可以在[JWT 的 RFC](https://tools.ietf.org/html/rfc7519#page-9) 中找到他们的名单。下面是一些常用的:

*   `iat`:令牌发放的时间戳。
*   `key`:唯一的字符串，可用于验证令牌，但不利于没有集中的颁发机构。
*   `iss`:包含发行者的名称或标识符的字符串。可以是域名，可用于丢弃来自其他应用程序的令牌。
*   `nbf`:令牌应该开始被认为有效的时间戳。应等于或大于`iat`。
*   `exp`:令牌应该停止有效的时间戳。应该大于`iat`和`nbf`。

**大众主张**你看着合适就可以定义。但是，它们不能与已登记的权利要求相同，或者与[已经存在的公共权利要求](https://tools.ietf.org/html/rfc7519#page-9)相同。您可以随意创建私人索赔。它们只在两方之间使用:生产者和消费者。

### JWT 的签名

JWT 签名是一种加密机制，旨在通过令牌内容独有的数字签名来保护 JWT 的数据。签名确保了 JWT 的完整性，因此消费者可以验证它没有被恶意行为者篡改。

JWT 的签名是三件事的结合:

*   JWT 的头球
*   JWT 的有效载荷
*   秘密价值

这三个数字签名(*未加密*)使用 JWT 报头中指定的算法。如果我们对上面的例子进行解码，我们将得到下面的 JSON 字符串:

**JWT 的头球**

```
{
    "alg": "HS256",
    "typ": "JWT"
} 
```

**JWT 的数据**

```
{
    "iat": 1416929109,
    "jti": "aa7f8d0a95c",
    "scopes": [
        "repo",
        "public_repo"
    ]
} 
```

亲自尝试一下 [jwt.io](http://jwt.io/) ，在这里您可以尝试编码和解码自己的 jwt。

## 让我们在基于 PHP 的应用程序中使用 JWTs

既然您已经了解了 jwt 是什么，现在是时候学习如何在 PHP 应用程序中使用它们了。在我们开始之前，请随意[克隆这篇文章](https://github.com/sitepoint-editors/basic-php-jwt-auth-example)的代码，或者按照我们的思路创建它。

有许多方法可以集成 jwt，但是这里是我们要做的。

除了登录和注销页面之外，对应用程序的所有请求都需要通过 JWT 进行身份验证。如果用户在没有 JWT 的情况下发出请求，他们将被重定向到登录页面。

在用户填写并提交登录表单后，表单将通过 JavaScript 提交给我们应用程序中的登录端点`authenticate.php`。然后，端点将从请求中提取凭证(用户名和密码),并检查它们是否有效。

如果是，它将生成一个 JWT 并发送给客户端。当客户端收到一个 JWT 时，它将存储它，并在以后对应用程序的每个请求中使用它。

对于一个简单的场景，用户只能请求一个资源——一个名为`resource.php`的 PHP 文件。它不会做太多，只是返回一个字符串，包含请求时的当前时间戳。

在发出请求时，有几种使用 jwt 的方法。在我们的应用中，JWT 将在[载体授权报头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)中发送。

如果您不熟悉无记名授权，它是 HTTP 身份验证的一种形式，其中令牌(如 JWT)在请求头中发送。服务器可以检查令牌，并确定是否应该给予令牌的“持有者”访问权。

下面是标题的一个示例:

```
Authorization: Bearer ab0dde18155a43ee83edba4a4542b973 
```

对于我们的应用程序收到的每个请求，PHP 将尝试从承载报头中提取令牌。如果存在，则进行验证。如果有效，用户将会看到该请求的正常响应。但是，如果 JWT 无效，则不允许用户访问资源。

请注意，JWT 是*而不是*设计来替代会话 cookies 的。

### 先决条件

首先，我们需要在系统上安装 [PHP](https://www.php.net/manual/en/install.php) 和 [Composer](https://getcomposer.org/download/) 。

在项目的根目录下，运行`composer install`。这将引入第三方库 [Firebase PHP-JWT](https://github.com/firebase/php-jwt) ，它简化了 JWTs 的工作，以及 [laminas-config](https://github.com/laminas/laminas-config/) ，旨在简化应用程序中配置数据的访问

### 登录表单

![Sample login form using HTML and JavaScript](img/6a3740210b93046fbd88a9517317370f.png)

安装好库之后，让我们一步一步地查看`authenticate.php`中的登录代码。我们首先进行常规设置，确保 Composer 生成的自动加载程序可用。

```
<?php

declare(strict_types=1);

use Firebase\JWT\JWT;

require_once('../vendor/autoload.php'); 
```

在收到表单提交后，根据数据库或其他数据存储对凭证进行验证。出于这个例子的目的，我们将假设它们是有效的，并将`$hasValidCredentials`设置为 true。

```
<?php

// extract credentials from the request

if ($hasValidCredentials) { 
```

接下来，我们初始化一组用于生成 JWT 的变量。请记住，由于可以在客户端检查 JWT，*不要在其中包含任何敏感信息。*

另一件值得指出的事情是，`$secretKey`不会像这样初始化。您可能会在环境中设置并提取它，使用库如 [phpdotenv](https://github.com/vlucas/phpdotenv) ，或者在配置文件中。在这个例子中，我避免这样做，因为我想把重点放在 JWT 代码上。

千万不要公开或者在版本控制下存储！

```
$secretKey  = 'bGS6lzFqvvSQ8ALbOxatm7/Vk7mLQyzqaS34Q4oR1ew=';
$issuedAt   = new DateTimeImmutable();
$expire     = $issuedAt->modify('+6 minutes')->getTimestamp();      // Add 60 seconds
$serverName = "your.domain.name";
$username   = "username";                                           // Retrieved from filtered POST data

$data = [
    'iat'  => $issuedAt->getTimestamp(),         // Issued at: time when the token was generated
    'iss'  => $serverName,                       // Issuer
    'nbf'  => $issuedAt->getTimestamp(),         // Not before
    'exp'  => $expire,                           // Expire
    'userName' => $username,                     // User name
]; 
```

有效载荷数据准备就绪后，我们接下来使用 php-jwt 的静态`encode`方法来创建 jwt。

该方法:

*   将数组转换为 JSON
*   制作标题
*   签署有效负载
*   对最终字符串进行编码

它需要三个参数:

*   有效载荷信息
*   秘密钥匙
*   用于对令牌签名的算法

通过对函数的结果调用`echo`,返回生成的令牌:

```
<?php
    // Encode the array to a JWT string.
    echo JWT::encode(
        $data,
        $secretKey,
        'HS512'
    );
} 
```

### 消费 JWT

![Retrieving a resource using JavaScript and JWTs](img/501a9825dc6952868efecd4398dc76d4.png)

现在客户机有了令牌，您可以使用 JavaScript 或您喜欢的任何机制来存储它。这里有一个使用普通 JavaScript 的例子。在`index.html`中，表单提交成功后，返回的 JWT 被存储在内存中，登录表单被隐藏，请求时间戳的按钮被显示:

```
const store = {};
const loginButton = document.querySelector('#frmLogin');
const btnGetResource = document.querySelector('#btnGetResource');
const form = document.forms[0];

// Inserts the jwt to the store object
store.setJWT = function (data) {
  this.JWT = data;
};

loginButton.addEventListener('submit', async (e) => {
  e.preventDefault();

  const res = await fetch('/authenticate.php', {
    method: 'POST',
    headers: {
      'Content-type': 'application/x-www-form-urlencoded; charset=UTF-8'
    },
    body: JSON.stringify({
      username: form.inputEmail.value,
      password: form.inputPassword.value
    })
  });

  if (res.status >= 200 && res.status <= 299) {
    const jwt = await res.text();
    store.setJWT(jwt);
    frmLogin.style.display = 'none';
    btnGetResource.style.display = 'block';
  } else {
    // Handle errors
    console.log(res.status, res.statusText);
  }
}); 
```

### 使用 JWT

当点击“获取当前时间戳”按钮时，向`resource.php`发出一个获取请求，该请求在授权头中设置认证后接收的 JWT。

```
btnGetResource.addEventListener('click', async (e) => {
  const res = await fetch('/resource.php', {
    headers: {
      'Authorization': `Bearer ${store.JWT}`
    }
  });
  const timeStamp = await res.text();
  console.log(timeStamp);
}); 
```

当我们单击该按钮时，会发出类似如下的请求:

```
GET /resource.php HTTP/1.1
Host: yourhost.com
Connection: keep-alive
Accept: */*
X-Requested-With: XMLHttpRequest
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE0MjU1ODg4MjEsImp0aSI6IjU0ZjhjMjU1NWQyMjMiLCJpc3MiOiJzcC1qd3Qtc2ltcGxlLXRlY25vbTFrMy5jOS5pbyIsIm5iZiI6MTQyNTU4ODgyMSwiZXhwIjoxNDI1NTkyNDIxLCJkYXRhIjp7InVzZXJJZCI6IjEiLCJ1c2VyTmFtZSI6ImFkbWluIn19.HVYBe9xvPD8qt0wh7rXI8bmRJsQavJ8Qs29yfVbY-A0 
```

假设 JWT 是有效的，我们将看到资源，之后响应被写入控制台。

### 验证 JWT

最后，让我们看看如何在 PHP 中验证令牌。一如既往，我们将包括作曲家的自动加载器。然后，我们可以选择检查是否使用了正确的请求方法。为此，我跳过了代码，继续关注特定于 JWT 的代码:

```
<?php
chdir(dirname(__DIR__));

require_once('../vendor/autoload.php');

// Do some checking for the request method here, if desired. 
```

然后，代码将尝试从承载报头中提取令牌。我已经用 [preg_match](https://www.php.net/manual/en/function.preg-match.phphttps://www.php.net/manual/en/function.preg-match.php) 做到了这一点。如果您不熟悉这个函数，它会对字符串执行正则表达式匹配

我在这里使用的正则表达式将尝试从承载报头中提取令牌，并转储其他所有内容。如果没有找到，则返回 HTTP 400 错误请求:

```
if (! preg_match('/Bearer\s(\S+)/', $_SERVER['HTTP_AUTHORIZATION'], $matches)) {
    header('HTTP/1.0 400 Bad Request');
    echo 'Token not found in request';
    exit;
} 
```

注意，默认情况下，Apache **不会将`HTTP_AUTHORIZATION`头传递给 PHP**。[这背后的原因是](https://www.codepunker.com/blog/php-a-primer-on-the-basic-authorization-header):

> 基本授权头只有在您的连接是通过 HTTPS 完成的情况下才是安全的，因为否则凭证是以编码的纯文本(未加密)通过网络发送的，这是一个巨大的安全问题。

我完全理解这个决定的逻辑。但是，为了避免大量的混淆，请将以下内容添加到 Apache 配置中。那么代码将按预期运行。如果您使用的是 NGINX，代码应该按照预期运行:

```
RewriteEngine On
RewriteCond %{HTTP:Authorization} ^(.+)$
RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}] 
```

接下来，我们试图提取匹配的 JWT，它将在变量`$matches`的第二个元素中。如果不可用，则没有提取 JWT，并返回一个 HTTP 400 错误请求:

```
$jwt = $matches[1];
if (! $jwt) {
    // No token was able to be extracted from the authorization header
    header('HTTP/1.0 400 Bad Request');
    exit;
} 
```

如果我们到达这一点，JWT *被*提取，所以我们移动到解码和验证阶段。为此，我们再次需要我们的秘密密钥，它将从环境或应用程序的配置中提取。然后我们使用 php-jwt 的静态`decode`方法，向它传递 jwt、密钥和一组用于解码 JWT 的算法。

如果它能被成功解码，我们就会尝试验证它。我这里的例子非常简单，因为它只使用了发行者，而没有使用之前和到期时间戳。在实际应用中，您可能还会使用许多其他声明。

```
$secretKey  = 'bGS6lzFqvvSQ8ALbOxatm7/Vk7mLQyzqaS34Q4oR1ew=';
$token = JWT::decode($jwt, $secretKey, ['HS512']);
$now = new DateTimeImmutable();
$serverName = "your.domain.name";

if ($token->iss !== $serverName ||
    $token->nbf > $now->getTimestamp() ||
    $token->exp < $now->getTimestamp())
{
    header('HTTP/1.1 401 Unauthorized');
    exit;
} 
```

如果令牌无效，例如，令牌已经过期，用户将被发送一个 HTTP 401 未授权的头，脚本将退出。

如果解码 JWT 的过程失败，可能是:

*   如前所述，提供的片段数量与标准的三个不匹配。
*   标头或有效负载不是有效的 JSON 字符串
*   签名无效，这意味着数据被篡改！
*   当当前时间戳小于时间戳时，`nbf`声明在 JWT 中设置有时间戳。
*   当当前时间戳小于时间戳时，`iat`声明在 JWT 中设置有时间戳。
*   `exp`声明在 JWT 中设置了一个时间戳，当当前时间戳大于该时间戳时。

如您所见，JWT 有一套很好的控件将它标记为无效，而不需要手动撤销它或对照有效令牌列表检查它。

如果解码和验证过程成功，用户将被允许发出请求，并将收到适当的响应。

## 最后

这是对 JSON Web 令牌(jwt)以及如何在基于 PHP 的应用程序中使用它们的快速介绍。从现在开始，您可以尝试在您的下一个 API 中实现 jwt，也许可以尝试使用 RS256 之类的非对称密钥的其他签名算法，或者将其集成到现有的 OAUTH2 认证服务器中作为 API 密钥。

如果您有任何意见或问题，请随时联系 [Twitter](https://twitter.com/settermjd) 。

## 分享这篇文章