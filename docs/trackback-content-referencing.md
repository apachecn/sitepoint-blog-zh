# 面向未来的 TrackBack:下一代内容参考

> 原文：<https://www.sitepoint.com/trackback-content-referencing/>

TrackBack 由 [MovableType](http://www.movabletype.org) 的创造者于 2002 年 8 月首次发布，自那以后，该产品在博客社区(或“博客圈”)中广受欢迎。TrackBack 是一个相对较新的框架，用于网站之间的对等通信和通知。通过引用通告，网站可以就相似的兴趣和资源进行交流。

尽管 TrackBack 最初是作为博客专用工具开发的，但最近开始在常规内容网站中流行起来，如 [O'Reilly](http://www.oreilly.com/) 。在本文中，我将简要介绍 TrackBack 的可能用途、工作原理(我不会深入研究代码)、产品的优点和缺点，最后给出结论，包括可以帮助您在自己的站点中实现 TrackBack 的资源。

##### 什么是 TrackBack？

TrackBack 是一个 API，它使网站能够就相似的兴趣和资源进行交流。

让我们想象你运行一个博客，你写了一个有趣的帖子。你的朋友也有博客，他想对你的帖子发表评论。他不是在你的博客上发表评论，而是简单地在他自己的博客上发表评论，并向你的网站发送一个引用通告。然后，您会收到关于您的条目的评论通知，并浏览您朋友的帖子。然后，你可以决定对你朋友的评论进行评论。讨论还在继续。因此，TrackBack 可以用作不同网站和人之间的讨论板。

以我刚才描述的方式使用 TrackBack 的几个例子是:

*   [http://Jeremy . zawodny . com/blog/archives/001504 . html # trackbacks](http://jeremy.zawodny.com/blog/archives/001504.html#trackbacks)
*   [http://weblogs.mozillazine.org/mt/mt-tb.cgi?__mode=view&entry _ id = 4702](http://weblogs.mozillazine.org/mt/mt-tb.cgi?__mode=view&entry_id=4702)
*   [该软件的另一个可能用途是建立相关内容的存储库。假设你是一个 PHP 爱好者，你想创建一个关于 PHP 的新网站。你可以简单地要求人们在每次对 PHP 有所评论时向你的网站发送一个“ping ”,而不是让人们向你的网站添加内容。

    引用通告知识库的几个例子是:](http://www.sixapart.com/log/2003/10/its_all_about_c.shtml#trackback</li>
    <p>“>http://www.sixapart.com/log/2003/10/its_all_about_c.shtml#trackback</li>
    <p></a>
    </ul>
    </p>
    <div id=) 
    [](http://www.sixapart.com/log/2003/10/its_all_about_c.shtml#trackback</li>
    <p>“>http://www.sixapart.com/log/2003/10/its_all_about_c.shtml#trackback</li>
    <p></a>
    </ul>
    </p>
    <div id=)
    *   [http://www.blogroots.com/blogpopuli.blog](http://www.blogroots.com/blogpopuli.blog)(一个博客库)
    *   [http://www.jacobsen.no/books/](http://www.jacobsen.no/books/)(一个书评库)
    *   [http://www.theredkitchen.net/](http://www.theredkitchen.net/)(一个烹饪食谱库)

    这只是几个场景，引用通告还有更多的用途，并且与 RSS 等其他技术相结合，它可以成为站点间通信的一种优秀方式。

    TrackBack 是目前唯一提供我刚才描述的功能的框架。到目前为止，还没有其他 API 能提供 TrackBack 所提供的相同或更好的功能。某些方面正在考虑创建一个类似的系统，但是在新系统开发出来之前，TrackBack 将继续是同类产品中唯一的产品。

    ##### TrackBack 是如何工作的？

    TrackBack 通过发送“pings”起作用，这是标准的 HTTP 调用。TrackBack 流程有两个部分:客户端和服务器端。客户端通常是你的软件或者 CMS(比如 [MovableType](http://www.movabletype.org) )。服务器在接收端；虽然它可以是任何东西，但在大多数情况下，它是一个 ASP/PHP/Perl 脚本。

    要发送 ping，您的客户机向服务器发出一个标准的 HTTP 请求。然后，它从服务器接收 XML 格式的响应。您必须将您的 ping 发送到一个引用文章、类别等的特定 URL。这称为引用通告 Ping URL。一个典型的引用通告 Ping URL 如下所示:http://www.example.com/mt-tb.cgi/3.在你自己的网站上实现引用通告时，你可以使用任何你喜欢的格式。

    要发送 ping，您的客户端必须向引用通告 URL 发送 HTTP POST 请求。应该使用的内容类型是“application/x-www-form-urlencoded”。典型的请求可能如下所示:

    ```
    POST http://www.example.com/mt-tb.cgi/5 

    Content-Type: application/x-www-form-urlencoded 

    title=An+Example&url=http://www.somesite.com/&excerpt=My+Excerpt&blog_name=SomeBlog
    ```

    如您所见，有 4 个可能的参数:

    *   **标题**–(可选)——你的文章或文章的标题。
    *   **摘录**–(可选)——文章的简短摘录或描述。根据软件的不同，该参数可能被裁剪为 255 个字符(例如，MovableType 就是这样做的)。
    *   **URL**–你的文章或文章的 URL。这应该是一个永久的链接，在不久的将来不应该被改变。
    *   **blog _ name**–(可选)–您的博客或网站的名称。

    在您的 ping 被发送到服务器后，您将从服务器收到一个 XML 格式的响应。对成功 ping 的响应如下所示:

    ```
    <?xml version="1.0" encoding="iso-8859-1"?> 

    <response> 

    <error>0</error> 

    </response>
    ```

    如果 ping 失败，响应将如下所示:

    ```
    <?xml version="1.0" encoding="iso-8859-1"?> 

    <response> 

    <error>1</error> 

    <message>The error message</message> 

    </response>
    ```

    ##### 事物的利与弊

    TrackBack 有几个优点和缺点。我个人的看法是利远大于弊，但是自己判断:

    ***优点***

    *   让别人知道你发现的有趣内容非常容易。
    *   您可以使用自己的博客或网站对文章或帖子发表评论。
    *   引用通告实际上是与其他网站的连接。它将允许你与其他人交谈，而不需要在讨论板、邮件列表等上注册。
    *   TrackBack 实际上对结识新朋友非常有用。自从我自己使用 TrackBack 以来，我已经通过这个系统认识了几个新朋友。

    ***缺点***

    *   TrackBack 系统，像任何其他在线通信模式一样，极易受到垃圾邮件的攻击。目前还没有真正解决这个问题的方法。
    *   如果你不小心，你可能会泄露某些应该保密的事情(阅读[jeremy.zawodny.com](http://jeremy.zawodny.com/blog/archives/001189.html)了解更多信息)。
    *   该产品的接受速度一直很慢，目前，没有足够的网站使用它来使该产品成为绝对不可或缺的。然而，随着越来越多的网站开始实施 TrackBack，它将很快变得真正有用。
    *   空的引用通告页面(通常由 MovableType 生成)给谷歌带来了很大的问题，可以在这里读到[，在这里](http://www.theregister.com/content/archive/33141.html)读到[。这个问题源于 TrackBack 的实现方式——这不是 TrackBack 本身的核心缺陷。](http://www.theregister.com/content/archive/33448.html)

    这些是产品的主要优点和缺点。TrackBack 的主要优势在于它在与他人交流方面提供了强大的功能——您无需离开自己的网站或博客就可以进行交流。在我个人看来，这个优点使得它非常值得在你自己的站点上实现！

    ##### 结论

    虽然 TrackBack 仍然是一个相对较新的系统，但它已经非常有前途。随着越来越多的网站开始流行，该产品的潜力是巨大的。

    引用通告允许您轻松引用外部网站、文章和其他内容。因为 TrackBack 使用标准的 HTTP POST 请求，所以它可以用任何编程语言在任何服务器上实现。

    TrackBack 系统有几个优点和缺点，但在我个人看来，利远大于弊。TrackBack 提供的直接交流对任何面向社区的网站都是一个巨大的好处。

    我还没有在自己的网站上实现 TrackBack，但过去几个月我一直在我的博客上使用它。到目前为止，这种体验是非常积极的。我通过 TrackBack 结识了几个新朋友，发现了不少有趣的文章，否则我是不会去读的。

    这里有一些资源可以帮助你在自己的网站上开始实施 TrackBack。你不需要担心任何授权方案，因为 TrackBack 是免费的！

    *   [TrackBack 技术规范](http://www.movabletype.org/docs/mttrackback.html)
    *   [单机版 TrackBack 实现](http://www.movabletype.org/downloads/tb-standalone.tar.gz)(tar.gz 文件)
    *   [TrackBack 开发](http://www.movabletype.org/trackback/)
    *   [RSS 1.0/2.0 的 TrackBack 模块](http://madskills.com/public/xml/rss/module/trackback/)
    *   [自动发现引用通告 Ping URL](http://www.movabletype.org/docs/mttrackback.html#autodiscovery%20of%20trackback%20ping%20urls)

## 分享这篇文章