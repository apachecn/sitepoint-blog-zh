# 休息——除了拼写，你还能做什么？第二部分

> 原文：<https://www.sitepoint.com/rest-can-you-do-more-than-spell-it-2/>

我们中断我们定期安排的系列节目，为您带来这篇关于表述性状态转移的文章，更好的说法是 REST。是的，这是 David Shirey 关于这个主题的系列文章，但是他盛情邀请我撰写这一期文章，并分享一些关于在 PHP 中支持 REST 请求的见解。

在他的系列文章的第一篇文章中，David 解释了 REST 不仅仅是一个架构模式。这是一组指导原则，如果遵循这些原则，可以帮助您编写可伸缩且健壮的应用程序。在接下来的文章中，David 将从客户端的角度重新讨论 REST。在这篇文章中，我想把重点放在服务器端。您将学习如何将您的思维从当今 web 开发中流行的以动作为中心的思维模式转变为一种更加 RESTful、面向资源的方法，并看到一种在 PHP 中构建代码和路由 URI 请求的方法。

## 改变你的心态

在决定按照 REST 原则开发应用程序之后，您应该做的第一件事就是头脑风暴，列出可用的资源。

一种普遍的思考 web 应用程序的方式是关注功能。例如，用 PHP 编写的餐馆评级系统可能有一个`editRestaurant.php`脚本，该脚本提供一个表单来更新一个条目，该表单可能会提交给一个名为`saveRestaurant.php`的脚本。请注意脚本执行的操作是如何作为文件名的一部分公开的。另一方面，REST 关注于资源……在这里是餐馆帖子本身。

用户仍然可以在基于 REST 的评级系统中创建、查看和更新 resturant 条目，但是不能通过像`example.com/viewRestaurant.php?id=42`这样的链接。相反，帖子将通过向`example.com/restaurant`发送 HTTP `POST`请求来创建，由 HTTP `GET`向`example.com/restaurant/42`查看，并由 HTTP `PUT`向`example.com/restaurant/42`更新。不管它们是胖客户端应用程序、网络应用程序、移动应用程序还是其他什么，所有的程序都允许用户完成任务(动作)。RESTful 应用程序没有什么不同，只是核心动作是由使用的协议预先定义的。因为已经定义了操作，所以您可以自由地关注这些操作的目标资源是什么(例如，餐馆)。

在这一点上，你应该只对应用程序的用户公开的资源进行头脑风暴，而不一定是组成你的应用程序的对象。继续评级的例子，系统可以有一个`User`对象来验证应用程序用户，有`Restaurant`和`Comment`对象来验证餐馆和用户提供的评论，甚至可能有一个`RestApp`容器对象和静态便利方法(餐馆应用程序，休息应用程序，看到我的双关语了吗？).用户自己不会与这些对象进行交互，尽管它们没有被归类为资源。

餐馆维护者想要写下关于小餐馆的信息，并批准用户留下的所有精彩评论；读者希望阅读评论并留下评论。如果你是一个敏捷开发商店，那么你可以从你的用户故事中收集一个资源列表。介意猜猜会有什么资源吗？是的…你猜对了！一家餐厅和一个评论！

## 映射资源:从 URIs 到代码

大多数关注 REST 的代码可能会使用某种资源映射。这让您拥有干净的资源 URL，并对您的最终用户隐藏实际的架构和实现，这确实是一件非常好的事情！

常见的做法是将所有请求路由到中央索引或控制器文件，然后根据请求的资源和请求的任何参数或属性从那里调用适当的代码逻辑。如果您使用 LAMP 堆栈，将请求路由到单个入口点就像启用 mod_rewrite 一样简单，允许 htaccess 覆盖，并在 web 目录中放置一个包含以下内容的`.htaccess`文件:

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.php
```

映射到现有文件的所有资源(如图像、CSS 文件等。)由 Apache 直接提供服务。否则将使用`index.php`文件。

为了便于说明，假设您的 web 根目录中有`index.php`、`css/style.css`和`img/logo.png`。阿帕奇将为“T3”提供服务，以响应“T4”的请求；`img/logo.png`将为`example.com/img/logo.png`服务；Apache 将调用`index.php`来处理对`example.com/restaurant`和`example.com/restaurant/42`的请求。

`index.php`文件需要知道它是通过哪个 URL 到达的，知道用户真正在请求什么，对于特定的请求应该执行应用程序中的什么 PHP 代码，以及当请求不能被满足时应该做什么。

到目前为止，我在我的示例 URL 中巧妙使用的自然“层次结构”(如`www.example.com/restaurant/42`)遵循了一个非常常见的模式，您将在基于 REST 的应用程序中看到这个模式。URL ( `/restaurant/42`)的路径很容易被解析并理解为对餐馆资源的请求，特别是由 ID 42 标识的餐馆资源的实例。因此，代码库可以有一个`Restaurant`类，一个适当的实例可以被实例化，并相应地被操作。例如:

```
<?php
// assume autoloader available and configured
$path = parse_url($_SERVER["REQUEST_URI"], PHP_URL_PATH);
$path = trim($path, "/");
@list($resource, $params) = explode("/", $path, 2);

$resource = ucfirst(strtolower($resource));
$method = strtolower($_SERVER["REQUEST_METHOD"]);
$params = !empty($params) ? explode("/", $params) : array();

if (class_exists($resource)) {
    try {
        $resource = new $resource($params);
        $resource->{$method}();
    }
    catch (Exception $e) {
        header("HTTP/1.1 500 Internal Server Error");
    }
}
else {
    header("HTTP/1.1 404 File Not Found");
}
```

这个简单的示例说明了一种将 URL 路径解析为资源及其可能的参数，然后使用这些信息创建与资源同名的类实例的方法。路径中的参数通过对象的构造函数注入到对象中，然后处理请求的责任就交给了对象的方法，该方法与请求它的 HTTP 方法同名。

然而，上述代码的一个缺点是，您受限于对象的浅命名空间层次。如果您希望追求这样的动态调度机制，我建议从右到左解析 URI，并测试是否存在合适的类。这应该允许你在层次结构中有更多的深度。

## 将资源表示为类

您选择的路由策略将影响资源对象的结构。前面的路由代码假设该类将具有`post()`、`get()`、`put()`、`head()`等。定义的方法。因为所有可能的资源对象都必须遵循这个约定，所以定义一个主接口是一个好主意，这个主接口是一个承诺对象将提供特定 API(因此有希望提供特定功能)的契约。

你也可以用标准方法的部分实现写一个抽象类。抽象类本身不能被实例化，必须由具体的类来扩展，但这有助于避免重复的方法定义。经过一点思考，可以为其他资源对象构建一个非常强大和健壮的基础。

```
<?php
abstract class Resource
{
    protected static $httpMethods = array("GET", "POST", "HEAD",
        "PUT", "OPTIONS", "DELETE", "TRACE", "CONNECT"); 

    protected $params;

    public function __construct(array $params) {
        $this->params = $params;
    }

    protected function allowedHttpMethods() {
        $myMethods = array();
        $r = new ReflectionClass($this);
        foreach ($r->getMethods(ReflectionMethod::IS_PUBLIC) as $rm) {
            $myMethods[] = strtoupper($rm->name);
        }
        return array_intersect(self::$httpMethods, $myMethods);

    }

    public function __call($method, $arguments) {
        header("HTTP/1.1 405 Method Not Allowed", true, 405);
        header("Allow: " . join($this->allowedHttpMethods(), ", "));
    }
}
```

HTTP/1.1 规范规定，如果使用了不支持的请求方法，应该向客户端返回 405 代码，并且应该返回有效方法的列表。假设`example.com/restaurant`资源应该支持`GET`和`POST`选项，但显然不支持像`DELETE`这样的选项。对`DELETE`请求的响应应该是这样的:

```
HTTP/1.1 405 Method Not Allowed
Allow: GET, POST
```

抽象类使用神奇的方法`__call()`来捕捉任何未定义的对象方法，然后使用反射来确定具体类支持的 HTTP 方法列表。具体类必须有一个与它处理的 HTTP 方法同名的公共方法，这样它才能工作…这是一个合意的约定。这些类只需要为它们打算支持的 HTTP 请求方法实现这样的函数/方法，而对不支持的请求的错误处理则留给封装在抽象父类中的功能。

扩展了`Resource`的示例`Restaurant`类可能如下所示:

```
<?php
class Restaurant extends Resource
{
    public function __construct($params) {
        parent::__construct($params);
    }

    public function get() {
        // logic to handle an HTTP GET request goes here
        // ...
    }

    public function post() {
        // logic to handle an HTTP POST request goes here
        // ...
    }
}
```

## 结束语

尽管 REST 很棒，但考虑到用户对 web 的期望，纯粹的 REST-full 方法可能并不实用。这个世界是在 HTTP 之上的强制 RPC 风格的范例中成长起来的，我们现在已经被它困住了；这是人们所期望的。他们想要一个漂亮的登录页面，登录/退出功能，等等。这在休息的世界里毫无意义。记住，服务器满足请求所需的所有信息都必须伴随着请求；服务器端会话和跟踪登录/注销不符合 REST 范式。

这并不是说你的基于 REST 的项目一定不安全；存在的策略有 HTTP AUTH、DIGEST AUTH 等。这允许用户凭证伴随请求。我只是说最好理解 REST 的好处和局限性，并将其应用到最有意义的项目中。REST-full 系统不是网站，而是 web 服务，比如发布博客文章的 Atom 发布协议，或者 CouchDB 公开的 API。

我们现在把你带回到已经在进行中的常规系列节目。:)

图片 via[little Sam](http://www.shutterstock.com/gallery-351511p1.html)/[Shutterstock](http://www.shutterstock.com/)

## 分享这篇文章