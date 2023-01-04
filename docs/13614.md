# OSCON 2006:用 PHP5 致富

> 原文：<https://www.sitepoint.com/oscon-2006-gettting-rich-with-php5/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

PHP 的创始人拉斯马斯·勒德尔夫做了一个关于使用 PHP5 构建 PHP 富 web 应用程序的令人惊讶的演讲，因为整整一半的演讲都集中在底层 PHP 优化技术上。幻灯片可以在网上找到。

以一个简单的 PHP 脚本为例，他开始对其进行优化，以满足每秒 1700 个请求的请求吞吐量需求(通过 [http_load](http://www.acme.com/software/http_load/) 测量)。他在这个过程中接触到的技术包括使用 [KCachegrind](http://kcachegrind.sourceforge.net/) 来分析运行脚本的 Apache 进程，以便确定 PHP 的哪些部分实际上导致了脚本的变慢。

他发现并修复的一些瓶颈示例包括默认的 PostgreSQL 设置，该设置使用安全 SSL 连接(开销很大)和 PostgreSQL 数据库连接，这比连接 MySQL 要慢得多。他还展示了如何通过缓存 PHP 中间代码并使用 [Alternative PHP Cache](http://pecl.php.net/package/APC) 的各种功能自动优化代码来进一步提高性能，这个项目是 Lerdorf 目前正在积极进行的。

然后，Lerdorf 花了一些时间研究 PHP 5.0 和 5.1 的新特性，这些新特性使构建 Web 2.0 应用程序变得更加容易，包括基于 libxml 的 xml 处理函数、[、SimpleXML](http://www.php.net/simplexml)和面向对象的 SOAP 库，但这些都是大多数严肃的 PHP 开发人员已经熟悉的东西。

他的一些演示展示了 PHP 解析各种提要并使用 [YUI 库](http://developer.yahoo.com/yui/)生成有趣的用户界面，他还用一些谷歌地图和雅虎结束了这次演讲！由简单的 PHP 脚本支持的地图混搭。这些演示中有趣的是那些直接称[为雅虎的演示。Maps API](http://developer.yahoo.com/maps/) 直接在服务器端获取特定位置的地图块(而不是在客户端加载完整的 DHTML 用户界面)。

## 分享这篇文章