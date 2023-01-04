# PouchDB 客户端 JavaScript 数据库入门

> 原文：<https://www.sitepoint.com/getting-started-with-pouchdb/>

*这篇文章由[塞巴斯蒂安·塞茨](https://www.sitepoint.com/community/users/m3g4p0p/activity)和[陶兰特·斯帕希乌](https://www.sitepoint.com/author/tspahiu)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

近年来，客户端 web 应用程序变得越来越复杂。浏览器一直在提供更好的 JavaScript 性能，并且能够做越来越多的事情，为地理定位和点对点通信等提供了丰富的 JavaScript APIs。

富 web 应用程序的兴起也创造了对良好的客户端存储机制的需求，这就是像 [PouchDB](https://pouchdb.com/) 这样的 JavaScript 数据库的用武之地。

## PouchDB 是什么？

> PouchDB 是一个开源的 JavaScript 数据库，受 Apache CouchDB 的启发，旨在很好地在浏览器中运行。

### 什么是 JavaScript 数据库？

简而言之，JavaScript 数据库是一个 JavaScript 对象，它提供了上传、获取和搜索数据的方法(或 API)。事实上，普通的旧 JavaScript 对象是最简单的 JavaScript 数据库。如果你熟悉 [Meteor](https://www.meteor.com) ，那么你可能听说过 [Minimongo](https://guide.meteor.com/collections.html#client-collections) ，它是另一个客户端 JavaScript 数据库，模仿 MongoDB API。

> PouchDB 是 CouchDB 的 JavaScript 实现。它的目标是以近乎完美的保真度模拟 CouchDB API，同时在浏览器或 Node.js 中运行。

PouchDB 与 Minimongo 等数据库的不同之处在于，默认情况下，它是*而不是*在内存中，它在后台使用 [IndexedDB](https://developer.mozilla.org/en/docs/Web/API/IndexedDB_API) 进行存储。IndexedDB 是一个低级 API，用于客户端存储大量结构化数据，包括文件/blob。这意味着 PouchDB 数据存储在磁盘上，即使在页面刷新后仍然可用(但是，一个浏览器存储的数据对其他浏览器不可用)。

不同的[适配器](https://pouchdb.com/adapters.html)让你改变底层的数据存储层。

### 与 CouchDB 的关系

PouchDB 是 CouchDB 的一个 JavaScript 实现，尽可能模拟它的 API。

在 CouchDB 中，您可以使用这个 API 调用获取所有文档

```
/db/_all_docs?include_docs=true 
```

在 PouchDB 中，它变成了

```
db.allDocs({include_docs: true}) 
```

PouchDB 允许应用程序在脱机时在本地存储数据，然后在应用程序重新联机时将数据与 CouchDB 同步。

现在，让我们看看如何在应用程序中使用 PouchDB。

## 装置

要开始使用 PouchDB，您只需要包含 PouchDB 客户端库。您可以使用独立构建，这使得`PouchDB`构造函数在`window`对象上全局可用

```
<script src="https://cdn.jsdelivr.net/pouchdb/5.4.5/pouchdb.min.js"></script> 
```

或者，如果你在 Node.js/browserify/webpack 环境下使用它，你可以用`npm`安装它。

```
$ npm install pouchdb --save 
```

然后在您的 JavaScript 中:

```
var PouchDB = require('pouchdb'); 
```

(好玩的事实:`npm isntall pouchdb`也管用！)

## 使用 PouchDB

### 创建数据库

创建 PouchDB 数据库就像调用 PouchDB 构造函数一样简单。让我们创建一个名为“电影”的数据库。

```
var movies = new PouchDB('Movies'); 
```

运行之后，您可以看到关于您的数据库的基本信息，通过使用`info`方法，它返回一个`Promise`。

```
movies
 .info()
 .then(function (info) {
   console.log(info);
 }) 
```

上面的代码输出以下内容:

```
{"doc_count":0,"update_seq":0,"idb_attachment_format":"binary","db_name":"Movies","auto_compaction":false,"adapter":"idb"} 
```

`adapter`字段表明它正在使用 IndexedDB。

### 使用文档

PouchDB 是一个基于文档的 NoSQL 数据库，因此没有严格的模式，您可以直接插入 JSON 文档。让我们看看如何插入、更新、检索或删除文档。

#### 创建文档

您可以使用`put`方法创建一个新文档

```
// returns a promise
db.put(doc, [docId], [docRev], [options]) 
```

方括号中的参数是可选的。每个文档都有一个与之相关联的`_id`字段，作为唯一的标识符。

通过运行以下代码，在先前创建的`Movies`数据库中创建一个新文档:

```
movies
  .put({
    _id: 'tdkr',
    title: 'The Dark Knight Rises',
    director: 'Christopher Nolan'
  }).then(function (response) {
    console.log("Success", response)
  }).then(function (err) {
    console.log("Error", err)
  }) 
```

如果成功，响应将类似于:

```
Success {ok: true, id: "tdkr", rev: "3-f8afdea539618c3e8dceb20ba1659d2b"} 
```

现在调用`movies.info()`将给出`{doc_count: 1}`以及其他数据，表明我们的文档确实已经被插入。

响应中的`rev`字段表示文档的修订。每个文档都有一个名为`_rev`的字段。每次文档更新时，文档的`_rev`字段都会改变。每个修订版都指向它以前的修订版。PouchDB 维护每个文档的历史(很像 git)。

#### 阅读文件

PouchDB 提供了一个`get` API 方法来通过 ID 检索文档。跑步:

```
movies
  .get('tdkr')
  .then(function(doc) {
    console.log(doc)
  })
  .catch(function (err) {
    console.log(err)
  }) 
```

会给出这样的回答

```
{title: "The Dark Knight Rises", director: "Christopher Nolan", _id: "tdkr", _rev: "3-f8afdea539618c3e8dceb20ba1659d2b"} 
```

#### 更新文档

假设我们想在文档中添加一个“年份”字段。您可以通过运行以下命令来更新上面创建的文档:

```
movies
  .get('tdkr')
  .then(function(doc) {
    doc.year = "2012"    // new field
    console.log(doc._rev) // doc has a '_rev' field
    return db.put(doc)   // put updated doc, will create new revision
  }).then(function (res) {
    console.log(res)
  }) 
```

更新文档时，必须提供一个`_rev`字段。

您应该在控制台中看到类似的输出:

```
{ok: true, id: "tdkr", rev: "4-7a34189fb8f2e28fe08b666e699755b8"} 
```

表示文件的新版本。

#### 删除文档

在 PouchDB 中删除一个文档只是将它的`_deleted`属性设置为`true`。你可以给`.remove()`打电话:

```
movies
  .get('tdkr')
  .then(function(doc) {
    return movies.remove(doc) // return the promise
  }).then(function(res) {
    console.log("Remove operation response", res)
  }) 
```

这相当于

```
movies
  .get('tdkr')
  .then(function (doc) {
    doc._deleted = true
    return db.put(doc)
  })
  .then(...) 
```

### 删除数据库

您可以通过调用 db 对象上的`destroy()`来删除数据库。

```
// returns a promise
movies.destroy() 
```

## 批量操作

到目前为止，我们一直在 PouchDB 中处理单个文档。然而，它也提供了 API 来处理一组文档。PouchDB 为批量操作提供了两种方法——`bulkDocs()`用于批量写入，而`allDocs()`用于批量读取。

`bulkDocs()`方法非常简单。它只接受一组您想要插入数据库的文档。

### 插入多个文档

```
// Returns a promise
movies.bulkDocs([
  {
    _id: 'easy-a',
    title: "Easy A",
    // other attribues
  },
  {
    _id: 'black-swan',
    title: 'Black Swan',
    // ...
  }
]) 
```

样本响应:

```
[
  {
    "ok": true,
    "id": "easy-a",
    "rev": "1-84abc2a942007bee7cf55007cba56198"
  },
  {
    "ok": true,
    "id": "black-swan",
    "rev": "1-7b80fc50b6af7a905f368670429a757e"
  }
] 
```

如果您想要插入多个文档，使用批量 API 通常是比执行多个`put()`请求更好的方式。批量操作往往比单个操作更快，因为它们可以组合成单个事务(对于本地 IndexedDB/WebSQL 存储)或单个 HTTP 请求(对于远程 CouchDB 服务器)。

### 检索多个文档

为了读取多个文档，PouchDB 提供了`allDocs()`方法。

```
// without {include_docs: true}, only document ids are returned
movies
  .allDocs({include_docs: true})
  .then(function (docs) {
    console.log(docs)
  }) 
```

这是一种快速且非常有用的方法。来自 PouchDB 文档:

> allDocs()是 PouchDB 世界的无名之星。它不仅按顺序返回文档——它还允许您颠倒顺序，按 _id 过滤，使用 _id 上的“大于”和“小于”操作进行切片和切块，等等。

默认情况下，文档按升序`_id`返回。您可以指定`{descending: true}`来反转顺序。

```
movies
  .allDocs({
    include_docs: true, 
    descending: true
  })
  .then(...) 
```

您还可以指定一个`startkey`和`endkey`参数来获取某个范围内的文档。例如，要获取所有`_id`以‘a’或‘b’开头的电影，您可以运行以下查询:

```
movies
  .allDocs({
    include_docs: true,
    startkey: 'a',
    endkey: 'c'
  })
  .then(console.log)
  .catch(console.log) 
```

`startKey`和`endKey`参数对于分页 API 特别有用。

## 实时更新订阅源

我们讨论了 PouchDB 如何使用`_rev`字段来跟踪修订，每个修订指向前一个修订。PouchDB 和 CouchDB 使用这个修订链进行数据库复制。

但是，这种复制算法的一个含义是，它允许您查看数据库的历史，让您回答类似以下的问题

*   自给定时间以来，数据库发生了哪些变化？
*   对特定文档进行了哪些更改？

这就是`changes()` API 的用武之地。

要获取自时间开始以来的所有更改:

```
db.changes({
  since: 0,
  include_docs: true
}).then(function (changes) {
  console.log(changes)
}).catch(...) 
```

然而，在 web 应用程序中，您通常更感兴趣的是在初始页面加载后发生的数据库更改，这样您就可以相应地更改 UI。PouchDB/CouchDB duo 也为您提供了实时变化提要。

```
db
  .changes({
    since: 'now',
    live: true,
    include_docs: true
  })
  .on('change', function (change) {
    // This is where you can modify UI, based on database change.
    // change.id contains the doc id, change.doc contains the doc
    if (change.deleted) {
      // document was deleted
    } else {
      // document was added/modified
    }
  })
  .on('error', function (err) {
    // handle errors
  }) 
```

因此，如果你有一个基本的列表应用程序，那么你可以在一个窗口中添加项目，它们会实时显示在另一个窗口中。

你可以在中看到一个[演示。](https://sitepoint-editors.github.io/pouchdb-demo)

## 同步:在浏览器之外获取 PouchDB 数据

大多数应用程序需要在后端存储数据，而不仅仅是在浏览器中，所以您可以使用 PouchDB 在本地保存数据，但要与后端 CouchDB 实例同步，以便数据在任何地方都可用，而不仅仅是在特定的浏览器中。

PouchDB 为此提供了一个非常简单的 API。假设您已经建立了一个远程 CouchDB 数据库，同步它只需要两行 JavaScript 代码。

```
// local database, that lives in the browser's IndexedDB store
var localDB = new PouchDB('mylocaldb')

// remote CouchDB 
var remoteDB = new PouchDB('http://localhost:5984/myremotedb') 
```

您可以通过编写以下内容将本地更改复制到远程数据库

```
localDB
  .replicate
  .to(remoteDB)
  .on('complete', function () {
    // local changes replicated to remote
  }).on('error', function (err) {
    // error while replicating
  }) 
```

然而，由于许多用户可能访问同一个数据库，所以能够将远程数据库中的更改同步到浏览器会更有用。PouchDB 也为您提供了这方面的服务。

使用这一行 JavaScript 就可以实现双向同步:

```
// replicates once
localDB.sync(remoteDB); 
```

或者对于实时同步:

```
// keeps syncing changes as they occur
localDB.sync(remoteDB, {live: true}) 
```

## 后续步骤

PouchDB 也有一个不断增长的生态系统，包括[插件和框架适配器](https://pouchdb.com/external.html),我们没有足够的空间在这里详细介绍，但是绝对值得一试。另外，在使用 PouchDB 时，您可以使用 [PouchDB Inspector](https://chrome.google.com/webstore/detail/pouchdb-inspector/hbhhpaojmpfimakffndmpmpndcmonkfa) chrome 扩展，它提供了一个很好的 GUI 来查看您的数据库。

这就是对 PouchDB 的介绍。这绝对是最有趣的数据库之一，我希望您能看到如何使用它来构建离线优先的实时应用程序。

## 分享这篇文章