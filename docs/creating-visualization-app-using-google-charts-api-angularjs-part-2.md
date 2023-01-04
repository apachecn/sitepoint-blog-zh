# 使用 Google Charts API 和 AngularJS 创建可视化应用程序——第 2 部分

> 原文：<https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs-part-2/>

本教程的[第一部分](https://www.sitepoint.com/creating-visualization-app-using-google-charts-api-angularjs/)集中于 AngularJS 控制器的使用和`$scope`的使用。教程的这一部分将关注角度指令及其与`$scope`的关系。我们将从第一篇文章中停止的地方开始。

## angularjs 指令

指令是 AngularJS 的另一个有趣而重要的方面。指令是定制的标签或属性，可以增强 HTML 的功能。例如，考虑一个简单的`input`元素:

```
<input type="text" />
```

现在，假设我们为 datepicker 编写了一个指令，并希望将它应用于上面的 input 元素。我们将按以下方式应用它。

```
<input type="text" datepicker />
```

在第一个教程中，我们收集了图表数据，以及图表选项，并将数据绑定到来自控制器的`div`。然而，指令提供了一种更好、更有组织的方式来完成同样的事情。在编写我们自己的指令之前，让我们来看看 Angular seed 项目中已经存在的一个指令。打开`app/js/directives.js`，找到以下代码:

```
'use strict';

/* Directives */

angular.module('myApp.directives', []).
  directive('appVersion', ['version', function(version) {
    return function($scope, elm, attrs) {
      elm.text(version);
    };
  }]);
```

这个例子定义了一个名为`appVersion`的指令。注意，当我们添加这个指令(其名称写在[came case](http://en.wikipedia.org/wiki/CamelCase))作为一个元素的定制标签时，外壳被转换为 [SnakeCase](http://en.wikipedia.org/wiki/Snake_case) 。

在这个指令中，我们为指令所附加的元素分配一个版本号。但是，这个版本号是从哪里来的？我们有一种叫做 AngularJS 的服务，这就是版本的来源。打开`app/js/services.js`，你可以看到那里定义的版本号。

```
'use strict';

/* Services */

// In this case it is a simple value service.
angular.module('myApp.services', []).
  value('version', '0.1');
```

为了在指令中访问`version`的值，使用以下代码将它注入到指令中:

```
directive('appVersion', ['version', function(version) {
```

然后，使用`elm.text(version);`将其分配给元素。

现在，只需在`index.html`上加一个`div`，如下所示。这将把`appVersion`指令附加到一个`span`元素上。

```
<div >Angular seed app: v<span app-version></span></div>
```

要运行该应用程序，请从终端窗口发出以下命令。

```
node scripts/web-server.js
```

接下来，将浏览器指向`[http://localhost:8000/app/index.html](http://localhost:8000/app/index.html)`。在`services.js`中定义的版本应该显示在页面上。现在，让我们创建一个显示图表的新指令。

## 创建 AngularJS 指令

让我们在`directives.js`中创建一个名为`gChart`的新指令:

```
'use strict';

/* Directives */

angular.module('myApp.directives', []).
directive('appVersion', ['version',
  function (version) {
    return function ($scope, elm, attrs) {
      elm.text(version);
    };
  }
])
  .directive('gChart', function () {
    return function ($scope, elm, attrs) {
    };
  });
```

在上一篇文章中，我们将数据绑定在控制器本身中。这一次我们将使用`$scope`传递数据，并将其绑定到指令中。修改后的代码如下所示。

```
'use strict';

/* Directives */

angular.module('myApp.directives', []).
directive('appVersion', ['version',
  function (version) {
    return function ($scope, elm, attrs) {
      elm.text(version);
    };
  }
])
  .directive('gChart', function () {
    return function ($scope, elm, attrs) {
      var chart = new google.visualization.LineChart(document.getElementById('chartdiv'));

      chart.draw($scope.chart.data, $scope.chart.options);
    };
  });
```

在`controllers.js`中，删除我们将数据绑定到`div`的最后两行，取而代之的是将图表数据和选项保存在`$scope`中。该文件现在应该如下所示:

```
'use strict';

/* Controllers */
google.load('visualization', '1', {packages:['corechart']});

google.setOnLoadCallback(function () {
  angular.bootstrap(document.body, ['myApp']);
});

angular.module('myApp.controllers', []).
  controller('MyCtrl1', ['$scope', function($scope) {
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
    var chart = {};

    chart.data = data;
    chart.options = options;
    $scope.chart = chart;
  }])
  .controller('MyCtrl2', [function() {
  }]);
```

现在，将`gChart`指令添加到`index.html`中的`div`中，如下所示:

```
<div ng-controller="MyCtrl1" id="chartdiv" g-chart></div>
```

接下来，运行应用程序，您应该能够看到图表。

此时，我们使用控制器来处理应用程序逻辑。通过`$scope`将数据分配给视图，并使用指令处理 DOM 操作。

## 有关指令的更多信息

当创建一个指令时，我们可以指定如何使用这个指令。例如，指令可以用作类、属性或标记。在本教程中，我们将使用我们的指令作为一个属性。因此，我们可以使用`restrict : A`显式声明。使用这种方法的一个极简指令如下所示。

```
.directive('gChart', function() {
  return {
    restrict: 'A',
    link: function($scope, elm, attrs) {
      var chart = new google.visualization.LineChart(document.getElementById('chartdiv'));

      chart.draw($scope.chart.data, $scope.chart.options);
    }
  };
});
```

我们还可以从指令代码中移除硬编码的`chartDiv`，并使用`elm`参数来访问它，如下所示。

```
.directive('gChart', function() {
  return {
    restrict: 'A',
    link: function($scope, elm, attrs) {
      var chart = new google.visualization.LineChart(elm[0]);

      chart.draw($scope.chart.data, $scope.chart.options);
    }
  };
});
```

## 结论

在本教程中，我们关注于指令的使用。在本教程的下一部分，我们将深入探讨 AngularJS 的双向绑定特性，并将它们整合到我们现有的应用程序中。同时，本文中的所有代码都可以在 [GitHub](https://github.com/jay3dec/AngularJSVisualizationApp_Part2) 上获得。

## 分享这篇文章