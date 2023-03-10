# 10 个 Ruby on Rails 最佳实践

> 原文：<https://www.sitepoint.com/10-ruby-on-rails-best-practices-2/>

如果您是 Ruby on Rails 的新手，最令人畏惧的一个方面就是知道完成给定任务的首选方式是什么。虽然作为社区的首选方法，许多技术和库来了又去，但仍然有一些最佳实践，可以编写出最干净、最安全和可维护的 Rails 代码。

今天这里列出了作为 Ruby 开发人员可以使用的 10 个最流行和最有用的最佳实践。

## 胖模特，瘦控制器

可以说是用 Ruby on Rails 编写清晰简洁代码的最重要的方法之一，格言“胖模型，瘦控制器”指的是 MVC 的 M 和 C 部分如何完美地协同工作。也就是说，任何与响应无关的逻辑都应该放在模型中，最好是放在一个好的、可测试的方法中。同时,“skinny”控制器只是视图和模型之间的一个很好的接口。

在实践中，这可能需要一系列不同类型的重构，但这都归结为一个想法:通过将任何与响应无关的逻辑(例如，设置 flash 消息，或者选择是否重定向或呈现视图)移动到模型(而不是控制器)，不仅在可能的地方促进了重用，而且还使得在请求的上下文之外测试代码成为可能。

让我们看一个简单的例子。假设您有这样的代码:

```
def index
  @published_posts = Post.all :conditions => {['published_at <= ?', Time.now]}
  @unpublished_posts = Post.all :conditions => {['published_at IS NULL OR published_at > ?', Time.now]}
end
```

您可以将其更改为:

```
def index
  @published_posts = Post.all_published
  @unpublished_posts = Post.all_unpublished
end
```

然后，您可以将该逻辑移动到您的 post 模型中，看起来可能是这样的:

```
def self.all_published
  all :conditions => {['published_at <= ?', Time.now]}
end

def self.all_unpublished
  all :conditions => {['published_at IS NULL OR published_at > ?', Time.now]}
end
```

使用方法`Post.all_published`和`Post.all_unpublished`，我们不仅简化了代码的测试，还使得在其他地方重用相同的条件集成为可能。但是我们很快就会看到，即使这样也不理想。

## 可重用的范围和关系

在上面的例子中，我们仍然没有达到最佳水平。例如，如果我们想获取一个已发布的帖子呢？我们必须用另一种方法复制这些条件——这只会导致越来越多的垃圾代码。

幸运的是，Rails 提供了一种更好的方式— **作用域**(在 Rails 的旧版本中，它们被称为**命名作用域**)。简而言之，作用域是数据库交互的一组约束(比如条件、限制或偏移量)，它们是可链接和可重用的。因此，我可以调用`MyModel.my_scope.another_scope`，或`MyModel.my_scope.first`，或`MyModel.my_scope.all`。

因此，再次以我们之前的例子为例，我们可以在 Rails 3 中将其重写如下:

```
scope :published, lambda { where('published_at < = ?', Time.now) }
scope :unpublished, lambda { where('published_at > ?', Time.now) }
```

在 Rails 2 中:

```
named_scope :published, lambda { {:conditions => ['published_at < = ?', Time.now]} }
named_scope :unpublished, lambda { {:conditions => ['published_at > ?', Time.now]} }
```

这将允许我们在需要的地方使用`Post.published.all`和`Post.unpublished.all`。

更好的是，从 Rails 3 开始，Rails 现在支持**关系**——本质上，可以在任何地方使用的任意范围。例如，`Post.where(:title => 'Hello World').first`代替了`Post.first :conditions => {:title => 'Hello World'}`，这意味着您现在获得了强大的特性，比如链接任意数据库调用。

这导致更好地构建代码和更多可重用的查询——您开始考虑一个范围或范围组合可以实现什么——换句话说，大图——而不仅仅是您的一个查询是什么。另外，通过简单地添加所需的关系和范围，可以更好地构建非常复杂的查询，比如搜索。

## 将你的代码打包成宝石和插件

如果你经常使用 Ruby on Rails，你很可能会注意到 Rails 开发者可以使用的 rubygems 的财富。如果有一个相对普遍的问题，很可能另一个开发人员已经解决了它。

因此，当你编写你认为足够通用的代码时——这通常意味着你已经在另一个应用程序中不止一次地编写过它，停下来想想如何将它提取到一个适合更广泛用途的插件或 gem 中。这不仅在下一次你需要上述功能时会有回报，而且它还会迫使你停下来评估你解决问题的方法——通常，我发现从应用程序中提取代码会导致更简单的设计。你也不应该忘记，作为一名开发人员，发布开源代码可以在其他方面获得回报。

当下次使用这些代码时，作为一个额外的好处，它通常已经被测试和充分探索过了——从重构的多个阶段中得到更好的代码。

同样，花些时间看看已经解决了您的问题的开源宝石。如果你不确定从哪里开始，GitHub 搜索通常是一个很好的起点，T2 Ruby 工具箱包含了社区中最流行的插件列表。

## 使用内置的 Ruby Duck 类型方法

作为一种语言，Ruby 使用了几个约定来简化开发。例如，在一个对象上实现一个`to_s`实例方法会给你一个标准的方法来获得对象的字符串表示。

通过实现这些标准的类型转换——除了`to_s`,还有针对整数的`to_i`和针对数组的`to_a`——您可以让您的 Ruby 代码更加简洁。作为一个例子，看看下面的字符串插值:

```
"Hello there, #{user.name}"
```

如果您将属性`name`别名为`to_s`，您可以简单地写为:

```
"Hello there, #{user}"
```

Ruby 中使用`to_s`(在其他情况下，`to_i`等)的其他地方将自动利用对象的这种字符串表示。

除此之外，您还可以为您想要提供有用迭代特性的任何类实现[可枚举](http://ruby-doc.org/core/classes/Enumerable.html)模块。你只需要在你的类中编写`each`和`<=>`方法。这两个简单的附加功能免费为您提供了一大堆额外的功能:像`map`、`inject`、`sort`、`max`、`min`等方法。

## 管理属性访问

默认情况下，当在 Rails 中使用 mass 赋值时——也就是说，类似于`User.new(params[:user])`和`@user.update_attributes params[:user]`的代码——Rails 将对每个属性进行赋值，而不做任何检查。您的验证可以防止错误数据，但是不能防止您覆盖不想更改的属性。

为了解决这个问题，ActiveRecord 使用了两种方法— `attr_protected`和`attr_accessibile`。使用`attr_protected`，你可以声明一个你不想赋值的变量的黑名单(例如，`attr_protected :admin, :password_hash`)。使用 attr_accessible，这是通常的首选，您可以声明您希望能够分配的对象(例如，`attr_accessible :login, :email, :password, :password_confirmation`)。

通过这样做，您可以防止任何可能通过应用程序的表单发生的大量赋值——仅仅因为您没有给定属性的字段并不意味着黑客不能将它添加到请求中。这样，您要么被迫手动设置某些属性值，要么更有用的是，提供一个受保护的方法在您想要设置值时使用。

从安全的角度来看，使用`attr_accessible`和`attr_protected`迫使你考虑什么应该是可编辑的，以及如何保护你的类的属性被设置的方式。

## 使用非数据库支持的模型

尽管 Rails 中的模型大多基于`ActiveRecord::Base`或其他类型的数据库对象映射器，但重要的是要记住，在 MVC 中，M 并不局限于数据库支持的模型。

使用非数据库支持的模型可以帮助组织逻辑，否则可能会变得混乱。例如，有些图书馆给你一个类似于`ActiveRecord`的界面，用于[联系人表单电子邮件](https://github.com/YouthTree/big-help-mob/blob/develop/app/models/contact_form.rb)。

使用 ActiveModel(在 Rails 3 和更高版本中可用)，可以获得封装了一组公共行为的任意对象，并将它们用作您的模型。添加虚拟模型还使得遵循 RESTful 控制器设计变得更加容易，因为您可以将数据而不是数据库条目表示为资源。举个主要的例子，Rails 中几个流行的认证库现在将用户当前认证的会话表示为一个模型，我个人实现了一个[密码重置](https://github.com/YouthTree/big-help-mob/blob/develop/app/models/password_reset.rb)作为模型。

当需要在控制器代码中与这些模型进行交互时，您的代码会更加简洁，因为您可以使用与数据库支持的模型完全相同的方法。

## 虚拟属性

如果您发现在将数据传递给模型之前，您正在操纵数据(例如，转换对象的类型)，那么很可能是时候开始构建您的代码，以利用**虚拟属性**。

虚拟属性是一个非常简单的想法——本质上，您所做的就是定义自己的 getter 和 setter 方法。

假设您使用以下代码来设置用户名:

```
@user = User.new(params[:user])
@user.first_name, @user.last_name = params[:user][:full_name].split(" ", 2)
```

您可以删除第二行，而是将以下内容添加到您的用户模型中:

```
def full_name=(value)
  self.first_name, self.last_name = value.to_s.split(" ", 2)
end
```

每当您设置`full_name`属性时，您的模型现在也会自动为您设置`first_name`和`last_name`属性，即使数据库中不存在`full_name`。同样，您通常会想要定义一个 getter 方法，`full_name`，它返回`"#{first_name} #{last_name}"`。

使用虚拟属性，您可以相对轻松地在表单中使用数据的替代表示。孤立地测试逻辑也简单得多，这总是一件好事。

## 使用翻译

从 Rails 2.2 开始，框架本身已经提供了对国际化的强大支持(或者说 T2 i18n T3)。您需要做的就是维护一个翻译的 YAML 文件，并在代码中向用户显示数据的地方使用`I18n.t` / `t`。本质上，Rails i18n 框架使得声明抽象上下文到字符串的映射变得很容易。

从开发者的角度来看，使用 I18n 不仅仅是将你的应用推广到更多的地区。当需要重命名接口中的某个内容时，在一个地方查找有问题的字符串，以便在整个应用程序中替换它，这比在代码中搜索每一个出现的内容要快得多。

如果你想开始使用 Rails 的 I18n 框架，社区免费提供了一个非常全面的指南。

## 最后

实际上有数百种编码实践或技术可以让 Ruby on Rails 开发人员的生活变得更加轻松，但是我试图挑选出十种广泛适用于几乎所有项目，并且经常被忽略的实践或技术。你已经遵循这些惯例了吗？你会开始吗？你还有什么要补充的吗？请在评论中告诉我。

## 分享这篇文章