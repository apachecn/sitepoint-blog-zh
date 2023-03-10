# WS-死鹦鹉

> 原文：<https://www.sitepoint.com/ws-deadparrot/>

又到了咆哮的时间了(抱歉——除非你感到烦躁，否则请走开)。是关于休息和肥皂的事情。各种博客上又掀起了一轮争论。这一次似乎是 SOAP 最后的垂死挣扎。如果肥皂和所有这些都没什么意义，知道你并不孤单，你可以愉快地忽略它，专注于[更聪明的选择](http://developer.yahoo.net/common/phpserial.html)。

无论如何，[大卫从远处](http://www.loudthinking.com/arc/000575.html)总结，而[蒂姆·布雷](http://www.tbray.org/ongoing/)在几周前的 [WS-Angst](http://www.tbray.org/ongoing/When/200x/2006/02/22/WS-Flurry) 中很好地抓住了细节，它始于[这篇文章](http://pluralsight.com/blogs/dbox/archive/2006/02/17/18869.aspx)(它归结为:“[向我展示你的数据结构](http://zhurnal.net/ww/zw?DataStructures)”与“[向我展示你的 API](https://www.w3.org/TR/wsdl)”)，最后以此结束:

> 如果你让微软说“嗯，最好的方法是让我们花费数十亿美元构建的复杂基础设施成为可选的”，那么争论就结束了。

毫无疑问，这是真正的[对](http://www.mtholyoke.edu/~ebarnes/python/dead-parrot.htm)。忘记 WS-*支持的任何奇怪的用例；当现实世界开始做[这个](http://developer.yahoo.net/common/phpserial.html)时，是时候继续前进了。你想要消息路由？试试这个…

```
 < ?php $in = 'http://api.search.yahoo.com/WebSearchService'. '/V1/webSearch?appid=YahooDemo&results=10&output=php'. '&query=SOAP'; $data = unserialize(file_get_contents($in)); file_put_contents('/dev/null',serialize($data)); ?> 
```

**WS-Headless**

一方面，这都是可以原谅的人类失败——太多的建筑师破坏了 WS-brow。委员会设计意味着支持奇怪的边缘情况和不必要的复杂性。最后让我们继续休息吧。

另一方面，一些责任怎么样？头需要滚动。这不仅仅是一个有趣的智力练习，以帮助提升萎靡不振的自我和工资——这在高层引起了轰动，现在[真人](http://koranteng.blogspot.com/2005/05/get-on-bus.html#glue-layer-people)在 WS-project 失败时受到了指责，尽管被要求做出导致失败的选择。WS-Bandwagon 仍然在全力以赴，即使司机已经跳清楚。

至少，我们需要防止类似的事情再次发生。一些想法；

*   如果你雇佣了架构师，让他们花 5 天中的 4 天来写一些他们真正需要的代码。远离键盘的程序员会有奇怪的想法——现实是一个很好的提醒。
*   如果你有一个很棒的新架构，在和任何人讨论之前，用 Perl 构建一个工作原型。你可以使用 [CPAN](http://www.cpan.org/) ，这给了你一个相当好的机会(称之为[向](http://ftp.ics.uci.edu/pub/websoft/libwww-perl/)[罗伊](http://www.ics.uci.edu/~fielding/)学习)。
*   在描述你伟大的新架构时，如果你需要发明新的单词和缩写，回到你的键盘上。
*   如果你不能在 60 秒内*清楚地*向一个[普通人](http://koranteng.blogspot.com/2005/05/get-on-bus.html#glue-layer-people)描述你伟大的新架构，回到你的键盘前。如果你让他们在这样做的时候去拿字典，请贡献 4 周[志愿服务](http://bugs.php.net/search.php?search_for=&boolean=0&limit=10&order_by=&direction=ASC&cmd=display&status=Open&php_os=&phpver=&assign=&author_email=&bug_age=0)给一个好的事业。
*   永远不要成立委员会。
*   不要发明用例。如果今天没人做，他们明天也不会做。
*   至少有两个关于配置、故障排除和调试是多么容易的故事(来自实际经验)。更好的是，如果你能培养出你所强加的系统管理员。

现在剩下的就是把 [REST](http://en.wikipedia.org/wiki/Representational_State_Transfer) 带出 WS-*争论，[回到现实](http://lesscode.org/2006/03/19/high-low-rest/)，这样我们就可以解决人们[使用 HTTP](http://www.rubyonrael.com/rails.jpg) 的真正问题，就像让[认证](http://bitworking.org/news/Problems_with_HTTP_Authentication_Interop) [工作](http://www.berenddeboer.net/rest/authentication.html)。

我们还需要注意那些做实际工作的人在说什么；

> 最好的建议是尽可能保持一切简单——简单的流程、简单的 SKU、简单的工程。这些系统变得非常大非常快。我不认为真的有任何一个特别困难、棘手的问题，但当你把它们都加起来时，就会有许许多多的小问题。只要你能让每一部分都简单，这似乎就是关键。我认为这更多的是一种哲学，而不是别的。

## 分享这篇文章