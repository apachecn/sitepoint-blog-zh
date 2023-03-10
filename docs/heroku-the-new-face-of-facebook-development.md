# Heroku:脸书发展的新面貌

> 原文：<https://www.sitepoint.com/heroku-the-new-face-of-facebook-development/>

上周，Heroku [宣布与脸书建立新的合作关系，将 Heroku 托管平台整合到脸书应用开发中。这是一个令人振奋的消息，因为它完全消除了进入脸书应用程序开发的负担。Heroku 的简单性和规模以及脸书的影响力肯定会激发一些伟大的应用和新想法。让我们快速浏览一下如何用 Ruby 完成这项任务。](http://devcenter.Heroku.com/articles/facebook)

你知道你那个超棒的脸书应用程序的想法吗？你甚至不会告诉你的朋友因为害怕他们偷它？那个会让你在脸书和社交媒体界疯狂出名的人？现在，感谢 Heroku，你可以专注于使它变得伟大，而不是你将如何主持它。

在过去的日子里(在这种情况下，是上周)，蓬勃发展的脸书开发商必须找出在哪里托管一个改变世界的脸书应用程序。这意味着在云提供商上配置虚拟机，安装所有基础设施(web 服务器、代理和 gems——哦，天哪！)并编写各种代码来处理 OAuth 认证和脸书图形 API。

那些日子已经过去了，我的朋友，因为今天我希望向你展示新的日子:充满了不考虑基础设施的日子；充满商业逻辑、彩虹和独角兽的日子(或者你的用户在脸书“喜欢”的任何东西)；脸书应用程序开发只需点击几下鼠标和终端命令。

## 跟踪狂

在上面链接的帖子中，Heroku 上的基本脸书应用程序被很好地描述了。如果你还没看过，现在就去做吧(真的…去做吧)。Heroku 还做了一个[截屏](https://vimeo.com/29082940),展示了一个 Ruby 应用程序的创建过程，其中有一个非常基本的应用程序更改。一旦你从震惊和怀疑这一切有多容易中恢复过来，就继续使用这个演练创建一个应用程序。

我称我的为“跟踪者”,并使用 Ruby 作为语言(咄)。你必须为你的平台下载 Heroku Toolbelt，并确保`heroku login`在你的终端上工作。此外，确保您的 Heroku 应用程序(我的应用程序的名称是“floating-mountain-9900”)有一个本地 git 存储库，我们应该准备好启动了。准备好牛逼吧。

## 应用程序结构

应用程序结构没有太多变化。我们有一个 Gemfile 来管理我们与 Bundler 的依赖关系(耶！)，一个用于主 app 逻辑的`app.rb`文件，一个用于配置 Rack 的`config.ru`文件，以及两个目录:`public`和`views`。唯一的视图文件是`index.erb`，所以让我们看看我们的视图。

## 景色(比电视上的好)

这个`index.erb`文件只是一个散布着 ERB 片断的 HTML 文件。我们看到的第一个 ERB 标签是应用程序在`head`部分的标题:

```
<title><%= @app.name %></title>
```

如果你熟悉 ERB 模板，这应该是熟悉和令人欣慰的。您可以使用 Ruby 的模板工具来构建您的脸书应用程序页面。标题后面是一堆`meta`标签。如果你想让你的应用在脸书有好的曝光率，这些标签是很重要的，在这里有完整的解释[。我让我的看起来像:](https://developers.facebook.com/docs/opengraph/)

```
<meta property="og:title" content="Stalker"/>
<meta property="og:type" content="website"/>
<meta property="og:url" content="https://floating-mountain-9900.Herokuapp.com/"/>
<meta property="og:image" content=""/>
<meta property="og:site_name" content="Stalker"/>
<meta property="fb:app_id" content="<%= @app.id %>" />
```

我没有做图像，但你肯定会想这么做。此外，您可能希望用一个自定义域来替换您的 URL，这是一个简单的(并且是免费的！)Heroku 应用程序的附加组件。

此外，在`head`部分，您可以看到链接的`screen.css`文件，这让您知道如何更改页面上的样式。样式表存储在`public/stylesheets`目录中，这对 Rails/Sinatra 开发人员来说应该很合适。您希望包含在应用程序页面中的任何图像都存储在`public/images`中，这也是一个众所周知的 Ruby 习语。

继续查看，我们开始看到一些非常有趣的项目:*实例变量*。如果你寻找 id 为`samples`的`section`标签，这个页面会给出一些例子，显示用户的一些朋友(`@friends`)、照片(`@photos`)，甚至是使用你的新应用的朋友(`@friends_using_app`)。这些变量从何而来？你如何用数据将它们水化？真的有这么简单吗？为此，我们需要打开`app.rb`文件。

## 应用文件

您的脸书应用程序的主文件是`app.rb`文件。如果你熟悉[辛纳特拉](http://www.sinatrarb.com/)(ruby source 的读者也应该熟悉，这要感谢达伦令人敬畏的[辛纳特拉系列](https://www.sitepoint.com/just-do-it-learn-sinatra-i/))，那么你一定会像老蓝眼睛一样哼唱。它只是一个 Sinatra 应用程序，所有的魔法都发生在根(“/”)处理程序中，我们一会儿就会谈到它。

从文件的顶部开始，有几个要求:

```
require 'sinatra'
require 'mogli'
```

我们知道为什么需要 Sinatra，但 mogli 是什么？嗯， [mogli](https://github.com/mmangino/mogli) 是一个宝石，声称是“Ruby 的脸书开放图形库”。这意味着，作为一名 Ruby 开发者，你可以完全访问应用程序中的[图形 API](http://developers.facebook.com/docs/reference/api/) 。很漂亮，是吧？Mogli 正在处理对脸书的 OAuth 认证(参见文件中的`/auth/facebook`处理程序)并提供对当前用户的访问(在“/”或根处理程序中)

```
@user = Mogli::User.find("me", @client)
```

以及所有用户的对象

```
@friends = @user.friends[0, 4]
@photos  = @user.photos[0, 16]
@likes   = @user.likes[0, 4]

# for other data you can always run fql
@friends_using_app = @client.fql_query("SELECT uid, name, is_app_user, pic_square FROM user WHERE uid in (SELECT uid2 FROM friend WHERE uid1 = me()) AND is_app_user = 1")
```

这是我们在视图中看到的实例变量。尼利斯。请特别注意最后一个变量`@friends_using_app`。它使用 [FQL](http://developers.facebook.com/docs/reference/fql/) ，所以脸书 API 的所有能力只是一个 FQL 字符串。

根处理程序的最后一行使用`views/index.erb`模板合并并创建视图:

```
erb :index
```

简单如 E-R-B。

## 访问用户字段

Stalker 要打印一个随机朋友的属性。为了好玩，我们会让它打印姓名、性别、家乡和生日。前两个(姓名和性别)不需要访问令牌，但后两个需要。我们需要告诉我们的应用程序。搜索`FACEBOOK_SCOPE`并将其更改为:

```
FACEBOOK_SCOPE = 'publish_actions,user_likes,user_photos,user_photo_video_tags,friends_hometown,friends_birthday'
```

我们将`friends_hometown,friends_birthday`添加到范围中，所以我们的应用程序现在会在用户第一次打开 Stalker 时提示用户，并询问 Stalker 是否可以看到家乡和生日。所有你可以询问的领域，以及如何询问，都可以在[这里](http://developers.facebook.com/docs/reference/api/user/)找到。

该常量用于身份验证处理程序:

```
get "/auth/facebook" do
  session[:at]=nil
  redirect authenticator.authorize_url(:scope => FACEBOOK_SCOPE, :display => 'page')
end
```

当应用程序被授权时。

## 成为跟踪者

现在我们知道了 app 的背景，让我们快速创建 Stalker。基本上，Stalker 将允许用户输入朋友的名字，然后显示朋友的一些属性和照片。这有点令人生厌，但不是令人毛骨悚然的方式(我希望如此)。我遵循了 Heroku 脸书开发者页面上的[本地工作](http://devcenter.Heroku.com/articles/facebook#working_locally)说明，我强烈建议你也这样做，以创建一个本地开发环境。

简而言之，您需要创建第二个应用程序(这次只是一个“网站”应用程序)并安装 Foreman gem ( `gem install foreman`)。env 文件，并从应用程序的根目录键入`foreman start`。Stalker 的本地开发版现在将在 [http://127.0.0.1](http://127.0.0.1) 运行。这仍然只是一个示例应用程序，但是我们离自己的应用程序只有几步之遥。

首先，我们将改变我们的根处理程序来创建一个`@friend`实例变量:

```
get "/" do
  redirect "/auth/facebook" unless session[:at]
  @client = Mogli::Client.new(session[:at])

  # limit queries to 15 results
  @client.default_params[:limit] = 15

  @app  = Mogli::Application.find(ENV["FACEBOOK_APP_ID"], @client)
  @user = Mogli::User.find("me", @client)
  # Randomly pick a friend
  @friend = @user.friends(:name, :gender, :hometown,:birthday).sort_by {rand}.first unless @friend
  erb :index
end
```

我们随便找了一个朋友，询问我们想要显示的字段。看我们问家乡和生日的地方？我们可以这样做，因为我们在授权应用程序时要求这些字段。

现在，修改`index.erb`文件。我只是包含了一部分更改，打印出了朋友的一些信息。

```
<h1>Is THIS what you wanna know, sicko?</h1>
<ul>
  <li><em>Name: </em> <%=@friend.name%></li>
  <li><em>Gender: </em> <%=@friend.gender%></li>
  <li><em>Hometown: </em> <%=@friend.hometown%></li>
  <li><em>Birthday: </em> <%=@friend.birthday%></li>
</ul>
</section>
```

你可能想改变或添加任何 CSS 样式到网站上，就像我一样。我把`base.css`里的各种背景图片拿出来，把顶部横幅的背景颜色改了一下。它不会赢得任何设计奖项，但像“跟踪者”这样的名字可能已经过时了。这是我的新`screen.css`:

```
@import url("https://statics.Herokuapp.com/fonts/fonts.css");
@import url("reset.css");
@import url("base.css");

a {
  color:#FFF;
}
#get_started a:visited {
  color:#fff;
}
```

这是正在开发的应用程序的图片:

![](img/2aee6e8540b55bf96e475fe48f128b9b.png)

我们现在要做的就是把这个推给 Heroku。这就是 Heroku 的闪光点，它将基础设施问题从开发者的道路上拉了出来。部署非常简单:

```
git commit -am "Basic Stalking in place. BWAAHAHAHA"
git push Heroku
```

现在，任何想随机跟踪他们在脸书的朋友的人都可以用你的可怕的跟踪应用程序这样做。你可以专注于为应用程序添加更多功能，因为你不必担心它如何托管，如何扩展，或者你会用你的数百万做什么。

## 开始吧。

希望你现在已经有了一个好主意，如何使用 Heroku 与脸书开发以及 Ruby 的新集成来打造你的社交应用开发者的名字！

很明显，比起 Stalker，你可以用脸书应用程序做更多的事情。我确信，在接下来的几周内，我们将会看到更多关于这种新整合的例子和帖子。我很想听听你对这个新热点的看法，以及你有什么问题。Heroku 的人很高兴让开发人员忙于脸书的开发，所以开始吧。

## 资源

[Heroku announcement](http://blog.heroku.com/archives/2011/9/15/facebook/)
[脸书 announcement](http://developers.facebook.com/blog/post/558/)
[Heroku:开始使用你的脸书 App](http://devcenter.heroku.com/articles/facebook)
[脸书开发者文档](http://developers.facebook.com/docs/)

**SitePoint Content Partner**

本教程是在 SFDC 的支持下完成的。我们与 SFDC 合作，由 SitePoint 独立撰写，努力共同开发对您最有用、最相关的内容。

## 分享这篇文章