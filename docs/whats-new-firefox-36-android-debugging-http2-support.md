# Firefox 36 的新功能:Android 调试、HTTP/2 支持等等！

> 原文：<https://www.sitepoint.com/whats-new-firefox-36-android-debugging-http2-support/>

Firefox 35 发布六周后，用户可以接触到新的 36 更新，承诺一些新的添加和修复。你可以通过等待自动更新，或者从菜单中选择**关于火狐**或者从手动下载新的安装程序来升级到火狐 36。

我亲自为最终用户和开发人员尝试了新的变化和调整，注意到一些非常有趣的变化主要针对开发人员。让我们来看看:

## 新选项屏幕

虽然主要是终端用户的功能，但新的选项屏幕现在对 Firefox 36 用户可用，提供了更光滑整洁的体验。如果你已经安装了火狐开发者版，你可能已经很熟悉它了。

![](img/9f5bf69d67811918152da7190840a30f.png)

## 用 WebIDE 调试 Android 版 Firefox

一段时间以来，Firefox 开发者工具提供了连接到 Firefox for Android 的功能，以便您可以调试您的移动网站。到目前为止，这是一个需要反复试验的复杂过程。然而，有了 Firefox 36，你再也不用处理 Android 调试桥工具了。相反，您可以通过 WebIDE 进行连接，WebIDE 在幕后负责 ADB。

![](img/72298c5a748287f7be74aaa6ae647895.png)

要做到这一点，你至少需要在桌面上运行 Firefox 36，在移动设备上运行 Firefox 35。

更多说明，请查看 Mozilla 开发者网络上的[文档。](https://developer.mozilla.org/en-US/docs/Tools/Remote_Debugging/Debugging_Firefox_for_Android_with_WebIDE)

## 反转调用树

Firefox 36 中的新功能也是一个漂亮的小功能，可以节省你很多时间:反转调用树的选项。

与大多数调用堆栈一样，分析器从根到叶显示调用树。基本上是从每个顶级函数到它调用的函数，再到它们调用的函数，依此类推:

`4 doSomething()`
`2 > logTheValue()`
`1 > getTheValue()`

通常，这是合乎逻辑的做法，因为这是构建堆栈的时间顺序。但是考虑到在很多情况下开发人员花时间深入调用树，这不是一个有效的解决方案，如此频繁地点击通过许多具有低"**自我成本**"的高级函数。

在新的 36 更新中，Firefox 在分析器中提供了一个新的复选框，名为“**反转调用树**”。它允许分析器:

*   列出所有自我成本大于零的函数:也就是说，取样时实际执行的函数
*   根据在该功能中采集的样本数量对列表进行排序
*   对于列表中的每个条目，向后显示顶级函数的调用堆栈。

这通常是一种更有效的方式来突出代码花费时间的地方。

## 调试评估源

在最近一次更新中，您还可以调试动态评估的 JavaScript 代码，或者作为传递给`eval()`的字符串，或者作为传递给函数构造函数的字符串。

一旦字符串被求值，它将作为一个单独的源出现在调试器中，并像任何其他源一样完全可调试。

![](img/9cfdb735f4a9017e46673188c403aebe.png)

更多文档可在 [Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/Tools/Debugger##Debug_eval_sources)上获得

## 检查器:标记视图中的更多粘贴选项

使用弹出菜单，您可以在特定节点上执行特定任务。Firefox 36 增加了各种新的粘贴选项:

*   粘贴内部 HTML
*   粘贴外部 HTML
*   粘贴/之前
*   粘贴/之后
*   粘贴/作为第一个孩子
*   粘贴/作为最后一个孩子

![](img/86894d67b9c9c3ea6df1305e1a161b1c.png)

## 添加了 HTTP/2 支持

基于 SPDY 的 HTTP/2 最近完成了，一旦浏览器制造商正确地集成它，它将保证更快的浏览速度。下面是关于这个过程的更多信息。

## RC4 密码不再被接受

RC4 现在被认为是不安全的，所有的用户界面指示器都会做出这样的反应。SSLv3 在 Firefox 34 中被默认禁用，但用户界面已经改变，以帮助用户更好地理解正在发生的事情。此外，在 TLS 的初始握手中不再提供 RC4。

## 附加兼容性

Firefox 36 中有各种可能影响插件兼容性的新变化。看看 Mozilla 插件博客中的[变化，看看你是否需要适应。](https://blog.mozilla.org/addons/2015/01/13/compatibility-for-firefox-36/)

其中，JavaScript 代码模块有两个变化:

*   **promise utils . resolveortimeout**已实现
*   **PromiseUtils.defer** (代替 **Promise.defer** )被实现

## 其他更改/修复:

*   重启后从脸书或谷歌意外注销已修复
*   CSS 渐变现在对预乘颜色有效
*   改进的新型 [ES6 发电机](https://wingolog.org/archives/2014/11/14/generators-in-firefox-now-twenty-two-times-faster)性能更佳
*   添加了对 ECMAScript 6 符号数据类型的支持
*   实现了 unicoderange CSS 描述符
*   实现了对象匹配和对象位置
*   实现了隔离 CSS 属性
*   网络:在 36 中已经删除了对 SPDY/3 的支持，但是仍然支持 SPDY/3.1。
*   添加的区域设置:乌兹别克语(乌兹别克语)

在 Mozilla Developer Network 上查看 Firefox 36 中[变化的文档。](https://developer.mozilla.org/en-US/Firefox/Releases/36)

## 火狐 36

正如你所看到的，版本 36 不是一个主要的版本，但是它为开发者提供了各种漂亮的变化。随着 Mozilla 与雅虎合作，成为 Firefox 的默认搜索引擎，我们将在浏览器大战中看到一些有趣的变化。尽管 Firefox 在过去几个月失去了市场份额，但它可能会卷土重来。

从现在开始，你对火狐有什么期望？新版本最好的特点是什么？

## 分享这篇文章