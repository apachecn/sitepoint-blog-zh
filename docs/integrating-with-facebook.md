# 与脸书图形 API 集成

> 原文：<https://www.sitepoint.com/integrating-with-facebook/>

借助脸书的 PHP SDK 和一些 HTTP 库，如 Zend_Http_Client 或 PEAR HTTP_Request2，从 PHP 集成脸书很容易。在本文中，我将向您展示如何开始使用脸书 PHP SDK。您将了解脸书图形 API 并创建一个能够更新您的状态消息和上传照片的脸书应用程序。

如果你还没有，你可以从 GitHub 克隆或者下载 [PHP SDK。你还需要一个经过验证的脸书账户。](https://github.com/facebook/facebook-php-sdk)

## 在脸书上注册你的应用

你首先需要在脸书上注册你的应用程序。前往[developers.facebook.com/apps](https://developers.facebook.com/apps/)，点击页面顶部的*创建新应用*按钮。

打开的对话框要求您输入应用程序的名称和命名空间。*应用程序显示名称*是将显示给用户的应用程序的名称。*应用命名空间*是您的应用程序将用于打开图形和画布页面的命名空间。

![](img/f44a9f15cebce401b78742600ea67a12.png "integrate-fb-01")

注册应用程序后，您将进入基本设置屏幕，在该屏幕上，您需要指定应用程序将如何与脸书集成:

*   **网站**–网站选项用于为您的网站添加社交功能。
*   **脸书上的应用**–这个脸书应用选项将你的应用嵌入脸书画布页面。代码托管在您的服务器上，但在脸书页面的上下文中执行，类似于 IFrame。
*   **移动网络**–移动网络选项类似于网站集成选项，尽管它是为移动网站设计的。
*   **原生 iOS/Android 应用**–原生选项允许您将脸书数据集成到您的 iOS 和 Android 应用中。
*   **页面选项卡**–选项卡选项将您的应用程序显示为脸书页面选项卡。

出于本文的目的，我将使用网站集成选项。我的应用程序将是一个独立的网站，授权后，脸书将用户重定向到一个指定的网址。选择选项旁边的复选标记，并输入应用程序入口页面的 URL。然后确保点击页面底部的*保存更改*按钮。

您还应该在页面顶部记下*应用 ID* 和*应用秘密*的值，因为您将需要这些值来将您的应用连接到脸书。

![](img/9cd0db5cfc30243516073bf2074c91e4.png "integrate-fb-02")

## 使用 SDK

与脸书连接和交互的功能通过 PHP SDK 定义的`Facebook`对象公开。构造函数接受一个包含应用程序信息的参数数组，比如出现在应用程序基本设置页面上的*应用程序 ID* 和*应用程序秘密*。

```
<?php 
session_start();
require_once "php-sdk/src/facebook.php"; 

$config = array(
    "appId" => FACEBOOK_APP_ID,
    "secret" => FACEBOOK_APP_SECRET);

$fb = new Facebook($config);
```

### 批准

`getUser()`方法用于检索脸书用户的用户 ID。该信息可能可用，也可能不可用，这取决于用户是否登录。如果方法返回 0，那么您知道用户还没有登录。

```
<?php
$user = $fb->getUser();
```

使用`getLoginUrl()`方法获得登录链接，该链接是 OAuth 认证过程与脸书的起点。`getLoginUrl()`接受一个参数数组，其中我已经提供了 *redirect_uri* 和 *scope* 。

```
<?php
$params = array(
    "redirect_uri" => REDIRECT_URI,
    "scope" => "email,read_stream,publish_stream,user_photos,user_videos");
    echo '<a href="' . $fb->getLoginUrl($params) . '">Login</a>';
```

*redirect_url* 应该与您在注册应用程序时为站点 url 提供的地址相同。范围是应用程序所需的请求权限的逗号分隔列表。当用户登录时，应用程序可以访问公共配置文件信息和脸书允许的其他默认设置，但是如果您想要访问其他功能(如发布状态消息)，您必须获得用户的授权。脸书开发者文档有一个可用权限列表[。在这里，我请求获得访问用户电子邮件地址、阅读和发布状态更新、发布照片和视频的权限。](http://developers.facebook.com/docs/reference/api/permissions/)

无论用户是接受请求并登录到脸书，还是拒绝请求，他都将被重定向回 *redirect_uri* ，并且有几个值可以作为 URL 参数使用。拒绝将包括`error`、`error_reason`和`error_description`参数:

```
http://example.com/facebook/myapp.php?error=access_denied&error_reason=user_denied&error_description=The+user+denied+your+request.
```

成功的认证/授权将附加一个`code`参数，如下所示:

```
http://example.com/facebook/myapp.php?code=TOKEN_VALUE
```

然后，该代码用于请求访问令牌:

```
https://graph.facebook.com/oauth/access_token?client_id=FACEBOOK_APP_ID&redirect_uri=FACEBOOK_REDIRECT_URI&client_secret=FACEBOOK_APP_SECRET&code=TOKEN_VALUE
```

因为您使用的是 SDK，它可以为您处理所有这些，所以我不会深入探讨 OAuth 是如何工作的。如果你有兴趣了解更多，请阅读 Dustin Runnell 的 [Understanding OAuth](https://www.sitepoint.com/understanding-oauth-1/) 文章和 SDK 的[认证文档](http://developers.facebook.com/docs/authentication/)。(脸书使用 OAuth v2，Dustin 的文章涵盖了 v1，但它仍然会让您很好地了解请求和凭证在这个过程中扮演的角色)。

### 图形 API

一旦用户授予权限，您就可以通过`GET`请求阅读用户的状态消息提要:

```
https://graph.facebook.com/me/feed?access_token=ACESS_TOKEN
```

或者，您可以使用`api()`方法来包装对脸书图形 API 方法的调用:

```
<?php
$data = $fb->api("/me/feed");
```

本例中的`api()`方法可以接受三个参数:请求的图形 API 路径、请求的 HTTP 方法(默认为`GET`)、特定于图形 API 方法的参数数组。

Graph API 提供了一个接口来访问脸书社交图中的成员和关系。每个成员都有一个唯一的 ID，可以通过以“https://graph.facebook.com”开头的资源以类似 REST 的方式访问。例如，用您的浏览器发送一个`GET`请求:

```
https://graph.facebook.com/harikt
```

将返回一个 JSON 对象，包含关于我和我的个人资料的基本公共信息。

```
{
   "id": "596223095",
   "name": "Hari Kt",
   "first_name": "Hari",
   "last_name": "Kt",
   "link": "http://www.facebook.com/harikt",
   "username": "harikt",
   "gender": "male",
   "locale": "en_US"
}
```

一些请求需要访问令牌。请求消息更新提要是一个特权操作，因此发送一个`GET`请求是为了:

```
https://graph.facebook.com/harikt/feed
```

将返回一个 JSON 对象，其中填充了关于一个`OAuthException`错误的信息。

```
{
   "error": {
      "message": "An access token is required to request this resource.",
      "type": "OAuthException"
   }
}
```

ID `me`是一个方便的简写，指的是当前用户。

要使用`api()`方法向用户的提要添加更新，您需要向`/me/feed`发出一个`POST`请求，并提供一个*消息*值。

```
<?php
$data = array("message" => "Hello World!");
$status = $fb->api("/me/feed", "POST", $data);
```

要上传一张新照片，你可以向`/me/photos`(或`ALBUM_ID/photos`发出一个`POST`请求，上传到一个特定的相册)并提供一个带有*名称*和*图像*参数的数组。

```
<?php
$fb->setFileUploadSupport(true); 
$data = array(
    "name" => "a vacation photo",
    "image" => "@/home/hari/vacation/img42.jpg");
$status = $fb->api("/me/photos", "POST", $data);
```

SDK 使用 PHP 的 cURL 扩展来发布数据，用`true`调用`setFileUploadSupport()`将把数据值作为数组提供给`CURLOPT_POSTFIELDS`，这又导致 cURL 将数据编码为“多部分/形式数据”。与 cURL 相关的还有在要发布的图像的完整路径前使用`@`。更多信息参见 PHP 的`curl_setopt()` 的[文档中对`CURLOPT_POSTFIELDS`的描述。](http://www.php.net/curl_setopt)

要了解更多关于脸书图形 API 的信息，我推荐你阅读[图形 API 文档](http://developers.facebook.com/docs/reference/api/)并尝试使用[图形 API 浏览器](https://developers.facebook.com/tools/explorer)，这是一个非常方便的工具。

## 你的第一份申请

现在，让我们将您所学的一切汇集在一起，编写一个非常基本的脸书应用程序示例。它将提示用户登录并授权该应用程序，然后使他能够更新他的状态消息并上传照片。

```
<?php 
session_start();
require_once "php-sdk/src/facebook.php"; 

$config = array(
    "appId" => FACEBOOK_APP_ID,
    "secret" => FACEBOOK_APP_SECRET);

$fb = new Facebook($config);

$user = $fb->getUser();
?>
<html> 
 <head> 
  <title>Hello Facebook</title> 
 </head> 
 <body>
<?php
if (!$user) { 
    $params = array(
        "scope" => "read_stream,publish_stream,user_photos",
        "redirect_uri" => REDIRECT_URI);
    echo '<a href="' . $fb->getLoginUrl($params) . '">Login</a>'; 
}
else { 
?>
  <form action="<?php echo $_SERVER["PHP_SELF"];?>" method="post" enctype="multipart/form-data">
   <textarea name="message" id="message" rows="2" cols="40"></textarea><br>
   <input type="file" name="image" id="image"><br>
   <input type="submit" value="Update"> 
  </form> 
<?php
    // process form submission
    if ($_SERVER["REQUEST_METHOD"] == "POST" && !empty($_POST["message"])) {
        if (is_uploaded_file($_FILES["image"]["tmp_name"])) { 
            $finfo = finfo_open(FILEINFO_MIME_TYPE);
            $mime = finfo_file($finfo, $_FILES["image"]["tmp_name"]);
            $allowed = array("image/gif", "image/jpg", "image/jpeg", "image/png");
            // upload image
            if (in_array($mime, $allowed)) { 
                $data = array(
                    "name" => $_POST["message"],
                    "image" => "@" . realpath($_FILES["image"]["tmp_name"]));
                $fb->setFileUploadSupport(true); 
                $status = $fb->api("/me/photos", "POST", $data);    
            }
        }
        else {
            // update status message
            $data = array("message" => $_POST["message"]);
            $status = $fb->api("/me/feed", "POST", $data); 
        }
    } 
    if (isset($status)) {
        echo "<pre>" . print_r($status, true) . "</pre>";
    } 
}
?>
 </body> 
</html>
```

该代码根据`getUser()`的返回值显示一个适当的登录或注销链接。然后，显示一个简单的 HTML 表单，允许用户输入状态消息和可能的图像文件。当用户提交表单时，代码会验证上传的图像(如果提供的话)并将其发送到脸书，或者只执行状态消息更新。

## 摘要

这里的代码用于演示目的，我已经省略了许多在编写实际应用程序时需要执行的过滤和安全相关的检查。然而，它突出了本文中提出的要点。脸书 PHP SDK 使得与脸书的集成变得容易。它抽象了 OAuth 认证和脸书图形 API 的工作。

图片 via [姆卡巴科夫](http://www.shutterstock.com/gallery-810352p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章