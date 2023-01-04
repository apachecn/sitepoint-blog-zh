# Zimbra: Gmail 和 Exchange 遇到开源、Java 和 AJAX

> 原文：<https://www.sitepoint.com/zimbra-gmail-and-exchange-meet-open-source-java-and-ajax/>

Zimbra 要么是我这个月见过的最酷的东西，要么好得令人难以置信。我还没决定哪一个。

Zimbra 以前被称为 Liquid Systems，是该公司的新名称，也是其旗舰产品:一个可扩展的开源客户端/服务器系统，用于管理电子邮件、联系人和日历，可以通过一个流畅的跨浏览器 AJAX 支持的用户界面访问，或者通过 Microsoft Outlook、Mozilla Thunderbird/Sunbird、Apple Mail/iCal 等桌面应用程序访问。

驱动这一切的服务器，Zimbra Collaboration Server，是用 Java 编写的，并基于熟悉的开源组件，如一个 [MySQL](http://www.mysql.com/) 数据库，一个 [Postfix](http://www.postfix.org/) 邮件传输代理(MTA)(默认情况下带有 [SpamAssassin](http://spamassassin.apache.org/) 和 [ClamAV](http://www.clamav.net/) 用于反垃圾邮件和反病毒)和一个 [Tomcat](http://jakarta.apache.org/tomcat/) Web 应用服务器。

尽管 Zimbra 提供的服务(邮件、联系人和日历)都可以通过桌面应用程序(见上文)访问，但是 Zimbra 也提供了自己的跨浏览器、加载了 AJAX 的基于 Web 的用户界面。这个 UI 支持 Zimbra 的许多“额外”功能，如消息标记、统一搜索、电子邮件呈现插件(例如，当您公司的发票号码出现在电子邮件消息中时，将它们链接到您的订单跟踪系统，或者[集成 Google Maps、Skype 和 Alexa](http://www.zimbra.com/blog/archives/2005/09/google_maps_sky.html) 等服务)等等。所有这些功能都带有开放的 API，因此桌面客户端可以得到增强以利用它们，但目前最好通过 Web 客户端界面来访问 Zimbra 的全部功能。

简而言之，Zimbra 是使用开源技术用 Java 重新编写的[微软 Exchange](http://www.microsoft.com/exchange/) 和[Gmail](http://www.gmail.com)……全部免费，跨浏览器，跨平台。

我想我会尝试一下我的个人电子邮件的当前测试版(我目前在家里下载，在工作时使用一个 [Outlook-to-IMAP 网关](http://www.mailrelayer.com/))，如果它兑现了具有合理可扩展性的承诺，我们可能会将 SitePoint 的电子邮件转移到系统中。

## 分享这篇文章