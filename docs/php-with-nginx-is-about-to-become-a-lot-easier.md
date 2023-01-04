# 使用 nginx 的 PHP 将变得更加容易

> 原文：<https://www.sitepoint.com/php-with-nginx-is-about-to-become-a-lot-easier/>

PHP 5.4 版本很可能会在核心部分包含 PHP-FPM 补丁，这对我们这些喜欢在 T2 nginx T3 网络服务器下运行 PHP 的人来说是个好消息。你可能会问，“什么是 PHP-FPM，我为什么要关心？”

PHP-FPM 是 PHP 核心的一个补丁，可以根据需要处理 FastCGI 进程的启动、停止和重启。这一点很重要，因为 nginx 只能通过 FastCGI 与 PHP 接口，而不像 Apache 那样将整个 PHP 环境直接加载到自身中。除了 nginx 优于 Apache 的性能优势之外，通过 FastCGI 而不是作为 Apache 模块运行 PHP 还有它自己的优势:

*   更低的内存使用量(因为额外的 nginx 工作线程没有 PHP 环境的全部负担)
*   更容易的权限管理(PHP 可以以不同于服务器进程的用户身份运行)
*   如果 PHP 崩溃，nginx 可以继续运行

不利的一面是，与 mod_php 相比，nginx、php 和 FastCGI 栈的设置工作要多得多。在过去，使这个设置工作的方法是从 LightTPD 项目中选择`spawn-fcgi`脚本，并使用它来启动 FastCGI 进程。然而，这种设置也有问题:它很脆弱(如果一个进程停止，它可能无法正确重启)，而且设置起来很痛苦。在安装和配置 nginx 之后，您需要下载 LightTPD，获取有问题的脚本，并将其配置为自动启动，然后配置所有内容，使它们能够很好地一起运行。如果你习惯了 mod_php 的开箱即用体验，这可能会说服你只升级你的服务器，而不是切换到更精简、更有效的 nginx。

输入 PHP-FPM。这个补丁将 FastCGI 进程管理嵌入 PHP。因此，如果你用 FPM 补丁和`--enable-fpm`配置选项编译 PHP，PHP 将按照 nginx 的请求启动和停止进程，不需要额外的配置。当然，手动修补和编译 PHP 源代码仍然比我们这些懒惰的 web 开发人员想做的要多，这就是为什么从 PHP 5.4 开始，FPM 将被纳入 PHP 项目的核心是一个好消息。

这意味着您将能够下载 PHP，用`--enable-fpm`开关编译它，然后用 nginx 运行它，所花的时间和精力与安装 Apache 所花的时间和精力是一样的。

因此，如果你过去曾经尝试过用 nginx 运行 PHP，但在经历了第七个考验后放弃了，我建议你试试 PHP-FPM。目前仍有一些争论，但它一直在改进。与此同时，你将成为街区里的酷小子，拥有可爱的新玩具！

## 分享这篇文章