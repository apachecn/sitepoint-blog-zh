# 用 HTML5 和 Javascript: 6 构建一个基于位置的移动应用

> 原文：<https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-6/>

在本系列的前一篇文章[中，我描述了最后剩下的实用函数，以及那些设置我们的应用程序的函数。在本系列的最后一部分，您将看到如何在](https://www.sitepoint.com/build-a-location-based-mobile-app-with-html5-and-javascript-part-5/ "Build a Location-Based Mobile App With HTML5 and Javascript: Part 5") [Adobe PhoneGap Build](http://build.phonegap.com) 中创建用于定义项目属性的配置文件。我还将分享一些关于额外功能的想法，这些功能可以进一步改善“我把车停在哪里”，以及任何其他基于 Cordova (PhoneGap)框架的应用程序。

### 构建配置

开发的每个项目都有几个作者可以设置的元数据字段:版本、名称、作者、描述等等。如果您使用 Adobe PhoneGap Build 服务，您有两种可能来指定这些属性:使用服务 web 界面，或者使用名为`config.xml`的配置文件。在这一节中，我将向您概述我最喜欢的方法——配置文件。详细解释这种格式超出了本系列的范围，但是我会给你一个概述，这样你就可以理解文件的来源。如果你需要更多信息，请看一下[官方文档页面](https://build.phonegap.com/docs/config-xml "Using config.xml")。

`config.xml`遵循 [W3C 小部件规范](https://www.w3.org/TR/widgets/ "W3C widget specification")，并且必须位于应用程序的根目录下，与`index.html`文件处于同一层。因为我们在谈论 XML 文件，所以`config.xml`的第一行是 XML 声明。整个文档的根是一个`<widget>`标签，它有几个有趣的属性。最重要的是`id`(项目的唯一标识符)，和`version`(指定应用程序的版本)。在`<widget>`标签里面，你可以写很多元素，但是在“我把车停在哪里”的配置文件里，我只使用下面这些:

*   **名称**(必选):指定应用程序的名称。不一定要独一无二。
*   **描述**(必选):指定您的工作的描述。这将显示在应用程序的市场列表中。
*   **作者**(可选):应用的作者。它可以有两个可选属性:`href`，一个到开发者或公司主页的 URL，和`email`，开发者或公司的电子邮件地址。不幸的是，您只能指定一个作者，所以您不能在这里有多个作者的详细信息。
*   **图标**(可选):在将要安装你的应用的设备上显示的图标。如果不指定，将使用 Cordova 徽标。它唯一需要的属性是`src`,正如您可能猜到的，它是图像的路径。
*   **特性**(可选):指定您想要使用的特性。例如，Android 在安装任何应用程序之前，会向用户显示它需要的权限，如果用户同意，它就会继续运行。在我们的应用程序中，这是我们请求访问互联网连接和地理定位功能的地方。
*   **首选项**(可选):表示您想要应用的一组首选项。这是一个封闭的标签，你可以在文件中有零个或者更多的标签。它有两个属性，都是必需的:`name`和`value`。您可以定义许多偏好，但在我看来，最重要的是使用 Cordova 版本。

除了举例说明的元素，我还想解释最后一个标签:`<gap:splash>`。这个标签设置你的应用程序的闪屏——加载时显示的图像。这是一个封闭的标签，你可以有一个或多个这样的元素。虽然唯一需要的属性是`src`，图像的 URL，但是您也可以设置宽度(属性`width`和高度(属性`height`)。使用这个标签，你可以为每个平台和屏幕尺寸指定不同的图像，这样它就可以在不同的情况下完美地呈现你的闪屏。要了解更多关于这个和其他元素的信息，请参考[官方文档页面](https://build.phonegap.com/docs/config-xml "Using config.xml")。

现在你已经看到了格式的要点，你就可以理解“我把车停在哪里”的配置文件的来源了。

```
<?xml version="1.0" encoding="UTF-8"?>
<widget xmlns     = "https://www.w3.org/ns/widgets"
        xmlns:gap = "http://phonegap.com/ns/1.0"
        id        = "com.audero.free.utility.whereiparkedmycar"
        version   = "1.0.0">

   <name>Where I parked my car</name>

   <description>"Where I parked my car" is an application for mobile devices that lets you bookmark where you parked your car, and then find a route to return to it. This application is useful if you don't have a good memory or if you're in a foreign city. In addition, "Where I parked my car" will also save a log of your saved positions (up to 50), so that you can retrieve a location for future use. The list allows you to see the position on a map, and you can also to delete one or more old locations.</description>

   <author href="http://www.audero.it" email="aurelioderosa@gmail.com">Aurelio De Rosa</author>

   <icon src="icon.png" gap:role="default" />

   <feature name="http://api.phonegap.com/1.0/network"/>
   <feature name="http://api.phonegap.com/1.0/geolocation"/>

   <preference name="phonegap-version" value="2.0.0" />
   <preference name="target-device" value="universal" />

   <!-- Splash Screens -->
   <gap:splash src="splash.png" />
   <gap:splash src="images/splash-160x220.png" gap:platform="android" gap:density="ldpi" />
   <gap:splash src="images/splash-220x330.png" gap:platform="android" gap:density="mdpi" />
   <gap:splash src="images/splash-450x650.png" gap:platform="android" gap:density="hdpi" />
</widget>
```

### 可能的改进

如果你正在阅读这一节，你已经阅读了所有的功能和“我停车的地方”的代码。然而，像每个项目一样，总有改进的空间和新的特性。下面的一些提议现在就可以实现，而其他的则需要 Cordova 开发团队的关注，他们需要在你继续之前实现一些新的 API。请注意，一些第三方插件已经可以满足一些请求，但是它们中的许多只适用于少数移动平台——而不是所有受 Cordova 支持的平台。如果把它们放在 Cordova 框架的核心而不是外部插件中，那就太好了。说完这些，我们来看看这些特性。

您现在可以添加和使用的第一个特性是应用程序的国际化(i18n)。因为这个项目不是很复杂，所以应该很容易。为了实现这个目标，您可以使用[全球化 API](http://docs.phonegap.com/en/2.2.0/cordova_globalization_globalization.md.html#Globalization) ，一个从 2.2.0 版本开始添加到核心的 API，以及一些特定的 jQuery 库，如 [jquery-i18n-properties](http://code.google.com/p/jquery-i18n-properties/) 和 [jQuery-i18n](http://recursive-design.com/projects/jquery-i18n/) 。

第二个附加功能可能涉及根据用户的当前位置和他们的位置历史找到更近的停车场。您可以确定用户过去是否在附近停车，并建议他们去过去的停车点。您还可以让用户使用 [jQuery Mobile slider widget](http://jquerymobile.com/demos/1.2.0/docs/forms/slider/) 来搜索特定距离内最近的停车场。

另一个容易添加的功能是让用户在每个停车位上添加一个注释，甚至一个小时的费率。

我想建议的最后一个提议(可能是最酷的一个)是，如果用户将车停在时间敏感的收费停车场，可以在设备上自动添加警报。因此，多亏了警报，当停车时间用完时，用户会得到警告。不幸的是，目前还没有一个本地的 Cordova API 来做这件事，但是你可以使用 Android 和 iOS 上的一个名为 [LocalNotification](https://github.com/phonegap/phonegap-plugins/tree/master/Android/LocalNotification "LocalNotification plugin for Android") 的插件。

上面的建议只是一些潜在的应用程序的增强，但是，当然，使用你自己的技能和网络技术，你可以做得更多。可能性实际上是无限的。

### 结论

哇，这是一次长途旅行，但你终于完成了申请。正如您在本系列中所看到的，使用 web 技术和流行的框架，您可以构建有用且强大的应用程序。现在，作为一个如此耐心和敏锐的读者，这是你的奖励:到[应用程序库](https://github.com/AurelioDeRosa/Where-I-Parked-My-Car "Repository of Where I parked my car")的链接。你可以在我的仓库中下载完整的现成的包“T2，我停车的地方”。请注意，“我停车的地方”是在 CC BY 3.0(“知识共享署名 3.0”)许可下发布的。

## 分享这篇文章