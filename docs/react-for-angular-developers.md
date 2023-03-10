# 为有棱角的开发者作出反应

> 原文：<https://www.sitepoint.com/react-for-angular-developers/>

**本文面向熟悉 Angular 1.x 并希望了解 React 更多信息的开发人员。我们将看看他们构建富 web 应用程序的不同方法，重叠的功能和 React 没有试图填补的空白。**

阅读后，您将了解 React 要解决的问题，以及如何使用您已经掌握的知识在自己的项目中开始使用 React。

## 框架与库

[Angular](https://angularjs.org/) 是一个*框架*，而 [React](https://facebook.github.io/react/) 是一个*库*只专注于视图层。使用框架和松散耦合的库集合都有成本和好处。

框架试图提供一个完整的解决方案，如果你是一个大型团队的一部分，它们可以通过模式和约定帮助组织代码。然而，拥有一个大的 API 会增加你写作时的认知负荷，你会花更多的时间阅读文档和记忆模式——尤其是在你还在学习的早期。

使用带有小 API 的松散耦合库集合更容易学习和掌握，但这意味着当遇到问题时，您需要用更多的代码来解决它们，或者根据需要引入外部库。这通常导致你不得不编写自己的框架来减少样板文件。

### 推荐课程

![](img/cba76132dc82ef2ac08afc0e875076ed.png)[Job-Ready Angular and TypeScript Training](https://ultimateangular.com/)Todd MottoThe ultimate resource to learn Angular and its ecosystem. Use coupon code **'SITEPOINT'** at checkout to get **25% off**.[](https://ultimateangular.com/)[](https://ultimateangular.com/)

## 非常好

Angular 为构建 web 应用程序提供了丰富的特性集。其特点包括:

*   带有双曲线动态表达式的 HTML 模板`{{ }}`
*   用于扩展 HTML 功能的内置指令，如`ng-model`、`ng-repeat`和`ng-class`
*   用于将逻辑分组并将数据传递给视图的控制器
*   双向绑定是保持视图和控制器同步的简单方法
*   一大堆模块，比如用于与服务器通信的`$http`和用于路由的`ngRoute`
*   用于创建自己的 HTML 语法的自定义指令
*   依赖注入，用于限制对象对应用程序特定部分的公开
*   共享业务逻辑服务
*   视图格式助手的过滤器。

反作用，另一方面，给你:

*   在单曲线中使用 JavaScript 表达式的模板的 JSX 语法`{ }`
*   组件，最像 Angular 的元素指令。

当涉及到应用程序结构的其余部分时，React 是独立的，它鼓励使用标准的 JavaScript APIs 而不是框架抽象。您可以使用 [fetch()](https://www.sitepoint.com/introduction-to-the-fetch-api/) 代替为服务器通信提供类似 [$http](https://www.sitepoint.com/api-calls-angularjs-http-service/) 的包装器。您可以自由使用像服务和过滤器这样的结构，但是 React 不会为它们提供抽象。您可以将它们放在 JavaScript 模块中，并根据需要在您的组件中使用它们。

因此，虽然 Angular 为常见任务提供了更多的抽象，但 React 有意避免了这一点，以使您更经常地编写标准 JavaScript，并对其他所有事情使用外部依赖。

## 拔靴带

初始化 Angular 应用程序需要一个模块、一个依赖列表和一个根元素。

```
let app = angular.module('app', [])
let root = document.querySelector('#root');
angular.element(root).ready(function() {
  angular.bootstrap(root, ['app']);
}); 
```

React 的入口点是将组件呈现到根节点中。也可能有多个根组件:

```
let root = document.querySelector('#root');
ReactDOM.render(<App />, root) 
```

## 模板

角度视图的剖析是复杂的，并且有许多职责。您的 HTML 模板包含混合的指令和表达式，它们将视图和相关的控制器联系在一起。数据通过`$scope`在多个上下文中流动。

在 React 中，组件*一直向下*，数据从组件树的顶部单向流向叶节点。 [JSX](https://www.sitepoint.com/getting-started-react-jsx/) 是编写组件最常见的语法，将熟悉的 XML 结构转换成 JavaScript。虽然这确实*类似于*模板语法，但它编译成嵌套的函数调用。

```
const App = React.createClass({
  render: function() {
    return (
      <Component>
        <div>{ 2 + 1 }</div>
        <Component prop="value" />
        <Component time={ new Date().getTime() }>
          <Component />
        </Component>
      </Component>
    )
  }
}) 
```

下面编译的代码应该有助于阐明上面的 JSX 表达式如何映射到`createElement(component, props, children)`函数调用:

```
var App = React.createClass({
  render: function render() {
    return React.createElement(
      Component,
      null,
      React.createElement("div", null, 2 + 1),
      React.createElement(Component, { prop: "value" }),
      React.createElement(
        Component,
        { time: new Date().getTime() },
        React.createElement(Component, null)
      )
    );
  }
}); 
```

## 模板指令

让我们看看 Angular 最常用的一些模板指令是如何在 React 组件中编写的。现在，React 没有模板，所以这些例子是位于组件的`render`函数内部的 JSX 代码。例如:

```
class MyComponent extends React.Component {
  render() {
    return (
      // JSX lives here
    )
  }
} 
```

### ng-重复

```
<ul>
  <li ng-repeat="word in words">{ word }</li>
</ul> 
```

我们可以使用标准的 JavaScript 循环机制，比如`map`来获取 JSX 中的元素数组。

```
<ul>
  { words.map((word)=> <li>{ word }</li> )}
</ul> 
```

### ng 级

```
<form ng-class="{ active: active, error: error }">
</form> 
```

在 React 中，我们只能自己为`className`属性创建以空格分隔的类列表。为此，通常使用现有的函数，如杰德·沃森的[类名](https://github.com/JedWatson/classnames)。

```
<form className={ classNames({active: active, error: error}) }>
</form> 
```

在 JSX 中考虑这些属性的方式就好像您直接在这些节点上设置属性。这就是为什么它是`className`而不是`class`属性名。

```
formNode.className = "active error"; 
```

### ng-if

```
<div>
  <p ng-if="enabled">Yep</p>
</div> 
```

语句在 JSX 内部不起作用，因为 JSX 只是函数调用和对象构造的语法糖。为此通常使用三元运算符，或者将条件逻辑移到 render 方法的顶部，在 JSX 之外。

```
// ternary
<div>
  { enabled ? <p>Enabled</p> : null }
</div>

// if/else outside of JSX
let node = null;
if (enabled) {
  node = <p>Enabled</p>;
}
<div>{ node }</div> 
```

### ng-显示/ng-隐藏

```
<p ng-show="alive">Living</p>
<p ng-hide="alive">Ghost</p> 
```

在 React 中，您可以直接设置样式属性，或者向 CSS 添加一个实用程序类，如`.hidden { display: none }`，以隐藏您的元素(Angular 就是这样处理它的)。

```
<p style={ display: alive ? 'block' : 'none' }>Living</p>
<p style={ display: alive ? 'none' : 'block' }>Ghost</p>

<p className={ classNames({ hidden: !alive }) }>Living</p>
<p className={ classNames({ hidden: alive }) }>Ghost</p> 
```

你现在已经掌握它的窍门了。代替特殊的模板语法和属性，您需要使用 JavaScript 来实现您想要的。

## 一个示例组件

React 的*组件*最像 Angular 的*指令*。它们主要用于将复杂的 DOM 结构和行为抽象成可重用的部分。下面是一个幻灯片组件的例子，它接受一组幻灯片，用导航元素呈现一列图像，并跟踪自己的`activeIndex`状态以高亮显示活动幻灯片。

```
<div ng-controller="SlideShowController">
  <slide-show slides="slides"></slide-show>
</div>
```

```
app.controller("SlideShowController", function($scope) {
  $scope.slides = [{
    imageUrl: "allan-beaver.jpg",
    caption: "Allan Allan Al Al Allan"
  }, {
    imageUrl: "steve-beaver.jpg",
    caption: "Steve Steve Steve"
  }];
});

app.directive("slideShow", function() {
  return {
    restrict: 'E',
    scope: {
      slides: '='
    },
    template: `
      <div class="slideshow">
        <ul class="slideshow-slides">
        <li ng-repeat="slide in slides" ng-class="{ active: $index == activeIndex }">
          <figure>
            <img ng-src="{{ slide.imageUrl }}" />
            <figcaption ng-show="slide.caption">{{ slide.caption }}</figcaption>
          </figure>
        </li>
        </ul>
        <ul class="slideshow-dots">
          <li ng-repeat="slide in slides" ng-class="{ active: $index == activeIndex }">
            <a ng-click="jumpToSlide($index)">{{ $index + 1 }}</a>
          </li>
        </ul>
      </div>
    `,
    link: function($scope, element, attrs) {
      $scope.activeIndex = 0;

      $scope.jumpToSlide = function(index) {
        $scope.activeIndex = index;
      };
    }
  };
}); 
```

### 角度中的幻灯片放映组件

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[角度幻灯片](http://codepen.io/SitePoint/pen/QyNJxO/)。