# 竞争条件、AJAX 和会话

> 原文：<https://www.sitepoint.com/race-conditions-ajax-and-sessions/>

Via [杰夫的](http://www.procata.com/blog) [书签](http://del.icio.us/selkirk)，[关于 Ajax 和 PHP 会话的竞争条件](http://thwartedefforts.org/2006/11/11/race-conditions-with-ajax-and-php-sessions/)由[安迪“挫败”巴昆](http://thwartedefforts.org/)对之前在本博客[这里](https://www.sitepoint.com/ajax-and-session-race-conditions/)出现的一个问题进行了出色的分析。

事实上，这个标题几乎是误导性的——前半部分着眼于 Javascript 中的“并行处理”,其中有一些有价值的见解——也就是说，即使你没有使用 PHP，你也想阅读这篇文章。

第二部分探索构建一个*定制的* PHP 会话处理程序(PHP 的默认会话处理程序不会受到竞争条件的影响，但在您开始处理严重的流量时可能会出现问题)，这导致了一个非常巧妙的策略，它允许您通过使用 [SPL](http://www.php.net/spl) 重载`$_SESSION`变量来锁定单个会话变量(而不是整个会话)——这实际上是使用[session _ set _ save _ handler()](http://www.php.net/session_set_save_handler)的一个替代方案——让我们希望这被认为是一个需要支持的*特性*。

令人振奋的是，在 PHP 相关内容中看到这样的内容…

> 内置的[PHP]会话处理程序使用 flock(2)系统调用[…]这可以通过使用 [strace](http://sourceforge.net/projects/strace/) 列出一个进程正在进行的所有系统调用来验证。重要的一点是:
> 
> ```
> open("/var/lib/php/session/sess_XXXXXXXXXXXXXXXXXXXXXXXXXX", O_RDWR|O_CREAT, 0600) = 18
> flock(18, LOCK_EX)                      = 0
> fcntl64(18, F_SETFD, FD_CLOEXEC)        = 0
> fstat64(18, {st_mode=S_IFREG|0600, st_size=11, ...}) = 0
> pread64(18, "count|i:17;", 11, 0)       = 11
> . . . .
> pwrite64(18, "count|i:18;", 11, 0)      = 11
> close(18)                               = 0
> 
> ```

旁注——我发现自己同意安迪关于 PHP 的大部分[问题——比你通常在](http://thwartedefforts.org/problems-with-php/) [PHP 烂网环](http://tnx.nl/php)上发现的那种抱怨更接近事实。

## 分享这篇文章