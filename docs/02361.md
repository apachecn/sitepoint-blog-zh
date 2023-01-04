# 快速提示:如何用 JavaScript 制作游戏循环

> 原文：<https://www.sitepoint.com/quick-tip-game-loop-in-javascript/>

***“游戏循环”*是一种技术的名称，该技术用于渲染状态随时间变化的动画和游戏。它的核心是一个尽可能多次运行的函数，接受用户输入，更新运行时间的状态，然后绘制帧。**

在这篇短文中，你将学习这一基本技术是如何工作的，并且你将能够开始制作你自己的基于浏览器的游戏和动画。

JavaScript 中的游戏循环是这样的:

```
function update(progress) {
  // Update the state of the world for the elapsed time since last render
}

function draw() {
  // Draw the state of the world
}

function loop(timestamp) {
  var progress = timestamp - lastRender

  update(progress)
  draw()

  lastRender = timestamp
  window.requestAnimationFrame(loop)
}
var lastRender = 0
window.requestAnimationFrame(loop) 
```

[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 方法请求浏览器在下一次重画发生之前尽快调用指定的函数。这是一个专门用于渲染动画的 API，但是你也可以使用`setTimeout`来获得类似的效果。向`requestAnimationFrame`传递回调开始触发的时间戳，它包含自窗口加载以来的毫秒数，等于 [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) 。

渲染之间的`progress`值或*时间*对于创建平滑的动画至关重要。通过使用它来调整我们的`update`函数中的 x 和 y 位置，我们确保我们的动画以一致的速度移动。

## 更新职位

我们的第一部动画将会超级简单。一个红色方块向右移动，直到到达画布的边缘，然后循环回到起点。

我们需要存储正方形的位置，并在我们的`update`函数中增加 x 位置。当我们到达一个边界时，我们可以减去画布的宽度来循环返回。

```
var width = 800
var height = 200

var state = {
  x: width / 2,
  y: height / 2
}

function update(progress) {
  state.x += progress

  if (state.x > width) {
    state.x -= width
  }
} 
```

## 绘制新框架

这个例子使用了`<canvas>`元素来渲染图形，但是游戏循环也可以用于其他输出，比如 HTML 或 SVG 文档。

`draw`函数只是呈现世界的当前状态。在每一帧上，我们将清空画布，然后以存储在我们的`state`对象中的位置为中心绘制一个 10px 的红色正方形。

```
var canvas = document.getElementById("canvas")
var width = canvas.width
var height = canvas.height
var ctx = canvas.getContext("2d")
ctx.fillStyle = "red"

function draw() {
  ctx.clearRect(0, 0, width, height)

  ctx.fillRect(state.x - 5, state.y - 5, 10, 10)
} 
```

我们有动静了。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 JavaScript:基本动作中的 Pen [游戏循环。](http://codepen.io/SitePoint/pen/EyLPoX/)