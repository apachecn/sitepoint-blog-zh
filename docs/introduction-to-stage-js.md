# Stage.js 简介

> 原文：<https://www.sitepoint.com/introduction-to-stage-js/>

[Stage.js](http://piqnt.com/stage.js/) 是一个轻量级的开源 JavaScript 库，可以用于跨平台的 2D HTML5 游戏开发。这个库使用一个类似 DOM 的模型来操作画布，并自己管理应用程序的渲染周期。
在本教程中，我将向您介绍这个库，试图涵盖您可能需要知道如何开始轻松使用它的一切。
我们开始吧！

## 设置和使用

首先，你必须下载库。你可以从它的 [GitHub 库](https://github.com/piqnt/stage.js)中获得 Stage.js 的最新版本(这里包括几个例子让你入门)。如果你愿意，你也可以[直接从 CDN](https://cdnjs.com/libraries/stage.js) 加载。在包含了核心文件之后，你必须添加你自己的 JavaScript 文件，但是要注意**不要**在库之前*包含你的应用文件。*

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/stage.js/0.8.2/stage.web.min.js"></script>
<script src="path/to/your/stage/application.js"></script>
```

Stage.js 中的应用程序是通过将回调函数传递给`Stage()`来创建的。然后，该库将加载所有需要的组件。最后，它将调用回调函数并在屏幕上呈现所有内容。你创建的每个应用都会有一棵树，而`stage`会在那棵树的根部。图像或字符串等所有其他元素都将是它的节点。在每个渲染周期中，当节点更新时，会重新绘制应用程序树。

## 销连接

固定决定了节点如何附加到其父节点。您可以通过锁定设置许多选项。其中一些是大小、定位、对齐和转换。下面是一个简单的例子，解释了它的作用。

```
Stage(function (stage) {
    stage.viewbox(700, 700);
    Stage.image('wheel')
        .appendTo(stage)
        .pin('handle', 0.5);
});

Stage({
    name: 'wheel',
    image: 'wheel.png'
});
```

我们首先指定视图框的大小。我们已经将标记为`wheel`的图像`wheel.png`附加到舞台上。之后，我们使用`handle`设置这个图像或节点的初始位置。任何节点上的`handle`将其自身放置在距其父节点上的对齐点的指定偏移处。`handle`和`align`都被指定为相对单位。例如，0 表示左上，1 表示右下。上面的代码将滚轮定位在视图框的中心。

[CodePen](http://codepen.io/SitePoint/pen/YyKQLx)上有该示例的现场演示，如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [YyKQLx](http://codepen.io/SitePoint/pen/YyKQLx/) 。

要将图像定位在距中心特定水平距离的位置，您可以使用“offsetX ”,如下所示:

```
Stage.image('wheel')
    .appendTo(stage)
    .pin({
        handle: 0.5,
        offsetX: 300
    })
);
```

请注意，上面的距离不是 300px，而是 viewbox 大小的`3/14`倍。您还可以为`scale`、`skew`和`rotation`等节点设置其他值。要在特定方向(例如水平方向)缩放，可以使用`scaleX`。下面的代码片段将水平缩放轮子 1.4 倍。

```
Stage.image('wheel')
    .appendTo(stage)
    .pin({
        handle: 0.5,
        scaleX: 1.4
    })
);
```

[这是应用了缩放轮的 CodePen 演示](http://codepen.io/SitePoint/pen/ZbzyRz)。

默认情况下，旋转、缩放和倾斜将以节点中心为轴点。还可以使用以下方法为节点设置不同的轴心点:

```
node.pin({
    pivotX: x,
    pivotY: y
});
```

总而言之，锁定元素使您能够四处移动它们，缩放或旋转它们。

## 鼠标和触摸事件

要更新用户交互的节点，您可以使用各种鼠标和触摸事件。以上面的轮子为例向前推进一步，我们可以编写以下代码:

```
var wheelNode = Stage.image('wheel').appendTo(stage);
wheelNode.pin({
    'handle': 0.5
});
wheelNode.on('click', function () {
    // Do something with the wheel here. 
});
```

或者，您可以像`Stage.Mouse.CLICK = 'click';`一样定义这些事件。更新后的代码将是:

```
wheelNode.on(click, function () {
     // Do something with the wheel here. 
 });
```

另一个例子是`Stage.Mouse.MOVE = 'touchmove mousemove';`。

## （小鸟）鸣叫

补间将平滑过渡应用于固定值。这防止了相关节点的位置或大小的突然变化。例如，下面的代码会将滚轮突然旋转`PI`弧度，并在每次点击时改变其位置`600`。

```
var wheelRotation = Math.PI;
 var wheelPosition = 300;

 wheelNode.on('click', function () {
     wheelRotation = -wheelRotation;
     wheelPosition = -wheelPosition;
     this.pin({
         rotation: wheelRotation,
         offsetX: wheelPosition
     });
 });
```

[这是没有补间的演示](http://codepen.io/SitePoint/pen/YyKQvx)，也如下图所示:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [YyKQvx](http://codepen.io/SitePoint/pen/YyKQvx/) 。

但是，添加补间方法会使过渡变得平滑。

```
wheelNode.on('click', function () {
    wheelRotation = -wheelRotation;
    wheelPosition = -wheelPosition;
    this.tween(3000)
        .pin({
            rotation: wheelRotation,
            offsetX: wheelPosition
        })
        .ease('bounce');
});
```

[在这里你可以找到一个应用了补间和缓动的演示](http://codepen.io/SitePoint/pen/XmrgBb)，也如下图所示，这样你就可以看出区别:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [XmrgBb](http://codepen.io/SitePoint/pen/XmrgBb/) 。

有很多可用的选项，如缓解方法，持续时间和延迟。在上面的代码中，我将持续时间设置为 3000 毫秒，并将缓动功能设置为`bounce`。此外，您可以使用几个缓解功能，如`linear`、`quad`、`cubic`、`quart`。

设置延迟将在指定的延迟后开始转换。如果动画完成后不再需要某个节点，可以调用`tween.remove();`删除该节点。要执行一些其他操作，您可以在补间完成后使用以下代码片段执行回调函数:

```
tween.done(function () {
    //Perform your actions here.
});
```

## 纹理图谱

树节点使用纹理在画布上绘制图形。要在画布上显示图形，可以使用一个 sprite 表，也称为“纹理贴图集”。设置纹理贴图集的名称是可选的。sprite 工作表需要有一组命名的纹理。为了在应用程序中使用它们，我们可以通过名称引用它们。您可以使用纹理数组作为帧来创建动画。一个动画或`anim`本身就是一个节点。这是一个动画战士的例子:

```
Stage({
    name: "warriorsprite",
    image: {
        src: "warriorsprite.png"
    },
    textures: {
        w1: {
            x: 0 * 120,
            y: 0,
            width: 120,
            height: 320
        },
        w2: {
            x: 1 * 120,
            y: 0,
            width: 120,
            height: 320
        },
        w3: {
            x: 2 * 128,
            y: 0,
            width: 128,
            height: 320
        },
        w4: {
            x: 3 * 128,
            y: 0,
            width: 128,
            height: 320
        },
        warrior: ['w1', 'w2', 'w3', 'w4']
    }
});
```

要制作战士的动画，你需要下面的代码。为了让它更快，你可以增加`fps`。：

```
Stage(function (stage) {
    stage.viewbox(320, 320);
    Stage.anim('warrior')
        .appendTo(stage)
        .pin('align', 0.5)
        .fps(8)
        .on('click', function () {
        this.play();
    });
});
```

要观看战士动画，[请看这个演示](http://codepen.io/SitePoint/pen/RWbgBj)或下面的演示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [RWbgBj](http://codepen.io/SitePoint/pen/RWbgBj/) 。