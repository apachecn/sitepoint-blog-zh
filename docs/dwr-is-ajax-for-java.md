# DWR 是 Java 的 AJAX

> 原文：<https://www.sitepoint.com/dwr-is-ajax-for-java/>

Java 和 JavaScript。如此相似的名字，但他们所指的技术却是天壤之别…或者至少到目前为止是这样。

**远程脚本**的技术使运行在网络浏览器中的 JavaScript 代码能够在网络服务器上做事情并显示结果，而无需加载新的网页。当 Web 服务器发送的结果是 XML 格式时，这种技术被称为 **AJAX** 。

新发布的 Direct Web Remoting (DWR)库极大地简化了 Java Web 应用程序的 AJAX。

该库消除了 Java Web 应用程序远程脚本编写的所有繁重工作，但它确实要求您知道自己在做什么。JavaScript 或 Java 的初学者可能会有一段艰难的时间。

您必须向服务器上的 Web 应用程序添加一个 JAR 文件，设置一个特殊的 servlet 来处理脚本请求，然后配置一个或多个您希望从 JavaScript 获得的类。这个配置是用一个漂亮、干净的 XML 文件完成的，在这个文件中，您可以指定每个类是如何实例化的(每个请求、每个会话等等)。).

在客户端，您必须包括通用的 DWR JavaScript 库以及为您的特定配置生成的 JavaScript 代码。

虽然一开始设置看起来有点扭曲，但这是非常值得的。然后，您的 JavaScript 代码可以透明地调用服务器端函数，而不考虑数据类型或事件处理…这一切都正常工作。

对于高级开发人员来说，DWR 在集成 Web 应用程序框架方面做得很好，可以完全扩展可插拔数据类型和对象实例化方法，甚至内置了对 [Spring 框架](https://www.springframework.org/)的支持。

虽然 DWR 肯定是最新的，但我确信它不是唯一的 Java Web 应用远程脚本库。你用过其他的吗？如果是这样，我想听听你的经历。

## 分享这篇文章