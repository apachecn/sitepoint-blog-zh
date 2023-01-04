# _ _ halt _ compiler()–多傻啊？

> 原文：<https://www.sitepoint.com/halt-compiler-how-nuts/>

[西蒙](http://simon.incutio.com) [blogmarked](http://simon.incutio.com/blogmarks/2006/05/10/) PHP 的新(ish) [__halt_compiler](http://www.php.net/manual/en/function.halt-compiler.php) 函数其中，根据包；

> …停止编译器的执行。这对于在 PHP 脚本中嵌入数据非常有用，比如安装文件。数据开始的字节位置可以由 __COMPILER_HALT_OFFSET__ 常量确定，该常量仅在文件中存在 __halt_compiler()时定义。

这可能是也可能不是一个优雅的解决方案(当然相对简单)，但它确实有一个非常有效的用例——允许 PHP 应用程序从单个脚本、所有相关部分(其他 PHP 脚本、CSS、HTML、图像等)安装自己。)以二进制形式打包在脚本的末尾。

最好的例子是 [Ilia 的](http://ilia.ws/)[fud forum](http://fudforum.org/)——如果你[下载](http://fudforum.org/download.php)一个最近的版本，你会看到它利用了这一点——只需运行 install.php 脚本，它就能从那里解包一切。Greg 提到了 _ _ halt _ compiler[不久前](http://greg.chiaraquartet.net/archives/75-go-pear.phar-works!-In-related-news,-PHP_Archive-is-now-PHP-5.1.0+.html)，以及他在 [phar](http://greg.chiaraquartet.net/archives/128-pharring-a-web-application-phpMyAdmin.html) archives 上做的所有事情。

无论如何——我个人并不觉得这很疯狂。并考虑建立工作保障的[机会](http://mindprod.com/jgloss/unmain.html)；)

另外注意到 [Sara Golemon](http://blog.libssh2.org/) 有一本[关于编写 PHP 扩展的书](http://www.amazon.com/exec/obidos/ASIN/067232704X/)即将出版——非常需要。还有一个相关的说明[布鲁斯·埃凯尔](http://www.bruceeckel.com/)在这个“思考”系列中发布了一个免费的研讨会——[用 C 语言思考](http://mindview.net/CDs/ThinkingInC/beta3)。

## 分享这篇文章