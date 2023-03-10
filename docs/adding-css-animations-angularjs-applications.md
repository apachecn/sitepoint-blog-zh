# 向 AngularJS 应用程序添加 CSS 动画

> 原文：<https://www.sitepoint.com/adding-css-animations-angularjs-applications/>

AngularJS 旨在构建 web 上丰富的数据绑定应用程序。将动画添加到这些应用程序中，感觉就像是在吃你最喜欢的比萨饼。动画不仅给用户界面增添了美感，还让它更加用户友好。应用程序中的一个小动画可能会将其丰富性提升到一个其他方式很难达到的水平。

如今，借助网络的力量，有几种方法可以制作动画。直到最近，动画还只能用 JavaScript 实现。但是现在我们有了所有主流浏览器都很好支持的 CSS3，我们可以只用 CSS 来激活我们的站点。

[动画](https://docs.angularjs.org/guide/animations)在 1.1.5 beta 版本的 AngularJS 中得到支持。在 AngularJS 1.2 中发布该功能的稳定版本之前，它经历了许多变化。Angular 应用程序中的动画支持可以非常容易地添加或删除。该库为基于 CSS 和基于 JavaScript 的动画提供了很好的支持。虽然动画可以用 JavaScript 编写，但是推荐使用 CSS 动画。这是因为 CSS 动画被浏览器视为低优先级，当线程可能正在做重要的事情时，它们不会阻塞处理线程。

在本文中，我们将看到如何使用 CSS 动画来使 AngularJS 内置指令的行为更具吸引力。研究 CSS 支持的动画功能超出了本文的范围。可以参考 SitePoint 上的 [CSS 频道了解更多。](https://www.sitepoint.com/css/)

## 入门指南

要使用动画，我们需要包含 *angular-animate.js* 库，并在模块中添加模块 *ngAnimate* 作为依赖，如下所示。

```
angular.module('coursesApp', ['ngAnimate']);
```

该库为指定事件的以下指令添加了动画支持:

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

当指令的状态发生任何变化时，这些事件会自动生成。需要记住的重要一点是，只有当模块 *ngAnimate* 作为应用程序模块中的依赖项传递时，才会生成这些事件。当这些事件被激发时，它们在应用的元素上添加 CSS 类。上表中提到了 CSS 类的相应名称。如你所见，当事件发生时以及事件发生后，我们被赋予定义行为的自由。

## 动画制作`ng-view`

当用户从一个视图导航到另一个视图时，路由在客户端被解析，页面的一部分被加载新内容。这种情况发生得非常快，有时用户可能会觉得视图的变化太自发了。这一步中的动画会使过渡更加平滑。

参考上表，我们看到当视图改变时,`ng-view`引发了两个事件。当视图改变时，让我们应用下面的样式。

```
.view-slide-in.ng-enter {
  transition: all 1s ease;
  -webkit-transition:all 1s ease;
  -moz-transition: all 1s ease;
  -o-transition: all 1s ease;

  opacity:0.5;
  position:relative;
  opacity:0;
  top:10px;
  left:20px;
}
```

上面的 CSS 在应用的元素上应用了缓动过渡，过渡的持续时间为 1 秒。但是，上面的样式本身没有任何效果，因为我们没有说明在转换过程中要改变元素的哪个属性。让我们操作一些 CSS 属性的值:

```
.view-slide-in.ng-enter {
  opacity: 0;
}
.view-slide-in.ng-enter.ng-enter-active {
  top:0;
  left:0;
  opacity: 1;
}
.view-slide-in.ng-leave.ng-leave-active{
  top:5px;
  left:5px;
  opacity:1;
}
.view-slide-in.ng-leave{
  top:0;
  left:0;
  opacity:0;
}
```

现在我们需要做的就是，在`ng-view`指令上应用类`view-fade`。

```
<div ng-view class="view-slide-in"> </div>
```

现在，当您从一个视图导航到另一个视图时，您将能够看到幻灯片效果。你可以应用任何[过渡](https://www.sitepoint.com/css3-transitions-101/)或者你甚至可以定义你自己的关键帧动画。你可以在[演示](http://plnkr.co/edit/vurf2Pm33Dam9IUitgrC?p=preview)中玩视图转换来获得更好的想法。

## 动画制作`ng-repeat`

在 AngularJS 应用中避免`ng-repeat`几乎是不可能的。我们最终会使用该指令的大部分功能，比如更新模型、在集合中添加或删除项目、在项目中应用排序和过滤。当我们执行这些操作时，指令中的项目一直在移动和变化。

让我们在元素的不透明度上应用线性过渡来查看项目的变化。

```
.repeat-animation.ng-enter,
.repeat-animation.ng-leave,
.repeat-animation.ng-move {
  -webkit-transition: all linear 1s;
  -moz-transition: all linear 1s;
  -o-transition: all linear 1s;
  transition: all linear 1s;

  position:relative;
  left:5px;
}
.repeat-animation.ng-enter {
  opacity: 0;
}
.repeat-animation.ng-enter.ng-enter-active {
  opacity: 1;
}
.repeat-animation.ng-leave {
  opacity: 1;
}
.repeat-animation.ng-leave.ng-leave-active {
  opacity: 0;
}
.repeat-animation.ng-move {
  left:2px;
  opacity: 0.5;
}
.repeat-animation.ng-move.ng-move-active {
  left:0;
  opacity: 1;
}
```

现在，当在列表上执行任何操作时，我们会看到淡入的混合效果和对项目的一点摇动效果。这种效果是由改变上面创建的`ng-move`类中项目的 left position 属性的值引起的。对[演示](http://plnkr.co/edit/vurf2Pm33Dam9IUitgrC?p=preview)中的项目列表应用任意随机过滤和排序，并观察元素的行为。

## 动画制作`ng-hide`

动画制作`ng-hide`类似于动画制作`ng-view`。唯一不同的是，我们需要应用`ng-add`和`ng-remove`类，而不是`ng-enter`和`ng-leave`。让我们通过`ng-hide`指令让项目在显示或隐藏时淡入或淡出。通过反转事件，同样的动画可以应用于`ng-show`。

```
.hide-fade.ng-hide-add, .hide-fade.ng-hide-remove {
  -webkit-transition:all linear 1s;
  -moz-transition:all linear 1s;
  -o-transition:all linear 1s;
  transition:all linear 1s;
  display:block!important;
}
.hide-fade.ng-hide-add.ng-hide-add-active,
.hide-fade.ng-hide-remove {
  opacity:0;
}
.hide-fade.ng-hide-add,
.hide-fade.ng-hide-remove.ng-hide-remove-active {
  opacity:1;
}
```

在[演示](http://plnkr.co/edit/vurf2Pm33Dam9IUitgrC?p=preview)中观看该动画。第一页右侧的复选框隐藏或显示复读机中的一些项目。

## 结论

随着 web 浏览器的功能与日俱增，我们的客户希望我们能够充分利用这些功能，并为他们提供一款优秀的产品。CSS 中的动画支持是一个非常受欢迎的特性。用 CSS 编写和使用动画要比写几行 JavaScript 代码来做同样的事情容易得多。我希望这篇教程对你有用，我期待听到你的反馈。

## 分享这篇文章