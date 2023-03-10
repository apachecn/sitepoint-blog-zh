# 用科尔多瓦扫描二维码

> 原文：<https://www.sitepoint.com/scanning-qr-code-cordova/>

条形码是一种机器可读的图像，它封装了有关它所附着的对象的一些数据。快速响应码(QR 码)是一种流行的移动使用的条形码类型，因为与普通条形码相比，它具有更快的可读性和更大的存储容量。

在本教程中，我将创建一个 Cordova 应用程序，可以扫描二维码，并存储从解码二维码获得的数据。为了扫描条形码，我将使用 [BarcodeScanner](https://github.com/phonegap/phonegap-plugin-barcodescanner) Cordova 插件。

## 条形码概述

条形码是结构化的图像，可以是 1D 或 2D。1D 条形码由不同高度、宽度和颜色的平行线组成。2D 条形码由点、六边形和其他几何图形组成。

条形码最初是用称为条形码阅读器的特殊光学扫描仪扫描的。但现在我们可以使用智能手机和打印机等设备扫描条形码。

条形码有许多不同的类型，取决于尺寸、几何图形和它可以封装的数据类型。以下是一些最常见的条形码类型:

![Common Barcodes](img/6080b4d01ae2ba6b1a13431a274b0375.png)

### 二维码

QR 码是当今使用的最流行的条形码类型之一，能够编码多达 4296 个字符。如上图所示，二维码是 2D。

典型的二维码应用包括产品跟踪、物品识别、时间跟踪、文档管理、一般营销、URL 共享、包裹跟踪等等。

有几种类型的二维码，模型 1，微，IQR，SQRC 和 LogoQ。模型 1 是最常见的类型，也是我将在这个应用程序示例中使用的类型。

QR 码在网站上被广泛使用，它们通常代表网站的 URL。当有人想在他们的手机上阅读你的网页内容时，他们可以扫描二维码，然后在浏览器中打开网址，而不是手动键入一个长网址。

我们将在本教程中创建的应用程序可用于扫描和存储作为 URL 的二维码。它允许你使用一个`InAppBrowser`窗口打开应用程序中的 URL。

## 条形码扫描仪科尔多瓦插件

BarcodeScanner Cordova 插件允许您扫描和解码不同类型的条形码。该插件可以在 Android、iOS、Windows 和黑莓平台上运行。

以下是显示如何启动相机应用程序并使用 BarcodeScanner Cordova 插件扫描条形码的基本代码:

```
cordova.plugins.barcodeScanner.scan(
  function (result) {
    if(!result.cancelled)
    {
      alert("Barcode type is: " + result.format);
      alert("Decoded text is: " + result.text);
    }
    else
    {
      alert("You have cancelled scan");
    }
  },
  function (error) {
      alert("Scanning failed: " + error);
  }
);
```

## 创建二维码扫描仪应用程序

让我们开始构建一个二维码扫描仪应用程序。这个应用程序可以让你扫描和存储二维码，这是网址。它还允许你使用应用浏览器窗口打开应用内部的 URL。

你可以在 [gitHub](https://github.com/sitepoint-editors/Cordova-QR-Code-Reader) 上找到这个项目的最终代码。

### 开始

我不会介绍安装和创建 Cordova 应用程序，如果你以前没有这样做过，[请阅读入门指南](http://cordova.apache.org/docs/en/4.0.0//guide_cli_index.md.html#The%20Command-Line%20Interface)。运行和构建应用程序的说明位于同一页面上。

给应用程序一个合适的名称，并添加您想要支持的平台。

因为我将使用 BarcodeScanner、Dialog 和 InAppBrowser 插件，所以通过运行 Cordova 项目目录中的以下命令来安装它们:

```
cordova plugin add phonegap-plugin-barcodescanner
cordova plugin add org.apache.cordova.dialogs
cordova plugin add org.apache.cordova.inappbrowser
```

在 *www/index.html* 文件中，在结束`body`标签之前添加以下 JavaScript 和 CSS 文件:

```
<script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>
<script src="https://code.jquery.com/mobile/1.4.5/jquery.mobile-1.4.5.min.js"></script>
<script type="text/javascript" src="cordova.js"></script>
```

移除电流:

```
<script type="text/javascript" src="js/index.js"></script>
```

这里我添加了来自 CDNs 的 jQuery 和 jQuery Mobile。您可以在本地嵌入这些文件，这样应用程序就可以在没有互联网连接的情况下工作。

根据您正在构建的平台，您可能需要更改以下代码行:

```
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *">
```

请参阅 Cordova 自动生成的该行上面的注释以获取指导。

**注意**:从这里开始的所有 JavaScript 代码都应该放在结束的`body`标签之前的`script`标签中。

### 初始化

我们将使用 HTML5 本地存储来存储扫描的数据。将此代码放在一个`script`标签中，以初始化本地存储(如果还没有初始化的话):

```
if(localStorage.getItem("LocalData") == null)
{
    var data = [];
    data = JSON.stringify(data);
    localStorage.setItem("LocalData", data);
}
```

这里我存储了一个空数组。每个数组元素都是另一个数组，包含被扫描的数据名称和值。

### 创建主屏幕

让我们创建一个主屏幕，它将在应用程序加载时显示。

主屏幕上有两个按钮。这些是用来扫描和显示数据的。

将这段代码放在*index.html*页面的`body`标签中(替换那里的 div):

```
<div data-role="page" id="home">
    <div data-role="header">
        <h1>Home</h1>
    </div>

    <div data-role="main" class="ui-content">
        <p>
            <a target="_blank" href="javascript:scan();" style="text-decoration: none"><button>Scan</button></a>
            <a target="_blank" href="#display" style="text-decoration: none"><button>Display Scanned Data</button></a>
        </p>
    </div>
</div>
```

以下是该页面的外观:

![QR Scanner](img/5cc8d88fbd53a1828ab9d0320185de27.png)

### 扫描

当用户点击*扫描*按钮时，名为`scan()`的功能被调用。这里是`scan`函数。将它放在一个`script`标签中:

```
function scan()
{
    cordova.plugins.barcodeScanner.scan(
        function (result) {
            if(!result.cancelled)
            {
                if(result.format == "QR_CODE")
                {
                    navigator.notification.prompt("Please enter name of data",  function(input){
                        var name = input.input1;
                        var value = result.text;

                        var data = localStorage.getItem("LocalData");
                        console.log(data);
                        data = JSON.parse(data);
                        data[data.length] = [name, value];

                        localStorage.setItem("LocalData", JSON.stringify(data));

                        alert("Done");
                    });
                }
            }
        },
        function (error) {
            alert("Scanning failed: " + error);
        }
   );
}
```

在这里，我检查用户扫描后的条形码类型。如果条形码是二维码，那么我们进一步前进，否则我们什么也不做。

如果条形码类型是 QR 码，那么我们要求用户输入一个名称来识别扫描的文本，并将其存储在本地存储中。最后，我们显示一个警告框，文本为 <q>Done</q> 。

以下是第一次点击*扫描*按钮时屏幕的样子:

![App Screen after button click](img/6a2a066dec66ff6ba14760b13bdb1a05.png)

### 显示

当用户点击*显示数据*按钮时，就会切换到 id 为`display`的页面。

让我们创建这个页面。将这段代码放在`body`标签中:

```
<div data-role="page" id="display">
    <div data-role="header">
        <a target="_blank" href="#home" class="ui-btn ui-icon-home ui-btn-icon-left">Home</a>
        <h1>Display</h1>
    </div>

    <div data-role="main" class="ui-content">
        <table data-role="table" data-mode="column" id="allTable" class="ui-responsive table-stroke">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Value</th>
                </tr>
            </thead>
            <tbody>
            </tbody>
        </table>
    </div>
</div>
```

这里我们显示一个空表。要用本地存储数据填充该表，请将此代码放在`script`标记中:

```
$(document).on("pagebeforeshow", "#display", function() {
    $("table#allTable tbody").empty();

    var data = localStorage.getItem("LocalData");
    console.log(data);
    data = JSON.parse(data);

    var html = "";

    for(var count = 0; count < data.length; count++)
    {
        html = html + "<tr><td>" + data[count][0] + "</td><td><a href='javascript:openURL(\"" + data[count][1] + "\")'>" + data[count][1] + "</a></td></tr>";
    }

    $("table#allTable tbody").append(html).closest("table#allTable").table("refresh").trigger("create");

});

function openURL(url)
{
    window.open(url, '_blank', 'location=yes');
}
```

在呈现页面之前，我们用本地数据填充表格。当点击一个 URL 时，我们调用`openURL`函数，该函数在一个`InAppBrowser`窗口中打开该 URL。

以下是该页面现在的外观:

![Local Data rendered in App](img/e2640f741345fedf9304f3a6c7577580.png)

这是`InAppBrowser`窗口的外观:

![InAppBrowser Window](img/567f85babc0798738b1a9d9bfeb19770.png)

## 结论

本教程是用 Cordova 应用程序读取条形码的基本介绍。我们学习了如何区分不同的条形码类型，可以帮助我们的插件，以及如何扫描和存储条形码。

让我知道你尝试本教程的经历和扩展它的想法。

## 分享这篇文章