# 从头开始编写 3D 软引擎:奖励部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-scratch-bonus-part/>

在之前的教程中，我们已经学习了如何填充三角形。因为我们的 3D 软件引擎是基于 CPU 的，所以它真的开始消耗大量的 CPU 时间。好消息是**今天的 CPU 是多核的**。然后我们可以想象使用**并行来提升性能**。我们将只在 C#中这样做，我将解释为什么我们不在 HTML5 中这样做。我们还将看到一些简单的技巧，可以提高这种渲染循环代码的性能。事实上，我们将**从 5 FPS 提升到 50 FPS** ，性能提升了 10 倍！

## 计算 FPS

第一步是计算 FPS，以便能够检查我们是否会通过修改算法来获得一些性能。当然，你可以用 C#或者 TypeScript/JavaScript 来实现。

我们需要知道渲染的两帧之间的**增量时间。然后我们只需要捕捉当前时间，绘制一个新的帧(HTML5 中的 *requestAnimationFrame* 或 *CompositionTarget)。渲染*在 XAML)，再次捕获当前时间并将其与之前保存的时间进行比较。你将在几毫秒内得到结果。要获得 FPS，只需将 1000 除以这个结果。例如，如果最佳增量时间**是 **16，66 毫秒，你将有 60 FPS。**

你可以在渲染每一帧后这样做，以获得非常精确的 FPS，或者计算 60 个样本的平均 FPS。David 和我已经在这个系列中研究过这个主题:[HTML5 游戏基准测试:html 5 土豆游戏平台](https://blogs.msdn.com/b/eternalcoding/archive/2013/05/21/benchmarking-a-html5-game-html5-potatoes-gaming-bench.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2013/05/21/benchmarking-a-html5-game-html5-potatoes-gaming-bench.aspx")

总之，在 C#中，添加一个新的 TextBlock XAML 控件，将其命名为“ *fps* ，并使用此代码计算 fps:

```
DateTime previousDate; void CompositionTarget_Rendering(object sender, object e) // Fps var now = DateTime.Now; var currentFps = 1000.0 / (now - previousDate).TotalMilliseconds;
   previousDate = now;
    fps.Text = string.Format("{0:0.00} fps", currentFps); // Rendering loop device.Clear(0, 0, 0, 255); foreach (var mesh in meshes)
   {
       mesh.Rotation = new Vector3(mesh.Rotation.X, mesh.Rotation.Y + 0.01f, mesh.Rotation.Z);
       device.Render(mera, mesh);
   }
    device.Present(); 
```

使用这段代码，使用我的**联想 Carbon X1 Touch** (1600×900)的原生分辨率，我用上一篇文章中分享的 C#解决方案平均运行了 **5 FPS** 。我的联想嵌入了一个英特尔酷睿 i7-3667U 和一个 HD4000 GPU。它是一个超线程双核 CPU。然后显示 **4 个逻辑 CPU**。

## 优化和并行化策略

WinRT 应用程序正在使用。NET Framework 4.5，默认包含**任务并行库**(TPL)。如果你注意你写算法的方式，如果你的算法可以并行化，由于 TPL，使它并行变得非常容易。如果你还不知道这个概念，可以看看《并行编程》中的[。NET 框架 4:入门](https://blogs.msdn.com/b/csharpfaq/archive/2010/06/01/parallel-programming-in-net-framework-4-getting-started.aspx "https://blogs.msdn.com/b/csharpfaq/archive/2010/06/01/parallel-programming-in-net-framework-4-getting-started.aspx")

### 避免触摸用户界面控件

多线程/多任务的第一条规则是线程中没有代码接触 UI 。只有 UI 线程可以接触/操作图形控件。在我们的例子中，我们有一段访问 *bmp 的代码。像素宽度*或 *bmp。PixelHeight* 其中 *bmp* 的类型为 *WriteableBitmap* 。 *WriteableBitmap* 被认为是 UI 元素，不是线程安全的。这就是为什么，我们首先需要改变这些代码块，使它们“可并行化”。在上一个教程中，我们就是这样开始的。您只需要在开始时保存这些值。我们已经在*渲染宽度*和*渲染高度*中完成了。在您的代码中使用这些值，而不是访问 *bmp* 。更改所有对 bmp 的引用。PixelWidth 到 renderWidth 和 bmp。PixelHeight 到 renderHeight。

顺便说一下，这条规则不仅对并行化很重要。这通常也是为了性能优化。因此，通过在我的代码中简单地删除对 *WriteableBitmap* 属性的访问，我就可以在同一台机器上从平均 5 FPS**切换到超过 45 FPS** ！

同样的规则在 HTML5 中非常重要(甚至可能更重要)。您应该**绝对避免直接测试 DOM 元素属性**。DOM 操作非常慢。因此，如果没有必要，每隔 16 毫秒访问一次并不是一个好主意。总是缓存您稍后需要测试的值。我们已经在之前的 HTML5 版本的 3D 引擎教程中这样做了。

### 自给自足

第二条规则是，将在几个可用内核上启动的代码块需要**自给自足**。您的代码不必等待其他代码块的结果太久，否则会降低并行性的兴趣。在我们的例子中，你很幸运，因为我已经在前面的教程中给了你代码来遵守这个规则。

您可能已经看到，我们有几个区域可以通过**并行来切换经典 For 循环。用于**回路。

第一种情况是在 *DrawTriangle* 方法中。然后我们要在一个三角形上平行画几条线。然后，您可以轻松地将回路的 2 个正常*转换为 2 个平行*。对于循环*:*

```
if (dP1P2 > dP1P3) Parallel.For((int)p1.Y, (int)p3.Y + 1, y =>
       { if (y < p2.Y)
           {
               ProcessScanLine(y, p1, p3, p1, p2, color);
           } else {
               ProcessScanLine(y, p1, p3, p2, p3, color);
           }
       }); else { Parallel.For((int)p1.Y, (int)p3.Y + 1, y =>
       { if (y < p2.Y)
           {
               ProcessScanLine(y, p1, p2, p1, p3, color);
           } else {
               ProcessScanLine(y, p2, p3, p1, p3, color);
           }
       }); 
```

但就我而言，输出有点令人惊讶。我正在降低性能**从 45 FPS 切换回 40 FPS** ！那么，性能下降的原因是什么呢？

嗯，在这种情况下，平行绘制几条线是不够的。然后**我们花更多的时间在上下文切换和从一个内核移动到另一个内核**上，而不是做一些真正的处理。您可以使用 Visual Studio 2012 的嵌入式分析工具来检查这一点:[Visual Studio 2012 的并发可视化工具](https://msdn.microsoft.com/en-us/library/dd537632.aspx "https://msdn.microsoft.com/en-us/library/dd537632.aspx")

这是采用此**第一种并行化方法**的核心利用率图:

[![image](img/6473158e1d4462ce7495ffeda93d290c.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/6254.image_5F00_0EE7F5AD.png)

各种颜色与工作线程相关联。真的很没效率。看看与**非并行版本**的区别:

[![image](img/ef0969d48a4a61ee1547ca237eff3540.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/1263.image_5F00_389B4AD3.png)

我们只有一个线程在工作(绿色的那一个),由操作系统自己分派到几个内核上。即使我们在这种情况下没有使用 CPU 的多核功能，我们最终在全局范围内提高了效率。在我们的第一个并行化方法中，我们产生了太多的切换。

### 锁定保护并选择合适的循环进行并行化

嗯，我猜你得出的结论和我一样。并行化 *drawTriangle* 方法中的循环似乎不是一个好的选择。我们需要找到一些需要更多时间来执行的东西，并且在内核切换中更加高效。**我们不是平行画一个三角形，而是平行画几个三角形**。总之，每个内核将处理一个完整三角形的绘制。

使用这种方法的问题在于 *PutPixel* 方法。既然我们想并行绘制几个面，我们可能会遇到两个内核/线程试图同时访问同一个像素的情况。然后，我们需要在处理像素之前保护对像素的访问。我们还需要找到一种有效的方法来锁定对像素缓冲区的访问。事实上，如果我们花更多的时间保护数据而不是处理数据，并行化将再次变得毫无用处。

解决方案是使用一个包含我们将锁定的伪对象的数组。

```
private object[] lockBuffer; public Device(WriteableBitmap bmp) this.bmp = bmp;
   renderWidth = bmp.PixelWidth;
   renderHeight = bmp.PixelHeight; // the back buffer size is equal to the number of pixels to draw
   // on screen (width*height) * 4 (R,G,B & Alpha values). backBuffer = new byte[renderWidth * renderHeight * 4];
   depthBuffer = new float[renderWidth * renderHeight];
   lockBuffer = new object[renderWidth * renderHeight]; for (var i = 0; i < lockBuffer.Length; i++)
   {
       lockBuffer[i] = new object();
   } // Called to put a pixel on screen at a specific X,Y coordinates public void PutPixel(int x, int y, float z, Color4 color) // As we have a 1-D Array for our back buffer
   // we need to know the equivalent cell in 1-D based
   // on the 2D coordinates on screen var index = (x + y * renderWidth); var index4 = index * 4; // Protecting our buffer against threads concurrencies lock (lockBuffer[index])
   { if (depthBuffer[index] < z)
       { return; // Discard }
        depthBuffer[index] = z;
        backBuffer[index4] = (byte)(color.Blue * 255);
       backBuffer[index4 + 1] = (byte)(color.Green * 255);
       backBuffer[index4 + 2] = (byte)(color.Red * 255);
       backBuffer[index4 + 3] = (byte)(color.Alpha * 255);
   } 
```

使用第二种方法，我从平均 45 FPS 移动到 53 FPS。你可能认为性能提升并不显著。但是在下一个教程中， *drawTriangle* 方法将会更加复杂来处理阴影&光照。例如，使用这种方法**和 Gouraud 着色，并行化将使性能几乎翻倍**。

我们还可以使用第二种并行化方法来分析新的核心视图:

[![image](img/797a2bf452f17f23c3d6e5c5173f780a.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3757.image_5F00_0D933701.png)

将它与之前的内核视图进行比较，您就会明白为什么这要高效得多。

您可以**在此下载包含此优化版本的 C#解决方案**:

–**c#**:[softenicsharppart 4 bonus . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart4Bonus.zip)

## 那么，这种情况下 HTML5/JavaScript 哪里出问题了？

HTML5 为 JavaScript 开发人员提供了一个新的 API 来处理类似的方法。它被命名为 Web Workers，可以解决特定场景中的多核使用问题。

David Catuhe &我已经在这三篇文章中多次提到这个话题:

–[html 5 Web Workers 简介:JavaScript 多线程方法](https://blogs.msdn.com/b/davrous/archive/2011/07/15/introduction-to-the-html5-web-workers-the-javascript-multithreading-approach.aspx "https://blogs.msdn.com/b/davrous/archive/2011/07/15/introduction-to-the-html5-web-workers-the-javascript-multithreading-approach.aspx"):如果您还不了解 Web Workers，您应该先阅读这篇文章

–[使用 Web Workers 来提高图像处理的性能](https://blogs.msdn.com/b/eternalcoding/archive/2012/09/20/using-web-workers-to-improve-performance-of-image-manipulation.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2012/09/20/using-web-workers-to-improve-performance-of-image-manipulation.aspx"):这是一篇非常有趣的文章，我们通过 Workers 来提高像素处理的性能

–[教程系列:使用 WinJS & WinRT 为 Windows 8 (4/4)](https://blogs.msdn.com/b/davrous/archive/2012/10/02/tutorial-series-using-winjs-amp-winrt-to-build-a-fun-html5-camera-application-for-windows-8-4-4.aspx "https://blogs.msdn.com/b/davrous/archive/2012/10/02/tutorial-series-using-winjs-amp-winrt-to-build-a-fun-html5-camera-application-for-windows-8-4-4.aspx") 构建一个有趣的 HTML5 摄像头应用程序:教程系列的第四部分，我将使用 web workers 对网络摄像头拍摄的图像应用一些滤镜。

与工人的交流是通过信息进行的。这意味着大部分时间数据都是通过拷贝从 UI 线程发送给工作线程的。很少有类型是通过引用发送的。顺便说一句，如果你是 C++开发人员，不要把它当作参考。事实上，使用[可转移对象](https://www.w3.org/html/wg/drafts/html/master/infrastructure.html#transferable-objects)，当转移到 worker 时，原始对象从调用者上下文(UI 线程)中清除。今天几乎只有*数组缓冲*属于这一类，我们需要发送一个*图像数据*类型。

但这不是我们试图在 HTML5 中加速 3D 引擎的主要问题。在复制的情况下，数据通过非常快速的 *memcpy()* 操作发送。真正的问题是工人们什么时候完成他们的加工工作。您需要将结果发送回主线程，这个 UI 线程需要遍历发送回的每个数组，以重建主像素数组。不幸的是，这个操作只会扼杀我们在工人中可能获得的任何性能增益。

总之，我还没有找到用我们的 3D 软件引擎在 HTML5 中实现并行方法的方法。但我可能漏掉了什么。如果您设法解决当前 web workers 的限制，以获得显著的性能提升，我欢迎您的建议！:)

在我们的下一个教程中，我们将回到常规教程系列来讨论平面着色和 gouraud 着色。我们的物体将开始真正发光！:)

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/06/25/tutorial-part-4-bonus-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-optimizing-amp-parallelism . aspx](https://blogs.msdn.com/b/davrous/archive/2013/06/25/tutorial-part-4-bonus-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-optimizing-amp-parallelism.aspx)。经作者许可，在此转载。*

## 分享这篇文章