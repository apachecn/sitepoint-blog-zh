# 用 PHP 生成 XUL

> 原文：<https://www.sitepoint.com/generating-xul-with-php/>

我最近遇到了几个有趣的 PHP / XUL 项目，用 PHP 进行 XUL 生成实验。

首先，有一个 PEAR 包正在经历审批过程([这里是](http://pear.php.net/pepr/pepr-overview.php) ): PEAR::XML_XUL。这为生成 XUL 提供了一个类似 DOM 的 PHP API。不知道有多少英里的方法通过；与 HTML 中常见的使用字符串(或模板)简单生成 XUL 相比，这会增加很多价值吗？

令人着迷的是 phpclasses 上的 HV WDDX 元数据(你需要自己的账户来查看代码)。这似乎是一个更有前途的方法。如果我理解正确的话，使用 WDDX(一种广泛用于序列化数据的 XML 格式)来定义以 XUL 形式出现的“小部件”，以及用于在客户端验证它们的 JavaScript。在提供的示例中，生成是用 XSLT 完成的。换句话说，您只需要修改原始的 WDDX 文档，向 XUL 表单添加更多的字段/验证。

很高兴看到 PHP 开发人员正在探索 XUL。还有其他项目吗？

另一个 XUL 新闻是 XULPlanet 现在有一个类似 PHP 手册的评论系统来评论一些在线参考和教程。

## 分享这篇文章