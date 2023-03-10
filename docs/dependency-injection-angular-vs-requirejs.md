# 依赖注入:角度与要求

> 原文：<https://www.sitepoint.com/dependency-injection-angular-vs-requirejs/>

如果您以前构建过大型 JavaScript 应用程序，那么您很有可能会面临管理组件依赖关系的任务。您可以将组件视为功能块。它可以是函数、对象或实例。该块选择公开一个或多个公共方法。它也可以选择隐藏非公共功能。在本文中，我们将关注两个主要的库，AngularJS 和 RequireJS。我们将分析他们如何使用依赖注入来跨应用程序共享组件。

## 关于依赖注入的小故事

当您需要一种简单的方法将一个或多个组件引入到应用程序中时，依赖注入就变得必不可少。例如，假设您有两个名为`database`和`logger`的组件。假设`database`组件公开了方法`getAll`、`findById`、`create`、`update`和`delete`。组件的公共 API 中只有一个方法`saveNewLog`。让我们假设`logger`组件依赖于`database`组件来运行。使用依赖注入，我们可以在创建过程中将`database`组件作为对`logger`组件的依赖来传递。

为了让您更好地可视化依赖关系，我将用代码来编写它。注意，实际的语法取决于您使用的依赖注入库。Angular 和 RequireJS 具有不同的语法，所以下面的代码是一个通用示例，我们稍后将讨论这两个库的实际表示。

下面是`database` API:

```
function database() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {}
  };

  return publicApis;
}
```

这里是`logger` API:

```
function logger(database) {
  var publicApis = {
    saveNewLog: function() {}
  };

  return publicApis;
}
```

如您所见，我们正在将`database`组件传递到`logger`的构造函数中。处理`logger`实例化的应用程序部分必须为它提供一个`database`组件的实例。

## 依赖注入的需求

现在我们对什么是依赖注入有了更多的了解，让我们来确定它带来了什么好处。如果您是优秀 JavaScript 设计的倡导者，那么依赖注入的一些好处对您来说可能是显而易见的。如果不是，让我解释几个一般的好处。我相信无论你使用 AngularJS 还是 RequireJS，这些都适用。

### 测试变得很容易

测试变得更加容易，因为您可以提供模拟的依赖项，而不是真实的实现。

### 关注点分离

依赖注入允许你分离应用程序的各个部分，这样每个部分处理一个不同的任务。在上面的例子中，`database`模块只与处理数据库有关。`logger`模块只负责记录数据，无论是在数据库、文件还是控制台中。这样做的好处是更容易交换依赖性。如果我们后来决定需要使用基于文件的数据库而不是传统的关系数据库，我们只需传入一个不同的模块。这个模块只需公开与`database`模块相同的 API 方法，而`logger`模块将继续正常工作。

### 更容易重用组件

由于这种分离关注点的性质，我们可以重用组件。这使得重用遵循相同模式的外部库变得容易。

## 依赖性管理库

我们已经看到了一些好处，现在让我们比较一下游戏中的两个主要库——Angular 和 RequireJS。RequireJS 致力于依赖管理。AngularJS 提供的不仅仅是依赖管理，但是我们将只关注这个功能。

## 安古斯

AngularJS 有这些东西叫做食谱。配方类似于前面描述的组件。角度组件的例子有工厂、指令和过滤器。Angular 提供了几种将组件注入其他东西的方法。我们将以`database`和`logger`组件为例。

在我们深入研究用 Angular 进行依赖注入的不同方法之前，让我们先构建我们的示例场景。假设我们有一个名为`myModule`的角度模块，让我们创建一个`UserController`:

```
function UserController() {
  //some controller logic here
}
```

我们还定义了`database`和`logger`服务:

```
myModule.factory('database', function() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {}
  };

  return publicApis;
});

myModule.factory('logger', function(){
  var publicApis = {
    saveNewLog: function() {}
  };

  return publicApis;
});
```

让我们假设`UserController`依赖于`logger`组件来运行。当然，`logger`组件仍然依赖于`database`组件。我们可以用三种不同的方式来表示 AngularJS 中的依赖关系。

### 参数名推断

当读入依赖项时，该方法依赖于函数参数的名称。我们可以像这样把它应用到上面的例子中:

```
function UserController(logger) {
  //some controller logic here to use injected logger factory
}

myModule.factory('database', function() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {}
  };

  return publicApis;
});

myModule.factory('logger', function(database) {
  //use injected database factory here
  var publicApis = {
      saveNewLog: function() {}
  };

  return publicApis;
});
```

### 使用`$inject`

这种依赖注入方法在组件的函数上使用`$inject`属性。属性应该是一个指定依赖关系的字符串数组。对于`UserController`来说，这很容易做到。对于`logger`工厂，我们需要稍微修改一下上面的例子，这样我们可以将属性添加到它的函数中。既然它是一个匿名函数，我们应该首先把它定义为一个命名函数。接下来，我们可以附加所需的属性，如下所示。

```
function UserController(logger) {
  //some controller logic here to use injected logger factory
}

UserController['$inject'] = ['logger'];

myModule.factory('database', function() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {}
  };

  return publicApis;
});

function loggerFactory(database) {
  //use injected database factory here
  var publicApis = {
    saveNewLog: function() {}
  };

  return publicApis;
}

loggerFactory['$inject'] = ['database'];
myModule.factory('logger', loggerFactory);
```

### 使用数组符号

第三种方法是在定义`UserController`和`logger`工厂时，将一个数组作为第二个参数传入。在这里，我们也必须改变我们定义`UserController`的方式，这样我们才能使用这个方法。

```
function UserController(loggerFactory) {
  //some controller logic here to use injected logger factory
}

myModule.controller('UserController', ['logger', UserController]);

myModule.factory('database', function() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {} 
  };

  return publicApis;
});

function loggerFactory(database) {
  //use injected database factory here
  var publicApis = {
    saveNewLog: function() {}
  };

  return publicApis;
}

myModule.factory('logger', ['database', loggerFactory]);
```

## 要求的

RequireJS 的依赖注入通过在文件中包含组件来工作。每个组件都存在于自己独立的文件中。AngularJS 预先加载组件，而 RequireJS 只在需要时加载组件。它通过对服务器进行 Ajax 调用来获取组件所在的文件。

让我们看看 RequireJS 是如何从语法上处理依赖注入的。我将跳过如何设置 RequireJS。为此，请参考本网站的第[篇文章](https://www.sitepoint.com/understanding-requirejs-for-effective-javascript-module-loading/)。

与 RequireJS 依赖注入相关的两个主要函数是`define`和`require`。简而言之，`define`函数创建一个组件，而`require`函数用于在执行代码块之前加载一组依赖关系。让我们更深入地研究一下这两个函数。

### `define`功能

坚持使用`logger`和`database`示例，让我们将它们创建为组件(`filename:`注释指出了我们实际定义组件的位置):

```
//filename: database.js
define([], function() {
  var publicApis = {
    getAll: function() {},
    findById: function(id) {},
    create: function(newObject) {},
    update: function(id, objectProperties) {},
    delete: function(id) {}
  };

  return publicApis;
});

//filename: logger.js
define(['database'], function(database) {
  //use database component here somewhere
  var publicApis = {
    saveNewLog: function(logInformation) {}
  };

  return publicApis;
});
```

如您所见，`define`函数有两个参数。第一个是可选的组件数组，必须在定义组件之前加载。第二个参数是一个函数，它必须返回一些东西。您可能会注意到，我们将`database`组件作为定义`logger`模块的依赖项传入。`database`组件不依赖任何其他组件。因此，它的`define`函数将一个空数组作为第一个参数。

### `require`功能

现在，让我们来看一个场景，在这个场景中，我们使用已定义的组件。让我们模拟记录一些信息。因为我们需要`logger`组件能够使用它的代码，所以我们必须使用`require`函数将它拉进来。

```
require(['logger'], function(logger) {
  //some code here
  logger.saveNewLog('log information');
});
```

正如你在上面看到的，`require`函数只是用来运行一些代码，并不返回任何东西。它接受的第一个参数是依赖模块的数组。第二个是加载完这些依赖项后要运行的函数。这个函数接受的参数和要加载的依赖项一样多。每一个都代表相应的组件。

## 结论

说到依赖注入，我们就要结束 AngularJS 和 RequireJS 之间的比较了。尽管两者采取了完全不同的方法，但没有理由说它们不能一起工作。请让我们知道您使用这两个库的体验，或者您是否有任何其他要添加的内容。

## 分享这篇文章