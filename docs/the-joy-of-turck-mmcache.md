# 图尔克 MMCache 的快乐

> 原文：<https://www.sitepoint.com/the-joy-of-turck-mmcache/>

在之前已经有[说](http://php.weblogs.com/2003/06/08) [了，但是](http://php.weblogs.com/discuss/msgReader$2659?mode=topic)[图尔克 MMCache](http://turck-mmcache.sourceforge.net/) 是一款很棒的软件。正如封面上写的:

> Turck MMCache 是一个免费的开源 PHP 加速器、优化器、编码器和 PHP 动态内容缓存。它通过在编译状态下缓存 PHP 脚本来提高性能，因此编译的开销几乎完全消除。它还使用了一些优化来加速 PHP 脚本的执行。Turck MMCache 通常会降低服务器负载，并将 PHP 代码的速度提高 1-10 倍。

我最后一次看到它是在 2003 年初，当时我需要一个运行 PHP 的 Windows 服务器的加速器。从那以后，它一路走来，现在提供了它所谓的“动态内容缓存”，这让我再次感兴趣。

在 Linux 上安装很容易，在 Windows 上甚至更容易——作者花时间为几乎每个 PHP 版本编译 Windows dlls，直到 4.1.0，这是唯一一个可以在 Windows 下轻松运行的开源 PHP 加速器，而不需要拖出 VC++。

动态内容缓存提供了一个在内存中存储数据的 API 你可以丢弃 HTML 输出，用 PHP 的[输出控制函数](http://www.php.net/manual/en/ref.outcontrol.php)捕获，或者甚至用它来实现对象持久化。PHP 的[共享内存](http://www.php.net/shmop) API 做了类似的事情，但是为了有用，你需要大量的“粘合”代码来处理像动态增长共享内存块这样的事情。MMCache API 会处理所有这些，并简单地填充空间到 PHP 的极限或你在 php.ini 中指定的值。 [Prevayler](http://www.prevayler.org/wiki.jsp) 我们来了…

总的来说，有些东西会让你的主机烦恼…[当然你不会在共享主机上获得共享内存访问权]

## 分享这篇文章