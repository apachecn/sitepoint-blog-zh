# 用 WebGL 和 JavaScript 构建地球

> 原文：<https://www.sitepoint.com/building-earth-with-webgl-javascript/>

在我们得到的所有 HTML5 和相关 API 中， [WebGL](https://www.khronos.org/webgl/) 可能是最有趣的。有了它，我们可以在我们的浏览器上创建令人惊叹的 3D 环境，在这篇文章中，我将带你去了解如何做到这一点。

特别是，我们将建立一个地球的模型，我们可以像一个疯子一样绕着它旋转，用它我们可以让我们的开发伙伴惊叹不已…嗯，至少有一点。

## 在开始之前

在本教程中，我们将使用奇妙的 [three.js WebGL 插件](http://threejs.org/)。它有点像 jQuery for WebGL，因为它抽象了许多本机 API 中固有的处理丑陋。简而言之，three.js 让我们的生活变得简单多了。

我们可以像 HTML 文档中的任何普通脚本一样包含它，就像这样:

```
<script src="//cdnjs.cloudflare.com/ajax/libs/three.js/r63/three.min.js"></script>
```

这里我们使用的是 CDN，但是您也可以使用本地版本。然后，我们需要确保 WebGL 有可以渲染的内容。这里我们有一些灵活性:我们可以使用普通的`div`或 canvas 直接呈现到 HTML 中，否则我们可以稍后使用 JavaScript 创建和附加 canvas。为了使事情变得简单一点，我们将采用第一种方法:

```
<body>
    <div id="container"></div>
    <script src="earth.js"></script>
</body>
```

将链接添加到 JavaScript 文件后，我们的 HTML 现在差不多完成了。

## 设置

Three.js 本身倾向于像一个真正的 3D 桌面程序那样进行设置。我们有一个事物生活的场景，一个观看它们的摄像机，一些灯光，一个渲染事物的渲染器，当然还有 3D 物体本身。这个列表看起来有点令人生畏，但是所有这些都可以在我们的`earth.js` JavaScript 文件中以变量的形式出现。

```
var scene,
    camera,
    light,
    renderer,
    earthObject;

var WIDTH = window.innerWidth - 30,
    HEIGHT = window.innerHeight - 30;

var angle = 45,
    aspect = WIDTH / HEIGHT,
    near = 0.1,
    far = 3000;
```

这里还定义了一些其他变量，即宽度和高度常量，用于获取渲染器的宽度和高度，以及一组变量，我们稍后将使用这些变量来设置我们的摄像机位置。

所有这些都是几乎所有 3D 项目的共同元素，无论平台或环境如何，所以要习惯这些家伙的存在。有了 three.js，我们能够非常容易地实现这些，我们马上就会看到它们如何适应我们的项目。

## 环境

首先，我们需要使用新的变量并设置它们，这样我们的地球模型看起来会很棒。

我们可以从设置处理环境问题的一切开始:

```
var container = document.getElementById('container');

camera = new THREE.PerspectiveCamera(angle, aspect, near, far);
camera.position.set(0, 0, 0);
scene = new THREE.Scene();

light = new THREE.SpotLight(0xFFFFFF, 1, 0, Math.PI / 2, 1);
light.position.set(4000, 4000, 1500);
light.target.position.set (1000, 3800, 1000);
```

下面是对上面代码中发生的事情的描述:

*   我们在 HTML 中获取对容器的引用
*   我们使用之前声明的变量来设置我们的相机(关于相机如何在 3D 中工作的更多信息，[查看这个](https://www.youtube.com/watch?v=EWY-SaHnxkM))。
*   我们使用`position.set`设置摄像机的位置，它为每个维度(x、y 和 z)取一个参数。正如你可能已经猜到的，我们将使用这个相机指向我们的 3D 对象，在这种情况下是我们的地球。
*   接下来，我们设置灯光。如果没有这个，当我们渲染所有东西的时候，我们只会有一个黑屏，所以我们需要非常小心地设置它。Three.js 的 [SpotLight object](http://threejs.org/docs/#Reference/Lights/SpotLight) 采用与我们的相机大致相同的参数，但添加了一个十六进制值的颜色作为其第一个参数，随后是其余的参数。
*   最后，我们设置我们的渲染器。这个难题的另一个重要部分，渲染器实际上将我们制作的场景渲染到屏幕上；同样，没有它，我们只能看到黑暗的太空。我们给它抗锯齿(对于一些光滑的表面)并把它作为一个 DOM 元素添加到我们的原始容器中。

现在我们需要开始构建地球本身，把它粘在一个网格里:

```
var earthGeo = new THREE.SphereGeometry (30, 40, 400), 
earthMat = new THREE.MeshPhongMaterial(); 

var earthMesh = new THREE.Mesh(earthGeo, earthMat); earthMesh.position.set(-100, 0, 0); 
earthMesh.rotation.y=5;

scene.add(earthMesh);
```

在这里，我们创建一个网格，它有点像一个物体，我们将打扮成地球的样子，并给它一些几何图形和材质，或纹理来覆盖它。我们也正确地放置它，并像处理其他物体一样把它添加到场景中。

您可以在下面看到我们的设置示例。这里有一些额外的代码来处理渲染(我们将在一会儿进行)，但目前看起来还不错！

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [WebGL 地球教程，Demo 1](http://codepen.io/SitePoint/pen/RNYJwB/) 。