# 检查 Rootkits

> 原文：<https://www.sitepoint.com/checking-for-rootkits/>

服务器被黑客攻击的一个困扰是没有意识到已经被入侵了。这可能会导致精明的恶意入侵者留下隐藏的工具，这些工具可以捕获身份验证数据，破坏关键的系统文件，并通过受损的服务器监控/中继流量，而通常不会被检测到。

这些威胁通常以[rootkit](http://searchsecurity.techtarget.com/sDefinition/0,,sid14_gci547279,00.html)的形式出现。

虽然事后检查可能不是最好的方法，但这是监视服务器完整性的一种方法。最佳实践是准备好工具，如配置良好的防火墙、难破解的 root 密码和应用程序，以防止二进制文件和配置文件发生更改或发出警报(如 [Tripwire](http://www.tripwire.org) )。

也就是说，当管理员担心系统可能出现问题时，由 Nelson Murilo 和 Klaus Steding-Jessen 编写的一款名为 [chkrootkit](http://www.chkrootkit.org/) 的工具可以在包括 FreeBSD、Linux、Solaris、惠普 ux 等众多平台变体上检测多达 56 个不同的根工具包。

它非常容易安装，只需在你的服务器上解压到你选择的目录下，然后在 chkrootkit 目录下输入“make sense”。然后就可以执行了。/chkrootkit ',并接收结果的屏幕报告。我的偏好是让它在 cron 中不时地运行，并将结果输出到一个文件中，以便我在服务器上检查日志和执行一般管理时可以查看。

## 分享这篇文章