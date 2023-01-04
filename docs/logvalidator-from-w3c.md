# W3C 的 LogValidator

> 原文：<https://www.sitepoint.com/logvalidator-from-w3c/>

万维网联盟(W3C)的 LogValidator 是站长和管理员定期检查网站(无论大小)上文档有效性的一个很好的工具。

该工具可以作为模块 W3C::LogValidator 通过 CPAN 获得，也可以手动下载和安装(参见下面的链接列表)。

LogValidator 有几个模块，可以根据自己的选择在 cron 中运行。模块包括检查 HTML，XHTML 和 CSS 的有效性。通过查看您的网络日志，该程序可以被配置为返回您站点上最受欢迎的页面，以及通过 W3C 验证程序发现无效的文件列表。可以手动更新文档的有效性，或者使用一个工具，比如也可以从 W3C 获得的 [Tidy](https://www.w3.org/People/Raggett/tidy/) 。

对于大型网站，如果发布的新页面继续返回错误，LogValidator 可以帮助识别模板引擎输出中可能存在的问题。此外，如果您的站点很大，并且您打算定期运行所有页面，那么您可以在网络上本地下载并安装 W3C 验证器，并将 LogValidator 配置文件指向它。

LogValidator 的配置文件有很好的文档记录，很容易根据您的需要进行定制。支持输出结果，包括原始文件，HTML 报告或通过电子邮件。可以设置工具和配置文件来扫描多个站点，并通过电子邮件向这些站点各自的网站管理员发送报告。

链接列表:

LogValidator 主页->[https://www.w3.org/QA/Tools/LogValidator/](https://www.w3.org/QA/Tools/LogValidator/)

LogValidator 手册->[https://www.w3.org/QA/Tools/LogValidator/Manual](https://www.w3.org/QA/Tools/LogValidator/Manual)

LogValidator 示例配置文件->[http://dev . w3 . org/CVS web/perl/modules/W3C/log validator/samples/log process . conf？only_with_tag=HEAD](http://dev.w3.org/cvsweb/perl/modules/W3C/LogValidator/samples/logprocess.conf?only_with_tag=HEAD)

## 分享这篇文章