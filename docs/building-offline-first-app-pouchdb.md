# 使用 PouchDB 构建离线第一个应用程序

> 原文：<https://www.sitepoint.com/building-offline-first-app-pouchdb/>

客户端数据库仍然是跨浏览器离线应用程序开发中的一个痛处。在一个角落，Safari 和 Opera 是否≤ 12。这两种浏览器都只支持 Web SQL。在另一个角落，我们有 Firefox 和 Internet Explorer (10+)，它们只支持 IndexedDB。Chrome(和 Opera 15+)，值得一提的是，两者都支持。

如果 Web SQL 和 IndexedDB 不是完全不同的数据库，使用不同的数据存储模型，这种分裂就不会那么可怕。支持这两者是一项艰巨的任务。幸运的是， [PouchDB](http://pouchdb.com/) 是存在的。

PouchDB 是一个客户端数据库 API。它是用 JavaScript 编写的，并模仿了 [CouchDB API](http://couchdb.apache.org) 。它甚至能够与 CouchDB 实例同步。然而，我们对 PouchDB 感兴趣，因为它抽象了 Web SQL 和 IndexedDB 之间的区别，并将它们包装在一个接口中。

在本文中，我们将通过构建一个简单的离线笔记应用程序来了解 PouchDB。这里只涉及部分代码。为了便于阅读，许多函数都进行了简化。你可以从 GitHub 下载整个东西。

## 你需要什么

对于这个项目，你需要以下。

*   一份 [PouchDB 脚本](https://github.com/daleharvey/pouchdb/releases/download/1.1.0/pouchdb-1.1.0.min.js)的副本
*   支持 IndexedDB 或 web SQL 的 Web 浏览器。Opera、Safari、Internet Explorer、Chrome 和 Firefox 的当前版本符合要求。
*   HTTP 服务器，如 Nginx、Lighttpd 或 Apache HTTP。

这不是真正必要的，但如果您想查看存储在本地数据库中的数据，请使用带有数据库检查工具的浏览器。Chrome、Opera 和 Safari 都支持使用其原生开发工具进行数据库检查。下图显示了 Chrome 中的 PouchDB 数据库。

![PouchDB Database in Chrome](img/7846b32d9da6879a4c4e1555f047cb30.png)

由于 IndexedDB 和 Web SQL 中的原始限制，您还需要使用 HTTP 服务器进行开发。使用任何你喜欢的服务器——Apache、T2、Nginx 和 T4 的 Lighttpd 是三个可靠的选择。或者你可以使用一些软件包，比如用于 Mac OS X 的 [MAMP](http://www.mamp.info/en/index.html) ，用于 Windows 的 [WAMP](http://www.wampserver.com/en/) ，或者用于 Mac、Windows 和 Linux 的 [XAMPP](http://www.apachefriends.org/index.html) 。

像处理任何其他 JavaScript 文件一样，将 PouchDB 添加到 HTML 文档中:

```
<script src="pouchdb-nightly.min.js"></script>
```

## 创建 PouchDB 数据库

所有 PouchDB 数据库或数据库连接都是用`PouchDB`构造函数创建的:

```
var pdb = new PouchDB('pouchnotes');
```

这将创建一个名为`_pouch_pouchnotes`的数据库。PouchDB 在每个数据库名称前加上`_pouch_`。如果你还在网站的其他区域使用“原始”的 IndexedDB 或 Web SQL，避免在这些数据库中使用`_pouch_`前缀。

## 规划我们的应用

那么笔记应用程序会是什么样子呢？嗯，我们可能希望每个音符都有一个标题。每个便笺还将包含组成便笺正文的文本。我们可能也想标记我们的笔记，所以我们将有一个字段。如果我们能附加一个文件不是很好吗？我们将使用如下所示的 HTML 表单。

![HTML Form that Uses PouchDB](img/2eeeb0d53d1ac0ad63375096391a428e.png)

我们将把我们的数据库结构建立在这个表单的基础上。

## 设计(各种)模式

PouchDB 的有趣之处在于它有一个灵活的模式。数据库中的每个对象实际上都是一个独立的文档。PouchDB 不使用数据组织的关系模型，所以我们可以在需要时向文档添加字段或属性。

PouchDB 查询使用 MapReduce，而不是 SQL/关系数据库的`SELECT * FROM tablename`语法。你编写函数来过滤和排序你的数据。与 SQL 相比，它需要一点思想上的转变，但是一旦你掌握了它，就很容易了。稍后我们会看到一个例子。

## 添加和更新注释

当我们提交表单时，我们会将注释添加到数据库中。PouchDB 提供了两种保存文档的方法:`post`和`put`。每个方法都接受两个参数。

*   `document`(必选):包含属性及其值的对象。在这种情况下，它将是表单字段及其值。
*   `callback`(可选):操作完成时调用的函数。它接受两个参数:`error`和`response`。

主要区别是:`post`添加一个新文档并生成一个标识符(`_id`)；对于`put`，我们需要提供一个。这意味着您可以使用`put`来添加或更新文档。但是`post`只用于向数据库添加新文档。现在，我们来看一个使用`put`的例子。

```
var form, savenote;

form = document.getElementById('addnote');

savenote = function(event) {
  var o = {};

  o.notetitle = form.notetitle.value;
  o.note = form.note.value;
  o.tags = form.tags.value;

  /* Generate an _id if we don't have one.
     It should be a string, which is why we're
     adding '' to it. */

  if (event.target._id.value == '') {
    o._id = new Date().getTime() + '';
  } else {
    o._id = event.target._id.value;
  }

  pdb.put(o, function(error, response) {
    if (error) {
      console.log(error);
      return;
    } else if(response && response.ok) {
      /* Do something with the response. */
    }
  });
}

/* Add the event handler */
form.addEventListener('submit', savenote);
```

如果我们的表单中没有`_id`值，我们将为它生成一个时间戳。否则，我们将使用`form._id`的值。我们的其他表单域将成为文档对象的属性和值。通过使用`put`而不是`post`，我们可以使用`savenote`函数来添加和更新注释。

如果一切顺利，我们的回调将收到 JSON 格式的响应。下面显示了一个成功响应的示例。

```
{ok: true, id: "1391406871281", rev: "1-1d95025598a94304a87ef14c108db7be"}
```

我们没有对我们的回应做任何事情。根据您的应用，您可能不想这样做。但是，对于我们的笔记应用程序，我们希望能够将文件与笔记关联起来。PouchDB 将这些文件称为*附件*。

## 保存附件

保存附件比保存文本稍微复杂一点。我们不能只查询`input type="file"`字段的`value`属性。相反，我们必须使用文件 API 读取文件数据，然后使用 PouchDB 的`putAttachment`方法保存它。让我们添加上一节中的`savenote`方法。

```
savenote = function(event) {
  var o = {};

  o.notetitle = form.notetitle.value;
  o.note = form.note.value;
  o.tags = form.tags.value;

  /* Generate an _id if we don't have one.
     It should be a string, which is why we're
     adding '' to it. */

  if (event.target._id.value == '') {
    o._id = new Date().getTime() + '';
  } else {
    o._id = event.target._id.value;
  } 

  pdb.put(o, function(error, response) {
    if (error) {
      console.log(error);
      return;
    }

    /* New code for saving attachments */
    if (response && response.ok) {
      if (form.attachment.files.length) {
        var reader = new FileReader();

        /* Using a closure so that we can extract the 
           File's attributes in the function. */
        reader.onload = (function(file) {
          return function(e) {
            pdb.putAttachment(response.id, file.name, response.rev, e.target.result, file.type);
          };
        })(form.attachment.files.item(0));
        reader.readAsDataURL(form.attachment.files.item(0));
      }
    }
  });
}
```

每个文件输入类型也有一个返回一个`FileList`对象的`files`属性。在这种情况下，那就是`form.attachment.files`。顾名思义，`FileList`对象是一个数组，包含使用该字段提交的一个或多个文件。我们可以用`length`属性确定列表中文件的数量。列表中的每个文件都可以使用它的索引和`item`方法来引用，就像我们在这里所做的那样(`form.attachment.files.item(0)`)。或者，您可以使用方括号语法(`form.attachment.files[0]`)。

如果注释添加成功，我们将得到一个`response.id`。然后，我们可以检查是否还有文件可以保存为附件。如果有，我们将使用一个`FileReader`对象(`var reader = new FileReader()`)来读取它。PouchDB 附件必须是 base64 编码的。编码文件最简单的方法是使用`readAsDataURL()`。一旦文件加载，我们可以使用`putAttachment`将它保存到数据库中。

PouchDB 的`putAttachment`方法最多接受六个参数。五个是必需的，一个是可选的。

*   `docID`(必选):将与此附件相关联的文档的标识符。这种情况下是`response.id`。
*   `Attachment ID`(必选):附件的名称。这里我们使用文件的名称。
*   `rev`(必选):父文档的修订号。
*   `attachment_doc`(必选):base64 编码的文件数据。在这种情况下，我们的`FileReader`对象的`result`属性。
*   `type`(必选):该数据的 MIME 类型。比如`image/png`或者`application/pdf`。
*   `callback`(可选):操作完成时调用的函数。和所有 PouchDB 回调函数一样，它接受两个参数，`error`和`response`。在我们的例子中，我们忽略了这一点。

在这个例子中，我们还将`onload`事件处理程序包装在一个闭包中。闭包使得从我们的事件处理程序中访问我们的文件属性成为可能(例如，用`file.name`和`file.type`)。

既然我们已经了解了如何保存笔记和附件，那么让我们来看看如何检索记录，包括单个记录和集合记录。

## 正在检索所有笔记

如果我们想查看数据库中的笔记列表，该怎么办？这就是 PouchDB 的`allDocs`有用的地方。`PouchDB.allDocs`让我们一次检索一批文档。

![A list of all notes in our database.](img/b966529ffa3c6077f6625c726aea5df6.png)

名字`allDocs`有点误导。我们当然可以用它来检索我们所有的文件。但是，我们也可以使用它来检索某个范围内的文档，或者检索与特定键匹配的文档。此方法接受两个参数，这两个参数都不是必需的。

*   `options`(可选):包含以下一个或多个属性的对象。
    *   `include_docs` (Boolean):包含每行的整个文档。当`false`时，将只返回文件的`id`和`rev`号。
        * `conflicts`(布尔):包含冲突。
    *   `startkey`和`endkey`:包含关键字在此范围内的文档。
    *   `descending` (boolean):改为按降序对结果进行排序。
        * `options.keys`(数组):只返回匹配指定键的文档。
        * `options.attachments`(布尔):返回带有文档的附件。
        * `callback`(可选):检索完成时调用的函数。与其他 PouchDB 回调一样，它接收一个`error`参数和一个`response`参数。

在下面的简化示例中，我们已经检索了数据库中的所有文档。为了检索文档标题、创建日期和修改日期，我们需要将`include_docs`的值设置为`true`。这是我们的`viewnoteset`函数。

```
var viewnoteset = function() {
  var df = document.createDocumentFragment(),
           options = {},
           nl = document.querySelector('#notelist tbody');

  options.include_docs = true;

  this.pdb.allDocs(options, function(error, response) {
    var row = response.rows.map(addrow); // Calls an addrow() function

    row.map(function(f) {
      if (f) {
        df.appendChild(f); 
      } 
    });
    nl.appendChild(df);
  });
};
```

`response`的值是一个包含三个属性的对象:`total_rows`、`offset`和`rows`。我们对`response.rows`最感兴趣，因为它是一个文档对象数组。这里我们在`response.rows`上使用了`map`，JavaScript 的内置数组方法之一。使用`map`为每个笔记调用我们的`addrow`函数，并将其添加到列出我们的笔记的表中。

## 检索单个笔记

检索单个音符稍微容易一些，因为我们可以使用 PouchDB 的`get`方法。唯一需要的参数是文档 ID。然而，我们可以包含一个`options`参数和一个回调函数来处理结果。

我们的 options 参数，`{attachments: true}`确保了如果某个特定的注释有任何附件，那么在查看时它将与注释一起显示。这里，我们的回调函数获取我们的笔记数据，并使用它来填充表单字段和显示任何附件。

```
var viewnote = function(noteid) {
  var noteform = document.querySelector('#noteform');

  pdb.get(noteid, {attachments: true}, function(error, response) {
    var fields = Object.keys(response), o, link, attachments, li;

    if (error) {
      return;
    } else {
      /* Since our note field names and form field names match,
         We can just iterate over them. */

      fields.map(function(f) {
        if (noteform[f] !== undefined && noteform[f].type != 'file') {
          noteform[f].value = response[f];
        }

        if (f == '_attachments') {
          attachments = response[f];

          for (o in attachments) {
            li = document.createElement('li');
            link = document.createElement('a');
            link.href = 'data:' + attachments[o].content_type + ';base64,' + attachments[o].data;
            link.target = "_blank";
            link.appendChild(document.createTextNode(o));
            li.appendChild(link);
          }

          document.getElementById('attachmentlist').appendChild(li);
        }
      });
    } 
  }); 
}
```

在我们的演示应用程序中，我们使用一个链接为每个音符传递`id`。每个`href`指向`/#/view/xxxxx`，其中`xxxxx`是音符`id`。单击一个链接会触发一个`hashchange`事件，而`hashchange`事件处理程序(如下所示)是我们将`id`传递给`viewnote`的地方。

```
window.addEventListener('hashchange', function(e) {
  var noteid;

  /* Replacing # for compatibility with IE */
  if (window.location.hash.replace(/#/,'')) {
    noteid = window.location.hash.match(/\d/g).join('');
    viewnote(noteid);
  }
});
```

## 使笔记可搜索

笔记在可搜索时特别有用。因此，让我们在应用程序中添加一个搜索特性。我们将从我们的搜索表单中获取输入，并将其用作我们搜索查询的基础。下图显示了当我们使用搜索特性时，我们的应用程序将会是什么样子。

![The example application's search feature](img/73931ab159e2bacdc515355defb1f5b9.png)

PouchDB 查询看起来与 SQL 非常不同。使用 SQL，您可以指定要选择的内容、来自哪个表以及根据什么标准。例如，一个简单的笔记搜索查询可能如下所示:`SELECT * FROM notes WHERE title, text, tags LIKE %interview%`。但是对于 PouchDB，我们使用函数来运行查询。

为了运行查询，我们将使用 PouchDB 的`query`方法。它接受三个参数。

*   **`fun`** (必选):函数的名称。
*   **`options`** (可选):包含搜索结果选项的对象。您可以指定一个 reduce 函数，或者将结果限制在特定的键或键范围内。
*   **`callback`** (可选):查询完成时调用的函数。

下面我们来看看我们的搜索功能。

```
var search = function(searchkey) {
  var map = function(doc) {
    var searchkey, regex;

    /* Escape characters with special RegExp meaning */
    searchkey = document.getElementById('q').value.replace(/[$-\/?[-^{|}]/g, '\\$&');
    regex = new RegExp(searchkey,'i');

    /* If the notetitle, note, or tags fields match, 
       return only the fields we need to create the result list. */
    if (doc.notetitle.match(regex) ||
        doc.note.match(regex) ||
        doc.tags.match(regex)) {
      emit(doc._id, {notetitle: doc.notetitle, note: doc.note, tags: doc.tags});
    }
  }

  db.query(map, function(err, response) { 
    if (err) {
      console.log(err);
    }

    if (response) {
      var df, rows, nl, results;

      /* Rewrite the response so that our object has the 
         correct structure for our addrow function. */
      results = response.rows.map(function(r) {
        r.doc = r.value;
        delete r.value;
        return r;
      });

      nl = document.querySelector('#notelist tbody');
      df = document.createDocumentFragment(), 
      rows = results.map(addrow, that);
      rows.map(function(f) {
        if (f) {
          df.appendChild(f); 
        }
      });

      nl.innerHTML = '';
      nl.appendChild(df);
    }
  });
}
```

在我们的搜索函数中，我们定义了一个`map`函数，这是我们查找和过滤记录的方式。`map`函数总是接收一个 PouchDB 文档作为它的唯一参数。我们不必将这个函数命名为`map`，但它必须是第一个参数。

在`map`中，我们从搜索表单输入中创建了一个正则表达式对象。我们将测试我们的`notetitle`、`note`和`tags`字段，看看这些字段是否匹配我们的正则表达式。如果是这样，我们将返回`notetitle`、`id`(这是一个时间戳)，并使用`emit`方法修改属性。`emit`方法内置在 PouchDB 中。顾名思义，它以指定的格式选择并返回指定的属性。`emit`的第一个论点成为我们结果的关键。

我们的`map`函数成为`query`的第一个参数。`query`的第二个参数是——你可能已经猜到了——回调函数。假设一切顺利，我们的`response`参数将是一个包含三个属性的对象:`total_rows`、`offset`和`rows`。我们要`rows`。这是一个数组，包含与我们的搜索词相匹配的注释。下面的代码示例显示了响应可能的样子。

```
[{
  value: {
    id: "1388703769529",
    modified: 1391742787884,
    notetitle: "Fluffernutter sandwich recipe"
  },
  id:"1388703769529",
  key:"1388703769529"
},
{
  value: {
    id: "1391656570611",
    modified: 1391656570611,
    notetitle: "Browned-butter Rice Krispie Treats recipe"
  },
  id:"1391656570611",
  key:"1391656570611"
}]
```

因为我们的响应是一个数组，所以我们可以使用本地的`Array.prototype`方法来操作结果。在本例中，我们使用了`Array.prototype.map`来重写每个 note 对象，这样我们的`value`属性就变成了`doc`，并再次为每个结果调用`addrow`。

## 使用应用程序缓存脱机工作

为了让这个应用程序完全离线工作，我们还需要使用[应用程序缓存](https://www.sitepoint.com/html5-application-cache/)离线保存 HTML、CSS 和 JavaScript。应用程序缓存是一个纯文本文件，带有一个`Content-type: text/cache-manifest`头，告诉浏览器哪些资产应该存储在本地。我们在这里不会对应用程序缓存进行“深入研究”，但是让我们来看看清单文件`pouchnotes.cache`，它是我们的[演示应用程序](http://webinista.github.io/PouchNotes/)的清单文件。

```
CACHE MANIFEST
# Version 2014.02.10.01

CACHE:
index.html
css/style.css
js/pouchdb-nightly.min.js
js/application.js
```

我们从行`CACHE MANIFEST`开始，这是所有缓存清单必须开始的方式。第二行告诉我们这是文件的哪个版本。只有当缓存清单发生变化时，浏览器才会更新缓存。如果我们修改 CSS、JavaScript 或 HTML 文件，更改版本号是触发更新的最简单方法。

不过，我们还需要做一件事。我们需要将清单添加到 HTML 文档中。这需要给我们的`<html>`标签添加一个`manifest`属性，就像这样:

```
<html lang="en-us" manifest="pouchnotes.manifest">
```

现在我们的数据库*和*我们的文件即使在我们离线时也是可用的。

预先警告:应用程序缓存增加了一层开发复杂性。因为为了让浏览器下载新文件，缓存清单必须更改，所以您应该等到准备好发布应用程序版本后再添加它。

## 结论

PouchDB 还有更多我们在这里没有涉及的内容。例如，您可以将 PouchDB 与一个 [CouchDB](http://couchdb.apache.org) 服务器同步。与数据库服务器同步使我们能够构建能够跨多种浏览器和计算机轻松共享数据和文件的应用程序。

我希望这篇文章已经让您了解了 PouchDB 是什么，以及如何使用它来构建即使在我们的互联网连接中断时也能工作的软件。

## 分享这篇文章