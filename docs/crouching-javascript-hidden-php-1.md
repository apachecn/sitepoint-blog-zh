# 蛰伏的 Javascript，隐藏的 PHP [1]

> 原文：<https://www.sitepoint.com/crouching-javascript-hidden-php-1/>

杰森一直在思考让 T2 的 Javascript 与 PHP 对话的选择，或者换句话说，如何让网页加载数据而不需要重新加载(或者至少不需要 T4 出现来重新加载)。

为什么要这么做？因为你可以给人一种富客户端(桌面 GUI)与典型的基于 web 的瘦客户端的印象，在瘦客户端中，简单的用户交互通常需要大量的页面重新加载和等待。

举例来说，考虑一个简单的基于 web 的管理应用程序，其中管理员有一个用户列表，可以编辑列表中的每个用户。我们如何才能避免在每次编辑之间将用户列表重新加载给单个用户？

乍一看，这是一个很容易解决的问题——或者也许*应该*是一个很容易解决的问题。再看远一点，你会发现这是 web 开发的灰色地带之一，让你想到“现在是 2004 年。现在肯定有人已经一劳永逸地解决了这个问题。”

无论如何，这是一个很长的，没有特别关注的长篇大论，也是从[将浏览器从资源中分离](https://www.sitepoint.com/blog/)和[将 PHP 数据结构序列化为 Javascript](https://www.sitepoint.com/blog/) 开始的。

随意喷火/纠正。任务是尽可能完整地定义“问题”。也许这需要在适当的时候移植到维基上。

**机制/有效载荷/群体**

当考虑如何让 Javascript 与 PHP 对话时，我认为有三个主要的技术领域需要考虑；

–用于在 Javascript 和远程服务器之间传输信息的机制

–通过该机制实际交换的有效载荷

–如何用收到的有效载荷填充用户界面

请注意，我有意避免使用“传输”和“协议”这样的词，因为我认为它们混淆了这个问题，特别是在处理 XML-RPC 这样的东西时，这相当于一个协议建立在一个协议之上。

**机制**

首先，简单说一下术语；

*同步调用与异步调用*
在本主题的上下文中，当您调用 Javascript 中的某个函数(该函数指示浏览器从远程服务器获取数据)时，执行该调用的代码是等待函数返回(同步调用)还是继续执行，允许函数在后台执行(异步调用)？

从最终用户的角度来看，当他们单击“获取数据”按钮时，用户界面是否会冻结，直到数据获取操作完成(这是同步调用的情况)，或者当浏览器忙于从远程服务器获取数据时，用户界面是否会继续混乱(异步)？

旁注–与此相关的是[阻塞或非阻塞插座](http://www.catalyst.com/support/tutorials/tcpintro/page9.html)的概念。参见 PHP 中的 [stream_set_blocking()](http://www.php.net/stream_set_blocking) 。

*可用机制*
考虑到你必须让 Javascript 与远程服务器对话的选项，*实际上*(也就是忽略像 [Mozilla 的 SOAP 实现](http://www.mozilla.org/projects/webservices/)这样的东西)它似乎可以归结为两个选择之一；

–HTML src 属性的技巧

给定一个引用外部资源并可以返回可解析内容的标签，您可以使用 Javascript 改变 src 属性，允许主页面保留在浏览器中。

最常见的方法是使用框架或 iframe——参见[使用 IFRAME 的远程脚本](http://developer.apple.com/internet/webcontent/iframe.html)(这是来自 Google 的第一个显而易见的链接)。

另一种方法是改变 script 标签的 src 属性，这由 [Javascript Serializer](http://www.tekool.net/php/js_serializer/) 很好地说明了，这是一个让 PHP 和 Javascript 对话的库，这意味着从远程服务器加载可执行的 Javascript。

总的来说，我想这可能是最明智的机制，如果你想要的东西，你可以快速工作，最小的“风险”。在我看来，它基本上是“笨重”的。您几乎没有办法识别和处理错误，例如在发出请求时远程服务器关闭，而且您不得不在 HTML 中嵌入实际上不应该存在的内容(并且只是等待有人在修改页面时破坏它)。你也被迫进行异步调用，这并不是灾难，但有时你*希望*你的应用程序让用户等待，直到一个动作的响应完成。

–XMLHttpRequest

XMLHttpRequest ，在 Mozilla / Firefox、IE 和 Safari 的最新版本中提供了或多或少类似的 API，为您提供了一个 Javascript 对象，您可以将其视为 HTTP 客户端。

XMLHttpRequest 为您提供了很多控制，允许您的代码知道服务器响应(如 404)以进行错误处理，并提供同步和异步模式，例如(Mozilla 示例)；

 `var XmlHttp = new XMLHttpRequest();`

 `// Async -在这个
XmlHttp.open('GET '，' http://localhost/slow script . PHP '，true)之后继续执行；` 

`// Sync - execution waits until HTTP request is complete
XmlHttp.open('GET', 'http://localhost/slowscript.php', false);`

没有必要在 HTML 中嵌入可疑的标签

反对 XMLHttpRequest 的显而易见的一点是浏览器不支持它，但是，就像 Jason 的情况一样，您想要富客户端行为的应用程序类型通常被部署到一个已知的用户群(例如内部网应用程序)，在那里您可以合理地要求他们使用该程序。否则，关于 XMLHttpRequest 的文档和经验仍然相对较少(但正在变得更好)——如果您对 Javascript 不自信，这可能是个问题。

**有效载荷**

下一个问题是你实际上交换的是什么？很多讨论都是用 SOAP 和 RPC 相对于 REST 以及编码相对于文字的思想进行的。为了避免冗长的语义讨论，所涉及的选择看起来像这样；

*RPC 与 REST 的对比*

–RPC(远程过程调用)

适合程序员；不是在本地使用某个库，而是知道该库的网络地址并远程调用它。

对于 web 而言，“网络地址”通常意味着一个 URL，即使用 HTTP POST 方法交换的数据。

要最大程度地了解基于 web 的 RPC(避免用 XML 混淆问题)，看一看 [MIME-RPC](http://www.mime-rpc.com/) ，尤其是[示例](http://www.mime-rpc.com/examples.html)(阅读 Kev 的[PHP 高级电子邮件](https://www.sitepoint.com/advanced-email-php/)教程，作为理解 MIME plus scan over [方法 GET 和 POST in HTML 表单的起点——有什么不同？](http://www.cs.tut.fi/~jkorpela/forms/methods.html)如果你不确定)。

从 PHP 的角度来看，你可能有一个类似于；

[http://www.site.com/rpc-server.php](http://www.site.com/rpc-server.php)

在 Javascript 中，你可以向这个 URL 发送内容(要么使用 Jason 建议的隐藏在框架中的表单，要么使用 XMLHttpRequest)。

对于基于 web 的 RPC 的问题，最简单的观点是“你能只用浏览器与服务器交互吗？”(容易调试？).这意味着上面的基于“src”的机制将不起作用，因为它仅限于获取基于请求的请求。

–休息

“REST 方式”(参见此处的获得一个很好的总结)本质上优先考虑 HTTP GET 方法。

换句话说，应该可以用浏览器在你的远程服务器上冲浪，例如[http://www . te kool . net/PHP/js _ serializer/example _ calculator . PHP？1，+，1](http://www.tekool.net/php/js_serializer/exemple_calculator.php?1,+,1)–可以手动修改网址。

REST 非常适合基于 src 属性的方法，并且通常易于调试。如果您的服务器生成类似 XML 的东西，那么对于不同类型的操作也很容易重用(例如，加入一些 XSLT)。

问题(或需要做出明智决策的地方)是如何将 Javascript 中的函数调用(及其参数)映射到给定的 URL。向服务器发送一个复杂(或大型)的数据结构，比如多维数组，该怎么办呢(此时您可能需要 POST)。

*编码与文字*

下一个问题是被交换的数据实际上应该采取什么形式？请求是否包含与响应相同形式的数据？

–编码

对数据进行编码意味着以客户机和服务器都能理解的某种中间形式来表示本地变量(用 Javascript 或 PHP)。 [XML-RPC](http://www.xmlrpc.com/spec) 是一种众所周知的以 XML 格式编码客户端请求和服务器响应的方法，该 XML 格式直接描述数据类型，例如服务器响应；

 `< ?xml version="1.0"?>
<methodresponse><params><param> <array><data><value><i4>12</i4></value>
<value><string>Egypt</string></value>
<value><boolean>0</boolean></value>
<value><i4>-31</i4></value></data></array></params></methodresponse>` 

…可能对应于 PHP 中的以下内容；

 `$response = array (
12,
"Egypt",
false,
-31,
);`

…在 Javascript 中；

 `var response = [
12,
"Egypt",
false,
-31
];`

XML-RPC 也很大程度上决定了您将使用 RPC(相对于 REST ),但鉴于 PHP 和 Javascript 中众多成熟的实现，它仍然是“最明智”的方法之一(参见下面的列表)。

有替代的方法来编码数据，包括 [WDDX](https://www.php.net/manual/en/book.wddx.php) 和 [SOAP 编码](https://www.w3.org/2001/12/soap-encoding)。当考虑 PHP 和 Javascript ( [PHP WDDX 扩展](http://www.php.net/wddx)和一个 Javascript 实现)时，WDDX 可能是继 XML-RPC 之后第二明智的选择。

您还可以用不同的方式对请求和响应进行编码，可能对请求使用 URL 编码(GET 查询字符串中的变量),甚至为响应生成 Javascript。

此外，PHP(我认为)处理客户端请求中更复杂的数据类型的一个好技巧是将 Javascript 变量序列化为一个字符串，为 PHP 的 [unserialize()](http://www.php.net/unserialize) 函数做好准备(参见不断增长的用户贡献的注释和我的尝试[这里](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/scriptserver/ScriptServer/js/tophp.js?rev=1.6&view=auto))。

与此同时，响应可能包含文字 Javascript ready for eval()——我尝试用代码生成器来实现这一点，这里是[这里是](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/scriptserver/ScriptServer/Serializer.php?rev=1.10&view=auto)，而 [Javascript Serializer](http://www.tekool.net/php/js_serializer/) 有一个更简单的实现。

编码的主要问题是，在开发和性能方面，在原生数据和编码数据之间的转换会陷入困境。

–文字

字面上的方法意味着在没有信息的情况下围绕*移动信息，信息*本身*包含如何将其转换为本地语言数据类型的描述。您可能有一些额外的东西，比如 XML 模式文档，它描述了文档应该如何“翻译”,但是是否使用它取决于您自己。*

也许最广为人知的形式是亚马逊的 XML 服务。如果考虑一个类似于 [this](http://rcm.amazon.com/e/cm?t=sitepoint&l=st1&search=php&mode=books&p=102&o=1&f=xml) 的 URL，它包含了关于你应该把文档翻译成你所选择的语言/平台的信息。很明显这一点。

 `< ?xml version="1.0" encoding="ISO-8859-1"?>
 <catalog><keyword>php</keyword> <product_group>Books</product_group> <product><title>PHP and MySQL for Dynamic Web Sites: Visual QuickPro Guide</title>
<asin>0321186486</asin>
<author>Ullman, Larry</author></product> <product><title>PHP and MySQL Web Development, Second Edition</title>
<asin>067232525X</asin>
<author>Welling, Luke</author></product></catalog>`

在 PHP 中应该是这样的:

 `$catalog = array (
'keyword' => 'php',
'product_group' => 'books',
'products' => array (
array (
'title'=>'PHP and MySQL for Dynamic Web Sites',
'asin'=>'0321186486',
'author'=>'Ullman, Larry',
),
array (
'title'=>'PHP and MySQL Web Development',
'asin'=>'067232525X',
'author'=>'Welling, Luke',
),
),
);`

这非常适合类似于 [PEAR::XML_Serializer](http://pear.php.net/XML_Serializer) 的东西，就像我之前在[这里](https://www.sitepoint.com/article/xml-php-pear-xml_serializer/5)提到的那样。不幸的是，我们还没有在 Javascript 中找到一个现成的等同于 XML_Serializer 的东西(尽管编写一个并不难)。我们想要解析 XML 的开销吗？

另一种非常字面意义的方法是 Christian Stocker 用 [LiveSearch](http://blog.bitflux.ch/wiki/LiveSearch) 所做的，输出是一大块[HTML](http://blog.bitflux.ch/livesearch.php?s=PHP)你可以直接插入页面。到目前为止，还没有人问过类似“doctype 在哪里？”；)

一个普遍的问题(或关键决策点)是如何发送客户端请求——例如，一个客户端如何请求三个不同类别的所有书籍——当响应是文字时，请求应该被编码吗？

此外，特别是如果服务器端开发人员独立于(远程)构建客户端工作，他们如何就一个通用格式达成一致？并且容易解析吗？屏幕造型的所有乐趣…

**人口**

一旦我们已经从服务器收到了一些东西，不管是什么，下一个问题是如何将它与用户界面融合在一起？

选择似乎是在用 DOM、XSLT 操作或者用框架的有些狡猾(但可能更容易破解)的视觉技巧之间。

GMail 似乎采用了后者，使用了框架(我没有账户，所以我是根据 Mark Pilgrim 说的话猜测的[这里是](http://diveintomark.org/archives/2004/04/10/gmail-accessibility))。换句话说，客户端一点也不费力——响应以随时可查看的形式交付。

XSLT 可能是一种更巧妙的做事方式。话说回来……

或者使用 DOM，您可以操纵任何您喜欢的东西……但要付出代价——您需要编写代码，用您刚从服务器收到的结果集填充该表。这可能会非常痛苦，并且可能会将您带入模板化的领域(在服务器端已经是一个灾难性的领域)。当您想在编辑单个用户后只更新用户列表中的一行时，生活会变得更加有趣。

**要走的路？**

不知道；)

或许最安全的是 GMail 正在做的事情——我们不要自作聪明。也就是说，估计这会导致它自己的发展噩梦。下次再详细讲。

**咆哮着**

第二部分更侧重于实际的开发问题，例如，用 PHP 构建 XML-RPC 服务器应该是这样的吗？

 `< ?php
include 'xmlrpc.inc';
include 'xmlrpcs.inc';`

 `函数 sumAndDifference ($params) {

//解析我们的参数。
$ XV al = $ params->get param(0)；
$ x = $ XV al->scalar val()；
$ yval = $ params->getParam(1)；
$ y = $ yval->scalar val()；

//构建我们的响应。
$ struct = array(
' sum ' =>new xmlrpcval($ x+$ y，' int ')，
' difference ' =>new xmlrpcval($ x-$ y，' int ')
)；
$ return _ val =&new xmlrpcval($ struct，' struct ')；

返回新的 xmlrpcresp($ return _ val)；
}` 

`new xmlrpc_server(
array(
'sample.sumAndDifference' => array(
'function' => 'sumAndDifference'
)
)
);
?>`

或者像这样；

 `< ?php
include('IXR_Library.inc.php');`

 `函数 sumAndDifference($ params){
$ x = $ args[0]；
$ y = $ args[1]；

返回数组(
'sum' = > $x + $y，
'difference' = > $x - $y，【T2))；
}

$server =新 IXR _ 服务器(
数组(
' sample . sumand difference ' =>' sumand difference '，
)，
)；` 

`>>`

另一点——当服务器端发生变化时，破坏客户端有多容易？用生成的 Javascript 能做什么？

**PHP 和 Javascript 中的 XML-RPC**

精选的少数人；

–PHP

+[XML-RPC](http://www.php.net/xmlrpc)——不断实验的扩展。如果你已经在你的服务器上编译了它，那就太好了，尽管当涉及到[自省](http://scripts.incutio.com/xmlrpc/introspection.html)时，使用起来很痛苦，并且对于用 PHP 定义的类来说也很笨拙。

+[incu TiO XML-RPC](http://scripts.incutio.com/xmlrpc/)—[西蒙](http://simon.incutio.com)的优秀库。西蒙很遗憾地放弃了。与此同时，我在这里慢慢地劈一个(现在有些臃肿的)叉子。**注意:**我强烈推荐拆开 Incutio XML-RPC，看看它是如何工作的。

+[PEAR::XML-RPC](http://pear.php.net/package/XML_RPC)–追溯到最初的 [usefulinc](http://xmlrpc.usefulinc.com/php.html) 库。个人不太喜欢(例如，计算全球)。如果使用它，一定要弄清楚 XML_RPC_encode()函数是做什么的。

+ Keith 的 XML-RPC 实现——这再简单不过了。

–Javascript

+[JavaScript o lait](http://jsolait.net/)–准备用于 XMLHttpRequest。我还没有试过，但是代码看起来不错，这使得[这个](https://www.sitepoint.com/blog/)成为可能。变得相当大，但也在积极发展。

+ Scott Andrew 的[XML-RPC message builder](http://www.scottandrew.com/xml-rpc/)–为您提供 XML-RPC 消息–取决于您使用什么机制。轻量级，这很好。

+[vcXMLRPC](http://www.vcdn.org/Public/XMLRPC/)–相当完整的实现，尽管现在开发已经停止。

+[nsxmlrpclient](http://mozblog.mozdev.org/nsXmlRpcClient.js)–Mozilla only over XMLHttpRequest–小心权限雷区([见此](http://www.phppatterns.com/index.php/article/articleview/86/1/2/))。

## 分享这篇文章