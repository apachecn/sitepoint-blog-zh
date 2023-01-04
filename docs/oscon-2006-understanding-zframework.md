# OSCON 2006:理解 ZFramework

> 原文：<https://www.sitepoint.com/oscon-2006-understanding-zframework/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

John Coggeshall 在 PHP 世界中是一个受人尊敬的名字，他是 Zend 的技术顾问，并提供了一个快速的 Zend 框架介绍，因为它目前存在于预发布版本中。

Zend Framework(简称 ZFramework)是一个旨在使用 PHP5 的面向对象特性，以干净、结构化的方式构建开发 web 应用程序的标准框架的项目。尽管它仍在开发中，但它已经有了相对稳定的部分，这些部分本身已经非常有用了。例如，框架中支持 MVC 风格的应用程序开发的部分可以在今天使用。

web 应用程序开发的模型-视图-控制器(MVC)方法将应用程序分成三个部分:**模型**，它提供了一组表示应用程序核心数据的对象(通常，这些对象对应于数据库记录)；**视图**，它提供了 HTML 模板，用于将应用程序的内容和状态传达给用户；以及**控制器**，它确定了响应从浏览器接收到的每个请求要采取的动作。

Coggeshall 用代码演示了 Zend 框架用来实现 MVC 风格开发的简单机制。该系统完全不需要配置，只需要开发者简单地编写一组 PHP 类，其名称形式为`someController`(例如`articleController`)，每个类都应该有一系列名称形式为`someAction`(例如`createAction`、`updateAction`、`deleteAction`)的方法，这些方法由框架映射到指定控制器、动作和一些参数(例如`/article/delete/id/123`)的简单 URL。

在处理用户输入时，框架提供了`Zend_InputFilter`类，通过指定您期望接收到的值的类型，该类允许您安全地访问从浏览器接收到的值。例如，如果您想要一个数值，您可以要求输入过滤器以该格式向您提供数据。如果浏览器碰巧发送了数值以外的内容，输入过滤器会防止这成为安全问题。

Coggeshall 还展示了内置于 Zend 框架中的搜索引擎所提供的一些功能，该框架基于 Java 世界中流行的搜索引擎 [Apache Lucene](http://lucene.apache.org/) 。

有一些事情还没有构建到框架中，在那里你必须带来你自己的解决方案。例如，对于视图层，您可以自由地使用您喜欢的任何模板系统。Coggeshall 的演示使用了 [Smarty 模板引擎](http://smarty.php.net/)，尽管更简单的应用程序可以使用嵌入在 HTML 中的纯 PHP 来输出动态值。

框架中还没有一个用户认证系统，但是 Coggeshall 有效地演示了如何快速创建一个专门的控制器类来执行应用程序的认证任务。

总的来说，Coggeshall 的演示信息非常密集，但对于外行人来说可能代码太多了。对于 Zend 框架更温和的介绍，我推荐 Chris Shiflett 在 php|architect 上的介绍文章。

## 分享这篇文章