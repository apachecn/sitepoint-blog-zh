# 测试 WebGL 的极限:Babylon.js 火车演示

> 原文：<https://www.sitepoint.com/testing-limits-webgl-babylon-js-train-demo/>

为了庆祝 **Windows 8.1** 和 **Internet Explorer 11** 的发布，我们决定为 [Babylon.js](https://www.babylonjs.com/index.html?TRAIN) 创建一个新的演示场景。

这个演示是为了在现代浏览器如 **Internet Explorer 11** 上展示 **WebGL** 的强大功能。我请求我的一个朋友([罗穆亚尔·鲁希尔](https://www.hypnotik.info))允许我们使用他用 3dsMax 制作的 3D 场景:

[https://www.youtube.com/embed/Z1dIZWSdyn4](https://www.youtube.com/embed/Z1dIZWSdyn4)

正如你在这里看到的，这里的景色真的很美。挑战在于让它能够在 **WebGL** 下实时运行。

[![image](img/649be474eb00caff9aaae40a92e18561.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/3288.image_5F00_56C18DF5.png)

## 一些统计数据

场景本身是一大堆超过 900，00 0 个活动顶点(用于水面的镜子几乎将顶点的体积乘以 2)。它使用超过 **28** 不同的着色器和重量 **70 MB** 。

为了评估场景的性能，我们进行了两次基准测试:一次是在我的个人电脑上(一台配备 Nvidia GeForce 680 的胖而强大的英特尔酷睿 I7 电脑)，另一次是在我的笔记本电脑上(一台配备英特尔酷睿 I7 和英特尔板载显卡(HD4000)的联想 X1 Carbon)。

我们每次运行两个测试。一个在主旋转摄像机上，一个在不太宽的摄像机上(行走摄像机)。旋转摄像机有这样的观点:

[![image](img/a268ec3b90e414d211e13f0193cdf1d4.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/0361.image_5F00_2929A3B5.png)

这台相机需要巨大的能量，因为每一个物体都是可见的！

另一个相机渲染起来稍微简单一点:

[![image](img/54b7fea7941ff3c085c58ebd8234340f.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/1513.image_5F00_73E19EB2.png)

这些基准测试的结果如下:

[![image](img/f376a24dadd599541ea8b43c07a18595.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/7827.image_5F00_690AEC2F.png)

 <font style="background-color: rgb(255, 255, 0);">[![image](img/cd08ce4ea0069a0f595ce661dc5fc038.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/8877.image_5F00_68A149EB.png)

正如你所看到的，即使在中等硬件(我的笔记本电脑)上， **WebGL** 的能力也能让我们在 **Internet Explorer 11** 上每秒传送 30 帧。

## 在后台

为了能够渲染这样的场景，我们必须给 **Babylon.js.** 添加新的特性

首先，这是我们第一次能够使用这个新界面动态更换摄像机:

[![image](img/589fe331cb7a0aa6d4d1c29d525feb78.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/0284.image_5F00_4573DF39.png)

然后，为了给场景添加更多的生命，我们添加了对动画摄像机的支持。例如，名为 CAM_ROT 的摄像机围绕整个场景旋转。

同理，如果你换成 *CAM_TRAIN* camera 或者 CAM _ TRAIIN _ AVANT camera，我们也会发现一个新的特性:相机可以关联网格。这允许摄像机(作为孩子)链接到一个对象(例如这里的火车):

[![image](img/0e559e6bab1301361e61aa62b518ac1e.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/4743.image_5F00_0A6EF187.png)

关于原始性能，为了简化渲染，我们花了很多时间为几乎每个 **WebGL** 函数创建缓存。这些缓存允许我们删除冗余的状态更改，这对 **WebGL** 状态机来说是非常昂贵的。

使用 **Internet Explorer 11** 的 **F12 开发者栏**，我们能够优化引擎，这样 Javascript 代码就不会成为瓶颈。集成分析器(见下文)告诉我们，几乎所有的时间都花在显卡代码内部:

[![image](img/2af366f5f7b8a309effc231682162c5d.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-44-73-metablogapi/1512.image_5F00_38AF1C06.png)

*drawElements* 函数是一个用于渲染三角形的 WebGL 函数。我们可以在之前的截图上看到， **Babylon.js** (从第二行开始)不是问题。

不要犹豫，使用评论来分享您在自己的计算机上实现的性能(不要忘记指出您使用哪个版本的操作系统、浏览器和硬件)。我们渴望听到你自己的经历！

## 更进一步

你也想这样做，释放出 **WebGL** 的力量？以下是一些有趣的链接:

*   [现代。IE](https://www.modern.ie/)
*   [通过用 babylon.js 创建一个小型 3D 游戏来理解 DeviceOrientation 事件](https://blogs.msdn.com/b/eternalcoding/archive/2013/10/07/understanding-deviceorientation-events-by-creating-a-small-3d-game-with-babylon-js.aspx)
*   [使用 babylon.js 为您的 Windows 8.1 应用创建 3D 图表](https://blogs.msdn.com/b/eternalcoding/archive/2013/08/12/creating-a-3d-chart-for-your-windows-8-1-app-using-babylon-js.aspx)
*   [Babylon.js:用自定义着色器、高度贴图和天空盒为你的游戏创建一个令人信服的世界](https://blogs.msdn.com/b/eternalcoding/archive/2013/08/06/babylon-js-creating-a-convincing-world-for-your-game-with-custom-shaders-height-maps-and-skyboxes.aspx)
*   [介绍 Babylon.js](https://blogs.msdn.com/b/eternalcoding/archive/2013/06/27/babylon-js-a-complete-javascript-framework-for-building-3d-games-with-html-5-and-webgl.aspx)
*   Github 回购:[https://github.com/BabylonJS/Babylon.js](https://github.com/BabylonJS/Babylon.js)
*   在线教程:[https://github.com/BabylonJS/Babylon.js/wiki/Tutorials](https://github.com/BabylonJS/Babylon.js/wiki/Tutorials)

*最初发布:[https://blogs . msdn . com/b/eternal coding/archive/2013/10/28/Babylon-js-the-train-demo . aspx](https://blogs.msdn.com/b/eternalcoding/archive/2013/10/28/babylon-js-the-train-demo.aspx)。经作者许可，在此转载。*

## 分享这篇文章</font>