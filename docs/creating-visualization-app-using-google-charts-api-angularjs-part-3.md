# 使用 Google Charts API 和 AngularJS 创建可视化应用程序——第 3 部分

> 原文：<https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs-part-3/>

在本系列的第一和第二部分，我们主要关注 AngularJS 控制器和指令。在这一部分，我们将关注 AngularJS 的双向数据绑定特性。

## AngularJS 中的数据绑定

Angular 的数据绑定允许模型的变化自动反映在视图中，反之亦然。AngularJS 数据绑定的详细解释可以在[这里](http://docs.angularjs.org/guide/databinding)找到。

我们将为我们的可视化应用添加一些功能。首先，我们将添加一个下拉菜单，从中可以选择图形的类型。让我们在下拉列表中添加一些图表。打开`index.html`并添加一个`select`元素，如下所示:

```
<select id="chartType"></select>
```

如果我们愿意，我们可以只在 HTML 中定义下拉菜单的选项，但是让我们用有角度的方式来做。打开`controllers.js`，如下图所示定义选项。

```
$scope.chartTypes = [
  {typeName: 'PieChart', typeValue: 'PieChart'},
  {typeName: 'BarChart', typeValue: 'BarChart'},
  {typeName: 'ScatterChart', typeValue: 'ScatterChart'},
  {typeName: 'LineChart', typeValue: 'LineChart'}
];
$scope.chartType = $scope.chartTypes[0];
```

现在，`controllers.js`看起来是这样的:

```
'use strict';

/* Controllers */
google.load('visualization', '1', {packages: ['corechart']});
google.setOnLoadCallback(function() {
  angular.bootstrap(document.body, ['myApp']);
});
angular.module('myApp.controllers', []).
  controller('MyCtrl1', ['$scope',function($scope) {
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
    var chart = {};

    chart.data = data;
    chart.options = options;

    $scope.chartTypes = [
      {typeName: 'LineChart', typeValue: '1'},
      {typeName: 'BarChart', typeValue: '2'},
      {typeName: 'ColumnChart', typeValue: '3'},
      {typeName: 'PieChart', typeValue: '4'}
    ];
    $scope.chartType = $scope.chartTypes[0];
    $scope.chart = chart;
  }])
  .controller('MyCtrl2', [function() {
  }]);
```

现在，我们需要将`chartTypes`绑定到下拉菜单。在 AngularJS 中，我们可以使用 [ngOptions](http://docs.angularjs.org/api/ng.directive:select) 将选项绑定到下拉菜单。我们还需要将`chartType`绑定到下拉列表中选择的值，为此我们使用了 [ngModel](http://docs.angularjs.org/api/ng.directive:ngModel) 。因此，将名为`ng-options`和`ng-model`的属性添加到下拉列表中，如下所示。

```
<select id="chartType" ng-model="chartType" ng-options="c.typeName for c in chartTypes">
</select>
```

`ng-options`遍历`chartTypes`中的值，并将每个`typeName`绑定到下拉菜单。在运行节点服务器之前，我们需要修改`ng-controller`值，使其附加到`body`元素。生成的`index.html`文件如下所示。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My AngularJS App</title>
    <link rel="stylesheet" href="css/app.css" />
    <script type="text/javascript" src="https://www.google.com/jsapi"></script>
  </head>
  <body ng-controller="MyCtrl1">
    <div g-chart></div>
    <select id="chartType" ng-model="chartType" ng-options="c.typeName for c in chartTypes">
    </select>
    <div>Angular seed app: v<span app-version></span></div>

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

接下来，使用以下命令启动节点服务器。

```
node scripts/web-server.js
```

通过导航到`http://localhost:8000/app/index.html`，您应该会看到预填充的下拉列表。

## 更改图表类型

我们将使用`ngChange`根据下拉列表中的部分呈现我们的图表。在`controllers.js`中定义另一个`$scope`变量，如下所示。

```
$scope.selectType = function(type) {
  $scope.chart.type = type.typeValue;
}
```

我们还想设置默认的图表类型:

```
chart.type = $scope.chartTypes[0].typeValue;
```

在将`ng-change`添加到`select`元素之后，它看起来应该是这样的:

```
<select id="chartType" ng-change="selectType(chartType)" ng-model="chartType" ng-options="c.typeName for c in chartTypes">
</select>
```

更改图表类型会导致`$scope.chart.type`变量被更新。应该观察这种变化，以便图表相应地变化。为此，我们有一个叫做`$scope.$watch`的东西，它监视着`$scope`的变化。在`directives.js`中，将`link`回拨包裹在`$scope.$watch`内，如下图所示。

```
link: function($scope, elm, attrs) {
  $scope.$watch('chart', function() {
    var chart = new google.visualization.LineChart(elm[0]);

    chart.draw($scope.chart.data, $scope.chart.options);
  }, true);
}
```

这种改变导致对`$scope.chart`的每一次改变都触发回调函数。在`$scope.$watch`回调函数中，我们需要检查`$scope.chart.type`，并相应地创建一个图表对象。修改`directives.js`中的`gChart`指令，如下图所示。

```
.directive('gChart',function() {
  return {
    restrict: 'A',
    link: function($scope, elm, attrs) {
      $scope.$watch('chart', function() {
        var type = $scope.chart.type;
        var chart = '';

        if (type == '1') {
          chart = new google.visualization.LineChart(elm[0]);
        } else if (type == '2') {
          chart = new google.visualization.BarChart(elm[0]);
        } else if (type == '3') {
          chart = new google.visualization.ColumnChart(elm[0]);
        } else if (type == '4') {
          chart = new google.visualization.PieChart(elm[0]);
        }

        chart.draw($scope.chart.data, $scope.chart.options);
      },true);
    }
  };
});
```

现在，当您从下拉列表中选择不同的图表类型时，图表会更新。

## 结论

在本教程中，我们实现了一个下拉列表，并使用 Angular 的双向数据绑定来绑定它。在我们的下一个教程中，我们将着重于添加更多的功能和引导应用程序，给它一个良好的外观。与此同时，代码可以在 [GitHub](https://github.com/jay3dec/AngularJSVisualizationApp_Part3) 上获得，并且在 [Heroku](http://pure-castle-8667.herokuapp.com/app/index.html) 上有一个现场演示。

## 分享这篇文章