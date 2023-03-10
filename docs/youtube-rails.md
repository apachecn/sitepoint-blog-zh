# YouTube 上的 Rails

> 原文：<https://www.sitepoint.com/youtube-rails/>

> 这篇文章已经过时了，因为 YouTube 已经发布了他们 API 的第三版。如果你需要使用第三版的教程，可以看看[这篇文章](https://www.sitepoint.com/youtube-api-version-3-rails/)。

![yt-rails](img/62caae6f6366f66f1bf2bd2d7263f7ac.png)

[YouTube](https://www.youtube.com) 。世界上访问量第三大的资源。我们都访问过它，可能还上传过视频。但是，作为 Rails 开发人员，我们如何获取关于 YouTube 视频的信息呢？我们如何在我们的网站上展示它们？有什么图书馆可以帮助我们吗？我们来调查一下，好吗？

在本文中，我将向您展示如何创建一个允许用户添加和观看 YouTube 视频的 web 应用程序。添加后，视频信息将通过 YouTube API 自动获取。该应用程序还将在 YouTube IFrame API 的帮助下，使用典型的 YouTube 播放器来显示这些添加的视频。最后，您可能会面临一些潜在的问题和陷阱。

源代码可以在 [GitHub](https://github.com/bodrovis/SitePoint-YtVideos) 上获得。

工作演示可以在 [Heroku](http://sitepoint-yt-videos.herokuapp.com) 上找到。

## 准备演示应用程序

在这篇文章中，我将使用 Rails 3.2.17，但是你可以用 Rails 4 实现几乎相同的解决方案。

首先创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new yt_videos -T
```

首先要抢到这个 demo 要用到的宝石。我们将使用 Kyle J. Ginavan 编写的方便的 gem`youtube_it`来处理 YouTube API。对于造型，让我们使用我们的老朋友 Twitter Bootstrap，特别是`bootstrap-sass` gem。继续将这两行添加到您的`Gemfile`中:

*Gemfile*

```
gem 'bootstrap-sass', '~> 3.1.1.0'
gem 'youtube_it', '~> 2.4.0'
```

那就跑

```
$ bundle install
```

现在添加

```
//= require bootstrap
```

`application.js`并且

```
@import "bootstrap";
```

添加到`application.css.scss`以包含引导样式和脚本。当然，在实际应用中，您可以只选择所需的组件。

我们应该想想我们的`Video`模型会是什么样子。当然，它应该包含用户添加的视频的链接。YouTube API 只提供唯一的视频标识符(我们将很快学习提取它)，所以我们需要获取它。此外，我们希望存储一些关于视频的信息。以下是我们的 ActiveRecord 模型的所有属性列表:

*   `id`–整数，主键，索引，唯一
*   `link`–字符串
*   `uid`–字符串。在这里添加一个索引来加强惟一性也是一个好主意。对于这个演示，我们不希望用户多次添加相同的视频
*   `title`–字符串。这将包含从 YouTube 提取的视频标题
*   `author`–字符串。作者姓名摘自 YouTube
*   `duration`–字符串。我们将存储视频的持续时间，格式如下:“00:00:00”
*   `likes`–整数。视频点赞数
*   `dislikes`–整数。不喜欢的视频也算。

对于这个演示来说，这已经足够了，但是请记住，YouTube API 允许您获取更多的信息。

运行以下命令创建模型和相应的迁移:

```
$ rails g model Video link:string title:string author:string duration:string likes:integer dislikes:integer
```

现在设置路线:

*routes.rb*

```
resources :videos, only: [:index, :new, :create]
root to: 'videos#index'
```

出于演示目的，我们不需要`edit`、`update`、`show`和`destroy`动作。另外，如果你正在使用 Rails 3，不要忘记删除`public/index.html`文件。

最后要做的是在布局中添加一个用于呈现 flash 消息的块:

*application.html.erb*

```
[...]

<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <button type="button" class="close" data-dismiss="alert">&times;</button>
      <%= value %>
    </div>
  <% end %>
</div>

[...]
```

## 添加视频

当用户访问我们的网站时，他首先看到的可能是“添加视频”按钮。

*videos/index.html.erb*

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

多亏了 Bootstrap，这个块看起来会很漂亮。下一步是实现`new`动作。

但是让我们停下来检查一下`models/video.rb`文件。如果您使用的是 Rails 3，默认情况下所有属性(除了`id`、`updated_at`和`created_at`)都是可访问的。我们唯一需要从用户那里得到的是他想要添加的视频的链接。其他属性将会自动填充，所以让我们进行适当的修改:

*models/video.rb*

```
[...]

attr_accessible :link

[...]
```

对于 Rails 4，您应该只允许控制器中的`link`属性:

```
params.require(:video).permit(:link)
```

好了，现在我们可以继续了。将这两种方法添加到控制器中:

*控制器/视频 _ 控制器. rb*

```
[...]

def new
  @video = Video.new
end

def create
  @video = Video.new(params[:video])
  if @video.save
    flash[:success] = 'Video added!'
    redirect_to root_url
  else
    render 'new'
  end
end

[...]
```

这里没什么特别的。

观点:

*videos/new.html.erb*

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

    <%= f.submit class: 'btn btn-default' %>
  <% end %>
</div>
```

正如你所看到的，我们只要求用户输入 YouTube 上的视频链接。在这里，还呈现了一个共享分部以显示任何错误，如下所示:

*shared/_errors.html.erb*

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

现在是有趣的部分——提取用户指定的视频信息。在创建记录之前，我们将在回调中完成这项工作。

*models/video.rb*

```
before_create -> do
  # Our code here
end
```

为了从 YouTube API 获取信息，我们需要视频的唯一标识符。实际上，这个标识符已经存在于用户提供的链接中。这里的技巧是这些链接可以用多种格式指定，例如:

*   http://www.youtube.com/watch?v=0zM3nApSvMg&feature=feedrec grec 指数
*   http://www.youtube.com/user/IngridMichaelsonVEVO#p/a/u/1/QdK8U-VIH_o
*   http://www.youtube.com/v/0zM3nApSvMg?fs=1&hl=en_US&rel=0
*   http://www.youtube.com/watch?v=0zM3nApSvMg#t=0m10s
*   http://www.youtube.com/embed/0zM3nApSvMg?rel=0
*   http://www.youtube.com/watch?v=0zM3nApSvMg
*   http://youtu.be/0zM3nApSvMg

所有这些 URL 都指向 id 为`0zM3nApSvMg`的同一个视频(你可以在 [StackOverflow](https://stackoverflow.com/questions/3452546/javascript-regex-how-to-get-youtube-video-id-from-url) 上了解更多)。

另外值得一提的是，一个视频的 uID 包含 11 个符号。为了从链接中获取 uID，我们将使用下面的 RegExp:

```
/^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/i
```

不过，在此之前，让我们确保有一个有效的链接:

*models/video.rb*

```
YT_LINK_FORMAT = /^.*(youtu.be\/|v\/|u\/\w\/|embed\/|watch\?v=|\&v=)([^#\&\?]*).*/i

validates :link, presence: true, format: YT_LINK_FORMAT

[...]
```

好，获取回调函数中的 uID:

*models/video.rb*

```
[...]

before_create -> do
  uid = link.match(YT_LINK_FORMAT)
  self.uid = uid[2] if uid && uid[2]

  if self.uid.to_s.length != 11
    self.errors.add(:link, 'is invalid.')
    false
  elsif Video.where(uid: self.uid).any?
    self.errors.add(:link, 'is not unique.')
    false
  else
    get_additional_info
  end
end
```

这里有一些对 uid 的验证，特别是检查它的长度和唯一性。如果一切正常，调用一个方法来获取关于视频的信息:

*models/video.rb*

```
[...]

private

def get_additional_info
  begin
    client = YouTubeIt::OAuth2Client.new(dev_key: 'Your_YT_developer_key')
    video = client.video_by(uid)
    self.title = video.title
    self.duration = parse_duration(video.duration)
    self.author = video.author.name
    self.likes = video.rating.likes
    self.dislikes = video.rating.dislikes
  rescue
    self.title = '' ; self.duration = '00:00:00' ; self.author = '' ; self.likes = 0 ; self.dislikes = 0
  end
end

def parse_duration(d)
  hr = (d / 3600).floor
  min = ((d - (hr * 3600)) / 60).floor
  sec = (d - (hr * 3600) - (min * 60)).floor

  hr = '0' + hr.to_s if hr.to_i < 10
  min = '0' + min.to_s if min.to_i < 10
  sec = '0' + sec.to_s if sec.to_i < 10

  hr.to_s + ':' + min.to_s + ':' + sec.to_s
end
```

这就是`youtube_it`发挥作用的地方。首先，创建一个`client`变量——它将用于发出查询。你可能想知道“什么是 YouTube 开发者密钥？”该键用于对公共 YouTube 数据发出查询。请注意，有些动作需要用户授权，你可以在这里阅读更多关于[的内容。](https://github.com/kylejginavan/youtube_it/wiki/How-To:-Use-OAuth-2)

要获得开发者密钥，请在[https://code.google.com/apis/console](https://code.google.com/apis/console)注册新应用。打开它的设置，进入“API&auth”，然后是“凭证”。为浏览器应用程序创建一个新的公钥，这是您的开发人员密钥。

初始化客户端后，使用`video_by`获取视频，并获得相关信息。注意，视频的持续时间是以秒为单位的，所以我们必须实现一个`parse_duration`方法来按照我们想要的方式格式化它。

此时，我们的应用程序允许用户添加他们的视频。它还获取一些信息，并为用户输入提供验证。很好，不是吗？

## 显示视频

好了，我们有视频了，现在来展示它们。将以下内容添加到控制器中:

*控制器/视频 _ 控制器. rb*

```
def index
  @videos = Video.order('created_at DESC')
end
```

在视图上:

*videos/index.html.erb*

```
[...]

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
                <%= image_tag "https://img.youtube.com/vi/#{video.uid}/mqdefault.jpg", alt: video.title,
                              class: 'yt_preview img-rounded', :"data-uid" => video.uid %>

                <div class="caption">
                  <h5><%= video.title %></h5>
                  <p>Author: <b><%= video.author %></b></p>
                  <p>Duration: <b><%= video.duration %></b></p>
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

`#player-wrapper`是一个将显示 YouTube 播放器的空白块。`in_groups_of`方法将我们的记录按 3 分组，并按行显示它们。请注意，如果没有足够的元素组成一个组，Rails 会用`nil`替换每个缺失的元素，所以我们必须添加一个`if video`条件。

还有一点要提的是视频预览图。要获得视频的预览图像，请使用以下链接之一:

*   `https://img.youtube.com/vi/mqdefault.jpg`–320×180 图像，图片上下无黑色条纹；
*   `https://img.youtube.com/vi/hqdefault.jpg`–480×360 图像，图片上下有黑色条纹；
*   `https://img.youtube.com/vi/<1,2,3>.jpg`–120×90 的图像，与视频中的场景不同，图片上下有黑色条纹。

此外，当渲染视频预览图像时，我们使用 HTML5 `data-*`属性存储视频的 uID。这个属性很快就会用到。

为了在我们的网站上显示实际的视频，将使用 YouTube IFrame API。它用某些参数创建 YouTube 播放器，允许用户改变视频，暂停和停止它，等等。你可以在这里阅读更多相关信息[。像这样连接所需的 javascript 文件:](https://developers.google.com/youtube/iframe_api_reference)

*layouts/application . html . erb*

```
[...]

  <script src="https://www.google.com/jsapi"></script>
  <script src="https://www.youtube.com/iframe_api"></script>
</head>

[...]
```

现在，让我们在一个新的`yt_player.coffee`文件中编写一些 CoffeeScript 代码:

*JavaScript s/yt _ player . coffee*

```
jQuery ->
  # Initially the player is not loaded
  window.ytPlayerLoaded = false

  makeVideoPlayer = (video) ->
    if !window.ytPlayerLoaded
      player_wrapper = $('#player-wrapper')
      player_wrapper.append('<div id="ytPlayer"><p>Loading player...</p></div>')

      window.ytplayer = new YT.Player('ytPlayer', {
        width: '100%'
        height: player_wrapper.width() / 1.777777777
        videoId: video
        playerVars: {
          wmode: 'opaque'
          autoplay: 0
          modestbranding: 1
        }
        events: {
          'onReady': -> window.ytPlayerLoaded = true
          'onError': (errorCode) -> alert("We are sorry, but the following error occured: " + errorCode)
        }
      })
    else
      window.ytplayer.loadVideoById(video)
      window.ytplayer.pauseVideo()
    return
  return
```

首先，我们初始化`ytPlayerLoaded`布尔变量，它检查播放器是否已经加载。然后，创建一个`makeVideoPlayer`函数，它接受一个参数——视频的 uID——并创建一个 YouTube 播放器或改变正在播放的视频。如果播放器还没有被加载，我们在`#player-wrapper`后面添加一个新的`#ytPlayer`块，它最终会被播放器替换掉。所有这些就绪后，最后创建 YouTube player 对象并将其分配给`ytplayer`(我们将用它来调用 API 函数)。

让我们停下来，讨论一下创建这个对象时传递的参数。`ytPlayer`是应该用播放器替换的块的 DOM id。第二个参数是包含播放器设置的 javascript 对象:

*   `width`–播放器的宽度。我们的网站有响应式布局，所以我们将其设置为`100%`
*   `height`–球员的身高。我们必须根据宽度来计算。YouTube 的典型分辨率是 16:9，这意味着 1.7(7)的比率
*   `videoId`–要加载的视频的 uID
*   `wmode`–该参数控制 Flash 对象的分层和透明度([了解更多](http://helpx.adobe.com/flash/kb/flash-object-embed-tag-attributes.html#main_Using_Window_Mode__wmode__values_))。如果我们不将它设置为`opaque`，播放器将覆盖模态窗口(例如用 jQuery UI 创建的窗口)并且看起来很糟糕
*   `autoplay`–设置为`1`，视频将加载播放。在本演示中，我们不希望这种情况发生
*   `modestbranding`–如果设置为`1`，YouTube 徽标将不会显示在控制栏中，但在视频暂停时仍会显示在右上角
*   这里我们指定了两个要处理的事件。当播放器加载完毕后，将`ytPlayerLoaded`设置为`true`。如果加载播放器或视频时出现错误(例如，此视频被删除)，则提醒用户。

如果播放器已经加载，我们使用`loadVideoById`功能改变正在播放的视频，然后使用`pauseVideo`暂停播放(用于演示目的)。

好了，我们的功能准备好了。我们想加载播放器，并显示最新的视频，只要用户打开我们的网站。我们如何实现这一点？我们将使用 Google API 提供的一个特殊函数:

*JavaScript s/yt _ player . coffee*

```
[...]

_run = ->
  # Runs as soon as Google API is loaded
  $('.yt_preview').first().click()
  return

google.setOnLoadCallback _run

[...]
```

这个`google.setOnLoadCallback _run`意味着一旦 API 完成加载，就应该调用`_run`函数。如果我们不使用这个回调函数，并试图在 DOM 准备好的时候加载播放器，就会出现一个错误，指出`YT.Player`不是一个函数——这是因为 API 还没有加载。

最后要做的是将一个`click`事件处理程序绑定到视频预览:

*JavaScript s/yt _ player . coffee*

```
[...]

$('.yt_preview').click -> makeVideoPlayer $(this).data('uid')

[...]
```

仅此而已！现在我们的用户可以在我们的网站上添加视频并观看。

实际上，还有一个小问题。我们用百分比指定了玩家的宽度，但是玩家的高度是用像素指定的。这意味着，如果用户试图调整窗口的大小，播放器将变得更窄，但它将保持相同的宽度-这是不好的。为了解决这个问题，我们可以像这样将一个`resize`事件处理程序绑定到`window`:

*JavaScript s/yt _ player . coffee*

```
[...]

$(window).on 'resize', ->
  player = $('#ytPlayer')
  player.height(player.width() / 1.777777777) if player.size() > 0
  return

[...]
```

现在球员的身高将会被正确地重新调整——去试试吧。您也可以延迟触发此事件，直到用户停止调整大小，否则它将不断被触发。为此，使用 Brian Grinstead 编写的 [BindWithDelay](http://briangrinstead.com/files/bindWithDelay) 库。这非常简单:

*JavaScript s/yt _ player . coffee*

```
[...]

$(window).bindWithDelay('resize', ->
  player = $('#ytPlayer')
  player.height(player.width() / 1.777777777) if player.size() > 0
  return
, 500)

[...]
```

有了这个结构，我们将延迟 500 毫秒触发这个事件。

## 结论

这就把我们带到了文章的结尾。我们讨论了可用于 YouTube API 的 gem 和 YouTube IFrame API。我希望这里提供的信息是有用的。不要忘记在评论中分享你对这篇文章的看法。很快再见！

## 分享这篇文章