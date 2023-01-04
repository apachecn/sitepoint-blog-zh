# 网络主机整顿:审查了 6 个主机方案

> 原文：<https://www.sitepoint.com/developer-friendly-web-hosts/>

如果您和您的客户手头总是有完全正确的硬件，安装了您最喜欢的环境的正确版本，并有能力运行您的所有应用程序，那就太好了。当然，在现实世界中，这种情况只在某些时候出现。这就是为什么每个开发者都应该有一些托管的选择，但是你能依靠哪一个来支持你的应用呢？在本文中，我们将为您的下一个项目考察一些对开发人员最好的 web 主机。

##### 候选人

我们比较了六家相对开发人员友好的主机提供商。排名不分先后，我们考察了小橙、DreamHost、Hosting Rails、Media Temple、RailsPlayground 和 WebFaction 的计划。

在评估主机时，我们已经寻找了所有常见的疑点:磁盘空间、每月成本、合同期限等等。在为研究选择主机时，我们试图坚持适合开发人员的主机:低资源限制、对简化管理面板的支持差或缺乏支持以及有限的控制是导致我们从研究中排除一个计划的一些属性。

最终阵容中几乎所有的计划都有足够慷慨的资源限制，所以你永远不需要担心它们；大量的数据库、用户、域和邮箱，唯一的限制是磁盘空间和带宽。在这一端，CPU/RAM 的使用是一个更大的问题，其中大多数主机都有针对这种使用的某种策略。

另一个主要因素是语言支持:我们希望所有这些主机至少支持 PHP、Python 和 Ruby，最好支持 Perl。对于非 PHP 应用程序的部署，FastCGI 支持是最少的，mod_python 和 Passenger (mod_rails)是最理想的。

最后，我们通过在这些 web 主机上实际运行软件，尝试了一些真实世界的测试。我们从 Joomla 的预安装检查开始，它验证 PHP 配置指令的一些合理值，然后安装一些基本的 PHP 和 Django 应用程序，使用主机提供的任何安装方法。

我们已经在一个方便的表格中列出了每个计划和功能[，但是当然，一个好的比较必须更深入——让我们更详细地讨论每个产品。](https://www.sitepoint.com/examples/developer-friendly-web-hosts/)

##### 一个小橘子:小的

我们选择研究[一个小橙子的小计划](http://www.asmallorange.com/hosting/shared/)——虽然即使是小计划也能很好地服务于一个小应用程序，但这个小计划与我们阵容中的其他计划相比更好。

在小橙，提供的控制面板是 cPanel 11，乍一看，它有许多选项，几乎可以完成所有可以想到的任务——电子邮件设置、MySQL 管理等等。然而，使用通用的控制面板解决方案会使一些问题变得不清楚:例如，要在小橙上启用 SSH 的 shell 访问，用户必须在注册时请求它，或者通过完全独立的系统提交支持请求。但是，cPanel 系统仍然包含一个用于设置 SSH 访问的区域。

MySQL 在这里是一个有趣的问题，事实上在我们审查的所有 cPanel 主机上——DB 服务器总是与 web 服务器运行在同一台机器上。这篇综述中的另外两个 cPanel 主机也有这个问题，这可能只是测试软件的一个小问题，但是可能会影响您在不切换服务器的情况下扩展部署的应用程序的能力。

一个小桔黄色的领域是 PHP 部署；Joomla 预安装检查没有问题，除了`display_errors`指令(`ON`而不是 Joomla 推荐的`OFF`)。这在接受调查的主机中很常见，但是只要在每个应用程序的基础上禁用了`display_errors`的`ON`值，这只是一个小问题。

PHP 的性能似乎非常好。为了测试 PHP 的性能，我们使用 WordPress 转换了一个包含 500 多篇文章和评论的 XML 文件。一个小型 Orange 的服务器快速完成了 XML 导入，并在一两分钟内导入了所有的帖子。我们在 Media Temple 上进行了类似的测试，十分钟后服务器仍在运行！

虽然 Django 没有官方支持，但在一个小型 Orange 的 wiki 上找到的[说明已经足够详细，可以启动并运行 FastCGI 部署。然而，FastCGI 方法非常冗长，并且几乎没有优化和调优的空间；在一个单独的域上提供静态文件本来可以比现在更简单，cPanel 系统提供的帮助很少。](http://wiki.asmallorange.com/HOWTODjangoFastCGI)

##### 梦幻主机

DreamHost 只提供[一种方案，](http://dreamhost.com/hosting.html)其价格根据你选择提前还款的时间长短以及你购买的额外服务而变化。

DreamHost 的用户群比这项研究中的大多数主机都要大，它开发了一系列基于自定义控制面板的内部系统。在面板中，您会发现一组常见问题的快速访问选项:数据库、域、用户等的管理，以及对邮件的细粒度控制，一些报告系统，以及从 crontabs 到邮件列表再到 Subversion 存储库的各种实用工具。还提供了各种一键安装选项，包括最新版本的 Joomla。

Joomla 预安装检查工作正常，只有一个小问题——`display_errors ON`。然而，可以在 DreamHost 服务上使用自己的 PHP 配置；详情见 DreamHost wiki。CakePHP 部署工作正常，没有任何问题。

DreamHost 没有对 Django 的官方支持，所以一般的解决方案是在 FastCGI 下运行 Django。我们在 DreamHost 服务上部署了一个标准的 Django 博客应用程序，遵循 Django wiki 页面上的冗长说明，没有遇到任何重大挫折。由于 DreamHost 灵活的域控制，通过单独的域提供媒体文件相对简单。

对于 Rails，DreamHost 有一个非常好的 Phusion Passenger (mod_rails)设置，您可以在没有系统管理员帮助的情况下自行配置。您可以在每个域的基础上启用它，这是部署 Rails 应用程序的绝佳选择。

##### HostingRails:初学者

我们试驾了 [HostingRails 的初级计划](http://www.hostingrails.com/)，价格极其低廉，每年仅 43.08 美元。

HostingRails 标榜自己是面向 Rails 的提供商，但实际上它是一个非常好的全能提供商，提供相当简单的产品。他们的服务器运行 cPanel 10，装载了足够多的 Apache 模块、Ruby gems 和 PHP 扩展。服务器管理是件简单的事情；欢迎电子邮件中提供了您的 shell 详细信息，您的所有域或多或少都可以开箱即用。

运行 Joomla 预安装检查导致了与 RailsPlayground 相同的错误 web 服务器无法写入其配置文件，除非它是全球可读的。类似地，CakePHP 的 tmp 和 tmp/cache 文件夹必须是全局可写的，Cake 才能写出它的数据库、缓存和其他临时数据。Joomla 还注意到`display_errors`就是`ON`。然而，总的来说，PHP 体验非常令人满意，没有什么大问题。URL 重写也是现成的。

然而，我们的 Django 测试是一种不同的体验。wiki 指南首先指导我们在我们的主目录中安装我们自己的 Python 副本，建议安装的 Python 版本(我们服务器上的 2.4.3)对于 Django 的最新版本是不够的。编译完 Python 之后，我们必须安装 Python MySQL DB 模块，以便从 Django 的数据库适配器建立 MySQL 连接。一些快速的改变将系统指向我们的定制 Python 安装；然后，我们必须复制 django-admin.py 文件，并将其指向我们的自定义 Python。最后，我们设置了动态 FastCGI 配置。

虽然 Django 的部署本来可以做得更好，但是正如我们所料，HostingRails 的 mod_rails 功能远远领先于其他产品！HostingRails 的 wiki 上的 Rails [部署说明非常简单且相对快速，尽管其中一部分需要技术支持人员的手动干预。](http://www.hostingrails.com/wiki/61/How-to-deploy-a-Rails-app-with-Passenger--mod_rails-)

##### 媒体圣殿:(gs)网格服务

Media Temple 的(gs)网格服务产品在这项研究中表现突出，不仅仅是价格——虽然每年 240 美元，但对于备用测试平台来说并不理想。Media Temple 的服务器集群基础设施被宣传为极其可靠的系统，用于托管可能会经历意外流量爆发的站点；也就是说，经历短时间的显著高于平均水平的负载，比如在 Slashdot 或 Digg 上出现。

Media Temple 的 Django 和 Rails 部署解决方案名为 Containers，是本次评论中的一个亮点。从每月 20 美元起，一个容器基本上是一个迷你虚拟私有服务器，用于你的 Django、Rails 或 MySQL 设置。例如，Django 容器为您的 Django 应用程序分配了一定数量的 RAM，并为您的 Django 版本和每个容器安装的 eggs 提供了简单的管理。(容器本质上是 FastCGI 线程前面的一个 [lighttpd](http://www.lighttpd.net/) 服务器，都是预先配置好的。)

Media Temple 控制面板设计得非常好，可以对系统进行合理的控制。特别是，该面板提供了大量的文档，甚至还附带了概述用户名、访问域和其他服务细节的指南。虽然该面板本身缺乏与其他审查产品相同的控制级别，但对于部署来说非常有用。在撰写本文时，只有 Drupal、WordPress 和 Zen Cart 可以作为一键安装选项使用。

值得一提的是 [Media Temple 知识库](http://kb.mediatemple.net/)，这是一个惊人的资源，在 web 主机指南中具有无与伦比的深度。它甚至包括适合最终用户的视频教程，如电子邮件程序配置。

Joomla 安装结果与 DreamHost 上的结果相同；所有需要的设置都满足了，我们再次发现`display_errors`被设置为`ON`。我们的 CakePHP 部署进行得很顺利，但正是 Django/Rails 容器系统使这一产品物有所值:我们的示例 Django 应用程序在几分钟内就可以投入使用，相比之下，小型 Orange 和 DreamHost 的应用程序需要一个多小时才能投入使用。单个容器可以很好地管理多个应用程序，并且应用程序级控制对于部署来说非常方便。

##### 铁路运动场

RailsPlayground 提供了一系列相当标准的托管、VPS 和专用托管服务——我们选择了[开发者托管一级。](http://railsplayground.com/plans-products/developer-hosting/)服务器相对标准化，运行 cPanel 10，该公司在 Rails 和 Django 圈子中因提供比通常更少痛苦的 FastCGI 部署而闻名。有趣的是，RailsPlayground 为静态流程提供了一个带有 FastCGI 的 DIY lighttpd 部署解决方案，支持小组为此分配了一个专用端口。

cPanel 的设置与一个小橙子的设置几乎相同，对任何有 cPanel 以前版本经验的人来说都有意义；对于我们其他人来说，在第一次登录时，一组介绍性的视频会很有帮助。有趣的是，cPanel 提供了一个安装 [PEAR](http://pear.php.net/) 包的部分，这个选项对于处理需要它们的应用程序，尤其是开源项目，可能很有用。

在测试 Joomla 和 CakePHP 部署时，我们不得不手动使文件夹对 web 服务器是全局可写的；没有这个，Joomla 就无法更新自己的配置文件，同样 CakePHP 也抱怨对其临时文件夹的控制不足。幸运的是，当你开始使用你的帐户时，你会从 RailsPlayground 收到五封电子邮件，SSH 会在其中一封邮件中提供详细的访问信息。几个小时后，我们准备好了。Joomla 再次发现`display_errors`设置为`ON`，但除此之外 Joomla 和 CakePHP 都运行良好，包括 Cake 的 URL 重写。

Django 的部署在 RailsPlayground 上轻而易举——虽然不如 Media Temple 的方法，但也相对简单。在一个单独的域上提供文件不成问题，我们使用的服务器安装了 Django 的最新稳定版本，django-admin.py 已经准备就绪。RailsPlayground wiki 有一个[有用的 Django 和 Rails 部署指南](http://wiki.railsplayground.com/railsplayground/show/How+To+Use+Django+at+RailsPlayground)解释每个步骤。

##### 网络派系:共享 1 个计划

最后，或许也是最重要的一点——我们测试了[网络派系的共享 1 计划](http://www.webfaction.com/services/hosting),这可能是本次综述中最独特的一项服务。

前身为 python-hosting.com 的网络派系专注于网络应用程序部署，并取得了惊人的成果。控制面板是一件相当简单的事情，一个完成工作的定制系统——嗯，直到我们看到域和应用程序。web inflation 有一种独特的一键安装方法，在~/webapps 文件夹下创建应用程序，从 Rails 到 Drupal 到 Django 都有。一旦创建了应用程序，就可以将网站配置为在特定的 URL 上安装一个或多个应用程序。

我们开始创建一个“Static/CGI/PHP”站点，并在一分钟内准备好 Joomla 预安装检查。来自安装者的唯一警告是我们的老朋友，`display_errors`–`ON`而不是`OFF`–其他一切都准备好了。然而，我们的 CakePHP 安装暴露了一些意想不到的问题:由于 web fabrication 对每个应用程序的托管 URL 重写，我们无法让 mod_rewrite 使用 Cake 自己的漂亮 URL。然而，其他一切都运行良好。

然而，控制面板确实在我们的 Django 部署中大放异彩；这个过程非常简单，不需要通过 SSH 登录就可以启动并运行第一个项目模板。各种各样的 Django 版本(在撰写本文时，有 0.96.3、1.0、1.0.2 和 trunk)都是可用的，并被推荐使用，少数以前的版本被标记为“不安全”。web 派系甚至提供了 mod_python 和 mod_wsgi 之间的选择；我们在这篇评论中尝试了 WSGI 方法。在创建了 Django 主干应用程序之后，我们可以立即加载 URL 并准备就绪。

这个部署和 Media Temple 差不多，而且每月不到 10 美元(取决于你预付多少钱)，这对 Django 开发来说绝对是一个极好的选择。如果你对诸如 [CherryPy](http://cherrypy.org) 、 [Pylons](http://pylonshq.com/) 、 [Turbogears](http://turbogears.org/) 之类的框架感兴趣，或者你可能喜欢在 mod_python 下运行的任何其他东西，看看网络派系计划；对所有这些的官方支持都是现成的。

##### 判决

在这篇评论中，我们已经探讨了几个优秀的托管选择，你选择哪一个取决于你和你需要什么。以下是我们挑选的常见场景。

**无争议的 Rails/Django 部署**

为了尽可能简单地部署 Rails 或 Django 应用程序，很难越过 Media Temple。他们的网格服务器平台可以更加灵活，但是容器系统使得推送应用程序变得轻而易举，同时仍然与现有的版本控制兼容。

亚军:web division 的 Django 和 Rails 部署系统令人难以置信，比 Media Temple 选项更灵活，价格也更合理。绝对值得一看。

**PHP 部署服务器**

一个小橘子轻而易举地赢得了这场比赛。cPanel 11 管理面板有足够多的选项来控制 PHP，而且服务器足够强大，可以应付您计划向它们扔的任何东西。

亚军:RailsPlayground:恰到好处的能力让您的应用程序启动并运行，并支持足够多的非 PHP 环境来探索。请注意那些文件权限！

**通用 Web 开发测试平台**

这一次我们将不得不使用 WebFaction，它有多种选择，可以轻松地尝试 PHP、Django 和 Rails 应用程序。专门构建的体系结构让您可以灵活地随心所欲地尝试应用程序。

亚军:一个小橘子。有了足够的灵活性、相对最新的库和应用程序以及稳定的环境，您将能够避免部署麻烦。

## 分享这篇文章