# 使用 CreateJS 进行丰富的演示

> 原文：<https://www.sitepoint.com/rich-presentations-using-createjs/>

自 1996 年 Macromedia Flash 1.0 问世以来，Flash 一直是将声音、视频和图像资产合并到新媒体中的事实方法。然而，随着各种数字设备的激增，Flash 已经慢慢失去了 HTML5 及其处理复杂动画的能力。

虽然许多动画师仍然在灵活和高度可定制的 Flash 环境中找到舒适和熟悉的感觉，但开源社区继续推动独立于浏览器的客户端解决方案所能实现的边界。虽然有许多框架已经开始拥有足够的工具带来处理各种媒体资产，但目前看来， [CreateJS](http://createjs.com) 是最全面的。

CreateJS 不仅充当 JavaScript canvas 框架，容纳各种库和工具，而且它还出色地简化了通常与耗时的 HTML5 开发相关的繁重工作。就复杂的交互式内容而言，CreateJS 可能是一套强大的工具，可以为您节省不止一次调试偏头痛的时间。

## 引擎盖下的一瞥

如果你还没有机会一窥她的内部，CreateJS 软件套件由五个部分组成:TweenJS、EaselJS、SoundJS、PreloadJS 和 Zo。

*   TweenJS 由一个简化的数字和非数字补间引擎组成，可以选择单独工作，也可以与 EaselJS 协同工作。
*   EaselJS 是特定于项目的媒体资产的总体管理器，包括图像、精灵和事件。它以列表格式显示这些资产，类似于我们熟悉的 Flash 布局。
*   SoundJS 包含声音引擎，并允许您插入根据用户能力播放声音的模块，包括多点触摸。
*   PreloadJS 帮助您组织和预加载所有的 javascript、声音、视频、图像和其他数据。
*   Zo 直接从 Flash (SWF)动画中编译和导出漂亮的 sprite 表和 JSON，允许您在 Flash 中创建插图并将它们无缝地合并到项目中。
*   Toolkit for Flash CS6 允许您以与 EaselJS 兼容的格式导出 Flash 动画。

现在，我们有工具开始创建完全交互式的演示，超越了像 Prezi 这样的商业工具的限制，以及像[impression js](http://bartaz.github.io/impress.js)和 [KineticJS](http://kineticjs.com) 这样的热门库的竞争对手。

## EaselJS

由于不精通标准 HTML5 画布的人所经历的困难，EaselJS 的价值开始发挥作用。相反，EaselJS 语法源于直观的 ActionScript 3 语言。这使得经验丰富的 Flex/Flash 开发人员和 Flash 新手构建丰富的体验变得更加容易。一大堆触摸事件(甚至是 IE 10 支持的事件)和花哨的动画都将在你的武器库中，提供与原生 Android 和 iOS 应用程序仅略有不同的行为、外观和感觉。

EaselJS 的跨浏览器方面扩展到了 HTML5 画布应用程序，每个支持 HTML5 的浏览器都支持这些应用程序，并且:

*   OS 2.1+: Android 平板电脑和智能手机
*   iOS 3.2+: iPad，iPhone，iPod 4+
*   黑莓 7.0 和 10.0 以上版本

与简单 HTML 页面的集成是 EaselJS 的一个特点，这一点怎么强调都不为过。它的 HTML5 canvas 元素像任何其他元素一样被合并到 HTML 页面中；能够填充文档的全部或部分区域，并准备与其他页面对象分层。

兼容性曾经是一个问题，因为很难将各种媒体整合到 HTML DOM 中。在这种情况下，EaselJS 类似于 Flash，因为图形元素在几乎所有浏览器中都会呈现在相同的相对位置。

EaselJS 中心的功能包括每 20-60 毫秒重绘舞台一次的“心跳”。位图、文本和图形可以通过各种不同的变换、滤镜、不透明度、可见性等来控制。舞台上的元素然后被分组到容器中，如下所示:

```
// Let's group some elements with a container
var container = new createjs.Container();

// Let's make a shape
var shape = new createjs.Shape();

shape.graphics.beginFill("#333").drawRect(0,0,50,50);

// Let's create a bitmap image
var bitmap = new createjs.Bitmap("path/to/image.jpg");

bitmap.x = 50;

// And now for some text
var text = new createjs.Text("Lorem ipsum dolor", "16px Verdana", "#000000");

text.x = 100;

// Put it all in a container on the stage
container.addChild(shape, bitmap, text);
stage.addChild(container);
```

使用这种方法，您可以避免在 canvas 上下文中工作，从而允许您几乎不受限制地创建和删除元素。

EaselJS Graphics 类提供了简洁的语法、链接/共享命令以及与通用 API 的集成:

```
// Here's a graphic object of some logo
var logo = new createjs.Graphics();

logo.setStrokeStyle(2);
logo.beginStroke(createjs.Graphics.getRGB(0,0,0));
logo.beginFill("#999999").moveTo(0,0).lineTo(0,0).lineTo(0,0);
```

sprite sheets 只加载一次，通过允许多个动画资源适合单个图像来优化性能。单个工作表还可以包含关于多个 sprite 的信息，通过使用 Flash Toolkit for CreateJS 的 SpriteSheetBuilder，可以将导出的矢量数据直接转换为 sprite 工作表:

```
{
  "frames": {
    "width": 60,
    "height": 60,
    "count": 20,
    "regX": 0,
    "regY": 0
  },
  "animations": {
    "FigureCycle": [5, 25],
    "FigureAnimate": [26, 36, "FigureCycle", 2]
  },
  "images": ["figure-sheet.png"]
}
```

**EaselJS 的其他优势**

标准 HTML5 画布上的交互仅在平面画布上捕获，没有感知多个用户事件和交互的上下文。EaselJS 提供了一个直观的舞台，可以记忆过去的用户事件，从而简化设计；即使是多点触控支持。

## TweenJS

TweenJS 允许补间，就像 Flash 一样，但更容易链接补间事件，使复杂的动画更容易。请记住，EaselJS 中包含了内置的 ticker 函数，所以如果您没有使用 EaselJS，您必须构建自己的 [ticker 函数](http://www.createjs.com/Docs/TweenJS/classes/Tween.html#method_tick)并调用它。

```
createjs.Tween.get(sphere).wait(500).play(
  cjs.Tween.get(sphere, {paused: true, loop: true})
    .to({x: 400}, 1000)
    .to({x: 25}, 1000)
  );

// Hover sphere up and down
createjs.Tween.get(sphere, {loop: true})
  .to({y: 20}, 500, createjs.Ease.quadInOut)
  .to({y: 0}, 500, createjs.Ease.quadInOut);
```

使用这个库，你只需要发挥你的想象力，想出一些随时间变化的值，然后让 TweenJS 把它一帧一帧地重绘到屏幕上。

## 其他一切

预加载通常是一个缺乏强大可靠性的低效过程，预加载标志着对传统工作流的显著改进。通过引用单一的 XML 清单，可以通过简单的 API 调用指向所有媒体资产来管理它们。

**Flash 集成**–对于习惯于在 Adobe 套件中进行创作的设计师来说，Zo 通过直接从 Flash 时间轴动画中制作 sprite 表，并可以选择导出额外的丰富 JSON 数据，使过渡更加容易。

音频(Audio)–声音有助于演示、游戏和富媒体，通常只有当某些东西以不好的方式突出时才会被注意到。SoundJS 不一定做什么特别的事情，相反，它通过在后台以插件的形式不引人注意地管理它来预加载所有的音频。SoundJS 会根据当前的浏览器功能自动决定使用哪种音频。

## 结论

随着 CreateJS 套件继续发展并适应行业需求，想象力似乎是创建丰富的 HTML5 交互的唯一限制。不断增长的 CreateJS 开发人员社区也创造了一些有价值的 SDK 和 reposs，这些 SDK 和 repo 继续在该框架适用的特定应用程序上构建价值。

## 分享这篇文章