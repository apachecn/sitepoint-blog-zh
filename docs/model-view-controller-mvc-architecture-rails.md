# 理解 Rails 中的模型-视图-控制器(MVC)架构

> 原文：<https://www.sitepoint.com/model-view-controller-mvc-architecture-rails/>

以下是我们的书《Rails:忍者新手，第三版》的简短摘录，作者是格伦·古德里奇和帕特里克·伦茨。这是 Rails 初学者的终极指南。SitePoint Premium 会员可以通过他们的会员身份访问，或者您可以在世界各地的商店购买一份。

我们在第一章中第一次遇到的模型-视图-控制器(MVC)架构并不是 Rails 独有的。事实上，它比 Rails 和 Ruby 语言早很多年。然而，Rails 真正将应用程序的数据、用户界面和控制逻辑分离到了一个全新的层次。

让我们看看使用 MVC 架构构建应用程序背后的概念。一旦我们有了合适的理论，我们将看到它如何转化为我们的 Rails 代码。

### 理论上的 MVC

MVC 是一个软件应用程序架构的模式。它将应用程序分成以下组件:

*   **模型**用于处理数据和业务逻辑
*   **控制器**用于处理用户界面和应用程序
*   **视图**用于处理图形用户界面对象和演示

这种分离导致用户请求被如下处理:

1.  浏览器(在客户机上)向服务器上的控制器发送页面请求。
2.  控制器从模型中检索它需要的数据，以便响应请求。
3.  控制器将检索到的数据提供给视图。
4.  视图被呈现并发送回客户机供浏览器显示。

这个过程如下图 4-2 所示。

![MVC in Rails](img/17359d905d2b06717b97e40845073c39.png)

将软件应用程序分成这三个不同的组件是一个好主意，原因有很多，包括:

*   *改进的可扩展性*(应用程序增长的能力)–例如，如果您的应用程序由于数据库访问缓慢而开始遇到性能问题，您可以升级运行数据库的硬件，而不会影响其他组件

*   *易于维护*—由于组件之间的相互依赖性很低，对一个组件进行更改(修复错误或更改功能)不会影响另一个组件

*   *可重用性*—一个模型可以被多个视图重用

如果你正在努力理解 MVC 的概念，不要担心。现在，重要的是要记住，Rails 应用程序分为三个不同的组件。如果你以后需要参考的话，跳回 MVC 图。

### MVC 的发展之路

Rails 提倡模型、视图和控制器应该分开的概念，将每个元素的代码作为单独的文件存储在单独的目录中。

这就是我们在第 2 章中创建的 Rails 目录结构发挥作用的地方。是时候探索一下这个结构了。如果你看一下图 4-3 中描述的`app`目录，你会看到一些文件夹，它们的名字可能听起来很熟悉。

![](img/89dbf94f66fd8ad4d2ec80828b9ededa.png)

正如您所看到的，模型-视图-控制器架构的每个组件在`app`子目录中都有自己的位置——分别是`models`、`views`和`controllers`子目录。(我们将在第 7 章中讨论`assets`，在第 6 章中讨论`helpers`，在本章的稍后部分讨论`mailers`。`jobs`和`channels`不在本书讨论范围内。)

这种分离在组成框架本身的代码中继续存在。构成 Rails 核心功能的类位于以下模块中:

`ActiveRecord`

`ActiveRecord` is the module for handling business logic and database communication. It plays the role of model in our MVC architecture.While it might seem odd that `ActiveRecord` doesn't have the word “model” in its name, there is a reason for this: Active Record is also the name of a famous design pattern—one that this component implements in order to perform its role in the MVC world. Besides, if it had been called `ActionModel`, it would have sounded more like an overpaid Hollywood star than a software component …

`ActionController`

`ActionController` is the component that handles browser requests and facilitates communication between the model and the view. Your controllers will inherit from this class. It forms part of the `ActionPack` library, a collection of Rails components that we'll explore in depth in Chapter 5.

`ActionView`

code>ActionView is the component that handles the presentation of pages returned to the client. Views inherit from this class, which is also part of the `ActionPack` library.

让我们依次仔细看看这些组件。

### `ActiveRecord`模块

`ActiveRecord`旨在处理与数据库相关的所有应用任务，包括:

*   建立与数据库服务器的连接
*   从表中检索数据
*   在数据库中存储新数据

锦囊妙计还有一些其他的妙招。现在让我们来看看其中的一些。

#### 数据库抽象

`ActiveRecord`附带数据库适配器，可连接 SQLite、MySQL 和 PostgreSQL。通过 RubyGems，大量适配器可用于其他流行的数据库服务器包，如 Oracle、MongoDB 和 Microsoft SQL Server。

`ActiveRecord`模块基于数据库抽象的概念。回顾一下第一章，数据库抽象是一种编写应用程序的方式，它不依赖于任何一个数据库。特定于特定数据库服务器的代码被安全地隐藏在`ActiveRecord`中，并在需要时被调用。结果是，Rails 应用程序没有绑定到任何特定的数据库服务器软件。如果以后需要更改底层数据库服务器，则不需要更改应用程序代码。

#### 注意:陪审团在活动记录上

正如我所说的，`ActiveRecord`是活动记录模式的一个实现。有些人不同意`ActiveRecord`采取的方法，所以你也会听到很多。现在，我建议你学习一下`ActiveRecord`的工作方式，然后在学习的过程中形成你对实现的判断。

一些不同供应商之间差异很大的代码示例，`ActiveRecord`将其抽象化，包括:

*   登录到数据库服务器的过程
*   日期计算
*   布尔(`true` / `false`)数据的处理
*   数据库结构的演变

不过，在我向你展示`ActiveRecord`的神奇之处之前，有必要做一点整理。

#### 数据库表

表是关系数据库中的容器，它以结构化的方式存储数据，由行和列组成。行映射到单个对象，列映射到这些对象的属性。数据库中所有表的集合以及这些表之间的关系被称为**数据库模式**。图 4-4 给出了一个表格的例子。

![](img/d39f9d6addc14df8ab60d9e24a8d3fd7.png)

在 Rails 中，Ruby 类和数据库表的命名遵循一种直观的模式:如果我们有一个名为`stories`的包含 5 行的表，这个表将存储 5 个`Story`对象的数据。类和表之间映射的好处是不需要编写代码来实现它；映射就这样发生了，因为`ActiveRecord`从类名中推断出了表名。

注意，我们在 Ruby 中的类名是单数名词(`Story`)，而表名是复数(`stories`)。仔细想想，这种关系是有意义的:当我们在 Ruby 中引用一个`Story`对象时，我们是在处理一个单独的故事。但是 SQL 表保存了大量的故事，所以它的名字应该是复数。虽然您可以忽略这些约定——在处理遗留数据库时有时是必要的——但是遵守它们要容易得多。

对象和表之间的密切关系进一步扩大。如果我们的`stories`表有一个`link`列，如图 4-4 中的例子，这个列中的数据会自动映射到一个`Story`对象中的`link`属性。向表中添加新列会导致同名属性在该表的所有对应对象中可用。

因此，让我们创建一些表来保存我们创建的故事。

目前，我们将使用在 SQLite 控制台中输入 SQL 的老式方法来创建一个表。您可以键入以下 SQL 命令，尽管键入 SQL 并不有趣。相反，我建议您从代码归档中下载以下脚本，并将其直接复制并粘贴到您通过应用程序目录中的以下命令调用的 SQLite 控制台中:

```
$ sqlite3 db/development.sqlite3 
```

启动 SQLite 控制台后，粘贴以下内容:

```
CREATE TABLE stories (
  "id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
  "name" varchar(255) DEFAULT NULL,
  "link" varchar(255) DEFAULT NULL,
  "created_at" datetime DEFAULT NULL,
  "updated_at" datetime DEFAULT NULL
); 
```

您不必担心记住这些 SQL 命令以便在自己的项目中使用；相反，鼓起勇气，我们将在第 5 章中讨论迁移。迁移是特殊的 Ruby 类，我们可以编写它来为我们的应用程序创建数据库表，而根本不用使用任何 SQL。

#### 注意:寻求一些 SQL 的智慧

尽管 Rails 抽象出了创建表和数据库对象所需的 SQL，但是如果您熟悉 SQL 及其语法，那也是在帮自己的忙。SitePoint 已经出版了一本关于学习 SQL 的书，所以去看看吧。

#### 使用 Rails 控制台

现在我们已经准备好了`stories`表，让我们退出 SQLite 控制台(只需键入`.quit`)并打开一个 Rails 控制台。Rails 控制台就像我们在第二章中使用的交互式 Ruby 控制台(`irb`)，但是有一个关键的区别。在 Rails 控制台中，您可以访问应用程序运行时可用的所有环境变量和类。这些在标准`irb`控制台中是不可用的。

要进入 Rails 控制台，切换到您的`readit`文件夹，输入命令`rails console`或`rails c`，如下面的代码所示。`>>`提示符准备好接受您的命令:

```
$ cd readit
$ rails console
Loading development environment (Rails 5.0.0)
>> 
```

#### 保存对象

要开始使用`ActiveRecord`，只需定义一个从`ActiveRecord::Base`继承的类。我们在第 3 章非常简要地提到了`::`操作符，在那里我们提到了它是一种在对象上调用类方法的方法。它也可以用来引用存在于一个模块中的类，这就是我们在这里所做的。如果你需要继承方面的复习，请翻回到第三章的面向对象编程(OOP)部分。

考虑下面的代码片段:

```
class Story < ActiveRecord::Base
end 
```

这两行代码定义了一个看似空的类，名为`Story`；然而，这个类并不是空的，我们很快就会看到。

从 Rails 控制台，让我们通过输入以下命令来创建这个`Story`类和一个名为`story`的类实例:

```
>> class Story < ActiveRecord::Base; end
=> nil
>> story = Story.new
=> #<Story id: nil, name: nil, url: nil, created_at: nil,
  updated_at: nil>
>> story.class
=> Story(id: integer, name: string, link: string,
  created_at: datetime, updated_at: datetime) 
```

正如你所看到的，创建一个新的`ActiveRecord`对象的语法和我们在第 3 章中创建其他 Ruby 对象的语法是一样的。此时，我们已经创建了一个新的`Story`对象；然而，这个对象只存在于内存中——我们还没有将它存储在数据库中。

我们可以通过检查`new_record?`方法的返回值来确认我们的`Story`对象没有被保存:

```
>> story.new_record?
=> true 
```

因为对象还没有被保存，所以当我们退出 Rails 控制台时，它将会丢失。为了将它保存到数据库中，我们调用对象的 save 方法:

```
>> story.save
=> true 
```

现在我们已经保存了我们的对象(返回值`true`表示保存方法成功)，我们的故事不再是新的记录。它甚至被分配了一个唯一的 ID:

```
>> story.new_record?
=> false
>> story.id
=> 1 
```

#### 定义对象之间的关系

除了我们刚刚看到的基本功能，`ActiveRecord`还使定义对象间关系(或关联)的过程变得尽可能简单。当然，有些数据库服务器可以完全在数据库模式中定义这种关系。为了测试`ActiveRecord`,让我们看看它在 Rails 中定义这些关系的方式。

对象关系可以用多种方式定义；这些关系之间的主要区别在于关系中指定的记录数量。数据库关联的主要类型有:

*   一对一的关联
*   一对多关联
*   多对多关联

让我们来看看这些关联的一些例子。出于练习的目的，如果您愿意，可以随意将它们键入 Rails 控制台。请记住，您的类定义不会被保存——稍后我将向您展示如何在文件中定义关联。

假设我们的应用程序具有以下关联:

*   一个`Author`可以有一个`Blog`:

    ```
    class Author < ActiveRecord::Base
      has_one :weblog
    end 
    ```

*   一个`Author`可以提交多个`Stories`:

    ```
    class Author < ActiveRecord::Base
      has_many :stories
    end 
    ```

*   一个`Story`属于一个`Author`:

    ```
    class Story < ActiveRecord::Base
      belongs_to :author
    end 
    ```

*   一个`Story`拥有并属于许多不同的`Topic`:

    ```
    class Story < ActiveRecord::Base
      has_and_belongs_to_many :topics
    end
    class Topic < ActiveRecord::Base
      has_and_belongs_to_many :stories
    end 
    ```

毫无疑问，您已经厌倦了在控制台中输入类定义，但是当您退出控制台时，它们就消失了。出于这个原因，我们现在不会进一步讨论我们的对象之间的关联——相反，我们将在第 5 章更详细地研究 Rails `ActiveRecord`模块。

### `ActionPack`图书馆

`ActionPack`是包含 MVC 架构的视图和控制器部分的库的名称。与`ActiveRecord`模块不同，这些模块的命名更加直观:`ActionController`和`ActionView`。

在命令行上探索应用程序逻辑和表示逻辑意义不大；毕竟，视图和控制器*是为与网络浏览器交互而设计的*！相反，我将提供一个`ActionPack`组件的简要概述，我们将在第 5 章中讨论实际操作的内容。

### `ActionController`(控制器)

**控制器**处理程序的应用程序逻辑，充当应用程序数据、表示层和 web 浏览器之间的粘合剂。在这个角色中，管制员执行许多任务，包括:

*   决定如何处理特定的请求(例如，是呈现整个页面还是只呈现其中的一部分)
*   从模型中检索要传递给视图的数据
*   从浏览器请求中收集信息，并使用它来创建或更新模型中的数据

当我们在本章前面介绍图 4-2 中的 MVC 图表时，你可能没有想到一个 Rails 应用程序可以由许多不同的控制器组成。嗯，可以！每个控制器负责应用程序的特定部分。

对于我们的 Readit 应用程序，我们将创建:

*   一个用于显示故事链接的控制器，我们将其命名为`StoriesController`
*   用于处理用户认证的另一个控制器，称为`SessionsController`
*   一个显示用户页面的控制器，名为`UsersController`
*   一个显示评论页面的控制器，名为`CommentsController`
*   处理故事投票的最终控制器，称为`VotesController`

每个 Rails 应用程序都有一个继承自`ActionController::Base`的`ApplicationController`(位于`app/controllers/application_controller.rb`)。我们所有的控制器都将从`ApplicationController`、继承，实际上在这个类和`ActionController::Base`类之间会有一个中间类；然而，这并不能改变一个事实，即`ActionController::Base`是每个控制器继承的基类。我们将在第 5 章更详细地讨论`StoriesController`类的创建。但是它们会有不同的功能，作为实例方法来实现。下面是`StoriesController`类的示例类定义:

```
class StoriesController < ApplicationController
  def index
  end

  def show
  end
end 
```

这个简单的类定义为我们的`StoriesController`设置了两个空方法:`index`方法和`show`方法。我们将在后面的章节中详述这些方法。

每个控制器驻留在自己的 Ruby 文件中(扩展名为`.rb`),该文件位于`app/controllers`目录中。例如，我们刚刚定义的`StoriesController`类将驻留在文件`app/controllers/stories_controller.rb`中。

#### 注意:类和文件的命名约定

您现在应该已经注意到，类和文件的名称遵循不同的约定:

*   类名用大写字母书写(每个单词以大写字母开头，单词之间没有空格)。CamelCase 实际上有两种变体:一种首字母大写(也称为 PascalCase)，另一种首字母小写。Ruby 对类名的约定要求首字母大写。

*   文件名用小写字母书写，用下划线分隔每个单词。

这是一个重要的细节。如果不遵守这个约定，Rails 将很难找到你的文件。幸运的是，你不需要经常手动命名你的文件，如果有的话，当我们在第 5 章看生成的代码时你会看到。

### `ActionView`(视图)

如前所述，MVC 的原则之一是视图应该只包含表示逻辑。这个原则认为，视图中的代码应该只执行与应用程序中显示页面相关的操作；视图中的任何代码都不应该执行任何复杂的应用程序逻辑，也不应该从数据库中存储或检索任何数据。在 Rails 中，发送到 web 浏览器的所有内容都由视图处理。

可以预见，视图存储在我们应用程序的`app/views`文件夹中。

一个视图实际上不需要包含任何 Ruby 代码——可能你的一个视图是一个简单的 HTML 文件；然而，更有可能的是，您的视图将包含 HTML 和 Ruby 代码的组合，使页面更加动态。Ruby 代码使用嵌入式 Ruby (ERb)语法嵌入到 HTML 中。

ERb 通过将服务器端代码包装在特殊的标签中，允许服务器端代码分散在整个 HTML 文件中。例如:

```
<strong><%= 'Hello World from Ruby!' %></strong> 
```

ERb 标记对有两种形式:一种包含等号，另一种不包含等号:

`<%= … %>`

This tag pair is for regular output. The output of a Ruby expression between these tags will be displayed in the browser.

`<% … %>`

This tag pair is for execution. The output of a Ruby expression between these tags will not be displayed in the browser.

以下是每个 ERb 标签的示例:

```
<%= 'This line is displayed in the browser' %>
<% 'This line executes silently, without displaying any output' %> 
```

您可以在这些标签之间放置任何 Ruby 代码，无论是简单的还是复杂的。

创建一个视图的实例与创建一个模型或控制器的实例略有不同。虽然`ActionView::Base`(所有视图的父类)是 Rails 中视图的基类之一，但是视图的实例化完全由`ActionView`模块处理。Rails 开发人员唯一需要修改的文件是模板，它是包含视图表示代码的文件。正如你可能已经猜到的，这些模板存储在`app/views`文件夹中。

和其他 Rails 一样，模板文件的命名和存储有严格的约定:

*   模板与控制器的动作(方法)有一对一的映射。模板文件的名称与其映射到的操作的名称相匹配。
*   存储模板的文件夹以控制器命名。
*   模板文件的扩展是双重的，并且根据模板的类型和编写模板的实际语言而变化。默认情况下，Rails 中有三种类型的扩展:

    `html.erb`

    This is the extension for standard HTML templates that are sprinkled with ERb tags.

    `xml.builder`

    This extension is used for templates that output XML (for example, to generate RSS feeds for your application).

    `json.builder`

    This extension is used for templates that output JSON, which is a common data integration for APIs. We'll talk more about JSON in Chapter 9 on advanced topics.

这个约定听起来可能很复杂，但实际上很直观。例如，考虑前面定义的`StoriesController`类。默认情况下，调用这个控制器的`show`方法会试图显示位于`app/views/stories`目录中的`ActionView`模板。假设这个页面是一个标准的 HTML 页面(包含一些 ERb 代码)，这个模板的名字应该是`show.html.erb`。

Rails 还提供了特殊的模板，比如布局和部分。**布局**是控制应用程序全局布局的模板，比如在页面之间保持不变的结构(比如主导航菜单)。 **Partials** 是特殊的子模板(模板被分割成独立文件的结果，如二级导航菜单或表单)，可以在应用程序中多次使用。我们将在第 7 章讨论布局和局部。

控制器和视图之间的通信是通过从控制器的动作中填充的实例变量进行的。让我们扩展我们的示例`StoriesController`类来说明这一点(现在还不需要键入任何内容):

```
class StoriesController < ActionController::Base
  def index
    @variable = 'Value being passed to a view'
  end
end 
```

如您所见，在控制器的动作中，实例变量`@variable`被赋予一个字符串值。通过`ActionView`的魔力，现在可以从相应的视图中直接引用这个变量，如下面的代码所示:

```
<p>The instance variable @variable contains: <%= @variable %></p> 
```

这种方法允许在视图之外执行更复杂的计算——记住，它应该只包含表示逻辑——并允许视图只显示计算的最终结果。

Rails 还提供对特殊容器的访问，比如`params`和`session`散列。它们包含当前页面请求和用户会话等信息。我们将在接下来的章节中利用这些散列。

## 分享这篇文章