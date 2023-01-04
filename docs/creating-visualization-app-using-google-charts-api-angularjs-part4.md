# 使用 Google Charts API 和 Angular 的可视化应用程序:4

> 原文：<https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs-part4/>

在本系列的第一部分[、](https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs/)[第二部分](https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs-part-2/)和[第三部分](https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs-part-3/)中，我们重点关注了 AngularJS 的各个方面，比如指令、控制器、范围和双向数据绑定特性。在本系列的最后一部分，我们将学习如何基于 Twitter Bootstrap 的标记和来自 [UI Bootstrap](http://angular-ui.github.io/bootstrap) 的 CSS 来集成 AngularJS 指令。

## 引导应用程序

首先，让我们使用 [Twitter Bootstrap](http://getbootstrap.com/) 给我们的应用一个干净的外观。我们将使用一个初学者[模板](http://getbootstrap.com/examples/starter-template/)。下面是入门模板 html，姑且命名为`bootstrap_index.html`。

```
<html lang="en">

<head>
  <meta charset="utf-8"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge"/>
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <meta name="description" content=""/>
  <meta name="author" content=""/>

  <title>Starter Template for Bootstrap</title>

  <!-- Bootstrap core CSS -->
  <link href="css/bootstrap.min.css" rel="stylesheet"/>

  <!-- Custom styles for this template -->
  <link href="css/starter-template.css" rel="stylesheet"/>

</head>

<body>

  <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">Project name</a>
      </div>
      <div class="collapse navbar-collapse">
        <ul class="nav navbar-nav">
          <li class="active"><a href="#">Home</a>
          </li>
          <li><a href="#about">About</a>
          </li>
          <li><a href="#contact">Contact</a>
          </li>
        </ul>
      </div>
      <!--/.nav-collapse -->
    </div>
  </div>

  <div class="container">

    <div class="starter-template">
      <h1>Visualization App Using AngularJS</h1>
      <hr />
    </div>

  </div>

  <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
  <script src="lib/common/bootstrap.min.js"></script>
</body>

</html>
```

将其放在与`index.html`相同的目录中。还要确保将`bootstrap.min.css`、`starter_template.css`和`bootstrap.min.js`文件复制到各自的文件夹中。

在终端中，键入:

```
node scripts/web-server.js
```

然后将浏览器指向`http://localhost:5000/app/bootstrap_index.html`。您应该会看到初学者模板。

## 引导标签

现在，我们将添加一个来自 [AngularJS 引导](http://angular-ui.github.io/bootstrap/#/tabs)的 Tab 指令。首先，将`ui.bootstrap`模块添加到我们的应用程序中。打开`app/js/app.js`并添加`ui.bootstrap`模块。下面是修改后的`app.js`:

```
'use strict';

// Declare app level module which depends on filters, and services
angular.module('myApp', [
  'ngRoute',
  'myApp.filters',
  'myApp.services',
  'myApp.directives',
  'myApp.controllers',
  'ui.bootstrap'
]).
config(['$routeProvider',
  function ($routeProvider) {
    $routeProvider.when('/view1', {
      templateUrl: 'partials/partial1.html',
      controller: 'MyCtrl1'
    });
    $routeProvider.when('/', {
      templateUrl: 'index.html',
      controller: 'MyCtrl1'
    });
    $routeProvider.otherwise({
      redirectTo: '/view1'
    });
  }
]);
```

从 [UI 引导](http://angular-ui.github.io/bootstrap/)下载`ui-bootstrap-tpls-0.10.0.js`并包含在`bootstrap_index.html`中。也包括我们在`bootstrap_index.html`的`index.html`中包含的所有脚本。为了添加一个选项卡，只需在`bootstrap_index.html`中添加以下代码

```
<div>
  <tabset>
    <tab heading="Welcome">
      <h4>AngularJS Data Visualization App</h4>
    </tab>
    <tab heading="Visualization">Data Visualization</tab>

  </tabset>
</div>
```

下面是修改后的`bootstrap_index.html`的样子:

```
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>Starter Template for Bootstrap</title>

  <!-- Bootstrap core CSS -->
  <link href="css/bootstrap.min.css" rel="stylesheet">

  <!-- Custom styles for this template -->
  <link href="css/starter-template.css" rel="stylesheet">

</head>

<body>

  <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">Project name</a>
      </div>
      <div class="collapse navbar-collapse">
        <ul class="nav navbar-nav">
          <li class="active"><a href="#">Home</a>
          </li>
          <li><a href="#about">About</a>
          </li>
          <li><a href="#contact">Contact</a>
          </li>
        </ul>
      </div>
      <!--/.nav-collapse -->
    </div>
  </div>

  <div class="container">

    <div class="starter-template">
      <h1>Visualization App Using AngularJS</h1>
      <hr/>
    </div>
    <div>
      <tabset>
        <tab heading="Welcome">
          <h4>AngularJS Data Visualization App</h4>
        </tab>
        <tab heading="Visualization">Data Visualization</tab>

      </tabset>
    </div>
  </div>
  <script type="text/javascript" src="https://www.google.com/jsapi"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
  <script src="lib/common/bootstrap.min.js"></script>
  <script src="lib/angular/angular.js"></script>
  <script src="lib/common/ui-bootstrap-tpls-0.10.0.js"></script>
  <script src="lib/angular/angular-route.js"></script>
  <script src="js/app.js"></script>
  <script src="js/services.js"></script>
  <script src="js/controllers.js"></script>
  <script src="js/filters.js"></script>
  <script src="js/directives.js"></script>
</body>

</html>
```

重启节点服务器，将浏览器指向`http://localhost:5000/app/bootstrap_index.html`以查看引导选项卡。

接下来，我们将把我们的`index.html`集成到`bootstrap_index.html`的第二个选项卡中。从`index.html`中，复制主体内部的代码，添加一个`div`容器。它应该是这样的:

```
<div ng-controller="MyCtrl1">
  <div g-chart></div>
  <select id="chartType" ng-change="selectType(chartType)" ng-model="chartType" ng-options="c.typeName for c in chartTypes">
  </select>
  <div>Angular seed app: v<span app-version></span>
  </div>
</div>
```

如你所见，有一个额外的`div`用于定义这部分的控制器逻辑。在`bootstrap_index.html`中，将其粘贴到:

```
<tab heading="Visualization">Data Visualization</tab>
```

它现在应该是这样的:

```
<tab heading="Visualization">
  <div ng-controller="MyCtrl1">
    <div g-chart></div>
    <select id="chartType" ng-change="selectType(chartType)" ng-model="chartType" ng-options="c.typeName for c in chartTypes">
    </select>
    <div>Angular seed app: v<span app-version></span>
    </div>
  </div>
</tab>
```

重启节点服务器后，将浏览器指向`http://localhost:5000/app/bootstrap_index.html`。您应该在 visualization 选项卡中看到数据可视化部分。

## 引导下拉菜单

到目前为止，我们一直使用 HTML 下拉菜单来选择图表类型。让我们也引导下拉菜单。用以下 HTML 代码替换现有的`select`控件:

```
<li class="dropdown">
  <a class="dropdown-toggle">
    Change Chart Type
  </a>
  <ul class="dropdown-menu">
    <li ng-repeat="type in chartTypes">
      <a>{{type.typeName}}</a>
    </li>
  </ul>
</li>
```

重新启动服务器，刷新浏览器，您应该看到一个链接标题更改图表类型，而不是下拉列表。如果你点击它，它会展开成一个好看的下拉菜单。但是，它的功能还不全。所以，让我们把它功能化。

首先，我们需要设置要显示的默认图表类型，而不是更改图表类型。打开`app/js/controllers.js`并在`MyCtrl1`控制器中再声明一个名为`chart.typeName`的变量。

```
chart.typeName = $scope.chartTypes[0].typeName;
```

接下来，在`selectType`函数中设置它的值，如下所示。

```
$scope.selectType = function (type) {
  $scope.chart.type = type.typeValue;
  $scope.chart.typeName = type.typeName;
}
```

接下来，修改下拉 HTML 代码，如下所示。

```
<li class="dropdown">
  <a class="dropdown-toggle">
    {{chart.typeName}}
  </a>
  <ul class="dropdown-menu">
    <li ng-repeat="type in chartTypes">
      <a ng-click="selectType(type)">{{type.typeName}}</a>
    </li>
  </ul>
</li>
```

现在，从 visualization 选项卡的下拉列表中选择一个图表类型将导致图表发生变化。

## 结论

在本教程中，我们着重于在 Angular 应用程序中使用 AngularUI 团队创建的两个 AngularJS 指令。你可以试试其他指令[这里](http://angular-ui.github.io/bootstrap/)。同时， [GitHub](https://github.com/jay3dec/AngularJSVisualizationApp_Part4) 上有演示代码。在 [Heroku](http://glacial-retreat-1949.herokuapp.com/app/bootstrap_index.html) 也有现场演示。

## 分享这篇文章