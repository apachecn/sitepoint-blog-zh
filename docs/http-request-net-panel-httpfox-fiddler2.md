# 如何使用 Net Panel，HTTP Fox & Fiddler2 分析 HTTP 请求

> 原文：<https://www.sitepoint.com/http-request-net-panel-httpfox-fiddler2/>

对于任何 web 开发人员来说，理解 HTTP 请求并能够分析加载网页时加载了哪些文件是至关重要的。例如，说一个网页加载非常慢，你想知道为什么！是的，我们都去过那里！幸运的是，有一些工具可以帮助我们检查 HTTP 请求，并监控在页面执行期间发送了哪些 HTTP 请求。

这篇文章包括:

1.  **网络面板**–概述及其使用方法。
2.  **HTTP Fox**——概述及使用方法。
3.  **fiddler 2**–概述及使用方法。

我将简要介绍每个工具的特性，它们的优点，以及如何使用它们来帮助您进行 web 诊断。

## # 1–Firebug 网板

![firebug-net-panel](img/88488c8b3e576c102856c8ea4e9da76f.png "firebug-net-panel")

网络面板是 [Firebug](http://www.jquery4u.com/utilities/live-jquery-debugging-firebug/) 安装的一部分，位于“网络”标签下。Net Panel 的主要目的是监视由网页发起的 HTTP 流量，并简单地向用户呈现所有收集和计算的信息。它的内容由一个条目列表组成，其中每个条目代表页面进行的一次请求/响应往返。


### 它有什么好的

*   实时查看 JS 文件，CSS 文件，Flash，HTML 和图像，因为他们正在加载。
*   瀑布图时间线当文件被加载时，它显示它们

### 如何使用它

例如，我将检查加载到我的 web 页面上的 jQuery 文件。首先，加载 Firebug 并打开网络面板。然后重新加载你的网页。文件在加载时应该会出现在列表中。

![net-panel](img/3b421216f8a842eb30347ce006d3a8ea.png "net-panel")

网络面板有一个很棒的瀑布风格的图表，显示你的加载顺序和加载时间。

![net-panel-waterfall-chart](img/69361dc3129bc0d24e7b07b708cbf9cd.png "net-panel-waterfall-chart")

**请求参数**(对于查看您在请求中发送的参数和值非常有用)。

![net-panel-params](img/e4297974f1208f6b1e3c81253f91a187.png "net-panel-params")

**请求报头信息**

![net-panel-headers](img/a3d31e3f33608894f72ea7e27e32ddd5.png "net-panel-headers")

**请求响应**(在示例中是 JavaScript 文件代码，但也可以是 JSON、HTML、数据或请求返回的任何内容)。

![net-panel-response](img/18d6bccfefaa6db3b85afafa917c354c.png "net-panel-response")

**请求缓存信息**

![net-panel-cache](img/2dc01322180def3c97f73da4b48829dd.png "net-panel-cache")

[获取 Firebug(+网板)](https://addons.mozilla.org/en-US/firefox/addon/firebug/)

## # 2–HTTP Fox

![http-fox](img/67a06e509687d43c489e58895d7a2bd5.png "http-fox")

HTTP Fox 是一个 Firefox 插件，可以作为 Firebug 的 NET Panel 的替代。我更喜欢 HTTP 而不是 NET panel，因为它的速度和易用性。HttpFox 监视和分析浏览器和 web 服务器之间所有传入和传出的 HTTP 流量。

### 它有什么好的

*   全屏分离选项，获得最大细节
*   实时搜索结果出现时进行搜索过滤

### 如何使用它

它本质上和上面的 NET 面板是一样的过程，但是我更喜欢 HTTP Fox，因为它的速度和易用性！这肯定是我最喜欢的，我喜欢的使用方式是把它从主窗口中分离出来，放在自动滚动的第二个屏幕旁边。您可以使用浏览器右下角的按钮将其分离。

![httpfox-open-window](img/ac6474453c19d50eb9744b673a523963.png "httpfox-open-window")

当页面加载时，文件被列出。

![httpfox-list](img/ce3f6079c22f0ba3ce1e8b8f4bc30b7d.png "httpfox-list")

我绝对推荐使用过滤器来查找文件。在下面的例子中，我找到了所有加载的 PNG 图像。

![httpfox-png-search](img/15897684e5e48f7520a9bbec64d06e77.png "httpfox-png-search")

**请求 cookie**

![httpfox-cookies](img/a12da1d5e48d62c68eb00d0463d5aa18.png "httpfox-cookies")

**请求发布数据**

![httpfox-post-data](img/397e33ede89cfdcf73f9b9663b947c5e.png "httpfox-post-data")

有时，如果一个文件没有正确加载，它会被高亮显示为红色——非常适合查找丢失的文件/错误的请求！

![httpfox-error-example](img/26f4284025b8b3312acf27d20e35b72f.png "httpfox-error-example")

[获取 HTTP Fox](https://addons.mozilla.org/en-US/firefox/addon/httpfox/)

## # 3–小提琴手 2

![fiddler2](img/cd56113b6f618f17a695535464a86bcf.png "fiddler2")

我把 Fiddler 2 包括在内，因为每个 web 开发人员都应该进行多浏览器测试，Fiddler 2 可以在任何浏览器上工作，尤其是 IE。请注意，Firebug NET Panel 和 HTTP Fox Mozilla 附加组件只适用于 FireFox，所以瞎搞。

### 它有什么好的

*   Fiddler2 可以与 Internet Explorer 和其他浏览器一起工作
*   小提琴 2 是免费的

### 有什么不好的

*   Fiddler2 有广泛的选项/设置(你可能只会使用其中的 5%)

### 如何使用它

将进程过滤器按钮拖到您想要跟踪的窗口上，如 IE。

![fiddler2-process-filter](img/fdf742e7ce337fd82720b23bf850fab5.png "fiddler2-process-filter")

选择您想要添加的任何过滤器:

*   勾选使用过滤器(在过滤器选项卡上)
*   选择“仅显示下列主机”
*   输入您希望显示活动的域

![fiddler2-search-js-files-google](img/1c8fdd96d1de46a64ee9025ae9150a82.png "fiddler2-search-js-files-google")

重新加载页面以加载页面请求。您可以在 web sessions 窗口中双击请求以查看其详细信息，或者单击 Inspectors 以获取加载的 javascript、cookies 集、缓存和标题信息。

你必须刷新过滤器才能工作。有时如果它不能正常工作，关闭并重新加载 fiddler，并使用启动 ie 按钮。

![fiddler2-search-js-files](img/ad3616356ba6356d3081f0e3632c7716.png "fiddler2-search-js-files")

[拿小提琴 2](http://www.fiddler2.com/Fiddler2/)

## # 4–Wireshark

Wireshark 是一个强大的网络分析工具，我只建议在上面的其他选项对你不起作用时使用它。

写这篇文章是为了帮助扩展 web 开发的知识。当你的网页加载时，知道(并理解)幕后发生的事情是值得的。如果您有任何意见或建议，我们将一如既往地欢迎您！

## 分享这篇文章