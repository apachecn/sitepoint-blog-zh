# Zimbra AjaxTK:基于 AJAX 的 GUI 工具包

> 原文：<https://www.sitepoint.com/zimbra-ajaxtk-a-toolkit-for-ajax-powered-guis/>

虽然你可能不喜欢 Zimbra 这种完全替代电子邮件和协作服务器的产品(见[我的上一篇文章](https://www.sitepoint.com/zimbra-gmail-and-exchange-meet-open-source-java-and-ajax/)),但像这样的大型开源应用程序中总会藏着一些好东西。Zimbra AJAX Toolkit (AjaxTK)就是这样一个好东西。

Zimbra 仍处于早期阶段，完整的文档仍有待完成，但有一份白皮书从相当技术性的角度记录了 AjaxTK。

简而言之，AjaxTK 是一个完全用 JavaScript 编写的库，它可以让您编写带有按钮、菜单、标签、工具栏等组件的桌面风格的图形用户界面(GUI ),同时避免了跨浏览器 DHTML 兼容性问题的复杂性。

AjaxTK 中包含的组件库被称为 DHTML Widget Toolkit (DWT ),它包含了您从桌面应用程序开发背景中所能想到的所有基本 GUI 组件。所包含的组件提供了适当的事件处理、拖放操作、工具提示等等，并且该框架的设计使得您可以编写自己的自定义组件来继承这些功能。

至于框架的实际 AJAX 功能，服务器通信被巧妙地抽象成一组网络类，以及一个完整的 SOAP Web 服务客户端库。同样，像 XMLHttpRequest 跨浏览器实现差异这样的细节是完全隐藏的，所以您可以专注于您想要完成的事情。

一个特别令人惊喜的发现是，这个库使用 W3C XForms 标准的部分(但可用)实现——也完全用 JavaScript 编写——作为用户界面小部件和发送到服务器和从服务器接收的数据之间的中介。我在过去的几期 Tech Times (#83，#53)中写了很多关于 XForms 的文章，作为这个基本上还没有实现的标准的粉丝，我很高兴看到它终于可以在 airplay 上使用了。

尽管 AjaxTK 绝不是唯一流行的 AJAX 工具包，但它看起来确实很强大——特别是对于有桌面应用程序开发背景的有经验的开发人员来说。有了一个令人印象深刻的真实世界应用程序(Zimbra 客户端界面)来推动其向实用方向发展，它的未来看起来确实很光明。

## 分享这篇文章