# 使用 mLab 和 Heroku 在 30 分钟内部署您自己的 REST API

> 原文：<https://www.sitepoint.com/deploy-rest-api-in-30-mins-mlab-heroku/>

*本文最初发表于[Heroku Dev Center](https://devcenter.heroku.com/articles/mean-apps-restful-api)T3*

MEAN stack 是一个流行的 web 开发栈，由 [MongoDB](https://www.mongodb.org/) 、 [Express](http://expressjs.com/) 、 [AngularJS](https://angularjs.org/) 和 [Node.js](https://nodejs.org/en/) 组成。MEAN 越来越受欢迎，因为它允许开发人员在客户端和服务器端都用 JavaScript 编程。MEAN stack 实现了 JavaScript Object Notation (JSON)开发的完美和谐:MongoDB 以类似 JSON 的格式存储数据，Express 和 Node.js 方便了 JSON 查询的创建，AngularJS 允许客户端无缝地发送和接收 JSON 文档。

MEAN 一般用于创建基于浏览器的 web 应用，因为 AngularJS(客户端)和 Express(服务器端)都是 web 应用的框架。MEAN 的另一个引人注目的用例是开发 RESTful API 服务器。创建 RESTful API 服务器已经成为越来越重要和常见的开发任务，因为应用程序越来越需要优雅地支持各种终端用户设备，如手机和平板电脑。本教程将演示如何使用 MEAN stack 快速创建一个 RESTful API 服务器。

客户端框架 AngularJS 不是创建 API 服务器的必要组件。您也可以编写一个运行在 REST API 之上的 Android 或 iOS 应用程序。我们将 AngularJS 包含在本教程中，以演示它如何允许我们快速创建一个运行在 API 服务器之上的 web 应用程序。

我们将在本教程中开发的应用程序是一个基本的联系人管理应用程序，它支持标准的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (创建、读取、更新、删除)操作。首先，我们将创建一个 RESTful API 服务器，作为在 MongoDB 数据库中查询和持久化数据的接口。然后，我们将利用 API 服务器构建一个基于角度的 web 应用程序，为最终用户提供一个界面。最后，我们将把我们的应用程序部署到 [Heroku](https://www.heroku.com/) 。

为了让我们能够专注于说明一个普通应用程序的基本结构，我们将有意省略一些常见的功能，如[身份验证](https://www.sitepoint.com/user-authentication-mean-stack/)、访问控制和健壮的数据验证。

## 先决条件

要将应用程序部署到 Heroku，您需要一个 [Heroku 帐户](https://signup.heroku.com/)。如果您以前从未在 Heroku 上部署过 Node.js 应用程序，我们建议您在开始之前阅读一下 Heroku 上的[node . js 入门教程。](https://devcenter.heroku.com/articles/getting-started-with-nodejs)

此外，请确保您的本地计算机上安装了以下软件:

*   [Heroku 工具带](https://toolbelt.heroku.com/)
*   [Node.js](https://nodejs.org/en/download/)

## 源代码结构

这个项目的源代码可以在 https://github.com/sitepoint-editors/mean-contactlist 的 GitHub 上找到。该存储库包含:

*   `package.json` —包含应用程序元数据的配置文件。当这个文件存在于项目的根目录中时，Heroku 将使用 Node.js buildpack。
*   `app.json` —描述 web 应用的清单格式。它声明了在 Heroku 上运行应用程序所需的环境变量、附加组件和其他信息。需要创建一个“部署到 Heroku”按钮。
*   这个文件包含了我们所有的服务器端代码，它实现了我们的 REST API。它是用 Node.js 编写的，使用了 Express 框架和 MongoDB Node.js 驱动程序。
*   `/public`目录—该目录包含所有客户端文件，包括 AngularJS 代码。

## 查看运行的示例应用程序

要查看本教程将创建的应用程序的运行版本，您可以在这里查看我们的运行示例:[https://sleepy-citadel-45065.herokuapp.com/](https://sleepy-citadel-45065.herokuapp.com/)

现在，让我们按照教程一步一步来。

## 创建新应用程序

为您的应用程序创建一个新目录，并使用`cd`命令导航到该目录。从这个目录中，我们将在 Heroku 上创建一个应用程序，让 Heroku 准备好接收您的源代码。我们将使用 [Heroku CLI](https://devcenter.heroku.com/articles/creating-apps) 开始。

```
$ git init
Initialized empty Git repository in /path/.git/
$ heroku create
Creating app... done, stack is cedar-14
https://sleepy-citadel-45065.herokuapp.com/ | https://git.heroku.com/sleepy-citadel-45065.git 
```

当您创建一个应用程序时，还会创建一个 git remote(称为 heroku ),并与您的本地 git 存储库相关联。Heroku 还为您的应用程序生成一个随机名称(在本例中为 sleep-citadel-45065)。

Heroku 通过根目录中存在一个`package.json`文件来识别一个 app 为 Node.js。创建一个名为`package.json`的文件，并将以下内容复制到其中:

```
{
  "name": "MEAN",
  "version": "1.0.0",
  "description": "A MEAN app that allows users to manage contact lists",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  },
  "dependencies": {
    "body-parser": "^1.13.3",
    "express": "^4.13.3",
    "mongodb": "^2.1.6"
  }
} 
```

`package.json`文件决定了将用于在 Heroku 上运行应用程序的 Node.js 版本，以及应用程序应该安装的依赖项。当部署一个应用程序时，Heroku 读取这个文件，并使用`npm install`命令安装适当的 Node.js 版本和依赖项。

要准备您的系统以便在本地运行应用程序，请在您的本地目录中运行以下命令以安装依赖项:

```
$ npm install 
```

安装完依赖项后，您就可以在本地运行应用程序了。

## 供应 MongoDB 数据库

在设置了应用程序和文件目录之后，创建一个 MongoDB 实例来持久化应用程序的数据。我们将使用 [mLab](https://mlab.com/) 托管数据库，这是一个完全托管的 MongoDB 服务，可以轻松地提供一个新的 MongoDB 数据库:

*   [注册一个免费的 mLab 账户](https://mlab.com/signup/)。
*   [在美国东部创建一个新的单节点沙盒 MongoDB 数据库](https://mlab.com/create)。
*   你现在应该在你的帐户中看到一个 mLab 沙盒数据库。
*   单击您刚刚创建的数据库。
*   单击通知您创建用户的通知。
*   输入用户名和密码

当你创建一个 mLab 数据库时，你会得到一个 MongoDB [连接字符串](http://docs.mlab.com/connecting/#connect-string)。该字符串包含访问数据库的凭证，因此最好将该值存储在配置变量中。让我们继续将连接字符串存储在名为`MONGODB_URI`的配置变量中:

```
heroku config:set MONGODB_URI=mongodb://your-user:your-pass@host:port/db-name 
```

您可以在 Node.js 中以`process.env.MONGODB_URI`的形式访问这个变量，我们将在稍后完成。

现在我们的数据库已经准备好了，我们可以开始编码了。

## 使用 Node.js 驱动程序连接 MongoDB 和应用服务器

Node.js 开发人员使用两个流行的 MongoDB 驱动程序:官方的 [Node.js 驱动程序](https://docs.mongodb.org/ecosystem/drivers/node-js/#node-js-driver)和一个名为[mongose](http://mongoosejs.com/)的对象文档映射器，它封装了 Node.js 驱动程序(类似于 SQL ORM)。两者各有优势，但是在这个例子中，我们将使用官方的 Node.js 驱动程序。

创建一个名为`server.js`的文件。在这个文件中，我们将创建一个新的 Express 应用程序，并连接到我们的 mLab 数据库。

```
var express = require("express");
var path = require("path");
var bodyParser = require("body-parser");
var mongodb = require("mongodb");
var ObjectID = mongodb.ObjectID;

var CONTACTS_COLLECTION = "contacts";

var app = express();
app.use(express.static(__dirname + "/public"));
app.use(bodyParser.json());

// Create a database variable outside of the database connection callback to reuse the connection pool in your app.
var db;

// Connect to the database before starting the application server.
mongodb.MongoClient.connect(process.env.MONGODB_URI, function (err, database) {
  if (err) {
    console.log(err);
    process.exit(1);
  }

  // Save database object from the callback for reuse.
  db = database;
  console.log("Database connection ready");

  // Initialize the app.
  var server = app.listen(process.env.PORT || 8080, function () {
    var port = server.address().port;
    console.log("App now running on port", port);
  });
});

// CONTACTS API ROUTES BELOW 
```

关于连接到数据库，有一些事情需要注意:

*   我们希望尽可能频繁地使用我们的数据库[连接池](http://blog.mlab.com/2013/11/deep-dive-into-connection-pooling/)，以便最好地管理我们的可用资源。我们在全局范围内初始化`db`变量，这样所有的路由处理程序都可以使用这个连接。
*   我们只有在数据库连接就绪后才初始化应用程序。这确保了应用程序不会因为在连接建立之前尝试数据库操作而崩溃或出错。

现在我们的 app 和数据库连接上了。接下来，我们将通过首先定义所有端点来实现 RESTful API 服务器。

## 用 Node.js 和 Express 创建一个 RESTful API 服务器

作为创建 API 的第一步，我们定义想要公开的端点(或数据)。我们的联系人列表应用程序将允许用户对他们的联系人执行 CRUD 操作。

我们需要的端点是:

**/联系人**

| 方法 | 描述 |
| --- | --- |
| 得到 | 查找所有联系人 |
| 邮政 | 创建新联系人 |

**/联系人/:id**

| 方法 | 描述 |
| --- | --- |
| 得到 | 通过 ID 查找单个联系人 |
| 放 | 更新整个联系人文档 |
| 删除 | 按 ID 删除联系人 |

现在我们将把路线添加到我们的`server.js`文件中:

```
// CONTACTS API ROUTES BELOW

// Generic error handler used by all endpoints.
function handleError(res, reason, message, code) {
  console.log("ERROR: " + reason);
  res.status(code || 500).json({"error": message});
}

/*  "/contacts"
 *    GET: finds all contacts
 *    POST: creates a new contact
 */

app.get("/contacts", function(req, res) {
});

app.post("/contacts", function(req, res) {
});

/*  "/contacts/:id"
 *    GET: find contact by id
 *    PUT: update contact by id
 *    DELETE: deletes contact by id
 */

app.get("/contacts/:id", function(req, res) {
});

app.put("/contacts/:id", function(req, res) {
});

app.delete("/contacts/:id", function(req, res) {
}); 
```

该代码为上面定义的所有 API 端点创建了一个框架。

## 实现 API 端点

接下来，我们将添加数据库逻辑来正确实现这些端点。

我们将首先为`/contacts`实现 POST 端点，这将允许我们创建新的联系人并保存到数据库中。每个联系人将具有以下模式:

```
{
  "_id": <ObjectId>
  "firstName": <string>,
  "lastName": <string>,
  "email": <string>,
  "phoneNumbers": {
    "mobile": <string>,
    "work": <string>
  },
  "twitterHandle": <string>,
  "addresses": {
    "home": <string>,
    "work": <string>
  }
} 
```

下面的代码实现了`/contacts` POST 请求:

```
app.post("/contacts", function(req, res) {
  var newContact = req.body;
  newContact.createDate = new Date();

  if (!(req.body.firstName || req.body.lastName)) {
    handleError(res, "Invalid user input", "Must provide a first or last name.", 400);
  }

  db.collection(CONTACTS_COLLECTION).insertOne(newContact, function(err, doc) {
    if (err) {
      handleError(res, err.message, "Failed to create new contact.");
    } else {
      res.status(201).json(doc.ops[0]);
    }
  });
}); 
```

要测试 POST 实现，请部署代码:

```
$ git add package.json
$ git add server.js
$ git commit -m 'first commit'
$ git push heroku master 
```

应用程序现已部署。确保至少有一个应用程序实例正在运行:

```
$ heroku ps:scale web=1 
```

然后，使用 [cURL](https://curl.haxx.se/) 发出 POST 请求:

```
curl -H "Content-Type: application/json" -d '{"firstName":"Chris", "lastName": "Chang", "email": "support@mlab.com"}' http://your-app-name.herokuapp.com/contacts 
```

我们还没有创建我们的 web 应用程序，但是您可以通过访问 mLab [管理门户](https://devcenter.heroku.com/articles/mongolab#using-mlab-s-admin-tools-for-mongodb)来确认数据已成功保存到数据库。您的新联系人应该显示在“联系人”集合中。

或者，您可以访问`https://mlab.com/databases/your-db-name/collections/contacts`，在那里观察您的新联系人。

下面是最终版本的`server.js`文件，它实现了所有的端点:

```
var express = require("express");
var path = require("path");
var bodyParser = require("body-parser");
var mongodb = require("mongodb");
var ObjectID = mongodb.ObjectID;

var CONTACTS_COLLECTION = "contacts";

var app = express();
app.use(express.static(__dirname + "/public"));
app.use(bodyParser.json());

// Create a database variable outside of the database connection callback to reuse the connection pool in your app.
var db;

// Connect to the database before starting the application server.
mongodb.MongoClient.connect(process.env.MONGODB_URI, function (err, database) {
  if (err) {
    console.log(err);
    process.exit(1);
  }

  // Save database object from the callback for reuse.
  db = database;
  console.log("Database connection ready");

  // Initialize the app.
  var server = app.listen(process.env.PORT || 8080, function () {
    var port = server.address().port;
    console.log("App now running on port", port);
  });
});

// CONTACTS API ROUTES BELOW

// Generic error handler used by all endpoints.
function handleError(res, reason, message, code) {
  console.log("ERROR: " + reason);
  res.status(code || 500).json({"error": message});
}

/*  "/contacts"
 *    GET: finds all contacts
 *    POST: creates a new contact
 */

app.get("/contacts", function(req, res) {
  db.collection(CONTACTS_COLLECTION).find({}).toArray(function(err, docs) {
    if (err) {
      handleError(res, err.message, "Failed to get contacts.");
    } else {
      res.status(200).json(docs);
    }
  });
});

app.post("/contacts", function(req, res) {
  var newContact = req.body;
  newContact.createDate = new Date();

  if (!(req.body.firstName || req.body.lastName)) {
    handleError(res, "Invalid user input", "Must provide a first or last name.", 400);
  }

  db.collection(CONTACTS_COLLECTION).insertOne(newContact, function(err, doc) {
    if (err) {
      handleError(res, err.message, "Failed to create new contact.");
    } else {
      res.status(201).json(doc.ops[0]);
    }
  });
});

/*  "/contacts/:id"
 *    GET: find contact by id
 *    PUT: update contact by id
 *    DELETE: deletes contact by id
 */

app.get("/contacts/:id", function(req, res) {
  db.collection(CONTACTS_COLLECTION).findOne({ _id: new ObjectID(req.params.id) }, function(err, doc) {
    if (err) {
      handleError(res, err.message, "Failed to get contact");
    } else {
      res.status(200).json(doc);
    }
  });
});

app.put("/contacts/:id", function(req, res) {
  var updateDoc = req.body;
  delete updateDoc._id;

  db.collection(CONTACTS_COLLECTION).updateOne({_id: new ObjectID(req.params.id)}, updateDoc, function(err, doc) {
    if (err) {
      handleError(res, err.message, "Failed to update contact");
    } else {
      res.status(204).end();
    }
  });
});

app.delete("/contacts/:id", function(req, res) {
  db.collection(CONTACTS_COLLECTION).deleteOne({_id: new ObjectID(req.params.id)}, function(err, result) {
    if (err) {
      handleError(res, err.message, "Failed to delete contact");
    } else {
      res.status(204).end();
    }
  });
}); 
```

## 为 Web 应用程序设置静态文件

现在我们的 API 已经完成，我们将使用它来创建我们的 web 应用程序。web 应用程序允许用户从浏览器管理联系人。

在你的项目根目录下创建一个`public`文件夹，并从示例应用的[公共文件夹](https://github.com/sitepoint-editors/mean-contactlist/tree/master/public)中复制文件。该文件夹包括 HTML 模板和我们的 AngularJS 代码。

当您浏览 HTML 文件时，您可能会注意到有一些非常规的 HTML 代码，例如 index.html 文件中的“ng-view ”:

```
<div class="container" ng-view> 
```

这些扩展是 AngularJS 模板系统的特征。模板允许我们重用代码并为最终用户动态生成视图。

## 用 AngularJS 构建 Web 应用程序

我们会用 AngularJS 把所有东西绑在一起。AngularJS 将帮助我们路由用户请求，呈现不同的视图，并向数据库发送数据。

我们的 AngularJS 代码位于`app.js`文件的`/public/js`文件夹中。为了简化起见，我们将只关注在请求默认主页路径(`/`)时检索和显示联系人所需的代码。实施这一功能要求我们:

*   使用 angular js[route provider](https://docs.angularjs.org/api/ngRoute/provider/%24routeProvider)(`index.html`和`list.html`)渲染适当的视图和模板。
*   使用 AngularJS [服务](https://docs.angularjs.org/guide/services) (GET `/contacts`)从数据库中获取联系人。
*   用 AngularJS [控制器](https://docs.angularjs.org/guide/controller) ( `ListController`)将数据从服务传递到视图。

代码如下所示:

```
angular.module("contactsApp", ['ngRoute'])
  .config(function($routeProvider) {
    $routeProvider
      .when("/", {
        templateUrl: "list.html",
        controller: "ListController",
        resolve: {
          contacts: function(Contacts) {
              return Contacts.getContacts();
          }
        }
      })
  })
  .service("Contacts", function($http) {
    this.getContacts = function() {
      return $http.get("/contacts").
        then(function(response) {
            return response;
        }, function(response) {
            alert("Error retrieving contacts.");
        });
    }
  })
  .controller("ListController", function(contacts, $scope) {
    $scope.contacts = contacts.data;
  }); 
```

接下来，我们将介绍代码的每个部分及其作用。

## 使用 AngularJS routeProvider 路由用户请求

`routeProvider`模块帮助我们在 AngularJS 中配置路线。

```
angular.module("contactsApp", ['ngRoute'])
  .config(function($routeProvider) {
    $routeProvider
      .when("/", {
        templateUrl: "list.html",
        controller: "ListController",
        resolve: {
          contacts: function(Contacts) {
              return Contacts.getContacts();
          }
        }
      })
  }) 
```

主页路线由几个部分组成:

*   `templateUrl`，指定显示哪个模板
*   `Contacts`服务，它从 API 服务器请求所有联系人
*   `ListController`，它允许我们将数据添加到范围中，并从我们的视图中访问它。

## 使用 AngularJS 服务向 API 服务器发出请求

AngularJS 服务生成一个可供应用程序其余部分使用的对象。我们的服务充当所有 API 端点的客户端包装器。

主页路由使用`getContacts`功能请求联系人数据。

```
.service("Contacts", function($http) {
  this.getContacts = function() {
    return $http.get("/contacts").
      then(function(response) {
        return response;
      }, function(response) {
        alert("Error retrieving contacts.");
      });
  } 
```

我们的服务功能利用内置的 AngularJS `$http`服务来生成 HTTP 请求。该模块还返回一个承诺，您可以对其进行修改以添加额外的功能(如日志记录)。

注意，对于`$http`服务，我们使用相对 URL 路径(例如，`/contacts`)，而不是像`app-name.herokuapp.com/contacts`这样的绝对路径。

## 使用 AngularJS 控制器扩大我们的范围

到目前为止，我们已经配置了路线，定义了要显示的模板，并使用我们的`Contacts`服务检索了数据。为了将一切联系在一起，我们将创建一个控制器。

```
.controller("ListController", function(contacts, $scope) {
  $scope.contacts = contacts.data;
}) 
```

我们的控制器将服务中的联系人数据作为名为`contacts`的变量添加到主页范围中。这允许我们直接从模板中访问数据(`list.html`)。我们可以使用 AngularJS 内置的[n repeat 指令](https://docs.angularjs.org/api/ng/directive/ngRepeat)迭代联系人数据:

```
<div class="container">
  <table class="table table-hover">
    <tbody>
      <tr ng-repeat="contact in contacts | orderBy:'lastName'" style="cursor:pointer">
        <td>
          <a ng-href="#/contact/{{contact._id}}">{{ contact.firstName }} {{ contact.lastName }}</a>
        </td>
      </tr>
    </tbody>
  </table>
</div> 
```

## 完成项目

现在我们已经了解了如何在 AngularJS 中实现主页路由，其余 web 应用程序路由的实现可以在源项目的 [/public/js/app.js 文件](https://github.com/sitepoint-editors/mean-contactlist/blob/master/public/js/app.js)中找到。它们都需要在`routeProvider`中定义一个路由，一个或多个服务函数来发出适当的 HTTP 请求，以及一个控制器来扩大范围。

完成角度代码后，再次部署应用程序:

```
$ git add server.js
$ git add public
$ git commit -m 'second commit'
$ git push heroku master 
```

现在，web 应用程序组件已经完成，您可以通过从 CLI 打开网站来查看您的应用程序:

```
$ heroku open 
```

## 摘要

在本教程中，您学习了如何:

*   在 Express 和 Node.js 中创建一个 RESTful API 服务器。
*   将一个 MongoDB 数据库连接到 API 服务器，用于查询和持久化数据。
*   使用 AngularJS 创建丰富的 web 应用程序。

我们希望您已经看到了 MEAN stack 的强大功能，能够为当今的 web 应用程序开发通用组件。

## 关于缩放的注释

如果您在 Heroku 上运行一个生产应用程序，那么随着流量和数据量的增加，您将需要扩展您的应用程序和数据库。参考[优化 Node.js 应用并发性](https://devcenter.heroku.com/articles/node-concurrency)一文，了解扩展应用的最佳实践。要升级数据库，请参见 [mLab 附加文档](https://devcenter.heroku.com/articles/mongolab)。

## 可选的后续步骤

正如我们之前提到的，这个应用程序故意省略了你想要在真实的生产应用程序中包含的细节。特别是，我们没有实现用户模型、[用户认证](https://www.sitepoint.com/user-authentication-mean-stack/)或健壮的输入验证。考虑添加这些功能作为额外的练习。如果你对本教程有任何疑问，请在下面的评论中告诉我们。

## 分享这篇文章