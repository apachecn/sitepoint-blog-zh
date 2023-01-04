# PHP 安全，已回答

> 原文：<https://www.sitepoint.com/php-security-answered/>

特里·蔡写了一份详细的[回应关于 PHP 安全的常见投诉](http://terrychay.com/blog/article/php-security-the-oxymoron.shtml)。他解决了许多针对 PHP 的常见[抱怨，比如 PHP 对全局名称空间的使用，PHP 在 4.2 中关闭](http://slashdot.org/comments.pl?sid=157313&cid=13203338&pid=13203338&threshold=-1&mode=nested&commentsort=0&op=Change) [register_globals](http://au.php.net/register_globals) 的决定，以及诸如[strips slashs](http://au2.php.net/stripslashes)和 [magic quotes](http://de.php.net/magic_quotes) 等特性的问题(我已经[在之前的](https://www.sitepoint.com/blog/)博客中讨论过)。

他提出的一个有趣的观点是，易用性和灵活性与安全性之间的平衡正在发生变化。PHP 的成功很大程度上可以归功于它在早期版本中的易用性。Terry 认为，相对于其他语言，PHP 非常注重灵活性，对于由此可能产生的安全隐患，唯一的解决方案是更好的教育。他为 [PHP 安全联盟](http://phpsec.org/)插上了一个插头，该联盟发布材料来教育其他 PHP 用户关于最佳编程实践以确保安全(由[克里斯·希弗莱特](http://shiflett.org/)领导，该组织发布了一份以 HTML 或[其他](http://phpsec.org/projects/)格式提供的[指南)。](http://phpsec.org/projects/guide/)

我并不完全同意用易用性来证明缺乏安全性的观点，因为我觉得在理想世界中，语言应该确保最简单的做事方式也是正确的。但是，当然，问题是复杂的，他毕竟是在泛泛而谈，对 Slashdot 上针对 PHP 的类似概括和绝对化进行辩护。

## 分享这篇文章