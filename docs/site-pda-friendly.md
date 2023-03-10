# 让你的网站对 PDA 友好

> 原文：<https://www.sitepoint.com/site-pda-friendly/>

*****警告！***这篇文章有点尘封，有些链接可能不再起作用。我们建议您查看以下文章，了解关于构建针对移动设备优化的网站的更多最新信息:[为移动网络设计](https://www.sitepoint.com/article/designing-for-mobile-web/)。**

如果你想让 PDA(个人数字助理)用户可以使用你的网站内容，小是好的。尽管 PDA 的硬件和软件能力无法与台式计算机相提并论，但你也不需要满足于一个贫血的掌上型网站。通过仔细的规划和巧妙的编码，你可以创建一个优雅的 PDA 友好型网站。

现在，你可以以最小的风险和努力加入那些处于危险边缘的人。我将重点介绍如何使用 AvantGo 作为交付机制，尽管这里的大部分信息可以应用于其他 PDA 浏览器解决方案(见下文)。

AvantGo 为用户提供免费的浏览器和同步软件。对于开发者来说，有一个快速的在线工具，你可以用它来建立一个 AvantGo 频道，用户可以订阅。总而言之，AvantGo 确实是 PDA 的杀手级应用。

##### 决定发布什么

你的第一步是决定你将向 PDA 装备的观众提供什么。从小处着手，有选择性。新闻故事、文章、产品描述和订单都是这个渠道及时发送的好对象。

你想要一个简单的 2-3 级导航，如主菜单，子菜单(可选)和内容页面。例如，我们的网站联合频道包括两个层次——文章的主菜单和文章本身。

将页面的总大小保持在 50-100KB 之间。AvantGo 强制执行每个通道 256KB 和每页 32KB 的限制。页面应该简短，仅限于一个主题。

##### 编码基础

AvantGo 支持 HTML 3.2，但也有例外。忘记使用框架和图像地图。高度、宽度和文本浮动等图像标签属性也不会浮动。文本格式功能有限。`FONT`和`BASEFONT`标签是没有用的。

牢记屏幕尺寸。你可以设计最小公分母的页面(Palm 的 150 x 150 可用屏幕)。或者，您可以使用 HTTP 头(如下所述)来调整您的内容，以适应 Windows CE 和 HPC Pro 设备的大屏幕。

由于 PDA 的图形能力有限，最安全的做法是使用宽度为 150 像素或更小的高对比度灰度图像。非彩色设备会将彩色图像转换为灰度图像。确保使用 alt 标签。

有关这些主题的更多详细信息，请访问 [AvantGo 开发人员指南](https://avantgo.com/developer/reference/)。

##### 使用 HTTP 头

一旦你确定了你的频道的目标内容，你就面临着为 PDA 进行优化的挑战。您可以为标准浏览器和 PDA 创建不同的版本，但是有经验的开发人员可能更喜欢使用 JavaScript、PHP 或其他脚本工具来动态优化页面。使用 HTTP 头来确定客户端的操作系统、屏幕大小、颜色功能等。

对于我们的通道，我们使用 PHP 只是为了判断用户的浏览器是否是“AvantGo”。如果是的话，一个包含一个小图片和一些导航链接的 HTML 的精简头文件(PDA 头文件)被调用，而不是我们的普通网站的访问者看到的完整的头文件(完整的头文件):

```
<?php

  $agent = getenv("HTTP_USER_AGENT");

  $handheld = strpos($agent, "AvantGo") ? "Yes" : "No" ;

  if($handheld == "Yes")

  {

  include("../PDA_header.inc");

  } else {

  include("../FULL_header.inc");

  }

?>
```

访问[这一页](https://avantgo.com/developer/reference/tutorials/tipsntricks/tipsntricks.html#TOC1/)获得关于使用 HTTP 头的完整独家新闻。

##### 创建您的内容

设计一个小的主页，让访问者清楚地知道下面有什么。主菜单很好。第二和较低级别的内容可以从头开始创建，或者可以使用如上所述的 HTTP 头编写现有页面的脚本，以消除对单独的 PDA 友好版本的需要。

将主页文件和任何其他新建或修改的文件保存在 Web 服务器上。考虑将主页文件放入一个单独的子目录中，其页眉、页脚和图像文件仅供您的 AvantGo 频道使用。

##### 使用表单

任何值得占据网络空间的网站都有一些通过表单实现的交互性。AvantGo 和 PDA 几乎可以处理任何形式的表单。一定要记住屏幕尺寸和前面提到的 HTML 限制。

用户在 PDA 上填写并提交的表单保存在表单管理器中。下次用户同步到您的频道时，AvantGo 会将完成的表单发送到您的服务器，在服务器上，表单的 cgi-bin 或其他操作会照常处理表单。

您需要对表单进行的唯一主要更改是用一点 JavaScript 定制 Submit 按钮。替换标准:

```
<input type="submit" name="submit" value="Submit">
```

使用:

```
<input type="button" name="submit" value="Submit"

     onClick="document.forms[0].submitNoResponse(

        'This is the message that will be displayed after form 

	submission',

          false,

          true); 

      back()">
```

`submitNoResponse`函数是 AvantGo 向用户显示确认消息的方式，也是指定表单是否在表单管理器中隐藏(在本例中为“false”)以及是否可以提交多个表单(“true”)的方式。

AvantGo 的一个强大功能是通过表单和 cookies 实现频道个性化。欲了解更多信息，请访问本文。

##### 创建您的 AvantGo 频道

如果您还没有这样做，请注册一个免费的 AvantGo 帐户。接下来，使用 AvantGo 的[向导](https://avantgo.com/mydevice/channel_wizard/url.html)创建您的频道。您将指定主页的位置、链接深度等等。这总共需要五分钟。

完成后，点击“导出渠道”以生成 URL，您可以复制并发布到您的网站上，以便客户可以注册您的渠道。URL 将如下所示:

```
http://avantgo.com/mydevice/autoadd.html?title=WEBtheJOINT&url=ht

tp%3A%2F%2Fwww.webthejoint.com%2Favantgo%2Findex.php&max=100&dept

h=2&images=1&links=0&refresh=always&hours=1&dflags=127&hour=16&qu

arter=30&s=00
```

它并不漂亮，但它很管用。当您转到此 URL 时，该频道将被添加到您的“我的频道”列表中。同步你的 PDA，然后点击你的 PDA 上的 AvantGo 图标来测试你的手持友好网站。

如果你是 PDA，不要担心。将 [Palm OS 模拟器](http://www.palmos.com/dev/tech/tools/emulator/)下载到您的 Windows/Mac/Unix 计算机上。

##### 替代 PDA 浏览器解决方案

AvantGo 不是镇上唯一的游戏，但它远远领先于排名第二的游戏。

***西装外套***

Palm 浏览器几乎可以访问网络上的任何站点，但删除不支持的内容(流媒体、Java、JavaScript)。与 AvantGo 不同，Blazer 不在 PDA 上存储内容。
**费用:19.95 美元**
【http://www.handspring.com/software/blazer_overview.jhtml】T4

***掌景***

可以缓存页面以便脱机查看的 Palm 浏览器。不支持 SSL。
**费用:29.95 美元**
【http://www.ilinx.co.jp/en/products/ps.html】T4

***网页剪报***

Palm VII 手持设备、带有 OmniSky 调制解调器的 Palm Vs 以及安装了移动互联网套件的其他手持设备支持的独立应用程序。
[http://www.palm.com/dev/tech/webclipping/](http://www.palm.com/dev/tech/webclipping/)。

##### 你还在等什么？

如果您还没有，请注册一个免费的 AvantGo 帐户。订阅几个频道，包括[site point 频道](https://avantgo.com/channels/detail.html?cha_id=2700&cat_id=374&type=category_channel&data=374)——你会很快找到一些具有优雅导航和有价值内容的频道。你的可能是其中之一。试试看！

## 分享这篇文章