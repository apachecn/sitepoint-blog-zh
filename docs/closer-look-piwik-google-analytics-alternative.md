# 近距离观察 Piwik:谷歌分析的替代方案

> 原文：<https://www.sitepoint.com/closer-look-piwik-google-analytics-alternative/>

Jacco Blankenspoor 最近在 SitePoint 上发表的一篇文章介绍了一些无处不在的 Google Analytics 的替代方案。其中有[Piwik](http://piwik.org/)——一个自托管的开源包，Jacco 称之为“有力的竞争者”。在本文中，我将更详细地研究 Piwik，以评估这种说法。

## 为什么使用 Piwik？

或许有必要先将 Piwik 与无可争议的市场领导者 Google Analytics 进行比较。

谷歌一直是一个[数字](http://www.theguardian.com/technology/2014/jan/10/privacy-concerns-raised-as-google-makes-it-possible-to-send-email-via-name-search)[隐私](http://www.usatoday.com/story/tech/2014/01/16/google-acquires-nest-privacy/4518317/) [担忧](http://www.france24.com/en/20140109-france-fines-google-maximum-penalty-privacy-row/) [最近](http://www.examiner.com/article/google-raises-privacy-issues-with-its-gmail-announcement)的来源，导致他们对如何处理你的数据感到不安。有了 Piwik，就没有这样的担心，因为数据归你所有。你也可以放心，这些数据不会与第三方共享，尤其是广告商，除非你选择这样做。

Piwik 是非常可定制的；仪表板——Piwik 的默认视图——由许多“小部件”组成，这些小部件可以换入或换出，或者根据您的喜好重新排列。

![Piwik's Dashboard](img/02872ea30b1d2d8b5326a08a3deddc2f.png)

您可以随心所欲地修改 Piwik 的外观——从上传徽标到品牌化，到调整配色方案——一直到从头开始构建您自己的主题。

因为 Piwik 是自托管的，所以您还可以指定它所在的域。

Piwik 拥有这类服务的大部分功能，包括实时分析、访客地图、目标、活动跟踪、推荐信息和 JavaScript 跟踪 API。

对于那些在欧盟的人来说，另一个关键优势是它符合[有争议的欧盟 cookie 法](https://www.sitepoint.com/perhaps-your-site-isnt-illegal-in-europe/)，所以如果 GA 是你目前显示 Cookie 通知的唯一原因，那么切换到 Piwik 意味着你可能不必这样做(免责声明:这是一个非常灰色的领域，所以你可能应该做必要的研究并咨询当地的律师)。

Piwik 是可扩展的；甚至还有一个专门面向第三方插件的市场(可从设置页面访问)，或者你可以自己编写。因为它是开源的，你甚至可以修改内核。

## 装置

Piwik 是一个托管的 web 应用程序，所以您需要将它上传到某个服务器上。把它放在哪里并不重要，尽管把它放在子域上可能更有意义，例如`analytics.example.com`。

[下载 Piwik](http://piwik.org/download/) 并上传至您的服务器。或者，使用命令行:

```
wget http://builds.piwik.org/latest.zip
unzip latest.zip
cp -R piwik /var/www/analytics.example.com
```

许多目录需要可由 web 服务器写入:

```
/tmp
/tmp/assets/
/tmp/cache/
/tmp/logs/
/tmp/tcpdf/
/tmp/templates_c/
```

接下来，您需要为它创建一个 MySQL 数据库。Piwik 使用的数据库用户需要读、写和修改模式的权限。

设置好虚拟主机后，在浏览器中导航到它，开始设置向导。

向导将首先列出依赖项，并检查它们是否都满足。它会要求您提供数据库连接的详细信息，然后要求您创建一个“超级用户”帐户。接下来，它会要求你添加你的第一个网站。此时，您需要给站点一个名称，提供它的 URL，指定城市(用于计算时区)以及它是否是一个电子商务站点。您可以稍后添加更多网站。

接下来，它将为您生成并显示 JavaScript 跟踪代码；如果你使用的是插件，你可能不需要它，但是你可以简单地把它粘贴到你网站的页面模板中。完成安装后，它会要求您使用刚刚创建的超级用户帐户登录。

接下来，我们将开始研究 Piwik 的一些功能领域。

## 目标

目标是用来衡量一个网站在商业目标方面的有效性。您可以从访问者那里追踪一些目标示例:

*   在网站上注册
*   购物
*   订阅时事通讯
*   使用联系表格进行查询
*   下载手册或其他文档

要添加目标，请转到“目标”，然后选择“添加新目标”。首先给目标起一个名字，例如“下载的手册”。接下来，您可以设置基于用户访问页面、下载文件或单击链接而触发的目标。当人们登陆确认页面时，触发目标是很常见的，例如，当你完成一个联系表单时，旁边会出现一条感谢信息。

接下来，决定这是一个“一次性”目标，还是多次触发该目标有意义。这真的取决于你的商业目标和目标本身；订阅时事通讯可能是一次性的目标，而购买可能是多次的。

考虑使用表达式而不是精确的 URLs 否则它可能匹配`http://example.com/newsletter/thanks`，但不匹配`https://example.com/newsletter/thanks`或`http://www.example.com/newsletter/thanks`(注意“https”和“www”)。然而，`(.*)/newsletter/thanks`将匹配所有这些。

您可能希望将目标小部件添加到仪表板中。浏览至控制面板，单击“Widgets & Dashboard”按钮，将鼠标悬停在“Goals”上，然后单击“Goals Overview”。小部件应该出现在您的仪表板上，现在您可以将它拖到页面上您喜欢的位置。

![Adding a Widget to Piwik's Dashboard](img/fbc9b781b9a3ffbae395a1850e9e5100.png)

## 跟踪活动

任何营销活动的一个重要部分是衡量其有效性。当你的活动旨在引导人们访问你的网站时，你可以使用跟踪代码来记录它。

要使用 Piwik 做到这一点，首先要在相关网站的 URL 中加入一个参数，例如，当您在促销电子邮件或推文中链接到该网站时。您可以随意调用该参数，并且该值没有固定的格式——只要它对您以后有意义。这里有几个例子:

`http://www.example.com?campaign=email_march_2014`
`http://www.example.com?source=twitter`


你也可以使用标签作为你的追踪代码，例如

`http://www.example.com#campaign=email_march_2014`

这种方法的优点是你不会因为有重复的 URL 而被搜索引擎惩罚。

要查看您的营销活动统计数据，请在 Piwik 中单击主导航中的推荐人，然后单击营销活动。您还可以将一个小部件添加到您的仪表板:小部件和仪表板->推荐人->活动。

## 地理定位

默认情况下，Piwik 试图根据访问者的语言设置猜测他们在哪里。这是一个有缺陷的方法，然而，这很可能会扭曲你的地理分析数据。

![Piwiki tracks the geographic location of your site's visitors](img/ba24e5f62c5e57111761e703d5cf449d.png)

为了提高这些信息的质量，从 Maxmind 下载 Geolite City 文件[，然后将其复制到 Piwik 的`misc`目录。](http://dev.maxmind.com/geoip/legacy/geolite/)

要在命令行上实现这一点:

```
wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
gunzip GeoLiteCity.dat.gz
cp GeoLiteCity.dat /var/www/analytics.example.com/misc/
```

然后，进入设置->地理定位，选择“GeoIP (Php)”。在该页面上，您还可以看到与基于您的语言设置的默认方法相比，该方法得出的关于您所处位置的结论。虽然不容易，但您可能会发现 GeoIP 方法要精确得多。

为了更好的地理定位，你可以购买高级地理数据库，你甚至可以设置 Piwik 来自动更新数据库。

## 网站和高级网站设置

如果您选择“设置”(右上角)，然后选择“网站”(在左侧的“管理”下)，您将获得一个您已配置的网站列表，您可以在那里添加更多网站。

如果您点按网站旁边的“编辑”,您还可以访问一些附加设置，其中一些设置也可以作为全局设置使用。以下是其中的几个。

### 排除的 IP

您可以将某些 IP 地址排除在跟踪范围之外。当您或其他 analytics 用户(例如，您的客户)拥有静态 IP 地址或范围，并且您希望从报告中排除他们的访问时，这非常有用。

### 排除的参数

一些 URL 参数，如会话 id，与跟踪无关。许多，比如`phpsessid`和`sessionid`，被自动删除，但是如果驱动一个特定站点的系统使用其他东西，你可以在这里设置。

### 网站搜索

你也可以在网站上追踪搜索。只需指定搜索参数(如`q`、`query`或`search`)，Piwik 就会选择。(要找出您的站点使用的参数，要么在搜索后检查 URL，要么检查搜索框的`name`属性。)

### 用户

您可以添加任意数量的用户帐户，并单独控制他们对网站的访问。转到“设置”->“用户”以添加新用户、编辑现有用户或管理他们的权限。

*注意:如果您想知道，token_auth 字段用于 API 调用。*

## JavaScript 应用程序

如果您的应用程序使用 Ajax 页面转换，或者如果您使用 Angular JS、Ember 或 Backbone 等前端框架，您可能需要 [JavaScript API](http://developer.piwik.org/api-reference/tracking-javascript) 。除了能够以编程方式记录页面视图，您还可以传递[自定义变量](http://piwik.org/docs/custom-variables/)。

例如，要跟踪 JavaScript 事件的页面视图:

```
<a href="About" onclick="javascript:_paq.push(['trackPageView', 'About']);">About</a>
```

或者使用 jQuery:

```
$('nav a').click(function(){
  _paq.push(['trackPageView', $(this).data('title')]);
});
```

要传递自定义变量:

```
_paq.push(['setCustomVariable','1','VisitorType','Member']);
```

您还可以使用 JavaScript 来跟踪目标:

```
_paq.push(['trackGoal', 1]);
```

您还可以跟踪目标和特定收入。例如，在订单确认页面上，您可以这样做，使用 PHP 将相关信息注入页面:

```
_paq.push(['trackGoal', 1, <?php echo $order->getTotal(); ?>]);
```

对于 [analytics.js](https://github.com/segmentio/analytics.js) 用户，Github 上有一个支持 Piwik [的 fork。](https://github.com/charlesportwoodii/analytics.js)

## 集成

Piwik 有许多可用的[集成，例如](http://piwik.org/integrate/) [Drupal](http://drupal.org/project/piwik) 、 [WordPress](http://wordpress.org/extend/plugins/wp-piwik/) 、 [Magento](http://www.magentocommerce.com/magento-connect/catalog/product/view/id/13253/) 、 [Silex](http://exchange.silexlabs.org/?p=977) 、 [Laravel](https://github.com/RobBrazier/Laravel_Piwik) ，以及 PHP 的[客户端](http://piwik.org/docs/analytics-api/calling-techniques/#toc-call-the-piwik-api-in-php)和[跟踪](http://piwik.org/docs/tracking-api/) API。

## 移动的

Piwik 为苹果手机和 T2 安卓手机提供移动应用。

## 进一步的信息

Youtube 上有很多视频向你介绍 Piwik [的几个方面。](http://www.youtube.com/user/PiwikAnalytics?feature=watch)

## Piwik 的缺点

自托管引入了自身的缺点。设置 Piwik 需要时间、精力和技术知识。它使用服务器资源，并且由您来管理任何停机时间。

不可避免的还有一个学习曲线，所以转换客户可能会引起一些摩擦。当然，当您或您的用户熟悉它时，没有什么可以阻止您将它与您当前的解决方案一起运行。

## 摘要

Google Analytics 与自托管网络分析解决方案 Piwik 存在激烈竞争。对我来说，控制我或我的客户自己的数据的好处——加上对谷歌隐私政策的一些非常真实的担忧——远远超过了自我托管的缺点。

不过，这是一个很主观的原因，我就留给你自己决定了。但我很想听听大家在评论里的看法。

## 分享这篇文章