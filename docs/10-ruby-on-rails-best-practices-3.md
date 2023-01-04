# 10 个 Ruby on Rails 最佳实践

> 原文：<https://www.sitepoint.com/10-ruby-on-rails-best-practices-3/>

![](img/a1b5c4c665bfffc27ac31a7a48eeb42a.png)

Ruby on Rails 是一个 web 应用框架，我们都知道。Rails 让我们更有效率，让我们专注于手头的任务而不是技术。但是在 Rails 中坚持最佳实践是非常重要的，尤其是在开始的时候。在这篇文章中，我们将看看 Ruby on Rails 的一些最佳实践。

## 毁灭之路

如果您忽略了 web 应用程序框架的最佳实践，那么您就冒着不知不觉中脱离框架的风险。在最坏的情况下，您的应用程序开始成为您处理的噩梦，使得开发新功能、维护项目或引入新开发人员变得困难。相信我，坚持最佳实践来保持有效和高效，而不是让人们(例如，你的团队)拔他们的头发。

## 通往荣耀的道路

名字说明了一切:**最佳**实践。它们是最好的，也是应用最广泛的，这是有原因的。以下是一些好处:

1.  可维护性
2.  可读性
3.  高雅
4.  更快的发展
5.  干代码

让我们开始吧。

## 遵循 Ruby on Rails 社区风格指南:

在每一种编程语言中，我们都见过丑陋和美丽的代码。代码风格因人而异，这会导致新开发人员的延迟。拥有一个社区驱动的风格指南是非常重要的，因为它在整个代码库中执行一致的风格方面起着至关重要的作用。项目通常由小型到大型团队组成，团队成员来自不同的编程背景和风格。遵循 Ruby 社区风格指南是我的最佳实践，以下是我非常想强调的一些风格偏好:

### 两个空格缩进

这是 Ruby 社区中被广泛采用和认可的风格指南之一。使用 **2** 空格缩进代替 4 空格缩进。让我们看一个例子:

**4 空格缩进**

```
def some_method
    some_var = true
    if some_var
        do_something
    else
        do_something_else
    end
end 
```

**2 个空格缩进**

```
def some_method
  some_var = true
  if some_var
    do_something
  else
    do_something_else
  end
end 
```

后者更干净，可读性更好。此外，这在具有更多缩进层次的较大文件中会更加明显。

### 用`?`定义谓词方法

在 Ruby 中，我们对返回`true`或`false`的方法有一个约定。这些方法也被称为*谓词*方法，约定是在名称后面加上一个问号(`?`)。在大多数编程语言中，你会看到像`is_valid`或`is_paid`等定义的方法或变量名。Ruby 不鼓励这种风格，并鼓励他们使用更人性化的语言方式，如`object.valid?`或`fee.paid?`(注意没有`is_`前缀)，以保持 Ruby 的地道性和可读性。

### 迭代:用`each`代替`for`

几乎所有的 Ruby 程序员在遍历集合时都使用`each`而不是`for`。它只是可读性更强。

** for **

```
for i in 1..100
  ...
end
```

**每个**

```
(1..100).each do |i|
  ...
end
```

看到了吗？

### 条件句:用`unless`代替`!if`:

如果你发现自己在使用带有否定条件的`if`陈述，即:

```
if !true
  do_this
end 
```

或者

```
if name != "sarmad"
  do_that
end 
```

那你应该用 Ruby 的专属`unless`。像这样:

```
unless true
  do_this
end 
```

或者

```
unless name == "sarmad"
  do_that
end
```

同样，这是关于可读性。然而，如果你需要在你的条件中包含一个`else`，千万不要使用`unless-else`。

**坏**

```
unless user.save
  #throw error
else
  #return success
end
```

**好的**

```
if user.save
  #return success
else
  #throw error
end
```

### 短路**

短路是用于在特定条件下提前退出方法的术语。考虑这个例子:

```
if user.gender == "male" && user.age > 17
  do_something
elsif user.gender == "male" && user.age < 17 && user.age > 5
  do_something_else
elsif user.age < 5
  raise StandardError
end 
```

在这种情况下，它需要检查所有条件，以确定用户是否小于 5 岁，并引发一个异常。首选方式是:

```
raise StandardError if user.age < 5
if user.gender == "male" && user.age > 17
  do_something
elsif user.gender == "male" && user.age < 17 #we saved a redundant check here
  do_something_else
end 
```

在满足一定条件的情况下，早点返回比较有效。

提示:我强烈推荐你仔细看看这里的风格指南(Ruby)和这里的[(Rails)](https://github.com/bbatsov/rails-style-guide)。

### 编写测试

如果您熟悉 Rails，那么您就会知道 Rails 社区对测试的重视程度。我听人说过，作为一个新手，测试让学习 Rails 变得很难。另外，有些人说，先了解 Rails 的基础知识(或者在某些情况下，一般的 web 开发)是有意义的。但是这并没有阻止测试成为软件开发中绝对的最佳实践。事实上，我已经看到有人抱怨说，完成一个特性所花的时间比测试所需要的时间要多。但是一旦他们跳上了测试轨道，忍受了首先编写测试的“麻烦”,他们实际上立刻就开始构建特性了。此外，它还涵盖了如此多的边缘情况，以至于我们的对象有了更好的设计。一个优秀的 Ruby 开发人员天生擅长测试。

让我们列举一些测试的好处:

*   测试作为一个特性或应用程序的详细规范。
*   测试充当其他开发人员的文档，帮助他们理解您在实现中的意图。
*   测试有助于提前捕捉和修复错误。
*   在重构代码或进行性能增强时，测试会让您有信心确保不会出现任何问题。

### 干(不重复)

尽一切努力确保自己不会重复，尽可能避免重复。让我们讨论 Ruby 的面向对象原则有助于避免重复的几种方式。

**使用抽象类**:假设你有这两个类:

```
class Mercedes
  def accelerate
    "60MPH in 5 seconds"
  end

  def apply_brakes
    "stopped in 4 seconds"
  end

  def open_boot
    "opened"
  end

  def turn_headlights_on
    "turned on"
  end

  def turn_headlights_off
    "turned off"
  end
end

class Audi
  def accelerate
    "60MPH in 6.5 seconds"
  end

  def apply_brakes
    "stopped in 3.5 seconds"
  end

  def open_boot
    "opened"
  end

  def turn_headlights_on
    "turned on"
  end

  def turn_headlights_off
    "turned off"
  end
end 
```

我们有三个重复的方法`open_boot`、`turn_headlights_on`和`turn_headlights_off`。在这篇文章中，我们不会讨论为什么我们不应该有代码重复，你可以在这里读到这个[。目前来看，只是违背了干原则。这里的最佳实践是使用类继承和/或抽象类。让我们重写我们的类来解决这个问题。](https://en.wikipedia.org/wiki/Duplicate_code#Problems_associated_with_duplicate_code)

```
class Car
  # Uncomment the line below if you want this class to be uninstantiable
  # i.e you can't make an instance of this class.
  # You can only inherit other classes from it.
  # self.abstract = true

  def open_boot
    "opened"
  end

  def turn_headlights_on
    "turned on"
  end

  def turn_headlights_off
    "turned off"
  end
end

class Mercedes < Car
  def accelerate
    "60MPH in 5 seconds"
  end

  def apply_brakes
    "stopped in 4 seconds"
  end
end

class Audi < Car
  def accelerate
    "60MPH in 6.5 seconds"
  end

  def apply_brakes
    "stopped in 3.5 seconds"
  end
end 
```

明白了吗？好多了！

另一方面，使用模块
模块是一种跨类共享行为的灵活方式。使用模块(组合)而不是继承的原因超出了本文的范围。只要说模块是“has-a ”,继承是类和行为之间的“is-a”关系就够了:

```
class Newspaper
  def headline
    #code
  end

  def sports_news
    #code
  end

  def world_news
    #code
  end

  def price
    #code
  end
end

class Book
  def title
    #code
  end

  def read_page(page_number)
    #code
  end

  def price
    #code
  end

  def total_pages
    #code
  end
end 
```

假设我们需要向两个类都添加一个`print`方法，而不需要复制代码。使用一个模块，像这样:

```
module Printable
  def print
    #code
  end
end 
```

修改这些类，使它们包含此模块:

```
class Newspaper

  #This wil add the module's methods as instance methods to this class
  include Printable

  def headline
    #code
  end

  def sports_news
    #code
  end

  def world_news
    #code
  end

  def price
    #code
  end
end

class Book

  #This wil add the module's methods as instance methods to this class
  include Printable

  def title
    #code
  end

  def read_page(page_number)
    #code
  end

  def price
    #code
  end

  def total_pages
    #code
  end
end 
```

这是一个非常强大和有用的技术。我们还可以通过使用`extend Printable`而不是`include Printable`来添加模块的方法作为类方法。

### 枚举的巧妙使用

假设您有一个名为`Book`的模型和一个列/字段，您想在其中存储它是草稿、已完成还是已发布的状态。你发现自己在做这样的事情:

```
if book.status == "draft"
  do_something
elsif book.status == "completed"
  do_something
elsif book.status == "published"
  do_something
end 
```

或者

```
if book.status == 0 #draft
  do_something
elsif book.status == 1 #completed
  do_something
elsif book.status == 2 #published
  do_something
end 
```

你应该看看 Enums！将`status`列定义为整数，最好不为空(`null: false`)，以及您希望您的模型在创建后具有的状态的默认值，即`default: 0`。现在，在模型中定义枚举，如下所示:

```
enum status: { draft: 0, completed: 1, published: 2 } 
```

现在，您可以将代码重写为:

```
if book.draft?
  do_something
elsif book.completed?
  do_something
elsif book.published?
  do_something
end 
```

看起来很棒，不是吗？它不仅为您提供了这些带有名称的谓词方法，还为您提供了在定义的状态之间切换的方法。

*   `book.draft!`
*   `book.completed!`
*   `book.published!`

这些方法将切换与方法匹配的状态。你的武器库中有一个多么优雅的工具。

### 胖模特，瘦控制器和关注

另一个最佳实践是将与响应无关的逻辑排除在控制器之外。您不希望在控制器中出现的代码示例包括任何业务逻辑或持久性/模型改变逻辑。例如，有人可能让他们的控制器像这样:

```
class BooksController < ApplicationController
  before_action :set_book, only: [:show, :edit, :update, :destroy, :publish]

  # code omitted for brevity

  def publish
    @book.published = true
    pub_date = params[:publish_date]
    if pub_date
      @book.published_at = pub_date
    else
      @book.published_at = Time.zone.now
    end

    if @book.save
      # success response, some redirect with a flash notice
    else
      # failure response, some redirect with a flash alert
    end
  end

  # code omitted for brevity

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_book
      @book = Book.find(params[:id])
    end
  # code omitted for brevity
end 
```

让我们将这个复杂的逻辑移到相关的模型中:

```
class Book < ActiveRecord::Base

  def publish(publish_date)
    self.published = true
    if publish_date
      self.published_at = publish_date
    else
      self.published_at = Time.zone.now
    end
    save
  end
end

class BooksController < ApplicationController
  before_action :set_book, only: [:show, :edit, :update, :destroy, :publish]

  # code omitted for brevity

  def publish
    pub_date = params[:publish_date]
    if @book.publish(pub_date)
      # success response, some redirect with a flash notice
    else
      # failure response, some redirect with a flash alert
    end
  end

  # code omitted for brevity

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_book
      @book = Book.find(params[:id])
    end
  # code omitted for brevity
end 
```

这是一个简单的例子，很明显，这个功能属于模型。在很多其他情况下，你必须聪明地找到正确的平衡，知道什么应该去哪里。有时，从控制器中提取的逻辑并不适合任何模型的上下文。你必须找出它最适合的地方。我会试着根据我的经验给你制定一些简单的规则。如果你认为有些问题有更好的解决方法，请在评论中告诉我。

*   控制器应该只对模型进行简单的查询。复杂的查询应该转移到模型中，并分解到[可重用范围](http://guides.rubyonrails.org/active_record_querying.html#scopes)中。控制器应该主要包含请求处理和响应相关的逻辑。

*   任何与请求和响应不相关而与模型直接相关的代码都应该移出到模型中。

*   任何表示数据结构的类都应该作为非活动记录模型(无表类)进入 *app/models* 目录。

*   当逻辑属于特定领域(印刷、图书馆等)时，使用 PORO(普通旧 Ruby 对象)Ruby 类。)并且并不真正适合模型的上下文(ActiveRecord 或非 ActiveRecord)。你可以把那些类放在*app/models/some _ directory/*里。 *app/* 目录中的任何内容都会在应用启动时自动加载，因为它包含在 Rails 自动加载路径中。POROs 也可以放在*app/models/concerns*&*app/controllers/concerns*目录下。

*   如果您的 PORO、模块或类与应用程序无关，并且可以与其他应用程序一起使用，请将它们放在 *lib/* 目录中。

*   如果你必须从不相关的功能中提取共同的功能，就使用模块。你可以把它们放在 *app/* *目录和 *lib/* 目录，如果它们是应用无关的。

*   当应用程序代码不断增长，并且很难决定将特定的逻辑放在哪里时，“服务”层是支持普通 MVC 的另一个非常重要的地方。想象一下，当一本书出版时，您需要一种机制来向一些订阅者发送短信或电子邮件通知，或者向他们的设备发送推送通知。您可以在 *app/services/* 中创建通知服务，并开始服务化您的功能。

### 国际化/本地化

从一开始就将你的应用国际化。不要把它留到以后，否则它只会越来越困扰你。好的网站不能依赖单一语言，他们通常有更大的语言目标。越大越好。随着发展而国际化是最佳实践之一。这就是为什么 Rails 附带了 **I18n** gem，这表明了应用程序国际化的重要性。你可以在这里了解更多关于[的信息。](http://guides.rubyonrails.org/i18n.html)

它为您提供了开箱即用的功能:

*   对英语和类似语言的现成支持
*   使得为其他语言定制和扩展一切变得容易

它允许您设置默认的区域设置，并根据用户的位置或偏好更改区域设置。

下面是一个将非国际化 *HTML* 代码转换为国际化代码的简短示例:

```
<h1>Books Listing</h1>
<table>
  <thead>
    <th>Name</th>
    <th>Author</th>
  </thead>
  <tbody>
    <td> Some book </td>
    <td> Some author </td>
  </tbody>
</table 
```

**config/locales** 目录中的文件用于国际化，由 Rails 自动加载。默认情况下，每个 Rails 应用都有一个**配置/locales/en.yml** 。这个文件负责保存*的英文*翻译。如果您需要添加更多语言的翻译，您可以使用*添加与语言环境名称匹配的文件。yml* 扩展。在这个例子中，我们将坚持使用 *en.yml* 。让我们重构上面的 *HTML* 来使用国际化:

```
<h1><%= t('.title') %></h1>
<table>
  <thead>
    <th><%= t('.name') %></th>
    <th><%= t('.author') %></th>
  </thead>
  <tbody>
    <td> Some book </td>
    <td> Some author </td>
  </tbody>
</table> 
```

现在将要显示的内容放在*中。yml* 文件，以便更新后的 *HTML* 可以提取翻译。

```
# config/en.yml
en:
  title: "Books Listing"
  name: "Name"
  author: "Author" 
```

### 数据库最佳实践

**db/schema.rb** 文件的顶部有一条注释，内容是:

```
It's strongly recommended that you check this file into your version control system. 
```

和

```
If you need to create the application database on another system, you should
be using db:schema:load, not running all the migrations from scratch. The
latter is a flawed and unsustainable approach (the more migrations you'll
amass, the slower it'll run and the greater likelihood for issues). 
```

强烈建议总是将这个文件签入到您的版本控制系统中。如果该文件未检入且不是最新的，则不能使用`rails db:schema:load`。如上所述，如果你需要在另一台机器上创建应用数据库，那么你应该使用`db:schema:load`而不是`db:migrate`。不鼓励从头开始运行所有的迁移，因为随着时间的推移，它可能会有缺陷。我亲身经历过这个问题很多次。当迁移有问题时，很难跟踪问题出在哪里，迁移哪里出了问题。`db:schema:load`在那些情况下是救世主。

**当心！** `db:schema:load`就是当你需要在一个新的系统上创建你的应用数据库的时候使用。如果您正在添加新的迁移，您应该简单地让`db:migrate`来完成这项工作。如果您在现有的已填充的数据库上运行`db:schema:load`，您的数据(可能是生产数据)将被清除。所以只要记住这三条简单的规则，你就没事了:

1.  当您添加和应用任何新的迁移时，请始终将 **schema.rb** 签入您的版本控制系统。
2.  在新系统上创建应用数据库时，使用`db:schema:load`。
3.  当您需要应用新添加的迁移时，在所有其他情况下使用`db:migrate`。

**提示:**不要使用迁移向数据库添加数据。为此，请使用 **db/seeds.rb** 。

### 嵌套资源/路线

如果您有一个属于另一个*资源*的*资源*，那么最好定义嵌套在父*资源*的路由中的子*资源*的路由。例如，如果您有一个 **Post** 资源和一个 **Comment** 资源，并且设置了这些模型关联:

*   **帖子**模型有很多评论
*   **评论**模特所属**岗位**

您的 **config/routes.rb** 文件如下所示:

```
resources :posts
resources :comments 
```

这将像这样定义您的路线:

*   http://localhost:3000/ **帖子**
*   http://localhost:3000/**posts/1**
*   http://localhost:3000/**posts/1/edit**
*   http://localhost:3000/ **评论**
*   http://localhost:3000/**comments/1**
*   http://localhost:3000/ **评论/1/编辑**

这很好，但不是一个好的实践。我们应该定义嵌套在**帖子**路线中的**评论**路线。这是怎么回事:

```
resources :posts do
  resources :comments
end 
```

现在这将像这样定义你的路线:

*   http://localhost:3000/ **帖子**
*   http://localhost:3000/**posts/1**
*   http://localhost:3000/**posts/1/edit**
*   http://localhost:3000/**posts/1**/**评论**
*   http://localhost:3000/**posts/1**/**评论/1**
*   http://localhost:3000/**posts/1**/**comments/1/edit**

这些 URL 是可读的，并且表明这些评论属于一个 ID 为 **1** 的**帖子**。不过有一个小问题:您必须在使用 Ruby 的表单和 URL 助手时做一些改变。例如，在注释表单中，您有这样的内容:

```
<%= form_for(@comment) do |f| %>
  <!-- form elements removed for brevity -->
<% end %> 
```

这需要改变。目前它需要一个新的**注释**的实例，我们需要把它改成这样:

```
<%= form_for([@comment.post, @comment]) do |f| %>
  <!-- form elements removed for brevity -->
<% end %> 
```

注意在`form_for`助手中传递的参数。现在它是一个数组，首先包含父资源，其次包含**注释**实例。

我们需要改变的另一件事是**评论**的所有 URL 助手:

```
 <%= link_to 'Show', comment %> 
```

会被改成这样:

```
<%= link_to 'Show', [comment.post, comment] %> 
```

你的节目链接就会起作用。我们来看看*编辑*链接:

```
<%= link_to 'Edit', edit_comment_path(comment) %> 
```

这将被改变成这样:

```
<%= link_to 'Edit', edit_post_comment_path(comment.post, comment) %> 
```

注意了！助手名称(`edit_post_comment_path`)和参数(2 个参数而不是 1 个)都被更改，以便使其与嵌套资源/路由一起工作。

### 用`Time.zone.now`代替`Time.now`

最佳实践是始终在 **config/application.rb** 中定义应用程序的默认时区，如下所示:

```
config.time_zone = ‘Eastern Time (US & Canada)'`. 
```

`Date.today`和`Time.now`总是给出机器所在时区的当地日期和时间。使用`Time.zone.now`和`Time.zone.today`来避免开发机器和生产服务器上的时区冲突是有意义的。

### 不要在视图中加入太多的逻辑

视图是表示层，不应该包含逻辑。您应该避免这样的检查:

```
<% if book.published? && book.published_at > 1.weeks.ago %>
  <span>Recently added</span>
<% end %> 
```

或者

```
<% if current_user.roles.collect(&:name).include?("admin") || (user == book.owner && book.draft?) %>
  <%= link_to 'Delete', book, method: :delete, data: { confirm: 'Are you sure?' } %>
<% end %> 
```

你可以做的是将这个条件检查移到一个助手模块，它位于 **app/helpers** 中，并且在所有视图中自动可用。这里有一个例子:

```
# app/view/helpers/application_helper.rb
module ApplicationHelper
  def recently_added?(book)
    book.published? && book.published_at > 1.weeks.ago
  end

  # current_user is defined in application controller, which can be
  # accessed from helper modules & methods
  def can_delete?(book)
    current_user.roles.collect(&:name).include?("admin") || (user == book.owner && book.draft?)
  end
end 
```

将上面的视图标记修改为:

```
<% if recently_added?(book) %>
  <span>Recently added</span>
<% end %> 
```

和

```
<% if can_delete?(book) %>
  <%= link_to 'Delete', book, method: :delete, data: { confirm: 'Are you sure?' } %>
<% end %> 
```

还有许多其他地方可以使用这个`can_delete?`方法，但这只是一个将逻辑从视图中分离出来的例子。

### 结论

正如我在这篇文章的开头所讨论的，如果项目是按照框架和社区定义的正确方式编写的，我们的生活会变得更容易。框架的最佳实践是由有经验的人开发的，他们经历过以某种方式做事的负面影响，促使他们采取最佳实践来解决问题。在我们的社区里有这样的人并从他们的经历中获益对我们来说是件好事。幸运的是，Rails 因为拥有一个伟大的社区而广受欢迎，这使得它像我们一样受到喜爱。

我确信还有其他你认为重要的实践。请在评论中告诉我你最喜欢的。

## 分享这篇文章