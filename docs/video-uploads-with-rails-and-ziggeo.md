# 使用 Rails 和 Ziggeo 上传视频

> 原文：<https://www.sitepoint.com/video-uploads-with-rails-and-ziggeo/>

![ziggeo](img/52b0463a5ed31b36cafc93a1271aa74b.png)

我记得有段时间，因为网速太慢，互联网上几乎没有人看视频。然后，速度开始提高，我可以下载音乐了:当我听另一首歌的时候，下载一首歌大概需要 4-5 分钟。2005 年，YouTube 出现，视频内容开始传播。如今视频无处不在，许多人实际上更喜欢视频而不是文本(尽管也有许多人喜欢阅读文本)。

我已经在我之前的一篇文章的[中介绍了使用 YouTube API 的过程，所以今天我们将讨论另一个名为](https://www.sitepoint.com/youtube-api-version-3-rails/) [Ziggeo](https://ziggeo.com) 的视频托管平台。它提供了一个 API 来存储和管理视频以及嵌入它们的能力(使用一个简单的播放器)。此外，还有其他功能，如评论、审核、回调访问权限、与第三方服务的集成等等。Ziggeo 有免费和付费的价格计划。通过免费计划，你可以上传总长度为 100 分钟的视频，因此这是测试该服务的一个很好的选择。

在本文中，我将向您展示如何上传视频到 Ziggeo，将它们与用户关联，嵌入它们，获取元信息，设置服务器回调，以及监听事件。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Video_Uploads_with_Ziggeo) 上获得。

特别感谢 Ziggeo 的支持团队，他们提供了快速而专业的帮助。

## 创建应用程序

像往常一样，首先创建一个名为`ZigZag`的新 Rails 5 应用程序:

```
$ rails new ZigZag -T 
```

加入一些必要的宝石:

*Gemfile*

```
# ...
gem 'Ziggeo'
gem 'dotenv-rails'
gem 'devise' 
```

*   Ziggeo 是一个宝石，允许你轻松地使用 Ziggeo 的 API。注意，它的名字以大写的“Z”开头！
*   [Dotenv](https://github.com/bkeepers/dotenv) 将用于存储开发的环境变量。
*   [设计](https://github.com/plataformatec/devise)将提供认证解决方案。

安装这些 gem，为 Devise 创建一个基本配置，并添加一个`User`模型:

```
$ bundle install
$ rails generate devise:install
$ rails generate devise User
$ rails db:migrate 
```

创建一个`VideosController`并设置一些路线:

*videos_controller.rb*

```
class VideosController < ApplicationController
end 
```

*config/routes.rb*

```
# ...
resources :videos, only: [:new, :index, :destroy]
root 'videos#index' 
```

在`ApplicationController`内部实施用户认证:

*application _ controller . Rb*

```
# ...
before_action :authenticate_user! 
```

另外，让我们在布局中显示 flash 消息:

*视图/布局/应用程序. html.erb*

```
# ...
<% flash.each do |key, value| %>
  <div class="alert alert-<%= key %>">
    <%= value %>
  </div>
<% end %> 
```

就是这样。下一步是建立 Ziggeo。

## Ziggeo 初始设置

[在继续之前注册 Ziggeo 的](https://ziggeo.com/signup)。一旦你完成了，一个名为“Default”的应用程序将可供你使用，但是，当然，另一个应用程序也可能被创建。每个应用程序都有自己的设置、相关视频和在 API 中工作的键。Ziggeo 提供了三把钥匙，所以把它们放在*里面。env* 文件:

*。环境*

```
ZIGGEO_KEY=123
ZIGGEO_SECRET=abc
ZIGGEO_ENCRYPTION=345 
```

确保从 Git 中排除该文件:

*。gitignore〔t1〕*

```
.env 
```

Ziggeo 还提供了一个不错的[快速入门指南](https://ziggeo.com/quickstart)，所以你可能也想浏览一下。接下来，包括必要的 CSS 和 JavaScript 文件:

*视图/布局/应用程序. html.erb*

```
<link rel="stylesheet" href="//assets-cdn.ziggeo.com/v1-stable/ziggeo.css" />
<script src="//assets-cdn.ziggeo.com/v1-stable/ziggeo.js"></script>
<script>ZiggeoApi.token = '<%= ENV['ZIGGEO_KEY'] %>';</script> 
```

基本上，这足以开始在网站上上传和嵌入视频。

## 上传和录制

Ziggeo [指示我们](https://ziggeo.com/docs#client-integration)在页面上放置一个特殊的标签(`ziggeo`)。它同时充当视频播放器、录像机和视频上传器。为了控制组件的行为和外观，可以设置多个选项。

下面是最简单的变体:

*views/videos/new . html . erb*

```
<ziggeo ziggeo-width="320" ziggeo-height="240"></ziggeo> 
```

这将创建一个特殊的控件来记录和上传视频。我不希望视频太长，所以设置一个`ziggeo-limit`参数:

```
<ziggeo ziggeo-width="320" ziggeo-height="240" ziggeo-limit="60"></ziggeo> 
```

`60`这里的意思是“不超过 60 秒”。此外，允许用户上传已经录制的视频也不错，所以将`ziggeo-perms`设置为`allowupload`:

```
<ziggeo ziggeo-limit="60"
        ziggeo-width="320"
        ziggeo-height="240"
        ziggeo-perms="allowupload"></ziggeo> 
```

现在你可以测试一下了。任何上传的视频都会出现在 Ziggeo 仪表盘上的“视频”和“审核”标签中。除了实际的视频文件，你会看到额外的元信息，如其长度，大小，创建日期，标签，等等。此外，还有一个方便的“Events”部分，列出了应用程序内部发生的所有事件。

## 嵌入视频

使用相同的`ziggeo`标签在页面上嵌入视频。主要属性是接受视频 uid 的`ziggeo-video`:

```
<ziggeo ziggeo-video='123abc'
        ziggeo-width="320"
        ziggeo-height="240">
</ziggeo> 
```

更重要的是，视频可以在弹出窗口中播放。为此，只需设置`ziggeo-popup`参数:

```
<ziggeo ziggeo-video='123abc'
        ziggeo-width="320"
        ziggeo-height="240" ziggeo-popup>
</ziggeo> 
```

有一个[沙箱](https://ziggeo.com/sandbox/configuration)可用，在那里你可以看到上传和嵌入操作，以及测试各种配置选项。

还要注意，Ziggeo 的播放器应用了一些样式，但看起来并不漂亮。因此，您可以根据需要进一步设计它的样式。[这里是操场](https://ziggeo.com/sandbox/styling)看造型在行动。

## 收听事件

Ziggeo 提供了一大堆 [Javascript 事件](https://ziggeo.com/docs/sdks/javascript/browser-interaction/events#javascript-revision=v1-stable&javascript-version=v1)，您可以收听:`play`、`pause`、`uploaded`和`camera_nosignal`等等。例如，让我们创建一个自定义进度条，显示视频上传过程的进展情况。对于不断触发的`upload_progress`事件来说，这很容易做到，它会告诉你到目前为止已经上传了多少字节。

首先，在页面中添加`progress`元素。我使用 [Bootstrap 4](http://v4-alpha.getbootstrap.com/) 来设计风格，但是你可以使用任何其他的 CSS 框架或者编写你自己的风格:

*views/videos/new . html . erb*

```
<!-- ... -->
<progress class="progress progress-striped hidden-xs-up" value="0" max="100"></progress> 
```

类将在所有屏幕上隐藏这个进度条。或者，你可以简单地说`display: none`。

现在编写一些 CoffeeScript 代码:

*JavaScript s/videos . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  ZiggeoApi.Events.on "upload_progress", ( uploaded, total, data ) ->
    $('progress').removeClass('hidden-xs-up').attr 'value', (uploaded / total) * 100 
```

加载 Ziggeo 的 JS 后，全局对象是否可用。`uploaded`和`total`变量包含字节数，所以将它们除以`100`得到一个百分比。`data`变量包含当前正在上传的视频信息。

不要忘记包括这个新文件:

*JavaScript/application . js*

```
//= require videos 
```

我们可能做的另一件事是在视频上传后将用户重定向到根页面:

*JavaScript s/videos . coffee*

```
ZiggeoApi.Events.on "submitted", ( data ) -> window.location.href = '/' 
```

很简单，不是吗？

还要注意，Ziggeo 提供了一些可调用的方法来操作播放器和上传器。这些方法可以在[文档](https://ziggeo.com/docs/sdks/javascript/browser-interaction/methods#javascript-revision=v1-stable&javascript-version=v1)中找到。

## 查询 API

所以，现在视频可以上传了，但是我们不会在任何地方展示它们。为了获取上传视频的列表，你可以使用我们在文章开头提到的 [Ziggeo gem](https://github.com/Ziggeo/ZiggeoRubySdk) 。

使用它非常简单。您只需要通过传递前面获得的所有三个键来实例化客户端，然后调用所需的方法:

*videos_controller.rb*

```
# ...
def index
  ziggeo = Ziggeo.new(ENV['ZIGGEO_KEY'], ENV['ZIGGEO_SECRET'], ENV['ZIGGEO_ENCRYPTION'])
  @videos = ziggeo.videos.index
end 
```

`index`方法[接受](https://github.com/Ziggeo/ZiggeoRubySdk#server-side-methods)参数，如`limit`和`skip`来进一步定制请求。因此，`@videos`变量将包含一个散列数组，每个散列存储视频的 uid 和其他元信息。该数组现在可以呈现在主页上:

*views/videos/index . html . erb*

```
<h1>Videos</h1>

<%= link_to 'Add video', new_video_path %>

<%= render partial: 'video', collection: @videos, as: :video %> 
```

注意，我们不能说`render @videos`，因为它是一个简单的数组。现在渲染部分里面的播放器。视频将在弹出窗口中打开:

*views/videos/_ video . html . erb*

```
<div class="card">
  <div class="card-block">
    <ziggeo ziggeo-video='<%= video['token'] %>'
            ziggeo-width="320"
            ziggeo-height="240" ziggeo-popup>
    </ziggeo>
</div> 
```

`token`字段包含视频的唯一标识符。

## 将视频与用户相关联

一个常见的功能是允许每个用户拥有自己的视频。然而，目前，视频没有关于谁拥有它们的信息。不幸的是，没有专门的字段来存储这些信息，但可以通过使用视频的标签来解决。标签以逗号分隔的字符串形式传递，该字符串后来被转换为数组。当稍后获取视频时，我们将简单地传递`tags`选项。

我们*可能*利用用户 id 作为标签，但这不是很安全。相反，让我们在创建用户后生成一个唯一的令牌:

*型号/用户. rb*

```
# ...
before_create -> { self.uid = generate_uid }

private

def generate_uid
  loop do
    uid = Digest::MD5.hexdigest(self.email + self.created_at.to_s + rand(10000).to_s)
    return uid unless User.exists?(uid: uid)
  end
end 
```

我们只是根据用户的电子邮件、创建日期和一些随机数生成一个 MD5 散列。这个令牌必须是唯一的，所以我们确保没有其他用户已经拥有它。

`uid`字段尚不存在，因此现在创建相应的迁移:

```
$ rails g migration add_uid_to_users uid:string 
```

调整迁移以包括强制唯一性的索引:

*db/migrate/XYZ _ add _ uid _ to _ users . Rb*

```
class AddUidToUsers < ActiveRecord::Migration[5.0]
  def change
    add_column :users, :uid, :string
    add_index :users, :uid, unique: true
  end
end 
```

现在应用迁移:

```
$ rails db:migrate 
```

有了这个 uid，我们就可以为视频设置令牌了。这和提供`ziggeo-tags`参数一样简单。请记住，它接受逗号分隔的字符串:

*views/videos/new . html . erb*

```
<ziggeo ziggeo-limit="60"
        ziggeo-width="320"
        ziggeo-height="240"
        ziggeo-perms="allowupload"
        ziggeo-tags="<%= current_user.uid %>"></ziggeo> 
```

太好了！现在为了只获取当前用户的视频，为`index`方法设置`tags`选项:

*videos_controller.rb*

```
# ...
def index
  ziggeo = Ziggeo.new(ENV['ZIGGEO_KEY'], ENV['ZIGGEO_SECRET'], ENV['ZIGGEO_ENCRYPTION'])
  @videos = ziggeo.videos.index(tags: current_user.uid)
end 
```

很好，但我们可以做得更好。Ziggeo 的 API 的问题是，没有办法只获取经版主批准的视频。这很奇怪，但 Ziggeo 的支持团队证实了这一信息。当然，我们可以通过使用 [keep_if](http://ruby-doc.org/core-2.2.2/Array.html#method-i-keep_if) 方法在控制器中过滤批准的视频，但是如果您想使用分页机制，事情会变得相当复杂。因此，我们为什么不设置[服务器回调](https://ziggeo.com/docs#server-events)，并以我们认为合适的方式将视频信息存储在我们自己的数据库中？让我们在下一节开始吧！

## 设置回拨

### 准备工作和视频创作

回调是按应用程序配置的，所以打开你的仪表板，选择一个应用程序，然后点击**管理> Web 钩子**。在这里输入一个 URL(我会选择`https://sitepoint-ziggeo.herokuapp.com/api/video_callbacks`)并从下拉列表中选择“JSON 编码”。现在事件将以 POST 请求的形式转发给`/api/video_callbacks`。[这是你可以使用的所有回调列表](https://ziggeo.com/docs#server-events)。请注意，并非所有事件都会被转发，只有最重要的事件才会被转发。

首先，我们希望跟踪所有视频的添加，因此需要一个名为`Video`的新模型。它将包含以下字段:

*   `uid`(字符串，索引，唯一)–视频的令牌
*   `user_id`(整数，索引)-建立用户和视频之间关系的外键
*   `duration`(十进制)–视频的持续时间(秒)
*   `ziggeo_created_at`(日期时间)-在 Ziggeo 上创建视频的日期和时间
*   `approved` (boolean，indexed)–视频是否被版主批准，默认为`false`

创建相应的迁移:

```
$ rails g model Video user:belongs_to uid:string duration:decimal ziggeo_created_at:datetime approved:boolean 
```

稍微调整一下迁移:

*db/migrate/XYZ/create _ videos . Rb*

```
# ...
create_table :videos do |t|
  t.string :uid
  t.belongs_to :user, foreign_key: true
  t.decimal :duration, scale: 2, precision: 5
  t.datetime :ziggeo_created_at
  t.boolean :approved, default: false

  t.timestamps
end

add_index :videos, :approved
add_index :videos, :uid, unique: true 
```

应用它:

```
$ rails db:migrate 
```

确保设置了正确的关联和验证:

*型号/用户. rb*

```
# ...
has_many :videos, dependent: :destroy 
```

*models/video.rb*

```
# ...
belongs_to :user
validates :uid, presence: true, uniqueness: true 
```

现在在`:api`下添加一个新的路径名称空间:

*config/routes.rb*

```
namespace :api do
  resources :video_callbacks, only: [:create]
end 
```

在 *api* 文件夹中创建一个新的控制器:

*控制器/API/video _ callbacks _ controller . Rb*

```
class Api::VideoCallbacksController < ActionController::Base
  def create
  end
end 
```

当一个新事件到达时，它有一个设置为某个值的`event_type`参数。目前我们对`video_ready`事件感兴趣。让我们只获取视频数据，并基于它创建一个新记录:

*控制器/API/video _ callbacks _ controller . Rb*

```
class Api::VideoCallbacksController < ActionController::Base
  def create
    type = params['event_type']
        respond_to do |format|
          @result = if type == 'video_ready'
                      Video.from_api(params['data']['video'])
                    end
        end
  end
end 
```

视频数据存储在`['data']['video']`键下。

如果一切正常，也用 204(无内容)状态码响应，如果出现问题，则用 500(服务器错误)响应:

*控制器/API/video _ callbacks _ controller . Rb*

```
def create
  type = params['event_type']
  respond_to do |format|
    @result = if type == 'video_ready'
                Video.from_api(params['data']['video'])
              end
    format.html { @result ? head(:no_content) : head(500) }
  end
end 
```

现在编写`from_api`类方法。它应该根据视频的标签获取一个用户(记住，我们使用用户的 UID 作为标签),并创建一个属于他的新记录:

*models/video.rb*

```
# ...
def self.from_api(data)
  user = User.find_by(uid: data['tags'][0])
  video = user.videos.find_or_initialize_by(uid: data['token'])
  video.ziggeo_created_at = Time.at(data['created'])
  video.duration = data['duration']
  video.save
end 
```

包含一个数组，所以我们只需获取第一个元素。我注意到有时一个事件可能会被发送两次，所以使用`find_or_initialize_by`来避免创建重复的记录。嗯，这是不可能的，因为为`uid`设置的索引加强了唯一性，但仍然如此。

### 视频审批

当视频被主持人批准或拒绝时，相应的事件也会被发送。我们将使用`video_approve`事件类型。当它到达时，根据它的令牌(uid)在数据库中找到视频，并将`approved`属性设置为`true`:

*控制器/API/video _ callbacks _ controller . Rb*

```
def create
  type = params['event_type']
  respond_to do |format|
    @result = if type == 'video_ready'
                Video.from_api(params['data']['video'])
              else
                if type == 'video_approve'
                  video = Video.find_by(uid: params['data']['video']['token'])
                  video.approve! if video
                else
                  true
                end
              end
    format.html { @result ? head(:no_content) : head(500) }
  end
end 
```

如果事件是其他类型，我们只需将`true`赋给`@result`实例变量。下面是`approve!`的方法:

*models/video.rb*

```
# ...
def approve!
  self.approved = true
  self.save
end 
```

### 视频删除

可以使用 Ziggeo 仪表盘删除视频。当它发生时，我们也想从我们的数据库中删除这个视频。我们感兴趣的事件类型叫做`video_delete`。再一次，找到合适的视频，然后销毁它:

*控制器/API/video _ callbacks _ controller . Rb*

```
# ...
def create
  type = params['event_type']
  respond_to do |format|
    @result = if type == 'video_ready'
                Video.from_api(params['data']['video'])
              else
                if type == 'video_approve' || type == 'video_delete'
                  video = Video.find_by(uid: params['data']['video']['token'])
                  if video
                    type == 'video_approve' ?
                        video.approve! :
                        video.destroy
                  end
                else
                  true
                end
              end
    format.html { @result ? head(:no_content) : head(500) }
  end
end 
```

不错！现在我们已经有了这些回调函数，可以重写`VideosController`中的`index`动作了。

## 显示视频和元信息

在`index`动作中，我们不再需要 Ziggeo 客户端。相反，只需抓取当前用户的视频——只有那些已被批准的视频:

*videos_controller.rb*

```
# ...
def index
  @videos = current_user.videos.where(approved: true)
end 
```

只要每个视频现在都有额外的元信息，我们也可以在主页上呈现它:

*views/videos/_ video . html . erb*

```
<div class="card">
  <div class="card-block">
    <ziggeo ziggeo-video='<%= video.uid %>'
            ziggeo-width="320"
            ziggeo-height="240" ziggeo-popup>
    </ziggeo>
    <p>
      <strong>Duration:</strong> <%= video.duration %>s<br>
      <strong>Created:</strong> <%= video.ziggeo_created_at  %>
    </p>
  </div>
</div> 
```

## 通过 API 删除视频

我们今天要编写的最后一个功能是从应用程序中删除视频的能力。只有拥有该视频的用户才能执行此操作。

首先，显示一个“删除”链接:

*views/videos/_ video . html . erb*

```
<ziggeo ziggeo-video='<%= video.uid %>'
        ziggeo-width="320"
        ziggeo-height="240" ziggeo-popup>
</ziggeo>
<!-- ... -->
<p><%= link_to 'Delete', video_path(video.uid), method: :delete %></p> 
```

请注意，我传递的是视频的 *uid* ，而不是*id*——我们实际上不会在`destroy`动作中从数据库中删除视频。

为了删除视频，需要一个 Ziggeo API 客户端。使用接受 uid 的`delete`方法执行删除。

*videos_controller.rb*

```
# ...
def destroy
  video = current_user.videos.find_by(uid: params[:id])
  if video
    ziggeo = Ziggeo.new(ENV['ZIGGEO_KEY'], ENV['ZIGGEO_SECRET'], ENV['ZIGGEO_ENCRYPTION'])
    ziggeo.videos.delete(video.uid)
    flash[:success] = 'Video removed! It may take some time to reflect changes on the website.'
  else
    flash[:warning] = 'Cannot find such video...'
  end
  redirect_to root_path
end 
```

我们只删除了 Ziggeo 的一段视频。这个操作完成后，一个`video_delete`事件将被发送到我们的回调函数，相应的记录将从数据库中删除。这个过程不是即时的，这就是为什么我们警告用户可能需要一些时间来反映变化。

## 结论

我们已经到了这篇文章的结尾！Ziggeo 比我们今天讨论的功能多得多，因为我们只讨论了它的基本特性。因此，请务必[阅读更多关于该服务的](https://ziggeo.com/docs)信息，并亲自试用。此外，请注意，Ziggeo 可以作为 [Heroku 附加软件](https://ziggeo.com/integrations/heroku)使用，[可以与 YouTube 和 Dropbox 等流行服务集成](https://ziggeo.com/integrations)。

我希望你喜欢阅读这篇文章，我感谢你和我呆在一起。编码快乐，再见！

## 分享这篇文章