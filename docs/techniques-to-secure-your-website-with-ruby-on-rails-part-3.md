# 用 Ruby On Rails 保护网站的技术(第 3 部分)

> 原文：<https://www.sitepoint.com/techniques-to-secure-your-website-with-ruby-on-rails-part-3/>

这是我们探索 Rails 安全性的最后一部分。上周，我们研究了[大规模分配和 SQL 注入](https://www.sitepoint.com/techniques-to-secure-your-website-with-ruby-on-rails-part-2)问题，之前已经探讨了[会话劫持的风险](https://www.sitepoint.com/techniques-to-secure-your-website-with-ruby-on-rails-part-1)。本周，我们将探讨缓存的风险、防范 XSS 以及其他一些 Rails 安全技巧。

### 缓存的风险

良好的片段缓存是提高性能的最重要的事情之一。优雅到期的嵌套片段缓存功能强大且美观，但有一个很大的风险:如何确保人们只看到他们应该看到的东西？有几次我被抓到了，追查起来会很恐怖。

最大的危险是在最终被缓存的模板中嵌入特定于用户的代码。我将举一个真实世界的例子，这个例子差点让我们陷入困境。在[媒体教育](http://www.meducation.net)上，一个用户有一个关于他们的各种信息的简档。我们曾经在个人资料页面上有一个部分，只有管理员才能看到，显示用户的电子邮件地址和其他信息——主要是为了我们需要联系他们。我们有一个这样的模板: