# Servlets 2.5:尖叫还是打哈欠？

> 原文：<https://www.sitepoint.com/servlets-25-scream-or-yawn/>

> 这就像一场梦，你试着回忆，但它已经消失，然后你试着尖叫，但它只是作为一个哈欠出现

裸女们，掐我一下

[JavaWorld](http://www.javaworld.com/) 昨天发表了一篇由 Java Servlets 权威人士[杰森·亨特](http://www.servlets.com/)撰写的[文章](http://www.javaworld.com/javaworld/jw-01-2006/jw-0102-servlet.html)。这篇文章提供了最近最终确定的 [Servlet 2.5 规范](http://jcp.org/aboutJava/communityprocess/maintenance/jsr154/index3.html) ( [JSR 154](http://www.jcp.org/en/jsr/detail?id=154) )中的变化。

乍一看，更新的规范可能看起来很可怕。servlet 构成了几乎所有服务器端 Java Web 开发技术的基础，该规范的最新更新迫使开发人员使用 Java 5.0(servlet 2.4 在 Java 1.3 或更高版本上工作)来利用新的语言特性，如**注释**。

注释允许你用标签来点缀你的 Java 类、方法和属性，这些标签被编译成最终的类文件。当加载这些类时，这些注释可以标识服务器应该如何使用这些类、方法和属性。例如，Servlet 2.5 规范支持的一种注释允许您用用户必须拥有的安全角色来标记 Servlet 类。

在他的文章中，Jason 揭示了规范中的变化并没有看起来那么剧烈。受支持的新注释主要用于 Enterprise JavaBean s(EJB ),非 EJB 服务器甚至不需要支持它们。事实上，除了 EJB 级别的功能之外，您可以用新注释做的任何事情，仍然可以用应用程序的`web.xml`文件中熟悉的指令来做。

注释*可能*影响非 EJB 开发者的地方是在**性能**的领域。任何兼容 Servlet 2.5 且*支持*注释的服务器都必须在启动时加载 Web 应用程序中的所有类，以便处理它们可能包含的注释。如果您不打算在您的应用程序中使用注释，您的`web.xml`文件可以通过在`<web-app>`标签中设置`full="true"`来禁用注释支持，这样您的应用程序的类将只在需要时由服务器加载。

```
<web-app 
    version="2.5" full="true">
  ...
</web-app>
```

Servlet 2.5 规范中剩下的重要变化是在将 Servlet 和过滤器映射到您的`web.xml`文件中的 URL 的过程中添加了一些小的便利特性，并帮助您的应用程序添加对 HTTP 协议的扩展。无论如何，没有什么值得失眠的。

所有这些的结果是，从实践的角度来看，您没有理由急于将 Java Web 应用程序迁移到 Servlet 2.5 规范。事实上，除非您计划使用新的 EJB 3.0 Enterprise JavaBean s 标准，它依赖于该规范的一些注释特性，否则 Servlet 2.5 为您做的只是限制您在支持 Java 5.0 的平台上部署。

## 分享这篇文章