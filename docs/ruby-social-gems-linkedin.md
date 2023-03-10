# Ruby 社交宝石:LinkedIn

> 原文：<https://www.sitepoint.com/ruby-social-gems-linkedin/>

## Ruby Social Gems 系列简介

在这个系列中，我们将讨论基于社交媒体和社交媒体服务的宝石，例如***【LinkedIn】******【Twitter】******【脸书】******Youtube***，以及任何其他建议的宝石。

## LinkedIn 创业板

该系列的第一个 gem 是 LinkedIn gem 和 LinkedIn APIs。我们将看到 LinkedIn API 是多么简单和清晰，以及 gem 是如何使它更易访问的。

在开始之前，我应该指出另一个重要的亮点:omniauth 和 omniauth-linkedin。这些 gem 允许用户用他们的 LinkedIn 账户，或者脸书，或者 Twitter 等登录。稍后我们将专门为 Omniauth 编写一个教程，但是在本文中我们将使用 Devise 来创建简单的用户帐户。

## 演示应用程序

我们将要用 LinkeIn gem 构建的应用程序非常简单。我们将把 LinkedIn 的所有用户数据放入我们的 rails 应用程序模型，这样我们就可以用我们想要的任何格式显示它。

从应用程序开始:

> 你可以在这里下载教程表单 [github 的完整源代码](https://github.com/ahmdrefat/rsg_linkedin_gem)

创建一个新的 rails 应用:
`rails new rsg_linkedin_gem`

添加领英，定计，自举宝石`path/to/project/Gemfile`