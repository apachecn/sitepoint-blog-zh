# 为 Web 开发配置 OS X 文章

> 原文：<https://www.sitepoint.com/os-x-web-development-2/>

##### Perl 语言

要开始向您的本地网站添加动态，您可能会使用的主要工具之一是 Perl。这包含在标准的 OS X 安装中，只需要几个简单的步骤就可以让 cgi 脚本在本地主机上运行。

在/Library/web server/CGI-Executables/目录中，您会发现一些包含的测试脚本。默认情况下，可以在 Apache 的 httpd.conf 文件中找到这个目录的别名 cgi-bin。要使系统上的单个用户也可以使用脚本，您还需要在 httpd.conf 中找到并取消注释以下行:

```
# AddHandler cgi-script .cgi
```

请注意，您还可以添加一个额外的行，如上所示，但是带有. pl 扩展名，以支持使用。pl 文件与。cgi 扩展。

作为一个安全警告，我应该提到这确实允许在 Apache 提供文档的任何地方执行 cgi。对于本地主机测试来说，这可能不是问题，但是如果您要在系统上启用对这些本地站点的 Internet 访问，请仔细考虑您的安全策略。

要测试您的 CGI 功能，请在您的浏览器中加载一个测试脚本，即 http://127 . 0 . 0 . 1/CGI-bin/test-CGI(由 Apple 在 CGI-Executables 目录中提供)。如果您收到禁止的错误，这是由于没有设置权限。所有的 CGI 脚本都需要执行权限，最常见的是 755。这可以通过使用 CGI 脚本位置的终端并运行 CHMOD 755 filename.cgi 来完成，或者在本例中，运行 test-cgi。在这种情况下，我们将打开终端并发出以下命令:

```
CD /Library/WebServer/CGI-Executables  

sudo chmod 755 test-cgi
```

在 Web 浏览器中重新访问该脚本时，代码将执行其职责，您应该会看到类似下图的内容。

![1263_image2](img/3f2571625be9d346c930637f1cf3d5fd.png)

一旦您成功地执行了一个测试脚本，您可以将 Perl 脚本放在 CGI executables 目录中(记住设置您的权限)，或者，如果您已经在 Users/shortname/Sites 文件夹中启用了脚本运行，您可以将它们放在那里。

##### 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

正如您在 Apache 和 Perl 的前两节中所看到的，除了几个文件位置之外，在 OS X 上没有什么不同。PHP 也是同样的情况。你可以从源代码安装 PHP，从 php.net 下载，或者你可以使用专门为 OS X 设计的二进制安装包

苹果在苹果开发者连接网站上指出，在 PHP 4.3 版本之前，从源代码安装可能有点棘手；然而，PHP 在最新版本中已经解决了这些问题。对于那些喜欢从二进制文件安装 PHP 的人来说。pkg 文件)，Marc Liyanage 有一个网站提供了最新版本的二进制文件，它与 OS 10.2 和新的 Panther 10.3 都兼容。这个可以在这里[下载。您会发现二进制文件提供了内置支持多种配置的便利，包括 MySQL(和 PostgreSQL)、PDF 和 XML。](http://www.entropy.ch/software/macosx/php/)

##### 关系型数据库

将 MySQL 安装到您的系统上同样简单！MySQL 为 OS X 提供了一个二进制包安装程序，可以在这里找到。请务必从 Mac OS X 软件包安装程序下载列表中选择下载。

安装完成后，您可以使用传统的 mysql 命令行操作，或者通过第三方的附加工具，从终端实用程序管理 MySQL。这位作者使用 Webmin(Webmin . com 免费提供)，它不仅提供了对 MySQL 的基于浏览器的控制，还提供了对 Apache、Sendmail 和 Perl 模块的控制。

##### 动态网络服务准备就绪

正如您所看到的，在 OS X 上安装并运行您的 Web 服务器既快速又简单，包括在本地测试您的脚本甚至整个 Web 应用程序的能力。下面，我在 OS X 系统中加入了一些关于利用 Python 和 CVS 的结束语。

***在 OS X* 上安装 Python**

Python 2.2 包含在 OS X 的 BSD 子系统中；然而，许多新的开源应用程序正在使用新版本的 Python。这里有一个具有特殊 OS X 特性的 MacPython 版本。

***使用 CVS 和***

对于那些可能使用 CVS 进行源代码控制的开发人员，最后一个参考说明是:OS X 在单用户和多用户环境中都有对 CVS 的本地支持。苹果网站上有一篇关于使用 CVS 进行 Web 开发的优秀文章。使用 CVS 确实需要通过包含的命令行操作终端实用程序对 OS X 进行一些“幕后”使用。

**Go to page:** [1](https://sitepoint.com/os-x-web-development) | [2](https://sitepoint.com/os-x-web-development-2/)

## 分享这篇文章