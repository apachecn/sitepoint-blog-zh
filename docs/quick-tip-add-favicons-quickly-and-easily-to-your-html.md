# 快速提示:快速方便地在 HTML 中添加图标

> 原文：<https://www.sitepoint.com/quick-tip-add-favicons-quickly-and-easily-to-your-html/>

网站图标给你的网站增加了额外的润色，帮助你的网站与众不同。随着桌面客户端、操作系统和移动设备让用户锁定有用的网站以便快速访问，这些图标变得越来越重要。获得正确的图标是很重要的，这样无论你的网站挂在哪里，你的用户都会得到最好看的图标。

图标通常不容易管理。由于移动和桌面操作系统和浏览器的碎片化，迎合每一个设备以确保使用最好的图标成为一个缓慢而乏味的过程。有时，您可能需要管理 30 多项资源来完成这项任务，这取决于您想要提供多少支持。

然而，谢天谢地，有真正的图标生成器网络服务。这个网站带你一步一步地快速轻松地生成你需要的所有图标和网络资源。

![Real Favicon Generator site](img/af64b30784a9349b9f28271aec63f297.png)

## 生成过程

真正的图标生成器使整个过程无痛。首先选择你的收藏夹图标，并上传到生成器。当页面加载时，您将看到不同设备目标的不同屏幕。

其中几个部分提供了类似的选项，例如能够提供一个专门的图片来定位设备，在图标周围添加边距并应用背景色。下面是你可以调整的主屏幕，分别是 iOS、Android 和其他(Windows / Safari)各一个。

![Favicon for iOS Screen](img/aa4c2201f948dd27ec846c4f81349d92.png)

![Favicon for Android Screen](img/ac5c19ac039392dd803dab3e18333329.png)

![Favicon for Other Screen](img/02eb3279c721e360516c64d521805eca.png)

可视化界面使得调整你的图标变得容易，当你把你的站点绑定到你的设备上时，你可以看到你的图标是怎样出现的。

除了实际的 favicons 本身，这个生成器还将处理移动 Chrome 的清单文件的创建，以及 Safari 中可锁定标签的其他设置。这些值最终在最终输出中被转换成`<meta>`标签。

## 使用您的收藏夹图标

当您准备好继续时，请按“生成”按钮。一旦页面加载，它会给你需要添加到您的网站的原始 HTML。下面是一个输出示例:

```
<link rel="apple-touch-icon" sizes="57x57" href="/apple-touch-icon-57x57.png">
<link rel="apple-touch-icon" sizes="60x60" href="/apple-touch-icon-60x60.png">
<link rel="apple-touch-icon" sizes="72x72" href="/apple-touch-icon-72x72.png">
<link rel="apple-touch-icon" sizes="76x76" href="/apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="114x114" href="/apple-touch-icon-114x114.png">
<link rel="apple-touch-icon" sizes="120x120" href="/apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="144x144" href="/apple-touch-icon-144x144.png">
<link rel="apple-touch-icon" sizes="152x152" href="/apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon-180x180.png">
<link rel="icon" type="image/png" href="/favicon-32x32.png" sizes="32x32">
<link rel="icon" type="image/png" href="/android-chrome-192x192.png" sizes="192x192">
<link rel="icon" type="image/png" href="/favicon-96x96.png" sizes="96x96">
<link rel="icon" type="image/png" href="/favicon-16x16.png" sizes="16x16">
<link rel="manifest" href="/manifest.json">
<link rel="mask-icon" href="/safari-pinned-tab.svg" color="#cc0033">
<meta name="apple-mobile-web-app-title" content="Web Bird Digital">
<meta name="application-name" content="Web Bird Digital">
<meta name="msapplication-TileColor" content="#cc0033">
<meta name="msapplication-TileImage" content="/mstile-144x144.png">
<meta name="theme-color" content="#cc0033"> 
```

生成器根据您之前提供的设置创建所有的`<link>`和`<meta>`标记元素。

现在你只需要按下*图标包*按钮来获取你的文件。一旦下载，只需将它们提取到某个地方，并将这些图标复制到您的站点。作为生成过程的一部分，如果您不打算将它们存储在站点的根位置*(例如将它们存储在类img/favicons` )* 的嵌套目录中)，您可以指定目录。

![Our Favicons Generated](img/48e648051eb6ec633cbdfb61e76db6b7.png)

一旦你复制了你的文件并将你的 HTML 添加到你的站点的`<head>`，你就可以开始了。当你把你的网站钉在你的设备上时，你的网站将为你的用户提供最好看的图标。

如果你最终发现这项服务很有用，并发现它节省了你的时间，你可能会考虑捐几美元给他们作为感谢(他们甚至接受比特币！).

## 额外的功能和选项

除了生成图标，真正的图标生成器服务让你检查你的网站是否提供了你需要的所有图标，给你一个你可能会错过的细分。

![Favicon Checker](img/9aa2e6c94d6a1a0567dcc29b3a8e284c.png)

![Favicon Error Results](img/2c9472ce373116c9c84a969841dc787e.png)

Favicon 支持是不断发展的，所以这是一个很好的工具，可以确保你提供最好的 favicon 和资源。

## 分享这篇文章