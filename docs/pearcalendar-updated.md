# PEAR::日历已更新

> 原文：<https://www.sitepoint.com/pearcalendar-updated/>

今天用 v0.52 更新了[梨::日历](http://pear.php.net/package/Calendar/)，主要包含[洛伦佐](http://pear.php.net/user/quipo)的辛苦(多谢！).再次提醒我，拥有一个像样的 [PHP 单元测试](http://www.lastcraft.com/simple_test.php)框架是多么重要(首先还要编写测试)——没有它，维护 PEAR::Calendar 多少是不可能的。

PEAR::Calendar 还需要一些突出的特性，比如添加一些简单的方法来确定某个日期是否是周末(马上就要到了)，但是它已经达到了所有基本功能都具备的程度。看看它是否会导致替代的“计算引擎”被编写将会很有趣——在*理论*中，应该有可能用它建立一个中国的甚至是[托尔金](http://cpan.uwinnipeg.ca/dist/Date-Tolkien-Shire)日历——“只是”实现[接口](http://pear.php.net/package/Calendar/docs/0.5.2/Calendar/Calendar_Engine_Interface.html)的问题。

不过，最大的问题是性能，它必须变得更快。特别是对于一个与动态数据“挂钩”的日历来说，缓存可能很棘手，因此即使在每次请求都“重新执行”时，它也需要表现良好。

现在它并不慢，但是在内部它可以(取决于你在做什么)生成很多函数调用，特别是在使用[装饰器](http://www.phppatterns.com/index.php/article/articleview/92/1/1/)的时候。有些调用可以通过直接访问对象属性来绕过。

否则仍在考虑使用序列化的某种内部缓存。这里的一个问题是，对包含对象的字符串进行非序列化通常比直接从 PHP 重新实例化要慢——在这种特定情况下，绕过创建对象的日期相关数学运算可能会有一点好处。另一种方法可能是代码生成(还没有认真考虑过)。另一个问题可能是日期选择方式的基础——选择与正常的日历数据结构“合并”,但出于缓存的目的需要分开——可能需要一个替代的选择机制。

无论如何——最终会到达那里，随着 [PEAR::Date_Holidays](http://pear.php.net/pepr/pepr-proposal-show.php?id=85) 看起来会被接受，PEAR 的[日期和时间](http://pear.php.net/packages.php?catpid=8&catname=Date+and+Time)系列开始看起来很有吸引力。

## 分享这篇文章