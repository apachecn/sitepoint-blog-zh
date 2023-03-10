# 从头开始编写 3D 软引擎:第 1 部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-scratch-part-1/>

我想和你们分享我是如何通过一系列教程学会构建所谓的“ **3D 软引擎**”的。“软件引擎”意味着我们**将只使用 CPU** 以一种古老的方式建造一个 3D 引擎(还记得你的 80386 上的 Doom 吗？).

我给大家分享一下代码的 **C#、TypeScript 和 JavaScript** 版本。在这个列表中，你应该找到你最喜欢的语言或者至少是你最喜欢的语言附近的东西。这个想法是为了帮助你在你喜欢的平台上转换下面的例子&概念。最后，您还可以下载 Visual Studio 2012 C#/TS/JS 解决方案。

那么为什么要建立一个 3D 软引擎呢？嗯，这只是因为它真的有助于理解现代 3D 如何与我们的 GPU 一起工作。事实上，我目前正在学习 3D 的基础知识，这要归功于微软内部由了不起的大卫·卡图赫举办的研讨会。他已经掌握 3D 很多年了，矩阵运算已经硬编码在他的大脑中。当我年轻的时候，我梦想着能够编写这样的引擎，但我觉得这对我来说太复杂了。最后，你会发现这并不复杂。你只是需要有人以简单的方式帮助你理解潜在的原则。

通过本系列，您将学习如何在 2D 屏幕上投影与一个点(一个顶点)相关的一些 3D 坐标(X，Y，Z ),如何在每个点之间画线，如何填充一些三角形，如何处理灯光、材质等等。这第一个教程将简单地向你展示如何显示与立方体相关的 8 个点，以及如何在虚拟的 3D 世界中移动它们。

本教程是以下系列的一部分:

1–编写相机、网格和设备对象的核心逻辑(本文)
2—[绘制线条和三角形以获得线框渲染](https://blogs.msdn.com/b/davrous/archive/2013/06/14/tutorial-part-2-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-ts-or-js-drawing-lines-amp-triangles.aspx)
3—[以 JSON 格式加载从 Blender 导出的网格](https://blogs.msdn.com/b/davrous/archive/2013/06/17/tutorial-part-3-learning-how-to-write-a-3d-soft-engine-in-c-ts-or-js-loading-meshes-exported-from-blender.aspx)
4—[使用光栅化填充三角形并使用 Z-Buffer](https://blogs.msdn.com/b/davrous/archive/2013/06/21/tutorial-part-4-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-rasterization-amp-z-buffering.aspx)
4b—[奖励:使用技巧&并行性来提高性能](https://blogs.msdn.com/b/davrous/archive/2013/06/25/tutorial-part-4-bonus-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-optimizing-amp-parallelism.aspx)
5—[使用平面阴影处理光线&gour](https://blogs.msdn.com/b/davrous/archive/2013/07/03/tutorial-part-5-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-flat-amp-gouraud-shading.aspx)

如果你遵循完整的系列，你将**知道如何建立你自己的 3D 软件引擎**！然后，您的引擎将开始进行一些线框渲染，然后光栅化，接着是 gouraud 着色，最后是应用纹理:

[![3dSoftEngineProgression](img/0f82b92d6ccd6cc412364a32ece76e1f.png "3dSoftEngineProgression")](https://david.blob.core.windows.net/softengine3d/part6sample2/index.html)

点击图像在另一个窗口打开最终的纹理渲染。

通过正确地遵循第一个教程，您将学习如何旋转立方体的 8 个点，以最终获得以下结果:

[https://david.blob.core.windows.net/softengine3d/part1/index.html](https://david.blob.core.windows.net/softengine3d/part1/index.html)

**<u>免责声明:</u>** 你们中的一些人可能想知道为什么我要构建这个 3D 软件引擎而不是使用 GPU。真的是为了教育目的。当然，如果你需要构建一个具有流畅 3D 动画的游戏，你将需要 DirectX 或 OpenGL/WebGL。但是一旦你理解了如何构建一个 3D 软引擎，更“复杂”的引擎就会更容易理解。为了更进一步，你绝对应该看看由大卫·卡图赫构建的 [BabylonJS WebGL 引擎](https://www.babylonjs.com/)。更多详情&教程在这里: [Babylon.js:用 HTML 5 和 WebGL 构建 3D 游戏的完整 JavaScript 框架](https://blogs.msdn.com/b/eternalcoding/archive/2013/06/27/babylon-js-a-complete-javascript-framework-for-building-3d-games-with-html-5-and-webgl.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2013/06/27/babylon-js-a-complete-javascript-framework-for-building-3d-games-with-html-5-and-webgl.aspx")

## 阅读先决条件

这些教程怎么写，我现在已经想了很久了。我最终决定不亲自解释每个必要的原则。网上有很多很好的资源，可以比我更好地解释这些重要的原则。但我还是花了相当多的时间浏览网页，让你选择，据我自己说，最好的一个来阅读:

–[世界、视图和投影矩阵亮相](https://robertokoci.com/world-view-projection-matrix-unveiled/ "https://robertokoci.com/world-view-projection-matrix-unveiled/")
–[教程 3:矩阵](https://www.opengl-tutorial.org/beginners-tutorials/tutorial-3-matrices/ "https://www.opengl-tutorial.org/beginners-tutorials/tutorial-3-matrices/")将向您介绍矩阵、模型、视图&投影矩阵。
–[OpenGL ES 2 . x 上的相机–模型视图投影矩阵](https://db-in.com/blog/2011/04/cameras-on-opengl-es-2-x/ "https://db-in.com/blog/2011/04/cameras-on-opengl-es-2-x/"):这个非常有趣，因为它解释了相机和镜头如何工作的故事。
–[Transforms(direct 3D 9)](https://msdn.microsoft.com/en-us/library/windows/desktop/bb206269(v=vs.85).aspx "https://msdn.microsoft.com/en-us/library/windows/desktop/bb206269(v=vs.85).aspx")
–[3D 简介](https://inear.se/talk/a_brief_introduction_to_3d.pptx):一个优秀的 PowerPoint 幻灯片卡座！至少读到第 27 页。之后就太挂钩一个和 GPU 对话的技术了(OpenGL 或者 DirectX)。
–[OpenGL 转换](https://www.songho.ca/opengl/gl_transform.html "https://www.songho.ca/opengl/gl_transform.html")

![OpenGL vertex transformation](img/52c9f930444432e1dd78804c7b86cdf4.png)

阅读这些文章时，不要关注相关的技术(如 OpenGL 或 DirectX ),也不要关注你可能在图中看到的三角形概念。我们稍后会看到这一点。

通过阅读这些文章，您真的需要理解有一系列的转换是这样完成的:

–我们从一个以自身为中心的 **3D 对象**
开始–然后通过矩阵
的平移、缩放或旋转操作将同一个对象**移动到虚拟 3D 世界**–一个**相机将查看这个位于 3D 世界
中的 3D 对象**–所有这些的最终**投影**将被完成到一个 **2D 空间**中，也就是你的屏幕

![Diagram of how world coordinates and local coordinates are related](img/4eb1b284ac8b87cf327d275129eb49fa.png "Diagram of how world coordinates and local coordinates are related") ![OpenGL Perspective Frustum and NDC](img/f73307e5a899117feb44911bc47167a7.png)

所有这些魔术都是通过矩阵运算累积变换来完成的。在浏览这些教程之前，你至少应该对这些概念有点熟悉。即使你第一次读的时候没有理解所有的内容。你应该先看看。在编写自己版本的 3D 软引擎时，您可能会回头阅读这些文章。这是完全正常的，不用担心！；)学习 3D 最好的方法如果通过实验和做错。

我们也不会花时间在矩阵运算的工作原理上。好消息是你不需要真正理解矩阵。简单地把它看作一个为你做正确操作的黑匣子。我不是矩阵大师，但我设法自己编写了一个 3D 软引擎。所以你这样做也应该成功。

然后，我们将使用为我们完成这项工作的库: **SharpDX** ，DirectX 之上的一个托管包装器，供 C#开发人员使用，以及由 David Catuhe 为 JavaScript 开发人员编写的 **babylon.math.js** 。我也用打字稿重写了。

## 软件先决条件

我们将用 C#编写一个 WinRT/ **XAML Windows 商店应用程序**和/或用 TypeScript/JavaScript 编写一个 **HTML5 应用程序**。因此，如果您想按原样使用 C#示例，您需要安装:

1–Windows 8
2–适用于 Windows 应用商店应用的 Visual Studio 2012 学习版。可以免费下载:[https://msdn.microsoft.com/en-US/windows/apps/br211386](https://msdn.microsoft.com/en-US/windows/apps/br211386 "https://msdn.microsoft.com/en-US/windows/apps/br211386")

如果你选择使用 **TypeScript** 示例，你需要从【https://www.typescriptlang.org/#Download】的[安装。所有示例都已用 TypeScript 0.9 成功更新和测试。](https://www.typescriptlang.org/#Download "https://www.typescriptlang.org/#Download")

您将找到 Visual Studio 2012 的插件，但还有其他可用选项: [Sublime Text，Vi，Emacs: TypeScript enabled！](https://msopentech.com/blog/2012/10/01/sublime-text-vi-emacs-typescript-enabled "https://msopentech.com/blog/2012/10/01/sublime-text-vi-emacs-typescript-enabled")在我这边，通过把我的代码的 C#版本移植到 TypeScript，我学会了 TypeScript。如果你也对学习 TypeScript 感兴趣，这个网络广播是一个很好的介绍:[安德斯·海尔斯伯格:TypeScript 简介](https://channel9.msdn.com/posts/Anders-Hejlsberg-Introducing-TypeScript "https://channel9.msdn.com/posts/Anders-Hejlsberg-Introducing-TypeScript")。请同时安装 [Web Essentials 2012](https://visualstudiogallery.msdn.microsoft.com/07d54d12-7133-4e15-becb-6f451ea3bea6 "https://visualstudiogallery.msdn.microsoft.com/07d54d12-7133-4e15-becb-6f451ea3bea6") ，它完全支持打字稿预览和编译。

如果选择 **JavaScript** ，只需要自己喜欢的 IDE 和兼容 HTML5 的浏览器即可。:)

请针对您想要使用的语言创建一个名为“ ***”的项目。如果是 **C#** ，在您的解决方案上使用 NuGet 添加“ **SharpDX 核心程序集**”:***

[![image](img/a883c9933e22e4ba76ff4caac53a38c6.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3108.image_5F00_13610CD0.png)

如果是**打字稿**，下载**[**Babylon . math . ts**](https://david.blob.core.windows.net/softengine3d/babylon.math.ts)。如果是 **JavaScript** 下载[**Babylon . math . js**](https://david.blob.core.windows.net/softengine3d/babylon.math.js)。在这两种情况下都添加对这些文件的引用。**

 **## 后台缓冲和渲染循环

在 3D 引擎中，我们在每一帧中渲染完整的场景，希望保持最佳的每秒 60 帧(FPS)以保持流畅的动画。为了完成渲染工作，我们需要一个我们称之为后台缓冲区的东西。这可以被视为映射屏幕/窗口大小的二维数组。阵列的每个单元都映射到屏幕上的一个像素。

在我们的 **XAML** Windows 商店应用中，我们将使用一个<font color="#0000ff">字节</font> []数组作为我们的动态**后台缓冲区**。对于在动画循环(tick)中渲染的每一帧，该缓冲区将被影响到一个<font color="#2b91af">可写位图</font>，该位图充当将被称为**前端缓冲区**的 XAML 图像控件的源。对于渲染循环，我们将要求 XAML 渲染引擎为它将生成的每一帧调用我们。这一行代码完成了注册:

```
CompositionTarget.Rendering += CompositionTarget_Rendering;
```

在 **HTML5** 中，我们当然要使用 **<画布/ >** 元素。canvas 元素已经有一个与之关联的后台缓冲区数据数组。可以通过 ***getImageData()*** 和 ***setImageData()*** 函数访问。动画循环将由*[requestAnimationFrame()](https://msdn.microsoft.com/library/ie/hh920765(v=vs.85).aspx)*函数处理。这比一个等效的 *setTimeout(function() {]，1000/60)* 要有效得多，因为它是由浏览器本机处理的，只有当代码准备好绘制时，浏览器才会回调我们的代码。

**<u>注意:</u>** 在这两种情况下，你都可以以不同的分辨率渲染帧，即最终窗口的实际宽度&高度。例如，您可以有一个 640×480 像素的后台缓冲区，而最终的显示屏(前台缓冲区)将是 1920×1080。在 XAML，由于 HTML5 中的 CSS，你将从“*硬件扩展*中受益。XAML 和浏览器的渲染引擎甚至通过使用反走样算法将后台缓冲数据延伸到前台缓冲窗口。在这两种情况下，这个任务都是由 GPU 完成的。这就是为什么我们称之为“硬件扩展”(硬件就是 GPU)。你可以在这里阅读 HTML5 中关于这个主题的更多内容:[释放 HTML 5 游戏画布的力量](https://blogs.msdn.com/b/eternalcoding/archive/2012/03/22/unleash-the-power-of-html-5-canvas-for-gaming-part-1.aspx?Redirected=true "https://blogs.msdn.com/b/eternalcoding/archive/2012/03/22/unleash-the-power-of-html-5-canvas-for-gaming-part-1.aspx?Redirected=true")。这种方法经常在游戏中使用，例如，提高性能，因为你需要处理的像素更少。

## 相机和网格对象

开始编码吧。首先，我们需要定义一些对象来嵌入相机和网格所需的细节。网格是描述 3D 对象的一个很酷的名字。

我们的*相机*将有两个属性:它在 3D 世界中的位置和它正在看的目标。两者都由名为 Vector3 的 3D 坐标组成。C#将使用 **SharpDX。Vector3** 和 TypeScript & JavaScript 将使用 **BABYLON。矢量 3** 。

我们的*网格*将有一个顶点集合(几个顶点或 3D 点),这些顶点将用于构建我们的 3D 对象，它在 3D 世界中的位置和它的旋转状态。为了识别它，它还会有一个名字。

为了恢复，我们需要下面的代码:

*   [C#](#one)
*   [打字稿](#two)
*   [JavaScript](#three)

```
// Camera.cs & Mesh.cs using SharpDX; namespace SoftEngine public class Camera { public Vector3 Position { get; set; } public Vector3 Target { get; set; }
   } public class Mesh { public string Name { get; set; } public Vector3[] Vertices { get; private set; } public Vector3 Position { get; set; } public Vector3 Rotation { get; set; } public Mesh(string name, int verticesCount)
       {
           Vertices = new Vector3[verticesCount];
           Name = name;
       }
   } 
```

```
//<reference path="babylon.math.ts"/> module SoftEngine { export class Camera {
       Position: BABYLON.Vector3;
       Target: BABYLON.Vector3; constructor() { this.Position = BABYLON.Vector3.Zero(); this.Target = BABYLON.Vector3.Zero();
       }
   } export class Mesh {
       Position: BABYLON.Vector3;
       Rotation: BABYLON.Vector3;
       Vertices: BABYLON.Vector3[]; constructor(public name: string, verticesCount: number) { this.Vertices = new Array(verticesCount); this.Rotation = BABYLON.Vector3.Zero(); this.Position = BABYLON.Vector3.Zero();
       }
   } 
```

```
var SoftEngine; function (SoftEngine) { var Camera = (function () { function Camera() { this.Position = BABYLON.Vector3.Zero(); this.Target = BABYLON.Vector3.Zero();
       } return Camera;
   })();
   SoftEngine.Camera = Camera; var Mesh = (function () { function Mesh(name, verticesCount) { this.name = name; this.Vertices = new Array(verticesCount); this.Rotation = BABYLON.Vector3.Zero(); this.Position = BABYLON.Vector3.Zero();
       } return Mesh;
   })();
   SoftEngine.Mesh = Mesh;    
)(SoftEngine || (SoftEngine = {}));
```

例如，如果您想使用我们的网格对象来描述一个立方体，您需要创建与立方体的 8 个点相关联的 8 个顶点。以下是 Blender 中显示的立方体坐标:

[![image](img/57d42b34be72f3c50b046f60f8398c57.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/4540.image_5F00_2D374352.png)

一个左撇子的世界。还要记住，当你创建一个网格时，坐标系是从网格的中心开始的。所以，X=0，Y=0，Z=0 是立方体的中心。

这可以通过这种代码创建:

```
var mesh = new Mesh("Cube", 8);
esh.Vertices[0] = new Vector3(-1, 1, 1);
esh.Vertices[1] = new Vector3(1, 1, 1);
esh.Vertices[2] = new Vector3(-1, -1, 1);
esh.Vertices[3] = new Vector3(-1, -1, -1);
esh.Vertices[4] = new Vector3(-1, 1, -1);
esh.Vertices[5] = new Vector3(1, 1, -1);
esh.Vertices[6] = new Vector3(1, -1, 1);
esh.Vertices[7] = new Vector3(1, -1, -1);
```

## 最重要的部分:设备对象

现在我们有了基本的对象，并且知道了如何构建 3D 网格，我们需要最重要的部分:设备对象。它是我们 3D 引擎的核心。

在它的渲染功能中，我们将基于之前定义的摄像机构建视图矩阵和投影矩阵。

然后，我们将遍历每个可用的网格，根据它们当前的旋转和平移值来构建它们的关联世界矩阵。最后，一旦完成，要应用的最终转换矩阵是:

```
var transformMatrix = worldMatrix * viewMatrix * projectionMatrix;
```

这是您绝对需要通过阅读前面的先决条件资源来理解的概念。否则，您可能只是简单地复制/粘贴代码，而不了解其中的神奇之处。对于后续教程来说，这不是一个很大的问题，但是同样，最好知道你在编码什么。

使用这个变换矩阵，我们将在 2D 世界中投影每个网格的每个顶点，以从它们的 X，Y，Z 坐标获得 X，Y 坐标。为了最终在屏幕上绘制，我们添加了一个小的剪辑逻辑，通过 PutPixel 方法/函数只显示可见像素。

以下是设备对象的各种版本。我已经尝试对代码进行注释，以帮助您尽可能地理解它。

**<u>注意:</u>** 微软 Windows 使用 BGRA 色彩空间(蓝、绿、红、阿尔法)进行绘制，而 HTML5 画布使用 [RGBA](https://en.wikipedia.org/wiki/RGBA_color_space) (红、绿、蓝、阿尔法)色彩空间进行绘制。这就是为什么，你会注意到 C#和 HTML5 的代码有一些细微的差别。

*   [C#](#devicecsharp)
*   [打字稿](#devicetypescript)
*   [JavaScript](#devicejavascript)

```
using Windows.UI.Xaml.Media.Imaging; using System.Runtime.InteropServices.WindowsRuntime; using SharpDX; namespace SoftEngine public class Device { private byte[] backBuffer; private WriteableBitmap bmp; public Device(WriteableBitmap bmp)
       { this.bmp = bmp; // the back buffer size is equal to the number of pixels to draw
           // on screen (width*height) * 4 (R,G,B & Alpha values). backBuffer = new byte[bmp.PixelWidth * bmp.PixelHeight * 4];
       } // This method is called to clear the back buffer with a specific color public void Clear(byte r, byte g, byte b, byte a) { for (var index = 0; index < backBuffer.Length; index += 4)
           { // BGRA is used by Windows instead by RGBA in HTML5 backBuffer[index] = b;
               backBuffer[index + 1] = g;
               backBuffer[index + 2] = r;
               backBuffer[index + 3] = a;
           }
       } // Once everything is ready, we can flush the back buffer
       // into the front buffer. public void Present()
       { using (var stream = bmp.PixelBuffer.AsStream())
           { // writing our byte[] back buffer into our WriteableBitmap stream stream.Write(backBuffer, 0, backBuffer.Length);
           } // request a redraw of the entire bitmap bmp.Invalidate();
       } // Called to put a pixel on screen at a specific X,Y coordinates public void PutPixel(int x, int y, Color4 color)
       { // As we have a 1-D Array for our back buffer
           // we need to know the equivalent cell in 1-D based
           // on the 2D coordinates on screen var index = (x + y * bmp.PixelWidth) * 4;
            backBuffer[index] = (byte)(color.Blue * 255);
           backBuffer[index + 1] = (byte)(color.Green * 255);
           backBuffer[index + 2] = (byte)(color.Red * 255);
           backBuffer[index + 3] = (byte)(color.Alpha * 255);
       } // Project takes some 3D coordinates and transform them
       // in 2D coordinates using the transformation matrix public Vector2 Project(Vector3 coord, Matrix transMat)
       { // transforming the coordinates var point = Vector3.TransformCoordinate(coord, transMat); // The transformed coordinates will be based on coordinate system
           // starting on the center of the screen. But drawing on screen normally starts
           // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.X * bmp.PixelWidth + bmp.PixelWidth / 2.0f; var y = -point.Y * bmp.PixelHeight + bmp.PixelHeight / 2.0f; return (new Vector2(x, y));
       } // DrawPoint calls PutPixel but does the clipping operation before public void DrawPoint(Vector2 point)
       { // Clipping what's visible on screen if (point.X >= 0 && point.Y >= 0 && point.X < bmp.PixelWidth && point.Y < bmp.PixelHeight)
           { // Drawing a yellow point PutPixel((int)point.X, (int)point.Y, new Color4(1.0f, 1.0f, 0.0f, 1.0f));
           }
       } // The main method of the engine that re-compute each vertex projection
       // during each frame public void Render(Camera camera, params Mesh[] meshes)
       { // To understand this part, please read the prerequisites resources var viewMatrix = Matrix.LookAtLH(camera.Position, camera.Target, Vector3.UnitY); var projectionMatrix = Matrix.PerspectiveFovRH(0.78f, 
                                                          (float)bmp.PixelWidth / bmp.PixelHeight, 
                                                          0.01f, 1.0f); foreach (Mesh mesh in meshes) 
           { // Beware to apply rotation before translation var worldMatrix = Matrix.RotationYawPitchRoll(mesh.Rotation.Y, 
                                                              mesh.Rotation.X, mesh.Rotation.Z) * Matrix.Translation(mesh.Position); var transformMatrix = worldMatrix * viewMatrix * projectionMatrix; foreach (var vertex in mesh.Vertices)
               { // First, we project the 3D coordinates into the 2D space var point = Project(vertex, transformMatrix); // Then we can draw on screen DrawPoint(point);
               }
           }
       }
   } 
```

```
///<reference path="babylon.math.ts"/> module SoftEngine {
  export class Device { // the back buffer size is equal to the number of pixels to draw
       // on screen (width*height) * 4 (R,G,B & Alpha values). private backbuffer: ImageData; private workingCanvas: HTMLCanvasElement; private workingContext: CanvasRenderingContext2D; private workingWidth: number; private workingHeight: number; // equals to backbuffer.data private backbufferdata; constructor(canvas: HTMLCanvasElement) { this.workingCanvas = canvas; this.workingWidth = canvas.width; this.workingHeight = canvas.height; this.workingContext = this.workingCanvas.getContext("2d");
       } // This function is called to clear the back buffer with a specific color public clear(): void { // Clearing with black color by default this.workingContext.clearRect(0, 0, this.workingWidth, this.workingHeight); // once cleared with black pixels, we're getting back the associated image data to 
           // clear out back buffer this.backbuffer = this.workingContext.getImageData(0, 0, this.workingWidth, this.workingHeight);
       } // Once everything is ready, we can flush the back buffer
       // into the front buffer. public present(): void { this.workingContext.putImageData(this.backbuffer, 0, 0);
       } // Called to put a pixel on screen at a specific X,Y coordinates public putPixel(x: number, y: number, color: BABYLON.Color4): void { this.backbufferdata = this.backbuffer.data; // As we have a 1-D Array for our back buffer
           // we need to know the equivalent cell index in 1-D based
           // on the 2D coordinates of the screen var index: number = ((x >> 0) + (y >> 0) * this.workingWidth) * 4;

                    // RGBA color space is used by the HTML5 canvas this.backbufferdata[index] = color.r * 255; this.backbufferdata[index + 1] = color.g * 255; this.backbufferdata[index + 2] = color.b * 255; this.backbufferdata[index + 3] = color.a * 255;
       } // Project takes some 3D coordinates and transform them
       // in 2D coordinates using the transformation matrix public project(coord: BABYLON.Vector3, transMat: BABYLON.Matrix): BABYLON.Vector2 { // transforming the coordinates var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
           // starting on the center of the screen. But drawing on screen normally starts
           // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0 >> 0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0 >> 0; return (new BABYLON.Vector2(x, y));
       } // drawPoint calls putPixel but does the clipping operation before public drawPoint(point: BABYLON.Vector2): void { // Clipping what's visible on screen if (point.x >= 0 && point.y >= 0 && point.x < this.workingWidth 
                                             && point.y < this.workingHeight) { // Drawing a yellow point this.putPixel(point.x, point.y, new BABYLON.Color4(1, 1, 0, 1));
           }
       } // The main method of the engine that re-compute each vertex projection
       // during each frame public render(camera: Camera, meshes: Mesh[]): void { // To understand this part, please read the prerequisites resources var viewMatrix = BABYLON.Matrix.LookAtLH(camera.Position, camera.Target, BABYLON.Vector3.Up()); var projectionMatrix = BABYLON.Matrix.PerspectiveFovLH(0.78, this.workingWidth / this.workingHeight, 0.01, 1.0); for (var index = 0; index < meshes.length; index++) { // current mesh to work on var cMesh = meshes[index]; // Beware to apply rotation before translation var worldMatrix = BABYLON.Matrix.RotationYawPitchRoll(
                       cMesh.Rotation.y, cMesh.Rotation.x, cMesh.Rotation.z)
                       .multiply(BABYLON.Matrix.Translation(
                           cMesh.Position.x, cMesh.Position.y, cMesh.Position.z)); var transformMatrix = worldMatrix.multiply(viewMatrix).multiply(projectionMatrix); for (var indexVertices = 0; indexVertices < cMesh.Vertices.length; indexVertices++) { // First, we project the 3D coordinates into the 2D space var projectedPoint = this.project(cMesh.Vertices[indexVertices], transformMatrix); // Then we can draw on screen this.drawPoint(projectedPoint);
               }
           }
       }
   } 
```

```
var SoftEngine; function (SoftEngine) { var Device = (function () { function Device(canvas) { // Note: the back buffer size is equal to the number of pixels to draw
           // on screen (width*height) * 4 (R,G,B & Alpha values). this.workingCanvas = canvas; this.workingWidth = canvas.width; this.workingHeight = canvas.height; this.workingContext = this.workingCanvas.getContext("2d");
       } // This function is called to clear the back buffer with a specific color Device.prototype.clear = function () { // Clearing with black color by default this.workingContext.clearRect(0, 0, this.workingWidth, this.workingHeight); // once cleared with black pixels, we're getting back the associated image data to 
           // clear out back buffer this.backbuffer = this.workingContext.getImageData(0, 0, this.workingWidth, this.workingHeight);
       }; // Once everything is ready, we can flush the back buffer
       // into the front buffer. Device.prototype.present = function () { this.workingContext.putImageData(this.backbuffer, 0, 0);
       }; // Called to put a pixel on screen at a specific X,Y coordinates Device.prototype.putPixel = function (x, y, color) { this.backbufferdata = this.backbuffer.data; // As we have a 1-D Array for our back buffer
           // we need to know the equivalent cell index in 1-D based
           // on the 2D coordinates of the screen var index = ((x >> 0) + (y >> 0) * this.workingWidth) * 4;

                      // RGBA color space is used by the HTML5 canvas this.backbufferdata[index] = color.r * 255; this.backbufferdata[index + 1] = color.g * 255; this.backbufferdata[index + 2] = color.b * 255; this.backbufferdata[index + 3] = color.a * 255;
       }; // Project takes some 3D coordinates and transform them
       // in 2D coordinates using the transformation matrix Device.prototype.project = function (coord, transMat) { var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
           // starting on the center of the screen. But drawing on screen normally starts
           // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0 >> 0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0 >> 0; return (new BABYLON.Vector2(x, y));
       }; // drawPoint calls putPixel but does the clipping operation before Device.prototype.drawPoint = function (point) { // Clipping what's visible on screen if (point.x >= 0 && point.y >= 0 && point.x < this.workingWidth 
                                             && point.y < this.workingHeight) { // Drawing a yellow point this.putPixel(point.x, point.y, new BABYLON.Color4(1, 1, 0, 1));
           }
       }; // The main method of the engine that re-compute each vertex projection
       // during each frame Device.prototype.render = function (camera, meshes) { // To understand this part, please read the prerequisites resources var viewMatrix = BABYLON.Matrix.LookAtLH(camera.Position, camera.Target, BABYLON.Vector3.Up()); var projectionMatrix = BABYLON.Matrix.PerspectiveFovLH(0.78, this.workingWidth / this.workingHeight, 0.01, 1.0); for (var index = 0; index < meshes.length; index++) { // current mesh to work on var cMesh = meshes[index]; // Beware to apply rotation before translation var worldMatrix = BABYLON.Matrix.RotationYawPitchRoll(
                   cMesh.Rotation.y, cMesh.Rotation.x, cMesh.Rotation.z)
                    .multiply(BABYLON.Matrix.Translation(
                      cMesh.Position.x, cMesh.Position.y, cMesh.Position.z)); var transformMatrix = worldMatrix.multiply(viewMatrix).multiply(projectionMatrix); for (var indexVertices = 0; indexVertices < cMesh.Vertices.length; indexVertices++) { // First, we project the 3D coordinates into the 2D space var projectedPoint = this.project(cMesh.Vertices[indexVertices], transformMatrix); // Then we can draw on screen this.drawPoint(projectedPoint);
               }
           }
       }; return Device;
   })();
   SoftEngine.Device = Device;    
)(SoftEngine || (SoftEngine = {}));
```

## 把所有的放在一起

我们最后需要创建一个网格(我们的立方体)，创建一个相机和目标我们的网格&实例化我们的设备对象。

一旦完成，我们将启动动画/渲染循环。在最佳情况下，该循环将每 16ms (60 FPS)调用一次。在每个节拍期间(调用注册到渲染循环的处理程序)，我们每次都将启动以下逻辑:

1—**清除屏幕**和所有相关的黑色像素( *Clear()* 功能)

2-**更新我们网格的各种位置&旋转值**

3-**通过执行所需的矩阵操作( *Render()* 函数)，将它们**渲染到后台缓冲区中

4—**通过将后台缓冲区数据刷新到前台缓冲区，在屏幕上显示**(*Present()*功能)

*   [C#](#maincsharp)
*   [打字稿](#maintypescript)
*   [JavaScript](#mainjavascript)

```
private Device device; Mesh mesh = new Mesh("Cube", 8); Camera mera = new Camera(); private void Page_Loaded(object sender, RoutedEventArgs e) // Choose the back buffer resolution here WriteableBitmap bmp = new WriteableBitmap(640, 480);
    device = new Device(bmp); // Our XAML Image control frontBuffer.Source = bmp;
    mesh.Vertices[0] = new Vector3(-1, 1, 1);
   mesh.Vertices[1] = new Vector3(1, 1, 1);
   mesh.Vertices[2] = new Vector3(-1, -1, 1);
   mesh.Vertices[3] = new Vector3(-1, -1, -1);
   mesh.Vertices[4] = new Vector3(-1, 1, -1);
   mesh.Vertices[5] = new Vector3(1, 1, -1);
   mesh.Vertices[6] = new Vector3(1, -1, 1);
   mesh.Vertices[7] = new Vector3(1, -1, -1);
    mera.Position = new Vector3(0, 0, 10.0f);
   mera.Target = Vector3.Zero; // Registering to the XAML rendering loop CompositionTarget.Rendering += CompositionTarget_Rendering; // Rendering loop handler void CompositionTarget_Rendering(object sender, object e)
   device.Clear(0, 0, 0, 255); // rotating slightly the cube during each frame rendered mesh.Rotation = new Vector3(mesh.Rotation.X + 0.01f, mesh.Rotation.Y + 0.01f, mesh.Rotation.Z); // Doing the various matrix operations device.Render(mera, mesh); // Flushing the back buffer into the front buffer device.Present(); 
```

```
///<reference path="SoftEngine.ts"/> var canvas: HTMLCanvasElement; var device: SoftEngine.Device; var mesh: SoftEngine.Mesh; var meshes: SoftEngine.Mesh[] = []; var mera: SoftEngine.Camera;
document.addEventListener("DOMContentLoaded", init, false); function init() {
   canvas = <HTMLCanvasElement> document.getElementById("frontBuffer");
   mesh = new SoftEngine.Mesh("Cube", 8);
   meshes.push(mesh);
   mera = new SoftEngine.Camera();
   device = new SoftEngine.Device(canvas);
    mesh.Vertices[0] = new BABYLON.Vector3(-1, 1, 1);
   mesh.Vertices[1] = new BABYLON.Vector3(1, 1, 1);
   mesh.Vertices[2] = new BABYLON.Vector3(-1, -1, 1);
   mesh.Vertices[3] = new BABYLON.Vector3(-1, -1, -1);
   mesh.Vertices[4] = new BABYLON.Vector3(-1, 1, -1);
   mesh.Vertices[5] = new BABYLON.Vector3(1, 1, -1);
   mesh.Vertices[6] = new BABYLON.Vector3(1, -1, 1);
   mesh.Vertices[7] = new BABYLON.Vector3(1, -1, -1);
    mera.Position = new BABYLON.Vector3(0, 0, 10);
   mera.Target = new BABYLON.Vector3(0, 0, 0); // Calling the HTML5 rendering loop requestAnimationFrame(drawingLoop); // Rendering loop handler function drawingLoop() {
   device.clear(); // rotating slightly the cube during each frame rendered mesh.Rotation.x += 0.01;
   mesh.Rotation.y += 0.01; // Doing the various matrix operations device.render(mera, meshes); // Flushing the back buffer into the front buffer device.present(); // Calling the HTML5 rendering loop recursively requestAnimationFrame(drawingLoop); 
```

```
var canvas; var device; var mesh; var meshes = []; var mera;
document.addEventListener("DOMContentLoaded", init, false); function init() {
   canvas = document.getElementById("frontBuffer");
   mesh = new SoftEngine.Mesh("Cube", 8);
   meshes.push(mesh);
   mera = new SoftEngine.Camera();
   device = new SoftEngine.Device(canvas);
    mesh.Vertices[0] = new BABYLON.Vector3(-1, 1, 1);
   mesh.Vertices[1] = new BABYLON.Vector3(1, 1, 1);
   mesh.Vertices[2] = new BABYLON.Vector3(-1, -1, 1);
   mesh.Vertices[3] = new BABYLON.Vector3(-1, -1, -1);
   mesh.Vertices[4] = new BABYLON.Vector3(-1, 1, -1);
   mesh.Vertices[5] = new BABYLON.Vector3(1, 1, -1);
   mesh.Vertices[6] = new BABYLON.Vector3(1, -1, 1);
   mesh.Vertices[7] = new BABYLON.Vector3(1, -1, -1);
    mera.Position = new BABYLON.Vector3(0, 0, 10);
   mera.Target = new BABYLON.Vector3(0, 0, 0); // Calling the HTML5 rendering loop requestAnimationFrame(drawingLoop); // Rendering loop handler function drawingLoop() {
   device.clear(); // rotating slightly the cube during each frame rendered mesh.Rotation.x += 0.01;
   mesh.Rotation.y += 0.01; // Doing the various matrix operations device.render(mera, meshes); // Flushing the back buffer into the front buffer device.present(); // Calling the HTML5 rendering loop recursively requestAnimationFrame(drawingLoop); 
```

如果您已经设法正确地遵循了第一个教程，您应该会获得如下内容:

[https://david.blob.core.windows.net/softengine3d/part1/index.html](https://david.blob.core.windows.net/softengine3d/part1/index.html)

如果没有，**下载包含源代码的解决方案**:

–**c#**:[softenginecsharppart 1 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart1.zip "https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart1.zip")

–**TypeScript**:[softenginetspart 1 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart1.zip "https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart1.zip")

–**JavaScript**:[softenginejspart 1 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart1.zip "https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart1.zip")或者直接在内嵌的 iframe 上右键单击->查看源代码

简单地回顾一下代码，试着找出你的问题所在。:)

在下一个教程中，我们将**学习如何在每个顶点** &之间绘制线条面的概念/ **三角形**以获得类似这样的东西:

[![image](img/51496504cdf9e0eb3b91863941b10dc9.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/8228.image_5F00_445B9E1D.png)

本系列的第二部分再见。

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/06/13/tutorial-series-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-typescript-or-JavaScript . aspx](https://blogs.msdn.com/b/davrous/archive/2013/06/13/tutorial-series-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-typescript-or-javascript.aspx)。经作者许可，在此转载。*

## 分享这篇文章**