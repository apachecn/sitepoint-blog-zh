# 用 PHP 和 XML-RPC 构建自己的 Web 服务

> 原文：<https://www.sitepoint.com/own-web-service-php-xml-rpc/>

凯文·杨克在他的文章《Web 服务去神秘化》中介绍了 Web 服务，这是当前互联网的热门话题之一。

如果你一直在关注这方面的行业新闻，你可能会认为这是一些高水平的技术，只有拥有巨额预算的大公司才会感兴趣。但如果你知道，那你就错了。

Web 服务背后的概念非常简单，在本文中，我们将更深入地了解其中涉及的内容。然后，在我们的好朋友 [PHP](http://www.php.net) 的帮助下，我们将建立我们的第一个 Web 服务。

我们将假设你对 PHP、MySQL 和 XML 有所了解，如果你不确定，你可以从[使用 PHP & MySQL](http://www.webmasterbase.com/article/228) 和 [XML 构建你自己的数据库驱动网站——简介](http://www.webmasterbase.com/article/345)中快速学到。

这是菜单上的内容:

***Web 服务的基础:*** 首先，我们将快速回顾 Web 服务背后的基本概念。

***介绍 XML-RPC:*** 接下来，我们将向您介绍一种用于系统间数据交换的 XML 标准，我们将把它放到 Kevin 的文章的上下文中。

***PHP XML-RPC 实现:*** 然后我们将回顾 PHP 中 XML-RPC 的一些开源实现:您可以使用代码快速构建自己的 Web 服务，或者从您的站点访问其他 Web 服务。

***你的第一个网络服务:*** 如果你想直接进入正题，这里就是你要去的地方。这里，我们将采用前面描述的一个实现，并用 PHP 为它构建一个 Web 服务。

***你能用 XML-RPC 做什么:*** 想知道下一步该做什么？我们将为您提供一些关于如何使用 XML-RPC 和 Web 服务的想法。

我们开始吧！

##### Web 服务的基础

关于 Web 服务，要理解的第一件事是它们并不是什么新东西。如果你曾经使用 RSS Feed 从另一个网站获取新闻并放在你自己的网站上，你已经对 Web 服务如何工作有了很好的了解(见 Kevin Yank 的文章: [PHP 和 XML:解析 RSS 1.0]( http://www.webmasterbase.com/article/560) )。

Web 服务是关于在服务器和客户机之间交换数据，使用标准的 XML 格式来“打包”请求和数据，以便两个系统能够相互“理解”。服务器和客户机都可以是网络服务器，或者任何你能想到的电子设备。

就网络而言，Web 服务中的数据交换通常通过 TCP 端口 80 进行，使用标准的 HTTP 协议 post。换句话说，Web 服务的工作方式基本上与浏览器向站点发送 HTML 表单并接收网页响应的方式相同。唯一真正的区别是，Web 服务使用 XML，而不是 HTML。这意味着网络服务可以在互联网上的任何地方使用，就像浏览网页一样穿过防火墙。数据交换发生在**封装**层。

除了数据交换，您还需要描述服务接口(或应用程序接口——API)的信息。这使得 Web 服务对互联网的其他部分有用，允许其他开发人员开发可以访问您的 Web 服务的程序。这被称为**描述**层，WSDL (Web 服务描述语言)标准正在开发中。

除此之外，还有描述服务本身性质的信息(与 HTML 描述性元标签相似)，这样就可以对其进行分类，并在提供网络服务目录的网站上找到。这是**发现**层，目前正由 UDDI(通用描述、发现和集成)标准解决。

描述层和发现层都是简单的 XML，由一种特定的格式管理，这种格式使得可以在互联网上找到所有 Web 服务的相关信息。

也许除了微软和 IBM 这样的公司的营销之外，最近使 Web 服务成为热门话题的是这些标准的发展。它们将允许网络服务在互联网上大规模推出，并由开发工具支持，使对它们的访问既可预测又容易。

但是应该记住，Web 服务现在所做的一切，就数据交换而言，也可以在 5 年甚至 10 年前使用 HTTP 标准和您选择使用或发明的任何 XML 格式来完成(RSS 提要就是一个主要的例子)。今天的“热点新闻”是构建和发布 Web 服务现在比过去容易多了。

无论如何，如果你正在寻找一个关于 Web 服务技术发展的无炒作的新闻来源，试试 [XML Hack](http://xmlhack.com/list.php?cat=25) 。

我们将跳过进一步的概括，但足以说明本文关注的是**打包**层，即如何构建和访问 Web 服务。

##### XML-RPC 简介

在《Web 服务揭秘》中，Kevin 着眼于一个叫做 [SOAP](https://www.w3.org/TR/soap12-part1/) 的标准，它被用来“打包”数据以便在两个系统之间交换。SOAP 正在成为 Web 服务的 W3C 标准，在微软和 IBM 的支持下，可能很快就会获得正式批准。

但是另一个标准也有同样的目的: [XML-RPC](http://www.xmlrpc.com) 。它自 1998 年就出现了，尽管它不是 W3C 的官方标准，但已经被广泛使用，并以开源项目的形式得到了广泛的支持。由 [Useful Inc.](http://usefulinc.com/) 与微软联合开发，它在许多方面可以被视为 SOAP 的先驱，SOAP 还没有完全停止运行。

正如本文将展示的，XML-RPC 最大的优点是简单，当您将 1500 字的规范[与 SOAP 的 11000 字(并且还在增长)的圣经进行比较时，这一点从一开始就很明显。另一个好消息是 XML-RPC 在 PHP 中得到了很好的支持，以及在您想要的任何其他语言中的](http://www.xmlrpc.com/spec)[实现](http://www.xmlrpc.com/directory/1568/implementations)。

关于 XML-RPC 和 SOAP 的详细比较，我推荐阅读 [XML-RPC 与 SOAP](http://weblog.masukomi.org/writings/xml-rpc_vs_soap.htm) 。简而言之，SOAP 旨在填补 XML-RPC 中的空白，为系统间的数据交换提供真正的企业级机制。例如，通过 SOAP 交付多维数组应该比通过 XML-RPC 更容易。但是使用 XML-RPC 并没有错，特别是考虑到它是一个稳定的标准，而 SOAP 可能会在未来经历进一步的重新定义。

您可能想知道，如果 XML-RPC 即将被另一种标准所取代，是否值得花精力去学习它。不用担心:您可以使用 [XSLT](https://www.w3.org/TR/xslt) 轻松地[将 SOAP 请求](http://www.xmlrpc.com/discuss/msgReader$559)转换为 XML-RPC 请求，这将允许您的 XML-RPC 在混乱的互联网中生存，每个人都在寻找 SOAP(抱歉——但必须说一句双关语)。

那么 XML-RPC 是如何工作的呢？正如您将在本文后面看到的，要使用它，您真的不需要了解太多。但是有大量的信息描述它，值得一读的是《T2 新手用 XML-RPC》。我们将快速看一下 XML-RPC 现在是如何工作的，然后继续讨论我们如何利用它。

XML-RPC“消息”的两个主要组成部分是**方法**和**参数**。方法大致对应于您在 PHP 中定义的函数，而参数对应于您传递给这些函数的变量。参数可以是许多不同类型中的一种，比如字符串、整数和数组——非常类似于您在 PHP 中已经习惯的变量类型。此外，XML-RPC 定义了其他标签，比如错误处理，但是我们将把解释留给[规范](http://www.xmlrpc.com/spec)，因为在大多数情况下，我们永远不需要担心它们。

两个系统 bgins 之间的 XML-RPC“对话”，来自 XML-RPC 客户端的**请求**，服务器用**响应**进行应答。该请求包含一个方法，并且可能包含该方法所需的一些参数。响应用包含所请求数据的参数进行回复。这个过程非常像使用你在 PHP 脚本中定义的函数；你调用一个函数并给它传递一些变量。然后，该函数通过返回一些变量来响应。

从目标 XML-RPC 服务器的角度来看，客户端请求可能是这样的:

*请求(来自您的 XML-RPC 客户端):*

```
 POST /xmlrpcInterface HTTP/1.0  

  User-Agent: Sitepoint XML-RPC Client 1.0  

  Host: xmlrpc.sitepoint.com  

  Content-type: text/xml  

  Content-length: 195  

  <?xml version="1.0"?>  

  <methodCall>    <methodName>forums.getNumTodaysThreads</methodName>  

    <params>  

      <param><value><string>PHP Development</string></value></param>  

    </params>  

  </methodCall>
```

这是从 XML-RPC 客户端看到的来自服务器的响应。

*响应(来自 XML-RPC 服务器):*

```
 HTTP/1.1 200 OK  

  Connection: close  

  Content-Length: 148  

  content-Type: text/xml  

  Date: Wed, Jul 28 1999 15:59:04 GMT  

  Server: Sitepoint XML-RPC Server 1.0  

  <?xml version="1.0"?>  

  <methodResponse>  

    <params>  

      <param>  

        <value><int>42</int></value>  

      </param>  

    </params>  

  </methodResponse>

The example request might be to find out how many threads have been posted in the PHP Development forum today. The response tells the client the number it asked for. This is just a simple example, sending one parameter to a method and getting one parameter back. XML-RPC provides much more, allowing you to pass the server more complicated sets of data (such as arrays), and receive in return a detailed response that contains all sorts of data.如果您想知道，XML-RPC 中的“RPC”代表远程过程调用。客户端系统“调用”远程服务器上的“过程”(或方法)。远程过程调用的概念对于软件开发来说是很普遍的，你会在很多地方遇到它。XML-RPC 是用于远程过程调用的数据交换的 XML 标准。

XML-RPC 本身位于 Kevin 的“Web 服务去神秘化”中描述的技术堆栈的包装层(如上所述，它是 SOAP 的替代物，Kevin 将 SOAP 用作包装层的示例)。在描述层和发现层发生什么取决于您。XML-RPC 的一些实现提供了提供自省的扩展，这相当于描述层提供了调用 XML-RPC 服务器的方法和参数的细节。XML-RPC 缺少发现层:没有 XML-RPC 服务器的中央注册中心，但是当需要出现时，符合 UDDI 标准的扩展无疑会出现。

XML-RPC 已经足够详细了！使用 PHP 已经存在的实现，您不需要过多地参与标准本身。因此，让我们看看您可以免费获得哪些 XML-RPC 和 PHP...

##### PHP XML-RPC 实现

就在您的手开始出汗的时候，是时候回顾一些开源项目了，这些项目可以让您不必编写自己的代码来生成 XML-RPC 请求和响应。这里回顾的都来自 xmlrpc.com 的[的](http://www.xmlrpc.com)[实现](http://www.xmlrpc.com/directory/1568/implementations)页面。审查它们的两个主要“压力测试”是:

1.  安装和使用它们需要多长时间？

3.  它们会受到普通 Web 主机上的 PHP 配置类型的广泛支持吗？

 *** XMLRPC-EPI *** 

[http://xmlrpc-epi.sourceforge.net/](http://xmlrpc-epi.sourceforge.net/)

XMLRPC-EPI 最初是在 [epinions](http://www.epinions.com ) 为内部使用而开发的，现在已经成功地为 PHP 提供了其*实验* [ XML-RPC 函数](http://www.php.net/manual/en/ref.xmlrpc.php)。XMLRPC-EPI 本身是一个用 C++编写的基类(其余的是用 PHP 编写的)，如果您没有 root 访问权限，就不能将它安装在 Web 服务器上——您需要重新编译 PHP。

除此之外，这个类只处理 XML-RPC 请求和响应的解释，并不实际发送或接收请求本身。你可以在这里找到关于它的用法的早期教程。

***eZ xmlrpc***

[http://developer.ez.no/article/static/53/](http://developer.ez.no/article/static/53/)

由博德·法尔斯塔德开发(很喜欢这个名字)，这是在 [ezPublish](http://developer.ez.no) 中使用的 XML-RPC 类，用于接收来自[桌面客户端](http://shop.ez.no/trade/productview/25/2/ )的请求。这个类的设计很好，因为它操作起来相当直观，并且由[这个教程](http://zez.org/article/articleview/47/ )很好地介绍了，另外它提供了内省。

但是它在一个要点上失败了——它要求 [xml 解析器](http://doc.trolltech.com/2.3/xml-dom.html qt-dom) ( - with-qtdom)是可用的，并且被配置为与 PHP 一起使用。这对于大多数 PHP 安装来说将是不标准的，它们通常采用 [expat XML 解析器](http://sourceforge.net/projects/expat/) ( - with-xml)。所以，很遗憾，我们不得不去别处寻找...

*Fase 4 XML-RPC*

[http://www . phase 4 . com/xmlrpc/](http://www.fase4.com/xmlrpc/)

查看该站点，文档是不错的，但是在安装和尝试“server.php”演示脚本时，会导致“class not found”错误。有这么多的选择，这消除了这个阶层的比赛。

 *** phpRPC *** 

[http://sourceforge.net/projects/phprpc/](http://sourceforge.net/projects/phprpc/)

这个班有潜力。它超越了解释和请求/响应处理，提供了像连接到“抽象”数据库这样的功能。当前的版本是 alpha 0.9，所以现在我们不考虑它，但它值得关注。如果你有大项目计划，这个课程可以节省你很多开发时间。

***【phpx mlrpc】***

[http://phpxmlrpc.sourceforge.net/](http://phpxmlrpc.sourceforge.net/)

由 XML-RPC 标准的创建者 Useful Inc .开发的这个类显然有很多优点。基本上，它完全支持 XML-RPC 标准，并提供调试，正如我们稍后将看到的，这可能是构建 Web 服务时的一个大问题。

文档很好，有大量的例子，以及关于如何用它构建客户端的教程。但是作为构建第一个 Web 服务的起点，这个类并不理想——它只能推荐给更有经验的 PHP 开发人员，这就是为什么我们要去别处看看...

 ***基思·德文斯的 XML-RPC 客户/服务器*** 

[http://www.keithdevens.com/software/xmlrpc/](http://www.keithdevens.com/software/xmlrpc/)

访问这个项目的主页，你会很快意识到你有了一个更容易的开始。代码只是一组用户定义的 PHP 函数，用来将[包含在脚本中，而不是一个完整的类。](http://www.php.net/include )

它使 XML-RPC 服务器和客户机不再需要知道它们要处理什么类型的 XML-RPC 参数。这确实意味着您的 XML-RPC 客户机需要更加小心地检查它接收到的数据，但是您只会担心 PHP 变量(您应该已经习惯了)，而不会担心 XML-RPC 参数。另外，文档也很好。因此，这就是我们将要开始构建您的第一个 Web 服务的地方...

##### 你的第一个网络服务

开始了。让我们构建一个 Web 服务，按照 RSS 提要的方式从您的网站发布新闻。但是，我们的 Web 服务不是简单地提供一个标题和链接列表，最终将用户带回到最初的新闻源，而是让它看起来像是新闻生活在安装了 XML-RPC 客户端的站点上。

我们将定义两个供我们使用的 XML-RPC 方法:“`news.getNewsList`”，它将提供新闻条目的列表，以及“`news.viewNewsItem`”，它将完整地显示单个新闻条目。`method_not_found`还会有默认的方法，以防麻烦。每个方法对应于我们将在 PHP 中定义的一个函数，比如一个来自数据库的查询。

我们假设您有 PHP 4.1.0 或更高版本，并且可以访问 MySQL 数据库(版本 3.23 以上)。对于 Keith 的 XML-RPC 相关函数，值得阅读代码、检查注释以及查看[文档。](http://www.keithdevens.com/software/xmlrpc/ )

好的。卷起袖子，我们走吧...

 ***准备工作*** 

 **1。**下载本文的[代码档案](http://webmasterbase.com/examples/phpws/kd_xmlrpc.zip)——它将使您从从本教程的页面中辛苦地剪切和粘贴代码中解脱出来。

 **2。**从 http://www.keithdevens.com/software/xmlrpc/source.php[下载源代码](http://www.keithdevens.com/software/xmlrpc/source.php )，保存为“kd_xmlrpc.php”。

 **3。**现在在您的 Web 服务器上创建一个名为 kd_xmlrpc 的目录，位于您的 Web 根目录(您的“主页”所在的位置)下，并将 kd_xmlrpc.php 放在那里。我们将把下面创建的所有其他文件放在同一个目录中。

 **4。**使用以下查询在 MySQL 数据库中创建一个表:

```
CREATE TABLE kd_xmlrpc_news (    

  news_id mediumint(9) NOT NULL auto_increment,    

  title varchar(255) default NULL,    

  short_desc text,    

  full_desc text,    

  author varchar(100) default NULL,    

  date datetime NOT NULL default '0000-00-00 00:00:00',    

  PRIMARY KEY  (news_id)    

) TYPE=MyISAM COMMENT='XML RPC News';
```

 **4。**现在`INSERT` 将一些虚构的新闻条目放入表中——或者，如果您缺乏灵感，运行您将在代码档案中找到的“kd_xmlrpc_news.sql”查询(在步骤 1 中下载)。

***XML-RPC 服务器*** 

数据库现在可以开始运行了。接下来，让我们创建 XML-RPC 服务器，并将其命名为“server.php”:

```
<?php    

/* server.php */    

/* Variables for accessing MySQL */    

$dbserver= "localhost";     // Hostname of your MySQL server    

$db = "database_name";      // Name of your MySQL database    

$dbuser = "username";       // MySQL user with access to $db    

$dbpassword = "password";   // Password for MySQL user    

/* Connect to the MySQL server */    

$link = @mysql_connect ($dbserver, $dbuser, $dbpassword);    

if (! $link){    

    echo ( "Unable to connect to db" );    

exit();    

}    

/* Select the database */    

if (!mysql_select_db ($db, $link) ){    

exit ();    

}    

/* Include Keith's xml-rpc library */    

include("kd_xmlrpc.php");    

/* Include a file that defines all the xml-rpc "methods" */    

include("web_service_api.php");    

/* Now use the XMLRPC_parse function to take POST     

   data from what xml-rpc client connects and turn     

   it into normal PHP variables */    

$xmlrpc_request = XMLRPC_parse($GLOBALS['HTTP_RAW_POST_DATA']);    

/* From the PHP variables generated, let's get the     

   method name ie. server asks "What would you like     

   me to do for you?" */    

$methodName = XMLRPC_getMethodName($xmlrpc_request);    

/* Get the parameters associated with that method     

   e.g "So you want to view a news item. Tell me     

   which one you want. What's the id#?" */    

$params = XMLRPC_getParams($xmlrpc_request);    

/* Error check - if a method was used that doesn't     

   exist, return the error response to the client */    

if(!isset($xmlrpc_methods[$methodName])){    

    $xmlrpc_methods['method_not_found']($methodName);    

/* Otherwise, let's run the PHP function corresponding     

   to that method - note the functions themselves     

   return the correct formatted xml-rpc response     

   to the client */    

}else{    

    /* Call the method - notice $params[0] not just $params as the    

    documentation states. */    

    $xmlrpc_methods[$methodName]($params[0]);    

}    

?>
```

下面是 server.php 中发生的事情的简要总结:

1.  连接到 MySQL，让脚本访问数据库。

2.  包括 kd_xmlrpc.php 代码，这样我们就可以使用 Keith 的函数。

3.  包括 web_service_api.php，它定义了 XML-RPC 方法(见下文)。

4.  使用`XMLRPC_parse()` 函数获取`$GLOBALS['HTTP_RAW_POST_DATA']`的内容，其中包含来自 XML-RPC 客户端的请求，并将 XML 转换成 PHP 变量。

5.  现在使用`XMLRPC_getMethodName()`函数确定调用了哪个 XML-RPC 方法。我们希望同一个 XML-RPC 服务器能够执行多个任务(方法/函数)，这里我们决定为当前请求执行哪个任务(例如“`news.getNewsList`”或“`news.viewNewsItem`”)。

6.  现在检查客户机请求中发送的任何参数，比如新闻条目的“`id`”(标识号)。

7.  最后，我们检查方法是否存在，如 web_service_api.php 中所定义的，如果一切正常，我们运行所需的方法(即 php 函数)。如果我们找不到方法，我们运行默认的“`method_not_found`”方法。

我们使用的特殊 XML-RPC 函数是:

*   这个函数获取一些 XML 并将其转换成 PHP 变量——因此来自客户端请求的 XML 被转换成 PHP 可以处理的形式。在这一点上，这并不特定于 XML-RPC 标准——发送的任何 XML 都将被转换成 PHP 变量。

*   确定正在使用的方法，这样我们就可以决定使用哪个 PHP 函数。

*   `XMLRPC_getParams()`接受任何 XML-RPC 参数，并将它们转换成 PHP 变量。

基本上 XML-RPC 服务器就完成了。通常，这是您为构建的每个 XML-RPC 服务器所做的事情。不难吧。现在我们所需要的就是那些与方法一起使用的 PHP 函数...

 ***战法*** 

接下来，我们定义一些对应 XML-RPC 方法的 PHP 函数，并将它们存储在 web_service_api.php 中:

```
<?php     

/* web_service_api.php */     

/* Define an array to name the xmlrpc methods and      

   their corresponding PHP functions */     

$xmlrpc_methods = array();     

$xmlrpc_methods['news.getNewsList'] = news_getNewsList;     

$xmlrpc_methods['news.viewNewsItem'] = news_viewNewsItem;     

$xmlrpc_methods['method_not_found'] = XMLRPC_method_not_found;     

/* Now a useful function for converting MySQL datetime      

   to a UNIX timestamp which can then be used with      

   the XMLRPC_convert_timestamp_to_iso8601($timestamp) function.     

   This is not a method!     

   It comes from: http://www.zend.com/codex.php?id=176&single=1 */     

function mysql_datetime_to_timestamp($dt) {       

    $yr=strval(substr($dt,0,4));       

    $mo=strval(substr($dt,5,2));       

    $da=strval(substr($dt,8,2));       

    $hr=strval(substr($dt,11,2));       

    $mi=strval(substr($dt,14,2));       

    $se=strval(substr($dt,17,2));       

    return mktime($hr,$mi,$se,$mo,$da,$yr);       

}      

/* Function for listing news items, corresponding      

   to the news.getNewsList method Allows ordering by      

   column name and a result limit of up to 20 rows */     

function news_getNewsList ( $query_info=0 ) {     

    /* Define an array of column names we'll accept      

   to ORDER BY in our query */     

    $order_fields = array ( "author", "title" );     

    /* Now check to see if $query_info['order'] has      

    an acceptable value and assign the correct value      

    to the $order variable */     

    if ( ISSET ( $query_info['order'] ) &&     

            in_array ( $query_info['order'], $order_fields ) ) {     

        $order = "ORDER BY " . $query_info['order'] . ", date DESC ";     

    } else {     

        $order = "ORDER BY date DESC ";     

    }     

    /* Now check for $query_info['limit'] to specify      

    the number of news items we want returned,      

    and assign the correct value to $limit */     

    if ( ISSET ( $query_info['limit'] ) && $query_info['limit'] < 20 ) {     

        $limit = "LIMIT 0, " . $query_info['limit'] . " ";     

    } else {     

        $limit = "LIMIT 0, 5 ";     

    }     

    /* Now build the query */     

    $query = "SELECT * FROM kd_xmlrpc_news " . $order . $limit;     

    $sql = mysql_query ( $query );     

    if ( $sql ) {     

        $news_items = array();     

        while ( $result = mysql_fetch_array ( $sql ) ) {     

            /* Extract the variables we want from the row */     

            $news_item['news_id'] = $result['news_id'];     

            $news_item['date'] = XMLRPC_convert_timestamp_to_iso8601(     

                mysql_datetime_to_timestamp( $result['date'] )     

                );             

            $news_item['title'] = $result['title'];     

            $news_item['short_desc'] = $result['short_desc'];     

            $news_item['author'] = $result['author'];     

            /* Add to the $news_items array */     

            $news_items[] = $news_item;     

        }     

        /* Convert the $news_items array to a set      

        of XML-RPC parameters then respond with the XML. */     

        XMLRPC_response(XMLRPC_prepare($news_items),      

        KD_XMLRPC_USERAGENT);     

    } else {     

        /* If there was an error, respond with an      

        error message */     

        XMLRPC_error("1", "news_getNewsList() error: Unable      

        to read news:"     

            . mysql_error() . "nQuery was: " . $query,      

KD_XMLRPC_USERAGENT);     

    }     

}     

/* Function for viewing a full news item corresponding      

   to the news.viewNewsItem method */     

function news_viewNewsItem ( $news_id ) {     

    /* Define the query to fetch the news item */     

    $query = "SELECT * FROM kd_xmlrpc_news WHERE news_id = '"      

    . $news_id . "'";     

    $sql = mysql_query ( $query );     

    if ( $result = mysql_fetch_array ( $sql ) ) {     

        /* Extract the variables for sending in      

        our server response */     

        $news_item['news_id'] = $result['news_id'];     

        $news_item['date'] = XMLRPC_convert_timestamp_to_iso8601(     

            mysql_datetime_to_timestamp( $result['date'] ) );          

        $news_item['title'] = $result['title'];     

        $news_item['full_desc'] = $result['full_desc'];     

        $news_item['author'] = $result['author'];     

        /* Respond to the client with the news item */     

        XMLRPC_response(XMLRPC_prepare($news_item),      

        KD_XMLRPC_USERAGENT);     

    } else {     

        /* If there was an error, respond with a      

        fault code instead */     

        XMLRPC_error("1", "news_viewNewsItem() error: Unable      

to read news:"     

            . mysql_error(), KD_XMLRPC_USERAGENT);     

    }     

}     

/* Function for when the request method name      

doesn't exist */     

function XMLRPC_method_not_found($methodName){     

    XMLRPC_error("2", "The method you requested, " . $methodName     

        . ", was not found.", KD_XMLRPC_USERAGENT);     

}     

?>
```

这里发生了什么？

1.  首先，我们定义了一个 PHP 数组`$xmlrpc_methods`，它是所有可用的 XML-RPC 方法和为该方法运行的 PHP 函数的列表。

2.  `mysql_datetime_to_timestamp()`函数本身不是一个 XML-RPC 方法，而是一个将 MySQL 日期时间字段转换成 PHP 可以处理的格式的有用函数。

3.  然后我们定义`news_getNewsList()`，我们将使用它来查询"`kd_xmlrpc_news`"表。这个函数能够接受一个名为`$query_info`的数组，它对应于 XML-RPC 客户端可以发送的可选参数。如您所见，这改变了我们对 mysql 运行的查询，改变了“`ORDER BY`”和“`LIMIT`”选项。

4.  函数`news_viewNewsItem()`用于从数据库中提取单个新闻条目，并且只接受一个变量/参数:`$news_id,`，它对应于数据库中文章的行 id。

5.  最后一个函数`XMLRPC_method_not_found()`是我们的默认函数，当客户机请求一个不存在的 XML-RPC 方法时，它会生成一个响应。

我们使用的特殊 XML-RPC 函数是:

*   `XMLRPC_convert_timestamp_to_iso8601()`:XML-RPC 有一个特定的发送日期和时间的格式。这个函数接受一个 PHP 时间戳，并将其转换成 XML-RPC 的正确格式。

*   `XMLRPC_prepare()`:这需要一组 PHP 变量，并将其转换成 XML-RPC 参数。它识别正在使用的变量的类型，并从中创建正确的 XML-RPC 输出。这是 Keith Devens 的代码易于使用的原因之一——在构建 XML-RPC 响应请求时，其他实现通常要求对数组、整数、字符串等使用单独的函数。

*   `XMLRPC_response()`:是根据请求向客户端提供结果的函数。它有两个值——第一个是 XML-RPC 数据(由`XMLRPC_prepare())`构建),第二个是服务器或用户代理的可选名称，例如`KD_XMLRPC_USERAGENT`——这可以是您喜欢的任何名称。

*   `XMLRPC_error()`:使用规范中定义的错误报告标签生成 XML-RPC 响应。如果您愿意，您可以在这里为您的服务器构建自己的错误代码集，这对调试服务器很有帮助。

唷！所以现在我们已经定义了我们的 PHP 函数，并且有了一个数组来将它们翻译成 XML-RPC 方法:我们的 XML-RPC 服务器已经准备好了！现在，作为结束，让我们组装一个简单的客户端来访问我们的新闻。

***XML-RPC 客户端*** 

为了举例，我们将把客户机脚本存储在与服务器相同的地方，但是如果您有另一个 Web 服务器，您可以把脚本放在那里，确保它可以访问函数库“kd_xmlrpc.php”。

```
<?php      

/* Include the library */      

include ( "kd_xmlrpc.php" );      

/* Define variables to find the rpc server script */      

$site = "www.yourdomain.com";      

$location = "/kd_xmlrpc/server.php";      

/* Function to give us back a nice date */      

function convert_date ( $date ) {      

    $date = date ( "D M y H:i:s",      

                  XMLRPC_convert_iso8601_to_timestamp ( $date ) );      

    return ( $date );      

}      

?>      

<!doctype html public "-//W3C//DTD HTML 4.0 Transitional//EN">      

<html>      

<head>      

<title> KD XML RPC News Client </title>      

<meta name="Generator" content="EditPlus">      

<meta name="Author" content="HarryF">      

<meta name="Keywords" content="XML RPC">      

<meta name="Description" content="Gets news form server.php">      

</head>      

<body>      

<?php       

/* client.php */      

/* If user is viewing a single news item, do this */      

if ( ISSET ( $_GET['news_id'] ) ) {      

    /* $success is 0 (fail) / 1 ( succeeded ).       

       XMLPRC_request preforms the XML POST to       

       the server script, calling the method and       

       sending the correct parameters using       

       XMLRPC_prepare */      

    list($success, $response) = XMLRPC_request(      

        $site,      

        $location,      

        'news.viewNewsItem',      

        array(XMLRPC_prepare($_GET['news_id']),      

        'HarryFsXMLRPCClient')      

    );      

    /* If all went well, show the article */      

    if ($success) {      

?>      

<table align="center" width="600">      

<tr valign="top">      

<th colspan="2"><b><?php echo ( $response['title'] );?></b></th>      

</tr>      

<tr valign="top">      

<th><?php echo ( $response['author'] );?></th>      

<th><?php echo ( convert_date ( $response['date'] ) );?></th>      

</tr>      

<tr valign="top">      

<td colspan="2">      

<?php echo ( nl2br ( $response['full_desc'] ) );?>      

</th>      

</tr>      

</table>      

<?php      

    /* Else display the error */      

    } else {      

        echo ( "<p>Error: " . nl2br ( $response['faultString'] ) );      

    }      

} else {      

    /* Define the parameters to pass to the XML-RPC       

    method as a PHP array */      

    $query_info['limit'] = 10;      

    $query_info['order'] = "author";      

    /* XMLRPC_prepare works on an array and       

    converts it to XML-RPC parameters */      

    list($success, $response) = XMLRPC_request(      

        $site,      

        $location,      

        'news.getNewsList',      

        array(XMLRPC_prepare($query_info),      

        'HarryFsXMLRPCClient')      

    );      

    /* On success, display the list as HTML table */      

    if ($success) {      

        echo ( "<table align="center" width="600">n" );      

        $count = 0;      

        while ( list ( $key, $val ) = each ( $response ) ) {      

?>      

<tr valign="top">      

<td colspan="2">      

<a href="<?php echo ( $_SERVER['PHP_SELF'] );?>?news_id=<?php      

echo ( $response[$count]['news_id'] );      

?>">      

<?php echo ( $response[$count]['title'] ); ?>      

</a>      

</td>      

</tr>      

<tr valign="top">      

<td colspan="2">      

<?php echo ( $response[$count]['short_desc'] ); ?>      

</td>      

</tr>      

<tr valign="top">      

<td>      

<?php echo ( $response[$count]['author'] ); ?>      

</td>      

<td>      

<?php echo ( convert_date ( $response[$count]['date'] ) ); ?>      

</td>      

</tr>      

<?php      

            $count++;      

        }      

        echo ( "</table>n" );      

    /* Or error */      

    } else {      

        echo ( "<p>Error: " . nl2br ( $response['faultString'] ) );      

    }      

}      

;?>      

</body>      

</html>
```

这里发生了什么？PHP 代码本身只是一个 if/else 结构，所以如果我们决定使用 `$_GET['news_id']` 变量查看一个新闻条目，我们就显示这个条目。否则，我们将显示新闻项目列表。

对于新闻列表，我们使用`news.getNewsList`方法。请注意，我们已经将`$query_info['limit']`设置为 10(也就是说，我们最多想要 10 条新闻)，并将`$query_info['order']`设置为“作者”(按作者姓名对新闻进行排序)。这些将成为在 XML-RPC 客户端请求中发送的参数。

当我们查看单个新闻条目时，我们将把`$_GET['news_id']`转换成一个参数，通过这个参数，服务器将能够识别我们想要的新闻条目。

我们在这里使用的特殊 XML-RPC 函数是:

*   `XMLRPC_prepare()`:我们以前在服务器中使用过，再次将 PHP 变量转换成 XML-RPC 参数。这是我们向服务器提供我们所使用的特定方法所需的数据的地方。

*   `MLRPC_request()`:这个函数接受五个变量并返回一个数组。基本上是什么让你的客户成为客户。接受的有:

*   `$site` -服务器的域名

*   `$location` -服务器上从 Web 根目录到服务器脚本的路径

*   `$methodName` -我们正在调用的 XML-RPC 方法的名称

*   `$params ` -方法所需的任何变量

*   `$user_agent` -这是可选的，可以是你喜欢的任何东西(例如 HarryFsXMLRPCClient)。

返回的数组包含两个变量。第一个是 0 表示失败，或者 1 表示对请求的成功响应，我们用它来进行错误检查。第二个是多维数组，包含响应的所有数据。

*   `-XMLRPC_convert_iso8601_to_timestamp`:顶部是我们的`convert_date()`函数，我们使用这个特殊的函数，通过一个中间的 PHP 时间戳，将 XML-RPC 日期格式改变成看起来不错的格式。

一个技巧:对于编写 XML-RPC 客户机来说，像看待数据库一样看待服务器是有帮助的——它只是一个数据源。就您的脚本而言，代码将与您查询 MySQL 时使用的结构相似。

原来如此！把你的浏览器指向 http://www.yourdomain.com/kd_xmlrpc/client.php,，坐下来看新闻。

下载代码:本教程使用的脚本和 MySQL 表都在[这个 zip 文件]( http://webmasterbase.com/examples/phpws/kd_xmlrpc.zip)里。

##### 包裹

如您所见，使用 Keith Devens 的 XML-RPC 代码，创建自己的 Web 服务相当容易。它为掌握 Web 服务提供了一个极好的起点。也许作为“家庭作业”，你可能想尝试更新客户端，允许访问者选择他们想要的新闻条目排序方式(例如，按作者、标题或日期)。如果你有雄心壮志，也许你甚至可以考虑给客户端添加一个界面来浏览新闻。

如果你有 Web 服务的大计划，你可能想看看其他的实现，比如 Userland 的`phpxmlrpc`类，来优化你的代码设计。但是您肯定可以通过 Keith Devens 的实现，获得与任何其他解决方案相同的最终结果。

Keith 的代码中有一两个你应该注意的小缺点。

首先，客户机使用的`XMLRPC_request()`函数删除了存储在 XML-RPC 响应中的每个变量是什么类型的“知识”,这可能会导致客户机编写额外的代码。例如，您可能希望得到一个数组，但得到的只是一个标量(单值)变量——客户端需要处理这两种情况。

此外，没有自省的扩展，以允许其他开发人员查看您的 API(例如，您的服务器接受什么方法和参数)。不过，添加自己的自省应该不会太难——只需使用我们在 web_service_api.php 中定义的数组。

最后，只有有限的调试条款。如果您唯一的接口是客户机，那么在计算 XML-RPC 服务器正在做什么时，您会遇到很多令人头痛的问题。您可能想考虑使用 PHP 的自定义错误处理为服务器生成某种日志文件——请看一下[这篇文章](http://www.devshed.com/Server_Side/PHP/ErrorHandling )以开始使用自定义错误处理程序。

但是感谢 Keith Devens 的一些伟大的代码，让我们有了一个良好的开端！

这里有几个问题我们还没有考虑:安全性和事务处理。

如果你想要一些恐惧和厌恶，试试这篇文章。关于安全性，有三个主要问题需要注意:

1.  **如何认证 XML-RPC 客户端。**如果您在上面添加了`INSERT` ing 和`UPDATE` ing 新闻的方法，您还需要找到一种方法来认证远程站点和/或该站点的用户。目前，如何做到这一点取决于你自己。您可以使用 IP 地址或主机名来检查远程站点。对于远程用户来说，一种方法是将 XML-RPC 服务器想象成对照数据库检查用户的方式，在允许访问敏感方法之前要求用户名和密码的组合，使用一种纯粹用于身份验证的方法。理想情况下，如果你这样做，你将使用 SSL 加密客户机和服务器之间的连接，但这将需要使用 PHP 的 [Curl 函数](http://www.php.net/manual/en/ref.curl.php)来发送和接收数据(你可能需要重新编写你正在使用的实现的一部分来做到这一点)。

2.  您将如何控制“拒绝服务”攻击？更普遍的问题是:当一个客户端向您的服务器发送大量请求并阻止它响应其他客户端时，会发生什么？

3.  **你在描述层发布你的 API** (自省/WSDL)的时候会有什么控制？为您的 XML-RPC 服务器发布所有方法，以便每个人都可以“试一试”，这是一个好主意吗？你如何控制谁能看到什么？如果您的方法提供了对敏感数据的访问，而这些敏感数据通常在您的防火墙后保持安全，那么您需要仔细考虑。

关于事务处理，如果在 XML-RPC 对话期间失去客户机和服务器之间的连接，会发生什么情况？您提供了什么机制来处理重试(重新发送请求或响应)？您可能无法承受正在运行的 Web 服务的数据丢失，在这种情况下，您需要考虑一些请求和响应的排队机制。有关交易处理原则的介绍，请阅读本文。

抛开担忧不谈，您现在已经构建了您的第一个 Web 服务，并且有望很好地理解该技术是如何工作的。现在的问题是如何处理它？发布新闻没问题，但以前也有人做过。还有什么？我们将在下一节中讨论这一点...

##### 你能用 XML-RPC 做什么？

这个问题有点像问“我能用网页做什么？”唯一的限制是你的想象力。一般来说，使用 XML-RPC 和 PHP 可以将您的网站扩展到简单的网页服务之外。你的站点将能够与你喜欢的任何系统进行数据通信，并且通过采用和接受标准，你提供了一个其他开发者乐于使用的接口。

广义上的网络服务将为那些拥有“有价值数据”的人赚钱。例如，如果您可以直接访问货币汇率，您可以使用 PHP 和 XML-RPC 向其他网站提供货币转换器。但是这里有一些其他的想法和例子:

*   为更新您的网站提供另一种客户端。当你在 HTML 文本区输入一条很长的消息，点击“提交”时，你曾经沮丧地尖叫过吗？因为你的会话超时，或者你失去了与网站的连接，你失去了这一切。XML-RPC 可以:

*   为您提供一个界面来更新您的站点，

*   允许您运行标准的 Windows 客户端，这样您就可以将输入内容保存在本地硬盘上，并且

*   在适当的时候更新网站。

*   ez Systems 已经做到了这一点，为他们的 T4 ez publish T5 内容管理系统提供了 T2 桌面版 T3。

*   用[填充狗的](http://www.stuffeddog.com/) XML-RPC [拼写检查器](http://www.stuffeddog.com/speller/doc/rpc.html )纠正你网站上的拼写。

*   通过在一个 Web 服务器上运行数据库来分配负载和带宽使用，然后让多个服务器提供用户界面，通过 XML-RPC 访问数据库。对于像 [Planetarion](http://www.planetarion.com ) 这样的大型多人游戏，可能性是无限的。

*   使用众多 XML-RPC [实现](http://www.xmlrpc.com/directory/1568/implementations)中的一个，让您的 PHP 站点与 CGI/Perl 或 ASP 站点对话，这允许您在升级到 PHP 时选择保留旧代码。

*   利用[谷歌搜索网关](http://www.xmlrpc.com/googleGateway)，让搜索结果直接出现在你的网站上。

*   从任何地方弹出你的电子邮件，然后用 CGI 服务器从一个 XML-RPC 源读取全部内容(好的——Perl，不是 PHP——但这是一个很好的例子)。

*   使用他们的 [XML-RPC 服务器](http://www.newsisfree.com/webservice.php)，从[获取各种新闻是免费的](http://www.newsisfree.com/)。

可能性是无限的...

## 分享这篇文章

```