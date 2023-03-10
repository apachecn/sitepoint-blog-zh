# 从头开始编写 3D 软引擎:第 3 部分

> 原文：<https://www.sitepoint.com/write-3d-soft-engine-from-scratch-part-3/>

在[之前的教程](https://www.sitepoint.com/write-3d-soft-engine-scratch-part-2/)中，我们学习了如何绘制线条&三角形，由于这种线框渲染，我们真正开始看到网格的 3D 面。但是我们只展示了一个立方体…甚至一个简单的立方体已经有 12 个面了！我们会被迫这样处理更复杂物体的所有面吗？希望不会。

**3D 建模师帮助 3D 设计者和开发者之间的合作**。设计师可以使用其最喜欢的工具来构建他的场景或网格(3D Studio Max，Maya，Blender 等)。).然后，他将他的工作导出到一个文件中，该文件将由开发人员加载。开发人员将最终把网格推入他的实时 3D 引擎。市场上有几种文件格式可以用来序列化艺术家完成的工作。在我们的例子中，我们将使用 JSON。事实上，[大卫·卡图赫](https://blogs.msdn.com/eternalcoding)已经为 Blender 做了一个导出库，输出一个**。巴比伦文件使用 JSON** 。然后，我们将看到如何解析该文件，并在我们可爱的软引擎中显示网格。

Blender 是一款免费的 3D 建模软件，你可以在这里下载:[https://www.blender.org/download/get-blender/](https://www.blender.org/download/get-blender/ "https://www.blender.org/download/get-blender/")

可以用 Python 写插件。这就是我们为出口商所做的。

通过遵循本系列教程，您将能够获得这样的结果:

[https://david.blob.core.windows.net/softengine3d/part3/index.html](https://david.blob.core.windows.net/softengine3d/part3/index.html)

您将会看到，您已经完成了前两个教程中的大部分工作。

## 安装 Babylon exporter，用 Blender 生成你自己的场景

一旦你安装了 Blender，请从这里下载我们的 Babylon exporter:[io _ export _ Babylon . py](https://david.blob.core.windows.net/softengine3d/io_export_babylon.py)

将该文件复制到安装 Blender 的 ***\script\addons*** 目录下(例如我的具体例子中的“*C:\ Program Files \ Blender Foundation \ Blender \ 2.67 \ scripts \ addons*”)。

您需要在用户首选项中激活我们的插件。进入“ ***文件***”–>***用户首选项*** ”和“ ***插件*** ”选项卡。搜索“ ***巴比伦*** ”，勾选案例激活。

[![image](img/6fa8d84b1e64772f0a55abd5eba2cd71.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/2671.image_5F00_01DE239B.png)

用搅拌机做你想做的。如果你像我一样，真的不擅长构建 3D 网格，这里有一个很酷的选项，会在极客聚会上给你的朋友留下深刻印象:“ ***添加***”–>***网格***”–>***猴子*** ”:

[![image](img/06b4ca6302c3ed2216b0c5659ceda791.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/0435.image_5F00_65ECEEA2.png)

然后，您应该会看到这样一个屏幕:

[![image](img/df7387627501fdb0515cdbd03c9e5e54.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/0880.image_5F00_561D33D1.png)

最后一步是将它导出到。巴比伦文件格式(我们的 JSON 文件)。"**文件**"–>"**出口**"–>"**Babylon . js**"

[![image](img/74f04bc542b2089580c4adf7129e7a56.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/7444.image_5F00_21D8E4BE.png)

将文件命名为“*”。*

 ***<u>注:</u>** 这只猴子名叫 Suzanne，在 3D/游戏界非常知名。通过认识她，你现在是这个酷社区的骄傲成员了！欢迎登机！；)

## 加载导出的 JSON 文件并显示其网格

正如我在本文开始时告诉你的，我们已经建立了所有需要的逻辑来显示更复杂的网格，如 Suzanne。我们有我们的脸，网格和顶点逻辑。这就是我们目前所需要的。

在 JSON 格式中，Babylon exporter 添加了比我们当前需要的更多的细节。例如，它还添加了纹理、灯光等潜在的细节。这就是为什么，我们将解析文件并直接跳转到我们感兴趣的区域:顶点和面的索引来构建我们的线框渲染。

**<u>注意:</u>** 对于 **C#** 开发者来说，你需要像我们在第一个教程中做的那样，通过 nuGet 从 Newtonsoft 安装**Json.NET**来添加 SharpDX。事实上，JSON 解析在。NET 就像在使用 JavaScript 的浏览器中一样。

让我们从在**设备**对象中添加加载逻辑开始:

*   [C#](#loadjsoncsharp)
*   [打字稿](#loadjsonts)
*   [JavaScript](#loadjsonjs)

```
// Loading the JSON file in an asynchronous manner public async Task<Mesh[]> LoadJSONFileAsync(string fileName) var meshes = new List<Mesh>(); var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(fileName); var data = await Windows.Storage.FileIO.ReadTextAsync(file); dynamic jsonObject = Newtonsoft.Json.JsonConvert.DeserializeObject(data); for (var meshIndex = 0; meshIndex < jsonObject.meshes.Count; meshIndex++)
   { var verticesArray = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray = jsonObject.meshes[meshIndex].indices; var uvCount = jsonObject.meshes[meshIndex].uvCount.Value; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch ((int)uvCount)
       { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.Count / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.Count / 3; var mesh = new Mesh(jsonObject.meshes[meshIndex].name.Value, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++)
       { var x = (float)verticesArray[index * verticesStep].Value; var y = (float)verticesArray[index * verticesStep + 1].Value; var z = (float)verticesArray[index * verticesStep + 2].Value;
           mesh.Vertices[index] = new Vector3(x, y, z);
       } // Then filling the Faces array for (var index = 0; index < facesCount; index++)
       { var a = (int)indicesArray[index * 3].Value; var b = (int)indicesArray[index * 3 + 1].Value; var c = (int)indicesArray[index * 3 + 2].Value;
           mesh.Faces[index] = new Face { A = a, B = b, C = c };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new Vector3((float)position[0].Value, (float)position[1].Value, (float)position[2].Value);
       meshes.Add(mesh);
   } return meshes.ToArray(); 
```

```
// Loading the JSON file in an asynchronous manner and
/ calling back with the function passed providing the array of meshes loaded public LoadJSONFileAsync(fileName: string, callback: (result: Mesh[]) => any): void { var jsonObject = {}; var xmlhttp = new XMLHttpRequest();
   xmlhttp.open("GET", fileName, true); var that = this;
   xmlhttp.onreadystatechange = function () { if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
           jsonObject = JSON.parse(xmlhttp.responseText);
           callback(that.CreateMeshesFromJSON(jsonObject));
       }
   };
   xmlhttp.send(null); private CreateMeshesFromJSON(jsonObject): Mesh[] { var meshes: Mesh[] = []; for (var meshIndex = 0; meshIndex < jsonObject.meshes.length; meshIndex++) { var verticesArray: number[] = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray: number[] = jsonObject.meshes[meshIndex].indices; var uvCount: number = jsonObject.meshes[meshIndex].uvCount; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch (uvCount) { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.length / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.length / 3; var mesh = new SoftEngine.Mesh(jsonObject.meshes[meshIndex].name, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++) { var x = verticesArray[index * verticesStep]; var y = verticesArray[index * verticesStep + 1]; var z = verticesArray[index * verticesStep + 2];
           mesh.Vertices[index] = new BABYLON.Vector3(x, y, z);
       } // Then filling the Faces array for (var index = 0; index < facesCount; index++) { var a = indicesArray[index * 3]; var b = indicesArray[index * 3 + 1]; var c = indicesArray[index * 3 + 2];
           mesh.Faces[index] = {
               A: a,
               B: b,
               C: c
           };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new BABYLON.Vector3(position[0], position[1], position[2]);
       meshes.push(mesh);
   } return meshes; 
```

```
// Loading the JSON file in an asynchronous manner and
/ calling back with the function passed providing the array of meshes loaded Device.prototype.LoadJSONFileAsync = function (fileName, callback) { var jsonObject = {}; var xmlhttp = new XMLHttpRequest();
   xmlhttp.open("GET", fileName, true); var that = this;
   xmlhttp.onreadystatechange = function () { if(xmlhttp.readyState == 4 && xmlhttp.status == 200) {
           jsonObject = JSON.parse(xmlhttp.responseText);
           callback(that.CreateMeshesFromJSON(jsonObject));
       }
   };
   xmlhttp.send(null);
;
evice.prototype.CreateMeshesFromJSON = function (jsonObject) { var meshes = []; for(var meshIndex = 0; meshIndex < jsonObject.meshes.length; meshIndex++) { var verticesArray = jsonObject.meshes[meshIndex].vertices; // Faces var indicesArray = jsonObject.meshes[meshIndex].indices; var uvCount = jsonObject.meshes[meshIndex].uvCount; var verticesStep = 1; // Depending of the number of texture's coordinates per vertex
       // we're jumping in the vertices array  by 6, 8 & 10 windows frame switch(uvCount) { case 0:
               verticesStep = 6; break; case 1:
               verticesStep = 8; break; case 2:
               verticesStep = 10; break;
       } // the number of interesting vertices information for us var verticesCount = verticesArray.length / verticesStep; // number of faces is logically the size of the array divided by 3 (A, B, C) var facesCount = indicesArray.length / 3; var mesh = new SoftEngine.Mesh(jsonObject.meshes[meshIndex].name, verticesCount, facesCount); // Filling the Vertices array of our mesh first for (var index = 0; index < verticesCount; index++) { var x = verticesArray[index * verticesStep]; var y = verticesArray[index * verticesStep + 1]; var z = verticesArray[index * verticesStep + 2];
           mesh.Vertices[index] = new BABYLON.Vector3(x, y, z);
       } // Then filling the Faces array for(var index = 0; index < facesCount; index++) { var a = indicesArray[index * 3]; var b = indicesArray[index * 3 + 1]; var c = indicesArray[index * 3 + 2];
           mesh.Faces[index] = {
               A: a,
               B: b,
               C: c
           };
       } // Getting the position you've set in Blender var position = jsonObject.meshes[meshIndex].position;
       mesh.Position = new BABYLON.Vector3(position[0], position[1], position[2]);
       meshes.push(mesh);
   } return meshes;
;
```

你可能想知道为什么我们在顶点数组中跳跃 6，8 & 10 来获取我们顶点的 3D 坐标(X，Y，Z)。再次，这是因为巴比伦出口商增加了更多的细节，我们目前需要我们的线框渲染。这就是为什么，**我们使用这种框架方法过滤那些细节**。这个逻辑是特定于我们的文件格式的。如果您想从 other(比如 three.js)加载导出，您只需确定在哪里检索另一种文件格式的顶点和面索引。

**<u>注:</u>** 能够装载我们的。巴比伦文件， **TypeScript/JavaScript** 开发者，你需要定义一个新的 MIME 类型的“ **application/babylon** ”目标扩展”。巴比伦”。在 IIS 中，您需要在您的 **web.config** 中声明它:

```
 <system.webServer>
   <staticContent>
     <mimeMap fileExtension=".babylon" mimeType="application/babylon" />
   </staticContent>
 </system.webServer>
```

对于开发人员来说，您需要更改您将包含在解决方案中的文件的属性。将“*构建动作*切换到“*内容*，并始终复制到输出目录:

[![image](img/7f04297d0ba5fd37af21710c6d4f1f55.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/8103.image_5F00_52D7D3C4.png)

否则，将找不到该文件。

最后，我们现在需要更新 main 函数的等效函数来调用这个新的 LoadJSONFileAsync 函数，而不是手动创建我们的多维数据集。因为我们还可能有几个网格要制作动画，所以我们还需要在每次滴答时改变每个网格的旋转值:

*   [C#](#maincsharp)
*   [打字稿](#maints)
*   [JavaScript](#mainjs)

```
private Device device; Mesh[] meshes; Camera mera = new Camera(); private async void Page_Loaded(object sender, RoutedEventArgs e) // Choose the back buffer resolution here WriteableBitmap bmp = new WriteableBitmap(640, 480); // Our Image XAML control frontBuffer.Source = bmp;

   device = new Device(bmp);
   meshes = await device.LoadJSONFileAsync("monkey.babylon");
   mera.Position = new Vector3(0, 0, 10.0f);
   mera.Target = Vector3.Zero; // Registering to the XAML rendering loop CompositionTarget.Rendering += CompositionTarget_Rendering; // Rendering loop handler void CompositionTarget_Rendering(object sender, object e)
   device.Clear(0, 0, 0, 255); foreach (var mesh in meshes) { // rotating slightly the meshes during each frame rendered mesh.Rotation = new Vector3(mesh.Rotation.X + 0.01f, mesh.Rotation.Y + 0.01f, mesh.Rotation.Z);
   } // Doing the various matrix operations device.Render(mera, meshes); // Flushing the back buffer into the front buffer device.Present(); 
```

```
///<reference path="SoftEngine.ts"/> var canvas: HTMLCanvasElement; var device: SoftEngine.Device; var meshes: SoftEngine.Mesh[] = []; var mera: SoftEngine.Camera;
document.addEventListener("DOMContentLoaded", init, false); function init() {
   canvas = <HTMLCanvasElement> document.getElementById("frontBuffer");
   mera = new SoftEngine.Camera();
   device = new SoftEngine.Device(canvas); 
    mera.Position = new BABYLON.Vector3(0, 0, 10);
   mera.Target = new BABYLON.Vector3(0, 0, 0);
    device.LoadJSONFileAsync("monkey.babylon", loadJSONCompleted) function loadJSONCompleted(meshesLoaded: SoftEngine.Mesh[]) {
   meshes = meshesLoaded; // Calling the HTML5 rendering loop requestAnimationFrame(drawingLoop); // Rendering loop handler function drawingLoop() {
   device.clear(); for (var i = 0; i < meshes.length; i++) { // rotating slightly the mesh during each frame rendered meshes[i].Rotation.x += 0.01;
       meshes[i].Rotation.y += 0.01;
   } // Doing the various matrix operations device.render(mera, meshes); // Flushing the back buffer into the front buffer device.present(); // Calling the HTML5 rendering loop recursively requestAnimationFrame(drawingLoop); 
```

```
var canvas; var device; var meshes = []; var mera;
document.addEventListener("DOMContentLoaded", init, false); function init() {
   canvas = document.getElementById("frontBuffer");
   mera = new SoftEngine.Camera();
   device = new SoftEngine.Device(canvas);
   mera.Position = new BABYLON.Vector3(0, 0, 10);
   mera.Target = new BABYLON.Vector3(0, 0, 0);
   device.LoadJSONFileAsync("monkey.babylon", loadJSONCompleted); function loadJSONCompleted(meshesLoaded) {
   meshes = meshesLoaded; // Calling the HTML5 rendering loop requestAnimationFrame(drawingLoop); // Rendering loop handler function drawingLoop() {
   device.clear(); for (var i = 0; i < meshes.length; i++) { // rotating slightly the mesh during each frame rendered meshes[i].Rotation.x += 0.01;
       meshes[i].Rotation.y += 0.01;
   } // Doing the various matrix operations device.render(mera, meshes); // Flushing the back buffer into the front buffer device.present(); // Calling the HTML5 rendering loop recursively requestAnimationFrame(drawingLoop); 
```

你现在应该有一个 3D 引擎，能够加载由 Blender 导出的网格，并在线框渲染模式下制作动画！我不知道你怎么想，但我非常兴奋能达到这个阶段。:)

同样，您可以**下载包含源代码的解决方案**:

–**c#**:[softenginecsharpart 3 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineCSharpPart3.zip)

–**TypeScript**:[softenginetspart 3 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineTSPart3.zip)

–**JavaScript**:[softenginejspart 3 . zip](https://david.blob.core.windows.net/softengine3d/SoftEngineJSPart3.zip)或者只需在第一个嵌入的 iframe 上单击右键->查看源代码

那么，下一步是什么？嗯，我们需要**填充三角形**。这被称为**光栅化**。我们还将处理一个我们称之为**的 Z 缓冲器**，以获得正确的渲染。在下一个教程中，您将学习如何获得类似的东西:

[![image](img/0dc6af94ad671d59bcc3d4507de1a514.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/4846.image_5F00_333936D6.png)

我们将用随机的颜色填充三角形。第四节教程见。

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/06/17/tutorial-part-3-learning-how-to-write-a-3d-soft-engine-in-c-ts-or-js-loading-meshs-exported-from-blender . aspx](https://blogs.msdn.com/b/davrous/archive/2013/06/17/tutorial-part-3-learning-how-to-write-a-3d-soft-engine-in-c-ts-or-js-loading-meshes-exported-from-blender.aspx)。经作者许可，在此转载。*

## 分享这篇文章*