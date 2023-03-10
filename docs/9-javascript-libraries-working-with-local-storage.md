# 9 个用于本地存储的 JavaScript 库

> 原文：<https://www.sitepoint.com/9-javascript-libraries-working-with-local-storage/>

HTML5 本地存储 API(Web 存储的一部分)拥有[出色的浏览器支持](http://caniuse.com/#feat=namevalue-storage)，正在越来越多的应用中使用。它有一个简单的 API，当然也有它的缺点，类似于 cookies。

在过去一年左右的时间里，我遇到了相当多的使用 localStorage API 的工具和库，所以我将其中的许多工具和库一起编译到这篇文章中，并提供了一些代码示例和功能讨论。

## [Lockr](https://github.com/tsironis/lockr)

Lockr 是 localStorage API 的包装器，允许您使用许多有用的方法和特性。例如，虽然 localStorage 仅限于存储字符串，但 Lockr 允许您存储不同的数据类型，而无需自己进行转换:

```
Lockr.set('website', 'SitePoint'); // string
Lockr.set('categories', 8); // number
Lockr.set('users', [{ name: 'John Doe', age: 18 }, { name: 'Jane Doe', age: 19 }]);
// object
```

其他功能包括:

*   用`Lockr.get()`方法检索所有的键/值对
*   用`Lockr.getAll()`将所有的键/值对编译成一个数组
*   用`Lockr.flush()`删除所有存储的键/值对
*   使用`Lockr.sadd`和`Lockr.srem`添加/删除散列关键字下的值

## [本地存储桥](https://github.com/krasimir/lsbridge)

一个 1KB 的库，使用 localStorage 作为通信通道，便于在同一个浏览器中的选项卡之间交换消息。在包含了这个库之后，下面是一些您可能会用到的示例代码:

```
// send a message
lsbridge.send('my-namespace', { 
  message: 'Hello world!' 
});

// listen for messages
lsbridge.subscribe('my-namespace', function(data) {
  console.log(data); // prints: 'Hello world!'
});
```

如图所示，`send()`方法创建并发送消息，`subscribe()`方法让您监听指定的消息。你可以在[这篇博文](http://krasimirtsonev.com/blog/article/Using-Local-Storage-as-a-communication-channel)中了解更多关于图书馆的信息。

## [子](https://github.com/arokor/barn)子

这个库提供了一个类似于 Redis 的 API，在 localStorage 之上有一个“快速、原子的持久存储层”。下面是一个示例代码片段，摘自 repo 的自述文件。它演示了许多可用的方法。

```
var barn = new Barn(localStorage);

barn.set('key', 'val');
console.log(barn.get('key')); // val

barn.lpush('list', 'val1');
barn.lpush('list', 'val2');
console.log(barn.rpop('list')); // val1
console.log(barn.rpop('list')); // val2

barn.sadd('set', 'val1');
barn.sadd('set', 'val2');
barn.sadd('set', 'val3');
console.log(barn.smembers('set')); // ['val1', 'val2', 'val3']
barn.srem('set', 'val3');
console.log(barn.smembers('set')); // ['val1', 'val2']
```

该 API 的其他特性包括获取具有开始/结束值的范围、获取一组项目以及压缩整个数据存储以节省空间的能力。回购协议对所有方法及其作用有一个完整的引用。

## [store.js](https://github.com/marcuswestin/store.js)

这是另一个包装器，类似于 Lockr，但这次通过回退提供了更深层次的浏览器支持。自述文件解释说,“store.js 在可用时使用 localStorage，并依赖于 IE6 和 IE7 中的 userData 行为。没有 flash 来减缓你的页面加载速度。没有 cookies 来养肥你的网络请求。”

以下代码中的注释解释了基本 API:

```
// Store 'SitePoint' in 'website'
store.set('website', 'SitePoint');

// Get 'website'
store.get('website');

// Remove 'website'
store.remove('website');

// Clear all keys
store.clear();
```

此外，还有一些更高级的功能:

```
// Store an object literal; uses JSON.stringify under the hood
store.set('website', {
  name: 'SitePoint',
  loves: 'CSS'
});

// Get the stored object; uses JSON.parse under the hood
var website = store.get('website');
console.log(website.name + ' loves ' + website.loves);

// Get all stored values
console.log(store.getAll());

// Loop over all stored values
store.forEach(function(key, val) {
  console.log(key, val);
});
```

GitHub repo 上的 README 有许多关于浏览器支持深度的细节，以及需要考虑的潜在错误和陷阱(例如，一些浏览器不允许在私有模式下进行本地存储)。

## [lscache](https://github.com/pamelafox/lscache)

lscache 是另一个本地存储包装器，但是有一些额外的特性。您可以将它用作简单的 localStorage API，也可以使用模拟内存对象缓存系统 [Memcached](http://memcached.org/) 的特性。

lscache 公开了代码注释中描述的以下方法:

```
// set a greeting with a 2 minute expiration
lscache.set('greeting', 'Hello World!', 2);

// get and display the greeting
console.log(lscache.get('greeting'));

// remove the greeting
lscache.remove('greeting');

// flush the entire cache of items
lscache.flush();

// flush only expired items
lscache.flushExpired();
```

与前面的库一样，这个库也负责序列化，因此您可以存储和检索对象:

```
lscache.set('website', {
  'name': 'SitePoint',
  'category': 'CSS'
}, 4);

// retrieve data from the object
console.log(lscache.get('website').name);
console.log(lscache.get('website').category);
```

最后，lscache 允许您将数据划分到“桶”中。看一下这段代码:

```
lscache.set('website', 'SitePoint', 2);
console.log(lscache.get('website')); // 'SitePoint'

lscache.setBucket('other');
console.log(lscache.get('website')); // null

lscache.resetBucket();
console.log(lscache.get('website')); // 'SitePoint'
```

注意在第二个日志中，结果是`null`。这是因为在记录结果之前，我已经设置了一个自定义存储桶。一旦我设置了一个桶，在此之前添加到 lscache 的任何内容都将无法访问，即使我尝试刷新它。只有“其他”桶中的项目是可访问或可冲掉的。然后，当我重置存储桶时，我能够再次访问我的原始数据。

## [secStore.js](https://github.com/jas-/secStore.js)

secStore.js 是一个数据存储 API，它通过斯坦福 Javascript 加密库增加了一个可选的安全层。secStore.js 允许您选择存储方法:localStorage、sessionStorage 或 cookies。要使用 secStore.js，还必须包含前面提到的 [sjcl.js](https://github.com/bitwiseshiftleft/sjcl) 库。

下面是一个示例，演示了如何在将`encrypt`选项设置为“真”的情况下保存一些数据:

```
var storage = new secStore;
var options = {
    encrypt: true,
      data: {
        key: 'data goes here'
      }
    };

storage.set(options, function(err, results) {
  if (err) throw err;
  console.log(results);
});
```

注意正在使用的`set()`方法，它传入您指定的选项(包括定制数据)以及一个回调函数，让您测试结果。然后我们可以使用`get()`方法来检索数据:

```
storage.get(options, function(err, results) {
  if (err) throw err;
  console.log(results.key); // Logs: "data goes here"
});
```

如果您想在 secStore.js 中使用会话存储或 cookies 而不是本地存储，您可以在选项中定义:

```
var options = {
  encrypt: true,
  storage: 'session', // or 'cookies'
  data: {
    key: 'data here'
  }
};
```

## [本地饲料](https://github.com/mozilla/localForage)

这个由 Mozilla 构建的库提供了一个简单的类似 localStorage 的 API，但是通过 IndexedDB 或 WebSQL 使用异步存储。该 API 与 localStorage ( `getItem()`、`setItem()`等)完全相同，只是它的 API 是异步的，并且语法要求使用回调。

例如，无论您设置还是获取一个值，您都不会获得返回值，但是您可以处理传递给回调函数的数据，并且(可选地)处理错误:

```
localforage.setItem('key', 'value', function(err, value) {
  console.log(value);
});

localforage.getItem('key', function(err, value) {
  if (err) {
    console.error('error message...');
  } else {
    console.log(value);
  }
});
```

关于 local feed 的其他几点:

*   支持使用 JavaScript 承诺
*   像其他库一样，不仅限于存储字符串，还可以设置和获取对象
*   允许您使用`config()`方法设置数据库信息

## [Basil.js](http://wisembly.github.io/basil.js/)

Basil.js 被描述为一个统一的 localStorage、sessionStorage 和 cookie API，它包括一些独特且非常易于使用的特性。可以使用的基本方法如下所示:

```
basil = new Basil(options);

basil.set('foo', 'bar');
basil.get('foo');
basil.remove('foo');
basil.reset();
```

您还可以使用 Basil.js 来测试 localStorage 是否可用:

```
basil.check('local'); // returns Boolean value
```

Basil.js 还允许您使用 cookies 或会话存储:

```
basil.cookie.set(key, value, { 
  'expireDays': days, 'domain': 'mydomain.com'
});
basil.cookie.get(key);

basil.sessionStorage.set(key, value);
basil.sessionStorage.get(key);
```

最后，在一个`options`对象中，您可以用一个`options`对象定义以下内容:

*   数据不同部分的命名空间
*   要使用的存储方法的优先级顺序
*   默认存储方法
*   饼干的过期日期。

```
options = {
  namespace: 'foo',
  storages: ['cookie', 'local'],
  storage: 'cookie',
  expireDays: 31
};
```

## [LZ-字符串](http://pieroxy.net/blog/pages/lz-string/index.html)

lz-string 实用程序允许您通过使用压缩在本地存储中存储大量数据，使用起来非常简单。将库包含在页面上后，您可以执行以下操作:

```
var string = 'A string to test our compression.';
console.log(string.length); // 33
var compressed = LZString.compress(string);
console.log(compressed.length); // 18
string = LZString.decompress(compressed);
```

注意`compress()`和`decompress()`方法的使用。上面代码中的注释显示了压缩前后的长度值。看到客户端存储空间总是有限，您会发现这是多么有益。

正如库文档中的[所解释的，可以选择将数据压缩到 uint 8 array(JavaScript 中的一种新数据类型),甚至可以压缩数据以存储在客户端之外。](http://pieroxy.net/blog/pages/lz-string/guide.html)

## 荣誉奖

上述工具可能会帮助您在 localStorage 中做您想做的任何事情，但是如果您还在寻找更多的工具，这里有几个相关的工具和库，您可能想看看。

*   [Loki js](http://lokijs.org/)–node . js、browser 和 Cordova 的快速、内存中面向文档的数据存储。
*   【Angular JS 的客户端存储–Angular JS 的命名空间客户端存储。写入 localStorage，带有 cookie 回退。除了角形核心之外没有外部依赖性；不依赖于 ngCookies。
*   [alasql . js](https://github.com/agershun/alasql)–浏览器和 Node.js 的 JavaScript SQL 数据库。处理传统的关系表和嵌套的 JSON 数据(NoSQL)。从 localStorage、IndexedDB 或 Excel 导出、存储和导入数据。
*   angular-locker–angular 项目中本地/会话存储的一个简单且可配置的抽象，提供了一个功能强大且易于使用的流畅 api。
*   [js cache](https://github.com/mortzdk/jsCache)–支持使用 localStorage 缓存 JavaScript 文件、CSS 样式表和图像。
*   [large local storage](https://github.com/tantaman/LargeLocalStorage)–克服了各种浏览器缺陷，在客户端提供了一个大型键值存储。

## 知道其他的吗？

如果您已经在 localStorage API 或增强客户端存储的相关工具上构建了一些东西，请在评论中告诉我们。

## 分享这篇文章