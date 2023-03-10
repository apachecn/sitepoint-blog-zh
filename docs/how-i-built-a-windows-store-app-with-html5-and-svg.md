# 我如何使用 HTML5 和 SVG 构建 Windows 应用商店应用

> 原文：<https://www.sitepoint.com/how-i-built-a-windows-store-app-with-html5-and-svg/>

Paint 4 Kids 是一款 [Windows Store 应用](http://apps.microsoft.com/windows/en-gb/app/paint-4-kids/92d1de2b-232b-43ab-993b-b7435ea96439)，专门为孩子设计。一个简单的应用程序，用于着色和绘图。你可以直接从 Windows Store 网站上了解消费者的特征，在那里你还可以看到一些屏幕截图。对于本文，你可以简单地把它想象成一个有几个绘图的应用程序，你可以和它们进行交互。

从技术角度来看，它完全是使用 HTML、CSS、JavaScript 和 SVG 等标准 web 技术构建的。这里考虑一个重要的方面:使用 Windows 8，我们正在编写一个重用我们的 web 技能的应用程序，我们正在为 web 平台开发，使用 Internet Explorer 10 渲染引擎的底层 HTML5 支持。因此，一个直接的好处是，我们不必测试和支持所有不同的浏览器版本，也不必使用 polyfills 来模拟旧浏览器中的新 API 特性。我们可以利用 web 平台的优势，直接在 JavaScript 中使用 Windows 8 的一些特定 API。

本文将讨论可缩放矢量图形(SVG)在 Paint 4 Kids 中的使用，从项目的一些需求开始，继续解决它们以及使用 SVG 的一些缺陷。我希望这些考虑因素也能适用于你的应用程序。

## 使用 SVG 的要求和原因

Windows 应用商店应用为你提供了一个很好的机会，在市场上你可以分发和销售你的应用，在设备上你可以运行你的应用。你的应用可以在不同屏幕尺寸、不同屏幕分辨率和像素密度的设备上使用。这一点必须加以考虑，浏览[这篇文章](http://blogs.msdn.com/b/b8/archive/2012/03/21/scaling-to-different-screens.aspx)可以让你很好地理解如何将你的应用扩展到不同的屏幕，以及如何使用 Windows 模拟器测试你的应用。

其中一个要求是，我们希望有一个单一的绘图，将显示在不同的分辨率。我们最终使用了 SVG，一种用于二维图形的矢量图像格式。我们的方法是以 2560×1440 的特定分辨率创建一个图形，并缩小到当前用户的分辨率。

一个优点是在 SVG 中用颜色填充路径非常简单快速，而*即*是该应用的主要特性之一。当用户点击屏幕时，很容易截取绘图的相应部分，并用特定的颜色填充路径。

另一个要求是，我们希望重用一些在 XAML 制作的图纸，并有一个良好的工具支持，以便我们可以创建和添加新的应用程序在一个简单快捷的方式绘图。Inkscape 非常适合这个目的。您可以导入 XAML 绘图并将其导出为 SVG 文件，因为这两种基于矢量的格式非常相似，所以导出总是非常成功。

使用 SVG 的一个 *dis* 优点是，随着添加到 DOM 中的对象数量的增加，DOM 操作会变得很慢，因此一些性能测试和优化通常是必要的。在这篇 Internet Explorer [的博客文章](http://blogs.msdn.com/b/ie/archive/2011/04/22/thoughts-on-when-to-use-canvas-and-svg.aspx)中，你可以看到一篇关于使用 SVG 而不是 Canvas 的利弊的精彩文章。

Paint 4 Kids 的另一个要求是用户应该能够保存他们的绘图。不幸的是，在撰写本文时，不可能从 SVG 文件创建图像，所以我们最终将 SVG 文件转换为 Canvas 对象。

另一种方法是使用 Canvas 代替 SVG。使用 Canvas，您必须为不同的分辨率创建不同的 raw 图像——至少如果您希望您的绘图在不同的分辨率下看起来不错，否则线条会看起来像素化。

另一个考虑是如何填充绘图中的形状。当你使用带有触摸点的画布时，你是在处理一个仅仅由像素组成的原始图像；你不能用简单的方法来处理像矩形、圆形和路径这样的形状。如果你想有一个以黑线为边界的形状，你必须实现一个整体填充(或种子填充)算法来实现这个目的，如果要填充的区域很大(相对于屏幕大小)，你会看到这个区域逐渐被填满，而 SVG 的填充效果是瞬间的。

## 使图形适应不同的外形:视口和视框

如上所述，我们以 2560 x 1440 像素的特定分辨率开始我们的 SVG 绘图，每个绘图都从 Windows Store 应用程序内的一个文件动态加载到 DOM 中。为了方便起见，选择了这个特定的值，您可以使用其他的起点。在《画 4 个孩子》中，我们想使用一幅画，并以不同的分辨率缩放。查看以下在 Windows 模拟器中以不同分辨率运行的同一图形的图像。

![figure1](img/b0269c8485f7a689f429003dc73d6e4a.png)

![figure2](img/df70f2aa4ed68972da9e5db48db64123.png)

第一张照片是模拟分辨率为 1024 x 768 像素的 10 英寸显示器拍摄的，第二张照片是模拟分辨率为 2560 x 1440 像素的 27 英寸显示器拍摄的。

这种固定的分辨率给了我们一种虚拟的空间坐标系统。在 SVG 中，我们可以使用 viewBox 属性设置这个坐标系。如果你用一个工具用这个坐标系来画，所有的图形元素都是相对于它的。

您可以从这个分辨率缩小(或放大)到另一个特定的分辨率。例如，假设您的平板电脑的分辨率为 1366 x 768，您只需在包含您的绘图的 SVG 元素上设置宽度和高度属性。这两个属性定义了 SVG 的视口，即设备的实际分辨率。

视框的值由四个数字组成，分别代表最小*x*-坐标、最小*y*-坐标、您要在视口中映射和缩放的坐标系的宽度和高度。因此，组合视图框、宽度和高度属性给我们预期的结果。

下面是每个绘图的根 SVG 元素，一个简单的 XML 文件。坐标系从屏幕的左上角开始，如果您已经使用过 SVG，您会想到这一点。

```
<svg viewbox="0 0 2560 1440">...
```

当用户选择要着色的图形时，图形被加载到 DOM 中，我们可以设置视口属性。

为了找到未命名的 SVG 元素的根，我们使用[document . query selector](http://msdn.microsoft.com/en-us/library/ie/cc288169(v=vs.85).aspx)API，使用伪类选择器来找到第一个 SVG 元素。因为这个 API 只被调用一次，所以当用户选择一个元素来绘制时，我们可以忽略任何性能延迟。

```
var svgd;

svgd = document.querySelector("svg:first-of-type");

svgd.setAttribute("width", window.innerWidth);

svgd.setAttribute("height", window.innerHeight);
```

代码示例还使用[窗口](http://msdn.microsoft.com/en-us/library/windows/apps/hh466245.aspx)对象及其属性 inner*来获取运行时的实际像素分辨率。

处理 viewport 和 viewBox 时的另一个考虑因素是**纵横比**。如果两个坐标系具有不同的宽高比，有时您会希望生成的图像不一致。在其他情况下，您可能希望保持纵横比，并统一缩放图像。SVG 使用[preserve spectra ratio 属性](https://www.w3.org/TR/SVG/coords.html#PreserveAspectRatioAttribute)来决定图像是否需要均匀缩放。我们将在以后谈到“邮票”时讨论这个问题。对于图形，默认行为是统一缩放视图框以适应视口，这正是我们想要的。

**如何用颜色和图像填充路径**

填充 SVG 形状，如路径、矩形或其他形状是一个非常简单的步骤，而且非常迅速，因为这就像在 CSS 中设置一个**样式属性**，所以您不需要编写代码来查找由线条包围的每个像素。你可以看看下面的代码，它是在 SVG 区域内触发 tap 事件时的回调函数。

```
var el = document.elementFromPoint(e.x, e.y);

var selectedColor = "255,0,0,1";

el.setAttribute("style", "fill:rgba(" + selectedColor + "); stroke-width:3;);
```

在上面的代码中， **e** 是一个类型为 [MSPointerEvent](http://msdn.microsoft.com/it-IT/library/windows/apps/hh441233) 的对象。这个对象非常重要，如果你订阅了一些 MSPointer*事件(比如 [**MSPointerDown**](http://msdn.microsoft.com/en-US/library/windows/apps/hh465891) )就可以得到这个对象。只需一行代码，您就可以订阅来自鼠标、触摸甚至是笔的事件！此外，在 [MSPointerEvent](http://msdn.microsoft.com/it-IT/library/windows/apps/hh441233) 中，如果需要，您可以读取 [pointerType](http://msdn.microsoft.com/it-IT/library/windows/apps/hh466130) 属性，该属性为您提供哪种设备生成事件的详细信息。如果你对这个话题感兴趣，你可以阅读这篇关于[IE 10 和 Windows Store 应用上触摸输入的 API](http://blogs.msdn.com/b/ie/archive/2011/09/20/touch-input-for-ie10-and-metro-style-apps.aspx)的博文。

回到代码，这里的 e 对象只用于从输入设备获取 x，y 点坐标，使用 elementFromPoint API。el 现在是我们想要填充颜色的特定形状，它的类型是 [SVGPathElement](http://msdn.microsoft.com/en-us/library/ie/ff972086(v=vs.85).aspx) 。代码的其余部分很简单，设置 SVGPathElement 的填充颜色和线条宽度，而不管它的实际形状如何。

除了使用 setAttirbute API，您还可以直接在 SVGPathElement 上设置 [fill](http://msdn.microsoft.com/en-us/library/ie/ff972257(v=vs.85).aspx) 和 [strokeWidth](http://msdn.microsoft.com/en-us/library/ie/ff972279(v=vs.85).aspx) 属性，避免字符串解析可以提高性能；即使在这种情况下用户不会察觉到。

在示例中，颜色是标准的纯色 RGBA，用户可以从调色板中选择，但是您也可以使用来自图像的路径或使用渐变来填充形状。Paint 4 Kids 不仅定义了一组常见的颜色，还定义了一些图像，如石头、草等。要在 SVG 中做到这一点，您可以定义一些**模式**，如下面的代码所示:

```
<pattern id="imgPatterns_1" patternUnits="userSpaceOnUse" width="128" height="128">

    <image xlink:href="img/BRICK.PNG" width="128" height="128" />

</pattern>
```

代码中需要注意两件事:首先，我们定义了一个基于包含的图像的 [SVGPatternElement](http://msdn.microsoft.com/en-us/library/windows/apps/hh869088.aspx) 。其次，您可以定义 patternUnits 属性，该属性定义如何用图案本身填充形状。用户空间使用简单地重复图片越来越多的次数，中间没有填充。一旦定义了模式，我们就可以在上面使用的 fill 属性中使用以下语法:

```
var selectedColor = "url(#imgPatterns_1)";

el.setAttribute("style", "fill:" + selectedColor + "; stroke-width:3;);
```

查看代码，您会注意到 fill 属性现在是一个 url，它使用上面定义的模式元素的 id，前面带有#符号(例如#imgPattern_1)。

看下面的图片，看看使用中的一些图案效果。

![figure3](img/448ceba59a8c9cc4cca92ac950668f85.png)

**为“邮票”重用 SVG 元素**

Paint 4 Kids 还为您提供了将一些形状作为图章插入到您的绘图中的可能性，您可以将这些图章附加到绘画上。对于用户来说，邮票是一个像南瓜一样的形状，或者是一个球，他们可以把它放在绘图中任何他们想要的地方，他们还可以决定形状的大小；他们可以在绘图中插入越来越多的相同形状。

从 SVG 的角度来看，每个形状都可能是复杂的，正如你所希望的那样，所以越来越多地插入相同的形状，最终可能会使你的 DOM 越来越大，最终使你的应用程序在慢速设备上出现一些性能滞后。优化如何管理这种情况至关重要。

为了实现这个特性，我们在 SVG 中使用了符号和 use 元素的组合。symbol 元素提供了一种对元素进行**分组的方法，但是当一个 symbol 元素被添加到页面的 DOM 中时，它不会被显示出来，所以你可以把它当作可以**重用的东西**。因此，我们可以一次插入一个复杂的 SVG 形状，并使用 use 关键字多次重用它。在下面的例子中，您会看到一些 SVG 代码。**

```
<symbol id="bottiglia" viewBox="0 0 40 40" preserveAspectRatio="xMinYMin meet" >

    <path …>

</symbol>

<use id="onlyforimg" xlink:href="#bottiglia" height ="80" width="80"></use>
```

包含实际形状的路径——*为简洁起见省略了*——包含在一个 symbol 元素中，该元素还定义了一个 viewBox，用于在最初绘制形状时设置形状的大小。这里的视图框是必要的，因为我们可以在下面的使用元素中调整形状的大小，在这里我们设置高度和宽度为原始大小的两倍——这是另一个*需要的东西*,如上所述。从上面的代码中可以很容易地注意到，use 元素引用了 symbol 元素的 id 属性。此外，这种用法非常少，因此我们可以越来越多地重复使用它，最终改变绘图的大小，减小 DOM 的大小。

在 real 应用程序中，use 元素是在 JavaScript 中动态创建的，不仅设置了宽度和高度，还设置了 x 和 y 坐标来定位形状，使形状的中心位于用户点击屏幕的位置。为了做到这一点，一些数学(这里不解释)是设置这两个坐标所必需的。为了让所有的东西都工作，我们需要使用 preserveAspectRatio 和 meet 属性来获得统一的缩放比例，以便视图框被完全包含，而不是在映射到视口上时被分割，并设置**对齐**。xMin 将视框的最小 x 与视口的左角对齐，yMin 将视框的最小 y 与视口的上边缘对齐。你可以从 SVG 规范[这里](https://www.w3.org/TR/SVG/coords.html#PreserveAspectRatioAttribute)获得这些值的完整参考。

**加载 SVG 文件**

本文的前一部分讨论了最终可以直接在现代浏览器中运行的标准 SVG。从现在开始，我们将混合一些 Windows Store API，这些 API 可用于构建特定于 Windows 8 平台的 Windows Store 应用程序，并最终可以从其他支持的语言中调用。

因为 Paint 4 Kids 使用许多绘图，所以在需要时从 appx 文件异步加载单个绘图，然后放入页面的 DOM 中。异步加载是使用 Windows 8 load async API 完成的，如代码所示。Windows 应用商店应用阻止我们将动态代码加载到 DOM 中，这是因为一般来说，这些代码可能来自外部调用，如果代码是恶意的，最终会在您的应用中造成一些安全问题。然而，我们正在加载的代码是可信的，因为它已经在 appx 文件中，并且是由我们提供的。您可以安全地调用 [WinJS。utilities . setinnerhtmlunsafe](http://msdn.microsoft.com/en-us/library/windows/apps/br211696.aspx)函数，允许我们在需要时将绘图加载到 DOM 中。

```
svgfolder.getFileAsync("drawing.svg").then(function (file) {

    file.openAsync(Windows.Storage.FileAccessMode.read).then(function (stream) {

        inputStream = stream.getInputStreamAt(0);

        reader = new Windows.Storage.Streams.DataReader(inputStream);

        size = stream.size;

        reader.loadAsync(size).then(function () {

            svg = reader.readString(size);

            WinJS.Utilities.setInnerHTMLUnsafe(document.getElementById("s1"), svg);

        });

    });

});
```

svgFolder 是一个类型为 [StorageFolder](http://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.storagefolder) 的对象，你可以调用一个异步方法来搜索一个 [storageFile](http://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.storagefile) 对象——这里省略了错误处理——在这个对象上我们可以打开一个流和一个 [dataReader](http://msdn.microsoft.com/library/windows/apps/BR208119) 对象来读取内容。最后，svg 变量包含一个表示整个 SVG 绘图的字符串。如上所述，我们可以调用 setInnerHTMLUnsafe 将绘图添加到 s1 DOM 元素中。

这里的好处是，SVG 可以在 Visual Studio 2012 的 DOM Explorer 中进行检查，如下所示:

![figure4](img/ecba26ceebb7e85397c3ffb7ab62698f.png)

例如，您可以在运行时从应用程序中选择一个特定的形状——它也可以在模拟器中工作 DOM Explorer 将在运行时向您显示相关的 SVG，它可能是动态加载和随后应用于它的转换的结果。非常酷的东西！

**将 SVG 图形转换为画布并保存为图像**

你需要一张图片的 jpeg 格式，例如，如果你想将图片保存到文件系统，或者如果你想使用 Windows 8 Share Charm，或者在应用程序的其他部分使用。为此，我们使用了 [canvg 库](http://code.google.com/p/canvg/) **，它将 SVG 文件转换为画布对象**。转换可能需要一些时间，这取决于 SVG 文件的大小和您的硬件平台，所以您可以通知用户正在发生的事情，我们使用带有进度环的**弹出按钮**，当转换开始时会显示出来。

既然有了 Canvas 对象，就可以获得对整个字节数组的引用，然后可以使用 Windows 编码 API 来获得所需格式和大小的图像。下面是一个示例函数:

```
function doSaveDrawingToFileEnd (fil) {

    var Imaging = Windows.Graphics.Imaging;

    var stream, encoderId, cc, offset, outputPixelData;

    fil.openAsync(Windows.Storage.FileAccessMode.readWrite).then(function (_stream){

        stream = _stream;

        return Imaging.BitmapEncoder.createAsync(jpegEncoderId, stream);

    }).then(function (encoder) {

        cc = document.getElementById('canvas');

        //ignore the realBRectHeight variable below

        //it is not important to understanding the logic

        offset = (height - realBRectHeight) / 2;

        outputPixelData =

cc.getContext("2d").getImageData(0, offset, cc.width,cc.height - offset);

        encoder.setPixelData(Imaging.BitmapPixelFormat.rgba8, Imaging.BitmapAlphaMode.straight, width, realBRectHeight, 90, 90, outputPixelData.data);

        return encoder.flushAsync();

    }).then(null, function (error) {

    console.log(error.message);

    }).then(function () {

    if (stream) stream.close();

    })

}
```

首先你得到一个对 [storageFile](http://msdn.microsoft.com/en-us/library/windows/apps/windows.storage.storagefile) 对象的引用——没有在代码中列出 fil 变量，这将是我们最终的 jpeg 图像；然后打开一个流进行写入，并创建一个 BitmapEncoder 对象，将编码类型设置为 jpegEncoderId，这用于将字节数组编码到您想要的图像中。cc 变量是对包含转换后的 SVG 文件的 canvas 对象的引用。使用标准的 getImageData 方法，我们获得了我们想要转换的字节——在代码中，您可以看到一个用于裁剪绘图的特定部分的偏移值，但是您可以在这里忽略该值，因为它对于理解逻辑不是特别有用。现在，使用带有编码器的 setPixelData API，您可以为要生成的图像设置一些值，图像实际上是在调用 flushAsync 时生成的。

**结束**

在整篇文章中，我们看到了一些关于 SVG 技术考虑，以及如何在 Paint 4 Kids 中使用它。SVG 是一种非常有趣和强大的 web 技术，您可以利用它用 JavaScript 构建令人惊叹的 Windows 应用商店应用程序。你也可以在 [IE 试驾网站](http://ie.microsoft.com/testdrive/Graphics/SVGoids/Default.xhtml)上搜索一些**搞笑**样片**用 SVG 直接玩**。如果您有类似的情况，我希望这有所帮助。

如果你想了解更多关于如何构建 Windows 应用商店应用的信息，请查看[世代应用](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200185226)。

## 分享这篇文章