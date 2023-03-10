# 将“应用”放入渐进式网络应用中

> 原文：<https://www.sitepoint.com/putting-app-progressive-web-apps/>

网络比以往任何时候都好。您现在可以构建快速、丰富的类似应用程序的体验。正如谷歌的扎克·科奇所说:

> 网络和应用之间的界限比以往任何时候都更加模糊。

这是一个很好的机会去创造那些不可思议的经历。

但是网络也有它的问题。我们的移动网络体验并不完美。为什么我们总是倾向于选择下载一个原生 app，而不是浏览它的手机网站？

我们可能会遇到许多移动网络问题:

*   **性能缓慢**–如果加载时间超过 3 秒，普通用户可以离开你的网站。手机网站平均加载时间在 19 秒左右。这是一个巨大的差距。
*   **无响应体验**–你有没有注意到在移动网站上滚动时，滚动速度有多慢？
*   令人沮丧的 UX:有时你可能会误敲页面。你必须等待整个页面加载完毕(平均 19 秒)才能回到上一页。

谷歌早在 2015 年就提出了[渐进式网络应用(PWAs)](https://developers.google.com/web/fundamentals/getting-started/codelabs/your-first-pwapp/) 来解决这些问题，让网络再次变得伟大。

## 什么使一个应用程序成为 PWA？

渐进式 web 应用程序是结合了 Web 和移动应用程序的优点来创建强大体验的体验。我喜欢 Jad Joubran 将 PWAs 定义为“普通移动网络问题的持续解决方案”。它不是一个解决方案，它更像是您可以用来创建更好的 web 应用程序的几个最佳实践。
那么一个网站有什么资格被称为进步的网络应用呢？

*   **快速**–PWA 使用[服务人员](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)和最佳性能实践快速加载并平稳运行。
*   **渐进式**–PWAs 可以在任何地方工作，无论设备是什么。而且它们在现代浏览器中增压。
*   **连接独立**–pwa 离线工作，使用低端连接。即使连接很弱或不稳定，PWA 也会减轻连接变化的影响
*   **引人入胜**–用户更有可能重复使用 PWA，而不是普通网站，这是因为用户可以将 PWA 安装到主屏幕上并推送通知功能

## 创造类似应用的体验

PWA 的关键是创建一个利用 web 技术构建的网站，该网站能够提供本地应用程序体验。为了使 PWA 成功，它应该满足用户的期望。

由于用户最终会从他们的主屏幕上打开 PWA，他们希望它能像普通应用程序一样工作和运行。

### #1 从本地应用中获取 UX 的灵感

设计人员和开发人员的一个常见错误是创建过于“web 化”的设计，比如双导航条、页脚和静态组件。但是为了匹配用户的心智模型，我们需要使用更多类似 app 的设计。例如，用大按钮代替文本链接，或者用固定的底部横条代替页脚。为了帮助创造这种心态，从浏览像 pttrns.com 和 T2 这样的网站寻找设计灵感开始。在 Pttrns 上，你可以找到常见的 UI/UX 模式，比如 onboarding、add to cart 等。

![Pattrns.com for PWA Inspiration](img/59cb70bad9799f9eeebd1172e75223aa.png)

另一方面，谷歌的[材料设计](https://material.io/)创造了一种视觉语言，在原生应用中使用优秀设计的经典原则。当您创建 PWAs 时，如果您提高了对材料设计的熟悉程度以使用这些原则，将有利于最终产品。
有大量的[资源和指南](https://www.sitepoint.com/10-essential-material-design-resources-and-tutorials/)帮助你理解材料设计。从学习运动、风格、模式等物质背后的原理开始。

![Google Material Design for Progressive Web Apps Design](img/2dcd2de6be58f75dc63e0966ccd9f8fe.png)

您还可以使用[材料设计组件](https://material.io/components/web/) (MDC)创建应用程序。MDC 是一个模块化和可定制的 UI 组件库。您可以快速编写波纹动画、材质卡片、材质主题颜色等内容。

![Google Material Design Components Library](img/d4e46152f8781e0d575f4527b191168a.png)

### #2 确保你的应用程序图标看起来不错

记住，你的应用程序将位于用户的主屏幕上。这意味着你的图标应该在视觉上匹配本地应用程序图标。有一些视觉标准你应该仔细考虑。

![Progressive Web Apps and Native Icons](img/05db45da78f4f89025301c54bdcbd6aa.png)

你的图标将被用于闪屏、任务切换器、通知横幅、应用安装横幅等。这意味着它应该反应灵敏，可以在不同的尺寸下工作。你可以把它做成一个简单独特的形状，在小号里看起来也不错。在一个图标中有太多的细节会显得笨重和怪异。

此外，确保应用程序图标在所有平台上都好看。例如，iOS 上的图标不能是透明的，它们需要一个纯色的正方形背景。如果你上传透明背景的图标，他们会给你加一个黑色背景，看起来可能不太好。

![Web App Icon Design Done Wrong](img/b92fc651a37b929673c05167d997de5d.png)

了解不同平台需求的最佳方式是使用图标生成器，比如 realfavicongenerator.net T2。您可以一次看到所有的变化，并让您针对每个平台进行调整。然后生成一个 [`manifest.json`文件](https://developer.chrome.com/apps/manifest):

![Realfavicongenerator.net](img/f3adc9ec10d100fba2e9a7f4ff20c163.png)

### #3 使用主题颜色打造你的应用品牌

主题颜色是给你的应用一个品牌身份和脱颖而出的能力的好方法。这是让 PWA 有本土感的主要特征之一。

有两种类型的主题颜色。我喜欢称它们为浏览器颜色和应用程序颜色。这是它们之间的区别。

*   **浏览器主题颜色**–这是当您从浏览器打开应用程序时，您在浏览器标题上看到的背景颜色。

![Browser Theme Color](img/4592c2106db71e0cc919c7fe4677a4b6.png)

要指定浏览器主题颜色，使用`<head>`中的元主题颜色。

```
<meta name="theme-color" content="#4285f4">
```

*   **应用主题颜色**–这是您通过主屏幕访问应用时在应用标题上看到的颜色。它也用在任务切换模式的标题中(预览这个[动画 GIF](https://i.imgur.com/6XbyV9u.gif) 的视觉表现):

![App Theme Color](img/5a1f6b069931436fd462455650650479.png)

要指定应用程序主题颜色，请将主题颜色添加到`manifest.json`文件中:

```
{
  "theme_color": "#2196F3"
}
```

### #4 使用应用外壳模型

PWA 最重要的特征之一是弹性。PWA 应该能够顺利运行并脱机工作。但最重要的是，PWA 应该快速加载，如果不是立即加载的话。

快速的第一次加载改善了新用户的体验，并且会有本地的感觉。在常规网站中，第一次加载需要用户在白屏上等待几秒钟，然后所有组件将同时加载(查看[这个视频短片中的视觉示例](https://i.imgur.com/fkFI2f8.gif))。

一个更进步的方法是[立即开始尽可能快地一个接一个地加载组件。这种方法更令人满意，因为它减少了用户的不确定性，并且被证明减少了对时间的感知。](https://i.imgur.com/nr5idqY.mp4)

一个很好的方法是使用应用程序外壳模型。应用程序外壳是支持用户界面的最小 HTML、CSS 和 Javascript。当你使用一个应用程序外壳时，你首先开始加载它，越快越好，然后加载你的动态组件。

![Twitter App Shell](img/adf11519a80ffef81bae77c782a051f1.png)

应用程序外壳最常见的组件是导航栏、标签栏(或侧边栏)、加载器和主操作按钮。要优先考虑应用程序外壳并首先加载它，可以内联与应用程序外壳相关的 CSS:

```
<style> body{
  background-color: #ececec;
  margin: 0;
}
.shell-header{
  background-color: #2196F3;
  height: 56px;
}
.fab{
  position: fixed;
  bottom: 40px;
}
... </style>
```

还有许多其他技术可以用来在 PWAs 中创建渐进式快速加载，比如缓存应用程序外壳、预加载它、解除 JS 阻塞等等。

### #5 限制你的字体

第一次加载缓慢的最常见原因之一是字体。尽管排版是网页设计的一个重要部分，不幸的是拥有强大的性能是一个挑战。缓解这个问题的一个方法是限制你的字体。尽量只使用你需要的字体。确保你没有加载任何你不使用的字体粗细，并尽量将字体文件限制在最多 3 个(这包括不同的粗细)。

在大多数情况下，会发生以下情况:字体文件在 CSS 文件完全下载后开始渲染。这就产生了一个被称为 FOUT 的问题。

![Font Loading](img/0228840213ee8c1cdd67c55525b8c264.png)

为了解决这个问题，您可以通过将这个代码片段添加到 HTML 文档的`<head>`部分来预加载您的字体文件:`link rel="preload"`。确保指定字体类型并添加交叉原点以避免重复请求:

```
<link rel="preload" href="roboto.woff2" as="font" type="font/woff2" crossorigin>
```

通过预加载字体，当字体开始与 CSS 文件同时加载时，您将能够立即看到字体，而不会阻止任何内容，从而避免 FOUT。

![Preloading Font Files](img/2c05b74ac50bae578d13883138ea2349.png)

这是一个很好的解决方案。但是如果有更好的方法呢？既然你正在创建具有本土和本地感觉的应用程序，那么如果有一种方法可以调整应用程序的版式以匹配用户的操作系统呢？要在 Android 上使用 Roboto，在 iOS 上使用 San Fransisco 等。

嗯，有！尽可能堆叠现代平台的所有字体。用户的浏览器将使用适当的字体，而忽略其他字体。完全没有重量。这样，您的用户将会有一种宾至如归的感觉，因为他们已经习惯了这些字体:

```
font-family: -apple-system, BlinkMacSystemFont, // Safari Mac/iOS, Chrome
”Segoe UI”, Roboto, Oxygen, // Windows, Android, KDE
Ubuntu, Cantarell, "Fira Sans”, // Ubuntu, Gnome, Firefox OS 
“Droid Sans”, ”Helvetica Neue”, sans-serif; // Old Android
```

Medium 正在他们的应用程序中使用这种技术。他们在用户界面中堆叠设备字体。Roboto 字体在 Android 上显示，而 San Fransisco 字体在 iOS 上呈现。

![Native Fonts](img/a5f1e8eaf77b27e754a465b7b7bd8e34.png)

### 拿走

正如谷歌人欧文·坎贝尔-摩尔所说:

> 进步的网络应用给了我们一个机会来重新设定我们的期望，并大声宣布我们可以在设计网络用户体验时做得更好。

通过采取这几个步骤，你真的可以在网上建立惊人的体验。你可以做更多的事情来创造类似应用的体验，比如推送通知、离线体验等等。这篇文章的要点是:

*   总是从本地应用中获取灵感
*   使用这样的清单准备你的 PWA 材料
*   使用真实设备进行测试，并在 Chrome 开发工具中使用节流功能
*   使用 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 评估性能、指标、可访问性等。

## 分享这篇文章