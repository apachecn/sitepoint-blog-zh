# 用 Ruby On Rails 保护网站的技术(第 2 部分)

> 原文：<https://www.sitepoint.com/techniques-to-secure-your-website-with-ruby-on-rails-part-2/>

上周我们看了恶意用户试图劫持合法用户会话的方法。本周，我们将关注当恶意用户注册你的网站时，你将面临的两种危险。我们将再次看到 Rails 如何保护您，以及您如何进一步保护自己。

### 更改意外属性

2012 年 3 月 4 日，Github 中的一个漏洞[被成功利用](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)，使得恶意用户能够获得对 Rails 存储库的提交访问权。这次攻击是一个批量分配漏洞——这是我见过的大多数 Rails 应用程序中的常见问题。这种攻击非常容易实现，而且您的应用程序中很有可能存在这种安全漏洞。

**这是一个如此大的漏洞，却是如此简单的修复方法——为什么不花点时间，立即更新您的应用程序呢？**

我们来详细看看这个黑客。我们将从一个基本模型、控制器和表单开始: