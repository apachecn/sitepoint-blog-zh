# 如何开始使用 restdb.io 并创建一个简单的 CMS

> 原文：<https://www.sitepoint.com/how-to-get-started-with-restdb-io-and-create-a-simple-cms/>

![How to Get Started With restdb.io and Create a Simple CMS](img/c34da36033aa9bda376694e9755ede2f.png)

*本文由 [restdb.io](http://synd.co/2vd75nQ) 赞助。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

数据库让最有经验的开发人员感到恐惧。安装、更新、磁盘空间供应、备份、高效索引、优化查询和扩展是大多数人都需要解决的问题。较大的组织将雇用一个知识渊博的开发人员，他将毕生精力投入到数据库的开发中。然而，在他们去度假的那一刻，这个系统不可避免地失灵了。

一个更实际的选择是外包你的数据库，这正是 restdb.io 提供的服务。他们管理棘手的数据存储把戏，让您专注于更紧迫的开发任务。

## restdb.io:基础知识

[restdb.io](http://synd.co/2vd75nQ) 是一个即插即用的云 NoSQL 数据库。任何有 [MongoDB](http://synd.co/2hC0RJs) 经验的人都会很快熟悉它。主要区别:

*   无需管理您的安装、存储或备份
*   您可以在 restdb.io 中定义数据结构模式
*   数据字段可以与其他集合中的其他字段有关系
*   没有必要定义索引
*   可以通过由 HTTP 或 Auth0/JWT 令牌认证的 REST API 来查询和更新数据
*   查询和更新以 JSON 格式发送和接收
*   有一些工具可以输入、查看和导出各种格式的数据
*   它支持一些有趣的附加功能，如代码挂钩、电子邮件、web 表单生成、网站、实时消息等等。

一个免费账户可以让你无义务地评估服务。[付费计划](http://synd.co/2vxMgVf)提供额外的存储空间、查询吞吐量、开发者账户和 MongoDB 集成。

在以下部分中，我将描述如何:

1.  配置新数据库并输入数据
2.  使用该数据呈现 restdb.io 上托管的一组网页，并
3.  使用 API 为内容编辑者提供搜索工具。

## 步骤 1:创建一个新的数据库

注册谷歌、脸书或电子邮件账户后，你可以创建一个新的空数据库。这会在 *yourdbname* .restdb.io 处生成一个新的 API 端点 URL:

![create a new database](img/e25c3f7690bfbb272ab613af7ed07a3b.png)

## 步骤 2:创建一个新的收藏库

数据库包含一个或多个用于存储数据的*集合*。这些类似于 SQL 数据库表。集合包含类似于 SQL 数据库记录(表行)的*“文档”*。

restdb.io 接口提供了两种模式:

1.  标准模式显示可用的集合，并允许您插入和修改数据。
2.  开发人员模式允许您创建和配置集合。

![enter developer mode](img/063eb4dbfff83ca385b4d5a4748ee2ce.png)

进入**开发者模式**(屏幕右上方)，点击**添加收藏**按钮。

![create a new collection](img/d3637e7656447be6904c950f10892164.png)

一个集合需要一个惟一的名称(我使用了“内容”)和一个可选的描述和图标。点击**保存**返回到您的数据库概览。“内容”集合将与其他几个不可编辑的系统集合一起出现在列表中。

或者，可以从 Excel、CSV 或 JSON 文件导入数据，通过点击标准视图中的 **Import** 来创建集合。

## 第三步:定义字段

停留在**开发者模式**，点击“内容”集合并选择**字段**标签。点击**添加字段**，添加和配置新的字段，对集合中的数据进行分类。

![create fields](img/bb8dd74ec6dfc9f077b4897542c24470.png)

每个集合文档将存储关于数据库驱动的网站中单个页面的数据。我添加了五个字段:

*   **slug**–页面路径 URL 的文本字段
*   **标题**–页面标题的文本字段
*   **body**–页面内容的特殊减价文本字段
*   **image**–一个特殊的图像字段，允许上传任意数量的图像(也存储在 restdb.io 系统中)
*   **published**–布尔值，对于公开可见的页面，该值必须为真。

## 第四步:添加文档

文档可以以标准或开发模式*(或通过 API)* 添加到集合中。创建几个具有典型页面内容的文档:

![create documents](img/42a17502ae8493ee6cc67390f694e91e.png)

主页的 slug 应该是空的。

## 步骤 5:创建一个数据库驱动的网站(可选)

restdb.io 提供了一个有趣的特性，可以使用集合中的数据文档创建和托管一个数据库驱动的网站。

该网站托管在 www-*your dbname*. restdb . io，但你可以[在页面](http://synd.co/2vGk3v2)指向任何域名。有关说明，请从数据库列表或左侧面板底部单击**设置**，然后单击**虚拟主机**选项卡。

要创建网站，必须在开发人员模式下配置**页面**，这些页面定义了查看内容的模板。模板包含一个代码片段，它设置:

1.  *上下文*–在集合中定位正确文档的查询，以及
2.  HTML——一种使用[手柄](http://synd.co/2vG0hQ2)模板语法将内容插入适当元素的结构。

点击**添加页面**创建页面。将其命名为特殊名称*/:slug*–这意味着该模板将应用于除主页(没有 slug)之外的任何 URL。点击**保存**返回页面列表，然后点击 */:slug* 条目进行编辑。

切换到**设置**选项卡，确保在点击**更新**之前，将*文本/html* 输入为**内容类型**并勾选**发布**:

![create page](img/ffb6aa9570d55751086c421eb7ebf7f7.png)

现在切换到“/:slug”选项卡的**代码。在编辑器顶部输入*上下文*代码:**

```
{{#context}}
{
  "docs": {
    "collection": "content",
    "query": {
      "slug": "{{pathparams.slug}}",
      "published": true
    }
  }
}
{{/context}} 
```

这定义了一个查询，因此模板可以从我们的*内容*集合中访问特定的文档。在这种情况下，我们将获取与 URL 上传递的 slug 相匹配的已发布文档。

所有 restdb.io 查询都返回一个对象数组。如果没有文档返回，docs 数组将为空，这样我们可以添加代码来返回页面在*上下文*下不可用:

```
<!doctype html>
{{#unless docs.[0]}}
  <html>
  <body>
    <h1>Page not available</h1>
    <p>Sorry, this page cannot be viewed. Please return later.</p>
  </body>
  </html>
{{/unless}} 
```

在此之下，我们可以编写模板，将标题、正文和图像字段放入适当的 HTML 元素中:

```
{{#with docs.[0]}}
  <html>
  <head>
    <meta charset="utf-8">
    <title>{{title}}</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <style> body {
        font-family: sans-serif;
        font-size: 100%;
        color: #333;
        background-color: #fff;
        max-width: 40em;
        padding: 0 2em;
        margin: 1em auto;
      } </style>
  </head>
  <body>
    <header>
      {{#each image}}
        <img src="https://sitepoint-fbbf.restdb.io/media/{{this}}" alt="image" />
      {{/each}}

      <h1>{{title}}</h1>
    </header>
    <main>
      {{markdown body}}

      <p><a href="/">Return to the home page...</a></p>
    </main>
  </body>
  </html>
{{/with}} 
```

注意，我们的 markdown *主体*字段必须用 markdown 处理程序来呈现。

用 Ctrl|Cmd + S 保存代码，或者返回到**设置**选项卡，点击**更新**保存代码。

*/:slug* 页面模板将应用于我们所有的*内容*集合——除了主页，因为它没有 slug！为了呈现主页，创建一个名为 *home* 的**新页面**，具有相同的设置和内容。您可能希望为特定于主页的内容调整模板。

保存后，您可以从 https://www-*your dbname*. restdb . io/访问您的站点。我在 https://www-sitepoint-fbbf.restdb.io/的创建了一个非常简单的三页网站。

有关 restdb.io 页面托管的更多信息，请参考:

*   [网页和数据库驱动的网站托管](http://synd.co/2wAXBRb)
*   [restdb . io 动态网站演示](http://synd.co/2uzNSsP)
*   [车把模板语法](http://synd.co/2vG0hQ2)

## 步骤 6: API 查询

创建一个站点来显示您的数据可能是有用的，但是您最终会想要构建一个查询和操作信息的应用程序。

restdb.io 的 [REST API](http://synd.co/2vfhtKh) 提供了通过 HTTP:

*   HTTP GET 请求从集合中检索数据
*   HTTP POST 请求在集合中创建新文档
*   HTTP PUT 请求更新集合中的文档
*   HTTP 补丁请求更新集合中文档的一个或多个属性
*   HTTP DELETE 请求从集合中删除文档

有很多 API 可以处理上传的媒体文件、数据库元数据和邮件，但你最常用的是收藏。API URL 是:

https://*yourdbname*. restdb . io/rest/*收藏-名称* /

因此，我的“内容”收藏的 URL 是:

```
https://sitepoint-fbbf.restdb.io/rest/content/ 
```

查询作为 JSON 编码的名为 **q** 的 querystring 参数传递给该 URL，例如获取集合中所有已发布的文章:

```
https://sitepoint-fbbf.restdb.io/rest/content/q={"published": true} 
```

但是，如果没有在 **x-apikey** HTTP 头中传递 API 键，这个查询将会失败。缺省情况下提供了一个完全访问的 API 密匙，但是建议创建仅限于特定操作的密匙。从数据库**设置**， **API** 选项卡:

![RestDB API Key](img/db45275a9b3316fb2d1c59216edbb165.png)

点击**添加新的**创建一个新的密钥。我在这里创建的这个仅限于对*内容*集合的 GET(查询)请求。如果您将使用客户端 JavaScript Ajax 代码，您应该创建一个类似的受限键，因为字符串在代码中是可见的。

现在可以构建一个独立的 JavaScript 查询处理程序 *(ES5 已经被用来确保跨浏览器的兼容性，而不需要预编译步骤！)*:

```
// restdb.io query handler
var restDB = (function() {

  // configure for your own DB
  var 
    api = 'https://sitepoint-fbbf.restdb.io/rest/',
    APIkey = '597dd2c7a63f5e835a5df8c4';

  // query the database
  function query(url, callback) {

    var timeout, xhr = new XMLHttpRequest();

    // set URL and headers
    xhr.open('GET', api + url);
    xhr.setRequestHeader('x-apikey', APIkey);
    xhr.setRequestHeader('content-type', 'application/json');
    xhr.setRequestHeader('cache-control', 'no-cache');

    // response handler
    xhr.onreadystatechange = function() {
      if (xhr.readyState !== 4) return;
      var err = (xhr.status !== 200), data = null;
      clearTimeout(timeout);
      if (!err) {
        try {
          data = JSON.parse(xhr.response);
        }
        catch(e) {
          err = true;
          data = xhr.response || null;
        }
      }
      callback(err, data);
    };

    // timeout
    timeout = setTimeout(function() {
      xhr.abort();
      callback(true, null);
    }, 10000);

    // start call
    xhr.send();
  }

  // public query method
  return {
    query: query
  };

})(); 
```

这段代码将查询传递给 restdb.io API 端点，并为 API 键设置适当的 HTTP 头，包括 **x-apikey** 。如果响应时间超过 10 秒，则超时。向回调函数传递一个错误和任何作为本机对象返回的数据。例如:

```
// run a query
restDB.query(
  '/content?q={"published":true}',
  function(err, data) {
    // success!
    if (!err) console.log(data);
  }
); 
```

控制台将从*内容*集合中输出一组文档，例如

```
[
  {
    _id: "1111111111111111",
    slug: "",
    title: "Home page",
    body: "page content...",
    image: [],
    published: true
  },
  {
    _id: "22222222222222222",
    slug: "page-two",
    title: "Page Two",
    body: "page content...",
    image: [],
    published: true
  },
  {
    _id: "33333333333333333",
    slug: "page-three",
    title: "Another page",
    body: "page content...",
    image: [],
    published: true
  }
] 
```

可以从任何能够发出 HTTP 请求的语言中调用 API。 [restdb.io 为 cURL，jQuery $提供了示例](http://synd.co/2vf9WuQ)。ajax、JavaScript XMLHttpRequest、NodeJS、Python、PHP、Java、C#、Objective-C 和 Swift。

我在 [Codepen.io](http://synd.co/2wn71QX) 创建了一个简单的例子，它允许您在标题和正文字段中搜索字符串并显示结果:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )查看笔 [restdb.io 查询](https://codepen.io/SitePoint/pen/brWEqW/)。