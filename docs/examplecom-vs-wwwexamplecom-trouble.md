# example.com 诉 www . example . com……麻烦了！

> 原文：<https://www.sitepoint.com/examplecom-vs-wwwexamplecom-trouble/>

如果你有 [SitePoint SEM Kit](https://www.sitepoint.com/blog/) 的话，你应该已经知道这个了，但是因为有些人实际上还没有买，这里有一些重要的事情你应该知道。

大多数 web 服务器(因此大多数网站)都被配置为显示相同的内容，不管访问者请求的页面地址中有没有 www。所以 h t t p : / /example.com 很可能会给你和 h t t p://www.example.com 一样的内容。

如果你的网站这样做，那就不好了，因为你在不止一个 URL 下向搜索引擎显示相同的内容。换句话说，你的网站提供了重复的内容。

现在，一些人听到“重复内容”并认为会受到某种惩罚，但这并不是真正的问题。问题是搜索引擎必须选择这些副本中的一个，并声明它是官方的“标准”URL。如果你已经尽全力推广 www.example.com，你会希望搜索引擎会把它视为标准版本……但是他们会吗？

也许吧，但是如果上帝不和宇宙玩骰子，你绝对不应该和搜索引擎玩骰子。尤其是当搜索引擎垃圾邮件制造者的官方博客 Threadwatch 上的一篇帖子谈论如何利用你的小错误来破坏你的排名时。

正确的解决方案是选择一个版本(我喜欢 www。我自己的版本)并将另一个版本重定向到它，使用 301 永久重定向。有许多方法可以实现这一点，在大多数 Apache 设置中，这就像使用。htaccess 文件。

实施例 1，。htacces 条目将 anything.example.com(和 example.com 本身)重定向到 www.example.com:

 `Options +FollowSymlinks
RewriteEngine on
RewriteCond %{HTTP_HOST} !^www.example.com
RewriteRule ^(.*)$ http://www.example.com/$1 [R=permanent,L]`

示例 2，将 anything.example.com(subdomain1.example.com 和 subdomain2.example.com 除外)重定向到 www.example.com:

 `Options +FollowSymlinks
RewriteEngine on
RewriteCond %{HTTP_HOST} !^www|subdomain1|subdomain2.example.com
RewriteRule ^(.*)$ http://www.example.com/$1 [R=permanent,L]`

本代码仅供娱乐之用，使用风险自担。

* *你好，尼克，我只是想提高你在目标搜索项中的排名。*

## 分享这篇文章