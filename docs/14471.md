# 使用 Cronolog 记录

> 原文：<https://www.sitepoint.com/logging-with-cronolog/>

我一直在用安德鲁·福特开发的 [Cronolog](http://cronolog.org/index.html) 做实验，他也是阿帕奇袖珍参考的作者，由 O'Reilly and Associates 出版。

我选择与 cronolog 一起工作是基于一个同行的建议和这样一个事实，即该站点的日志文件在大小上是不可管理的。你可能还记得在 Apache 网络日志上我的专栏文章的结尾曾简单提到过。

这个方便的程序允许日志按日、月、年分解。我选择了一个中间地带，让他们在每个月的第一天轮换新的日志。

安装非常容易。下载和解包后——只需使用。/configure 并 make 来生成可执行文件。然后您可以将 src/cronolog 复制到您想要调用它的地方。我用的是/usr/local/sbin/cronolog。

用法也相当简单。我在下面的 httpd.conf 中记录了一个片段，展示了它如何集成到虚拟服务器内部的 Apache 日志配置中。

 <virtualhost>…
…
…
custom log " |/usr/local/sbin/cronolog/home/domains/my domain . com/logs/% Y/% m/access _ log "组合
error log " |/usr/local/sbin/cronolog/home/domains/my domain . com/logs/% Y/% m/error _ log "
…
…</virtualhost> 

cronolog 程序被调用并读取日志消息输入，管理日志文件，还可以方便地按月份和年份在日志文件夹中创建文件夹层次结构，因为它每月都会创建新的日志文件。

现在，我的日志文件夹如下所示:

日志/
2004 年/

在 2004 目录中，我看到了 7 月和 8 月的 07 和 08 目录，每个目录都包含该月的 access_log 和 error_log。

最后一点——Cronolog 归功于 Klaus Mueller 为 Cronolog 创建了一个可以在 Windows 系统上运行的 Windows 二进制文件。下载是[这里](http://cronolog.org/download/cronolog-1.6.1-win32.zip)。

## 分享这篇文章