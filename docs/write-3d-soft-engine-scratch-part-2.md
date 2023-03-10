# 从头开始编写 3D 软引擎:第 2 部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-scratch-part-2/>

由于之前的教程[第 1 部分](https://www.sitepoint.com/write-3d-soft-engine-scratch-part-1/ "Write a 3D Soft Engine from Scratch: Part 1")，现在我们已经构建了 3D 引擎的核心，我们可以开始增强渲染了。下一步是将这些点连接起来，画出一些线条，来渲染你可能知道的**“线框”渲染**。

1—[编写相机的核心逻辑，网格&设备对象](https://blogs.msdn.com/b/davrous/archive/2013/06/13/tutorial-series-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-typescript-or-javascript.aspx)
2—绘制线条和三角形以获得线框渲染(本文)
3—[加载从 Blender 导出的 JSON 格式的网格](https://blogs.msdn.com/b/davrous/archive/2013/06/17/tutorial-part-3-learning-how-to-write-a-3d-soft-engine-in-c-ts-or-js-loading-meshes-exported-from-blender.aspx)
4—[使用光栅化填充三角形并使用 Z 缓冲区](https://blogs.msdn.com/b/davrous/archive/2013/06/21/tutorial-part-4-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-rasterization-amp-z-buffering.aspx)
4b—[额外好处:使用技巧&并行性来提升性能](https://blogs.msdn.com/b/davrous/archive/2013/06/25/tutorial-part-4-bonus-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-optimizing-amp-parallelism.aspx)
5—[使用平面阴影处理光线【t2t](https://blogs.msdn.com/b/davrous/archive/2013/07/03/tutorial-part-5-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-flat-amp-gouraud-shading.aspx)

在本教程中，您将学习如何画线，什么是面，以及 Bresenham 算法绘制一些三角形有多酷。

由于这一点，最终，你将知道如何编写如此酷的代码:

[https://david.blob.core.windows.net/softengine3d/part2sample3/index.html](https://david.blob.core.windows.net/softengine3d/part2sample3/index.html)

是啊！我们的 3D 旋转立方体开始真正出现在我们的屏幕上！

## 在两点之间画线的第一个基本算法

让我们从编写一个简单的算法开始。为了**在两个顶点**之间画一条线，我们将使用以下逻辑:

–如果两个点(点 0 和点 1)之间的距离小于 2 个像素，则无需采取任何措施
–否则，我们将寻找两个点之间的**中点**(点 0 坐标+(点 1 坐标–点 0 坐标)/2)
–我们将在屏幕上绘制该点
–我们将在点 0 &中点和中点&点 1 之间递归启动此**算法**

下面是实现这一点的代码:

*   [C#](#drawlinecsharp)
*   [打字稿](#drawlinets)
*   [JavaScript](#drawlinejs)

```
public void DrawLine(Vector2 point0, Vector2 point1) var dist = (point1 - point0).Length(); // If the distance between the 2 points is less than 2 pixels
   // We're exiting if (dist < 2) return; // Find the middle point between first & second point Vector2 middlePoint = point0 + (point1 - point0)/2; // We draw this point on screen DrawPoint(middlePoint); // Recursive algorithm launched between first & middle point
   // and between middle & second point DrawLine(point0, middlePoint);
   DrawLine(middlePoint, point1); 
```

```
public drawLine(point0: BABYLON.Vector2, point1: BABYLON.Vector2): void { var dist = point1.subtract(point0).length(); // If the distance between the 2 points is less than 2 pixels
   // We're exiting if (dist < 2) return; // Find the middle point between first & second point var middlePoint = point0.add((point1.subtract(point0)).scale(0.5)); // We draw this point on screen this.drawPoint(middlePoint); // Recursive algorithm launched between first & middle point
   // and between middle & second point this.drawLine(point0, middlePoint); this.drawLine(middlePoint, point1); 
```



```
Device.prototype.drawLine = function (point0, point1) { var dist = point1.subtract(point0).length(); // If the distance between the 2 points is less than 2 pixels
   // We're exiting if(dist < 2) { return;
   } // Find the middle point between first & second point var middlePoint = point0.add((point1.subtract(point0)).scale(0.5)); // We draw this point on screen this.drawPoint(middlePoint); // Recursive algorithm launched between first & middle point
   // and between middle & second point this.drawLine(point0, middlePoint); this.drawLine(middlePoint, point1);
;
```



您需要更新渲染循环以使用这段新代码:

*   [C#](#newforcsharp)
*   [打字稿](#newforts)
*   [JavaScript](#newforjs)

```
for (var i = 0; i < mesh.Vertices.Length - 1; i++) var point0 = Project(mesh.Vertices[i], transformMatrix); var point1 = Project(mesh.Vertices[i + 1], transformMatrix);
   DrawLine(point0, point1); 
```

```
for (var i = 0; i < cMesh.Vertices.length -1; i++){ var point0 = this.project(cMesh.Vertices[i], transformMatrix); var point1 = this.project(cMesh.Vertices[i + 1], transformMatrix); this.drawLine(point0, point1); 
```

```
for (var i = 0; i < cMesh.Vertices.length -1; i++){ var point0 = this.project(cMesh.Vertices[i], transformMatrix); var point1 = this.project(cMesh.Vertices[i + 1], transformMatrix); this.drawLine(point0, point1); 
```



您现在应该可以获得类似这样的内容:

[https://david.blob.core.windows.net/softengine3d/part2sample1/index.html](https://david.blob.core.windows.net/softengine3d/part2sample1/index.html)

我知道这看起来很奇怪，但这是意料之中的行为。它应该帮助你开始理解你需要做什么来显示一个 3D 网格。但是为了有更好的渲染，我们需要发现一个新的概念。

## 用三角形显示面

现在我们知道了如何画线，我们需要一个更好的方法来渲染网格。最简单的 2D 几何形状是三角形。3D 的想法是通过使用这些三角形来绘制我们所有的网格。然后，我们需要将立方体的每一边分成两个三角形。我们将“手动”完成这一步，但我们将在下一个教程中看到 3D 建模师现在自动为我们完成这一步。

要画三角形，你需要有 3 个点/顶点。一个面仅仅是一个包含 3 个值的结构，这 3 个值是指向要渲染的网格的正确顶点数组的索引。

为了理解这个概念，让我们以 Blender 显示的立方体为例:

[![image](img/0165172b7dd41fa80a3d25ba5cf89da0.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/7532.image_5F00_43428C9F.png)

我们在这个图上显示了 4 个顶点，索引如下:0，1，2，3。要画立方体的上边，我们需要画两个三角形。第一个面 0，将从顶点 **0** (-1，1，1)到顶点 **1** (1，1，1)，从顶点 **1** (1，1，1)到顶点 **2** (-1，–1，1)，最后从顶点 **2** (-1，–1，1)到顶点 **0** (-1，1，1)，用 3 条线绘制。第二个三角形，面 1，将用从顶点 **1** 到顶点 **2** ，顶点 **2** 到顶点 **3** 和顶点 **3** 到顶点 **1** 的线绘制。

等效的代码应该是这样的:

```
var mesh = new SoftEngine.Mesh("Square", 4, 2);
eshes.Add(mesh);
esh.Vertices[0] = new Vector3(-1, 1, 1);
esh.Vertices[1] = new Vector3(1, 1, 1);
esh.Vertices[2] = new Vector3(-1, -1, 1);
esh.Vertices[3] = new Vector3(1, -1, 1);
mesh.Faces[0] = new Face { A = 0, B = 1, C = 2 };
esh.Faces[1] = new Face { A = 1, B = 2, C = 3 };
```

如果你想画整个立方体，你需要找到剩下的 10 个面，因为我们已经有了 12 个面来画立方体的 6 个面。

现在让我们定义一个 **Face** 对象的代码。这是一个非常简单的对象，因为这只是一组 3 个索引的**。下面是面的代码和现在也使用它的新网格定义:**

 ***   [C#](#facecsharp)
*   [打字稿](#facets)
*   [JavaScript](#facejs)

```
namespace SoftEngine public struct Face { public int A; public int B; public int C;
   } public class Mesh { public string Name { get; set; } public Vector3[] Vertices { get; private set; } public Face[] Faces { get; set; } public Vector3 Position { get; set; } public Vector3 Rotation { get; set; } public Mesh(string name, int verticesCount, int facesCount)
       {
           Vertices = new Vector3[verticesCount];
           Faces = new Face[facesCount];
           Name = name;
       }
   } 
```



```
///<reference path="babylon.math.ts"/> module SoftEngine { export interface Face {
       A: number;
       B: number;
       C: number;
   } export class Mesh {
       Position: BABYLON.Vector3;
       Rotation: BABYLON.Vector3;
       Vertices: BABYLON.Vector3[];
       Faces: Face[]; constructor(public name: string, verticesCount: number, facesCount: number) { this.Vertices = new Array(verticesCount); this.Faces = new Array(facesCount); this.Rotation = new BABYLON.Vector3(0, 0, 0); this.Position = new BABYLON.Vector3(0, 0, 0);
       }
   } 
```

```
var SoftEngine; function (SoftEngine) { var Mesh = (function () { function Mesh(name, verticesCount, facesCount) { this.name = name; this.Vertices = new Array(verticesCount); this.Faces = new Array(facesCount); this.Rotation = new BABYLONTS.Vector3(0, 0, 0); this.Position = new BABYLONTS.Vector3(0, 0, 0);
       } return Mesh;
   })();
   SoftEngine.Mesh = Mesh;    
)(SoftEngine || (SoftEngine = {}));
```

我们现在需要更新我们的**设备**对象的 **Render()** 函数/方法，以遍历所有定义的面并绘制相关的三角形。

*   [C#](#newrendercsharp)
*   [打字稿](#newrenderts)
*   [JavaScript](#newrenderjs)

```
foreach (var face in mesh.Faces) var vertexA = mesh.Vertices[face.A]; var vertexB = mesh.Vertices[face.B]; var vertexC = mesh.Vertices[face.C]; var pixelA = Project(vertexA, transformMatrix); var pixelB = Project(vertexB, transformMatrix); var pixelC = Project(vertexC, transformMatrix);
    DrawLine(pixelA, pixelB);
   DrawLine(pixelB, pixelC);
   DrawLine(pixelC, pixelA); 
```



```
for (var indexFaces = 0; indexFaces < cMesh.Faces.length; indexFaces++) var currentFace = cMesh.Faces[indexFaces]; var vertexA = cMesh.Vertices[currentFace.A]; var vertexB = cMesh.Vertices[currentFace.B]; var vertexC = cMesh.Vertices[currentFace.C]; var pixelA = this.project(vertexA, transformMatrix); var pixelB = this.project(vertexB, transformMatrix); var pixelC = this.project(vertexC, transformMatrix); this.drawLine(pixelA, pixelB); this.drawLine(pixelB, pixelC); this.drawLine(pixelC, pixelA); 
```

```
for (var indexFaces = 0; indexFaces < cMesh.Faces.length; indexFaces++) var currentFace = cMesh.Faces[indexFaces]; var vertexA = cMesh.Vertices[currentFace.A]; var vertexB = cMesh.Vertices[currentFace.B]; var vertexC = cMesh.Vertices[currentFace.C]; var pixelA = this.project(vertexA, transformMatrix); var pixelB = this.project(vertexB, transformMatrix); var pixelC = this.project(vertexC, transformMatrix); this.drawLine(pixelA, pixelB); this.drawLine(pixelB, pixelC); this.drawLine(pixelC, pixelA); 
```



我们最后需要用它的 **12 面**正确地声明与我们的**立方体**相关联的网格，以使这个新代码按预期工作。

这是新的宣言:

*   [C#](#newcubecsharp)
*   [打字稿/a>](#newcubets)
*   [JavaScript](#newcubejs)

```
var mesh = new SoftEngine.Mesh("Cube", 8, 12);
eshes.Add(mesh);
esh.Vertices[0] = new Vector3(-1, 1, 1);
esh.Vertices[1] = new Vector3(1, 1, 1);
esh.Vertices[2] = new Vector3(-1, -1, 1);
esh.Vertices[3] = new Vector3(1, -1, 1);
esh.Vertices[4] = new Vector3(-1, 1, -1);
esh.Vertices[5] = new Vector3(1, 1, -1);
esh.Vertices[6] = new Vector3(1, -1, -1);
esh.Vertices[7] = new Vector3(-1, -1, -1);
mesh.Faces[0] = new Face { A = 0, B = 1, C = 2 };
esh.Faces[1] = new Face { A = 1, B = 2, C = 3 };
esh.Faces[2] = new Face { A = 1, B = 3, C = 6 };
esh.Faces[3] = new Face { A = 1, B = 5, C = 6 };
esh.Faces[4] = new Face { A = 0, B = 1, C = 4 };
esh.Faces[5] = new Face { A = 1, B = 4, C = 5 };
mesh.Faces[6] = new Face { A = 2, B = 3, C = 7 };
esh.Faces[7] = new Face { A = 3, B = 6, C = 7 };
esh.Faces[8] = new Face { A = 0, B = 2, C = 7 };
esh.Faces[9] = new Face { A = 0, B = 4, C = 7 };
esh.Faces[10] = new Face { A = 4, B = 5, C = 6 };
esh.Faces[11] = new Face { A = 4, B = 6, C = 7 };
```

```
var mesh = new SoftEngine.Mesh("Cube", 8, 12);
eshes.push(mesh);
esh.Vertices[0] = new BABYLON.Vector3(-1, 1, 1);
esh.Vertices[1] = new BABYLON.Vector3(1, 1, 1);
esh.Vertices[2] = new BABYLON.Vector3(-1, -1, 1);
esh.Vertices[3] = new BABYLON.Vector3(1, -1, 1);
esh.Vertices[4] = new BABYLON.Vector3(-1, 1, -1);
esh.Vertices[5] = new BABYLON.Vector3(1, 1, -1);
esh.Vertices[6] = new BABYLON.Vector3(1, -1, -1);
esh.Vertices[7] = new BABYLON.Vector3(-1, -1, -1);
mesh.Faces[0] = { A:0, B:1, C:2 };
esh.Faces[1] = { A:1, B:2, C:3 };
esh.Faces[2] = { A:1, B:3, C:6 };
esh.Faces[3] = { A:1, B:5, C:6 };
esh.Faces[4] = { A:0, B:1, C:4 };
esh.Faces[5] = { A:1, B:4, C:5 };
mesh.Faces[6] = { A:2, B:3, C:7 };
esh.Faces[7] = { A:3, B:6, C:7 };
esh.Faces[8] = { A:0, B:2, C:7 };
esh.Faces[9] = { A:0, B:4, C:7 };
esh.Faces[10] = { A:4, B:5, C:6 };
esh.Faces[11] = { A:4, B:6, C:7 };
```

```
var mesh = new SoftEngine.Mesh("Cube", 8, 12);
eshes.push(mesh);
esh.Vertices[0] = new BABYLON.Vector3(-1, 1, 1);
esh.Vertices[1] = new BABYLON.Vector3(1, 1, 1);
esh.Vertices[2] = new BABYLON.Vector3(-1, -1, 1);
esh.Vertices[3] = new BABYLON.Vector3(1, -1, 1);
esh.Vertices[4] = new BABYLON.Vector3(-1, 1, -1);
esh.Vertices[5] = new BABYLON.Vector3(1, 1, -1);
esh.Vertices[6] = new BABYLON.Vector3(1, -1, -1);
esh.Vertices[7] = new BABYLON.Vector3(-1, -1, -1);
mesh.Faces[0] = { A:0, B:1, C:2 };
esh.Faces[1] = { A:1, B:2, C:3 };
esh.Faces[2] = { A:1, B:3, C:6 };
esh.Faces[3] = { A:1, B:5, C:6 };
esh.Faces[4] = { A:0, B:1, C:4 };
esh.Faces[5] = { A:1, B:4, C:5 };
mesh.Faces[6] = { A:2, B:3, C:7 };
esh.Faces[7] = { A:3, B:6, C:7 };
esh.Faces[8] = { A:0, B:2, C:7 };
esh.Faces[9] = { A:0, B:4, C:7 };
esh.Faces[10] = { A:4, B:5, C:6 };
esh.Faces[11] = { A:4, B:6, C:7 };
```

你现在应该有这个美丽的旋转立方体:

[https://david.blob.core.windows.net/softengine3d/part2sample2/index.html](https://david.blob.core.windows.net/softengine3d/part2sample2/index.html)

祝贺你！:)

## 用 Bresenham 增强画线算法

使用 [Bresenham 的直线算法](https://en.wikipedia.org/wiki/Bresenham's_line_algorithm "http://en.wikipedia.org/wiki/Bresenham's_line_algorithm")有一种优化的方法来绘制我们的直线。它比我们目前的简单递归版本更快&更清晰。这个算法的故事很吸引人。请阅读维基百科对该算法的定义，以了解 Bresenham 是如何构建它的，以及原因是什么。

以下是该算法的 C#、TypeScript 和 JavaScript 版本:

*   [C#](#bresenhamcsharp)
*   [打字稿](#bresenhamts)
*   [JavaScript](#bresenhamjs)

```
public void DrawBline(Vector2 point0, Vector2 point1) int x0 = (int)point0.X; int y0 = (int)point0.Y; int x1 = (int)point1.X; int y1 = (int)point1.Y; var dx = Math.Abs(x1 - x0); var dy = Math.Abs(y1 - y0); var sx = (x0 < x1) ? 1 : -1; var sy = (y0 < y1) ? 1 : -1; var err = dx - dy; while (true) {
       DrawPoint(new Vector2(x0, y0)); if ((x0 == x1) && (y0 == y1)) break; var e2 = 2 * err; if (e2 > -dy) { err -= dy; x0 += sx; } if (e2 < dx) { err += dx; y0 += sy; }
   } 
```



```
public drawBline(point0: BABYLON.Vector2, point1: BABYLON.Vector2): void { var x0 = point0.x >> 0; var y0 = point0.y >> 0; var x1 = point1.x >> 0; var y1 = point1.y >> 0; var dx = Math.abs(x1 - x0); var dy = Math.abs(y1 - y0); var sx = (x0 < x1) ? 1 : -1; var sy = (y0 < y1) ? 1 : -1; var err = dx - dy; while (true) { this.drawPoint(new BABYLON.Vector2(x0, y0)); if ((x0 == x1) && (y0 == y1)) break; var e2 = 2 * err; if (e2 > -dy) { err -= dy; x0 += sx; } if (e2 < dx) { err += dx; y0 += sy; }
   } 
```

```
Device.prototype.drawBline = function (point0, point1) { var x0 = point0.x >> 0; var y0 = point0.y >> 0; var x1 = point1.x >> 0; var y1 = point1.y >> 0; var dx = Math.abs(x1 - x0); var dy = Math.abs(y1 - y0); var sx = (x0 < x1) ? 1 : -1; var sy = (y0 < y1) ? 1 : -1; var err = dx - dy; while(true) { this.drawPoint(new BABYLON.Vector2(x0, y0)); if((x0 == x1) && (y0 == y1)) break; var e2 = 2 * err; if(e2 > -dy) { err -= dy; x0 += sx; } if(e2 < dx) { err += dx; y0 += sy; }
   }
;
```

在渲染函数中，用 DrawBline 替换调用 do DrawLine，你应该会注意到这更加流畅&更加清晰:

[https://david.blob.core.windows.net/softengine3d/part2sample3/index.html](https://david.blob.core.windows.net/softengine3d/part2sample3/index.html)

如果你仔细观察，你会发现使用 Bresenham 的这个版本比我们的第一个算法更稳定。

同样，您可以**下载包含源代码的解决方案**:

–**c#**:[softenginecsharppart 2 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart2.zip)

–**TypeScript**:[softenginetspart 2 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart2.zip)

–**JavaScript**:[softenginejspart 2 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart2.zip)或者只需在嵌入式 iframe 上右键单击->查看源代码

在下一个教程中，你将学习如何从免费的 3D 建模工具 Blender 中导出一些网格到 JSON 格式。然后我们将**加载这个 JSON 文件，用我们的线框引擎**显示它。事实上，我们已经做好了一切准备来显示更复杂的网格，如下图所示:

[![image](img/ad70a1f85881528934a2a620e4507195.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3323.image_5F00_08997040.png)

第三部见。

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/06/14/tutorial-part-2-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-ts-or-js-drawing-lines-amp-triangles . aspx](https://blogs.msdn.com/b/davrous/archive/2013/06/14/tutorial-part-2-learning-how-to-write-a-3d-soft-engine-from-scratch-in-c-ts-or-js-drawing-lines-amp-triangles.aspx)。经作者许可，在此转载。*** 

## **分享这篇文章**