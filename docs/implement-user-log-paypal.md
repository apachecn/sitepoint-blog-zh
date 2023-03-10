# 如何用 PayPal 实现用户登录

> 原文：<https://www.sitepoint.com/implement-user-log-paypal/>

好奇心是我们工作中最重要的特征之一。前几天，我发现自己在浏览 PayPal 文档，寻找一些有趣的东西来学习(和分享)。过了一会儿，我偶然发现了用 PayPal 工具登录的[。](https://developer.paypal.com/docs/integration/direct/identity/log-in-with-paypal/)

通过“使用 PayPal 登录”工具，您的用户可以使用 PayPal 验证您的应用程序。这和我们已经知道的脸书或者 Twitter 和 GitHub 的程序是一样的。

如果您想将其与电子商务网站集成，建议使用这种类型的身份验证，但是您可以在需要用户帐户或成员资格的任何情况和应用程序中使用它。

## 为什么要使用“用 PayPal 登录”？

使用它的一个最重要的原因是**简单**。你的用户不需要记住另一组证书 OAuth 通过 FB、Twitter 等拥有同样的优势。此外，登录过程是绝对安全的:更少的顾虑。然而，这不仅仅是一个电子邮件和密码。

当你创建一个 PayPal 账户时，你需要输入很多信息。通过使用 API，这些信息也可以以许多有用的方式在您的应用程序中使用。我能给出的最好的例子之一是电子商务:如果你已经知道送货地址，为什么还要麻烦你的用户去问他呢？他已经告诉贝宝他住在哪里了！就用户体验而言，一个简单的按钮要比填写一个有很多字段的表单好得多……当涉及到发货时，至少有三四个字段。

另一个很棒的功能是我们正在谈论一个标准，因为使用 PayPal 登录工具是基于 OpenID Connect 的，它使用 OpenID 2.0 和 OAuth 2.0 开放标准。

使用 PayPal 工具也为商家/应用程序打开了许多新的视角。想想看，PayPal 实际上拥有超过 1 亿用户。你有可能扩大你的用户群。一切都伴随着用户数据的实时更新:如果你的用户在 PayPal 上改变了一些自己的信息，你的应用程序会立即“知道”。

我认为你现在有很好的理由开始思考这个问题。让我们看看如何在一个示例应用程序中实现这个解决方案。

## 它是如何工作的？

在进入实现过程之前，如果您能够理解幕后的机制，这将是一件好事。就像我们之前提到的，用 PayPal 登录工具是基于 Open ID Connect 的。在下面的图片中，你可以找到整个过程中每一个瞬间的所有信息。

![Login with PayPal Flowchart](img/2656656f71b069fc3cdb67dac7dba301.png)

一切都从我们正在开发的应用程序开始:它可以是我们之前使用的电子商务作为例子。使用 PayPal 登录按钮将用户重定向到 PayPal 身份验证和授权服务。在给出凭证后，用户必须同意与我们的应用程序共享他的数据。他会被告知他将与我们分享的每一个数据。如果一切顺利，PayPal 会给应用程序一个授权码。

下一步是商家(应用程序)和 PayPal 令牌服务端点之间的通信。第一个给第二个授权码，以便接收*访问令牌*和*刷新令牌*。

使用*访问令牌*,应用程序可以查询 PayPal 用户资料服务，获取所需的所有用户数据。注册(或登录)过程可以在几秒钟内完成。

现在事情比以前更清楚了，我们可以开始真正的实施了。让我们把手弄脏吧！

## 创建 PayPal 应用程序

首先，我们必须创建一个 PayPal 应用程序，以便获得*客户 ID* 和*客户秘密*。我们将在每次调用 API 方法时使用它们。它们将是我们使用 API 服务的凭证。

前往位于[https://developer.paypal.com/](https://developer.paypal.com/)的开发者仪表盘。在那里，点击“仪表板”，然后点击“应用程序”。点击“我的应用程序”进入 PayPal 应用程序列表。

如果你是第一次来这里，你可能会看到这样的东西。

![](img/91cb912cdd4c4669386606d0a910ebf7.png)

选择“创建应用程序”。您将被要求输入应用程序的名称和沙盒用户作为参考。

**注:**如果这是你第一次和 PayPal 合作，你很可能对沙盒很好奇。顾名思义，沙盒是 PayPal 提供的一种测试服务，让你在真实世界中尝试你的代码，而不会产生任何后果。它给你创造假用户(商家和顾客)的可能性，用假用户重新创建整个工作流程，以确保你的代码会像你期望的那样工作。沙盒和真实生产代码之间的唯一区别是您将使用的端点。然而，这并不是很难改变的事情:只需从端点上删除*沙箱*前缀。如果您需要更多信息，请查看文档:[https://developer . paypal . com/docs/classic/life cycle/sb _ overview/](https://developer.paypal.com/docs/classic/lifecycle/sb_overview/)。

在确认应用程序创建后，您将立即看到客户端 ID 和客户端密码。

**注意:**别忘了在这里设置返回页面 URL。这是 PayPal 将在认证步骤后重定向用户的页面，也是您必须处理令牌和数据的地方。点击“应用重定向网址”旁边的“编辑”。您将能够指定两个不同的 URL:一个用于测试，一个用于生产。在我的情况下，它将是类似于 http://myapp.ext/return.php 的。

![](img/6a3143af13750a965e14a31d99fcfe44.png)

让我们向下滚动页面，搜索“应用功能”部分。这是我们可以找到“用 PayPal 登录”的地方。点击“高级选项”,定制您真正需要的客户数据和其他小东西。

以下是您将在那里找到的内容的完整概述:

*   选择我们需要用户提供的信息:个人数据、地址数据(物理数据)和其他与账户类型相关的数据；
*   指定隐私政策和用户协议的链接:在每一个真实世界的情况下，我们都需要它们；
*   附加权限:关于集成的许多方面的其他选择。然而，我们现在什么都不需要；

让我们单击“保存”来确认我们的选择。

我们的任务已经完成了。现在我们有了第一个 PayPal 应用程序，我们可以用它来创建我们的第一个示例项目。

## 测试项目:简单的集成

为了专注于集成工作流，我决定做一个小的测试应用程序。没什么复杂的:我们只是在一个需要认证的站点上模拟登录过程。这种访问将通过登录贝宝。更准确地说，这是我们将要遵循的完整流程。

*   用户到达应用程序页面；
*   我请求通过贝宝访问用户(与按钮)；
*   该应用程序打开一个窗口，引导贝宝程序；
*   用户插入其凭据并授予所请求的权限；
*   用户被重定向到一个“结果页面”，在那里他将能够看到他的贝宝帐户数据。这将是我们的“确认”,以了解我们的整合运作良好；

这将是我们最后的确认屏幕。

![](img/2f3c73256d9ffce318f201d84e3946d1.png)

你准备好了吗？让我们开始吧。

### 配置和安装

即使是一个非常小的应用程序，我也会使用 [Httpful](http://phphttpclient.com/) 库来调用 PayPal API。我喜欢它，因为它是:

*   光；
*   易于安装(通过 Composer)；
*   易于理解(得益于直观的语法)；

因此，让我们为我们的项目创建一个文件夹，并在其中保存一个新的 *composer.json* 文件，我们将在其中写入:

```
{
"require": {
"nategood/httpful": "*"
}
}
```

只要一声`composer update`我们就完成了。

之后，创建两个文件:第一个文件`index.php`，将作为我们示例的登录页面。第二个，`result.php`将从 PayPal 接收授权码，并将处理 API 调用和结果数据。

打开`index.php`文件，同时用浏览器进入[https://developer . paypal . com/web apps/developer/docs/integration/direct/identity/button-js-builder/](https://developer.paypal.com/webapps/developer/docs/integration/direct/identity/button-js-builder/)。从这里，您将能够动态生成我们的“用 PayPal 登录”按钮的代码。生成之后，复制代码并粘贴到*index.php*页面。

这是生成器页面:

![](img/daa1e466782b3bb720ac31bb0b246293.png)

没有太多事情需要决定。我们必须指定的第一件事是我们想要使用的客户端 ID。在您可以指定想要使用的端点之后:实时生产还是沙盒(用于测试)？然后，你可以从“审美”的角度选择其他关于本地化、定制化的小事。

您还必须再次填写“返回网址”。是，使用与以前相同的 URL。

完成后，获取生成的代码非常简单:只需向下滚动页面，就可以在那里找到它。

### index.php 档案

索引文件现在看起来像这样:

```
<!DOCTYPE html>
<html>
<head>
<!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">

<!-- Optional theme -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">

<!-- Latest compiled and minified JavaScript -->
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>

<title>Login with PayPal - Demo App</title>

<style type="text/css">
body {
text-align: center;
}
</style>
</head>
<body>
<h1>Login with PayPal - Demo App</h1>
<p>Welcome! No boring signup here. Just use the following button to login.</p>

<hr/>

<p><span id="myContainer"></span></p>

<script src="https://www.paypalobjects.com/js/external/api.js"></script>
<script>
paypal.use( ["login"], function(login) {
login.render ({
"appid": "my-application-client-id",
"authend": "sandbox",
"scopes": "profile email address phone https://uri.paypal.com/services/paypalattributes",
"containerid": "myContainer",
"locale": "en-us",
"returnurl": "http://sandbox.hellofrancesco.com/paypal_test/return.php"
});
});
</script>
</body>
</html>
```

在这个特定的例子中，正如你从代码中看到的，我为沙箱创建了一个按钮。然而，没有什么是你不能理解的:正如我经常做的那样，我通过 CDN 使用 Bootstrap 来赋予页面一个基本的样式。现在，保存并运行页面:我们已经可以验证按钮工作正常！这是我们点击它会发现的。

![](img/2335d4fbc615ed7853abb2e8dddbc336.png)

**注:**看不懂的也不用担心。实际上，我是意大利人，在我的语言中使用 PayPal。

现在，我们必须在访问`result.php`文件后实现登录。

### `result.php`文件

打开`result.php`并给它这个内容:

```
<?php require('paypal_login.inc.php') ?>

<!DOCTYPE html>
<html>
<head>
<!-- Latest compiled and minified CSS -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">

<!-- Optional theme -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">

<!-- Latest compiled and minified JavaScript -->
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>

<title>Login with PayPal - Demo App</title>

<style type="text/css"> body {
text-align: center;
padding:20px;
} </style>
</head>
<body>
<h1>Login with PayPal - Demo App</h1>
<p>Great! Now you are a member of our site.</p>

<hr/>

<h2>Your Data</h2>
<p><b>Name:</b> <?php echo htmlspecialchars_decode($user->given_name . ' ' . $user->family_name); ?></p>
<p><b>Address:</b> <?php echo htmlspecialchars_decode($user->address->street_address . ', ' . $user->address->locality); ?></p>

<hr/>

<p><button class="btn btn-success form-control" type="button" onclick="window.close()">Ok, done.</button></p>
</body>
</html>
```

这是一个带有用户数据的简单屏幕。我们使用的是存储所有用户信息的`$user`对象。您还会注意到，在文件的开头包含了一个`paypal_login.inc.php`文件。

现在创建这个新文件:

```
<?php
// helper package autoload.
require('vendor/autoload.php');

// setting some variables here.
$clientId = 'your-client-id';
$clientSecret = 'your-client-secret';
$requestData = '?grant_type=authorization_code&code='.$_GET['code'].'&return_url=http://sandbox.hellofrancesco.com/paypal_test/return.php';

// here we exchange the authorization code with access and refresh tokens.
$response = \Httpful\Request::get('https://api.sandbox.paypal.com/v1/identity/openidconnect/tokenservice' . $requestData)
->authenticateWith($clientId, $clientSecret)
->send();

$jsonResponse = json_decode($response->raw_body);

// checking out for errors.
if(isset($jsonResponse->error))
{
die('Error: just got some problems during operations. Try again.');
}

// getting user data, using the Identity APIs.
$response = \Httpful\Request::get('https://api.sandbox.paypal.com/v1/identity/openidconnect/userinfo/?schema=openid')
->contentType("application/json")
->authorization($jsonResponse->access_token)
->authenticateWith($clientId, $clientSecret)
->send();

// user data is here!
$user = json_decode($response->raw_body);
```

在第一部分，您可以看到作曲家`autoload.php`和一些变量设置:

*   我们在应用程序创建后立即收到的客户端 ID；
*   客户秘密，以同样的方式获得；
*   变量`$requestData`,包含我们将要传递给令牌交换端点的所有信息；

**注意:**在`$_GET['code']`你会发现 PayPal 传递给我们`result.php`页面的授权码。因为这是一个测试应用程序，所以我按原样使用该值。在现实世界中，不要忘记通过过滤这些输入来尽可能地保证一切安全。

在那之后，您可以找到我们对令牌专用服务的第一个 API 调用。正如注释所建议的，`\Httpful\Request`的`get()`方法准备 API 调用，指定必须使用什么动词。语法非常容易理解——链式方法`authenticateWith()`使用客户端 ID 和客户端秘密设置认证数据。

此时，`send()`方法开始调用。结果存储在`$response`变量中，该变量是[响应](http://phphttpclient.com/docs/class-Httpful.Response.html)类的实例。在那之后，我们获取并解码`raw_body`属性。在进行一般性检查以发现可能的错误之后，如果一切正常，我们可以进行第二次 API 调用，这次直接调用处理用户数据的身份 API。

返回的对象是与之前相同类型的另一个对象，`Response`。这一次，请求结果被存储到一个`$user`变量中，这个变量将用于我们之前看到的`result.php`文件中。

我们的第一个示例应用程序已经完成。正如我们之前在本文开头的图片中看到的那样，我们尊重并实现了整个工作流。

## 结论和考虑

我们刚刚制作的这个应用程序是一个非常基本的例子。当然，在现实世界中，您将不得不实现许多其他东西:首先，存储令牌的机制，以保持您的用户登录。此外，当令牌续订服务到期时，您必须考虑对其进行一些调用。你将不得不使用*刷新令牌*来做到这一点，用[用这个方法](https://developer.paypal.com/webapps/developer/docs/api/#grant-token-from-refresh-token)。

我们已经讨论了为什么您应该在您的应用程序上实现类似的解决方案的原因。

然而，你可以使用 PayPal 登录的最好工具之一是[无缝结账](https://developer.paypal.com/webapps/developer/docs/integration/direct/identity/seamless-checkout/)。这两个工具一起使用，可以让你的客户在不离开网站的情况下完成订单并付款。

“使用 PayPal 登录”功能是您可以尝试和评估的，特别是如果您想让您的用户体验到您的应用程序与世界上最好的(也是最常用的)在线支付服务之一的更深入集成。

你已经试过了吗？你怎么想呢?让我们知道。

## 分享这篇文章