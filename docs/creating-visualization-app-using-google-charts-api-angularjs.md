# 使用 Google Charts API 和 AngularJS 创建可视化应用程序

> 原文：<https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs/>

如今，JavaScript 无处不在。一些有用的 JavaScript 框架，如 Ember.js、Backbone.js 等。正在改变网络的面貌。Google 开发的 AngularJS 是最流行的框架之一。本文是三篇文章中的第一篇，将教你如何使用 AngularJS 构建一个可视化应用。示例应用程序将使用 Google Charts API 可视化数据。我们将使用 Angular 的一个非常棒的`two-way binding`特性，让我们的图表随着数据和用户输入的变化而动态变化。

在开始之前，我们先来看看如何使用 Google Charts API。对于这个应用程序的目的，我们将坚持一些基本的图表，如折线图，饼状图等。

## 谷歌图表

下面的例子直接来自 Google Charts 文档，给出了如何使用 Google Charts API 的快速复习。第一个脚本加载 AJAX API。在第二个脚本中，第一行加载可视化 API 和折线图包。第二行设置了一个在 Google Visualization API 加载时运行的回调。回调函数创建一个数据表，设置一些图表选项，实例化我们的图表，并创建图表。

```
<html>
  <head>
    <script type="text/javascript" src="https://www.google.com/jsapi"></script>
    <script type="text/javascript">
      google.load('visualization', '1', {packages:['corechart']});
      google.setOnLoadCallback(drawChart);

      function drawChart() {
        var data = google.visualization.arrayToDataTable([
          ['Year', 'Sales', 'Expenses'],
          ['2004',  1000,      400],
          ['2005',  1170,      460],
          ['2006',  660,       1120],
          ['2007',  1030,      540]
        ]);
        var options = {
          title: 'Company Performance'
        };
        var chart = new google.visualization.LineChart(document.getElementById('chart_div'));

        chart.draw(data, options);
      }
    </script>
  </head>
  <body>
    <div id="chart_div" style="width: 900px; height: 500px;"></div>
  </body>
</html>
```

如果你是这个 API 的新手，或者需要复习，我建议你阅读一下 Google Charts 文档。

## 安古斯

在开始使用 Angular 之前，您应该:

*   安装 [Node.js](http://nodejs.org/)
*   从 [GitHub](https://github.com/angular/angular-seed) 中克隆种子项目

在终端中，导航到 seed 项目，然后输入以下命令:

`cd angular-seed
node scripts/web-server.js`

您应该会在控制台上看到以下消息输出:

`HTTP Server running at http://localhost:8000/`

此时，您可以通过导航到`http://localhost:8000/app/index.html`来查看演示页面。

Angular 使用 MVC(模型-视图-控制器)设计模式。在本教程中，我们将重点关注控制器。目前，将控制器视为处理页面特定部分并使用视图呈现数据的逻辑。一旦我们开始编写应用程序，我们将对控制器有更好的了解。

现在，让我们来看看棱角种子项目。这是一个有角度的应用程序模板，我们将在其上构建我们的应用程序。在 angular seed 项目中导航至`app/js`。在那里，我们可以看到控制器、指令、应用程序、过滤器和服务。这些是我们在创建应用程序时将会用到的东西。

## 构建应用程序

用以下代码替换`index.html`中的代码:

```
<!DOCTYPE html>
<html lang="en" ng-app="myApp">
<head>
  <meta charset="utf-8">
  <title>My AngularJS App</title>
</head>
<body>
  <div ng-controller="MyCtrl1">{{name}}</div>
  <script src="lib/angular/angular.js"></script>
  <script src="lib/angular/angular-route.js"></script>
  <script src="js/app.js"></script>
  <script src="js/services.js"></script>
  <script src="js/controllers.js"></script>
  <script src="js/filters.js"></script>
  <script src="js/directives.js"></script>
</body>
</html>
```

### 控制器

如前所述，控制器包含处理页面特定部分的逻辑。在前面的代码示例中，请注意下面一行:

```
<div ng-controller="MyCtrl1">{{name}}</div>
```

这个`div`有一个值为`MyCtrl1`的`ng-controller`属性。`MyCtrl1`是在`app/js/controllers.js`文件中找到的一个控制器函数的名称。`ng-controller`属性被称为*指令*。Angular 指令用于增强 HTML，而`ng-controller`指令用于为页面的特定部分设置控制器。

`{{name}}`是用于将数据从控制器传递到视图的变量。现在，问题是，我们如何在`MyCtrl1`控制器中访问变量`name`。这就是`$scope`出现的原因。`$scope`是作为控制器和查看器之间通信机制的对象。检查以下修改后的`controllers.js`代码:

```
'use strict';

/* Controllers */

angular.module('myApp.controllers', []).
  controller('MyCtrl1', ['$scope',
    function($scope) {
      $scope.name = 'Jay';
    }
])
  .controller('MyCtrl2', [
    function() {

    }
]);
```

在前面的代码中，我们将`$scope`作为参数传递，并设置变量`name`。现在，只需使用以下命令重启 Node.js 服务器。

`node scripts/web-server.js`

现在，将浏览器 URL 指向`http://localhost:8000/app/index.html`，应该会显示名称。

## 创建图表

现在，让我们画一些图表。首先，在`index.html`中包含 Ajax API。

```
<script type="text/javascript" src="https://www.google.com/jsapi"></script>
```

接下来，修改`index.html`里面的`div`，如下图所示。

```
<div ng-controller="MyCtrl1" id="chartdiv"></div>
```

在`controllers.js`中，加载可视化 API 和折线图包。

```
google.load('visualization', '1', {packages:['corechart']});
```

一旦包被加载，我们需要初始化我们的 Angular 应用程序。

```
google.setOnLoadCallback(function() {
  angular.bootstrap(document.body, ['myApp']);
});
```

`angular.bootstrap`是一个用于手动启动 Angular app 的全局 API。只需将 Google 图表创建代码复制并粘贴到控制器函数中，这就是我们最终得到的结果:

```
'use strict';

/* Controllers */
google.load('visualization', '1', {
  packages: ['corechart']
});

google.setOnLoadCallback(function() {
  angular.bootstrap(document.body, ['myApp']);
});

angular.module('myApp.controllers', []).
  controller('MyCtrl1', ['$scope',
    function($scope) {
      var data = google.visualization.arrayToDataTable([
        ['Year', 'Sales', 'Expenses'],
        ['2004', 1000, 400],
        ['2005', 1170, 460],
        ['2006', 660, 1120],
        ['2007', 1030, 540]
      ]);
      var options = {
        title: 'Company Performance'
      };
      var chart = new google.visualization.LineChart(document.getElementById('chartdiv'));

      chart.draw(data, options);
    }
  ])
    .controller('MyCtrl2', [
      function() {

      }
  ]);
```

在运行代码之前，编辑`index.html`并从`html`标签中删除`ng-app="myApp"`。`ng-app`用应用程序引导元素。然而，由于我们已经在控制器代码中这样做了(用下面的代码行)，我们可以把它从 HTML 中删除。

```
angular.bootstrap(document.body, ['myApp']);
```

重启节点服务器并访问`http://localhost:8000/app/index.html`。您应该会看到基于虚拟数据的折线图。

## 结论

在本教程的这一部分，我们将重点放在角度控制器上。在下一篇文章中，我们将关注指令和$scope 的使用。同时，本文中的所有代码都可以在 [GitHub](https://github.com/jay3dec/drunken-octo-ironman.git) 上获得。

## 分享这篇文章