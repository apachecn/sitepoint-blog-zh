# 10 个客户端存储选项以及何时使用它们

> 原文：<https://www.sitepoint.com/client-side-storage-options-comparison/>

在浏览器中存储和操作数据——也称为客户端存储——在没有必要或不切实际地将数据发送到 web 服务器时非常有用。

在浏览器中存储和操作数据的情况包括:

*   保留客户端应用程序的状态，例如当前屏幕、输入的数据、用户偏好等。
*   访问本地数据或文件并有严格隐私要求的实用程序
*   离线工作的渐进式网络应用(PWAs)

以下是存储浏览器数据的十个选项:

1.  [JavaScript 变量](#toc_1)
2.  [DOM 节点存储](#toc_2)
3.  [网络存储](#toc_3) ( `localStorage`和`sessionStorage`)
4.  [索引编制单位](#toc_4)
5.  [缓存 API](#toc_5) (不要用 [AppCache](#toc_6) ！)
6.  [文件系统访问 API](#toc_7)
7.  [文件和目录条目 API](#toc_8)
8.  [饼干](#toc_9)
9.  [T2`window.name`](#toc_10)
10.  [WebSQL](#toc_11)

本文研究了在浏览器中存储数据的十种不同方式，包括它们的局限性、优缺点以及每种技术的最佳用途。

在我们浏览选项之前，有一个关于数据持久性的快速提示…

## 数据持久性

通常，您存储的数据将是:

1.  *持久*:它一直存在，直到你的代码选择删除它，或者
2.  *volatile* :它一直保持到浏览器会话结束，通常是当用户关闭标签页时

现实更加微妙。

用户、操作系统、浏览器或插件可以随时阻止或删除持久数据。当接近分配给该存储类型的容量时，浏览器可以决定删除较旧或较大的项目。

浏览器也记录页面状态。您可以离开网站，单击“后退”或“关闭”并重新打开选项卡；页面*应该*看起来一样。仅被视为会话的变量和数据仍然可用。

## 1.JavaScript 变量

| 公制的 | 评论 |
| --- | --- |
| 容量 | 没有严格的限制，但当您填满内存时，浏览器可能会变慢或崩溃 |
| 读/写速度 | 最快的选择 |
| 坚持 | 差:数据被浏览器刷新擦除 |

将状态存储在 JavaScript 变量中是最快也是最简单的选择。我肯定你不需要一个例子，但是…

```
const
  a = 1,
  b = 'two',
  state = {
    msg:  'Hello',
    name: 'Craig'
  };
```

优势:

*   使用方便
*   快的
*   不需要序列化或反序列化

缺点:

*   易碎:刷新或关闭标签会擦除所有内容
*   第三方脚本可以检查或覆盖全局(`window`)值

你已经在使用变量了。当[页面卸载](https://developer.mozilla.org/docs/Web/API/Window/beforeunload_event)时，您可以考虑永久存储变量状态。

## 2.DOM 节点存储

| 公制的 | 评论 |
| --- | --- |
| 容量 | 没有严格的限制，但对于大量数据并不理想 |
| 读/写速度 | 快的 |
| 坚持 | 差:数据可能被其他脚本或刷新清除 |

大多数 DOM 元素，无论是在页面上还是在内存中，都可以在命名属性中存储值。使用以`data-`为前缀的属性名称更安全:

1.  该属性永远不会有关联的 HTML 功能
2.  您可以通过一个 [`dataset`属性](https://developer.mozilla.org/docs/Web/API/HTMLOrForeignElement/dataset)而不是更长的 [`.setAttribute()`](https://developer.mozilla.org/docs/Web/API/Element/setAttribute) 和 [`.getAttribute()`](https://developer.mozilla.org/docs/Web/API/Element/getAttribute) 方法来访问值。

值存储为字符串，因此可能需要序列化和反序列化。例如:

```
// locate <main> element
const main = document.querySelector('main');

// store values
main.dataset.value1 = 1;
main.dataset.state = JSON.stringify({ a:1, b:2 });

// retreive values
console.log( main.dataset.value1 ); // "1"
console.log( JSON.parse(main.dataset.state).a ); // 1
```

优势:

*   您可以在 JavaScript 或 HTML 中定义值，例如`<main data-value1="1">`
*   用于存储特定组件的状态
*   DOM 很快！(与流行观点相反)

缺点:

*   脆弱:刷新或关闭标签会清除所有内容(除非一个值被服务器呈现到 HTML 中)
*   仅字符串:需要序列化和反序列化
*   较大的 DOM 会影响性能
*   第三方脚本可以检查或覆盖值

DOM 节点存储比变量慢。在用 HTML 存储组件状态比较实际的情况下，尽量少用它。

## 3.网络存储(`localStorage`和`sessionStorage`)

| 公制的 | 评论 |
| --- | --- |
| 容量 | 每个域 5MB |
| 读/写速度 | 同步操作:可能很慢 |
| 坚持 | 数据会一直保留，直到被清除 |

[Web 存储](https://developer.mozilla.org/docs/Web/API/Web_Storage_API)提供了两个类似的 API 来定义名称/值对。使用:

1.  `window.localStorage`存储持久数据，以及
2.  `window.sessionStorage`在浏览器标签保持打开时保留仅会话数据(但请参见[数据持久性](#toc_0)

用 [`.setItem()`](https://developer.mozilla.org/docs/Web/API/Storage/setItem) 存储或更新命名项:

```
localStorage.setItem('value1', 123);
localStorage.setItem('value2', 'abc');
localStorage.setItem('state', JSON.stringify({ a:1, b:2, c:3 }));
```

用[取回。`getItem()`](https://developer.mozilla.org/docs/Web/API/Storage/getItem) :

```
const state = JSON.parse( localStorage.getItem('state') );
```

并用[删除它们。`removeItem()`](https://developer.mozilla.org/docs/Web/API/Storage/removeItem) :

```
localStorage.removeItem('state')
```

其他属性和方法包括:

*   [`.length`](https://developer.mozilla.org/docs/Web/API/Storage/length) :存储的物品数量
*   [`.key(N)`](https://developer.mozilla.org/docs/Web/API/Storage/key) :第 n 个键的名称
*   [`.clear()`](https://developer.mozilla.org/docs/Web/API/Storage/clear) :删除所有存储的项目

更改任何值都会在连接到同一域的其他浏览器选项卡/窗口中引发存储事件。您的应用程序可以做出相应的响应:

```
window.addEventListener('storage', s => {

  console.log(`item changed: ${ s.key }`);
  console.log(`from value  : ${ s.oldValue }`);
  console.log(`to new value: ${ s.newValue }`);

});
```

优势:

*   简单名称/值对 API
*   会话和持久存储选项
*   良好的浏览器支持

缺点:

*   仅字符串:需要序列化和反序列化
*   没有事务、索引或搜索的非结构化数据
*   同步访问会影响大型数据集的性能

网络存储是更简单、更小、特别的价值的理想选择。这对于存储大量结构化信息来说不太实际，但是您可以通过在[页面卸载](https://developer.mozilla.org/docs/Web/API/Window/beforeunload_event)时写入数据来避免性能问题。

## 4.索引 b

| 公制的 | 评论 |
| --- | --- |
| 容量 | 取决于设备。至少 1GB，但最多可达剩余磁盘空间的 60% |
| 读/写速度 | 快的 |
| 坚持 | 数据会一直保留，直到被清除 |

IndexedDB 提供了一个类似 NoSQL 的低级 API，用于存储大量数据。可以使用事务对存储进行索引、更新，并使用异步方法进行搜索。

IndexedDB API 很复杂，需要一些事件杂耍。以下函数在传递了名称、版本号和可选的升级函数(在版本号更改时调用)时打开数据库连接:

```
// connect
function dbConnect(dbName, version, upgrade) {

  return new Promise((resolve, reject) => {

    const request = indexedDB.open(dbName, version);

    request.onsuccess = e => {
      resolve(e.target.result);
    };

    request.onerror = e => {
      console.error(`indexedDB error: ${ e.target.errorCode }`);
    };

    request.onupgradeneeded = upgrade;

  });

}
```

下面的代码连接到一个`myDB`数据库并初始化一个`todo`对象存储(类似于一个 SQL 表或 MongoDB 集合)。然后它定义了一个名为`id`的自动递增键:

```
(async () => {

  const db = await dbConnect('myDB', 1.0, e => {

    db = e.target.result;
    const store = db.createObjectStore('todo', { keyPath: 'id', autoIncrement: true });

  })

})();
```

一旦`db`连接就绪，您就可以在事务中`.add`新的数据项:

```
db.transaction(['todo'], 'readwrite')
  .objectStore('todo')
  .add({ task: 'do something' })
  .onsuccess = () => console.log( 'added' );
```

并且您可以检索值，例如第一项:

```
db.transaction(['todo'], 'readonly')
  .objectStore('todo')
  .get(1)
  .onsuccess = data => console.log( data.target.result );
  // { id: 1, task: 'do something' }
```

优势:

*   具有最大空间的灵活数据存储
*   强大的事务、索引和搜索选项
*   良好的浏览器支持

缺点:

*   复杂的回调和基于事件的 API

IndexedDB 是可靠存储大量数据的最佳选择，但是您可能希望使用包装器库，例如 [idb](https://www.npmjs.com/package/idb) 、 [Dexie.js](https://dexie.org/) 或 [JsStore](https://jsstore.net/) 。

## 5.缓存 API

| 公制的 | 评论 |
| --- | --- |
| 容量 | 取决于设备，但 Safari 将每个域限制为 50MB |
| 读/写速度 | 快的 |
| 坚持 | 数据会一直保留，直到被清除或在 Safari 中保留两周后 |

[缓存 API](https://developer.mozilla.org/docs/Web/API/Cache) 为 HTTP 请求和响应对象对提供存储。您可以创建任意数量的命名缓存来存储任意数量的网络数据项。

该 API 通常用于服务人员缓存渐进式 web 应用程序的网络响应。当设备与网络断开连接时，缓存的资产可以重新提供，因此 web 应用程序可以离线运行。

以下代码将网络响应存储在名为`myCache`的缓存中:

```
// cache name
const cacheName = 'myCache';

(async () => {

  // cache network response
  const stored = await cacheStore('/service.json') );
  console.log(stored ? 'stored OK' : 'store failed');

})();

// store request
async function cacheStore( url ) {

  try {

    // open cache
    const cache = await caches.open( cacheName );

    // fetch and store response
    await cache.add( url );
    return true;

  }
  catch(err) {
    return undefined; // store failed
  }

}
```

一个类似的函数可以从缓存中检索一个项目。在本例中，它返回响应正文文本:

```
(async () => {

  // fetch text from cached response
  const text = await cacheGet('/service.json') );
  console.log( text );

})();

async function cacheGet( url ) {

  try {

    const

      // open cache
      cache = await caches.open( cacheName ),

      // fetch stored response
      resp = await cache.match(url);

    // return body text
    return await resp.text();

  }
  catch(err) {
    return undefined; // cache get failed
  }

}
```

优势:

*   存储任何网络响应
*   可以提高 web 应用程序的性能
*   允许 web 应用程序脱机工作
*   一个现代的基于承诺的 API

缺点:

*   对于存储应用程序状态不实用
*   在渐进式网络应用程序之外可能没那么有用
*   苹果对 PWAs 和缓存 API 并不友好

缓存 API 是存储从网络上检索的文件和数据的最佳选择。你可以*或许*用它来存储应用程序状态，但是它不是为这个目的而设计的，有更好的选择。

## 5.5 AppCache

[AppCache](https://developer.mozilla.org/docs/Web/HTML/Using_the_application_cache) 是[缓存 API](#toc_5) 的前任。这不是您想要的存储解决方案。这里没什么可看的。请往前走。

## 6.文件系统访问 API

| 公制的 | 评论 |
| --- | --- |
| 容量 | 取决于剩余的磁盘空间 |
| 读/写速度 | 取决于文件系统 |
| 坚持 | 数据会一直保留，直到被清除 |

[文件系统访问 API](https://developer.mozilla.org/docs/Web/API/File_System_Access_API) 允许浏览器读取、写入、修改和删除本地文件系统中的文件。浏览器在沙盒环境中运行，因此用户必须授予对特定文件或目录的权限。这将返回一个[文件系统句柄](https://developer.mozilla.org/docs/Web/API/FileSystemHandle)，因此 web 应用程序可以像桌面应用程序一样读写数据。

下面的函数将一个 [Blob](https://developer.mozilla.org/docs/Web/API/Blob) 保存到一个本地文件:

```
async function save( blob ) {

  // create handle to a local file chosen by the user
  const handle = await window.showSaveFilePicker();

  // create writable stream
  const stream = await handle.createWritable();

  // write the data
  await stream.write(blob);

  // save and close the file
  await stream.close();
}
```

优势:

*   web 应用程序可以安全地读写本地文件系统
*   较少需要在服务器上上传文件或处理数据
*   渐进式网络应用程序的一个伟大特性

缺点:

*   最低限度的浏览器支持(仅 Chrome)
*   API 可能会改变

这种存储方式最让我兴奋，但是你需要等几年才能让它适用于生产。

## 7.文件和目录条目 API

| 公制的 | 评论 |
| --- | --- |
| 容量 | 取决于剩余的磁盘空间 |
| 读/写速度 | 未知的 |
| 坚持 | 数据会一直保留，直到被清除 |

[文件和目录条目 API](https://developer.mozilla.org/docs/Web/API/File_and_Directory_Entries_API) 提供了一个沙盒文件系统，可用于创建、写入、读取和删除目录和文件的域。

优势:

*   可能有一些有趣的用途

缺点:

*   实现之间的非标准、不兼容性和行为可能会改变。

MDN 明确声明: *<q>不要在生产现场使用这个</q>* 。广泛的支持最多需要几年时间。

## 8.饼干

| 公制的 | 评论 |
| --- | --- |
| 容量 | 每个域 80Kb 个 cookies，每个最多 4Kb) |
| 读/写速度 | 快的 |
| 坚持 | 好:数据会一直保留，直到被擦除或过期 |

Cookies 是特定于域的数据。它们以跟踪人而闻名，但是它们对于任何需要维护服务器状态的系统都是必不可少的——比如登录。与其他存储机制不同，cookies(通常)会在每个 HTTP 请求和响应时在浏览器和服务器之间传递。两台设备都可以检查、修改和删除 cookie 数据。

[`document.cookie`](https://developer.mozilla.org/docs/Web/API/Document/cookie) 在客户端 JavaScript 中设置 cookie 值。必须用等号(`=`)分隔名称和值来定义字符串。例如:

```
document.cookie = 'cookie1=123';
document.cookie = 'anothercookie=abc';
```

值不得包含逗号、分号或空格，因此 [`encodeURIComponent()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent) 可能是必要的:

```
document.cookie = `hello=${ encodeURIComponent('Hello, everyone!') }`;
```

进一步的 cookie 设置可以附加分号分隔符，包括:

*   `;domain=`:如果没有设置，cookie 只在当前 URL 域上可用。使用`;path=mysite.com`将允许它出现在`mysite.com`的任何子域上。
*   `;path=`:如果没有设置，cookie 只在当前路径和子路径中可用。设置`;path=/`以允许域中的任何路径。
*   `;max-age=`:以秒为单位的 cookie 到期时间——如`;max-age=60`。
*   `;expires=`:一个 cookie 的截止日期——比如`;expires=Thu, 04 July 2021 10:34:38 UTC`(使用 [`date.toUTCString()`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Date/toUTCString) 进行适当的格式化)。
*   cookie 只会在 HTTPS 传送。
*   `;HTTPOnly`:使客户端 JavaScript 无法访问 cookies。
*   `;samesite=`:控制另一个域是否可以访问 cookie。将其设置为`lax`(默认情况下，将 cookie 共享给当前域)、`strict`(在跟踪来自另一个域的链接时停止发送初始 cookie)或`none`(无限制)。

示例:设置一个状态 cookie，该 cookie 将在 10 分钟后过期，并且在当前域中的任何路径上都可用:

```
const state = { a:1, b:2, c:3 };

document.cookie = `state=${ encodeURIComponent(JSON.stringify(state)) }; path=/; max=age=600`;
```

`document.cookie`返回包含由分号分隔的每个名称和值对的字符串。例如:

```
console.log( document.cookie );
// "cookie1=123; anothercookie=abc; hello=Hello%2C%20everyone!; state=%7B%22a%22%3A1%2C%22b%22%3A2%2C%22c%22%3A3%7D"
```

下面的函数解析字符串，并将其转换为包含名称-值对的对象。例如:

```
const
  cookie = cookieParser();
  state = cookie.state;

console.log( state );
// { a:1, b:2, c:3 }

// parse cookie values
function cookieParser() {

  const nameValue = {};

  document.cookie
    .split('; ')
    .map(nv => {

      nv = nv.split('=');
      if (nv[0]) {

        let v = decodeURIComponent( nv[1] || '' );

        try { v = JSON.parse(v); }
        catch(e){}

        nameValue[ nv[0] ] = v;

      }

    })

  return nameValue;

}
```

优势:

*   一种在客户端和服务器之间保持状态的可靠方法
*   限于一个域和一个路径(可选)
*   用`max-age`(秒)或`Expires`(日期)进行自动到期控制
*   默认情况下在当前会话中使用(设置一个过期日期，以便在页面刷新和标签关闭之后保存数据)

缺点:

*   cookie 经常被浏览器和插件屏蔽(它们通常被转换成会话 cookie，这样网站可以继续工作)
*   笨重的 JavaScript 实现(最好创建自己的 cookie 处理程序，或者选择像 [js-cookie](https://www.npmjs.com/package/js-cookie) 这样的库)
*   仅字符串(需要序列化和反序列化)
*   有限的存储空间
*   cookies 可以被第三方脚本检查，除非你[限制访问](https://developer.mozilla.org/docs/Web/HTTP/Cookies#restrict_access_to_cookies)
*   因侵犯隐私而受到指责(地方立法可能要求您出示非必要 cookies 的警告)
*   cookie 数据会附加到每个 HTTP 请求和响应中，这会影响性能(存储 50Kb 的 cookie 数据，然后请求 10 个 1 字节的文件，会占用 1 兆字节的带宽)

除非别无选择，否则不要吃饼干。

## 9.`window.name`

| 公制的 | 评论 |
| --- | --- |
| 容量 | 变化，但几兆字节应该是可能的 |
| 读/写速度 | 快的 |
| 坚持 | 会话数据会一直保留，直到选项卡关闭 |

属性设置并获取窗口浏览上下文的名称。您可以设置一个字符串值，该值在浏览器刷新或链接到其他地方并单击“后退”之间保持不变。例如:

```
let state = { a:1, b:2, c:3 };
window.name = JSON.stringify( state );
```

使用以下方法检查该值:

```
state = JSON.parse( window.name );
console.log( state.b );
// 2
```

优势:

*   使用方便
*   可用于仅会话数据

缺点是:

*   仅字符串:需要序列化和反序列化
*   其他域中的页面可以读取、修改或删除数据(不要将它用于敏感信息)

`window.name`不是为数据存储而设计的。这是一个黑客和*有更好的选择*。

## 10.WebSQL

| 公制的 | 评论 |
| --- | --- |
| 容量 | 每个域 5MB |
| 读/写速度 | 萧条的 |
| 坚持 | 数据会一直保留，直到被清除 |

WebSQL 致力于将类似 SQL 的数据库存储引入浏览器。示例代码:

```
// create DB (name, version, description, size in bytes)
const db = openDatabase('todo', '1.0', 'my to-do list', 1024 * 1024);

// create table and insert first item
db.transaction( t => {

  t.executeSql('CREATE TABLE task (id unique, name)');
  t.executeSql('INSERT INTO task (id,name) VALUES (1, "wash cat")');

});

// output array of all items
db.transaction( t => {

  t.executeSql(
    "SELECT * FROM task",
    [],
    (t, results) => { console.log(results.rows); }
  );

});
```

Chrome 和 Safari 的一些版本支持这项技术，但它遭到了 Mozilla 和微软的反对，而支持 [IndexedDB](#toc_4) 。

优势:

*   专为强大的客户端数据存储和访问而设计
*   服务器端开发人员经常使用的熟悉的 SQL 语法

缺点:

*   有限且有缺陷的浏览器支持
*   跨浏览器的不一致的 SQL 语法
*   异步但笨拙的基于回调的 API
*   性能差

**不要用 WebSQL！**自从该规范在 2010 年被否决后，它就不再是一个可行的选择。

## 仔细检查存储

[存储 API](https://developer.mozilla.org/docs/Web/API/Storage_API) 可以检查 Web 存储、IndexedDB 和缓存 API 的可用空间。除了 Safari 和 IE 之外的所有浏览器都支持基于 Promise 的 API，该 API 提供了一个 [`.estimate()`方法](https://developer.mozilla.org/docs/Web/API/StorageManager/estimate)来计算`quota`(域的可用空间)和`usage`(已用空间)。例如:

```
(async () => {

  if (!navigator.storage) return;

  const storage = await navigator.storage.estimate();

  console.log(`bytes allocated  : ${ storage.quota }`);
  console.log(`bytes in use     : ${ storage.usage }`);

  const pcUsed = Math.round((storage.usage / storage.quota) * 100);
  console.log(`storage used     : ${ pcUsed }%`);

  const mbRemain = Math.floor((storage.quota - storage.usage) / 1024 / 1024);
  console.log(`storage remaining: ${ mbRemain } MB`);

})();
```

还有两种异步方法可用:

*   [`.persist()`](https://developer.mozilla.org/docs/Web/API/StorageManager/persist) :如果站点有存储持久数据的权限，则返回`true`，并且
*   [`.persisted()`](https://developer.mozilla.org/docs/Web/API/StorageManager/persisted) :如果站点已经存储了持久数据，则返回`true`

浏览器开发工具中的**应用**面板(在 Firefox 中命名为**存储**)允许您查看、修改和清除 localStorage、sessionStorage、IndexedDB、WebSQL、cookies 和缓存存储。

您还可以通过单击 developer tools 的 **Network** 面板中的任何项目来检查在 HTTP 请求和响应头中发送的 cookie 数据。

## 存储自助餐

这些存储解决方案没有一个是完美的，您需要在复杂的 web 应用程序中采用几个。这意味着学习更多的 API。但是在每种情况下都有选择是一件好事——当然，前提是你能选择合适的选项！

## 分享这篇文章