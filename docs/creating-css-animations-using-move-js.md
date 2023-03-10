# 使用 Move.js 创建 CSS 动画

> 原文：<https://www.sitepoint.com/creating-css-animations-using-move-js/>

CSS3 过渡和动画是目前在网站上创建轻量级动画的首选方法。不幸的是，许多开发人员发现他们的语法复杂而令人困惑。如果这听起来像你，那么 [Move.js](https://visionmedia.github.io/move.js/) 可能是你的完美解决方案。Move.js 是一个简单的 JavaScript 库，使用简单的函数创建 CSS3 动画。本教程探索了 Move.js 的基础知识，并提供了一个实际的 Move 演示。

## 基础知识

Move.js 为创建 CSS3 动画提供了简化的 JavaScript API。让我们假设我们有一个类`box`的`div`元素。当它悬停在上方时，我们想将它从左侧移动到 100px。在这种情况下，我们的 CSS 代码如下所示:

```
.box {
  -webkit-transition: margin 1s;
  -moz-transition: margin 1s;
  -o-transition: margin 1s;
  transition: margin 1s;
}
.box:hover {
  margin-left: 100px;
}
```

使用 Move.js，我们可以简单地使用`set()`方法来获得相同的结果，如下所示。

```
move('.box')
  .set('margin-left', 100)
  .end();
```

## 入门指南

首先，访问 [Move.js GitHub 页面](https://github.com/visionmedia/move.js)，下载最新包。提取并复制`move.js`文件到您的工作目录。接下来，将这个文件包含在 HTML 页面中。包含 Move.js 的示例 HTML 文件应该如下所示:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Move.js Demo</title>
    <link rel="stylesheet" type="text/css" href="styles.css"> 
  </head>
  <body>
    <a href="#" id="playButton">Play</a>
    <div class="box"></div>
    <script type="text/javascript" src="js/move.js"></script>
  </body>
</html>
```

我们已经定义了一个类`box`的`div`元素，以及一个 ID 为`playButton`的链接，它将用于我们的演示目的。让我们创建一个新的`styles.css`文件，并向其中添加以下样式。注意，Move.js 不需要这些样式。

```
.box {
  margin: 10px;
  width: 100px;
  height: 100px;
  background: #7C9DD4;
  box-shadow: 5px 5px 0px #D1D1D1;
}
#playButton {
  display: block;
  font-size: 20px;
  margin: 20px 10px;
  font-weight: bold;
  color: #222;
  text-decoration: none;
}
```

我们的 HTML 页面现在应该看起来如下图所示。

![enter image description here](img/3a9abe2d155910ce396d2650ee7f4762.png)

现在，让我们编写第一个 Move.js 片段。我们需要在 Play 按钮上附加一个`onclick`处理程序，当点击时，它会将盒子向右移动 100px。这个 hander 的 JavaScript 代码如下所示。这段代码将`transform: translateX(300px);`添加到`box`元素中。

```
document.getElementById('playButton').onclick = function(e) {
  move('.box')
    .x(300)
    .end();
};
```

添加 Move.js 代码后的完整代码如下所示。

**HTML**

```
<!DOCTYPE html>
<html>
  <head>
    <title>Move.js Demo</title>
    <link rel="stylesheet" type="text/css" href="styles.css"> 
  </head>
  <body>
    <a href="#" id="playButton">Play</a>
    <div class="box"></div>
    <script type="text/javascript" src="js/move.js"></script>
    <script type="text/javascript">
      document.getElementById('playButton').onclick = function(e){
        move('.box')
          .x(300)
          .end();
      };
    </script>
  </body>
</html>
```

**CSS**

```
.box {
  margin-left: 10px;
  width: 100px;
  height: 100px;
  background: #7C9DD4;
  box-shadow: 5px 5px 0px #D1D1D1;
}
#playButton {
  display: block;
  font-size: 20px;
  margin: 20px 10px;
  font-weight: bold;
  color: #222;
  text-decoration: none;
}
```

## Move.js 方法

在前面的演示中，我们看到了`x()`方法的运行。现在，让我们了解一些其他的 Move.js 方法。

### `set(prop, val)`

方法用于设置相关元素的 CSS 属性。它有两个参数，CSS 属性和它的值。使用示例如下:

```
.set('background-color', '#CCC')
.set('margin-left', 500)
.set('color', '#222')
```

### `add(prop, val)`

`add()`用于增加一个已经设置好的 CSS 属性。属性必须接受数值，以便可以递增。这个方法有两个参数，CSS 属性和增量。

```
.add('margin-left', 200)
```

当调用前面的代码片段时，它会将现有值加上 200px。

### `sub(prop, val)`

`sub()`是`add()`的逆。它接受同样的两个参数，但是属性值被减去。

```
.sub('margin-left', 200)
```

### `rotate(deg)`

顾名思义，这个方法按照参数提供的量旋转元素。这个方法在被调用时将 CSS `transform`属性附加到元素上。例如，下面的代码将元素旋转 90 度。

```
.rotate(90)
```

这段代码将下面的 CSS 添加到 HTML 元素中。

```
transform: rotate(90deg);
```

### `duration(n)`

使用这种方法，您可以指定动画需要多长时间。例如，下面的代码告诉 Move.js 在两秒钟内将框从左边移动到 200px。

```
.set('margin-left', 200)
.duration('2s')
```

让我们看另一个例子。在下面的代码中，Move.js 将更改边距，设置背景色，并在两秒钟内将元素旋转 90 度。

```
.set('margin-left', 200)
.set('background-color', '#CCC')
.rotate(90)
.duration('2s')
```

### `translate(x[, y])`

`translate()`用于改变元素的默认位置，使用提供的坐标作为参数。如果只提供了一个参数，它将被用作 x 坐标。如果提供了第二个参数，它将用作 y 坐标。

```
.translate(200, 400)
```

### `x()`和`y()`

使用`x()`，你可以移动调整一个元素的 x 坐标。类似地，`y()`用于垂直移动一个元素。两种方法都接受正值和负值。

```
.x(300)
.y(-20)
```

### `skew(x, y)`

`skew()`相对于 x 轴和 y 轴将一个元素旋转到一个给定的角度。这种方法可以分为`skewX(deg)`和`skewY(deg)`。

```
.skew(30, 40)
```

### `scale(x, y)`

这个方法根据提供给它的值放大或压缩 HTML 元素的大小。它使用 CSS3 转换的`scale`方法。

```
.scale(3, 3)
```

上面的代码片段将 HTML 元素的高度和宽度增加了三倍。

### `ease(fn)`

如果您使用过 CSS3 过渡，您应该知道提供给`transition`属性的`ease`函数。它规定了转换的行为。各种`ease`功能有`in`、`out`、`in-out`、`snap`、`cubic-bezeir`等。这些功能可以使用 Move 的`ease()`方法提供。例如:

```
.ease('in').x(400)
.ease('cubic-bezier(0,1,1,0)').x(400)
```

### `end()`

此方法用在 Move.js 代码段的末尾。它标志着动画的结束。从技术上讲，该方法触发动画播放。`end()`方法也接受一个可选的回调函数。下面是一个例子。

```
move('.box')
  .set('background-color', 'red')
  .duration(1000)
  .end(function() {
    alert("Animation Over!");
  });
```

### `delay(n)`

顾名思义，该方法用于将动画延迟指定的时间。

```
move('.box')
  .set('background-color', 'red')
  .delay(1000)
  .end();
```

### `then()`

这是 Move.js 中最重要的函数之一，它将用来把动画分成两组，按顺序执行。例如，下面的动画包含两个步骤，这两个步骤由对`then()`的调用来划分。

```
move('.box')
  .set('background-color', 'red')
  .x(500)
  .then()
  .y(400)
  .set('background-color', 'green')
  .end();
```

## 使用 Move.js 创建复杂动画

在本教程中，我们已经编写了许多基本的动画来查看各个方法。现在，我们将使用 Move.js 轻松创建一个更复杂的动画。该演示将阐明 Move.js 的大多数概念。我们将创建此[演示页面](https://cdpn.io/jucIv)上描述的动画。该动画的 Move.js 代码如下所示。

```
move('.square')
  .to(500, 200)
  .rotate(180)
  .scale(.5)
  .set('background-color', '#FF0551')
  .set('border-color', 'black')
  .duration('3s')
  .skew(50, -10)
  .then()
  .set('opacity', 0)
  .duration('0.3s')
  .scale(0.1)
  .pop()
  .end();
```

## 结论

希望本教程已经让你清楚地了解了什么是 Move.js，以及它如何创建 CSS3 动画。Move.js 还可以帮助你在一个地方适当地组织所有的动画代码。任何时候你想修复一个动画，你都知道它在哪里！

请查看本文的[源代码](https://extremecss.com/demos/movejs/movejs_tutorial_demo_files.zip)，并阅读以下与使用 CSS 和 JS 的动画相关的资源。

*   CSS3 动画 101:什么是动画？
*   [动画关键帧捕获](https://www.sitepoint.com/animation-keyframe-gotcha/)
*   [CSS3 动画和 JavaScript API](https://www.sitepoint.com/css3-animations-javascript-api/)

如果您有任何与本文相关的建议或问题，请在下面留下评论。我们将很高兴收到您的来信。编码快乐！

## 分享这篇文章