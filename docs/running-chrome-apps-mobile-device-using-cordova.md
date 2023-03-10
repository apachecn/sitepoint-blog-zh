# 使用 Cordova 在移动设备上运行 Chrome 应用程序

> 原文：<https://www.sitepoint.com/running-chrome-apps-mobile-device-using-cordova/>

Chrome 应用程序在 Chrome 用户中很受欢迎。为什么不呢，它们提供了一种在 Chrome 浏览器的熟悉环境中创建可移植的“应用程序”的方法。

想象一下智能手机上的 Chrome 应用程序的威力。我们现在可以使用基于 Apache Cordova 的工具集在 Android 和 iOS 上运行我们最喜欢的 chrome 应用程序。

Cordova 是一个开源框架，使用 HTML、CSS 和 JavaScript 打包移动应用程序。Cordova 使用原生外壳包装 HTML、CSS 和 JavaScript web 应用，并允许在 [Google Play](https://play.google.com/store?hl=en) 、[苹果应用商店](https://play.google.com/store?hl=en)和其他商店上分发。

## 概观

在本教程中，我们将使用 HTML、CSS 和 JavaScript 创建自己的 Chrome 应用程序，并将其移植到 Android 模拟器。我们将创建的应用程序将是一个简单的应用程序，根据特定的 twitter 标签获取最新的推文。

## 创建 Chrome 应用程序

我们将从创建我们的 Chrome 应用程序开始。一旦我们创建了 Chrome 应用程序，并在 Chrome 浏览器上进行测试，我们将把它移植到 Android 上。

本教程的源代码可以在 GitHub 上找到。

创建一个名为`TwitterChromeApp`的项目文件夹。在项目文件夹中创建一个名为`manifest.json`的清单文件，这将是我们应用程序的配置文件。在`manifest.json`中，我们将定义 Chrome 应用程序所需的一些设置。Chrome 应用要求`manifest_version`是`2`。我们还将定义应用程序的名称、版本以及启动应用程序时要执行的后台脚本的路径。我们将从一个外部服务 url 获取 tweets，所以我们也将在这个文件中指定它。下面是`manifest.json`的外观:

```
{
    "manifest_version": 2,
    "name": "Tweet Chrome App",
    "version": "1.0",
    "app": {
        "background": {
            "scripts": ["scripts/main.js"]
        }
    },
    "permissions": [
        "http://twittersearchapi.herokuapp.com/search"
    ]
}
```

当 Chrome 应用程序启动时，我们将显示一个窗口，其中将显示来自 twitter 的推文。Chrome 应用程序有一个名为 [`onLaunched`](https://developer.chrome.com/apps/app_lifecycle#lifecycle) 的事件，我们用它在应用程序启动时为我们的应用程序创建窗口。

在项目文件夹`TwitterChromeApp`中，创建另一个名为`scripts`的文件夹，并在其中创建名为`main.js`的后台脚本。将以下代码添加到`main.js`:

```
chrome.app.runtime.onLaunched.addListener(function() {
  // creating window for app code will be here
});
```

在`onLaunched`事件中，我们将为 Chrome 应用程序创建一个窗口。将以下代码添加到`main.js`:

```
chrome.app.runtime.onLaunched.addListener(function() {

    var screenWidth = screen.availWidth;
    var screenHeight = screen.availHeight;
    var width = 500;
    var height = 300;

    chrome.app.window.create('index.html', {
        id: "tweetAppID",
        outerBounds: {
            width: width,
            height: height,
            left: Math.round((screenWidth - width) / 2),
            top: Math.round((screenHeight - height) / 2)
        }
    });
});
```

在上面的代码中，我们使用了`screen`对象来获取可用的屏幕宽度和高度。基于屏幕的实际宽度，我们设置了新 Chrome 窗口的外部边界以正确显示。

`chrome.app.window.create`使用文件`index.html`中的 html 创建一个新窗口。

在项目文件夹`TwitterChromeApp`中创建一个名为`index.html`的新文件，如下所示:

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Chrome Tweet App</title>
</head>

<body>
    <h1>Tweets !!</h1>

</body>

</html>
```

现在尝试在你的 Chrome 浏览器中安装 Chrome 应用。打开*工具- >扩展*(可能还有*更多工具*)。从右上角的复选框中启用开发者模式，然后点击*加载解压缩扩展*并选择项目文件夹。

通过*应用*或*扩展*窗口运行扩展，您应该会看到以下内容:

![Preview of Chrome App](img/6a4a3734ce02ab7d1ff6a3383fdc7c64.png)

接下来，我们将创建一个 ajax 调用，该调用在窗口启动时触发，从服务 url 检索 tweets。我们将使用 Heroku 上托管的服务。这项服务有一些限制。它只获取标签为 *perkytweets* 的 tweets，这对于我们的例子来说已经足够了。

我们将使用 [jQuery](http://jquery.com/download/) 进行 AJAX 调用，因此将其下载到`scripts`文件夹并包含在`index.html`中，例如:

```
<script type="text/javascript" src="scripts/jquery-1.11.1.min.js"></script>
```

现在在`scripts`文件夹中创建一个名为`script.js`的新文件，并创建 ajax 调用，如下所示:

```
$(function() {
    $.ajax({
        type: 'GET',
        url: 'http://twittersearchapi.herokuapp.com/search',
        success: function(response) {
            var resObj = JSON.parse(response);
            console.log(resObj);
        },
        error: function(error) {
            console.log(error);
        }
    });
});
```

将`script.js`包含在`index.html`中:

```
<script type="text/javascript" src="scripts/script.js"></script>
```

现在打开*工具- >扩展*(或*更多工具*，点击*重新加载*以反映更改。接下来点击应用程序重新启动它。如果您检查 Chrome 控制台(只是您在 Chrome 中访问的普通控制台)，它应该会显示来自服务 URL 的响应。

接下来，我们将在`index.html`中显示响应。我们将使用 [Bootstrap](http://getbootstrap.com/) 来设计页面。

在项目文件夹中创建一个`styles`文件夹，并将引导 css 文件下载到该文件夹中。在`index.html`中包含引导 CSS 文件。

```
<link href="styles/bootstrap.min.css" rel="stylesheet">
```

在`index.html`中包含一个`ul`元素来显示推文。以下是`index.html`现在的样子:

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Chrome Tweet App</title>
    <link href="styles/bootstrap.min.css" rel="stylesheet">
    <script type="text/javascript" src="scripts/jquery-1.11.1.min.js"></script> <!-- Check this matches your jQuery version and file name -->
    <script src="scripts/script.js"></script>

</head>

<body>
    <h1>Tweets !!</h1>
    <ul id="ulTweets" class="list-group">

    </ul>
</body>

</html>
```

在 AJAX success call back in `scripts.js`中包含下面的代码，将从服务调用中获取的项目追加到`index.html`中的`ul`中。

```
$.each(resObj, function(index, value) {
    $('#ulTweets').append('<li class="list-group-item"><span class="badge">' + resObj[index].retweet_count + '</span>' + resObj[index].text + '</li>');

});
```

重新加载并重新启动应用程序，你应该可以看到满屏的推文。

## 为 Android 开发 Chrome 应用

由于我们将在 Android 上运行我们的应用程序，请确保您的系统上安装了 [Java JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或更高版本、 [Android SDK 4.4.2](http://developer.android.com/sdk/index.html) 或更高版本以及 [Apache Ant](http://ant.apache.org/bindownload.cgi) 。

我们还需要`cca`命令行工具。您可以使用安装它

```
npm install -g cca
```

*在[官方文档](https://developer.chrome.com/apps/chrome_apps_on_mobile)中可以找到关于设置环境将 Chrome 应用移植到 Android 和 iOS 的详细信息。*

一旦环境设置完成，我们将从现有的`TwitterChromeApp`创建一个新项目，移植到 Android。

运行以下命令创建项目:

```
cca create TwitterAppForAndroid --copy-from=path/to/TwitterChromeApp/manifest.json
```

导航到`TwitterAppForAndroid`并运行以下命令，在 android 模拟器中运行项目:

```
cd TwitterAppForAndroid
cca emulate android
```

模拟器成功启动后，您应该会看到应用程序在模拟器中运行。

## 结论

在本教程中，我们看到了如何创建一个简单的 chrome 应用程序并将其移植到 Android 平台。关于使用 Apache Cordova 在移动设备上运行 Chrome 应用程序的更多信息可以在[官方文档](https://developer.chrome.com/apps/chrome_apps_on_mobile)中找到。

你如何看待这个用于创建移动应用的 Chrome 应用选项？与在 Cordova 中使用标准的 HTML、CSS 和 JavaScript web 应用程序相比，它有什么优势吗？

## 分享这篇文章