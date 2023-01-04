# AngularJS 未来新型路由器介绍

> 原文：<https://www.sitepoint.com/introduction-futuristic-new-router-angularjs/>

[AngularJS](https://angularjs.org/) 是最流行的 JavaScript MV*框架之一，广泛用于构建单页面应用(SPA)。spa 中最具挑战性的特性之一是路由。客户端的路由包括更改视图的一部分，并在浏览器的导航历史中创建一个条目。作为一个全功能的客户端框架，AngularJS 一直支持通过`ngRoute`模块进行路由。虽然这对于基本路线来说已经足够好了，但是它不支持更复杂的场景，例如嵌套视图、平行视图或视图序列。

用于 Angular 2 的[新路由器目前正在开发中，它将被移植回 Angular 1.4。在本文中，我们将看到新路由器如何被用来定义路由，以及它如何解决一些`ngRoute`无法解决的问题。](https://github.com/angular/router)

如前所述，在撰写本文时，新路由器的开发工作仍在进行中，一些 API 可能会在以后发生变化。Angular 团队还没有为新路由器想出一个名字，所以现在它被称为未来路由器。

## ngRoute 的局限性

`ngRoute`没有考虑复杂的企业应用程序。我个人见过一些应用程序，其中页面的某些部分需要分几个步骤加载。可以使用`ngRoute`构建这样的应用程序，但是几乎不可能为应用于视图的每一个变化都提供 URL 状态。

在`ng-app`指令的实例中，`ng-view`指令只能使用一次。这阻止了我们创建并行路线，因为我们不能同时加载两个并行视图。

要在`ng-view`中呈现的视图模板不能包含另一个`ng-view`指令。这阻止了我们创建嵌套视图。

新路由器解决了这些问题，并提供了定义和使用路由的灵活方式。新路由器还采用了 [`Controller as`语法](http://toddmotto.com/digging-into-angulars-controller-as-syntax/)。我强烈推荐使用`Controller as`语法，因为这是今天为 Angular 2 做准备要遵循的惯例之一。

## 创建简单的路线

新路由器的设计考虑了 Angular 2。Angular 2 将通过消除模块配置阶段来简化依赖注入，这意味着我们不需要编写配置块来定义路由——我们可以在任何地方定义它们。

添加到新路由器的每条路由都由两部分组成:

*   `path`:路线模板的 URL
*   `component`:模板和控制器的组合。按照惯例，控制器和模板都必须以组件命名

使用`$router`服务配置路线。由于`$router`是一个服务，我们可以在应用程序中的任何地方定义路线(除了在提供者或配置块中)。然而，我们需要确保应用程序一加载，就执行定义路线的代码块。例如，如果路由是在一个控制器中定义的(我们很快就会这么做)，那么控制器必须在页面加载时执行。如果它们是在服务中定义的，那么服务方法必须在运行块中执行。

## 在模板间导航

让我们定义两条简单的路由，并使用新路由器在它们之间导航。如果您想理解这段代码，您需要获取新路由器的副本。 *[演示一下](#)* 。

您可以通过 [npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 在每个项目的基础上安装新路由器。

```
mkdir new-router && cd new-router
npm install angular-new-router
```

这将在您的项目目录中创建一个名为`node_modules`的文件夹。新路由器可以在`node_modules/angular-new-router/dist/router.es5.min.js`找到。在 AngularJS 本身之后将其包含在您的项目中。

首先，让我们定义一个模块并配置路由:

```
angular.module('simpleRouterDemo', ['ngNewRouter'])
  .controller('RouteController', ['$router', function($router){
    $router.config([
      { path:'/', redirectTo:'/first' },
      { path:'/first', component:'first' },
      { path:'/second/:name', component:'second' }
    ]);

    this.name='visitor';
  }])
```

上面代码片段中的控制器定义了三条路线。注意，根路由重定向到我们的第一个模板，第三个路由在 URL 中接受一个参数。如您所见，指定参数的语法与指定`ngRoute`的语法相同。

如前所述，每个组件都需要相应的视图模板和控制器。按照惯例，控制器的名称应该是以“控制器”为后缀的组件的名称(在我们的例子中是`firstController`和`secondController`)。视图样板的名称必须与构件的名称相同。它还必须驻留在一个与组件同名的文件夹中，在一个名为`components`的文件夹中。这将给我们:

```
projectRoot/
  components/
    first/
      first.html
    second/
      second.html
```

使用`$componentLoaderProvider`可以覆盖这些约定。稍后我们将看到一个这样的例子，但是现在让我们坚持约定。

接下来是上面使用的组件`first`和`second`的视图。我们使用`ng-template`指令来定义它们(这样我们可以重新创建一个可运行的演示程序)，但是理想情况下它们应该在单独的 HTML 文件中:

```
<script type="text/ng-template" id="./components/first/first.html"> <h3>{{first.message}}</h3> </script>

<script type="text/ng-template" id="./components/second/second.html"> <h3>{{second.message}}</h3> </script>
```

因为视图非常简单，所以控制器也非常简单:

```
angular.module('simpleRouterDemo')
  .controller('FirstController', function(){
    console.log('FirstController loaded');
    this.message = 'This is the first controller! You are in the first view.';
  })
  .controller('SecondController', function($routeParams){
    console.log('SecondController loaded');
    this.message = 'Hey ' + $routeParams.name + 
      ', you are now in the second view!';
  });
```

因为这两个控制器都是为使用`Controller as`语法而创建的，所以它们不接受`$scope`。[`$routeParams`服务](https://docs.angularjs.org/api/ngRoute/service/%24routeParams)用于检索路由中传递的参数值。

现在，我们需要加载这个控制器来注册路由:

```
<body ng-app="simpleRouterDemo" ng-controller="routeController as route">
</body>
```

最后，我们需要链接这些路线，并将它们加载到页面中。新路由器给[带来了`ng-link`指令](http://angular.github.io/router/getting-started#linking-to-routes)和`ng-viewport`指令，分别链接视图和加载模板。`ng-viewport`指令类似于`ng-view`；它是基于路径配置动态加载的应用程序的一部分的占位符。

以下代码片段显示了这些指令的用法:

```
<div class="col-md-3">
  <ul class="nav">
    <li>
      <a ng-link="first">First</a>
    </li>
    <li>
      <a ng-link="second({ name:route.name })">Second</a>
    </li>
  </ul>
</div>
<div class="col-md-9">
  <ng-viewport></ng-viewport>
</div>
```

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到 Angular 的新路由器在模板间导航的笔[。](http://codepen.io/SitePoint/pen/Ggbbwm/)



## 分享这篇文章