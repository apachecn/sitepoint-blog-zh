# ZZ/OSS 安装程序

> 原文：<https://www.sitepoint.com/zzoss-installer/>

有趣的项目在[http://www.zzoss.com/phpwiki/index.php/ZzossInstaller](http://www.zzoss.com/phpwiki/index.php/ZzossInstaller)结束；

" ZZ/OSS 安装程序是一个基于 PHP 的安装向导，用于模块化 PHP 应用程序的专业发布管理."

本质上，它建立在 PEAR 包管理器的基础上，允许安装和管理完整的应用程序 WIKI 上有大量的进一步信息。

这当然是个好主意，但目前，我对此表示怀疑。

随着可用的版本，而发挥“我是一个哑用户”，无法安装安装程序。我没有仔细查看问题出在哪里，但是一些 PHP“陷阱”，比如 allow _ call _ time _ pass _ reference([见这里](https://www.sitepoint.com/php-anthology-3-php-mysql/))露出了丑陋的嘴脸，这表明当前版本还没有准备好大规模部署。

更一般地说，为 PHP 编写安装程序，安装程序本身也是 PHP，是出了名的困难。PEAR 包管理器已经开发了很长时间，在像 Win98 这样的平台上仍然存在问题。还有像“你感到安全吗？”在服务器的某个地方公开这样的工具(即使它受到某种形式的身份验证的保护)。

撇开疑问不谈，PHP 缺少一个可靠的应用程序安装器/管理器，ZZ/OSS 安装器是我第一次认真尝试构建一个。随着更多的用户反馈，也许当前的问题可以得到解决，我们将有一些每个人都可以使用的东西。

## 分享这篇文章