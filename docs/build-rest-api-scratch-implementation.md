# 从头构建 REST API:实现

> 原文：<https://www.sitepoint.com/build-rest-api-scratch-implementation/>

**我们结束了[本教程的第一部分](https://www.sitepoint.com/best-practices-rest-api-scratch-introduction/)，API 的所有基础层都已就绪。我们有自己的服务器设置、认证系统、JSON 输入/输出、错误管理和几个虚拟路由。但是，最重要的是，我们编写了定义资源和动作的`README`文件。现在是时候处理这些资源了。**

## 创建和更新联系人

我们现在没有数据，所以我们可以从创建联系人开始。当前的 REST 最佳实践建议**创建和更新操作应该返回一个资源表示**。因为本文的核心是 API，所以处理数据库的代码非常基础，可以做得更好。在真实的应用程序中，您可能会使用更健壮的 ORM/模型和验证库。

```
$app->post(
'/contacts',
function () use ($app, $log) {

$body = $app->request()->getBody();

$errors = $app->validateContact($body);

if (empty($errors)) {
$contact = \ORM::for_table('contacts')->create();

if (isset($body['notes'])) {
$notes = $body['notes'];
unset($body['notes']);
}

$contact->set($body);

if (true === $contact->save()) {

// Insert notes
if (!empty($notes)) {
$contactNotes = array();
foreach ($notes as $item) {
$item['contact_id'] = $contact->id;
$note = \ORM::for_table('notes')->create();
$note->set($item);
if (true === $note->save()) {
$contactNotes[] = $note->asArray();
}
}
}

$output = $contact->asArray();
if (!empty($contactNotes)) {
$output['notes'] = $contactNotes;
}
echo json_encode($output, JSON_PRETTY_PRINT);
} else {
throw new Exception("Unable to save contact");
}

} else {
throw new ValidationException("Invalid data", 0, $errors);
}
}
);
```

我们在这里处于`/api/v1`组路线，用`POST`方法处理`/contacts`资源。首先，我们需要请求的主体。我们的中间件确保它是一个有效的 JSON，否则我们就不会在代码的这一点上。方法`$app->validateContact()`确保所提供的数据被净化并执行基本验证；它确保我们至少有一个名字和一个唯一有效的电子邮件地址。我们可以合理地认为 JSON 有效负载可以包含联系人和 notes 数据，所以我同时处理这两种数据。我正在创建一个新的联系人，使用我的 ORM 特定代码，如果成功，我会插入链接的注释，如果有的话。ORM 为我提供了包含来自数据库的 ID 的 contact 和 notes 对象，所以最后我生成了一个数组，用 JSON 进行编码。PHP 5.4 版本提供了`JSON_PRETTY_PRINT`选项，对于旧版本，你可以[向谷歌](https://www.google.it/search?q=php+json+pretty+print&oq=php+json+pretty+print)请求替换。

更新联系人的代码非常相似，唯一的区别是我们在处理数据之前测试联系人和注释的存在，验证略有不同。

```
$contact = \ORM::forTable('contacts')->findOne($id);
if ($contact) {
$body = $app->request()->getBody();
$errors = $app->validateContact($body, 'update');

// other stuff here...
}
```

我们可以通过将相同的代码映射到多个方法来进一步优化，例如我将`PUT`和`PATCH`方法映射到相同的代码:

```
$app->map(
'/contacts/:id',
function ($id) use ($app, $log) {

// Update code here...

)->via('PUT', 'PATCH');
```

## 列出联系人

现在我们的数据库中已经有了一些联系人，是时候进行列表和筛选了。让我们从简单的开始:

```
// Get contacts
$app->get(
'/contacts',
function () use ($app, $log) {

$contacts = array();
$results = \ORM::forTable('contacts');

$contacts = $results->findArray();
echo json_encode($contacts, JSON_PRETTY_PRINT);
}
);
```

检索数据的语句取决于您的 ORM。Idiorm 使它变得简单，返回一个关联数组或空数组，用 JSON 编码并显示出来。在出现错误或异常的情况下，我们之前编写的 JSON 中间件会捕捉异常并将其转换成 JSON。但是让我们把它复杂化一点…

### 字段、过滤器、排序和搜索

一个好的 API 应该允许我们限制检索的字段，对结果进行排序，并使用基本的过滤器或搜索查询。例如，URL:

```
/api/v1/contacts?fields=firstname,email&sort=-email&firstname=Viola&q=vitae
```

我应该返回所有名为“Viola”的联系人，其中名字`OR`电子邮件地址包含字符串`vitae`，他们应该按字母顺序降序电子邮件地址(`-email`)排序，我只想要`firstname`和`email`字段。我们如何做到这一点？

```
$app->get(
'/contacts',
function () use ($app, $log) {

$contacts = array();
$filters = array();
$total = 0;

// Default resultset
$results = \ORM::forTable('contacts');

// Get and sanitize filters from the URL
if ($rawfilters = $app->request->get()) {
unset(
$rawfilters['sort'],
$rawfilters['fields'],
$rawfilters['page'],
$rawfilters['per_page']
);
foreach ($rawfilters as $key => $value) {
$filters[$key] = filter_var($value, FILTER_SANITIZE_STRING);
}

}

// Add filters to the query
if (!empty($filters)) {
foreach ($filters as $key => $value) {
if ('q' == $key) {
$results->whereRaw(
'(`firstname` LIKE ? OR `email` LIKE ?)',
array('%'.$value.'%', '%'.$value.'%')
);
} else {
$results->where($key,$value);
}
}

}

// Get and sanitize field list from the URL
if ($fields = $app->request->get('fields')) {
$fields = explode(',', $fields);
$fields = array_map(
function($field) {
$field = filter_var($field, FILTER_SANITIZE_STRING);
return trim($field);
},
$fields
);
}

// Add field list to the query
if (is_array($fields) && !empty($fields)) {
$results->selectMany($fields);
}

// Manage sort options
if ($sort = $app->request->get('sort')) {
$sort = explode(',', $sort);
$sort = array_map(
function($s) {
$s = filter_var($s, FILTER_SANITIZE_STRING);
return trim($s);
},
$sort
);
foreach ($sort as $expr) {
if ('-' == substr($expr, 0, 1)) {
$results->orderByDesc(substr($expr, 1));
} else {
$results->orderByAsc($expr);
}
}
}

// Pagination logic
$page = filter_var(
$app->request->get('page'),
FILTER_SANITIZE_NUMBER_INT
);
if (!empty($page)) {

$perPage = filter_var(
$app->request->get('per_page'),
FILTER_SANITIZE_NUMBER_INT
);
if (empty($perPage)) {
$perPage = 10;
}

// Total after filters and
// before pagination limit
$total = $results->count();

// Pagination "Link" headers go here...

$results->limit($perPage)->offset($page * $perPage - $perPage);
}

$contacts = $results->findArray();

// ORM fix needed
if (empty($total)) {
$total = count($contacts);
}
$app->response->headers->set('X-Total-Count', $total);

echo json_encode($contacts, JSON_PRETTY_PRINT);
}
);
```

首先我定义了一个默认的结果集(所有联系人)，然后我将完整的查询字符串参数提取到`$rawfilters`数组中，取消键`fields`、`sort`、`page`和`per_page`的设置，稍后我会处理它们。我整理键和值以获得最终的`$filters`数组。然后使用 ORM 特定的语法将过滤器应用于查询。我对字段列表和排序选项做了同样的事情，将片段添加到我们的结果集查询中。只有这样，我才能用`findArray()`运行查询并返回结果。

### 分页逻辑和标题

提供一种方法来限制返回的数据是一个好主意。在上面的代码中，我提供了`page`和`per_page`参数。验证之后，可以将它们传递给 ORM 来过滤结果:

```
$results->limit($perPage)->offset(($page * $perPage) - $perPage);
```

在此之前，我获得了总结果的计数，因此我可以设置`X-Total-Count` HTTP 头。现在我可以计算链接头来发布分页 URL，如下所示:

```
Link: <https://mycontacts.dev/api/v1/contacts?page=2&per_page=5>; rel="next",<https://mycontacts.dev/api/v1/contacts?page=20&per_page=5>; rel="last"
```

分页 URL 是使用实际清理的参数计算的:

```
$linkBaseURL = $app->request->getUrl()
. $app->request->getRootUri()
. $app->request->getResourceUri();

// Adding fields
if (!empty($fields)) {
$queryString[] = 'fields='
. join(
',',
array_map(
function($f){
return urlencode($f);
},
$fields
)
);
}

// Adding filters
if (!empty($filters)) {
$queryString[] = http_build_query($filters);
}

// Adding sort options
if (!empty($sort)) {
$queryString[] = 'sort='
. join(
',',
array_map(
function($s){
return urlencode($s);
},
$sort
)
);
}

if ($page < $pages) {
$next = $linkBaseURL . '?' . join(
'&',
array_merge(
$queryString,
array(
'page=' . (string) ($page + 1),
'per_page=' . $perPage
)
)
);
$links[] = sprintf('<%s>; rel="next"', $next);
}
```

首先，我计算资源的当前基本 URL，然后将字段、过滤器和排序选项添加到查询字符串中。最后，我通过连接分页参数构建了完整的 URL。

## 联系方式和自动加载

此时，获取单个联系人的详细信息非常容易:

```
$app->get(
'/contacts/:id',
function ($id) use ($app, $log) {

// Validate input code here...

$contact = \ORM::forTable('contacts')->findOne($id);
if ($contact) {
echo json_encode($contact->asArray(), JSON_PRETTY_PRINT);
return;
}
$app->notFound();
}
);
```

我们尝试一个简单的 ORM 查询，并对结果(如果有的话)或 404 错误进行编码。但是我们可以更进一步。对于联系人创建，我们可能需要联系人和注释，这是足够合理的，因此我们可以使用查询字符串参数触发此选项，而不是进行多次调用，例如:

```
https://mycontacts.dev/api/v1/contacts/1?embed=notes
```

我们可以将代码编辑为:

```
// ...
if ($contact) {

$output = $contact->asArray();

if ('notes' === $app->request->get('embed')) {
$notes = \ORM::forTable('notes')
->where('contact_id', $id)
->orderByDesc('id')
->findArray();

if (!empty($notes)) {
$output['notes'] = $notes;
}
}

echo json_encode($output, JSON_PRETTY_PRINT);
return;
}
// ...
```

如果我们有一个有效的联系人和一个请求笔记的`embed`参数，我们运行另一个查询，按照 id(或日期或任何我们想要的)的逆序搜索链接的笔记。有了全功能的 ORM/Model 结构，我们可以而且应该对数据库进行一次查询，以提高性能。

## 贮藏

缓存对于我们应用程序的性能非常重要。一个好的 API 至少应该允许使用 HTTP 协议的缓存框架进行客户端缓存。在这个例子中，我将使用`ETag`，除此之外，我们将使用 APC 添加一个简单的内部缓存层。所有这些特性都是由中间件支持的。一年前 [Tim 在 Sitepoint 上写了一篇关于瘦中间件](https://www.sitepoint.com/working-with-slim-middleware/)的文章，作为例子，他编写了一个缓存中间件。我已经为我们的`API\Middleware\Cache`对象扩展了他的代码。中间件是在应用程序的引导阶段以标准方式添加的:

```
$app->add(new API\Middleware\Cache('/api/v1'));
```

缓存构造函数接受一个根 URI 作为参数，所以我们可以在 main 方法中从`/api/v1`及其子路径激活缓存。

```
public function __construct($root = '')
{
$this->root = $root;
$this->ttl = 300; // 5 minutes

}
```

我们还设置了 5 分钟的默认 TTL，稍后可以用`$app->config()`实用程序方法覆盖它。

```
// Cache middleware
public function call()
{
$key = $this->app->request->getResourceUri();
$response = $this->app->response;

if ($ttl = $this->app->config('cache.ttl')) {
$this->ttl = $ttl;
}

if (preg_match('|^' . $this->root . '.*|', $key)) {

// Process cache here...

}

// Pass the game...
$this->next->call();
}
```

初始缓存键是资源 URI。如果它与我们的根不匹配，我们就把动作传递给下一个中间件。下一个十字路口是 HTTP 方法:我们希望在更新方法(PUT、POST 和 PATCH)上清理缓存，并在 GET 请求上读取缓存:

```
$method = strtolower($this->app->request->getMethod());
if ('get' === $method) {
// Process cache here...
} else {
if ($response->status() == 200) {
$response->headers->set(
'X-Cache',
'NONE'
);
$this->clean($key);
}
}
```

如果已经执行了成功的写操作，我们为匹配的密钥清理缓存。实际上，`clean()`方法将清理所有键以`$key`开始的对象。如果请求是 GET，缓存引擎开始工作。

```
if ('get' === $method) {
$queryString = http_build_query($this->app->request->get());
if (!empty($queryString)) {
$key .= '?' . $queryString;
}

$data = $this->fetch($key);
if ($data) {

// Cache hit... return the cached content
$response->headers->set(
'Content-Type',
'application/json'
);
$response->headers->set(
'X-Cache',
'HIT'
);
try {

$this->app->etag($data['checksum']);
$this->app->expires($data['expires']);
$response->body($data['content']);
} catch (\Slim\Exception\Stop $e) {
}
return;
}

// Cache miss... continue on to generate the page
$this->next->call();

if ($response->status() == 200) {

// Cache result for future look up
$checksum = md5($response->body());
$expires = time() + $this->ttl;

$this->save(
$key,
array(
'checksum' => $checksum,
'expires' => $expires,
'content' => $response->body(),
)
);

$response->headers->set(
'X-Cache',
'MISS'
);
try {
$this->app->etag($checksum);
$this->app->expires($expires);
} catch (\Slim\Exception\Stop $e) {
}
return;
}

} else {
// other methods...
}
```

首先我计算完整的键，它是包括查询字符串的资源 URI，然后我在缓存中搜索它。如果有缓存数据(缓存命中)，它们是由到期日期、md5 校验和以及实际内容组成的关联数组的形式。前两个值用于`Etag`和`Expires`头，内容填充响应体，方法返回。在 Slim 中，`$app->etag()`方法处理来自客户端的类型为`If-None-Match`的头，返回一个`304 Not Modified`状态码。

如果没有缓存的数据(缓存未命中)，动作被传递给另一个中间件，响应被正常处理。我们的缓存中间件在渲染之前再次被调用(像洋葱一样，还记得吗？)，这次是经过处理的响应。如果最终响应是有效的(状态 200)，它将被保存在缓存中以供重用，然后被发送到客户端。

## 休息率限制

在为时已晚之前，我们应该有办法限制客户对我们的 API 的调用。这里，另一个中间件帮助了我们。

```
$app->add(new API\Middleware\RateLimit('/api/v1'));
```

```
public function call()
{
$response = $this->app->response;
$request = $this->app->request;

if ($max = $this->app->config('rate.limit')) {
$this->max = $max;
}

// Activate on given root URL only
if (preg_match('|^' . $this->root . '.*|', $this->app->request->getResourceUri())) {

// Use API key from the current user as ID
if ($key = $this->app->user['apikey']) {

$data = $this->fetch($key);
if (false === $data) {

// First time or previous perion expired,
// initialize and save a new entry

$remaining = ($this->max -1);
$reset = 3600;

$this->save(
$key,
array(
'remaining' => $remaining,
'created' => time()
),
$reset
);
} else {

// Take the current entry and update it

$remaining = (--$data['remaining'] >= 0)
? $data['remaining'] : -1;

$reset = (($data['created'] + 3600) - time());

$this->save(
$key,
array(
'remaining' => $remaining,
'created' => $data['created']
),
$reset
);
}

// Set rating headers

$response->headers->set(
'X-Rate-Limit-Limit',
$this->max
);

$response->headers->set(
'X-Rate-Limit-Reset',
$reset
);

$response->headers->set(
'X-Rate-Limit-Remaining',
$remaining
);

// Check if the current key is allowed to pass
if (0 > $remaining) {

// Rewrite remaining headers
$response->headers->set(
'X-Rate-Limit-Remaining',
0
);

// Exits with status "429 Too Many Requests" (see doc below)
$this->fail();
}

} else {
// Exits with status "429 Too Many Requests" (see doc below)
$this->fail();
}

}

$this->next->call();

}
```

我们允许传递根路径，并且像缓存中间件一样，我们可以从应用程序配置中设置其他参数，如`rate.limit`。这个中间件使用由认证层创建的`$app->user`上下文；用户 API 密钥用作 APC 缓存的密钥。如果我们没有找到给定键的数据，我们就生成它们:我存储剩余的调用，值创建的时间戳，我给它一个小时的 TTL。如果 APC 中有数据，我将重新计算剩余的呼叫并保存更新的值。

然后我设置`X-Rate-Limit–*`头(这是惯例，不是标准),如果用户没有剩余的呼叫，我会将`X-Rate-Limit-Remaining`重置为零，并以`429 Too Many Requests`状态码失败。这里有一个小的变通方法:我不能使用 Slim 的`$app->halt()`方法来输出错误，因为 2.4 之前的 Apache 版本不支持状态代码`429`并将它悄悄地转换成`500`错误。所以中间件使用自己的`fail()`方法:

```
protected function fail()
{
header('HTTP/1.1 429 Too Many Requests', false, 429);

// Write the remaining headers
foreach ($this->app->response->headers as $key => $value) {
header($key . ': ' . $value);
}
exit;
}
```

该方法向客户端输出一个原始标头，并且由于标准响应流被中断，因此它输出该响应先前生成的所有标头。

## 我们将何去何从？

我们在这里已经讨论了很多东西，并且我们有我们的基本 API，它尊重公共的最佳实践，但是仍然有许多我们可以添加的改进。例如:

*   使用更健壮的 ORM/模型进行数据访问
*   使用注入到模型中的单独的验证库
*   使用依赖注入来利用其他键/值存储引擎，而不是 APC
*   用 [Swagger](https://helloreverb.com/developers/swagger) 和类似的工具建立一个发现服务和游乐场
*   用[共同异常](http://codeception.com/docs/10-WebServices)构建一个测试套件层

完整的源代码可以在[这里](https://github.com/sitepoint-examples/REST)找到。一如既往，我鼓励您尝试使用示例代码来找到并分享您的解决方案。编码快乐！

## 分享这篇文章