# 如何使用 React、Redux 和 Immutable.js 构建 Todo 应用程序

> 原文：<https://www.sitepoint.com/how-to-build-a-todo-app-using-react-redux-and-immutable-js/>

**[的反应方式](https://facebook.github.io/react)使用组件和单向数据流，这使得它非常适合描述用户界面的结构。然而，它处理状态的工具故意保持简单——以帮助提醒我们 React 只是传统[模型-视图-控制器](http://blog.codinghorror.com/understanding-model-view-controller/)架构中的视图。**

没有什么可以阻止我们使用 just React 构建大型应用程序，但是我们会很快发现，为了保持代码简单，我们需要在其他地方管理我们的状态。

虽然没有官方的解决方案来处理应用程序状态，但是有一些库非常符合 React 的范例。在本文中，我们将把 React 与两个这样的库配对，并使用它们来构建一个简单的应用程序。

## Redux

Redux 是一个微型库，通过结合来自 [Flux](https://facebook.github.io/flux) 和 [Elm](https://github.com/evancz/elm-architecture-tutorial) 的思想，充当我们应用程序状态的容器。我们可以使用 Redux 来管理任何类型的应用程序状态，只要我们坚持以下原则:

1.  我们的状态保存在一个单独的存储中
2.  变化来自于*动作*而不是*突变*

Redux 存储的核心是一个函数，它接受当前应用程序状态和一个动作，并将它们组合起来创建一个新的应用程序状态。我们称这个函数为**减速器**。

我们的 React 组件将负责向我们的存储发送动作，反过来，我们的存储将告诉组件何时需要重新呈现。

## 不变的

因为 Redux 不允许我们改变应用程序状态，所以通过用不可变的数据结构建模应用程序状态来强制执行这一点是有帮助的。

受 Clojure 和 Scala 实现的启发，ImmutableJS 为我们提供了许多具有可变接口的不可变数据结构，它们以一种高效的方式实现了[。](https://www.youtube.com/watch?v=I7IdS-PbEgI)

## 演示

我们将使用 React with Redux 和 ImmutableJS 来构建一个简单的 todo 列表，允许我们添加 todo 并在完成和不完成之间切换。

见笔[反应，在](http://codepen.io/SitePoint/pen/bpxapd/) [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )还原&不可变 Todo 。