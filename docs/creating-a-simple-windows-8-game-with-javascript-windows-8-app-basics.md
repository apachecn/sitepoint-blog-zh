# 用 JavaScript 创建一个简单的 Windows 8 游戏:Windows 8 应用基础

> 原文：<https://www.sitepoint.com/creating-a-simple-windows-8-game-with-javascript-windows-8-app-basics/>

## “我们做个游戏好吗？”

这是为期四周的四篇系列文章中的第一篇，将向您展示如何创建一个(非常)简单的 Windows 8 游戏。在这个过程中，我们将使用 HTML5、JavaScript、WinJS 和来自 [CreateJS](http://createjs.com/) 的一些有用的库。

游戏基于 XNA 样本游戏[《弹射器战争实验室》](http://msdn.microsoft.com/en-us/windowsphonetrainingcourse_2dgamedevelopmentwithxnalab.aspx)。在开发基于网络技术的新版 Windows 8 时，我们将重复使用该游戏中的资产。

![Catapult Wars](img/b4ec3c0b32dc7c81e1f7315ddda0bfa8.png "Blank App template")

这个游戏关注的是*简单性*，所以有很多中间技术在这个例子中没有用到。更多综合样本，见本帖末尾的**【进一步阅读】**。

让我们开始吧。在本帖中，我们将关注 Windows 8 项目的基础。

## 安装

要开发这个游戏(或任何 Windows 8 Metro 风格的应用程序)，你需要同时拥有 [Windows 8 和 Visual Studio 2012](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200115728) 。在继续之前，请下载并安装这些软件。我们不会在这里涉及，因为它在网上解释得很好。

## 创建项目

启动 Visual Studio 2012，选择文件->新建项目(或 CTRL + SHIFT + N)。我们将使用 JavaScript，所以选择 Templates 下的那个节点。我们将依赖于 HTML5 画布的用户界面，所以我们只需要“空白应用程序”模板(其他模板有共同的地铁风格布局和控件内置):

![screen shot of Blank App template](img/96b7301ecaf1a6ea9878c2e2f9bf31a3.png "Blank App template")

将项目命名为“CatapultGame ”,然后单击“确定”创建项目。有一些关于空白应用程序模板的[指南，但我现在会给你一个快速浏览。](http://msdn.microsoft.com/en-us/library/windows/apps/hh758331.aspx#blank_project_template_for_metro_style_apps)

## 了解项目

那么，我们得到了什么？在解决方案资源管理器中打开一些文件并查看一下。如果您展开所有文件夹，您会看到如下内容:

![screen shot of Solution Explorer](img/b1bbac0a2546064edd51c3fb1bbc0ae8.png "Solution Explorer")

以下是需要重点关注的主要部分:

*   **/References/Windows Library for JavaScript**——这是“WinJS”，JavaScript 和 CSS 的层，帮助我们为 Windows 8 开发 JavaScript Metro 风格的应用。您不能修改这些文件，但我建议您(稍后)浏览一下。
*   **/CSS/default . CSS**–Starter CSS，包括通过媒体查询处理屏幕变化的基础知识。我们稍后还将在此基础上进行构建。
*   **/js/default . js**——有一些 starter JavaScript 逻辑。我们很快会在这里添加我们自己的。
*   **default.html**——我们游戏的根 HTML 页面
*   **package . appx manifest**–包含许多帮助您个性化应用程序的设置。它是 XML，但是如果你双击打开，你会得到一个友好的界面来改变设置。

此外，“图像”文件夹有一些默认的应用程序使用的标志和闪屏内容。你会想要添加你自己的。

## 运行游戏…嗯，空项目

所以，让我们运行这个项目，看看我们得到了什么。首先，运行项目有一些[选项:](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh441477%28v=vs.110%29.aspx)

![screen shot of Local Machine settings](img/0c9fe1c08cc1a3e25b7b1a95eb416107.png "Local Machine")

默认情况下使用本地机器，但是也很容易使用远程机器连接到另一台机器，这样您就可以在目标机器上运行和使用项目，同时在本地机器上进行调试。[模拟器](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh441475%28v=vs.110%29.aspx)让你测试各种屏幕尺寸/分辨率，模拟触摸输入，调整位置，等等。

现在，让我们在本地机器上运行(点击按钮，按 F5，或者选择 DEBUG-> Start DEBUG)。

![screen shot of the empty project ](img/46130390c8fe2a990392bda2a4be60f0.png "Empty project")

一个有趣的游戏，的确！

## 入门指南

那么,“内容在这里”的文字是从哪里来的呢？打开`default.html`，你会看到:

```
 <!DOCTYPE html> 
<html> 
<head> 
<meta charset="utf-8" /> 
<title>CatapultGame</title> 

<!-- WinJS references --> 
<link href="//Microsoft.WinJS.1.0.RC/css/ui-dark.css" rel="stylesheet" /> 
<script src="//Microsoft.WinJS.1.0.RC/js/base.js"></script> 
<script src="//Microsoft.WinJS.1.0.RC/js/ui.js"></script> 

<!-- CatapultGame references --> 
<link href="/css/default.css" rel="stylesheet" /> 
<script src="/js/default.js"></script> 
</head> 
<body> 
<p>Content goes here</p> 
</body> 
</html> 
```

这是怎么回事？

*   **第 1 行**–表示我们的目标是 HTML5
*   **第 7-10 行**——引用 WinJS 的 CSS & JavaScript
*   **第 12-14 行**–其他引用，包括默认的 JS 和 CSS 文件。我们稍后会在这里添加其他人。
*   **第 16-18 行**——我们 HTML 页面的主体

如你所见，这只是基本的 HTML。继续下去，在`<body>`中更改 HTML 并再次运行。

## CSS 适合在哪里

我们已经看到了 default.html 文件，它为项目提供了参考和基本内容，但是 HTML 只是故事的一部分。Web 应用程序使用 HTML 来处理内容，使用 JavaScript 来处理事情，使用 CSS(层叠样式表)来以可维护的方式影响设计和外观。

CSS 样式通过识别(选择)目标元素和应用效果来工作——颜色、阴影、文本、过渡、变换、布局……不胜枚举。CSS 有如此多的功能来帮助我们创造惊人的效果，在这里试图概述是荒谬的。只要知道 Visual Studio 2012 有许多功能可以帮助你创建和修改样式，你知道的 CSS 越多，你就越有效。

看一看`/css/default.css`:

```
body { 
  } 

@media screen and (-ms-view-state: fullscreen-landscape) { 
  } 

@media screen and (-ms-view-state: filled) { 
  } 

@media screen and (-ms-view-state: snapped) { 
  } 

@media screen and (-ms-view-state: fullscreen-portrait) { 
  } 
```

默认情况下，没有应用任何样式，但这是为了什么？

*   **Lines 1&2**–这将选择引用该文件的任何页面的`<body>`标签，并将我们添加的任何样式规则应用于该内容。
*   **第 4-14 行**–这些是 CSS 媒体查询，对于根据内容显示方式调整布局非常有用。在这种情况下，Metro 风格的应用程序可以运行的状态有全屏横向、填充、快照和全屏纵向，这些状态映射到四个区域。您可以使用这种方法来定制您的游戏，以最好地利用可用的屏幕空间。

我们稍后将对此进行补充。敬请关注。

## JavaScript，动作英雄

我们已经看到了 HTML 内容和 CSS 样式，但是事情是如何发生的呢？游戏怎么能“知道”发生了什么？

让我们切换到`js/default.js`文件，看一看:

```
 // For an introduction to the Blank template, see the following documentation: 
// http://go.microsoft.com/fwlink/?LinkId=232509 
(function () { 
  "use strict"; 

  var app = WinJS.Application; 
  var activation = Windows.ApplicationModel.Activation; 
  WinJS.strictProcessing();

  app.onactivated = function (args) { 
    if (args.detail.kind === activation.ActivationKind.launch) { 
      if (args.detail.previousExecutionState !== activation.ApplicationExecutionState.terminated) {
      // TODO: This application has been newly launched. Initialize 
      // your application here. 
      } else { 
        // TODO: This application has been reactivated from suspension. 
        // Restore application state here. 
        } 
      args.setPromise(WinJS.IU.processAll()); 
      } 
    }; 

  app.oncheckpoint = function (args) { 
    // TODO: This application is about to be suspended. Save any state 
    // that needs to persist across suspension here. You might use the 
    // WinJS.Application.sessionState object, which is automatically 
    // saved and restored across suspension. If you need to complete an 
    // asynchronous operation before your application is suspended, call 
    // args.setPromise(). 
  }; 

  app.start(); 
  })(); 
```

这可能看起来很多，但是非常简单，只是为了帮助我们开始:

*   **第 3-33 行**–以前没见过这样的 JavaScript 函数？这是一个“立即调用的函数表达式”或 IIFE(读作“iffy”)。短版本，它保持变量和函数整洁，不影响全局命名空间。中等版本，阅读 [Addy Osmani 的基本 JavaScript 命名空间模式](http://addyosmani.com/blog/essential-js-namespacing/)的第 4 节。更长的版本，我是[本·奥尔曼生活贴](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)的粉丝。
*   **第 6-7 行**–常用应用和激活类的变量。
*   **第 8 行**–调用 [WinJS.strictProcessing](http://msdn.microsoft.com/en-us/library/windows/apps/hh987021.aspx) 启用严格的声明性处理(帮助更容易地检测错误)
*   **第 10-21 行**–激活码。第一个 TODO 是当应用程序新启动时，第二个 TODO 是当应用程序从 Windows 暂停状态返回时，我们可以加载保存的状态。
*   **第 23-30 行**–在应用程序暂停前保存状态的机会。特别重要，因为暂停之后可能会终止，如果游戏的状态没有保存在这里，玩家必须在返回游戏时重新开始。

这是我们将很快加入游戏逻辑的地方。从加载图像、管理游戏世界和各种助手功能的代码，到控制更新状态和显示内容的游戏循环。

顺便说一下，在这个示例中，我们将通过只向`default.js`添加代码来做简单的事情，但是良好分解的应用程序通常会有多个 JavaScript 文件，例如围绕游戏元素创建类——玩家、星球、物品、世界等。关于一些优秀的推荐和实践，参见格雷格·史密斯的[《JavaScript 游戏代码组织》](http://buildnewgames.com/js-game-code-org/)。

## 下一步是什么？

这是 Metro 风格应用程序背后的基本齿轮和滑轮的简要概述。下周，我们将通过引入“弹射器战争”启动项目的内容并建立游戏的基本结构来开始创建游戏。

## 进一步阅读

同样，本系列侧重于易学性和简单性，因此要获得更全面的游戏示例，请参见 MSDN 上的[“JavaScript 和 HTML5 触摸游戏示例”](http://code.msdn.microsoft.com/windowsapps/Sample-Game-9303b5c6)和[相关教程](http://msdn.microsoft.com/en-us/library/windows/apps/hh452778.aspx)。

## 分享这篇文章