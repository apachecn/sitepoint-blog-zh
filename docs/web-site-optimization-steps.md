# 网站优化:13 个简单的步骤

> 原文：<https://www.sitepoint.com/web-site-optimization-steps/>

今年早些时候，来自雅虎的史蒂夫·索德斯(Steve Souders)。性能团队公布了一系列[前端性能优化“规则”用于优化一个页面](http://developer.yahoo.com/performance/rules.html)。

本教程采用基于实例的实用方法来实现这些规则。它的目标是预算不多的 web 开发人员，他们很可能使用共享主机，并在这种设置带来的各种限制下工作。共享主机使得使用 Apache 配置变得更加困难——有时甚至是不可能的——所以我们将看看您能做些什么，给出一些常见的限制，并假设您的主机运行 PHP 和 Apache。

本教程分为四个部分:

1.  基本优化规则
2.  优化资产(图像、脚本和样式)
3.  特定于脚本的优化
4.  特定于样式的优化

##### 学分和建议阅读

这篇文章不是要解释雅虎！的性能规则，所以您最好自己通读一下，以便更好地理解它们的重要性、规则背后的推理以及它们是如何产生的。以下是有疑问的规则列表:

1.  减少 HTTP 请求
2.  使用内容交付网络
3.  添加过期标题
4.  Gzip 组件
5.  将 CSS 放在顶部
6.  将脚本移到底部
7.  避免 CSS 表达式
8.  使 JavaScript 和 CSS 成为外部的
9.  减少 DNS 查找
10.  缩小 JavaScript
11.  避免重定向
12.  删除重复的脚本
13.  配置 ETags

你可以在雅虎上阅读这些规则。开发者网络站点。你也可以看看《T2》这本书，史蒂夫·索德斯的《高性能网站》，以及坦尼·蒂勒在 YUI 博客上发表的《T4》性能研究文章。

##### 基本优化规则

***减少下载大小***

降低下载大小甚至不在雅虎！的规则列表——可能是因为它太明显了。然而，我不认为重申这一点有什么坏处——让我们称之为规则#0。

当我们看一个简单的网页时，我们看到:

*   一些 HTML 代码
*   HTML 引用的不同页面组件(资产)

这些资产是图像、脚本、样式，可能还有一些外部媒体，比如 Flash 电影或 Java 小程序(还记得那些吗？).因此，当涉及到下载大小时，你应该尽可能地使所有的资源轻量级——这个建议也延伸到页面的 HTML 内容。创建精益 HTML 代码通常意味着使用更好的(语义)标记，这也与网站创建过程中必要的 SEO(搜索引擎优化)工作重叠。正如大多数专业 web 开发人员所知，好的标记的一个关键特征是它只描述了内容，而不是页面的表示(没有布局表格！).任何布局或表示元素都应该转移到 CSS 中。

下面是一个导航菜单的 HTML 标记的好方法的例子:

`<ul id="menu">
<li><a href="home.html">Home</a></li>
<li><a href="about.html">About</a></li>
<li><a href="contact.html">Contact</a></li>
</ul>`

这种标记应该提供“钩子”,以允许有效地使用 CSS，并使菜单看起来像你想要的那样——无论这意味着添加花哨的项目符号、边框或翻转，还是将菜单项放置在水平菜单中。标记是最小的，这意味着下载的字节更少；它是语义性的，意味着它描述了内容(导航菜单是一系列链接)；最后，最小化，它也给你一个 SEO 优势:人们普遍认为搜索引擎更喜欢在他们索引的页面中有更高的内容-标记比率。

一旦你确定你的标记是轻量级的和语义化的，你应该检查你的资产并确保它们也是最小的。例如，检查是否有可能在不损失太多质量的情况下进一步压缩图像，或者选择不同的文件格式以获得更好的压缩效果。像 [PNGOUT](http://advsys.net/ken/utils.htm) 和 [pngcrush](http://pmt.sourceforge.net/pngcrush/) 这样的工具是一个很好的起点。

***发出较少的 HTTP 请求***

减少 HTTP 请求被证明是[最重要的优化技术，影响最大](http://yuiblog.com/blog/2006/11/28/performance-research-part-1/)。如果你的时间有限，只能完成一个优化任务，就挑这个吧。HTTP 请求通常是浏览器在显示页面时执行的最“昂贵”的活动。因此，您应该确保您的页面发出尽可能少的请求。

如何在保持页面丰富性的同时做到这一点？

*   **结合脚本和样式表:**你脑子里有几个`<script>`标签？嗯，把`.js` 的文件合并成一个，给你的访客省一些往返；然后对 CSS 文件做同样的事情。
*   使用图像精灵:这种技术允许你将几个图像合并成一个，并使用 CSS 只显示图像中需要的部分。当你将五个或十个图像合并成一个文件时，你已经在请求/响应开销上节省了一大笔[。](http://www.alistapart.com/articles/sprites)
*   **避免重定向:**重定向增加了另一个客户机-服务器往返过程，因此浏览器将不得不发出另一个请求并等待第二个响应，而不是在收到初始响应后立即处理您的页面。
*   避免框架:如果你使用框架，浏览器必须请求至少三个 HTML 页面，而不是一个——框架集和每个框架的页面。

你现在已经掌握了基本知识。总的来说，使你的页面和它的资源更小，并且尽可能地组合它们来使用更少的资源。如果你只关注这方面的优化，你和你的访问者将会注意到显著的改进。

现在让我们探索一些雅虎！更详细的建议，看看还可以做哪些优化来提高性能。

##### 优化资产

***使用内容传递网络***

[内容交付网络(CDN)](http://en.wikipedia.org/wiki/Content_Delivery_Network) 是位于不同地理位置的服务器网络。每台服务器都有一份站点文件的副本。当网站的访问者请求文件时，文件会从最近的服务器(或当时负载最轻的服务器)传送。

这种设置会对页面的整体性能产生重大影响，但不幸的是，使用 CDN 的成本会很高。因此，这可能不是你为个人博客做的事情，但当客户要求你建立一个可能会经历高流量的网站时，这可能是有用的。一些最广为人知的 CDN 提供商是 Akamai 和 T2 亚马逊，通过他们的 S3 服务 T3。

市场上有一些非盈利的 CDNs 查看 CDN 维基百科文章,看看你的项目是否有资格使用其中一个。例如，一个免费的非营利性点对点 CDN 是[珊瑚 CDN](http://www.coralcdn.org/) ，它非常容易与您的网站集成。对于这个 CDN，您获取一个 URL 并在主机名后面附加“nyud.net”。这里有一个例子:

`http://example.org/logo.png`

变成了:

`http://example.org.nyud.net/logo.png`

***托管不同域上的资产但减少 DNS 查找***

当你的访问者的浏览器下载了一个页面的 HTML，并且发现还需要一些组件时，它就开始下载这些组件。浏览器限制同时下载的次数；根据 HTTP/1.1 规范，每个域的限制是两个资产。

因为这种限制存在于每个域的基础上，您可以使用几个域(或简单地使用子域)来托管您的资产，从而增加并行下载的数量。大多数共享主机允许你创建子域。即使您的主机限制了您可以创建的子域数量(有些限制您最多创建五个)，这也不重要，因为您不需要利用太多的子域就可以看到一些显著的性能改进。

然而，正如[规则#9](http://developer.yahoo.com/performance/rules.html#dns_lookups) 所述，你也应该减少 DNS 查找的次数，因为这些也可能是昂贵的。对于托管页面资产的每个域或子域，浏览器都需要进行 DNS 查找。所以你拥有的域名越多，你的网站就越容易被 DNS 查询拖慢。雅虎！的研究表明，两到四个域名是一个最佳的数字，但你可以自己决定什么是最适合你的网站。

一般来说，我建议你用一个域来托管 HTML 页面，用另外两个域来托管你的资产。这里有一个例子:

*   www.sitepoint.com-仅托管 HTML(可能还有内容图片)
*   i1.sitepoint.com——托管 JS、CSS 和一些图像
*   i2.sitepoint.com——拥有网站的大部分图片

不同的主机提供商可能会为创建子域提供不同的接口，理想情况下，他们应该为您提供一个选项来指定保存子域文件的目录。例如，如果您的规范域是 www.sitepoint.com，并且它指向`/home/sitepoint/htdocs`，理想情况下，您应该能够创建子域 i1.sitepoint.com(通过管理控制面板或者通过在文件系统中创建一个符号链接)并将其指向同一个文件夹`/home/sitepoint/htdocs`。这样，您可以将所有文件保存在同一个位置，就像它们在您的开发环境中一样，但是使用子域来引用它们。

但是，有些主机可能会阻止您创建子域，或者限制您指向文件系统上特定位置的能力。在这种情况下，您唯一真正的选择是将资产物理复制到新位置。在这种情况下，不要试图创建某种重定向——这只会使事情变得更糟，因为它为每个图像创建了两个请求。

如果你的主机提供商根本不允许子域名，你总是可以选择购买更多的域名，纯粹用来托管资产——毕竟，这是很多大网站的做法。雅虎！使用域名 yimg.com，亚马逊有 images-amazon.com，SitePoint 有 sitepointstatic.com。如果你拥有几个站点，或者管理客户站点的托管，你可以考虑购买两个域名，比如 yourdomain-i1.com 和 yourdomain-i2.com，并用它们来托管你维护的所有站点的组件。

***将资产放在无 Cookie 的域中***

如果您设置了大量的 cookie，那么您的页面的请求头的大小将会增加，因为这些 cookie 是随每个请求一起发送的。此外，您的资产可能不使用 cookies，因此所有这些信息可能会被毫无理由地重复发送给客户端。有时，这些标题甚至可能比所请求的资产的大小还要大——当然，这些都是极端的情况，但这种情况确实会发生。考虑下载那些小于半 kB 的小图标或笑脸，并请求它们带有 1kB 的 HTTP 头。

如果您使用子域名托管您的资产，您需要确保您设置的 cookies 是针对您的规范域名(如 www.example.org)，而不是针对顶级域名(如 example.org)。这样，您的资产子域将是无 cookie 的。如果你试图提高现有网站的性能，并且你已经在顶级域名上设置了 cookies，你可以考虑在新域而不是子域上托管资产。

***在域间分割资产***

哪些资产托管在 i1.example.org，哪些资产托管在 i2.example.org，这完全由你决定——在这一点上没有明确的指示。只要确保不要在每个请求上随机化域，因为这将导致相同的资产被下载两次——一次从 i1，一次从 i2。

您可以根据文件大小或其他对页面有意义的标准来平均分割资产。您还可以选择将所有内容图像(包含在 HTML 中带有`<img />`标签的图像)放在 i1 上，将所有布局图像(由 CSS 的`background-image:url()`引用的图像)放在 i2 上，尽管在某些情况下这种解决方案可能不是最佳的。在这种情况下，浏览器将下载并处理 CSS 文件，然后，根据需要应用的规则，[将有选择地只下载样式表](http://www.phpied.com/smart-browsers-dont-download-unneeded-images/)需要的图像。结果是 CSS 引用的图像可能不会立即下载，因此资产服务器上的负载可能会不平衡。

决定分割资产的最佳方式是通过实验；您可以使用 [Firebug](http://www.getfirebug.com) 的网络面板来监控资产下载的顺序，然后决定您应该如何跨域分布组件以加快下载过程。

***在论坛和博客上配置 DNS 查找***

因为你的目标应该是每页不超过四次 DNS 查询，所以集成第三方内容(比如 Flickr 图片或第三方服务器上托管的广告)可能会很棘手。此外，热链接图像(通过在你的页面上放置一个`<img />`标签，其`src`属性指向另一个人的服务器上的文件)不仅会窃取其他网站的带宽，还会损害你自己页面的性能，导致额外的 DNS 查找。

如果你的网站包含用户生成的内容(比如论坛)，你就不能轻易地阻止多次 DNS 查询，因为用户可能会在网上的任何地方张贴图片。您可以编写一个脚本，将用户帖子中的每张图片复制到您的服务器上，但是这种方法可能会相当复杂。

瞄准低垂的果实。比如在 [phpBB 论坛软件](http://www.phpbb.com)中，你可以配置用户是否需要热链接自己的头像图片或者上传到你的服务器。在这种情况下，上传的头像会给你的网站带来更好的表现。

***使用`Expires`表头***

为了获得最佳性能，您的静态资产应该完全是静态的。这意味着不应该有动态生成的脚本或样式，或者指向生成动态图像的脚本的`<img>`标签。如果您有这样的需求——例如，您想生成包含访问者用户名的图形——动态生成可以“离线”进行，结果作为静态图像缓存。在本例中，您可以在会员注册时生成一次图像。然后，您可以将图像存储在文件系统中，并将图像的路径写入数据库中。另一种方法可能涉及调度一个自动进程(在 UNIX 中是 cron 作业),该进程生成动态组件并将它们保存为静态文件。

拥有完全静态的资产允许您将这些文件的`Expires`头设置为一个遥远的未来日期，这样当一个资产被下载一次时，它就被浏览器缓存，并且永远不会被再次请求(或者至少不会被请求很长时间，我们马上就会看到)。

在 Apache 中设置`Expires`头很简单:将包含以下指令的`.htaccess`文件添加到 i1 和 i2 子域的根文件夹中:

`ExpiresActive On
ExpiresDefault "modification plus 10 years"`

这些指令中的第一个允许生成`Expires`头。第二个选项将到期日期设置为文件修改日期后的 10 年，即您将文件复制到服务器后的 10 年。您还可以使用设置“access plus 10 年”，这将在用户第一次请求文件 10 年后使文件过期。

如果需要，您甚至可以为每种文件类型设置一个截止日期:

`ExpiresActive On
ExpiresByType application/x-javascript "modification plus 2 years"
ExpiresByType text/css "modification plus 5 years"`

有关更多信息，请查看`mod_expires` 上的 [Apache 文档。](http://httpd.apache.org/docs/2.0/mod/mod_expires.html)

***名称资产***

当您想要修改页面上的资产(比如图像)时，我们刚刚看到的技术(将`Expires`标题设置为很久以后的日期)会出现问题。如果您只是将更改后的图像上传到您的 web 服务器，新访问者会收到更新后的图像，但回头客不会。他们会看到旧的缓存版本，因为你已经指示他们的浏览器不再要求这个图像。

解决方案是修改资产的名称——但是这带来了一些维护上的障碍。例如，如果您有几个指向`img.png`的 CSS 定义，并且您修改了图像并将其重命名为`img2.png`，那么您必须定位样式表中引用了该文件的所有点，并更新这些点。对于更大的项目，您可以考虑编写一个工具来自动完成这项工作。

在命名资产时，您需要想出一个命名约定。例如，您可能:

*   在文件名后附加一个纪元时间戳，例如`img_1185403733.png`。
*   使用来自源代码控制系统的版本号(例如 cvs 或 svn)，例如`img_1.1.png`。
*   手动增加文件名中的数字(例如，当您看到名为`img1.png`的文件时，只需将修改后的图像保存为`img2.png`)。

这里没有一个正确的答案——你的决定将取决于你的个人偏好、你的网页的细节、项目和你的团队的规模等等。

如果您使用 CVS，这里有一个小 PHP 函数可以帮助您从存储在 CVS 中的文件中提取版本:

`function getVersion($file) {`

 `$ cmd = ' CVS log-h % s '；
$cmd = sprintf($cmd，$ file)；

exec($cmd，$ RES)；
$ version = trim(str _ replace(' head:'，''，$ RES[3])；

返回$ version
}` 

`// example use
$file = 'img.png';
$new_file = 'img_' . getVersion($file) . '.png';`

***提供 gzipped 内容***

大多数现代浏览器都理解 gzipped(压缩)内容，所以一个性能良好的页面应该致力于提供所有压缩内容。由于大多数图像、swf 文件和其他媒体文件已经被压缩，所以您不需要担心压缩它们。

但是，您确实需要负责提供压缩的 HTML、CSS、客户端脚本和任何其他类型的文本内容。如果您对返回 XML(或 JSON，或纯文本)的服务发出 XMLHttpRequests，请确保您的服务器也 gzips 这些内容。

如果您在 Firebug 中打开网络面板(或者使用 [LiveHTTPHeaders](http://livehttpheaders.mozdev.org/) 或其他一些数据包嗅探器)，您可以通过在响应中查找内容编码头来验证内容是否被压缩，如以下示例所示:

请求示例:

`GET /2.2.2/build/utilities/utilities.js HTTP/1.1
Host: yui.yahooapis.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.5) Gecko/20070713 Firefox/2.0.0.5
Accept-Encoding: gzip,deflate`

示例响应:

`HTTP/1.x 200 OK
Last-Modified: Wed, 18 Apr 2007 17:36:33 GMT
Vary: Accept-Encoding
Content-Type: application/x-javascript
Content-Encoding: gzip
Cache-Control: max-age=306470616
Expires: Sun, 16 Apr 2017 00:01:52 GMT
Date: Mon, 30 Jul 2007 21:18:16 GMT
Content-Length: 22657
Connection: keep-alive`

在这个请求中，浏览器通知服务器它理解 gzip 和 deflate 编码(`Accept-Encoding: gzip,deflate`)，服务器用 gzip 编码的内容(`Content-Encoding: gzip`)进行响应。

在提供 gzipped 内容时有一个问题:您必须确保代理不会妨碍您。如果一个 ISP 的代理缓存你的 gzip 内容并提供给它的所有客户，有可能某个浏览器不支持压缩的人会收到你的压缩内容。

为了避免这种情况，您可以使用`Vary: Accept-Encoding`响应头来告诉代理只为发送相同 Accept-Encoding 请求头的客户机缓存这个响应。在上面的例子中，浏览器说它支持 gzip 和 deflate，服务器用一些额外的信息来响应服务器和客户端之间的任何代理，说 gzip 编码的内容对于任何发送相同`Accept-Encoding`内容的客户端都是可以的。

这里还有一个额外的问题:一些浏览器(例如 IE 5.5，IE 6 SP 1)声称他们支持 gzip，但实际上在阅读它时会遇到问题(正如在[微软下载网站](http://www.microsoft.com/downloads/details.aspx?familyid=85BB441A-5BB1-4A82-86EC-A249AF287513&displaylang=en)和[支持网站](http://support.microsoft.com/kb/871205)上所描述的)。如果你关心使用这些浏览器的人(他们通常只占网站访问者的不到 1%)，你可以使用一个不同的标题——`Cache-Control: Private`——它完全消除了代理缓存。另一种防止代理缓存的方法是使用头`Vary: *`。

gzip 还是 Deflate？

如果你对浏览器发送的两个`Accept-Encoding`值感到困惑，可以把 deflate 看作是另一种对浏览器中不太流行的内容进行编码的方法。效率也比较低，所以首选 gzip。

***确保你发送 gzipped 内容***

好了，现在让我们看看你能做些什么来根据你的主机允许提供 gzipped 内容。

**选项 1:早于 2** 的 Apache 版本的 mod_gzip

如果您使用的是 Apache 1.2 和 1.3，那么 [mod_gzip 模块](http://sourceforge.net/projects/mod-gzip/)是可用的。要验证 Apache 版本，您可以检查 Firebug 的 Net 面板，并查找任何请求的服务器响应头。如果您看不到它，请检查您的提供商的文档或创建一个简单的 PHP 脚本来将此信息回显到浏览器，如下所示:

`<?php echo apache_get_version(); ?>`

在`Server`头签名中，您可能还能看到 mod_gzip 版本，如果安装了的话。它可能看起来像这样:

`Server: Apache/1.3.37 (Unix) mod_gzip/1.3.26.1a.....`

好了，我们已经确定，在将所有文本内容、PHP 脚本输出、静态 HTML 页面、JavaScripts 和样式表发送到浏览器之前，我们要对它们进行压缩。要用 mod_gzip 实现这一点，在您站点的根目录中创建一个包含以下内容的`.htaccess`文件:

`mod_gzip_on Yes`

 `mod _ gzip _ item _ include mime ^application/x-javascript$
mod _ gzip _ item _ include mime ^application/json$
mod _ gzip _ item _ include mime ^text/.*$

mod_gzip_item_include 文件 html$
mod_gzip_item_include 文件。php$
mod_gzip_item_include 文件。js$
mod_gzip_item_include 文件。css$
mod_gzip_item_include 文件。txt$
mod_gzip_item_include 文件。xml$
mod_gzip_item_include 文件。json$` 

`Header append Vary Accept-Encoding`

第一行启用 mod_gzip。接下来的三行根据 MIME-type 设置压缩。下一节做同样的事情，但是基于文件扩展名。最后一行将`Vary`头设置为包含`Accept-Encoding`值。

如果您想发送`Vary: *`报头，请使用:

`Header set Vary *`

注意，一些主机提供商不允许你使用`Header`指令。如果是这种情况，希望您能够用这一行替换最后一行:

`mod_gzip_send_vary On`

这也将把`Vary`标题设置为`Accept-Encoding`。

请注意，gzip 上可能有一个最小大小的条件，所以如果您的文件太小(例如小于 1kb)，即使您已经正确配置了所有内容，它们也可能不会被 gzip。如果出现这个问题，您的主机认为 gzipping 进程开销对于非常小的文件是不必要的。

**选项 Apache 2.0 的 mod _ deflate】**

如果您的主机运行 Apache 2，您可以使用 mod_deflate。尽管名字如此，mod_deflate 也使用 gzip 压缩。要配置 mod_deflate，请将以下指令添加到您的`.htaccess`文件中:

`AddOutputFilterByType DEFLATE text/html text/css text/plain text/xml application/x-javascript application/json
Header append Vary Accept-Encoding`

**选项三:`php.ini`**

理想情况下，我们希望 Apache 能够处理内容的压缩，但不幸的是，一些主机提供商可能不允许这样做。如果你的主机提供商是其中之一，它可能允许你使用自定义的`php.ini`文件。如果您将一个`php.ini`文件放在一个目录中，它会覆盖这个目录及其子目录的 PHP 配置设置。

如果您不能使用 Apache 的 mod_gzip 或 mod_deflate 模块，您仍然可以使用 PHP 压缩您的内容。为了让这个解决方案工作，您必须配置您的 web 服务器，以便所有静态 HTML、JavaScript 和 CSS 文件都由 PHP 处理。这意味着服务器会有更多的开销，但是根据您的主机，这可能是您唯一的选择。

在您的`.htaccess`文件中添加以下指令:

`AddHandler application/x-httpd-php .css
AddHandler application/x-httpd-php .html
AddHandler application/x-httpd-php .js`

这将确保 PHP 将处理这些(否则是静态的)文件。如果不起作用，您可以尝试重命名文件，使其具有`.php`扩展名(如`example.js.php`，等等)，以达到相同的结果。

现在在同一个目录下创建一个`php.ini`文件，内容如下:

`[PHP]
zlib.output_compression = On
zlib.output_compression_level = 6
auto_prepend_file = "pre.php"
short_open_tag = 0`

这将启用压缩并将压缩级别设置为 6。压缩级别的值范围从 0 到 9，其中 9 是最佳(也是最慢)压缩。最后一行设置了一个名为`pre.php`的文件，它将在每个脚本的开头执行，就好像您在每个脚本的开头输入了`<?php include "pre.php"; ?>`。你将需要这个文件来设置`Content-Type`头，因为当你发送一个 CSS 文件时，有些浏览器可能不喜欢它，例如，有一个`text/html`内容类型头。

`short_open_tag`设置用于禁用 PHP 短标签(`<? ... ?>`，与`<?php ... ?>`相比)。这很重要，因为 PHP 会试图将 HTML 中的`<?xml`标签视为 PHP 代码。

最后，创建包含以下内容的文件`pre.php`:

`<?php`

 `$ path = pathinfo($ _ SERVER[' SCRIPT _ NAME '])；

if($ path[' extension ']= ' CSS '){
header(' Content-type:text/CSS ')；
}` 

`if ($path['extension'] == 'js')  {
header('Content-type: application/x-javascript');
}
?>`

该脚本将在每个文件扩展名为`.php`、`.html`、`.js`或`.css`的文件之前执行。对于 HTML 和 PHP 文件，默认的`Content-Type text/html`是可以的，但是对于 JavaScript 和 CSS 文件，我们使用 PHP 的 header 函数来改变它。

**选项 3(变式 2)`.htaccess`**中的 PHP 设置

如果您的主机允许您在您的`.htaccess`文件中设置 PHP 设置，那么您不再需要使用`php.ini`文件来配置您的压缩设置。相反，使用`php_value`(和`php_flag`)在`.htaccess`中设置 PHP 设置。

从上面修改过的例子来看，我们会有相同的`pre.php`文件，没有`php.ini`文件，并且修改过的`.htaccess`包含以下指令:

`AddHandler application/x-httpd-php .css
AddHandler application/x-httpd-php .html
AddHandler application/x-httpd-php .js
php_flag zlib.output_compression on
php_value zlib.output_compression_level 6
php_value auto_prepend_file "pre.php"
php_flag short_open_tag off`

**选项 4:脚本内压缩**

如果你的主机提供商不允许你在你的`.htaccess`文件中使用`php_value`，也不允许你使用自定义的`php.ini`文件，你最后的办法就是修改脚本，手动包含通用的`pre.php`文件来负责压缩。这是最不喜欢的选择，但有时你可能没有其他选择。

如果这是您唯一的选择，您要么使用包含上面选项 3 中概述的指令的`.htaccess`文件，要么必须重命名每个`.js`和`.css`文件(以及`.xml`、`.html`等)。)有一个`.php`扩展名。在每个文件的顶部，添加`<?php include "pre.php"; ?>`并创建一个名为`pre.php`的文件，该文件包含以下内容:

`<?php
ob_start("ob_gzhandler");`

 `$ path = pathinfo($ _ SERVER[' SCRIPT _ NAME '])；

if($ path[' extension ']= ' CSS '){
header(' Content-type:text/CSS ')；
}` 

`if ($path['extension'] == 'js')  {
header('Content-type: application/x-javascript');
}
?>`

正如我所指出的，这是所有选项中最不可取的—您应该首先尝试选项 1 或 2，如果它们不起作用，则考虑选项 3 或 4，或者两者的组合，这取决于您的主机允许什么。

一旦建立了主机允许的自由度，就可以使用压缩静态文件的技术来实现所有与 Apache 相关的设置。例如，前面我向您展示了如何设置 Expires 头。你猜怎么着？有些主机不允许。如果您发现自己处于这种情况，您可以使用 PHP 的 header 函数从您的 PHP 脚本中设置`Expires`头。

为此，您可以在您的`pre.php`文件中添加如下内容:

`<?php
header("Expires: Mon, 25 Dec 2017 05:00:00 GMT");
?>`

***禁用 ETags***

与执行上述规则时可能遇到的潜在麻烦相比，应用这条规则非常容易。您只需要将以下内容添加到您的`.htaccess`文件中:

`FileETags none`

请注意，此规则适用于服务器场中的站点。如果您使用共享主机，您可以跳过这一步，但我建议您还是这样做，因为:

*   主机为了内部目的而改变它们的机器。
*   你可以换主持人。
*   太简单了。

***使用 CSS 精灵***

使用一种称为 CSS sprites 的技术，您可以将几个图像组合成一个图像，然后使用 CSS 背景位置属性只显示您需要的图像。该技术并不打算用于内容图像(那些出现在 HTML 的`<img />`标签中的图像，比如照片库中的照片)，而是打算用于装饰性和修饰性图像。这些图像不会影响页面的基本可用性，通常从样式表中引用，以保持 HTML 简洁(规则#0)。

让我们看一个例子。我们要拍两张照片。第一个是`help.png`；第二个是`rss.png`。从这些，我们将创建第三个图像，`sprite.png`，包含这两个图像。

![Combining two image files into a single image](img/f3160a0b64db165210f4f941a2003509.png)

结果图像的大小通常小于两个文件大小的总和，因为与图像文件相关的开销只包含一次。为了显示第一张图片，我们使用下面的 CSS 规则:

`#help {
background-image: url(sprite.png);
background-position: -8px -8px;
width: 16px;
height: 16px;
}`

为了显示第二个图像，我们将使用以下规则:

`#rss {
background-image: url(sprite.png);
background-position: -8px -40px;
width: 16px;
height: 16px;
}`

乍一看，这种技术可能看起来有点奇怪，但是它对于减少 HTTP 请求的数量非常有用。以这种方式组合的图像越多越好，因为您大大减少了请求开销。关于这种技术在“野外”使用的例子，请看这张图片。的首页，或者[这个来自谷歌的](http://www.google.cimg/nav_logo3.png)。

为了快速生成精灵图像，无需计算像素坐标，请随意使用我开发的 [CSS 精灵生成器](http://www.csssprites.com/)工具。关于 CSS 精灵的更多信息，请务必阅读戴夫·谢伊的文章，标题是 [CSS 精灵:图像切片的死亡之吻](http://www.alistapart.com/articles/sprites)。

**使用后加载预加载和内联资产**

如果你是一个负责任的 web 开发人员，你可能已经在坚持[关注点分离](https://www.sitepoint.com/article/simply-javascript/)并且使用 HTML 作为你的内容，CSS 作为表现，JavaScript 作为行为。虽然页面的这些不同部分应该始终保存在单独的文件中，但出于性能原因，有时您可能会考虑打破索引(主页)页面上的规则。主页应该永远是你网站上速度最快的页面——如果许多第一次访问你的网站的人发现主页加载速度很慢，那么不管网站包含什么内容，他们都可能会离开。

当访问者带着一个空的缓存到达你的主页时，交付页面的最快方式是只有一个请求，没有单独的组件。这意味着要有内嵌的脚本和样式(gasp)！实际上也可以有内嵌图片(尽管 IE 不支持)，但这可能有点过了。除了语义不正确之外，使用内联脚本和样式还会阻止这些组件被缓存，因此一个好的策略是在主页加载后在后台加载组件——这种技术的名称有点令人困惑，叫做后加载预加载。让我们看一个例子。

让我们假设包含您的主页的文件被命名为`home.html`，包含内容的许多其他 HTML 文件分散在您的站点中，并且所有这些内容页面都使用一个 JavaScript 文件`mystuff.js`，其中只有一小部分是主页所需要的。

你的策略可能是将主页使用的 JavaScript 部分从`mystuff.js`中取出，放入`home.html`中。然后，一旦`home.html`完成加载，就在后台请求预加载`mystuff.js`。这样，当用户点击您的内容页面时，JavaScript 就已经被发送到浏览器并被缓存了。

这种技术再次被一些大公司使用:谷歌和雅虎！在他们的主页上有内嵌的脚本和样式，他们还利用了后加载预加载。如果你访问谷歌的主页，它会加载一些 HTML 和一个单独的图像——徽标。然后，一旦主页完成加载，就有一个获取[子画面图像](http://www.google.cimg/nav_logo3.png)的请求，在第二个页面加载之前，实际上并不需要这个子画面图像——显示搜索结果的页面。

[雅虎搜索页面](http://search.yahoo.com/)执行条件预加载——该页面不会自动加载额外的资产，而是等待用户开始在搜索框中键入内容。一旦你开始输入，几乎可以保证你会提交一个搜索查询。当您这样做时，您将进入一个搜索结果页面，其中包含一些已经为您缓存的组件。

预加载图像可以通过一行简单的 JavaScript 代码来完成:

`new Image().src='image.png';`

为了预加载 JavaScript 文件，使用 JavaScript [include_DOM 技术](http://www.phpied.com/javascript-include/)并创建一个新的`<script>`标签，如下所示:

`var js = document.createElement('script');
js.src = 'mysftuff.js';
document.getElementsByTagName('head')[0].appendChild(js);`

下面是 CSS 版本:

`var css  = document.createElement('link');
css.href = 'mystyle.css';
css.rel  = 'stylesheet';
document.getElementsByTagName('head')[0].appendChild(css);`

在第一个例子中，图像被请求但从未被使用，所以它不影响当前页面。在第二个示例中，脚本被添加到页面中，因此在下载的同时，它将被解析和执行。CSS 也是如此——它也将应用于页面。如果不希望这样，您仍然可以使用 XMLHttpRequest 预加载资产。

##### JavaScript 优化

在深入 JavaScript 代码并对每个函数和每个循环进行微优化之前，让我们先来看看我们可以轻松处理哪些可能对网站性能产生重大影响的大项目。以下是一些改善 JavaScript 文件对网站性能影响的指导原则:

1.  合并`.js`文件。
2.  缩小或混淆脚本。
3.  将脚本放在页面底部。
4.  删除重复项。

***合并`.js`文件***

根据基本规则，您应该让您的 JavaScripts 发出尽可能少的请求；理想情况下，这也意味着您应该只有一个`.js`文件。这个任务非常简单，只需将所有的`.js`脚本文件放到一个文件中。

虽然在大多数情况下推荐使用单文件方法，但有时使用两个脚本可能会有一些好处——一个脚本用于页面加载后立即需要的功能，另一个脚本用于可以等待页面首先加载的功能。另一种可能需要两个文件的情况是，当您的站点跨多个页面使用一项功能时，共享脚本可以存储在一个文件中(因此可以从一个页面缓存到另一个页面)，特定于该页面的脚本可以存储在第二个文件中。

 *现在你已经合并了你的脚本，你可以继续缩小或混淆它们。缩小意味着删除所有不必要的东西——比如注释和空白。模糊处理更进一步，包括重命名和重新排列函数和变量，使它们的名字更短，使得脚本很难阅读。模糊处理通常被用作保持 JavaScript 源代码秘密的一种方式，尽管如果你的脚本可以在网上找到，它也不可能是 100%秘密的。在[道格拉斯·克洛克福特关于主题](http://yuiblog.com/blog/2006/03/06/minification-v-obfuscation/)的有益文章中阅读更多关于缩小和混淆的内容。

一般来说，如果您 gzip JavaScript，您已经在文件大小上取得了巨大的收益，并且您将仅仅通过缩小和/或混淆脚本获得一点额外的好处。平均而言，单独使用 gzipping 可以节省 75-80%，而 gzipping 和 minifying 可以节省 80-90%。此外，当你改变你的代码，以缩小或混淆，有一个风险，你可能会引入错误。如果你不太担心有人窃取你的代码，你可能会忘记混淆，只是合并和缩小，甚至只是合并你的脚本(但总是 gzip 他们！).

一个优秀的 JavaScript 缩小工具是 [JSMin](http://www.crockford.com/javascript/jsmin.html) ，它还有一个 [PHP 端口](http://code.google.com/p/jsmin-php/)，等等。一个混淆工具是[打包器](http://dean.edwards.name/packer/)——一个免费的在线工具，顺便提一下，它被 [jQuery](http://www.jquery.com) 使用。

在开发网站的过程中，为了合并和缩小而修改代码应该成为一个额外的、独立的步骤。在开发过程中，你应该使用你认为合适的尽可能多的`.js`文件，然后当网站准备上线时，用合并和缩小的版本替换你的“普通”脚本。你甚至可以开发一个工具来帮你做这件事。下面，我提供了一个小工具的例子。这是一个命令行脚本，使用 JSMin 的 PHP 端口:

`<?php
include 'jsmin.php';
array_shift($argv);`

`foreach ($argv AS $file) {
echo '/* ', $file, ' */';
echo JSMin::minify(file_get_contents($file)), "n";
}
?>`

很简单，不是吗？您可以将其保存为`compress.php`并按如下方式运行:

`$ php compress.php source1.js source2.js source3.js > result.js`

这将把文件`source1.js`、`source2.js`和`source3.js`合并并缩小成一个文件，称为`result.js`。

当您将合并和缩小作为站点部署过程中的一个步骤时，上面的脚本非常有用。另一个更慵懒的选择是在飞机上做同样的事情——看看埃德·艾略特的博客文章和 T2 的博客文章。

许多第三方 JavaScript 库都是以未压缩的形式和缩小的版本提供的。因此，您可以下载并使用库的创建者提供的缩小版本，然后只需担心您自己的脚本。要记住的是你使用的任何第三方库的许可。即使您可能已经合并并缩小了所有的脚本，您仍然应该在代码旁边保留每个库的版权声明。

***在页面底部放置脚本***

关于 JavaScript 优化要遵循的第三条经验法则是，脚本应该放在页面的底部，尽可能靠近结束标签`</body>`。原因？嗯，由于脚本的性质(它们可能会改变页面上的任何内容)，浏览器在遇到`<script>`标签时会阻止所有下载。因此，在下载并解析脚本之前，不会启动其他下载。

将脚本放在底部是避免这种负面阻塞效应的一种方法。使用尽可能少的`<script>`标签的另一个原因是浏览器会为它遇到的每个脚本启动它的 JavaScript 解析引擎。这可能会很昂贵，因此解析最好每页只发生一次。

***删除重复***

关于 JavaScript 的另一个准则是避免两次包含同一个脚本。这听起来像是奇怪的建议(你为什么要这么做？)但这种情况时有发生:例如，如果一个大型网站使用了多个包含 JavaScript 文件的服务器端 include，可以想象其中两个可能会重叠。重复的脚本会导致浏览器的解析引擎启动两次，甚至可能(在某些 IE 版本中)第二次请求文件。当您使用第三方库时，重复的脚本也可能是一个问题。假设您从不同的站点下载了一个 carousel 小部件和一个照片库小部件，它们都使用 jQuery。在这种情况下，您需要确保没有错误地包含 jQuery 两次。此外，如果你使用 [YUI](http://developer.yahoo.com/yui/articles/hosting/) ，确保你没有包含一个库两次，例如，通过包含 DOM 实用程序(`dom-min.js`)、事件实用程序(`event-min.js`)和包含 DOM 和事件的`utilities.js`库。

##### CSS 优化

***合并和缩小***

对于你的 CSS 文件，你可以遵循我们讨论过的 JavaScripts 的指导方针:将所有的样式表缩小并合并到一个文件中，以最小化下载的大小和 HTTP 请求的数量。将所有文件合并成一个文件是一件小事，但缩小的工作可能有点困难，特别是如果你使用 CSS 黑客针对特定的浏览器——因为一些黑客利用浏览器中的解析错误，他们也可能欺骗你的 minifier 实用程序。

您可能决定不经历缩小样式表的麻烦(以及缩小后相关的重新测试)。毕竟，如果你决定提供合并和压缩的样式表，这已经是一个很好的优化了。

如果你决定缩小 CSS，除了选择手动缩小(简单地删除注释和空白)，你可以使用一些可用的工具，如 [CSSTidy](http://csstidy.sourceforge.net/) ，PEAR 的 HTML_CSS 库(http://PEAR . PHP . net/package/HTML _ CSS/)，或 SitePoint 自己的 [Dust-me Selectors Firefox 插件](https://www.sitepoint.com/dustmeselectors/)。

***在页面顶部放置样式***

您的单个、gzipped(可选地缩小)样式表最好放在 HTML 文件的开头，在`<head>`部分——这是您通常会放它的地方。原因是大多数浏览器(Opera 是个例外)不会在页面上呈现任何东西，直到所有的样式表都被下载和解析。此外，除非 CSS 解析完成，否则不会下载 CSS 引用的任何图像。所以最好在页面的最前面包含 CSS。

不过，您可能会考虑跨不同的域分发图像。从 CSS 链接的图像直到稍后才会被下载，因此在此期间，您的页面可以使用可用的下载窗口从承载 CSS 图像并且暂时“空闲”的域请求内容图像。

***班表情***

IE 允许在 CSS 中使用 JavaScript 表达式，如下所示:

`#content {
left: expression(document.body.offsetWidth)
}`

出于多种原因，您应该避免使用 JavaScript 表达式。首先，并不是所有的浏览器都支持它们。它们也损害了“关注点分离”。而且，当涉及到性能时，表达式是不好的，因为每当页面被渲染或调整大小时，或者只是当您将鼠标滚动到页面上时，表达式都要重新计算。有[种方法可以让表达式变得更便宜](http://webfx.eae.net/dhtml/cssexpr/cssexpr.html)——你可以在最初计算后缓存值，但是你最好还是避免使用它们。

##### 性能优化工具

许多工具可以帮助您实现性能优化。最重要的是，您希望监视页面加载时发生的情况，以便做出明智的决策。尝试这些实用程序:

*   Firebug 的火狐网络面板，在[http://www.getfirebug.com](http://www.getfirebug.com)
*   YSlow，雅虎！的性能扩展到 Firebug，在[http://developer.yahoo.com/yslow/](http://developer.yahoo.com/yslow/)
*   火狐浏览器的 LiveHTTP Headers，位于[http://livehttpheaders.mozdev.org/](http://livehttpheaders.mozdev.org/)
*   fiddler——用于 IE，也是一个通用的数据包嗅探器，在[http://www.fiddlertool.com/fiddler/](http://www.fiddlertool.com/fiddler/)
*   在[http://www.httpwatch.com/](http://www.httpwatch.com/)观看 IE(商业，免费版本)
*   Safari 的网页检查器，位于[http://webkit.org/blog/?p=41](http://webkit.org/blog/?p=41)

##### 摘要

咻！如果您已经做到了这一步，那么您现在已经非常了解如何进行站点优化项目(更重要的是，如何在考虑性能的情况下构建您的下一个网站)。记住一般的经验法则，当涉及到优化时，你应该专注于影响最大的项目，而不是“微优化”。

你可以选择不实施上面讨论的所有建议，但是你仍然可以通过关注真正容易实现的目标来做出很大的改变，例如:

*   通过组合组件——JavaScript 文件、样式表和图像(通过使用 CSS 精灵),减少 HTTP 请求
*   以 gzipped 格式提供所有文本内容，包括 HTML、脚本、样式、XML、JSON 和纯文本
*   缩小并把脚本放在底部，样式表放在文件的顶部
*   为您的组件使用单独的无 cookie 域

祝您的优化工作好运——当您看到结果时，会非常有收获！* 

## *分享这篇文章*