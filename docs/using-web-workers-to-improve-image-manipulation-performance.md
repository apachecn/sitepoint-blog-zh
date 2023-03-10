# 使用 Web Workers 提高图像处理性能

> 原文：<https://www.sitepoint.com/using-web-workers-to-improve-image-manipulation-performance/>

今天我想谈谈 HTML5 中的图片操作，使用纯 JavaScript。

## 测试案例

测试应用程序很简单。左边是要操作的图片，右边是更新后的结果(应用了棕褐色色调效果):

![figure1](img/de2ef47008acd4300353ae6520f258a7.png)

页面本身很简单，描述如下:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>PictureWorker</title>

    <link href="default.css" rel="stylesheet" />
</head>
<body id="root">
    <div id="sourceDiv">
        <img id="source" src="mop.jpg" />
    </div>
    <div id="targetDiv">
        <canvas id="target"></canvas>
    </div>
    <div id="log"></div>
</body>
</html>
```

应用棕褐色调效果的整个过程需要您为现有源图片的每个像素计算一个新的 RGB 值，然后用 id="target "将其呈现在

<canvas>标签上。下面是我们用来从像素的现有 RGB 值创建新 RGB 值的公式:</canvas>

finalRed=(红色* 0.393) +(绿色* 0.769) +(蓝色* 0.189)；
finalGreen =(红色* 0.349) +(绿色* 0.686) +(蓝色* 0.168)；
finalBlue=(红色* 0.272) +(绿色* 0.534) +(蓝色* 0.131)；

为了让它更真实一点，我给我的棕褐色公式添加了一点随机性。我创建了一个范围从 0.5 到 1 的噪波值，它决定了我的最终像素输出与通过上述公式计算的 RGB 值匹配的程度，以及它保留其原始 RGB 值的程度。

```
function noise() {
    //Returns a value between 0.5 and 1
    return Math.random() * 0.5 + 0.5;
};

function colorDistance(scale, dest, src) {
    // returns a red, blue or green value for the 'sepia' pixel
    // which is a weighted average of the original value and the calculated value
    return (scale * dest + (1 - scale) * src);
};

var processSepia = function (pixel) {
    // takes a given pixel and updates its red, blue and green values
    // using a randomly weighted average of the initial and calculated red/blue/green values
    pixel.r = colorDistance(noise(), (pixel.r * 0.393) + (pixel.g * 0.769) + (pixel.b * 0.189), pixel.r);
    pixel.g = colorDistance(noise(), (pixel.r * 0.349) + (pixel.g * 0.686) + (pixel.b * 0.168), pixel.g);
    pixel.b = colorDistance(noise(), (pixel.r * 0.272) + (pixel.g * 0.534) + (pixel.b * 0.131), pixel.b);
};
```

## 强力

显然，第一个解决方案是使用暴力，用一个函数将前面的代码应用到每个像素上。要访问像素，您可以使用包含以下代码的画布上下文，它创建了一个指向源 img 和目标画布的指针:

```
var source = document.getElementById("source");

    source.onload = function () {
        var canvas = document.getElementById("target");
        canvas.width = source.clientWidth;
        canvas.height = source.clientHeight;

  // ... tempContext is the 2D context of canvas
        tempContext.drawImage(source, 0, 0, canvas.width, canvas.height);

        var canvasData = tempContext.getImageData(0, 0, canvas.width, canvas.height);
        var binaryData = canvasData.data;
    }
```

此时，binaryData 对象包含一个包含每个像素的数组，可用于快速直接在画布上读取或写入数据。考虑到这一点，我们可以用下面的代码来应用整个效果:

```
var source = document.getElementById("source");

    source.onload = function () {
        var start = new Date();

        var canvas = document.getElementById("target");
        canvas.width = source.clientWidth;
        canvas.height = source.clientHeight;

        if (!canvas.getContext) {
            log.innerText = "Canvas not supported. Please install a HTML5 compatible browser.";
            return;
        }

        var tempContext = canvas.getContext("2d");
        // len is the number of items in the binaryData array
        // it is 4 times the number of pixels in the canvas object
        var len = canvas.width * canvas.height * 4;

        tempContext.drawImage(source, 0, 0, canvas.width, canvas.height);

        var canvasData = tempContext.getImageData(0, 0, canvas.width, canvas.height);
        var binaryData = canvasData.data;

        // processSepia is a variation of the previous version. See below
        processSepia(binaryData, len);

        tempContext.putImageData(canvasData, 0, 0);
        var diff = new Date() - start;
        log.innerText = "Process done in " + diff + " ms (no web workers)";

     }
```

*processSepia* 函数只是前一个函数的变体:

```
var processSepia = function (binaryData, l) {
    for (var i = 0; i < l; i += 4) {
        var r = binaryData[i];
        var g = binaryData[i + 1];
        var b = binaryData[i + 2];

        binaryData[i] = colorDistance(noise(), (r * 0.393) + (g * 0.769) + (b * 0.189), r);
        binaryData[i + 1] = colorDistance(noise(), (r * 0.349) + (g * 0.686) + (b * 0.168), g);
        binaryData[i + 2] = colorDistance(noise(), (r * 0.272) + (g * 0.534) + (b * 0.131), b);
    }
};
```

借助该解决方案，在我的英特尔至尊处理器(12 核)上，主进程耗时 150 毫秒，并且显然只使用了一个处理器:

![figure2](img/8b11968eab4d1bf8833ee8332ab8c0e4.png)

## 输入 web workers

在处理 SIMD(单指令多数据)时，最好的方法是使用并行化方法，尤其是当您希望使用资源有限的低端硬件(如电话设备)时。

在 JavaScript 中，要享受并行化的强大功能，您必须使用 Web Workers。我的朋友大卫·鲁塞就这个主题写了一篇出色的论文。

图片处理非常适合并行化，因为(就像我们的棕褐色调函数一样)每个处理都是独立的。因此，以下方法是可行的:

![figure3](img/4e084e226195c5ecfb1435e269fa02fa.png)

为此，首先必须创建一个 tools.js 文件，供其他脚本引用。

```
// add the below functions to tools.js
function noise() {
    return Math.random() * 0.5 + 0.5;
};

function colorDistance(scale, dest, src) {
    return (scale * dest + (1 - scale) * src);
};

var processSepia = function (binaryData, l) {
    for (var i = 0; i < l; i += 4) {
        var r = binaryData[i];
        var g = binaryData[i + 1];
        var b = binaryData[i + 2];

        binaryData[i] = colorDistance(noise(), (r * 0.393) + (g * 0.769) + (b * 0.189), r);
        binaryData[i + 1] = colorDistance(noise(), (r * 0.349) + (g * 0.686) + (b * 0.168), g);
        binaryData[i + 2] = colorDistance(noise(), (r * 0.272) + (g * 0.534) + (b * 0.131), b);
    }
};
```

这个脚本的要点是画布数据的一部分，即当前块需要处理的部分，由 JavaScript 克隆并传递给 worker。工作人员不是在处理原始源代码，而是在处理它的副本(使用[结构化克隆算法](https://www.w3.org/TR/html5/infrastructure.html#safe-passing-of-structured-data))。复制本身真的很快，而且仅限于图片的特定部分。

主客户端页面(default.js)必须创建四个工人，并给他们图片的正确部分。然后，每个工作者将使用消息传递 API(*postMessage/on message*)回调主线程中的函数，以返回结果:

```
var source = document.getElementById("source");

source.onload = function () {

    // We use var start at the beginning of the code and stop at the end to measure turnaround time

    var start = new Date();

    var canvas = document.getElementById("target");
    canvas.width = source.clientWidth;
    canvas.height = source.clientHeight;

    // Testing canvas support
    if (!canvas.getContext) {
        log.innerText = "Canvas not supported. Please install a HTML5 compatible browser.";
        return;
    }

    var tempContext = canvas.getContext("2d");
    var len = canvas.width * canvas.height * 4;

    // Drawing the source image into the target canvas
    tempContext.drawImage(source, 0, 0, canvas.width, canvas.height);

    // If workers are not supported
    // Perform all calculations in current thread as usual
    if (!window.Worker) {
        // Getting all the canvas data
        var canvasData = tempContext.getImageData(0, 0, canvas.width, canvas.height);
        var binaryData = canvasData.data;

        // Processing all the pixel with the main thread
        processSepia(binaryData, len);

        // Copying back canvas data to canvas
        tempContext.putImageData(canvasData, 0, 0);

        var diff = new Date() - start;
        log.innerText = "Process done in " + diff + " ms (no web workers)";

        return;
    }

    // Let say we want to use 4 workers
    // We will break up the image into 4 pieces as shown above, one for each web-worker
    var workersCount = 4;
    var finished = 0;
    var segmentLength = len / workersCount; // This is the length of array sent to the worker
    var blockSize = canvas.height / workersCount; // Height of the picture chunck for every worker

    // Function called when a job is finished
    var onWorkEnded = function (e) {
        // Data is retrieved using a memory clone operation
        var canvasData = e.data.result; 
        var index = e.data.index;

        // Copying back canvas data to canvas
        // If the first webworker  (index 0) returns data, apply it at pixel (0, 0) onwards
        // If the second webworker  (index 1) returns data, apply it at pixel (0, canvas.height/4) onwards, and so on
        tempContext.putImageData(canvasData, 0, blockSize * index);

        finished++;

        if (finished == workersCount) {
            var diff = new Date() - start;
            log.innerText = "Process done in " + diff + " ms";
        }
    };

    // Launching every worker
    for (var index = 0; index < workersCount; index++) {
        var worker = new Worker("pictureProcessor.js");
        worker.onmessage = onWorkEnded;

        // Getting the picture
        var canvasData = tempContext.getImageData(0, blockSize * index, canvas.width, blockSize);

        // Sending canvas data to the worker using a copy memory operation
        worker.postMessage({ data: canvasData, index: index, length: segmentLength });
    }
};

source.src = "mop.jpg";
```

使用这种技术，整个过程在我的计算机上只持续了 80 毫秒(从 150 毫秒开始),显然，使用了四个处理器:

![figure4](img/0adb6c3364ee78b621423de67e8b49fd.png)

在我的低端硬件上(基于双核系统)，处理时间下降到 500 毫秒(从 900 毫秒)。

最终代码可以在这里[下载，一个工作示例在](http://www.catuhe.com/msdn/pictureworkers.zip)这里[发布。作为比较，这里是没有 web workers](http://www.catuhe.com/msdn/workers/default.html) 的相同代码[。](http://www.catuhe.com/msdn/workers/defaultnoworker.html)

需要注意的重要一点是，在最新的计算机上，这种差异可能很小，甚至有利于没有工人的代码。内存复制的开销必须由工人使用的复杂代码来平衡。上述棕褐色调转换的例子在某些情况下可能不足以保证切换到 web workers。

然而，网络工作者在具有多个内核的低端硬件上将会非常有用。

## 移植到 Windows 8

最后，我无法抗拒移植我的 JavaScript 代码来创建 Windows 8 应用程序的乐趣。我花了大约 10 分钟创建了一个空白的 JavaScript 项目，并将 JavaScript 代码复制/粘贴到其中。你可以在这里抓取 Windows 应用代码[，感受 Windows 8 原生 JavaScript 代码的强大！](http://www.catuhe.com/msdn/Win8PictureWorkers.zip)

## 分享这篇文章