# 重新想象飞行模拟器:过去和现在

> 原文：<https://www.sitepoint.com/reimagining-flight-simulator-then-and-now/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

第一版飞行模拟器于 1980 年在 Apple II 上发布，令人惊讶的是，它是 3D 的！这是一项了不起的成就。当你考虑到所有的 3D 都是手工完成的，是细致的计算和低级像素命令的结果，这就更令人惊讶了。当 Bruce Atwick 解决早期版本的飞行模拟器时，不仅没有 3D 框架，而且根本没有框架！这些版本的游戏大部分是用汇编语言编写的，离流经 CPU 的 1 和 0 只有一步之遥。

当我们着手为网络重新设计[飞行模拟器](http://flightarcade.com/?WT.mc_id=13406-DEV-sitepoint-article30)(或者我们称之为 Flight Arcade)并展示新的[微软 Edge 浏览器和 EdgeHTML 渲染引擎](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article30&utm_campaign=SitePoint)的可能性时，我们不禁想到了当时和现在创建 3D 的对比——旧的飞行 Sim、新的飞行 Sim、旧的 Internet Explorer、新的微软 Edge。当我们用像 [Babylon.js](http://babylonjs.com?WT.mc_id=13406-DEV-sitepoint-article30) 这样的伟大框架在 WebGL 中塑造 3D 世界时，现代编码看起来几乎是奢侈的。它让我们专注于非常高层次的问题。在这篇文章中，我们将分享我们的方法来应对这些有趣的挑战之一:一种简单的方法来创建逼真的大规模地形。

[https://www.youtube.com/embed/xyaq9TPmXrA](https://www.youtube.com/embed/xyaq9TPmXrA)

注意:本文的交互式代码和示例也位于:[http://www.flightarcade.com/learn/](http://www.flightarcade.com/learn/?WT.mc_id=13406-DEV-sitepoint-article30)

## 建模和 3D 地形

大多数 3D 对象都是用建模工具创建的，这是有道理的。用代码很难创建复杂的对象(比如一架飞机甚至一座建筑)。建模工具几乎总是有意义的，但也有例外！其中之一可能是像飞行拱廊岛起伏的山丘。我们最终使用了一种我们发现更简单甚至可能更直观的技术:高度图。

高度图是一种使用常规二维图像来描述岛屿或其他地形等表面的高程起伏的方法。这是一种非常常见的处理高程数据的方式，不仅在游戏中如此，在制图师和地质学家使用的地理信息系统(GIS)中也是如此。

为了帮助你了解这是如何工作的，看看下面的互动高度图。尝试在图像编辑器中绘图，然后检查生成的地形。

![Image1-interactive-heightmap-demo](img/71fd39697fddbd63225b058dfcfefddc.png)

尝试这里的互动演示。

高度图背后的概念非常简单。在上图这样的图像中，纯黑是“地板”，纯白是最高峰。中间的灰度颜色代表相应的高程。这给了我们 256 层高度，这对于我们的游戏来说是足够的细节。现实生活中的应用程序可能会使用全色谱来存储更多层次的细节(如果包含 alpha 通道，则 256 <sup>4</sup> = 4，294，967，296 个层次的细节)。

与传统的多边形网格相比，高度贴图有几个优点:

首先，高度图要紧凑得多。只有最重要的数据(高程)被存储。它需要通过编程变成一个 3D 物体，但这是典型的交易:你现在节省空间，以后再用计算来支付。通过将数据存储为图像，您可以获得另一个空间优势:您可以利用标准的图像压缩技术，使数据变得很小(相比之下)！

其次，高度图是一种生成、可视化和编辑地形的便捷方式。当你看到一个时，它是非常直观的。感觉有点像看地图。这被证明是特别有用的飞行街机。我们用 Photoshop 设计和编辑了我们的岛！这使得根据需要进行小的调整变得非常简单。例如，当我们想确保跑道完全平坦时，我们只需确保用单一颜色覆盖该区域。

你可以在下面看到飞行走廊的高度图。看看你能否找到我们为跑道和村庄创建的“平坦”区域。

![image2-heightmap-for-the-flight-arcade-island](img/1a8dbe4cd1df929af9a7cee510a625d6.png)

飞行拱廊岛的高度图。它是用 Photoshop 创作的，取材于太平洋一个著名岛链中的“大岛”。有什么猜测吗？

![image3-texture-mapping-3d-results](img/c17113ece149365af2b3364dc63eba8e.png)

*一种纹理，在高度映射解码后映射到最终的 3D 网格上。更多信息请见下文。*

## 解码高度图

我们用 [Babylon.js](http://www.babylonjs.com/?WT.mc_id=13406-DEV-sitepoint-article30) 建立了飞行街机，Babylon 给了我们一条从 heightmap 到 3D 的非常直接的路径。Babylon 提供了一个从高度图图像生成网格几何图形的 API:

![image4-code-to-create-heightmap](img/5ebb3fc9e0ece90f12833e9c3b9b325b.png)

细节的数量由该细分的属性决定。需要注意的是，该参数指的是 heighmap 图像每一侧的细分数，而不是像元总数。因此，稍微增加这个数字会对网格中的顶点总数产生很大的影响。

| 20 个分支 | = | 400 个细胞 |
| 50 个分支 | = | 2500 个细胞 |
| 100 个细分 | = | 10，000 个细胞 |
| 500 个分支 | = | 25 万个细胞 |
| 1000 个细分 | = | 一百万个细胞 |

在下一节中，我们将学习如何对地面进行纹理处理，但是当尝试创建高度图时，查看线框是很有用的。下面是应用简单线框纹理的代码，因此很容易看到高度图数据是如何转换为网格顶点的:

![image5-simple-wireframe-material](img/0f1a45c871b14425cd2c05eb74c66cac.png)

## 创建纹理细节

一旦我们有了一个模型，贴图就相对简单了。对于 Flight Arcade，我们简单地创建了一个非常大的图像来匹配我们高度图中的岛屿。图像在地形的轮廓上被拉伸，因此纹理和高度图保持相关。这很容易想象，所有的制作工作都是在 Photoshop 中完成的。

原始纹理图像以 4096×4096 的分辨率创建。那是相当大的！(为了保持合理的下载，我们最终将大小减少到 2048×2048，但所有的开发都是用全尺寸图像完成的)。这是原始纹理的全像素样本。

![image6-full-pixel-sample-of-arcade-island-image](img/284c89fc4561585cd8f22408c3d7746d.png)

原始岛屿纹理的全像素样本。整个小镇只有 300px 左右的广场。

那些长方形代表岛上城镇的建筑。我们很快注意到，在地形和其他 3D 模型之间，我们可以实现的纹理细节水平存在差异。即使我们有巨大的岛屿纹理，差异也是非常明显的！

为了解决这个问题，我们将额外的细节以随机噪声的形式“混合”到地形纹理中。下面可以看到前后的。请注意额外的噪波如何增强地形的细节外观。

![image7-additional-detail-with-blend](img/ca8fac9f2ae03b6213140cf89fe5dc28.png)

我们创建了一个自定义着色器来添加噪声。着色器可以让您对 WebGL 3D 场景的渲染进行难以置信的控制，这是着色器如何有用的一个很好的例子。

WebGL 着色器由两个主要部分组成:顶点着色器和片段着色器。顶点着色器的主要目标是将顶点映射到渲染帧中的某个位置。片段(或像素)着色器控制像素的结果颜色。

着色器是用一种称为 GLSL(图形库着色器语言)的高级语言编写的，这种语言类似于 c。该代码在 GPU 上执行。要深入了解着色器如何工作，请参见[本教程](http://blogs.msdn.com/b/eternalcoding/archive/2014/04/17/learning-shaders-create-your-own-shaders-with-babylon-js.aspx?WT.mc_id=13406-DEV-sitepoint-article30)如何为 Babylon.js 创建自己的自定义着色器

### 顶点着色器

我们没有改变纹理映射到地面网格的方式，所以我们的顶点着色器非常简单。它只是计算标准映射并指定目标位置。

![image8-the-vertetdx-shader](img/69a9702736f1909c073c9d31411a34f0.png)

### 片段着色器

我们的片段着色器稍微复杂一些。它结合了两个不同的图像:基础图像和混合图像。基本图像被映射到整个地面网格。在飞行游戏中，这是岛屿的彩色图像。混合图像是小噪声图像，用于在近距离给地面一些纹理和细节。着色器组合来自每个图像的值，以创建整个岛的组合纹理。

飞行街机的最后一课发生在一个雾天，所以我们的像素着色器的另一个任务是调整颜色来模拟雾。这种调整是基于顶点离相机有多远，距离远的像素被雾“模糊”得更严重。您将在主着色器代码上方的 calcFogFactor 函数中看到这个距离计算。

![image9-calcFogFactor-function](img/cf23906ae243d2efc922adaaf1984749.png)

我们自定义混合着色器的最后一部分是 Babylon 使用的 JavaScript 代码。这段代码的主要目的是准备传递给顶点和像素着色器的参数。

![image10-prepare-parameters-for-shader](img/f465a1442c064985f729d15549a8545c.png)

![image11-blend-material-prototype](img/5c2d6135613c6a184f452ab5e1dde2de.png)

Babylon.js 使创建自定义的基于着色器的材质变得很容易。我们的混合材料相对简单，但当飞机低飞到地面时，它确实使岛屿的外观发生了很大的变化。着色器为浏览器带来了 GPU 的强大功能，扩展了可应用于 3D 场景的创意效果类型。在我们的例子中，这是最后一笔！

## JavaScript 的更多实践

微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是用 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13406-DEV-sitepoint-article30) 创造更多。以下是一些要检查的:

*   【2015 年微软 Edge 网络峰会(关于新浏览器、新网络平台功能和社区演讲嘉宾的完整系列)
*   //BUILD/和 Windows 10 的构建(包括网站和应用的新 JavaScript 引擎)
*   [在不破坏网络的情况下推进 JavaScript](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=13406-DEV-sitepoint-article30)(Christian heil Mann 最近的主题演讲)
*   [托管网络应用和网络平台创新](http://channel9.msdn.com/Events/Build/2015/2-665?WT.mc_id=13406-DEV-sitepoint-article30)(深入探讨流形等主题。JS)
*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13406-DEV-sitepoint-article30)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)

以及一些免费的入门工具: [Visual Studio Code](https://code.visualstudio.com/?WT.mc_id=13406-DEV-sitepoint-article30) 、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13406-DEV-sitepoint-article30)和[跨浏览器测试工具](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article30&utm_campaign=SitePoint)——都适用于 Mac、Linux 或 Windows。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享[微软 Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13406-DEV-sitepoint-article30) 和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=13406-DEV-sitepoint-article30)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article30&utm_campaign=SitePoint)。

## 分享这篇文章