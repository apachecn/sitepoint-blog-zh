# Rails:动态链接范围以清理 SQL 查询

> 原文：<https://www.sitepoint.com/dynamically-chain-scopes-to-clean-up-large-sql-queries/>

![Text SQL in white hexagon, repeated on orange background](img/a1d77be1c02b5fa48e26e847fc996f4a.png)

对于 Rails 应用程序来说，最糟糕的事情之一就是 SQL 查询变成了一堆复杂的条件。我遇到过使用一种“条件链”方法构建查询字符串的控制器操作，如下所示:

```
sql = "active= 1"
if condition
  sql += "and important=1"
end
 if second_condition
  sql += "and important=1"
end
Article.where(sql) 
```

随着应用程序变得越来越复杂，当您开始使用 SQL 查询时，检索所需的信息会变得很麻烦。当您想要执行不同的报告查询时，这很快就变得很麻烦。随着团队越来越大，越来越多的员工需要更多地访问应用程序数据。随着所需数据变得越来越复杂，您很快就会得到一个非常复杂的控制器动作和一个带有特殊方法的模型。

在本教程中，我希望演示如何用 Ruby `send`方法链接作用域来构建一个更小的控制器。作为目标的一部分，我希望保持示波器的便利性。

如果你想知道什么是作用域，或者甚至思考 Ruby `send`方法到底是什么，那么不要害怕。它们很简单。

活动记录范围是您在模型内部创建的过程，就像方法调用一样使用:

```
class Article < ActiveRecord::Base
  enum status: [ :draft, :pending_review,:flagged, :published]

  scope :drafts, -> { (where("`status` = ? ", 0)) } # 0 is :draft in the enum
end

drafts = Article.drafts 
```

Ruby `send`方法是向 Ruby 对象发送消息的一种方式。您可以将`send`视为调用对象方法的另一种方式:

```
drafts = Article.send("drafts") 
```

如果说不通，也不用担心。我将再次谈到这一点，这样你会有更好的想法。让我们构建一个微型应用程序来进一步了解这两个概念。

首先，我们将构建一个简单的博客应用程序:

```
$ rails new blog 
```

切换到该目录，生成一个包含许多属性的快速博客框架:

```
$ rails generate scaffold Article title:string description:text status:integer author:string website:string meta_title:string meta_description:text 
```

迁移数据库:

```
$ rake db:migrate 
```

有了我们最基本的支架，我们只需要用数据填充它。为此，我强烈推荐 [Faker 宝石](https://github.com/stympy/faker)。在您的**gem 文件**中，添加以下内容:

```
gem 'faker' 
```

去抢宝石吧。

现在，在您的 **db/seeds.rb** 文件中，添加以下内容以生成数据集:

```
10.times do
  Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 0)
  Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 1)
  Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 2)
  Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 3)
end

10.times do
 Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 0,:website => Faker::Internet.domain_name)
 Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 1,:author => Faker::Name.first_name)
 Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 2,:meta_title => Faker::Lorem.word)
 Article.create(:title => Faker::Lorem.word, :description => Faker::Lorem.sentence, :status => 3,:meta_description => Faker::Lorem.sentence)
end 
```

在终端中，我们需要输入以下内容来填充我们的开发数据库:

```
$ rake db:seed 
```

启动服务器(`rails s`)，访问`/articles`页面，你会看到许多名字完全随机的不同文章。有了一些数据，我们现在可以开始利用作用域从应用程序中检索相关数据。在我们的 **app/models/article.rb** 文件中，添加一个`enum`和一些范围，像这样:

```
 class Article < ActiveRecord::Base
   enum status: [ :draft, :pending_review,:flagged, :published]

   scope :with_author, -> {(where("`author` IS NOT NULL ")) }
   scope :with_website, -> {(where("`website` IS NOT NULL ")) }
   scope :with_meta_title, -> {(where("`meta_title` IS NOT NULL ")) }
   scope :with_meta_description, -> {(where("`meta_description` IS NOT NULL")) }
end 
```

在我们构建报告应用程序面向用户的部分之前，我总是希望确保从 Rails 控制台开始工作。回到终端，启动一个 Rails 控制台，并测试一个新的范围:

```
$ rails c
> Article.draft
=> #<ActiveRecord::Relation [#<Article id: 1, title: "labore", description: "Tempora debitis nihil illum vel vero suscipit cupi...", status: "draft", created_at: "2016-09-04 12:15:39", updated_at: "2016-09-04 12:15:39", author: nil, website: nil, meta_title: nil, meta_description: nil>] 
```

作用域的一个优点是我们可以将它们链接在一起，构建一个更大的 SQL 语句:

```
> Article.published.with_meta_title
=> #<ActiveRecord::Relation [#<Article id: 1, title: "labore", description: "Tempora debitis nihil illum vel vero suscipit cupi...", status: "draft", created_at: "2016-09-04 12:15:39", updated_at: "2016-09-04 12:15:39", author: nil, website: nil, meta_title: "A meta Title to remember", meta_description: nil>] 
```

> > Note: The `status` enumeration adds a scope for each value of the enumeration, which is where we get `Article.published`.

希望，你开始看到范围的力量。由于作用域与方法完全一样，我们可以利用极其强大的 Ruby `send`方法，该方法允许我们将方法名作为参数字符串传入:

```
> Article.send("draft")
=> #<ActiveRecord::Relation [#<Article id: 1, title: "labore", description: "Tempora debitis nihil illum vel vero suscipit cupi...", status: "draft", created_at: "2016-09-04 12:15:39", updated_at: "2016-09-04 12:15:39", author: nil, website: nil, meta_title: nil, meta_description: nil>] 
```

然而，使用`send`方法将方法链接在一起有点困难。我们需要为我们的模型创建另一个方法，以便能够动态地链接方法。在 **app/models/article.rb** 中，添加以下内容:

```
def self.send_chain(methods)
  methods.inject(self, :send)
end 
```

这个方法接受一个方法数组，并对每个方法调用`send`。` send_chain 允许我们动态地调用任意多的作用域。例如:

```
> Article.send_chain(["with_author", "pending_review"])
=> #<ActiveRecord::Relation [#<Article id: 82, title: "quia", description: "Adipisci nisi tempora culpa atque vel quo.", status: "pending_review", created_at: "2016-09-04 12:16:37", updated_at: "2016-09-04 12:16:37",. .  .] 
```

现在请允许我演示我们如何在视图和控制器中使用它。在**app/views/articles/index . html . erb**的顶部，输入以下内容:

```
<%= form_tag("/articles", method: "get") do %>
  With Author<%= check_box_tag "article[methods][]", "with_author"  %>
  Pending Review<%= check_box_tag "article[methods][]", "pending_review"  %>
  Draft<%= check_box_tag "article[methods][]", "draft"  %>
  Flagged<%= check_box_tag "article[methods][]", "flagged"  %>
  Published<%= check_box_tag "article[methods][]", "published"  %>
  With Website<%= check_box_tag "article[methods][]", "with_website"  %>
  With Meta Title<%= check_box_tag "article[methods][]", "with_meta_title"  %>
  With Meta Description<%= check_box_tag "article[methods][]", "with_meta_description"  %>
 <%= submit_tag("Search") %>
<% end %> 
```

现在是真正的魔术。在**app/controllers/articles _ controller . Rb**中，更改`index`动作以匹配我这里的:

```
def index
  if params[:article]
    methods = params[:article][:methods]
    @articles = Article.send_chain(methods)
  else
    @articles = Article.all
  end
end 
```

该操作将通过勾选方框来构建一个巨大的 SQL 查询。如果您勾选了所有的框，您将在您的 Rails 开发日志中看到以下内容:

```
Processing by ArticlesController#index as HTML
 Parameters: {"utf8"=>"✓", "article"=>{"methods"=>["with_author", "pending_review", "draft", "flagged", "published", "with_website", "with_meta_title", "with_meta_description"]}, "commit"=>"Search"}
 Rendering articles/index.html.erb within layouts/application
 Article Load (1.0ms)  SELECT "articles".* FROM "articles" WHERE (`author` IS NOT NULL ) AND (`status` = 1 ) AND (`status` = 0 ) AND (`status` = 2 ) AND (`status` = 3 ) AND (`website` IS NOT NULL ) AND (`meta_title` IS NOT NULL ) AND (`meta_description` IS NOT NULL)
Rendered articles/index.html.erb within layouts/application (60.8ms) 
```

## 结论

我在野外遇到的许多 Rails 应用程序都使用了巨大的控制器动作，试图决定返回什么。我甚至看到人们构建字符串传递给 Rails `where`参数的模式:

```
sql = "active =1"
if condition
  sql += "and important=1"
end
... tons of other conditions ...

Article.where(sql) 
```

对于范围，我们只是将方法链接在一起，以动态创建 SQL 查询:

```
scope :active, -> {(where("`active` = 1")) }
scope :important, -> {(where("`important` = 1")) } 
```

然后，我们可以使用这些范围以清晰、直观的方式构建相同的 SQL 查询:

```
if condition
  query = Article.active.important
end 
```

在我们的示例博客应用程序中，将 Ruby `send`方法与 Rails 作用域相结合减少了控制器代码的大小，这反过来使得重构更加容易。这是因为业务逻辑被下推到模型中，并且控制器被用来决定呈现什么信息。通常，使用 Ruby `send`方法，我们只能向对象发送一条消息。添加一种将方法链接在一起的方式(`send_chain`)允许我们动态地将范围方法链接在一起，使我们的控制器更加精简:

```
$ methods = ["with_author", "pending_review", "draft", "flagged", "published", "with_website", "with_meta_title", "with_meta_description"]
$ Article.send_chain(methods) 
```

希望我已经很好地将作用域和 Ruby `send`方法结合起来，缩短了控制器动作的长度。如果你有任何反馈，我很乐意在下面的评论中听到。

## 分享这篇文章