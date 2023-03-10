# 从头开始编写 3D 软引擎:第 6 部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-scratch-part-6/>

下面是这个[长系列](https://www.sitepoint.com/series/write-a-3d-soft-engine-from-scratch/)的最终教程。我们将看到**如何通过使用从 Blender 导出的**贴图坐标**将纹理**应用到网格。如果你已经理解了前面的教程，那么应用一些纹理将是小菜一碟。主要概念还是在每个顶点之间插入一些数据。在本教程的第二部分，我们将看到如何提高我们的渲染算法的性能。为此，我们将通过使用**背面剔除**方法只显示可见的面。但是为了更进一步，我们将使用我们最后的秘密武器:GPU。然后你就会明白为什么 OpenGL/WebGL 和 DirectX 技术对制作实时 3D 游戏如此重要。它们有助于使用 GPU 而不是 CPU 来渲染我们的 3D 对象。为了真正看出区别，我们将在名为 **Babylon 的 **WebGL 3D 引擎**中加载完全相同的 JSON 文件。JS** 。渲染效果会好得多，FPS 也无与伦比，尤其是在低端设备上！

在本教程结束时，您将在我们基于 CPU 的 3D 软件引擎中获得最终渲染结果:

[https://david.blob.core.windows.net/softengine3d/part6/index.html](https://david.blob.core.windows.net/softengine3d/part6/index.html)

## 纹理映射

### 概念

让我们从维基百科的定义开始:[纹理映射](https://en.wikipedia.org/wiki/Texture_mapping "https://en.wikipedia.org/wiki/Texture_mapping")::*一个**纹理映射**被应用(映射)到一个形状或者多边形的表面。这个过程类似于将有图案的纸应用到普通的白盒上。通过显式指定或程序定义，为多边形中的每个顶点指定一个纹理坐标(在 2d 情况下也称为 UV 坐标)。然后，在多边形的面上内插图像采样位置，以产生看起来比用有限数量的多边形实现的视觉结果更丰富的视觉结果。*

现在让我们试着理解这到底意味着什么。

我第一次试图想象我们如何将纹理应用到 3D 网格是通过首先考虑一个立方体，这是我们在这个系列中绘制的第一个网格。然后我在考虑用一张图片作为我们的纹理，并把它映射到每个立方体的表面。在如此简单的情况下，这可能会很好地工作。但是第一个问题是:如果我想在每个立方体的表面上应用不同的图像/纹理呢？第一个想法是为你的立方体的 6 个面拍摄 6 张不同的照片。更准确地说，取 6 张图片，把它们分成 2 个三角形，映射到一个立方体的 12 个三角形上。

但是有一种更简单更优雅的方法，你小时候可能已经玩过了。这张图片将帮助您:

[![image](img/bdcee9268214d1b4c2267b44894d95a5.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/0003.image_5F00_08945DEF.png)

同样的方法也适用于 3D 引擎。想象这个图像是将被应用到我们的立方体的纹理。将其视为颜色字节的二维数组。我们将能够影响一些 2D 坐标移动到这个数组的每个立方体的顶点，以获得类似这样的东西:

[![image](img/f2d9be294d83413015af442975e41b38.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/6825.image_5F00_6270B2FC.png)

*<font size="1">图片取自本论坛跟帖:</font>* [*<font size="1">在搅拌机里纹理化一个立方体，并打算把我的头发拉出来</font>*](https://www.sluniverse.com/php/vb/content-creation/65233-texturing-cube-blender-going-pull.html "https://www.sluniverse.com/php/vb/content-creation/65233-texturing-cube-blender-going-pull.html")

这些 2D 纹理坐标被称为 **UV 坐标**。

**<u>注:</u>** 我曾经问过一位 3D 大师，给它们取名为 U & V 的理由是什么？答案非常明显:“*嗯，因为它就在 X，Y，z*之前”。我期待一个更复杂的答案！；)

你现在可能会问自己如何处理高级的复杂网格，比如我们美丽的猴子的头 Suzanne，不是吗？

对于这种网格，我们也将使用一个单一的 2D 图像，将在三维映射。为了建立相应的纹理，我们需要一个你的网格的计划 2D 视图。该操作被称为**展开**操作。如果你像我一样是一个糟糕的开发者，相信我，你将需要一个像我朋友[米歇尔·鲁索](https://blogs.msdn.com/designmichel)这样出色的 3D 设计师来帮助你完成这个阶段！这正是我所做的:寻求帮助。:)

以 Suzanne 模型为例，在展开操作之后，设计者将获得这种结果:

[![image](img/141d60447b1c56495a43012343c1f24f.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3580.image_5F00_69838075.png)

设计师然后将绘制这个计划的 2D 视图，结果将是纹理准备好供我们的引擎使用。在我们的例子中，米歇尔·鲁索已经为我们做了这项工作，这是他自己版本的苏珊娜:

[![image](img/994d47aa4d9d76dae3a3e8987f94fff0.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/8322.image_5F00_38C51F37.png)

我知道这个结果可能在你第一次尝试理解纹理映射时看起来很奇怪。但是你应该已经在纹理的右下角看到了一个看起来像眼睛的东西。这部分将被 3D 映射到苏珊的双眼，使用简单的对称操作来区分双眼。

你现在知道了纹理映射的基础。为了明确了解它是如何工作的，请阅读我在网上为您找到的这些附加资源:

–[教程 16–基本纹理映射](https://ogldev.atspace.co.uk/www/tutorial16/tutorial16.html "https://ogldev.atspace.co.uk/www/tutorial16/tutorial16.html")，阅读第一部分，这将有助于理解如何将 UV 坐标(介于 0 和 1 之间)映射到我们的网格的三角形
–[Blender 2.6 手册–UV 映射一个网格](https://wiki.blender.org/index.php/Doc:2.6/Manual/Textures/Mapping/UV/Unwrapping "https://wiki.blender.org/index.php/Doc:2.6/Manual/Textures/Mapping/UV/Unwrapping")，它描述了各种映射类型
–[教程 5–纹理映射](https://www.real3dtutorials.com/tut00005.php "https://www.real3dtutorials.com/tut00005.php")，阅读第一部分，这肯定会帮助你至少知道如何映射一个立方体。:)

![](img/627a4bf28483b62b96ea67be2b4c1dab.png)

### 密码

我们现在准备深入研究代码。有几项任务需要完成:

1–创建一个纹理类，该类将加载充当纹理的图像，并返回与每像素插值的 U & V 坐标相关联的颜色
2–在完整的渲染流中添加/传递纹理信息
3–解析由 Babylon Blender 的插件导出的 [JSON 文件，以加载 UV 坐标](https://blogs.msdn.com/b/davrous/archive/2013/06/17/tutorial-part-3-learning-how-to-write-a-3d-soft-engine-in-c-ts-or-js-loading-meshes-exported-from-blender.aspx)

#### 纹理逻辑

在使用 TypeScript/JavaScript 的 HTML5 中，我们当然要通过动态创建一个 canvas 元素来加载纹理，并获取它的相关图像数据以获得我们的颜色字节数组。

用 C#/XAML，我们将创建一个 WriteableBitmap，用我们将要加载的图像设置它的源，并获取它的 PixelBuffer 属性以获得我们的颜色字节数组。

*   [C#](#sample1csharp)
*   [打字稿](#sample1ts)
*   [JavaScript](#sample1js)

```
public class Texture { private byte[] internalBuffer; private int width; private int height; // Working with a fix sized texture (512x512, 1024x1024, etc.). public Texture(string filename, int width, int height)
   { this.width = width; this.height = height;
       Load(filename);
   } async void Load(string filename)
   { var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(filename); using (var stream = await file.OpenReadAsync())
       { var bmp = new WriteableBitmap(width, height);
           bmp.SetSource(stream);
            internalBuffer = bmp.PixelBuffer.ToArray();
       }
   } // Takes the U & V coordinates exported by Blender
   // and return the corresponding pixel color in the texture public Color4 Map(float tu, float tv)
   { // Image is not loaded yet if (internalBuffer == null)
       { return Color4.White;
       } // using a % operator to cycle/repeat the texture if needed int u = Math.Abs((int) (tu*width) % width); int v = Math.Abs((int) (tv*height) % height); int pos = (u + v * width) * 4; byte b = internalBuffer[pos]; byte g = internalBuffer[pos + 1]; byte r = internalBuffer[pos + 2]; byte a = internalBuffer[pos + 3]; return new Color4(r / 255.0f, g / 255.0f, b / 255.0f, a / 255.0f);
   } 
```

```
export class Texture {
   width: number;
   height: number;
   internalBuffer: ImageData; // Working with a fix sized texture (512x512, 1024x1024, etc.). constructor(filename: string, width: number, height: number) { this.width = width; this.height = height; this.load(filename);
   } public load(filename: string): void { var imageTexture = new Image();
       imageTexture.height = this.height;
       imageTexture.width = this.width;
       imageTexture.onload = () => { var internalCanvas: HTMLCanvasElement = document.createElement("canvas");
           internalCanvas.width = this.width;
           internalCanvas.height = this.height; var internalContext: CanvasRenderingContext2D = internalCanvas.getContext("2d");
           internalContext.drawImage(imageTexture, 0, 0); this.internalBuffer = internalContext.getImageData(0, 0, this.width, this.height);
       };
       imageTexture.src = filename;
   } // Takes the U & V coordinates exported by Blender
   // and return the corresponding pixel color in the texture public map(tu: number, tv: number): BABYLON.Color4 { if (this.internalBuffer) { // using a % operator to cycle/repeat the texture if needed var u = Math.abs(((tu * this.width) % this.width)) >> 0; var v = Math.abs(((tv * this.height) % this.height)) >> 0; var pos = (u + v * this.width) * 4; var r = this.internalBuffer.data[pos]; var g = this.internalBuffer.data[pos + 1]; var b = this.internalBuffer.data[pos + 2]; var a = this.internalBuffer.data[pos + 3]; return new BABYLON.Color4(r / 255.0, g / 255.0, b / 255.0, a / 255.0);
       } // Image is not loaded yet else { return new BABYLON.Color4(1, 1, 1, 1);
       }
   } 
```

```
var Texture = (function () { // Working with a fix sized texture (512x512, 1024x1024, etc.). function Texture(filename, width, height) { this.width = width; this.height = height; this.load(filename);
   }
    Texture.prototype.load = function (filename) { var _this = this; var imageTexture = new Image();
       imageTexture.height = this.height;
       imageTexture.width = this.width;
       imageTexture.onload = function () { var internalCanvas = document.createElement("canvas");
           internalCanvas.width = _this.width;
           internalCanvas.height = _this.height; var internalContext = internalCanvas.getContext("2d");
           internalContext.drawImage(imageTexture, 0, 0);
           _this.internalBuffer = internalContext.getImageData(0, 0, _this.width, _this.height);
       };
       imageTexture.src = filename;
   }; // Takes the U & V coordinates exported by Blender
   // and return the corresponding pixel color in the texture Texture.prototype.map = function (tu, tv) { if (this.internalBuffer) { // using a % operator to cycle/repeat the texture if needed var u = Math.abs(((tu * this.width) % this.width)) >> 0; var v = Math.abs(((tv * this.height) % this.height)) >> 0; var pos = (u + v * this.width) * 4; var r = this.internalBuffer.data[pos]; var g = this.internalBuffer.data[pos + 1]; var b = this.internalBuffer.data[pos + 2]; var a = this.internalBuffer.data[pos + 3]; return new BABYLON.Color4(r / 255.0, g / 255.0, b / 255.0, a / 255.0);
       } // Image is not loaded yet else { return new BABYLON.Color4(1, 1, 1, 1);
       }
   }; return Texture;
)();
oftEngine.Texture = Texture;
```

#### 在流中传递纹理信息

我不会深究每一个细节，因为您可以从下面下载完整的源代码。让我们全面回顾一下您需要做什么:

–向 ***网格*** 类添加一个纹理属性，并向 ***顶点*** 结构添加一个名为 ***纹理坐标*** 的 ***矢量 2*** 属性

–更新 ***ScanLineData*** 再嵌入 8 个浮点数/数字:每个顶点的 UV 坐标(ua，ub，uc，ud & va，vb，vc，vd)。

–更新 ***项目*** 方法/函数，返回一个新的*顶点*，其*纹理坐标*按原样传递(传递)

–将一个*纹理*对象作为最后一个参数传递给 ***进程扫描线*** ， ***绘制三角形*** 方法/函数

–用适当的 UV 坐标填充 *drawTriangle* 中新的 ***ScanLineData*** 结构

–**在 ***ProcessScanLine*** 上插值 UV** 得到 SU/SV&EU/EV(start U/start V/End U/End V)然后在 X 上插值 U，V，在纹理中找到与之对应的颜色。这个颜色纹理将与自然物体的颜色(在我们的教程中总是白色)混合，并用 NDotL 操作测量法线的光量。

**<u>注:</u>** 我们的*项目*方法可以看作是我们在 3D 硬件引擎中命名的一个 ***顶点着色器*** ，我们的 *ProcessScanLine* 可以看作是一个 ***像素着色器*** 。

我在本文中只分享新的 ***ProcessScanLine*** 方法，这是真正需要更新的主要部分:

*   [C#](#sample2csharp)
*   [打字稿](#sample2ts)
*   [JavaScript](#sample2js)

```
void ProcessScanLine(ScanLineData data, Vertex va, Vertex vb, Vertex vc, Vertex vd, Color4 color, Texture texture) Vector3 pa = va.Coordinates; Vector3 pb = vb.Coordinates; Vector3 pc = vc.Coordinates; Vector3 pd = vd.Coordinates; // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.Y != pb.Y ? (data.currentY - pa.Y) / (pb.Y - pa.Y) : 1; var gradient2 = pc.Y != pd.Y ? (data.currentY - pc.Y) / (pd.Y - pc.Y) : 1; int sx = (int)Interpolate(pa.X, pb.X, gradient1); int ex = (int)Interpolate(pc.X, pd.X, gradient2); // starting Z & ending Z float z1 = Interpolate(pa.Z, pb.Z, gradient1); float z2 = Interpolate(pc.Z, pd.Z, gradient2); // Interpolating normals on Y var snl = Interpolate(data.ndotla, data.ndotlb, gradient1); var enl = Interpolate(data.ndotlc, data.ndotld, gradient2); // Interpolating texture coordinates on Y var su = Interpolate(data.ua, data.ub, gradient1); var eu = Interpolate(data.uc, data.ud, gradient2); var sv = Interpolate(data.va, data.vb, gradient1); var ev = Interpolate(data.vc, data.vd, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++)
   { float gradient = (x - sx) / (float)(ex - sx); // Interpolating Z, normal and texture coordinates on X var z = Interpolate(z1, z2, gradient); var ndotl = Interpolate(snl, enl, gradient); var u = Interpolate(su, eu, gradient); var v = Interpolate(sv, ev, gradient); Color4 textureColor; if (texture != null)
           textureColor = texture.Map(u, v); else textureColor = new Color4(1, 1, 1, 1); // changing the native color value using the cosine of the angle
       // between the light vector and the normal vector
       // and the texture color DrawPoint(new Vector3(x, data.currentY, z), color * ndotl * textureColor);
   } 
```

```
public processScanLine(data: ScanLineData, va: Vertex, vb: Vertex, vc: Vertex, vd: Vertex, color: BABYLON.Color4, texture?: Texture): void { var pa = va.Coordinates; var pb = vb.Coordinates; var pc = vc.Coordinates; var pd = vd.Coordinates; // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (data.currentY - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (data.currentY - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // starting Z & ending Z var z1: number = this.interpolate(pa.z, pb.z, gradient1); var z2: number = this.interpolate(pc.z, pd.z, gradient2); // Interpolating normals on Y var snl = this.interpolate(data.ndotla, data.ndotlb, gradient1); var enl = this.interpolate(data.ndotlc, data.ndotld, gradient2); // Interpolating texture coordinates on Y var su = this.interpolate(data.ua, data.ub, gradient1); var eu = this.interpolate(data.uc, data.ud, gradient2); var sv = this.interpolate(data.va, data.vb, gradient1); var ev = this.interpolate(data.vc, data.vd, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++) { var gradient: number = (x - sx) / (ex - sx); // Interpolating Z, normal and texture coordinates on X var z = this.interpolate(z1, z2, gradient); var ndotl = this.interpolate(snl, enl, gradient); var u = this.interpolate(su, eu, gradient); var v = this.interpolate(sv, ev, gradient); var textureColor; if (texture)
           textureColor = texture.map(u, v); else textureColor = new BABYLON.Color4(1, 1, 1, 1); // changing the native color value using the cosine of the angle
       // between the light vector and the normal vector
       // and the texture color this.drawPoint(new BABYLON.Vector3(x, data.currentY, z), new BABYLON.Color4(color.r * ndotl * textureColor.r, 
                                          color.g * ndotl * textureColor.g, 
                                          color.b * ndotl * textureColor.b, 1));
   } 
```

```
Device.prototype.processScanLine = function (data, va, vb, vc, vd, color, texture) { var pa = va.Coordinates; var pb = vb.Coordinates; var pc = vc.Coordinates; var pd = vd.Coordinates; // Thanks to current Y, we can compute the gradient to compute others values like
   // the starting X (sx) and ending X (ex) to draw between
   // if pa.Y == pb.Y or pc.Y == pd.Y, gradient is forced to 1 var gradient1 = pa.y != pb.y ? (data.currentY - pa.y) / (pb.y - pa.y) : 1; var gradient2 = pc.y != pd.y ? (data.currentY - pc.y) / (pd.y - pc.y) : 1; var sx = this.interpolate(pa.x, pb.x, gradient1) >> 0; var ex = this.interpolate(pc.x, pd.x, gradient2) >> 0; // starting Z & ending Z var z1 = this.interpolate(pa.z, pb.z, gradient1); var z2 = this.interpolate(pc.z, pd.z, gradient2); // Interpolating normals on Y var snl = this.interpolate(data.ndotla, data.ndotlb, gradient1); var enl = this.interpolate(data.ndotlc, data.ndotld, gradient2); // Interpolating texture coordinates on Y var su = this.interpolate(data.ua, data.ub, gradient1); var eu = this.interpolate(data.uc, data.ud, gradient2); var sv = this.interpolate(data.va, data.vb, gradient1); var ev = this.interpolate(data.vc, data.vd, gradient2); // drawing a line from left (sx) to right (ex) for (var x = sx; x < ex; x++) { var gradient = (x - sx) / (ex - sx); // Interpolating Z, normal and texture coordinates on X var z = this.interpolate(z1, z2, gradient); var ndotl = this.interpolate(snl, enl, gradient); var u = this.interpolate(su, eu, gradient); var v = this.interpolate(sv, ev, gradient); var textureColor; if (texture)
           textureColor = texture.map(u, v); else textureColor = new BABYLON.Color4(1, 1, 1, 1); // changing the native color value using the cosine of the angle
       // between the light vector and the normal vector
       // and the texture color this.drawPoint(new BABYLON.Vector3(x, data.currentY, z), new BABYLON.Color4(color.r * ndotl * textureColor.r, 
                                          color.g * ndotl * textureColor.g, 
                                          color.b * ndotl * textureColor.b, 1));
   }
;
```

如果您已经按照之前的教程构建了自己的版本，请下载我的解决方案的代码，查看其他细微的修改以放入您自己的项目中。

#### 从 Babylon JSON 文件格式加载信息

为了能够有你在本文顶部看到的漂亮的渲染，你需要加载一个由[米歇尔·鲁索](https://twitter.com/rousseau_michel)修改的新版本的 Suzanne，并用 UV 坐标从 Blender 导出。为此，请下载这两个文件:

–设置了 UV 坐标的 Suzanne Blender 模型:[https://David . blob . core . windows . net/softengine 3d/part 6/monkey . Babylon](https://david.blob.core.windows.net/softengine3d/part6/monkey.babylon "https://david.blob.core.windows.net/softengine3d/part6/monkey.babylon")

–加载的 512×512 纹理图像:[https://David . blob . core . windows . net/softengine 3d/part 6/Suzanne . jpg](https://david.blob.core.windows.net/softengine3d/part6/Suzanne.jpg "https://david.blob.core.windows.net/softengine3d/part6/Suzanne.jpg")

巴比伦。David Catuhe 的 JSON 格式文件包含了很多我们在本系列中不会涉及的细节。举个例子，你可以玩的有趣的东西是材料。事实上，设计者可以将特定的材料分配给网格。在我们的例子中，我们只需要处理一个漫反射纹理。如果你想实现更多，可以看看 David Catuhe 的文章作为基础: [Babylon.js:为你的 Babylon.js 游戏释放标准素材](https://blogs.msdn.com/b/eternalcoding/archive/2013/07/01/babylon-js-unleash-the-standardmaterial-for-your-babylon-js-game.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2013/07/01/babylon-js-unleash-the-standardmaterial-for-your-babylon-js-game.aspx")

同样，我只与您分享要更改的主要部分:加载和解析 JSON 文件的方法/函数。

*   [C#](#sample3csharp)
*   [打字稿](#sample3ts)
*   [JavaScript](#sample3js)

```
// Loading the JSON file in an asynchronous manner public async Task<Mesh[]> LoadJSONFileAsync(string fileName) var meshes = new List<Mesh>(); var materials = new Dictionary<String,Material>(); var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(fileName); var data = await Windows.Storage.FileIO.ReadTextAsync(file); dynamic jsonObject = Newtonsoft.Json.JsonConvert.DeserializeObject(data); for (var materialIndex = 0; materialIndex < jsonObject.materials.Count; materialIndex++)
   { var material = new Material();
       material.Name = jsonObject.materials[materialIndex].name.Value;
       material.ID = jsonObject.materials[materialIndex].id.Value; if (jsonObject.materials[materialIndex].diffuseTexture != null)
           material.DiffuseTextureName = jsonObject.materials[materialIndex].diffuseTexture.name.Value;
        materials.Add(material.ID, material);
   } for (var meshIndex = 0; meshIndex < jsonObject.meshes.Count; meshIndex++)
   { var verticesArray = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray = jsonObject.meshes[meshIndex].indices; var uvCount = jsonObject.meshes[meshIndex].uvCount.Value; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch ((int)uvCount)
       { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.Count / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.Count / 3; var mesh = new Mesh(jsonObject.meshes[meshIndex].name.Value, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++)
       { var x = (float)verticesArray[index * verticesStep].Value; var y = (float)verticesArray[index * verticesStep + 1].Value; var z = (float)verticesArray[index * verticesStep + 2].Value; // Loading the vertex normal exported by Blender var nx = (float)verticesArray[index * verticesStep + 3].Value; var ny = (float)verticesArray[index * verticesStep + 4].Value; var nz = (float)verticesArray[index * verticesStep + 5].Value;
            mesh.Vertices[index] = new Vertex {
               Coordinates = new Vector3(x, y, z),
               Normal = new Vector3(nx, ny, nz)
           }; if (uvCount > 0)
           { // Loading the texture coordinates float u = (float)verticesArray[index * verticesStep + 6].Value; float v = (float)verticesArray[index * verticesStep + 7].Value;
               mesh.Vertices[index].TextureCoordinates = new Vector2(u, v);
           }
       } // Then filling the Faces array for (var index = 0; index < facesCount; index++)
       { var a = (int)indicesArray[index * 3].Value; var b = (int)indicesArray[index * 3 + 1].Value; var c = (int)indicesArray[index * 3 + 2].Value;
           mesh.Faces[index] = new Face { A = a, B = b, C = c };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new Vector3((float)position[0].Value, (float)position[1].Value, (float)position[2].Value); if (uvCount > 0)
       { // Texture var meshTextureID = jsonObject.meshes[meshIndex].materialId.Value; var meshTextureName = materials[meshTextureID].DiffuseTextureName;
           mesh.Texture = new Texture(meshTextureName, 512, 512);
       }
        meshes.Add(mesh);
   } return meshes.ToArray(); 
```

```
private CreateMeshesFromJSON(jsonObject): Mesh[] { var meshes: Mesh[] = []; var materials: Material[] = []; for (var materialIndex = 0; materialIndex < jsonObject.materials.length; materialIndex++) { var material: Material = {};
        material.Name = jsonObject.materials[materialIndex].name;
       material.ID = jsonObject.materials[materialIndex].id; if (jsonObject.materials[materialIndex].diffuseTexture)
           material.DiffuseTextureName = jsonObject.materials[materialIndex].diffuseTexture.name;
        materials[material.ID] = material;
   } for (var meshIndex = 0; meshIndex < jsonObject.meshes.length; meshIndex++) { var verticesArray: number[] = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray: number[] = jsonObject.meshes[meshIndex].indices; var uvCount: number = jsonObject.meshes[meshIndex].uvCount; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch (uvCount) { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.length / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.length / 3; var mesh = new SoftEngine.Mesh(jsonObject.meshes[meshIndex].name, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++) { var x = verticesArray[index * verticesStep]; var y = verticesArray[index * verticesStep + 1]; var z = verticesArray[index * verticesStep + 2]; // Loading the vertex normal exported by Blender var nx = verticesArray[index * verticesStep + 3]; var ny = verticesArray[index * verticesStep + 4]; var nz = verticesArray[index * verticesStep + 5];
            mesh.Vertices[index] = {
               Coordinates: new BABYLON.Vector3(x, y, z),
               Normal: new BABYLON.Vector3(nx, ny, nz)
           }; if (uvCount > 0) { // Loading the texture coordinates var u = verticesArray[index * verticesStep + 6]; var v = verticesArray[index * verticesStep + 7];
               mesh.Vertices[index].TextureCoordinates = new BABYLON.Vector2(u, v);
           } else {
               mesh.Vertices[index].TextureCoordinates = new BABYLON.Vector2(0, 0);
           }
       } // Then filling the Faces array for (var index = 0; index < facesCount; index++) { var a = indicesArray[index * 3]; var b = indicesArray[index * 3 + 1]; var c = indicesArray[index * 3 + 2];
           mesh.Faces[index] = {
               A: a,
               B: b,
               C: c
           };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new BABYLON.Vector3(position[0], position[1], position[2]); if (uvCount > 0) { var meshTextureID = jsonObject.meshes[meshIndex].materialId; var meshTextureName = materials[meshTextureID].DiffuseTextureName;
           mesh.Texture = new Texture(meshTextureName, 512, 512);
       }
        meshes.push(mesh);
   } return meshes; 
```

```
Device.prototype.CreateMeshesFromJSON = function (jsonObject) { var meshes = []; var materials = []; for (var materialIndex = 0; materialIndex < jsonObject.materials.length; materialIndex++) { var material = {};
        material.Name = jsonObject.materials[materialIndex].name;
       material.ID = jsonObject.materials[materialIndex].id; if (jsonObject.materials[materialIndex].diffuseTexture)
           material.DiffuseTextureName = jsonObject.materials[materialIndex].diffuseTexture.name;
        materials[material.ID] = material;
   } for (var meshIndex = 0; meshIndex < jsonObject.meshes.length; meshIndex++) { var verticesArray = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray = jsonObject.meshes[meshIndex].indices; var uvCount = jsonObject.meshes[meshIndex].uvCount; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch (uvCount) { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.length / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.length / 3; var mesh = new SoftEngine.Mesh(jsonObject.meshes[meshIndex].name, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++) { var x = verticesArray[index * verticesStep]; var y = verticesArray[index * verticesStep + 1]; var z = verticesArray[index * verticesStep + 2]; // Loading the vertex normal exported by Blender var nx = verticesArray[index * verticesStep + 3]; var ny = verticesArray[index * verticesStep + 4]; var nz = verticesArray[index * verticesStep + 5];
            mesh.Vertices[index] = {
               Coordinates: new BABYLON.Vector3(x, y, z),
               Normal: new BABYLON.Vector3(nx, ny, nz)
           }; if (uvCount > 0) { // Loading the texture coordinates var u = verticesArray[index * verticesStep + 6]; var v = verticesArray[index * verticesStep + 7];
               mesh.Vertices[index].TextureCoordinates = new BABYLON.Vector2(u, v);
           } else {
               mesh.Vertices[index].TextureCoordinates = new BABYLON.Vector2(0, 0);
           }
       } // Then filling the Faces array for (var index = 0; index < facesCount; index++) { var a = indicesArray[index * 3]; var b = indicesArray[index * 3 + 1]; var c = indicesArray[index * 3 + 2];
           mesh.Faces[index] = {
               A: a,
               B: b,
               C: c
           };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new BABYLON.Vector3(position[0], position[1], position[2]); if (uvCount > 0) { var meshTextureID = jsonObject.meshes[meshIndex].materialId; var meshTextureName = materials[meshTextureID].DiffuseTextureName;
           mesh.Texture = new Texture(meshTextureName, 512, 512);
       }
        meshes.push(mesh);
   } return meshes;
;
```

多亏了所有这些修改，我们现在有了这张漂亮的渲染图，展示了使用 gouraud 着色算法的 Suzanne 纹理:

[![image](img/b6325caa531dfb1516f6cba29d8acb5c.png "image")](https://david.blob.core.windows.net/softengine3d/part6sample1/index.html "view Suzanne textured with a gouraud shading in HTML5 in your browser")

3D 软件引擎:[在你的浏览器中用 HTML5 查看带有 gouraud 阴影的 Suzanne 纹理](https://david.blob.core.windows.net/softengine3d/part6sample1/index.html)

你可以**在这里下载实现这个纹理映射算法的解决方案**:

–**c#**:[软化器部件 6 sample 1 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart6Sample1.zip)

–**类型脚本** : [软化器部件 6 sample 1 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart6Sample1.zip)

–**JavaScript**:[软化器部件 6Sample1.zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart6Sample1.zip) 或者直接在上面的【HTML5 演示上点击右键->查看源代码

性能并不巨大。我在我的机器上以平均 18 fps 的速度运行 1600×900 的 C#版本，在 IE11 中以平均 15 fps 的速度运行 640×480 的 HTML5 版本。

但在请求 GPU 的帮助之前，让我们看看你的 3D 软件引擎的最终优化。

## 背面剔除

让我们从阅读维基百科中的定义开始:[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling "https://en.wikipedia.org/wiki/Back-face_culling"):[*中的*计算机图形学**](https://en.wikipedia.org/wiki/Computer_graphics) *，**背面剔除**确定图形对象的* [*多边形*](https://en.wikipedia.org/wiki/Polygon) *是否可见< … >实现背面剔除的一种方法是丢弃* [*点积的所有多边形*](https://en.wikipedia.org/wiki/Dot_product)

我们的想法是，在 JSON 加载阶段，使用前一教程中用于平面着色的相同算法，预先计算网格的每个曲面法线。一旦完成，在*渲染*方法/函数中，我们将把表面法线的坐标转换成世界视图(相机看到的世界)并检查它的 Z 值。如果它是> = 0，我们根本不会画三角形，因为这意味着从相机的视角看不到这个面。

3D 软件引擎:[在启用背面剔除的 HTML5 中查看带有 gouraud 阴影的 Suzanne 纹理](https://david.blob.core.windows.net/softengine3d/part6sample2/index.html)

您可以**在此下载实现该背面算法的解决方案**:

–**c#**:[软化器部件 6 sample 2 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart6Sample2.zip)

–**类型脚本** : [软化器部件 6 sample 2 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart6Sample2.zip)

–**JavaScript**:[软化器部件 6Sample2.zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart6Sample2.zip) 或者直接在上面的【HTML5 演示上点击右键->查看源代码

**<u>注意:</u>** 你会注意到我在我的背面剔除实现中有一个小的渲染错误。极少数三角形没有画出来，而它们应该画出来。这是因为我们应该调整法线的变换，以考虑相机的当前视角。当前的算法假设我们有一个正交的摄像机，但事实并非如此。这对你来说是一个很好的练习。:)

**性能提升很有趣，大约是 66%** ，因为我在启用背面剔除的情况下，从 IE11 中的平均 15 fps 切换到 25 fps。

## 感谢巴比伦用 WebGL 渲染。射流研究…

今天的现代 3D 游戏当然是使用 GPU。这个系列的目的是通过构建你自己的 3D 软件引擎来理解 3D 的基础知识。一旦你能够理解这个系列的 6 个部分，使用 OpenGL/WebGL 或 DirectX 进入 3D 引擎将会容易得多。

在我们这边，我们一直在法国开发一套框架，让开发者以一种非常简单的方式构建 HTML5 3D 游戏。第一步是释放巴比伦。大卫·卡图赫建造的 JS。但是我们正在他的令人敬畏的 3D 引擎之上开发其他酷的框架来帮助你构建你的 WebGL 游戏。

David 在他的博客上开始了一系列关于如何使用 3D WebGL 引擎的教程。切入点在这里: [Babylon.js:用 HTML 5 和 WebGL 构建 3D 游戏的完整 JavaScript 框架](https://blogs.msdn.com/b/eternalcoding/archive/2013/06/27/babylon-js-a-complete-javascript-framework-for-building-3d-games-with-html-5-and-webgl.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2013/06/27/babylon-js-a-complete-javascript-framework-for-building-3d-games-with-html-5-and-webgl.aspx")

通过学习本教程: [Babylon.js:如何加载使用 Blender](https://blogs.msdn.com/b/eternalcoding/archive/2013/06/28/babylon-js-how-to-load-a-babylon-file-produced-with-blender.aspx "https://blogs.msdn.com/b/eternalcoding/archive/2013/06/28/babylon-js-how-to-load-a-babylon-file-produced-with-blender.aspx") 生成的. Babylon 文件，您将能够重新加载本系列中使用的网格，并受益于浏览器中的 GPU 硬件加速！

如果你有 IE11、Chrome 或 Firefox 或任何兼容 WebGL 的设备/浏览器，你可以在这里测试结果:

[![image](img/3a6b0d07ea4232de8d951bc347930e85.png "image")](https://david.blob.core.windows.net/softengine3d/part6webgl/index.html)

巴比伦。JS–3D web GL 引擎:[查看 Suzanne 纹理和硬件加速！](https://david.blob.core.windows.net/softengine3d/part6webgl/index.html)

多亏了 WebGL，我们的性能大幅提升。例如，在我的在 Windows 8.1 预览版中更新的 **Surface RT 上，使用 IE11，我正在**用我的 3D 软引擎从 640×480 的不到 4 fps 切换到 1366×768 的 60 FPS**！**

这个系列现在完成了。我很高兴能写下它。我已经收到了很多很棒的反馈，你们中的一些人已经把这个系列移植到 Java(由 Yannick Comte 开发)、Windows CE 和 WPF 了！我很高兴看到它对你们中的一些人有用，并发现了代码的分叉。欢迎在评论中分享你自己的观点。

我将很快写一个新的系列教程，内容是我们目前正在开发的构建 3D 游戏的框架。敬请期待！

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/07/18/tutorial-part-6-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-texture-mapping-back-face-culling-amp-web GL . aspx](https://blogs.msdn.com/b/davrous/archive/2013/07/18/tutorial-part-6-learning-how-to-write-a-3d-software-engine-in-c-ts-or-js-texture-mapping-back-face-culling-amp-webgl.aspx)。经作者许可，在此转载。*

## 分享这篇文章