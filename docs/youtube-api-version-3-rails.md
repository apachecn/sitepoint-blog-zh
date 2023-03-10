# YouTube API，Rails 上的版本 3

> 原文：<https://www.sitepoint.com/youtube-api-version-3-rails/>

![Old YouTube Icon](img/6e845e01e830b5cf69aca2fe33fef924.png)

不久前，我写了一篇关于在 Rails 上使用 [YouTube 的文章，解释了与 YouTube API 交互的基础知识。上一篇文章介绍了如何获取视频信息并使用 YouTube IFrame API 来操作视频播放器。后来，另一篇文章](https://www.sitepoint.com/youtube-rails/)[用 Rails](https://www.sitepoint.com/uploading-videos-youtube-rails/) 上传视频到 YouTube，展示了如何创建一个允许用户直接上传视频到 YouTube 的应用。

两次演示都使用了 Youtube_it gem。我的两个帖子都得到了不少评论，对此我表示感谢。

`youtube_it`是一个伟大的宝石，然而，它采用了 YouTube API 的版本 2，现在[正式否决了](https://developers.google.com/youtube/2.0/developers_guide_protocol_audience)，并且在[2015 年 4 月 20 日](http://apiblog.youtube.com/2014/09/have-you-migrated-to-data-api-v3-yet.html)之后将不再被支持。哎哟。

幸运的是，Claudiofullscreen 利用 YouTube API 的第 3 版创建了一个名为 [yt](https://github.com/Fullscreen/yt) (现在是**的简称**)的新 gem，扭转了局面。

在本文中，我们将构建一个类似于“YouTube on Rails”和“用 Rails 上传视频到 YouTube”帖子中介绍的应用程序，但是让它与 YouTube API 的版本 3 一起工作。

工作演示可在[sitepoint-ytv3.herokuapp.com](https://sitepoint-ytv3.herokuapp.com/)获得。

源代码可以在 [GitHub](https://github.com/bodrovis/YTv3) 获得。

## V3 的变化

v3 中有一些值得注意的变化:

*   [**认证**](https://developers.google.com/youtube/registering_an_application) 。API v2 允许通过 OAuth、OAuth 2、AuthSub 或 Developer Key 进行认证(以执行只读请求)，而 API v3 只支持 [OAuth 2](https://developers.google.com/youtube/v3/guides/authentication) 。使用 OAuth 2，您可以访问用户的私有数据，并通过 API 操纵它。也支持不需要用户验证的只读访问。您需要提供一个 API 键来标识您的项目，稍后我会展示给您看。
*   **抓取视频**。不再支持通过标签获取视频和查找链接最多的视频。带有布尔运算符的高级查询也被删除。
*   **你没有更多的评论**。至少现在，由于 YouTube 正在[重新工作它的评论系统](https://developers.google.com/youtube/2.0/developers_guide_protocol_comments)，所以评论现在不是 API 的一部分。这意味着您不能再列出或管理视频评论。
*   **视频响应被停用**。撕开。正如[这个公告](http://youtubecreator.blogspot.ru/2013/08/so-long-video-responsesnext-up-better.html)所说，视频回复被使用了大约 0.0004%的时间，所以 YouTube 团队决定删除它。
*   **访问控制列表**。此功能的大部分已被删除；唯一剩下的是`embeddable`，然而[有报道](https://code.google.com/p/gdata-issues/issues/detail?id=4861)说它不工作了。

可以参考[迁移指南](https://developers.google.com/youtube/v3/migration-guide)了解更多。对了，对于那些从 *youtube_it* gem 迁移到 *yt* 的人，有一个[特别指南](https://github.com/Fullscreen/yt/blob/master/YOUTUBE_IT.md)。

## 准备应用程序

我要建立一个应用程序，将提供以下功能:

*   用户应该能够添加他们的视频已经存在于 YouTube 上。
*   视频应该显示在网站的主页上，以及一些基本信息(如标题)。
*   用户应该能够通过应用程序将他们的视频上传到 YouTube。上传的视频也应该保存在应用程序的数据库中。

在本指南中，我将坚持使用 Rails 4.2.0，但同样的解决方案(稍作修改)也可以在 Rails 3 和 4.1 上实现。

首先创建一个没有默认测试套件的新应用程序:

```
$ rails new YtVideosV3 -T
```

将以下宝石放入您的宝石档案中:

*Gemfile*

```
[...]
gem 'yt', '~> 0.13.7'
gem 'bootstrap-sass', '~> 3.3.0.1'
gem 'autoprefixer-rails'
[...]
```

这里的主星是 *yt* 。我使用 Bootstrap 进行样式设计，但这不是必需的。 [autoprefixer-rails](https://github.com/ai/autoprefixer-rails) 推荐与 Bootstrap 一起使用，自动添加浏览器厂商前缀。

别忘了跑步

```
$ bundle install
```

连接引导程序:

*application.scss*

```
@import "bootstrap-sprockets";
@import "bootstrap";
@import 'bootstrap/theme';
```

好的，现在稍微调整一下布局:

*layouts/application . html . erb*

```
[...]
<div class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'YT APIv3', root_path, class: 'navbar-brand' %>
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
</div>
[...]
```

接下来，继续使用名为`Video`的模型，它将存储用户的视频。它将包含以下属性:

*   `link`(`string`)–YouTube 上的视频链接
*   `uid`(`string`)–YouTube 呈现的视频的唯一标识符。在这里添加一个数据库索引是一个好主意
*   `title`(`string`)–视频的标题
*   `published_at`(`datetime`)–视频在 YT 上发布的日期
*   `likes`(`integer`)–为视频点赞
*   `dislikes`(`integer`)–不喜欢的视频也算

创建并应用相应的迁移:

```
$ rails g model Video link:string title:string published_at:datetime likes:integer dislikes:integer uid:string:index
$ rake db:migrate
```

不要忘记设置路线:

*config/routes.rb*

```
[...]
resources :videos, only: [:index, :new, :create]
root to: 'videos#index'
[...]
```

创建控制器:

*videos_controller.rb*

```
class VideosController < ApplicationController
  def index
    @videos = Video.order('created_at DESC')
  end

  def new
    @video = Video.new
  end

  def create
  end
end
```

在`index`页面，显示用户添加的所有视频。还会有一个`new`页面，呈现一个添加新视频的表单。`create`行动将在下一部分中被充实。

最后，用一个按钮创建一个`index`视图:

*views/videos/index . html . erb*

```
<div class="jumbotron">
  <div class="container">
    <h1>Share your videos with the world!</h1>
    <p>Click the button below to share your video from YouTube.</p>
    <p>
      <%= link_to 'Add video now!', new_video_path, class: 'btn btn-primary btn-lg' %>
    </p>
  </div>
</div>
```

## 从 YouTube 添加视频

下一步是创建一个表单来添加以前上传到 YouTube 的视频，也就是说，在我们的应用程序之外。我们唯一需要知道的是用户希望添加的视频链接。关于它的所有其他信息将使用 YouTube API 找到。因此，形式非常简单:

*views/videos/new . html . erb*

```
<div class="container">
  <h1>New video</h1>

  <%= form_for @video do |f| %>
    <%= render 'shared/errors', object: @video %>

    <div class="form-group">
      <%= f.label :link %>
      <%= f.text_field :link, class: 'form-control', required: true %>
      <span class="help-block">A link to the video on YouTube.</span>
    </div>

    <%= f.submit class: 'btn btn-primary' %>
  <% end %>
</div>
```

这里使用了`shared/_errors.html.erb`部分:

*views/shared/_ errors . html . erb*

```
<% if object.errors.any? %>
  <div class="panel panel-danger">
    <div class="panel-heading">
      <h3 class="panel-title">The following errors were found while submitting the form:</h3>
    </div>

    <div class="panel-body">
      <ul>
        <% object.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
      </ul>
    </div>
  </div>
<% end %>
```

现在，`create`行动:

*videos_controller.rb*

```
[...]
def create
  @video = Video.new(video_params)
  if @video.save
    flash[:success] = 'Video added!'
    redirect_to root_url
  else
    render :new
  end
end

private

def video_params
  params.require(:video).permit(:link)
end
[...]
```

非常简单。我们还要设置验证，使用户不能输入无效链接:

*models/video.rb*

```
class Video < ActiveRecord::Base
  YT_LINK_FORMAT = /\A.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/i

  validates :link, presence: true, format: YT_LINK_FORMAT
end
```

### 设置 YT API

在继续之前，我们必须配置 yt gem，以便它可以与 YouTube API 通信。首先，导航到 [Google 开发者控制台](https://console.developers.google.com/project)并创建一个新的应用程序。您可以随意称呼它，但是请记住，当通过 OAuth 2 进行身份验证时，用户将会看到这个名称。接下来，导航到同意屏幕页面(API&认证部分),并提供关于您的应用程序的基本信息。

打开**API**页面并启用以下功能:

*   Google+ API
*   YouTube 分析 API
*   YouTube 数据 API v3

如果您忘记启用其中的一些，那么在尝试与 YT API 通信时将会产生错误。还要注意，API 有使用配额，所以要注意向 API 发送了多少。

最后一步是获取公共 API 请求的服务器密钥，因为目前我们不需要任何用户交互，只需要执行基本的操作。导航到**凭证**并点击“公共 API”访问部分中的“创建新密钥”。然后选择服务器密钥并输入您的服务器的 IP 地址，以便请求不能从其他 IP 发送。

如果您不确定要输入哪个 IP，只需暂时将该字段留空(这实际上意味着
任何 IP 都可以使用提供的服务器密钥发送请求)——毕竟我们正在构建一个演示应用程序。最后，单击“Create”——将添加一个用于服务器应用程序的新密钥。API 键值是我们所需要的。

创建一个初始化文件来设置 yt:

*config/initializer/yt . Rb*

```
Yt.configure do |config|
  config.api_key = 'your_server_key'
end
```

这个 API 密匙应该保持安全——我使用一个环境变量来存储它。

yt 现在已经配置好了，可以发出 API 请求来获取基本信息，比如视频的标题或发布日期。

### 查询 YT API

在将视频保存到数据库中之前，应该从 YouTube 加载有关它的信息。在“YouTube on Rails”这篇文章中，我使用了一个`before_create`回调函数来获取所有需要的信息，但是有一位读者注意到 observer 也可以用于这项任务，所以让我们试试看。

在 Rails 4 中，观察者不再是框架核心的一部分，所以我们需要一个单独的 gem 来将它们带回来:

*Gemfile*

```
[...]
gem 'rails-observers'
[...]
```

奔跑

```
$ bundle install
```

并像这样调整应用程序的配置

*配置/应用. rb*

```
[...]
config.active_record.observers = :video_observer
[...]
```

注册新的观察员。

在*模型*目录中创建观察者:

*models/video_observer.rb*

```
class VideoObserver < ActiveRecord::Observer
  def before_save(resource)
    video = Yt::Video.new url: resource.link
    resource.uid = video.id
    resource.title = video.title
    resource.likes = video.like_count
    resource.dislikes = video.dislike_count
    resource.published_at = video.published_at
  rescue Yt::Errors::NoItems
    resource.title = ''
  end
end
```

`before_save`方法只会在记录保存之前运行。此方法接受资源作为参数。在这个方法中，我使用`Yt::Video.new`通过 API 的 URL 获取指定的视频。然后，我们简单地使用 yt 的方法来获得所有必要的信息。

我也从`Yt::Errors::NoItems`错误中解救出来——当请求的视频找不到时就会发生。

仅此而已！你可以继续添加一些你选择的视频来检查是否一切正常。

### 显示视频

让我们花几分钟时间修改一下`index`页面，这样就可以播放视频了。使用以下布局:

*views/videos/index . html . erb*

```
<div class="jumbotron">
  <div class="container">
    <h1>Share your videos with the world!</h1>
    <p>Click the button below to share your video from YouTube.</p>
    <p>
      <%= link_to 'Add video now!', new_video_path, class: 'btn btn-primary btn-lg' %>
    </p>
  </div>
</div>

<% if @videos.any? %>
  <div class="container">
    <h1>Latest videos</h1>
    <div id="player-wrapper"></div>
    <% @videos.in_groups_of(3) do |group| %>
      <div class="row">
        <% group.each do |video| %>
          <% if video %>
            <div class="col-md-4">
              <div class="yt_video thumbnail">
                <%= link_to image_tag("https://img.youtube.com/vi/#{video.uid}/mqdefault.jpg", alt: video.title,
                                      class: 'img-rounded'),
                            "https://www.youtube.com/watch?v=#{video.uid}", target: '_blank' %>
                <div class="caption">
                  <h5><%= video.title %></h5>
                  <p>Published at <%= video.published_at.strftime('%-d %B %Y %H:%M:%S') %></p>
                  <p>
                    <span class="glyphicon glyphicon glyphicon-thumbs-up"></span> <%= video.likes %>
                    <span class="glyphicon glyphicon glyphicon-thumbs-down"></span> <%= video.dislikes %>
                  </p>
                </div>
              </div>
            </div>
          <% end %>
        <% end %>
      </div>
    <% end %>
  </div>
<% end %>
```

是一个 Rails 方法，它将视频数组分成 3 个元素的组。然后我们迭代每个组来渲染它们。请注意`if video`条件——这是必需的，因为如果数组中有 5 个元素，并将它们分成 3 个一组，那么最后一个元素将被设置为`nil`。

对于每个视频，显示它的缩略图，这是 YouTube 为我们生成的。*mqdefault.jpg*意味着我们要获取一张 320×180 的图片，图片上下没有黑色条纹。还有一张【hqdefault.jpg】*(480×360 图像，图片上下有黑色条纹)和 *< 1，2，3 >。jpg* (120×90 图像，场景与视频不同，图片上下有黑色条纹)。*

每个缩略图充当 YouTube 上视频的链接。在“YouTube on Rails”一文中，我展示了如何实现 YouTube IFrame API，以便将播放器添加到您的页面中(并操作它)。这个过程没有任何改变，所以不需要在这里重复相同的代码。有关更多信息，请浏览该文章中显示视频的*部分。*

## 上传视频到 YouTube

现在，用户可以轻松地将他们的视频添加到我们的应用程序中。如果有人有尚未上传到 YouTube 的视频怎么办？我们应该指导这个人首先使用 YT 视频管理器上传文件，然后使用我们应用程序的界面来共享视频吗？如果用户可以直接通过我们的应用程序上传视频，那就更方便了。

### 通过 Google+认证

首先，我们需要一个认证系统。您可能还记得，YT APIv3 只允许 OAuth 2 协议进行身份验证，该协议要求我们传递一个特殊的令牌，该令牌是在用户通过我们的应用程序登录时生成的。

我们将使用为 [OmniAuth](https://github.com/intridea/omniauth) 提供 Google OAuth 2 策略的 [omniauth-google-oauth2](https://github.com/zquestz/omniauth-google-oauth2) 。

将新宝石添加到*宝石文件*中:

*Gemfile*

```
[...]
gem 'omniauth-google-oauth2'
[...]
```

然后跑

```
$ bundle install
```

创建一个初始化器，它将包含我们的认证策略的设置:

*config/initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :google_oauth2, 'YT_CLIENT_ID', 'YT_CLIENT_SECRET', scope: 'userinfo.profile,youtube'
end
```

我们正在注册一项名为`google_oauth2`的新战略。`YT_CLIENT_ID`和`YT_CLIENT_SECRET`可以通过[谷歌开发者控制台](https://console.developers.google.com/project)获得，我们几分钟前用过。回到那里，打开您之前创建的应用程序，并导航到**凭证**。单击“创建新的客户端 ID”按钮，然后选择“Web 应用程序”。将您站点的 URL 放在“授权的 JavaScript”origins 字段中(如果在开发人员的机器上工作，请使用“http://127.0.0.1:3000”)。

对于授权重定向 URIs，提供站点 URL 加“/auth/google_oauth2/callback”(例如
“http://127 . 0 . 0 . 1:3000/auth/Google _ oauth 2/callback”)。将为 web 应用程序创建一个新的客户端 ID。客户端 ID 和客户端密码字段是您想要的。同样，那些键**不应该**出现在你的版本控制系统中。

我们策略的最后一个参数是`scope`，它指定了应用程序想要执行的动作。`userinfo.profile`意味着我们希望能够获取用户账户的基本信息(如姓名、唯一标识符等)。`youtube`意味着该应用程序将能够管理用户的 YouTube 帐户(因为我们需要能够上传新视频)。

再添加几条路线:

*config/routes.rb*

```
[...]
get '/auth/:provider/callback', to: 'sessions#create'
delete '/logout', to: 'sessions#destroy', as: :logout
[...]
```

第一个是回调路由，用户在成功认证后被重定向。第二条路线将用于注销。

我们需要在某个地方存储用户的数据，因此需要一个新表。姑且称之为`users`。目前，它将包含以下字段:

*   `name`(`string`)–用户的姓名(可能带有姓氏)。
*   `token`(`string`)–执行 API 请求的令牌。
*   `uid`(`string`)–用户的唯一标识符。我们将在这里添加一个具有唯一性约束的索引。

创建迁移:

```
$ rails g model User name:string token:string uid:string
```

并在`create_table`方法后添加这一行:

*xxx_create_users.rb*

```
[...]
create_table :users do |t|
  [...]
end
add_index :users, :uid, unique: true
[...]
```

我们还需要一个具有两个动作的新控制器:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    user = User.from_omniauth(request.env['omniauth.auth'])
    session[:user_id] = user.id
    flash[:success] = "Welcome, #{user.name}"
    redirect_to root_url
  end

  def destroy
    session[:user_id] = nil
    flash[:success] = "Goodbye!"
    redirect_to root_url
  end
end
```

`request.env['omniauth.auth']`包含服务器发送给我们的应用程序的所有信息(它被称为“认证散列”)。现在，让我们创建`from_omniauth`方法:

*型号/用户. rb*

```
class User < ActiveRecord::Base
  class << self
    def from_omniauth(auth)
      user = User.find_or_initialize_by(uid: auth['uid'])
      user.name = auth['info']['name']
      user.token = auth['credentials']['token']
      user.save!
      user
    end
  end
end
```

这里使用了`find_or_initialize_by`方法。它试图用所提供的`uid`来查找用户，如果找到了，则返回记录作为结果。如果没有找到，则创建并返回一个新的对象。这样做是为了避免多次创建同一个用户的情况。然后，我们获取用户名和令牌，保存记录，并将其返回。

这里有一个示例 [auth hash](https://github.com/zquestz/omniauth-google-oauth2#auth-hash) ，可以作为参考。

是时候创建一个新方法来测试用户是否登录了。我们将称之为`current_user`，这是 Rails 项目中常见的习语。

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

它只是检查会话是否包含`user_id`,如果包含，则尝试查找具有指定 id 的用户。`helper_method`确保这个方法也可以在视图中使用。

最后，让我们扩展一下菜单:

*视图/布局/应用程序. html.erb*

```
[...]
<div class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'YT APIv3', root_path, class: 'navbar-brand' %>
    </div>
    <ul class="nav navbar-nav">
      <li><%= link_to 'Videos', root_path %></li>
      <li><%= link_to 'Add Video', new_video_path %></li>
      <% if current_user %>
        <li><%= link_to 'Upload Video', new_video_upload_path %></li>
      <% end %>
    </ul>
    <ul class="nav navbar-nav pull-right">
      <% if current_user %>
        <li><span><%= current_user.name %></span></li>
        <li><%= link_to 'Log Out', logout_path, method: :delete %></li>
      <% else %>
        <li><%= link_to 'Log In', '/auth/google_oauth2' %></li>
      <% end %>
    </ul>
  </div>
</div>
[...]
```

我还添加了一些样式来美化这些链接:

*application.scss*

```
[...]
.nav > li > span {
  display: block;
  padding-top: 15px;
  padding-bottom: 15px;
  color: #9d9d9d;
}
```

### 上传

太好了，只剩一步了。我们必须创建另一个表单，允许用户选择一个视频，并为它提供标题和描述。

我们可以使用相同的`VideosController`，但是我决定在这里使用另一种方法，这种方法遵循 REST 原则并使验证非常简单。

创建新控制器:

*video _ uploads _ controller . Rb*

```
class VideoUploadsController < ApplicationController
  def new
    @video_upload = VideoUpload.new
  end

  def create
  end
end
```

我们很快就会回到`create`方法。现在，添加新路线:

*config/routes.rb*

```
[...]
resources :video_uploads, only: [:new, :create]
[...]
```

还有另一个菜单项:

*视图/布局/应用程序. html.erb*

```
[...]
<ul class="nav navbar-nav">
  <li><%= link_to 'Videos', root_path %></li>
  <% if current_user %>
    <li><%= link_to 'Add Video', new_video_upload_path %></li>
  <% end %>
</ul>
[...]
```

现在，实际形式是:

*views/video _ uploads/new . html . erb*

```
<div class="container">
  <h1>Upload video</h1>
  <% if current_user %>
    <%= form_for @video_upload do |f| %>
      <%= render 'shared/errors', object: @video_upload %>

      <div class="form-group">
        <%= f.label :file %>
        <%= f.file_field :file, class: 'form-control', required: true %>
      </div>

      <div class="form-group">
        <%= f.label :title %>
        <%= f.text_field :title, class: 'form-control', required: true %>
      </div>

      <div class="form-group">
        <%= f.label :description %>
        <%= f.text_area :description, class: 'form-control', cols: 3 %>
      </div>

      <%= f.submit 'Upload', class: 'btn btn-primary' %>
    <% end %>
  <% else %>
    <p>Please <%= link_to 'sign in', '/auth/google_oauth2' %>.</p>
  <% end %>
</div>
```

我们必须检查用户是否登录，否则试图上传视频将导致错误。该表单包含三个字段:*文件*、*标题*和*描述*。您可以进一步扩展它，允许用户为他们的视频提供标签或类别(不要忘记相应地调整控制器和模型)。

我们需要考虑验证。显然，我们不需要一个单独的表，因为关于上传视频的信息将被保存到已经存在的同一个`videos`表中。因此，似乎不需要模型，但是所有的验证逻辑都必须放入控制器中，这不是最好的主意:

```
def create
  if params[:file].present? && params[:title].present? # ... and more checks here
    # upload video
  else
    # display an error (and user won't even understand what exactly is wrong)
  end
end
```

相反，创建一个 Ruby 类并将其命名为`VideoUpload`——所有的验证逻辑都可以放在那里。不过，如果这个类借用一些很酷的 ActiveRecord 特性也不错。我们能做到，我们有技术。认识一下马坎德拉人创造的 [active_type](https://github.com/makandra/active_type) 。ActiveType 让 Ruby 对象像 ActiveRecord 一样嘎嘎作响。

将此宝石放入*宝石文件*

```
[...]
gem 'active_type', '0.3.1'
[...]
```

然后跑

```
$ bundle install
```

现在在**车型**目录下创建 **video_upload.rb** 文件:

*models/video_upload.rb*

```
class VideoUpload < ActiveType::Object
  attribute :file, :string
  attribute :title, :string
  attribute :description, :text

  validates :file, presence: true
  validates :title, presence: true
end
```

不幸的是，Postgres 和 Rails 4.2 有一个[问题](https://github.com/makandra/active_type/issues/19)(可能还有其他版本的 Rails ),要求我修改第二和第三行，如下所示:

*models/video_upload.rb*

```
[...]
attribute :file, :varchar
attribute :title, :varchar
[...]
```

这是一个继承自`ActiveType::Object`的简单 Ruby 类，它被赋予了超能力。在`attribute`方法的帮助下，我们指定属性和类型。`validates`方法直接来自 ActiveRecord，你可以用同样的方式使用它。相当酷！

此时，我们可以返回到控制器

*video _ uploads _ controller . Rb*

```
def create
  @video_upload = VideoUpload.new(title: params[:video_upload][:title],
                                  description: params[:video_upload][:description],
                                  file: params[:video_upload][:file].try(:tempfile).try(:to_path))
  if @video_upload.save
    uploaded_video = @video_upload.upload!(current_user)

    # check if the video was uploaded or not

    redirect_to root_url
  else
    render :new
  end
end
```

这只是一个基本的控制器方法。当然，在`@video_upload`上调用`save`实际上并不保存任何东西——它只是运行验证。`upload!`方法并不存在，所以让我们来解决这个问题:

*models/video_upload.rb*

```
[...]
def upload!(user)
  account = Yt::Account.new access_token: user.token
  account.upload_video self.file, title: self.title, description: self.description
end
[...]
```

这个方法用我们之前收到的访问令牌创建了一个新的 yt 客户机。`upload_video`方法开始实际的上传。它接受文件和视频参数，如标题和描述。如果你读过我的文章“用 Rails 上传视频到 YouTube ”,那么你可能会注意到上传过程现在比 T2 简单多了。对于 YT API v2，您必须实际执行两个请求:第一个请求返回上传令牌，第二个请求允许上传开始。这真的很混乱，感谢谷歌，他们简化了事情。

`create`的最后一点逻辑:

```
[...]
def create
  @video_upload = VideoUpload.new(title: params[:video_upload][:title], description: params[:video_upload][:description], file: params[:video_upload][:file].try(:tempfile).try(:to_path))

  if @video_upload.save
    uploaded_video = @video_upload.upload!(current_user)

    if uploaded_video.failed?
      flash[:error] = 'There was an error while uploading your video...'
    else
      Video.create({link: "https://www.youtube.com/watch?v=#{uploaded_video.id}"})
      flash[:success] = 'Your video has been uploaded!'
    end
    redirect_to root_url
  else
    render :new
  end
end
[...]
```

如果视频上传失败，显示错误。否则，将视频信息添加到数据库中，并重定向到
和`root_url`。请随意进一步重构这段代码。

顺便说一下，除了`failed?`还有其他的状态检查——看看这里的例子[。](https://github.com/Fullscreen/yt#ytvideo)

### 一些问题

你应该记住，YouTube 需要一些时间来消化视频，视频越长，这个过程就需要越长的时间。你为什么要在乎？因为如果您试图在上传过程完成后立即获取视频的持续时间，结果将返回零秒。

这同样适用于缩略图——它们将在几分钟内不可用，你将看到默认的、乏味的灰色图像。

为了解决这个问题，你可以设置某种后台进程，定期检查新上传的视频是否被消化(使用`processed?`方法)。如果是，获取他们的所有信息。您甚至可能想要隐藏这些视频，只在解析完成后才在主页上显示它们。只是不要忘记警告你的用户这个事实(解析长视频可能需要超过 10 分钟)。

此外，不要忘记，YouType 可以出于多种原因拒绝视频:太长、太短、重复、违反版权、具有不支持的编解码器等。因此，使用广泛的状态检查。

## 结论

各位，今天就到这里吧！我希望你会发现这篇文章和最初的版本一样有用。yt gem 有许多更吸引人的特性，所以浏览它的[自述文件](https://github.com/Fullscreen/yt/blob/master/README.md)！你已经用过 YT API v3 了吗？你怎么想？有没有遇到什么具体问题？

另外，不要犹豫，发表你的问题或要求，你想让我涵盖的主题。再见！

## 分享这篇文章