# 了解谷歌应用引擎(GAE) Java API 第 1 部分:景观概述

> 原文：<https://www.sitepoint.com/understanding-google-app-engine-gae-java-api-part-1-landscape-overview-2/>

对开发者来说，在网上用你最喜欢的语言编写应用程序是有趣且有回报的——对于小企业和初创企业来说，这可能是一个很好的价值主张。使用谷歌应用引擎(GAE)，用 Java 作为开发语言，这是很容易做到的。了解使用新兴 GAE 构建应用程序的环境，以及了解不断发展的产品的局限性是非常重要的。

## Java 沙盒运行时环境

Google 中的应用运行在 Java 6 JVM 上，SDK 支持 Java 5 或更高版本。应用程序在沙盒环境中运行，在沙盒环境中，它们被隔离以提供服务和安全性。在受限的沙盒环境中，应用程序可以执行代码，查询和写入数据存储，使用 mail 和 URLFetch 等应用引擎服务，并可以接受用户的请求，向他们发送回响应。但是，我们需要了解这种沙盒环境中存在某些限制:

*   应用程序可以**而不是**写入文件系统。允许从文件系统读取应用程序文件范围。

*   不允许打开套接字并直接访问主机。用户可以使用 URL 获取服务来访问 URL。

*   应用程序无法创建新线程。这种限制也适用于使用 java.util.Timer 等线程的 JRE 类。不过，应用程序可以对当前线程执行操作，比如获取转储堆栈。

*   所有应用程序类都允许反射，包括私有成员。然而，对于 JRE & SDK 类，只有公共成员可以使用反射来访问。

*   您设置的系统属性仅限于应用程序。

*   只有在[白名单](http://code.google.com/appengine/docs/java/jrewhitelist.html)中列出的类才能在所有 Java 库类中使用。

## 域

每个应用程序都有一个 app-id，该应用程序的 URL 是[app-id.appspot.com](http://app-id.appspot.com/)，你也可以拥有像【mail.app-id.appspot.com】T2 这样的子域名。部署的应用程序可以有版本，默认版本服务于请求。每个版本都有自己的 URL，因此您可以在服务器上测试您的应用程序，而不会影响默认的实时版本。例如，使用 7.latest.app-id.appspot.com 的[可以访问上述应用程序的版本 7](http://7.laetst.app-id.appspot.com/)

## Servlet 环境

收到的所有请求都由 web 应用程序中 web.xml 文件中定义的 servlets 处理。Java Servlet API 用于请求响应处理。对于后端请求，backend.xml 或 backend.yaml 用于调用符合请求标准的 servlets。

app engine 使用多个 web 服务器来处理对您的应用程序的请求，服务器数量的扩展由平台来处理。请求被聚集在多个 web 服务器上，每个 web 服务器一次处理一个请求。如果希望 web 服务器接收多个请求，可以通过向 appengine-web.xml 添加*<threadsafe>true</threadsafe>*元素来实现

app engine 将请求传递给应用程序进行处理，一旦收到响应，就会将其传递回客户端，但不支持流式传输。这里有一个重要的注意事项，如果不处理，这一直是各种开发人员和企业的痛苦:请求处理程序必须在 60 秒内完成处理，否则 JRE 将抛出 com . Google . app hosting . API . deadlineexceededexception，如果不处理该异常，将导致最终用户出现错误 500。app engine 是为许多几秒钟的小请求而不是更长的请求而构建的。这是一种范式的转变，需要为 GAE 设计应用程序。需要运行更长时间的请求需要使用任务队列或后端等替代方法来处理。

## 开发工具

应用引擎的 Eclipse 插件与应用引擎 SDK 和 GWT SDK 捆绑在一起。该插件提供了创建应用引擎项目、调试和一键式云部署的能力。该插件允许您上传应用程序文件和下载日志文件。它还有一个 Apache Ant 组件，用于自动化常见的开发和部署任务。第三方插件也可用于 NetBeans 和 IntelliJ。

您也可以使用“appcfg”命令行工具，它允许您通过 IDE 启用所有可能的任务，如上传应用程序文件、从服务器获取日志等。

SDK 附带的开发服务器使您能够通过模拟数据存储、服务、沙箱限制来本地测试您的应用程序，并且可以基于生成索引的查询来生成索引。

有了对开发和运行时环境的理解，我们就对平台有了一个大致的了解。本系列的下一部分将关注设置，并熟悉配置和部署结构。继续看！

在 Shutterstock 上的 Christian Lagerek 图片

## 分享这篇文章