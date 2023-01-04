# 精灵动画:猫老板

> 原文：<https://www.sitepoint.com/sprite-animations-boss-kitty/>

这是从[精灵动画:吸血鬼小猫生活](https://www.sitepoint.com/sprite-animations-vampire-kitty-lives/)开始的教程的延续。

那篇文章以承诺我们会做一些改进而结束。

## requestAnimFrame

很好，它在几乎所有的浏览器中都工作得很好，但是还有一个更好的方法，requestAnimFrame。

`requestAnimFrame`基本上充当一个`setTimeout`，但是浏览器知道你正在渲染一个框架，所以它可以优化绘制周期，以及如何与页面回流的其余部分进行交互。它甚至会检测选项卡是否可见，如果它是隐藏的，就不会费事去画它，这样可以节省电池(是的，网页游戏以 60fps 的速度循环会消耗电池)。在引擎盖下，浏览器也有机会以其他神秘的方式进行优化，他们没有告诉我们太多。根据我处理较重的帧负载(尤其是数百个精灵)的经验，性能会有很大的提高；尤其是最近的浏览器版本。

我要补充的一点是，在某些情况下,`setTimeout`的表现会优于`requestAnimFrame`,尤其是在移动领域。测试它，并根据设备配置您的应用程序。

使用`requestAnimFrame`的调用在不同的浏览器中是不同的，因此检测这一点的标准垫片([感谢保罗·爱尔兰](http://paulirish.com/2011/requestanimationframe-for-smart-animating/))是:

```
window.requestAnimFrame = (function(){
    return  window.requestAnimationFrame       ||
            window.webkitRequestAnimationFrame ||
            window.mozRequestAnimationFrame    ||
            window.oRequestAnimationFrame      ||
            window.msRequestAnimationFrame     ||
            function( callback ){
              window.setTimeout(callback, 1000 / 60);
            };
})();
```

如果 requestAnimFrame 支持不可用，还有一个内置的回退机制。

然后，您需要修改更新方法来重复发出请求:

```
function update() {
    requestAnimFrame(update);
    redraw();
    frame++;
    if (frame >= 6) frame = 0;
}
```

在实际执行渲染/更新之前调用`requestAnimFrame`往往会提供更一致的结果。

顺便说一句，当我第一次开始使用`requestAnimFrame`时，我四处寻找如何计时，但什么也没找到。那是因为它不是。你会发现用`setTimeout`设置 MS 延迟是不等价的，这意味着你实际上不能控制帧速率。做好你的工作，让浏览器来处理剩下的事情。

另一件要注意的事情是，如果您在自己的闭包中使用`requestAnimFrame`，那么您需要做一个本机包装来调用它，比如:

```
my.requestAnimFrame = (function () {
    var func = window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        window.oRequestAnimationFrame ||
        window.msRequestAnimationFrame ||
        function (callback, element)
        {
            window.setTimeout(callback, 1000 / this.fps);
        };

    // apply to our window global to avoid illegal invocations (it's a native) return function (callback, element) {
        func.apply(window, [callback, element]);
    };
})();
```

## 基于时间的动画

接下来我们需要求解可怜的小猫跑的速度。现在，动画帧根据帧速率前进，这将在不同的设备上跳跃。那不好；如果你在移动一个角色的同时制作动画，那么在不同的帧速率下，事情看起来会很奇怪并且不一致。你可以试着控制帧速率，但是最终基于实时的动画会带来更好的全方位体验。

你还会发现游戏中的时间一般会应用到你做的每一件事上:射击速度，旋转速度，加速，跳跃，这些都可以通过恰当的时间得到更好的处理。

为了以规定的速度推进 kitty，我们需要跟踪已经过了多少时间，然后根据分配给每个帧的时间推进这些帧。这个的基础是:

1.  以每秒帧数为单位设置动画速度。(msPerFrame)
2.  当你循环游戏时，算出自上一帧以来已经过了多长时间(delta)。
3.  如果已经过了足够的时间来向前移动动画帧，则前进该帧并将累积增量设置为 0。
4.  如果还没有经过足够的时间，记住(累加)增量时间(acDelta)。

我们的代码是这样的:

```
var frame = 0;
var lastUpdateTime = 0;
var acDelta = 0;
var msPerFrame = 100;

function update() {
    requestAnimFrame(update);

    var delta = Date.now() - lastUpdateTime;
    if (acDelta > msPerFrame)
    {
        acDelta = 0;
        redraw();
        frame++;
        if (frame >= 6) frame = 0;
    } else
    {
        acDelta += delta;
    }

    lastUpdateTime = Date.now();
}
```

如果你把这个装上，我们的小猫咪已经冷静到一个更合理的速度。

[https://jsfiddle.net/dariusk/p5VVA/2/embedded/result](https://jsfiddle.net/dariusk/p5VVA/2/embedded/result)

## 缩放和旋转

您还可以使用 2D 画布在图像渲染时对其执行各种操作，如旋转和缩放。

例如，让我们通过将图像缩小一半来制作一些小猫。您可以通过向 draw 调用添加一个`ctx.scale(0.5, 0.5)`来做到这一点:

```
function redraw()
{
    ctx.fillStyle = '#000000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    if (imageReady)
    {
        ctx.save();
        ctx.scale(0.5,0.5);
        ctx.drawImage(img, frame*96, 0, 96, 54,
                      canvas.width/2 - 48, canvas.height/2 - 48, 96, 54);
        ctx.restore();
    }
}
```

[https://jsfiddle.net/dariusk/p5VVA/3/embedded/result](https://jsfiddle.net/dariusk/p5VVA/3/embedded/result)

由于缩放正在改变，您会注意到我还在 scale 调用之前添加了一个`ctx.save()`，然后在末尾添加了一个 ctx.restore()。如果没有这一点，对规模化的呼吁就会越来越多，可怜的凯蒂就会很快被遗忘(试试吧，很有趣)。

缩放也可以使用负值来反转图像。如果您将缩放值从(0.5，0.5)更改为(-1，1)，猫图像将水平翻转，因此它将向相反的方向跑。请注意，translate 用于翻转起始的 X 位置，以抵消图像的反转。

```
function redraw() {
    ctx.fillStyle = '#000000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    if (imageReady) {
        ctx.save();
        ctx.translate(img.width, 0);
        ctx.scale(-1, 1);
        ctx.drawImage(img, frame*96, 0, 96, 54,
                      canvas.width/2 - 48, canvas.height/2 - 48, 96, 54);
        ctx.restore();
    }
}
```

你可以用 rotate 来做(duh)旋转。这是凯蒂在爬墙:

```
ctx.rotate( 270*Math.PI/180 );

ctx.drawImage(img, frame*96, 0, 96, 54,
               -(canvas.width/2 - 48), (canvas.height/2 - 48), 96, 54);
```

在这种情况下，通过旋转上下文，坐标也被旋转，而不仅仅是图像，因此 drawImage 通过反转将绘制小猫的 x 位置来调用偏移量。

[https://jsfiddle.net/dariusk/p5VVA/8/embedded/result](https://jsfiddle.net/dariusk/p5VVA/8/embedded/result)

这么有天赋的猫咪(虽然吸血鬼应该会爬墙吧？)

缩放和旋转很酷。伙计，我无所不能！不完全是。这很棒，但也很慢，并且会对渲染性能产生相当大的影响。在制作游戏中，有另一个技巧来处理这个问题，以及你可能会遇到的一系列其他渲染性能问题:预渲染。

## 预渲染

预渲染只是获取在常规绘制周期中已经渲染过的图像，并预先组装或处理它们。您只需执行一次昂贵的渲染操作，然后在常规的绘制周期中绘制预渲染的结果。

在 HTML5 中，您需要在一个单独的不可见画布上绘制，然后在它的位置上绘制另一个画布，而不是绘制一个图像。

下面是一个函数的例子，它将小猫预先渲染成一个反转的图像。

```
var reverseCanvas = null;

function prerender() {
    reverseCanvas = document.createElement('canvas');
    reverseCanvas.width = img.width;
    reverseCanvas.height = img.height;
    var rctx = reverseCanvas.getContext("2d");
    rctx.save();
    rctx.translate(img.width, 0);
    rctx.scale(-1, 1);
    rctx.drawImage(img, 0, 0);
    rctx.restore();
}
```

请注意，创建了一个 canvas 对象，但没有添加到 DOM 中，因此不会显示它。高度和宽度设置为原始 spritesheet，然后使用渲染缓冲区的 2D 上下文绘制原始图像。

要设置 prerender，您可以从加载的函数中调用它。

```
function loaded() {
    imageReady = true;
    prerender();
    requestAnimFrame(update);
}
```

然后，当您进行常规的 redraw 调用时，使用 reverseCanvas，而不是原来的:

```
function redraw() {
    ctx.fillStyle = '#000000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    if (imageReady) {
        ctx.save();
        ctx.drawImage(reverseCanvas, frame*96, 0, 96, 96, 
                      (canvas.width/2 - 48), (canvas.height/2 - 48), 96, 96);
        ctx.restore();
    }
}
```

不幸的是，当我们反转图像时，动画现在也向后播放，因此您也需要反转动画序列:

```
function update() {
    requestAnimFrame(update);
    var delta = Date.now() - lastUpdateTime;
    if (acDelta > msPerFrame) {
        acDelta = 0;
        redraw();
        frame--;
        if (frame < 0) frame = 5;
    } else {
        acDelta += delta;
    }
    lastUpdateTime = Date.now();
}
```

[https://jsfiddle.net/dariusk/p5VVA/9/embedded/result](https://jsfiddle.net/dariusk/p5VVA/9/embedded/result)

如果需要，您可以将画布转换为图像，方法是将其源设置为使用包含编码图像数据的数据 url。Canvas 有一个方法可以做到这一点，因此非常简单:

```
newImage = new Image();

newImage.src = reverseCanvas.toDataURL("image/png");
```

另一个不错的图像操作是处理实际的像素数据。HTML5 canvas 元素将图像数据显示为 RGBA 格式的像素数组。您可以使用以下方法从上下文中访问数据数组:

```
var imageData = ctx.getImageData(0, 0, width, height);
```

这将返回一个包含宽度、高度和数据成员的 ImageData 结构。数据元素是我们要寻找的像素数组。

数据阵列由所有像素组成，每个像素由 4 个条目表示，红色、绿色、蓝色和 alpha 级别，范围都从 0 到 255。因此，一个 512 宽乘 512 高的图像将产生一个包含 1048576 个元素的数组–512×512 等于 262，144 个像素，乘以每个像素 4 个条目。

使用这个数据阵列，这里有一个例子，其中图像的特定红色成分增加，而红色和蓝色成分减少，从而创建我们的 2 级怪物，地狱-产卵-恶魔-小猫。

```
function prerender() {
    reverseCanvas = document.createElement('canvas');
    reverseCanvas.width = img.width;
    reverseCanvas.height = img.height;
    var rctx = reverseCanvas.getContext("2d");
    rctx.save();
    rctx.translate(img.width, 0);
    rctx.scale(-1, 1);
    rctx.drawImage(img, 0, 0);
    // modify the colors
    var imageData = rctx.getImageData(0, 0, reverseCanvas.width, reverseCanvas.height);
    for (var i=0, il = imageData.data.length; i < il; i+=4) {
        if (imageData.data[i] != 0) imageData.data[i] = imageData.data[i] + 100;    // red
        if (imageData.data[i+1] != 0) imageData.data[i+1] = imageData.data[i+1] - 50; // green
        if (imageData.data[i+1] != 0) imageData.data[i+2] = imageData.data[i+2] - 50; // blue
    }
    rctx.putImageData(imageData, 0, 0);
    rctx.restore();
}
```

for 循环分四步遍历数据数组，每次修改三原色。第四通道，阿尔法，保持原样，但如果你喜欢，你可以用它来改变某些像素的透明度。(注意:在下面的 jsdild 示例中，我们对图像数据使用 dataURL，特别是为了避免直接像素操作的跨域问题。您不需要在自己的服务器上这样做。)

这是我们的二级 boss kitty:

[https://jsfiddle.net/dariusk/p5VVA/12/embedded/result](https://jsfiddle.net/dariusk/p5VVA/12/embedded/result)

由于使用像素数组操作图像需要迭代所有元素——在 hell kitty 的情况下，这需要超过一百万次——所以您应该保持事情非常优化:尽可能地预先计算，不要创建变量/对象，并尽可能跳过像素。

## 结论

画布绘制、缩放、旋转、平移和像素操作的结合，以及使用预渲染的性能选项，提供了一系列制作酷的动态游戏的功能。

例如，我最近在一个 Playcraft 的演示游戏中使用了这些技术，这是一个 2D 4 向滚动空间射击游戏。艺术家们只制作了每艘船(玩家和敌人的战斗机)的单个画面，然后我会根据我们希望船只转弯的角度和平滑程度来旋转和预渲染。我可以在运行时根据船只的类型调整角度的数量——默认情况下，玩家的船只呈现 36 个转弯角度(非常平滑),而敌人和对手的船只只有 16 个角度(起伏不平)。我还增加了一个选项，让更强大的计算机上的玩家选择将平滑角度增加到 72 度(超级平滑)。此外，我会根据你所在的团队动态地给船上的徽章和标记重新着色(翅膀上很酷的大条纹)。这再次节省了渲染和资源，而且还允许基于用户选择的团队颜色来动态调整船只颜色。

关于 canvas 的更多信息，请查看 [Canvas 元素 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-canvas-element.html) 。

*这篇文章最初发表在[BuildNewGames.com](http://buildnewgames.com)上，由[博客](http://www.bocoup.com/)和[网络浏览器的团队合作完成。](http://ie.microsoft.com/testdrive/)*

## 分享这篇文章