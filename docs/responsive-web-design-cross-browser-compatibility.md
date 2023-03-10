# 如何测试响应式网页设计的跨浏览器兼容性

> 原文：<https://www.sitepoint.com/responsive-web-design-cross-browser-compatibility/>

HTML 本质上是一种流动的媒介。文本和容器水平和垂直扩展以利用可用空间。这种流动性使得复杂的设计变得更加困难，所以在世纪之交，许多网站采用了基于流行屏幕尺寸的固定宽度。

随着屏幕尺寸从 800×600 增加到 1024×768 甚至更大，该工艺仍然可行。然而，智能手机的兴起和 2007 年 iPhone 的发布扭转了这一趋势。今天，[超过一半的用户在更小的移动设备上访问网页](https://gs.statcounter.com/platform-market-share/desktop-mobile/worldwide/#monthly-202008-202008-bar)。

注意:从技术上来说，智能手机通常比许多显示器的分辨率都高，但单个像素变得不可见。一部 iPhone 11 Max 将其 2688 x 1242 的硬件分辨率转化为更实用的 896 × 414 逻辑分辨率。每个逻辑像素映射到一个 3×3 实际像素的网格，这可以实现更平滑的字体和更多的图像细节。

最初的解决方法是两个站点:一个用于桌面，一个用于移动，通常在必要时使用用户代理嗅探进行重定向。随着设备种类呈指数增长，这很快变得不切实际。

最后，术语*响应式网页设计* (RWD)是由[伊森·马科特在 2010 年](https://alistapart.com/article/responsive-web-design/)发明的。这项技术允许同一个网站在任何设备上工作，而不管屏幕大小或视窗尺寸。

## RWD 是如何工作的？

没有单一的 RWD 方法或技术。

首先，你必须确定一个网站设计将如何应对不同大小的显示器。这是一个挑战，许多第一个 RWD 网站采用了现有的桌面布局，并随着屏幕尺寸的减小而删除了一些部分。

一个更好的技术是**先移动**。它从线性移动视图开始，可以在所有设备上工作，然后随着更多空间和支持的浏览器功能的出现，重新排列或调整内容。最近，许多网站采用了更简单的布局，移动和桌面体验基本相似。

RWD 的一个典型例子是汉堡包菜单。较小屏幕上的用户可以点击图标来查看导航链接，而较大屏幕上的用户可以看到水平列表中的所有选项。

以下部分提供了一些技术实现选项。

### HTML `viewport` Meta 标签

不考虑任何 RWD 技术，下面的标签**必须**设置在你的 HTML `<head>`中:

```
<meta name="viewport" content="width=device-width,initial-scale=1"> 
```

`width=device-width`设置确保移动浏览器将逻辑 CSS 像素缩放到屏幕宽度。如果没有这个，浏览器会认为它在渲染一个桌面站点，并相应地缩放它，这样它就可以被平移和缩放。

### 媒体查询

媒体的询问是第一批 RWD 网站的主要基础。它们允许 CSS 瞄准特定范围的视口尺寸。例如:

```
/* styles applied to all views */
p {
  font-size: 1rem;
}

/* styles applied to viewports
   with a width between 900px and 1200px */
@media (min-width: 900px) and (max-width: 1200px) {

  p {
    font-size: 1.5rem;
  }

} 
```

虽然现在有了不太明确的选项，但是仍然使用媒体查询。

### `<picture>`元素

HTML `<picture>`元素使用媒体查询语法来控制从`<source>`选项中选择显示哪个图像。这通常用于艺术指导，以便为设备视窗显示合适的图像。例如:

```
<picture>

  <!-- image shown when viewport
       width is greater than the height -->
  <source  srcset="landscape.jpg"
            media="(min-aspect-ratio:1/1)"
              alt="landscape" />

  <!-- default image -->
  <img src="portrait.jpg" alt="portrait" />

</picture> 
```

### CSS Viewport Units

`vw`和`vh` CSS 单位分别代表视口宽度和高度的 1%。`vmin`是最小尺寸的 1%，而`vmax`是最大尺寸的 1%。

这些允许 RWD 的灵活性，特别是当与`calc`一起使用时。例如:

```
/* font size increases with viewport width */
p {
  font-size: 1rem + 0.5vw;
} 
```

### CSS 列

CSS 多列布局提供了一种随着容器尺寸增加而创建多个文本列的方法。例如:

```
/*
columns must be a minimum width of 12rem
with a gap of 2rem between each
*/
.container {
  columns: 12rem auto;
  column-gap: 2rem;
} 
```

### CSS Flexbox 和网格

CSS Flexbox 和 [CSS Grid](https://developer.mozilla.org/Web/CSS/CSS_Grid_Layout) 提供了现代技术，根据内容和可用空间来布局子元素。主要区别:

*   Flexbox 用于一维布局。元素可以根据需要换行(或不换行),这样列就不会对齐。
*   网格是二维布局，通常有可识别的行和列。

两者都可以用来创建一个*内在布局* ( [一个由 Jen Simmons](https://aneventapart.com/news/post/designing-intrinsic-layouts-aea-video) 发明的术语)。本质上，元素是根据视口尺寸来调整大小的，而不需要媒体查询。例如:

```
/*
Child elements will be at least 20rem and fill the row.
Displays smaller than 20rem will have elements sized to 1fr
(100% of the available width).

A 1rem gap will always surround elements.
*/
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(20rem, 1fr));
  grid-gap: 1rem;
} 
```

### JavaScript RWD 选项

JavaScript 还可以用于确定视口尺寸并做出相应的反应。例如:

```
// get viewport width and height
const
  vw = window.innerWidth,
  vh = window.innerHeight; 
```

类似地，可以使用 [offsetWidth](https://developer.mozilla.org/Web/API/HTMLElement/offsetWidth) 和 [offsetHeight](https://developer.mozilla.org/Web/API/HTMLElement/offsetHeight) 检查单个元素的尺寸，尽管 [getBoundingClientRect()方法](https://developer.mozilla.orgWeb/API/Element/getBoundingClientRect)可以返回更多信息，包括像素的分数:

```
const
  element = document.getElementById('myelement'),
  rect    = element.getBoundingClientRect(),
  ew      = rect.width,
  eh      = rect.height; 
```

当旋转设备或调整浏览器窗口大小时，窗口和元素的尺寸会发生变化。 [`matchMedia` API](https://developer.mozilla.org/Web/API/Window/matchMedia) 可以解析 CSS 媒体查询[触发变更事件](https://developer.mozilla.org/Web/API/MediaQueryList/addListener):

```
// media query
const mql = window.matchMedia('(min-width: 600px)');

// initial check
mqTest(mql);

// call mqTest when media query changes
mql.addListener(mqTest);

// media query testing function
function mqTest(e) {

  if (e.matches) {
    console.log('viewport is at least 600px width');
  }
  else {
    console.log('viewport is less than 600px width');
  }

} 
```

### 浏览器支持

首先，RWD 技术提供了良好的浏览器支持。最近的选择——CSS Grid——被现在使用的几乎 95%的浏览器支持。然而，仍然有必要在一系列设备、分辨率和浏览器上测试你的网站…

## 浏览器内测试

在几个小时内，调整浏览器窗口大小是一个合理的测试策略，但是它很快变得不准确和麻烦。大多数浏览器都提供了*响应式设计模式*，可以让你选择设备和用户代理，旋转它，选择分辨率，改变像素密度，调节带宽，模拟触摸，以及截图。

在 Firefox 中，从 **Web Developer** 菜单中选择**响应式设计模式**或者按`Ctrl` | `Cmd` + `Shift` + `M`:

![Firefox Responsive Design Mode](img/df59b7dcb31d950d18c4ead8be6865d3.png)

在基于 Chromium 的浏览器中，从**更多工具**菜单中打开**开发者工具**或者按`Ctrl` | `Cmd` + `Shift` + `I`。然后点击**切换设备工具栏**图标:

![Firefox Responsive Design Mode](img/df59b7dcb31d950d18c4ead8be6865d3.png)

切换回浏览器选项卡以查看调整后的网站:

![Chrome device toolbar](img/32753f1f6a6662e32e8572f1802d7a54.png)

在 Safari 中，从浏览器**偏好设置**的**高级**标签中启用**在菜单栏**中显示开发菜单选项。加载页面，从**开发**菜单中选择**进入响应设计模式**。

但是，请注意，这些工具仅*模拟*设备的屏幕尺寸和用户代理。它们无法准确模拟以下内容:

*   **渲染能力**

    浏览器将使用自己的渲染引擎，而不是仿真设备的渲染引擎。在 Firefox 中工作的 CSS 特性将“工作”在它的模拟 iPhone 视图上，而不管实际支持如何。也就是说，Chrome 桌面将显示出与 Android Chrome 的合理近似，macOS Safari 将与 iPhone 相似，因为它们基于相同的渲染引擎。

*   **旧设备**

    在最新版本的 Safari 上测试 iPhone 浏览器视图无法准确地表示使用传统操作系统和软件的旧设备。

*   **高密度显示器**

    你受到电脑显示器物理像素的限制，所以一个网站在真实设备上看起来可能更好或更差。

*   **触摸**

    鼠标或触控板比带有小显示屏的触摸屏设备具有更好的控制。可能无法查看悬停时应用的效果。

*   **加工速度**

    你的电脑可能比移动设备有更快的网络和处理速度。

## 移动操作系统模拟器

在您的设备上运行 Android 或 iOS 虚拟机允许您安装和运行真正的移动浏览器，并使用它们实际的渲染引擎。

Android 模拟器包括:

*   [Genymotion](https://www.genymotion.com/) :免费和商业云选项，适用于 Windows、macOS 和 Linux 的所有 Android 版本
*   [Android Studio(和模拟器)](https://developer.android.com/studio):免费，Windows，macOS，Linux
*   [Visual Studio Xamarin(和模拟器)](https://visualstudio.microsoft.com/xamarin/):商用、Windows 和 macOS
*   [Bliss OS](https://blissroms-x86.github.io/) :免费，安卓 9，Windows，Linux，Chrome 操作系统
*   [凤凰 OS](http://www.phoenixos.com/) :免费，Windows 和 macOS 的 Android 7.1 或 5.1
*   [Android-x86](https://www.android-x86.org/) :任意虚拟机平台免费 Android ISOs。

Chrome 是 Android 的显而易见的浏览器选择，但你也可以安装 [Opera Mini](https://play.google.com/store/apps/details?id=com.opera.mini.native) ，这在低功率功能手机上很突出。

iOS 的选项更加有限:

*   [苹果 Xcode(和模拟器)](https://developer.apple.com/xcode/):商用，macOS
*   [电动移动工作室](http://www.electricplum.com/studio.aspx):商业，Windows
*   [iPadian 模拟器](https://ipadian.net/):商用，Windows

这些模拟器仍然有缺点:

*   该软件需要一些专业技术知识，并使用大量的系统资源。
*   很多 iOS 选项都是*模拟器*。他们采用另一个渲染引擎，并不总是准确的。

## 在线测试服务

*这个部分是与 [LambdaTest](https://www.lambdatest.com/) 合作创建的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

一些在线服务允许你在移动浏览器上测试网页的响应。本质上，你在一个真实的设备上租借时间，并且可以在你的浏览器中查看它的屏幕。不需要安装或维护任何软件。

除了实时测试之外，一些服务还包括自动化测试 API，允许你运行脚本并检查风格退化或损坏的用户界面。

[**LambdaTest**](https://www.lambdatest.com/) 提供超过[2000 种设备、操作系统和浏览器的组合](https://www.lambdatest.com/list-of-browsers)。[功能](https://www.lambdatest.com/feature)包括:

*   测试在您的开发电脑上运行的`localhost`页面
*   使用集成的开发工具进行调试
*   从不同位置进行地理定位测试
*   跨多个设备自动生成的整页屏幕截图
*   内置问题跟踪器
*   [LT 浏览器软件](https://www.lambdatest.com/lt-browser) (Windows、macOS、Linux)，用于测试和比较具有自动重新加载和滚动同步功能的设备
*   基于硒的自动化测试 API
*   全天候支持
*   每月 15 美元起无限制使用的免费计划。

[**注册一个免费的 LambdaTest 账户……**](https://accounts.lambdatest.com/register)

![LambdaTest live view](img/d65435745250956db6ff68bf80d86cfa.png)

还提供实时和自动移动测试的替代服务包括:

*   [浏览器堆栈](https://www.browserstack.com/)
*   [酱实验室](https://saucelabs.com/)
*   [交叉浏览器测试](https://crossbrowsertesting.com/)

## 真实设备测试

最后，在真实设备上进行测试是无可替代的。这是评估实际处理速度、触摸控制和网站响应性网页设计的最佳方式。

理想情况下，您应该测试尽可能多的设备，但是您自己最近的智能手机可能并不能代表一般的硬件。尝试购买一两年前的中档设备，比如二手的 Moto G7 或 iPhone 8。

同一网络上的设备可以通过在浏览器中输入其 IP 地址来访问您的 PC 服务器。这可以通过 macOS 和 Linux 上的`ifconfig`或 Windows 上的`ipconfig`获得。

你也可以用 USB 线把智能手机连接到电脑上。这使您可以远程控制设备，并轻松访问其开发人员工具面板进行调试。技术因平台而异，但是，要在浏览器的桌面版上调试 Android Chrome:

1.  在 Android 设备上，从**设置**中选择**开发者选项**，并启用 **USB 调试**。
2.  使用合适的 USB 电缆将设备连接到电脑。首次尝试时，系统可能会提示您在一台或两台设备上确认操作。
3.  在你的电脑上启动 Chrome 并在一个新标签中打开<chrome:>。确保**发现 USB 设备**已启用。</chrome:>
4.  可选地，设置**端口转发** —例如，远程设备上的端口`8888`可以转发到`localhost:8888`。
5.  您的设备应该会出现在列表中。您现在可以检查一个新的或现有的选项卡，该选项卡会打开设备的开发人员工具:

![remote device DevTools](img/9dfb83e0fa3c83fb28ef87647c419fc0.png)

若要调试 iPhone 的 Safari，请执行以下操作:

1.  将手机连接到苹果电脑。
2.  在 iPhone 上的 Safari 中打开您想要调试的网页。
3.  在电脑上启动 Safari。
4.  在电脑上的 Safari 中，进入**开发** > **【你的 iPhone】**>**【网站考察】**。这将在您的电脑上打开 Safari 的开发者工具，允许您在 iPhone 上调试站点。

## 一个站点，多个视图

响应式网页设计技术允许你创建一个单一的网站，任何人都可以在任何设备上浏览，不受技术限制和边界的限制。让它成为一个很好的用户体验是另一回事，但是测试工具的范围和能力在继续改进。

## 分享这篇文章