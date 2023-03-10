# 在 HTML5 浏览器中实现“割断绳子”

> 原文：<https://www.sitepoint.com/bringing-cut-the-rope-to-life-in-an-html5-browser/>

屡获殊荣的手机游戏**切割绳子**是任何玩家的最爱。既有趣又可爱。所以我们有了一个想法:让我们通过使用 HTML5 的力量在网络上提供这个伟大的游戏，让更多的观众可以使用它。

为了做到这一点，微软的 Internet Explorer 团队与 [ZeptoLab](http://zeptolab.com/) (游戏的创造者)和 [Pixel Lab](http://thinkpixellab.com/) 的专家合作，让**在浏览器中实现了《砍断绳子》**。最终的结果是一个真实的网络游戏翻译，展示了 HTML5 提供的一些最好的东西:画布渲染的图形，基于浏览器的音频和视频，CSS3 风格和 WOFF 字体的个性。

可以在: [www.cuttherope.ie](http://www.cuttherope.ie/) 玩 HTML5 版的**砍断绳子**。

我们认为 HTML5 版本使网络更加有趣，它展示了最新版本的 Internet Explorer 在标准支持方面的进步。考虑到这一点，我们想分享一些在这个项目中使用的很酷的“幕后”技术细节，以帮助您构建自己的 HTML5 网站，并最终为 [Windows 8 商店](http://msdn.microsoft.com/en-us/windows)做好准备！

![Cut the Rope in IE9](img/3b97c84adf9f842f2a45d5f10b29cb81.png "figure1")

*剪绳运行在 IE9 中作为 HTML5 应用，移植自原 iOS 源代码。*

![special levels](img/8b3a65402dc9504b4dc2b0c5e149881e.png "figure2")
*本版本独有的一个特别设计关卡的截图。*

**Objective-C 转 JavaScript**

在将 **Cut the Rope** 带到一个新平台的过程中，我们希望确保我们保留了体验中独特的物理、运动和个性。所以我们很早就决定将这款游戏作为原生 iOS 版本的“移植”(而不是重写)。我们以对原始 Objective-C 代码库的广泛调查开始了这个项目。事实证明这是一个庞大而复杂的游戏。原生 iOS 版本由大约 15000 行代码组成(不包括库)！代码库中最复杂的部分是物理、动画和绘图引擎。它们本身就很复杂，但三者紧密相连且高度优化的事实使它们变得更加复杂。

这是一项艰巨的任务:将这些代码放入浏览器，同时保持游戏玩家习惯的独特个性和令人难以置信的质量。为了实现这一点，我们押注于 JavaScript。

在过去，JavaScript 有着缓慢语言的名声。坦率地说，这个名声最初是有效的。早期的 JavaScript 引擎是为简单的“脚本”任务设计的(因此得名)。然而今天，JavaScript 引擎已经得到了高度优化。有了即时编译等特性，JavaScript 现在可以以接近本地的速度执行。

除此之外，我们知道 JavaScript 编码不同于——也需要不同于——编译语言编码的思维方式。当我们从 Objective-C 移植这个游戏时，我们知道我们需要接受做这些改变和优化的任务。

一个明显的例子是 JavaScript 中缺少*结构*。结构是相关属性的轻量级聚合。使用 JavaScript 对象保存一组属性很容易，但是这种方法和适当的结构之间有重要的区别。第一个区别是，无论何时将结构赋给变量或传递给函数，它们都会被复制。因此，用 Objective-C 这样的编译语言编写的函数可以修改作为参数传递的结构，而不会影响调用者中的值。即使在一个函数中，将一个结构赋给不同的变量也会复制值。另一方面，JavaScript 对象是通过引用传递的。所以当一个函数修改一个对象参数时，调用者可以看到这些变化。

模仿结构本质的一个简单方法是创建 JavaScript 对象的副本，用于赋值或参数传递。在本地语言中，使用结构的开销通常很小。用 JavaScript 创建一个对象要昂贵得多，所以我们必须非常小心地尽量减少分配的次数。特别是在赋值的时候，只要有可能，我们就试图复制单个的属性，而不是创建全新的对象实例。

另一个例子是 Objective-C 代码库的面向对象特性。代替传统的基于对象的继承，JavaScript 提供了原型继承，即基于原型属性的继承。这是一种高度简化的对象继承形式，与 Objective-C 等传统的面向对象语言不兼容。幸运的是，有各种类库可以帮助您为 JavaScript 编写面向对象编程(OOP)风格的代码；我们使用了一个非常简单的工具,它是由 John Ressig(因 jQuery 出名)编写的。(请注意，ECMAScript5 是 JavaScript 最新版本的规范，它也包含了对类的一些支持，但是我们选择不在这个端口中使用它，因为我们对这个语言版本不太熟悉，再加上我们的开发进度很紧)。

除了从 Objective-C 移植到 JavaScript，我们还需要将我们的图形代码从 OpenGL 移植到 HTML5 Canvas API。总的来说，一切都很顺利。Canvas 是一个速度惊人的渲染表面，尤其是在 API 被硬件加速的浏览器中(比如 Internet Explorer 9)。

![aliased lines](img/61670f99b5e9b94d3f6dc8aa8728ec3f.png "figure3")

使用 canvas API 绘制绳索的例子。

令人惊讶的是，我们遇到了一些领域，画布提供的功能比《割断绳子》移动版使用的 OpenGL ES 版本更多。一个例子是绘制[反锯齿线](http://en.wikipedia.org/wiki/Spatial_anti-aliasing)。在 OpenGL 中绘制抗锯齿线条需要将线条镶嵌成一个三角形带，并淡化端帽的不透明度以完成透明。HTML5 画布自动处理用其 line API 绘制的线条的抗锯齿。这意味着我们实际上需要从 OpenGL 版本中删除代码。在 OpenGL 代码中展开三角形顶点的数组也给了我们比试图本地复制绘制直线的三角形条带方法更好的性能。

最后，我们有将近 15，000 行代码在浏览器中执行(它被缩小了，所以如果你在浏览器中查看源代码，它会比这个少很多行)。预计到与这么多代码相关的复杂性，Denis Morozov(zepto lab 的开发总监)在早期提出了一个合理的问题:HTML5 会给我们提供这个游戏所需的速度和性能吗？

为了回答这个问题，我们创建了一个早期的“性能”里程碑，在这个里程碑中，我们专注于获得游戏运行中最激烈部分的最小版本。也就是说，我们想看看绳子是什么样子，以及我们是否能在浏览器中处理复杂的物理引擎。

**性能**

这个项目进行了三个星期，我们终于有了物理和绘图引擎的基础，用一个简单的计时器来启动动画。我们现在可以在游戏场景中渲染几根绳子、一颗星星和一个精灵。进步！到第四周，我们已经包括了一些基本的鼠标交互，这样我们就可以玩游戏了！我们一直在测试性能，但是我们想让 ZeptoLab 的团队给我们反馈。

当我们与 ZeptoLab 分享代码时，他们对我们在现代浏览器中看到的性能(尤其是游戏的速度和流畅度)感到惊喜。老实说，我们只是稍微屏住了呼吸。我们期望 JavaScript 很快，但是物理计算很密集，而且必须实时进行。这是一个很好的例子，证明了关于 JavaScript“缓慢”的常见偏见是错误的。最新一代的 JavaScript 引擎速度惊人。

在这种情况下，我们在 Internet Explorer 9 中预览游戏。当你加载游戏时，Internet Explorer 9 的 Chakra JavaScript 引擎会在后台线程上预编译代码——就像编译器编译 Objective-C 或 C++这样的语言一样。然后，它实时地将编译后的代码(字节码)发送给游戏线程执行。结果是近乎本机的执行速度。令人惊讶的是，这是您可以从 JavaScript 引擎免费获得的东西——我们不需要在代码中做任何特殊的事情。

![framerate test results](img/a0218eea41e0881f06198f70e180dbbc.png "figure4")

*项目早期的帧率测试结果(注意帧率上限为 60FPS)*

我们在 JavaScript 上的赌注得到了回报，所以我们将注意力转向了硬件和浏览器。凭借 Internet Explorer 的硬件加速渲染堆栈以及我们在[迪士尼创](http://windowsteamblog.com/ie/b/ie/archive/2011/06/03/behind-the-scenes-of-disney-tron-legacy-digital-book-site.aspx)和其他 [HTML5 网站](http://www.beautyoftheweb.com/)的经验，我们对它在我们的测试机上完美运行游戏的能力没有任何担忧。我们轻松达到了 60 FPS(每秒帧数)的上限目标。然而，我们想确保游戏能在其他硬件和浏览器上运行良好。[这是我们在一些初步测试后看到的](http://www.cuttherope.ie/dev/notes-framerates.jpg)。

基于这些数字，我们设定 30 帧/秒为最低标准。我们决定当浏览器低于这个阈值时，我们会通知用户。他们仍然可以玩游戏，但我们会告诉他们，这可能会感觉有点迟钝。这确保了我们支持硬件和软件的巨大多样性，并为游戏的所有访问者提供最佳体验。

我们想指出两件事。第一，这款游戏目前的版本在台式电脑和苹果电脑上用鼠标玩效果最好。我们还没有添加对基于触摸的输入的支持，但这是我们正在考虑的未来版本。

第二，当前版本的 Chrome(版本 16)有[个与媒体播放](http://code.google.com/p/chromium/issues/detail?id=107933)相关的已知问题，使得《割绳子》中声音不可预测。我们研究了解决方法，并尝试以多种格式(包括 WebM)重新编码媒体，但还没有找到一种格式或 MIME 配置或任何其他可以可靠地解决问题的东西。这些似乎是浏览器错误和已知问题。更重要的是，尽管音频时断时续，游戏仍然可以继续玩下去，令人愉快。有鉴于此，虽然我们可以说 Internet Explorer 9 用户获得了很好的免费插件体验，但 Chrome 和一些 Firefox 用户可能会遇到音频问题，但会注意到我们退回到 flash 插件，以确保声音效果和音乐能够正常工作。

**工具**

HTML5 的一个伟大之处在于，你不需要学习一门新的语言来释放这项新技术的力量。如果你知道并理解 JavaScript，你已经可以使用现代浏览器所能做的一切。你甚至可以像这样创建自己的游戏！

**代码编辑器和开发环境**

![VWD 2010 Express](img/3825bc90124906d359e74795227e4c52.png "figure5")

Visual Web Developer 2010 Express 是一款免费下载的软件，对于经验丰富的 Web 开发人员来说也是一款非常棒的编辑器。

![profiler screenshot](img/443c2a6345d003b507af6a09e1e8787b.png "figure6")

*一张来自分析器的截图，显示了 Calc2PointBezier 所花费的不成比例的时间，该函数用于计算绳子段的位置。*

有一些很棒的免费工具可以让 JavaScript 和 HTML5 变得简单。我们的大部分开发是在 Visual Web Developer 2010 中完成的([“express”版本可以在这里免费获得](http://www.microsoft.com/visualstudio/en-us/products/2010-editions/visual-web-developer-express))。这是一个真正强大的 web 编辑器，具有 JavaScript 和 CSS 的自动完成功能。速成版免费太好了！我们在 Windows 7 上的 Internet Explorer 9 中进行了大部分测试，有时我们也会在 Firefox、Chrome、Safari 和 Internet Explorer 10 开发者预览版中进行测试。总的来说，所有主流浏览器对我们使用的 HTML5 特性都有非常一致的实现，而且在大多数情况下，我们在 Internet Explorer 9 中测试的任何东西在其他地方都“正常工作”。

查看我们的资源加载器！

《割断绳子》拥有独特而细致的视觉风格——大量的图像、声音和视频形式的媒体——成本很低。结果就是整个游戏比一般的网站大很多。总的来说，大约是 6 MB(相比之下，一个典型的站点需要 20-30 万)。这么多的媒体可能需要一点时间来下载，我们不能开始游戏，直到我们知道一切都在那里。如果您丢失了一两张图像，传统的网页是相当宽容的，但是如果图像不可用，HTML5 canvas API (drawImage)就会失败。

为了应对这一挑战，我们希望创建一个资源加载器，它可以下载页面所需的所有内容，并在下载时给我们良好的反馈。这段代码做了很多聪明的事情:

1.它处理不同浏览器如何处理下载的特性，以及它们如何通知你它们的进度。

2.它可以让你对下载的顺序做出明智的决定(例如，你可能想先下载大文件，或者在得到游戏图像之前下载所有的菜单图像)。

3.最后，当事情到来时，它会给你智能事件，这样你就可以向用户显示进度，甚至在第一组完成时开始游戏的一部分。

构建这些类型的库很难做好。因为我们真的很高兴这一切是如何走到一起的，我们想与您分享我们的资源加载器的代码。结果是 PxLoader，一个 javascript 资源加载器库，你可以使用它来为 HTML5 应用程序、游戏和网站制作预加载器。它是开源和免费的。你可以从页面顶部抓取，或者点击[这里](http://thinkpixellab.com/pxloader)。

**Internet Explorer 中的性能工具**

开发过程中另一个不可或缺的工具是 Internet Explorer 9 中的 JavaScript Profiler。剖析让您发现代码中的热点和瓶颈。在我们的第一个与性能相关的里程碑中，当我们发现在一些机器上我们停留在 20 或 30 FPS 时，我们几乎停止了。

我们做了一些最初的代码审查，但没有什么突出的。我们用分析器加载了游戏，立即发现我们在 satisfyConstraints()函数中花费了大量时间。这个函数计算一些与绳子物理相关的数学。我们移植的 Objective-C 实现是递归编写的，向每个更深层次的调用传递一个新对象。

在微软的指导下，我们决定用相同代码的“解包”迭代版本替换递归函数。结果是惊人的。我们看到每个浏览器都有 10 倍的提升！坦率地说，如果没有 Internet Explorer 9 中的分析工具，我们永远也不会发现这一点。

下一步是什么？

在 9 月的 BUILD 上，微软展示了 Windows 8 的开发者预览版。随着这一宣布，HTML5 的故事变得更加有趣，因为 Metro 风格的应用程序可以使用各种开发工具集来创建，包括 HTML5。这意味着 web 开发人员可以将为 web 编写的代码轻松无缝地移植到 Windows 8。现在对沉浸式在线体验的投资可以在以后通过 Windows 商店获得实际利润。

事实上，只需很少的额外工作，我们就可以将这种 HTML5 体验移植到 Windows 8 Metro 风格的应用程序中。阅读这篇博文，了解**切断绳索**及其与[Windows 商店的整合。](http://blogs.msdn.com/b/windowsstore/archive/2011/12/06/announcing-the-new-windows-store.aspx)

我们很高兴看到开发人员今天可以用 HTML5 构建什么。你可以在[www.beautyoftheweb.com](http://www.beautyoftheweb.com/)下载 Internet Explorer 9 并找到其他漂亮的网站，或者在[dev.windows.com](http://dev.windows.com/)下载 Windows 8 的开发者预览版。

## 分享这篇文章