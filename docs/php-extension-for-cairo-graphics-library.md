# Cairo 图形库的 PHP 扩展

> 原文：<https://www.sitepoint.com/php-extension-for-cairo-graphics-library/>

感谢 Jared“Ren”Williams 的提示(是你吗？有什么联系吗？)[这里](http://www.santosj.name/php/what-i-would-like-to-see-in-php-7/)—[Cairo wrapper 扩展](http://cairo-wrapper.php-baustelle.de/trac/)由[hart mut Holzgraefe](http://www.php-groupies.de/blogs/)—[漂亮的演示](http://cairo-wrapper.php-baustelle.de/examples/dragon-curve.php)。

Cairo 是用于 Linux 的“下一代”矢量图形库，在 PHP 中使用它非常酷。同样酷的是这个扩展是用 [PEAR::CodeGen_PECL](http://pear.php.net/package/CodeGen_PECL) 创建的，哈特穆特在这里描述了:看妈妈-[no C](http://cairo-wrapper.php-baustelle.de/trac/browser/trunk/cairo-wrapper.xml)！(嗯差不多)。

这就提出了一个问题，是不是该贬值 [GD](http://www.php.net/gd) 了？据我所知，它和 [PCRE 扩展](http://www.php.net/pcre)是阻止 PHP 成为[线程安全](http://en.wikipedia.org/wiki/Thread_safe)的两个主要问题孩子。用声称基于 Perl 正则表达式的 ICU 正则表达式来代替 PCRE 并保持向后兼容性是可能的。放弃 GD 会更痛苦，但是有了 Cairo 和 imagick 扩展，可能会让每个人都满意。这有利于在 [Apache mpm](http://httpd.apache.org/docs/2.0/mod/worker.html) 下运行 PHP。有兴趣听听这有多现实的想法/意见吗？

旁注:我对 GD 和 imagemagick 之间的区别的愚蠢看法是，前者更多的是为了图像*的创建*，而后者更多的是为了操作*现有的*图像。

## 分享这篇文章