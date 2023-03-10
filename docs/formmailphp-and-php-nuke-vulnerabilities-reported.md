# Formmail.php 和 PHP-Nuke 漏洞报告

> 原文：<https://www.sitepoint.com/formmailphp-and-php-nuke-vulnerabilities-reported/>

SecurityFocus 是一个供应商中立的网站，提供关于封闭和开放源代码软件的客观、及时和全面的安全信息。今天的漏洞报告(以 BugTraq 的形式通过电子邮件发送)报告了 web 设计人员和开发人员使用的两种流行的开源解决方案——Formmail.php 和 PHP-Nuke。

5.乔·兰布罗索·杰克的 Formmail.php 未授权远程文件上传…
BugTraq ID: 9591
远程:是
发布日期:2004 年 2 月 6 日
相关 URL:【http://www.securityfocus.com/bid/9591】T4摘要:
杰克的 Formmail.php 是一个基于 web 的电子邮件网关。
应用程序是用 PHP 编写的，然而，Perl 版本也可以作为
使用。

据报告，该软件中存在一个漏洞，使得远程攻击者能够对易受攻击的服务器进行未经授权的访问，并上传任意文件。

据报道，该软件通过 HTTP referer 验证请求
的来源。由于在
‘check _ referer()’函数中执行了不正确的验证，攻击者可以通过向
提供一个空的 HTTP referer 值来绕过检查。该问题可能允许攻击者通过“file.php”脚本的“css”变量
上传文件。

成功利用此问题可能允许攻击者将
恶意文件保存到系统中，或者可能覆盖敏感文件。

尽管未经证实，Formmail.php 5.0 及更早版本可能会受到此问题的影响
。

14.PHP-Nuke 'News '模块跨站脚本漏洞
BugTraq ID:9605
Remote:Yes
发布日期:2004 年 2 月 09 日
相关 URL:【http://www.securityfocus.com/bid/9605】T5
摘要:
PHP-Nuke 是一个免费的内容管理系统。它用 PHP 实现，可用于一系列系统，包括 Unix、Linux 和微软的 Windows。

据报道，PHP-Nuke 的“新闻”模块容易出现
跨站脚本漏洞。该问题的出现是由于模块
未能正确整理用户提供的信息。URI 参数
‘title’未正确清理 HTML 标记。这将允许
访问易受攻击网页的用户在 web 客户端执行恶意 HTML 和脚本代码。这将发生在托管软件的站点
的安全环境中。

利用该漏洞可能允许窃取基于 cookie 的身份验证
凭据。其他攻击也是可能的。

据报道，该问题会影响版本 6 . x–7 . x 的
软件，但是早期版本也可能存在漏洞。

21.PHP-Nuke 'Reviews '模块跨站脚本漏洞
BugTraq ID:9613
Remote:Yes
发布日期:2004 年 02 月 09 日
相关 URL:【http://www.securityfocus.com/bid/9613】
概要:
PHP-Nuke 是一个免费的内容管理系统。它用 PHP 实现，可用于一系列系统，包括 Unix、Linux 和微软的 Windows。

据报道，PHP-Nuke 的“评论”模块容易出现
跨站脚本漏洞。该问题的出现是由于模块
未能正确整理用户提供的信息。URI 参数
‘title’未正确清理 HTML 标记。这将允许
访问易受攻击网页的用户在 web 客户端执行恶意 HTML 和脚本代码。这将发生在托管软件的站点
的安全环境中。

利用该漏洞可能允许窃取基于 cookie 的身份验证
凭据。其他攻击也是可能的。

据报道，该问题会影响版本 6 . x–7 . x 的
软件，但是早期版本也可能存在漏洞。

23.PHP-Nuke 公开消息 SQL 注入漏洞
BugTraq ID:9615
Remote:Yes
发布日期:2004 年 2 月 09 日
相关网址:[http://www.securityfocus.com/bid/9615](http://www.securityfocus.com/bid/9615)
概要:
PHP-Nuke 是一个免费的内容管理系统。它用 PHP 实现，可用于一系列系统，包括 Unix、Linux 和微软的 Windows。

据报道，PHP-Nuke 的“公共消息”功能容易受到 SQL 注入漏洞的攻击。该问题是由于
未能正确整理“/mainfile.php”脚本的
‘public _ message()’函数中的“$p_msg”参数造成的。

由于 PHP-Nuke 强制所有变量在
应用程序的上下文中是全局的，所以可以在 POST、GET
或 COOKIE 数据中指定' $p_msg '参数。在' public_message()'函数中，' $p_msg'
参数被解码为' $c_mid '参数，该参数直接用于生成 SQL 查询
。攻击者可以使用通过' $p_msg '参数传递的 SQL Union
命令从数据库中挖掘数据。

由于这个问题，攻击者可以修改数据库查询的逻辑和结构
。其他攻击也是可能的，比如获得对敏感信息的
访问权。

据报道，该问题会影响版本 6 . x–7 . x 的
软件，但是早期版本也可能存在漏洞。

## 分享这篇文章