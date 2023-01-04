# OSCON 2006:实用 OpenID

> 原文：<https://www.sitepoint.com/oscon-2006-practical-openid/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

[大卫·雷科登](http://www.davidrecordon.com/) ( [六分之](http://www.sixapart.com/)，然后是[威瑞信](http://www.verisign.com/))和[布莱恩·艾林](http://brianellin.com/) ( [詹瑞恩](http://janrain.com/))从 OpenID 成立之初就已经染指其中。 [OpenID](http://openid.net/) 是一个开放的用户认证系统，以个人拥有 URL 为前提。最初开发该系统是为了验证希望对博客帖子发表评论的用户，试图减少垃圾评论。它被设计得非常简单，因此用户只需要理解如何编写 HTML 就可以使用该系统。

OpenID 2.0 项目(最近[被 Apache 孵化器](http://netmesh.info/jernst/News/apache-heraldry-incubator-accepted.html)接受)在 OpenID 的原始范围上扩展，增加了像消息传递这样的功能，但仍然忠实于 OpenID 的原始精神:简单和开放。

重要的是，OpenID 不处理信任问题——OpenID 只允许一个人证明他或她拥有一个特定的 URL，它不会给你任何关于此人可信度的信息。

OpenID 最大的特点是它的去中心化
性质。任何人都可以自由建立自己的 OpenID 服务器，它可以
存储用户凭证，并为那些
用户提供对任何支持 OpenID 的网站或应用的认证服务。

OpenID 目前在博客服务和软件中被广泛采用；然而，有一股积极的力量推动它在更大范围内被采用。甚至有一个由感兴趣的公司发起的奖金计划，愿意为实现 OpenID 核心支持的应用支付 5000 美元。

将 OpenID 支持构建到您自己的 web 应用程序中意味着您可以花更少的时间来实现诸如用户认证之类的事情，并且您的用户不需要仅仅为您的站点记住一组新的凭证。

作为用户，设置您自己的 OpenID 身份只需向这样的 OpenID 服务器注册(例如， [MyOpenID](http://myopenid.com/) 提供免费的 OpenID 服务器)，然后在您声明的 URL 中包含以下 HTML 链接元素，这些元素声明您希望站点用来检查您的凭证的 OpenID 服务器:

```
 <link rel="openid.delegate" href="http://brian.myopenid.com/" />
  <link rel="openid.server" href="http://www.myopenid.com/server" /> 
```

还存在一种替代的 XML 格式，用于提供这些细节，而不将它们嵌入到网页中。

Ellin 继续演示了使用他的公司 JanRain 开发的 Ruby OpenID 支持库向 Ruby on Rails 应用程序添加 OpenID 支持所涉及的代码。

OpenID 还可以扩展为包括简单的用户注册信息(例如，电子邮件地址、昵称、位置、时区等)。)，Ellin 也展示了一个演示。

这次演讲中的听众似乎非常关心所有潜在的安全问题，Recordon 和 Ellin 泰然自若地回答了这些问题。

## 分享本文