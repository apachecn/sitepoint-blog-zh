# 使用 HTML5 和 Javascript: 1 构建基于位置的移动应用程序

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-1/>

你不需要单独的特定于平台的技术来开发强大、有用的应用。你会惊讶于用 HTML5 和流行的 JavaScript 库所能构建的东西。本文是我将纯粹通过 HTML5 和 JavaScript 框架开发移动应用程序的系列文章的第一篇。这款应用将被称为“我停车的地方”，我们将使用 jQuery Mobile 和 Cordova(也称为 [PhoneGap](https://www.sitepoint.com/whats-new-in-phonegap-2-2/ "What’s New in PhoneGap 2.2?") )来构建它。在第一篇文章中，我将解释这个应用程序的特性，以及你需要了解哪些语言和框架才能理解这个教程。为了创建这个应用程序，我还将使用新的 HTML5 API，特别是本地存储和地理位置(实际上不是 HTML5 规范的一部分)，以及 Google Maps API。

混合应用<q cite="https://www.sitepoint.com/native-hybrid-or-web-apps/">使用 web 技术构建，然后包装在特定于平台的外壳中，允许它像本地应用一样安装。如果你想了解本地、混合和网络应用之间的区别，我建议你去读读[本地、混合或网络应用？](https://www.sitepoint.com/native-hybrid-or-web-apps/ "Native, Hybrid or Web Apps?")。</q>

### 揭开特征

“我停车的地方”的目的是让你使用智能手机的 GPS 功能在地图上标记你停车的地方，然后——在你完成步行后——找到返回的路线。如果你没有好的记忆力或者你在一个外国城市，这种应用程序是有用的。此外，“我停车的地方”还将保存您保存位置的日志(最多 50 个)，以便您可以检索位置供将来使用。由于滚动 50 个项目的列表真的很烦人，该应用程序将有一个搜索过滤器，所以你可以轻松完成任务。该列表允许您查看地图上的位置，并且您还可以删除一个或多个旧位置。最后，我还将在应用程序中创建一个小的学分页面。

### 配料

为了充分理解将要开发的内容，您必须至少具备以下语言和框架的基础知识:

*   HTML :它将被用来创建页面的标记。在适当的地方，我将使用一些新的 HTML5 标签。
*   CSS :由于大部分 CSS 增强将由 jQuery Mobile 完成，所以并不需要太多。但是，我将使用一些规则来纠正或调整一些样式，稍后您将会看到。
*   JavaScript :业务逻辑完全是用 JavaScript 编写的，所以如果你不知道一些关键概念，比如什么是回调或闭包，你需要在继续之前学习一下。
*   **jQuery** :我用 jQuery 主要是选择元素和附加事件处理程序，这里没什么特别的。
*   jQuery Mobile :用于自动增强应用界面。我将使用页面加载小部件，职位日志的 listview，我将在页面的页眉和页脚中放置一些按钮来构建工具栏。
*   Cordova (PhoneGap) :如前所述，Cordova 将用于包装文件，这样你就可以编译它们，就像你构建了一个原生应用一样。当然，我将利用框架提供的一些 API，比如通知、地理定位和连接

使用的 jQuery Mobile 版本将会是 1.2.0，所以如果你需要复习这个版本中的新特性，你可以阅读[jQuery Mobile 1 . 2 . 0 中的新特性？](https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/ "What’s New in jQuery Mobile 1.2.0?")和[使用 jQuery Mobile](https://www.sitepoint.com/working-with-jquery-mobile-1-2-0/ "Build Lists and Popups in Minutes Using jQuery Mobile") 在几分钟内建立列表和弹出窗口。使用的科尔多瓦版本将是 2.0.0。我选择这个版本而不是新版本，因为在我的测试中，它与 jQuery Mobile 结合使用时性能更好。

除了上面的列表，我还将使用这些 API:

*   **地理定位 API** :以经纬度的形式提供设备的位置信息。由于许多操作系统已经支持它，Cordova 将使用本机接口，而不是自己的实现。然而，对于没有这个 API 的平台，Cordova 使用符合 W3C 规范的实现。如果你需要这个 API 的介绍，你可以阅读使用 HTML5 地理定位 API 的[。](https://www.sitepoint.com/using-the-html5-geolocation-api/ "Using the HTML5 Geolocation API")
*   谷歌地图应用编程接口:谷歌地图应用编程接口允许你将谷歌地图服务与你的网站整合。该服务是完全免费的，但是您应该订阅 API 控制台网站以获得您自己的 API 密钥，并在您向该服务发送请求时使用它。因为这个 API 在没有密钥的情况下仍然可以工作，所以我将在没有唯一的 API 密钥的情况下使用它。
*   **网络存储 API** :提供对设备存储选项的访问。如果你需要一个起点，你可以看看[的 Web 存储 API](https://www.sitepoint.com/an-overview-of-the-web-storage-api/ "An Overview of the Web Storage API") 概述。

请记住，Cordova 为每个操作系统使用不同的 JavaScript 文件，因此如果您想自己编译应用程序，您需要为每个平台使用特定的 IDE-SDK 包，如 Eclipse 和 Android SDK、Visual Studio 和 Windows Phone SDK 等。这可能是一个真正的痛苦，所以“我把车停在哪里”将在假设编译将使用 Adobe 云服务完成的情况下开发，称为 [Adobe PhoneGap Build](http://build.phonegap.com "Adobe PhoneGap Build") 。服务本身将在编译时为每个平台包含正确的 Cordova 库。因此，要遵循本教程，您可以使用简单的文本编辑器或您喜欢使用的 IDE，例如 NetBeans，而无需安装任何其他 SDK。我将在[我的 bitbucket 存储库](https://bitbucket.org/AurelioDeRosa "Aurelio De Rosa's repository")上发布最终代码，在那里我将移除由 [Adobe PhoneGap 构建文档](http://build.phonegap.com/docs/preparing-your-app)指定的库。

### 项目的结构

项目的结构将非常简单。事实上，我将创建 HTML 文件、 [Adobe PhoneGap 构建配置文件](https://build.phonegap.com/docs/config-xml "Adobe PhoneGap Build configuration file")、标准闪屏和项目根目录中的应用程序图标。然后，我将其他文件分成三类(实际上是文件夹):CSS、images 和 JS。为了开发“我停车的地方”，除了 jQuery、jQuery Mobile 和 Cordova 文件之外，我将创建以下文档:

*   index.html:这是入口点，将包含应用程序中使用的所有库文件。它还将有按钮来设置和获取当前位置，并查看位置的日志。
*   map.html:在这里你会看到地图，它会显示你的位置、你的车的位置以及到达它的路线。
*   **positions.html**:该页面将以列表形式显示持仓日志。
*   aurelio.html:这是演职员表的页面。这里没什么有趣的东西。
*   **style.css** :正如我之前所说的，我将编写一些偏离 jQuery Mobile 标准样式的样式更改。
*   jquery.mobile.config.js :它将包含 jQuery Mobile 的配置
*   **functions.js** :这个文件将包含初始化应用程序的函数和其他一些实用函数。
*   **maps.js** :这个文件将具有使用 Google Maps API 绘制地图和汽车路线的函数。
*   **positions.js** :该文件将使用本地存储 API 保存并加载之前的位置。
*   **config.xml** :这个 xml 文件将包含应用程序的元数据，Adobe cloud service 将使用它来存储应用程序版本号和包内容等设置。

在这一点上，你可能会说，既然我们正在构建一个小的应用程序，我可以使用一个包含所有页面的文件。你说的完全正确，但是我更喜欢把事情分开来组织。

### 设置项目

首先，创建项目文件夹，例如“where-i-parked-my-car”并创建上面提到的三个文件夹:CSS、images 和 JS。现在，您需要下载 jQuery、jQuery Mobile 和 Codova 文件。将这些框架的所有 JavaScript 文件放在“JS”文件夹内，将 jQuery Mobile 捆绑图片放在“images”文件夹内，最后将 jQuery Mobile CSS 文件放在“CSS”文件夹内。如果您做的一切都是正确的，那么项目应该具有以下结构:

```
---css
|   ---jquery.mobile-1.2.0.min.css
|   ---style.css
---images
|   ---ajax-loader.gif
|   ---icons-18-black.png
|   ---icons-18-white.png
|   ---icons-36-black.png
|   ---icons-36-white.png
---js
|   ---cordova-2.0.0.js
|   ---jquery-1.8.3.min.js
|   ---jquery.mobile-1.2.0.min.js
```

如前所述，需要参考 Google Maps API。包含您需要的文件非常简单，只需在 HTML 中添加下面一行。

```
<script src="http://maps.google.com/maps/api/js?sensor=true"></script>
```

请注意，这一行包含一个`sensor`参数。如果你使用的是传感器(比如 GPS ),它必须包含在 URL 中并设置为`true`,否则设置为`false`。由于我们使用 Maps API 来创建一个移动应用程序，我们将它设置为`true`。

### 结论

在这第一篇介绍性文章中，我展示了“我把车停在哪里”的特性，并概述了将用于实现它的技术。我已经将组成应用程序的文件组织得井井有条，这样我们就可以直接进行开发了。最后，我解释了如何设置项目。在下一篇文章中，我将深入研究代码，并向您展示如何创建 HTML 页面。

## 分享这篇文章