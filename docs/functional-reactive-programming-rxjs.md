# RxJS 函数式反应式编程简介

> 原文：<https://www.sitepoint.com/functional-reactive-programming-rxjs/>

*这篇文章由[莫里茨·克罗格](https://github.com/morkro)、[布鲁诺·莫塔](https://www.sitepoint.com/author/bmota/)和[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在我们深入这个话题之前，我们必须回答一个至关重要的问题:**什么是反应式编程？**到今天为止，最流行的答案是反应式编程就是用并发数据流编程。大多数时候，我们会发现“并发”这个词被“异步”所代替，但是，我们稍后会看到，流不一定是异步的。

很容易看出，“一切都是流”的方法可以直接应用于我们的编程问题。毕竟，CPU 只不过是一个处理由指令和数据组成的信息流的装置。我们的目标是观察流，并在特定数据的情况下对其进行转换。

对于 JavaScript 来说，反应式编程的原则并不陌生。我们已经有了像属性绑定、`EventEmitter`模式或 Node.js 流这样的东西。有时候，这些方法的优雅伴随着性能下降、过于复杂的抽象或者调试问题。通常，与新抽象层的优点相比，这些缺点是微不足道的。当然，我们最少的例子不会反映通常的应用，但是会尽可能的简短。

事不宜迟，让我们通过使用 JavaScript (RxJS)库的[反应式扩展来弄脏我们的手。RxJS 大量使用链接，这是一种流行的技术，在 jQuery 等其他库中也使用。在 SitePoint](https://github.com/Reactive-Extensions/RxJS) 上有一个[的方法链接指南(在 Ruby 的上下文中)。](https://www.sitepoint.com/a-guide-to-method-chaining/)

## 流示例

在深入 RxJS 之前，我们应该列出一些例子，以便以后使用。这也将结束反应式编程和一般流的介绍。

一般来说，我们可以区分两种流:内部流和外部流。前者可以被认为是人为的，在我们的控制范围内，而后者来自我们无法控制的源头。外部流可能从我们的代码中触发(直接或间接)。

通常，溪流不会等我们。不管我们能不能处理它们，它们都会发生。例如，如果我们想观察道路上的汽车，我们将无法重新启动汽车流。流的发生与我们是否观察到它无关。在 Rx 术语中，我们称之为*热可观测*。Rx 还引入了*冷观测值*，它的行为更像标准迭代器，这样来自流的信息就包含了每个观测者的所有项目。

下图说明了一些外部类型的流。我们看到提到了(以前启动的)请求和一般设置的 web 挂钩，以及 UI 事件，如鼠标或键盘交互。最后，我们还可以从设备接收数据，例如 GPS 传感器、加速度计或其他传感器。

![Image showing different types of streams](img/75080f343bcee2cf8bc010d7d49e0a04.png)

该图像还包含一个标记为*消息*的流。消息可以以几种形式出现。最简单的形式之一是我们的网站和其他网站之间的交流。其他例子包括与 WebSockets 或 web workers 的通信。让我们看看后者的一些示例代码。

工人的代码如下所示。该代码试图找到从 2 到 10 的质数 <sup>10</sup> 。一旦找到一个数字，就会报告结果。

```
(function (start, end) {
    var n = start - 1;

    while (n++ < end) {
        var k = Math.sqrt(n);
        var found = false;

        for (var i = 2; !found && i <= k; ++i) {
            found = n % i === 0;
        }

        if (!found) {
            postMessage(n.toString());
        }
    }
})(2, 1e10); 
```

传统上，web worker(假设在文件`prime.js`中)包含如下。为了简洁起见，我们跳过对 web worker 支持和返回结果合法性的检查。

```
var worker = new Worker('prime.js');
worker.addEventListener('message', function (ev) {
    var primeNumber = ev.data * 1;
    console.log(primeNumber);
}, false); 
```

关于 web workers 和 JavaScript 多线程的更多细节可以在文章[Parallel JavaScript with Parallel . js](https://www.sitepoint.com/parallel-javascript-with-paralleljs/)中找到。

考虑到上面的例子，我们知道素数在正整数中遵循渐近分布。对于`x`到∞，我们得到`x / log(x)`的分布。这意味着我们将在一开始看到更多的数字。在这里，检查也便宜得多(也就是说，我们开始时每单位时间收到的素数比后来多得多。)

这可以用简单的时间轴和结果斑点来说明:

![Ball diagram showing Prime number distribution](img/2c82b1ba92c29382c0f303d5dbb13988.png)

通过查看用户对搜索框的输入，可以给出一个不相关但相似的例子。最初，用户可能热衷于输入要搜索的东西；然而，他的要求越具体，击键之间的时间差就越大。提供显示实时结果的能力无疑是可取的，可以帮助用户缩小请求范围。然而，我们不希望为每一次击键都执行一次请求，特别是因为第一次击键会执行得非常快，不需要思考也不需要专门化。

在这两种情况下，答案都是聚合给定时间间隔内的先前事件。上述两种情况的区别在于，素数应该总是在给定的时间间隔之后显示(即，一些素数在显示时可能会延迟)。相反，如果在指定的时间间隔内没有发生击键，搜索查询将仅触发新的请求。因此，一旦检测到击键，定时器就复位。

## RxJS 来救援了

Rx 是一个使用可观察集合编写异步和基于事件的程序的库。它以其声明性语法和可组合性而闻名，同时引入了简单的时间处理和错误模型。想起我们以前的例子，我们对时间处理特别感兴趣。然而，我们会看到 RxJS 有更多的好处。

RxJS 的基本构件是可观测者(生产者)和观察者(消费者)。我们已经提到了两种类型的可观测量:

*   **热门观察点**甚至在我们没有订阅它们的时候也在推送(例如 UI 事件)。
*   只有当我们订阅的时候，冷观才开始推送。如果我们再次订阅，他们会重新开始。

冷可观测量通常指的是已经被转换以便在 RxJS 中使用的数组或单个值。例如，下面的代码创建了一个 cold observable，它在完成之前只产生一个值:

```
var observable = Rx.Observable.create(function (observer) {
  observer.onNext(42);
  observer.onCompleted();
}); 
```

我们也可以从可观察的创建函数中返回一个包含清理逻辑的函数。

订阅可观察对象与可观察对象的种类无关。对于这两种类型，我们可以提供三个满足通知语法基本要求的函数，包括`onNext`、`onError`和`onCompleted`。`onNext`回调是强制性的。

```
var subscription = observable.subscribe(
  function (value) {
      console.log('Next: %s.', value);
  },
  function (ev) {
      console.log('Error: %s!', ev);
  },
  function () {
      console.log('Completed!');
  }
);

subscription.dispose(); 
```

作为最佳实践，我们应该使用`dispose`方法终止订阅。这将执行任何所需的清理步骤。否则，可能会阻止垃圾收集清理未使用的资源。

没有`subscribe`变量`observable`中包含的可观察量只是一个冷的可观察量。然而，也可以使用`publish`方法将其转换为热序列(即，我们执行伪订阅)。

```
var hotObservable = observable.publish(); 
```

RxJS 中包含的一些助手只处理现有数据结构的转换。在 JavaScript 中，我们可以区分其中的三种:

1.  **承诺**返回单个异步结果，
2.  **单个结果的功能**，以及
3.  **生成器**用于提供迭代器。

后者是 ES6 新增的，可能会被 ES5 或更老版本的数组所取代(尽管这不是一个好的替代品，应该被视为一个单一值)。

RxJS 现在引入了一个数据类型来提供异步多值(返回值)支持。因此，现在四个象限都已填满。

![Diagram classifying the four RxJS data types](img/eddbff77c7ebe6c6120a1d713517bf12.png)

当迭代器需要被拉时，可观测量的值被推。事件流就是一个例子，我们不能强迫下一个事件发生。我们只能等待事件循环的通知。

```
var array = [1,2,3,4,5];
var source = Rx.Observable.from(array); 
```

大多数创建或处理 observables 的助手也接受一个调度器，它控制订阅开始的时间和通知发布的时间。我们不会在这里深入讨论细节，因为默认的调度程序对于大多数实际用途来说已经很好了。

RxJS 中很多操作符都引入了并发，比如`throttle`、`interval`或者`delay`。我们现在再来看一下前面的例子，在这些例子中，这些助手变得非常重要。

## 例子

首先，让我们看看我们的素数生成器。我们希望在给定的时间内聚合结果，这样 UI(尤其是在开始时)就不必处理太多的更新。

在这里，我们实际上可能希望将 RxJS 的`buffer`函数与前面提到的`interval`助手结合使用。

结果应该由下图表示。绿色斑点在指定的时间间隔后出现(由用于构建`interval`的时间给出)。在这样的间隔期间，缓冲器将聚集所有可见的蓝色斑点。

![Ball diagram of the Prime number delay](img/c4a2afe67e3e59efce42032ed25803a9.png)

此外，我们还可以引入`map`，它帮助我们转换数据。例如，我们可能希望转换接收到的事件参数，以获得数字形式的传输数据。

```
var worker = new Worker('prime.js');
var observable = Rx.Observable.fromEvent(worker, 'message')
                   .map(function (ev) { return ev.data * 1; })
                   .buffer(Rx.Observable.interval(500))
                   .where(function (x) { return x.length > 0; })
                   .map(function (x) { return x.length; }); 
```

`fromEvent`函数使用标准的事件发射器模式从任何对象构造一个可观察对象。`buffer`也将返回长度为零的数组，这就是为什么我们引入了`where`函数来减少非空数组的流。最后，在这个例子中，我们只对生成的素数的数量感兴趣。因此，我们映射缓冲区以获得其长度。

另一个例子是搜索查询框，它应该被限制为在一定的空闲时间后才启动请求。在这种情况下，有两个函数可能有用:`throttle`函数产生在指定时间窗口内看到的第一个条目。`debounce`函数产生指定时间窗口内看到的最后一个条目。时间窗口也相应地移动(即，相对于第一个/最后一个项目)。

我们希望实现下图所反映的行为。因此，我们将使用`debounce`机制。

![Ball diagram of the search query throttle](img/73266e10f2b61439e3d299913f1a4c72.png)

我们希望丢弃所有以前的结果，只获得时间窗口耗尽前的最后一个结果。假设输入字段的 id 为`query`,我们可以使用下面的代码:

```
var q = document.querySelector('#query');
var observable = Rx.Observable.fromEvent(q, 'keyup')
                   .debounce(300)
                   .map(function (ev) { return ev.target.value; })
                   .where(function (text) { return text.length >= 3; })
                   .distinctUntilChanged()
                   .map(searchFor)
                   .switch()
                   .where(function (obj) { return obj !== undefined; }); 
```

在这个代码中，窗口被设置为 300 毫秒。此外，我们限制对至少包含 3 个字符的值的查询，这与之前的查询不同。这消除了对输入的不必要的请求，这些输入是通过输入和删除来纠正的。

在这整个表达中有两个关键部分。一个是使用`searchFor`将查询文本转换成请求，另一个是 [switch()](http://reactivex.io/documentation/operators/switch.html) 函数。后者采用任何返回嵌套可观测量的函数，并且只从最近的可观测量序列中产生值。

创建请求的函数可以定义如下:

```
function searchFor(text) {
    var xhr = new XMLHttpRequest();
    xhr.open('GET', apibaseUrl + '?q=' + text, true);
    xhr.send();
    return Rx.Observable.fromEvent(xhr, 'load').map(function (ev) {
        var request = ev.currentTarget;

        if (request.status === 200) {
            var response = request.responseText;
            return JSON.parse(response);
        }
    });
} 
```

注意嵌套的可观察对象(这可能导致无效请求的`undefined`),这就是我们链接`switch()`和`where()`的原因。

## 结论

RxJS 让 JavaScript 中的反应式编程成为一种快乐的现实。作为一种选择，也有 [Bacon.js](https://baconjs.github.io/) ，其工作方式类似。尽管如此，RxJS 最好的一点是 Rx 本身，它可以在许多平台上使用。这使得向其他语言、平台或系统的过渡相当容易。它还将反应式编程的一些概念统一在一组简洁且可组合的方法中。此外，还有几个非常有用的扩展，比如 [RxJS-DOM](https://github.com/Reactive-Extensions/RxJS-DOM) ，它简化了与 DOM 的交互。

你在哪里看到 RxJS 发光？

## 分享这篇文章