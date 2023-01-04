# 从头开始编写 3D 软引擎:第 4 部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-scratch-part-4/>

在之前的教程中，[第三部分](https://www.sitepoint.com/write-3d-soft-engine-from-scratch-part-3/)，我们已经加载了一个 JSON 文件，其中我们的网格是从 Blender 序列化的。到目前为止，我们的渲染功能只是用简单的线框渲染来绘制网格。我们现在来看看如何使用**光栅化**算法填充三角形。然后，我们将看到如何处理一个 **Z-Buffer** 来避免生活在后面的面被绘制在前面的面上。

通过遵循本教程，您将能够有这样的渲染:

[https://david.blob.core.windows.net/softengine3d/part4/index.html](https://david.blob.core.windows.net/softengine3d/part4/index.html)

## 光栅化

有许多不同类型的光栅化算法。我甚至知道我的团队中有人为一个著名的 GPU 制造商制作了自己的专利光栅化算法。也是因为他，我现在知道了什么是 Boustrophedon，从那以后，它真的改变了我的生活。:-)

更严重的是，我们将在本教程中实现一个简单而有效的光栅化算法。因为我们是用我们的 3D 软件引擎在 CPU 上运行的，所以我们必须对这部分给予很多关注。的确，这将耗费我们大量的 CPU。当然，今天这个繁重的部分直接由 GPU 来完成。

让我们从一个练习开始。拿一张纸，开始画你能想到的所有类型的三角形。想法是**找到一个通用的方法来画任何类型的三角形**。

如果我们在 Y 坐标上对每个三角形的三个顶点进行排序，以便总是让 P1、P2 和 P3 紧随其后，我们最终将只有两种可能的情况:

[![image](img/fd40eb1350100377da3a0ef367f4ffc0.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/2117.image_5F00_70D69DD9.png)

然后你会看到我们有两种情况: **P2 在 P1P3** 的右边，或者 **P2 在 P1P3** 的左边。在我们的例子中，因为我们希望**总是从左到右**从 sx 到 ex 画我们的线，我们将有第一个条件 IF 来处理这两个例子。

此外，我们将通过从 P1 向下移动**从左到右绘制。y 到 P3。Y** 沿着图中左边画的红线。但是我们需要改变我们到达 P2 的逻辑。因为斜率在两种情况下都会改变。这就是为什么，我们在扫描线过程中有 **2 步**。**从 P1 搬下来。y 到 P2。Y** 然后是来自 P2 的**。y 到 P3。Y** ，我们的最终目的地。

理解如何构建我们的算法所需的所有逻辑在维基百科上都有描述:[https://en.wikipedia.org/wiki/Slope](https://en.wikipedia.org/wiki/Slope "https://en.wikipedia.org/wiki/Slope")。这真的是一些基本的数学。

为了能够对情况 1 和情况 2 之间的情况进行排序，您只需按照以下方式计算反斜率:

dP1P2 = P2P1。x P2。P1。y 和 dP1P3 = P3。P1。x P3。P1。Y

如果 dP1P2 > dP1P3，那么我们在第一种情况下，P2 在右边，否则如果 dP1P2 > dP1P2，我们在第二种情况下，P2 在左边。

现在我们有了算法的基本逻辑，我们需要知道如何**计算我图上 SX(开始 X)和 e X(结束 X)** 之间每条线上的 X。所以我们需要先计算 SX & EX。由于我们知道 Y 值和斜率 P1P3 & P1P2，我们可以很容易地找到我们感兴趣的 SX & EX。

让我们以案例 1 的步骤 1 为例。第一步是用我们循环中的当前 Y 值计算我们的**梯度**。它将告诉我们在 P1 之间的扫描线处理处于哪个阶段。y 和 P2。步骤 1 中的 y。

渐变=当前–P1。y P2。P1。Y

由于 X 和 Y 是线性关联的，我们可以使用 P1 基于这个梯度对 s X 进行插值。x 和 P3。X &使用 P1 插值 EX。x 和 P2.X。

如果你设法**理解了插值**的概念，你将能够理解所有剩余的教程来处理光&纹理。然后，您肯定需要花时间阅读相关的代码。您还需要确保您能够自己从头开始重新构建它，而无需复制/粘贴下面的代码。

如果这还不够清楚，这里有其他有趣的文章可以阅读寻址和栅格化:

–[3D 软件渲染引擎–第一部分](https://www.codeproject.com/Articles/170296/3D-Software-Rendering-Engine-Part-I "https://www.codeproject.com/Articles/170296/3D-Software-Rendering-Engine-Part-I")
–[三角形栅格化](https://lva.cg.tuwien.ac.at/ecg/wiki/doku.php?id=students:fill_rasterization "https://lva.cg.tuwien.ac.at/ecg/wiki/doku.php?id=students:fill_rasterization")
–[填充三角形的软件栅格化算法](https://www.sunshine2k.de/coding/java/TriangleRasterization/TriangleRasterization.html)

既然我们已经描述了我们的算法。现在让我们开始编写代码。首先从设备类别中移除*拉线*和*拉线*。然后，用这些函数/方法替换现有的函数/方法:

*   [C#](#code1csharp)
*   [打字稿](#code1ts)
*   [JavaScript](#code1js)

```
// Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix public Vector3 Project(Vector3 coord, Matrix transMat) // transforming the coordinates var point = Vector3.TransformCoordinate(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.X * bmp.PixelWidth + bmp.PixelWidth / 2.0f; var y = -point.Y * bmp.PixelHeight + bmp.PixelHeight / 2.0f; return (new Vector3(x, y, point.Z)); // DrawPoint calls PutPixel but does the clipping operation before public void DrawPoint(Vector2 point, Color4 color) // Clipping what's visible on screen if (point.X >= 0 && point.Y >= 0 && point.X < bmp.PixelWidth && point.Y < bmp.PixelHeight)
   { // Drawing a point PutPixel((int)point.X, (int)point.Y, color);
   } 
```

```
// Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix public project(coord: BABYLON.Vector3, transMat: BABYLON.Matrix): BABYLON.Vector3 { // transforming the coordinates var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0; return (new BABYLON.Vector3(x, y, point.z)); // drawPoint calls putPixel but does the clipping operation before public drawPoint(point: BABYLON.Vector2, color: BABYLON.Color4): void { // Clipping what's visible on screen if (point.x >= 0 && point.y >= 0 && point.x < this.workingWidth && point.y < this.workingHeight) { // Drawing a yellow point this.putPixel(point.x, point.y, color);
   } 
```

```
// Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix Device.prototype.project = function (coord, transMat) { var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0 >> 0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0 >> 0; return (new BABYLON.Vector3(x, y, point.z));
; // drawPoint calls putPixel but does the clipping operation before Device.prototype.drawPoint = function (point, color) { // Clipping what's visible on screen if (point.x >= 0 && point.y >= 0 && point.x < this.workingWidth
                                       && point.y < this.workingHeight) { // Drawing a yellow point this.putPixel(point.x, point.y, color);
   }
;
```

我们只是在为本教程的第二部分准备一些材料。现在，这是最重要的部分。这是根据前面的解释画三角形的逻辑。

*   [C#](#code2csharp)
*   [打字稿](#code2ts)
*   [JavaScript](#code2js)

```
// Clamping values to keep them between 0 and 1 float Clamp(float value, float min = 0, float max = 1) return Math.Max(min, Math.Min(value, max)); // Interpolating the value between 2 vertices 
/ min is the starting point, max the ending point
/ and gradient the % between the 2 points float Interpolate(float min, float max, float gradient) return min + (max - min) * Clamp(gradient); // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before void ProcessScanLine(int y, Vector3 pa, Vector3 pb, Vector3 pc, Vector3 pd, Color4 color) // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
    // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.Y != pb.Y ? (y - pa.Y) / (pb.Y - pa.Y) : 1; var gradient2 = pc.Y != pd.Y ? (y - pc.Y) / (pd.Y - pc.Y) : 1; int sx = (int)Interpolate(pa.X, pb.X, gradient1); int ex = (int)Interpolate(pc.X, pd.X, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++)
   {
       DrawPoint(new Vector2(x, y), color);
   } public void DrawTriangle(Vector3 p1, Vector3 p2, Vector3 p3, Color4 color) // Sorting the points in order to always have this order on screen p1, p2 & p3
   // with p1 always up (thus having the Y the lowest possible to be near the top screen)
   // then p2 between p1 & p3 if (p1.Y > p2.Y)
   { var temp = p2;
       p2 = p1;
       p1 = temp;
   } if (p2.Y > p3.Y)
   { var temp = p2;
       p2 = p3;
       p3 = temp;
   } if (p1.Y > p2.Y)
   { var temp = p2;
       p2 = p1;
       p1 = temp;
   } // inverse slopes float dP1P2, dP1P3; // https://en.wikipedia.org/wiki/Slope
   // Computing inverse slopes if (p2.Y - p1.Y > 0)
       dP1P2 = (p2.X - p1.X) / (p2.Y - p1.Y); else dP1P2 = 0; if (p3.Y - p1.Y > 0)
       dP1P3 = (p3.X - p1.X) / (p3.Y - p1.Y); else dP1P3 = 0; // First case where triangles are like that:
   // P1
   // -
   // -- 
   // - -
   // -  -
   // -   - P2
   // -  -
   // - -
   // -
   // P3 if (dP1P2 > dP1P3)
   { for (var y = (int)p1.Y; y <= (int)p3.Y; y++)
       { if (y < p2.Y)
           {
               ProcessScanLine(y, p1, p3, p1, p2, color);
           } else {
               ProcessScanLine(y, p1, p3, p2, p3, color);
           }
       }
   } // First case where triangles are like that:
   //       P1
   //        -
   //       -- 
   //      - -
   //     -  -
   // P2 -   - 
   //     -  -
   //      - -
   //        -
   //       P3 else { for (var y = (int)p1.Y; y <= (int)p3.Y; y++)
       { if (y < p2.Y)
           {
               ProcessScanLine(y, p1, p2, p1, p3, color);
           } else {
               ProcessScanLine(y, p2, p3, p1, p3, color);
           }
       }
   } 
```

```
// Clamping values to keep them between 0 and 1 public clamp(value: number, min: number = 0, max: number = 1): number { return Math.max(min, Math.min(value, max)); // Interpolating the value between 2 vertices 
/ min is the starting point, max the ending point
/ and gradient the % between the 2 points public interpolate(min: number, max: number, gradient: number) { return min + (max - min) * this.clamp(gradient); // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before public processScanLine(y: number, pa: BABYLON.Vector3, pb: BABYLON.Vector3, 
                       pc: BABYLON.Vector3, pd: BABYLON.Vector3, color: BABYLON.Color4): void { // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
    // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (y - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (y - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++) { this.drawPoint(new BABYLON.Vector2(x, y), color);
   } public drawTriangle(p1: BABYLON.Vector3, p2: BABYLON.Vector3, 
                    p3: BABYLON.Vector3, color: BABYLON.Color4): void { // Sorting the points in order to always have this order on screen p1, p2 & p3
   // with p1 always up (thus having the Y the lowest possible to be near the top screen)
   // then p2 between p1 & p3 if (p1.y > p2.y) { var temp = p2;
       p2 = p1;
       p1 = temp;
   } if (p2.y > p3.y) { var temp = p2;
       p2 = p3;
       p3 = temp;
   } if (p1.y > p2.y) { var temp = p2;
       p2 = p1;
       p1 = temp;
   } // inverse slopes var dP1P2: number; var dP1P3: number; // https://en.wikipedia.org/wiki/Slope
   // Computing slopes if (p2.y - p1.y > 0)
       dP1P2 = (p2.x - p1.x) / (p2.y - p1.y); else dP1P2 = 0; if (p3.y - p1.y > 0)
       dP1P3 = (p3.x - p1.x) / (p3.y - p1.y); else dP1P3 = 0; // First case where triangles are like that:
   // P1
   // -
   // -- 
   // - -
   // -  -
   // -   - P2
   // -  -
   // - -
   // -
   // P3 if (dP1P2 > dP1P3) { for (var y = p1.y >> 0; y <= p3.y >> 0; y++)
       { if (y < p2.y) { this.processScanLine(y, p1, p3, p1, p2, color);
           } else { this.processScanLine(y, p1, p3, p2, p3, color);
           }
       }
   } // First case where triangles are like that:
   //       P1
   //        -
   //       -- 
   //      - -
   //     -  -
   // P2 -   - 
   //     -  -
   //      - -
   //        -
   //       P3 else { for (var y = p1.y >> 0; y <= p3.y >> 0; y++)
       { if (y < p2.y) { this.processScanLine(y, p1, p2, p1, p3, color);
           } else { this.processScanLine(y, p2, p3, p1, p3, color);
           }
       }
   } 
```

```
// Clamping values to keep them between 0 and 1 Device.prototype.clamp = function (value, min, max) { if (typeof min === "undefined") { min = 0; } if (typeof max === "undefined") { max = 1; } return Math.max(min, Math.min(value, max));
; // Interpolating the value between 2 vertices 
/ min is the starting point, max the ending point
/ and gradient the % between the 2 points Device.prototype.interpolate = function (min, max, gradient) { return min + (max - min) * this.clamp(gradient);
; // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before Device.prototype.processScanLine = function (y, pa, pb, pc, pd, color) { // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between    
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (y - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (y - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // drawing a line from left (sx) to right (ex) for(var x = sx; x < ex; x++) { this.drawPoint(new BABYLON.Vector2(x, y), color);
   }
;
Device.prototype.drawTriangle = function (p1, p2, p3, color) { // Sorting the points in order to always have this order on screen p1, p2 & p3
   // with p1 always up (thus having the Y the lowest possible to be near the top screen)
   // then p2 between p1 & p3 if(p1.y > p2.y) { var temp = p2;
       p2 = p1;
       p1 = temp;
   } if(p2.y > p3.y) { var temp = p2;
       p2 = p3;
       p3 = temp;
   } if(p1.y > p2.y) { var temp = p2;
       p2 = p1;
       p1 = temp;
   } // inverse slopes var dP1P2; var dP1P3; // https://en.wikipedia.org/wiki/Slope
   // Computing slopes if(p2.y - p1.y > 0) {
       dP1P2 = (p2.x - p1.x) / (p2.y - p1.y);
   } else {
       dP1P2 = 0;
   } if(p3.y - p1.y > 0) {
       dP1P3 = (p3.x - p1.x) / (p3.y - p1.y);
   } else {
       dP1P3 = 0;
   } // First case where triangles are like that:
   // P1
   // -
   // -- 
   // - -
   // -  -
   // -   - P2
   // -  -
   // - -
   // -
   // P3 if(dP1P2 > dP1P3) { for(var y = p1.y >> 0; y <= p3.y >> 0; y++) { if(y < p2.y) { this.processScanLine(y, p1, p3, p1, p2, color);
           } else { this.processScanLine(y, p1, p3, p2, p3, color);
           }
       }
   } // First case where triangles are like that:
   //       P1
   //        -
   //       -- 
   //      - -
   //     -  -
   // P2 -   - 
   //     -  -
   //      - -
   //        -
   //       P3 else { for(var y = p1.y >> 0; y <= p3.y >> 0; y++) { if(y < p2.y) { this.processScanLine(y, p1, p2, p1, p3, color);
           } else { this.processScanLine(y, p2, p3, p1, p3, color);
           }
       }
   }
;
```

你可以在代码中看到我们是如何处理两种类型的三角形填充以及扫描线过程中的两个步骤的。

最后，你需要更新渲染函数来调用 *drawTriangle* 而不是 3 次调用*drawLine*/*drawLine*。我们还使用灰度绘制每个三角形。否则，如果我们用同样的颜色画它们，我们就不能真正看到发生了什么。我们将在下一个教程中看到如何以适当的方式处理灯光。

*   [C#](#code3csharp)
*   [打字稿](#code3ts)
*   [JavaScript](#code3js)

```
var faceIndex = 0; foreach (var face in mesh.Faces) var vertexA = mesh.Vertices[face.A]; var vertexB = mesh.Vertices[face.B]; var vertexC = mesh.Vertices[face.C]; var pixelA = Project(vertexA, transformMatrix); var pixelB = Project(vertexB, transformMatrix); var pixelC = Project(vertexC, transformMatrix); var color = 0.25f + (faceIndex % mesh.Faces.Length) * 0.75f / mesh.Faces.Length;
   DrawTriangle(pixelA, pixelB, pixelC, new Color4(color, color, color, 1));
   faceIndex++; 
```

```
for (var indexFaces = 0; indexFaces < cMesh.Faces.length; indexFaces++) { var currentFace = cMesh.Faces[indexFaces]; var vertexA = cMesh.Vertices[currentFace.A]; var vertexB = cMesh.Vertices[currentFace.B]; var vertexC = cMesh.Vertices[currentFace.C]; var pixelA = this.project(vertexA, transformMatrix); var pixelB = this.project(vertexB, transformMatrix); var pixelC = this.project(vertexC, transformMatrix); var color: number = 0.25 + ((indexFaces % cMesh.Faces.length) / cMesh.Faces.length) * 0.75; this.drawTriangle(pixelA, pixelB, pixelC, new BABYLON.Color4(color, color, color, 1)); 
```

```
for (var indexFaces = 0; indexFaces < cMesh.Faces.length; indexFaces++) { var currentFace = cMesh.Faces[indexFaces]; var vertexA = cMesh.Vertices[currentFace.A]; var vertexB = cMesh.Vertices[currentFace.B]; var vertexC = cMesh.Vertices[currentFace.C]; var pixelA = this.project(vertexA, transformMatrix); var pixelB = this.project(vertexB, transformMatrix); var pixelC = this.project(vertexC, transformMatrix); var color = 0.25 + ((indexFaces % cMesh.Faces.length) / cMesh.Faces.length) * 0.75; this.drawTriangle(pixelA, pixelB, pixelC, new BABYLON.Color4(color, color, color, 1)); 
```

你应该得到第一个结果:

[https://david.blob.core.windows.net/softengine3d/part4sample1/index.html](https://david.blob.core.windows.net/softengine3d/part4sample1/index.html)

那里出了什么问题？你可能已经有了透过网格观看的感觉。这是因为我们在绘制所有的三角形，而没有“隐藏”住后面的三角形。

## z 缓冲或如何使用深度缓冲

然后我们需要测试当前像素的 Z 值，并在绘制它之前将其与缓冲区进行比较。如果要绘制的当前像素的 Z 值低于此处绘制的前一个像素，我们可以覆盖它。事实上，这意味着我们正在绘制的当前面在先前绘制的面的前面。但是，如果要绘制的当前像素的 Z 大于此处绘制的前一个像素，我们可以放弃绘制操作。

然后我们需要**在屏幕**上保存每个像素的这些 Z 索引的历史。为此，声明一个新的 float 数组，将其命名为**。它的大小将等于屏幕上的像素数(宽*高)。该深度缓冲器必须在每个 *clear()* 操作期间用非常高的默认 Z 值初始化。**

 **在 *putPixel* 函数/方法中，我们只需要对照存储在深度缓冲区中的 Z 索引来测试像素的 Z 索引。此外，我们之前的部分逻辑是返回 Vector2 以在屏幕上进行逻辑绘制。我们将把它改为 Vector3 来推动顶点的 Z 值，因为我们现在需要这些信息来正确地绘制面。

最后，以同样的方式，我们在三角形的每条边之间插入 X 值，我们需要使用完全相同的算法为每个像素插入 Z 值。

总之，下面是您需要在设备对象中更新的代码:

*   [C#](#code4csharp)
*   [打字稿](#code4ts)
*   [JavaScript](#code4js)

```
private byte[] backBuffer; private readonly float[] depthBuffer; private WriteableBitmap bmp; private readonly int renderWidth; private readonly int renderHeight; public Device(WriteableBitmap bmp) this.bmp = bmp;
   renderWidth = bmp.PixelWidth;
   renderHeight = bmp.PixelHeight; // the back buffer size is equal to the number of pixels to draw
   // on screen (width*height) * 4 (R,G,B & Alpha values). backBuffer = new byte[bmp.PixelWidth * bmp.PixelHeight * 4];
   depthBuffer = new float[bmp.PixelWidth * bmp.PixelHeight]; // This method is called to clear the back buffer with a specific color public void Clear(byte r, byte g, byte b, byte a) { // Clearing Back Buffer for (var index = 0; index < backBuffer.Length; index += 4)
   { // BGRA is used by Windows instead by RGBA in HTML5 backBuffer[index] = b;
       backBuffer[index + 1] = g;
       backBuffer[index + 2] = r;
       backBuffer[index + 3] = a;
   } // Clearing Depth Buffer for (var index = 0; index < depthBuffer.Length; index++)
   {
       depthBuffer[index] = float.MaxValue;
   } // Called to put a pixel on screen at a specific X,Y coordinates public void PutPixel(int x, int y, float z, Color4 color) // As we have a 1-D Array for our back buffer
   // we need to know the equivalent cell in 1-D based
   // on the 2D coordinates on screen var index = (x + y * renderWidth); var index4 = index * 4; if (depthBuffer[index] < z)
   { return; // Discard }
    depthBuffer[index] = z;
    backBuffer[index4] = (byte)(color.Blue * 255);
   backBuffer[index4 + 1] = (byte)(color.Green * 255);
   backBuffer[index4 + 2] = (byte)(color.Red * 255);
   backBuffer[index4 + 3] = (byte)(color.Alpha * 255); // Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix public Vector3 Project(Vector3 coord, Matrix transMat) // transforming the coordinates var point = Vector3.TransformCoordinate(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.X * bmp.PixelWidth + bmp.PixelWidth / 2.0f; var y = -point.Y * bmp.PixelHeight + bmp.PixelHeight / 2.0f; return (new Vector3(x, y, point.Z)); // DrawPoint calls PutPixel but does the clipping operation before public void DrawPoint(Vector3 point, Color4 color) // Clipping what's visible on screen if (point.X >= 0 && point.Y >= 0 && point.X < bmp.PixelWidth && point.Y < bmp.PixelHeight)
   { // Drawing a point PutPixel((int)point.X, (int)point.Y, point.Z ,color);
   } // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before void ProcessScanLine(int y, Vector3 pa, Vector3 pb, Vector3 pc, Vector3 pd, Color4 color) // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.Y != pb.Y ? (y - pa.Y) / (pb.Y - pa.Y) : 1; var gradient2 = pc.Y != pd.Y ? (y - pc.Y) / (pd.Y - pc.Y) : 1; int sx = (int)Interpolate(pa.X, pb.X, gradient1); int ex = (int)Interpolate(pc.X, pd.X, gradient2); // starting Z & ending Z float z1 = Interpolate(pa.Z, pb.Z, gradient1); float z2 = Interpolate(pc.Z, pd.Z, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++)
   { float gradient = (x - sx) / (float)(ex - sx); var z = Interpolate(z1, z2, gradient);
       DrawPoint(new Vector3(x, y, z), color);
   } 
```

```
// the back buffer size is equal to the number of pixels to draw
/ on screen (width*height) * 4 (R,G,B & Alpha values). private backbuffer: ImageData; private workingCanvas: HTMLCanvasElement; private workingContext: CanvasRenderingContext2D; private workingWidth: number; private workingHeight: number; // equals to backbuffer.data private backbufferdata; private depthbuffer: number[]; constructor(canvas: HTMLCanvasElement) { this.workingCanvas = canvas; this.workingWidth = canvas.width; this.workingHeight = canvas.height; this.workingContext = this.workingCanvas.getContext("2d"); this.depthbuffer = new Array(this.workingWidth * this.workingHeight); // This function is called to clear the back buffer with a specific color public clear(): void { // Clearing with black color by default this.workingContext.clearRect(0, 0, this.workingWidth, this.workingHeight); // once cleared with black pixels, we're getting back the associated image data to 
   // clear out back buffer this.backbuffer = this.workingContext.getImageData(0, 0, this.workingWidth, this.workingHeight); // Clearing depth buffer for (var i = 0; i < this.depthbuffer.length; i++) { // Max possible value this.depthbuffer[i] = 10000000;
   } // Called to put a pixel on screen at a specific X,Y coordinates public putPixel(x: number, y: number, z: number, color: BABYLON.Color4): void { this.backbufferdata = this.backbuffer.data; // As we have a 1-D Array for our back buffer
   // we need to know the equivalent cell index in 1-D based
   // on the 2D coordinates of the screen var index: number = ((x >> 0) + (y >> 0) * this.workingWidth); var index4: number = index * 4; if (this.depthbuffer[index] < z) { return; // Discard } this.depthbuffer[index] = z; // RGBA color space is used by the HTML5 canvas this.backbufferdata[index4] = color.r * 255; this.backbufferdata[index4 + 1] = color.g * 255; this.backbufferdata[index4 + 2] = color.b * 255; this.backbufferdata[index4 + 3] = color.a * 255; // Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix public project(coord: BABYLON.Vector3, transMat: BABYLON.Matrix): BABYLON.Vector3 { // transforming the coordinates var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0; return (new BABYLON.Vector3(x, y, point.z)); // drawPoint calls putPixel but does the clipping operation before public drawPoint(point: BABYLON.Vector3, color: BABYLON.Color4): void { // Clipping what's visible on screen if (point.x >= 0 && point.y >= 0 && point.x < this.workingWidth && point.y < this.workingHeight) { // Drawing a yellow point this.putPixel(point.x, point.y, point.z, color);
   } // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before public processScanLine(y: number, pa: BABYLON.Vector3, pb: BABYLON.Vector3, pc: BABYLON.Vector3, pd: BABYLON.Vector3, color: BABYLON.Color4): void { // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (y - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (y - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // starting Z & ending Z var z1: number = this.interpolate(pa.z, pb.z, gradient1); var z2: number = this.interpolate(pc.z, pd.z, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++) { var gradient: number = (x - sx) / (ex - sx); // normalisation pour dessiner de gauche à droite var z = this.interpolate(z1, z2, gradient); this.drawPoint(new BABYLON.Vector3(x, y, z), color);
   } 
```

```
function Device(canvas) { this.workingCanvas = canvas; this.workingWidth = canvas.width; this.workingHeight = canvas.height; this.workingContext = this.workingCanvas.getContext("2d"); this.depthbuffer = new Array(this.workingWidth * this.workingHeight); // This function is called to clear the back buffer with a specific color Device.prototype.clear = function () { // Clearing with black color by default this.workingContext.clearRect(0, 0, this.workingWidth, this.workingHeight); // once cleared with black pixels, we're getting back the associated image data to 
   // clear out back buffer this.backbuffer = this.workingContext.getImageData(0, 0, this.workingWidth, this.workingHeight); // Clearing depth buffer for (var i = 0; i < this.depthbuffer.length; i++) { // Max possible value this.depthbuffer[i] = 10000000;
   }
; // Called to put a pixel on screen at a specific X,Y coordinates Device.prototype.putPixel = function (x, y, z, color) { this.backbufferdata = this.backbuffer.data; // As we have a 1-D Array for our back buffer
   // we need to know the equivalent cell index in 1-D based
   // on the 2D coordinates of the screen var index = ((x >> 0) + (y >> 0) * this.workingWidth); var index4 = index * 4; if(this.depthbuffer[index] < z) { return; // Discard } this.depthbuffer[index] = z; // RGBA color space is used by the HTML5 canvas this.backbufferdata[index4] = color.r * 255; this.backbufferdata[index4 + 1] = color.g * 255; this.backbufferdata[index4 + 2] = color.b * 255; this.backbufferdata[index4 + 3] = color.a * 255;
; // Project takes some 3D coordinates and transform them
/ in 2D coordinates using the transformation matrix Device.prototype.project = function (coord, transMat) { // transforming the coordinates var point = BABYLON.Vector3.TransformCoordinates(coord, transMat); // The transformed coordinates will be based on coordinate system
   // starting on the center of the screen. But drawing on screen normally starts
   // from top left. We then need to transform them again to have x:0, y:0 on top left. var x = point.x * this.workingWidth + this.workingWidth / 2.0; var y = -point.y * this.workingHeight + this.workingHeight / 2.0; return (new BABYLON.Vector3(x, y, point.z));
; // drawPoint calls putPixel but does the clipping operation before Device.prototype.drawPoint = function (point, color) { // Clipping what's visible on screen if(point.x >= 0 && point.y >= 0 && point.x < this.workingWidth && point.y < this.workingHeight) { // Drawing a point this.putPixel(point.x, point.y, point.z, color);
   }
; // drawing line between 2 points from left to right
/ papb -> pcpd
/ pa, pb, pc, pd must then be sorted before Device.prototype.processScanLine = function (y, pa, pb, pc, pd, color) { // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (y - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (y - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // starting Z & ending Z var z1 = this.interpolate(pa.z, pb.z, gradient1); var z2 = this.interpolate(pc.z, pd.z, gradient2); // drawing a line from left (sx) to right (ex) for(var x = sx; x < ex; x++) { var gradient = (x - sx) / (ex - sx); var z = this.interpolate(z1, z2, gradient); this.drawPoint(new BABYLON.Vector3(x, y, z), color);
   }
;
```

使用这个新代码，您应该可以获得与本文顶部嵌入的 iframe 相同的呈现。

像往常一样，您可以**下载包含源代码的解决方案**:

–**c#**:[软化器部件 4 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart4.zip)

–**类型脚本** : [软化器部件 4 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart4.zip)

–**JavaScript**:[软化器部件 4.zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart4.zip) 或者直接在第一个嵌入的 iframe 上右键单击->查看源代码

在第五个教程中，我们将看到如何通过 Gouraud 着色来模拟光照，我们将获得这种渲染:

[![image](img/bf708d13a0b48523b7434af2edc76537.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/7077.image_5F00_02CD12D8.png)

但在此之前，我有一个额外的关于优化和并行的额外教程，解释如何通过并行来提升当前的算法。以及为什么我们不能在 JavaScript 中进行同样的优化。明天你要小心了。

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/06/21/tutorial-part-4-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-rasterization-amp-z-buffering . aspx](https://blogs.msdn.com/b/davrous/archive/2013/06/21/tutorial-part-4-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-rasterization-amp-z-buffering.aspx)。经作者许可，在此转载。*** 

## **分享这篇文章**