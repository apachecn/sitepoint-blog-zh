# AngularJS 应用程序中的 JavaScript 动画

> 原文：<https://www.sitepoint.com/javascript-animations-angularjs-applications/>

AngularJS 是一个功能丰富的框架，用于创建单页 web 应用程序，提供了构建丰富的交互式应用程序所需的所有功能。Angular 带来的一个关键特性是对[动画](https://docs.angularjs.org/guide/animations)的支持。

我们可以将应用程序的一部分制作成动画来表示正在发生的变化。在我的[上一篇文章](https://www.sitepoint.com/adding-css-animations-angularjs-applications/)中，我谈到了在 Angular 应用程序中对 CSS 动画的支持。在本文中，我们将看到如何利用 JavaScript 来制作 AngularJS 应用程序的动画。

在 Angular 中，CSS 和 JavaScript 动画的唯一区别是它们的定义。定义的动画的使用方式没有区别。首先，我们需要将`ngAnimate`模块加载到应用程序的根模块中。

```
angular.module('coursesApp', ['ngAnimate']);
```

JavaScript 动画中要处理的动画事件也保持不变。以下是支持不同动作的动画及其事件的指令列表:

| 指令 | 事件 |
| --- | --- |
| ng-查看
ng-包含
ng-切换
ng-if | 回车
离开 |
| ng-重复 | 输入
离开
移动 |
| ng-显示
ng-隐藏
ng-等级 | 添加
删除 |

上面的清单与上一篇文章中的清单相同，但是没有提到相应的 CSS 类，因为我们不需要它们来定义 JavaScript 动画。只有当应用程序模块加载了`ngAnimate`模块时，才会产生这些事件。现在让我们看看如何让一些指令变得生动。

## 自定义角度动画的语法

自定义 JavaScript 动画的基本框架如下:

```
angular.module('coursesApp').animation('.name-of-animation', function(<injectables>) {
  return {
    event: function(elem, done){
      //logic of animation
      done();
    }
  };
});
```

在用 AngularJS 编写 JavaScript 动画时，需要记住以下几点:

1.  动画的名称以点(.)
2.  每个动画动作都接受两个参数:
    *   一个对象，它是将应用动画的当前 DOM 元素。如果在加载 AngularJS 之前没有加载 jQuery，则它是一个 [jQlite 对象](https://docs.angularjs.org/api/ng/function/angular.element)。否则，它就是一个 jQuery 对象。
    *   动画完成后调用的回调函数。指令的动作被暂停，直到 done 函数被调用。

我们有很多 JavaScript 库，比如 [jQuery](http://api.jquery.com/animate/) 、 [Greensock](http://greensock.com/) 、 [Anima](http://lvivski.com/anima/) 以及其他几个简化编写动画工作的库。为了简单起见，我在本文中使用 jQuery 来创建动画。要了解其他图书馆，您可以访问它们各自的网站。

## 动画制作`ng-view`

当用户在 AngularJS 应用程序的视图之间切换时，应用于`ng-view`指令的动画发生。如上表所列，我们可以在视图进入或离开时制作动画。没有必要处理这两种情况；我们可以让一个看起来必要的动画。

下面是一个动画，当一个视图进入时会产生一些视觉效果:

```
courseAppAnimations.animation('.view-slide-in', function () {
  return {
    enter: function(element, done) {
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
        }, 1000, done);
    }
  };
});
```

当一个视图进入页面时，上面创建了一个滑入效果。`done`方法作为回调传递。这表示动画已经完成，现在框架可以继续下一个动作。

注意调用`animate()`方法的方式。我们不必将元素转换成 jQuery 对象，因为 jQuery 库是在加载 AngularJS 之前加载的。

现在我们需要将这个动画应用到`ng-view`指令中。虽然动画是在 JavaScript 中定义的，但是按照惯例，我们使用 target 指令上的类来应用它。

```
<div ng-view class="view-slide-in"></div>
```

## 动画制作`ng-repeat`

`ng-repeat`是最重要的指令之一，有许多选项可用。该指令的两个基本操作是过滤和排序。根据执行的操作类型，可以添加、删除或移动指令下的项目。

让我们应用一些基本的动画，以便人们可以看到什么时候发生了变化。

```
courseAppAnimations.animation('.repeat-animation', function () {
  return {
    enter : function(element, done) {
      console.log("entering...");
      var width = element.width();
      element.css({
        position: 'relative',
        left: -10,
        opacity: 0
      });
      element.animate({
        left: 0,
        opacity: 1
      }, done);
    },
    leave : function(element, done) {
      element.css({
        position: 'relative',
        left: 0,
        opacity: 1
      });
      element.animate({
        left: -10,
        opacity: 0
      }, done);
    },
    move : function(element, done) {
      element.css({
        left: "2px",
        opacity: 0.5
      });
      element.animate({
        left: "0px",
        opacity: 1
      }, done);
    }
  };
});
```

## 动画制作`ng-hide`

`ng-hide`指令在目标元素上添加或删除`ng-hide` CSS 类。要应用动画，我们需要处理添加和删除 CSS 类的情况。类的名称被传递给动画处理程序类。这让我们可以检查类并采取适当的行动。

以下是激活或停用`ng-hide`指令时淡出或淡入元素的动画代码示例:

```
courseAppAnimations.animation('.hide-animation', function () {
  return {
    beforeAddClass : function(element, className, done) {
      if (className === 'ng-hide') {
        element.animate({
          opacity: 0
        },500, done);
      } else {
        done();
      }
    },
    removeClass : function(element, className, done) {
      if (className === 'ng-hide') {
      element.css('opacity',0);
      element.animate({
          opacity: 1
        }, 500, done);
      } else {
        done();
      }
    }
  };
});
```

## 制作自定义指令的动画

为了动画化一个自定义指令，我们需要使用 [`$animate`](https://docs.angularjs.org/api/ng/service/%24animate) 服务。虽然`$animate`是 AngularJS 核心框架的一部分，但是为了充分利用服务，应该加载`ngAnimate`。

使用与上一篇文章相同的演示，我们展示了一个包含课程列表的页面。我们创建一个指令，在一个框中显示课程的详细信息，单击“查看统计数据”链接后，框中的内容会发生变化。让我们添加一个动画，让用户可以看到这个过渡。

当转换发生时，我们将添加一个 CSS 类，当动画完成时，我们将删除该类。以下是该指令的代码:

```
app.directive('courseDetails', function ($animate) {
      return {
        scope: true,
        templateUrl: 'courseDetails.html',
        link: function (scope, elem, attrs) {
          scope.viewDetails = true;
          elem.find('button').bind('click', function () {
            $animate.addClass(elem, "switching", function () {
              elem.removeClass("switching");
              scope.viewDetails =! scope.viewDetails;
              scope.$apply();
        });
      });
    }
  };
});
```

如你所见，我们在动画完成后执行动作。在浏览器开发工具中检查 directive 元素时，我们将看到类`switching-active`和`switching-add`被非常快速地添加和删除。我们可以定义一个 CSS 转换或者一个自定义的 JavaScript 动画来观察动画的发生。以下是可用于上述指令的 CSS 转换示例，为简洁起见，省略了供应商前缀:

```
.det-anim.switching {
  transition: all 1s linear;
  position: relative;
  opacity: 0.5;
  left: -20px;
}
```

或者，下面是用于相同指令的 jQuery 动画:

```
courseAppAnimations.animation('.js-anim', function () {
  return {
    beforeAddClass: function(element, className, done) {
      if (className === 'switching') {
        element.animate({
          opacity: 0
        },1000, function (){
          element.css({
            opacity: 1
          });
          done();
        });
      }
      else {
        done();
      }
    }
  }
});
```

其中一个动画可以应用到自定义指令上，就像我们在内置指令上应用动画一样。

```
<div course-details 
     class="det-anim"
     title="{{course.title}}">
</div>
```

你可以在演示页面 上看到上述所有动画 [**。**](http://embed.plnkr.co/0r7GdM91jU2NWsqPtruh/)

## 结论

如果使用得当，动画会给应用程序带来活力。正如我们所见，AngularJS 对 CSS 和 JavaScript 动画都有丰富的支持。您可以根据您团队的情况选择其中之一。

但是使用大量动画可能会导致应用程序变慢，并且对于最终用户来说，应用程序可能显得过于复杂。所以，这种武器必须小心谨慎地使用。

## 分享这篇文章