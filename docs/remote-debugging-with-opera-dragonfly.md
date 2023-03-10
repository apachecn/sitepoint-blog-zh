# 使用 Opera 蜻蜓进行远程调试

> 原文：<https://www.sitepoint.com/remote-debugging-with-opera-dragonfly/>

[蜻蜓](http://www.opera.com/products/dragonfly/)是 Opera 的 web 开发调试工具，最初随 Opera 9.5 发布。凯文在第 194 期对此做了简要描述。它仍在发展中，但它显示了很大的前景。我设法在墨尔本 Web 标准组织的一次活动中见到了来自 Opera 的 Charles McCathieNevile 和 Andreas Bovens，他们展示了一个很酷的蜻蜓特性:远程调试。

你现在就可以测试它。如果你安装了 Opera 9.6，你将得到蜻蜓 0.7 alpha 2。是的，它仍然是阿尔法软件，所以它有点古怪。自从 Kevin 写了关于它的文章后，已经添加了活 CSS 编辑，但它仍然缺少 DOM 编辑和 HTTP 请求日志记录。

使用远程调试非常容易。在我的测试运行中，我让蜻蜓在 Mac OS X 上运行，让 Opera 浏览器客户端在 Windows Vista 虚拟机上运行。首先，在您将用作调试器的客户端上，您需要在蜻蜓设置对话框中启用远程调试(单击右下角的工具图标)，这将启用调试代理。

![Dragonfly settings dialog](img/0fa2504637fd1b5973357cdf3d5f3d45.png)

启用后，您将看到以下确认信息:

![Remote debugging enabled confirmation](img/c4eb88b375990358809ebfc5c71a0a82.png)

咳咳。我说过它仍然是阿尔法软件。

不管上面的确认中说了什么，在您希望调试的客户机上(如果您愿意，也可以是被调试者)，您访问 url [opera:debug](opera:debug) ，它将呈现以下形式:

![Remote debugging connect form](img/3065fb55c4177a7a75bc9329325032ed.png)

输入运行蜻蜓的电脑的 IP 地址，然后单击连接。希望您会收到以下确认信息:

![Connection confirmation dialog](img/470fd7c0545c146f80ae6bdc8f94b257.png)

一旦连接，您就可以使用蜻蜓，就像在同一个浏览器实例中调试页面一样。如果编辑 CSS 属性，远程客户端上的更改会立即可见。如果您在调试器中启用了“通过单击查找元素”功能，则在远程客户端上，您可以移动鼠标指针并看到元素高亮框。在客户端中选择元素，它的 HTML 会在蜻蜓 DOM 选项卡中突出显示。

JavaScript 调试器也像您所期望的那样工作；在蜻蜓添加一个断点，并在远程客户端重新加载页面；当您在调试器中单步执行代码时，加载将会暂停。下面是蜻蜓的 JavaScript 调试器，我正在逐步调试远程浏览器客户端上运行的一些 JavaScript:

[![Dragonfly JavaScript debugger](img/6d94a6c6688f7175dd7de5e45fcc33b9.png)](https://i2.sitepoint.com/g/nl/tt/op-dragonfly-jsdebug.jpg)

在他们的演示中，Charles 和 Andreas 提供了两个使用案例。第一个涉及移动设备——由于屏幕大小和输入限制，在移动设备上调试 CSS 和 JavaScript 很痛苦。您可以在工作站上运行 dragonfly，并远程调试您的移动设备。蜻蜓已经在用于 Windows Mobile 设备的 Opera Mobile 9.51 beta 1 中推出。

第二个用例涉及测试不同的浏览器版本；不同操作系统上的版本或同一操作系统上的不同版本。您可以使用远程调试来防止浏览器崩溃——如果客户端浏览器崩溃，您仍然可以在调试器中获得有关浏览器最后状态的信息。

很明显，目前用例是有限的，并且只对 Opera 中的调试有帮助。然而，Opera 有更大的计划；它试图为 web 开发调试工具创建一个开放的平台。

将调试器绑定到浏览器客户端的粘合剂是[作用域](http://dev.opera.com/articles/view/opera-dragonfly-architecture/#componentscope)。范围模块充当浏览器和调试器之间的接口；它公开了浏览器当前显示的信息。两者之间的通信通过代理服务器完成，并使用范围协议；使用 XML 或 JSON 的消息格式。Opera 计划在不久的将来发布 Scope 协议。

这意味着任何人都可以为其他浏览器创建作用域客户端，并创建作用域兼容的调试客户端；无论是作为浏览器插件，独立的客户端，或内置到您最喜爱的 IDE。想象一下。从您最喜欢的 web 开发工具调试所有浏览器中的 CSS、JavaScript 和 DOM 的能力。

虽然目前蜻蜓和 Scope 有限，但它让我们得以一瞥 web 开发工具的未来。

## 分享这篇文章