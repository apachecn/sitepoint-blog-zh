# N+1 问题的银弹

> 原文：<https://www.sitepoint.com/silver-bullet-n1-problem/>

在构建最小可行产品(MVP)时，Rails 是最受欢迎的选择之一。您可以如此轻松地引导、原型化和发布应用程序，以至于 Rails 是许多开发人员对 MVP 的默认选择。

通常在开发这些原型时，大多数开发人员不会考虑性能指标，无论如何，这在当时真的不应该是一个问题。但是当应用程序需要扩展和优化时，这些性能指标就会发挥作用。那么同样的开发人员需要关注如何重构应用程序以提高速度和性能。

一个潜在的改进领域是应用程序发送到数据库的查询。减少查询次数，提高应用程序的性能。

大多数 Rails 应用程序的数据分布在许多模型上，这些模型之间有关联，使用 ORM 进行访问。ORMs 可以帮助您解决关系数据库和面向对象模型之间的阻抗不匹配问题，有望使您的生活更加简单。但是不了解它们的一些缺陷会大大降低应用程序的性能。一个这样的陷阱是选择 N+1 问题。

## N+1 查询问题是什么？

当代码需要加载父子关系的子节点(一对多中的“多”)时，就会出现这个问题。默认情况下，大多数 ORM 都启用了延迟加载，所以对父记录发出查询，然后对每个子记录发出一个查询。如您所料，执行 N+1 个查询而不是单个查询会使您的数据库充满查询，这是我们能够也应该避免的。

考虑一个简单的博客应用程序，其中有许多由不同作者发表的文章:

```
#Articles model
class Article < ActiveRecord::Base
  belongs_to :author
end

#Authors model
class Author < ActiveRecord::Base
  has_many :posts
end
```

我们想在文章的边栏上列出最近的 5 篇文章，以及它们的标题和作者的名字。

这可以通过以下方式实现

```
#In our controller
@recent_articles = Article.order(published_at: :desc).limit(5)

#in our view file
@recent_articles.each do |article|
    Title: <%= article.title %> 
    Author:<%= article.author.name %>
end
```

上面的代码将向数据库发送 6 (5+1)个查询，1 个查询获取最近的 5 篇文章，然后 5 个查询对应的作者。在上面的例子中，由于我们将查询数量限制为 5，我们不会看到这个问题对我们的应用程序性能有太大影响。但是，对于具有较大限制的查询，这可能是致命的。

每个查询都有相当大的开销。发出 1 个返回 100 个结果的查询比发出 100 个每个返回 1 个结果的查询要快得多。如果您的数据库在网络上相距 1-2 毫秒的另一台机器上，情况尤其如此。在这里，连续发出 100 个查询的最低成本为 100-200 毫秒，即使 MySQL 可以立即满足它们。

## 解答-急切加载

急切加载是使用尽可能少的查询来加载由`Model.find`返回的对象的相关记录的机制。在上面的例子中，如果我们使用急切加载来获取数据，作者的详细信息将随文章一起加载。

在 Rails 中，ActiveRecord 有一个名为`includes`的方法，它确保所有指定的关联数据都以最少的查询次数加载。

让我们将代码重构为急切的加载关联。

```
#In our controller
#Using includes(:authors) will include authors model.
@recent_articles = Article.order(published_at: :desc).includes(:authors).limit(5)

#in our view file
@recent_articles.each do |article|
    Title: <%= article.title %> 
    Author:<%= article.author.name %>
end
```

## 急切加载如何防止 N+1 查询问题？

急切加载是 N+1 查询问题的解决方案，确保在遍历对象时不会运行不必要的查询。

我们示例中的查询来自

```
Article Load (0.9ms) SELECT 'articles'.* FROM 'articles'
Author Load (0.4ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' = ? ORDER BY 'authors'.'id' ASC LIMIT 1 [["id", 1]]
Author Load (0.3ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' = ? ORDER BY 'authors'.'id' ASC LIMIT 1 [["id", 2]]
Author Load (0.4ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' = ? ORDER BY 'authors'.'id' ASC LIMIT 1 [["id", 3]]
Author Load (0.3ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' = ? ORDER BY 'authors'.'id' ASC LIMIT 1 [["id", 4]]    
Author Load (0.4ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' = ? ORDER BY 'authors'.'id' ASC LIMIT 1 [["id", 5]]
```

到

```
Article Load (0.4ms) SELECT 'articles'.* FROM 'articles'
Author Load (0.4ms) SELECT 'authors'.* FROM 'authors' WHERE 'authors'.'id' IN (1,2,3,4,5)
```

少问有益。:)

## 子弹宝石

Bullet 是 Richard Huang 编写的一个程序，它有助于减少应用程序的查询次数。它在 2009 年作为一个插件首次发布，它仍然是一个非常有用的工具来监控你的应用程序的性能改进。Bullet 帮助您监视应用程序对数据库进行的查询，并通知您任何 N+1 情况。有趣的是，它还会通知您任何未使用的急切加载。

Bullet 有很多方法可以通知你 N+1 的查询问题:Growl 通知，默认的 JavaScript 警告，甚至使用 XMPP。此外，它将导致警报的确切行和堆栈跟踪保存到 *bullet.log* 。如果您愿意，它还可以写入应用程序日志。

## 使用和配置

将 Bullet 添加到您的 gem 文件并运行`bundle install`。

```
group :development do
  gem 'bullet'
end
```

gem 应该只在开发环境中使用，因为您不希望应用程序的用户收到关于 N+1 查询问题的警告。

接下来要做的是配置 Bullet 通知您的方式。

### 一般配置

应该在应用程序中启用 Bullet，只添加 bullet gem 不会通知您错误的查询。配置在*config/environments/development . Rb*中完成。

```
config.after_initialize do
  #Enable bullet in your application
  Bullet.enable = true
end
```

#### 通过 Javascript 警报通知

可以将 Bullet 配置为使用简单的 javascript 警告来通知开发人员。加载运行 N+1 查询的页面时会弹出一个警告框。要配置 javascript 警报，请将以下代码添加到上面的配置块中

```
Bullet.alert = true
```

#### 通过浏览器控制台通知

如果您不喜欢在应用程序中到处弹出警告框，您可以使用浏览器控制台通知您 N+1 个查询，方法是添加

```
Bullet.console = true
```

#### 通过 Rails 日志通知

Bullet 还可以将关于错误查询的通知附加到 Rails 日志中。因此，如果您正在为您的日志使用一些分析器工具，您可以添加以下内容

```
Bullet.rails_logger = true
```

#### 记录到文件中

如果您需要将查询记录到文件中，Bullet 允许您通过以下方式来完成

```
Bullet.bullet_logger = true
```

这将创建一个名为 *bullet.log* 的日志文件，其中包含您所有的错误查询。

#### 通过咆哮通知进行通知

如果你喜欢咆哮通知，你可以使用

```
Bullet.growl = true
```

### 结论

这就结束了我对 N+1 查询问题和子弹宝石的旋风之旅。如果你的 Rails 应用中有关联，你应该从今天开始使用这些技巧。

## 资源

Rubygems : [子弹宝石](http://rubygems.org/gems/bullet)
Github : [源代码](http://github.com/flyerhzm/bullet)
RailsCast : [教程](http://railscasts.com/episodes/372-bullet)

## 分享这篇文章