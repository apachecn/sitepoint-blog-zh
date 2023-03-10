# 共享主机和 PHP5 升级

> 原文：<https://www.sitepoint.com/shared-hosts-and-php5-upgrades/>

[伊沃·扬施](http://www.achievo.org/blog/)给[写了一篇关于](https://www.sitepoint.com/how-to-tell-when-php-5-is-safe-to-use/#comment-11652)[这篇博客](https://www.sitepoint.com/how-to-tell-when-php-5-is-safe-to-use/)的评论，这让我陷入了思考；

> 我们有同样的问题。落后的兼容性问题很小，我们可以很容易地修复我们自己的应用程序。但我们也为数百个不是我们创建的网站提供托管服务。让我们所有的客户修复他们的网站(并为他们提供测试手段)将是地狱般的。

共享主机可以采用什么策略来处理这个问题？鉴于没有魔法棒来解决这个问题，而且许多共享主机的人力有限，该怎么办呢？

对我来说，戴着“共享主机客户”的帽子，第一个问题是信息——如果主机想要迁移到 PHP5，他们需要传达一个清晰的时间表，并有现实的时间框架(例如，至少 3 个月的警告)。随之而来的应该是警告，客户需要开始针对新的 PHP 版本测试他们的代码。

总的来说，正如我[在](https://www.sitepoint.com/php-certification-for-hosts/)之前说过的，我认为一些共享主机提供的信息类型还有改进的空间。所有的 PHP 安装都*不*相同，即使版本号相同，神奇的`php.ini`文件或可用的扩展是“工作”的代码和不工作的代码之间的区别。在这种程度上，我认为共享主机需要在这里发布完整的细节——他们计划使用的确切的 PHP 5 版本，反映 PHP 将如何配置的示例`php.ini`,将可用的扩展列表以及任何其他可能相关的内容。

这些步骤应该足以满足有经验的 PHP 高手。但是对于那些 PHP 仅仅意味着下载和 FTP 通过像 [Hotscripts](http://www.hotscripts.com) 这样的地方找到的代码的人来说呢？

想象一下，最好的方法是给客户一个能够测试他们代码的环境。昂贵但可能最有效的方法是在运行 PHP5 的测试服务器上。现实，成本和破碎的网址是一个问题。

另一种方法可能是为用户提供一个“准备安装”的灯或 WAMP 环境，它反映了计划的配置，但他们可以在自己的电脑上运行——类似于 [XAMPP](http://www.apachefriends.org/en/index.html) 或[类似的](https://www.sitepoint.com/lamppix-bootable-webserver-on-a-cd/)。

某种程度上，技术解决方案也不错。我想知道 [runkit](http://www.php.net/runkit) 的沙箱功能是否可以用来帮助共享主机自动发现客户的问题。这并不能解决问题，但至少人们可以得到警告。也许类似的信息可以通过 PHP 在命令行上使用 lint 选项`$ php -l`来提取

还有什么？

## 分享这篇文章