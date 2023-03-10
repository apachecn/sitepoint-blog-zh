# 用指针事件统一触摸和鼠标

> 原文：<https://www.sitepoint.com/unifying-touch-and-mouse-with-pointer-events/>

我经常收到来自开发人员的问题，比如，“*手机和平板电脑上有这么多支持触摸的设备，我该从哪里开始呢？*、*什么是最简单的触摸输入方式？*“简答:*“很复杂。”*

肯定有一种更统一的方式来处理网络上的多点触摸输入——在现代的触摸浏览器中，或者作为旧浏览器的后备。在这篇文章中，我将向您展示一些使用 [MSPointers](https://blogs.msdn.com/b/ie/archive/2011/09/20/touch-input-for-ie10-and-metro-style-apps.aspx) 的浏览器实验，这是一种新兴的多点触摸技术和 polyfills，使跨浏览器支持，嗯……*不那么复杂*。这种代码您也可以在自己的站点上试验并轻松使用。

首先，许多触摸技术在网络上不断发展——为了支持浏览器，你需要看看 [iOS 触摸事件](https://developer.apple.com/library/safari/#documentation/UserExperience/Reference/TouchEventClassReference/TouchEvent/TouchEvent.html)模型和 [W3C 鼠标事件](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-eventgroupings-mouseevents)模型，此外还有 at [MSPointers](https://blogs.msdn.com/b/ie/archive/2011/09/20/touch-input-for-ie10-and-metro-style-apps.aspx) ，以支持所有浏览器。然而，越来越多的人支持(和愿意)标准化。九月，微软向 W3C 提交了 [MSPointers 进行标准化](https://www.w3.org/Submission/pointer-events/)，我们已经到了最后的草案:[https://www.w3.org/TR/pointerevents](https://www.w3.org/TR/pointerevents "https://www.w3.org/TR/pointerevents")。MS Open Tech 团队最近也发布了 Webkit 的初始指针事件原型。

我试验 MSPointers 的原因不是基于设备份额——这是因为微软的基本输入处理方法与目前网络上可用的方法非常不同，值得一看它*可能会成为*。

不同之处在于，开发人员可以编写更抽象的输入形式，称为“指针”指针可以是鼠标光标、笔、手指或多个手指在屏幕上形成的任何接触点。所以你不用浪费时间为每一种输入分别编码。

## 概念

我们将首先回顾运行在 [Internet Explorer 10](https://ietestdrive.com) 中的应用程序，这些应用程序公开了 MSPointer 事件 API，然后是支持所有浏览器的解决方案。之后，我们将看到如何利用 IE10 手势服务，帮助您以一种简单的方式处理 JavaScript 代码中的触摸。由于 Windows 8 和 Windows Phone 8 共享相同的浏览器引擎，这两个平台的代码和概念是相同的。此外，本文中你将在 touch 上学习的一切将帮助你在用 HTML5/JS 构建的 [Windows 商店应用](https://windowsstore.com)中完成完全相同的任务，因为这也是正在使用的相同引擎。

MSPointer 背后的想法是让你通过一个单一的代码库，使用一个与你已经知道的经典鼠标事件相匹配的模式来寻址鼠标、笔和触摸设备。事实上，鼠标、笔和触摸有一些共同的属性:例如，你可以用它们移动指针，你可以用它们点击元素。然后让我们通过同样的代码来处理这些场景！指针将聚集这些公共属性，并以类似于鼠标事件的方式公开它们。

[![clip_image001](img/d92bf5a62a9c6edfcd061a989330e6a1.png "clip_image001")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/8004.clip_5F00_image001_5F00_43511543.png)

最明显的常见事件是: [MSPointerDown](https://msdn.microsoft.com/en-us/library/hh771909(v=vs.85).aspx) ，[MSPointerMove](https://msdn.microsoft.com/en-us/library/hh771911(v=vs.85).aspx)&[MSPointerUp](https://msdn.microsoft.com/en-us/library/hh771914(v=vs.85).aspx)，它们直接映射到鼠标事件等价物。您将得到屏幕的 X & Y 坐标作为输出。

你也有特定的事件，比如: [MSPointerOver](https://msdn.microsoft.com/en-us/library/hh771913(v=vs.85).aspx) 、 [MSPointerOut](https://msdn.microsoft.com/en-us/library/hh771912(v=vs.85).aspx) 、[MSPointerOver](https://msdn.microsoft.com/en-us/library/hh771910(v=vs.85).aspx)或 [MSPointerCancel](https://msdn.microsoft.com/en-us/library/hh846776(v=vs.85).aspx) 。

但是当然，在某些情况下，您可能希望以不同于默认鼠标行为的方式来处理触摸，以提供不同的 UX。此外，由于多点触摸屏，您可以轻松地让用户旋转、缩放或平移一些元素。笔/手写笔甚至可以为你提供一些鼠标无法提供的压力信息。指针事件将汇总这些差异，您将为每个设备的具体情况构建一些自定义代码。

**注意:** 如果你在 Windows 8/RT 设备上有触摸屏(当然)或者如果你使用的是 Windows Phone 8，那么测试以下嵌入式样本会更好。否则，您有一些选择:

> 1.通过使用免费的 Visual Studio 2012 Express 开发工具附带的 Windows 8 模拟器获得初级体验。关于如何工作的更多细节，请阅读本文:[使用 Windows 8 模拟器& VS 2012 调试 IE10 触摸事件&您的响应式设计](https://blogs.msdn.com/b/davrous/archive/2012/07/10/using-the-windows-8-simulator-amp-vs-2012-to-debug-the-ie10-touch-events-amp-your-responsive-design.aspx)。
> 2。看看[这个视频，](https://david.blob.core.windows.net/videos/MSPointerEventsArticleVideo.mp4) [在一个支持触摸、笔&鼠标的 Windows 8 平板上演示了下面所有的样本](https://david.blob.core.windows.net/videos/MSPointerEventsArticleVideo.mp4)。
> 3。如果您无法访问 Windows 8 设备，请使用虚拟交叉浏览测试服务，如 [BrowserStack](https://browserstack.com) 进行交互式测试。你可以免费使用 BrowserStack 三个月(T11)，这是由 modern 上的 Internet Explorer 团队提供的。即。

## 处理简单的触摸事件

### 步骤 1:在 JS 中不做任何事情，但是添加一行 CSS

让我们从基础开始。您可以轻松地使用任何现有的处理鼠标事件的 JavaScript 代码，它将只需使用 Internet Explorer 10 中的一些笔或触摸设备即可工作。事实上，如果您不在代码中处理指针事件，IE10 将触发鼠标事件作为最后的手段。这就是为什么你可以用手指“点击”按钮或任何网页的任何元素，即使开发者从未想过有一天有人会这样做。因此，任何注册到 mousedown 和/或 mouseup 事件的代码都可以工作，根本不需要修改。但是 mousemove 呢？

让我们回顾一下默认行为来回答这个问题。例如，让我们看看这段代码:

```
<!DOCTYPE html>
<html>
<head>
    <title>Touch article sample 1</title>
</head>
<body>
    <canvas id="drawSurface" width="400px" height="400px" style="border: 1px dashed black;">
    </canvas>
    <script>
        var canvas = document.getElementById("drawSurface");
        var context = canvas.getContext("2d");
        context.fillStyle = "rgba(0, 0, 255, 0.5)";

        canvas.addEventListener("mousemove", paint, false);

        function paint(event) {
            context.fillRect(event.clientX, event.clientY, 10, 10);
        }
    </script>
</body>
</html> 
```

它只是通过跟踪鼠标的移动，在 HTML5 canvas 元素中绘制一些 10px 乘 10px 的蓝色正方形。要测试它，在盒子里移动你的鼠标。如果您有触摸屏，尝试与画布互动，自己检查当前行为:

| [https://david.blob.core.windows.net/html5/touchsample1.htm](https://david.blob.core.windows.net/html5/touchsample1.htm) | *默认样本* : **默认行为**如果你什么都不做*结果* : **只有鼠标按下/向上/点击才能和触摸**一起工作。也就是说，只有当你在屏幕上点击时，你才能通过触摸来绘制蓝色方块，而不是当你在屏幕上移动手指时。 |

您将会看到，当您在 canvas 元素中移动鼠标时，它会绘制一系列蓝色方块。但是使用 touch，它只会在您点击画布元素的确切位置绘制一个唯一的正方形。只要您试图在 canvas 元素中移动手指，浏览器就会尝试在页面内平移，因为这是正在定义的默认行为。

因此，您需要指定您想要覆盖浏览器的默认行为，并告诉它将触摸事件重定向到您的 JavaScript 代码，而不是试图解释它。为此，将页面中不应再对默认行为做出反应的元素作为目标，并对它们应用 CSS 规则:

```
-ms-touch-action: auto | none | manipulation | double-tap-zoom | inherit;
```

根据您想过滤或不想过滤的内容，您可以使用不同的值。你会发现本文中描述的价值观:[构建触摸友好网站的指导方针](https://blogs.msdn.com/b/ie/archive/2012/04/20/guidelines-for-building-touch-friendly-sites.aspx)。

经典的用例是当你的页面中有一个地图控件时。您希望让用户在地图区域内平移和缩放，但在页面的其余部分保持默认行为。在这种情况下，您将只在公开地图的 HTML 容器上应用这个 CSS 规则(-ms-touch-action: manipulation)。

在我们的例子中，添加这个 CSS 块:

```
<style> #drawSurface {
        -ms-touch-action: none; /* Disable touch behaviors, like pan and zoom */ }
</style> 
```

现在产生了这个结果:

| [https://david.blob.core.windows.net/html5/touchsample2.htm](https://david.blob.core.windows.net/html5/touchsample2.htm) | *步骤 1* :刚加完**-ms-touch-action:无***结果*:默认浏览器平移禁用，**鼠标移动可用，但只能用一个手指** |

现在，当您在 canvas 元素中移动手指时，它的行为就像鼠标指针一样。太酷了！但是你会很快问自己这个问题:*“为什么这个代码只追踪一个手指？”*嗯，这是因为我们刚刚陷入 IE10 正在做的提供非常基本的触摸体验的最后一件事:映射你的一个手指来模拟鼠标。据我所知，我们一次只用一只鼠标。因此，使用这种方法，一只鼠标=最多一根手指。那么，如何处理多点触摸事件呢？

### 步骤 2:使用 MSPointer 事件代替鼠标事件

使用您现有的任何代码，用“MSPointerDown/Up/Move”替换您对“mousedown/up/move”的注册，您的代码将直接支持 IE10 中的多点触摸体验！

例如，在前面的示例中，更改这行代码:

```
canvas.addEventListener("mousemove", paint, false);
```

对这一个:

```
canvas.addEventListener("MSPointerMove", paint, false);
```

你会得到这样的结果:

| [https://david.blob.core.windows.net/html5/touchsample3.htm](https://david.blob.core.windows.net/html5/touchsample3.htm) | *第二步*:使用 **MSPointerMove** 代替 mousemove*结果* : **多点触摸作品** |

现在，您可以根据屏幕支持的触摸点在系列中绘制尽可能多的方块！更好的是，同样的代码适用于触摸、鼠标和笔。这意味着，例如，你可以用你的鼠标画一些线，同时用你的手指画其他的线。

如果您想根据输入的类型改变代码的行为，您可以通过`pointerType` 属性值进行测试。例如，让我们想象我们想要为手指画一些 10px 乘 10px 的红色方块，为钢笔画 5px 乘 5px 的绿色方块，为鼠标画 2px 乘 2px 的蓝色方块。您需要用这个处理程序替换前面的处理程序(paint 函数):

```
function paint(event) {
    if (event.pointerType) {
        switch (event.pointerType) {
            case event.MSPOINTER_TYPE_TOUCH:
                // A touchscreen was used
                // Drawing in red with a square of 10 context.fillStyle = "rgba(255, 0, 0, 0.5)";
                squaresize = 10;
                break;
            case event.MSPOINTER_TYPE_PEN:
                // A pen was used
                // Drawing in green with a square of 5 context.fillStyle = "rgba(0, 255, 0, 0.5)";
                squaresize = 5;
                break;
            case event.MSPOINTER_TYPE_MOUSE:
                // A mouse was used
                // Drawing in blue with a squre of 2 context.fillStyle = "rgba(0, 0, 255, 0.5)";
                squaresize = 2;
                break;
        }

        context.fillRect(event.clientX, event.clientY, squaresize, squaresize);
    }
}
```

你可以在这里测试结果:

| [https://david.blob.core.windows.net/html5/touchsample4.htm](https://david.blob.core.windows.net/html5/touchsample4.htm) | *步骤 2b* :测试**指针类型**以测试触摸/笔或鼠标*结果*:你可以改变鼠标/笔/触摸的**行为，但是从 2a 开始 **代码现在只在 IE10+** 中*起作用*** |

如果你足够幸运地拥有一台支持三种输入类型的设备(像*索尼 Duo 11、**微软 Surface Pro* 或*三星平板电脑*)，你将能够看到基于输入类型的三种绘图。很棒，不是吗？

尽管如此，这段代码还是有一个问题。它现在可以在 IE10 中正确处理所有类型的输入，但对于不支持 MSPointer 事件的浏览器(如 IE9、Chrome、Firefox、Opera & Safari)则完全不起作用。

### 步骤 3:进行特征检测以提供回退代码

您可能已经意识到，处理多浏览器支持的最佳方法是特性检测。在我们的例子中，您需要测试这个:

```
window.navigator.msPointerEnabled
```

要知道这只是告诉你当前的浏览器是否支持 MSPointer。它没有告诉你是否支持触摸。要测试是否支持触摸，需要检查 [**msMaxTouchPoints**](https://msdn.microsoft.com/en-us/library/hh772144(v=vs.85).aspx) 。

总之，要让代码在 IE10 中支持 MSPointer 并在其他浏览器中正确地回退到鼠标事件，您需要这样的代码:

```
var canvas = document.getElementById("drawSurface");
var context = canvas.getContext("2d");
context.fillStyle = "rgba(0, 0, 255, 0.5)";
if (window.navigator.msPointerEnabled) {
    // Pointer events are supported. canvas.addEventListener("MSPointerMove", paint, false);
}
else {
    canvas.addEventListener("mousemove", paint, false);
}

function paint(event) {
    // Default behavior for mouse on non-IE10 devices
    var squaresize = 2;
    context.fillStyle = "rgba(0, 0, 255, 0.5)";
    // Check for pointer type on IE10
    if (event.pointerType) {
        switch (event.pointerType) {
            case event.MSPOINTER_TYPE_TOUCH:
                // A touchscreen was used
                // Drawing in red with a square of 10 context.fillStyle = "rgba(255, 0, 0, 0.5)";
                squaresize = 10;
                break;
            case event.MSPOINTER_TYPE_PEN:
                // A pen was used
                // Drawing in green with a square of 5 context.fillStyle = "rgba(0, 255, 0, 0.5)";
                squaresize = 5;
                break;
            case event.MSPOINTER_TYPE_MOUSE:
                // A mouse was used
                // Drawing in blue with a square of 2 context.fillStyle = "rgba(0, 0, 255, 0.5)";
                squaresize = 2;
                break;
        }
    }

    context.fillRect(event.clientX, event.clientY, squaresize, squaresize);
}
```

你也可以在这里测试结果:

| [https://david.blob.core.windows.net/html5/touchsample5.htm](https://david.blob.core.windows.net/html5/touchsample5.htm) | *样本 3* :特征检测 **msPointerEnabled** 提供回退*结果*:在 IE10 中的完整体验和**在其他浏览器中的默认鼠标事件** |

### 步骤 4:支持所有触摸实现

如果你想更进一步，支持所有浏览器和所有触摸实现，你有两个选择:

1–编写代码以并行处理这两个事件模型，如本文所述:[在所有浏览器中处理多点触摸和鼠标输入](https://blogs.msdn.com/b/ie/archive/2011/10/19/handling-multi-touch-and-mouse-input-in-all-browsers.aspx)

2–只需添加一个对 HandJS 的引用，HandJS 是我的朋友 David Catuhe 编写的非常棒的 JavaScript polyfill 库，正如他的文章中所描述的: [HandJS 是一个用于在每个浏览器上支持指针事件的 polyfill](https://blogs.msdn.com/b/eternalcoding/archive/2013/01/16/hand-js-a-polyfill-for-supporting-pointer-events-on-every-browser.aspx)

正如我在本文介绍中提到的，微软最近[向 W3C](https://blogs.msdn.com/b/ie/archive/2012/09/24/towards-interoperable-pointer-events-evolving-input-events-for-multiple-devices.aspx) 提交了 MSPointer Events 规范进行标准化。W3C 成立了一个新的工作组，并且已经公布了基于微软提议的最后工作草案[。MS Open Tech 团队最近也发布了 Webkit](https://www.w3.org/TR/pointerevents/) 的[初始指针事件原型，你可能会感兴趣。](https://blogs.msdn.com/b/interoperability/archive/2012/12/18/open-source-release-from-ms-open-tech-pointer-events-initial-prototype-for-webkit.aspx)

虽然指针事件规范还不是一个标准，但是你仍然可以利用 [David 的 Polyfill](https://blogs.msdn.com/b/eternalcoding/archive/2013/01/16/hand-js-a-polyfill-for-supporting-pointer-events-on-every-browser.aspx) 来实现支持它的代码，并为指针事件成为所有现代浏览器中实现的标准做好准备。通过 David 的库，事件将被传播到 IE10 上的 MSPointer，传播到基于 Webkit 的浏览器上的 Touch 事件，最后传播到鼠标事件。太酷了。查看他的文章，发现并理解它是如何工作的。请注意，这个 polyfill 对于支持老式浏览器优雅地回退到鼠标事件也非常有用。

要想知道如何使用这个库，请看看这篇文章:[借助 Hand 创建一个适用于所有触摸模式的通用虚拟触摸操纵杆。JS](https://blogs.msdn.com/b/davrous/archive/2013/02/16/creating-an-universal-virtual-touch-joystick-working-for-all-touch-models-thanks-to-hand-js.aspx "https://blogs.msdn.com/b/davrous/archive/2013/02/16/creating-an-universal-virtual-touch-joystick-working-for-all-touch-models-thanks-to-hand-js.aspx") 向你展示了如何使用指针事件编写一个虚拟触摸操纵杆。多亏了 HandJS，它可以在 IE10 上运行，在 Windows 8/RT、Windows Phone 8、iPad/iPhone & Android 设备上运行，代码基础完全相同！

## 识别简单的手势

现在我们已经了解了如何处理多点触摸，让我们看看如何识别简单的手势，如点击或按住元素，然后是一些更高级的手势，如平移或缩放元素。

IE10 提供了一个 MSGesture 对象来帮助我们。注意，这个对象目前是 IE10 特有的，不是 W3C 提交的一部分。结合 [MSCSSMatrix](https://msdn.microsoft.com/en-us/library/windows/apps/hh453593.aspx) 元素(我们相当于 [WebKitCSSMatrix](https://developer.apple.com/library/safari/#documentation/AudioVideo/Reference/WebKitCSSMatrixClassReference/WebKitCSSMatrix/WebKitCSSMatrix.html) one)，你会发现你可以用一种非常简单的方式构建非常有趣的多点触控体验。MSCSSMatrix 实际上代表了一个 4×4 的同质矩阵，支持文档对象模型(DOM)脚本访问 CSS 二维和三维转换功能。但是在玩那个之前，让我们从基础开始。

基本概念是**首先向 MSPointerDown** 注册一个事件处理程序。然后在负责 MSPointerDown 的处理程序中，你需要**选择** **你想发送给 MSGesture 对象**的指针，让它检测一个特定的手势。然后它会触发这些事件中的一个: [**MSGestureTap**](https://msdn.microsoft.com/en-us/library/hh771901(v=vs.85).aspx) ，[**MSGestureHold**](https://msdn.microsoft.com/en-us/library/hh771894(v=vs.85).aspx)，[**MSGestureStart**](https://msdn.microsoft.com/en-us/library/hh771898(v=vs.85).aspx)，[**MSGestureChange**](https://msdn.microsoft.com/en-us/library/hh771887(v=vs.85).aspx)， [**MSGestureEnd**](https://msdn.microsoft.com/en-us/library/hh771892(v=vs.85).aspx) ，[**msineriastart**](https://msdn.microsoft.com/en-us/library/hh771906(v=vs.85).aspx)。MSGesture 对象然后将所有提交的指针作为输入参数，并在它们上面应用手势识别器，以提供一些格式化的数据作为输出。你唯一需要做的就是选择/过滤你想让哪些指针成为手势的一部分(基于它们的 ID，屏幕上的坐标，等等)。之后 MSGesture 对象将为您完成所有的魔法。

### 示例 1:处理保持手势

我们将看到如何保存一个元素(一个简单的 div，包含一个图像作为背景)。一旦元素被保持，我们将添加一些角来指示用户他当前已经选择了这个元素。通过在图像的每个角上动态创建四个 div 来生成角。最后，一些 CSS 技巧会以一种巧妙的方式使用变换和线性渐变来获得如下结果:

[![image](img/42affef61a9bd2e5d85a1c3917c9b3ce.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/6840.image_5F00_576B37A6.png)

顺序如下:

1–在您感兴趣的 HTML 元素上注册 mspointer down &[mspointer hold](https://msdn.microsoft.com/en-us/library/hh771894(v=vs.85).aspx)事件

2–创建一个针对相同 HTML 元素
的 MSGesture 对象 3–在 MSPointerDown 处理程序中，向 MSGesture 对象添加您想要监控的各种指针 ID(所有指针 id 或它们的子集，具体取决于您想要实现的目标)

4–在 MSPointerHold 事件处理程序中，检查用户是否刚刚开始保持手势(MSGESTURE_FLAG_BEGIN 标志)的详细信息。如果是，添加角。如果没有，请删除它们。

这导致了下面的代码:

```
<!DOCTYPE html>
<html>
<head>
    <title>Touch article sample 5: simple gesture handler</title>
    <link rel="stylesheet" type="text/css" href="toucharticle.css" />
    <script src="Corners.js"></script>
</head>
<body>
    <div id="myGreatPicture" class="container" />
    <script>
        var myGreatPic = document.getElementById("myGreatPicture");
        // Creating a new MSGesture that will monitor the myGreatPic DOM Element var myGreatPicAssociatedGesture = new MSGesture();
        myGreatPicAssociatedGesture.target = myGreatPic;

        // You need to first register to MSPointerDown to be able to
        // have access to more complex Gesture events myGreatPic.addEventListener("MSPointerDown", pointerdown, false);
        myGreatPic.addEventListener("MSGestureHold", holded, false);

        // Once pointer down raised, we're sending all pointers to the MSGesture object function pointerdown(event) {
            myGreatPicAssociatedGesture.addPointer(event.pointerId);
        }

        // This event will be triggered by the MSGesture object
        // based on the pointers provided during the MSPointerDown event function holded(event) {
            // The gesture begins, we're adding the corners if (event.detail === event.MSGESTURE_FLAG_BEGIN) {
                Corners.append(myGreatPic);
            }
            else {
                // The user has released his finger, the gesture ends
                // We're removing the corners Corners.remove(myGreatPic);
            }
        }

        // To avoid having the equivalent of the contextual  
        // "right click" menu being displayed on the MSPointerUp event, 
        // we're preventing the default behavior myGreatPic.addEventListener("contextmenu", function (e) {
            e.preventDefault();    // Disables system menu }, false);
    </script>
</body>
</html>
```

这是结果:

[https://david.blob.core.windows.net/html5/touchsample6.html](https://david.blob.core.windows.net/html5/touchsample6.html)

尝试点击或鼠标点击元素，没有任何反应。仅用一个手指触摸并保持在图像上，或者用鼠标在图像上长时间点击，角就会出现。松开手指，边角消失。

触摸并保持两个或更多手指在图像上，不会发生任何事情，因为只有当一个唯一的手指握着元素时，才会触发保持手势。

**注:** 白色边框，边角&背景图片通过 *toucharticle.css* 中定义的 CSS 设置。 *Corners.js* 简单地创建四个 div(使用 append 函数)并用适当的 CSS 类将它们放在每个角落的主元素之上。

尽管如此，我对目前的结果还是有些不满意。一旦你拿着图片，只要你稍微移动你的手指，MSGESTURE_FLAG_CANCEL 标志就会被移除边角的处理程序捕获。我更喜欢只有当用户在图片上方的任何地方放开他的手指时，或者当他将手指移出图片所限定的框时，才移除边角。为此，我们将只移除 MSPointerUp 或 MSPointerOut 上的角点。这将给出以下代码:

```
var myGreatPic = document.getElementById("myGreatPicture");
// Creating a new MSGesture that will monitor the myGreatPic DOM Element var myGreatPicAssociatedGesture = new MSGesture();
myGreatPicAssociatedGesture.target = myGreatPic;

// You need to first register to MSPointerDown to be able to
// have access to more complex Gesture events myGreatPic.addEventListener("MSPointerDown", pointerdown, false);
myGreatPic.addEventListener("MSGestureHold", holded, false);
myGreatPic.addEventListener("MSPointerUp", removecorners, false);
myGreatPic.addEventListener("MSPointerOut", removecorners, false);

// Once touched, we're sending all pointers to the MSGesture object function pointerdown(event) {
    myGreatPicAssociatedGesture.addPointer(event.pointerId);
}

// This event will be triggered by the MSGesture object
// based on the pointers provided during the MSPointerDown event function holded(event) {
    // The gesture begins, we're adding the corners if (event.detail === event.MSGESTURE_FLAG_BEGIN) {
        Corners.append(myGreatPic);
    }
}

// We're removing the corners on pointer Up or Out function removecorners(event) {
    Corners.remove(myGreatPic);
}

// To avoid having the equivalent of the contextual  
// "right click" menu being displayed on the MSPointerUp event, 
// we're preventing the default behavior myGreatPic.addEventListener("contextmenu", function (e) {
    e.preventDefault();    // Disables system menu }, false);
```

它现在提供了我一直在寻找的行为:

[https://david.blob.core.windows.net/html5/touchsample6b.html](https://david.blob.core.windows.net/html5/touchsample6b.html)

### 示例 2:处理缩放、平移和旋转

最后，如果你想缩放、平移或旋转一个元素，你只需要写几行代码。你需要首先注册到 [MSGestureChange](https://msdn.microsoft.com/en-us/library/hh771887(v=vs.85).aspx) 事件。该事件将通过 [MSGestureEvent 对象](https://msdn.microsoft.com/en-us/library/hh772076(v=vs.85).aspx)文档中描述的各种属性发送给你，比如当前应用于 HTML 元素的[旋转](https://msdn.microsoft.com/en-us/library/hh772363(v=vs.85).aspx)、[缩放](https://msdn.microsoft.com/en-us/library/hh772082(v=vs.85).aspx)、[翻译](https://msdn.microsoft.com/en-us/library/hh772083(v=vs.85).aspx)、[翻译](https://msdn.microsoft.com/en-us/library/hh772085(v=vs.85).aspx)。

更好的是，默认情况下，MSGesture 对象免费提供了一个惯性算法。这意味着您可以用手指将 HTML 元素放到屏幕上，动画就会自动处理。

最后，为了反映 MSGesture 发送的这些更改，您需要相应地移动元素。最简单的方法是应用一些 CSS 转换来映射旋转、缩放、平移细节，以匹配您的手指手势。为此，使用 [MSCSSMatrix](https://msdn.microsoft.com/en-us/library/windows/apps/hh453593.aspx) 元素。

总之，如果你想把所有这些很酷的手势都用到前面的例子中，就像这样注册这个事件:

```
myGreatPic.addEventListener("MSGestureChange", manipulateElement, false);
```

并使用以下处理程序:

```
function manipulateElement(e) {
    // Uncomment the following code if you want to disable the built-in inertia 
    // provided by dynamic gesture recognition
    // if (e.detail == e.MSGESTURE_FLAG_INERTIA)
    // return;

    // Get the latest CSS transform on the element var m = new MSCSSMatrix(e.target.currentStyle.transform); 
    e.target.style.transform = m
    .translate(e.offsetX, e.offsetY) // Move the transform origin under the center of the gesture .rotate(e.rotation * 180 / Math.PI) // Apply Rotation .scale(e.scale) // Apply Scale .translate(e.translationX, e.translationY) // Apply Translation .translate(-e.offsetX, -e.offsetY); // Move the transform origin back }
```

这给了你最后一个样本:

[https://david.blob.core.windows.net/html5/touchsample7.html](https://david.blob.core.windows.net/html5/touchsample7.html)

尝试用一个或多个手指将图像移动并扔进黑色区域。也尝试用两个或更多手指缩放或旋转元素。结果非常棒，代码非常简单，因为所有的复杂性都由 IE10 本机处理。

## 所有样品的直接链接

如果您没有 IE10 的触摸屏体验，并且您想知道该页面上的示例是如何工作的，您可以在这里单独查看它们:

–[简单触摸默认示例，未执行任何操作](https://david.blob.core.windows.net/html5/touchsample1.htm "https://david.blob.core.windows.net/html5/touchsample1.htm")
–[简单触摸示例步骤 1，使用 CSS-ms-touch-action](https://david.blob.core.windows.net/html5/touchsample2.htm "https://david.blob.core.windows.net/html5/touchsample2.htm")
–[简单触摸示例步骤 2a，使用基本 MSPointerMove 实现](https://david.blob.core.windows.net/html5/touchsample3.htm "https://david.blob.core.windows.net/html5/touchsample3.htm")
–[简单触摸示例步骤 2b，使用指针类型区分](https://david.blob.core.windows.net/html5/touchsample4.htm "https://david.blob.core.windows.net/html5/touchsample4.htm")
–[简单触摸示例步骤 3，使用 ms 指针和鼠标回退
–](https://david.blob.core.windows.net/html5/touchsample5.htm "https://david.blob.core.windows.net/html5/touchsample5.htm")[ms 手势示例 1: MSGestureHold 处理程序](https://david.blob.core.windows.net/html5/touchsample6.html "https://david.blob.core.windows.net/html5/touchsample6.html")

## 相关资源:

–[W3C 指针事件规范](https://www.w3.org/TR/pointerevents/)

–[在所有浏览器中处理多点触摸和鼠标输入](https://blogs.msdn.com/b/ie/archive/2011/10/19/handling-multi-touch-and-mouse-input-in-all-browsers.aspx):polyfill 库，将来会对很多开发者有帮助
–[指针和手势事件](https://msdn.microsoft.com/en-us/library/hh673557(v=vs.85).aspx)

–[使用手势事件超越平移、缩放和点击](https://blogs.msdn.com/b/ie/archive/2012/06/20/go-beyond-pan-zoom-and-tap-using-gesture-events.aspx "https://blogs.msdn.com/b/ie/archive/2012/06/20/go-beyond-pan-zoom-and-tap-using-gesture-events.aspx")

–[IE 试驾浏览器 Surface](https://ie.microsoft.com/testdrive/Browser/BrowserSurface/) 极大地激发了许多嵌入式演示

–在 IE10 中尝试一些出色的触摸游戏:

–[Contre Jour](https://www.contrejour.ie)阅读一篇非常有趣的[幕后](https://www.contrejour.ie/BehindTheScenes.html)文章

–[Atari Arcade Games](https://atari.com/arcade)并阅读这篇内容丰富的文章:[用 CreateJS](https://atari.com/arcade/developers/building-atari-createjs "https://atari.com/arcade/developers/building-atari-createjs") 构建 Atari，这篇文章详细介绍了在所有平台上支持触摸的选择。

–构建会话的记录 3-140: [触摸屏、手写笔和鼠标，天哪！](https://channel9.msdn.com/Events/Build/2012/3-140 "Touchscreen and stylus and mouse, oh my!")

从逻辑上讲，有了本文分享的所有细节和其他资源的相关链接，**您现在就可以在您的网站** & Windows 应用商店应用中实现 MSPointer 事件模型了。然后，您就有机会轻松增强用户在 Internet Explorer 10 中的体验。

* * *

本文是来自 Internet Explorer 团队的 HTML5 技术系列的一部分。通过三个月的免费浏览器堆栈跨浏览器测试@ [来尝试本文中的概念。IE](https://modern.IE)

* * *

## 分享这篇文章