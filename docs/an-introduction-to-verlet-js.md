# Verlet.js 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-verlet-js/>

verlet-js 是一个轻量级的物理引擎，由[子协议](http://www.subprotocol.com)编写，你可以在你的网络游戏中使用。如果你对科学和工程感兴趣，你也可以在你的模拟中使用它。顾名思义，verlet-js 基于一种叫做 [Verlet integration](https://en.wikipedia.org/wiki/Verlet_integration) 的迭代技术，用于计算二维世界中对象的行为。

在本教程中，我将教你如何在你的项目中有效地使用这个物理引擎。

## 设置

首先，[从 GitHub](https://github.com/subprotocol/verlet-js/tree/master/js) 下载最新版本的 verlet-js，并使用`script`标签将其包含在您的网页中:

```
<script src="verlet-1.0.0-min.js"><script>
```

由于这个物理引擎的 2D 世界是绘制在一个`canvas`上的，所以你必须在你的网页上添加一个:

```
<canvas width="800" height="300" id="canvas"></canvas>
```

## 初始化 2D 世界

要初始化一个 2D 世界——你也可以称之为模拟——你必须调用名为`VerletJS()`的 verlet-js 构造函数。向这个构造函数传递`canvas`的尺寸，以及对`canvas`本身的引用:

```
var world;

function initializeWorld() {
   world = new VerletJS(800, 300, document.getElementById("canvas"));
}
```

## 渲染世界

要绘制 2D 世界，您必须重复调用执行所有物理计算的`frame()`函数和绘制属于 2D 世界的所有粒子和约束的`draw()`函数。每秒调用这些函数的频率决定了您的世界的帧速率:

```
// The frame rate of the world
var fps = 32;

function renderWorld() {
   world.frame(16);
   world.draw();
   setTimeout(renderWorld, 1000 / fps);			
}
```

## 激活世界

现在您已经有了初始化和渲染世界的函数，让我们添加在页面加载时调用它们的代码:

```
window.addEventListener("load", function() {
   initializeWorld();
   renderWorld();
});
```

此时，如果您试图在浏览器中一起运行这些代码片段，您将看不到`canvas`元素中发生的任何事情，因为在您的世界中什么都没有。

## 向世界添加物品

使用 verlet-js 创建的世界只能包含粒子。但是，您可以在粒子之间创建约束，以限制它们彼此相对移动的方式。例如，使用距离约束，可以强制两个粒子始终保持固定的距离。同样，使用角度约束，可以强制三个粒子始终保持固定的角度。

只要有一点点创造力，你就可以使用粒子和约束来创建复杂的形状和行为。

### 形状

verlet-js 有一个名为`tire()`的辅助函数，允许您快速创建简单的形状(由粒子和约束组成)，而不必自己定义粒子或约束。要创建一个三角形，您可以将以下代码添加到`initializeWorld`函数中:

```
world.tire(new Vec2(100,100), 100, 3, 1, 1);
```

第一个参数指定形状原点的坐标，而第二个参数指定半径(从原点到形状所有点的距离)。第三个参数是组成形状的线段数。最后两个参数指定约束的刚度。

有了这个语句，您将获得以下结果:

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [GJvXwV](http://codepen.io/SitePoint/pen/GJvXwV/) 。