# 智能前端和非智能后端:在 AngularJS 中保持状态

> 原文：<https://www.sitepoint.com/persisting-state-in-angularjs/>

状态是您在与网站交互时生成的东西，例如，通过点击按钮，或者在文本字段中键入一些文本。这个*状态*驻留在浏览器的 RAM 中，由数组、字符串和对象等 JavaScript 对象组成。

在本文中，我们将深入探讨一种模式，即获取这些数组和对象并持久化它们。要做到这一点，我们需要一些聪明的方法来将暂时的“垃圾”与我们不能丢失的真正重要的东西分开。

## 入门指南

假设我们有一个对象数组，每个对象都有“临时”键。我们不希望这些键在为持久存储(`localStorage`或 Ajax)发送对象数组时是持久的。

在我将要展示的例子中，我们将使用 AngularJS 应用程序。在其中，我们将不需要持久的东西放入每个对象中。所有不重要的事情都以下划线开始。

下面是我们开始的 HTML 代码:

```
<div ng-repeat="thing in things track by thing.id"
     ng-click="thing._expanded=!thing._expanded">
    <div ng-if="thing._expanded">
        EXPANDED VIEW
    </div>
    <div ng-if="!thing._expanded">
        collapsed view
    </div>
</div>
```

这是支持它的小 JavaScript 代码:

```
angular
  .module('app', [])
  .controller('Ctrl', ($scope) => {
    $scope.things = [
      {id: 1, key: 'Value'},
      {id: 2, key: 'Value2'},
      {id: 3, key: 'Value3'},
    ]
  });
```

如果你想现场观看这个例子，[看一下这个演示](http://codepen.io/SitePoint/pen/XXNdab)。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [XXNdab](http://codepen.io/SitePoint/pen/XXNdab/) 。