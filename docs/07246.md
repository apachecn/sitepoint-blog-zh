# 有史以来最简单的 WordPress 安全提示！

> 原文：<https://www.sitepoint.com/easiest-wordpress-security-tip-ever/>

有时你会遇到一个简单得让你难以相信你以前不知道的提示。

如果你运行的是 WordPress，你就已经定义了一个 wp-config.php 文件，其中包含了一些重要的设置，比如 MySQL 数据库的主机、名称、用户和密码。它通常位于 WordPress 的安装位置——在大多数情况下，这是 web 服务器的根目录，但也可以是任何子文件夹。

你肯定不希望 wp-config.php 落入坏人之手。在正常情况下，淘气的黑客无法查看文件，因为 PHP 解释器会解析它并返回一个空页面。然而:

*   黑客将知道文件的确切位置，并能更有效地锁定它。
*   如果 PHP 失败，例如，可能在更新期间，可以通过输入 URL 直接在浏览器中查看 wp-config.php。

*准备好接受简单的提示了吗……*

将 wp-config.php 文件移动到你的 WordPress 安装上面的文件夹中。

例如，您可能有一个安装 WordPress 的文件夹结构，如/home/mysite/public_html/在这种情况下，您应该将 wp-config.php 移动到/home/mysite/中。

这有几个好处:

1.  假设/home/mysite/public_html/是 web 服务器的根文件夹，则任何使用浏览器的人都无法访问/home/mysite/
2.  黑客找到正确文件的机会更小。
3.  这么简单，没什么理由不做！

也许这不会是你今天读到的最激动人心的科技文章，但知道这一点是很有用的。我希望它对您的安全工作有所帮助。

## 分享这篇文章