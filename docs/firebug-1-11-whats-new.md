# Firebug 1.11 的新特性

> 原文：<https://www.sitepoint.com/firebug-1-11-whats-new/>

Firebug 是最初的开发者控制台，尽管来自 Webkit 和 Opera 的竞争越来越激烈，它仍然是最好的。1.11 版本已经在上周发布，应该会自动出现在你的 Firefox 安装中。如果没有，点击主菜单中的**附加组件**，然后点击**检查更新**按钮。或者，去 getfirebug.com 的[下载。](https://getfirebug.com/)

新版本修复了 120 多个问题，实现了 40 项增强功能。它包括…

## 新的 CSS 查询选择器工具

这太棒了。切换到 CSS 选项卡，右键单击任意选择器并选择**获取匹配元素**。与选择器匹配的所有元素的列表显示在右侧的元素窗格中:

![Firebug CSS Query Selector](img/f1824e819f5eb28715f74e5773086d7f.png)

## 复制并粘贴 HTML

复制部分已经有一段时间了，但是 Firebug 现在允许你将 HTML、XML 或者 SVG 代码直接粘贴到 DOM 中。右键单击任何元素，选择**粘贴 HTML** 并选择插入代码的方式:

![Firebug paste HTML](img/6fd8c6e772c3ca92dafb1edffc4de123.png)

## 性能时序图

一旦你的页面下载完毕，在控制台中输入`performance.timing`,你会看到一个交互式图形:

![Firebug Performance Timing](img/9b727f670329564d75e2ef8abb82e38b.png)

其中:

*   **页面加载**是加载页面所需的总时间
*   **等待**是服务器响应开始之前所用的时间
*   **接收**下载时间
*   **DOM 处理**构建 DOM 所花费的时间
*   **DOMContentLoaded** 处理 DOMContentLoaded 事件所需的时间
*   **onLoad** 处理 onLoad 事件所需的时间

您可以将鼠标悬停在任何时间线上，了解更多详细信息。

## 风格日志记录

如果控制台中一直缺少消息，可以使用`%c`变量对输出应用不同的样式。例如:

```
console.log("%cred-text %cgreen-text", "color:red", "color:green");
```

生成:

![Firebug styled logging](img/c46034d7d6986ccc1fbddf3ea4b5053b.png)

## window.postMessage()事件

在窗口和框架之间发送消息被记录到控制台:

![Firebug window.postMessage() logging](img/2e975edc910642139a272068a40b5aeb.png)

这将显示源窗口/框架 URL、数据消息和目标窗口/框架对象。

## SPDY 协议支持

SPDY(发音为 speedy)是一种相当新的类似 HTTP 的网络协议，它使用压缩、多路复用和优先级来提高下载速度。Firebug 的网络面板现在显示 SPDY 何时被使用:

![Firebug SPDY in Net panel](img/2137f8343711c6e95d0b5b47b6a69074.png)

## 新的 include()命令

`include()`命令将一个 JavaScript 文件加载到当前页面，例如

```
include("https://mysite.com/mylib.js");
```

如果您经常这样做，您可以创建一个别名，然后使用该引用，例如

```
include("https://mysite.com/mylib.js", "mylib");
include("mylib");
```

Firebug 已经有将近七年的历史了，但是开发团队还在继续增加创新的新特性。对于许多人来说，这是使用 Firefox 作为开发浏览器的主要原因。

## 分享这篇文章