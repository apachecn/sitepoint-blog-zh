# 使用易贝交易 API 配置您的商店设置

> 原文：<https://www.sitepoint.com/configuring-stores-settings-ebay-trading-api/>

在第 1 部分中，我们解释了易贝开发人员仪表板的不同部分，并配置了我们的测试应用程序。我们还创建了我们的数据库。现在我们准备创建一个项目。在这一部分，我们将重点关注商店设置。在第 3 部分中，我们将向我们的商店添加新产品。

![](img/6fb2f6735c29aaf491b97067ac905445.png)

## 属国

在我们继续之前，我们首先需要安装应用程序的依赖项。创建一个`composer.json`文件并添加以下内容:

```
{
    "require" : {
        "slim/slim-skeleton": "dev-master",
        "slimcontroller/slimcontroller": "dev-master",
        "guzzlehttp/guzzle": "4.*",
        "vlucas/valitron": "~1.2",
        "codeguy/upload": "*"
    },
    "autoload": {
      "classmap": [
        "controllers"
      ]
    }
}
```

我们将为这个应用程序使用[瘦框架](http://www.slimframework.com/)。由于 Slim 并没有将`MVC`中的`C`嵌入其中，我们还需要 [Slim 控制器](https://github.com/fortrabbit/slimcontroller)。然后我们还需要 [Guzzle](http://guzzle.readthedocs.org/en/latest/) ，这样我们就可以轻松地对易贝进行 API 调用。接下来是 [valitron](https://github.com/vlucas/valitron) 进行表单验证。最后，还有用于验证和处理上传的[上传](https://github.com/codeguy/Upload)库。

在项目目录的根目录下创建一个`index.php`文件，然后添加以下代码:

```
<?php
require 'vendor/autoload.php';

$app = New \SlimController\Slim(array(
    'templates.path' => 'templates'
));

$app->view(new \Slim\Views\Twig());
$app->view->parserOptions = array(
    'charset' => 'utf-8',
    'cache' => realpath('templates/cache'),
    'auto_reload' => true,
    'strict_variables' => false,
    'autoescape' => true
);

$app->hook('slim.before', function () use ($app) {
    $app->view()->appendData(array('baseUrl' => '/tester/ebay_trading_api'));
});

$app->run();
```

分解一下，首先我们需要`autoload.php`文件，这样我们就可以使用之前在`composer.json`文件中需要的库。

```
require 'vendor/autoload.php';
```

接下来，我们初始化瘦控制器。这允许我们配置控制器要使用的选项，例如模板路径，它是模板目录的路径。

```
$app = New \SlimController\Slim(array(
    'templates.path' => 'templates'
));
```

接下来，我们告诉 Slim 使用 [Twig](http://twig.sensiolabs.org/) 来处理我们的视图:

```
$app->view(new \Slim\Views\Twig());
$app->view->parserOptions = array(
    'charset' => 'utf-8',
    'cache' => realpath('templates/cache'),
    'auto_reload' => true,
    'strict_variables' => true,
    'autoescape' => true
);
```

我们还指定了以下选项:

*   **字符集**–Twig 使用的字符集。
*   **缓存**–Twig 编译我们正在使用的模板，因此它不必在每次页面加载时都这样做。这些编译后的模板文件存储在此处指定的目录中。
*   **auto _ reload**–这告诉 Twig 在每次源代码改变时重新编译模板。
*   **strict _ variables**–这告诉 Twig 在模板中使用的变量没有声明时返回错误。
*   **auto escape**–这告诉 Twig 在模板中遇到 html、CSS 和 JavaScript 代码时自动转义。

接下来，我们创建一个钩子，它将在执行 Slim 应用程序之前运行。这个钩子的作用是将`baseUrl`附加到视图上。这意味着无论我们赋予`baseUrl`什么值，它在每个视图中都是可访问的。我们稍后使用这个值来链接我们的前端资产。

```
$app->hook('slim.before', function () use ($app) {
    $app->view()->appendData(array('baseUrl' => '/tester/ebay_trading_api'));
});
```

最后，我们运行 Slim 应用程序:

```
$app->run();
```

### Ebay 类别

我们需要创建一个类，我们可以用它来更容易地调用 API。在项目目录的根目录下创建一个`classes`文件夹，然后创建一个文件并命名为`Ebay.php`。在该文件中，添加以下内容:

```
<?php
class Ebay {
}
```

接下来声明我们将在整个课程中使用的变量:

```
public $compatability_level = 885;

public $sandbox_url = 'https://api.sandbox.ebay.com/ws/api.dll';
public $url = 'https://api.ebay.com/ws/api.dll';

public $run_name;
public $dev_id; 
public $app_id;
public $cert_id;
public $site_id;
public $user_token;
```

这些变量与第 1 部分中的 HTTP 头中的变量相同。我们在向 API 发出的每个请求中都需要这些变量。我唯一没有介绍的是发出请求的 URL。我们在这个类中声明了两个 URL:一个用于 API 的实时版本，另一个用于沙盒版本。您可以在发出请求时选择使用哪一个。但有一点你需要记住的是，你只能使用沙盒帐户的沙盒网址，真正的易贝帐户的活网址。这些不能互换使用。

接下来，创建构造函数方法。它所做的只是分配全局应用程序设置，以便我们可以在整个课程中使用它们:

```
public function __construct($settings){
  $this->run_name = $settings->run_name;
  $this->user_token = $settings->user_token;
  $this->dev_id = $settings->dev_id;
  $this->app_id = $settings->app_id;
  $this->cert_id = $settings->cert_id;
  $this->site_id = $settings->site_id;
}
```

然后，声明一个`request`方法。这将允许我们向 API 发出实际的请求:

```
public function request($method, $request_body){

  $client = new GuzzleHttp\Client();

  $response = $client->post($this->url, array(
      'verify' => false,
      'headers' => array(
          'X-EBAY-API-COMPATIBILITY-LEVEL' => $this->compatability_level,
          'X-EBAY-API-DEV-NAME' => $this->dev_id,
          'X-EBAY-API-APP-NAME' => $this->app_id,
          'X-EBAY-API-CERT-NAME' => $this->cert_id,        
          'X-EBAY-API-SITEID' => $this->site_id,
          'X-EBAY-API-CALL-NAME' => $method     
      ),
      'body' => $request_body
  ));

  $body = $response->xml();

  return $body;

}
```

这个方法接受两个参数:`$method`和`$request_body`。`$method`是您想要调用的 API 方法的名称。在这里你可以找到一个可以在易贝交易 API 中使用的 API 方法的[列表。
另一个参数是`$request_body`，它是一个 XML 字符串，包含 API 方法所需的数据。您还可以在我提供的链接中找到关于您需要为每个 API 方法传递的数据的详细信息，所以一定要查看一下。](http://developer.ebay.com/Devzone/xml/docs/Reference/ebay/index.html)

在该方法中，我们声明了 GuzzleHTTP 客户端的一个新实例。

```
$client = new GuzzleHttp\Client();
```

接下来，我们向沙箱 URL 发出一个`POST`请求。我们传入一个包含请求头和请求体的数组:

```
$response = $client->post($this->sandbox_url, array(
            'verify' => false,
            'headers' => array(
                'X-EBAY-API-COMPATIBILITY-LEVEL' => $this->compatability_level,
                'X-EBAY-API-DEV-NAME' => $this->dev_id,
                'X-EBAY-API-APP-NAME' => $this->app_id,
                'X-EBAY-API-CERT-NAME' => $this->cert_id,        
                'X-EBAY-API-SITEID' => $this->site_id,
                'X-EBAY-API-CALL-NAME' => $method   
            ),
            'body' => $request_body
        ));
```

我们已经将`verify`设置为`false`。这将 SSL 证书验证设置为`false`。注意这是不安全的，所以最好设置为`true`。但是由于我们还处于测试阶段，我们可以将它设置为`false`来避免 SSL 证书检查。

接下来是`headers`——这些基本上与我们之前看到的请求头相同。

最后，还有包含请求主体的`body`。稍后我们将看到我们需要传递给它的数据类型。

一旦请求返回响应，它将是 XML 格式的，所以我们告诉 Guzzle 通过调用`xml()`方法来解析它。这非常类似于调用`simplexml_load_string`将 XML 转换成对象。最后，我们调用`json_encode`和`json_decode`将对象转换成数组。

接下来，创建将从 API 中检索会话 ID 的方法。为此，我们需要使用 API 中的`GetSessionID`方法。这需要应用程序的运行名称作为其参数:

```
public function getSessionID(){

    $request_body = '<?xml version="1.0" encoding="utf-8"?>
      <GetSessionIDRequest xmlns="urn:ebay:apis:eBLBaseComponents">
        <RuName>' . $this->run_name . '</RuName>
      </GetSessionIDRequest>';

    $response = $this->request('GetSessionID', $request_body);
    return $response->SessionID;
}
```

接下来是使用`FetchToken` API 方法的`getUserToken`方法。这需要我们通过调用`getSessionID`获得的会话 ID。这个方法的作用是返回用户令牌，我们可以用它代表用户向 API 发出请求。

```
public function getUserToken($session_id){

    $request_body = '<?xml version="1.0" encoding="utf-8"?>
        <FetchTokenRequest xmlns="urn:ebay:apis:eBLBaseComponents">
          <SessionID>' . $session_id . '</SessionID>
        </FetchTokenRequest>';

    $response = $this->request('FetchToken', $request_body);
    return $response->eBayAuthToken;      

}
```

流程大概是这样的:

1.  我们调用返回会话 ID 的`getSessionID`。
2.  我们将应用程序设置为重定向到易贝，将会话 ID 传递到重定向 URL。
3.  用户登录并授予应用程序权限。
4.  用户被重定向到应用程序。
5.  我们调用`getUserToken`来获取用户令牌，传入我们之前获取的会话 ID。
6.  API 返回用户令牌，我们将它保存到数据库中供以后使用。

稍后，我们将在代码中实现它。

这就是我们目前需要的全部内容。现在我们需要告诉 Composer 为我们自动加载类。打开`composer.json`，在`autoload`的`classmap`属性中添加`classes`:

```
"autoload": {
      "classmap": [
        "controllers",
        "classes"
      ]
    }
```

接下来打开项目根目录中的`index.php`文件，并在`$app->run()`之前添加以下代码。这将允许我们在任何地方使用`$this->app->ebay`调用`Ebay`类的方法:

```
$app->container->singleton('ebay', function () use($app) {

    $id = 1;
    $settings_result = $app->db->query("SELECT user_token, run_name, dev_id, app_id, cert_id, site_id FROM settings WHERE id = $id");
    $settings = $settings_result->fetch_object();
    return new Ebay($settings);
});
```

在这里，让我们也为应用程序容器分配一个 mysqli 实例，并将其命名为`db`。

```
$app->container->singleton('db', function (){

    $server = 'localhost';
    $user = 'user';
    $pass = '';
    $database = 'ebaytrading';

    return new mysqli($server, $user, $pass, $database);

});
```

### 主页

现在我们已经准备好开始前端的工作了。首先，在`templates`目录下创建一个`base.twig`文件，然后添加以下代码:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{{ title }}</title>
  <link rel="stylesheet" href="{{ baseUrl }}/assets/lib/bootstrap/bootstrap.css">
  {% if is_productpage %}
  <link rel="stylesheet" href="{{ baseUrl }}/assets/lib/dropzone/dropzone.css">
  {% endif %}
</head>
<body>

    <div class="navbar navbar-default navbar-static-top" role="navigation">
      <div class="container">
        <div class="navbar-header">
          <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target=".navbar-collapse">
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
          <a class="navbar-brand" href="#">{{ title }}</a>
        </div>
        <div class="navbar-collapse collapse">
      <ul class="nav navbar-nav">
        <li class="active"><a href="{{ baseUrl }}">Home</a></li>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown">Products <span class="caret"></span></a>
          <ul class="dropdown-menu" role="menu">
            <li><a href="{{ baseUrl }}/products/new">New</a></li>
            <li><a href="{{ baseUrl }}/products">List</a></li>
          </ul>
        </li>
      </ul>
      <ul class="nav navbar-nav">
        <li class="dropdown"><a href="{{ baseUrl }}/token">Get Token</a></li>
      </ul>
      <ul class="nav navbar-nav">
        <li><a href="{{ baseUrl }}/settings">Settings</a></li>
      </ul>
        </div>
      </div>
    </div>

    <div class="container">
      {% block content %}{% endblock %}
    </div>

  <script src="{{ baseUrl }}/assets/js/jquery.js"></script>
  <script src="{{ baseUrl }}/assets/lib/bootstrap/bootstrap.js"></script>
  {% if is_productpage %}
  <script src="{{ baseUrl }}/assets/js/handlebars.js"></script>
  <script src="{{ baseUrl }}/assets/lib/dropzone/dropzone.js"></script>
  <script src="{{ baseUrl }}/assets/js/dropzone-options.js"></script>
  <script src="{{ baseUrl }}/assets/js/new-product.js"></script>
  {% endif %}

</body>
</html>
```

如果这个模板看起来很熟悉，那是因为我们在这个项目中使用了 Twitter Bootstrap。

回到模板，我们使用三个变量:`baseUrl`、`content`和`title`。之前，我们已经从`index.php`文件中声明了`baseUrl`。稍后，我们将从控制器传入`content`和`title`。现在，让我们关注 Twig 中的`blocks`。我们通过调用`block`和变量名在模板中使用它们。在这种情况下，我们称该模块为`content`:

```
{% block content %}{% endblock %}
```

块是一种基于我们从控制器传递的内容来呈现来自另一个模板的内容的方法。稍后，我们将讨论如何传递我们在这里呈现的实际内容。

正如我前面提到的，我们正在使用 Twitter Bootstrap，所以我们需要将它放在`assets/lib/bootstrap`目录中。如果你不想使用默认主题，你可以使用 bootswatch.com 的主题。就我个人而言，我使用的是[雪人](http://bootswatch.com/yeti/)主题。我们还需要 jQuery，因为 Twitter Bootstrap 的 JavaScript 依赖于它，并且我们需要它来处理事件。我们还需要[手柄](http://handlebarsjs.com/)用于模板，以及 [dropzone](http://www.dropzonejs.com/) 用于上传产品图片。

请注意，我们有以下条件:

```
{% if is_productpage %}
{% endif %}
```

我们用它来加载当前页面所需的样式和脚本。

如果您还没有下载必要的文件，请继续下载。

返回后，在`controllers`目录下创建 Home 控制器。这将延长`SlimController`:

```
<?php
class Home extends \SlimController\SlimController {

}
```

在控制器内部，声明`indexAction`方法。它使用`render`方法为我们呈现索引模板。`render`方法接受两个参数:模板的名称和一些要传递的可选数据。在这种情况下，我们只是简单地传入一个名称:

```
public function indexAction(){
    $this->render('index', array('title' => 'Home', 'name' => 'Dog'));
}
```

接下来，创建索引模板，将其命名为`index.twig`。使用模板时，需要记住给模板文件起一个与控制器中使用的名称相同的名称。从索引模板中，我们需要调用`extends`方法并传递我们使用的基本模板的文件名。接下来，我们将想要呈现的内容封装在`block`标签中。确保您传递的名称与您在基本模板中传递的名称相同:

```
{% extends "base.twig" %}
{% block content %}
{{ parent() }}
<h1>Hi {{ name }}</h1>
{% endblock %}
```

既然我们已经在编写家庭控制器的代码，我们也可以编写获取会话 id 和用户令牌的方法。首先让我们做会话 ID。这将从`Ebay`类中调用`getSessionID()`方法，并返回一个会话 ID，然后我们将它存储到会话中。然后，我们将用户重定向到易贝登录页面。在这里，我们重定向到沙盒登录页面。我还为那些想稍后部署易贝应用程序的人提供了 live 的 URL。live 和沙盒版本都需要将`RuName`和`SessID`作为查询参数传递。

```
public function sessionAction(){

    $session_id = $this->app->ebay->getSessionID();
    $_SESSION['session_id'] = $session_id;

    //live: https://signin.ebay.co.uk/ws/eBayISAPI.dll?SignIn&RuName=" . $this->app->ebay->run_name . "&SessID=$session_id

    $redirect_url = "https://signin.sandbox.ebay.com/ws/eBayISAPI.dll?SignIn&RuName=" . $this->app->ebay->run_name . "&SessID=" . $session_id;
    $this->app->redirect($redirect_url);

}
```

一旦用户批准了应用程序，就会调用`tokenAction()`方法。这个方法的作用是检索我们之前保存在会话中的会话 id。然后我们用它作为返回用户令牌的`getUserToken()`方法的参数。然后，我们将这个用户令牌保存到数据库中，以便以后向 API 发出请求时可以使用它。

```
public function tokenAction(){

    $session_id = $_SESSION['session_id'];
    $token = $this->app->ebay->getUserToken($session_id);

    $id = 1;

    $settings_result = $this->app->db->query("SELECT user_token FROM settings WHERE id = $id");
    $settings = $settings_result->fetch_object();

    if(!empty($settings)){
        $this->app->db->query("UPDATE settings SET user_token = '$token' WHERE id = $id");
    }else{
        $this->app->db->query("INSERT INTO settings SET user_token = '$token'"); 
    }

    $this->app->redirect('/tester/ebay_trading_api');
}
```

### 路线

接下来，我们需要添加我们将在整个应用程序中使用的路线。我们可以在调用`$app->run()`之前从`index.php`文件中完成:

```
$app->addRoutes(array(
    '/' => 'Home:index',
    '/settings' => 'Settings:view',
    '/settings/update' => 'Settings:update',
    '/products/new' => 'Product:new',
    '/products/create' => 'Product:create',
    '/upload' => 'Product:upload',
    '/session' => 'Home:session',
    '/token' => 'Home:token',
    '/categories' => 'Product:categories'
));
```

在上面的代码中，我们使用了 Slim 控制器扩展中的`addRoutes`方法。这占用了一个路由数组，键是实际的路由，值是控制器和方法。控制器和方法由冒号`:`分隔。Slim 使用的惯例是，控制器中响应路由的每个方法都应该有一个后缀`Action`。

以下是每条路线的功能概述:

*   `/`–主页。
*   `/settings`–用于查看商店设置页面。
*   `/setttings/update`–用于更新商店设置。
*   `/products/new`–用于查看创建新产品的表格。
*   `/products/create`–用于创造新产品。
*   `/upload`–用于上传产品图片。
*   `/session`–用于获取会话 ID。
*   `/token`–用于获取用户令牌。
*   `/categories`–用于建议产品类别。

### 商店设置

接下来，在`controllers`目录下创建一个新文件，并将其命名为`Settings.php`。这将处理商店设置的更新。简而言之，我们只需为商店设置输入一些默认数据，这样我们就不需要编写创建商店设置的代码了。执行以下查询来插入默认数据:

```
INSERT INTO `store_settings` (`id`, `store_name`, `county`, `street`, `country_code_type`, `ebay_website`, `postal_code`, `category_mapping`, `category_prefill`, `currency_code`, `item_location`, `dispatch_time`, `listing_duration`, `listing_type`, `condition_type`, `optimal_picturesize`, `out_of_stock_control`, `get_it_fast`, `include_prefilled`, `shipping_profile`, `return_profile`, `payment_profile`, `shipping_service`, `shippingservice_priority`, `shippingservice_cost`, `shippingservice_additionalcost`) VALUES
(1, 'My Awesome Store', 'Driftveil', '275 Star Street', 'GB', 'UK', 'XYZ 123', 1, 1, 'GBP', 'Driftveil Unova', 1, 'GTC', 'FixedPriceItem', '', 1, 1, 1, 1, '', '', '', '', 3, 30, 15);
```

请注意，对于本教程，我们主要使用表中的第一行，该行的 ID 为 1，因此我们将使用 1 作为获取或更新商店设置表的 ID。

接下来，创建一个`viewAction`方法并放入以下代码:

```
public function viewAction(){   

    $user_preferences = $this->app->ebay->getUserPreferences();
    $shipping_services = $this->app->ebay->getEbayDetails('ShippingServiceDetails');

    $condition_types_result = $this->app->db->query('SELECT id, name FROM condition_types');
    $condition_types = array();
    while($row = $condition_types_result->fetch_assoc()){
      $condition_types[] = $row;
    }

    $listing_durations_result = $this->app->db->query('SELECT name FROM listing_durations');
    $listing_durations = array();
    while($row = $listing_durations_result->fetch_assoc()){
      $listing_durations[] = $row['name'];
    }

    $listing_types_result = $this->app->db->query('SELECT name FROM listing_types');
    $listing_types = array();
    while($row = $listing_types_result->fetch_assoc()){
      $listing_types[] = $row['name'];
    }

    $shippingservice_priorities = range(1, 4);

    $store_id = 1;
    $store_result = $this->app->db->query("SELECT store_name, county, street, country_code_type,
   ebay_website, postal_code, category_mapping, category_prefill, currency_code, item_location,
   dispatch_time, listing_duration, listing_type, condition_type, optimal_picturesize, out_of_stock_control,
   get_it_fast, include_prefilled, shipping_profile, return_profile, payment_profile, shipping_service,
   shippingservice_priority, shippingservice_cost, shippingservice_additionalcost
     FROM store_settings WHERE id = '$store_id'");
    $store = $store_result->fetch_object();

    $settings = array(
      'user_preferences' => $user_preferences,
      'shipping_services' => $shipping_services,
      'condition_types' => $condition_types,
      'listing_durations' => $listing_durations,
      'listing_types' => $listing_types,
      'store' => $store,
      'shippingservice_priorities' => $shippingservice_priorities
    );
    $this->render('settings/view', $settings);    

}
```

`viewAction`方法从`Ebay`类中调用`getUserPreferences`方法。我们稍后将介绍该方法，但现在我们知道它的作用是返回经过身份验证的用户的销售者配置文件。我们还调用了`getEbayDetails`方法，该方法返回特定易贝网站可用的运输服务。接下来，我们从数据库中获取[条件类型](http://developer.ebay.com/Devzone/finding/CallRef/Enums/conditionIdList.html)、[列表持续时间](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingDurationCodeType.html)和[列表类型](http://developer.ebay.com/devzone/xml/docs/reference/ebay/types/ListingTypeCodeType.html)，并将它们分配给一个数组。
然后我们生成一个包含数字 1 到 4 的数组。这将作为运输服务优先级的选择。最后，我们获取商店设置并呈现页面。

之前，我们已经使用了来自`Ebay`类的两个方法，但是我们还没有真正定义它们。打开您的`classes`目录中的`Ebay.php`，并添加以下方法。

首先是`getUserPreferences`方法，它使用 API 中的`GetUserPreferences`方法。这允许您从已验证的易贝帐户获取用户设置。这个 API 方法大部分需要布尔值。它返回一堆数据，所以我们通过只指定我们需要的字段来过滤掉其他数据。这是我们正在使用的字段的明细:

*   **showglobalippingprogramlistingpreference**–如果指定，则返回全球运输程序是否启用的数据。
*   **ShowSellerExcludeShipToLocationPreference**–如果指定，则返回排除的运输地点。
*   **ShowSellerPaymentPreferences**–如果指定，则返回卖家的支付首选项。
*   **ShowSellerProfilePreferences**–如果指定，它将返回业务策略信息。
*   **showsellerarrorpreferences**–如果指定，则返回退货首选项。

对于销售者配置文件，我们只获取默认的配置文件，然后将它们存储在一个数组中，数组中只有 ID、类型和配置文件的名称。

```
public function getUserPreferences(){

  $request_body = '<?xml version="1.0" encoding="utf-8"?>
    <GetUserPreferencesRequest xmlns="urn:ebay:apis:eBLBaseComponents">
     <RequesterCredentials>
        <eBayAuthToken>' . $this->user_token . '</eBayAuthToken>
      </RequesterCredentials>
      <ShowGlobalShippingProgramListingPreference>true</ShowGlobalShippingProgramListingPreference>
      <ShowSellerExcludeShipToLocationPreference>true</ShowSellerExcludeShipToLocationPreference>
      <ShowSellerPaymentPreferences>true</ShowSellerPaymentPreferences>
      <ShowSellerProfilePreferences>true</ShowSellerProfilePreferences>
      <ShowSellerReturnPreferences>true</ShowSellerReturnPreferences>
    </GetUserPreferencesRequest>';

  $response = $this->request('GetUserPreferences', $request_body);

  $seller_profiles = array();
  foreach($response->SellerProfilePreferences->SupportedSellerProfiles->SupportedSellerProfile as $profile){
    $is_default = $profile->CategoryGroup->IsDefault;

    if($is_default == 'true'){      

      $seller_profiles[] = array(
        'id' => json_decode(json_encode($profile->ProfileID), true)[0],
        'type' => json_decode(json_encode($profile->ProfileType), true)[0],
        'name' => json_decode(json_encode($profile->ProfileName), true)[0]
      );

    }
  }

  $user_preferences = array(  
    'seller_profiles' => $seller_profiles,
    'paypal_emailaddress' => json_decode(json_encode($response->SellerPaymentPreferences->DefaultPayPalEmailAddress), true)[0]
  );

  return $user_preferences;

}
```

我们使用的另一种方法是`getEbayDetails`。它使用 API 中的`GeteBayDetails`方法。我们只需要为`DetailName`指定一个值就可以了。之前，我们提供了`ShippingServiceDetails`作为值。这使我们能够获得易贝网站支持的运输服务列表，产品将在该网站上列出。对于`DetailName`的其他适用值列表，您可以查看该页面。

```
public function getEbayDetails($detail_name){

  $request_body = '<?xml version="1.0" encoding="utf-8"?>
  <GeteBayDetailsRequest xmlns="urn:ebay:apis:eBLBaseComponents">
    <RequesterCredentials>
    <eBayAuthToken>' . $this->user_token . '</eBayAuthToken>
    </RequesterCredentials>
  <DetailName>' . $detail_name . '</DetailName>
  </GeteBayDetailsRequest>';

  $response = $this->request('GeteBayDetails', $request_body);

  $services = array();
  foreach($response->ShippingServiceDetails as $service){
    $services[] = json_decode(json_encode($service->ShippingService), true)[0];
  }

  return $services; 

}
```

回到设置控制器，添加`updateAction`方法。这样做的目的是更新存储设置。这里我们使用 valitron 进行验证。如果验证通过，我们将逐个提取值。对于复选框，我们检查相应的字段是否不为空。如果不是，那么我们将值设置为 1，否则为 0。之后，我们使用一个准备好的语句来更新存储设置，传递一个要返回给用户的消息，然后重定向到设置页面。如果验证失败，我们只需传递错误并重定向到设置页面。

```
public function updateAction(){

    $v = new Valitron\Validator($_POST);
    $v->rule('required', array('store_name', 'county', 'street', 'country_code_type', 'ebay_website', 'postal_code',
      'currency_code', 'item_location', 'dispatch_time', 'listing_duration', 'listing_type', 'condition_type',
      'PAYMENT', 'RETURN_POLICY', 'SHIPPING', 'shipping_service', 'shippingservice_priority', 'shippingservice_cost', 'shippingservice_additionalcost'));

    if($v->validate()){

      $id = 1; //store settings id
      $store_name = $_POST['store_name'];
      $street = $_POST['street'];
      $county = $_POST['county'];
      $country_code_type = $_POST['country_code_type'];
      $ebay_website = $_POST['ebay_website'];
      $postal_code = $_POST['postal_code'];

      $category_mapping = (!empty($_POST['category_mapping'])) ? 1 : 0;
      $category_prefill = (!empty($_POST['category_prefill'])) ? 1 : 0;
      $optimal_picturesize = (!empty($_POST['optimal_picturesize'])) ? 1 : 0;
      $out_of_stock_control = (!empty($_POST['out_of_stock_control'])) ? 1 : 0;
      $get_it_fast = (!empty($_POST['get_it_fast'])) ? 1 : 0;
      $include_prefilled = (!empty($_POST['include_prefilled'])) ? 1 : 0;

      $currency_code = $_POST['currency_code'];
      $item_location = $_POST['item_location'];
      $dispatch_time = $_POST['dispatch_time'];
      $listing_duration = $_POST['listing_duration'];
      $listing_type = $_POST['listing_type'];
      $condition_type = $_POST['condition_type'];
      $payment_policy = $_POST['PAYMENT'];
      $return_policy = $_POST['RETURN_POLICY'];
      $shipping_policy = $_POST['SHIPPING'];
      $shipping_service = $_POST['shipping_service'];

      $shippingservice_priority = $_POST['shippingservice_priority'];
      $shippingservice_cost = $_POST['shippingservice_cost']; 
      $shippingservice_additionalcost = $_POST['shippingservice_additionalcost'];

      if($query = $this->app->db->prepare("UPDATE store_settings SET store_name = ?, county = ?, street = ?, 
          country_code_type = ?, ebay_website = ?, postal_code = ?, category_mapping = ?, category_prefill = ?, 
          currency_code = ?, item_location = ?, dispatch_time = ?, listing_duration = ?, listing_type = ?, 
          condition_type = ?, optimal_picturesize = ?, out_of_stock_control = ?, get_it_fast = ?, include_prefilled = ?, 
          shipping_profile = ?, return_profile = ?, payment_profile = ?, shipping_service = ?,
          shippingservice_priority = ?, shippingservice_cost = ?, shippingservice_additionalcost = ? 
          WHERE id = ?")){

      $query->bind_param("ssssssiississsiiiissssiddi", $store_name, $county, $street, $country_code_type, $ebay_website, $postal_code, $category_mapping, $category_prefill, $currency_code, $item_location, $dispatch_time, $listing_duration, $listing_type, $condition_type, $optimal_picturesize, $out_of_stock_control, $get_it_fast, $include_prefilled, $shipping_policy, $return_policy, $payment_policy, $shipping_service, $shippingservice_priority, $shippingservice_cost, $shippingservice_additionalcost, $id);

        $query->execute();

        $this->app->flash('message', array('type' => 'success', 'text' => 'Settings was updated!'));
        $this->app->redirect('/tester/ebay_trading_api/settings');
      }

    }else{

        $this->app->flash('form', $_POST);
        $this->app->flash('message', array(
            'type' => 'danger', 
            'text' => 'Please fix the following errors', 
            'data' => $v->errors())
        );

        $this->app->redirect('/tester/ebay_trading_api/settings');

    }

}
```

## 结论

在这一部分中，我们实现了商店设置的基本更新机制。在本系列的下一个也是最后一个部分，我们将添加产品功能——向我们的易贝商店添加新产品的能力。敬请期待！

## 分享这篇文章