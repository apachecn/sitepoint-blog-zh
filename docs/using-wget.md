# 使用 WGET

> 原文：<https://www.sitepoint.com/using-wget/>

Wget 是 Linux 和 Mac OS X 系统上的一个实用工具，对于 web 系统管理员来说非常方便。

Wget — [位于 GNU.org 网站](http://www.gnu.org/software/wget/wget.html) —是一个命令行应用程序，用于通过 ftp、http 和 https 连接进行文件检索。

我发现它对于将文件直接下载到我在 shell 会话中工作的服务器非常有用，节省了时间，而不是下载到我的本地桌面并上传。此外，由于它可以传递用户名和密码，因此在网站迁移、设置镜像站点等方面非常有用。

最后，可以使用 cron 调度 Wget，因此，如果需要定期复制一个文件或目录，可以将其设置为不需要管理员干预。

利用 Wget 的一些有用示例:

1)下载远程文件–也许您正在下载应用程序的更新，并且已经收到了 url。在这种情况下，您可以使用 ftp 或 http 来检索:

 `wget [http://somedomain.com/public/remotefilename.tar.gz](http://somedomain.com/public/remotefilename.tar.gz)
or wget [ftp://somedomain.com/public/remotefilename.tar.gz](ftp://somedomain.com/public/remotefilename.tar.gz)`

Wget over ftp 默认为二进制(ftp lingo 上的 I 模式)，然而，如果你需要使用 ascii 模式，你只需添加'；在上面的 ftp url 示例的末尾键入=a '(不带引号)。

2)带身份验证的下载–您可能正在更新一个需要用户名和密码才能访问的注册应用程序。如下所示更改语法:

 `wget username:password@[http://somedomain.com/reg/remotefilename.tar.gz](http://somedomain.com/reg/remotefilename.tar.gz)
or wget username:password@[ftp://somedomain.com/reg/remotefilename.tar.gz](ftp://somedomain.com/reg/remotefilename.tar.gz)`

3)在 wget 请求中插入自定义端口——也许您的下载需要一个自定义端口和身份验证。Wget 通过在主机后面和文件的/path 前面插入一个冒号和端口号，也可以很容易地处理这个问题:

 `wget username:password@
https://somedomain.com:portnumber/reg/remotefilename.tar.gz
or wget username:password@[ftp://somedomain.com:portnumber/reg/remotefilename.tar.gz](ftp://somedomain.com:portnumber/reg/remotefilename.tar.gz)`

4)整个目录也可以从一个服务器迁移到另一个服务器，即把网站移动到新的硬件上。我发现 ftp 访问对此最有效。我还利用了日志记录(-o 选项)在需要调试或验证文件检索时进行传输，并使用递归选项(-r)在新服务器上重新创建目录结构。

因此，如果我要移动 mydomain.com，我会使用:

 `wget -o mylogfile -r myuser:mypass@[ftp://mydomain.com/](ftp://mydomain.com/)`

如果您的 ftp 用户可以查看多个域，请确保您指定了要移动的域的文件和目录的路径。

还有其他几个有趣且有用的选项，包括:

–passive-FTP:用于在防火墙后使用 wget

-nd:不在远程计算机上重新创建目录结构，而是将所有检索到的文件保存到当前本地目录中。

–cookies =开/关:远程站点是否需要打开或关闭 cookies 来回收文件(有时有助于身份验证)

–retr-symlinks:将检索符号链接指向的文件。

Wget 中还有其他几个强大的特性，幸运的是，附带的手册提供了很好的例子。只需在命令行上运行 man wget 即可查看。

## 分享这篇文章