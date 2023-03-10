# CouchDb:面向文档的持久性

> 原文：<https://www.sitepoint.com/couchdb-document-oriented-persistence/>

如果你正在寻找一些“有趣”的东西来摆弄， [Damien Katz 的](http://damienkatz.net/) [CouchDb](http://couchdb.com/) 项目正在进行*原型*，在 [Erlang](http://en.wikipedia.org/wiki/Erlang_programming_language) 中实现的服务器(在某些地方是一个[热门话题](http://programming.reddit.com/info/gfy2/comments)，以及一个 PHP 的演示客户端应用程序(一个简单的论坛)。

在 Windows 上启动 CouchDb 服务器是一件轻而易举的事情——请阅读自述文件。就 PHP 而言，您需要新的 [http 扩展](http://pecl.php.net/package/pecl_http/)，这在 Win32 上通过获取最新的 PHP 5 版本(5.1.6)和相应的 PECL 模块集合是最容易做到的。或者最近的 [XAMPP](http://www.apachefriends.org/en/xampp.html) (显然)包扩展。

CouchDb 和 PHP 之间的接口是[REST](http://en.wikipedia.org/wiki/Representational_State_Transfer)–XML+HTTP——你也可以将浏览器直接指向 CouchDb 服务器(默认——localhost:8080 ),并在 [CouchDb wiki](http://www.couchdbwiki.com/index.php?title=HTTP_REST_API) 的帮助下四处走动。

什么是 CouchDb？鉴于关系数据库等，CouchDb 为什么有趣？在某种程度上很难定义——最好的起点可能是 Damien 对[面向文档开发](http://damienkatz.net/2006/05/document_orient.html)的讨论。这里有一个快速概述，但仍然很难找到一个真正的*销售*论点。不如来点代码吧？这里有一个来自演示应用(`couchthread2.php`)的片段，它正在处理一个表单 post

```
 if ($_SERVER['REQUEST_METHOD'] == 'POST') {    
        // someone is creating a new response    

        // Set a field named Type to "response". This is a simple
        // way to identify the "Type" of the document. (but we could
        // have as easily used Form, Class, Category etc as a field
        // name)
        $_POST['Type'] = "response";

        // Add a creation date, and use a format that will sort correctly as text
        $_POST['CreationDate'] = date(DATE_ATOM);

        // add the threadid from query arg
        $_POST['threadid'] = $_GET['threadid'];

        // just take all the posted fields and save them as a new document
        if (couch_create_doc('http://localhost:8888/couchtest/', $_POST)) {    
            header('Location: ' . $_SERVER['REQUEST_URI']); // reload the page
            exit;
        }
    } 

```

让我们放大最后一部分…

```
 if (couch_create_doc('http://localhost:8888/couchtest/', $_POST)) {    
            header('Location: ' . $_SERVER['REQUEST_URI']); // reload the page
            exit;
        } 

```

…只要通过`$_POST`(至少对于这个简单的例子来说)。开始感兴趣了吗？PHP 和数据库之间的[反向代理](http://en.wikipedia.org/wiki/Reverse_proxy)使负载平衡变得透明又如何呢？

从我在 Dokuwiki 中看到的情况来看，wiki 页面直接存储在文件系统中，以一种易于识别的形式保存“原始资源”有很多好处。计算出最后修改时间(缓存)、复制/镜像、管理和一大堆其他东西变得更容易管理，而在关系数据库中，构成一个完整“文档”的内容可能分布在多个表中。当然，缺点是搜索、排序和关联变得更加困难——进入 CouchDb，在那里(如果我理解正确的话),你可以使用它的 [fabric 公式语言](http://www.couchdbwiki.com/index.php?title=Fabric_Formula_Language)从原始文档的内容中“编译”表格。假设创建表的过程是可重复的，那么跨系统复制数据库将“仅仅”是复制原始文档的问题。

这一点的另一面是 Erlang 所能实现的——专为电话交换机而设计，函数式编程语言的意思是 **[没有松鼠](https://www.sitepoint.com/php-to-the-rescue/)** 。一个值得一读的帮助事情点击是[为我们其余的人](http://www.defmacro.org/ramblings/fp.html) …

> 一个函数式程序无需任何进一步的修改即可用于并发。您永远不必担心死锁和竞争条件，因为您不需要使用锁！函数式程序中的任何数据都不会被同一个线程修改两次，更不用说被两个不同的线程修改了。这意味着您可以轻松地添加线程，而不必再考虑困扰并发应用程序的传统问题！

…使用函数式编程，事务之类的事情(显然)变得更容易了——回滚后没有尴尬的[状态在](http://damienkatz.net/2006/04/error_code_vs_e.html)徘徊。

不管怎样，我想是一部值得一看的电影。

## 分享这篇文章