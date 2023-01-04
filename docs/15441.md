# 安装 Apache 教程

> 原文：<https://www.sitepoint.com/installing-apache-tutorial/>

Blair Ireland 撰写的这篇深入教程涵盖了安装 Apache 所需的一切，从入门到配置和优化！

##### 教程部分

*   介绍*   Starting Off
    *   下载 Apache
    *   编译源代码
    *   使用 Apache*   Configuring Apache
    *   了解你的指示
    *   服务器端包括
    *   让那些 CGI 的作品
    *   错误文档
    *   内容协商
    *   单个配置文件
    *   旋转原木
    *   多拿一些
    *   买一本书*   Optimize your Apache Server
    *   文件查找
    *   主机名查找
    *   内容协商
    *   五金器具
    *   服务器进程

    那我们开始吧，好吗？

    ##### 介绍

    大多数人把质量和知名度与价格联系在一起。然而在网络上，情况并非如此。目前最受欢迎、质量最高的 Web 服务器是什么？

    *   网景企业服务器？不对。
    *   微软-IIS？又错了
    *   阿帕奇？答对了而且数量惊人。

    根据 Netcraft 1999 年 12 月的网络服务器调查( [Slashdot](http://www.netcraft.com//#el#/), apache is in the lead with 54.49%, followed by IIS at 23.78% and Netscape at 7.02%. Even though the latter two servers are heavily pushed by their companies, with lots of money backing them, Apache, a free, open source server, tops them all. So who runs it, just small Websites? Nope, guess again. Apache is such a powerful server that it runs sites such as /#l#/http://www.slashdot.org/) 、[、【金融时报】T3、](http://www.ft.com/)[、Linux.com](http://www.linux.com)和 [JavaSoft](http://java.sun.com/) 更不用说成吨的其他网站了。

    Apache 项目([http://www.apache.org/](http://www.apache.org/))始于 1995 年，当时一群网站管理员决定他们需要一个稳定、强大的服务器来支持即将到来的网站(其中一个网站是 http://www.hotwired.com/的[)。当时，网上最流行的服务器软件是由国家超级计算应用中心(NCSA)开发的。开发人员罗布·麦克库尔离开了公司，所有的开发都停止了。当时，网站管理员正在为服务器做他们自己的扩展和错误修复，但是这些并没有以任何方式发布。该组网站管理员决定协调服务器的变化，后来被称为“补丁”。这就是 Apache 服务器得名的原因:A-Patchy 服务器。Apache 服务器发布一年后，这一小组黑客创造了互联网上的第一服务器。](http://www.hotwired.com/)

    能够查看服务器的源代码是它最大的优势之一。您不仅可以自己修改和添加特性到服务器(如果您知道 C++，或者 Perl，如果使用 mod_perl 的话)，而且大量的开发人员已经制作了添加到服务器的模块。

    由于它的流行，我们决定创建一组提示和教程来帮助您安装、配置/定制和优化您的 Apache 服务器。由于 Apache 可用于 Unix 和 Windows，我们将指定提示是否是特定于平台的。

    ##### 出发

    安装并启动 Apache 通常被认为是一项令人疲惫不堪的任务，但事实并非如此。事实上，有了这些技巧和教程，对任何人来说都应该是相当容易的。安装 Unix 版本的 Apache 就像安装大多数其他应用程序一样，您下载源代码，编译它，瞧！Windows 版本也使它变得容易；使用安装向导，就像安装任何其他 Windows 应用程序一样。

    无论如何，首先我们需要下载一份 Apache。由于它相对较小(大小从 1 到 3MB，取决于平台)，这应该是一个无痛的过程。之后就是安装的问题了。以下提示将使这一过程尽可能无痛。

    ##### 下载 Apache

    要获得 Apache，显然你必须去他们在 http://www.apache.org/ T2 的站点。进入下载([http://www.apache.org/dist/](http://www.apache.org/dist/))部分或者找到离你最近的阿帕奇镜像([http://www.apache.org/dyn/closer.cgi](http://www.apache.org/dyn/closer.cgi))来获得你的副本。

    如果您想使用 Unix 版本的 Apache，请仔细阅读文件列表并获取 gzipped 副本(apache_1.3.9.tar.gz)。对于 Windows 版本，请下载 Windows 二进制安装文件(apache_1_3_9_win32.exe)。

    由于安装 Windows 版本是最容易的，我将首先谈论这一点。下载完文件后，像往常一样打开它，安装向导将为您打开并安装服务器。由于 Apache 刚开始做 Windows 版本，代码不如 Unix 稳定。Windows 支持完全是实验性的，仅推荐给有经验的用户。Apache Group 并不保证该软件会像文档中描述的那样工作，甚至根本不保证。在安装过程中，您将被要求

    *   Apache 的安装目录
    *   开始菜单名称
    *   安装类型

    典型安装除源代码之外的所有内容。Minimum 不安装手册或源代码，Custom 允许您“自定义”安装的内容。

    现在来看 Unix 版本。因为你下载了一个“tarball”发行版，所以你必须解压它。只需输入**tar-zxf Apache _ 1 . 3 . 9 . tar . gz**。将会创建一个名为 **apache_1.3.9** 的目录。您必须转到这个目录才能继续下一步并编译源代码。

    ##### 编译源代码

    所有的源代码都整齐地放在这个目录(及其子目录)中，你必须编译它才能让它工作。这里我们将使用 GNU cc (gcc 是缩写)编译器来编译源代码。它是免费的，随大多数 Unix 操作系统一起分发。如果它不在你的电脑上，从[http://www.fsf.org/software/gcc/gcc.html](http://www.fsf.org/software/gcc/gcc.html)下载安装。

    不管怎样，一旦进入到 **apache_1.3.9** 目录，从提示符下键入

    **。/配置**

    这将使用默认配置创建一个 makefile。要更改配置，您必须在运行 configure 之前编辑**Apache _ 1 . 3 . 9/src/configuration . tmpl**。您可以从列表中添加/删除模块，并调整许多其他选项。不过通常情况下，您应该让它使用缺省值，尤其是如果这是您第一次安装 Apache。

    有关编辑配置文件的更多信息，请查看发行版中包含的 README.configure 文件。

    还要执行两个命令行命令。类型

    **制作**

    编译服务器，以及

    **制作安装**

    将它放在正确的目录中。缺省值是/usr/local/apache/

    ##### 使用 Apache

    完成所有这些工作后，我们现在可以开始运行 Apache 了。显然有两种方法可以运行它，一种是 Windows，另一种是 Unix。

    **窗户**

    由于 Apache 只是另一个 Windows 应用程序，只需进入开始菜单，然后是 Apache Web 服务器程序组。如果你没有运行 Windows NT，只需选择“启动 Apache 作为控制台应用程序”。如果你在 NT 上运行 Apache，你可以点击“Install Apache as Service(仅 NT)”选项，Apache 将作为一个服务安装在你的计算机上。这意味着它会在每次 NT 启动时自动启动。

    要关闭服务器，在“开始”菜单中还有另外两个选项，它们应该是不言自明的。

    Unix 系统

    要在 Unix 中启动 Apache，您只需运行 httpd。这看起来会像这样

    **/usr/local/apache/httpd**

    但这也取决于您将二进制文件安装在哪里。它将自动使用编译时创建的名为 **httpd.conf** 的配置文件。如果您想使用另一个配置文件，您可以使用 **-f** 参数。

    例如。/usr/local/Apache/httpd-f/usr/local/Apache/conf/httpd . conf

    Apache 发行版提供了另一种启动、停止和重启 Apache 的方法。这个脚本叫做 apachectl。在 Apache src 目录( **apache_1.3.9/src** )中，在提示符下键入 make。您将看到更多编译的文件。make 完成后，转到支持目录( **apache_1.3.9/src/support** )，在那里你会发现一堆新创建的文件。其中，您会发现一些助手脚本，包括 apachectl、htpasswd(用于为受保护的目录设置 Apache 密码)和 rotatelogs(其用法将在后面解释)。还有一些其他文件，但这些是最重要的。

    将这三个文件复制到 Apache 二进制目录中。如果您没有更改默认设置，它将位于 **/usr/local/apache/bin/。**

    要启动服务器，请使用

    **/usr/local/Apache/bin/Apache CTL start**

    还有就是

    **/usr/local/Apache/bin/Apache CTL 停止**

    和

    **/usr/local/Apache/bin/Apache CTL 重启**

    可用，显然是为了停止或重启服务器。

    要让 Apache 在引导系统时启动，请在启动目录(通常是 **/etc/rc.d/init.d 或/etc/rc3.d/)中创建 apachectl 的副本。**)

    检查一下，确保一切正常。随后，从浏览器中查看 **http://localhost/** 。如果你看到任何东西，你就会知道它是否有效。

    唷！现在一切正常，是时候配置我们的服务器了。

    ##### 配置 Apache

    尽管人们普遍认为编辑任何东西的配置文件都很可怕，但对于 Apache 来说，情况并非如此。该配置可以在 **apache_1.3.9/conf/** 目录中找到，尽管有很多文件可用，但实际上只需要其中的三个。

    首先是 **httpd.conf** ，它包含与服务器整体操作相关的指令和配置。例如，服务器日志和服务器管理。

    名单上的下一个是 **srm.conf** 。该文件包含文件系统中资源管理的配置，如别名、目录索引等。

    最后， **access.conf** 。这个文件包含了任何目录下的访问控制信息。所有其他的文件，你可以不去管它们。

    但是，当您第一次安装 Apache 时，文件的名称并不完全是这样的，它们似乎被命名为 **name.conf-dist** 。这意味着它是文件的分发副本。因为我们喜欢备份任何东西，所以只需使用类似下面的命令(来自 unix)

    **CP httpd . conf-dist httpd . conf**

    或者，对于 windows，只需复制文件，并将其重命名。

    另一个注意，在我们开始之前，您必须重新启动 Apache，对配置文件的任何更改才会生效。这是因为文件是在启动时加载的，所以这些更改不会被加载。

    ##### 了解你的指示

    对于刚开始配置服务器的用户来说，Apache 文档非常有用。

    在[http://www.apache.org/docs-1.2/mod/directives.html](http://www.apache.org/docs-1.2/mod/directives.html)，你可以找到你的服务器的可用指令(配置)列表，以及解释。但是应该注意，列出的指令是针对 1.2 版的，而不是新的 1.3 版。大部分应该是一样的，只是有些可能没了，新的可能还存在。

    听起来不像是小费？嗯，在不知道它到底能做什么的情况下，很难完全按照您的需求来配置它。也就是说，这绝对是一个重要的提示。

    还有一本包含这些指令的书。Apache Web 服务器安装和管理指南详细描述了安装和管理 Apache Web 服务器的过程，是一个方便的桌面伴侣。它还包括 Apache 文档的安装和一般管理部分的印刷版本。

    了解你的指令也能让你的生活更方便。如果您不喜欢 Apache 设置 DocumentRoot 的位置(放置 HTML 和其他文件的位置)，您可以使用以下指令在 **httpd.conf** 中轻松地更改它:

    **document root/where/you/want/to/put/your/docs**

    或者，如果您想设置在服务器出现问题时电子邮件将发送到哪里，只需使用以下指令:

    **server admin you @ your . address**

    现在这应该是一个走出去学习更多东西的借口…这会让你的生活更轻松。

    ##### 使用服务器端包括

    当你不只是想为你的用户提供普通的 HTML，但还没有专业知识来编写一些 CGI 脚本时(或者只是懒惰)，试试 Apache 的服务器端包含(SSI)。

    **那么什么是 SSI 呢？**

    基本上，它是一个包含特殊命令的 HTML 文档。如果命名正确，Apache 将在请求时对文档进行预解析，并发送结果文档。SSI 由名为 **mod_include** 的模块控制。

    使用 SSI，您可以做很多事情，例如显示一些环境变量、显示日期，甚至在文档中包含外部文件。

    在使用 SSI 之前，必须确保 mod_include 模块是用 Apache 的其余部分编译的。默认情况下包含它，但是您可能在之前编辑 **Configuration.tmpl** 时注释掉了它。如果是这种情况，取消对该行的注释，并重新编译 Apache。一旦完成，打开你的 **httpd.conf** 文件。

    要使用服务器解析的 HTML 文件，请查找以下部分:

    添加文本/html 类型。shtml
    AddHandler 服务器解析。shtml

    确保你去掉了行前的注释，因为它们是默认的注释。注意他们如何使用？shtml？你可以把它改成你想要的任何扩展名，你只需要记住以后用这些扩展名命名你的 SSI 文件。

    在配置文件中更改这些指令后，重新启动 Apache，让我们创建一个测试 SSI 文件。既然我们用的是**。shtml** 作为我们的 SSI 扩展名，我们将我们的文件命名为**test . shtml**
     **<！DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN ">
    <HTML>
    <HEAD><TITLE>测试服务器端包括</TITLE></HEAD>
    <BODY BGCOLOR = " # FFFFFF ">
    当前日期:<！–# echo var = " DATE _ LOCAL "–>
    <P>文档名称:<！–# echo var = " DOCUMENT _ NAME "–>
    <P>环境变量:<BR>
    <PRE><！–# printenv-></PRE>
    </BODY>
    </HTML>** 
    如果它看起来正确，并且您的所有空白都已填写，则 SSI 在您的服务器上已正确启用。恭喜你！

    有关 SSI 的更多信息，请查看 Scripts.com 的 SSI 部分，网址为[http://ssi.thescripts.com/](http://ssi.thescripts.com/)。

    ##### 让那些 CGI 的作品

    从 SSI 毕业后，是时候设置你的服务器使用 CGI 了。CGI 代表公共网关接口，但是由于某种原因，人们总是把它和 Perl 混淆，Perl 是用于 CGI 的最流行的语言。事实上，CGI 可以是任何语言，比如 C/C++、Java、TCL 或 Perl，还有许多其他语言。

    Apache 中控制 CGI 的模块叫做 **mod_cgi** ，默认编译。如果您从 **Configuration.tmpl** 中删除了它，那么您必须将它添加回去，并重新编译 Apache，然后才能继续。

    启用 CGI 的第一种方法是创建一个包含脚本的特定目录。ScriptAlias 就像普通的别名一样，只是当客户端请求时，文档被视为应用程序，而不是文档。

    典型的 ScriptAlias 设置如下所示
     **script alias/CGI-bin/" @ @ Server Root @ @/CGI-bin/"** 
    这使得 **/cgi-bin/** 成为您的服务器 **Root/cgi-bin/** 目录的别名。 **@@ServerRoot@@** 反映了您在配置文件顶部附近设置的 ServerRoot 变量。你可以用任何方式来写，比如

    **script alias/CGI-bin/"/usr/local/Apache/CGI-bin/"**

    或者任何你想放 cgi-bin 目录的地方。您可以按照上面的配置使用下面的
     **<目录"/usr/local/Apache/CGI-bin/">
    选项 ExecCGI
    AddHandler CGI-script。cgi。pl
    </Directory>** 
    AddHandler 部分允许你拥有带**的 CGI 脚本。cgi** 和**。本例中的 pl** 扩展。下面是另一种方法。这个方法允许你在脚本化目录之外使用 CGI。您将使用以下配置；
     **AddHandler CGI-script。cgi。pl** 
    注意它不是在目录容器中找到的吗？这样，AddHandler 指令适用于所有文档，而不仅仅是在某个目录中找到的文档。在这里，我们允许。cgi 和。pl 扩展再次为我们的 CGI 脚本。你可以用任何东西。

    CGI 应用程序在执行时，以拥有 Apache 服务器进程的用户的名字运行。默认情况下，用户被称为**‘nobody’**，这是由用户和组指令指定的。只有当 Apache 服务器由 root 用户启动时，这些指令才能起作用。
     **用户无名小卒
    组#-1** 
    在大多数情况下，你在这里什么都不用改变。这并不总是合适的，因为，如果您的系统上有多个用户，您可能希望 CGI 以该用户的名字运行。这对虚拟主机系统来说是极好的，因为你可以用客户的名字运行 CGI 并访问他们的文件。

    为了解决这个问题，Apache 创建了 suEXEC 程序，包含在 Apache 服务器中。要了解如何在您的系统中使用它，请查看[http://www.apache.org/docs-1.2/suexec.html](http://www.apache.org/docs-1.2/suexec.html)。

    ##### 错误文档

    有没有注意到一些网站有自己的错误文档？比如，试试去这里([http://www.thescripts.com/nopage.html](http://www.thescripts.com/nopage.html))。你得到一个 404 错误，但不是任何 404 错误，我们有一个定制的 404 错误页面。您也可以对您可能遇到的任何其他错误这样做(我们也有一个 403 错误页面)。仅供参考，404 错误意味着没有找到页面，403 意味着您没有访问特定文档的权限。

    那么我们该怎么做呢？其实挺简单的。不使用 Apache 的默认/有点难看的错误页面，您可以指定针对某些错误返回的某些文件。这里使用的指令叫做 ErrorDocument，下面我们找到了一个例子
     **error document 404/four ohfour . html** 
    这就是 404 错误页面。在本例中，它位于 htdocs 目录中，名为**fourohfour.html**。如果你从浏览器访问它，它会在[http://www.yoursite.com/fourohfour.html](http://www.yoursite.com/fourohfour.html)

    要添加另一种类型的错误文档，例如 403，只需执行以下操作；
     **ErrorDocument 403/four ohthree . html** 
    由此可见，指令 error document 中的第一个参数是错误号。第二个是页面的位置。

    请注意，最好在错误文档中使用完整的图像和链接路径。这是因为使用相对链接/图像路径是不准确的。例如，如果你去[http://www.thescripts.com/nothere/nothere.html](http://www.thescripts.com/nothere/nothere.html)，fourohfour.html 页面会被提供给你，就好像它是在一个不存在的目录中，然而它实际上是在你的 html 文档的根目录中。关于 ErrorDocument 指令的更多信息，请访问 http://www.apache.org/docs/mod/core.html

    ##### 内容协商

    我知道你在想什么，什么是内容谈判？首先，这是 Apache 的一个经常被忽视的特性。更准确地说，它被称为内容选择，是从几个可用文档中选择最符合客户浏览器功能的文档。

    你为什么需要它？

    嗯，你没有…但是，如果你的网站是针对多语言地区的，那么拥有它会对你有很大的好处。你的文档不仅会被正确地提供给访问者，而且还会提高他们在你网站的停留时间。默认情况下，内容协商在您的服务器中编译，因为它由 mod_negotiation 提供支持。如果由于某种原因，你没有用 Apache 编译这个特性，你必须回到你的 **Configuration.tmpl** 文件并把它放回。

    好吧，你现在需要什么？

    嗯，你必须选择你将要使用的语言，并且要有两种语言的内容。实际上有两种方法我们可以使用:使用变体文件，这可以在这里找到讨论([http://www.apacheweek.com/features/negotiation](http://www.apacheweek.com/features/negotiation))，或者，通过使用文件扩展名。

    对于这个例子，我们将使用三种不同的语言，英语，法语和德语。

    现在打开 **access.conf** 文件，让我们开始吧。首先，我们需要指定一个目录，我们称之为国际目录，并设置它用于内容协商。国际目录将位于根文档目录之外，在我们的例子中，这个目录是**/usr/local/Apache/htdocs/**。
     **<目录/usr/local/Apache/htdocs/international>
    选项多视图
    </目录>** 
    选项多视图设置目录，以便服务器进行文件名模式匹配，从结果中进行选择。现在，打开 httpd.conf，这样我们可以添加要查找的语言，以及服务器将如何识别它们。我们将使用指令 add language
     **add language en。添加语言 fr。添加语言。我们在这里添加了英语、法语和德语，都用它们右边的扩展名来标识。**

    默认情况下，已经设置了相当多的语言。您可以将它们注释掉，并根据需要进行添加。

    LanguagePriority 指令允许您在内容协商期间出现平局时优先考虑某些语言。语言按降序或偏好排列。
     **language priority en fr de** 
    那么你现在怎么给你的文件命名呢？最好的方法是对英文文档执行类似于 **test.html.en** 的操作，对法文文档执行类似于 **test.html.fr** 的操作，对德文文档执行类似于 **test.html.de** 的操作。当你想链接到这个文件，你只需使用**test.html**。

    有关内容协商的更多信息，请参见[http://www.apache.org/docs/content-negotiation.html](http://www.apache.org/docs/content-negotiation.html)。

    ##### 单个配置文件

    天哪，这有什么用？

    嗯，它会把你所有的选项放在一个文件里，当你想编辑任何东西的时候，这让生活变得很容易。在这种情况下，我们将使用 httpd.conf 来存储所有的指令。有关这三个配置文件的一些注释，请参见[http://www.apache.org/info/three-config-files.html](http://www.apache.org/info/three-config-files.html)。

    不管怎样，回到单个配置文件的问题。现在您正在使用三个配置文件: **httpd.conf** 、 **access.conf** 和 **srm.conf** 。你不需要。只需将后两个文件中的重要内容清空到 **httpd.conf** 中，然后也添加这两行:
     **access config/dev/null
    resource config/dev/null** 
    这将通知 Apache**httpd . conf**是唯一的配置文件。有了这些，您就可以简单地删除 **srm.conf** 和 **access.conf** 文件，面带微笑地继续工作。

    ##### 旋转原木

    那么，你一直听到的这些日志文件是什么？

    每次用户从您的站点请求文档时，服务器都会将该请求的记录“记录”到日志文件中。日志文件包含有关用户的重要统计信息，例如他们的主机、日期/时间和包含浏览器信息的请求行。

    如果您的站点非常繁忙，或者已经有一段时间没有对日志做任何处理了，那么它们可能会非常大。通常，它们可以在短时间内达到许多 MB。由于日志文件的最佳应用是与其他软件一起进行日志分析，因此大的日志文件会使这一过程慢如蜗牛。

    那么，你能做些什么呢？使用日志旋转。

    日志轮换是一个过程，它获取一个日志文件，将其内容放入一个新文件中，然后清除它自己的内容。因为这是一个相当枯燥的过程，所以有几个脚本可以帮助您完成，包括 Apache group 自己的一个脚本。

    例如，一个脚本是一个小工具，它允许快速分割和处理日志文件。该实用程序将为每天创建一个新的日志文件，以日期为扩展名。创建新的日志文件后，旧的日志文件可以被压缩或移动到新的位置。这个工具叫做 logbox，可以从 ftp://ftp.lemuria.org/pub/Code/logbox.tar.gz 的[买到。在使用它之前，您必须在您的服务器上编译它。](ftp://ftp.lemuria.org/pub/Code/logbox.tar.gz)

    Apache group 开发了另一种分割文件的方法。早先你编译它的时候，也是你编译 apachectl 和 htpasswd 的时候。如果你使用了默认设置，你也在事后把它复制到了 **/usr/local/apache/bin/** 。这个程序叫做 rotatelogs(多么巧妙！)，它与 Apache 中的 TransferLog 指令一起使用。

    在 **httpd.conf** 中添加这样的:
     **log format " % h % l % u % t " % r " % s % b " % { Referer } I " " % { User-agent } I " "
    error log/usr/local/Apache/logs/error _ log
    transfer log " |/usr/local/Apache/bin/rotatelogs/usr/local/Apache/logs/access _ log 86400 "** 
    Let

    LogFormat 指令实际上没什么好担心的。它只是为访问和错误日志的外观指定了模板格式。有关 LogFormat 提供的选项的更多信息，请参见[http://www.apache.org/docs-1.2/mod/mod_log_config.html](http://www.apache.org/docs-1.2/mod/mod_log_config.html)。

    ErrorLog 设置文件的名称，服务器会将遇到的任何错误记录到该文件中。这一点很重要，要不时地检查一下，看看你的站点/服务器可能出了什么问题。

    现在，到了你期待已久的部分。

    TransferLog 指令。在这里，我们同时将它与 rotatelogs 程序混合在一起。从技术上讲，这里有一个关于 TransferLog 的参数，但它可以被分解。第一部分是我们的 rotatelogs 程序的位置(在本例中，它位于**/usr/local/Apache/bin/rotate logs**)。

    第二部分是我们的 **access_log 文件**的位置(我们记录服务器请求的文件，位于**/usr/local/Apache/logs/access _ log**)。最后一部分设置循环日志文件之前的时间。它以秒为单位，因为 86400 秒等于 1 天(24 小时)，所以我们每天轮换文件。您可以将此设置为您喜欢的任何时间段。在这种情况下，旧日志文件(每次轮换后)的生成名称将是**/usr/local/Apache/logs/access _ log . nnnn**，其中 **nnnn** 是日志开始的系统时间。每次轮换时间后，都会启动一个新的空日志。

    ##### 多拿一些

    因为 Apache 是开源的，所以它有很多额外的功能。这些额外的功能可以大大提高服务器的性能，甚至给你的网站带来更多的可能性。两个最著名的插件包括 **mod_perl** 和 **mod_php**

    mod_perl 是 Apache Web 服务器和 Perl(【http://www.perl.com/】)这种流行的 CGI 脚本语言的极好结合。该模块复制了 Perl 解释器，并将其嵌入 Apache 本身。这不仅加快了现有 CGI 的速度，而且还允许您用 Perl 本身编写更多的模块。与通常的方法不同，Perl 脚本以这种方式编译一次。通常，脚本是由解释器在运行时编译的，这使得它们从一开始就运行得有点慢。如果它们已经编译好了，它们会立即启动，使这个模块成为一个高流量网站的优秀补充。

    mod_php 是 Apache 的另一个伟大的补充。强大的服务器端脚本语言 PHP(【http://www.php.net/】)也是开源/免费的，编译后可以很容易地与服务器链接。PHP 脚本可以像普通的 CGI 一样运行，但是这种方法允许它们以更快的速度运行。

    这两个附加组件必须从一开始就用 Apache 编译，因为它们是模块。如果您想使用它们，只需下载它们，编辑您的 Configuration.tmpl 文件，运行 configure，然后照常编译。

    尽管这些模块有点复杂，但还是有说明。您可以分别在 http://perl.apache.org/和 http://www.php.net/的[和](http://perl.apache.org/)[查阅文件。](http://www.php.net/)

    ##### 买一本书

    我不得不说这是最容易找到好建议的地方。不仅你可以随时使用它们(因为它们应该放在你旁边)，而且如果你在洗手间没有笔记本电脑，你也可以在那里阅读。有几本好书，像往常一样，我最喜欢的是奥莱利出版的。

    阿帕奇:权威指南

    这本书吹嘘了 Apache 开发团队的一名成员。在引导读者完成安装 Apache 的过程之前，它从一个关于 Web 服务器做什么的学术讨论开始。Apache 的安装得到了很多关注，您会学到关于网站安全性和其他偏好的知识。

    [阿帕奇服务器圣经](http://www.amazon.com/exec/obidos/ASIN/0764532189/o/qid=946951096/sr=2-2/103-8761155-1094243)

    Apache 服务器圣经是管理 Apache Web 服务器的优秀指南，面向没有 Web 管理经验的人。主题包括编译源代码，安装，配置问题。

    [Apache Server for Windows 小黑本:小黑本](http://www.amazon.com/exec/obidos/ASIN/1576103919/ref=sim_books/103-8761155-1094243)

    这本书将向您展示如何使用 Apache 的功能。这本书甚至谈到了诸如 CGI、数据库管理、加密等内容。

    ##### 优化您的 Apache 服务器

    一旦你的网站变得受欢迎，你将不得不开始担心另一个问题:如何充分利用你的服务器。随着流量负载不断冲击它，你必须找到一种方法来优化它，以更好地处理流量。

    这就是教程的这一部分发挥作用的地方。在开始之前，我必须声明，所有这些技巧都是针对 unix 服务器的，因为 Apache 根本不是针对 Windows 优化的。

    在考虑速度问题和 Apache 时，我们必须考虑硬件/操作系统、您的配置文件以及您如何从一开始就编译服务器。如果你想阅读你的所有性能选项，请查看[http://www.apache.org/docs/misc/perf-tuning.html](http://www.apache.org/docs/misc/perf-tuning.html)和[http://www.apache.org/docs/misc/perf.html](http://www.apache.org/docs/misc/perf.html)。您可能还想看看 O'Reilly 的书《Web 性能调优》。

    ##### 文件查找

    这是你可能从未想过的事情。这里的两个罪魁祸首简直就是**。htaccess** 文件和符号链接。对于那些觉得这个页面有趣的人来说，**。htaccess** 文件是一种访问控制文件。它们可以包含 Apache 指令，并应用于它们所在的目录及其下的所有内容，仅被其他**覆盖。htaccess** 文件。

    默认情况下，Apache 指令 AccessFileName 被设置为**。htaccess** ，所以除非你改变它，否则它就被称为。因此，您可以把它看作是另一个 Apache 配置文件，尽管它是可选的。

    向客户端返回文档时，服务器会在文档路径的每个目录中查找具有此名称的访问控制文件(如果该目录启用了访问控制文件)。例如，假设我正在获取一个在 http://www.mysite.com/test/test2/test3/test4.html 找到的文档，我的 **DocumentRoot** 指令被设置为 **/usr/local/apache/htdocs** 。阿帕奇会寻找一个**。htaccess** 文件在以下目录中；
     **/
    /usr
    /usr/local
    /usr/local/Apache
    /usr/local/Apache/htdocs
    /usr/local/Apache/htdocs/test
    /usr/local/Apache/htdocs/test/test 2
    /usr/local/Apache/htdocs/test/test 2/test 3** 
    你看到什么了吗

    启用访问控制文件后，Apache 必须检查大量不必要的文件，这会降低性能。

    那么我们如何解决这个问题呢？

    我们可以使用目录容器来指定容器中不接受任何覆盖。在这种情况下，我们使用根目录。
     **<目录/ >
    AllowOverride 无
    </目录>** 
    在此之后，我们或许希望能够仍然使用。htaccess 文件。如果是这种情况，你可以再次启用它们，
     **<目录/usr/local/Apache/htdocs>
    allow override All
    </目录>** 
    就这样。AllowOverride 指令实际上有一些选项，要查看它们，请查看[http://www.apache.org/docs-1.2/mod/core.html](http://www.apache.org/docs-1.2/mod/core.html)。

    Symbollic 链接也有类似的问题，除了使用了不同的指令之外，修复方法几乎完全相同。
     **<目录/ >
    选项 follow symlinks
    </目录>** 
    后跟
     **<目录/usr/local/Apache/htdocs>
    选项-follow symlinks+SymLinksIfOwnerMatch
    </目录>**
    现在我们摆脱了文件查找问题，因为没有不必要的文件检查。

    ##### 主机名查找

    默认情况下，Apache 将指令 HostnameLookups 设置为 off。这是一件好事，因为启用它后，每个客户端对服务器的请求都会导致对名称服务器的查找请求。

    如果您使用 order 指令以及 allow 和 deny 来限制对站点特定部分的访问，这也是一个问题。在这种情况下，最好使用 IP 地址，否则，您必须进行另一次名称服务器查找。

    有了这种方法，Apache 服务器就不必做任何不必要的 DNS 工作，可以继续工作。如果您的 CGI、PHP 或其他任何程序可能需要使用主机名查找，您可以使用文件容器来指定。
     **<【文件】”。(PHP 3 | CGI)$>

    </Files>** 上的主机名查找

    ##### 内容协商

    我又要说我的花言巧语了，内容谈判。我知道我建议早点使用它，但那只是在它对你有益的情况下。否则，你应该远离它。为什么？由于它需要搜索特定的文件，而不是确切的名称，搜索部分会增加服务器的负载，因此会稍微减慢速度。此外，对于要求您提供文件的指令，比如 DirectoryIndex，不要只提供索引，而是提供实际的文件名。例子如下；
     **directory Index Index** 
    Index 是一个通配符，因为没有指定扩展名，它必须再次进行一些搜索才能找到文件。列出一个名单会是一个更快、更有效的方法。把最常见的名字放在最前面，按降序排列。
     **DirectoryIndex index.html 指数. shtml 指数. cgi** 

    ##### 五金器具

    和大多数软件一样，Apache 需要内存，尤其是当你的流量很大的时候。如果到了你的操作系统必须开始与硬盘交换内存的地步，你必须要么获得更多的 RAM，要么将指令 MaxClients 设置得低一点。

    交换内存不一定很慢，但是对于客户端来说，确实很慢。他们会被激怒，点击几次 stop-reload，进一步增加服务器的负载。不是什么好事。

    除此之外，有了足够快的硬盘、网卡和 CPU，你的服务器应该表现得相当好(至少在硬件方面)。通过一些实验，您应该能够找到一个最佳配置。

    ##### 服务器进程

    MinSpareServers、MaxSpareServers、StartServers 和 KeepAliveTimeout 等设置(如果 KeepAlive 设置为“开”)。SpareServers 指令指示服务器要创建多少个“子”进程。Apache 使用这些指令来确定它必须适应的负载。

    如果您的服务器有等待请求的子进程，并且数量超过了 MaxSpareServers 设置，那么其中的一些子进程将被终止。如果数量小于 MinSpareServers，将创建一些子进程。这些指令的设置将直接关系到您的服务器性能。如果您没有足够的子进程来处理当前的服务器负载，一些请求将会延迟，导致您的服务器变慢。如果你没有足够的内存和 CPU 来处理，太多的进程也会降低服务器的速度。

## 分享这篇文章