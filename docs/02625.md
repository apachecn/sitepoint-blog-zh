# 将您的应用程序升级到 Angular 1.5 组件及更高版本！

> 原文：<https://www.sitepoint.com/upgrade-to-angular-components/>

*这篇文章由[丹普林斯](https://www.sitepoint.com/author/dprince)和[米凯拉莱尔](https://www.sitepoint.com/author/mlehr/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

随着 AngularJS 的每一个新版本，开发团队都在试图弥合 AngularJS 1.x 和 2 之间的差距。随着 AngularJS 1.5 的发布，开发者将能够编写结构上类似于 [AngularJS 2.0](https://angular.io/) 的应用。

在本教程中，我们将在 AngularJS 1.4 中创建一个网格[指令](https://docs.angularjs.org/guide/directive)。然后，我们将逐步将其升级到 1.5，然后看看如何将其转换为 2.0 版。

## 入门指南

让我们首先创建一个名为`AngularMigrateApp`的项目目录。在这个文件夹中，创建一个名为`index.html`的 HTML 页面。页面应该是这样的:

```
<!DOCTYPE html>
<html lang="en" ng-app="myApp" class="no-js">
<head>
    <title>My AngularJS App</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
</head>
<body>
    <script src="https://code.angularjs.org/1.4.10/angular.js"></script>
</body>
</html> 
```

除了角度框架，我们还将利用 [Bootstrap](http://getbootstrap.com/) 来设计我们的方向布局。我们包括这两个直接来自 CDNs 的文件。

## 创建网格指令

让我们创建一个简单的 grid 指令来显示 JSON 数组。我们将从创建 AngularJS 模块开始。

```
angular.module('myApp', [])
    .constant('employees', [{
        firstName: 'Rima',
        lastName: 'George',
        location: 'San Francisco'
    }, {
        firstName: 'Shaun',
        lastName: 'John',
        location: 'Germany'
    }, {
        firstName: 'Rahul',
        lastName: 'Kurup',
        location: 'Bangalore'
    }, {
        firstName: 'Samson',
        lastName: 'Davis',
        location: 'Canada'
    }, {
        firstName: 'Shilpa',
        lastName: 'Agarwal',
        location: 'Noida'
    }])

    .controller('HomeCtrl', ['$scope', 'employees', function($scope, employees) {
        $scope.employees = employees;
    }]) 
```

我们定义了一个名为`employees`的常数，它保存了一组示例数据。然后我们将这个数组注入到`HomeCtrl`中，并使它在控制器的范围内可用。

让我们创建一个名为`myGrid`的指令，我们将使用它来显示上面的 JSON 数组。

```
.directive('myGrid', function() {
    return {
    }
}) 
```

我们希望通过标记名来使用该指令，如下所示:

```
<my-grid></my-grid> 
```

因此，我们将添加 restrict 选项来指定:

```
.directive('myGrid', function() {
    return {
        restrict: 'E'
    }
}) 
```

接下来，我们希望将视图中的`employees`数据传递给指令。因此，我们将它作为绑定添加:

```
.directive('myGrid', function() {
    return {
        restrict: 'E',
        scope: {
            info: '=info'
        }
    }
}) 
```

现在我们可以将`employees`数据作为属性传递给指令:

```
<my-grid info="employees"></my-grid> 
```

最后但同样重要的是，我们需要一个 HTML 模板来显示数据:

```
.directive('myGrid', function() {
    return {
        restrict: 'E',
        scope: {
            info: '=info'
        },
        templateUrl : '/directiveGrid.html'
    }
}) 
```

在`index.html`的正文中添加以下 HTML 模板脚本。

```
<script type="text/ng-template" id="/directiveGrid.html"> <div class="panel panel-primary">
        <div class="panel-heading">Site Point Directive Grid</div>
        <table class="table">
            <thead>
                <tr>
                    <th>FirstName</th>
                    <th>Last Name</th>
                    <th>Location</th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="employee in info">
                    <td>{{ employee.firstName }}</td>
                    <td>{{ employee.lastName }}</td>
                    <td>{{ employee.location }}</td>
                </tr>
            </tbody>
        </table>
    </div> </script> 
```

正如您在上面的代码中看到的，我们正在迭代`info`属性，并显示雇员列表中的每一项。

让我们在`index.html`中使用`myGrid`指令。添加以下代码:

```
<div ng-controller="HomeCtrl">
    <my-grid info="employees"></my-grid>
</div> 
```

我们指定了`HomeCtrl`控制器，并在其中使用了我们的指令。保存更改并浏览至`index.html`页面。下面是数据网格的运行演示:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [AngularJS 1.4 Demo](http://codepen.io/SitePoint/pen/bpOQZK/) 。