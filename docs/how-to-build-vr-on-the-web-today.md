# 今天如何在网络上构建虚拟现实

> 原文：<https://www.sitepoint.com/how-to-build-vr-on-the-web-today/>

到 2020 年，虚拟现实的价值将达到 70 亿美元。在这段时间里，网络肯定不会一直是 2D 的专属环境。事实上，已经有几种简单的方法可以将 VR 带入浏览器。和它一起工作也非常有趣！

要开始虚拟网络的开发之旅，有三种可能的方法:

*   JavaScript、Three.js 和观察设备方向
*   JavaScript，Three.js 和 WebVR *(我的新首选方法！)*
*   CSS 和 WebVR *(还很早)*

我将对每一个进行检查，并给出一个简短的总结。

## JavaScript、Three.js 和观察设备方向

目前大多数基于浏览器的虚拟现实项目的工作方式之一是通过 [deviceorientation](https://developer.mozilla.org/en-US/docs/Web/Events/deviceorientation) 浏览器事件。这告诉浏览器设备是如何定向的，并允许浏览器拾取它是否被旋转或倾斜。虚拟现实视角中的这一功能允许您检测何时有人环顾四周，并调整相机以跟随他们的目光。

为了在浏览器中实现美妙的 3D 场景，我们使用了 [three.js](http://threejs.org) ，这是一个 JavaScript 框架，可以轻松创建 3D 形状和场景。它消除了整合 3D 体验的大部分复杂性，并允许您专注于您试图在场景中整合的内容。

我在 SitePoint 上写了两个使用设备定位方法的演示:

*   [用 Google Cardboard 和 Three.js 把虚拟现实带到网络上](https://www.sitepoint.com/bringing-vr-to-web-google-cardboard-three-js)
*   [用 Three.js 和节点](https://www.sitepoint.com/visualizing-a-twitter-stream-in-vr-with-three-js-and-node/)在 VR 中可视化 Twitter 流

如果您不熟悉 three.js 以及如何组合一个场景，我建议您看一下上面两篇文章，以获得对这种方法的更深入的介绍。我将在这里介绍关键概念，不过这将是一个更高的层次。

其中的关键组件包括以下 JavaScript 文件(您可以从上面的示例演示中获得这些文件，也可以在 [three.js 示例下载](https://github.com/mrdoob/three.js/tree/master/examples/)中找到它们):

*   `three.min.js`–我们的 three.js 框架
*   这是一个 three.js 插件，它提供了我们上面讨论过的设备方向。它会移动我们的摄像头，以适应我们设备的移动。
*   `OrbitControls.js`–这是一个备用控制器，如果我们没有可以访问设备定向事件的设备，用户可以使用鼠标来移动摄像机。
*   `StereoEffect.js`–three . js 效果，将屏幕分割成立体图像，每只眼睛的角度略有不同，就像在 VR 中一样。这创建了实际的 VR 分屏，而我们不需要做任何复杂的事情。

### 设备方向

启用设备方向控制的代码如下所示:

```
function setOrientationControls(e) {
  if (!e.alpha) {
    return;
  }

  controls = new THREE.DeviceOrientationControls(camera, true);
  controls.connect();
  controls.update();

  element.addEventListener('click', fullscreen, false);

  window.removeEventListener('deviceorientation', setOrientationControls, true);
}
window.addEventListener('deviceorientation', setOrientationControls, true);

function fullscreen() {
  if (container.requestFullscreen) {
    container.requestFullscreen();
  } else if (container.msRequestFullscreen) {
    container.msRequestFullscreen();
  } else if (container.mozRequestFullScreen) {
    container.mozRequestFullScreen();
  } else if (container.webkitRequestFullscreen) {
    container.webkitRequestFullscreen();
  }
}
```

当 DeviceOrientation 事件侦听器有兼容的设备时，它会提供 alpha、beta 和 gamma 值。如果我们没有任何 alpha 值，它不会将我们的控件更改为使用设备方向，以便我们可以使用动态观察控件。

如果它有这个 alpha 值，那么我们创建一个设备方向控件，并提供我们的`camera`变量来控制它。如果用户点击屏幕，我们也设置它来设置我们的场景为全屏(我们不想在 VR 中盯着浏览器的地址栏)。

### 轨道控制

如果 alpha 值不存在，且我们不能访问设备设备方向事件，这种技术提供了一个控件，通过用鼠标拖动来移动摄像机。看起来是这样的:

```
controls = new THREE.OrbitControls(camera, element);
controls.target.set(
  camera.position.x,
  camera.position.y,
  camera.position.z
);
controls.noPan = true;
controls.noZoom = true;
```

从上面的代码来看，可能令人困惑的主要事情是`noPan`和`noZoom`。基本上，我们不想通过鼠标在场景中移动，也不想放大或缩小，我们只想四处看看。

### 立体声效应

为了使用立体声效果，我们这样定义它:

```
effect = new THREE.StereoEffect(renderer);
```

然后在调整窗口大小时，我们更新它的大小:

```
effect.setSize(width, height);
```

在每个`requestAnimationFrame`中，我们通过我们的效果设置场景进行渲染:

```
effect.render(scene, camera);
```

这就是实现虚拟现实的设备导向方式的基本原理。这对于使用 Google Cardboard 实现一个漂亮而简单的应用来说是有效的，但是在 Oculus Rift 上就不那么有效了。下一个方法对裂缝来说更好。

## JavaScript，Three.js 和 WebVR

希望像 Oculus Rift 一样访问 VR 头戴设备方向？WebVR 是目前的做法。 [WebVR](http://mozvr.github.io/webvr-spec/webvr.html) 是一个早期的实验性 Javascript API，提供对 Oculus Rift 和谷歌 Cardboard 等虚拟现实设备功能的访问。目前，它可以在 [Firefox Nightly](http://mozvr.com/downloads/) 和几个[移动 Chrome 和 Chrome](https://drive.google.com/folderview?id=0BzudLt22BqGRbW9WTHMtOWMzNjQ&usp=sharing#list)的实验版本上下载。要记住的一件事是，如果规范仍在草案中，可能会发生变化，所以要进行试验，但要知道，随着时间的推移，你可能需要进行调整。

总的来说，WebVR API 通过以下方式提供对 VR 设备信息的访问:

```
navigator.getVRDevices
```

我不会在这里讨论太多的技术细节(我将在 SitePoint 未来的文章中更详细地讨论这个问题！)，如果你有兴趣了解更多[请查看 WebVR 编辑的草稿](http://mozvr.github.io/webvr-spec/webvr.html)。我不详细介绍它的原因是有一个更简单的方法来实现这个 API。

前面提到的实现 WebVR API 的更简单的方法是使用 Boris Smus 的 [WebVR 样板。它提供了良好的基线功能，实现了 WebVR，并优雅地降低了不同设备的体验。这是目前我见过的最好的网络虚拟现实实现。*如果你正在寻找建立网络虚拟现实体验，这是目前最好的起点！*](https://github.com/borismus/webvr-boilerplate)

要开始使用这种方法，请在 Github 上下载 [WebVR 样板文件。](https://github.com/borismus/webvr-boilerplate)

您可以专注于编辑`index.html`并使用设置中的所有文件，或者您可以从头开始将特定插件实现到您自己的项目中。如果你想比较每个实现中的差异，我已经将我的[用 Three.js 和 Node](https://www.sitepoint.com/visualizing-a-twitter-stream-in-vr-with-three-js-and-node/) 在 VR 中可视化 Twitter 流的例子迁移到了一个[在 VR 中支持 WebVR 的 Twitter 流](https://github.com/sitepoint-editors/WebVRTwitterWorld)。

要从头开始将此项目包含到您自己的项目中，您需要以下文件:

*   当然是我们的 three.js 框架
*   `VRControls.js`–一个 three.js 插件，用于通过 WebVR 进行 VR 控制(这可以在样板项目的`bower_components/threejs/examples/js/controls/VRControls.js`中找到)
*   `VREffect.js`–VR 效果本身的 three.js 插件，显示 Oculus Rift 的场景(这可以在样板项目的`bower_components/threejs/examples/js/effects/VREffect.js`中找到)
*   `webvr-polyfill.js`——这是一个为还不完全支持 WebVR 的浏览器准备的填充(这可以在 GitHub 的[中找到，也可以在样板代码的`bower_components/webvr-polyfill/build/webvr-polyfill.js`中找到)](https://github.com/borismus/webvr-polyfill)
*   这是为你管理一切的样板代码的一部分，包括提供一种进入和退出 VR 模式的方法(这可以在`build/webvr-manager.js`中找到)

实现它只需要从设备定向方法进行一些调整。以下是对那些想尝试这种方法的人的概述:

### 控制

虚拟现实控件的设置非常简单。我们可以将一个新的`VRControls`对象赋给我们之前使用的`controls`变量。轨道控件和设备方向控件应该是不必要的，因为样板文件现在应该可以处理没有 VR 功能的浏览器。这意味着你的场景在 Google Cardboard 上应该也能很好的工作！

```
controls = new THREE.VRControls(camera);
```

### 虚拟现实效应

其效果与实现`StereoEffect`非常相似。只需用我们新的`VREffect`效果来替换它:

```
effect = new THREE.VREffect(renderer);
effect.setSize(window.innerWidth, window.innerHeight);
```

然而，在这个方法中，我们不渲染效果。相反，我们通过我们的虚拟现实管理器进行渲染。

### 虚拟现实经理

虚拟现实管理器负责我们所有的虚拟现实进入/退出等等，所以这是我们的场景最终被渲染的地方。我们最初通过以下方式设置它:

```
manager = new WebVRManager(renderer, effect, {hideButton: false});
```

VR 管理器提供了一个按钮，如果用户在兼容的浏览器上，可以让用户进入 VR 模式，如果他们的浏览器不支持 VR，则可以进入全屏模式(全屏是我们想要的移动设备)。`hideButton`参数表示我们是否想要隐藏那个按钮。我们绝对不想隐藏它！

我们的渲染调用看起来是这样的，它使用了一个来自 three.js' `update()`函数的`timestamp`变量:

```
function update(timestamp) {
  controls.update();

  manager.render(scene, camera, timestamp);
}
```

有了所有这些，你应该有一个工作的 VR 实现，它可以根据设备将自己翻译成各种格式。

*`renderer.getSize()`是否返回错误？这让我抓狂了几个小时，但是你需要做的就是更新 three.js！*

### WebVR 样板文件实际上是什么样子的

以下是我的 Twitter 示例在支持虚拟现实的浏览器上的视图:

![The view when VR is available](img/ebb94aa91757405e9ac67f3a2e51bbcd.png)

这是当你点击虚拟现实图标时出现的 Oculus Rift 视图:

![Our view within a VR headset like Oculus](img/0edf6153aa25783371d92d97902121d1.png)

这是智能手机上的视图，设备方向仍然允许我们环视场景，我们没有分屏。内容的良好响应视图:

![Our view on a smartphone in portrait orientation](img/e6797b46bdb2a1537d98d82259844119.png)

如果我们点击移动设备上的虚拟现实图标，我们会看到谷歌 Cardboard 风格设备的全屏视图:

![Our scene in a Google Cardboard smartphone view](img/cdf99a457c8b4bde00e596ed4f9380af.png)

## CSS 和 WebVR

Mozilla 的目标是在 Firefox 夜间版本中为其浏览器带来 VR 观看功能，然而这还为时尚早！我没有太多的运气让它在我的 Mac 和 Oculus 设置上工作。Firefox 的 Vladimir Vukić ević提到的各种惯例包括将 CSS 3D 转换与 VR 全屏模式集成。

Vladimir 在博客中举了一个例子，他说带有`transform-style: preserve-3d`的元素应该从两个视点渲染两次，才能进入 VR:

```
#css-square {
  position: absolute;
  top: 0; left: 0;

  transform-style: preserve-3d;
  transform: translate(100px, 100px, 100px);
  width: 250px;
  height: 250px;
  background: blue;
}
```

如果你知道任何使用 VR 和 CSS 的演示，请让我知道！我还没能找到任何一个。将网页的 HTML 和 CSS 部分带入虚拟现实的想法无疑是一个非常有趣的概念。随着我们慢慢转向虚拟现实设备，网络在某个时候像这样进入虚拟现实领域是不可避免的！

## 结论

随着我们的技术能力与我们的远大抱负相匹配，技术世界将在未来几年缓慢但肯定地拥抱虚拟现实！推动虚拟现实应用及其价值的一个因素是内容。我们需要让虚拟现实用户享受虚拟现实内容！有什么比通过网络更好、更容易的方式呢？

如果你冒险使用这段代码构建一个 VR 演示，请在评论中分享或在 Twitter 上与我联系( [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )。我很想戴上我的 Oculus Rift 或谷歌 Cardboard，带着它转一圈！

我通过 JavaScript 为那些寻找快速参考的人准备了一套虚拟现实和增强现实的链接。前往 Dev Diner，查看我的 [Dev Diner VR 和 AR with JavaScript Developer Guide](http://devdiner.com/guides/vr-and-ar-with-javascript/)，其中包含本文中提到的所有链接、其他优秀的 SitePoint 文章等等。如果你有我没有列出的其他好资源，也请告诉我！

## 分享这篇文章