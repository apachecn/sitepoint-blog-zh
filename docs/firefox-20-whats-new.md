# 火狐 20 的新功能

> 原文：<https://www.sitepoint.com/firefox-20-whats-new/>

版本号可能毫无意义，但 Firefox 20 标志着 Mozilla 旗舰浏览器的一个里程碑。新版本出现在 4 月 2 日，正好是版本 19 的六周之后[。如果没有，打开关于火狐的帮助>或者从 getfirefox.com](https://www.sitepoint.com/firefox-19-whats-new/)[下载浏览器](http://getfirefox.com/)。

Firefox 20 拥有数量惊人的新特性——让我们来看看最好的…

## 开发人员工具更新

在过去的几个版本中，Firefox 获得了数量惊人的工具。开发者菜单已经重新组织，以便更有效地排序，新的**切换工具**选项提供了对 Web 控制台的快速访问。控制台也可以在它自己的面板中查看，也可以停靠。

一个新的 JavaScript 分析器已经承诺在版本 20 中实现，但是这个特性已经被推迟到以后的版本中。

## 新下载管理器

如果我没记错的话，这是下载管理器自 2002 年出现在 Phoenix 以来的第一次重大更新。专用面板已被新工具栏图标访问的下拉列表所取代，该图标突出显示剩余时间:

![Firefox download manager](img/a4bce450027dc79af9477bee1b10d5df.png)

这是一个很好的接触。以前的下载仍然记录在书签和历史面板中。

## getUserMedia 支持

Firefox 现在提供了 getUserMedia HTML5 API ,该 API 已经在 Chrome 和 Opera 中发布了几个月。如果用户允许，您可以从麦克风或摄像机采集音频和视频。 [Opera 的照相亭](http://shinydemos.com/photo-booth/)提供了一个很好的技术展示。

## 画布混合模式

当你在画布元素上绘制时，任何现有的像素通常都会被替换。然而，新的`globalCompositeOperation`属性允许类似 Photoshop 的叠加效果，包括乘法、加网、变暗、变亮、颜色减淡、颜色加深和差异。参考[这篇 Mozilla 文章](https://hacks.mozilla.org/2012/12/firefox-development-highlights-per-window-private-browsing-canvas-globalcompositeoperation-new-values/)了解更多信息。

## 音频和视频播放速率支持

你有没有想过以双倍速度看一部乏味的电影？`playbackRate`属性只允许这样做(为内联代码道歉):

```
<video id="v" src="video.webm" controls autoplay></video>
<a href="#" onclick="document.getElementById("v").playbackRate=2;return false;">fast foward</a>
```

[观看演示电影……](http://jsbin.com/videoPlaybackRate/4)

## 改进的隐私浏览

隐私浏览阻止火狐更新你的历史，缓存页面，保存密码和存储 cookies。

虽然它已经可用了一段时间，但从菜单中单击“开始私人浏览”将关闭您当前的窗口并开始一个新的会话。从版本 20 开始，Firefox 允许你一次打开任意数量的私有和非私有窗口。

## 更好的插件稳定性

如果你的插件引起混乱，浏览器的稳定性在很大程度上是无关紧要的。幸运的是，新版 Firefox 可以检测到挂起的插件，并允许你在不重启浏览器的情况下关闭它们。我怀疑这将结束插件问题，但这是朝着正确方向迈出的一步。

## 想要更多吗？

Firefox 开发人员度过了忙碌的六周…

*   CSS 和 JavaScript 现在以比图像更高的优先级加载，以缩短响应时间。
*   ECMAScript 6 的各种新增功能
*   性能改进
*   一打[安全补丁](https://www.mozilla.org/security/known-vulnerabilities/firefox.html)。

我不确定 Mozilla 能保持这样的发展速度多久，但其他供应商应该注意一下*(说真的谷歌，你能在 Chrome 26 中只管理一个拼写检查器吗？！)*

## 分享这篇文章