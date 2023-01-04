# 关于饼干你可能不知道的 3 件事

> 原文：<https://www.sitepoint.com/3-things-about-cookies-you-may-not-know/>

如果你曾经困惑于为什么[实时 HTTP 头](http://livehttpheaders.mozdev.org/)和 [Firebug](http://getfirebug.com/) 告诉你你的“Cookie:”请求头与你刚刚收到的“Set-Cookie:”响应头不匹配，这里有几点值得记住:

1.  只有与原始 cookie 的*名称*、*路径*和*域*完全匹配的后续 cookie 才能覆盖(或删除)cookie。即使由 www.example.org 设置的域为“. example.org”的 cookie 完全有效，它也不会覆盖先前针对“www . example . org”设置的同名 cookie。相反，两个 cookies 都将被存储，并且在后续请求中只发送一个。
2.  如果多个同名的 cookies 匹配给定的请求 URI，浏览器将选择其中一个。
    路径越具体，优先级越高。然而，基于其他属性的优先级，包括域，[是未指定的](http://rfc.net/rfc2109.html#p10 "RFC 2109, Page 10")，并且可能在不同的浏览器之间有所不同。这意味着，如果你已经针对“. example.org”和“www.example.org”设置了同名 cookies，你无法确定哪个会被发回。
3.  根据 Netscape 的初步规范，HTTP 状态对象被称为 cookie <q>,没有令人信服的理由</q>。

## 分享这篇文章