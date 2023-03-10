# 使用 PhoneGap Wakanda Studio 扩展缩短移动应用程序开发时间

> 原文：<https://www.sitepoint.com/cut-mobile-app-dev-time-with-phonegap-wakanda-studio-extension/>

在我的[上一篇文章](https://www.sitepoint.com/build-contacts-app-with-html5-css-javascript-wakanda-studio/)中，我介绍了使用 Wakanda 和 PhoneGap 构建一个管理联系人列表的混合移动应用程序，在上传到 PhoneGap build 之前，我手工打包了应用程序的客户端部分。

这个打包过程太繁琐了，大约需要 10 个步骤。为了解决这个问题，我们开发了一个新的 Wakanda Studio 扩展，它执行客户端应用程序的所有打包，并提供一个. zip 文件作为输出。

现在，我将向您展示如何使用该扩展，这样您也可以减少开发时间。

## 背景

 **在开始本教程之前，您应该对 HTML5、JavaScript、移动开发世界和安装 Wakanda 有一些基础知识。

 **在本文中，我将使用 [Wakanda DataStore](http://doc.wakanda.org/Datastore/Datastore.100-588923.en.html) 作为 NoSQL 数据库，我们的本地应用程序将远程访问该数据库，以使用 REST/HTTP 和 JSON 格式获取数据，因此掌握一些使用 Wakanda 的基础知识会非常有帮助。****

## 扩展的作用

使用 Wakanda Studio 创建您的项目后，单击 Studio 顶部的扩展按钮，输入您的设置，然后单击 Package 按钮。

该扩展将:

*   创建将包含该包的新文件夹
*   通过添加 PhoneGap 库来修改 HTML 页面:
    `<script type="text/javascript" src="phonegap.js"></script>`
*   将这两行添加到 index-smartphone.js 中，将 http 请求重定向到给定的 IP 地址:
    `WAF.core.restConnect.defaultService = "cors";`
    `WAF.core.restConnect.baseURL = http://wakanda server_url:application_port`
*   将其他行添加到同一个文件中，以从本地客户端文件夹中获取 WAF API
*   使用给定的设置将 config.xml 文件添加到包中(更多细节请参见:[https://build.phonegap.com/docs/config-xml](https://build.phonegap.com/docs/config-xml))
*   向服务器端代码添加 CORS 支持:跨源资源共享，以允许来自移动设备的 REST/HTTP 调用

下图给出了该过程的概述:

1.  创建一个 Wakanda 项目，这是一个简单的 HTML5，CSS3 和 JavaScript 移动网站。
2.  使用 PhoneGap 扩展来打包项目。
3.  上传。zip 文件到 PhoneGapBuild。
4.  生成本机应用程序(。apk，。app，…等。)
5.  在你的手机上安装测试程序。

**![process](img/54af1b2c2af2c15f6224aa82dcd544ba.png)**

## 使用分机

在您开始使用 PhoneGap Wakanda Studio 扩展之前，您应该下载 Wakanda 版本 4 生产发行版。下载瓦坎达后，你会有两个文件夹:第一个是瓦坎达服务器，第二个是瓦坎达工作室。将该扩展的 PhoneGap 文件夹复制到 Wakanda StudioExtensions 中。

### 1)创建一个项目

 **打开瓦坎达工作室，创建一个新的解决方案。

![create](img/ad2fa7314be069ce7982c2806d41c87a.png)

为您的解决方案命名。

![name](img/75b64e7ece832b95cff0d1a5918cbb1b.png)

转到 WebFolder，双击索引页。

![index page](img/5a4f4424989428679acda18c56d2cbb4.png)

点击工作室右上角的，进入智能手机页面。

![smartphone](img/0acd89ad2a399dd93c875efc336ce332.png)

现在，通过从右侧面板添加小部件以及使用左侧的属性面板来设计您的界面。

在我的 PoC 中，我将在页面中添加一个按钮，这样当单击按钮时，设备的位置将显示在警报中。

![add a button](img/0ae9bd1e298c2a75ea133df19790b50a.png)

将按钮重命名为“获取地理位置参数”。

现在，单击按钮，并在右边的属性面板中选择“单击”事件。

![on click](img/afc38d2173b79dd757a5796820f420a5.png)

编写以下代码来获取设备位置，并将其显示在文本字段小部件上:

```
button1.click = <b>function</b> button1_click (event)

    {

        navigator.geolocation.getCurrentPosition(onSuccess, onError);

    function onSuccess(position) {

        var coord = 'Latitude: '  + position.coords.latitude  + '<br />' + 'Longitude: ' + position.coords.longitude + '<br />' + 'Altitude: ' + position.coords.altitude + '<br />';

        alert(coord);

    }

  // onError Callback receives a PositionError object

  //

    function onError(error) {

        alert('code: '    + error.code    + 'n' + 'message: ' + error.message + 'n');

    }

};

    };
```

现在，应用程序使用 PhoneGap API 来获取移动位置。

应用程序与服务器没有任何交互，所以我们对客户端代码比对服务器端更感兴趣。

### 2)打包项目使用**扩展名**

 **现在，点击工作室中的 PhoneGap 扩展并设置设置。

**注意:** 你要设置好所有细节才能启用套餐按钮。

![settings for packaging](img/f3f0154751b7a771942c51cdfdebc09a.png)

不要忘记选中设置首选项复选框，以允许应用程序使用 PhoneGap 的地理位置 API。

![package button](img/55cbd2bd251f522333627fb4c7500c4c.png)

现在，点击设置底部的包按钮。

### 3)上传 Zip 文件到 PhoneGap 建立

 **作为扩展的输出，您将获得一个压缩文件，该文件应该上传到 PhoneGap build 中，以获得您应该在手机中安装和测试的原生移动应用程序。

![upload zip](img/8e8f322e57803d0903a19773304446f1.png)

为每个设备生成本机应用程序后，我们下载。ipa 在模拟器或真正的智能手机上测试它。

**注意:** 你应该有一个 app store 预置密钥来构建一个 IOS 应用并获取。应用程序来测试它在您的手机或模拟器。

### 4)安装并测试本地应用程序

 ****![testapi](img/4927bd32f16a45128381f67fa58b7c80.png)**

在 iPhone 模拟器上安装应用程序后，我们可以通过点击菜单中的图标来运行它。

![run on iphone](img/cecf36719e0e9418fac62686e090d0f0.png)

单击应用程序图标后，我们将看到使用 Wakanda Studio 创建的相同页面。

![created](img/d816db3046f498f2eba722b43823c391.png)

单击“get geolocation parameters ”(获取地理位置参数)后，会出现警报，并显示地理位置参数，如下图所示。

![alert](img/8d4729d089228c7f6b930cc5bbed2fca.png)

## 结论

使用 PhoneGap Wakanda Studio 扩展将帮助 web 开发人员本地化，而无需手动打包他们的应用程序。我在 10 分钟内创建了这个演示混合应用程序，在使用 PhoneGap build 后，我有了六个原生应用程序:Android、IOS、windows phone…等等。

该扩展仍处于开发阶段，可能会有一些错误和限制，所以我需要更多的反馈来增强和帮助 web 开发人员将他们的 web 应用程序迁移到手机上。

 **让我知道你的评论。

## [**下载申请. zip**](http://www.codeproject.com/KB/Articles/542819/Working/Download_the_application.zip)

github:**[https://github.com/saadmos/PhoneGapWakandaStudioExtension](https://github.com/saadmos/PhoneGapWakandaStudioExtension)**** 

## **分享这篇文章**************