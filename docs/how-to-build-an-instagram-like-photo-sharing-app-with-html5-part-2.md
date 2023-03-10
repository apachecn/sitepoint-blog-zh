# 如何用 HTML5 构建类似 Instagram 的照片分享应用程序:第 2 部分

> 原文：<https://www.sitepoint.com/how-to-build-an-instagram-like-photo-sharing-app-with-html5-part-2/>

在第一部分中，我们看了一下 [InstaFuzz](http://blogorama.nerdworks.in/arbit/InstaFuzz/) 应用的一些 UI 布局实现细节。如果你想在本地运行，你可以从[这里](http://sdrv.ms/14B672O)获得该应用的源代码。在这一期中，我们将看看其他一些东西，比如如何使用拖放、文件 API、Canvas 和 Web Workers。

## 拖放

InstaFuzz 支持的功能之一是将图像文件直接拖放到黑色/蓝色的大盒子上。对此的支持是通过处理 CANVAS 元素上的“drop”事件来实现的。当一个文件被拖放到一个 HTML 元素上时，浏览器在该元素上触发“drop”事件，并传入一个 [*数据传输*](http://aka.ms/dataTransfer) 对象，该对象包含一个 [*文件*属性](http://aka.ms/filesproperty)，该属性包含一个对被拖放文件列表的引用。这是在应用程序中如何处理的(“图片”是页面上画布元素的 ID):

```
var pic = $("#picture");
pic.bind("drop", function (e) {
    suppressEvent(e);
    var files = e.originalEvent.dataTransfer.files;
    // more code here to open the file
});

pic.bind("dragover", suppressEvent).bind("dragenter", suppressEvent);

function suppressEvent(e) {
    e.stopPropagation();
    e.preventDefault();
}

```

*files* 属性是 [*File* objects](http://aka.ms/FileObject) 的集合，随后可以与 File API 一起使用来访问文件内容(在下一节中介绍)。我们还处理 *dragover* 和 *dragenter* 事件，并基本上防止这些事件传播到浏览器，从而防止浏览器处理文件丢弃。例如，IE 可能会卸载当前页面并尝试直接打开文件。

## 文件 API

一旦文件被拖放，应用程序会尝试打开图像并在画布中呈现它。它通过使用[文件 API](http://aka.ms/FileAPI) 来实现这一点。文件 API 是 W3C 规范，它允许 web 应用程序以安全的方式从本地文件系统以编程方式访问文件。在 *InstaFuzz* 中，我们使用 [*FileReader* 对象](http://aka.ms/FileReaderObject)读取文件内容作为[数据 URL](http://aka.ms/dataProtocol) 字符串，就像这样使用[*readAsDataURL*](http://aka.ms/readAsDataURLmethod)方法:

```
var reader = new FileReader();

reader.onloadend = function (e2) {
    drawImageToCanvas(e2.target.result);
};

reader.readAsDataURL(files[0]);

```

这里， *files* 是从处理 CANVAS 元素上“drop”事件的函数中检索到的 *File* 对象的集合。因为我们只对单个文件感兴趣，所以我们简单地从集合中挑选第一个文件，忽略其余的文件(如果有的话)。实际的文件内容是异步加载的，一旦加载完成，就会触发 [*onloadend*](http://aka.ms/onloadend) 事件，我们将文件内容作为数据 URL 获取，然后在画布上绘制。

## 渲染过滤器

这里的核心功能当然是应用过滤器。为了能够将过滤器应用于图像，我们需要一种方法来访问图像中的单个像素。在我们访问像素之前，我们需要将图像渲染到画布上。因此，让我们首先来看看呈现用户在 canvas 元素上选择的图像的代码。

### 将图像渲染到画布上

canvas 元素通过 [*drawImage*](http://aka.ms/drawImage) 方法支持 *Image* 对象的渲染。为了在*图像*实例中加载图像文件， *InstaFuzz* 使用以下实用程序:

```
App.Namespace.define("InstaFuzz.Utils", {
    loadImage: function (url, complete) {
        var img = new Image();
        img.src = url;
        img.onload = function () {
            complete(img);
        };
    }
});

```

这允许应用程序使用如下代码从 URL 加载图像对象:

```
function drawImageToCanvas(url) {
    InstaFuzz.Utils.loadImage(url, function (img) {
        // save reference to source image
        sourceImage = img;
        mainRenderer.clearCanvas();
        mainRenderer.renderImage(img);
        // load image filter previews
        loadPreviews(img);
    });
}

```

这里， *mainRenderer* 是从 *filter-renderer.js* 中定义的 *FilterRenderer* 构造函数创建的实例。该应用程序使用 *FilterRenderer* 对象来管理画布元素——包括预览窗格和右侧的主画布元素。 *FilterRenderer* 上的 *renderImage* 方法定义如下:

```
FilterRenderer.prototype.renderImage = function (img) {
    var imageWidth = img.width;
    var imageHeight = img.height;
    var canvasWidth = this.size.width;
    var canvasHeight = this.size.height;
    var width, height;

    if ((imageWidth / imageHeight) >= (canvasWidth / canvasHeight)) {
        width = canvasWidth;
        height = (imageHeight * canvasWidth / imageWidth);
    } else {
        width = (imageWidth * canvasHeight / imageHeight);
        height = canvasHeight;
    }

    var x = (canvasWidth - width) / 2;
    var y = (canvasHeight - height) / 2;
    this.context.drawImage(img, x, y, width, height);
};

```

这看起来似乎有很多代码，但它最终所做的只是考虑到图像的纵横比，找出在可用屏幕区域呈现图像的最佳方式。真正在画布上呈现图像的关键代码出现在方法的最后一行。 *context* 成员是指通过调用 canvas 对象的 [*getContext*](http://aka.ms/getContext) 方法从 canvas 对象中获取的 2D 上下文。

### 从画布中提取像素

现在图像已经渲染好了，我们需要访问单个像素，以便应用所有可用的不同过滤器。这很容易通过在画布的上下文对象上调用 [*getImageData*](http://aka.ms/getImageData) 来获得。下面是 *InstaFuzz* 如何从 *instafuzz.js* 调用它。

```
var imageData = renderer.context.getImageData(
    0, 0,
    renderer.size.width,
    renderer.size.height);

```

由 *getImageData* 返回的对象通过其*数据*属性提供对单个像素的访问，该属性又是一个类似数组的对象，包含一组字节值，其中每个值代表为单个像素的单通道渲染的颜色。每个像素用 4 个字节表示，指定红色、绿色、蓝色和 alpha 通道的值。它还有一个返回缓冲区长度的[](http://aka.ms/lengthProperty)**属性。如果你有一个 2D 坐标，你可以很容易地用下面的代码把它转换成这个数组的索引。每个通道的颜色强度值范围从 0 到 255。下面是来自 *filters.js* 的实用函数，它接受图像数据对象以及调用者感兴趣的像素的 2D 坐标作为输入，并返回包含颜色值的对象:**

```
function getPixel(imageData, x, y) {
    var data = imageData.data, index = 0;
    // normalize x and y and compute index
    x = (x < 0) ? (imageData.width + x) : x;
    y = (y < 0) ? (imageData.height + y) : y;
    index = (x + y * imageData.width) * 4;

    return {
        r: data[index],
        g: data[index + 1],
        b: data[index + 2]
    };
}

```

### 应用过滤器

现在我们已经可以访问单个像素，应用过滤器是相当简单的。例如，这里的函数对图像应用了加权灰度滤镜。它只是从红色、绿色和蓝色通道中选取强度，并在每个通道上应用一个倍增因子后将它们相加，然后将结果分配给所有 3 个通道。

```
// "Weighted Grayscale" filter
Filters.addFilter({
    name: "Weighted Grayscale",
    apply: function (imageData) {
        var w = imageData.width, h = imageData.height;
        var data = imageData.data;
        var index;

        for (var y = 0; y < h; ++y) {
            for (var x = 0; x < w; ++x) {
                index = (x + y * imageData.width) * 4;
                var luminance = parseInt((data[index + 0] * 0.3) +
                                         (data[index + 1] + 0.59) +
                                         (data[index + 2] * 0.11));
                data[index + 0] = data[index + 1] =
                    data[index + 2] = luminance;
            }

            Filters.notifyProgress(imageData, x, y, this);
        }

        Filters.notifyProgress(imageData, w, h, this);
    }
});

```

一旦应用了过滤器，我们就可以通过调用 [*putImageData*](http://aka.ms/putImageData) 方法，传入修改后的图像数据对象，将过滤器反映在画布上。虽然加权灰度过滤器相当简单，但大多数其他过滤器使用一种称为*卷积*的图像处理技术。所有滤波器的代码都可以在 *filters.js* 中找到，卷积滤波器从 C 代码[移植到这里](http://lodev.org/cgtutor/filtering.html)。

## 网络工作者

正如您可能想象的那样，进行所有这些数字运算以应用过滤器可能需要很长时间才能完成。例如，*运动模糊*滤镜使用 9×9 滤镜矩阵来计算每个像素的新值，实际上它是所有滤镜中占用 CPU 最多的滤镜。如果我们要在浏览器的 UI 线程上完成所有这些计算，那么每次应用过滤器时，应用程序基本上会冻结。为了提供快速响应的用户体验，该应用使用现代浏览器对 W3C [Web Workers](http://aka.ms/WebWorkers) 的支持，将核心图像处理任务委托给后台脚本。

Web workers 允许 Web 应用程序在与 UI 线程并行执行的后台任务中运行脚本。工作线程和 UI 线程之间的通信是通过使用 [*postMessage*](http://aka.ms/postMessage) API 传递消息来完成的。在两端(即 UI 线程和工作线程),这表现为您可以处理的事件通知。您只能在 worker 和 UI 线程之间传递“数据”,也就是说，您不能传递任何与用户界面有关的东西——例如，您不能从 UI 线程向 worker 传递 DOM 元素。

在 *InstaFuzz* 中，worker 在文件 *filter-worker.js* 中实现。它在 worker 中所做的只是处理 [*onmessage*](http://aka.ms/onmessage) 事件并应用过滤器，然后通过 *postMessage* 将结果传回。事实证明，即使我们不能传递 DOM 元素(这意味着我们不能只是将一个 CANVAS 元素交给 worker 来应用过滤器)，我们实际上可以传递由我们前面讨论的 *getImageData* 方法返回的图像数据对象。下面是来自 *filter-worker.js* 的过滤处理代码:

```
importScripts("ns.js", "filters.js");

var tag = null;

onmessage = function (e) {
    var opt = e.data;
    var imageData = opt.imageData;
    var filter;
    tag = opt.tag;
    filter = InstaFuzz.Filters.getFilter(opt.filterKey);
    var start = Date.now();
    filter.apply(imageData);
    var end = Date.now();

    postMessage({
        type: "image",
        imageData: imageData,
        filterId: filter.id,
        tag: tag,
        timeTaken: end - start
    });
}

```

第一行通过调用 [*importScripts*](http://aka.ms/importScripts) 拉入一些工人依赖的脚本文件。这类似于使用 Script 标签在 HTML 文档中包含 JavaScript 文件。然后，我们为 *onmessage* 事件设置一个处理程序，作为响应，我们简单地应用所讨论的过滤器，并通过调用 *postMessage* 将结果传递回 UI 线程。够简单！

初始化 worker 的代码在 *instafuzz.js* 中，如下所示:

```
var worker = new Worker("js/filter-worker.js");
```

不多吧？当工人向 UI 线程发送消息时，我们通过为工人对象上的 *onmessage* 事件指定一个处理程序来处理它。以下是如何在 *InstaFuzz* 中完成的:

```
worker.onmessage = function (e) {
    var isPreview = e.data.tag;
    switch (e.data.type) {
        case "image":
            if (isPreview) {
                previewRenderers[e.data.filterId].
                    context.putImageData(
                        e.data.imageData, 0, 0);
            } else {
                mainRenderer.context.putImageData(
                    e.data.imageData, 0, 0);
            }
            break;
        // more code here
    }

};

```

代码应该是不言自明的。它简单地选取工作人员发送的图像数据对象，并将其应用于相关画布的上下文对象，使修改后的图像呈现在屏幕上。使用 worker 安排过滤器进行转换也同样简单。下面是在 *InstaFuzz* 中执行该功能的例程:

```
function scheduleFilter(filterId,
                             renderer,
                             img, isPreview,
                             resetRender) {
    if (resetRender) {
        renderer.clearCanvas();
        renderer.renderImage(img);
    }

    var imageData = renderer.context.getImageData(
        0, 0,
        renderer.size.width,
        renderer.size.height);
    worker.postMessage({
        imageData: imageData,
        width: imageData.width,
        height: imageData.height,
        filterKey: filterId,
        tag: isPreview
});

}

```

## 包装它

*InstaFuzz* 的源码可以在[这里](http://sdrv.ms/11110mf)下载。我们看到，相当复杂的用户体验如今可以通过 HTML5 技术实现，如 Canvas、拖放、文件 API 和 Web Workers。几乎所有现代浏览器都支持所有这些技术。我们在这里没有提到的一件事是让应用程序兼容旧浏览器的问题。说实话，这是一项重要但必要的任务，我希望能够在以后的文章中讨论。

本文是来自 Internet Explorer 团队的 HTML5 技术系列的一部分。[试用](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200210648)本文中的概念，进行三个月的免费浏览器堆栈跨浏览器测试@ [http://modern。IE](http://modern.ie/)

## 分享这篇文章**