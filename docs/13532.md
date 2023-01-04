# 注册多个域名的情况

> 原文：<https://www.sitepoint.com/the-case-for-registering-multiple-domains/>

通过 [/。](http://slashdot.org/articles/06/10/30/0329258.shtml)–[优化页面加载时间](http://www.die.net/musings/page_load_time/)有一些非常有趣的见解，尤其是这一点；

> 默认情况下，IE 只允许每个主机名有两个未完成的连接，或者总共有八个未完成的连接。Firefox 也有类似的限制。使用最多四个主机名而不是一个主机名会给你更多的连接。(IP 地址不重要；主机名都可以指向同一个 IP。)

那其实是一个 [HTTP 1.1](http://www.faqs.org/rfcs/rfc2616.html) 推荐(8.1.4 节)；

> 使用持久连接的客户端应该限制它们与给定服务器保持的
> 同时连接的数量。一个
> 单用户客户端不应该与
> 任何服务器或代理保持 2 个以上的连接。一个代理应该使用多达 2*N 个连接到
> 另一个服务器或代理，其中 N 是同时
> 活动用户的数量。这些指导方针旨在改善 HTTP 响应
> 时间并避免拥塞。

更多真知灼见[在这里](http://kb.mozillazine.org/Network.http.max-connections)和[在这里](http://blogs.msdn.com/ie/archive/2005/04/11/407189.aspx)。这也是你应该考虑的与 AJAX 相关的事情。

一个小问题:“优化页面加载时间”中推荐的方法是；

> 不要从 http://static.example.com/,加载所有的对象，而是创建四个主机名(例如 static0.example.com、static1.example.com、static2.example.com、static3.example.com)

尽管这可能不是最佳解决方案——如果您在. mysite.com 这样的域中使用 cookie，那么客户端将在其静态内容请求中发送 cookie——这可能会错过让中间代理服务器为您缓存的机会，从而进一步分散负载/减少网络距离——在“3。静态内容的无 cookie TLD”(注意演讲者注释)。

换句话说，当你开始担心这些东西时，你可能会考虑为静态内容注册一个单独的域，在这个域下，你可以使用子域来减少页面加载时间。

## 分享这篇文章