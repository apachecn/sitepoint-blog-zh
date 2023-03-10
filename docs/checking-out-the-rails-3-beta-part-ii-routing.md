# 查看 Rails 3 Beta，第二部分:路由

> 原文：<https://www.sitepoint.com/checking-out-the-rails-3-beta-part-ii-routing/>

上周[我向你们](https://www.sitepoint.com/checking-out-the-rails-3-beta-part-i-bundler/)介绍了最新版本的 Ruby on Rails 框架中增加的一个特性:Bundler gem manager。本周，我想更深入一点，向你展示一些更酷的新东西。

然而，在这之前，我应该注意到自从我写了第一部分，Rails 3 的第二个测试版已经发布了。这给了我一个很好的机会来展示使用 Bundler 的另一个优势。假设您对我上周的帖子感到兴奋，并决定获取测试版，开始构建一个新的应用程序。现在，您想要升级该应用程序以使用新的和改进的 beta 2。简单。你只需要改变路线:

```
gem "rails", "3.0.0.beta"
```

改为:

```
gem "rails", "3.0.0.beta2"
```

![Rails logo](img/19b65d3525d2f450f180f27038c97d53.png "Rails logo")运行`bundle install`，然后 ta-da:您的应用程序现在运行在新的和改进的测试版上。完成这些之后，是时候深入研究 Rails 3 的另一个重大变化了。

**路由**

路线一直是 Rails 的优势之一。对于外行来说，路由(位于应用程序的`routes.rb`文件中)是告诉 Rails 哪个 URL 映射到哪个函数的方式。所以，通过写作，例如:

```
map.connect 'posts/:id', :controller => 'posts', :action => 'view'
```

您已经将一个类似于`posts/3`的 URL 连接到您的`posts`控制器中的`view`函数，传入一个`id`来查找正确的帖子。当然，上面的代码使用的是陈旧的 Rails 2 语法。在 Rails 3 中，您可以编写:

```
match 'posts/:id', :to => 'posts#view'
```

新的语法不仅更短，我想你也会发现它更具可读性。你可以用英语说:“将此 URL 与此操作匹配。”如您所见，您不再需要将控制器名称和动作名称指定为单独的参数；你可以只写`"controller#action"`并把它作为一个字符串传入。非常酷。

Rails 3 对 Rails 路由的每个方面都进行了彻底的修改:路由应用程序的根、处理重定向、对参数施加约束、创建命名路由——只要你能想到的。这些变化也不仅仅是装饰性的:有许多功能是旧的路由模块根本无法实现的。对于所有新东西的完整分类，你应该看看 Rizwan Reza 在 Engine Yard 博客上发表的关于这个主题的大量博客文章。

下周，我将试用新的 ActiveRecord 查询 API。在那之前，给自己拿一份测试版，玩一玩吧。你很快就会迷上铁轨的。

## 分享这篇文章