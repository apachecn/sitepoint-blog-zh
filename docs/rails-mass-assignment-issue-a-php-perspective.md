# Rails 批量赋值问题:PHP 的观点

> 原文：<https://www.sitepoint.com/rails-mass-assignment-issue-a-php-perspective/>

现在，您的 rails 应用程序的安全性比以往任何时候都更加受到密切关注。Github 用户[霍马科夫(叶戈尔·霍马科夫](https://github.com/homakov)揭露了 Rails 的一个漏洞(毫无疑问，它也存在于你附近的一个应用程序中),这是在 [Rails 项目本身中的一个戏谑行为。](https://github.com/rails/rails/commit/b83965785db1eec019edf1fc272b1aa393e6dc57)。我爱上这个承诺纯粹是因为随之而来的迷因节。然而，Rails 社区的总体反应让我有点惊讶。自那次攻击以来，博客上发布了无数的解决方案，从琐碎到 DSL 奢侈。

## 发生了什么事？

如果你错过了所有的噱头，批量赋值漏洞从年开始就存在于 Rails 或者更确切地说是 ActiveRecord 中。每次在控制器中编写代码时，例如: