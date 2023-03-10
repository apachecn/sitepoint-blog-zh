# 帕德里诺的简单管理

> 原文：<https://www.sitepoint.com/simple-admin-padrino/>

![Padrino](img/d926a91dc71ec2c9530abd1fc16a29cc.png)

Padrino 是一个优秀的网站框架，使得在轻量级环境中实现复杂的 web 应用变得容易。尽管它是建立在 [Sinatra](https://www.sinatrarb.com/) 之上，Padrino 在生成代码、建立有组织的文件结构和实现数据库功能时使用了类似 Rails 的方法。

本文简要介绍了 Padrino 框架，以及如何使用它在类似 Sinatra 的环境中创建简单的管理界面。

## 辛纳屈简介

简单来说，Sinatra 是一种处理 HTTP 连接的 DSL(特定于域的语言)。具体来说，Sinatra 将传入的用户请求与列表中的条目进行比较。当找到第一个匹配时，Sinatra 执行相关的代码，并将结果返回给用户。

例如，考虑一个 HTTP `GET`请求到达这个 URL:

```
www.example.com/product/shoehorn/index.html
```

Sinatra 实现中的 DSL 代码可能如下所示:

```
get 'product/laces' do
  haml :show_laces
end

get 'product/shoehorn' do
  haml :shoehorn
end

put 'order/shoppingcart' do
  haml :cart
end
```

在上面的代码中，`get`和`put`看起来像是 Ruby 命令，但实际上，它们是过滤器的一部分，用于查找 HTTP 动词，如`GET`、`PUT`、`POST`和`DELETE`。

上面 DSL 代码中的第二个过滤器条目匹配`/product/shoehorn` URL，因此在`do`和它的匹配`end`之间的任何东西都将被执行，结果将被发送回查看者的浏览器。在这个例子中，引用了一个包含 [Haml](https://haml.info/) 代码的文件，Ruby 将把它呈现到一个网页中。

实际的 DSL 结构通常比上面的例子复杂得多。例如，`before`和`after`过滤器可用于预处理和后处理结果。此外，匹配过滤器可以包含正则表达式以及若干其他类型的指令。此外，返回给查看者的浏览器的值不必是 HTML 在 RESTful API 实现的情况下，它将由一些 JSON 封装的数据组成，直接来自数据库，不呈现在视图中。

Sinatra 的底线是 DSL 实现了一个 HTTP 路由器。这是一个非常灵活的轻量级 web 处理引擎。如你所想，不利的一面是开发者被迫做所有的事情。因此，构建复杂的 web 应用程序可能非常困难。例如，像管理界面这样简单的事情可能要花费几天的开发时间。

## 帕德里诺:辛纳特拉延长

Padrino 扩展了 Sinatra，围绕它创建了一个简单的文件结构，并提供了生成器来快速启动和运行您的代码。

例如，在安装 Padrino ( `gem install padrino`)之后，您可以使用以下命令创建一个基本的 web 应用程序:

```
padrino g project my_website -d activerecord -b
```

这类似于 Rails 中的代码生成，在 Rails 中，系统生成一组目录和文件，旨在让您快速启动并运行。在这种情况下，创建一个根目录`my_website`，其他文件和子目录存储在其中。注意`-d`开关如何引入 ActiveRecord ORM，`-b`开关指示 bundler 安装所有的依赖项。

在 my_website 中创建的重要子目录有:

*   `app` —存储*主*应用程序的配置和控制器的地方。(下面有更多关于“主要”的信息)
*   `config` —存储所有内部应用程序相关文件的地方
*   `public` —存储图像、样式表和 JavaScript 文件的地方

请注意上面使用的“主要”一词。Padrino 的一个伟大之处在于，它可以将其他应用程序打包到主应用程序中。在这种情况下，主`app`目录用于跟踪主`my_website`应用程序，而`config`目录中的信息跟踪安装的应用程序。(我们将在下面看到安装管理应用程序时这一切是如何工作的。)

此时，我们有了一个功能最少的 web 应用程序，尽管没有 Rails 提供的任何类型的脚手架。为了让这个应用程序真正做些什么，我们将实现一个最小的类似 Sinatra 的 DSL。首先，我们将在“my_website/app/app.rb”文件的底部放置一些东西，如下所示。

```
get '/' do
  'Hello World!'
end
```

通过在`my_website`目录中执行以下命令来启动 web 服务器:

```
padrino start
```

然后我们导航到 *https://localhost:3000* 来看看我们的天才在工作的结果。事实上，如果一切顺利，我们会看到经典的“你好，世界！”语句。

在这一点上，我们可以继续我们的快乐之路，编写 Sinatra 代码并把它放进`my_website/app/app.rb`文件；然而，我们可能忽略了这一点。Padrino 的真正魅力在于它能够创建应用程序并将它们安装到框架中。

## 管理应用程序

让我们建立一个网站，限制访问一定范围的 URL 地址。更具体地说，我们将允许所有用户访问`/`区域，但是限制对`/admin`区域的访问。换句话说，每个人都可以访问以下列开头的 URL:

```
https://localhost:3000/
```

除了以下列开头的地址:

```
https://localhost:3000/admin
```

幸运的是，Padrino 的开发者已经完成了大部分艰难的工作。我们需要做的就是安装他们创建的`admin`应用程序。

请记住，管理应用程序需要引用数据库来存储用户名和密码。根据我们在生成 Padrino 应用程序时使用的信息，数据库将使用 ActiveRecord ORM 并创建一个默认的 sqlite 适配器。(注意:我们在数据库和适配器上有许多选择。)

使用这个命令安装管理应用程序，同样从`my_website`主目录中执行:

```
padrino g admin
```

请注意，在执行该命令后，我们会立即看到新的子目录:

*   `db` —包含子目录`migrate`下的数据库迁移以及一些数据库播种代码`seeds.rb`。
*   `admin` —包含已安装管理应用程序的代码。注意，这个子目录包含`app.rb`文件，定义了受保护和不受保护的 URL 路由的细节。
*   `models` —包含模型信息

另外，请注意,`config`目录中的`apps.rb`代码已经更改，为新的管理应用程序添加了一个挂载点。默认情况下，挂载点被更改为`/admin`。换句话说，对该位置的访问

```
https://localhost:3000/admin
```

现在将仅限于那些有管理权限的人。当然，如果您愿意，您可以自由地更改挂载点。(从技术上讲，这个挂载点将`/admin/app.rb`文件中的代码置于对`https://localhost/admin` URL 路径中的访问权限的控制之下。)

此外，还为您提供了数据库迁移的默认配置，如下面的`db/migration/001_create_accounts.rb`文件所示。该数据库最初跟踪用户的电子邮件地址、密码和角色等。电子邮件地址和密码用于登录信息，角色决定权限集。最初，角色只是“管理员”，但是您可以创建更多具有不同访问权限的角色。

```
class CreateAccounts < ActiveRecord::Migration
  def self.up
    create_table :accounts do |t|
      t.string :name
      t.string :surname
      t.string :email
      t.string :crypted_password
      t.string :role
      t.timestamps
    end
  end

  def self.down
    drop_table :accounts
  end
end
```

使用以下命令运行数据库迁移:

```
padrino rake ar:migrate
```

显然，当您最初创建管理员帐户时，您必须指定管理员用户的电子邮件地址和密码。该过程在您为数据库设定种子时发生，如下所示:

`padrino rake seed`

执行上述命令后，您将被引导完成创建管理员帐户信息的过程，就像在`db/seeds.rb`文件中的信息所引导的那样。

如果一切正常，您应该能够到达该位置:

`https://localhost:3000/admin`

您将被重定向到提供登录表单的`admin/sessions/new`页面。当您使用管理员身份登录时，您将获得默认的管理员页面，如下所示。你可以点击“账户”标签，获得当前账户持有人的列表。您甚至可以添加更多管理员或更改现有管理员的个人资料。

![Padrino Admin Interface](img/c788749b91c1687adb74d0cde5cb4215.png "Padrino Admin Interface")

您可以做很多事情来定制您的管理界面。通过在与管理相关的文件中实现简单的更改，您可以为不同类别的用户添加不同的角色，并且可以将这些角色的组与不同的路由相关联。

## 摘要

Padrino 用于在轻量级环境中实现复杂代码。您可以从基本的 Padrino 安装开始，然后以模块化的方式加载应用程序。本文使用这种简单的方法创建一个基本的 Padrino 应用程序，并演示其操作与 Sinatra 非常相似。然后，它只需几行代码就可以安装一个完整的管理界面。

## 分享这篇文章