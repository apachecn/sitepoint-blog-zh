# 为 Rails 2.0 做准备

> 原文：<https://www.sitepoint.com/preparing-for-rails-20/>

任何使用过 Rails 1.2.3、1.2.4 或 1.2.5 的人都可能在他们的开发日志中注意到一些反对通知。虽然这些不推荐使用的方法在 1.2.x 版本中仍然可以正常工作，但是当您尝试升级到 Rails 2.0 时，就会遇到问题。那么，你需要做什么，有什么工具可以帮助你完成这一步呢？很高兴你问了。

你可以做的第一件事是通过代码检查器运行你的代码——Geoffrey grosen Bach 发布了一个伟大的 [rake task](http://topfunky.net/svn/plugins/deprecated/tasks/deprecated.rake) ,它会在你的代码中挖掘旧的方法。它将提示您如何修复这些问题，但是让我们更仔细地看一看。

**@params，@session，@flash，@env**

从 Rails 2.0 开始，您将无法直接访问上述实例变量。它们已经被方法所取代，这使得定制它们的动作更加容易。它还允许在不破坏 API 的情况下改变 Rails 的内部结构。这很容易解决——只需去掉这些变量前面的@符号——它们将完全一样地工作。

**find_all，find_first，render_partial**

在早期版本的 Rails 中，有许多分组方法，它们做的事情非常相似——find、find_all 和 find_first 都从数据库中获取记录，唯一的区别是它们返回的记录数量。我们决定将这些方法合并成一个方法，通过传入的选项来区分它们。所以 find_all 变成 find(:all)find _ first 变成 find(:first)render _ partial 变成 render(:partial)。

**表格**

在所有的 HTML 助手中，form 标签是一个异常，因为它需要一个开始和结束助手。为了使它适应 Rails 的其他工作方式，并促进动态表单生成，创建了一个名为 form_tag 的块方法。这个特殊的更新有一个陷阱——因为块不返回值，所以你使用的 ERB 标签不能有=号，所以

```
 <%= start_form_tag %>
       <!-- Form stuff -->
   <%= end_form_tag %> 

```

成为

```
 <% form_tag do %>
        <!-- Form stuff -->
    <% end %> 

```

注意到后一个例子中省略了等号吗？

还要注意传递:post = > % gttrue 已被否决。为了推动 RESTfulness，表单需要了解其他 HTTP 动词 put 和 delete，因此创建了一个新选项:

```
 <% form_tag :method => :post do %>
        <!-- Form stuff -->
    <% end %> 

```

**插件**

许多曾经是 rails 核心组件的东西已经被转移到插件中，这样就不会用不常用的东西把核心弄得乱七八糟。这也意味着插件的开发可以比核心快得多。可能主要的提取是第三方数据库接口。现在，默认情况下只支持 MySQL、SQLite 和 PostgreSQL。所有其他数据库都通过名为 active record-*database*-adapter 的 gems 来支持。如果您想使用 Oracle，只需运行

gem active record-Oracle-适配器

你会恢复健康的。

其他值得注意的摘录是 acts_as 插件。如果你在你的模型中使用 acts_as_tree 或者 acts_as_list，你将需要*脚本/插件安装*它们，内置分页现在已经成为 classic_pagination 插件。注意，开发人员自己也承认插件很慢(在核心中时也很慢)，所以如果您使用它，您可能要考虑迁移到新的和改进的 will_paginate 插件。

因此，赶快行动起来，赶在高峰到来之前升级你的网络应用吧！

## 分享这篇文章