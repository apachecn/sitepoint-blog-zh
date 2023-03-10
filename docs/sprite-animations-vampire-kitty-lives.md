# 雪碧动画:吸血鬼小猫生活

> 原文：<https://www.sitepoint.com/sprite-animations-vampire-kitty-lives/>

我一直喜欢网页游戏；它们制作起来很有趣，很容易编码(大多数情况下)，当用户只需点击一个链接就可以开始玩游戏时，游戏的易用性真的很好。

Ajax 和移动 dom 元素带来了一些乐趣，但是限制了您可以创建什么样的体验。对于游戏开发商来说，事情正在发生变化，而且变化很快。HTML5 为纯粹在浏览器中的游戏开发引入了一系列新的选项，浏览器供应商正在努力竞争成为新标准的最佳平台。

因此，从游戏开发者的角度来看，一切都在朝着正确的方向发展:2D 和 3D 硬件加速、高性能 javascript 引擎、集成的调试器和分析器，以及可能最重要的浏览器供应商，他们正在积极争取成为严肃游戏开发的佼佼者。

因此，工具变得可用，浏览器变得有能力，供应商也在倾听，我们就可以制作出令人敬畏的游戏了，对吗？嗯，大部分是。

HTML5/Javascript 游戏开发仍处于早期，需要避免陷阱，以及选择部署哪种技术。

在这篇文章中，我将介绍一些开发 2D 游戏的选择，希望能给你一些使用 HTML5 开发你自己的游戏的想法。

## 基础知识

您必须回答的第一个问题是，是使用 HTML5 Canvas 标记来绘制图像(场景图)，还是通过操作 DOM 元素。

要使用 DOM 玩 2D 游戏，基本上要动态调整元素样式，以便在页面中移动它。虽然在某些情况下 DOM 操作很好，但我将把重点放在使用 HTML5 canvas 制作图形上，因为它是现代浏览器中最灵活的游戏。

如果你担心老的浏览器和画布的兼容性，看看 excan vas([http://excanvas.sourceforge.net/](http://excanvas.sourceforge.net/))。

## 页面设置

首先，您需要创建一个包含 canvas 标签的 HTML 页面:

```
<!doctype html>
<html>
<head>
  <title></title>
</head>
<body style='position: absolute; padding:0; margin:0; height: 100%; width:100%'>

<canvas id="gameCanvas"></canvas>

</body>
</html>
```

如果你把这个装上，你会得到奖励，嗯，没什么。这是因为虽然我们有一个画布标签，但我们没有在上面画任何东西。让我们添加一些简单的画布调用来绘制一些方框。

```
<head>
  <title></title>
  <script type='text/javascript'>
    var canvas = null;
    function onload() {
      canvas = document.getElementById('gameCanvas');
      var ctx = canvas.getContext("2d");
      ctx.fillStyle = '#000000';
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = '#333333';
      ctx.fillRect(canvas.width / 3, canvas.height / 3,
                   canvas.width / 3, canvas.height / 3);
    }
  </script>
</head>
<body onload='onload()' ...
```

在这个例子中，我添加了一个绑定到 body 标签的 onload 事件，然后实现了获取 canvas 元素并绘制一些框的函数。到目前为止够简单了。

[https://jsfiddle.net/dariusk/wa8eV/embedded/result](https://jsfiddle.net/dariusk/wa8eV/embedded/result)

这些框很漂亮，但是你会注意到画布没有占据浏览器窗口的整个区域。为了适应这种情况，我们可以通过向 canvas 标签添加宽度和高度样式来设置它的大小。我更喜欢通过根据包含画布的文档元素的大小调整大小来保持动态。

```
var canvas = null;
function onload() {
  canvas = document.getElementById('gameCanvas');
  canvas.width = canvas.parentNode.clientWidth;
  canvas.height = canvas.parentNode.clientHeight;
  ...
```

重新加载，你会看到画布占据了整个屏幕。太好了。

更进一步，如果用户调整了浏览器窗口的大小，让我们来处理画布的大小调整。

```
var canvas = null;
function onload() {
  canvas = document.getElementById('gameCanvas');
  resize();
}
function resize() {
  canvas.width = canvas.parentNode.clientWidth;
  canvas.height = canvas.parentNode.clientHeight;
  var ctx = canvas.getContext("2d");
  ctx.fillStyle = '#000000';
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = '#333333';
  ctx.fillRect(canvas.width/3, canvas.height/3, canvas.width/3, canvas.height/3);
}
```

并将 onresize 调用添加到 body 标记中。

```
  <body onresize='resize()' ...
```

现在，如果你调整浏览器的大小，矩形会很好地跟随。

[https://jsfiddle.net/dariusk/65WxU/embedded/result/](https://jsfiddle.net/dariusk/65WxU/embedded/result/)

## 加载图形

大多数游戏将需要动画精灵，所以让我们添加一些图形。

首先，你需要找到一个图片资源。由于我们将从 javascript 中绘制它，我发现在那里声明图像，然后将其 src 属性设置为您想要加载的图像的 url 是有意义的。请下载这个图像文件，它改编自[sprite lib GPL](http://www.widgetworx.com/widgetworx/portfolio/spritelib.html):【simba.png T3

```
var img = null;
function onload() {
    ...
    img = new Image();
    img.src = 'simba.png';
}
```

然后，您可以通过将此添加到 resize 方法来绘制图像:

```
  ctx.drawImage(img, canvas.width/2 - (img.width/2), canvas.height/2 - (img.height/2));
```

在大多数情况下，如果你重新加载页面，你会看到一个图像出现。我说大多数情况下，因为这取决于你的机器有多快，以及浏览器是否已经缓存了图像。这是因为在开始加载图像(设置它的 src 属性)和浏览器准备就绪之间调用了 resize 方法。有一两个图像你可能会侥幸逃脱，但是一旦你的游戏扩展，你需要等到所有的图像都加载后再采取行动。要等待，向图像添加一个通知监听器，这样当图像准备好时，您将得到一个回调。我不得不稍微重新安排一下，让它全部工作，所以下面是完整的更新代码:

```
var canvas = null;
var img = null;
var ctx = null;
var imageReady = false;
function onload() {
  canvas = document.getElementById('gameCanvas');
  ctx = canvas.getContext("2d");
  img = new Image();
  img.src = 'images/simba.png';
  img.onload = loaded();
  resize();
}
function loaded() {
  imageReady = true;
  redraw();
}
function resize() {
  canvas.width = canvas.parentNode.clientWidth;
  canvas.height = canvas.parentNode.clientHeight;
  redraw();
}
function redraw() {
  ctx.fillStyle = '#000000';
  ctx.fillRect(0, 0, canvas.width, canvas.height);
  if (imageReady)
      ctx.drawImage(img, canvas.width/2 - (img.width/2),
                         canvas.height/2 - (img.height/2));
}
```

结果应该是:

[https://jsfiddle.net/dariusk/BWPGS/embedded/result](https://jsfiddle.net/dariusk/BWPGS/embedded/result)

这张图片显示了一只小吸血鬼小猫的 6 个运行帧(嗯，这就是我认为它看起来的样子)。要制作精灵动画，我们需要一次绘制一帧。

## 精灵动画

您可以使用 drawImage 调用的源参数绘制单个帧。实际上，仅绘制源图像的受约束部分。因此，只绘制第一帧使用 drawImage 的扩展版本，让您在源图像中指定一个矩形。由于我们的猫动画由 6 帧 96 x 96 像素大小的帧组成，我们可以:

```
ctx.drawImage(img, 0, 0, 96, 54, canvas.width/2 - 48, canvas.height/2 - 48, 96, 54);
```

这里的关键是起始`0, 0, 96, 54`。这将图像限制在猫动画的第一帧。我还调整了中心位置，也是基于单帧(48s)，而不是包含所有六帧的整个图像大小。

现在是有趣的部分。为了让动画工作，我们需要跟踪要画哪一帧，然后随着时间的推移增加帧数。要做到这一点，我们需要从一个静态页面到一个定时循环的页面。

让我们从用老方法做事开始。添加一个间隔计时器，其周期时间相当于每秒 60 帧(1000 毫秒除以 60)。为了确保我们只在图像加载后才开始循环播放动画，请将调用放在加载的函数中:

```
function loaded() {
    imageReady = true;
    setTimeout( update, 1000 / 60 );
}
```

添加一个更新函数可以向前移动帧，并调用重绘:

```
var frame = 0;

function update() {
    redraw();
    frame++;
    if (frame >= 6) frame = 0;
    setTimeout( update, 1000 / 60 );
}
```

在画图和画框前进后，再次设置超时。

接下来，修改绘图图像以根据我们想要绘制的帧移动源窗口(关键部分是源 X 位置，设置为帧乘以帧的大小(在本例中为帧* 96):

```
function redraw() {
    ctx.fillStyle = '#000000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    if (imageReady)
        ctx.drawImage(img, frame*96, 0, 96, 54,
                      canvas.width/2 - 48, canvas.height/2 - 48, 96, 54);
}
```

结果是:

[https://jsfiddle.net/dariusk/NXaju/3/embedded/result](https://jsfiddle.net/dariusk/NXaju/3/embedded/result)

我们邪恶的不死吸血鬼小猫的生活！甚至以超猫的速度。

现在我们有了动画，我们将在这篇文章的第二部分《后天》中做一些改进。

*这篇文章最初发表在[BuildNewGames.com](http://buildnewgames.com)上，由[博客](http://www.bocoup.com/)和[网络浏览器的团队合作完成。](http://ie.microsoft.com/testdrive/)*

## 分享这篇文章