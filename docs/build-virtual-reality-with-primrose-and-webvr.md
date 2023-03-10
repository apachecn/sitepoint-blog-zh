# 用 Primrose 和 WebVR 在 VR 中构建虚拟现实

> 原文：<https://www.sitepoint.com/build-virtual-reality-with-primrose-and-webvr/>

虚拟现实今年真的取得了长足的进步，虚拟现实耳机销售一空。虚拟现实开发现在正稳步获得开发平台的更多关注，因为它们开始迎合这些热切的开发者。Unity 和 Unreal Engine 推出的最令人兴奋的功能之一是在虚拟现实中编辑虚拟现实场景的能力。使用 WebVR 和 JavaScript 的开发人员会很高兴地知道，他们也有一个框架，为 WebVR 原型和实验带来了这种能力——Primrose。

## 樱草是什么？

樱草花最好用他们的[官网](http://www.primrosevr.com/)来形容，

> Primrose 是一个跨浏览器、多设备的框架，用于在 WebVR 应用程序中构建生产力工具。

Primrose 为开发人员提供了一个很好的框架，让他们在浏览器中打开，尝试新的想法，探索他们在 VR 中的样子。它是开源的，仍然非常新，正在积极开发中。

## 你需要什么

要跟随 Primrose 开始您的 WebVR 向导之旅，您需要:

*   一个支持 WebGL 的浏览器，最好是一个支持 WebVR 的浏览器，如最新的 Chromium WebVR 版本(T1)或 T2 的 Firefox Nightly(T3)。
*   Oculus Rift 耳机(也可能是 HTC Vive)或谷歌 Cardboard——然而，你也可以在没有耳机的情况下在浏览器中进行实验和工作！
*   某种 web 服务器——像 WAMP/MAMP/静态节点服务器/静态 Python 服务器这样的本地 web 服务器就可以了！
*   触摸打字的能力——在 VR 中你将看不到你的键盘！

## 入门指南

你可以在 [Primrose 的网站](http://www.primrosevr.com/examples/editor3d/index.html) *上试用一个完整的实时编码工作版本(确保你使用如上所述的支持 WebGL 的浏览器访问它，否则你只会看到一个静态截图)*。

如果您想在本地运行自己的副本，您可以从 Primrose GitHub repo 下载/克隆最新版本及其示例。

## 我们在 GitHub 上的简化版本

对于本文，我已经整理了一个简化版的实时编码演示。它使用了与 Primrose 演示相同的代码，只是减少了功能，以保持事情简单，便于早期解释。要阅读本文，[请访问该版本的 GitHub repo](https://github.com/sitepoint-editors/Primrose-Simplified-Editor) 。

## 运行我们的简化版本

要运行我们的简化版实时编码演示，请将其复制到您的 web 服务器上，然后在启用了 WebGL 的浏览器中打开该位置(例如`http://localhost/primrose`，如果您将它放在服务器的根目录中，甚至只需打开`http://localhost`)。

***注意:**这不会从你的文件系统运行！如果你试图从像`file:///Users/yourname/primrose/index.html`这样的位置运行它，它将会崩溃，因为浏览器不会允许 JavaScript 访问纹理文件等等。*

运行该程序后，您应该会看到如下所示的内容:

![Our initial running demo](img/122831a517fd6f327bd10abb4ca07531.png)

如果你在这个场景中四处看看，你会看到一个代码编辑器已经准备好了，正等着你。您可以将光标指向代码行，单击将光标放在那里，然后像在常规文本编辑器中一样输入。如果我们在`for (var i = 0; i < 10; i++)`内的`10`之后点击，并将其更改为`for (var i = 0; i < 100; i++)`，如下所示:

![Updating our code in our demo](img/b7906b6bf5b94c9dc861d14101f83232.png)

我们的场景将实时改变，现在有 100 个街区随机移动！

![Our demo with 100 blocks](img/6558ce5ab6d916b3b032dfadfd46e907.png)

如果你想从不同的角度看得更清楚，你可以看着地板，点击你想移动到的地方:

![Moving around our demo](img/50b86bc78fd9600e797dfe8febb90dab.png)

你也可以使用键盘上的箭头键在场景中走动。

## 它是如何工作的

大多数情况下，您不需要重新构建太多的 Primrose 实时代码编辑器示例——只需插入 GitHub 上提供的示例代码，并将其改编到您自己的场景中即可。然而，我想我会提供一个简化的版本来探索一些幕后的概念，同时展示 Primrose 在其框架内的一些能力。

我们的 Primrose 应用程序的开始看起来是这样的:

```
var BRICK = "images/brick.png",
    GROUND = "images/deck.png",
    SKY = "images/bg2.jpg",
    app = new Primrose.BrowserEnvironment("Our Simplified 3D Editor", {
      skyTexture: SKY,
      groundTexture: GROUND
    }),
```

这些代码行定义了我们的三个纹理文件，然后在`app`变量中实例化了我们的 Primrose 应用程序。方法用天空和地面纹理来设置我们的场景。

我们还有一个`editorFrame`变量，它设置了一个`2048` by `2048`区域，供我们的编辑器放入其中:

```
editorFrame = new Primrose.Surface({
  bounds: new Primrose.Text.Rectangle(0, 0, 2048, 2048)
});
```

所有 Primrose 应用程序(以及许多 JavaScript 框架)的另一个共同点是在`addEventListener("ready", function() {});`中初始化我们的元素。在这里，我们添加:

*   我们将能够从我们的实时代码编辑器中添加和更改的元素。
*   `editor`–我们场景中的实时代码编辑器*(我们将在下面更详细地讨论这一点！)*。
*   来自`getSourceCode()`的初始代码——在我的简化版本中，这将检索 Primrose 编辑器附带的默认代码，并显示在我们的 VR 文本编辑器中。在复杂得多的 Primrose 编辑器中，它可以对本地存储做更多的事情。

```
app.addEventListener("ready", function() {
  app.scene.add(subScene);

  editor = new Primrose.Text.Controls.TextBox({
    bounds: new Primrose.Text.Rectangle(
      0, 0,
      editorFrame.surfaceWidth, 
      Math.floor(editorFrame.surfaceHeight)
    ),
    tokenizer: Primrose.Text.Grammars.JavaScript,
    value: getSourceCode(isInIFrame),
    fontSize: 45
  });

  editorFrame.appendChild(editor);
});
```

Primrose 最近发布了一个新版本，编辑器在`Primrose.Text`中有了一系列新的框架对象:

*   `Primrose.Text.Controls.TextBox` —这设置了一个文本区域，并在默认情况下提供了一系列功能。
*   `Primrose.Text.Rectangle` —这允许我们为文本区域定义一个矩形边界。当我们定义我们的`editorFrame`时，你可能已经注意到了。
*   `Primrose.Text.Grammars.JavaScript` —在`tokenizer`键中使用，用于设置 JavaScript 代码高亮显示。您可以通过`Primrose.Text.Grammars.PlainText`进行明文高亮显示。

当使用 Primrose 应用程序时，您可以注意到一些`"ready"`之外的事件，包括:

*   这是我们在示例编辑器代码中使用的唯一一个事件，它在每一帧都运行。您可以在 Primrose 中使用它来运行动画并检查场景中的更新。
*   `"keydown"`、`"keyup"`、`"keypress"`–分别在按键按下、松开、按下再松开时运行。
*   `"mousedown"`和`"mouseup"`–同上，但使用鼠标。
*   `"mousemove"`–检测鼠标移动。
*   `"wheel"`–检测用户何时移动鼠标滚轮。
*   `"touchstart"`、`"touchend"`和`"touchmove"`–检测手指何时使用触摸屏触摸场景中的元素，分别从屏幕上抬起手指并在屏幕上滑动手指。
*   `"unload"`–应用程序关闭时运行。我的简化版本的编辑器不使用这个，但是完整的 Primrose 编辑器使用这个将编辑器内容保存到本地存储。
*   `"load"`–当应用程序再次加载时运行。
*   `"pointerstart"`、`"pointerend"`和`"pointermove"`–对物体上的点击和触摸事件都做出响应，所以你不需要单独留意这两个事件。
*   `"gazestart"`–在用户看到一个物体的瞬间做出反应。
*   `"gazecomplete"`–默认情况下，当用户注视一个对象一秒钟时做出响应。
*   `"gazecancel"`–在默认的一秒时间过去之前，当用户移开视线时做出响应。您可以通过`gazeLength`更改一秒钟的默认值，但这超出了本文的范围。

我不会在本文中粘贴所有的简化代码(这会变得相当长且难以处理！).你可以在上面链接的 GitHub repo 里查看一下。然而，我们将回顾一些事件响应和函数包含的内容。

在我们简化的应用程序的`"update"`事件中，它检查编辑器中的任何代码更改，每隔`scriptUpdateTimeout`(每半秒运行一次的超时)更新它们，并运行它已经知道的任何动画。

在对`"keydown"`事件的响应中，它清除了`scriptUpdateTimeout`超时，延迟了我们的应用程序尝试更新的时间！

每次`scriptUpdateTimeout`结束时，都会运行`updateScript()`功能。这是检查我们的编辑器的脚本是否已经改变，并替换正在运行的脚本，如果它看到一个更新。它从上面清除了我们的`subScene`，并根据我们的 VR 编辑器的代码改变了元素。

## 设置按钮功能

为了能够体验适当的、身临其境的现场编码体验(或者你构建的任何其他基于 Primrose 的应用程序)，我们希望它能够全屏显示。这对 VR 来说是必不可少的，因为它不会以任何其他方式工作！为此，我们添加按钮来触发这些模式。Primrose 已经为我们完成了大部分工作，我们只需添加一个 ID 为`goVR`的按钮和一个 ID 为`goRegular`的按钮，然后我们调用`app.setFullScreenButton`并传入 ID 名称，这是一个点击事件，或者是用于 VR 的`true`或者是用于常规全屏模式的`false`:

```
app.setFullScreenButton("goVR", "click", true);
app.setFullScreenButton("goRegular", "click", false);
```

## 让樱草花奇迹发生

当我们调整代码时，我们目前有各种各样的立方体在漫游并实时变化——但是在 Primrose 中我们还能构建什么呢？这里有一些元素你可以添加到你的`app.js`中的`testDemo()`或者你的虚拟现实场景中！

### 轴

轴为您提供了一组红色、绿色和蓝色的条，分别显示 x、y 和 z 的方向。这通过`axis(length, width)`起作用，例如:

```
put(axis(5,0.1)).on(start);
```

生成以下最终结果:

![An axis object](img/2f0152ff5184ff99a60c21d4ae37b21f.png)

### 点云

通过`cloud(verts, color, size)`功能，可以用许多小正方形点生成点云。`verts`变量是一个顶点数组。点云的示例如下所示:

```
var verts = [];

for (var i = 0; i < 5000; ++i) {
  verts.push(
    v3(Primrose.Random.number( -0.5 * WIDTH, 0.5 * WIDTH),
      Primrose.Random.number(-0.5 * HEIGHT, 0.5 * HEIGHT),
      Primrose.Random.number(-0.5 * DEPTH, 0.5 * DEPTH)
    )
  );
}

put(cloud(
  verts, this.options.backgroundColor, 0.05)
).on(start).at(MIDX, MIDY, MIDZ);
```

`MIDX`、`MIDY`和`MIDZ`是我们样本代码中已经存在的变量，它们为我们的场景指定了一个焦点。你可以用任何数字来代替它们。

这在我们的场景中生成了以下内容:

![A point cloud in our scene](img/df71d5893099d0bd24d2eb525ca8aef9.png)

### 中心

在我上面的例子中，你可能已经注意到了一件事，那就是我们添加了一个叫做`start`的东西。这实际上是一个`hub()`——我们场景中的一个特殊点，在这里我们可以对其他对象进行分组。我们的`start`中心是这样定义的:

```
start = put(hub())
          .on(scene)
          .at(-MIDX, 0, -DEPTH - 2);
```

### 光

可以通过`light(color, [intensity, [distance, [decay]]])`定义灯光。我们场景中的默认照明是白光，如下所示:

```
put(light(0xffffff, 1, 500))
  .on(start)
  .at(MIDX + 5, 8, MIDZ + 20);
```

### 箱子

盒子——以及之后的形状——在出现在我们的场景中之前需要进行纹理处理。它们被包裹在一个`textured()`函数中，这个函数从我们的图像文件中给它们一个纹理。盒子的格式是这样的:`box(width, height, length)`。

```
put(textured(box(2, 2, 2), BRICK))
  .on(start).at(MIDX, MIDY, MIDZ);
```

这在我们的场景中创建了一个巨大的 [Three.js BoxGeometry 对象](http://threejs.org/docs/#Reference/Extras.Geometries/BoxGeometry):

![A simple box in our scene](img/58644ced9d8b74bf31ce6a2b8aebc2d9.png)

### 圆筒

可通过以下方式获得气缸:

```
cylinder(
  radiusTop,
  radiusBottom,
  height,
  radiusSegments,
  heightSegments,
  openEnded,
  thetaStart,
  thetaEnd
)
```

这创建了一个 [Three.js CylinderGeometry 对象](http://threejs.org/docs/#Reference/Extras.Geometries/CylinderGeometry)。例如，这里有一个圆柱体，其顶部和底部半径为`1`。其高度为`5`，半径/高度段为`40`:

```
put(textured(cylinder(1, 1, 4, 40, 40), BRICK))
    .on(start).at(MIDX, MIDY, MIDZ);
```

看起来是这样的:

![A cylinder in our scene](img/b9a6338933be95044401f35e8b78c0f3.png)

### 其他人

你可以在【脚本函数】下的 [Primrose 文档中找到很多其他选项，包括`quad()`、`shell()`、`sphere()`等等。探索和实验！](http://www.primrosevr.com/doc/editorVRManual.html)

## 结论

Primrose 是一个非常强大的网络虚拟现实框架，它肯定会在未来驱动一些非常有用的虚拟现实应用。尽管它在框架内和 WebVR 本身都处于早期，但随着时间的推移，这看起来已经准备好与 WebVR 一起成长和发展。

喜欢这里的潜力吗？请记住，这是一个你也可以参与的早期项目！与它的创造者[肖恩·麦克贝斯](https://twitter.com/Sean_McBeth)取得联系，并参与其中！

如果你用 Primrose 做你自己的 VR 应用，我很想看看你是怎么组装的！请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

如果你觉得虚拟现实和网络虚拟现实领域很吸引人，我在我的 Dev Diner 网站上和 Sean McBeth 本人谈过关于虚拟现实和 Primrose 的开发。这是我做过的最喜欢的采访之一！肖恩是一个鼓舞人心的家伙。

## 分享这篇文章