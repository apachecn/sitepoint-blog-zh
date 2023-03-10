# Three.js 和 Babylon . js:web GL 框架的比较

> 原文：<https://www.sitepoint.com/three-js-babylon-js-comparison-webgl-frameworks/>

自蒂姆·伯纳斯·李爵士和他的 Nexus 软件时代以来，今天的网络浏览器已经走过了漫长的道路。得益于 WebGL 等出色的 JavaScript APIs，现代浏览器完全能够在没有第三方插件帮助的情况下呈现高级 2D 和 3D 图形。通过利用专用图形处理器的马力，WebGL 使我们的网页能够访问动态阴影和真实的物理效果。

您可能已经猜到，这种强大的 API 通常都有一个缺点。WebGL 当然也不例外，它的缺点是复杂性。不过，不要担心，因为我们已经探索了两个完全有能力的框架，旨在使您的生活更容易，甚至可能在使用 WebGL 时更有成效。

## 3D 框架的卑微起源

曾经流行的 Three.js 和更新的 T2 Babylon . js 为 web 开发者提供了一个抽象的基础，来制作功能丰富的 WebGL 作品，从动画标志到完全互动的 3D 游戏。

Three.js 始于 2009 年 4 月，最初是用 ActionScript 编写的，后来被翻译成 JavaScript。Three.js 是在引入 WebGL 之前创建的，具有模块化渲染接口的独特便利性，除了 WebGL 之外，还允许它与 SVG 和 HTML5 的 canvas 元素一起使用。

Babylon.js 是相对较新的网站，在 2013 年夏天出现。Babylon.js 是由微软的 minds 为您带来的，它是与 Internet Explorer 11 对 WebGL API 的首次官方支持一起推出的。尽管来自雷德蒙的实验室，Babylon.js(以及 Three.js)仍然拥有开源许可。

## 设计上的细微差别

Three.js 和 Babylon.js 都提供了易于使用的库来处理 WebGL 动画的复杂性。

遵循动画的场景、渲染器、相机、对象模型，这些框架发现自己使用类似的方法来使用 WebGL。在 HTML 中使用这两种方法都很简单，只需一行脚本语句链接各自的 JavaScript 文件。注意:Babylon.js 有一些依赖项，需要包含开源的 Hand.js。

Three.js:

```
<script src="three.js"></script>
```

巴比伦. js:

```
<script src="babylon.js"></script>
<script src="hand.js"></script>
```

两者的主要区别在于它们的预期用途。虽然这两个框架中的任何一个都可能被硬塞进创建相同的 3D 动画中，但了解每个框架的创建目的是什么是很重要的。

创建 Three.js 的目的只有一个:利用基于 web 的渲染器来创建 GPU 增强的 3D 图形和动画。因此，这个框架采用了一种非常广泛的方法来处理网络图形，而不是专注于任何单一的动画领域。

这种灵活的设计使 Three.js 成为通用 web 动画(如徽标或建模应用程序)的一个很好的工具(很好的例子可以在[这里](http://threejs.org/examples/)找到)。

Three.js 试图为 WebGL 表带来广泛的动画特性，Babylon.js 则采取了更有针对性的方法。最初设计为 Silverlight 游戏引擎，Babylon.js 保持了其对基于 web 的游戏开发的偏好，具有碰撞检测和抗锯齿等功能。如前所述，Babylon.js 仍然完全有能力制作一般的网页图形和动画，在其[网站](http://www.babylonjs.com/)首页上的演示就是证明。

## WebGL 技术的并行演示

为了进一步展示这两个框架的相似之处和不同之处，让我们构建一个快速的 3D 动画。我们选择的项目将是一个超级简单的立方体，并缓慢旋转。在创建这两个示例项目时，您应该开始理解这两种技术是如何逐渐产生分歧并展示它们独特的优势的。就这样，让我们开始吧。

当构建任何类型的创意项目时，首要任务是初始化一个空白画布，在其中包含我们的 3D 动画。

Three.js:

```
<div style="height:250px; width: 250px;" id="three"></div>
```

```
var div = document.getElementById('three');
```

巴比伦. js:

```
<div style="height:250px; width: 250px;" id="babylon">
<canvas id="babylonCanvas"></canvas></div>
```

```
var canvas = document.getElementById('babylonCanvas');
```

使用 Three.js，我们只需创建一个空的 div 作为动画容器。另一方面，Babylon.js 使用明确定义的 HTML5 画布来保存其 3D 图形。

接下来，我们加载渲染器，它将负责准备场景并绘制到画布上。

Three.js:

```
var renderer = new THREE.WebGLRenderer();
renderer.setSize(width, height);
div.appendChild(renderer.domElement);
```

巴比伦. js:

```
var engine = new BABYLON.Engine(canvas, true);
```

这里没有什么太花哨的东西，我们只是初始化渲染器(或者 Babylon.js 中的引擎)并将它们附加到画布上。

我们的下一步变得更加复杂，因为我们设置了一个场景来放置我们的相机和立方体。

Three.js:

```
var sceneT = new THREE.Scene();
var camera = new THREE.PerspectiveCamera(70, width / height, 1, 1000);
camera.position.z = 400;
```

巴比伦. js:

```
var sceneB = new BABYLON.Scene(engine);
var camera = new BABYLON.ArcRotateCamera
("camera", 1, 0.8, 10, new BABYLON.Vector3(0, 0, 0), sceneB);
sceneB.activeCamera.attachControl(canvas);

 var light = new BABYLON.DirectionalLight
("light", new BABYLON.Vector3(0, -1, 0), sceneB);
light.diffuse = new BABYLON.Color3(1, 0, 0);
light.specular = new BABYLON.Color3(1, 1, 1);
```

在这里，我们以几乎相同的方式创建我们的场景，然后实现摄像机(两个框架都支持不同的类型),我们将从摄像机中实际查看创建的场景。传递给相机的参数规定了关于相机视角的各种细节，如视野、纵横比和深度。

我们还为 Babylon.js 添加了一个方向灯，并将其附加到我们的场景中，以避免以后看到一片漆黑的动画。

画布、场景和摄像机都设置好了，我们只需要在渲染和制作动画之前画出立方体本身。

Three.js:

```
var cube = new THREE.CubeGeometry(100, 100, 100);

var texture = THREE.ImageUtils.loadTexture('texture.gif');
texture.anisotropy = renderer.getMaxAnisotropy();

var material = new THREE.MeshBasicMaterial({ map: texture });
var mesh = new THREE.Mesh(cube, material);
sceneT.add(mesh);
```

巴比伦. js:

```
var box = BABYLON.Mesh.CreateBox("box", 3.0, sceneB);
var material = new BABYLON.StandardMaterial("texture", sceneB);

box.material = material;
material.diffuseTexture = new BABYLON.Texture("texture.gif", sceneB);
```

首先，我们创建指定大小的立方体对象，然后创建将被绘制到立方体上的材质/网格(想想纹理)。任何图像文件都可以用于纹理，两个框架都支持从 3D 建模工具如 [Blender](http://www.blender.org/) 中导出网格。

在最后一步，我们为动画应用一个柔和的旋转，然后渲染场景。

Three.js:

```
function animate() {
     requestAnimationFrame(animate);
     mesh.rotation.x += 0.005;
     mesh.rotation.y += 0.01;
     renderer.render(sceneT, camera);
}
```

巴比伦. js:

```
engine.runRenderLoop(function () {
     box.rotation.x += 0.005;
     box.rotation.y += 0.01;
     sceneB.render();
});
```

Three.js 和 Babylon.js 都使用动画或渲染循环来用新的旋转绘图更新画布。您还会注意到 Three.js 与 Babylon.js 略有不同，它在渲染点附加了相机。我们的最终产品是两个在半空中轻轻旋转的立方体。很简单，是吧？

## 两种框架的故事

现在你知道了。两个非常强大的 WebGL 框架构建在同一个基础上，但侧重于增强的基于 Web 的图形的不同方面。

你已经亲眼看到他们的动画制作方法是多么的相似，都遵循场景、渲染器、相机、对象的模式。尽管有相似之处，Babylon.js 通过关注引擎和定制照明等传统游戏引擎要求，巧妙地使自己与众不同。

最后，这两个相对年轻的框架使 web 开发者能够更容易地利用 WebGL 提供的强大的 3D 机会。因此，任何对 3D web 开发感兴趣的人都应该仔细看看这项前沿技术。

[下载 zip 文件中的演示代码](https://www.sitepoint.com/wp-content/uploads/2013/09/3DSourceCode.zip)

## 分享这篇文章