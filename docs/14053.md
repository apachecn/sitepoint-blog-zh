# 漏洞影响 PHP XML-RPC 库

> 原文：<https://www.sitepoint.com/vulnerability-affects-php-xml-rpc-library/>

刚刚把我们即将出版的书 [No 废话 XML Web Development With PHP](https://www.sitepoint.com/books/xml1/) 送去印刷，我今天松了一口气，因为一个广为宣传的[安全漏洞](http://www.gulftech.org/?node=research&article_id=00088-07022005)已经在我们*几乎在书中使用的*库中被发现了。

PHP 有一个[标准库](http://www.php.net/xmlrpc)，用于使用 [XML-RPC](http://www.xml-rpc.com/) 通信协议构建和消费 [Web 服务](https://www.sitepoint.com/article/web-services-demystified)。该库是本书中的示例所使用的库，不受报告的漏洞的影响。

因为在默认的 PHP 安装中没有启用这个标准库，所以许多需要 XML-RPC 功能的开源项目选择使用完全用 PHP 编写的替代库，它可以在大多数 PHP 配置上运行。这样的选择包括 [PEAR XML-RPC 模块](http://pear.php.net/package/XML_RPC)和[PHP 项目的 XML-RPC](http://phpxmlrpc.sourceforge.net/)。这两个库都受到该漏洞的影响。

这些库的更新版本现在可供下载，受影响的开源项目正在迅速发布 [咨询](http://secunia.com/advisories/15916/)和[更新版本](http://secunia.com/advisories/15917/)来解决问题。

## 分享这篇文章