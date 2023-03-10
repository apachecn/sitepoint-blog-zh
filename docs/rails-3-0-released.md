# Rails 3.0 发布

> 原文：<https://www.sitepoint.com/rails-3-0-released/>

上周，经过长时间的测试后，Ruby on Rails 团队终于发布了 Rails 3.0。今年早些时候，我在博客中介绍了 Rails 3 中一些最酷的新特性:[一个名为 Bundler](https://www.sitepoint.com/checking-out-the-rails-3-beta-part-i-bundler/) 的新包依赖管理器，一个对 ActiveRecord 查询语法的[改进](https://www.sitepoint.com/checking-out-the-rails-3-beta-part-iii-the-query-api/)，以及一个[全新的路由系统](https://www.sitepoint.com/checking-out-the-rails-3-beta-part-ii-routing/)。

不过，Rails 3 的功能远不止这些。新版本解耦了 Rails 的所有组件，因此它们可以很容易地被替换为您喜欢的替代品；例如，您可以轻松地使用 jQuery 代替 JavaScript 的默认原型，或者使用 [datamapper](http://github.com/datamapper/dm-core) 代替 ORM 的 ActiveRecord。Rails 3 还利用 Ruby 1.9 的 Unicode 魔力来解决字符编码问题，并包含内置的 XSS 保护。Rails 的电子邮件组件 ActiveMailer 已经重写，现在更容易使用。

看看 Rails 博客上的[官方声明，并查看标题为](http://weblog.rubyonrails.org/2010/8/29/rails-3-0-it-s-done)[深入 Rails 3](http://rubyonrails.org/screencasts/rails3) 的一系列截屏，了解更多细节。

渴望开始吗？像往常一样，用`gem install rails`更新或安装，或者[查看下载页面获得完整的安装说明](http://rubyonrails.org/download)。

## 分享这篇文章