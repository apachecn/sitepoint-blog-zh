# PHP WTF

> 原文：<https://www.sitepoint.com/the-php-wtf/>

受[这个网站](http://www.thedailywtf.com/)的启发，这个网站最近不断出现在我的雷达上。

虽然像这样的事情很有趣，但总的来说，人们对这个想法有着复杂的感情。一方面，任何帮助人们提高编码技能的东西都是好东西。另一方面，我认为这里采取的方法是一种精英主义，它会阻止问“愚蠢”的问题；整体效果是负面的。在我看来，编程中的无知是可以(也应该)被原谅的——没有人知道所有的事情。

更具体地说，[这个特别的 PHP WTF](http://thephpwtf.com/index.php?title=when_php_newbies_attack) 进一步引起了我的怀疑——阅读它你可能会认为 [mysql_num_rows()](http://www.php.net/mysql_num_rows) 是一个坏主意时期，但事实并非如此。在某些情况下，它在确定您将要使用的结果集的大小时有一定的价值*，但是阅读本文您可能会得出结论，正确的解决方案总是使用两个查询，如；*

 *`$query = 'SELECT COUNT(*) FROM sometable';
$result = mysql_query($query,$connection);
list($count) = mysql_fetch_array($result);`

`$query = 'SELECT * FROM sometable';
$result = mysql_query($query,$connection);
while ( $row = mysql_fetch_array($result) ) {
// etc.
}`

讨论中忽略的另一个更大的问题是“SELECT * FROM sometable”的影响，即全表扫描。如果可能的话，最好用一些 WHERE 子句缩小结果集，并利用索引。这里有一个很好的讨论([谷歌缓存](http://64.233.161.104/search?q=cache:kINexpMjHeoJ:www.linux-mag.com/2001-06/mysql_04.html&hl=en))在 [MySQL 性能调优](http://www.linux-mag.com/2001-06/mysql_01.html)来自 [The Man](http://jeremy.zawodny.com/blog/) 。

也就是说，觉得[这个 WTF](http://thephpwtf.com/index.php?title=the_launch_of_the_php_wtf) 还是据理力争的好。* 

## *分享这篇文章*