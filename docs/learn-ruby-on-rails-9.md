# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-9/>

## 数据库表

我们已经为我们的每个环境(开发、测试、生产)创建了一个数据库，但是这些数据库中还没有任何表。表是数据库中以结构化方式存储数据的容器，它们由行和列组成。行映射到单个对象，列映射到这些对象的属性。数据库中所有表的集合以及这些表之间的关系称为数据库模式。图 4.5 给出了一个表格的例子。

![1562_table](img/aab84cfab5b41d81b70cd53f6775a9a3.png)
*图 4.5。典型数据库表的结构，包括行和列*

在 Rails 中，Ruby 类和数据库表的命名遵循一种直观的模式:如果我们有一个名为`stories`的包含 5 行的表，那么这个表将存储 5 个`Story`对象的数据。类和表之间的映射的好处在于它不是你需要写代码才能实现的——它只是发生了，因为`ActiveRecord`从类名中推断出表的名称。注意，我们在 Ruby 中的类名是单数名词(`Story`)，而表名是复数(`stories`)。

仔细想想，这种关系是有意义的:当我们在 Ruby 中引用一个`Story`对象时，我们是在处理一个单独的故事。但是 MySQL 表保存了大量的故事，所以它的名字应该是复数。虽然可以忽略这些约定(在处理遗留数据库时有时是必要的)，但是遵守它们要容易得多。

表和对象之间的密切关系进一步扩展:如果我们的 stories 表有一个 link 列，如图 4.5 中的例子，那么这个列中的数据将自动映射到一个`Story`对象中的 link 属性。向表中添加新列会导致同名属性在该表的所有对应对象中可用。

因此，让我们创建一些表来保存我们创建的故事。

目前，我们将使用在 MySQL 命令行中输入 SQL 的老式方法来创建一个表。您可以输入以下 SQL 命令，尽管我承认输入 SQL 并不有趣。相反，我建议您从代码归档中下载以下脚本，并将其直接复制粘贴到 MySQL 控制台中。

```
Example 4.3\. 03-create-stories-table.sql
USE shovell_development;
CREATE TABLE `stories` (
`id` int(11) NOT NULL auto_increment,
`name` varchar(255) default NULL,
`link` varchar(255) default NULL,
PRIMARY KEY (`id`)
);
```

您不必担心记住这些 SQL 命令以便在自己的项目中使用；相反，在第 5 章“模型、视图和控制器”中，我们将会看到一些叫做迁移的东西，这是一些特殊的 Ruby 类，我们可以编写它们来为我们的应用程序创建数据库表，而根本不用使用任何 SQL。

## 使用 Rails 控制台

现在我们已经准备好了`stories`表，让我们退出 MySQL 控制台，打开一个 Rails 控制台。Rails 控制台就像我们在《第 3 章，Ruby 简介》中使用的交互式 Ruby 控制台(irb ),但是有一个关键的区别:在 Rails 控制台中，您可以访问所有的环境变量和类，这些变量和类在您的应用程序运行时都是可用的。这些在标准 irb 控制台中不可用。

要进入 Rails 控制台，请切换到您的 shovell 文件夹，并输入命令`ruby script/console`，如下所示。`>>`提示符准备好接受您的命令:

```
$ cd shovell
$ ruby script/console
Loading development environment.
>>
```

## 保存对象

要开始使用`ActiveRecord`，只需定义一个继承自`ActiveRecord::Base`类的类。(在第 3 章介绍 Ruby 时，我们简单地提到了`::`操作符，我们用它来指代常量。它也可以用来引用存在于一个模块中的类，这就是我们在这里所做的。)翻回到第 3 章“介绍 Ruby”中关于面向对象编程(OOP)的部分，如果你需要复习继承的话。

考虑下面的代码片段:

```
class Story < ActiveRecord::Base
end
```

这两行代码定义了一个名为`Story`的看似空的类。然而，这个类并不是空的，我们很快就会看到。

从 Rails 控制台，让我们通过输入以下命令来创建这个`Story`类和一个名为`story`的类实例:

```
>> class Story < ActiveRecord::Base; end
=> nil
>> story = Story.new
=> #<Story:0x2642900 @attributes={"link"=>nil, "name"=>nil},
@new_record=true>
>> story.class
=> Story
```

正如你所看到的，创建一个新的`ActiveRecord`对象的语法与我们在《第 3 章，Ruby 简介》中创建其他 Ruby 对象的语法相同。此时，我们已经创建了一个新的`Story`对象。然而，这个对象只存在于内存中——我们还没有将它存储在数据库中。

我们可以通过使用对象的访问器方法检查`new_record`属性的值来确认它还没有被保存的事实:

```
>> story.new_record?
=> true
```

因为对象尚未保存，所以当我们退出 Rails 控制台时，它将会丢失。为了将它保存到数据库，我们需要调用对象的`save`方法:

```
>> story.save

=> true
```

现在我们已经保存了我们的对象(返回值`true`表示保存方法是成功的),我们的故事不再是一个新的记录。它甚至被分配了一个唯一的 ID，如下所示:

```
>> story.new_record?
=> false
>> story.id
=> 1
```

## 定义对象之间的关系

除了我们刚刚看到的基本功能，`ActiveRecord`还使得定义对象之间的关系(或关联)的过程尽可能简单。当然，有些数据库服务器可以完全在数据库模式中定义这种关系。然而，为了测试`ActiveRecord`,让我们看看它在 Rails 中定义这些关系的方式。

对象关系可以用多种方式定义；这些关系之间的主要区别在于关系中指定的记录数量。数据库关联的主要类型有:

*   一对一的关联
*   一对多关联
*   多对多关联

让我们来看看这些关联的一些例子。如果您愿意，可以随意将它们输入到 Rails 控制台中进行练习。请记住，您的类定义不会被保存——稍后我将向您展示如何在文件中定义关联。

假设我们的应用程序具有以下关联:

一个`Author`可以有一个`Weblog`:

```
class Author < ActiveRecord::Base
has_one :weblog
end
```

一个`Author`可以提交多个`Stories`:

```
class Author < ActiveRecord::Base
has_many :stories
end
```

一个`Story`属于一个`Author`:

```
class Story < ActiveRecord::Base
belongs_to :author
end
```

一个`Story`拥有并属于许多不同的`Topics`:

```
class Story < ActiveRecord::Base
has_and_belongs_to_many :topics
end
class Topic < ActiveRecord::Base
has_and_belongs_to_many :stories
end
```

毫无疑问，您已经厌倦了在控制台中输入类定义，但是当您退出控制台时，它们就消失了。出于这个原因，我们不会进一步讨论我们的对象之间的关联——我们将在第 5 章“模型、视图和控制器”中更详细地研究 ActiveRecord 模块。

## `ActionPack`模块

`ActionPack`是包含 MVC 架构的视图和控制器部分的库的名称。与`ActiveRecord`模块不同，这些模块的命名更直观一些:`ActionController`和`ActionView`。

在命令行上探索应用程序逻辑和表示逻辑没有太大的意义(毕竟，视图和控制器是为了与 web 浏览器交互而设计的！).相反，我只给你一个`ActionPack`组件的简要概述，我们将在第 5 章“模型、视图和控制器”中讨论实际操作的内容。

## ActionController(控制器)

控制器处理程序的应用程序逻辑，充当应用程序数据、表示层和 web 浏览器之间的粘合剂。在这个角色中，管制员执行许多任务，包括:

*   决定如何处理特定的请求(例如，是呈现整个页面还是只呈现其中的一部分)
*   从模型中检索要传递给视图的数据
*   从浏览器请求中收集信息，并使用它来创建或更新模型中的数据

当我们在本章前面介绍图 4.3 中的 MVC 图时，您可能没有想到一个 Rails 应用程序可以由许多不同的控制器组成。嗯，可以！每个控制器负责应用程序的特定部分。

对于我们的 Shovell 应用程序，我们将创建:

*   一个用于显示故事链接的控制器，我们将其命名为`StoryController`
*   用于处理用户认证的另一个控制器，称为`AccountController`

两个控制器都将从`ActionController::Base`类继承，但是它们将具有不同的功能，作为实例方法实现。(这个类和`ActionController::Base`类之间实际上会有一个中间类；我们将在《第 5 章，模型、视图和控制器》中更详细地介绍`StoryController`类的创建。然而，这并不能改变一个事实，即`ActionController::Base`是每个控制器继承的基类。下面是`StoryController`类的示例类定义:

```
class StoryController < ActionController::Base
def index
end
def show
end
end
```

这个简单的类定义用两个空方法设置了我们的`StoryController`—`index`方法和`show`方法——这两个方法我们将在后面的章节中详述。

## 命名类和文件

您现在应该已经注意到，类和文件的名称遵循不同的约定:

*   类名用大写字母书写(每个单词以大写字母开头，单词之间没有空格)。
*   文件名用小写字母书写，用下划线分隔每个单词。

这很重要！如果不遵守这个约定，Rails 将很难找到您的文件。幸运的是，你不需要经常手动命名你的文件，如果有的话，当我们在第 5 章“模型、视图和控制器”中查看生成的代码时你会看到。

每个控制器驻留在自己的 Ruby 文件中(扩展名为`.rb`),该文件位于`app/controllers`目录中。例如，我们刚刚定义的`StoryController`类将位于文件`app/controllers/story_controller.rb`中。

## 动作视图(视图)

正如我们前面讨论的，MVC 的原则之一是视图应该只包含表示逻辑。这意味着视图中的代码应该只执行与在应用程序中显示页面相关的操作——视图中的任何代码都不应该执行任何复杂的应用程序逻辑，也不应该从数据库中存储或检索任何数据。在 Rails 中，发送到 web 浏览器的所有内容都由视图处理。

可以预见，视图存储在我们应用程序的`app/views`文件夹中。

一个视图实际上根本不需要包含任何 Ruby 代码——它可能是一个简单的 HTML 文件。然而，更有可能的是，您的视图将包含 HTML 和 Ruby 代码的组合，使页面更加动态。Ruby 代码使用嵌入式 Ruby (ERb)语法嵌入到 HTML 中。

ERb 类似于 PHP 或 JSP，它通过将服务器端代码封装在特殊的标签中，允许服务器端代码分散在整个 HTML 文件中。例如，在 PHP 中，您可能会这样做:

```
<strong><?php echo 'Hello World from PHP!' ?></strong>
```

雇员再培训局的对应情况如下:

```
<strong><%= 'Hello World from Ruby!' %></strong>
```

ERb 标记对有两种形式:一种包括等号，另一种不包括等号:

```
<%= ... %>
```

这个标记对用于常规输出。这些标记之间的 Ruby 表达式的输出将显示在浏览器中。

```
<% ... %>
```

此标记对用于不打算显示的代码，如计算、循环或变量赋值。

每种方法的示例如下所示:

```
<%= 'This line is displayed in the browser' %>
<% 'This line executes silently, without displaying any output' %>
```

您可以在这些标签之间放置任何 Ruby 代码，无论是简单的还是复杂的。

创建一个视图的实例与创建一个模型或控制器的实例略有不同。虽然`ActionView::Base`(所有视图的父类)是 Rails 中视图的基类之一，但是视图的实例化完全由`ActionView`模块处理。Rails 开发人员唯一需要修改的是模板，即包含视图表示代码的文件。正如你可能已经猜到的，这些模板存储在`app/views`文件夹中。

与 Rails 中的大多数东西一样，模板文件的命名和存储有严格的约定:

*   模板与控制器的动作(方法)有一对一的映射。模板文件的名称与其映射到的操作的名称相匹配。
*   存储模板的文件夹以控制器命名。
*   模板文件的扩展名因模板类型而异。默认情况下，Rails 中有三种类型的模板:
    *   这是散布着 ERb 标签的标准 HTML 模板的扩展。
    *   `rxml`–这个扩展用于输出 XML 的模板(例如，为您的应用程序生成 RSS 提要)。
    *   `rjs`–这个扩展用于返回 JavaScript 指令的模板。例如，这种类型的模板可能用于修改现有页面(通过 Ajax)来更新`<div>`标签的内容。

这个约定听起来可能很复杂，但实际上很直观。例如，考虑我们之前定义的`StoryController`类。默认情况下，调用这个控制器的 read 方法会试图显示位于`app/views/story`目录中的`ActionView`模板。假设这个页面是一个标准的 HTML 页面(包含一些 ERb 代码)，这个模板的名称应该是`read.rhtml`。

Rails 还提供了特殊的模板，比如布局和部分。布局是控制应用程序全局布局的模板，例如在页面之间保持不变的结构(例如主导航菜单)。部分是特殊的子模板(模板被分割成单独文件的结果，如二级导航菜单或表单)，可以在应用程序中多次使用。我们将在第 7 章 Ajax 和 Web 2.0 中讨论布局和部分内容。

控制器和视图之间的通信是通过从控制器的动作中填充的实例变量进行的。让我们扩展我们的示例`StoryController`类来说明这一点(现在还不需要输入任何内容):

```
class StoryController < ActionController::Base
def index
@variable = 'Value being passed to a view'
end
end
```

如您所见，在控制器的动作中，实例变量`@variable`被赋予一个字符串值。通过`ActionView`的魔力，现在可以从相应的视图中直接引用这个变量，如下面的代码所示:

```
<p>The instance variable @variable contains: <%= @variable %></p>
```

这种方法允许在视图之外执行更复杂的计算(记住，它应该只包含表示逻辑)，让视图只显示计算的最终结果。

Rails 还提供对特殊容器的访问，比如参数和会话散列。这些包含的信息包括当前页面请求和用户会话。我们将在接下来的章节中利用这些散列。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章