# Ruby 社交宝石:Twitter

> 原文：<https://www.sitepoint.com/ruby-social-gems-twitter/>

在本系列的上一部分中，我们已经介绍了 LinkedIn gem 并创建了一个简单的应用程序，它涵盖了身份验证和 API 查询。出于同样的目的，我们将使用 [Twitter gem](https://github.com/sferik/twitter) 构建一个类似的应用。

与 LinkedIn gem 不同，Twitter gem 不提供认证。因此，我们必须使用 [oauth-ruby](https://github.com/oauth/oauth-ruby) gem 来进行认证。

## 演示应用程序

我们将使用 Twitter gem 构建的应用程序将与我们使用 LinkedIn 构建的应用程序一样。我们将把来自 Twitter 的所有用户数据放入我们的 rails 应用程序模型，这样我们就可以用我们想要的任何格式显示它。

从应用程序开始:

> 你可以在这里下载教程表单 [github 的完整源代码](https://github.com/ahmdrefat/rsg_twitter_gem)

创建一个新的 rails 应用:
`rails new rsg_twitter_gem`

在`path/to/project/Gemfile`