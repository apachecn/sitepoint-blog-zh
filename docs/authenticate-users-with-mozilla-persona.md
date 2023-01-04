# 使用 Mozilla Persona 验证用户

> 原文：<https://www.sitepoint.com/authenticate-users-with-mozilla-persona/>

在本文中，我们将了解如何使用新的、面向未来的分布式认证系统 [Mozilla Persona](http://www.mozilla.org/en-US/persona/ "Mozilla Persona – and identity system for the web – mozilla.org") 。顾名思义，Persona 是由 Mozilla 基金会创建和赞助的，它是 OpenID 和 OAuth 的一个更简单、更安全的替代方案。

## Mozilla Persona 背后的理论

Mozilla Persona 建立在同样由 Mozilla 开发的 BrowserID 技术之上。你可以在 Lloyd Hilaiel 的文章[中找到对 BrowserID 如何工作](http://lloyd.io/how-browserid-works "How BrowserID Works")中概念的扩展解释，但是我将在这里浓缩关键点。

*   **电子邮件地址是一种身份**,由电子邮件提供商(如 Gmail、Yahoo 等)验证。)谁充当**主要身份权威**(或者简称为*主要*)的角色。
*   **认证在浏览器中进行。**没有与第三方网站的交易，因此它是高效的和隐私保护的。浏览器有**实现提供者** ( *IP* )的作用。

在理想的(未来的)工作流中，浏览器本地实现了`navigator.id` API，电子邮件提供商能够充当其用户的主要权威。今天，我们可以通过使用 Mozilla 提供的 HTML5 实现和 browserid.org 服务器作为第二身份认证机构来验证电子邮件地址，从而实现一个功能性的工作流。

假设马克想使用电子邮件地址 mr.nice.guy@gmail.com 登录 myfavoritebeer.org 网站。需要遵循三个步骤:

**1。证书提供**
如果是第一次使用电子邮件地址作为身份，浏览器会将 Mark 发送到 GMail 的安全认证页面。然后浏览器生成一个密钥对(使用`navigator.id.genKeyPair()`)并将公钥发送到 GMail。

GMail 会发回一份证书，一个包含电子邮件地址、用户公钥和有效期的签名包。它用 Gmail 的私钥签名，并打包成一个 JSON Web 令牌(JWT)，一个[签名的 JSON 对象](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html "JSON Web Token (JWT)")。

证书与私钥(使用`navigator.id.registerVerifiedEmail()`)一起存储在浏览器钥匙串中，并在下一步中使用。

如果电子邮件提供商本身不支持 BrowserID，则电子邮件将由 BrowserID 服务器作为二级授权机构进行验证。基本上，您会收到一封带有访问链接的电子邮件，以便确认您的身份。

**2。断言生成**
浏览器生成一个*断言*，一个证明马克拥有 mr.nice.guy@gmail.com 的 JWT 包。该包包含目标站点(myfavoritebeer.org)、截止日期和 GMail 颁发的证书。这些都是用马克的私人钥匙签名的，并被送到 myfavoritebeer.org 进行验证。

如果浏览器本身不支持 BrowserID，则操作由 Mozilla 提供的回退 JavaScript 代码执行。

**3。断言验证**
站点 myfavoritebeer.org 接收并验证马克的请求。首先，它检查有效期，然后检索 GMail 的公钥来验证证书。证书包含用于验证声明的马克公钥。

此步骤假设 GMail 支持 BrowserID 并共享其公钥。目前，证书包含一个 issued-by 属性，该属性指向生成它的二级机构。

如果一切顺利，Mark 会登录到站点，直到断言过期。

## 向您的站点添加角色身份验证

以下示例使用并扩展了官方 Mozilla Persona 快速设置文章中的信息和代码，以获得一个基本但功能完整的系统。按钮使用由 Sawyer Hollenshead 为 Mozilla 创建的 CSS 样式。该示例的代码可以从 GitHub 下载。

首先这里有一个基本的`index.php`文件:

```
<?php
// Check if the user is logged in
$user = null;
if (!empty($_COOKIE['auth'])) {
    $user = $_COOKIE['auth'];
}
?>
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8">
  <title>My Favorite Beer - Mozilla Persona Test Code</title>
  <link rel="stylesheet" href="css/persona/persona-buttons.css">
 </head>
 <body>
  <h1>My Favorite Beer: a test page for Mozilla Persona</h1>
<?php
if (!empty($user)) {
?>
  <p>Hello <strong><?=$user?></strong>!</p>
  <a href="#" class="persona-button persona-signout"><span>Sign out</span></a>
<?php
}
else {
?>
  <a href="#" class="persona-button persona-signin"><span>Sign in with Persona</span></a>
<?php
}
?>
  <!--jQuery Library from Google-->
  <script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>

  <!--Persona Library from Mozilla-->
  <script src="https://login.persona.org/include.js"></script>

  <!-- Our custom code -->
  <script src="js/mfb.js"></script>
 </body>
</html>
```

我将认证信息(仅电子邮件地址)存储在一个名为`auth`的 cookie 变量中。前几行 PHP 代码检查这个 cookie，并相应地设置一个本地$user 变量。

在主体中检查`$user`变量:如果用户登录，显示一条欢迎消息，然后是“退出”按钮。否则，默认显示“登录”按钮。

在页面底部，您可以找到 jQuery 库、Mozilla Persona fallback 库和我们的自定义 JavaScript 代码文件`mfb.js`。在这个文件中，我设置了将后端链接到 Persona API 的自定义挂钩。

```
(function(window, $, undefined) {
    // See: http://www.quirksmode.org/js/cookies.html
    window.readCookie = function(name) {
        var nameEQ = name + '=';
        var ca = document.cookie.split(';');
        for(var i = 0; i < ca.length; i++) {
            var c = ca[i];
            while (c.charAt(0) == ' ') {
                c = c.substring(1, c.length);
            }
            if (c.indexOf(nameEQ) == 0) {
                return c.substring(nameEQ.length, c.length);
            }
        }
        return null;
    };

    // Read auth info (the email address) from cookie
    var currentUser = window.readCookie('auth');

    // If the user is not logged in set the default to null
    if (!currentUser) {
        currentUser = null;
    }

    // The returned value must be URL-decoded
    if (currentUser != null) {
        currentUser = decodeURIComponent(currentUser);
    }

    navigator.id.watch({
        loggedInUser: currentUser,
        onlogin: function(assertion) {
            // A user has logged in! Here you need to send the
            // assertion to your backend for verification and to
            // create a session and then update your UI.
            $.ajax({
                type: 'POST',
                url: 'login.php', // This is a URL on your website.
                data: {assertion: assertion},
                success: function(res, status, xhr) {
                    window.location.reload();
                },
                error: function(xhr, status, err) {
                    alert('Login failure: ' + err);
                }
            });
        },
        onlogout: function() {
            // A user has logged out! Here you need to tear down the
            // user's session by redirecting the user or making a call
            // to your backend. Also, make sure loggedInUser will get
            // set to null on the next page load.
            $.ajax({
                type: 'POST',
                url: 'logout.php', // This is a URL on your website.
                success: function(res, status, xhr) {
                    window.location.reload();
                },
                error: function(xhr, status, err) {
                    alert('Logout failure: ' + err);
                }
            });
        }
    });

    $(document).ready(function(){

        $('a.persona-signin').click(function(e) {
            navigator.id.request();
            e.preventDefault();
        });
        $('a.persona-signout').click(function(e) {
            navigator.id.logout();
            e.preventDefault();
        });
    });
})(window, jQuery);
```

首先，有一个快速的实用功能读取 cookies。然后它试图从 cookie 中读取`currentUser`变量。它是 URL 编码的，所以我们需要内置的`decodeURIComponent()`函数来获取未编码的电子邮件地址。重要的是，该变量包含用户的有效电子邮件地址或 JavaScript 空值；任何其他值都将导致无限重载循环。

接下来，我们通过调用`navigator.id.watch()` API 并传递 PHP 后端的细节来“观察”所需的`onlogin`和`onlogout`动作。这个 API 通过 Ajax POST 调用`login.php`和`logout.php`脚本。

注销脚本非常简单:它取消设置`auth` cookie 并退出。在真实的应用程序中，包含一个 CSRF 保护令牌并执行其他安全检查是一种很好的做法。

登录脚本负责向电子邮件提供商验证用户的身份。在真正的应用程序中，我们还会在应用程序的数据库中检查用户的身份。

主要流程是:

```
<?php
// Call the BrowserID API
$response = PersonaVerify();

// If the authentication is successful set the auth cookie
$result = json_decode($response, true);
if ('okay' == $result['status']) {
    $email = $result['email'];
    setcookie('auth', $email);
}

// Print the response to the Ajax script
echo $response;
```

首先，它调用一个封装了所有业务逻辑的实用函数(我们很快就会看到)。如果身份检查是肯定的，则设置身份验证 cookie，并在此调用其他后身份验证代码；在任何情况下，原始 JSON 响应都被返回给调用脚本。

函数`PersonaVerify()`负责断言和证书验证。重要的是，这一步在服务器端执行，以限制恶意代码注入的可能性。通常，在此功能中，我们应该从身份认证机构检索公钥，并自己进行验证，但由于规范仍在制定中，并且 BrowserID 协议尚未由电子邮件提供商实施，因此安全的方法是使用 Persona verification service 执行远程验证。

```
<?php
function PersonaVerify() {
    $url = 'https://verifier.login.persona.org/verify';

    $assert = filter_input(
        INPUT_POST,
        'assertion',
        FILTER_UNSAFE_RAW,
        FILTER_FLAG_STRIP_LOW|FILTER_FLAG_STRIP_HIGH
    );

    $scheme = 'http';
    if (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] != "on") {
        $scheme = 'https';
    }
    $audience = sprintf(
        '%s://%s:%s',
        $scheme,
        $_SERVER['HTTP_HOST'],
        $_SERVER['SERVER_PORT']
    );

    $params = 'assertion=' . urlencode($assert) . '&audience='
        . urlencode($audience);

    $ch = curl_init();
    $options = array(
        CURLOPT_URL => $url,
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_POST => 2,
        CURLOPT_POSTFIELDS => $params
    );

    curl_setopt_array($ch, $options);
    $result = curl_exec($ch);
    curl_close($ch);
    return $result;
}
```

这个函数的代码来自 Mozilla Persona developers 区域。

`assertion`参数是从 Ajax 提交的变量中获取的，并经过了清理。`audience`参数本质上是我们的站点 URL，格式为 scheme://host:port，所以它是使用$_SERVER superglobal 计算的。

这两个参数都被编码，并使用 cURL HTTP POST 调用发送给角色验证服务。结果是一个 JSON 字符串，它被返回给调用脚本进行解析。

## 摘要

只需几个步骤，我们就可以轻松地为我们的应用集成一个新的认证系统。多亏了后备代码，我们现在就可以开始使用它，并为浏览器和电子邮件提供商支持它做好准备。

如果你想更深入地了解这个主题，你可以阅读 Persona 官方文档和这篇文章，这篇文章强调了 Persona 和 OpenID 之间的区别。

目前就这些了，编码快乐！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章