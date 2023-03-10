# 利用 XDK 编写您的首个跨平台应用

> 原文：<https://www.sitepoint.com/coding-first-cross-platform-app-intel-xdk/>

[在本系列的第一部分](https://www.sitepoint.com/introduction-intel-xdk/ "A New Cross Platform Option, Introducing Intel XDK"),我们了解了英特尔 XDK 的概况。在第二部分中，我们将看看可以帮助我们构建应用程序的框架，并开始使用应用程序框架构建一个简单的照片共享应用程序。

我们将要开发的应用程序的最终代码可以在 GitHub 上找到[。](https://github.com/sitepoint-examples/Intel-XDK-introduction-p2)

## 比较框架

XDK 支持几种不同的 HTML5 框架:App 框架、Bootstrap、TopCoat 和 JQuery Mobile。应用程序设计器也支持所有这些框架。App Starter 仅支持 App 框架。App Designer 和 App Starter 都是 GUI 构建工具。

[App Framework](http://app-framework-software.intel.com/) 是一个简单的字体端框架，用于构建混合移动应用。它的 UI 组件仅用于构建这类应用程序，并且适用于每个移动操作系统。在 iOS 设备上，它为应用程序提供了原生的 iOS 外观和感觉，而在 Android 上，它看起来像是原生的 Android 应用程序，等等，适用于其他平台。App Framework 是轻量级的，旨在尽可能快地执行混合应用程序。

[JQuery mobile](http://jquerymobile.com/) 是一个老派的混合移动 app 框架。它已经存在了很多年，是最古老的框架之一。您可以在 JQuery Mobile 中找到许多现有的组件以及一个庞大的插件库。这种框架的主要问题是性能，它在低端设备上运行缓慢，并且没有提供本机外观。

[面漆](http://topcoat.io/)是一种鲜为人知的框架。它不是一个完整的框架，而是一个 UI 组件的集合，你可以直接在你的应用中使用。

Bootstrap 是所有可用框架中最著名的。它是一个构建网站和移动响应网站的框架。当我第一次在列表中看到 bootstrap 时，我有点惊讶，因为它在混合移动应用程序开发中并不常用。

## 最后，让我们编码！

让我们从创建一个新项目开始。你可以通过点击应用程序左上方的“开始新项目”按钮来完成。然后选择“App Starter”方法，因为这是从一个现成的应用框架项目开始的。

这是我们正在创造的原型:

![A prototype of the app ](img/2aa242c9dc899c40535ffa555d80b797.png)

在`css`文件夹中创建一个`style.css`，在`js`文件夹中创建一个`app.js`。创建另一个名为`images`的文件夹，我们稍后会放一些图片。由于我们不制作或使用任何 REST APIs，我们将通过在本地工作来伪装它。通过一些小的改动，您可以编辑这个项目来使用任何外部 REST APIs。

我通常首先做的是清理英特尔为您生成的不必要的代码。在清除了不必要的代码之后，我们会得到类似这样的结果:

```
<!DOCTYPE html>
    <html>

        <head>

            <!-- Meta data -->
            <meta http-equiv="Content-type" content="text/html; charset=utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=0">
            <meta name="apple-mobile-web-app-capable" content="yes">
            <meta http-equiv="Pragma" content="no-cache">

            <script type="text/javascript" charset="utf-8" src="intelxdk.js"></script>
            <script type="text/javascript" language="javascript"> var isIntel=window.intel&amp;&amp;window.intel.xdk

                // This event handler is fired once the intel libraries are ready
                function onDeviceReady() {
                    //hide splash screen now that our app is ready to run
                    intel.xdk.device.hideSplashScreen();
                    setTimeout(function () {
                        $.ui.launch();
                    }, 50);
                }

                //initial event handler to detect when intel is ready to roll
                document.addEventListener("intel.xdk.device.ready", onDeviceReady, false); </script>

            <script src="js/appframework.ui.min.js"></script>

            <script> if(isIntel)
                    $.ui.autoLaunch = false;
                    $.ui.useOSThemes = true; //Change this to false to force a device theme
                    $.ui.blockPageScroll();
                    $(document).ready(function () {
                        if ($.ui.useOSThemes &amp;&amp; (!$.os.ios||$.os.ios7))
                            $("#afui").removeClass("ios");
                    }); </script>

            <!-- Import the css files -->
            <link href="css/icons.css" rel="stylesheet" type="text/css">
            <link href="css/af.ui.css" rel="stylesheet" type="text/css">
            <link href="css/style.css" rel="stylesheet" type="text/css">

        </head>

        <body>

            <div id="afui">

            </div>

            <script src="js/data.js"></script>
            <script src="js/app.js"></script>
        </body>

    </html>
```

在这个例子中，我还添加了`app.js`和`style.css`。让我们快速浏览并分析一下代码。在标题中有一些你不必编辑或更改的元数据。

当设备准备就绪时，第一个 Javascript 块隐藏闪屏(如果您在 build 部分指定了闪屏)。

在第二个 Javascript 块中，您可以配置是希望使用该设备正在运行的本机平台外观，还是强制应用程序在其运行的每个设备上使用 iOS7。

首先要知道的是带`id="afui"`的 div。您必须指定具有此 id 的元素。这就像是我们应用程序的根。一切都将从这个元素开始。在该元素中，我们指定了应用程序的页眉、页脚和所有页面。

```
<div id="afui">

    <div id="header" class="header"></div>

</div>
```

带有`id="header"`的 div 是与标题一起工作的元素。应用程序框架将在 div 内部执行大量自动生成，但是如果您愿意，您可以添加其他内容。例如，您可以手动添加后退按钮并指定标题。我的建议是尽可能多地让自动生成来完成，当框架自己完成时，不要手动完成。您需要维护的代码更少，也更整洁。

应用程序的所有页面都在`id="content"`元素中。您可以在其中为想要显示的每个页面添加面板，例如:

```
<div id="content">

    <div class="panel" title="Photos" id="main_pg" selected="selected">
        <!-- All the content goes here -->
    </div>

    <!-- You can add other panels here -->
</div>
```

创建面板时，你应该添加一些属性。例如，`title`属性显示在页面标题中，而`id`属性用于在面板之间导航。`selected`属性设置这是否是要显示的主选项卡，只有一个选项卡可以有此属性。

最后要做的是添加页脚。

```
<div id="afui">

    <div id="header" class="header"></div>

    <div id="content">
        .....
    </div>

    <!-- the footer -->
    <div id="navbar" class="footer">
        <a href="#main_pg" class="icon home">Home</a>
        <a href="#myphotos_pg" class="icon picture">My Photos</a>
    </div>

</div>
```

在页脚中，你可以添加带有文本和图标的标签。通常这些是标签，并且`href`属性指定了当点击这个标签时你想要导航到的面板的 id。在这种情况下，它是`#main_pg`。在这个类中，你可以指定这个标签的图标。在本例中，第一个选项卡有一个主页图标，第二个选项卡有一个照片图标。不幸的是，我找不到任何关于图标完整列表的文档。如果你想知道你能使用什么，打开`css`文件夹中的`icons.css`。

我们在页脚添加了第二个链接，但是该链接没有面板。在内容部分添加另一个面板。

```
<div id="content">

    <div class="panel" title="Photos" id="main_pg" selected="selected">
        <!-- All the content goes here -->
    </div>

    <div class="panel" title="My Photos" id="myphotos_pg">

        <span>My Photos</span>

    </div>

</div>
```

在模拟器中测试它。

![Screenshot of the app](img/4ebd93207771504bd40890b74f220b8b.png)

除了后退按钮之外，一切正常。这两个标签都是可见的，所以后退按钮不应该在那里，因为它与本机应用程序的用户体验不一致。

```
<div id="afui">

    <div id="header" class="header"></div>

    <header id="myphotos_header">
        <h1 id="pageTitle">My Photos</h1>
    </header>

    ......
</div>
```

让我们添加一个没有后退按钮的新标题。我们将只对`myphotos_pg`面板使用这个标题。

使用`data-header="myphotos_header"`添加对面板中标题的引用。`data-header`属性的值是所使用的头的 id。因为标题是从该标题中使用的，所以在该面板上拥有标题属性是没有意义的。`myphotos_pg`面板应该是这样的:

```
<div class="panel" data-header="myphotos_header" id="myphotos_pg">

    <span>My Photos</span>

</div>
```

在模拟器中测试它，你会看到现在一切正常。从主页标签切换到照片标签，你会看到没有返回按钮显示。

![Two tabs running in our app](img/d8e8865cf8266c841e2c514c9fd7d19e.png)

## 结论

在第二部分中，我们创建了一个新项目，研究了 XDK 支持哪些框架并使用了 App 框架。需要明确的是，当我说*“XDK 支持这个框架”*的时候，我指的是 App 启动者/设计者支持这个框架。你可以选择 Ionic，例如，在我的一个项目中，我使用了 Ionic 框架，一切都一样，但是我不能使用任何 GUI 工具。我强烈建议从头开始编码，因为使用 HTML GUI 构建工具时，代码不容易维护。

请继续关注第三部分。

## 分享这篇文章