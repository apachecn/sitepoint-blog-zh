# 为什么 robots.txt 中不允许的页面仍然出现在 Google 中

> 原文：<https://www.sitepoint.com/why-pages-disallowed-in-robots-txt-still-appear-in-google/>

robots.txt 是一个有用的文件，它位于你网站的根目录，控制搜索引擎如何索引你的网页。最有用的声明之一是“禁止”——它阻止搜索引擎访问你网站的私人或不相关的部分，例如

```
 Disallow: /junk/
Disallow: /temp/
Disallow: /section1/mysecretpage.html 
```

你甚至可以阻止搜索引擎索引你域名上的每一页:

```
 User-agent: *
Disallow: / 
```

我不确定为什么有人会这么做，但是某个地方的某个人不希望他们的网站出现在搜索引擎结果中。

但是，被屏蔽的页面仍然可以出现在谷歌中。在你站在讲台上痛斥谷歌侵犯 robots.txt 和该公司滥用对网络的控制之前，花一点时间来理解这是如何发生的以及为什么会发生。

假设你在 http://www.mysite.com/secretpage.html 有一个网页，里面有关于你公司新 Foozle 项目的机密信息。您可能希望与合作伙伴共享该页面，但不希望该信息公开。因此，您在 http://www.mysite.com/robots.txt:使用声明阻止了该页面

```
 User-agent: *
Disallow: /secretpage.html 
```

几周后，你在谷歌中搜索“Foozle ”,会出现以下条目:

mysite.com/secretpage.html

怎么会这样？首先要注意的是，谷歌遵守你的 robots.txt 指令——它不索引秘密页面的文本。但是，该 URL 仍然会显示，因为 Google 在其他地方找到了链接，例如

```
 <a href="http://mysite.com/secretpage.html">Read about the new Foozle project…</a> 
```

因此，谷歌将“Foozle”这个词与你的秘密页面联系起来。您的 URL 可能会出现在搜索结果的较高位置，因为 Foozle 是一个很少使用的术语，并且您的页面是唯一的信息来源。

此外，谷歌可以在网址下方显示页面描述。同样，这并不违反 robots.txt 规则——它的出现是因为谷歌在一个公认的资源中找到了你的秘密页面的一个条目，如[开放目录项目](http://www.dmoz.org/)。描述来自该网站，而不是你的网页内容。

## 可以屏蔽页面吗？

有几个解决方案可以阻止你的秘密页面出现在谷歌搜索结果中。

**1。设置一个“无索引”元标签**

如果您将以下代码添加到您的 HTML 中，Google 将永远不会显示您的秘密页面或跟随其链接:

```
 <meta name="robots" content="no index, no follow" /> 
```

**2。使用网址删除工具**

谷歌在其网站管理员工具中提供了一个网址删除工具。

**3。添加认证**

Apache、IIS 和大多数其他 web 服务器都提供基本的身份验证功能。访问者必须输入用户 ID 和密码才能查看页面。这可能不会阻止谷歌在搜索结果中显示页面的 URL，但会阻止未经授权的访问者阅读内容。

**4。查看您的发布政策**

如果你有绝密的内容，也许你不应该在公开的网络上发布这些文件！

## 分享这篇文章