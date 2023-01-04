# 在 AngularJS 应用程序中使用 RequireJS

> 原文：<https://www.sitepoint.com/using-requirejs-angularjs-applications/>

在编写大型 JavaScript 应用程序时，可以做的最简单的事情之一就是将代码库分成几个文件。这样做提高了代码的可维护性，但是增加了主 HTML 文档中脚本标签丢失或放错位置的可能性。随着文件数量的增加，跟踪依赖关系变得很困难。这个问题在大角度应用中也持续存在。我们有许多工具负责在应用程序中加载依赖项。

在本文中，我们将看到如何使用 RequireJS 和 AngularJS 来简化加载依赖项的工作。我们还将研究如何使用 Grunt 来生成包含 RequireJS 模块的组合文件。

## 要求简介

RequireJS 是一个 JavaScript 库，有助于延迟加载 JavaScript 依赖项。模块只是 JavaScript 文件，其中包含一些必需的语法糖。RequireJS 实现 CommonJS 指定的[异步模块](http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition)。RequireJS 提供了简单的 API 来创建和引用模块。

RequireJS 需要一个包含基本配置数据的主文件，比如模块和垫片的路径。下面的代码片段显示了一个`main.js`文件的框架:

```
require.config({
map:{
// Maps
},
paths:{
// Aliases and paths of modules
},
shim:{
// Modules and their dependent modules
}
});
```

不需要在 paths 部分指定应用程序中的所有模块。其他的可以使用它们的相对路径来加载。要定义一个模块，我们需要使用`define()`块。

```
define([
// Dependencies
], function(
// Dependency objects
){

function myModule() {
// Can use the dependency objects received above
}

return myModule;
});
```

一个模块可能有一些从属模块。一般来说，一个对象在一个模块的末尾被返回，但这不是必须的。

## Angular 的依赖注入与需求依赖管理

我从 Angular 开发人员那里听到的一个常见问题是关于 Angular 的依赖管理和 RequireJS 之间的区别。重要的是要记住这两个库的目的是完全不同的。AngularJS 内置的依赖注入系统处理组件中需要的对象；而 RequireJS 中的依赖管理处理模块或 JavaScript 文件。

当 RequireJS 试图加载一个模块时，它会检查所有的依赖模块并首先加载它们。加载的模块的对象被缓存，当再次请求相同的模块时，它们被服务。另一方面，AngularJS 维护一个带有名称和相应对象列表的注入器。当创建一个组件时，会在注入器中添加一个条目，只要使用注册的名称引用该对象，就会为其提供服务。

## 将 RequireJS 和 AngularJS 一起使用

本文附带的[可下载代码](https://github.com/jsprodotcom/source/blob/master/AngularRequireJsSample.zip)是一个包含两个页面的简单应用程序。它具有以下外部依赖性:

*   要求的
*   jQuery
*   安古斯
*   角度路线
*   角资源
*   角度界面网格

这些文件应该按照这里列出的顺序直接加载到页面上。我们有五个定制脚本文件，包含所需 AngularJS 组件的代码。让我们来看看这些文件是如何定义的。

## 将 AngularJS 组件定义为 RequireJS 模块

任何 AngularJS 组件都包括:

*   函数定义
*   依赖注入
*   注册到角度模块

在上述三个任务中，我们将在单独的模块中执行前两个任务，而第三个任务将在一个负责创建 AngularJS 模块的单独模块中执行。

首先，让我们定义一个配置块。config 块不依赖于任何其他块，并最终返回 config 函数。但是，在我们将配置模块装入另一个模块之前，我们需要装入配置块所需的所有内容。以下代码包含在`config.js`中:

```
define([],function(){
function config($routeProvider) {
$routeProvider.when('/home', {templateUrl: 'templates/home.html', controller: 'ideasHomeController'})
.when('/details/:id',{templateUrl:'templates/ideaDetails.html', controller:'ideaDetailsController'})
.otherwise({redirectTo: '/home'});
}
config.$inject=['$routeProvider'];

return config;
});
```

注意上面代码片段中执行依赖注入的方式。我使用`$inject`来获得注入的依赖项，因为上面定义的配置函数是一个普通的 JavaScript 函数。在关闭模块之前，我们返回 config 函数，这样就可以将它发送到相关模块供进一步使用。

我们遵循同样的方法来定义任何其他类型的角度组件，因为我们在这些文件中没有任何组件特定的代码。以下代码片段显示了控制器的定义:

```
define([], function() {
function ideasHomeController($scope, ideasDataSvc) {
$scope.ideaName = 'Todo List';
$scope.gridOptions = {
data: 'ideas',
columnDefs: [
{field: 'name', displayName: 'Name'},
{field: 'technologies', displayName: 'Technologies'},
{field: 'platform', displayName: 'Platforms'},
{field: 'status', displayName: 'Status'},
{field: 'devsNeeded', displayName: 'Vacancies'},
{field: 'id', displayName: 'View Details', cellTemplate: '&lt;a ng-href="#/details/{{row.getProperty(col.field)}}"&gt;View Details&lt;/a&gt;'}
],
enableColumnResize: true
};
ideasDataSvc.allIdeas().then(function(result){
$scope.ideas=result;
});
}

ideasHomeController.$inject=['$scope','ideasDataSvc'];

return ideasHomeController;
});
```

应用的角度模块取决于到目前为止定义的每个模块。该文件从所有其他文件中获取对象，并用 AngularJS 模块将它们挂钩。该文件可能会也可能不会返回任何结果，使用`angular.module()`可以从任何地方引用角度模块。下面的代码块定义了一个角度模块:

```
define(['app/config',
'app/ideasDataSvc',
'app/ideasHomeController',
'app/ideaDetailsController'],

function(config, ideasDataSvc, ideasHomeController, ideaDetailsController){
var app = angular.module('ideasApp', ['ngRoute','ngResource','ngGrid']);
app.config(config);
app.factory('ideasDataSvc',ideasDataSvc);
app.controller('ideasHomeController', ideasHomeController);
app.controller('ideaDetailsController',ideaDetailsController);
});
```

Angular 应用程序不能使用`ng-app`指令引导，因为所需的脚本文件是异步加载的。这里正确的方法是使用手动引导。这必须在一个名为`main.js`的特殊文件中完成。这需要首先加载定义角度模块的文件。该文件的代码如下所示。

```
require(['app/ideasModule'],
function() {
angular.bootstrap(document, ['ideasApp']);
}
);
```

## 配置 Grunt 以组合所需的模块

在部署 JavaScript 繁重的应用程序时，应该组合并缩小脚本文件，以优化脚本文件的下载速度。像 Grunt 这样的工具很容易实现这些任务的自动化。它定义了许多任务来简化前端部署过程。它有一个任务， *grunt-contrib-requirejs* 用于以正确的顺序组合 requirejs 文件模块，然后缩小结果文件。就像任何其他繁重的任务一样，它可以被配置为在部署的每个阶段表现不同。演示应用程序中可以使用以下配置:

```
requirejs: {
options: {
paths: {
'appFiles': './app'
},
removeCombined: true,
out: './app/requirejs/appIdeas-combined.js',
optimize: 'none',
name: 'main'
},
dev:{
options:{
optimize:'none'
}
},
release:{
options:{
optimize:'uglify'
}
}
}
```

当使用 dev 选项运行 grunt 时，这种配置会产生一个非精简文件，而当使用 release 选项运行 Grunt 时，会产生一个精简文件。

## 结论

当应用程序的大小超过一定数量的文件时，管理依赖关系就变得非常困难。像 RequireJS 这样的库使得定义依赖关系更加容易，并且不用担心文件的加载顺序。依赖性管理正在成为 JavaScript 应用程序不可或缺的一部分。AngularJS 2.0 将内置对 AMD 的支持。

## 分享这篇文章