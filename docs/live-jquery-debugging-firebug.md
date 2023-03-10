# 安装和使用 Firebug 的简易指南

> 原文：<https://www.sitepoint.com/live-jquery-debugging-firebug/>

![firebug-and-jquery](img/f69e6c41f5a0023c5f078fd88de8c5c2.png "firebug-and-jquery")

嗨，伙计们，我想我应该写一篇关于如何使用 Firebug 和 FireQuery 来查看和调试 jQuery 代码(与 HTML/CSS 等同时进行)的快速帖子，并在网页中注入实时代码来测试 jQuery 效果。在这篇文章中，我将写下:

1.  如何安装 Firebug
2.  关于 FireQuery——jQuery 的 Firebug 扩展
3.  如何配合 FireQuery 使用 Firebug 将 jQuery 代码注入网页？

## 什么是萤火虫？

“Firebug 与 Firefox 集成在一起，让您在浏览网页时，随手可得大量 web 开发工具。您可以在任何网页中实时编辑、调试和监控 CSS、HTML 和 JavaScript。”[阅读更多…](http://getfirebug.com/whatisfirebug)

主要特点:

*   HTML、CSS、JavaScript 和 jQuery 的实时编辑
*   探索 DOM 元素和属性
*   JavaScript 日志记录
*   监控网络活动

## 如何设置 Firebug？

1.下载并安装[火狐网络浏览器](http://www.mozilla.com/en-US/firefox/)(当前 3.6 版)

2.[下载 Firebug](https://getfirebug.com/downloads/) (目前 1.6 版兼容 Firefox v3.6 版)

![install](img/7fe96802038915626a02b04bb5553a43.png "install")

Firebug 现在应该作为你的 Firefox 插件之一出现了。
![addons](img/e0f110324279be1e74456980587639eb.png "addons")
注意:如果你目前使用的是 Firefox，它会要求重启浏览器来完成安装。

3.现在，您应该会在浏览器的右下角看到一个小小的 bug 图标。这是一个 firebug 图标，你可以点击它来激活 firebug。

![firebugwindow1](img/0acb01809eaafaee905acddaed85dd92.png "firebugwindow1")

如果脚本面板被禁用，请将其启用。

![firebugwindow2](img/1de1f23d22db2d2e0afc6cdda0342ab8.png "firebugwindow2")

选择要检查的 jQuery 脚本。

![firebugwindow3](img/80945415d7c977f7ea929177553e1df7.png "firebugwindow3")

jQuery 代码现在应该是可见的，可以进行检查和调试了。

![firebugwindow4](img/851ccdb5bdb5beca35d69ce345b62c5b.png "firebugwindow4")

## FireQuery——jQuery 的 Firebug 扩展

![firequery](img/ec2660bf26c7471455ffd5113ed57912.png "firequery")

有了 FireQuery，你可以将你的 jQuery 实时注入到任何网页中(听起来很棒，不是吗！).确保在安装 FireQuery 时，它能与您的 Firebug 版本和浏览器版本一起工作(在下面的测试页面链接上试试)。它仍处于“生命”的早期阶段，所以要知道它可能并不完美——但有 234，000 次下载，谁会否认这一点呢？！

[下载页面](http://firequery.binaryage.com/)
[测试页面](http://firequery.binaryage.com/test/)

## 如何使用 Firebug 和 FireQuery 将 jQuery 代码注入网页

1.  加载 firefox
2.  加载要添加 jQuery 代码的网页
3.  单击 firebug 图标进行加载
4.  点按 jQuery(重要的是，如果网页还没有运行 jQuery，请点按 jQuery 按钮)
5.  使用命令提示符在网页 DOM 元素上运行一些 jQuery 代码。

**工作示例:点击谷歌标志时显示消息** 

1.前往[http://google.com](http://google.com)

2.加载 Firebug

3.单击 jQuery(单击“jQuerify”按钮)。

![jquery-google2](img/2b214f15a62bcaabfdbe630db665d969.png "jquery-google2")

4.我们可以检查 google logo 元素来检索它的 Id“HP logo”(不知道为什么它不仅仅叫做 logo…)

![inspect-elemnt-logo](img/28345329853d1b994a413e97e4d68884.png "inspect-elemnt-logo")

5.在 jQuery 命令窗口中键入以下代码(在屏幕截图的右侧，但可以在主代码窗口下显示为一个提示行(显示上面的 html):

```
$('#hplogo').bind('click', function(){
    alert("Hello, I'm Google from jQuery4u!");
});
```

现在点击谷歌标志应该显示您的信息！

![firequery-alertmsg](img/4728d49718f4bfdc4659ed6c21b41158.png "firequery-alertmsg")

## 分享这篇文章