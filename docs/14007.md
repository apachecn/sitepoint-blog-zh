# WordPress 安全更新

> 原文：<https://www.sitepoint.com/the-wordpress-security-update/>

在 [PHP 安全博客](http://blog.php-security.org/)的斯特凡·埃塞尔很不高兴。他刚刚写了一篇名为[WordPress——开发者完全疯了](http://blog.php-security.org/archives/8-WordPress-developers-totally-nuts.html)的博客，声称在[发布 1.5.2 版本](http://wordpress.org/development/2005/08/one-five-two/)仅几个小时后，开发者就修补了一些额外的安全漏洞，并重新上传了下载文件，没有对其做任何不同的标记。Stefan[之前](http://blog.php-security.org/archives/7-WordPress-update.html)联系过 WordPress 关于他们产品的安全缺陷，并贡献了一些补丁。根据 Stefan 的说法，最终的结果是，许多下载了 1.5.2 预更新版本的 WordPress 用户仍然容易受到已知和公开的安全漏洞的攻击。

有趣的是，似乎在这篇博文发布几个小时后，Stefan 在没有通知的情况下，将这篇博文的标题重新命名为“[WordPress——不负责任的无声 tarball 更新](http://blog.php-security.org/archives/8-WordPress-developers-totally-nuts.html)”。

由[马丁·盖斯勒](http://mgeisler.net/)撰写的关于 WordPress 安全性的类似咆哮可以在他的博客上找到。他的建议是:“记得升级你可能有的任何安装”。

[Dougall Campbell](http://dougal.gunters.org/) ，WordPress 的开发者，[回应了他所看到的针对 1.5.2 版本的恐惧、不确定和怀疑](http://dougal.gunters.org/blog/2005/08/17/wordpress-152-security-fud)。Dougall 承认，在 wordpress.org 上发布的第一个可下载的档案并不包含他们打算包括的所有安全修复程序，但这种情况在发布最初公告之前已经得到了纠正，因此任何在官方公告发布后下载档案的人都不会出现问题。

根据 Stefan 的帖子，该漏洞涉及 WordPress 代码中的一个函数，该函数旨在绕过启用了 register_globals 的服务器。该函数检查 PHP 配置中是否启用了 register_globals，如果启用了，它会尝试[取消设置](http://au.php.net/unset)每个已创建的全局变量。该函数无意中引入了一个额外的缺陷——允许远程用户绕过该函数提供的保护。

## 分享这篇文章