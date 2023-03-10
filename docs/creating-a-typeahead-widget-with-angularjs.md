# 用 AngularJS 创建一个 Typeahead 小部件

> 原文：<https://www.sitepoint.com/creating-a-typeahead-widget-with-angularjs/>

如果你开始一个 AngularJS 项目，你可能希望所有的组件都用 Angular 编写。尽管重用现有的 jQuery 插件是完全可能的，但是将一堆 jQuery 放在一个指令中并不总是正确的做法。我的建议是，首先检查同样的事情是否可以用更简单/更好的方式用 pure Angular 实现。这保持了应用程序代码的整洁和可维护性。本教程面向初学者，引导读者使用 AngularJS 创建一个简单的 TypeAhead 小部件。

## 概观

在本教程中，我们将构建一个简单的 TypeAhead 小部件，当有人开始在文本框中键入时，它会立即创建建议。我们将以这样的方式设计应用程序，最终产品将是非常可配置的，可以很容易地插入到现有的系统中。创建过程中涉及的基本步骤是:

*   创建一个与 RESTful API 交互的工厂，并返回用于自动完成建议的 JSON。
*   创建一个使用 JSON 数据并封装 typeahead 输入字段的指令。
*   保持指令的可配置性，以便最终用户可以配置以下选项。

### 配置选项

1.  作为建议的一部分显示的确切的 JSON 对象属性。
2.  控制器范围内将保存选定项的模型。
3.  控制器范围内的一个函数，当一个项目被选中时执行。
4.  typeahead 输入字段的占位符文本(提示)。

## 步骤 1:构建获取数据的工厂

作为第一步，让我们创建一个使用 Angular 的 [`$http`](http://docs.angularjs.org/api/ng.%24http) 服务与 RESTful APIs 交互的工厂。看看下面的片段:

```
var typeAhead = angular.module('app', []);

typeAhead.factory('dataFactory', function($http) {
  return {
    get: function(url) {
      return $http.get(url).then(function(resp) {
        return resp.data; // success callback returns this
      });
    }
  };
});
```

前面的代码创建了一个名为`dataFactory`的工厂，它从一个 API 中检索 JSON 数据。我们不会深入工厂的细节，但我们需要简要了解一下`$http`服务是如何工作的。您向`get()`函数传递一个 URL，该函数返回一个承诺。对这个承诺的另一个调用`then()`也返回另一个承诺(我们从工厂的`get()`函数返回这个承诺)。这个承诺通过传递给`then()`的成功回调的返回值来解决。所以，在我们的控制器内部，我们不直接与`$http`交互。相反，我们在控制器中请求 factory 的一个实例，并用一个 URL 调用它的`get()`函数。因此，我们与工厂交互的控制器代码如下所示:

```
typeAhead.controller('TypeAheadController', function($scope, dataFactory) { // DI in action
  dataFactory.get('states.json').then(function(data) {
    $scope.items = data;
  });
  $scope.name = ''; // This will hold the selected item
  $scope.onItemSelected = function() { // this gets executed when an item is selected
    console.log('selected=' + $scope.name);
  };
});
```

前面的代码使用了一个名为`states.json`的 API 端点，它返回美国各州的 JSON 列表。当数据可用时，我们将列表存储在范围模型`items`中。我们还使用模型`name`来保存选中的项目。最后，当用户选择一个特定的状态时，函数`onItemSelected()`被执行。

## 步骤 2:创建指令

让我们从下面显示的`typeahead`指令开始。

```
typeAhead.directive('typeahead', function($timeout) {
  return {
    restrict: 'AEC',
    scope: {
      items: '=',
      prompt: '@',
      title: '@',
      subtitle: '@',
      model: '=',
      onSelect: '&amp;'
    },
    link: function(scope, elem, attrs) {
    },
    templateUrl: 'templates/templateurl.html'
  };
});
```

在指令中，我们创建了一个定义了几个属性的隔离范围:

*   `items`:用于将 JSON 列表传递给隔离的作用域。
*   `prompt`:单向绑定，用于传递`typeahead`输入字段的占位符文本。
*   `title`和`subtitle`:自动完成字段的每个条目都有一个`title`和`subtitle`。大多数`typeAhead`小部件都是这样工作的。他们通常(如果不总是)在下拉建议中为每个条目提供两个字段。如果一个 JSON 对象有额外的属性，这将作为传递两个属性的方式，这两个属性将显示在下拉列表中的每个建议中。在我们的例子中，`title`对应于州名，而`subtitle`代表它的缩写。
*   `model`:双向绑定保存选择。
*   `onSelect`:方法绑定，用于在选择结束后执行控制器范围内的功能。

**注意:【JSON 响应示例如下所示:**

```
{
  "name": "Alabama",
  "abbreviation": "AL"
}
```

## 步骤 3:创建模板

现在，让我们创建一个将由指令使用的模板。

```
<input type="text" ng-model="model" placeholder="{{prompt}}" ng-keydown="selected=false" />
<br/>

<div class="items" ng-hide="!model.length || selected">
  <div class="item" ng-repeat="item in items | filter:model  track by $index" ng-click="handleSelection(item[title])" style="cursor:pointer" ng-class="{active:isCurrent($index)}" ng-mouseenter="setCurrent($index)">
    <p class="title">{{item[title]}}</p>
    <p class="subtitle">{{item[subtitle]}}</p>
  </div>
</div>
```

首先，我们呈现一个用户将要输入的输入文本字段。范围属性`prompt`被分配给`placeholder`属性。接下来，我们遍历状态列表并显示`name`和`abbreviation`属性。这些属性名是通过`title`和`subtitle`范围属性配置的。指令`ng-mouseenter`和`ng-class`用于在用户用鼠标悬停时高亮显示条目。接下来，我们使用`filter:model`，它根据输入到输入字段中的文本过滤列表。最后，当输入文本字段为空或者用户选择了一个项目时，我们使用了`ng-hide`指令来隐藏列表。`selected`属性在`handleSelection()`函数中被设置为`true`，当有人开始在输入字段中输入内容时被设置为 false `false`(以显示建议列表)。

## 第四步:更新`link`函数

接下来，让我们更新指令的`link`函数，如下所示。

```
link: function(scope, elem, attrs) {
  scope.handleSelection = function(selectedItem) {
    scope.model = selectedItem;
    scope.current = 0;
    scope.selected = true;
    $timeout(function() {
      scope.onSelect();
    }, 200);
  };
  scope.current = 0;
  scope.selected = true; // hides the list initially
  scope.isCurrent = function(index) {
    return scope.current == index;
  };
  scope.setCurrent = function(index) {
    scope.current = index;
  };
}
```

函数`handleSelection()`用选择的状态名更新作用域属性`model`。然后，我们重置了`current`和`selected`属性。接下来，我们调用函数`onSelect()`。因为赋值`scope.model=selecteditem`没有立即更新绑定的控制器范围属性，所以增加了延迟。在用所选项更新了模型之后，最好执行控制器范围回调函数。这就是我们使用`$timeout`服务的原因。

此外，函数`isCurrent()`和`setCurrent()`在模板中一起使用，以突出显示自动完成建议中的条目。下面的 CSS 也用来完成高亮过程。

```
.active {
  background-color: #C44741;
  color: #f2f2f2;
}
```

### 步骤 5:配置和使用指令

最后，让我们调用 HTML 中的指令，如下所示。

```
<div class="container" ng-controller="TypeAheadController">
  <h1>TypeAhead Using AngularJS</h1>
  <typeahead items="items" prompt="Start typing a US state" title="name" subtitle="abbreviation" model="name" on-select="onItemSelected()" />
</div>
```

## 结论

本教程向您展示了如何创建一个带有配置选项的 AngularJS TypeAhead 小部件。完整的源代码可以在 [GitHub](https://github.com/sitepoint-editors/angular-typeahead) 上下载。如果有不清楚的地方或者你想改进什么，请随意评论。另外，别忘了看看[的现场演示](https://sitepoint-editors.github.io/angular-typeahead/)。

## 分享这篇文章