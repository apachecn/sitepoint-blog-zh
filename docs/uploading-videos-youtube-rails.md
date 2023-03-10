# 使用 Rails 将视频上传到 YouTube

> 原文：<https://www.sitepoint.com/uploading-videos-youtube-rails/>

![Media Player App Icon](img/90d452bd07197e36a485da5f0eda1065.png)

在我之前的文章中，我已经解释了[如何在你的应用中集成上传](https://www.sitepoint.com/file-uploads-dragonfly/)和[如何使上传过程异步](https://www.sitepoint.com/asynchronous-file-uploads-rails/)(以及上传多个文件)。今天，我们将再向前迈出一步，创建一个允许用户将视频上传到 YouTube 的应用程序。

我们将利用 OAuth2 协议，以及`youtube_it` gem 与 YouTube API 一起工作(如果你想了解更多关于 YT API 的知识，可以看看我的文章， [YouTube on Rails](https://www.sitepoint.com/youtube-rails/) )。最后，我将介绍一些您可能会遇到的潜在问题(相信我，其中一些非常烦人)。

演示应用的源代码可以在 GitHub 上找到。

在 http://sitepoint-yt-uploader.herokuapp.com/可以找到一个工作演示。

我们开始吧！

## 准备

> 这个演示将使用 Rails 4.1.4，但是 Rails 3 也可以实现相同的解决方案。

在深入研究代码之前，我们先停下来讨论一下我们将要构建的应用程序。用户场景很简单:

1.  用户访问主页，看到已经上传的视频列表，以及鼓励用户上传视频的大按钮。

2.  用户(希望)点击这个按钮，并被带到另一个页面，在那里他们被要求进行身份验证(我们稍后将更详细地讨论身份验证)。

3.  认证成功后，会出现一个表单，允许用户从他们的硬盘中选择一个视频。用户也可以提供视频的标题和描述。

4.  提交表单后，视频被上传到 YouTube。我们的应用程序获得唯一的视频 id，并将其存储在数据库中(这个唯一的 id 将用于在主页上显示视频)。

5.  用户被重定向到主页，在那里他们可以看到新上传的视频。

基本上我们有三个任务要解决:
*做一些地面工作(创建控制器，模型，视图，一些基础设计)
*创建某种认证
*实现视频上传机制。

让我们从基础工作开始。创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new yt_uploder -T
```

我们将使用 Twitter Bootstrap 来构建我们的基本设计，所以将这个宝石放入您的**宝石文件**:

```
[...]
gem 'bootstrap-sass'
[...]
```

然后将 **application.css** (在**样式表**目录中)重命名为 **application.css.scss** ，并将其内容修改为:

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

更改布局以利用 Bootstrap 的样式:

*layouts/application . html . erb*

```
[...]
<body>
  <div class="navbar navbar-inverse">
    <div class="container">
      <div class="navbar-header">
        <%= link_to 'YT Uploader', root_path, class: 'navbar-brand' %>
      </div>
      <ul class="nav navbar-nav">
        <li><%= link_to 'Videos', root_path %></li>
        <li><%= link_to 'Add Video', new_video_path %></li>
      </ul>
    </div>
  </div>

  <div class="container">
    <% flash.each do |key, value| %>
      <div class="alert alert-<%= key %>">
        <%= value %>
      </div>
    <% end %>

    <%= yield %>
  </div>
</body>
[...]
```

我们正在使用一些不存在的路由，所以让我们来解决这个问题:

*routes.rb*

```
[...]
resources :videos, only: [:new, :index]
root to: 'videos#index'
[...]
```

同样，创建相应的控制器(我们将在后面创建它的方法):

*videos_controller.rb*

```
class VideosController < ApplicationController
  def new
  end

  def index
  end
end
```

最后，创建视图(我们稍后将充实它们):

*videos/new.html.erb*

```
<div class="page-header">
  <h1>Upload Video</h1>
</div>
```

*videos/index.html.erb*

```
<div class="jumbotron">
  <h1>YouTube Uploader</h1>
  <p>Upload your video to YouTube and share it with the world!</p>
  <p><%= link_to 'Upload video now!', new_video_path, class: 'btn btn-primary btn-lg' %></p>
</div>
```

好了，现在我们有东西可以用了。

## 认证用户

此时，我们已经准备好考虑身份验证了。显然，为了能够将视频上传到用户的频道，我们需要访问权限。我们如何才能做到这一点？

Google API 支持 OAuth 2 协议，这使得第三方应用程序能够获得对其服务的有限访问(你可以在这里阅读更多关于使用 OAuth 2 访问 Google API 的信息)。

这个协议很酷的一点是，用户没有授权第三方应用程序访问他的整个帐户。相反，它只能访问所需的服务(这由*范围*控制)。此外，第三方应用程序永远不会收到用户的密码——相反，它会获得用于发出 API 调用的*令牌*。此令牌只能用于执行访问范围中列出的操作，并且具有有限的使用期限。此外，用户可以从帐户设置页面手动撤销对任何先前授权的第三方应用程序的访问。OAuth 2 是一个非常受欢迎的协议，受到各种网站的支持(如 Twitter、脸书、Vkontakte 和许多其他网站)。

对于 Rails，有 Josh Ellithorpe 创建的 omniauth-google-oauth2 gem。它提出了一个使用 OAuth2 和 OmniAuth 向 Google 认证的*策略*。 [OmniAuth](https://github.com/intridea/omniauth) 反过来是一个库，它标准化了由 Michael Bleigh 和 Intridea Inc .创建的 web 应用程序的多提供商身份验证。OmniAuth 允许您使用任意多的不同身份验证策略，以便用户可以通过 Google、脸书、Twitter 等进行身份验证。你只需要挂上相应的策略(有很多可用的)或者[创建你自己的](https://github.com/intridea/omniauth/wiki/Strategy-Contribution-Guide)。

好吧，我们怎么把这些联系起来？其实挺容易的。首先，将需要的宝石添加到**宝石文件**中:

*Gemfile*

```
[...]
gem 'omniauth-google-oauth2'
[...]
```

现在创建几条路线:

*routes.rb*

```
get '/auth/:provider/callback', to: 'sessions#create'
get '/auth/failure', to: 'sessions#fail'
```

`/auth/:provider/callback`路由是在成功的
认证之后用户被重定向的回调。`/auth`部分由 OmniAuth 定义。在我们的例子中，`:provider`是`google_oauth2`(如 [omniauth-google-oauth2 的文档](https://github.com/zquestz/omniauth-google-oauth2#usage)所述)。在一个有许多不同策略的应用程序中，`:provider`会取其他值(比如`twitter`或`facebook`)。`/auth/failure`，正如您可能猜到的，在认证阶段出现错误时使用。

现在，设置实际的 OmniAuth 策略:

*initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET'], scope: 'userinfo.profile,youtube'
end
```

我们在这里使用的是`google_oauth2`策略。通过使用 Google 控制台注册您的应用程序，可以获得客户端 id 和客户端密码。访问(console . developers . Google . com)[https://console . developers . Google . com/]，点击“创建项目”，输入你的项目名称。创建完成后，打开“同意屏幕”页面(从左侧菜单)并填写“产品名称”(我称之为“Sitepoint YT 上传演示”)——这是您的用户通过应用程序进行身份验证时会看到的内容。所有其他字段都是可选的。务必点击“保存”。

现在打开“API”页面。您可以在这里设置您的应用程序将访问的 API。启用 Google+ API 和 YouTube 数据 API v3。打开“凭据”页面，然后单击“创建新的客户端 ID”按钮。在弹出窗口中选择“Web 应用程序”。对于“授权的 JavaScript 源”，输入您的应用程序的 URL(对于这个演示，我输入了“http://site point-yt-uploader . heroku app . com”)。

在“授权重定向 URI”中，输入你的应用的 URL 加上`/auth/google_oauth2/callback`(还记得我们的 routes 文件和回拨路由`/auth/:provider/callback`？).对于这个演示，我输入了`http://sitepoint-yt-uploader.herokuapp.com/auth/google_oauth2/callback`。单击“创建客户端 ID ”,您将看到一个新创建的 web 应用程序客户端 ID。这里请注意两个值:客户端 ID 和客户端机密。这些是您需要粘贴到`omniauth.rb`初始化器中的值。

那`scope`呢？正如我提到的，`scope`列出了应用程序需要的服务。所有可能的值都可以在谷歌的 OAuth 2 平台上找到。还有其他选项可以传递给`provider`方法。整个列表可以在 omniauth-google-oauth2 的页面上找到。

这里我们要做的最后一件事是捕捉身份验证期间发生的任何错误，并将用户重定向到之前创建的`/auth/failure`路由:

*initializer/omni auth . Rb*

```
[...]
OmniAuth.config.on_failure do |env|
  error_type = env['omniauth.error.type']
  new_path = "#{env['SCRIPT_NAME']}#{OmniAuth.config.path_prefix}/failure?message=#{error_type}"
  [301, {'Location' => new_path, 'Content-Type' => 'text/html'}, []]
end
[...]
```

唷，我们到此为止了！

向用户展示实际的身份验证链接:

*videos/new.html.erb*

```
<div class="page-header">
  <h1>Upload Video</h1>
</div>

<p>Please <%= link_to 'authorize', '/auth/google_oauth2' %> via your Google account to continue.</p>
```

点击此链接后，用户将被重定向到 Google 的身份验证页面进行登录。谷歌将审查我们的应用程序请求的权限列表(在我们的演示中，这些权限将访问基本帐户信息和管理 YouTube 帐户)。

批准这个请求后，用户被重定向回我们的应用程序(到我们在 Google 控制台中设置的回调路径)。我们的回调方法还获取身份验证散列数据，根据策略和请求的权限，这些数据可能包含各种信息。在我们的例子中，它包含用户的姓名、照片、配置文件 URL、令牌和一些其他数据(在这里看一下示例 auth hash [和](https://github.com/zquestz/omniauth-google-oauth2#auth-hash))。

我们需要一个控制器来处理这个回调:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    auth = request.env['omniauth.auth']
    user = User.find_or_initialize_by(uid: auth['uid'])
    user.token = auth['credentials']['token']
    user.name = auth['info']['name']
    user.save
    session[:user_id] = user.id
    flash[:success] = "Welcome, #{user.name}!"
    redirect_to new_video_path
  end

  def fail
    render text: "Sorry, but the following error has occured: #{params[:message]}. Please try again or contact
administrator."
  end
end
```

`request.env['omniauth.auth']`包含认证散列。我们需要的三个值是用户的惟一 id(这样当他再次进行身份验证时，我们可以在表中找到这个用户)、用户的全名，以及最重要的向 Google API 发出请求的令牌。此外，我们将用户的 id 存储在会话中，以便在我们的应用程序中对他进行身份验证。

我们还需要一种方法来检查用户是否经过身份验证:

*application _ controller . Rb*

```
[...]
private

def current_user
  @current_user ||= User.find_by(id: session[:user_id]) if session[:user_id]
end

helper_method :current_user
[...]
```

这种方法现在可以在视图中使用:

*videos/new.html.erb*

```
<div class="page-header">
  <h1>Upload Video</h1>
</div>

<% if current_user %>
  <%# TODO: create form partial %>
<% else %>
  <p>Please <%= link_to 'authorize', '/auth/google_oauth2' %> via your Google account to continue.</p>
<% end %>
```

我们还没有创建`User`模型，所以现在让我们开始吧:

```
$ rails g model User name:string token:string uid:string
$ rake db:migrate
```

太好了！此时，我们的用户可以通过 Google 进行身份验证，并向我们的应用程序提供访问令牌来发出 API 请求。下一步是实现视频上传功能。

## 上传视频到 YouTube

首先，我们需要一个`Video`模型。它将包含以下字段(更不用说默认的`id`、
、`updated_at`):

*   `title`(`string`)——由用户提供
*   `description`(`text`)——由用户提供
*   `uid`(`string`)–将包含我们之前讨论过的视频的唯一标识符。这个 id 是 YouTube 在上传视频时生成的。我们将使用这个 id 通过 YouTube API 获取一些信息，并生成链接。
*   `user_id`(`integer`)–将视频与用户关联的关键字段(一个用户可以有多个视频)。

创建并应用迁移:

```
$ rails g model Video uid:string title:string description:text user:references
$ rake db:migrate
```

不要忘记在用户端建立一对多关系:

*型号/用户. rb*

```
[...]
has_many :videos
[...]
```

现在，我们准备继续视频的形式:

*videos/new.html.erb*

```
<div class="page-header">
 <h1>Upload Video</h1>
</div>

<% if current_user %>
 <%= render 'form' %>
<% else %>
 <p>Please <%= link_to 'authorize', '/auth/google_oauth2' %> via your Google account to continue.</p>
<% end %>
```

不幸的是，事情变得有点复杂。似乎我们需要创建不止一个，而是两个表单来上传视频到 YouTube。这是为什么呢？

YouTube API 要求我们首先发送视频数据，如标题和描述，而不发送实际的视频文件。作为回报，它用一个特殊的上传令牌和 URL 来响应。然后，我们必须向这个 URL 提交另一个包含令牌和文件的表单。很乱，不是吗？

当然，我们可以要求用户提交两个表单，但是我们不会这样做。用户不应该被这样的复杂性所困扰。让我们使用一点 AJAX 来代替。我将一步一步地进行，这样你就明白发生了什么。

稍微调整一下控制器:

*videos_controller.rb*

```
[...]
def new
  @pre_upload_info = {}
end
[...]
```

这个`@pre_upload_info`散列将存储视频的信息——标题和描述。我们将在第一种形式中使用它。

现在，在分部中创建两个表单:

*videos/_form.html.erb*

```
<%= form_tag '', id: 'video_pre_upload' do %>
  <div class="form-group">
    <%= text_field_tag :title, @pre_upload_info[:title], required: true,
                       placeholder: 'Title', class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= text_area_tag :description, @pre_upload_info[:description], required: true,
                      placeholder: 'Description', class: 'form-control' %>
  </div>
<% end %>

<%= form_tag '', id: 'video_upload', multipart: true do %>
  <%= hidden_field_tag :token, '' %>

  <div class="form-group">
    <%= file_field_tag :file, required: true %>
  </div>
<% end %>

<button id="submit_pre_upload_form" class="btn btn-lg btn-primary">Upload</button>
<%= image_tag 'ajax-loader.gif', class: 'preloader', alt: 'Uploading...', title: 'Uploading...' %>
```

正如你所看到的，只有一个按钮，我们马上会用 JavaScript 来装备它。没有一个表单有动作 URL——它也将来自 JavaScript。第一个表单(`#video_pre_upload`)将首先被发送，以获得第二个(`#video_upload`)表单的上传令牌和 URL。

我们还使用一个从[ajaxload.info/](http://ajaxload.info/)生成的 AJAX loader 图像，或者取一个已经生成的的[。页面加载时不应显示加载器，因此应用以下样式:](https://github.com/bodrovis/SitePoint-YT-Uploader/blob/master/app/asseimg/ajax-loader.gif)

*样式表/application.css.scss*

```
.preloader {
  display: none;
}
```

我们还使用占位符作为输入。旧的浏览器不支持它们，所以你可以使用 Mathias Bynens 的 [jquery-placeholder](https://github.com/mathiasbynens/jquery-placeholder) 插件来向后兼容。

此外，如果你正在使用 Turbolinks，在你的应用程序中添加 jquery-turbo links gem 可能是个好主意。

好了，现在是进行 jQuery 的时候了:

*videos/_form.html.erb*

```
<script>
  $(document).ready(function() {
    var submit_button = $('#submit_pre_upload_form');
    var video_upload = $('#video_upload');
    submit_button.click(function () {
      $.ajax({
        type: 'POST',
        url: '<%= get_upload_token_path %>',
        data: $('#video_pre_upload').serialize(),
        dataType: 'json',
        success: function(response) {
          video_upload.find('#token').val(response.token);
          video_upload.attr('action', response.url.replace(/^http:/i, window.location.protocol)).submit();
          submit_button.unbind('click').hide();
          $('.preloader').css('display', 'block');
        }
      });
    });
  });
</script>
```

我们将一个`click`事件处理程序绑定到按钮上。当点击这个按钮时，将第一个表单提交给`get_upload_token` route(我们稍后将创建它),期望得到 JSON 的响应。

响应成功后，获取令牌和 URL，并分别为`#token`和`#video_upload`的动作设置表单值。另外，请注意`response.url.replace(/^http:/i, window.location.protocol)`。这是可选的，但是如果您的页面可以通过`http`和`https`协议访问，您将需要这段代码。这是因为 YouTube API 返回的 URL 使用 http，而现代浏览器会阻止使用`https`发送表单。

提交表单后，隐藏按钮并向用户显示 PacMan (loader)。至此，我们完成了视图，可以继续学习路由和控制器了。

创建两条新路线:

*routes.rb*

```
[...]
post '/videos/get_upload_token', to: 'videos#get_upload_token', as: :get_upload_token
get '/videos/get_video_uid', to: 'videos#get_video_uid', as: :get_video_uid
[...]
```

第一个–`get_upload_token`–在发送第一个表单时用于我们的 partial 中。第二条路线将在稍后使用。

在控制器上:

*videos_controller.rb*

```
[...]
def get_upload_token
  temp_params = { title: params[:title], description: params[:description], category: 'Education',
                  keywords: [] }

  if current_user
    youtube = YouTubeIt::OAuth2Client.new(client_access_token: current_user.token,
                                          dev_key: ENV['GOOGLE_DEV_KEY'])

    upload_info = youtube.upload_token(temp_params, get_video_uid_url)

    render json: {token: upload_info[:token], url: upload_info[:url]}
  else
    render json: {error_type: 'Not authorized.', status: :unprocessable_entity}
  end
end
[...]
```

这是请求上传令牌和 URL 的方法。注意，在`temp_params`散列中，我们可以提供一些
更多的视频信息:类别和关键字。对于这个演示，我使用“教育”类别，但在一个真正的应用程序中，您可能会向用户提供一个下拉列表来选择一个可用的类别。

这里是 [youtube_it](https://github.com/kylejginavan/youtube_it) 宝石发挥作用的地方，所以让我们把它放到**宝石文件**中:

*Gemfile*

```
[...]
gem 'youtube_it', github: 'bodrovis/youtube_it'
[...]
```

*我正在使用这个宝石的分叉版本，因为`2.4.0`已经将`json`锁定到一个旧版本。*

当请求上传令牌时，我们需要传递开发者密钥。也可以使用[谷歌控制台](http://console.developers.google.com)获得该密钥。打开我们在上一节中创建的项目，导航到“APIs&Auth”-“Credentials”，然后单击“Create new key”。在弹出窗口中，选择“浏览器密钥”，填写“接受来自这些 HTTP 推荐人的请求”，然后单击“创建”。新生成的“API 密钥”就是您要寻找的。

在`youtube_it`的帮助下，我们请求令牌和 URL，向客户端发送 JSON 响应。

还要注意，在`upload_token`方法中，我们提供了`get_video_uid_url`–这是一个回调 URL，用户在视频上传后将被重定向到这个 URL。正如您所记得的，我们已经创建了这条路线，所以我们只需要添加一个相应的方法:

*videos_controller.rb*

```
[...]
def get_video_uid
  video_uid = params[:id]
  v = current_user.videos.build(uid: video_uid)
  youtube = YouTubeIt::OAuth2Client.new(dev_key: ENV['GOOGLE_DEV_KEY'])
  yt_video = youtube.video_by(video_uid)
  v.title = yt_video.title
  v.description = yt_video.description
  v.save
  flash[:success] = 'Thanks for sharing your video!'
  redirect_to root_url
end
[...]
```

包含视频的唯一 id 的 GET 参数`id`被添加到存储在数据库中的回调 URL 中。我们也可以在这里获取其他视频信息，如标题，描述，持续时间等。(在我的 [YouTube on Rails 文章](https://www.sitepoint.com/youtube-rails/)中了解更多信息)。

酷！剩下一小块:页面——我们需要显示所有上传的视频。这是最简单的部分。

*videos_controller.rb*

```
[...]
def index
  @videos = Video.all
end
[...]
```

*videos/index.html.erb*

```
<div class="jumbotron">
  <h1>YouTube Uploader</h1>
  <p>Upload your video to YouTube and share it with the world!</p>
  <p><%= link_to 'Upload video now!', new_video_path, class: 'btn btn-primary btn-lg' %></p>
</div>

<div class="page-header">
  <h1>Videos</h1>
</div>

<ul class="row" id="videos-list">
  <% @videos.each do |video| %>
    <li class="col-sm-3">
      <div class="thumbnail">
        <%= link_to image_tag("https://img.youtube.com/vi/#{video.uid}/mqdefault.jpg", alt: video.title,
                              class: 'img-rounded'),
                    "https://www.youtube.com/watch?v=#{video.uid}", target: '_blank' %>

        <div class="caption">
          <h4><%= video.title %></h4>
          <p><%= truncate video.description, length: 100 %></p>
        </div>
      </div>
    </li>
  <% end %>
</ul>
```

我们使用`image_tag("https://img.youtube.com/vi/#{video.uid}/mqdefault.jpg")`生成视频的缩略图，使用`"https://www.youtube.com/watch?v=#{video.uid}"`生成观看视频的链接。如你所见，视频的 uid 非常方便。

一些风格:

*样式表/application.css.scss*

```
[...]
#videos-list {
  margin: 0;
  padding: 0;
  li {
    padding: 0;
    list-style-type: none;
    word-wrap: break-word;
    .caption {
      text-align: center;
    }
  }
}
```

就是这样！用你闪亮的新应用上传一些视频吧！

## 附注:有几个问题

您应该意识到两个问题。第一个与认证有关。当一个没有在他们的频道中启用 YouTube 的用户试图通过您的应用程序(请求访问 YouTube API)进行身份验证时，将会返回一个错误(幸运的是，我们有一个机制来捕捉这些错误)。因此，您可能需要检查这个错误，并指示您的用户访问 www.youtube.com，创建一个频道，然后再次尝试进行身份验证。

第二个和上传有关。如你所知，YouTube 上传后需要一段时间来分析视频，生成缩略图；视频越长，需要分析的时间就越多。因此，就在用户上传视频后，默认缩略图将被用于主页。这个问题至少有两种解决方案:

1.  你可以在“新视频”页面上警告用户这个事实，解释缩略图将在大约 3-5 分钟后出现。
2.  您可以创建某种类型的后台进程，该进程将定期尝试访问缩略图，并仅在缩略图可用时在主页上显示视频(再次提醒用户这一事实)。

## 结论

最后，我们已经到了这篇文章的结尾。有很多话要说，是吧？你曾经尝试过在你的应用中实现类似的功能吗？你是怎么解决这个任务的？请在评论中分享你的想法！

感谢您的阅读，希望很快再次见到您！

## 分享这篇文章