# 用 AngularJS 创建幻灯片放映插件

> 原文：<https://www.sitepoint.com/creating-slide-show-plugin-angularjs/>

您曾经编写数百行 jQuery 来创建自定义图像滑块的日子已经一去不复返了！是时候走棱角分明的道路，用更小、更简单的代码实现同样的目标了。本教程将展示如何用 AngularJS 创建一个幻灯片插件。我们将使用角度指令和动画来实现这一点。如果你不熟悉指令，请务必阅读我的 [AngularJS 指令系列](https://www.sitepoint.com/series/a-practical-guide-to-angularjs-directives/)。这种方法的另一个好处是，我们不需要编写一行 jQuery 代码！多酷啊。那么，我们开始吧。

## 步骤 1:创建 AngularJS 指令

当谈到 AngularJS 时，最重要的是首先设计应用程序，然后使用适当的标记/设计。因为我们希望我们的滑块是自包含的，并且容易插入到现有的 AngularJS 应用程序中，所以创建一个指令是正确的做法。因此，让我们从一个名为`slider`的空指令开始:

```
var sliderApp = angular.module('sliderApp', []);

sliderApp.directive('slider', function($timeout) {
  return {
    restrict: 'AE',
    replace: true,
    scope: {
      images: '='
    },
    link: function(scope, elem, attrs) {},
    templateUrl: 'templates/templateurl.html'
  };
});
```

需要注意的重要一点是，我们已经隔离了指令的范围。因为我们需要几个函数/属性仅供内部使用，所以我们选择创建一个独立的作用域，而不是污染父作用域。此外，我们应该能够接受来自父范围的图像列表进行显示。这就是我们使用`=`绑定的原因。最后，指令的模板放在`templateurl.html`文件中。

## 步骤 2:设置控制器来提供图像

接下来，让我们创建一个控制器，该控制器在其作用域内创建一个包含五个图像对象的数组。稍后我们将把这些图像传递给指令。

```
sliderApp.controller('SliderController', function($scope) {
  $scope.images = [{
    src: 'img1.png',
    title: 'Pic 1'
  }, {
    src: 'img2.jpg',
    title: 'Pic 2'
  }, {
    src: 'img3.jpg',
    title: 'Pic 3'
  }, {
    src: 'img4.png',
    title: 'Pic 4'
  }, {
    src: 'img5.png',
    title: 'Pic 5'
  }];
});
```

## 步骤 3:编写指令标记

现在让我们回到我们的指令，并产生标记。由于该指令需要呈现数组中的每个图像，我们将使用`ng-repeat`。此外，我们将有两个按钮:`prev`和`next`来导航图像。`templates/templateurl.html`的内容如下图所示。

```
<div class="slider">
  <div class="slide" ng-repeat="image in images" ng-show="image.visible">
    <img ng-src="img/{{image.src}}" />
  </div>
  <div class="arrows">
    <a href="#" ng-click="prev()">
      <img src="img/left-arrow.png" />
    </a>
    <a href="#" ng-click="next()">
      <img src="img/right-arrow.png" />
    </a>
  </div>
</div>
```

标记非常简单。`image`的`src`属性指向图像位置。`image.visible`属性表示图像是否可见。当我们向前/向后移动到下一幅图像时，我们需要将该特定图像的`visible`属性设置为`true`。其余的`image`对象应该将该属性设置为`false`。我们还将`next()`和`prev()`函数传递给`ng-click`来执行导航。如果您想要显示每个图像的描述，那么`image.title`属性是很重要的。

## 步骤 4:更新指令

我们需要跟踪当前可见的图像。为此，我们将在指令的隔离范围内使用一个名为`currentIndex`的属性。我们还有增加`currentIndex`的`next()`函数和减少【】的`prev()`函数。让我们用下面的代码更新指令的`link`函数:

```
scope.currentIndex = 0; // Initially the index is at the first image

scope.next = function() {
  scope.currentIndex < scope.images.length - 1 ? scope.currentIndex++ : scope.currentIndex = 0;
};

scope.prev = function() {
  scope.currentIndex > 0 ? scope.currentIndex-- : scope.currentIndex = scope.images.length - 1;
};
```

这只是基于箭头按钮(下一个/上一个)的点击来增加/减少`currentIndex`。但是，我们需要检测这种变化何时发生，并通过将`visible`属性设置为`true`来适当地使`currentIndex`处的图像可见。因为我们已经在 HTML 标记中将`image.visible`传递给了`ng-show`指令，所以这个属性的任何改变都会自动显示/隐藏图像。我们还应该关注指令范围对`currentIndex`的更改。将以下代码追加到前面代码段的末尾:

```
scope.$watch('currentIndex', function() {
  scope.images.forEach(function(image) {
    image.visible = false; // make every image invisible
  });

  scope.images[scope.currentIndex].visible = true; // make the current image visible
});
```

## 步骤 5:制作滑块动画

Angular 1.2 引入了一个新的动画框架，可以用来无缝地将 CSS3 动画与各种事件关联起来。你只需要指定动画，剩下的就交给 Angular 了。例如，当一个元素被隐藏时，Angular 会自动添加像`ng-hide-add`和`ng-hide-active`这样的类。您可以针对这些类编写 CSS 来执行所需的动画。角度动画超出了本教程的范围。然而，我鼓励你通过这个[资源](http://docs.angularjs.org/guide/animations)来学习动画。要添加动画，请按如下方式更新模块:

```
var sliderApp = angular.module('sliderApp', ['ngAnimate']);
```

并在 HTML 中的 Angular 脚本后包含以下脚本:

```
<script src="http://code.angularjs.org/1.2.9/angular-animate.min.js"></script>
```

接下来，添加以下 CSS 规则来描述转换:

```
.slide.ng-hide-add,
.slide.ng-hide-remove {
  -webkit-transition: all linear 0.5s;
  -moz-transition: all linear 0.5s;
  -o-transition: all linear 0.5s;
  transition: all linear 0.5s;
  display: block!important;
}
.slide.ng-hide-add.ng-hide-add-active,
.slide.ng-hide-remove {
  opacity: 0;
}
.slide.ng-hide-add,
.slide.ng-hide-remove.ng-hide-remove-active {
  opacity: 1;
}
```

## 步骤 6:使用指令

现在是在 HTML 中使用该指令的时候了。在使用该指令时，我们还需要将控制器范围中声明的`images`数组传递给该指令。

```
<body ng-controller="SliderController">
  <h1>Slider Using AngularJS</h1>
  <slider images="images" />
</body>
```

仅此而已！我们全新的角度滑块已经准备好了。出于样式目的，我们可以包含以下 CSS:

```
* {
  font-family: 'Open Sans', sans-serif;
}
.center-grey {
  background: #f2f2f2;
}
.slider {
  position: relative;
  padding: 5px;
  width: 610px;
  margin: auto;
  margin-top: 40px;
}
.slide {
  position: absolute;
  top: 0;
  left: 0;
  box-shadow: 0px 0px 15px #999;
}
.arrows {
  position: absolute;
  top: 10px;
  right: 20px;
}
.arrows img {
  height: 32px;
}
h1 {
  text-align: center;
  padding: 10px;
  font-size: 40px;
  color: #222;
}
```

## 奖金

除了响应下一个/上一个点击，我们可能还希望我们的滑块在一段时间后自动滑动到下一个图像。为此，我们可以使用 Angular 的`$timeout`服务。如下所示修改指令，声明对`$timeout`的依赖:

```
sliderApp.directive('slider', function($timeout) {
  ...
  // configuarations here
});
```

并将下面的代码片段附加到每五秒钟调用一次`next()`函数的`link`函数中:

```
var timer;
var sliderFunc = function() {
  timer = $timeout(function() {
    scope.next();
    timer = $timeout(sliderFunc, 5000);
  }, 5000);
};

sliderFunc();

scope.$on('$destroy', function() {
  $timeout.cancel(timer); // when the scope is getting destroyed, cancel the timer
});
```

## 结论

我们已经到了教程的末尾，我们已经学会了如何使用一个指令(带一点动画)创建一个 AngularJS 图像滑块。我们用最少的 JavaScript 代码实现了这一点，并且没有 jQuery DOM 操作。在 Angular 中有很多方法可以达到同样的效果，本教程只展示了一种方法。如果你能想到一个更好的方法或有什么要补充/询问的，请随意评论。

完整应用的源代码可以从 GitHub 下载。你也可以查看该应用的现场[演示](http://extremecss.com/demos/slider)。

## 分享这篇文章