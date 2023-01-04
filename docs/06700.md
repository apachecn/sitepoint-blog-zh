# RIP Twitter API 1.0

> 原文：<https://www.sitepoint.com/rip-twitter-api-1/>

Twitter 最终在 2013 年 6 月 11 日关闭了 API 的 1.0 版本。如果您的网站或应用程序今天失败了，这很可能是原因。

Twitter 宣布他们去年废弃了第一个 API，给了我们至少六个月的时间来整理我们的代码。最初的 5 月 7 日停止日期被推迟，但如果你还在使用它，API 1.0 REST URLs 现在将报告*“Twitter REST API v1 不再活跃。请迁移到 API v 1.1 .[https://dev.twitter.com/docs/api/1.1/overview](https://dev.twitter.com/docs/api/1.1/overview)*

## 什么变了？

API v1.0 版和 1.1 版的主要区别在于:

1.  API v1.1 仅支持 SSL。
2.  JSON 现在是唯一可用的数据格式——XML、RSS 和 ATOM 已经退役。
3.  所有请求都必须通过 OAuth 进行身份验证。

## 公共数据的私有身份验证？

正是这最后一点引起了最大的混乱。

如果您的应用程序想代表用户发 tweet，用户必须提供许可。这是可以理解的，并且在第一个 API 中实现了。然而，如果你只是想在自己的网站上显示自己的推文呢？在 API 1.0 中，您可以使用 REST URL 访问您的时间线，例如:

[https://api.twitter.com/1/statuses/user_timeline.json?screen_name=craigbuckler](https://api.twitter.com/1/statuses/user_timeline.json?screen_name=craigbuckler)

可以相应地解析和显示返回的数据。有很多例子——2011 年初，我在 SitePoint 上发布了一个小的 Twitter 部件。

尽管这些推文在 Twitter.com 上[是公开的(可能会被截屏)，但原始数据不再可用，除非你的应用提供认证。](https://twitter.com/craigbuckler)

## 显示度数

情况变得更糟了。Twitter 规定，所有应用程序必须遵守[开发者显示要求](https://dev.twitter.com/terms/display-requirements)，以确保推文和时间线在整个网络上以一致的方式呈现*“以满足用户期望的方式”*。

有没有人在遇到 140 个字符的消息时很迷茫？不再是了——Twitter 的标识和链接必须贴满整个页面。最后，别忘了经常查看指南；每当 Twitter 说你应该更新你的应用程序时，你就有责任更新它。

## 网络商业化

Twitter 想要收回对一个不守规矩的系统的控制权。从商业角度来看，他们更喜欢你使用官方的 Twitter 小工具，允许他们操纵推文和演示。如果你坚持使用一个应用程序，API 1.1 允许 Twitter 在没有警告或理由的情况下关闭一个服务。

这项政策可能会适得其反。Twitter 增长迅猛的原因之一是因为一个开放的 API，它允许开发者创建公司从未设想过的应用和小工具。虽然他们必须赚钱才能生存，但关门将迫使许多网站取消最终促进 Twitter 发展的订阅源。会有同样多的人使用一个更公司化而不是更有用的服务吗？

Twitter 不再拥有他们创造的服务；它归用户所有。如果他们不能理解或货币化这个概念，Twitter 还有未来吗？

你的 Twitter 应用今天受到影响了吗？您有时间和意愿迁移到 API 1.1 吗？你会转而使用官方的 Twitter 插件吗？还是会抛弃一些 Twitter 应用？

## 分享这篇文章