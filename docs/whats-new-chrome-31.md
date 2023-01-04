# Chrome 31 有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-chrome-31/>

*“非常少”*可能是最好的答案。Chrome 版本很少提供很长的功能列表，但是有一些有趣的技术值得注意。

点击工具图标，然后点击**关于谷歌 Chrome** 以确保您在 Windows、Mac 或 Linux 上拥有最新版本。如果你是少数几个没有安装浏览器的人之一，那就去 google.com/chrome 的。

## 自动完成表单和请求 Autocomplete()

表单自动完成在浏览器中已经存在很多年了。输入您的电子邮件地址，当在其他地方的任何表单上再次提示时，您通常可以选择它。

`requestAutocomplete()`是一个新的 API，允许用户存储常用的支付细节，如信用卡号码。用户可以选择预定义的付款和运输值，而不是完成结账过程:

[![requestAutocomplete](img/4aeb650c3f3b13ff0c9d02de50e14b31.png)](https://www.chromium.org/_/rsrc/1368673401311/developers/using-requestautocomplete/1Zh85u4gzhhDkC6myiRAyO2SH1iRgCCbUoJ4O.png)

HTML 字段自动完成属性通常可以设置为“开”(默认)或“关”，但 Chrome 扩展了这一属性，允许您设置适当命名的值，如姓名、电话、电子邮件、抄送人姓名、抄送人号码等:

```
<form id="payment" method="/pay">
	<input autocomplete="cc-name" name="myname">
	<input autocomplete="cc-number" name="ccnumber">
	<input autocomplete="cc-exp" name="ccexp">
	<input autocomplete="cc-csc" name="cccvc">
</form>
```

HTMLFormElement 的`requestAutocomplete()`方法显示了单击“Checkout”等按钮时的自动完成选择。一个`autocomplete`事件表示已经做出了选择，并且表单已经填写好准备提交。如果出现取消等问题，就会触发一个`autocompleteerror`事件。

这个 API 还不是一个标准，但是看起来很简单，并且会逐渐增强现有的表单。如果用户信任将他们的信用卡信息交给谷歌应用程序进行安全保管，这将会很好地工作。

更多信息，请参考[使用 requestAutocomplete()](https://www.chromium.org/developers/using-requestautocomplete) 。

## 可移植本机客户端(PNaCl)

Google 的 Native Client (NaCl)是一个沙箱，用于直接在浏览器中运行编译好的 C/C++代码。换句话说，您可以将本地低级可执行文件下载到浏览器，该浏览器以接近最高的操作系统速度运行。该技术非常适合视频编辑器和 3D 游戏等复杂的高性能应用。

为了确保 NaCl 在任何地方都能工作，它要求所有设备都有特定于架构的表示。换句话说，开发人员必须多次编译他们的应用程序，才能在 x86、ARM 或 MIPS 设备上运行。

新的可移植 Native Client 通过将 C/C++编译成中间字节码，使生活变得更加轻松。当资源被访问时，Chrome 下载可移植的可执行文件并将其翻译成特定于架构的机器代码，这些代码针对运行它的设备进行了优化。这是一个类似于 Java 和. NET 的概念。

NaCl(和 PNaCl)目前是 Chrome 独有的特性，拥有一批追随者。技术让我不舒服；走极端的话，我希望浏览器永远不要成为可执行的分发平台。

我还怀疑这项技术有点超前。如果您需要本机操作系统的速度，您可能正在构建一个大型应用程序，那么为什么不为桌面构建呢？只要小心，你可以让它跨多个操作系统工作，它会给你更多的用户体验控制权。然后，它可以作为大型可执行文件分发，只需下载和安装一次。你也可以在晚上睡得很香，因为你知道谷歌不能把 PNaCl 从你下面拉出来。

web 运行良好，因为它与操作系统/设备无关，并允许应用程序利用网络连接进行协作。原始速度通常是次要的，但 NaCl/PNaCl 使谷歌能够在桌面应用程序市场上获得更大的份额。

## 杂项更新

其他次要功能包括:

*   WebM 中的视频 alpha 透明度
*   一个新的内部 [chrome://components](//components) 页面来检查已安装的组件
*   打包的应用程序可以针对[特定 URL](https://developer.chrome.com/apps/manifest/url_handlers.html)
*   用于在扩展中捕获屏幕的测试版
*   一个新的`chrome.sessions` API，用于查询和恢复扩展中的标签
*   用于从扩展名访问文件的新的`chrome.fileSystem` API
*   重新设计的*报告问题*对话框
*   Android 版 Chrome 允许将网站添加到主屏幕上
*   [25 个安全修复](https://googlechromereleases.blogspot.co.uk/2013/11/stable-channel-update.html)

当开发人员发现 Google 和 Mozilla 已经决定为新的 CSS3 和 JavaScript 特性放弃供应商前缀时，他们也会小小地欢呼一声。不幸的是，你仍然需要那些`-webkit`前缀，因为它们还没有从 Chrome 中移除，而且在一段时间内 Safari 还会需要它们…

## 分享这篇文章