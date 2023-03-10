# Android Studio 的 9 个重要插件

> 原文：<https://www.sitepoint.com/9-more-essential-plugins-for-android-studio/>

去年我为 Android Studio 写了一篇关于顶级插件的文章，它很受欢迎！随着 Android Studio 2+的发布，我认为是时候编写一份新的插件列表来改进和增强 Android 的官方 IDE 了。

我将专注于插件，以帮助更快地开发应用程序，管理项目资源，并创建更好的代码。

## 安装插件

所有插件的安装过程都是一样的:

启动 Android Studio，打开*偏好设置*。

打开插件部分。

![Plugins](img/c4388d09a230acd94ed2b7ba73f12a46.png)

点击*浏览存储库*加载 IntelliJ IDEA 插件的完整列表。

![Browse repositories](img/fea81c370c6bc16aa7055139d8c42912.png)

搜索插件名称，点击*安装*。

![install](img/7a721e68d0525371e36eab7a595b5136.png)

安装完成后重启 Android Studio。

## 1.生成可打包的对象

[可打包的样板代码生成](https://github.com/mcharmas/android-parcelable-intellij-plugin)

在应用程序开发过程中的某个时刻，您将不得不在 java 中处理可打包的对象。可打包对象将数据发送到安装在设备上的应用程序、系统组件或其他应用程序的不同组件。当应用程序需要 Parcelable 时，开发人员需要手动构建它们，或者可以使用 **Parcelable 样板代码生成**工具来基于 Parcelable 类的字段生成实现。

![Parcelable Example](img/e170e8dc6048f83b6867a14510e4a2e0.png)

## 2.ADB 命令

[亚行理念](https://github.com/pbreault/adb-idea)

ADB Idea 旨在通过提供至关重要的终端命令来加速 Android 开发者的工作。Android Studio 2.0 发布后，加上[即时运行](http://tools.android.com/tech-docs/instant-run)，我发现这个工具很有帮助。虽然这是一个很好的特性，但有时快速构建并不能提取小的变化，你必须重新构建并重新安装项目。这使得快速访问 ADB 命令变得非常重要。

![ADB Idea](img/b6564a046d7a2cd119a9ae0ead742690.png)

## 3.无线 ADB

[ADBWIFI](https://plugins.jetbrains.com/plugin/7856?pr=idea)

这是一个简单的插件，可以让你通过 Wi-Fi 调试 Android 应用程序，如果你问我，我会觉得这很酷。

![ADB WIFI](img/8828a5a6b267c0d661470c7c7c8cd343.png)

## 4.Minimap(最小化)

[共瞥](https://plugins.jetbrains.com/plugin/7275?pr=)

如果您使用过 Sublime 这样的编辑器，CodeGlance 可能是您所需要的，它可以让您的 IDE 更有趣，更容易浏览大型类文件。

![CodeGlance](img/1b986148e8199b4d0bc11bb6c1db08e4.png)

## 5.将 SVG 转换为 Android 矢量

[SVG2VectorDrawable](https://plugins.jetbrains.com/plugin/8103)

材料设计的思维转变为应用程序图标、徽标和静态图像带来了新的方法。资产格式从传统图像格式(*)转变而来。巴新*、*。jpg* 等……)到[矢量绘图](http://developer.android.com/training/material/drawables.html)。SVG2VectorDrawable 插件让你将 SVG 图像转换成矢量绘图(即*)。xml* 文件),这样您就可以将它们用作布局绘图。

![SVG Converter](img/ff48dd2722ad03eadce5b97d9c90b796.png)

## 6.数据传输对象生成器

[DTO 发电机](https://plugins.jetbrains.com/plugin/7834?pr=idea)

数据传输对象(DTO)生成器根据给定的提要创建 Java 类。如果您正在使用返回 JSON 响应的 REST APIs，这个插件会很有帮助，这是大多数 web 服务的情况。DTO 将从提供的提要中生成一个' [POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object) '对象。这里有一个来自他们 Github 项目的例子，在这里你还可以找到[更多的](https://github.com/nvinayshetty/DTOnator):

![DTO example](img/128fa9a694319b03ed9eb34fb6704d8a.png)

## 7.材料设计图标生成器

[安卓材质设计图标生成器](https://plugins.jetbrains.com/plugin/7647?pr=)

为了开发基于材料设计模式的应用程序，你需要添加[材料图标](https://design.google.com/icons/)，但是我发现[材料图标指南](https://google.github.io/material-design-icons/)令人沮丧，需要做很多工作。这个插件帮助你在几个步骤中建立材料设计图标。

![Material Design Icon Generator](img/2ec443730f508e8502c1eef42f9d9126.png)

## 8.Codota 代码搜索

[codata IntelliJ 插件](http://www.codota.com/ide-plugin)

如果你没听说过 Codota，我强烈推荐。如果你的代码有问题，需要一个解决方案，或者有一个你无法修复的 bug，Codota 会尝试给你一个现实世界的解决方案。Codota 是一个代码搜索引擎，它寻找 GitHub、Google Code 和 StackOverflow 等平台上可用的开源代码片段。这使得 Codota 成为快速开发的完美工具。

**举个例子**。在 Codota 上搜索一些我知道在小型 Android 项目中没有广泛使用的东西，例如 [java.util.Observable](http://developer.android.com/reference/java/util/Observable.html) 类。

![Codota search](img/88337dc083cd8bf62197f4486b9666f3.png)

结果被评定为“代码等级 5”。现在尝试类似于 [java.util.ArrayList](http://developer.android.com/reference/java/util/ArrayList.html) 的东西，你会看到‘CodeRank 10’。

这就是 codata 的美妙之处，有了这个插件，你可以在 Android Studio 中拥有 codata 的力量。

## 9.吉姆代码镜像

[吉姆镜](https://www.jimumirror.com/)

在真实设备上调试应用程序可能会很慢，需要相当长的时间，尤其是当您使用 UI 元素并频繁进行调整时。吉姆镜像通过在设备上呈现实时更新的布局预览来帮助最大限度地减少开发时间，即使在进行布局更改时也是如此。吉姆镜是一个伟大的工具，预览 Android 布局，自定义视图和动画的飞行。它不是开源的，也不在 IntelliJ 插件库中，所以你必须从他们的网站下载。这个工具不是免费的，但我认为它值得节省时间。

## 插入

我希望我的建议能帮助你开发出优秀的应用，但这仅仅是个开始。如果你知道任何对你有用的插件，请在下面的评论中提出建议。

## 分享这篇文章