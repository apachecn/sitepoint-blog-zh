# eZ publish: PHP 的黑仔应用程序-第 1-3 部分

> 原文：<https://www.sitepoint.com/phps-killer-app-parts-1-3/>

如果您曾经在 Hotscripts 上搜索过 PHP 清单，以寻找一个内容管理系统来避免您自己编写内容，那么您可能遇到过 eZ publish，一个基于 PHP 的 CMS 应用程序，并认为“哇！”喜气洋洋，你试图安装它…无济于事。你绝望地试图阅读代码，却发现它毫无意义。最后你躲在一个安静的角落舔伤口，转而求助于 [PHP Nuke](http://www.phpnuke.org/) 。

这一系列都是关于 eZ publish 以及为什么它配得上“PHP 的杀手级应用”的称号。我们将从头开始:首先，我们将在您的开发环境中安装 eZ publish。然后，我们将学习如何利用它的功能，并根据您的具体需求进行定制。最后，我们将深入了解 eZ publish 的成功之处:eZ publish 实际上是一个强大的工具，可以帮助您构建自己的基于 Web 的应用程序。

最后，你不仅可以建立和定制你自己的 eZ publish 网站，还可以构建你自己的应用程序在 eZ publish 中运行。

下面是真相…

第 1 部分–eZ publish 简介，我们在这里探索、下载和安装所需的文件。

[第 2 部分](http://webmasterbase.com/article/917/6)——我们直接进入 eZ publish，学习如何根据您的需求定制它——在外观和功能方面。我们将构建自己的小站点并实现 eZ article。

[第 3 部分](https://www.sitepoint.com/blog/)–我们讨论如何创建自己的 eZ publish 模块，深入探究使用 eZ publish 构建自己的模块是如何以及为什么如此简单。

我们开始吧！

##### 第一部分

本系列的第一篇文章中的菜单是:

1.  **功课:**严格的纪律

##### eZ publish 简介

eZ publish(表面上)是一个网站内容管理系统，由挪威公司 [eZ systems](http://ez.no ) 用 [PHP](http://www.php.net) 编写。eZ publish 于 2000 年推出，发展迅猛，已经在 Webservices.org 的、[奥地利国家旅游局](http://www.austria-tourism.at/)和[简单网络](http://www.symplicitynetworks.com/)(更多网站使用 eZ publish [这里](http://www.ez.no/developer/links/))等网站上成功投入使用，并获得了 IBM 和西门子等[商业伙伴](http://shop.ez.no/partner/list/)的支持。eZ publish 现在甚至包含在 Debian Linux 发行版中。

内容管理系统，你可能从凯文·杨克的著名教程中知道，允许你以易于维护和更新的方式在你的网站上发布动态内容。eZ publish 将这一模式发挥到了极致。首先，它允许你在数据库中存储内容(eZ publish 2.x 目前支持 [MySQL](http://www.mysql.com) 和 [PostgreSQL](http://www.postgresql.org) ，在 3.x 版本中支持其他数据库，如 Oracle)。在这个数据库之上，一组 PHP 应用程序为站点维护提供了一个强大的管理界面，而大量的前端模块允许 eZ publish 快速地实现为任何东西，从新闻和社区站点，如 SitePoint 或在线商店，到 B2B 门户，甚至是企业内部网。换句话说，它是你所有网站需求的解决方案！

更重要的是，eZ publish 已经在 Web 服务领域迈出了一步，它有一个商业许可的[桌面版](http://shop.ez.no/trade/productview/25/2/)，允许你使用 C++ Windows 客户端与 XML-RPC 服务器接口通信来更新 eZ publish 站点。

对我和你这样的穷光蛋来说，好消息是 eZ publish 有一个双重许可计划。它既提供了一个 [GNU 开源许可](http://www.gnu.org/copyleft/gpl.html1)，也提供了一个专业许可(对于那些希望做类似转售或更名 eZ publish 的事情的人)。对于一个开源项目来说，eZ systems 是一个很好的例子，它展示了如何将公开许可的软件转化为商业成功。

***有什么优惠？***

那么，除了简介，eZ publish 实际上能提供什么呢？获得答案的最简单方法是访问位于[http://publishdemo.ez.no/](http://publishdemo.ez.no/)的演示网站，然后使用以下方式前往[http://admin.publishdemo.ez.no/](http://admin.publishdemo.ez.no/)登录后端管理系统:

**用户名:**管理员
密码:发布

登录后，您会看到演示站点控制面板的顶部有一排图标(该玩了！).每一个都对应于一个 eZ publish *模块，*，它为你的网站前端提供了一系列的功能。

在这些模块中，您会发现:

*   eZ article: 这是“主”模块，允许你在你的站点上发布文章或“静态”页面
*   一个强大的购物车应用程序，你可以在 MyGold.com 找到它
*   eZ filemanager: 一个很棒的内部网和文件分发工具，提供一个在线文件系统供你的访问者浏览
*   eZ 论坛:你的网站论坛，类似于 [VBulletin](http://www.vbulletin.com/) ， [SitePoint 论坛](http://www.sitepointforums.com)背后的软件

…以及更多在[用户手册](http://doc.ez.no/article/archive/2/)中详细描述的内容。换句话说，eZ publish 为您提供了构建自己的站点网络版本所需的一切！你会注意到在[演示站点](http://publishdemo.ez.no/)和[主站点](http://developer.ez.no)上，甚至 URL 都与 SitePoint 的相似。例如:

<q>http://developer.ez.no/article/articleview/367</q>

和

<q>http://www.webmasterbase.com/article/228</q>

当然，要建立一个像 SitePoint 一样的网站并说服所有的 SitePoint 粉丝来访问你还有很长的路要走，但是你明白了。

***又一个 CMS？***

“好的”，我听到你说，“另一个内容管理系统。大不了。”

eZ publish 的特别之处不在于令人印象深刻的功能列表，而在于幕后发生的事情。eZ publish 实际上是一个应用程序开发框架，它为 PHP 开发人员提供了一个构建应用程序的结构，并从那里快速地将它们部署到一个真实的环境中，从而节省了大量的编码时间。

如果你曾经从头开始编写过自己的 PHP 网站，你可能会发现自己希望有一种更简单的方法来做事情。你面临着大量的问题:在哪里放置你的站点的脚本，如何建立一个集成的站点导航系统，如何防止为你的站点的每一页复制相同的代码块，以及一大堆其他的问题(同时，你的社交生活打包去夏威夷)。期待已久的网站的完成经常感觉像是一个奇迹…然后有人想出了一些新功能的好主意，而你正在重新编写整个网站，暗自哭泣。

应用程序开发框架消除了构建网站的负担。遵循它的规则和指导方针，你会发现维护和扩展你的网站轻而易举。一个好的框架应该为你做 90%的设计和开发决策。诸如命名约定、在你的网站文件系统中把你的脚本放在哪里、如何设计和组织你的代码，以及所有那些你纠结的细节问题的解决方案，只需要遵循一套清晰的指导方针。那些你曾经浪费了几个小时的代码呢，比如数据库连接和查询功能，那些你为每个新站点一遍又一遍重复的工作？所有这些都应该在框架内，供您在需要时重用。

这就是 eZ publish 真正提供的:让你的开发者生活变得无忧无虑和愉快的机会。

***PHP 编码员一般的警告:***

用 eZ publish 开发意味着编写面向对象的代码！

尽管我们都喜欢使用程序代码编写“黑客”脚本(也许只有在我们真的不得不这么做的时候才会包含一些 PHP 函数)，但事实是，如果你想在一两年后还留有一点头发，你*需要*面向对象。用 PHP 编写面向对象的代码不仅仅是为了节省时间:它允许您解决过程代码无法解决的编码问题。一旦你让两个或三个 PHP 类在一个应用程序中一起工作，你会意识到你再也不想回去了。然后，您将能够像以前从未黑过一样黑掉！

换句话说，面向对象让我们能够编写可重用的代码，并将不同的应用程序和功能集成到一个整体中。例如，您可能已经在您的一个站点上遇到了困难，在那里您实现了一个用户认证系统，并注册了许多人。你现在正绞尽脑汁地想如何将它与一个论坛应用程序整合在一起，比如 [VBulletin](http://www.vbulletin.com) 或 [phpBB](http://www.phpbb.com) 而不强迫你的用户重新注册。如果我们都写更多的面向对象的代码，至少一半的问题会得到解决。

总的来说，eZ publish 将各种模块捆绑在一起的方式在概念上类似于 [Fusebox](http://www.fusebox.org/) 方法。这种构建 Web 应用程序的策略是由 Coldfusion 的开发人员构思的，现在已经在 Bombusbee.com 的[开发出 PHP。一个类似 fusebox 的网站背后的基本概念是有一个单一的脚本(通常是网络根中的 index.php ),作为网站的“交通警察”,所有页面都“通过”index.php 提供服务。关于保险丝盒方法的更多细节，请尝试](http://bombusbee.com/)[保险丝盒新手指南](http://bombusbee.com/downloads/files/FuseboxNewbieGuideV3PHP.pdf)。从应用程序设计的角度来看，eZ publish 基本上符合 Fusebox 的方法，因此在准备本系列的第三篇文章时阅读 fuse box 可能会有所帮助。但是不要慌！我们将在本系列的第三篇文章中进一步讨论面向对象和应用程序设计。

回到 eZ publish！为了激起那些了解 PHP 类的人的兴趣，看看 eZ publish [Class Index](http://developer.ez.no/doc/view/index) 。然后逛到目前正在为 eZ publish v.3 开发的 [eZ publish sdk](http://sdk.ez.no)

希望现在你至少已经接受了 eZ publish 值得你努力的想法。在这种情况下，是时候卷起袖子开始安装了！eZ 出版，我们来了。

##### 正在安装 eZ publish

在设置 eZ publish 的工作版本时，我敢假设您使用 Windows，并且愿意在自己的机器上设置演示。这里使用的安装过程可以很容易地应用于许多 Web 主机常见的 LAMP (Linux、Apache、MySQL、PHP)虚拟主机环境。

建议你在 Apache 1.3x 和 MySQL 3.23 或更高版本下至少安装 PHP 4 . 0 . 6 版(如果是 PHP 4.1 以上就更好了)。如果你还没有安装这些软件，只需看看 [Firepage 的](http://www.firepages.com.au) phpdev，它在一个整洁的包中为你提供了 Apache、PHP 和 MySQL(加上其他一些好东西，如 [PHP-GTK](http://www.webmasterbase.com/article/839) 和 [phpMyAdmin](http://www.phpmyadmin.org/) )。当前的 phpdev5 beta3 非常稳定，但是为了避免一些麻烦，建议您使用 Apache 1.3.x 而不是 2.x (phpdev5 beta 3 两者都有)。

本系列文章中使用的 eZ publish 版本是 2.2.6。不同版本的安装过程可能会有所不同，请注意 eZ publish 对正确的 php.ini 设置非常敏感。如果您对本文中使用的说明有任何问题，请在本文末尾的[站点论坛](http://www.sitepointforums.com)讨论中提出您的问题。否则，你会在 http://developer.ez.no/developer/forums/的[找到很多愿意帮忙的人。](http://developer.ez.no/developer/forums/)

***正事***

eZ publish 2.2.6 可以在 http://developer.ez.no/filemanager/list/85/ T2 的文件管理器中找到。下载并解压缩到本地 Apache 或 PHP Web 服务器下的某个地方——对于 phpdev5，这可能是类似于“`C:phpdev5wwwpublic`”的东西。

注意:存在一个 eZ publish Windows installer 它也安装 Apache、PHP 和 MySQL。然而，我们做的事情略有不同，这样我们就可以在我们的 live LAMP 虚拟服务器上重用安装过程。

解压 eZ publish 后，您现在将拥有一个类似于`C:phpdev5wwwpublicezpublish_2_2_6` 的目录(从现在开始，我将把这个目录称为您的“eZ publish Root”)。在那里你会找到一个`installation`目录，其中包含一个名为`INSTALL.pdf`的文件——本文档的第三章是我们将用来安装 eZ publish 的方法。

***要不要贤惠？***

正如您在本文开头看到的，当我们查看 eZ publish 的在线演示时，有两个独立的 URL:一个用于主站点，另一个用于后端管理界面:

<q>http://publishdemo.ez.no/和
http://admin.publishdemo.ez.no/</q>

这是通过 Apache 的虚拟主机特性的一些巧妙技巧实现的，在第 2 章`INSTALL.pdf`中有描述。在典型的 Web 主机上，尝试以这种方式安装 eZ publish 可能非常复杂——如果不是不可能的话，这就是为什么我们选择不需要 Apache 配置的替代选项。话虽如此，通过更简单的方式进行安装会稍微改变 eZ publish 的行为方式:

*   http://www.yourdomain.com/index.php 是你的主页
*   http://www.yourdomain.com/index_admin.php 是你的管理界面

另外，eZ 发布的 URL 通常是这样的:

*   http://www.yourdomain.com/article/articleview/3/

使用更简单的方法，URL 将看起来像:

*   http://www.yourdomain.com/index.php/article/articleview/3/

这是一种权衡，但与您在第一次安装 eZ publish 时选择虚拟主机的痛苦相比，这是微不足道的。

***影像魔术师***

我们需要的另一个软件是 [Image Magick](http://www.imagemagick.org ) ，这是一套强大的命令行图像操作工具，当我们的 PHP 脚本在图形领域需要一点额外的“活力”时，它会非常方便。eZ publish 使用 Image Magick 来控制图像大小，使它们能够一致地呈现。

我们需要安装 Image Magick 的文件可以在 ftp://ftp.nluug.nl/pub/ImageMagick/binaries/的(或者其中一个[镜像](http://www.imagemagick.org/www/archives.html))找到。您正在寻找的文件将被称为`ImageMagick-5.4.9-xp.exe`，或者类似的名称。下载并运行可执行文件后，将 Image Magick 安装到目录`C:Imagemagick`。

***准备行动***

您需要采取以下步骤来启动并运行 eZ publish。

1.  将文件`htaccess-nVH`从`[eZ publish_root]installation`复制到`[eZ publish_root]`，将当前文件重命名为。将 htaccess 重命名为 htaccess_old，并将 htaccess-nVH 重命名为。htaccess(注意，根据您的版本，Windows 资源管理器可能会抱怨文件以句号(或“句号”)开头。在这种情况下，你需要使用 DOS 命令行重命名文件(见 [Kev 的命令提示备忘单](http://www.webmasterbase.com/article/846))。

更改 eZ publish 根目录并使用`rename htaccess-nVH .btaccess` 重命名文件。这一步确保 eZ publish 安装是安全的，网站访问者只能访问 index.php。

*   现在转到你的 MySQL 数据库(我假设你已经安装了 phpMyAdmin——如果你已经安装了 phpdev5，可以从[http://localhost/phpMyAdmin](http://localhost/phpmyadmin)下载)并创建一个名为“publish”的数据库。
*   现在，您需要导入 MySQL 数据库以进行 eZ publish。在 phpMyAdmin(版本 2.3.0 以上)中，当查看发布数据库时，您需要单击 SQL 选项卡，并使用文件上传框“文本文件的位置:”来选择文件。您需要的文件是`[eZ publish_root]sqlpublish_mysql.sql`，它构建了 eZ publish 数据库结构。我们还想使用一些样本数据，所以加载文件`[eZ publish_root]sqldata_mysql.sql`，它将填充数据库。
*   将文件`[eZ publish_root]site.ini`重命名为`[eZ publish_root]site.ini.php`，用自己喜欢的编辑器编辑。这样做的原因也是出于安全考虑(除了 index.php，带有. php 扩展名的文件对访问者来说是不可用的)。

现在，在`site.ini.php`中，您需要更改以下设置以匹配您的环境:

```
[site]    
SiteURL=localhost/public/ezpublish_2_2_6    
AdminSiteURL=localhost/public/ezpublish_2_2_6    
UserSiteURL=localhost/public/ezpublish_2_2_6    
...    
SiteTitle=phpPoint    
...    
SiteTmpDir=C:/Windows/temp    
...    
# Database settings set DatabaseImplementation to     
mysql|postgresql|informix.    
DatabaseImplementation=mysql    
Server=localhost    
Database=publish    
User=MySql_User    
Password=MySql_User_Password    
...    
ImageConversionProgram=C:Imagemagickconvert.exe
```

保存文件。

*   编辑 eZ publish root 中的 sitedir.ini 文件，并设置以下变量:

```
$siteDir = "C:/phpdev5/www/public/ezpublish_2_2_6/";
```

*   接下来，您需要创建 eZ publish 用来缓存页面的目录(对 eZ publish 感觉不错吧？).在 [`make_cache.zip`](http://www.webmasterbase.com/examples/ezpub/make_cache.zip) 下载文件。这包含一个批处理文件`make_cache.bat` ，它将为您创建目录。将它解压到你的 eZ publish 根目录下，用 DOS 命令行，从 eZ publish 根目录键入`make_cache`。现在应该已经创建了所有的缓存目录，比如`[eZ publish_root]classescache`。当你完成的时候确保你删除了`make_cache.bat`。
*   最后，我们需要确保 php.ini 设置是正确的。打开 php.ini 文件(从 C:Windows 目录)并检查以下设置:

*   eZ publish 经常使用`<? echo($variable); ?>`语法，而不是`<?php echo($variable); ?>`，希望他们在 3.x 版本中能纠正这一点，以提高可移植性。
*   `allow_call_time_pass_reference = On`
*   通过引用传递变量是 XML 解析脚本中常用的一种“坏习惯”——我们希望他们能花时间在 eZ publish 3.x 中解决这一问题
*   `error_reporting = E_ALL & ~E_NOTICE`
*   `register_globals = On`
*   eZ publish 2.x 的开发开始于 PHP 4.2 宣布默认关闭注册全局变量之前。正如 Kevin Yank 在[中警告我们的，用 PHP 4.2 编写安全的脚本！](http://www.webmasterbase.com/article/758)，开着注册全局是自找麻烦。同样，我们希望这个问题会在 eZ publish 版本中得到解决
*   `magic_quotes_gpc = Off`
*   魔语录是邪恶的！`magic_quotes_runtime = Off`也应该设定。

这些是重要的设置。保存 php.ini 并重启 Apache。

***神奇的一刻……***

前往 http://localhost/public/ez publish _ 2 _ 2 _ 6/index . PHP 查看您的网站！要登录到管理界面，请访问 http://localhost/public/ez publish _ 2 _ 2 _ 6/index _ admin . PHP，并使用以下用户名登录:

**用户 id:** 管理员
密码:发布

不要担心“未能创建图像变体”的图像——这只是告诉我们它期望的图像不可用(它们不包含在 eZ publish 安装 zip 中)。如果在你查看 eZ publish 主页后，你发现你在 eZ publish 安装中犯了错误，并且在你做出更改后需要进一步修改`site.ini.php`，你需要删除你在`[eZ publish_root]classescache`(eZ publish 在这里缓存你的`site.ini.php`文件)中找到的任何文件。否则，如果您有任何问题，请不要犹豫，在本文末尾的 [SitePoint 论坛](http://www.sitepointforums.com)讨论中提问。

***在 Linux 主机上安装***

如果我可以做另一个假设——假设您在一个共享的 Linux 服务器上托管您的 PHP 站点——安装过程将非常相似，但是有一些陷阱。这里有一些小贴士可以帮助你。

`./modfix.sh`

注意，该文件还在不完全*安全的*缓存目录上设置了安全权限(尽管风险很低)。备选方案`secure_modfix.sh`是存在的，但是如果您在典型的共享 Linux 环境中使用它，您可能会遇到麻烦。

*   要使用 Image Magick，需要在 Web 服务器上安装`convert`二进制文件。是时候请求你的主机帮个忙了(安装对他们来说并不难)，让他们告诉你他们把它放在哪里了。

如果您在 Web 主机上安装有任何问题，请随时在本文末尾的讨论中提出您的问题。

##### 家庭作业

eZ publish 现已启动并运行！在进入本系列的第二部分之前，如果您选择接受它，您的任务是(以管理员身份登录时):

***[【ez 图像目录】](http://doc.ez.no/article/archive/24/)***

*   删除现有类别
*   创建一个名为 Logos 的新类别，只有管理员和文章作者可以“写”和“上传”
*   在 Logos 文件夹下，创建一个名为“软件”的类别，拥有与“Logos”相同的权限
*   上传几张图片到软件文件夹，比如 [PHP 标识](http://www.php.net/logos/php-med-trans-light.gif)和 [MySQL 标识](http://www.mysql.cimg/poweredbymysql-88.png)。

***[ez 条](http://doc.ez.no/article/archive/7/ )***

*   这变得有点困难:编辑“文章标签演示”文章，看看你是否能让它使用你添加的两个标志。
*   最重要的:了解 eZ 文章标签。如果你下载 [eZ 文章文档](http://developer.ez.no/filemanager/list/8/)并阅读`Adminguide.pdf`，你会找到完整的细节——在[http://doc.ez.no/article/articleview/3/1/7/](http://doc.ez.no/article/articleview/3/1/7/)的网上提供了更简短的描述。对于下一篇文章来说，你熟悉 eZ 文章模块是很重要的——A 级学生将弄清楚静态页面“谢谢你的反馈”与 [eZ 表单模块](http://doc.ez.no/article/archive/9/)的关系。

如果这还不够，看看`[eZ publish_root]/checklists`中的文件——这些文件旨在帮助您测试 eZ publish 是否正常工作，但也可以作为一系列很好的练习来习惯所有的功能。哦，当你完成的时候，不要忘记看一下`error.log`文件。你可以在你的 eZ publish 根目录中找到——这是一本有趣的读物！

在本系列的下一部分中，我们将假设您对使用这些模块相当满意，然后继续讨论如何定制您的 eZ publish 站点，使其具有您自己的外观和感觉。在那之前，希望你所有的出版…事实上，很好！

##### 第二部分

在本系列的第 1 部分中，我向您介绍了 [eZ publish](http://developer.ez.no) ，并大胆地称它为“PHP 的杀手级应用”。到上一篇文章结束时，您已经在您的开发系统上安装了 eZ publish，当然，由于非常勤奋，您已经完成了所有的家庭作业，并且知道 eZ publish 必须提供什么。

不过现在，你可能想知道如何改变这种无聊的灰色背景，并应用你自己的设计。在本文中，这正是我要解释的:如何让 eZ publish 看起来和表现起来像你想要的那样。我将帮助您创建一个名为“phpPoint”的虚拟网站，作为您如何定制 eZ publish 的演示。

今天的特色菜是:

*   **傻瓜冲进来:**关于 eZ publish 目录结构、站点 ini 文件和缓存系统的必备知识。
*   **走向全球:**如何修改 eZ publish 的“全球”设计，以及如何使用 Sections 创建多个站点。
*   **点击模块:**在这里您将看到模块定制是如何工作的。专注于 eZ *的文章*，你将看到如何使用它的“API”，如何修改它的模板以及如何使用静态页面、URL 翻译和表单。
*   **phpPoint 完成:**大团圆结局！

当你完成的时候，你将知道你需要使用现有的模块来构建你自己的最先进的 eZ publish 站点。

##### 傻瓜冲进去

好的。在你开始动手之前，有几件事值得一看，这样你就知道你在哪里了。

***eZ 发布目录结构***

如果你用 Windows 资源管理器打开 eZ publish 安装目录`C:phpdev5wwwpublicezpublish_2_2_6`(或者你安装它的任何地方)，你会看到一长串子目录。其中大多数与 eZ publish 模块有关——它们很容易被发现，因为它们看起来都像“ezsomething”，例如:“ezarticle”、“eztrade”和“ezforum”。稍后我将回到模块目录，但首先，让我们看看其他一些重要的目录和文件:

*   **管理目录:**这包含了 eZ publish 管理后端的*常规*外观和感觉的代码和模板。模块为特定的管理任务添加更多的代码，存储在它们自己的目录下(您很快就会看到)。不过，我不会去管这个目录，因为我不太关心管理界面是什么样子:毕竟它是私有的。需要注意的一点是，您可能遇到的任何“intl”目录都包含允许 eZ publish 处理除英语之外的语言的文件——稍后您将会看到更多这样的内容。
*   **清单目录:**如果您真的陷入了上一篇文章的作业中，您应该已经知道这个目录包含了一组可以在 eZ publish 模块上运行的测试。eZ publish 仍然有一些小错误，正如你可能预料到的这样大的应用程序。如果你遇到任何问题，并且你确定那不是你的配置，看看 [eZ publish Bug Tracker](http://developer.ez.no/bug/archive/15) 。
*   类别目录:这包含了在 eZ publish 中使用的一般 PHP 类别，如 http://developer.ez.no/doc/view/index[的](http://developer.ez.no/doc/view/index )所述。如果你只是对应用你自己的设计感兴趣，你不需要担心这些。我将在本系列的第三篇文章中更详细地讨论它们。在 classes 目录下，您还可以找到非常重要的缓存目录。我们稍后将讨论更多关于缓存的内容，但是您需要知道这个目录缓存了全局配置文件，比如 site.ini.php。
*   **图像目录:**包含 eZ publish 中使用的图像。更多图像显示在模块目录下。
*   sitedesign 目录:这个目录很重要，因为它包含了决定整个 eZ publish 设计的文件。我们很快会深入探讨这个问题。
*   **site.ini.php 文件:**这个文件存储了 eZ publish 的全局配置选项。一分钟后更多！
*   **sitedir.ini 文件:**eZ publish 使用它来定位自己的安装，正如您在第一部分中看到的。
*   **cron.php 文件:**这是一个有用的文件，有助于在您睡觉时对 eZ publish 进行定期更新！用于 [Unix Cron 工具](http://www.unixgeeks.org/security/newbie/unix/cron-1.html)(也应该可以使用 Windows 调度工具激活它，比如任务调度器)。
*   **index.php 文件:**这是 eZ publish 的“交警”，按要求激活代码。它永远不需要改变——除非你知道自己在做什么，否则任何这样做的尝试都可能会破坏东西。
*   **index_admin.php 文件:**这是后端管理系统的“交通警察”。还是那句话，最好不要碰！
*   **index_xmlrpc.php 文件:**这为 eZ publish 提供了一个 XML-RPC 接口，供他们的[桌面版](http://shop.ez.no/trade/productview/25/2/)使用。不是你应该干涉的。

**网站. ini**T3]

在本系列的第 1 部分中，您将其重命名为`site.ini.php` ,并做了一些基本的修改，以使您的站点启动并运行。为了与 eZ publish 文档保持一致，我从现在开始将它简称为`site.ini`。

该文件包含整个 eZ publish 的全局设置，以及适用于特定 eZ publish 模块的设置。鉴于文件的大小，我不打算涵盖所有的设置，而是强调一些重要的设置，并解释更新`site.ini`的机制。您应该会发现大多数设置都是不言自明的。

但是首先，**做个备份**放在安全的地方。

在`site.ini` 的顶部，你会发现适用于整个网站的设置。让我们从修改这些开始。

```
# Meta content variable       
SiteAuthor=HarryF       
SiteCopyright=HarryF &copy; 2002       
SiteDescription=PHP: Getting the point?       
SiteKeywords=PHP, Development, Tutorials, Articles
```

这些决定了出现在每个页面顶部的 HTML `<meta>`标签。注意像 eZ article 这样的 eZ publish 模块可能会用你在文章中创建的值覆盖 SiteDescription 和 SiteKeywords:如果你想告诉 Google 你写的所有文章，这是非常有用的。这里的设置相当于默认设置。

让我们再看一些:

```
DefaultPage=/article/frontpage/1/       
LogFile=error.log       
EnabledModules=eZArticle;eZTrade;eZForum;eZLink;eZPoll;eZAd;       
eZNewsfeed;eZBug;eZContact;eZTodo;eZCalendar;eZFileManager;       
eZImageCatalogue;eZMediaCatalogue;eZAddress;eZForm;eZBulkMail;       
eZMessage;eZQuiz;eZStats;eZURLTranslator;eZSiteManager;eZUser       
CacheHeaders=true       
...       
URLTranslationKeyword=section-standard;section-intranet;section-trade;       
section-news       
Sections=enabled       
DefaultSection=1
```

好了，让我们一步一步地检查这段代码。

`DefaultPage`决定了什么会出现在你的主页上，即 http://localhost/public/ez publish _ 2 _ 2 _ 6/index . PHP，在这种情况下与 http://localhost/public/ez publish _ 2 _ 2 _ 6/index . PHP/article/front page/1/相同。

您在本系列第 1 部分中看到的日志文件用于跟踪管理员对站点做了什么，以及发生的任何应用程序错误。如果你有一个很大的网站，有很多人在上面工作，这是非常有用的。

```
EnabledModules is self-explanatory.
```

```
CacheHeaders controls the browser caching headers delivered with the HTTP protocol (it determines whether a browser use its own, locally-stored copy, or fetches a new one from your site) -- best to leave this as it is.
```

```
URLTranslaterionKeyword you'll see more of when we get to eZ article -- essentially it helps make "pretty" URLs, so that, rather than http://localhost/public/ezpublish_2_2_6/index.php/forum/forumlist/1/ you could have http://localhost/public/ezpublish_2_2_6/index.php/forum/.
```

```
Sections allows you to have multiple "styles" for your site (a bit like skins), each with a different look and feel, offering access to different eZ publish modules. In conjunction with this, DefaultSection tells eZ publish which to use on the home page, or if it's uncertain which style to use at any point. I'll look more at sections shortly.
```

在此之后，您需要担心的其他设置适用于模块，例如:

```
[eZAdMain]       
AdminTemplateDir=templates/standard/       
TemplateDir=templates/standard/ImageDir=images/standard/       
Language=en_GB       
DefaultCategory=1       
DefaultSection=1
```

你很快会看到更多这样的内容。在每个 eZ publish 模块目录中，您会发现一个名为`site.ini.txt`的文件，它给出了这些变量的含义。

***缓存***

除了生成 HTTP 头来告诉浏览器在缓存网页时做什么之外，eZ publish 还有自己的内部缓存机制来存储“解析”的输出。一旦一个页面被查看，eZ publish 就可以交付缓存的版本，而不是重新构建整个页面，从而提高整体性能。当你修改 eZ publish 的设计时，你需要知道缓存是如何工作的——否则你会奇怪为什么看不到你的修改。内部缓存机制主要以三种方式使用:

1.  目录`[ezPublish_root]/classes/cache/`用于存储 PHP 脚本和变量。`site.ini`的内容将以解析的形式在这里找到，还有 eZ publish 为整个操作范围分配的变量。如果您对`site.ini`进行了更改，您需要删除此目录中的文件以使您的更改生效。

*   在`site.ini`中，您会发现以下设置:

```
SiteCache=disabled       
SiteCacheTimeout=60
```

`SiteCache` 试图缓存 eZ publish 的每一页。这只对大部分内容是静态的网站有用。例如，对于使用论坛模块的网站，最好禁用这个功能。`SiteCacheTimeout` 告诉 eZ publish 缓存的页面何时可以被视为过期(以分钟为单位)并需要重新创建。

*   对于某些模块，比如 eZ article，你还可以在`site.ini`中找到一个`PageCaching`设置，它也可以被启用或禁用。当你处理一个像 eZ article 这样的模块的可视化布局时，把它设置为“disabled”是一个好主意，这样你就可以看到你所做的任何改变。另外，如果你发布了一篇文章，然后对它进行了修改，你需要删除它在`[ezPublish_root]/ezarticle/cache/`中的缓存版本。

在 eZ publish 的管理后端，你可以在站点管理器部分找到缓存管理工具(最右边第二个)。如果你已经在 Linux 下安装了 eZ publish，这就很好了，但遗憾的是在 Windows 下不行(你必须用 Explorer 手动删除缓存的文件)。

##### 走向全球

现在你已经知道 eZ publish 是如何组合在一起的了，是时候开始应用你自己的设计了(好吧，事实上是我的——但是假装是你的！).

在你开始用 eZ publish 做任何事情之前，你需要一个你希望你的站点看起来怎么样的“草图”。以我个人的偏见，首先要做的是从[http://www.opera.com/download/](http://www.opera.com/download/)下载一份歌剧。为什么？因为，一般来说，如果你的设计在 Opera 上看起来合适，那么在其他任何浏览器上看起来也合适。如果你运行的是 Windows，非 Java 版本也可以。

我已经制作了一个简单的 HTML 页面和一个样式表，我想“应用”到 eZ publish。如果你下载了 [sampler.zip](http://www.webmasterbase.com/examples/ezpub/sampler.zip) ，你会发现一个简单的 HTML 设计，旁边还有一个 CSS 文件。该网站将非常简单——它将只利用 eZ 文章模块。不过，您在这里学到的东西应该适用于更复杂的 eZ publish 站点。当你阅读这篇文章的其余部分时，打开 sampler.html(来自 ZIP)是值得的，这有助于想象发生了什么。

***网站设计目录***

第一步是找到`[ezpublish_root]/sitedesign/standard/`目录并复制它，您需要将其重命名为`[ezpublish_root]/sitedesign/phppoint/`。你将把所有的设计工作保存在`phppoint` 目录中。保持原始目录不变是一个好主意，这样可以有一些例子。

接下来，从管理后端，前往网站管理员(最右边第二个)。默认视图是“节列表”。删除内部网、贸易和新闻部分，因为你不需要它们，然后编辑标准部分，将名称和站点设计设置为“phpPoint”。在编辑页面的下方，您会看到一个名为“列”的部分。目的是让四篇文章同时出现在“首页”，每篇文章占据整个页面的宽度。所以添加一行，然后将它们全部设置为类别为“全部”的“1 列文章”。

现在转到 URLTranslator(右边第三个)，将`/section-standard`重命名为`/section-phppoint`，然后删除其他三个。

最后，在`site.ini`中，更新以下变量:

```
SiteDesign=phppoint        
SiteStyle=ezpublish        
SiteTitle=phpPoint        
...        
EnabledModules=eZArticle;eZImageCatalogue;eZMediaCatalogue;        
eZForm;eZStats;eZURLTranslator;eZSiteManager;eZUser        
...        
URLTranslationKeyword=section-phppoint        
Sections=disabled
```

变量 points eZ 发布在我将要放入我的设计的目录中。`SiteTitle`是出现在浏览器顶部的标题。`SiteStyle`我保持原样，因为这适用于后端管理部分。您需要使用`EnababledModules`变量确保只有您想要的模块可用。`URLTranslation`和`Sections`将在“章节”中详细讨论。

最后，清空`[ezpublish_root]/classes/cache/`中的缓存。

您现在已经准备好开始黑客攻击了！

***`frame.php`***

在您的`[ezpublish_root]/sitedesign/phppoint/`目录中，您会找到一个名为`frame.php`的文件。这个文件基本上是 eZ publish 中的主要模板:或多或少所有的页面都是由 eZ publish 通过这个模板生成的。

在你继续之前，如果你允许我有一个短暂的咆哮:这就是 PHP 中模板化的真正意义*。正如你在 [SitePoint 论坛](http://www.sitepointforums.com)的[讨论](http://www.sitepointforums.com/showthread.php?s=&postid=498687#post498687)中看到的，任何不使用 PHP 自己的语言结构和变量的模板系统都是对时间、内存和处理能力的彻底浪费。`frame.php`是模板*应该如何构造*的最好例子。*

 *现在，看一下`frame.php`的内容，你会看到 HTML 穿插在 PHP 中。在你编辑它之前，我将向你介绍一些你将在那里看到的例子。首先你有:

```
<title><?php        
// set the site title        

$SiteTitle = $ini->read_var( "site", "SiteTitle" );        

if ( isset( $SiteTitleAppend ) )        
    print( $SiteTitle . " - " . $SiteTitleAppend );        
else        
    print( $SiteTitle );        

?></title>
```

这里，代码首先通过使用`read_var()` 方法从`$ini`对象中获取变量`$SiteTitle`,来分配变量。如果你不确定这里使用的语法，看看凯文·杨克关于面向对象 PHP 的入门教程。

之后，代码检查一个`$SiteTitleAppend`变量，如果它存在，通常包含类似文章标题的内容。如果没有找到这个变量，`<title />`标签的内容将只是您在`site.ini`中设置的`$SiteTitle`变量。

沿着脚本继续往下，您会看到:

```
<img src="<? print $GlobalSiteIni->WWWDir; ?>/sitedesign/<?         
print ($GlobalSiteDesign); img/ezpublish-yourcontentmadeeasy.gif"         
height="20" width="290" border="0" alt="" />
```

下面的 eZ publish 构建了一个 URL，用以下内容获取站点的 URL:

```
$GlobalSiteIni->WWWDir
```

这些 eZ publish 类的工作方式很好，不是吗？对于一直在看凯文·杨克在[上的文章的人来说。NET framework](http://www.webmasterbase.com/article/815) ，你会注意到一个显著的相似之处。正如我在本系列的第一部分中提到的，eZ publish 也是一个专门为构建 PHP 应用程序而设计的框架。这是一个好的框架应该做的事情之一:通过提供一个你可以重用的设计良好的类库，让你的生活变得简单。

总之，回到正题。在 frame.php 使用的一些变量在[http://doc.ez.no/article/articleview/204/1/54/](http://doc.ez.no/article/articleview/204/1/54/ )有描述。长话短说，这是您之前看到的应用于 frame.php 的示例文件。请注意，我没有逐字复制和粘贴所有内容。特别是，左侧菜单需要更多的工作，当我们查看 eZ 文章模块时，我会谈到这一点。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"         
              "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">         
<html  xml:lang="no" lang="no">         

<head>         
<title><?php         
// set the site title         

$SiteTitle = $ini->read_var( "site", "SiteTitle" );         

if ( isset( $SiteTitleAppend ) )         
    print( $SiteTitle . " - " . $SiteTitleAppend );         
else         
    print( $SiteTitle );         

?></title>         

<?php         

// check if we need a http-equiv refresh         
if ( isset( $MetaRedirectLocation ) && isset( $MetaRedirectTimer ) )         
{         
    print( "<META HTTP-EQUIV=Refresh ".         
         "CONTENT="$MetaRedirectTimer; URL=$MetaRedirectLocation" />" );         
}         

?>         

<link rel="stylesheet" type="text/css"          
    href="<?          
        print $GlobalSiteIni->WWWDir;          
        ?>/sitedesign/<?         
            print ($GlobalSiteDesign);          
                        ?>/style.css" />         

<script language="JavaScript1.2">         
<!--//         

   function MM_swapImgRestore()         
   {         
      var i,x,a=document.MM_sr;         
      for(i=0;a&&i<a.length&&(x=a[i])&&x.oSrc;i++) x.src=x.oSrc;         
   }         

   function MM_preloadImages()         
   {         
      var d=document; if(d.images){ if(!d.MM_p) d.MM_p=new Array();         
      var i,j=d.MM_p.length,a=MM_preloadImages.arguments;         
      for(i=0; i<a.length; i++)         
          if (a[i].indexOf("#")!=0){         
              d.MM_p[j]=new Image; d.MM_p[j++].src=a[i];         
          }         
      }         
   }         

   function MM_findObj(n, d)         
   {         
      var p,i,x;  if(!d) d=document;         
      if((p=n.indexOf("?"))>0&&parent.frames.length) {         
        d=parent.frames[n.substring(p+1)].document; n=n.substring(0,p);         
      }         
      if(!(x=d[n])&&d.all) x=d.all[n];         
      for (i=0;!x&&i<d.forms.length;i++) x=d.forms[i][n];         
      for(i=0;!x&&d.layers&&i<d.layers.length;i++)         
        x=MM_findObj(n,d.layers[i].document);         
      return x;         
   }         

   function MM_swapImage()         
   {         
      var i,j=0,x,a=MM_swapImage.arguments; document.MM_sr=new Array;         
      for(i=0;i<(a.length-2);i+=3)         
      if ((x=MM_findObj(a[i]))!=null) {         
          document.MM_sr[j++]=x; if(!x.oSrc) x.oSrc=x.src; x.src=a[i+2];         
      }         
   }         

//-->         
</script>         

<!-- set the content meta information -->         

<meta name="author" content="<?php         

    $SiteAuthor = $ini->read_var( "site", "SiteAuthor" );         
    print( $SiteAuthor );         

?>" />         
<meta name="copyright" content="<?php         

    $SiteCopyright = $ini->read_var( "site", "SiteCopyright" );         
    print( $SiteCopyright );         

?>" />         
<meta name="description" content="<?php         

if ( isset( $SiteDescriptionOverride ) )         
{         
    print( $SiteDescriptionOverride );         
}         
else         
{         
    $SiteDescription = $ini->read_var( "site", "SiteDescription" );         
    print( $SiteDescription );         
}         

?>" />         
<meta name="keywords" content="<?php         
if ( isset( $SiteKeywordsOverride ) )         
{         
    print( $SiteKeywordsOverride );         
}         
else         
{         
    $SiteKeywords = $ini->read_var( "site", "SiteKeywords" );         
    print( $SiteKeywords );         
}         

?>" />         
<meta name="MSSmartTagsPreventParsing" content="TRUE" />         

<meta name="generator" content="eZ publish" />         

</head>         

<body bgcolor="#FFFFFF" topmargin="6"         
    marginheight="6" leftmargin="6" marginwidth="6">         

<table width="100%" border="0" cellspacing="0" cellpadding="0">         
<tr>         
<td class="sitetitle">phpPoint.com</td>         
</tr>         
</table>         

<table width="100%" border="0" cellspacing="6" cellpadding="6">         
<tr valign="top">         
<td width="15%" class="menuback">         

<!-- Left menu start -->         

<?         
$CategoryID = 0;         
include( "ezarticle/user/menubox.php" );         
?>         

<br />         

<table class="menu">         
<tr>         
<td class="menuitem">         
<a href="?PrintableVersion=enabled">Printable page</a>         
</td>         
</tr>         
<tr>         
<td class="menuitem">         
<a target="_blank" href="http://developer.ez.no">         
<img src="<?         
       print $GlobalSiteIni->WWWDir;         
     img/powered-by-ezpublish-100x35-trans-lgrey.gif"         
       width="100"         
       height="35" border="0" alt="Powered by eZ publish" /></a>         
</div>         
</td>         
</tr>         
</table>         

<!-- Left menu end -->         

</td>         
<td width="95%">         

<!-- Main content view start -->         

   <?         
   print( $MainContents );         
   ?>         

<!-- Main content view end -->         
</td>         
</tr>         
</table>         

<?         
// Store the statistics with a callback image.         
// It will be no overhead with this method for storing stats         
//         

$StoreStats = $ini->read_var( "eZStatsMain", "StoreStats" );         

if ( $StoreStats == "enabled" )         
{         
    // create a random string to prevent browser caching.         
    $seed = md5( microtime() );         
    // callback for storing the stats         
    $imgSrc = $GlobalSiteIni->WWWDir . "/stats/store/rx$seed-" .         
                $REQUEST_URI . "1x1.gif";         
    print( "<img src="$imgSrc" height="1"".     &oo;@
```

## 分享这篇文章*