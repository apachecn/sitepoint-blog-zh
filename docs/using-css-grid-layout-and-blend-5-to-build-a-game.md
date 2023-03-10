# 使用 CSS 网格布局和 Blend 5 构建游戏

> 原文：<https://www.sitepoint.com/using-css-grid-layout-and-blend-5-to-build-a-game/>

我想和你分享一个在微软内部藏了很久的神秘的内部秘密。这是微软为 IE10 和 Windows 应用商店设计的 CSS 网格布局概念背后的真实故事。

你们大多数人可能认为这个规范是为了给开发者一个更好的网站和应用程序布局引擎而设计的。但是最初的动机完全不同。最初的目标是能够以一种简单的方式创建一个类似俄罗斯方块的游戏！

我相信你还没有被说服。这就是为什么我要用 Blend 5 做伴侣来证明给你看。好了，我们走吧！

**先决条件:** 要遵循本教程，您需要首先:

1.  在你的机器上下载/购买并安装**Windows 8 RTM**:【https://msdn.microsoft.com/en-US/windows/apps/br229516.aspx】T2
2.  下载并安装免费版**Visual Studio 2012 Express RTM**for Windows 8:【https://msdn.microsoft.com/en-US/windows/apps/br229516.aspx】T2 包含 Expression Blend 5 for Visual Studio 或使用更高版本。

## 第一步:借助 Blend 5 发现 CSS 网格布局背后的秘密

启动 Expression Blend 5 并创建一个新的 HTML (Windows 商店)项目，类型为 Blank App。将其命名为“ ***”:***

![image](img/91d7c90047fb07f18d39444c4ca73cc2.png)

替换:

```
<p>Content goes here</p> 
```

使用:

```
<div class="mainGrid">
</div> 
```

让我们使用分数单位创建一个包含 10 列 20 行的网格，无论屏幕的分辨率如何。为此，添加以下 CSS 规则:

```
.mainGrid {
    display: -ms-grid;
    width: 100%;
    height: 100%;
    -ms-grid-columns: (1fr)[10];
    -ms-grid-rows: (1fr)[20];
}
```

在动态 DOM 中选择 *< div > mainGrid* 元素，您应该会得到:

![image](img/db6b4b98ec48485b6b79945fdcc8d288.png)

让我们在这个美丽的网格里画一个形状。将此 HTML 块添加到主网格中:

```
<div class="shape1">
</div> 
```

并插入与其相关联的 CSS:

```
.shape1 {
    -ms-grid-column: 4;
    -ms-grid-row: 3;
    -ms-grid-column-span: 3;
    -ms-grid-row-span: 2;
    background-color: red;
}
```

您现在应该可以看到，在 Blend 5:

![image](img/4f55f68995f98a1d4d1b7899270bc1ee.png)

很酷，但是还没有任何东西看起来像俄罗斯方块游戏。让我们继续努力。将这两个 div 添加到形状 1 中:

```
<div class="line1shape1"></div>
<div class="line2shape1"></div> 
```

并用下面的 CSS 块替换之前的 *.shape1* 规则:

```
.shape1 {
    -ms-grid-column: 4;
    -ms-grid-row: 3;
    -ms-grid-column-span: 3;
    -ms-grid-row-span: 2;
    display: -ms-grid;
    -ms-grid-columns: 1fr 1fr 1fr;
    -ms-grid-rows: 1fr 1fr;
    width: 100%;
    height: 100%;
}
.line1shape1 {
    -ms-grid-column-span: 2;
    background-color: red;

}

.line2shape1 {
    -ms-grid-column: 2;
    -ms-grid-row: 2;
    -ms-grid-column-span: 2;
    background-color: red;
}
```

形状 1 当前跨越三列两行。然后，我将在这个由三列两行定义的区域内创建一个新的网格，以便无论分辨率如何，单元格的大小都与主网格的单元格大小完全相同。

一旦完成，我将创建两条线，以模仿俄罗斯方块游戏的 Z 形状。您现在应该有这样的结果:

![z-shape in Tetris](img/d6abdaeda0171a2b78abb639a33a1ee3.png)

更好的是，玩设备标签中的各种视图，你会看到我们的游戏已经实现了[响应式设计](https://en.wikipedia.org/wiki/Responsive_web_design)！这太酷了，不是吗？

例如，以下是快照视图和纵向视图的输出:

![](img/d0ffddcd6be16758c3366d1a4ed015f5.png)


现在让我们解决另一个问题。

俄罗斯方块网格游戏网格是由方块组成的。我们当前的响应式设计是 100%宽度拉伸。为 Windows 商店构建 Windows 8 应用程序在大多数情况下会遇到宽屏显示器(当前的平板电脑为 1366×768 或 1920×1080，大多数台式 PC 的比例为 16/9)。然后，让我们假设以宽屏比率为目标可以解决几乎所有情况。要计算合适的响应宽度，你需要做:9/16 * 10/20(主游戏格子的比例)等于: **28.125%。**

将此规则添加到全屏横向模式的主网格中:

```
@media screen and (-ms-view-state: fullscreen-landscape) {
    .mainGrid {
        width: 28.125%;
        }
}
```

现在让我们再次使用 CSS 网格布局将游戏网格居中！(现在你应该开始相信它是真正为俄罗斯方块设计的了！)

将*体*元素切换为由一列一行组成的*–ms-grid*；

```
body {
    display: -ms-grid;
    -ms-grid-columns: 1fr;
    -ms-grid-rows: 1fr;
    width: 100%;
    height: 100%;
}
```

现在只需将该属性添加到与主网格相关联的 CSS 中:

```
-ms-grid-column-align: center;
```

网格现在居中:

![](img/c704a30d4846f55a2e670576e0a152fd.png)

在这个阶段，你可能已经震惊了。我怎么会错过这个难以置信的秘密呢？“你自己也在纳闷。

深呼吸。

现在你知道了这个秘密，让我们一起继续这个教程，去发现 CSS 规范结合在一起所带来的其他令人敬畏的可能性。

## 步骤 2:移动或旋转形状

我的第一个想法是尽可能多地使用 CSS 来避免使用 JS。然后，我首先尝试使用 [CSS3 动画](https://blogs.msdn.com/b/davrous/archive/2011/12/06/introduction-to-css3-animations.aspx)来移动和动画各行/列上的形状。但是坏消息是你不能通过 CSS3 动画改变*–ms-grid-column*或者*–ms-grid-row*的值。这将是一些 JavaScript 代码的工作。

然后我开始思考如何旋转这个形状。CSS 转换似乎完美地适应了这一点。让我们通过做一些实验来检查一下。Blend 5 真的很酷，因为你可以直接看到你的变化的结果。

通过将该类添加到 DIV 元素中，在 *shape1* 上添加 90 度旋转:

```
.shape1rotated {
    transform: rotate(90deg);
}
```

我肯定你没想到会这样:

![Rotated shape](img/221823add5bb68c8847bf0ef85d035a8.png "image")

问题:它没有正确地与游戏网格对齐。为了使我们的形状与网格对齐，我们需要做一些小的调整:

```
.shape1rotated {
    transform-origin: 33% 50%;
    transform: rotate(90deg) translateX(-33%);
}
```

我们现在有和俄罗斯方块一样的旋转。下面是旋转前后的两张截图:

![z-shape before rotation](img/98bb6a282e5da4d2e19bd2d5b418e8f5.png)


我们甚至可以更进一步，通过使用 shape1 上的转换集:

```
transition: all 1s ease-out;
```

现在，在 shape1 DIV 上移除/添加 *.shape1rotated* 类将触发平滑旋转动画。

通过这个简短的视频，查看 Blend 5 内部的结果:

<video id="MSPointerEventsArticleVideo" class="video-js" poster="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.jpg" preload="preload" controls="" width="800" height="450"><source type="video/mp4; codecs=&quot;avc1.42E01E, mp4a.40.2&quot;" src="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.mp4"><source type="video/webm; codecs=&quot;vp8, vorbis&quot;" src="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.webm"><object id="flash_fallback_1" class="vjs-flash-fallback" data="https://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" width="800" type="application/x-shockwave-flash" height="450"><param name="movie" value="http://releases.flowplayer.org/swf/flowplayer-3.2.1.swf"><param name="allowfullscreen" value="true"><param name="flashvars" value="config={&quot;playlist&quot;:[&quot;https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.jpg&quot;, {&quot;url&quot;: &quot;https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.mp4&quot;,&quot;autoPlay&quot;:false,&quot;autoBuffering&quot;:true}]}">![Poster Image](img/f4ffb13158c3e5e45240e09f84392689.png "No video playback capabilities.")</object></video>

**下载视频:** [MP4](https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.mp4) ， [WebM](https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo1.webm) ， [HTML5 视频播放器](https://videojs.com) by VideoJS

在这个阶段，我们可以认为这种方法是构建我们游戏的好方法。但不幸的是，情况并非如此。原因如下。尝试通过简单地改变它的*–ms-grid-column*属性来移动形状。Blend 5 将直接反映这些变化。不旋转时，形状可以移动到第 8 列:

![z-shape moved to the 8th column](img/54708ea5bf3b70a540c38358dd4823eb.png)

到目前为止一切顺利。但是当您通过将 *.shape1rotated* 类添加到 DIV 来旋转它时:

![z-shape rotated to the 8th column](img/a918a923bc31cdd62a1a99f8cc722f7a.png)

您会看到右边还有一行可用于移动形状。如果你认为我们只需要把它移到第九排，那你就错了！事实上，下面是我们将在第 9 行获得的内容:

![z-shape gets compressed when moved to the 9th row](img/778524f08c6842434a84fc33f1bf566a.png)

您可能忘记了我们当前正在移动一个 DIV 元素，它显示一个三列两行的网格布局，与底层游戏网格完全匹配。移动它时，我们真的感觉这是我们正在移动的主网格的一个块部分。但是为了让这个技巧发挥作用，我们至少需要三列来包含我们的 shape 元素。如果它包含在两列(当设置为第 9 列时)或更少，它将被“压缩”，如屏幕截图所示。

有两种方法可以解决这个问题。

1–停止使用 CSS 变换，使用另一个网格定义绘制旋转后的形状。例如，在形状中使用三个 div，而不是两个。但是使用这样的方法会阻止我们制作好的 CSS 动画。
2–重新定义主网格以处理 12 列而不是 10 列，我们将只使用从 2 到 11 的列(如果你愿意，可以是一种裁剪区域)。这将解决我们的“溢出”问题。

让我们实现第二个解决方案。

使用以下方法重新定义主网格:

```
.mainGrid {
    display: -ms-grid;
    -ms-grid-columns: (1fr)[12];
    -ms-grid-rows: (1fr)[20];
    -ms-grid-column-align: center;    
    width: 100%;
    height: 100%;
}
```

为了获得正确的比率，您需要更新相关的媒体查询:

```
@media screen and (-ms-view-state: fullscreen-landscape) {
    .mainGrid {
        width: 33.75%;
        }
}
```

33.75% = 9/16 *12/20

让我们也添加一个“虚拟网格”来划定空间，我们将能够移动的形状。在主网格 DIV 中，插入以下内容:

```
<div class="virtualGrid">
</div> 
```

与此 CSS 块相关联:

```
.virtualGrid {
    -ms-grid-column: 2;
    -ms-grid-column-span: 10;
    -ms-grid-row-span: 20;
    border-right-style: dashed;
    border-left-style: dashed;
    background-color: #505A5A;
}
```

这将有助于用灰色背景和一些虚线边框来划定游戏区域。

现在，如果我移动第 9 列第 2 行的 Z 形状，结果如下:

![z-shape at column 9](img/bb07289194ad831215780475a582d72e.png)

如果我用 CSS 变换旋转它，我可以在第 10 列正确地移动它:

![Rotated z-shape moved correctly to column 9](img/bd0ede38d2000129c17e1622ccd4a801.png)

#### 额外奖励——处理肖像模式:

如果您想支持纵向模式(这对于俄罗斯方块网格游戏来说更好)，请添加以下 CSS 媒体查询定义:

```
@media screen and (-ms-view-state: fullscreen-portrait) {
        .mainGrid {
        width: 106.66%;
        }
}
```

因为比率需要计算为= 16/9 * 12/20 = 106.66%。

![Tetris grid in landscape](img/1468768a2c1db804aade638930232830.png)

## 步骤 3:添加一些代码来处理部分游戏逻辑

现在我们已经只用一些纯 CSS 和 HTML 代码解决了游戏的图形部分，我们需要 JavaScript 的帮助来移动/旋转游戏区域中的形状。我们将通过一个 JS 对象来重新实现 CSS 逻辑，这个 JS 对象将通过 [WinJS 来定义。类别](https://msdn.microsoft.com/fr-fr/library/windows/apps/br229776.aspx)。

在 Visual Studio 2012 中打开“ *TheRealCSSGridStory* ”。

在 js 目录下创建一个*tetrishapez . JS*文件，并复制/粘贴以下代码:

```
(function () {
    "use strict";

    var ShapeZ = WinJS.Class.define(
    /// Constructor - columnIndex is optional. If provided, defines the column the shape starts in function (columnIndex) {
            // We're creating the equivalent of this HTML block :
            // <div class="shape1 ">
            //     <div class="line1shape1"></div>
            //     <div class="line2shape1"></div>
            // </div> this._shape1 = document.createElement("div");
            var line1 = document.createElement("div");
            var line2 = document.createElement("div");
            this._shape1.className = "shape1";
            line1.className = "line1shape1";
            line2.className = "line2shape1";
            this._shape1.appendChild(line1);
            this._shape1.appendChild(line2);
            // Boolean to indicate if the shape is in its default orientation mode or not
            // True means not rotated, false means rotated this._defaultOrientation = true;
            // Setting the column position in the main grid if (columnIndex) {
                this._currentColPos = columnIndex;
                this._shape1.style.msGridColumn = this._currentColPos;
            }
            else {
                this._currentColPos = 1;
            }
            // We always start at line 1 this._currentLinePos = 1;
            // Boolean to know if the shape can be move/rotate or not
            // If true, this means we've reached the last line possible this._fixed = false;
        },
        {
            /// Specify in which HTML element displayed in CSS Grid you'd like to work with
            /// width is the number of columns of the grid & height is the number of lines insertIntoGrid: function (element, width, height) {
                element.appendChild(this._shape1);
                this._gridWidth = width;
                this._gridHeight = height;
                // These are the left & bottom max limit for this shape
                // when displayed in default orientation mode this._maxLeft = width - 3;
                this._maxBottom = height - 1;
            },
            /// Rotate the Z shape 90 degrees anti/clockwise using CSS Transforms
            /// by simply removing/adding the shape1rotated class rotate: function () {
                if (!this._fixed && this._defaultOrientation) {
                    // rotating 90 degrees clockwise, it will trigger also the CSS Transition WinJS.Utilities.addClass(this._shape1, "shape1rotated");
                    this._defaultOrientation = false;
                    // the left limit is now +1 vs the default orientation this._maxLeft = this._gridWidth - 2;
                }
                else {
                    if (!this._fixed && this._currentColPos < this._maxLeft) {
                        // removing the shape1rotated will automatically reset the shape
                        // to its initial matrix and again the CSS Transition will do the 
                        // animation for you WinJS.Utilities.removeClass(this._shape1, "shape1rotated");
                        this._defaultOrientation = true;
                        this._maxLeft = this._gridWidth - 3;
                    }
                }
            },
            // Internal function called by public moveLeft/moveRight _moveHorizontally: function (direction) {
                if (!this._fixed && (this._currentColPos < this._maxLeft || direction === -1) && 
                (this._currentColPos > 2 || direction === 1)) {
                    this._currentColPos = this._currentColPos + direction;
                    this._shape1.style.msGridColumn = this._currentColPos;
                }
            },
            /// Move the shape on the immediate left column
            /// Test if you've reached the left limit or not moveLeft: function () {
                this._moveHorizontally(-1);
            },
            /// Move the shape on the immediate right column
            /// Test if you've reached the right limit or not moveRight: function () {
                this._moveHorizontally(1);
            },
            /// Move the shape down on the immediate below line
            /// Test if you've reached the bottom limit or not moveDown: function () {
                if (!this._fixed) {
                    this._currentLinePos = this._currentLinePos + 1;
                    this._shape1.style.msGridRow = this._currentLinePos;
                    if (this._currentLinePos === this._maxBottom) {
                        this._fixed = true;
                    }
                }
            }
        }
    );

    WinJS.Namespace.define("CSSTetris", { ShapeZ: ShapeZ });
} ());
```

只需阅读代码就能理解它在做什么。应该评论的足够多，不言自明。

在*default.html*中添加对这个脚本文件的引用，并且只在正文中保留这个 HTML 块:

```
<div class="mainGrid">
    <div class="virtualGrid">
    </div>
</div> 
```

跳转到 *default.js* 。

拥有文档化良好的代码最酷的地方在于，我们现在有了有趣的智能感知细节，比如关于**构造函数**:

![Intellisense for ShapeZ(columnIndex) constructor](img/8073e0c7cf93d6f465c0f29954bbef16.png)

或者**旋转**功能:

![Intellisense for the rotate function](img/b270bc58675dac9a14d22c97e7a4e42f.png)

要正确使用这段代码，请在 *processAll* 调用之后添加这段 JS:

```
document.addEventListener("keydown", OnKeyDown, false);
mainGrid = document.getElementsByClassName("mainGrid")[0];
myShape = new CSSTetris.ShapeZ(4);
myShape.insertIntoGrid(mainGrid, 12, 20);
init();
```

并添加这两个函数:

```
function init() {
    setInterval(function () {
        myShape.moveDown();
    }, 1000);
}

function OnKeyDown(event) {
    switch (event.keyCode) {
        case KEYCODE_X:
            myShape.rotate();
            break;
        case KEYCODE_LEFT:
            myShape.moveLeft();
            break;
        case KEYCODE_RIGHT:
            myShape.moveRight();
            break;
        case KEYCODE_DOWN:
            myShape.moveDown();
            break;
    }
}
```

我们完事了。我们现在有了一个非常基本的游戏，它使用 CSS 网格布局，图形部分使用 CSS 变换和动画，还有几行 JS 代码，这是一个类似俄罗斯方块的游戏的基础。

下面是演示最终结果的短片:

<video id="MSPointerEventsArticleVideo" class="video-js" poster="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.jpg" preload="preload" controls="" width="705" height="450"><source type="video/mp4; codecs=&quot;avc1.42E01E, mp4a.40.2&quot;" src="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.mp4"><source type="video/webm; codecs=&quot;vp8, vorbis&quot;" src="https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.webm"><object id="flash_fallback_1" class="vjs-flash-fallback" data="https://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" width="705" type="application/x-shockwave-flash" height="450"><param name="movie" value="https://releases.flowplayer.org/swf/flowplayer-3.2.1.swf"><param name="allowfullscreen" value="true"><param name="flashvars" value="config={&quot;playlist&quot;:[&quot;https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.jpg&quot;, {&quot;url&quot;: &quot;https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.mp4&quot;,&quot;autoPlay&quot;:false,&quot;autoBuffering&quot;:true}]}">![Poster Image](img/03f4499896da184565818c0217fdbe69.png "No video playback capabilities.")</object></video>

**下载视频:** [MP4](https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.mp4) ， [WebM](https://david.blob.core.windows.net/videos/TheRealCSSGridStoryVideo2.webm) ， [HTML5 视频播放器](https://videojs.com) by VideoJS

你可以在这里下载本教程三个步骤对应的最终 Visual Studio 解决方案:[https://David . blob . core . windows . net/win 8/therealcssgridstory . zip](https://david.blob.core.windows.net/win8/TheRealCSSGridStory.zip "https://david.blob.core.windows.net/win8/TheRealCSSGridStory.zip")

那么，你现在相信 CSS 网格布局是为了简化类似俄罗斯方块的游戏的创建吗？

* * *

本文是来自 Internet Explorer 团队的 HTML5 技术系列的一部分。通过 3 个月的免费浏览器堆栈跨浏览器测试@ [来尝试本文中的概念。即](https://modern.IE)。

* * *

这篇文章最初出现在 David Rousset 的 MSDN 博客上，于 2013 年 2 月 12 日编码为 4Fun。

## 分享这篇文章