# AngularJS 测试:引导程序块、路线、事件和动画

> 原文：<https://www.sitepoint.com/angularjs-testing-tips-bootstrap-blocks-routes-events-animations/>

在构建和交付全功能软件的过程中，我们应用多种技术来检查软件的正确性和质量。单元测试就是这些技术之一。许多组织非常重视单元测试，因为它降低了发现和修复应用程序潜在问题的成本。

当我们开始用成千上万行 JavaScript 代码开发应用程序时，我们无法逃避测试代码。一些 JavaScript 开发者说测试 JavaScript 甚至更重要，因为语言的行为直到运行时才为人所知。

幸运的是，AngularJS 通过支持依赖注入(DI)等特性，使得测试使用框架编写的代码变得更加容易。在我过去的三篇文章中，我讨论了一些关于模仿的技巧，比如如何测试控制器、服务和提供者的 T2 和如何测试指令的 T4。本文将涵盖测试 AngularJS 应用程序的引导程序块(包括配置块、运行块和路由解析块)、范围事件和动画。

您可以从我们的 [GitHub repo](https://github.com/sitepoint-editors/AngularTestingTips-TestingBlocksAndEvents) 下载本文中使用的代码，在那里您还可以找到运行测试的说明。

## 测试配置和运行块

配置和运行块在模块生命周期的开始执行。它们包含控制模块、小部件或应用程序工作方式的重要逻辑。测试它们有点棘手，因为它们不能像其他组件一样被直接调用。与此同时，不能忽视他们，因为他们的作用至关重要。

考虑以下配置和运行块:

```
angular.module('configAndRunBlocks', ['ngRoute'])
    .config(function ($routeProvider) {
    $routeProvider.when('/home', {
        templateUrl: 'home.html',
        controller: 'HomeController',
        resolve: {
            bootstrap: ['$q', function ($q) {
                return $q.when({
                    prop: 'value'
                });
            }]
        }
    })
        .when('/details/:id', {
        templateUrl: 'details.html',
        controller: 'DetailsController'
    })
        .otherwise({
        redirectTo: '/home'
    });
})
    .run(function ($rootScope, messenger) {

    messenger.send('Bootstrapping application');
    $rootScope.$on('$locationChangeStart', function (event, next, current) {
        messenger.send('Changing route to ' + next + ' from ' + current);
    });
});
```

与测试提供者的情况类似，我们需要确保在测试 config 和 run 块中的功能之前加载了模块。因此，我们将使用一个空的注入块来加载模块。

下面的代码片段模仿了上述块中使用的依赖关系，并加载了该模块:

```
describe('config and run blocks', function () {
    var routeProvider, messenger;

    beforeEach(function () {
        module('ngRoute');

        module(function ($provide, $routeProvider) {
            routeProvider = $routeProvider;
            spyOn(routeProvider, 'when').andCallThrough();
            spyOn(routeProvider, 'otherwise').andCallThrough();

            messenger = {
                send: jasmine.createSpy('send')
            };
            $provide.value('messenger', messenger);
        });

        module('configAndRunBlocks');
    });

    beforeEach(inject());
});
```

我故意没有模仿`$routeProvider`对象，因为我们将在本文后面测试注册的路由。

现在模块已经加载，配置和运行块已经执行。所以，我们可以开始测试它们的行为。当配置块注册路由时，我们可以检查它是否注册了正确的路由。我们将测试预期的路由数量是否已注册。以下测试验证配置模块的功能:

```
describe('config block tests', function () {
    it('should have called registered 2 routes', function () {
        //Otherwise internally calls when. So, call count of when has to be 3
        expect(routeProvider.when.callCount).toBe(3);
    });

    it('should have registered a default route', function () {
        expect(routeProvider.otherwise).toHaveBeenCalled();
    });
});
```

示例代码中的 run 块调用一个服务并注册一个事件。我们将在本文后面测试该事件。现在，让我们测试对服务方法的调用:

```
describe('run block tests', function () {
    var rootScope;
    beforeEach(inject(function ($rootScope) {
        rootScope = $rootScope;
    }));
    it('should send application bootstrap message', function () {
        expect(messenger.send).toHaveBeenCalled();
        expect(messenger.send).toHaveBeenCalledWith("Bootstrapping application");
    });
});
```

## 测试范围事件

事件聚合是使两个对象相互交互的好方法之一，即使它们彼此完全不知道对方。AngularJS 通过`$scope`上的`$emit` / `$broadcast`事件提供这一功能。根据需要，应用程序中的任何对象都可以引发事件或侦听事件。

当应用程序运行时，事件的订阅者和发布者都可用。但是，因为单元测试是独立编写的，所以我们在单元测试中只有一个可用的对象。因此，测试规范必须模拟另一端，以便能够测试功能。

让我们测试上面 run 块中注册的事件:

```
$rootScope.$on('$locationChangeStart', function (event, next, current) {
    messenger.send('Changing route to ' + next + ' from ' + current);
});
```

每当应用程序的位置改变时，`$location`服务就会广播`$locationChangeStart`事件。如前所述，我们需要手动触发该事件，并测试消息是否由信使发送。以下测试执行此任务:

```
it('should handle the $locationChangeStart event', function () {
    var next = '/second';
    var current = '/first';
    rootScope.$broadcast('$locationChangeStart', next, current);
    expect(messenger.send).toHaveBeenCalled();
    expect(messenger.send).toHaveBeenCalledWith('Changing route to ' + next + ' from ' + current);
});
```

## 测试路线

路线定义了用户导航应用程序的方式。任何对路由配置的不当或意外更改都会导致糟糕的用户体验。因此，路线也应该有测试。

到目前为止， [ngRoute](https://docs.angularjs.org/api/ngRoute) 和 [ui-router](https://github.com/angular-ui/ui-router) 是 AngularJS 应用中使用最广泛的路由器。这两个提供者的路由必须在配置块中定义，而路由数据通过服务提供。通过服务`$route`可以获得用 ngRoute 配置的路线数据。ui 路由器的路由数据可通过服务`$state`获得。这些服务可用于检查是否配置了正确的路由集。

考虑下面的配置块:

```
angular.module('configAndRunBlocks', ['ngRoute'])
    .config(function ($routeProvider) {
    $routeProvider.when('/home', {
        templateUrl: 'home.html',
        controller: 'HomeController',
        resolve: {
            bootstrap: ['$q', function ($q) {
                return $q.when({
                    prop: 'value'
                });
            }]
        }
    })
        .when('/details/:id', {
        templateUrl: 'details.html',
        controller: 'DetailsController'
    })
        .otherwise({
        redirectTo: '/home'
    });
});
```

现在让我们测试这些路线。首先，让我们获取一个对`$route`服务的引用:

```
beforeEach(inject(function ($route) {
    route = $route;
}));
```

上面的`/home`路线配置了`templateUrl`，一个控制器和一个解析块。让我们编写断言来测试它们:

```
it('should have home route with right template, controller and a resolve block', function () {
    var homeRoute = route.routes['/home'];
    expect(homeRoute).toBeDefined();
    expect(homeRoute.controller).toEqual('HomeController');
    expect(homeRoute.templateUrl).toEqual('home.html');
    expect(homeRoute.resolve.bootstrap).toBeDefined();
});
```

详细路线的测试将是类似的。我们还使用 otherwise 块配置了一个默认路由。默认路线以`null`作为键值注册。以下是对它的测试:

```
it('should have a default route', function () {
    var defaultRoute = route.routes['null'];
    expect(defaultRoute).toBeDefined();
});
```

## 测试解析块

解析块是加载路由时创建的工厂，与路由相关联的控制器可以访问它们。这是一个有趣的测试场景，因为它们的范围仅限于路由，我们仍然需要获取对象的引用。

我认为测试 resolve 块的唯一方法是使用`$injector`服务调用它。一旦被调用，它就可以像任何其他工厂一样被测试。下面的代码片段测试了使用我们在上面创建的 home 路由配置的 resolve 块:

```
it('should return data on calling the resolve block', function () {
    var homeRoute = route.routes['/home'];
    var bootstrapResolveBlock = homeRoute.resolve.bootstrap;
    httpBackend.expectGET('home.html').respond('<div>This is the homepage!</div>');
    var bootstrapSvc = injector.invoke(bootstrapResolveBlock); //[1].call(q);
    bootstrapSvc.then(function (data) {
        expect(data).toEqual({
            prop: 'value'
        });
    });
    rootScope.$digest();
    httpBackend.flush();
});
```

我不得不在上面的测试中模拟`templateUrl`，因为 AngularJS 试图在 digest 循环被调用时移动到默认路由。

同样的方法也可以用来测试`$httpInterceptors`。

## 测试动画

测试动画的技术与测试指令有一些相似之处，但是测试动画更容易，因为动画不像指令那样复杂。

[angular-mocks](https://docs.angularjs.org/api/ngMock/object/angular.mock) 库包含模块`ngAnimateMock`来简化测试动画的工作。这个模块必须在测试动画之前加载。

考虑以下 JavaScript 动画:

```
angular.module('animationsApp', ['ngAnimate']).animation('.view-slide-in', function () {
    return {
        enter: function (element, done) {
            element.css({
                opacity: 0.5,
                position: "relative",
                top: "10px",
                left: "20px"
            })
                .animate({
                top: 0,
                left: 0,
                opacity: 1
            }, 500, done);
        },
        leave: function (element, done) {
            element.animate({
                opacity: 0.5,
                top: "10px",
                left: "20px"
            }, 100, done);
        }
    };
});
```

现在让我们编写测试来验证这个动画的正确性。我们需要加载所需的模块并获取所需对象的引用。

```
beforeEach(function () {
    module('ngAnimate', 'ngAnimateMock', 'animationsApp');
    inject(function ($animate, $rootScope, $rootElement) {
        $animate.enabled(true);
        animate = $animate;
        rootScope = $rootScope;
        rootElement = $rootElement;
        divElement = angular.element('<div class="view-slide-in">This is my view</div>');
        rootScope.$digest();
    });
});
```

为了测试上面定义的动画的 enter 部分，我们需要以编程方式让一个元素进入上面代码片段中引用的`rootElement`。

在测试动画之前需要记住的一件重要事情是，AngularJS 会阻止动画运行，直到第一个摘要循环完成。这样做是为了使初始绑定更快。上面代码片段中的最后一个语句启动了第一个摘要循环，这样我们就不必在每个测试中都这样做了。

让我们测试上面定义的输入动画。它有两个测试用例:

1.  输入时，元素应位于顶部 10px，左侧 20px，不透明度为 0.5
2.  输入 1 秒后，元素应位于顶部 0px 和左侧 0px，不透明度为 1。这必须是一个异步测试，因为控件在断言之前必须等待 1 秒钟

以下是对上述两种情况的测试:

```
it('element should start entering from bottom right', function () {
    animate.enter(divElement, rootElement);
    rootScope.$digest();

    expect(divElement.css('opacity')).toEqual('0.5');
    expect(divElement.css('position')).toEqual('relative');
    expect(divElement.css('top')).toEqual('10px');
    expect(divElement.css('left')).toEqual('20px');
});

it('element should be positioned after 1 sec', function (done) {
    animate.enter(divElement, rootElement);
    rootScope.$digest();

    setTimeout(function () {
        expect(divElement.css('opacity')).toEqual('1');
        expect(divElement.css('position')).toEqual('relative');
        expect(divElement.css('top')).toEqual('0px');
        expect(divElement.css('left')).toEqual('0px');
        done();
    }, 1000);
});
```

类似地，对于离开动画，我们需要在 100 毫秒后检查 CSS 属性的值。由于测试必须等待动画完成，我们需要让测试异步进行。

```
it('element should leave by sliding towards bottom right for 100ms', function (done) {
    rootElement.append(divElement);
    animate.leave(divElement, rootElement);
    rootScope.$digest();
    setTimeout(function () {
        expect(divElement.css('opacity')).toEqual('0.5');
        expect(divElement.css('top')).toEqual('10px');
        expect(divElement.css('left')).toEqual('20px');
        done();
    }, 105);
    //5 ms delay in the above snippet is to include some time for the digest cycle
});
```

## 结论

在这篇文章中，我介绍了过去两年在测试 AngularJS 代码时学到的大部分测试技巧。这并不是结束，当您为真实应用程序的业务场景编写测试时，您将会学到更多。我希望你现在对测试 AngularJS 代码有足够的了解。为什么要等？去为你到目前为止写的每一行代码写测试吧！

## 分享这篇文章