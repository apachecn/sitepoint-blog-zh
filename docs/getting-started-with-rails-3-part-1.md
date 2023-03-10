# Rails 3 入门:第 1 部分

> 原文：<https://www.sitepoint.com/getting-started-with-rails-3-part-1/>

如果你听说过 Rails，但还没有尝试过，这是一个最好的尝试机会。Rails 3 是最新的主要版本，它不仅标志着框架本身成熟的一个重要里程碑，也是一个成熟社区的标志。

今天，我将向您展示一个构建简单 Rails 3 应用程序的快速示例，让您了解使用 Rails 是什么样子。本教程分为两部分，我们将构建一个数据库支持的应用程序:一个名为“Shorty”的 URL 缩写程序

在第一部分中，您将学习如何在 Rails 上安装 Ruby，生成应用程序和创建模型。在[的第二部分](https://www.sitepoint.com/getting-started-with-rails-3-part-2/)，我们将继续创建一个控制器，编写一个视图，然后我们将结束这一切。

我将略读很多东西，但是会有链接到更详细的解释，在第二部分的结尾有一些地方你可以在阅读完本教程后继续。

## **你想知道的事情**

要开始，理解 Rails 是建立在 MVC 架构之上的是很重要的；也就是模型-视图-控制器。

**模型**负责处理数据存储和业务逻辑。**视图**负责用户处理的层。**控制器**充当模型和视图之间的桥梁。换句话说，视图生成您的 HTML，模型处理您的数据库交互(以及基于该数据的任何逻辑)，而控制器将两者联系在一起，并且是您放置控制浏览器重定向到哪里以及用户可以做什么的逻辑的地方。

控制器由**动作**组成。把一个动作想象成一组独立的功能:当你点击应用程序时，Rails 会根据你的请求计算出要使用的动作。正是这种架构有助于编写干净、结构良好、易于使用的代码。

Rails 还引入了其他几个术语，了解这些术语对您很有帮助。

**路由器**本质上将一个 URL 映射到一个特定控制器上的一个特定动作，并带有请求条件。例如，Rails 就是这样知道当您获取/urls 时，它应该调用 URL 的控制器类上的 Index 操作。

接下来，Rails 有**迁移**——本质上是版本化的数据库模式——和一组工具来简化迁移的处理。这些是强大的工具，允许您编写设置数据库的代码，而不必手动处理 SQL(更重要的是，使编写可跨多个数据库引擎重用的代码变得容易)。

最后，Rails 使用了**环境的概念。**您的 Rails 应用程序可以在**开发**(当您处理代码时)、**测试**(运行自动化测试)和**生产**(部署您的应用程序)中运行。您还可以创建其他用户指定的环境，并且可以基于这些环境配置您的应用程序和数据库。

## **在 Rails 上安装 Ruby**

我们需要做的第一件事是在你的电脑上安装一个相对较新的 Ruby 版本(1.8.7 或更高版本)。需要注意的一点是，Rails 使用了大量命令行工具。如果您不习惯在 Windows 的命令提示符下工作，或者在 OSX 的终端上工作，或者选择您的 Linux 发行版，那么您可能希望事先在别处做一些阅读，以便将基础知识掌握得很好。如果是这样的话，Windows 和 T2 OSX/Linux 的指南是一个很好的起点。

## **在 Windows 上安装 Ruby 和 Rails**

在 Windows 上，最简单的方法是使用 [Rails 安装程序](http://railsinstaller.org/)，这是一个由 Wayne Seguin 开发的单一安装程序，它将为您设置您需要的一切。只需访问上面的链接并观看 Wayne 的演示视频，您应该很快就可以在自己的机器上安装 Ruby 了。

## **在 Mac 或 Linux 上安装 Ruby 和 Rails**

在 Linux 或 Mac 系统上，你可以选择使用你所选择的发行版:Ruby，或者大多数 Ruby 开发者更喜欢的, [Ruby Version Manager](http://rvm.beginrescueend.com/) ，也是由 Wayne Seguin 编写的。因为这两个选项都可能改变，并且相对于发行版来说是特定的，所以我建议谷歌一下与您的操作相关的指南，并通过 RVM 网站阅读。

另一个更新的选择是`bootstrap_rails_environment`脚本，也是由 Wayne Seguin 提供的。它设置 RVM 和所有依赖项，让 Rails 为 OSX 或 Linux 上的用户工作。

首先，您需要在`https://github.com/wayneeseguin/rvm/raw/master/contrib/bootstrap_Rails_environment`将文件下载到计算机上一个容易记忆的位置。默认情况下，这个脚本会安装 Rails 3.0.3、Ruby 1.8.7，并在`~/Sites`中为你设置一个目录。如果您希望更改这些值，请在您选择的文本编辑器中打开它，并根据您的需要更改以`rails_version`、`ruby_version`和`sites_path`开头的行。

下载并编辑完文件后，打开终端，切换到文件安装目录并运行:

```
chmod +x bootstrap_rails_environment
./bootstrap_rails_environment 
```

这将首先使脚本可执行，然后执行它。如果它遇到任何问题，它会让你知道并告诉你如何解决这些问题。

## **生成您的第一个 Rails 应用程序**

现在您已经安装了 Rails，您需要生成一个基本的应用程序。Ruby on Rails 将代码生成用于几个任务，最典型的是创建一个新的 Rails 应用程序，以及设置控制器和模型。

首先，打开你的终端或命令提示符——从现在开始你会非常需要它。如果您在 Windows 上使用了 Rails 安装程序，请确保使用“开始”菜单中的“Ruby 和 Rails 命令提示符”选项来打开它。在我们完成每一步的过程中，您会希望让它保持打开，因为我会经常提到它。

现在，转到您想要存储代码的目录。如果您使用 Rails bootstrap 脚本，那么您将`sites_path`设置为(默认情况下，`~/Sites`)的地方很可能就是您想要的地方；在 Windows 上，你会希望选择一个容易记忆和导航的地方(例如，`c:rails-projects`)。

进入目录后，使用`rails new`命令生成一个新的 Rails 应用程序。由于我们的应用程序名为“Shorty”，为了生成一个 Shorty 项目并进入其中，我们将在命令提示符下运行以下命令:

```
rails new shorty
cd shorty 
```

您还需要在代码目录中打开您选择的编辑器。如果你没有喜欢的编辑器，或者你的编辑器不完全支持 Ruby，我可以推荐 Windows 上的 [E 文本编辑器](http://www.e-texteditor.com/)，OSX 上的 [TextMate](http://macromates.com/) ，或者 Linux 上的 [Komodo Edit](http://www.activestate.com/komodo-edit) 。

再次在命令行上，运行`bundle install`。这告诉 bundler(Rails 使用的库)获取并安装它需要的所有库。在运行时，利用这个机会查看一下生成的 Rails 应用程序。

查看代码结构时，您可能会注意到 Rails 的核心价值之一——约定胜于配置——导致了代码的结构化目录布局。所有与配置相关的代码都放在`config`目录中，特定于应用程序的代码放在`app`目录中，依此类推。有关该结构的更多细节，请查看官方 [Rails 入门指南](http://guides.rubyonrails.org/getting_started.html#creating-the-blog-application)的第 3.2 点。

我们的 Rails 应用程序生成的文件之一`config/database.yml`，包含特定于环境的数据库连接细节。开箱即用，它使用 sqlite3，因为它很容易上手。要创建 sqlite3 数据库文件(并在 Rails web 服务器中开始构建)，请返回到 commond 提示符并运行:

```
rake db:create
rails server 
```

第一行调用 rake 中的“db:create”任务，这是一个工具(像其他语言中的 ant、nant 或 make ),它允许您运行少量保存的代码。在这种情况下，“db:create”任务将创建我们的数据库(如果您查看`config/database.yml`文件，您会看到它将在`db/development.sqlite3`创建一个文件)。

第二行启动 Rails 自带的内置 web 服务器，在端口 3000 上运行。在命令行上，您应该会看到类似于以下内容的内容:

```
=> Booting WEBrick
=> Rails 3.0.3 application starting in development on http://0.0.0.0:3000
=> Call with -d to detach
=> Ctrl-C to shutdown server 
```

现在，如果您打开浏览器并将其指向`http://localhost:3000/`，您应该会看到如下欢迎页面:

![fig1](img/ad989b962225c85a31ca06469fe8a876.png)

点击“关于您的应用程序环境”链接还会显示 Rails 所构建的不同库的版本号列表。

## **创建您的第一个模型**

现在我们已经有了一个空的 Rails 应用程序，可以开始开发 Shorty 了。

因为我们正在构建一个 URL 缩短器，所以我们需要某种方法来存储 URL 的记录。为此，我们将生成一个具有单一指定字段的 url 模型:URL。

首先，我们将使用 Rails 生成器通过在命令行运行一次以下命令来为我们生成类和迁移:

`rails generate model Url url:string` 

这告诉 Rails 为模型创建一个 url 类，创建一个迁移，该迁移将形成一个 url 表，其中包含一个开发人员指定的字段、URL 和相关的测试文件。

在本教程中，我们不会涉及测试，因为这是一个非常深入的话题。可以说，在实际应用中，测试是一个非常好的想法，Rails 使得测试驱动开发尽可能简单。有关测试的更多细节，请查看官方 Rails 指南中的[单元测试您的模型部分](http://guides.rubyonrails.org/testing.html#unit-testing-your-models)。

接下来，您将希望使用您的文本编辑器打开包含您的 URL 模型和生成的迁移文件的`app/models/urls.rb`文件(在`db/migrate`文件夹中，它将自动以时间戳后跟`_create_urls.rb`来命名)；比如`db/migrate/20110129184226_create_urls.rb`)。

迁移应该如下所示:

```
 class CreateUrls < ActiveRecord::Migration
  def self.up
    create_table :urls do |t|
      t.string :url
      t.timestamps
    end
  end

  def self.down
    drop_table :urls
  end
end 
```

你的模型应该是这样的:

```
class Url < ActiveRecord::Base
end 
```

## **编辑您的迁移**

迁移告诉 Rails，当应用它时，它应该创建一个带有 URL 字段(字符串类型，在大多数数据库中通常转换为 VARCHAR)的 Urls 表，以及两个时间戳字段— `created_at`和`updated_at`—这是 Rails 在您创建和更新模型时自动为您设置的。同样，当回滚时，您的迁移告诉 Rails 删除 Urls 表。

在这次迁移中，我们想要改变的唯一方面是告诉数据库不允许空 URL 字段。在文本编辑器中，转到迁移文件并更改:

```
t.string :url
```

到

```
t.string :url, :null => false
```

保存文件。现在，要将迁移应用到数据库，只需切换回控制台并运行:

```
rake db:migrate
```

Rails 足够聪明，知道您只需要运行一次迁移。如果您运行同一个命令两次，它不会尝试创建表两次，因此我前面使用了版本化数据库术语。

## **编辑模型**

接下来，我们想要编辑我们的模型，告诉 Rails 当我们创建或更新一个 URL 时，应该总是有一个给定的 URL。在 Rails 术语中，我们使用验证；具体来说，我们验证 URL 字段的存在。为此，我们只需编辑我们的模型文件(如上所述),如下所示:

```
class Url < ActiveRecord::Base
  validates :url, :presence => true
end 
```

不要忘记保存文件。关于验证器(以及一般的 ActiveRecord)的更多信息，一个好的参考通常是官方 Rails 指南。

为了测试这一点，我们将引入另一个工具:Rails 控制台。建立在 IRb(交互式 Ruby)之上的是所谓的 REPL:读取-评估-打印循环。本质上，它就像一个专门用于测试 Ruby 代码的终端会话。在其中，我们可以键入代码(读取)，它将运行它(评估)，输出返回值(打印)。

从命令行，只需在您的终端中键入`Rails console`；这将使用开发环境打开 Rails 控制台。举个简单的例子，如果您输入`puts Rails.env`，控制台应该打印出“development”，告诉您控制台正在您的开发环境中运行，正如预期的那样。

为了测试您的模型，我们首先通过键入以下命令初始化一个新的 URL 实例:

```
u = Url.new
```

这应该显示:

```
#<Url id: nil, url: nil, created_at: nil, updated_at: nil>
```

接下来，尝试通过运行以下命令将其保存到数据库:

```
u.save
```

因为它无效，控制台将输出 false。这意味着 save 调用失败，我们的对象没有保存到数据库中，因为它是无效的。要解决这个问题，我们有两个选择。首先，我们可以通过传入选项来创建一个新的 URL:

```
u = Url.new(:url => "http://google.com/")
```

或者，更简单地说，我们可以在现有的 URL 上设置 URL 属性:

```
u.url = "http://google.com/"
```

现在，再次运行`u.save`，这一次它将返回 true，告诉我们它已成功保存到数据库中。

如果我们键入`p u`(`p`方法将简单地打印作为参数给出的对象的表示，`u`)，我们应该看到:

```
#<Url id: 1, url: "http://google.com/", created_at: "2011-01-29 19:07:34", updated_at: "2011-01-29 19:07:34">
```

如前所述，它被自动分配了一个 id，并且还设置了`created_at`和`updated_at`字段的正确值:它的创建时间。

要测试从数据库中查找记录，请关闭控制台(按 Ctrl-C 或键入 exit)，然后通过键入 Rails console 重新打开控制台并运行:

```
u = Url.find 1
```

这应该会返回我们之前插入的 URL，方法是使用我们的 id 从数据库中获取它。同样，如果我们现在尝试:

```
u = Url.find 100000
```

如果没有 id 为 100000 的 URL，Rails 会在 Rails 中抛出一个`ActiveRecord::RecordNotFound`异常，告诉我们它找不到 id 为 100000 的 URL。

现在你已经安装了 Ruby，生成了你的第一个应用程序，创建了你的第一个模型。在本教程的第二部分中，您将创建您的第一个控制器，为您的应用程序编写视图，并让它正常工作。

转到[第 2 部分](https://www.sitepoint.com/getting-started-with-rails-3-part-2/)。

？

## 分享这篇文章