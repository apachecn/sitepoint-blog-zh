# 模拟 AngularJS 测试中的依赖关系

> 原文：<https://www.sitepoint.com/mocking-dependencies-angularjs-tests/>

AngularJS 的设计考虑到了测试。该框架的源代码经过了很好的测试，任何使用该框架编写的代码都是可测试的。内置的依赖注入机制使得用 AngularJS 编写的每个组件都是可测试的。AngularJS 应用程序中的代码可以使用任何 JavaScript 测试框架进行单元测试。测试 AngularJS 代码最广泛使用的框架是 Jasmine。本文中的所有示例片段都是使用 Jasmine 编写的。如果您在 Angular 项目中使用任何其他测试框架，您仍然可以应用本文中讨论的思想。

本文假设你已经有了一些单元测试和测试 AngularJS 代码的经验。你不需要成为测试专家。如果您对测试有基本的了解，并且能够为 AngularJS 应用程序编写一些简单的测试用例，那么您可以继续阅读本文。

## 嘲笑在单元测试中的作用

每个单元测试的工作都是孤立地测试一段代码的功能。隔离测试中的系统有时是具有挑战性的，因为依赖关系可能来自不同的源集合，并且我们需要完全理解要模拟的对象的职责。

在像 JavaScript 这样的非静态类型语言中，模仿是很困难的，因为不容易理解要模仿的对象的结构。同时，它还提供了一种灵活性，只模仿被测系统当前使用的对象的一部分，而忽略其余部分。

## 角度测试中的模仿

由于 AngularJS 的主要目标之一是可测试性，核心团队走了额外的路程，以使测试更容易，并在 angular-mocks 模块中为我们提供了一组模拟。该模块由围绕一组 AngularJS 服务(即，$http，$timeout，$animate 等)的模拟组成，这些服务广泛用于任何 AngularJS 应用程序中。这个模块减少了开发人员编写测试的大量时间。

在为真实的业务应用程序编写测试时，这些模拟帮助很大。同时，它们不足以测试整个应用程序。我们需要模拟任何在框架中但没有被模拟的依赖——来自第三方插件、全局对象的依赖，或者在应用程序中创建的依赖。本文将介绍一些模仿 AngularJS 依赖的技巧。

### 嘲弄服务

服务是 AngularJS 应用程序中最常见的依赖类型。如你所知，服务在 AngularJS 中是一个超载的术语。它可以指服务、工厂、值、常量或提供者。我们将在下一节讨论提供者。可以通过以下方式之一来模仿服务:

*   使用服务的注入块和探测方法获取实际服务的实例。
*   使用$provide 实现模拟服务。

我不喜欢第一种方法，因为它可能导致调用服务方法的实际实现。我们将使用第二种方法来模拟以下服务:

```
angular.module('sampleServices', [])
  .service('util', function() {
    this.isNumber = function(num) {
      return !isNaN(num);
    };

    this.isDate = function(date) {
      return (date instanceof Date);
    };
  });
```

以下代码片段创建了上述服务的模拟:

```
module(function($provide) {
  $provide.service('util', function() {
    this.isNumber = jasmine.createSpy('isNumber').andCallFake(function(num) {
      //a fake implementation
    });
    this.isDate = jasmine.createSpy('isDate').andCallFake(function(num) {
      //a fake implementation
    });
  });
});

//Getting reference of the mocked service
var mockUtilSvc;

inject(function(util) {
  mockUtilSvc = util;
});
```

虽然上面的例子使用 Jasmine 创建间谍，但是您可以使用 Sinon.js 用等价的实现来替换它。

在加载了测试所需的所有模块之后，创建所有的模拟总是好的。否则，如果在加载的模块之一中定义了服务，模拟实现将被实际实现覆盖。

可以分别使用 *$provide.constant* 、 *$provide.factory* 和 *$provide.value* 来模拟常量、工厂和值。

### 嘲笑供应商

模仿提供者类似于模仿服务。编写提供者时必须遵循的所有规则，在嘲笑它们时也必须遵循。考虑以下提供者:

```
angular.module('mockingProviders',[])
  .provider('sample', function() {
    var registeredVals = [];

    this.register = function(val) {
      registeredVals.push(val);      
    };

    this.$get = function() {
      function getRegisteredVals() {
        return registeredVals;
      }

      return {
        getRegisteredVals: getRegisteredVals
      };
    };
  });
```

下面的代码片段为上面的提供者创建了一个模拟:

```
module(function($provide) {
  $provide.provider('sample', function() {
    this.register = jasmine.createSpy('register');

    this.$get = function() {
      var getRegisteredVals = jasmine.createSpy('getRegisteredVals');

      return {
        getRegisteredVals: getRegisteredVals
      };
    };
  });
});

//Getting reference of the provider
var sampleProviderObj;

module(function(sampleProvider) {
  sampleProviderObj = sampleProvider;
});
```

获取提供者的引用和其他单例之间的区别在于，提供者在 *inject()* lock 中不可用，因为此时提供者已被转换为工厂。我们可以使用一个*模块()*块来获取它们的对象。

在定义提供者的情况下， *$get* 方法的实现在测试中也是强制性的。如果你不需要在测试文件中的 *$get* 函数中定义的功能，你可以将它分配给一个空函数。

### 模拟模块

如果要加载到测试文件中的模块需要一堆其他的模块，那么被测试的模块就不能被加载，除非所有需要的模块都被加载了。加载所有这些模块有时会导致糟糕的测试，因为一些实际的服务方法可能会从测试中被调用。为了避免这些困难，我们可以创建虚拟模块来加载测试中的模块。

例如，假设以下代码表示一个添加了示例服务的模块:

```
angular.module('first', ['second', 'third'])
  //util and storage are defined in second and third respectively
  .service('sampleSvc', function(utilSvc, storageSvc) {
    //Service implementation
  });
```

以下代码是示例服务的测试文件中的 beforeEach 块:

```
beforeEach(function() {
  angular.module('second',[]);
  angular.module('third',[]);

  module('first');

  module(function($provide) {
    $provide.service('utilSvc', function() {
      // Mocking utilSvc
    });

    $provide.service('storageSvc', function() {
      // Mocking storageSvc
    });
  });
});
```

或者，我们也可以将服务的模拟实现添加到上面定义的虚拟模块中。

### 嘲笑方法返回承诺

如果不使用承诺，编写一个端到端的角度应用程序可能会很困难。测试一段依赖于返回承诺的方法的代码是一个挑战。一个普通的 Jasmine spy 将导致一些测试用例的失败，因为被测试的函数将期望一个具有实际承诺结构的对象。

异步方法可以用另一个返回带有静态值的承诺的异步方法来模拟。考虑以下工厂:

```
angular.module('moduleUsingPromise', [])
  .factory('dataSvc', function(dataSourceSvc, $q) {
    function getData() {
      var deferred = $q.defer();

      dataSourceSvc.getAllItems().then(function(data) {
        deferred.resolve(data);
      }, function(error) {
        deferred.reject(error);
      });

      return deferred.promise;
    }

    return {
      getData: getData
    };
  });
```

我们将在上面的工厂中测试 *getData()* 函数。正如我们所见，这取决于服务*数据源 vc* 的方法 *getAllItems()* 。在测试 *getData()* 方法的功能之前，我们需要模拟服务和方法。

$q 服务有方法 *when()* 和 *reject()* ，允许解析或拒绝带有静态值的承诺。这些方法在模拟返回承诺的方法的测试中非常有用。下面的代码片段模仿了 *dataSourceSvc* 工厂:

```
module(function($provide) {
  $provide.factory('dataSourceSvc', function($q) {
    var getAllItems = jasmine.createSpy('getAllItems').andCallFake(function() {
      var items = [];

      if (passPromise) {
        return $q.when(items);
      }
      else {
        return $q.reject('something went wrong');
      }
    });

    return {
      getAllItems: getAllItems
    };
  });
});
```

$q 承诺在下一个消化周期后完成其动作。摘要循环在实际应用中保持运行，但在测试中却没有。所以，我们需要手动调用 *$rootScope。$digest()* 为了强制执行承诺。以下代码片段显示了一个示例测试:

```
it('should resolve promise', function() {
  passPromise = true;

  var items;

  dataSvcObj.getData().then(function(data) {
    items=data;
  });
  rootScope.$digest();

  expect(mockDataSourceSvc.getAllItems).toHaveBeenCalled();
  expect(items).toEqual([]);
});
```

### 模仿全局对象

全局对象有以下来源:

1.  作为全局“窗口”对象一部分的对象(如 localStorage、indexedDb、Math 等)。
2.  由第三方库创建的对象，如 jQuery、下划线、moment、breeze 或任何其他库。

默认情况下，全局对象不能被模仿。我们需要遵循一定的步骤使它们可以被模仿。

我们可能不想模仿实用程序对象，例如 Math 对象或 *_* (由下划线库创建)的函数，因为它们的操作不执行任何业务逻辑，不操纵 UI，也不与数据源对话。但是像$这样的对象。ajax、localStorage、WebSockets、breeze、toastr 都要被嘲讽。因为，如果不进行模拟，这些对象将在单元测试执行时执行它们的实际操作，这可能会导致一些不必要的 UI 更新、网络调用，有时还会导致测试代码中的错误。

由于依赖注入，用 Angular 编写的每一段代码都是可测试的。DI 允许我们传递任何跟在实际对象的垫片后面的对象，只是为了让测试中的代码在执行时不会中断。全局对象如果可以注入，就可以被嘲讽。有两种方法可以使全局对象可注入:

1.  向需要全局对象的服务/控制器注入$window，通过$window 访问全局对象。例如，以下服务通过$window 使用 localStorage:

```
angular.module('someModule').service('storageSvc', function($window) {
  this.storeValue = function(key, value) {
    $window.localStorage.setItem(key, value);
  };
});
```

1.  使用全局对象创建一个值或常数，并在需要的地方注入它。例如，以下代码是 toastr 的常量:

```
angular.module('globalObjects',[])
  .constant('toastr', toastr);
```

我更喜欢使用常量而不是值来包装全局对象，因为常量可以被注入到配置块或提供程序中，而常量不能被修饰。

以下代码片段显示了对 localStorage 和 toastr 的模仿:

```
beforeEach(function() {
  module(function($provide) {
    $provide.constant('toastr', {
      warning: jasmine.createSpy('warning'),
      error: jasmine.createSpy('error')
    });
  });

  inject(function($window) {
    window = $window;

    spyOn(window.localStorage, 'getItem');
    spyOn(window.localStorage, 'setItem');
  });
});
```

## 结论

模仿是用任何语言编写单元测试的重要部分之一。正如我们所看到的，依赖注入在测试和模拟中扮演着重要的角色。代码必须以一种使功能易于测试的方式来组织。本文列出了测试 AngularJS 应用时最常见的对象集。与本文相关的代码可以从 [GitHub](https://github.com/jsprodotcom/source/blob/master/TestingTips-Mocking.zip) 下载。

## 分享这篇文章