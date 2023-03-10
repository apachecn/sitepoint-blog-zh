# 平均堆栈简介

> 原文：<https://www.sitepoint.com/introduction-mean-stack/>

术语 MEAN stack 指的是用于开发 web 应用程序的基于 JavaScript 的技术的集合。MEAN 是 [MongoDB](http://mongodb.org/) 、 [ExpressJS](http://expressjs.com/) 、 [AngularJS](http://angularjs.org/) 和 [Node.js](http://nodejs.org) 的缩写。从客户端到服务器再到数据库，意思就是全栈 JavaScript。本文探索了均值堆栈的基础知识，并展示了如何创建一个简单的桶列表应用程序。

## 介绍

[Node.js](http://nodejs.org/) 是服务器端 JavaScript 执行环境。这是一个基于谷歌 Chrome 的 V8 JavaScript 运行时的平台。它有助于快速构建高度可伸缩的并发应用程序。

[Express](http://expressjs.com/) 是用于在 Node 中构建 web 应用的轻量级框架。它为构建单页面和多页面 web 应用程序提供了许多健壮的特性。Express 的灵感来自流行的 Ruby 框架， [Sinatra](http://www.sinatrarb.com/) 。

MongoDB 是一个无模式的 NoSQL 数据库系统。MongoDB 以二进制 JSON 格式保存数据，这使得在客户机和服务器之间传递数据更加容易。

[AngularJS](http://angularjs.org/) 是 Google 开发的 JavaScript 框架。它提供了一些很棒的特性，比如双向数据绑定。这是一个快速、出色的前端开发的完整解决方案。

在本文中，我们将使用 MEAN stack 创建一个简单的 CRUD 应用程序。所以，让我们开始吧。

## 先决条件

在开始之前，我们需要安装各种 MEAN 软件包。首先从[下载](http://www.nodejs.org/download/)页面安装 Node.js。接下来安装[下载安装 MongoDB](http://www.mongodb.org/downloads) 。[安装 MongoDB](http://docs.mongodb.org/manual/installation/) 页面包含在各种操作系统上安装 Mongo 的指南。为了使事情变得简单，我们将从一个[普通样板](http://www.mean.io/)项目开始。简单地克隆样板文件并使用 npm 安装依赖项，如下面的清单所示。

```
git clone http://github.com/linnovate/mean.git
cd mean
npm install
```

这将安装所需的软件包。接下来，我们需要将 MongoDB 运行的默认端口设置为`27017`,如样板文件的自述文件中所指定的。打开文件`/etc/mongodb.conf`，取消对行`port = 27017`的注释。现在，重启`mongod`服务器，如下所示。

```
mongod --config /etc/mongodb.conf
```

接下来，在项目目录中简单地输入`grunt`。如果一切顺利，您将看到如下消息:

```
Express app started on port 3000
```

现在服务器正在运行，在浏览器中导航到`http://localhost:3000/`来查看样板应用程序的运行。

## 样板概述

我们现在有了一个全功能的样板应用程序。它实现了身份验证，包括使用社交媒体登录。我们不会深入探讨这个问题，但会创建我们自己的小应用程序。如果你看一下应用程序的结构，`public`文件夹包含我们的 AngularJS 前端，`server`文件夹包含我们的 NodeJS 后端。

## 创建列表视图

首先，让我们开始创建我们的前端使用 AngularJS。导航到`public`文件夹。创建一个名为`bucketList`的新文件夹，我们将在其中保存我们的前端文件。在`bucketList`目录下，创建子目录`controllers`、`routes`、`services`和`views`。在`bucketList`文件夹中也创建一个名为`bucketList.js`的文件，其中包含以下代码。

```
'use strict';

angular.module('mean.bucketList', []);
```

接下来，打开`mean/public/init.js`并添加模块`mean.bucketList`。修改后的部分应该如下所示:

```
angular.module('mean', ['ngCookies', 'ngResource', 'ui.bootstrap', 'ui.router', 'mean.system', 'mean.articles', 'mean.auth', 'mean.bucketList']);
```

现在，导航到`public/bucketList/routes`并添加`bucketList.js` route 文件以在我们的应用中处理路由。实现这一点的代码如下所示。

```
'use strict';

//Setting up route
angular.module('mean.bucketList').config(['$stateProvider', '$urlRouterProvider',
  function($stateProvider, $urlRouterProvider) {
    // states for my app
    $stateProvider
      .state('all bucket list', {
        url: '/bucketList',
        templateUrl: 'public/bucketList/views/list.html'
      });
  }
]);
```

在`public/bucketList/views/`中创建一个名为`list.html`的文件。这是我们的视图，它将显示我们的清单。该文件的内容如下所示。

```
<section data-ng-controller="BucketListController">
  Welcome to the bucket list collection
</section>
```

另外，在包含以下代码的`public/bucketList/controllers`中创建一个名为`bucketList.js`的文件。

```
'use strict';

angular.module('mean.bucketList').controller('BucketListController', ['$scope', '$stateParams', '$location', 'Global',
  function($scope, $stateParams, $location, Global) {
    $scope.global = Global;
  }
]);
```

接下来，使用`grunt`启动应用程序。如果 MongoDB 还没有运行，请确保它也在运行。将您的浏览器导航到`http://localhost:3000/#!/bucketList`，您应该会看到我们创建的列表视图。如果你对 url 中的`#!`有疑问，它只是用来区分 AngularJS 和 NodeJS 路由。

## 添加到遗愿清单

让我们创建一个视图，将事情添加到我们的清单中。在`public/bucketList/views`中添加一个名为`create.html`的新 HTML 文件，包含以下代码。

```
<section data-ng-controller="BucketListController">
  <form class="form-horizontal col-md-6" role="form" data-ng-submit="create()">
    <div class="form-group">
      <label for="title" class="col-md-2 control-label">Title</label>
      <div class="col-md-10">
        <input type="text" class="form-control" data-ng-model="title" id="title" placeholder="Title" required>
      </div>
    </div>
    <div class="form-group">
      <label for="description" class="col-md-2 control-label">Description</label>
      <div class="col-md-10">
        <textarea data-ng-model="description" id="description" cols="30" rows="10" placeholder="Description" class="form-control" required></textarea>
      </div>
    </div>
    <div class="form-group">
      <div class="col-md-offset-2 col-md-10">
        <button type="submit" class="btn btn-default">Submit</button>
      </div>
    </div>
  </form>
</section>
```

这段代码附加了`BucketListController`控制器。还要注意，在表单提交时，会调用一个名为`create()`的方法。接下来，让我们在`BucketListController`中创建一个名为`create()`的方法。下面的代码必须添加到`public/bucketList/controllers/bucketList.js`中，如下所示。我们在控制器中注入了`BucketList`服务，我们需要与后端进行交互。

```
'use strict';

angular.module('mean.bucketList').controller('BucketListController', ['$scope', '$stateParams', '$location', 'Global', 'BucketList',
  function ($scope, $stateParams, $location, Global, BucketList) {
    $scope.global = Global;

    $scope.create = function() {
      var bucketList = new BucketList({
        title: this.title,
        description: this.description
      });

      bucketList.$save(function(response) {
        $location.path('/bucketList');
      });
    };
  }
]);
```

`public/bucketList/services/bucketList.js`的内容如下所示。

```
'use strict';

angular.module('mean.bucketList').factory('BucketList', ['$resource',
  function($resource) {
    return $resource('bucketList);
  }
]);
```

我们还需要添加一条路线来将项目添加到清单中。修改`public/bucketList/routes/bucketList.js`，增加一个状态，如下图所示。

```
'use strict';

//Setting up route
angular.module('mean.bucketList').config(['$stateProvider', '$urlRouterProvider',
  function($stateProvider, $urlRouterProvider) {
    // states for my app
    $stateProvider
      .state('all bucket list', {
        url: '/bucketList',
        templateUrl: 'public/bucketList/views/list.html'
      })
      .state('add bucket list', {
        url: '/addBucketList',
        templateUrl: 'public/bucketList/views/create.html'
      })
  }
]);
```

重启服务器并导航至`http://localhost:3000/#!/addBucketList`。您应该会看到遗愿清单创建表单。不幸的是，它还没有功能。我们还需要创建后端。

## 创建后端

遗愿清单应该有标题、描述和状态。所以，在`server/models/bucketlist.js`中创建一个名为`bucketlist.js`的新文件，并添加以下代码。

```
'use strict';

/**
 * Module dependencies.
 */
var mongoose = require('mongoose'),
  Schema = mongoose.Schema;

/**
 * Bucket List Schema
 */
var BucketListSchema = new Schema({
  created: {
    type: Date,
    default: Date.now
  },
  title: {
    type: String,
    default: '',
    trim: true
  },
  description: {
    type: String,
    default: '',
    trim: true
  },
  status: {
    type: Boolean,
    default: false
  }
});

mongoose.model('BucketList', BucketListSchema);
```

我们需要配置快速路由，以便正确处理来自 AngularJS 的服务呼叫。创建一个名为`server/routes/bucketList.js`的文件，包含以下代码。

```
'use strict';

var bucketList = require('../controllers/bucketList');

module.exports = function (app) {
  app.post('/bucketList', bucketList.create);
};
```

对`/bucketList`的请求由`bucketList.create()`方法处理。这个方法属于服务器控制器`bucketList.js`，我们仍然需要创建它。`server/controllers/bucketList.js`的内容应该是这样的:

```
'use strict';

/**
 * Module dependencies.
 */
var mongoose = require('mongoose'),
  BucketList = mongoose.model('BucketList');

/**
 * Create an Bucket List
 */
exports.create = function(req, res) {
  var bucketList = new BucketList(req.body);

  bucketList.save(function(err) {
    if (err) {
      console.log(err);
    } else {
      res.jsonp(bucketList);
    }
  });
};
```

还有很多要清理，但我们可以检查它是否如预期的那样工作。当用户提交 AngularJS 表单时，它调用 AngularJS 服务，后者调用服务器端的`create()`方法，然后将数据插入 MongoDB。

提交表单后，我们可以检查数据是否正确地插入到 Mongo 中。为了在 MongoDB 中检查数据，打开另一个终端并发出以下命令。

```
mongo                   // Enter the MongoDB shell prompt
show dbs;               // Shows the existing Dbs
use mean-dev;           // Selects the Db mean-dev
show collections;       // Show the existing collections in mean-dev
db.bucketlists.find()   //Show the contents of bucketlists collection
```

## 创建存储桶列表视图

首先，在`server/routes/bucketList.js`中添加一条新路线:

```
app.get('/bucketList', bucketList.all);
```

这个新路由调用控制器的`all()`方法。将此方法添加到`server/controllers/bucketList.js`中，如下所示。这段代码在`bucketList`集合中查找条目并返回它们。

```
exports.all = function(req, res) {
  BucketList.find().exec(function(err, bucketList) {
    if (err) {
      console.log(err);
    } else {
      res.jsonp(bucketList);
    }
  });
};
```

接下来，在`public/bucketList/controllers/bucketList.js`中添加一个新方法，如下所示。

```
$scope.getAllBucketList = function() {
  BucketList.query(function(bucketList) {
    $scope.bucketList = bucketList;
  });
};
```

这段代码从 Mongo 获取数据，并将其保存在我们的`$scope.bucketList`变量中。现在，我们只需要将它绑定到我们的 HTML。这是在`public/bucketList/views/list.html`中完成的:

```
<section data-ng-controller="BucketListController" data-ng-init="getAllBucketList()">
  <ul class="bucketList unstyled">
    <li data-ng-repeat="item in bucketList">
      <span>{{item.created | date:'medium'}}</span> /
      <span>{{item.title}}</span>

      <div>{{item.description}}</div>
    </li>
  </ul>
  <a href="/#!/addBucketList">Create One</a>
</section>
```

重启服务器并导航至`http://localhost:3000/#!/bucketList`。这将显示清单项目。您也可以点击列表下方的“创建”链接，尝试添加新项目。

## 结论

在本文中，我们着重于使用均值堆栈创建一个简单的应用程序。我们实现了向 MongoDB 添加一个条目，并显示来自 DB 的条目。如果您对扩展这个示例感兴趣，可以尝试添加更新和删除操作。这篇文章的代码可以在 [GitHub](https://github.com/jay3dec/MeanSampleApp) 上找到。

## 分享这篇文章