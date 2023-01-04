# Rails 安全隐患

> 原文：<https://www.sitepoint.com/rails-security-pitfalls/>

[![PylonAndBigHole_image](img/1e0234ddc1ca504facbf8e4aa9dbc514.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/Fotolia_36196868_Subscription_XL.jpg)

默认情况下，Rails 提供了许多很好的安全标准，但是也有一些常见的陷阱、不太为人所知的方法和创建安全应用程序必须考虑的细节。

我们将快速探究这些陷阱，看看如何防止它们。

## 公开秘密令牌

当创建一个新的应用程序时，Rails 会生成一个随机的`secret_token`来验证会话 cookie 的完整性。这听起来不错，那么问题是什么呢？

嗯，`secret_token`在我们代码的一个文件中，这意味着它有很多机会在我们的版本控制中。我们不喜欢黑客(或者愤怒的前同事！)访问我们的代码，也可以访问任何可以给他更多控制权的凭证或信息。

在 Rails 中，使用`Marshal.load`反序列化 cookie 数据，这可以[执行任意的 ruby 代码](http://ruby-doc.org/core-2.0/Marshal.html#label-Security+considerations)。这意味着，如果攻击者知道我们的`secret_token`，他可以轻松地用他想要的任何数据创建一个有效的 cookie，并可以在我们的服务器上执行一些令人讨厌的代码。

### 如何预防

幸运的是，解决这个问题非常简单。只需为`secret_token`使用一个`ENV`变量。这样，您可以在生产中设置一个，每个开发人员都可以有自己的。

为此，您必须将`config/initializers/secret_token.rb`中的行改为:

```
MyRailsApp::Application.config.secret_token = ENV['SECRET_TOKEN']
```

并且记得在启动你的应用程序之前设置一个`ENV`变量，否则它会引发一个错误。

**提示**:对于本地开发，你可以有一个`.env`，它会被像 [foreman](https://github.com/ddollar/foreman) 或者 [dot-env](https://github.com/bkeepers/dotenv) 这样的工具自动加载。将一个`env.example`文件签入到您的版本控制中是一个好主意，这样很容易知道需要哪些 ENV 变量。如果你用的是 [pow](http://pow.cx/) 你也可以把它放在你的`.powenv`里。

## 在野外的变化

提供了一些非常方便的处理字符串的方法。例如，要从字符串中获取对象，您可以:

```
"Hash".constantize #=> Hash
```

用它从参数中获取类对象是很常见的，例如:

```
klass = params[:class].constantize
```

这比看起来更危险。攻击者可以利用这段代码，检查我们是否在使用某个 gem 或需要某个 lib，只需用类 param(例如:`/faulty/action?class=Mysql2`)执行对该动作的请求。如果加载了类或模块，应用程序可能会以 HTTP 状态 200 响应，如果没有，则以 500 响应。

更糟糕的是，如果代码*也*有这样的东西:

```
object = klass.new(params[:name])
```

像`{ class: 'File', name: '/etc/hosts' }`这样的一组正确的参数可能会将我们的文件系统暴露给攻击者，或者允许执行任意代码。

这不仅发生在`constantize`中，也发生在许多其他用于获取表或部分名称的`ActiveSupport::Inflector`方法中。

### 如何预防

每次你使用上面这样的图案时，都要三思，看看你是否暴露得太多了。

在这种情况下，您可能希望将一些有效的类或参数列入白名单，以防止不必要的代码执行或信息泄漏。

## 测井参数

不仅你的应用会暴露漏洞，你的日志也会。是的，那些有用且看似无害的日志可能包含敏感数据。
默认情况下，Rails 过滤掉任何匹配正则表达式`/password/`的参数，将其替换为`[FILTERED]`。

这确实是一个很好的默认值，但是您可能希望向该过滤器添加任何其他敏感参数。

### 如何预防

添加任何敏感的参数名称(例如:令牌、代码，可能是电子邮件？)到您的`config/application.rb`文件中。默认文件配置如下所示:

```
# Configure sensitive parameters which will be filtered from the log file.
config.filter_parameters += [:password]
```

该滤波器甚至可以与`Proc`或`Regexp`一起工作。字符串和符号会自动转换为与参数名的任何部分匹配的正则表达式。

您还可以为每个环境添加过滤器。只需在上述环境初始化器中添加一行类似的代码。

## XSS

Rails 在防止开箱即用的跨站点脚本方面做得非常好。[安全指南](http://guides.rubyonrails.org/security.html#cross-site-scripting-xss)深入解释了这个问题。

从 Rails 3 开始，默认情况下，所有到视图的输出都是转义的。这很好，但是仍然有很多可能引入 XSS 漏洞的常见场合。

### 例子

假设您想要创建一个帮助器，将一些文本包装在 HTML 标记中。您可能会尝试做一些事情，例如:

```
def emphasize(text)
  "<em>#{text}</em>".html_safe
end
```

视图需要调用`html_safe`来正确呈现*标签。但是我们无意中引入了一个 XSS 漏洞:在`text`参数中的任何东西都将被视为`html_safe`。这意味着，如果`text`类似于`<script>alert("oops")</script>`，那么结果输出将是`<em><script>alert("oops")</script></em>`。用户看不到内容，脚本标签将由浏览器执行。那不是我们想要发生的。*

 *为了防止这种情况，有几个选择。您可以显式转义`text`变量:

```
def safe_emphasize(text)
  "<em>#{h text}</em>".html_safe
end
```

或者您可以使用 Rails 的助手:

```
def another_safe_emphasize(text)
  content_tag :em, text
end
```

在这两种情况下，内容都将被转义，输出将是`<em>&lt;script&gt;alert("oops")&lt;/script&gt;</em>`，它将向用户显示文本，浏览器不会执行脚本。

### 一个不太常见的案例

另一个陷阱是在`link_to`助手的`href`部分使用用户提供的数据。例如:

```
<%= link_to "Website", user.website %>
```

在这种情况下，如果用户将类似于`javascript:alert("oops")`的东西设置为他的网站属性，那么 javascript 代码就会被执行。不幸的是，解决这个案子并不简单。没有内置的帮助器删除`javascript:`代码。

### 如何预防

*   当从你的助手创建 HTML 标签时，尽量使用内置的 [`content_tag`](http://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-content_tag) 助手。
*   如果出于某种原因你需要使用字符串，看看一些有用的方法，如 [`html_escape`](http://api.rubyonrails.org/classes/ERB/Util.html#method-c-html_escape) 和 [`strip_tags`](http://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-strip_tags) 。
*   如果使用用户提供的数据作为链接的`href`属性，一定要仔细检查。您可能希望创建一些基本的助手或验证来防止漏洞。

## 拒绝服务

最近在 Rails 中发现了一个[漏洞](https://groups.google.com/forum/#!topic/rubyonrails-security/jgJ4cjjS8FE),当用作查询的查找值时，该漏洞将散列的键转换成符号。这意味着在如下查询中:

```
User.where(:name => { 'foo' => 'bar' })
```

字符串“foo”将被转换为符号。

#### 简短的解释

在 Ruby 中，符号在创建后不会被垃圾回收。一个符号总是指向同一个内存地址——这就是为什么它们非常适合重复散列键的原因。它们不会使用新的内存，也不会被垃圾回收！。

但这是一把双刃剑。如果创建了太多的符号，它们可能会消耗物理内存。

#### 返回 DoS

幸运的是，对于比 3.2.12 更新的 Rails 版本(以及 2.3.x 和 3.1.x 的最新补丁版本)，上面的例子已经得到了修复。但这并不意味着我们的应用程序对这种类型的攻击免疫。

如果我们将用户输入转换为符号，一个精心制作的请求可能会导致我们的应用程序创建大量的符号，从而导致拒绝服务。

### 如何预防

*   避免在用户提供的输入中使用`to_sym`。如果没有更好的选择，您可能希望在将输入转换为符号之前将其列入白名单。
*   在报告控制者中看到这种行为是很常见的，要特别注意那些情况。
*   许多库在幕后使用`to_sym`,在向它们发送用户提供的输入之前，您可能想仔细检查一下。

## SQL 注入

这是最常见的攻击之一。幸运的是，Rails 在防止这种情况方面做得很好，并在它的[安全指南](http://guides.rubyonrails.org/security.html#sql-injection)中解释了这一点。

大多数 Rails 的内置查询方法在生成 SQL 查询之前都会执行清理。*他们中的大多数*。这意味着有一些人不知道。让事情变得更复杂的是，有一些方法可以避免对那些需要清理的文件进行清理。

### 一个简单的案例

例如，类似以下的查询

```
@posts = Post.where(user_id: params[:user_id])
```

确实净化了它的参数。但是一个心不在焉的开发人员可以写出这样的东西:

```
@posts = Post.where("user_id = '#{params[:user_id]}'")
```

一切都将按预期运行，但刚刚引入了一个漏洞。ActiveRecord 不会逃脱查询的这一部分，这使得攻击者可以使用精心制作的`user_id`参数来访问所有的帖子记录(如果您正在处理敏感信息，这可能非常糟糕)。`' OR 1 --`的一个`user_id`参数将产生这个查询:

```
SELECT * FROM accounts WHERE user_id = '' OR 1 --'
```

它将匹配所有记录，攻击者就可以访问这些记录。

使用散列条件总是更好。如果您需要使用字符串来设置一些复杂的 SQL 条件，请始终使用 Rails 的插值:

```
@posts = Post.where("user_id = ? AND posted_at > ?", params[:user_id], params[:date])
```

这将和第一个例子完全一样，净化`user_id`和`date`参数。

### 一个不太常见的案例

这是一个相对简单的场景。但是真正的陷阱是那些不净化它们的参数和不记录它的方法。

下面是这些方法的详细列表。它甚至指出了一些需要注意的事情。

例如，`lock`方法接受任何 sql 作为它的参数。

```
User.where(id: params[:id]).lock(params[:lock])
```

如果攻击者提供一个精心制作的`lock`参数，如`" OR 1=1"`，应用程序将执行如下查询:

```
SELECT `users`.* FROM `users`  WHERE `users`.`id` = 1  OR 1=1
```

这将返回所有用户，而不是带有被询问的`id`的用户。

这些类型的攻击和有害参数的形式在不同的数据库引擎之间会有很大的不同。确保在所有环境中使用相同的数据库引擎，并阅读它们的文档。

### 如何预防

*   首先，请务必阅读 [Rails 的安全指南](http://guides.rubyonrails.org/security.html)并记住此处列出的[方法。](http://rails-sqli.org/)
*   总是尝试使用散列条件，或者在其他情况下使用`?`类型的插值。*永远不要只为查询条件*提供一个字符串。
*   编写不常见的查询时要仔细检查。您可能想自己提交一些 SQL，看看它是否被转义了。

## 保护我们安全的宝石

这是开发应用程序时需要考虑的几件事。不幸的是，Rails 应用程序中还有很多其他的漏洞和安全隐患。

让我们的生活变得更轻松(还是让我们更担心？)还有[布雷克曼](//brakemanscanner.org/)。Brakeman 是一个用于 Ruby on Rails 应用程序的安全扫描器。它通过直接查看源代码而不是与应用程序交互来工作。这有一些优点和一些缺点，在他们的[文档](http://brakemanscanner.org/docs/introduction/)中有很好的解释。

一旦您运行`brakeman`，它会生成一个漂亮的报告，其中包含一个警告列表，包括它的置信度、文件、类型和描述:

```
+------------+---------------------------------------------------------+----------------------+--------------------------------------------+
| Confidence | Template                                                | Warning Type         | Message                                    |
+------------+---------------------------------------------------------+----------------------+--------------------------------------------+
| High       | posts/show                                              | Cross Site Scripting | Unsafe parameter value in link_to href ... |
| Weak       | dashboard/index (DashboardController#index)             | Cross Site Scripting | Symbol conversion from unsafe string ...   |
+------------+---------------------------------------------------------+----------------------+--------------------------------------------+
```

消息在这里被截断，但是 Brakeman 产生了非常有用的警告消息，包括文件行和上下文。

**提示**:我建议将报告输出到一个 HTML 文件(通过`-o report.html`选项)以获得完整的信息。

如果你想在你的 CI 环境中扫描你的代码，Brakeman 也有一个 [Jenkins 插件](http://brakemanscanner.org/docs/jenkins/)。这是一种非常有用且简单的监视安全性的方法。

## 总结

Rails 提供了一些很好的安全默认设置，但是在保护应用程序时仍然有很多事情需要知道。牢记这些事情并定期执行手动或自动检查以防止可能的攻击是一个好主意。

## 更多资源

*   [官方 Rails 安全指南](http://guides.rubyonrails.org/security.html)
*   [官方 Rails 安全邮件列表](https://groups.google.com/forum/#!forum/rubyonrails-security)
*   [Rails 的查询方法列表，这些方法不整理它们的参数](http://rails-sqli.org/)
*   [铁路安全公告](http://railscasts.com/?tag_id=5)
*   [如何使用其 secret_token 破解 Rails 应用](http://robertheaton.com/2013/07/22/how-to-hack-a-rails-app-using-its-secret-token/)
*   CodeClimate 的铁路安全课程

## 分享这篇文章*