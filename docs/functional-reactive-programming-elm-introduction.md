# 基于 Elm 的函数式反应式编程:简介

> 原文：<https://www.sitepoint.com/functional-reactive-programming-elm-introduction/>

*本文由[莫里茨·克罗格](https://www.sitepoint.com/author/morkro)、[马克·布朗](https://www.sitepoint.com/author/mbrown)和[丹王子](https://www.sitepoint.com/author/dprince)进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

Elm 是一种函数式编程语言，最近吸引了相当多的兴趣。本文探讨了它是什么以及您为什么应该关注它。

Elm 当前的主要焦点是使前端开发更简单和更健壮。Elm 编译成 JavaScript，因此可以用于为任何现代浏览器构建应用程序。

**Elm 是带有类型推理的静态类型语言**。类型推断意味着我们不需要自己声明所有的类型，我们可以让编译器**为我们推断**许多类型。例如通过写`one = 1`，编译器知道`one`是一个整数。

Elm 是一种几乎纯粹的函数式编程语言。 Elm 建立在许多功能模式之上，如**纯视图**、**参照透明**、**不可变数据**和**受控副作用**。它与其他 ML 语言如 [Haskell](https://www.haskell.org/) 和 [Ocaml](https://ocaml.org/) 密切相关。

**榆木无功。**榆树的一切都流经**信号**。Elm 中的信号随着时间传递信息。例如，点击按钮会通过信号发送信息。

您可以认为信号类似于 JavaScript 中的事件，但与事件不同，信号是 Elm 中的一等公民，可以传递、转换、过滤和组合。

## Elm 语法

Elm 语法类似于`Haskell`，因为两者都是 ML 家族语言。

```
greeting : String -> String
greeting name =
  "Hello" ++ name 
```

这是一个接受一个`String`并返回另一个`String`的函数。

## 为什么要用 Elm？

为了理解您为什么应该关注 Elm，让我们来谈谈过去几年中的一些前端编程趋势:

### 描述状态而不是转换 DOM

不久前，我们还在通过手动改变 DOM(例如使用 jQuery)来构建应用程序。随着应用程序的增长，我们引入了更多的状态。必须对它们之间的转换进行编码，这成倍地增加了应用程序的复杂性，使其更难维护。

像 **React** 这样的库没有这样做，而是普及了专注于描述特定 DOM 状态的概念，然后让库为我们处理 DOM 转换。我们只关注描述谨慎的 DOM 状态，而不是我们如何到达那里。

这大大减少了要编写和维护的代码。

### 事件和数据转换

当谈到应用程序状态时，通常要做的事情是我们自己改变状态，例如向数组添加注释。

我们只能描述应用程序状态需要如何根据事件进行更改，而不是这样做，让其他东西为我们应用这些转换。在 JavaScript 中， **[Redux](http://redux.js.org/)** 让这种构建应用的方式变得流行起来。

这样做的好处是，我们可以编写‘纯’函数来描述这些转换。这些函数更容易理解和测试。一个额外的好处是，我们可以控制应用程序状态的更改，从而使应用程序更易于维护。

另一个好处是，我们的视图不需要知道如何改变状态，它们只需要知道调度什么事件。

### 单向数据流

另一个有趣的趋势是让我们所有的应用程序事件单向流动。我们通过中央消息管道发送消息，而不是允许任何组件与任何其他组件对话。这个集中的管道应用我们想要的转换，并将更改传播到应用程序的所有部分。就是一个例子。

通过这样做，我们获得了应用程序中发生的所有交互的更多可见性。

### 不可变数据

可变数据很难限制它可以在哪里被更改，因为任何可以访问它的组件都可以添加或删除某些内容。这导致了不可预测性，因为状态可能在任何地方改变。

通过使用不可变数据，我们可以避免这一点，通过严格控制应用程序状态的变化。将不可变数据与描述转换的函数相结合，为我们提供了一个非常健壮的工作流，不可变数据通过不让我们在意想不到的地方改变状态，帮助我们实施单向流。

### 中央集权国家

前端开发的另一个趋势是使用集中的“原子”来保存所有状态。这意味着我们将所有状态放在一棵大树中，而不是分散在各个组件中。

在一个典型的应用程序中，我们通常有全局应用程序状态(例如，用户集合)和组件特定状态(例如，特定组件的可见性状态)。将这两种状态存储在一个地方是否有益是有争议的。但至少将所有应用程序状态保存在一个地方有一个很大的好处，那就是在应用程序的所有组件之间提供一致的状态。

### 纯成分

另一个趋势是使用纯组件。这意味着给定相同的输入，组件将总是呈现相同的输出。这些组件内部没有发生副作用。

这使得理解和测试我们的组件比以前容易得多，因为它们更加可预测。

### 回到榆树

这些都是使应用程序更加健壮、可预测和可维护的优秀模式。然而，为了在 JavaScript 中正确使用它们，我们需要小心避免在错误的地方做一些事情(例如，在组件内部改变状态)。

Elm 是一种编程语言，从一开始就考虑到了这些模式。拥抱和使用它们是非常自然的，不用担心做错事情。

在 Elm 中，我们使用以下方式构建应用程序:

*   不可变数据
*   描述 DOM 的纯视图
*   单向数据流
*   中央集权国家
*   描述数据突变的集中位置
*   包含副作用

### 安全

Elm 的另一大好处是它提供的安全性。通过完全避免值为空的可能性，它迫使我们处理应用程序中的所有可选路径。

例如，在 JavaScript(和许多其他语言)中，您可以通过执行以下操作来获得运行时错误:

```
var list = []
list[1] * 2 
```

这将在 JavaScript 中返回`NaN`，您需要处理它以避免运行时错误。

如果您在 Elm 中尝试类似的东西:

```
list = []
(List.head list) * 2 
```

编译器会拒绝这一点，告诉你`List.head list`返回一个**或者**类型。一个**可能**类型可能包含也可能不包含值，我们必须处理值为`Nothing`的情况。

```
(Maybe.withDefault 1 (List.head list)) * 2 
```

这让我们对自己的应用充满信心。在 Elm 应用程序中很少看到运行时错误。

## 示例应用程序

为了更清楚地了解 Elm 语言以及如何用它构建应用程序，让我们开发一个小应用程序，展示一个在页面上移动的 HTML 元素。你可以去 http://elm-lang.org/try 试一下这个应用程序，然后把代码粘贴到那里。

```
import Html
import Html.Attributes exposing (style)
import Time

name : Html.Html
name =
  Html.text "Hello"

nameAtPosition : Int -> Html.Html
nameAtPosition position =
  Html.div [
    style [("margin-left", toString position ++ "px")]
  ] [
    name
  ]

clockSignal : Signal Float
clockSignal =
  Time.fps 20

modelSignal : Signal Int
modelSignal =
  Signal.foldp update 0 clockSignal

update : Float -> Int -> Int
update _ model =
  if model > 100 then
    0
  else
    model + 1

main : Signal Html.Html
main =
  Signal.map nameAtPosition modelSignal 
```

让我们一条一条地过一遍:

```
import Html
import Html.Attributes exposing (style)
import Time 
```

首先，我们导入应用程序中需要的模块。

```
name : Html.Html
name =
  Html.text "Hello" 
```

`name`是一个返回包含文本`Hello`的`Html`元素的函数。

```
nameAtPosition : Int -> Html.Html
nameAtPosition position =
  Html.div [
    style [("margin-left", toString position ++ "px")]
  ] [
    name
  ] 
```

`nameAtPosition`将`name`包装在一个`div`标签中。`Html.div`是一个返回`div`元素的函数。该函数将整数`position`作为唯一参数。

`Html.div`的第一个参数是 HTML **属性**的一个**列表**。第二个参数是子 HTML 元素的列表。空的 div 标签应该是`Html.div [] []`。

`style [("margin-left", toString position ++ "px")]`创建一个样式 HTML 属性，包含给定位置的`margin-left`。当用位置`11`调用时，这将以`style="margin-left: 11px;"`结束。

所以总的来说,`nameAtPosition`在左边显示了`Hello`。

```
clockSignal : Signal Float
clockSignal =
  Time.fps 20 
```

这里我们创建了一个信号**信号**,它每秒钟传输一条消息 20 次。这是漂浮物的信号。我们将用它作为刷新动画的心跳。

```
modelSignal : Signal Int
modelSignal =
  Signal.foldp update 0 clockSignal 
```

`clockSignal`给了我们一个心跳，但是它通过信号发送的消息是没有用的，`clockSignal`的有效载荷只是每条消息之间的增量。

我们真正想要的是一个计数器(即 1、2、3 等)。为此，我们需要在应用程序中保持状态。也就是说，每次`clockSignal`触发时，取我们拥有的最后一个计数并增加它。

在 Elm 应用程序中如何保持状态。你可以把`foldp`想成类似于 JavaScript 中的`Array.prototype.reduce`，`foldp`取一个**累加函数**，一个**初始值**和一个**源信号**。

每当源信号流一个事件时，`foldp`用以前的值调用累加函数，并保留返回值。

因此，在这种情况下，每次`clockSignal`传输消息时，我们的应用程序都会调用最后一个计数的`update`。`0`是初始值。

```
update : Float -> Int -> Int
update _ model =
  if model > 100 then
    0
  else
    model + 1 
```

`update`是**累加函数**。它将来自`clockSignal`的增量`Float`作为第一个参数。作为第二个参数的计数器的前一个值的整数。并返回另一个整数，即计数器的新值。

如果`model`(计数器的先前值)大于 100，我们将其重置为 0，否则只需增加 1。

```
main : Signal Html.Html
main =
  Signal.map nameAtPosition modelSignal 
```

最后，Elm 中的每个应用程序都是从`main`函数开始的。在这种情况下，我们通过`nameAtPosition`函数`map`上面创建的`modelSignal`。也就是说，每次`modelSignal`流一个值，我们重新渲染视图。`nameAtPosition`将从`modelSignal`接收有效载荷作为第一个参数，有效地改变 div 的`margin-left`样式，每秒二十次，因此我们可以看到文本在页面上移动。

我们刚刚构建的应用程序演示了:

*   Elm 中的 HTML
*   使用信号
*   以函数的方式保持状态
*   试映

如果你使用过 Redux，你会注意到 Elm 和 Redux 之间有一些相似之处。例如，Elm 中的`update`与 Redux 中的 reducers 非常相似。这是因为 Redux 深受榆树建筑 T2 的启发。

## 结论

Elm 是一种令人兴奋的编程语言，它包含了构建可靠应用程序的优秀模式。它有一个简洁的语法，内置了很多安全机制来避免运行时错误。它还有一个很棒的静态类型系统，在重构过程中帮助很大，而且不会碍事，因为它使用了类型推断。

如何构建一个 Elm 应用程序的学习曲线并不简单，因为使用函数式反应式编程的应用程序与我们所习惯的不同，但是非常值得。

## 额外资源

*   在 Elm 中构建大型应用程序时，使用 Elm 架构是一个很好的实践。更多信息见本教程。
*   榆树疏林社区是一个寻求帮助和建议的好地方。
*   Elm 上的[实用工作室视频是帮助你起步的绝佳资源。](https://pragmaticstudio.com/elm)
*   Elm-tutorial 是我正在编写的一个指南，教你如何用 Elm 构建 web 应用程序。

## 分享这篇文章