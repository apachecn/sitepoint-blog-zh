# 瞬间增强萤火虫

> 原文：<https://www.sitepoint.com/beef-up-firebug-in-a-jiffy/>

前端 web 开发工具的下一个前沿是性能测量和报告。

在[O ' Reilly Velocity Conference](http://en.oreilly.com/velocity2008/public/content/home)上，[WhitePages.com](http://www.whitepages.com/)的工程&技术副总裁 Scott Ruthfield 发表了题为“ [Jiffy:开源性能测量和仪器](http://en.oreilly.com/velocity2008/public/schedule/detail/4404)”的演讲，并展示了一款 web 应用程序界面性能测量和报告工具 [Jiffy](http://code.google.com/p/jiffy-web/) 。Jiffy 在客户端捕获任何指定事件的性能度量，将它们存储在数据库中，并报告它们。

在同一个会议上，网飞的 UI 工程总监 Bill Scott 在他的演讲“[提高网飞的性能](http://en.oreilly.com/velocity2008/public/schedule/detail/3632)”中，谈到了他一直在做的一个内部性能监控项目，其中包括一个方便的 [Firebug](http://getfirebug.com/) 的可视化扩展。

在一次偶然的会议上，Bill 修改了他的 Firebug 扩展以使用 Jiffy 性能数据，从而产生了 Jiffy Firebug 扩展。这个扩展是 Jiffy JavaScript 库提供的性能数据的一个方便的可视化，使得在即时、独立、单会话模式下使用这个库变得可行。

Jiffy 客户端的测量使用一个带有简单 API 的 JavaScript 库捕获，称为 [Jiffy.js](http://code.google.com/p/jiffy-web/wiki/Jiffy_js) ，并通过 Ajax 发送回服务器。Jiffy.js 使用了“标记和测量”的方法；您标记一个开始时间，给它一个名称，然后您可以测量命名标记和随后的任何事件之间经过的时间。虽然很简单，但它允许您根据需要添加尽可能多的性能细节。

Jiffy 扩展允许您为 web 应用程序创建上下文相关的性能度量。例如，您可以测量 DOM 脚本化小部件的呈现时间、加载第三方组件的时间、Ajax 的响应速度等等。在您想要开始测量的地方，您可以通过调用`mark`方法添加一个标记，并为该标记命名:

```
 Jiffy.mark("mark_name"); 
```

然后，在这之后的任何时候，您都可以调用`measure`方法:

```
 Jiffy.measure("measurement_name", "mark_name"); 
```

```
measure will store the elapsed time between the time it's called and the mark. The result is a JSON object that stores all the marks and measurements like this:

```
 {
  "PageStart": { et: 2676, m: [
     {et:2676, evt:"load", rt:1213159816044}
   ]},
  "onLoad": { et: 74, m: [
     {et:7,  evt:"carouselcreated", rt:1213159818722},
     {et:67, evt:"finishedonLoad",  rt:1213159818729}
   ]}
} 
```

您可以通过调用以下命令来访问该对象:

```
 Jiffy.getMeasures(); 
```

Firebug 扩展只是寻找这个 JSON 对象，并创建一个类似下图的可视化效果:
![](img/50f21953ef832917efa7c77efdfffe0e.png "Jiffy main display")
每个部分提供了每个命名标记的详细信息和所有测量结果:
![](img/4516ebfefb0a4a1820d79ced5ade6daf.png "Jiffy main display")
Jiffy JavaScript 库的标准行为是使用 Ajax 将 JSON 数据发送回服务器进行存储。然而，你根本不需要使用 Jiffy——你可以设置扩展来使用任何实现 Jiffy 使用的 JSON 格式的库，通过改变 [3 Firefox 属性](http://billwscott.com/jiffyext/#configuration)。这使得该扩展成为您定制的性能度量库的一个非常方便的可视化工具。
在 web 应用程序中，用户对界面响应性的感知是基于与浏览器、网络和驱动界面的代码的许多较小交互的响应性的集合。专业的前端开发人员需要像 Yahoo！ [YSlow](http://developer.yahoo.com/yslow/) Firebug 扩展和 Firebug JavaScript profiler，了解应用前端的不同元素如何影响整体性能。Jiffy Firebug 扩展是另一个工具，但是它将测量工具放在离用户更近的地方，提供了更清晰的用户体验。

```

## 分享这篇文章