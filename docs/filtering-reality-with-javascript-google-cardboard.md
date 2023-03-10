# 用 JavaScript 和 Google Cardboard 过滤现实

> 原文：<https://www.sitepoint.com/filtering-reality-with-javascript-google-cardboard/>

在移动浏览器中运行虚拟现实的能力令人振奋。谷歌 Cardboard 和其他类似的虚拟现实设备让它变得难以置信的简单，只需将你的手机放入支架中，然后就可以开始了！我之前报道过用 Google Cardboard 和 Three.js 将虚拟现实带到网络上，在那里我讨论了构建一个虚拟现实环境的基础。人们真的很喜欢那篇文章(我也很喜欢构建那个演示)，所以我想我应该用一个不同的想法来扩展它。与其引入网络应用编程接口，为什么不引入你手机的摄像头，把它变成一种增强现实体验呢？

在这篇文章中，我将探索我们如何使用 HTML5 和 JavaScript 获取相机数据，过滤并显示出来。我们将通过立体视觉效果来实现这一切，为谷歌 Cardboard 和其他 VR 设备创建增强现实体验。我们将应用一些不同的过滤器到我们的相机流——卡通灰度过滤器，深褐色电影风格过滤器，像素化过滤器(我最喜欢的)和反色过滤器。

如果你对使用 HTML5、canvas 标签和 JavaScript 过滤图像完全陌生，我在 Learnable 上有一个关于这个主题的完整课程，名为 [JavaScript in Motion](https://learnable.com/courses/html5-and-javascript-in-motion-2880/) ！我将假设您理解 canvas 和 video 标签，以及如何将视频流式传输到 canvas 标签中，来阅读本文。或者假设你有足够的信心去解决它！

## 演示代码

如果你热衷于直接进入代码并尝试它，你可以在 [GitHub](https://github.com/sitepoint-editors/RealityFilter) 上找到它。

想用行动试试吗？我这里托管了一个运行版本:[现实滤镜](https://devdiner.com/demos/realityfilter/)。

*注意:Chrome 处理摄像头输入的方式最近发生了变化，需要通过 HTTPS 运行页面才能工作！*

## 这将如何工作

我们将采用与之前的 [Google Cardboard 文章](https://www.sitepoint.com/bringing-vr-to-web-google-cardboard-three-js/)相同的初始设置——一个通过立体效果显示的 Three.js 场景。这种效果使我们每只眼睛都有一个显示器，使事物在虚拟现实中看起来非常三维。然而，我们没有使用前一篇文章中的浮动粒子，而是移除了大部分元素，并在播放我们的相机馈送的相机前面放置了一个简单的 Three.js 网格。

## 我们的准则解释说

查看我们的变量声明，这里的大多数变量对于那些已经完成了前面演示的人来说都很熟悉。准备 Three.js 场景的变量、相机、渲染器、画布输出的元素、放置该元素的容器以及存储立体效果的变量都是相同的。

```
var scene,
      camera, 
      renderer,
      element,
      container,
      effect,
```

我们的三个与摄像机镜头相关的新变量是`video`、`canvas`和`context`。

```
video,
      canvas,
      context,
```

*   `video`–我们实际的 HTML5 `<video>`元素。我们的摄像头会在里面播放。
*   `canvas`–一个虚拟的`canvas`元素，它将包含我们的`video`元素的内容。我们将从这个画布中读入视频数据，然后在将它的内容放入我们的 Three.js 场景之前，将我们的主题过滤器添加回画布上。
*   `context`–我们的`canvas`2D 上下文，我们使用它来执行大多数功能。

在与我们的过滤器功能相关的变量下，我们还有一些其他变量。

```
themes = ['blackandwhite', 'sepia', 'arcade', 'inverse'],
      currentTheme = 0,
      lookingAtGround = false;
```

*   `themes`–过滤器名称的数组。
*   `currentTheme`–我们当前在`themes`数组中查看的索引。
*   不管我们是否看了地面(这个很快就会变得更有意义)。

我们从`init()`函数开始，像以前一样设置场景、摄像机等等:

```
init();

  function init() {
    scene = new THREE.Scene();
    camera = new THREE.PerspectiveCamera(90, window.innerWidth / window.innerHeight, 0.001, 700);
    camera.position.set(0, 15, 0);
    scene.add(camera);

    renderer = new THREE.WebGLRenderer();
    element = renderer.domElement;
    container = document.getElementById('webglviewer');
    container.appendChild(element);

    effect = new THREE.StereoEffect(renderer);

    element.addEventListener('click', fullscreen, false);
```

这一次我们没有任何通过`DeviceOrientation`事件来移动摄像机的功能。与 VR 体验相比，在这个 Three.js 场景中，我们不需要改变实际的相机位置。我们将场景保持在同一个点上——当用户环顾四周时，摄像机画面将会移动。

我们在前面的例子中保留的一个监听器是一个事件监听器，如果我们点击场景，它会全屏显示。这将 Chrome 地址栏从我们的视图中移除。

### DeviceOrientationEvent 的不同用法

在本演示中,`DeviceOrientationEvent`有了新用途。我们将它设置为观察设备方向的变化，并将其用作切换过滤器的触发器。我们实际上没有任何物理控件来触发事件，所以我们通过用户在哪里看来控制事情。特别是，我们会在用户看地面的任何时候更换过滤器。

```
if (window.DeviceOrientationEvent) {
    window.addEventListener('deviceorientation', function(evt) {
      if (evt.gamma > -1 && evt.gamma < 1 && !lookingAtGround) {
        lookingAtGround = true;
        currentTheme = (themes.length > currentTheme+1) ? currentTheme+1 : 0;

        setTimeout(function() {
          lookingAtGround = false;
        }, 4000);
      }
    }.bind(this));
  }
```

在这段代码中，我们观察`evt.gamma`是否在-1 和 1 之间。如果是这样，他们在看地面。这是地面上一个相当精确的点，如果你发现它太小，很难触发，你可以将范围增加到-1.5 到 1.5…等等。

当他们在这个范围内寻找并且当`lookingAtGround`是`false`时，我们运行我们的主题切换器代码。这将把`currentTheme`调整到我们的`themes`数组的下一个索引号。我们将`lookingAtGround`设置为`true`，并在 4 秒钟后将其调回。这确保我们最多每四秒钟更换一次过滤器。

### 正在检索我们的主摄像头

为了过滤我们周围的世界，我们需要访问智能手机上的“环境”摄像头。我们首先创建一个`<video>`元素，将 autoplay 设置为 true(因为我们希望摄像机直接播放),然后为我们的流设置选项。在选项中，我们将`facingMode`设置为`"environment"`，如果该相机可用，它将使用该相机。如果没有，它将使用自拍风格的相机来代替。当您在没有环境摄像头的笔记本电脑上进行测试时，这很有帮助！(注意，您的笔记本电脑可能会不断切换过滤器，如果是这样，您需要在测试前将其关闭！)

```
video = document.createElement('video');
  video.setAttribute('autoplay', true);

  var options = {
    video: {
      optional: [{facingMode: "environment"}]
    }
  };
```

我们的下一步是使用这些选项实际拉入我们的摄像机馈送。为此，我们使用了[媒体流 API](https://w3c.github.io/mediacapture-main/getusermedia.html) 。这是一组 JavaScript APIs，允许我们从本地音频和视频流中提取数据——非常适合获取我们手机的摄像头流。特别是，我们将使用`getUserMedia`函数。MediaStream API 仍在“W3C 编辑草案”中，并且在不同的浏览器中实现略有不同。这个演示主要集中在 Google Chrome 移动版上，但是为了将来的兼容性，我们得到了一个与我们用户当前浏览器兼容的版本，并将其分配给`navigator.getUserMedia`:

```
navigator.getUserMedia = navigator.getUserMedia ||
  navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
```

然后，只要我们的浏览器理解来自 MediaStream API 的`MediaStreamTrack`，并成功地在我们的浏览器中找到一个兼容的`getUserMedia`函数，我们将开始搜索摄像机数据。

```
if (typeof MediaStreamTrack === 'undefined' && navigator.getUserMedia) {
    alert('This browser doesn\'t support this demo :(');
  } else {
    // Get our camera data!
```

在 MediaStream API 中，我们在`MediaStreamTrack.getSources()`中有一个函数，它从浏览器的设备中检索所有可用的音频和视频源。它可以从连接到您的设备的每个麦克风中检索麦克风数据，以及从每个摄像机中检索视频数据。

我们可以在一个名为`sources`的数组中获得这个函数的返回值。我们循环遍历每个源，寻找那些`kind`等于`"video"`的源。每个信号源将有一个`"audio"`的`kind`或`"video"`。然后我们查看找到的视频是否有一个等于`"environment"`的`facing`属性，如果是，这就是我们更愿意使用的摄像机。我们在 API 中检索它的 ID，然后更新前面的`options`对象，将这个源 ID 作为我们首选的视频流。

```
MediaStreamTrack.getSources(function(sources) {
      for (var i = 0; i !== sources.length; ++i) {
        var source = sources[i];
        if (source.kind === 'video') {
          if (source.facing && source.facing == "environment") {
            options.video.optional.push({'sourceId': source.id});
          }
        }
      }
```

这个`options`物体在幕后看起来是这样的:

```
{
    video: {
      optional: [{facingMode: "environment"}, {sourceId: "thatSourceIDWeRetrieved"}]
    }
  }
```

最后，我们将这些选项连同成功和错误回调一起传递给我们的`navigator.getUserMedia`函数。这将检索我们的视频数据。

```
navigator.getUserMedia(options, streamFound, streamError);
    });
  }
```

### 把我们的摄像放在我们的场景上

一旦我们有了我们的视频流，我们就把它放到我们成功回调的场景中，`streamFound()`。我们首先将`video`元素添加到 DOM 中，将其内容设置为我们返回的视频流，并使其与窗口的宽度和高度相等(因为我们希望以高分辨率读入画布)。

```
function streamFound(stream) {
    document.body.appendChild(video);
    video.src = URL.createObjectURL(stream);
    video.style.width = '100%';
    video.style.height = '100%';
    video.play();
```

在页面中播放摄像机流之后，我们在 JavaScript 中创建一个 canvas 元素，用于处理视频数据。canvas 元素本身从来没有被添加到页面中，它只存在于我们的 JavaScript 中。

我们将画布设置为与视频相同的宽度和高度，四舍五入到最接近的 2 的幂。原因是 Three.js 纹理作为 2 的幂工作得最好。如果您传入不符合这个的其他宽度和高度，那完全没问题，但是您必须使用特定的`minFilter`和`magFilter`选项。我更喜欢将它调整为 2 的幂，以保持这里的简单。

```
canvas = document.createElement('canvas');
  canvas.width = video.clientWidth;
  canvas.height = video.clientHeight;
  canvas.width = nextPowerOf2(canvas.width);
  canvas.height = nextPowerOf2(canvas.height);

  function nextPowerOf2(x) { 
      return Math.pow(2, Math.ceil(Math.log(x) / Math.log(2))); 
  }
```

接下来，我们创建我们的 Three.js 纹理，它将包含我们的流视频镜头，并向其中传递我们的`canvas`元素。我们将我们的`context`变量设置为我们创建的`canvas`元素的上下文，并将我们的纹理的上下文分配给我们的 canva 的上下文。保持同步。

```
context = canvas.getContext('2d');
    texture = new THREE.Texture(canvas);
    texture.context = context;
```

然后我们创建三个. js 平面，我们将使用`THREE.PlaneGeometry`把我们的提要放到上面。我将它设置为 1920×1280 作为我们视频的基本尺寸。

```
var cameraPlane = new THREE.PlaneGeometry(1920, 1280);
```

然后，我们用我们的平面和纹理创建一个`THREE.Mesh`对象。我们将它`-600`放置在 z 轴上，将它从我们的视野中移出，并将其添加到我们的 Three.js 场景中。如果您有不同大小的视频源，您可能需要调整 z 位置，以确保形状填充视口。

```
cameraMesh = new THREE.Mesh(cameraPlane, new THREE.MeshBasicMaterial({
      color: 0xffffff, opacity: 1, map: texture
    }));
    cameraMesh.position.z = -600;

    scene.add(cameraMesh);
  }
```

之后，我们有我们的错误回调函数，如果我们的视频流检索有问题，它将运行一个`console.log`。

```
function streamError(error) {
    console.log('Stream error: ', error);
  }
```

在我们的`init()`函数的最后，你会看到我们的`animate()`函数。这是我们处理视频图像的地方:

```
animate();
```

### 应用过滤器

我们的`animate()`功能从使用`context.drawImage()`将相机中的最新帧绘制到画布上开始:

```
function animate() {
    if (context) {
      context.drawImage(video, 0, 0, canvas.width, canvas.height);
```

从那里，我们可以使用`context.getImageData()`读回我们的画布，并根据我们设置的主题对它保存的数据应用过滤器。下面的代码从我们的黑白过滤器的设置开始，它读入我们的数据，获取图像中每个像素的一般亮度，然后根据每个像素的亮度等级将每个像素过滤为黑色、灰色或白色。这给图片一种卡通/老式报纸的感觉。

```
if (themes[currentTheme] == 'blackandwhite') {
        var imageData = context.getImageData(0, 0, canvas.width, canvas.height);
        var data = imageData.data;

        for (var i = 0; i < data.length; i+=4) {
          var red = data[i],
              green = data[i+1],
              blue = data[i+2],
              luminance = ((red * 299) + (green * 587) + (blue * 114)) / 1000; // Gives a value from 0 - 255
          if (luminance > 175) {
            red = 255;
            green = 255;
            blue = 255;
          } else if (luminance >= 100 && luminance <= 175) {
            red = 190;
            green = 190;
            blue = 190;
          } else if (luminance < 100) {
            red = 0;
            green = 0;
            blue = 0;
          }

          data[i] = red;
          data[i+1] = green;
          data[i+2] = blue;
        }

        imageData.data = data;

        context.putImageData(imageData, 0, 0);
      }
```

看起来是这样的:

![Our black and white reality filter in action](img/0f3d5c1c232d561f7995a5958bd05bf9.png)

下一个主题反转我们的像素，所以白色是黑色，等等。它赋予图像一种 X 射线风格:

```
else if (themes[currentTheme] == 'inverse') {
        var imageData = context.getImageData(0, 0, canvas.width, canvas.height);
        var data = imageData.data;

        for (var i = 0; i < data.length; i+=4) {
          var red = 255 - data[i],
              green = 255 - data[i+1],
              blue = 255 - data[i+2];

          data[i] = red;
          data[i+1] = green;
          data[i+2] = blue;
        }

        imageData.data = data;

        context.putImageData(imageData, 0, 0);
      }
```

看起来是这样的:

![Our inverse reality filter in action](img/d793bfd188570a5df950126418997791.png)

我们的棕褐色主题使用了一个我在网上很多地方看到的公式，给图像一种棕褐色的、老式的色彩感觉。我还通过给每个像素添加随机水平的红色、绿色和蓝色来给图像添加噪声。如果通过棕褐色的像素要比 255 更高的色阶，我把它限制在 255。

```
else if (themes[currentTheme] == 'sepia') {
        var imageData = context.getImageData(0, 0, canvas.width, canvas.height);
        var data = imageData.data;

        for (var i = 0; i < data.length; i+=4) {
          var red = data[i],
              green = data[i+1],
              blue = data[i+2];

          var sepiaRed = (red * 0.393) + (green * 0.769) + (blue * 0.189);
          var sepiaGreen = (red * 0.349) + (green * 0.686) + (blue * 0.168);
          var sepiaBlue = (red * 0.272) + (green * 0.534) + (blue * 0.131);

          var randomNoise = Math.random() * 50;

          sepiaRed += randomNoise;
          sepiaGreen += randomNoise;
          sepiaBlue += randomNoise;

          sepiaRed = sepiaRed > 255 ? 255 : sepiaRed;
          sepiaGreen = sepiaGreen > 255 ? 255 : sepiaGreen;
          sepiaBlue = sepiaBlue > 255 ? 255 : sepiaBlue;

          data[i] = sepiaRed;
          data[i+1] = sepiaGreen;
          data[i+2] = sepiaBlue;
        }

        imageData.data = data;

        context.putImageData(imageData, 0, 0);
      }
```

看起来是这样的:

![Our sepia reality filter in action](img/e426252341a367e39eb4e03d97c2a72b.png)

最后，我最喜欢的效果！“拱廊”风格的一个像素化的形象，使它看起来像一个复古的世界。为了达到这个效果，我调整了 David DeSandro 和 John Schulz 的 [Close Pixelate](http://close-pixelate.desandro.com/) 插件。该插件的原始版本转换一个内嵌图像，并用一个像素化的画布版本替换它。我的版本取而代之的是画布数据，并把它放回同一个画布和上下文中，这样我们就可以在直播视频中使用它。我调整后的版本仍然接受与插件页面上的[相同的参数。它比上面的其他过滤器慢一点，如果我有时间研究它，可能会得到优化。现在，我可以接受一点滞后，让它感觉更复古！给那些想在这个过滤器中应用新选项的人一个提示(例如，把世界变成钻石)——这会让它更加滞后！](http://close-pixelate.desandro.com/)

```
else if (themes[currentTheme] == 'arcade') {
        ClosePixelation(canvas, context, [
          {
            resolution: 6
          }
        ]);
      }
```

看起来是这样的:

![Our pixelated reality filter in action](img/92713cbd3b2095c7b7574ffbe31f4278.png)

最后，我们将 Three.js 的纹理设置为在下一帧更新(因为我们肯定以某种方式改变了它)，并在下一个`requestAnimationFrame()`再次运行`animate()`。我们还运行代码来更新和重新渲染我们的 Three.js 场景。

```
if (video.readyState === video.HAVE_ENOUGH_DATA) {
        texture.needsUpdate = true;
      }
    }

    requestAnimationFrame(animate);

    update();
    render();
  }
```

## 现在是 HTTPS 时间

【2015 年末更新—我跳回这篇文章，添加一条相当重要的新信息 Chrome 现在要求使用摄像头的网页在 HTTPS 提供。所以在尝试运行这个之前，你需要找到一种方法在 HTTPS 上运行你的服务。到目前为止，我使用的一种测试方法是 [ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) ，它可以提供到本地主机的 HTTPS 隧道。我们在[的 SitePoint 这里有一个从任何地方](https://www.sitepoint.com/accessing-localhost-from-anywhere)访问本地主机的指南，可以帮助你开始。

## 在活动

为了能够访问网络摄像头等，似乎你需要让它托管在服务器上，而不是在本地运行。出于测试目的，我使用 ngrok 在我的手机上从我的 Mac 上进行测试。否则，把你的东西 FTP 到某个地方的网络服务器上，然后开始测试！

在你的 Google Cardboard 或其他 VR 耳机中运行它，你应该可以通过我们的黑白过滤器看到你周围的环境。如果你向下看地面，它应该会切换过滤器。这很有趣！这里有一个小的动画 gif 来展示它的作用(在耳机外面，这样你可以看到它在显示什么):

![Our reality filter in action!](img/ddc8d37c6a586f0e42992865b5b59760.png)

## 结论

结合 Google Cardboard、HTML5、JavaScript 和 Three.js 的力量带来了一些不仅限于虚拟现实的非常好的可能性。使用相机输入，你也可以把你周围的世界带入场景！这个最初的想法可以发展到许多其他领域。还可以使用着色器通过 Three.js 本身过滤图像，并可以将增强现实对象添加到您的场景中——这两个想法我将在未来的文章中介绍。

如果你基于这个演示做出了一些非常棒的 AR 体验，请在评论中留言或在 Twitter 上与我联系( [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )，我总是非常想看看！

## 分享这篇文章