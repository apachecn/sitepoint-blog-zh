# 你安装的下一个热门 PHP 应用可能是 IBM 的

> 原文：<https://www.sitepoint.com/the-next-hot-php-app-you-install-might-be-ibms/>

如果你到目前为止还没有关注 QEDWiki，请查看这个演示。服务器端是使用 [Zend 的框架](http://framework.zend.com)用 PHP 编写的——更多细节[在这里](http://devzone.zend.com/node/view/id/678)，最近的信息似乎是[在这里](http://ajaxian.com/archives/qedwiki-and-atf)(它还没有发布——不久的将来)

应用维基和情景应用的想法有很多潜在的 IMO。举例来说，如果你考虑一下 [Dokuwiki](http://wiki.splitbrain.org) (这里经常会提到)的话，有趣的是，许多用户已经使用它作为 CMS 的替代品——由于有了像[名称空间](http://wiki.splitbrain.org/wiki:namespaces)这样的东西，它比大多数 wiki 提供了更多的结构，但比一般的 CMS 灵活得多。

更有趣的是检查一些[行为](http://wiki.splitbrain.org/wiki:plugins#behavior)语法插件，如 [sql 插件](http://wiki.splitbrain.org/plugin:sql)；

```
===== A wiki page with a query =====

Here is the //products// list:

<sql db="mysql://user:pwd@localhost/shop"> select * from products; </sql>

```

或者是 AJAX 聊天插件，它只需要你将`~~CHAT~~`添加到任何页面。

这里有潜力。Dokuwiki 有设计上的限制，比如很难应用页面的访问控制部分，而且我怀疑如果不进行大量的重写就很难解决这个问题:那个 SQL 插件实际上只适用于私有 wiki。

但是如果有正确的基本设计，比如“DOM”wiki 页面，你可以走很长的路。看起来 IBM 已经做到了。有看点。

## 分享这篇文章