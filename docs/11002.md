# 离推特 OAuthoclypse 还有 7 周

> 原文：<https://www.sitepoint.com/twitter-oauth-authentication/>

你的 Twitter 客户端使用基本的用户名和密码认证吗？如果是这样的话，它将于 2010 年 6 月 30 日停止工作— [倒计时已经开始](http://www.countdowntooauth.com/) *(顺便说一句，很好看的复古 JavaScript 动画！)*

Twitter API 中的基本认证将被关闭，OAuth 将成为从那时起唯一的选项。幸运的是，使用 OAuth 有几个好处:

*   您不需要担心存储或意外泄露凭据
*   用户密码更改是自动处理的
*   还有许多其他应用程序也使用 OAuth
*   有大量的文档和代码示例。

不幸的是，您是否相信并不重要:OAuth 更改必须实现，否则您的 Twitter API 调用将失败。

如果您以前没有使用过 OAuth，也不必惊慌。Twitter 的开发者文档提供了几个很好的资源:

*   [认证请求](http://dev.twitter.com/pages/auth)
*   [OAuth 库](http://dev.twitter.com/pages/oauth_libraries)语言包括 JavaScript、PHP、C#、ActionScript、iPhone Objective-C/Cocoa、Python 和 Perl。
*   [Twitter 开发讲座](http://groups.google.com/group/twitter-development-talk)

我怀疑在接下来的几周里，这个变化会让许多开发人员忙个不停。会影响你的申请吗？

## 分享这篇文章