# 哪个网址是对的？

> 原文：<https://www.sitepoint.com/which-url-is-right/>

继安德鲁斯上周的[关注好的超链接文本](https://www.sitepoint.com/crimes-against-hypertext/)之后，史蒂夫写信来询问超链接等式的另一面:URL。

史蒂夫写道，

> 发起一场统一网址的运动怎么样？
> 
> http://www.example.com
> http://www.example.com/
> http://www.example.com/index
> http://www.example.com/index.html
> http://example.com
> http://example.com/
> http://example.com/index
> http://example.com/index.html
> 
> 哪个是正确的？？
> 
> 史蒂夫(男子名)

问得好，史蒂夫！首先，这些对是等价的:

*   http://www.example.com
*   http://www.example.com/

*   http://example.com
*   http://example.com/

如果没有指定路径，尾部的“/”是隐含的，因此您可以自由使用这两种形式。同样，端口号(80)在大多数 URL 中是隐含的，但是您可以非常正确地拼写出来:

*   http://www.example.com:80
*   http://www.example.com:80/

*   http://example.com:80
*   http://example.com:80/

当然，最佳实践是当端口号为 80 时，不要使用它。至于是'/'的时候要不要离开路径，那就是个人喜好的问题了。有些人会说，在一个充斥着字节的互联网上，去掉“/”可以节省一个字节。其他人会争辩说，包含“/”会让读者明白你指的是有问题的网站的主页，而不是整个网站。

接下来我们有一个问题，是否包括一个“www。”在主机名的开头。这是一个颇有争议的话题，我们在《科技时报》第 184 期中详细讨论过。简而言之，包括“www。”是对网络传统的认可，而放弃它是对当前网络时尚的屈从。

严格来说，两者都不正确。挑选最适合你的。重要的是确保另一个表单自动重定向到您选择的表单。如果你的服务器对这两种形式都有响应而没有重定向，搜索引擎会把你的站点看作两个独立的站点(一个带有 www。和一个没有)，每一个都将有一个较低的搜索引擎排名，如果你的网站有一个单一的，权威的主机名。

你可以在[无 www 宣传网站](http://no-www.org/)找到[设置你的网络服务器](http://no-www.org/faq.php)的说明，以重定向到你的首选主机名。从它的名字你就能知道它喜欢哪种 URL 风格*！*

至于主页的 URL、/index 或/index.html)，这在很大程度上也是个人风格的问题，关键还是要选择一个，并确保其他的要么重定向到您喜欢的表单，要么显示 404 错误页面。这确保了搜索引擎不会将它们误认为同一页面在不同地址的多个副本。

你觉得*你的*网址怎么样？尾随斜线还是没有？' www . '或者没有“www。”？请留言告诉我！

## 分享这篇文章