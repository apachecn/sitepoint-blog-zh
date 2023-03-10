# Aurelia vs AngularJS 1.x —逐个特性比较

> 原文：<https://www.sitepoint.com/aurelia-vs-angular-feature-comparison/>

**更新 23.07.2016** :有几个人评论了这篇文章把 Aurelia 比作 Angular 1.x 而不是 Angular 2；这是故意的。虽然 Aurelia 与 Angular 2 确实是一个更公平的比较，但本文面向的是那些目前致力于 Angular 1.x 并正在寻找迁移到新框架的理由，但不确定是否迁移到 Angular 2 并需要证明不断变化的框架的成本/收益比的人和公司。我想提供一个详细的比较来帮助那些用户和公司做决定。

* * *

*这篇文章由[维尔丹·索蒂奇](https://github.com/zewa666)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在 web 开发和 JavaScript 的世界中，我们已经看到许多范例来来去去。但是有一种模式仍然存在:单页 web 应用程序。

在过去的六年中，最流行的登陆框架之一是 [AngularJS](https://angularjs.org/) 。它于 2010 年发布，由 Google 支持，承诺通过使用声明式双向数据绑定快速、轻松地开发丰富的客户端应用程序。AngularJS 获得了大量的追随者，很快成为 web 应用程序开发的首选方法，被 Amazon 和 Pluralsight 等公司使用。

2016 年的今天，AngularJS 已经开始显老了。一个名为 [Aurelia](http://aurelia.io/) 的新框架已经成为丰富的客户端应用程序的流行选择。Aurelia 最初是由杜兰达尔公司的 Rob Eisenberg 创建的，其目标是解决与 AngularJS 相同的问题。然而，Aurelia 使用了一种现代方法来简化开发，并解决了许多困扰 AngularJS 的问题。

在本文中，我们将详细了解 AngularJS 和 Aurelia，并比较和对比这两个框架。出于比较的目的，我们将 Angular 2 从框架比较中排除。相反，我们将只关注 AngularJS 1.x 框架。今天，我们将从 2016 年开发者的角度，对 2010 年设计的 AngularJS 方法和 Aurelia 使用的现代方法进行比较。

![Aurelia vs Angular](img/a57b3ab1f4c239f162d68bd19998a5a8.png)

## 《丛林奇兵》

AngularJS 和 Aurelia 都是客户端 JavaScript 框架，旨在创建单页面 web 应用程序。AngularJS 和 Aurelia 都支持直观的双向数据绑定、客户端路由和高级模板功能。AngularJS 和 Aurelia 都鼓励使用定制元素来扩展 HTML。AngularJS 和 Aurelia 都带有默认组件，这些组件封装了常见的功能。如前所述，AngularJS 和 Aurelia 的目标是相同的问题领域。那么，相似之处在哪里结束呢？

让我们快速看一下 AngularJS 和 Aurelia 的主要区别。

| 安古斯 | 蛹 |
| 所有人 | 符合标准 |
| 配置 | 惯例 |
| 复杂的 | 简单的 |
| 昂贵的 | 高效的 |
| 固定的；不变的 | 灵活的 |
| 整体的 | 模块化的 |

哇——等一下。你可能会说，嘿——看起来你把牌堆得有点高了。但是我想更深入地研究这些说法。

### 专有(AngularJS)与符合标准(Aurelia)

自 AngularJS 发布以来的六年里，Web 标准已经发生了变化。虽然 AngularJS 最初的设计遵循了当时的标准，但它被迫为没有明确规则的场景创建了许多专有解决方案。其中包括 JavaScript 语言标准和 HTML 模板。

#### JavaScript 语言标准

JavaScript 的语言和生态系统在不断向前发展；它的标准、特性和语法都在不断发展。虽然 AngularJS 在 2010 年被设计为利用网络浏览器功能，但 Aurelia 是在现代标准的基础上设计的。

AngularJS 提供了一个非标准的 JavaScript 模块格式实现，该实现旨在与 AngularJS 框架一起使用。相比之下，Aurelia 依赖 ES2015 模块标准。此外，Aurelia 利用新的语言结构(如 ES2016 decorators)来简化开发并支持新兴标准。

#### HTML 模板化

AngularJS 和 Aurelia 都允许你，作为一个开发者，以新的方式扩展 HTML。AngularJS 创建的时候，扩展 HTML 的标准还没有成熟。因此，AngularJS 为模板和定制元素创建了专有的解决方案。

今天，Web 组件规范为模板和定制元素定义了一组规则。Aurelia 积极遵守这些标准，支持影子 DOM、`<template>`元素、HTML 导入和本地定制元素。

### 配置(AngularJS)与常规(Aurelia)

当我第一次开始玩 Angular 时，我认为它很棒。学习如何配置 AngularJS 及其特定的代码调用并没有花费很多时间。然而，随着我对 AngularJS 越来越熟悉，并构建了更多的应用程序，Angular 的所有配置都开始碍事了。

AngularJS 要求您创建一个角度特定的模块。实际上，在 web 应用程序启动之前，您的应用程序将使用的所有东西都必须向框架显式注册，并进行配置。因此，在使用 AngularJS 模块之前，有必要将所有控制器、服务和自定义指令附加到它们上。此外，AngularJS 控制器通过代码耦合到视图:视图必须声明它打算使用的控制器。所有这些导致了大量的样板文件。让我们看一个使用 ES2015 的例子。

**hello.js**

```
// A Hello controller
export class Hello {
    constructor (userService) {
        this.userService = userService;
        this.greeting = "Hello, " + this.userService.getUser() + "!";
    }
}; 
```

**user-service.js**

```
// A User Service
export class UserService {
    getUser () {
        return "Newman";
    };
}; 
```

**index.js**

```
import {Hello} from 'hello';
import {UserService} from 'user-service';
// No matter how or where we declare our objects,
// we'll always have to use Angular's registration code
// to let AngularJS know about them.
angular.module('App', []);
    .controller('HelloCtrl', Hello)
    .service('UserService', UserService)
    ... and so on 
```

**hello.html**

```
<div data-ng-controller="HelloCtrl as hello">
    <h1>{{hello.greeting}}</h1>
    ...my view
</div> 
```

相比之下，Aurelia 不要求组件在使用前进行显式注册。该框架知道如何找到视图和视图模型，而不必使用默认约定显式配置它们。(如果有必要，可以通过配置来覆盖此约定，但显式配置不是强制性的。)最后，Aurelia 视图模型不是通过视图内部的代码耦合到视图的。

**hello.js**

```
// A Hello controller
export class Hello {
    constructor (userService) {
        this.userService = userService;
        this.greeting = "Hello, " + this.userService.getUser() + "!";
    }
}; 
```

**user-service.js**

```
// A User Service
export class UserService {
    getUser () {
        return "Newman";
    };
}; 
```

**index.js**

```
// We don't need to explicitly register our objects with
// Aurelia - so really, we don't even need this. 
```

**hello.html**

```
<template>
    <h1>${greeting}</h1>
    ...my view
</template> 
```

这意味着开始使用 Aurelia 很容易:开发人员需要学习使用的特定于框架的代码很少。Aurelia 的开箱即用的约定支持快速开发并减少学习曲线。然而，在更加熟悉 Aurelia 之后，如果您愿意，您可以更改它的约定——如果您不这样做，只需处理较少的特定于框架的代码。

### 复杂(AngularJS)与简单(Aurelia)

根据我使用 AngularJS 的经验，虽然一些基本概念可能相当简单，但高级概念在结构和语义上都很复杂。有些事情(比如编写可扩展的组件和模块)并不太糟糕，而其他事情(复杂的指令)则几乎是晦涩难懂的。Aurelia 旨在简化其先进理念的执行，创造一个更平坦的学习曲线。

#### 语义学

AngularJS 使用复杂的语义。为了真正利用框架，开发人员必须了解它们。例如，在 AngularJS 中，你可以声明一个`service`、一个`factory`、一个`value`或者一个`constant` : AngularJS 对所有这些进行了区分。你也可以声明一个`controller`，和一个`directive`。不幸的是，这些指令很少有相同的约定——尤其是 AngularJS 指令。

在 AngularJS 中，指令是一个强大的构造——允许应用程序使用自定义元素、属性和行为来扩展 HTML。不幸的是，他们也是一个先进的概念，他们可以有一个陡峭的学习曲线。

解释 AngularJS 指令超出了本文的范围，但是请相信我。让我们来看一个示例指令。

**index.html**

```
<body ng-controller="MainCtrl">
  <h1>What's your favorite Javascript framework?</h1>
  <choose-framework></choose-framework>
</body> 
```

**chooseFramework.html**

```
<div>
  <input id="framework-input" type="text" ng-model="framework" placeholder="Choose a framework" />
  <button data-ng-click="choose()">Choose</button>
  <p ng-if="chosen">You prefer {{chosenFramework}}!</p>
</div> 
```

**chooseFramework.js**

```
app.directive('chooseFramework', function() {
  return {
    scope: {
      framework: '',
      chosen: false,
    },
    restrict: 'E',
    replace: true,
    templateUrl: 'chooseFramework.html',
    link: function(scope, elem, attrs) {
      // Assume we're using jQueryUI autocomplete.
      $('#framework-input').autoComplete(['AngularJS', 'Aurelia', 'VanillaJS']);
    },
    controller: function ($scope) {
      $scope.choose = function () {
        // Log our preference somewhere.
        alert('Your framework choice has been stored for posterity.');
        $scope.chosenFramework = $scope.framework;
        $scope.chosen = true;
      }
    }
  };
}); 
```

相反，Aurelia 简化了所有这些语义，减少了学习曲线。它完全消除了声明步骤，允许您以更简单的方式将代码作为依赖项注入。此外，Aurelia 使用定义良好的生命周期方法而不是事件，因此视图模型和定制元素之间共享代码约定。这使得编写和推理代码变得简单。最后，晦涩难懂的 AngularJS 指令声明被自定义元素取代，其工作方式与 Aurelia viewmodels 相同。

让我们来看看:

**index.html**

```
<body>
  <h1>What's your favorite Javascript framework?</h1>
  <choose-framework></choose-framework>
</body> 
```

**chooseFramework.html**

```
<div>
  <input id="framework-input" type="text" value.bind="framework" placeholder="Choose a framework" />
  <button click.delegate="choose()">Choose</button>
  <p if.bind="chosen">You prefer ${chosenFramework}!</p>
</div> 
```

**chooseFramework.js**

```
@customElement('choose-framework')
export class ChooseFramework {
  constructor () {
    this.framework = '';
    this.chosen = false;
  }

  attached () {
    // Assume we're using jQueryUI autocomplete.
    $('#framework-input').autoComplete(['AngularJS', 'Aurelia', 'VanillaJS']);
  }

  choose () {
    // Log our preference somewhere.
    alert('Your framework choice has been stored for posterity.');
    this.chosenFramework = this.framework;
    this.chosen = false;
  }
} 
```

#### 互用性

由于其变化检测的工作方式，AngularJS 不能检测框架本身没有的对象或属性的变化。本质上，如果一个变化发生在 AngularJS 摘要周期之外，那么必须通知它，以便它能够拾取它。在实践中，虽然 AngularJS 提供了一些现成的通用功能(如超时、间隔和承诺)的服务包装器，但这意味着任何做出更改的第三方库都必须进行包装，以通知 AngularJS 发生了更改。您最终会编写出许多样板代码，如下所示:

```
$scope.$apply(function () {
    // some asynchronous activity that updates scope, such as a
    // timeout or an interval
    $scope.value = 'updated';
}); 
```

(在你意识到这一点之后，你就没事了——但是在你知道发生了什么之前，你很容易陷入这个陷阱，就像我在这里做的。然而，意识到这一点后，你将会写很多代码。)

Aurelia 不需要这些包装器，这意味着占用空间减少，约定更简单。这也意味着集成第三方组件和库要容易得多。

### 昂贵(AngularJS)与高效(Aurelia)

如果你曾经以任何重要的方式使用 AngularJS，你可能会遇到性能问题——特别是当使用`ng-repeat`处理一个非常大的列表时。AngularJS 的性能受到其变化检测方法的阻碍:脏检查。

Angular 的变化检测依赖于一个“消化周期”。本质上，AngularJS 将定义一个时间间隔，在每个时间间隔结束时，它将“消化”自上次消化以来发生的所有更改。这种情况每秒钟发生多次。虽然这种方法有效，但它有三个主要缺点。首先，即使没有发生变化，每次都会检查每个属性；第二，它需要持续的 CPU 活动；最后，当许多属性需要变更检测时，基于时间的摘要周期将开始停滞。

此外，当 AngularJS 对一个变更事件做出响应时，它会针对该变更立即更新 DOM。一个摘要周期中的许多不同更改会导致大量浏览器重绘，从而产生瓶颈并损害性能。

相比之下，Aurelia 的变化检测观察实际变化的属性，而不是以设定的时间间隔扫描变化。通过选择这种现代方法，Aurelia 实质上回避了上述三个问题。Aurelia 可以使用脏检查，但它只会作为一个极端的退路。在所有其他情况下，Aurelia 的变化检测不会导致困扰 AngularJS 的性能问题。

最后，Aurelia 使用微任务批量更新所有 DOM，而不是每次更改都更新 DOM，这大大减少了完全更新视图所需的浏览器重绘次数。这提高了 web 浏览器和移动设备的性能。

实际上，所有这些都使得 Aurelia 比 AngularJS 更快，性能更好。在 web 浏览器环境中，这一点很重要，但在移动设备上更为重要，因为在移动设备上，性能是最重要的考虑因素。然而，这也意味着作为一名开发人员，您可以用您的应用程序做更多的事情，而不会遇到性能障碍。

### 固定(成角)与灵活(奥雷利亚)

在我使用 AngularJS 的经验中，它的固定约定工作得很好，直到你遇到框架没有预料到的情况。我们将看看 AngularJS 和 Aurelia 实现之间的两个主要区别。

#### 有角度的物体

对于 AngularJS，框架假设有一个`window`对象可用:当脚本被加载时，它将创建一个`window.angular`全局变量。Aurelia 的方法抛弃了过时的全局变量约定。相反，核心框架库导出一个可在任何实例中使用的 Aurelia 对象。

给`window`附加一个变量不一定是不合理的假设；AngularJS 和 Aurelia 都是 web 框架，所以你可能会在 web 浏览器中运行它们，对吗？

根据我的经验，不一定是这样。AngularJS 中的单元测试和端到端测试需要像 Karma 这样的测试框架以及 AngularJS 特定的模拟库；这可能会使测试变得沉重和难以设置。([我自己也遇到了这个问题。](http://stackoverflow.com/q/22413767/677526))相比之下，由于 Aurelia 是模块化的，不需要`window`和`document`在场，因此测试变得更加简单。

另外，同构 JavaScript 在 Aurelia 中成为可能，而默认的 AngularJS 实现永远不会允许。这也意味着我们可以创建嵌套的 Aurelia 应用程序——这需要 AngularJS 进行一些创造性的编码。

#### 应用程序配置

在 AngularJS 中创建 web 应用程序时，必须在 AngularJS 启动应用程序之前配置框架和所有提供程序。由于框架的设计方式，不支持在 AngularJS 引导后进行配置。因此，在 web 应用程序启动后，您的配置是固定的，不能更改。

相比之下，Aurelia 允许在运行时进行动态配置。它有一个在引导时配置应用程序的约定，但是配置不是静态的。这使得您的配置非常灵活，可以根据您的应用需求进行调整。

一个实际的例子是 Angular 中的`$http`服务配置和 Aurelia 中的 HTTP 服务配置。这两个框架都允许开发人员创建“拦截器”——可以转换传入或传出 AJAX 调用的中间件。然而，AngularJS 要求在应用程序启动之前定义这些拦截器——这意味着它们不能在运行时被删除。(这实际上是人们在遇到的[真实世界场景。)](http://stackoverflow.com/a/21925209/677526)

### 整体式(AngularJS)与模块化(Aurelia)

作为一名开发人员，你曾经使用过只适用于自身的框架吗？根据我使用整体框架的经验，在框架范围内进行开发将是轻而易举的事情。但是一旦你需要打破这个模式，或者拒绝它的观点，你将不得不与这个框架斗争。

AngularJS 最初是作为一个整体框架建造的。它的所有组件，比如客户端路由、模板和绑定系统，都被打包成一个大的包。这不仅意味着总是需要整个包(即使是简单的应用程序)，而且 AngularJS 的整体架构使得在必要时很难移除组件并进行更改。(例如，Angular 的路由器组件就很明显。虽然后来的 AngularJS 版本通过模块化某些特性和服务在一定程度上缓解了这种情况，但核心框架本身仍然是一个紧密耦合的单一包。

相比之下，Aurelia 采用了更现代的方法。虽然它是一个完整的框架，但 Aurelia 是由一组库组成的，这些库使用定义良好的接口协同工作，因此它是完全模块化的。这意味着 web 应用程序只需要包含它所需要的依赖项。此外，这意味着只要实现符合定义的接口，单个组件就可以毫不费力地进行更改或交换。

举个例子，就拿 AngularJS 的原生依赖注入来说吧。在我的项目中，我有多个 AngularJS 模块，它们导出同名的服务，但是我发现原生注入器对所有模块使用单一的名称空间，这意味着用相同的名称注册服务会导致[与意外行为](http://stackoverflow.com/q/13406791/677526)的冲突。我希望 AngularJS 模块充当独立的 DI 容器，以避免任何注入器冲突，因此我编写了一个依赖注入组件来解决这个问题。遗憾的是，由于 AngularJS 的服务层是 AngularJS 的核心组件，不改变 AngularJS 的核心，我无法去掉它或改变它。除非我重新构建框架或更改我的应用程序代码，否则我会陷入困境。

在 Aurelia 中，尽管依赖注入是框架内部使用的核心组件，但因为它是一个模块化组件，我可以用自己的实现替换它——只要我遵守 Aurelia 的依赖注入接口。如果我发现原生注入器有问题，我可以换入我自己的依赖注入实现，而不必重新构建框架、修改任何应用程序代码或担心依赖组件。根据经验，能够交换框架组件是一种非常好的能力。

## 判决

是时候后退一步，稍微回顾一下了。我们从以下几个方面看了安古拉和奥雷利亚的比赛:

| 安古斯 | 蛹 |
| 所有人 | 符合标准 |
| 配置 | 惯例 |
| 复杂的 | 简单的 |
| 昂贵的 | 高效的 |
| 固定的；不变的 | 灵活的 |
| 整体的 | 模块化的 |

…根据这些比较，我只能得出一个合乎逻辑的结论:

| 安古斯 | 蛹 |
| 又旧又破 | 新热点 |

在这个比较中，听起来好像我在挑 AngularJS 的毛病。老实说，我有点担心。但这并不是说 AngularJS 是一个不好的框架。我在学习和使用它的过程中获得了很多乐趣，并且我能够用它制作一些很棒的东西。(事实上，如果不经常使用 AngularJS，我不可能如此详细地谈论它。)然而，AngularJS 有一些问题，这些问题在六年里都没有得到很好的解决。底线是这样的:在 2016 年的苹果比较中，Aurelia 比 AngularJS 更新鲜，更多汁。

事情的真相是这样的:如果你正在考虑选择 AngularJS 而不是像 Aurelia 这样更现代的框架，那么退一步重新评估可能是明智的。Aurelia 并不难学，有活跃的社区，对 AngularJS 开发者应该有些熟悉。但 Aurelia 真正奇妙的地方在于，虽然它确实提供了合理的默认设置，但它不会强迫你接受它的观点——根据我的经验，这意味着你可以依赖 Aurelia，而不是让它依赖你。使用以开发人员为中心的现代框架的好处是绝对值得的。

如果你有兴趣更好地了解 Aurelia，请查看以下 Aurelia 资源。

### 入门指南

*   [奥雷利亚骨架项目](http://www.github.com/aurelia/skeleton-navigation) (GitHub)
*   [奥雷利亚文档](http://aurelia.io/docs)
*   从安古拉兹迁徙到奥雷利亚

### 社区

*   [奥雷利亚吉特频道](http://gitter.im/Aurelia/Discuss)
*   StackOverflow 上的 Aurelia
*   奥雷利亚博客

## 分享这篇文章