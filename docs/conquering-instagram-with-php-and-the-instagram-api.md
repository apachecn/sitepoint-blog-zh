# 用 PHP 和 Instagram API 征服 Instagram

> 原文：<https://www.sitepoint.com/conquering-instagram-with-php-and-the-instagram-api/>

Instagram 的 API 允许我们与用户信息、媒体(照片和视频)、喜欢、评论和标签等数据进行交互。例如，您可以搜索特定位置周围的媒体，并按时间过滤结果。API 还允许我们发布评论或喜欢特定的媒体。目前，仅上传媒体不被支持。如果你想了解完整的功能，你可以随时查看 [API 端点文档](https://instagram.com/developer/endpoints/)。

![](img/229f2ace71a82f3d2ea963a43632f440.png)

## API 调用和限制

使用 Instagram API 可以执行两种类型的 API 调用:未经身份验证的和经过身份验证的。未经认证的 API 调用只需要客户端 ID，经过认证的 API 调用使用 OAuth，具体是 OAuth 2.0。如果你不知道 OAuth 是什么，可以看看这篇关于数字海洋的[OAuth 2 介绍文章。](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2)

在我们继续之前，理解这个 API 的局限性是很重要的。在撰写本文时，每个令牌只能执行 5000 个经过身份验证的调用，对 API 只能执行 5000 个未经身份验证的调用。这与您使用的端点无关，尽管有特定的端点，每个端点都有自己的限制。如果您想了解更多信息，可以查看[限制页面](https://instagram.com/developer/limits/)中的特定端点速率限制部分。

## 注册应用程序

不用说，为了使用 Instagram API，您需要有自己的 Instagram 帐户。然后，报名成为[开发者](https://instagram.com/developer/register/)。

![instagram developer signup](img/8361d7eb97c809b5a66052d739b9b468.png)

接下来我们必须注册一个应用程序。我们可以进入 Instagram 开发者页面，点击“注册你的应用”按钮。如果您以前从未创建过应用程序，您将被重定向到应用程序注册页面，如下所示:

![register new application](img/a226b493211ca45909c81dfe590fac99.png)

如果您之前已经创建了一个应用程序，那么它会将您带到列出您所有现有应用程序的[应用程序管理页面](https://instagram.com/developer/clients/manage/)。在那里，你所要做的就是点击“注册一个新客户”按钮，这将把你带到与上面相同的页面。

进入应用程序注册页面后，填写所有字段。网站 URL 是应用程序网站的 URL。重定向 URI 是授予用户对您的应用程序的访问权限后，用户将被重定向到的 URL。这必须是一个 HTTPS 网址。如果你没有 HTTPS 服务器，你可以使用 Apache 和 [Ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 。[下载适用于您的操作系统的 ngrok 版本](https://ngrok.com/download)，将其解压缩，然后在您的首选安装目录中执行以下命令。将`80`替换为运行服务器的端口:

```
ngrok http 80
```

这将为运行在本地主机上的 Apache 服务器分配一个 HTTP 和 HTTPS URL。然后，您可以使用 HTTPS 网址进行测试。将此 URL 用作网站 URL，并重定向应用程序注册页面中的 URI 字段。

完成后，只需点击“注册”按钮即可完成注册。如果一切顺利，你会收到一个客户 ID 和客户密码。稍后我们将使用这些来执行对 API 的请求。

## API 控制台

您可以使用 [API 控制台](https://instagram.com/developer/api-console/)来处理您可以使用 Instagram API 发出的请求。要使用 API 控制台，请展开控制台左侧的 API 方法选择菜单。在那里，您可以选择一种您希望用于您的请求的方法。大多数方法都需要认证，因此您必须从认证下拉列表中选择 OAuth 2，然后使用您现有的 Instagram 帐户登录。请注意，您执行的任何请求都是代表您执行的。这意味着您所做的任何操作，如喜欢照片或关注用户，都将由您的帐户执行。

![API Console](img/c684e2e4fdd7111ef2d5f9d46074e2e9.png)

API 控制台是不言自明的。您可以选择想要使用的 HTTP 请求类型(GET、POST、DELETE、PUT ),输入提交请求的 URL，并输入所需的查询参数。单击“发送”按钮后，您可以看到实际的请求和响应。

## 用 PHP 进行 API 调用

现在我们已经准备好使用 PHP 与 API 进行交互了。我们可以用 PHP 的 HTTP 客户端 [Guzzle](https://www.sitepoint.com/guzzle-php-http-client/) 来实现。用 Composer 安装它:

```
composer require guzzlehttp/guzzle:~5.0
```

可选地，如果您想跟随演示项目，您可以安装 Slim PHP 框架。请注意，我们使用的是 Guzzle 的第 5 版，因为第 6 版是基于 PSR-7 的，因此缺少过去版本的许多实用功能。

```
composer require slim/slim
```

如果您想使用 Slim，您需要安装 Twig，以及 Slim 视图，这样您就可以在 Slim 中使用 Twig。

```
composer require twig/twig
composer require slim/views
```

完成后，创建一个新的 PHP 文件并添加以下代码:

```
<?php require  'vendor/autoload.php';  use  GuzzleHttp\Client; $client =  new  Client();
```

接下来，添加您的 Instagram 应用程序的客户端 ID、客户端密码和重定向 URL。

```
define("CLIENT_ID",  "YOUR CLIENT ID"); define("CLIENT_SECRET",  "YOUR CLIENT SECRET"); define("REDIRECT_URL",  "YOUR REDIRECT URL");
```

设置 Slim 来利用 Twig 处理视图。此外，启用错误报告并设置缓存视图的目录:

```
app =  new \Slim\Slim(array(  'view'  =>  new \Slim\Views\Twig()  //use twig for handling views  )); $view = $app->view(); $view->parserOptions = array(  'debug'  =>  true,  //enable error reporting in the view  'cache'  => dirname(__FILE__)  .  '/cache'  //set directory for caching views  );
```

### 获取访问令牌

为了获得访问令牌，我们首先需要构造登录 URL。登录 URL 指向要求用户授予应用程序权限的页面。基础登录网址为:`https://api.instagram.com/oauth/authorize`。然后我们需要传入`client_id`、`redirect_uri`、`scope`和`response_type`作为查询参数。

```
https://api.instagram.com/oauth/authorize?client_id={$client_id}&redirect_uri={$redirect_url}&scope=basic&response_type=code
```

你已经知道了什么是`client_id`和`redirect_url`，那么我们就来谈谈`scope`和`response_type`。

*   这是你指定你的应用程序能做什么的地方。目前可用的范围有`basic`、`comments`、`relationships`和`likes`。`basic`是默认提供的。这为您提供了对所有 API 端点的读取权限。然而，其他 3 个要求你的应用程序提交审查，因为它们允许你的应用程序喜欢、评论、关注或取消关注特定用户。

*   `response_type`–一旦用户授予应用程序权限，我们将获得的响应类型。在服务器端，这应该是`code`，在客户端，这应该是`token`。我们主要在服务器上工作，所以这应该是`code`。这意味着在授予权限后会返回一个授权码。

一旦用户授予应用程序的权限，他将被重定向到指定的重定向 URL。授权码作为查询参数与这个 URL 一起传递。接下来，我们需要向`/oauth/access_token`端点发出一个`POST`请求，另外传入客户端 ID、客户端机密、授权类型、重定向 URL 和代码。授权类型是用户授予您的应用程序权限后获取访问令牌的方式。在这种情况下，我们使用`authorization_code`。这是在重定向 URL 中作为查询参数传递的代码。一旦发出请求，我们通过调用响应上的`json`方法将响应从 JSON 转换成数组。最后，我们渲染视图。

```
$app->get('/login',  function  ()  use  ($app, $client)  { $data = array(); $login_url =  '';  if($app->request->get('code')){ $code = $app->request->get('code'); $response = $client->post('https://api.instagram.com/oauth/access_token', array('body'  => array(  'client_id'  => CLIENT_ID,  'client_secret'  => CLIENT_SECRET,  'grant_type'  =>  'authorization_code',  'redirect_uri'  => REDIRECT_URL,  'code'  => $code ))); $data = $response->json();  }else{ $login_url =  "https://api.instagram.com/oauth/authorize?client_id={$client_id}&redirect_uri={$redirect_url}&scope=basic&response_type=code";  } $app->render('home.php', array('data'  => $data,  'login_url'  => $login_url));  });
```

默认情况下，Slim 中的视图存储在`templates`目录中。下面是`home.php`视图的内容。

```
{%  if login_url %}  <a href="{{ login_url }}">login with instagram</a>  {%  else  %}  <div>  <img src="{{ data.user.profile_picture }}" alt="{{ data.user.username }}">  </div>  <ul>  <li>username:  {{ data.user.username }}</li>  <li>bio:  {{ data.user.bio }}</li>  <li>website:  {{ data.user.website }}</li>  <li>id:  {{ data.user.id }}</li>  <li>access token:  {{ data.access_token }}</li>  </ul>  {% endif %}
```

现在，您可以提取访问令牌并将其存储在安全的地方。Instagram 没有提到一个访问令牌会持续多长时间。所有的文档只说[它将在未来的某个时间](https://instagram.com/developer/authentication/)到期。因此，我们需要处理访问令牌过期的事件。您可以通过检查响应中的`meta`项下的`error_type`来做到这一点。如果值是`OAuthAccessTokenError`，那么这意味着您的访问令牌已经过期。如果`meta`项中的`code`的值不是 200，则只需检查该项。200 表示 OK，就像 HTTP 状态码一样。400 表示错误。

### 标签搜索

现在我们可以对 API 进行认证调用了。首先，让我们尝试通过标签搜索来搜索最近在尼亚加拉瀑布拍摄的照片。请记住，标签中没有空格，所以我们必须坚持使用 camelCase:

```
$app->get('/tags/search',  function()  use($app, $client, $access_token)  { $tag =  'niagaraFalls'; $response = $client->get("https://api.instagram.com/v1/tags/{$tag}/media/recent?access_token={$access_token}"); $results = $response->json(); $app->render('images.php', array('results'  => $results));  });
```

`images.php`视图只是遍历所有返回的结果，并提取低分辨率图像的 URL。然后我们使用它作为图像标签的来源。

```
{%  for row in results.data %}  <img src="{{ row.images.low_resolution.url }}">  {% endfor %}
```

默认情况下，Instagram 每次请求最多返回 20 张照片。但是，您可以将`count`指定为查询参数之一，以增加或限制返回的照片数量。

如果您不确定您使用的标签是否存在，您可以先执行标签搜索，然后使用第一个结果:

```
$app->get('/tags/search-with-tagvalidation',  function()  use($app, $client, $access_token)  { $query =  'Niagara Falls'; $response = $client->get("https://api.instagram.com/v1/tags/search?access_token={$access_token}&q={$query}"); $result = $response->json();  if(!empty($result['data'])){ $tag = $result['data'][0]['name']; $response = $client->get("https://api.instagram.com/v1/tags/{$tag}/media/recent?access_token={$access_token}"); $results = $response->json(); $app->render('images.php', array('results'  => $results));  }else{ echo 'no results';  }  });
```

### 用户订阅源

可以通过向`/users/self/feed`端点提交 GET 请求来访问用户提要:

```
$app->get('/user/feed',  function()  use($app, $client, $access_token)  { $response = $client->get("https://api.instagram.com/v1/users/self/feed?access_token={$access_token}"); $results = $response->json();  });
```

下面是一个用户反馈回复示例的屏幕截图:

![user feed](img/1bfcd7f9feaffc5b9c4436c403f44a5a.png)

### 搜索用户

让我们尝试搜索以“小智”为名字的用户:

```
$app->get('/user/search',  function()  use($app, $client, $access_token)  { $query =  'Ash Ketchum'; $response = $client->get("https://api.instagram.com/v1/users/search?q={$query}&access_token={$access_token}"); $results = $response->json();  });
```

上面的调用返回用户的`username`、`id`、`profile_picture`和`full_name`。虽然不是所有的结果都完全匹配。

这是我得到的回复截图:

![user search](img/6938e9c2ec417c14faf715a2a17c05fb.png)

### 在特定地点搜索照片

你也可以通过使用谷歌地理编码 API 来搜索在特定地方上传的照片或视频。我们使用 Google Geocoding API 将我们的查询转换为 Instagram API 需要的坐标(纬度和经度)。这里有一个例子:

```
$app->get('/geo/search',  function()  use($app, $client, $access_token)  { $query =  'banaue rice terraces';  //make a request to the Google Geocoding API $place_response = $client->get("http://maps.googleapis.com/maps/api/geocode/json?address={$query}&sensor=false"); $place_result = $place_response->json();  if($place_result['status']  ==  'OK'){  //extract the lat and lng values $lat = $place_result['results'][0]['geometry']['location']['lat']; $lng = $place_result['results'][0]['geometry']['location']['lng'];  //make a request to the Instagram API using the lat and lng $response = $client->get("https://api.instagram.com/v1/media/search?access_token={$access_token}&lat={$lat}&lng={$lng}"); $results = $response->json();  if(!empty($results['data'])){ $app->render('images.php', array('results'  => $results));  }else{ echo 'no photos found';  }  }else{ echo 'place not found';  }  });
```

注意，您还可以为这个端点指定`distance`、`min_timestamp`和`max_timestamp`，以进一步过滤结果。默认距离为 1 公里，您最多可以指定 5 公里。`min_timestamp`和`max_timestamp`是 unix 时间戳，用于将结果限制在特定时间段内拍摄的照片。您可以使用 [Carbon](https://github.com/briannesbitt/Carbon) 根据用户输入轻松生成时间戳，例如“昨天”、“5 天前”、“1 周前”。

### 页码

你可能已经注意到 Instagram API 已经通过分页让我们的生活变得简单了。如果一个特定 API 调用的结果有下一页，您可以使用`pagination`项下的`next_url`值作为下一个请求使用的 URL。这使您可以轻松访问下一页。但是请记住，您需要存储当前页面上第一个项目的`id`,以便您在导航到下一个页面后仍然可以访问该页面。

## PHP 客户端

如果你想在使用 Instagram API 时让你的生活变得更简单，有一个名为 [Instagram-PHP-API](https://github.com/cosenary/Instagram-PHP-API) 的 PHP 库，它提供了方便的方法。要安装它，执行`composer require cosenary/instagram`。

完成后，您可以通过添加以下代码来使用它:

```
use  MetzWeb\Instagram\Instagram; $instagram =  new  Instagram(array(  'apiKey'  => CLIENT_ID,  'apiSecret'  => CLIENT_SECRET,  'apiCallback'  => REDIRECT_URL ));
```

这里有几个例子。

### 获取登录 URL

```
$instagram->getLoginUrl(array('basic',  'relationships'));
```

array 参数是可选的。它包含您要使用的范围。

### 获取访问令牌

与我们之前使用 Guzzle 所做的非常相似，只是这一次，我们调用方法，我们需要的数据变得容易获得。

```
$app->get('/login2',  function  ()  use  ($app, $instagram)  { $login_url = $instagram->getLoginUrl(array('basic',  'likes'));  if(!empty($_GET['code'])){ $code = $_GET['code']; $data = $instagram->getOAuthToken($code);  //get access token using the authorization code $instagram->setAccessToken($data); $access_token = $instagram->getAccessToken();  //do anything you want with the access token  }else{ $app->render('login.php', array('login_url'  => $login_url));  }  });
```

### 获取用户信息

您可以通过调用`getUser`方法来获取用户信息。如果您只想获取当前登录用户的用户信息，可以省略`$user_id`。

```
$user_data = $instagram->getUser($user_id);
```

### 拉勒韦尔

如果你使用 Laravel，有人也创建了一个使用这个库的 Laravel 包装器。你可以点击查看[。](https://github.com/vinkla/instagram)

## 结论

在本教程中，我们学习了如何使用 Guzzle HTTP 客户端和 insta gram PHP 客户端使用 Instagram API。Instagram API 是一种与 Instagram 用户数据交互的非常好的方式。有了它，您可以构建一些真正有趣的应用程序。

你用 API 构建过什么吗？你更喜欢 Guzzle 还是 Instagram PHP 库？为什么？请在评论中告诉我们！

## 分享这篇文章