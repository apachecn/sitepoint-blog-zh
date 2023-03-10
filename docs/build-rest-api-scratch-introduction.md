# 从头构建 REST API:简介

> 原文：<https://www.sitepoint.com/build-rest-api-scratch-introduction/>

当前的互联网生态系统已经被 API 入侵，而且理由很充分。通过在您的产品或服务中使用第三方 API，您可以获得大量有用的功能，如身份验证或存储服务，这对您和您的用户都有好处。通过公开您自己的 API，您的应用程序成为“组合的一部分”,并将以您从未想过的方式使用……当然，前提是您的方式正确。

在这个由两部分组成的系列文章中，我将向您展示如何为您的 PHP 应用程序创建一个 RESTful API 层，使用一组真实世界的最佳实践。

这个项目的完整源代码将在第 2 部分的末尾提供。

## REST:开发人员喜欢的 UI

首先，一个 API 是[开发者的用户界面](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#requirements)，所以它必须友好、简单、易用，当然也要令人愉悦；否则，它将成为另一个数字垃圾。

文档，即使是简单但写得很好的`README`文件，也是一个很好的起点。我们需要的最基本的信息是服务范围的摘要以及方法和访问点的列表。

一个好的总结可以是:

> 我们的应用程序是一个简单的联系人列表服务，它管理带有链接注释的联系人。它有**两种对象类型**，联系人和便笺。每个联系人都有基本属性，如名字、姓氏和电子邮件地址。此外，每个联系人都可以有许多链接到它的降价格式的笔记。

然后，列出我们将要实施的所有**资源和行动**是一个好主意。这可以被看作是视觉应用的线框图。遵循 REST 的关键原则，每个资源由一个 URL 表示，其中的动作是用于访问它的 HTTP 方法。

例如，`GET /api/contacts/12`用 12 的`id`检索联系人，而`PUT /api/contacts/12`将更新相同的联系人。

方法的完整列表如下所示:

```
URL HTTP Method Operation
/api/contacts GET Returns an array of contacts
/api/contacts/:id GET Returns the contact with id of :id
/api/contacts POST Adds a new contact and return it with an id attribute added
/api/contacts/:id PUT Updates the contact with id of :id
/api/contacts/:id PATCH Partially updates the contact with id of :id
/api/contacts/:id DELETE Deletes the contact with id of :id

/api/contacts/:id/star PUT Adds to favorites the contact with id of :id
/api/contacts/:id/star DELETE Removes from favorites the contact with id of :id

/api/contacts/:id/notes GET Returns the notes for the contact with id of :id
/api/contacts/:id/notes/:nid GET Returns the note with id of :nid for the contact with id of :id
/api/contacts/:id/notes POST Adds a new note for the contact with id of :id
/api/contacts/:id/notes/:nid PUT Updates the note with id if :nid for the contact with id of :id
/api/contacts/:id/notes/:nid PATCH Partially updates the note with id of :nid for the contact with id of :id
/api/contacts/:id/notes/:nid DELETE Deletes the note with id of :nid for the contact with id of :id
```

为了更完整和专业的文档，你可以考虑一些工具，如 [Swagger](https://helloreverb.com/developers/swagger) 、 [apiDoc](http://apidocjs.com) 或[Google APIs Discovery Service](https://developers.google.com/discovery/):你的用户会喜欢你的！

## 工具和设置

我将用来构建我们的 API 的主要工具是 [Slim Framework](http://slimframework.com/) 。为什么？

> [它]帮助您快速编写简单而强大的 web 应用程序和 API。

这是事实。其强大的路由功能使得使用标准`GET`和`POST`之外的方法变得容易，它提供了对 HTTP 方法覆盖的内置支持(通过 HTTP 头和隐藏的 POST 字段)，并且它可以与中间件和额外的功能挂钩，使应用程序和 API 开发变得非常容易。

除了 Slim 之外，我还使用 Idiorm 来访问数据库层，并使用 Monolog 来记录日志。因此我们的`composer.json`文件将如下所示:

```
{
"name": "yourname/my-contacts",
"description": "Simple RESTful API for contacts management",
"license": "MIT",
"authors": [
{
"name": "Your Name",
"email": "you@yourdomain.com"
}
],
"require": {
"slim/slim": "*",
"slim/extras": "*",
"slim/middleware": "*",
"monolog/monolog": "*",
"j4mie/paris": "*",
"flynsarmy/slim-monolog": "*"
},
"archive": {
"exclude": ["vendor", ".DS_Store", "*.log"]
},
"autoload": {
"psr-0": {
"API": "lib/"
}
}
}
```

`slim/extras`和`slim/middleware`包提供了有用的特性，比如内容类型解析和基本认证。我们的定制类在`API`的名称空间下，位于`lib`目录中。

此时，我们的工作目录结构如下所示:

```
bootstrap.php
composer.json
README.md
bin/
    import install
lib/ API/
public/
    .htaccess
    index.php
share/ config/
        default.php
    db/ logs/ sql/ data/ contacts.sql
            users.sql
        tables/ contacts.sql
            notes.sql
            users.sql
        ssl/ mysitename.crt
            mysitename.key
```

我们的应用程序的前端控制器是`public/index.php`，所有非文件或目录的流量都通过标准的 URL 重写规则进行重定向。

然后我将所有的初始化代码放在`bootstrap.php`中，我们稍后会看到。`share`目录包含日志、配置文件、SQLite 数据库和转储文件以及 SSL 证书等数据。`bin`目录包含创建数据库的实用程序脚本，并使用提供的`.sql`文件导入一些数据。

### SSL 无处不在

我们的 API 只能在 HTTPS 模式下访问，没有重定向。这简化了身份验证逻辑，并防止配置不佳的客户端访问未加密的端点。最简单也是最合理的设置方式是直接在 web 服务器上操作，或者通过代理服务器操作。

我使用旧的受信任的 Apache 来完成这项工作，我的虚拟主机文件如下所示:

```
<Directory "/path/to/MyApp/public">

# Required for mod_rewrite in .htaccess
AllowOverride FileInfo

Options All -Indexes

DirectoryIndex index.php index.shtml index.html

<IfModule php5_module>
# For Development only!
php_flag display_errors On
</IfModule>

# Enable gzip compression
<ifModule filter_module>
AddOutputFilterByType DEFLATE application/json
</ifModule>

Order deny,allow
Deny from all
Allow from 127.0.0.1
</Directory>

<VirtualHost *:80>
ServerAdmin you@yourdomain.com
DocumentRoot "/path/to/MyApp/public"
ServerName myapp.dev

<IfModule rewrite_module>
RewriteEngine on

## Throw a 403 (forbidden) status for non secure requests
RewriteCond %{HTTPS} off
RewriteRule ^.*$ - [L,R=403]
</IfModule>

</VirtualHost>

<IfModule ssl_module>

NameVirtualHost *:443

Listen 443
SSLRandomSeed startup builtin
SSLRandomSeed connect builtin

<VirtualHost *:443>
ServerAdmin you@yourdomain.com
DocumentRoot "/path/to/MyApp/public"
ServerName myapp.dev

SSLEngine on
SSLCertificateFile /path/to/MyApp/share/ssl/mysitename.crt
SSLCertificateKeyFile /path/to/MyApp/share/ssl/mysitename.key

SetEnv SLIM_MODE development

</VirtualHost>
</IfModule>
```

首先定义目录设置，这样它们对于我们站点的 HTTP 和 HTTPS 版本都是通用的。在不安全的主机配置中，我使用`mod_rewrite`为任何不安全的连接发出一个`403 Forbidden`错误，然后在安全部分，我使用我的自签名证书和告诉 Slim 当前应用程序模式的`SLIM_ENV`变量设置 SSL。

有关如何创建自签名证书并将其安装在 Apache 上的更多信息，请参见 SSLShopper 上的这篇文章。

现在我们有了清晰的目标、基本的目录结构和服务器设置，让我们运行`composer.phar install`并开始编写一些代码。

## 自举和前端控制器

如前所述，`bootstrap.php`文件负责加载我们的应用程序设置和自动加载器设置。

```
// Init application mode
if (empty($_ENV['SLIM_MODE'])) {
$_ENV['SLIM_MODE'] = (getenv('SLIM_MODE'))
? getenv('SLIM_MODE') : 'development';
}

// Init and load configuration
$config = array();

$configFile = dirname(__FILE__) . '/share/config/'
. $_ENV['SLIM_MODE'] . '.php';

if (is_readable($configFile)) {
require_once $configFile;
} else {
require_once dirname(__FILE__) . '/share/config/default.php';
}

// Create Application
$app = new API\Application($config['app']);
```

首先，我了解当前的环境。如果名为`<EnvName>.php`的文件存在，它被加载，如果不存在，默认配置文件被加载。特定于 Slim 的设置存储在`$config['app']`数组中，并传递给扩展基本 Slim 对象的应用程序的构造函数(可选但推荐)。

例如，以下语句:

```
$config['app']['log.writer'] = new \Flynsarmy\SlimMonolog\Log\MonologWriter(array(
'handlers' => array(
new \Monolog\Handler\StreamHandler(
realpath(__DIR__ . '/../logs')
.'/'.$_ENV['SLIM_MODE'] . '_' .date('Y-m-d').'.log'
),
),
));
```

配置在`app/path/share/logs/EnvName_YYYY-mm-dd.log`写入文件的日志记录器。

然后，经过一些改进(您可以在源代码中看到它们)，我获得生成的日志编写器并尝试连接到数据库:

```
// Get log writer
$log = $app->getLog();

// Init database
try {

if (!empty($config['db'])) {
\ORM::configure($config['db']['dsn']);
if (!empty($config['db']['username']) && !empty($config['db']['password'])) {
\ORM::configure('username', $config['db']['username']);
\ORM::configure('password', $config['db']['password']);
}
}

} catch (\PDOException $e) {
$log->error($e->getMessage());
}

// Add middleware
// Your middleware here...
$app->add(new Some\Middleware\Class(...));
```

最后，我将所需的中间件添加到我的应用程序实例中。Slim 的中间件就像洋葱的各层，你添加的第一个中间件将是最里面的一层，所以我们的中间件的顺序很重要。我在我们的 API 中使用了以下中间件:

*   缓存(最里面)；
*   ContentTypes:从客户端解析 JSON 格式的主体；
*   RateLimit:管理用户 API 限制；
*   JSON:用于“仅 JSON 响应”和“JSON 编码体”最佳实践的实用中间件；
*   认证(最外层)。

除了预存的`ContentTypes`，这些我们都会写。

在引导文件的末尾，我定义了两个全局变量`$app`(应用程序实例)和`$log`(日志编写器)。这个文件是由我们的前端控制器`index.php`加载的，在这里魔法发生了。

## 路由结构

Slim 有一个很好的特性叫做[路由组](http://docs.slimframework.com/#Route-Groups)。通过此功能，我们可以如下定义我们的应用程序路线:

```
// API group
$app->group('/api', function () use ($app, $log) {

// Version group
$app->group('/v1', function () use ($app, $log) {

// GET route
$app->get('/contacts/:id', function ($id) use ($app, $log) {

});

// PUT route, for updating
$app->put('/contacts/:id', function ($id) use ($app, $log) {

});

// DELETE route
$app->delete('/contacts/:id', function ($id) use ($app, $log) {

});

});

});
```

我已经创建了两个嵌套组，`/api`和`/v1`，因此我们可以很容易地遵循“URL 中的版本控制”最佳实践。我还为`/api/`创建了一些可选的路径，可以包含用户可读的内容，以及一个通用的根 URL ( `/` ) URL，在现实世界中可以包含应用程序的公共用户界面。

### JSON 中间件

我最初的方法是在`/v1`组中使用路由中间件(另一种 Slim 的中间件)进行认证和 JSON 请求/响应，但是我发现使用经典中间件更实用、更简洁。如前所述，中间件是从`Slim\Middleware`继承的类的实例。瘦中间件的`call()`方法是动作发生的地方，当中间件被链接为全局时，用`$app->add()`方法自动执行。

```
$app->add(new API\Middleware\JSON('/api/v1'));
```

我们的 JSON 中间件实现了两个最佳实践:“仅 JSON 响应”和“JSON 编码体”。方法如下:

```
// JSON middleware call
public function call()
{
if (preg_match('|^' . $this->root . '.*|', $this->app->request->getResourceUri())) {

// Force response headers to JSON
$this->app->response->headers->set(
'Content-Type',
'application/json'
);

$method = strtolower($this->app->request->getMethod());
$mediaType = $this->app->request->getMediaType();

if (in_array(
$method,
array('post', 'put', 'patch')
)) {

if (empty($mediaType)
|| $mediaType !== 'application/json') {
$this->app->halt(415);
}
}
}
$this->next->call();
}
```

我们可以向中间件构造函数传递一个根路径。在这种情况下，我传递`/api/v1`以便我们的中间件只应用于我们站点的 API 部分。如果当前路径匹配的响应内容类型头被强制为`application/json`，那么我检查请求方法。如果请求方法是可写的方法之一(`PUT`、`POST`、`PATCH`)，那么请求内容类型头必须是`application/json`，否则应用程序以一个`415 Unsupported Media Type` HTTP 状态代码退出。

如果一切正常，语句`$this->next->call()`运行链中的下一个中间件。

## 证明

由于我们的应用程序默认运行在 HTTPS 上，所以我决定使用基于基本认证的令牌方法:在基本 HTTP 认证头的用户名字段中发送一个 API 密钥(不需要密码)。为了做到这一点，我通过修改现有的 Slim `HttpBasicAuth`编写了一个名为`TokenOverBasicAuth`的 Slim 中间件类。这个中间件在链中首先运行，所以它作为最后一个被添加，并且它在构造函数中接受一个可选的根路径参数。

```
// Auth middleware call
public function call()
{
$req = $this->app->request();
$res = $this->app->response();

if (preg_match('|^' . $this->config['root'] . '.*|', $req->getResourceUri())) {

// We just need the user
$authToken = $req->headers('PHP_AUTH_USER');

if (!($authToken && $this->verify($authToken))) {
$res->status(401);
$res->header(
'WWW-Authenticate',
sprintf('Basic realm="%s"', $this->config['realm'])
);
}

}

$this->next->call();
}
```

该方法在`PHP_AUTH_USER`请求头中搜索身份验证令牌，如果它不存在或无效，则将`401 Forbidden`状态和身份验证头传递给客户端。`verify()`方法受到保护，因此它可以被子类覆盖；我的版本很简单:

```
protected function verify($authToken)
{
$user = \ORM::forTable('users')->where('apikey', $authToken)
->findOne();

if (false !== $user) {
$this->app->user = $user->asArray();
return true;
}

return false;
}
```

在这里，我只是检查在`users`表中是否存在 API 键，如果我找到一个有效的用户，它将被添加到应用程序上下文中，用于下一层(RateLimit)。您可以修改或扩展这个类来注入您自己的认证逻辑，或者使用一个 [OAuth 模块](http://www.lornajane.net/posts/2013/oauth-middleware-for-slim)。想了解更多关于 OAuth 的信息，请看杰米·芒罗的文章。

## 消耗性错误有效载荷

我们的 API 应该以可消费的格式显示有用的错误消息，如果可能的话，用 JSON 表示。我们需要一个包含错误代码和消息的最小有效负载。此外，验证错误需要更多的细分。使用 Slim，我们可以分别用`$app->notFound()`和`$app->error()`方法重新定义`404`错误和服务器错误。

```
$app->notFound(function () use ($app) {

$mediaType = $app->request->getMediaType();

$isAPI = (bool) preg_match('|^/api/v.*$|', $app->request->getPath());

if ('application/json' === $mediaType || true === $isAPI) {

$app->response->headers->set(
'Content-Type',
'application/json'
);

echo json_encode(
array(
'code' => 404,
'message' => 'Not found'
)
);

} else {
echo '<html>
<head><title>404 Page Not Found</title></head>
<body><h1>404 Page Not Found</h1><p>The page you are
looking for could not be found.</p></body></html>';
}
});
```

未发现错误更简单:首先我获取请求的媒体类型，然后`$isAPI`标志告诉我当前 URL 是否在`/api/v*`组下。如果客户端请求一个 API URL 或发送一个 JSON 内容类型头，我将返回一个 JSON 输出，否则我可以呈现一个模板或简单地打印一些静态 HTML，如下例所示。

其他错误有点棘手，当出现异常时会触发`$app->error()`方法，Slim 在`ErrorException`对象中转换标准 PHP 错误。为了避免安全缺陷，我们需要一种方法，在不暴露太多内部机制的情况下向客户端提供有用的错误。我为这个应用程序创建了两个定制的异常，一个`API\Exception`和一个`API\Exception\ValidationException`，它们向公众公开，所有其他的异常类型都被记录下来，并且只在开发模式下显示。

```
$app->error(function (\Exception $e) use ($app, $log) {

$mediaType = $app->request->getMediaType();

$isAPI = (bool) preg_match('|^/api/v.*$|', $app->request->getPath());

// Standard exception data
$error = array(
'code' => $e->getCode(),
'message' => $e->getMessage(),
'file' => $e->getFile(),
'line' => $e->getLine(),
);

// Graceful error data for production mode
if (!in_array(
get_class($e),
array('API\\Exception', 'API\\Exception\ValidationException')
)
&& 'production' === $app->config('mode')) {
$error['message'] = 'There was an internal error';
unset($error['file'], $error['line']);
}

// Custom error data (e.g. Validations)
if (method_exists($e, 'getData')) {
$errors = $e->getData();
}

if (!empty($errors)) {
$error['errors'] = $errors;
}

$log->error($e->getMessage());
if ('application/json' === $mediaType || true === $isAPI) {
$app->response->headers->set(
'Content-Type',
'application/json'
);
echo json_encode($error);
} else {
echo '<html>
<head><title>Error</title></head>
<body><h1>Error: ' . $error['code'] . '</h1><p>'
. $error['message']
.'</p></body></html>';
}

});
```

`$app->error()`方法接收抛出的异常作为参数。默认情况下，我获取我需要的所有数据并填充`$error`数组，然后如果我在生产模式下，我会取消私有数据的设置并用一个通用数据重写消息。自定义的`ValidationException`类有一个自定义的`getData()`方法，该方法返回一个添加到最终有效负载中的验证错误数组。然后根据请求用 JSON 或 HTML 显示错误。在 API 端，我们可能会有这样一个简单的错误:

```
{
"code": 123,
"message": "An error occurred, a support person is being notified of this"
}
```

或者像这样的完全验证错误:

```
{
"code": 0,
"message": "Invalid data",
"errors":
{
"contact":
[
{
"field": "email",
"message": "Email address already exists"
}
]
}
}
```

## 结论

我们现在已经有了 API 的核心。在下一部分，我们将添加一些肉，以便有一个完整的功能服务。同时，请随意通读本部分链接的文章——它们是有用的 API 设计原则的宝库。

## 分享这篇文章