# 10 个需要了解的 RxJS 函数及示例

> 原文：<https://www.sitepoint.com/rxjs-functions-with-examples/>

*这篇文章由[弗洛里安·拉普](https://www.sitepoint.com/author/frappl)和[莫里茨·克罗格](https://www.sitepoint.com/author/morkro/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

随着对[函数式反应式编程](https://en.wikipedia.org/wiki/Functional_reactive_programming) (FRP)兴趣的增长， [RxJS](https://github.com/Reactive-Extensions/RxJS) 已经成为这种范式中最流行的编程 JavaScript 库之一。在这篇文章中，我们将看看我认为的 RxJS 的十个必须知道的功能。

> **注意:**本文假定读者熟悉 RxJS 的基础知识，如文章[介绍使用 RxJS 进行函数式反应式编程](https://www.sitepoint.com/functional-reactive-programming-rxjs/)中所述。

## 反应式编程

[反应式编程](http://reactivex.io/intro.html)是一种编程范例，它将被称为可观察的数据流视为编程的基本单位。

流——或者 RxJS 行话中的[可观察对象](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/observable.md)——类似于事件监听器:两者都等待某件事情发生，并在发生时通知您。从 onClick 监听器获得的一系列异步通知是数据流的一个完美例子。

换句话说，一个可观察对象只不过是一个随时间填充的**数组。**

该数组的元素可以来自任何地方:文件系统、DOM 事件、API 调用，甚至是转换后的同步数据，比如数组。本质上，反应式编程只不过是使用可观察对象作为程序的构建模块。

### 与数组的关系

数组很简单，因为除非显式更改，否则它们的内容是最终的。从这个意义上说，数组没有什么本质上的时间性。

另一方面，可观察的是由时间定义的。关于一个流，你最多能知道的是，到目前为止它已经收到了`[1, 2, 3]`。你不能确定你会得到一个`4`——也不能确定你不会——决定的是数据源，而不是你的程序。

流和数组之间的关系如此深刻，以至于大多数反应式扩展都源自函数式编程的世界，在那里列表操作是面包和黄油。

### 为 RxJS 预热

想想太常见的待办事项应用程序。让我们看看使用 RxJS 只显示用户未完成任务的名称的问题是怎样的:

```
const task_stream =
  // Makes a stream of all the tasks in the database
  getTasks().
    // Get tasks only for this user
    filter((task) => task.user_id == user_id).
    // Get tasks that are incompleted
    filter((task) => !task.completed).
    // Only get name of task
    map((task) => task.name)

/* Tasks look like this:
   task = {
    user_id   : number,
    completed : boolean,
    name      : string
   }
 */ 
```

到目前为止，这只不过是[数组的额外功能](https://www.sitepoint.com/back-to-basics-array-extras/)，但是展示了反应式编程的功能味道。

随着更复杂的“真实世界”特性的加入，它的声明性变得清晰起来。假设我们想要:

*   启动请求以响应用户查看已完成或未完成任务的选择；
*   每秒只发送一次最后选择的请求，以免用户快速改变选择时浪费带宽；
*   重试失败的请求最多三次；和
*   仅当服务器发送与上次不同的响应时，才重新绘制视图。

```
const task_stream =
  parameter_stream.
    debounce(1000).
    map((parameter) => {
      getTasks().
        retry(3).
        filter((task) => task.user_id === user_id).
        filter((task) => task.completed === parameter).
        map((task)    => task.name)
    }).
    flatMap(Rx.Observable.from).
    distinctUntilChanged().
    update() 
```

循序渐进:

*   `parameter_stream`告诉我们用户想要完成还是未完成的任务，将选择存储在`parameter`中；
*   `debounce()`确保我们每秒钟只注意最后一次按钮点击；
*   `getTasks()`周围的部分与之前相同；
*   确保我们只关注服务器的响应与上次不同的情况；和
*   负责更新用户界面，以反映我们从服务器获得的信息。

以命令式的、基于回调的方式处理去抖、重试和“不同直到改变”逻辑是有效的，但是它可能既脆弱又复杂。

要点是用 RxJS 编程允许:

1.  声明性程序；
2.  可扩展系统；和
3.  简单、可靠的错误处理。

我们将通过 RxJS 的十个必须知道的函数来了解上例中的每个函数。

## 简单流上的操作

简单流上的基本函数(发出简单值，如字符串)包括:

*   [地图()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
*   [过滤器()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
*   [reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
*   take() / takeWhile()

除了`take()`和`takeWhile()`，这些都类似于 JavaScript 的高阶数组函数。

我们将通过解决一个示例问题来应用这些方法:在数据库中查找所有拥有. com 或。org 网站，并计算其网站名称的平均长度。

[JSONPlaceholder](http://jsonplaceholder.typicode.com/) 将是我们的用户来源。[这是我们将要使用的用户数据的 JSON 表示](http://jsonplaceholder.typicode.com/users/1)。

### 1.使用 map()转换数据

在可观察对象上使用`map()`等同于在数组上使用它。它:

1.  接受回调作为参数；
2.  对调用它的数组的每个元素执行它；和
3.  返回一个新数组，原始数组中的每个元素都替换为对其调用回调的结果。

在可观测量上使用`map()`的唯一区别在于:

1.  它不是返回一个新的数组，而是返回一个新的可观察值；和
2.  每当可观察对象发出一个新项目时，它就会执行，而不是立即执行。

我们可以使用`map()`将我们的用户数据流转换成他们的网站名称列表:

```
source.
  map((user) => user.website) 
```

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看来自 RxJS // map 的笔 [10 功能。](http://codepen.io/SitePoint/pen/xOLaWb/)