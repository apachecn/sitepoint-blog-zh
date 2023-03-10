# 使用 JpegCamera 和 Canvas 访问用户的相机

> 原文：<https://www.sitepoint.com/jpegcamera-and-canvas/>

*本文由[丹王子](https://www.sitepoint.com/author/dprince)同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在过去的十年中，浏览器供应商已经引入了各种新的 API，使我们作为程序员能够创建更丰富、更流畅的体验。其中之一是 [`getUserMedia`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) API，它使**能够访问用户的音频和视频设备**。然而，就[浏览器兼容性](http://caniuse.com/#search=getusermedia)而言*还没有完全到位*。

考虑到这一点， [Adam Wróbel](https://github.com/amw) 编写了 [JpegCamera](https://github.com/amw/jpeg_camera) ，这是一个考虑到浏览器之间与用户相机交互的不同警告的库，并为不支持访问客户端媒体的情况提供了后备。

在这篇文章中，我们将看到如何通过使用 JpegCamera，结合 HTML `canvas`元素功能，我们可以构建 Instagram 的[布局](https://itunes.apple.com/nz/app/layout-from-instagram/id967351793?mt=8)应用程序的克隆:

![Screenshot of the Layout-like app using JpegCamera and Canvas.](img/175db1be06b9207c30573151c6306b76.png "A Layout-like app using JpegCamera and Canvas")
*演示布局类应用*

演示的源代码可以从 [Github](https://github.com/sitepoint-editors/layout) 下载。

## JpegCamera 库

JpegCamera 允许您将用户的摄像头作为应用程序的一部分来访问，如果浏览器不支持`getUserMedia()`，则优雅地降级到 Flash fallback。

的第一步是在项目中包含必要的脚本。

这个库依赖于 [SWF 对象](https://github.com/swfobject/swfobject)和 [Canvas to Blob](https://github.com/blueimp/JavaScript-Canvas-to-Blob) 库，这两个库都是从项目的 Github 页面下载的 zip 文件的一部分。然而，在同一个 zip 中，有一个带有依赖关系的*版本的脚本，它提供了与在页面中加载三个脚本相同的功能。*

考虑到这一点，您可以包含三个需要的脚本。

```
<script src="/jpeg_camera/swfobject.min.js" type="text/javascript"></script>
<script src="/jpeg_camera/canvas-to-blob.min.js" type="text/javascript"></script>
<script src="/jpeg_camera/jpeg_camera.min.js" type="text/javascript"></script> 
```

或者只选择一种脚本。

```
<script type="text/javascript" src="js/libs/jpeg_camera/jpeg_camera_with_dependencies.min.js"></script> 
```

对于生产环境，与开发期间不同，后者似乎是更好的选择。

一旦包含了库，您可以使用全局 JpegCamera 对象来检查相机的可用性，如果不可用，选择如何管理回退。

如果访问被授权，您可以使用`JpegCamera()`构造函数设置一个监听器，以备摄像机准备就绪时使用。

`JpegCamera()`构造函数将一个 CSS 选择器作为一个参数，它应该标识用于摄像机流的容器。

下面的代码片段显示了这样做的代码:

```
(function() {
    if(!window.JpegCamera) {
      alert('Camera access is not available in your browser');
    } else {
      JpegCamera('.camera')
        .ready(function(resolution) {
          // ...
        }).error(function() {
          alert('Camera access was denied');
        });
    }
})(); 
```

这样，您可以将应用程序设置为仅在相机准备就绪时启动，或者让用户知道他们需要不同的浏览器或者需要为应用程序启用相机访问。

在`ready`回调函数中，设备的摄像头分辨率作为第一个参数传递。如果我们正在构建的应用程序依赖于设备的摄像头质量(即:是否支持高清捕捉)，这将非常有用。

与此同时，`error`回调接收一条`string`消息作为参数，解释发生了什么。如果您需要在出现错误时向用户显示解释，您可以使用库提供的消息。

除此之外，JpegCamera API 还提供了以下方法:

*   `capture()`:这是拍照的方法。它将图像本身作为一个`Snapshot`对象返回(`JpegCamera`用于图像的类)。
*   `show()`:一旦你拍摄了照片，你获得的`Snapshot`对象允许你通过调用它的`show()`方法在页面中显示图像。图像将显示在初始化相机时指定的同一容器中。
*   `showStream()`:如果容器中当前显示快照，`showStream()`隐藏图像并显示流。
*   `getCanvas()`:接受一个回调函数作为参数，该函数将接收带有捕获图像的`canvas`元素作为参数。

让我们深入一个示例应用程序，它展示了 JpegCamera 允许我们做什么。

## 构建应用程序

这个演示应用程序模拟了 Layout 所做的事情:它允许用户拍摄照片，并通过组合它们来生成新的图像。在我们的版本中，组合图像可以通过点击下载。

应用程序结构基于[模块模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)。这种模式给我们带来了几个好处:

1.  它允许在每个应用程序组件之间有一个清晰的分离。
2.  它通过只公开其他方法和属性严格要求的方法和属性来保持我们的全局范围的干净。换句话说，我们开始使用私有属性。

您会注意到，我将三个参数传递给自调用函数:

```
(window, document, jQuery) 
```

并且这些参数被接收:

```
function(window, document, $) 
```

通过`window`和`document`的原因是为了**缩小**的目的。如果我们将它们作为参数传递，那么它们中的每一个都可以被替换为一个单独的字符。如果我们只是直接引用这些全局对象，那么 minifier 就不能用更短的名字来代替它们。

对于`jQuery`，我们这样做是为了避免与其他可能也使用`$`作为主要功能的库发生冲突(例如:[原型](http://prototypejs.org/))。

在`Layouts`和`Custom`模块的顶部，您会看到如下内容:

```
if(!window.LayoutApp) {
  window.LayoutApp = {};
} 
```

这有两个原因:

1.  我们防止模块产生错误，以防我们没有在`index.html`中正确包含脚本。
2.  我们通过使模块成为主模块的一部分来保持全局范围的整洁，并且只在应用程序启动时才可用。

应用程序逻辑分为三个模块:

*   **App** 模块
*   **布局**模块
*   **自定义**模块

这三个模块连同我们的库必须包含在我们的`index.html`中，如下所示:

```
<!-- index.html -->
<script type="text/javascript" src="js/libs/jquery-1.12.1.min.js"></script>
<script type="text/javascript" src="js/libs/jpeg_camera/jpeg_camera_with_dependencies.min.js"></script>
<script type="text/javascript" src="js/src/custom.js"></script>
<script type="text/javascript" src="js/src/layouts.js"></script>
<script type="text/javascript" src="js/src/app.js"></script> 
```

还有一小段代码来启动应用程序。

```
<!-- index.html -->
<script type="text/javascript"> (function() {
    LayoutApp.init();
  })(); </script> 
```

现在，让我们逐一回顾各个模块。

### 应用程序模块

该模块包含主要的应用程序逻辑。它管理用户与相机的交互，根据拍摄的照片生成布局，并使用户能够下载生成的图像。

一切都从**应用**模块开始，使用`init`方法。

```
// App module (app.js)
initCamera = function () {
  if (!window.JpegCamera) {
    alert('Camera access is not available in your browser');
  } else {
    camera = new JpegCamera('#camera')
      .ready(function (resolution) {})
      .error(function () {
      alert('Camera access was denied');
    });
  }
},

bindEvents = function () {
  $('#camera-wrapper').on('click', '#shoot', capture);
  $('#layout-options').on('click', 'canvas', download);
};

init: function () {
  initCamera();
  bindEvents();
} 
```

调用时，`ìnit()`通过调用以下方法启动应用程序:

1.  `initCamera()`启动相机(如果有),或显示警告。
2.  `bindEvents()`设置必要的事件监听器:
    1。第一个点击**拍摄**按钮拍照的人。
    2。第二个是当点击其中一个组合图像时生成下载。

```
capture = function () {
  var snapshot = camera.capture();
  images.push(snapshot);
  snapshot.get_canvas(updateView);
}, 
```

当用户点击*拍摄*时，`capture()`被调用。`capture()`使用[快照的类](https://amw.github.io/jpeg_camera/doc/class/Snapshot.html)方法`getCanvas()`作为回调`updateView()`函数传递。

```
updateView = function (canvas) {
  canvas.selected = true;
  canvases.push(canvas);

  if (!measuresSet) {
    setCanvasMeasures(canvas);
    measuresSet = true;
  }

  updateGallery(canvas);
  updateLayouts(canvas);
}, 
```

反过来，`updateView()`缓存新的`canvas`对象(参见`updateGallery()`)并通过调用`updateLayouts()`用新的图像更新布局，这是*施展魔法*的方法。

`updateLayouts()`依靠以下三种方法:

*   考虑到已经拍摄了多少张照片，这个选项为照片定义了足够的宽度和高度。
*   `setSourceCoordinates()`:通过检查图像测量，返回图像中心的坐标。
*   `setTargetCoordinates()`:这个函数考虑了要绘制的图像的索引，并返回图像将在目标画布上绘制的坐标。

除此之外，`calculateCoeficient()`通过比较源画布度量和目标画布度量，注意保持原始图像和要生成的图像之间的比例。

最后，`updateLayout()`使用来自上述四个函数的数据通过`context.drawImage()`在新的画布中绘制图像。要使用的实现将是使用其[八个参数](https://developer.mozilla.org/en/docs/Web/API/CanvasRenderingContext2D/drawImage)的实现。这意味着我们指定源坐标、源度量、目标坐标和目标度量。

### 布局模块

`Layouts`模块提供了*基本的*布局数据，以及一些辅助功能。

因为我们希望保持我们的作用域干净，并且只与其他模块共享严格必要的内容，`Layouts`模块通过它的 getters 来访问`App`模块需要的属性。

```
// Layouts module (layouts.js)
var CANVAS_MAX_MEASURE = 200,
    LAYOUT_TYPES = {
      HORIZONTAL: 'horizontal',
      VERTICAL: 'vertical'
    },
    LAYOUTS = [
      {
        type: LAYOUT_TYPES.VERTICAL
      },
      {
        type: LAYOUT_TYPES.HORIZONTAL
      }
    ];

return {

   getCanvasMaxWidth: function() {
     return CANVAS_MAX_MEASURE;
   },

   getLayouts: function() {
     return LAYOUTS.concat(Custom.getCustomLayouts());
   },

   isHorizontal: function(layout) {
     return layout.type === LAYOUT_TYPES.HORIZONTAL;
   },

   isVertical: function(layout) {
     return layout.type === LAYOUT_TYPES.VERTICAL;
   },

   isAvailable: function(layout, totalImages) {
     return !layout.minImages || layout.minImages <= totalImages;
   }
 } 
```

如上所述，没有一个模块可以改变`Layouts`模块中的内容，但是应用程序运行所需的一切都是现成的。

下面是这些方法对应用程序的贡献:

*   `getCanvasMaxWidth()`:为了保持图像的整洁，我为它们确定了一个默认宽度，并将其分配给`CANVAS_MAX_MEASURE`。该值在`App`模块中用于定义*组合*图像测量。关于`App`模块中实际的*数学运算*，请参见下面的代码片段。

```
// App module (app.js)
setCanvasMeasures = function (canvas) {
    measures.height = canvas.height * MAX_MEASURE / canvas.width;
}, 
```

通过这种方式，我们的*组合*图像可以有我们喜欢的任何尺寸，独立于我们从 JpegCamera 获得的图像有多大，并且我们通过保持刚刚拍摄的图片的纵横比来防止任何拉伸或收缩。

*   `getLayouts()`:返回由用户拍摄的图片生成*合成*图像的版面。它返回应用程序*的默认*布局，以及可以添加到`Custom`模块中的自定义布局(稍后将详细介绍)。
*   `isHorizontal()`和`isVertical()`:应用程序中的默认布局是通过设置其`type`属性来定义的，该属性的值来自`LAYOUT_TYPES`。通过接收一个`layout`对象作为参数，并依靠这个常量，这两个方法计算`layout.type === LAYOUT_TYPES.HORIZONTAL`和`layout.type === LAYOUT_TYPES.VERTICAL`。基于这些函数的返回值，`App`模块为*组合的*图像定义测量、源坐标和目标坐标。
*   `isAvailable()`:根据用户拍摄的图像数量并考虑布局的`minImages`属性，该函数决定是否渲染布局。如果用户拍摄了与最小值一样多或更多的图像，那么布局将被渲染。否则，如果用户没有拍摄足够多的照片或者布局没有定义`minImages`属性，那么将生成`combined`图像。

### 自定义模块

`Custom`模块允许添加新的布局，其应用程序的实现主要有三种方法:`setImageMeasures()`、`setSourceCoordinates()`和`setTargetCoordinates()`。

这可以通过在`Custom`模块的`CUSTOM_LAYOUTS`数组中添加一个新的布局对象来实现，该布局对象有自己的上述三种方法的实现。

```
// Custom module (custom.js)
var CUSTOM_LAYOUTS = [
  /**
  * Place your custom layouts as below
  */
  // ,
  // {
  //   setImageMeasures: function (layout, targetCanvas, imageIndex) {
  //     return {
  //       height: 0,
  //       width: 0
  //     }
  //   },
  //   setSourceCoordinates: function (canvas, layout, imageWidth, imageHeight, imageIndex) {
  //     return {
  //       x: 0,
  //       y: 0
  //     }
  //   },
  //   setTargetCoordinates: function (targetCanvas, layout, imageWidth, imageHeight, imageIndex) {
  //     return {
  //       x: 0,
  //       y: 0
  //     }
  //   }
  // }
]; 
```

应用程序中的每个*覆盖*功能，将检查正在绘制的布局是否包含此功能。

请看`App.setImageMeasures()`中是如何做到的:

```
// App module (app.js)
setImageMeasures = function (layout, targetCanvas, imageIndex) {
  if (isFunction(layout.setImageMeasures)) {
    return layout.setImageMeasures(layout, targetCanvas, imageIndex);
  } else {
    if(Layouts.isVertical(layout)) {
      return {
        width: $(targetCanvas).width(),
        height: $(targetCanvas).height() / images.length
      };
    } else if(Layouts.isHorizontal(layout)) {
      return {
        width: $(targetCanvas).width() / images.length,
        height: $(targetCanvas).height()
      };
    }

    return {
      width: $(targetCanvas).width(),
      height: $(targetCanvas).height()
    };
  }
} 
```

这里我们简单地检查定制布局是否有自己的函数实现来定义图像度量，如果有，就调用它。

这是由`isFunction()`助手实现的，它通过检查类型来检查接收到的参数是否确实是一个函数。

```
// App module (app.js)
isFunction = function(f) {
  return typeof f === 'function';
} 
```

如果当前模块不包含自己的`setImageMeasures()`实现，应用程序继续运行并根据布局类型(或者`HORIZONTAL`或者`VERTICAL`)设置测量。

同样的流程后面是`setSourceCoordinates()`和`setTargetCoordinates()`。

新的布局可以确定要从拍摄的图像中裁剪的部分的大小、从哪个坐标以及它将被放置在目标画布上的什么位置。

一个重要的细节是要记住，定制布局方法必须返回与原始方法具有相同属性的对象。

更清楚地说，您的自定义实现`setImageMeasures()`应该以这种格式返回一些内容:

```
{
  height: 0, // height in pixels
  width: 0 // width in pixels
} 
```

### 创建自定义布局

让我们试着创建一个**自定义布局**。你可以在这里看到这个文件[的完整代码清单。](https://github.com/sitepoint-editors/layout/blob/master/js/src/custom.js)

正如在`Layouts`模块部分看到的，布局可以定义一个`minImages`属性。在这种情况下，我们将其设置为 3。我们也让第一个图像覆盖目标画布的 60%，而接下来的两个图像将分割剩余的 40%:

```
{
  minImages: 3,
  imageData: [
    {
      widthPercent: 60,
      heightPercent: 100,
      targetX: 0,
      targetY: 0
    },
    {
      widthPercent: 20,
      heightPercent: 100,
      targetX: 120,
      targetY: 0
    },
    {
      widthPercent: 20,
      heightPercent: 100,
      targetX: 160,
      targetY: 0
    },
  ],
  // ... 
```

为了实现这一点，让我们应用一个简单的三法则，使用`targetCanvas`度量:

```
// Custom module (custom.js)
setImageMeasures: function (layout, targetCanvas, imageIndex) {
  var imageData = this.imageData[imageIndex];
  if( imageData) {
      return {
        width: imageData.widthPercent * $(targetCanvas).width() / 100,
        height: imageData.heightPercent * $(targetCanvas).height() / 100
      };
  }

  return {
    height: 0,
    width: 0
  }
}, 
```

因为所有的函数都接收当前正在处理的图像的编号(`imageIndex`)作为参数，所以我们可以任意确定每张照片的大小、裁剪的源坐标以及原始图像部分在目标画布中放置的坐标。

在没有数据与特定的`imageIndex`相关联的情况下，我们可以返回一个两个属性都设置为 0 的对象。这样，如果用户拍摄的照片比自定义布局中定义的多，那么组合的*和*图像仍然会很好看。

让我们覆盖另外两个函数:

**setSourceCoordinates()**
假设我们想要包含图像的中心及其所有垂直内容，我们将返回一个对象，其中 x 设置为 50，y 设置为 0。

```
setSourceCoordinates: function (canvas, layout, imageWidth, imageHeight, imageIndex) {
  return {
    x: 50,
    y: 0
  }
}, 
```

**setTargetCoordinates()**
既然我们知道画布的尺寸，让我们手动定义它们在目标画布中的位置。

```
setTargetCoordinates: function (targetCanvas, layout, imageWidth, imageHeight, imageIndex) {
  var imageData = this.imageData[imageIndex];

  if (imageData) {
    return {
      x: imageData.targetX,
      y: imageData.targetY
    }
  }

  return {
    x: 0,
    y: 0
  }
} 
```

您可能会同意，这个例子有很大的改进空间，但这应该足以让您开始。

## 结论

正如我们所见，JpegCamera 消除了在应用程序中使用用户摄像头的痛苦，而无需担心跨浏览器兼容性。

将它作为项目的一部分就像向页面添加所需的脚本一样简单，使用它只需要理解四种 API 方法。编写一个有趣的应用程序只需要几百行代码！

你呢，你曾经需要和用户媒体打交道吗？你渴望尝试实现自己的布局吗？请在评论中告诉我！

## 分享这篇文章