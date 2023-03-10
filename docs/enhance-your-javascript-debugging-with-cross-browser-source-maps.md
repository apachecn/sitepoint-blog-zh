# 使用跨浏览器源代码地图增强您的 JavaScript 调试

> 原文：<https://www.sitepoint.com/enhance-your-javascript-debugging-with-cross-browser-source-maps/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

作为一名 JavaScript 开发人员，我相信您已经陷入了这种情况:您的代码的生产版本出现了问题，直接从生产服务器调试它是一场噩梦，因为它已经被缩小或已经由另一种语言编译，如 [TypeScript](http://www.typescriptlang.org/?WT.mc_id=13394-DEV-sitepoint-article18) 或 CoffeeScript。

好消息是什么？最新版本的浏览器可以通过使用源图来帮助你解决这个问题。在本教程中，我将向您展示如何在所有的浏览器中找到源地图，并充分利用您必须调试的几分钟时间。

## 等等，什么是源图？

根据 JavaScript Source Maps 的伟大文章[介绍，source map 是“一种将组合/缩小的文件映射回未构建状态的方法。当您为生产而构建时，在缩小和合并 JavaScript 文件的同时，您会生成一个包含原始文件信息的源映射”。](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/?WT.mc_id=13394-DEV-sitepoint-article18)

请不要犹豫，先读一下 Ryan Seddon 的文章，因为它详细介绍了源地图是如何工作的。然后，您将了解到 source map 使用一个中间文件来匹配代码的生产版本和它的原始开发状态。此处描述该文件的格式:[源地图修订 3 提案](https://docs.google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit?hl=en_US&pli=1&pli=1?WT.mc_id=13394-DEV-sitepoint-article18)

现在为了说明，我将分享我们目前在开发 WebGL Babylon.js 开源框架时的工作方式:[http://www.babylonjs.com](http://www.babylonjs.com/)。它是用打字稿写的。但是，如果您使用普通的 JavaScript 压缩/缩小版或其他语言(如 CoffeeScript ),原则将保持不变。

现在让我们直接在浏览器中玩源地图魔术。

## 我们将要使用的演示页面

最近，我一直在我们的游戏引擎中实现对[游戏手柄 API 的支持。让我们在本教程中使用它的代码。](https://www.youtube.com/watch?v=hQaKtGQ6qDo?WT.mc_id=13394-DEV-sitepoint-article18)

在本文中，我使用了以下浏览器:

*   **ie 浏览器 11** ，[8 月更新](http://blogs.msdn.com/b/ie/archive/2014/08/13/august-updates-for-internet-explorer.aspx?WT.mc_id=13394-DEV-sitepoint-article18)(版本 11.0.9600.17239)甚至更好，开发者渠道版本: [devchannel.modern.ie](http://devchannel.modern.ie/?utm_source=SitePoint&utm_medium=article18&utm_campaign=SitePoint) 支持 Gamepad API。关于 IE 的补充说明:微软正在开发一款新的浏览器[微软 Edge](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx) ，所以一定要查看最新的网络标准支持。
*   **Chrome** 38 开发者频道(版本 38 . 0 . 2125 . 8 dev-m)/**Opera**23
*   火狐 31 或火狐 34 每晚

导航到[这个网址](https://david.blob.core.windows.net/babylonjs/gamepad/index.html)，你会看到这个页面:

![GamePad Test Page](img/654e4ce3b1dc22cc493638fc8782b661.png)

将 Xbox 360 或 Xbox One 控制器插入机器的 USB 端口。按下 A 按钮激活游戏手柄，开始玩游戏:

![Test Page Properties](img/fe50c0e904e81aa25c29815e4a1d3865.png)

但是不要担心，你不需要游戏手柄来跟随这个教程。

**注意:**TypeScript 编译器正在自动为您生成源映射。如果你想在生成缩小版代码的同时生成一个源地图，我推荐你使用丑陋的 js2:【https://github.com/mishoo/UglifyJS2

对于这篇文章，我甚至混合了两者。我使用下面的命令行简化了 TypeScript 生成的 JS，并保持了源映射的完整性:

```
uglifyjs testgamepad.js -o testgamepad.min.js --source-map testgamepad.min.js.map --in-source-map testgamepad.js.map 
```

## 如何使用原始源代码进行调试

### 使用 Internet Explorer 11

一旦游戏手柄测试页面加载完毕，在 IE11 中按 F12。

您将看到 HTML 源代码引用了两个 JavaScript 文件:页面开头的`babylon.gamepads.js`和页面最后的`testgamepad.min.js`。第一个文件来自我们在 [Github](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Tools/babylon.gamepads.js?WT.mc_id=13394-DEV-sitepoint-article18) 上的框架，第二个文件是一个简单的示例，展示了如何使用它。

按下“调试器”按钮(或 CTRL+3)，然后按下文件夹图标。

你会看到 IE11 提供了两个文件来调试:`babylon.gamepads.ts`和`testgamepad.min.js`。

![Debug File Picker](img/2cc473e86cd7f215d11921236a8a8156.png)

先来回顾一下`babylon.gamepads.ts`案例。为什么 IE 显示它而不是？浏览器正在执行的 js 版本？

这要归功于源图机制。在`babylon.gamepads.js`文件的末尾，您可以找到这一行:

```
//# sourceMappingURL=babylon.gamepads.js.map
```

打开“babylon.gamepads.js.map”了解其工作原理:

```
{
    "version": 3,
    "file": "babylon.gamepads.js",
    "sourceRoot": "",
    "sources": [ "babylon.gamepads.ts" ], 
    "names": [ "BABYLON", "BABYLON.Gamepads", ... ]
}
```

通过读取这个 JSON 文件，IE11 知道它应该将`babylon.gamepads.ts`映射到`babylon.gamepads.js`。这就是为什么，它直接让你调试 TypeScript 源代码，而不是编译的 JS 版本。

在 IE11 F12 控制台中打开`babylon.gamepad.ts`，你会看到一些你可能从未见过的东西，一些打字稿语言:

![Debugging gamepadjs](img/4e1aa10e708240ec0baf10a262789c12.png)

您可以像调试 JS 代码一样调试它。即使它是浏览器当前正在执行的编译后的 JavaScript 版本。

在第 17 行设置一个断点，并在浏览器窗口中按 F5 键。您将能够调试构造函数的 TypeScript 版本:

![Setting Breakpoints](img/e08254ec6a7d3f1c5d0b487733be6f5f.png)

跳到第 20 行，将鼠标悬停在`this`上并展开，检查`gamepadEventSupported`是否设置为真:

![Resolving this](img/2d58a740ec358b2161a7d2d751a63b8b.png)

### 使用 Chrome/Opera

加载[同一个页面](https://david.blob.core.windows.net/babylonjs/gamepad/index.html)在 Chrome 中按 **F12** 或者在 Opera 中按 **CTRL+SHIFT+I**

![Using Chrome dev tools](img/daf7be6edc1e7f042c3c2fb8dc0b386a.png)

点击配置图标，确保**Enable JavaScript source maps**选项已启用。它应该默认设置为:

![Enabling JS Source Maps](img/90a5dffe6d1e8d8e7460d313136ffdde.png)

Chrome/Opera 允许你查看已执行的`babylon.gamepads.js`代码，但如果你试图在 JavaScript 版本中设置断点，它不会显示出来。它会在映射到这个版本的源代码中设置它:`babylon.gamepads.ts`。

例如，尝试在`babylon.gamepads.ts` JavaScript 文件的第 18 行设置一个断点，您会看到它将被设置在`babylon.gamepads.ts` TypeScript 文件的第 17 行:

![Setting Up the TypeScript file](img/c3c2cf7fb7a3672d3df6f3bb85abf499.png)

当然，您也可以直接在 TypeScript 源代码中设置适当的断点，就像之前在 IE11 中看到的那样。

在托管我们的演示页面的浏览器窗口中按 F5，现在您就可以调试我的 TypeScript 代码了。滚动到第 20 行，将鼠标放在`this.gamepadEventSupported`变量上。它应该显示为真:

![Resolving this](img/d068759f187c60b6b8b264bab9143ba9.png)

### 使用 Firefox

加载[同一个页面](https://david.blob.core.windows.net/babylonjs/gamepad/index.html)按 CTRL+SHIFT+S 打开原生调试器(不要用 F12/Firebug，不支持源码图)。

检查是否勾选了`Show Original Sources`选项:

![Showing Original Sources](img/3aeed17905714c7396155c856293a256.png)

像往常一样在第 17 行设置一个断点，并重新加载页面以中断代码。火狐不支持鼠标悬停在原源码上。转到第 20 行，在右侧面板中展开`this`对象，检查`gamepadEventSupported`是否也设置为 true。

![Gamepad Support true](img/239706fa716b0ead732c7cc443571095.png)

## 缩小后的 JavaScript 文件呢？

到目前为止，我们只使用`babylon.gamepads.ts`源代码调试了`babylon.gamepads.js`代码。但是这个缩小的`testgamepad.min.js` JavaScript 文件呢？

第一个解决方案是美化代码。它可以与 IE11/Chrome/Opera & Firefox 配合使用。

在 **IE11** 中，按“漂亮打印”按钮或按 CTRL+SHIFT+P:

![Minified File](img/11141df03f0f46ca4fabd8901b1f8d00.png)

在 **Chrome/Opera** 中，按下“{}”按钮:

![Minified File in Chrome](img/c7ff71ecddaf87a64018e71336ede7b6.png)

在 **Firefox** 中，也按下“{}”按钮:

![Minified File in Firefox](img/7e3b52d90fd85e99c9b307570bca8a24.png)

这要好得多，但仍不如使用源贴图集时好。为了能够更进一步，我们刚刚在 IE11 的[8 月更新中添加了一个新功能。即使已经从中移除了源映射，也可以加载它。js 文件。](http://blogs.msdn.com/b/ie/archive/2014/08/14/announcing-new-f12-dev-tools-features-in-august-update.aspx?WT.mc_id=13394-DEV-sitepoint-article18)

想象一下，你想避免其他人使用 source map 调试你的 web 应用程序的生产代码，但是你仍然希望能够这样做？只需从您的。js 文件。

使用 Internet Explorer 11，您仍然可以通过加载您的本地。地图和相关源代码。为此，右键单击“testgamepad.min.js”选项卡，然后按“选择源地图”:

![Source Map](img/0bbaa458812e280df67227411370dd26.png)

在这里下载它们[。](https://david.blob.core.windows.net/babylonjs/gamepad/testgamepad.zip)

将它解压缩到您的首选目录中，并导航到它以选择正确的。地图文件:

![Map File](img/7b48ec901e2d4cffef6e89868a2b0b13.png)

现在可以再次调试原始源代码:

![Original Source Code](img/dbfd687a1a96c6afb5dfb3be73ff90f0.png)

请注意，该选项卡已被重命名为`testgamepad.ts`，并且第一个变量现在已被类型化，因为我们正在显示一些类型脚本。

很酷吧。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享[微软 Edge](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=13394-DEV-sitepoint-article18) 和它的[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=13394-DEV-sitepoint-article18)。在 [modern 获得免费虚拟机或在你的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=article18&utm_campaign=SitePoint)。

## 分享这篇文章