# Ruby on Rails:代码一览

> 原文：<https://www.sitepoint.com/ruby-on-rails-a-look-at-the-code/>

Ruby on Rails 的名声建立在你只需编写很少的代码就能完成常见的 Web 开发任务的基础上。但是你*要*写的代码呢？自从[昨天发布](https://www.sitepoint.com/ruby-on-rails-10-is-out/)宣布 Ruby on Rails 1.0 以来，很多人都附和着问，当你不再大肆宣传的时候是什么感觉。

以下内容转载自《科技时报》第 128 期。

Ruby on Rails 是敏捷开发框架原则的典型代表:约定胜于配置。这在实践中意味着，不必为您承担的每个项目编写一堆配置文件和标准样板代码，您可以简单地根据一组假设进行编码，所有繁重的工作都会为您完成。

因此，第一次检查 Ruby on Rails 代码的体验可能会令人困惑。"变量`@posts`来自哪里，它是如何填充数据库记录的？"你可能会想。因为您有一个名为 posts 的数据库表，Rails 会帮您把它连接起来。

除了极少数例外，您唯一需要用 Rails 编写代码的时候就是您的需求与最常见的情况不同的时候。

考虑到这一点，您编写的第一个代码是一个名为`database.yml`的小文件，它告诉 Rails 如何连接到您的数据库，以便它可以设置所有这些智能默认设置。您可以为开发、测试和生产设置单独的数据库:

```
development:
  adapter:  mysql
  database: jokes_development
  host:     localhost
  username: username
  password: password

test:
  ...

production:
  ...
```

有了这个文件，您可以运行一个命令来生成一个 Rails scaffold，它是一组自动生成的文件，允许您使用一组 Web 页面来显示、创建、更新和删除数据库记录。然后，应用程序开发就变成了一个缓慢修改和替换自动生成的代码以获得您想要的特定功能的过程。

Rails 在其生成的代码中遵循模型-视图-控制器(MVC)模式。因此，Rails 应用程序由基于数据库记录的对象(模型**)、显示和编辑这些对象的网页(T2 视图)以及一组将两者联系在一起的动作(T4 控制器)组成。**

 **编辑模型文件可以让您控制如何从数据库中提取记录。假设您希望能够只显示已标记为要发布的笑话。您需要修改定义数据库中笑话模型的`joke.rb`文件:

```
def self.published_jokes
  find(:all, :conditions => "published = 1")
end
```

然后，我们可以修改笑话列表控制器(`jokelist_controller.rb`)中的默认动作(称为`index`)来显示已发布笑话的列表，而不是所有笑话(默认):

```
def index
  @jokes = Joke.published_jokes
end
```

我们还可以修改我们的模型(`joke.rb`)来为通过管理界面提交的新创建或更新的笑话设置一些要求(例如，笑话必须包含文本，并且该文本必须是唯一的):

```
class Joke < ActiveRecord::Base
  validates_presence_of :joketext
  validates_uniqueness_of :joketext
  def self.published_jokes
    find(:all, :conditions => "published = 1")
  end
end
```

像`validates_presence_of`和`validates_uniqueness_of`这样听起来很“响亮”的名字在 Ruby 中很常见。在大多数其他语言的简洁性之后，很容易被这些所迷惑，但是当你意识到在 Ruby 中，你必须记住复杂的 XML 配置文件语法(在 Java 中很常见)来换取这些非常易读的名称时，你会很快适应。

一旦您获得了正确的模型逻辑，您将需要调整向用户呈现模型的 Web 页面(视图)。在 Rails 中，通常使用一种叫做 Embedded Ruby (ERb)的简单模板语言来构建页面。ERb 允许您创建包含内联 Ruby 代码的`.rhtml`文件，但是因为您的应用程序的大部分逻辑将在模型和控制器中，所以您的`.rhtml`文件中的代码通常仅限于输出动态值的简短片段，或者遍历记录集，为每个记录输出一段 HTML。

如果在我们的笑话列表模板(`index.rhtml`)中，我们只想显示每个笑话的前 20 个字符，我们可以修改模板代码，如下所示:

```
<% for joke in @jokes %>
  <div class="joke">
    <p>
      <%= h(truncate(joke.joketext, 20)) %>
      <%= link_to 'Read this joke', {:action => 'show_joke', :id => joke} %>
    </p>
    <p class="author>
      by <%= h(joke.author.full_name) %></p>
  </div>
<% end %>
```

首先，这段代码循环遍历我们在上面的控制器中设置的集合中的所有笑话。对于每个笑话，它输出前 20 个字符(使用内置的`truncate` 方法)，转义任何 HTML 特殊字符(使用内置的`h`方法)。然后它输出一个文本为“阅读这个笑话”的超链接，该超链接将链接到另一个名为`show_joke`的 Rails 动作，并在查询字符串中传递当前笑话的 ID。内置的`link_to`方法使得创建这样的链接变得容易。

最后，我们输出笑话作者的名字。为此，我们需要为存储在数据库中的作者建立另一个 Rails 模型。让 Rails 了解这两种模型之间的关系很简单:

```
class Joke < ActiveRecord::Base
  belongs_to :author
  ...

class Author < ActiveRecord::Base
  has_many :jokes
  ...
```

从其他语言来看，很难相信这就是在基于数据库表的两个类之间建立关系所需的全部代码，但是只要您坚持使用 Rails 所基于的命名约定(数据库中的`jokes`表必须有一个`author_id`字段，其中包含来自`authors`表中的`id`字段的值),这一切都可以正常工作！

这是用 Ruby on Rails 编写代码的一个非常简短的概述。有一些很棒的教程(查看我的[前一篇博文](https://www.sitepoint.com/ruby-on-rails-10-is-out/)的[评论](https://www.sitepoint.com/ruby-on-rails-10-is-out/#comments))将帮助你创建一个完整的应用程序，但简而言之，一旦你掌握了 Rails 实现模型-视图-控制器应用程序结构的方式，学习 Rails 就只是学习命名约定和所有可以帮助你的内置方法。** 

## **分享这篇文章**