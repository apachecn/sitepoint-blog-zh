# 如何为您的移动网站创建二维码阅读器

> 原文：<https://www.sitepoint.com/create-qr-code-reader-mobile-website/>

条形码和二维码使我们的购物和搜索体验更加现代化。携带智能手机的客户现在可以在世界任何地方拿起一件产品，使用众多免费手机应用程序中的一个扫描其条形码或二维码，并找出其最低价格以及在哪里可以买到它。

![Screenshots of apps with barcode scanner buttons](img/44ddc5b18eb71063f4a1e3409bffee10.png "Phone apps that support barcode scanning")

沃尔玛(Walmart)和亚马逊(Amazon)等公司已经采用这种技术，利用他们的手机应用程序将顾客吸引到他们的在线和离线商店。联邦快递和 UPS 等其他公司允许客户使用手机应用程序扫描包裹上的代码，而不需要手动输入一长串字符。

如果您的移动网站的用户需要输入激活码之类的长代码，或者他们喜欢根据杂志或广告上印刷的型号在您的网站上查找特定产品，那么您也可以利用 QR 码来消除小键盘的挫折感，使他们不必反复检查错误。

## 使用您的手机网站扫描二维码

扫描二维码不需要原生手机 app。创建自己的二维码阅读器相当简单。你的网站在装有摄像头的智能手机上运行，并运行一点 JavaScript，也能达到同样的效果。

这里有一个二维码扫描仪的演示，它不仅可以在手机上使用，也可以在大多数现代设备上使用。你只需要一个摄像头和一个二维码来扫描。

如果你手边没有二维码，这里有一个显示圆周率的前八位数字。

![Test the QR Code Reader: QR code encoding the value of Pi](img/ce9988196bae55fe6ae7be4e40bdb13d.png "The digits of Pi encoded in a QR")

## 创建二维码阅读器

我们的 QR 码阅读器将需要一些 HTML 和 JavaScript，但最重要的是，一个 JavaScript 库能够解释 QR 码。

我们不打算自己构建，因为有一些很棒的库在为我们做这件事，所以我们不需要为我们当前的目的重新发明轮子。

让我们从创建一个`index.html`文件开始。

### 添加 HTML

我们将需要这个项目一些非常简单的 HTML。将以下内容添加到您的 body 标签中:

```
<div id="container">
    <h1>QR Code Scanner</h1>

    <a id="btn-scan-qr">
        <img src="https://uploads.sitepoint.com/wp-content/uploads/2017/07/1499401426qr_icon.svg">
    <a/>

    <canvas hidden="" id="qr-canvas"></canvas>

    <div id="qr-result" hidden="">
        <b>Data:</b> <span id="outputData"></span>
    </div>
</div>

<script src="./src/qrCodeScanner.js"></script> 
```

正如你所看到的，我们有一个带有标题的包装容器，QR 图标图像被包装在一个`a`标签、`canvas`和`div`中，我们将在其中显示扫描结果。

在容器`div`之外，我们包含了`qrCodeScanner.js`文件。我们稍后将创建它，但首先我们将改进我们的应用程序的外观。

### 添加样式

将样式表添加到我们的 HTML 的头部:

```
<link rel="stylesheet" href="src/styles.css" /> 
```

现在我们想在`src`文件夹中创建`style.css`文件。我们只是想要这个样本应用程序的一些基本风格。将以下内容添加到您的 css 文件中:

```
html {
  height: 100%;
}

body {
  font-family: sans-serif;
  padding: 0 10px;
  height: 100%;
  background: black;
  margin: 0;
}

h1 {
  color: white;
  margin: 0;
  padding: 15px;
}

#container {
  text-align: center;
  margin: 0;
}

#qr-canvas {
  margin: auto;
  width: calc(100% - 20px);
  max-width: 400px;
}

#btn-scan-qr {
  cursor: pointer;
}

#btn-scan-qr img {
  height: 10em;
  padding: 15px;
  margin: 15px;
  background: white;
}

#qr-result {
  font-size: 1.2em;
  margin: 20px auto;
  padding: 20px;
  max-width: 700px;
  background-color: white;
} 
```

一点也不花哨。我们会将所有内容居中，中间有一个大的 QR 按钮，下面是结果。我们使用黑色和白色，就像二维码。

### 包括相关的 JavaScript 库

阅读二维码的秘诀是数学，数学的替代品是开源库。为了读取二维码，我们将使用 ZXing 编写的基于 Java 的[图像处理库](https://github.com/zxing/zxing)的 [JavaScript 端口](https://github.com/LazarSoft/jsqrcode)。JavaScript 版本由耶戈·拉兹洛移植。

因为 JavaScript 库由 17 个文件组成，所以我们冒昧地将它们合并到一个文件中，将代码包装在一个匿名函数中以防止污染全局名称空间，并将文件放入 Google Closure 的 minifier 中以使文件更小。

### 对库的一些小改动

为了使库更具适应性，我们对库的输出函数做了一些小的改动，以区分成功响应和 T2 错误响应。

在 [qrcode.js](https://github.com/sitepoint-editors/jsqrcode/blob/master/src/qrcode.js) 中做了两个重要的更改，这两行是:

```
qrcode.result = "error decoding QR Code";
//...
qrcode.callback("Failed to load the image"); 
```

这些字符串已被替换为`Error`对象:

```
qrcode.result = Error("error decoding QR Code");
//...
qrcode.callback(Error("Failed to load the image")); 
```

现在，我可以在回调函数中检测是否发生了错误，只需检查回调有效负载是否是`Error`的实例。

这些变化可以在库的[这个分支](https://github.com/sitepoint-editors/jsqrcode/)中找到。

### 添加脚本标签

要在我们的 QR 码阅读器中使用这个库，我们首先需要使用一个正则脚本标签将它包含在我们的 HTML 中:

```
<script src="https://rawgit.com/sitepoint-editors/jsqrcode/master/src/qr_packed.js">  </script> 
```

### 把它当成一个应用程序

我们需要做的是告诉移动浏览器，我们不想在纵向模式下缩放这个网站。这可以通过在`head`元素中添加以下 meta 标签来实现:

```
<meta name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=0;"/> 
```

### 添加 JavScript

现在我们需要在`src`文件夹中创建`qrCodeReader.js`文件，它应该与我们的 HTML 文件在同一层。

让我们给新文件添加一些代码:

```
const qrcode = window.qrcode;

const video = document.createElement("video");
const canvasElement = document.getElementById("qr-canvas");
const canvas = canvasElement.getContext("2d");

const qrResult = document.getElementById("qr-result");
const outputData = document.getElementById("outputData");
const btnScanQR = document.getElementById("btn-scan-qr");

let scanning = false; 
```

在这段代码的顶部，我们从`window`中获取了`qrcode`对象，为了方便起见，将它赋给了一个常量。我们还创建了一个`video`元素，用于处理来自相机的图像。

然后我们得到`canvas`元素，并用它将`2d context`赋给一个常数。我们需要这个来绘制来自我们相机的图像。

然后，我们获取相关元素来显示结果并与应用程序交互，在底部，我们声明了`scanning`变量，以保持扫描仪的状态。

接下来，我们将为 QR 码阅读器设置回调。在文件底部添加以下内容:

```
qrcode.callback = (res) => {
  if (res) {
    outputData.innerText = res;
    scanning = false;

    video.srcObject.getTracks().forEach(track => {
      track.stop();
    });

    qrResult.hidden = false;
    btnScanQR.hidden = false;
    canvasElement.hidden = true;
  }
}; 
```

这里我们分配了`qrcode`对象的`callback`函数。当图书馆检测到二维码时，就会调用这个函数。它提供了包含扫描结果的`res`参数，所以我们把它赋给了`outputData`元素的`innerText`属性。

这里还有另外四件事。首先，我们将变量`scanning`设置为 false，因为我们已经解码了 QR 码，不想再扫描了。

然后，我们从`video`元素的`srcObjec`属性中获取流中的所有音轨，并逐个停止它们。这就是我们如何停止流式传输用户的相机。

紧接着，我们确保显示了`qrResult`元素和`btnScanQR`元素，这样用户就可以看到结果并触发另一次扫描。最后，我们隐藏了`canvasElement`，因为我们不再需要它了。

这就是我们处理扫描仪响应所需的全部内容。

现在，我们需要访问相机馈送，并设置一个循环来在我们的画布上绘制每一帧的图像。我们还需要另一个循环来每 x 毫秒扫描一次 QR 码。

扫描每一帧都是浪费资源，所以我们最好在一个单独的循环中处理，这样我们可以控制运行算法的频率。

我们将在`btnScanQR`元素的`onclick`处理程序中这样做:

```
btnScanQR.onclick = () =>
  navigator.mediaDevices
    .getUserMedia({ video: { facingMode: "environment" } })
    .then(function(stream) {
      scanning = true;
      qrResult.hidden = true;
      btnScanQR.hidden = true;
      canvasElement.hidden = false;
      video.setAttribute("playsinline", true); // required to tell iOS safari we don't want fullscreen
      video.srcObject = stream;
      video.play();
      tick();
      scan();
    });
}; 
```

好吧，让我们过一遍。我们从`mediaDevices`对象中调用 [getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) 函数，它是 [navigator](https://developer.mozilla.org/en-US/docs/Web/API/Navigator) 对象的一部分。这将使浏览器要求用户允许使用他们的相机。

`getUserMedia`函数将一个对象作为参数，我们将视频对象传递给它，并将`facingMode`设置为`"environment"`。如果用户使用移动设备，这将尝试获取背面的摄像头。它返回一个承诺，当解析后，提供一个流，我们可以将它分配给我们创建的`video`元素的`srcObject`。然后我们将`"playsinline"`属性设置为`true`，这将阻止 iOS safari 进入全屏。

在这一点上，我们可以`play()`视频，但当然，这还不够。我们需要绘制每一帧的流，因此我们调用`tick`函数来实现这个目的，然后调用`scan`函数来触发算法。

让我们定义一下`tick`函数:

```
function tick() {
  canvasElement.height = video.videoHeight;
  canvasElement.width = video.videoWidth;
  canvas.drawImage(video, 0, 0, canvasElement.width, canvasElement.height);

  scanning && requestAnimationFrame(tick);
} 
```

这是一个经典的逐帧循环。我们将`canvasElement`的高度和宽度设置为`video`的尺寸。然后我们将`video`绘制到`canvas`中，在底部我们使用`requestAnimationFrame`并传入`tick`函数，以便在浏览器绘制下一帧时再次调用。我们这样做是有条件的，因为变量`scanning`是`true`。

现在让我们定义扫描函数:

```
function scan() {
  try {
    qrcode.decode();
  } catch (e) {
    setTimeout(scan, 300);
  }
} 
```

这很简单。我们从`qrcode`库中运行`decode`函数，它将寻找一个 ID 为`"qr-canvas"`的`canvas`，并扫描其内容。如果我们找不到任何东西，我们定义的错误将被捕获，我们将调用一个`setTimeout`在 300 毫秒内扫描。您可以将它设置为其他值，看看会发生什么。你等待下一次扫描的时间越长，速度就越慢。你等待的越少，你对用户设备的要求就越多，所以要小心。试着寻找一个甜蜜点。

这就是我们所需要的！现在让我们试试这个应用程序。

### 查看运行中的二维码阅读器

这是 codesandbox 中的工作项目。点击二维码按钮，给相机显示一些二维码进行扫描。把它保持在原位一会儿，你就会得到你的结果。你会惊讶于它有多快多流畅。

### 结论

所以我们有它，你自己的移动网站的二维码阅读器。您还可以在任何平台上使用它，这使它变得超级动态，并为您的客户带来很多价值。

二维码已经存在很多年了，ZXing 写的图像处理代码第一次移植到 JavaScript 差不多是在 9 年前。它很好地经受了时间的考验，它仍然是网络上最快的选择之一——如果不是最快的话。它还是一个免费的开源软件，这让它变得更好。

我们希望你有乐趣想出一些惊人的东西！

### 可下载资产

*   [SVG 格式的 QR 图标](https://uploads.sitepoint.com/wp-content/uploads/2017/07/1499401426qr_icon.svg)
*   [QR JavaScript 库(缩小版)](https://github.com/sitepoint-editors/jsqrcode/raw/master/src/qr_packed.js)

### 外部链接

*   [W3 图像捕捉规范](https://www.w3.org/TR/html-media-capture/)
*   [用于图像捕捉的 CanIUse.com 支持矩阵](http://caniuse.com/#feat=html-media-capture)
*   [耶戈·拉兹洛的概念证明](http://www.webqr.com)
*   [Github 上 JavaScript 库的分支](https://github.com/sitepoint-editors/jsqrcode/)
*   [ZXing 的 Java 图像处理库](https://github.com/zxing/zxing)

## 分享这篇文章