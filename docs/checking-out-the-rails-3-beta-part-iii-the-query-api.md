# 查看 Rails 3 beta，第三部分:查询 API

> 原文：<https://www.sitepoint.com/checking-out-the-rails-3-beta-part-iii-the-query-api/>

在我的前两篇文章中，我已经向你介绍了 Ruby on Rails 3.0 测试版中我最喜欢的一些新特性:Bundler gem manager 和 T2 的新路由模块。我认为可以说我把最好的留到了最后:本周我将深入研究 ActiveRecord 查询 API 的变化。

可以说 Rails 的核心是 ActiveRecord，它的对象关系映射(ORM)库。尽管已经做了很多工作来分离 Rails 3.0 的组件，以便原则上可以使用您喜欢的任何 ORM，但 ActiveRecord 仍然是默认的。ActiveRecord 为您提供了创建新模型、将其保存到数据库、更新、删除以及检索其信息的方法。然而，Rails 3 中的新特性主要与您查询模型集合的数据库的方式有关，所以这是我在这里重点关注的。让我们先来看看做那件事的老方法。如果你想要“园艺”类别中最近的 10 篇文章，以及它们的相关评论，你可以这样写:

```
Article.find(:all, :limit => 10, :conditions => {:category => "gardening"}, :order => "created_at DESC", :include => :comments) 
```

您调用`find`方法，用`:all`参数指定您想要的集合，然后传入一堆选项。新语法依赖于将许多方法链接在一起，而不是将所有这些选项传递给`find`方法。因此，要检索这些相同的文章及其评论，您现在应该写:

```
recent = Article.where(:category => "gardening").order("created_at DESC").limit(10).includes(:comments)
```

这些方法中的每一个——`where`、`order`、`limit`和`includes`——都返回一种新类型的对象，称为*关系*。该关系的行为就像模型对象一样，这就是为什么您可以将尽可能多的方法链接在一起。这里很酷的一点是，上面的代码实际上并没有运行 SQL 查询。它只是记住你试图以这个关系对象的形式选择的所有东西。只有当您需要访问选择的内容时，才会执行查询。

假设我写道:

```
recent.each { |a| puts a.title }
```

将运行数据库查询，并将每篇文章的标题按顺序打印到控制台。这种新的链式语法可读性更好，允许您在处理模型集合时有更大的灵活性，并且使某些形式的缓存更加有效(因为控制器中的某些操作不再需要执行数据库查询，所以它们的结果可以被有效地缓存。)

当然，我只是触及了 Rails 3 中新的查询 API 的皮毛。你可以在 Pratik 的博客上读到更详细的描述。

更重要的是，我在本系列中介绍的特性只是 Rails 3 中的几个新特性(尽管在我看来是最炫的！)如果您已经有一段时间没有窥视 Rails 的世界了，那么现在是重新熟悉它的最佳时机。

## 分享这篇文章