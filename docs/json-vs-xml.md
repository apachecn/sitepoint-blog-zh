# 为什么使用 JSON 而不是 XML？

> 原文：<https://www.sitepoint.com/json-vs-xml/>

在服务器和 web 应用程序之间传输数据时，有时您可能不确定应该选择什么格式。以下是您可能选择使用 JSON 而不是 XML 的一些原因，以及您可能选择 XML 而不是 JSON 的一些原因。

## 背景资料

### JSON 是什么？

JavaScript Object Notation (JSON)是一个轻量级的基于文本的开放标准，为人类可读的数据交换而设计。它源自 JavaScript 编程语言，用于表示简单的数据结构和关联数组，称为对象。尽管它与 JavaScript 有关系，但它是独立于语言的，大多数编程语言都有解析器。
延伸阅读:[http://en.wikipedia.org/wiki/Json](http://en.wikipedia.org/wiki/Json)

### 什么是 XML？

可扩展标记语言(XML)是一组以机器可读形式编码文档的规则。XML 的设计目标强调互联网上的简单性、通用性和可用性。
延伸阅读:[http://en.wikipedia.org/wiki/XML](http://en.wikipedia.org/wiki/XML)

## 选择 JSON 而不是 XML 的理由

1.  JSON 比 XML 需要更少的标签——XML 项目必须包装在开始和结束标签中，而 JSON 只需给标签命名一次
2.  因为 JSON 是独立于传输的，所以您可以绕过 XMLHttpRequest 对象来获取数据。
3.  JavaScript 不仅仅是数据——您还可以将方法和各种好东西转换成 JSON 格式。
4.  JSON 更擅长帮助您的 JavaScript 基于对象及其值(或方法)做出程序性决策。
5.  您可以从任何地方获得 JSON 数据，而不仅仅是您自己的领域。不再有代理服务器废话。
6.  JSON 比 XML 更容易阅读——这显然是个人偏好

## 选择 XML 而不是 JSON 的理由

1.  轻松获取 XML 并应用 XSLT 制作 XHTML。
2.  JSON 可以在返回客户机的途中放在 XML 中——这是两者的好处！它被称为 XJAX(代表 X 域 JavaScript 和 XML)。
3.  简单地说，AJAX 包含 XML 而不是 JSON。

还是决定不了？抛硬币或者使用 [JSON/XML 转换器](https://codebeautify.org/xmltojson)。

## 分享这篇文章