# 版本控制和 Web 开发

> 原文：<https://www.sitepoint.com/version-control-and-web-development/>

CVS 或并发版本系统允许建立一个健壮的版本控制系统，以确保在测试和质量保证期间设计和开发网站时，以及在内容管理和改进期间的后期制作时，能够控制对网站的更改。

虽然有许多 web 开发人员在使用 CVS，但它最广为人知的用途是用于软件开发项目。然而，考虑到 21 世纪网站的动态，以及 web 应用程序的激增，CVS 应该是一个考虑因素，即使是那些偶尔使用项目分包商的独立开发人员。

[CVS(此处为](http://www.cvshome.org))通常已经安装在您的*Nix 服务器上，包括 OS X。客户端可用于多种平台(Windows、Macintosh 和*Nix)，并内置于一些 web 开发 ide 中(如 Macromedia Dreamweaver)。CVS 主页还包括大量关于如何在服务器上使用 CVS 的文档。

开始 CVS 项目有三个步骤:

1)如果使用现有文件启动新的 CVS 存储库，您应该对代码进行主备份，将其导入到新的 CVS 树中，然后删除旧目录。这可以确保你不会意外地编辑 CVS 和非 CVS 文件。

2)如果从一个新项目开始，首先决定文件系统的结构。例如，对于一个在`www.newclient.com`的新客户，您设置一个开发、试运行和生产环境。这些文件在文件系统中的位置如下:

A) `/home/sites/www.newclient.com/htdocs/`(用于生产中的`www.newclient.com`)
B)`/home/sites/staging.newclient.com/htdocs/`(用于在发布到生产现场之前测试所有最终代码的`staging.newclient.com`，
C)和`/home/sites/devel.newclient.com/htdocs/`(用于`devel.newclient.com`，所有真正的设计和开发都在这里进行。)

3)一旦设置和测试完毕，确保参与开发工作的人员拥有正确配置的客户端。Linux、Macintosh 和 Windows 的客户端可以在 [CVSGui](http://www.wincvs.org) 找到。

使用 CVS 的主要好处显然是源代码控制，这包括自动版本控制，带有版本编号(v 1.0、1.1、1.2 等等)和回滚的关键特性，这允许您回滚已经到位但未能满足标准的更改，或者如果发现了隐藏的 bug。

## 分享这篇文章