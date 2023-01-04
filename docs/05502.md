# 更好的文件上传与蜻蜓

> 原文：<https://www.sitepoint.com/file-uploads-dragonfly/>

![libellula tattoo](img/f941d6350f89d5fe53ac5aaec49c7f36.png)

文件上传是网络的重要组成部分。我们上传照片、视频和音乐来分享它们。我们发送带有一堆附件的电子邮件；大概每个活跃的互联网用户每天至少上传一次东西(嗯，那是我的猜测:)。

你可能被分配了一个任务，创建一个允许用户上传文件的系统，作为应用程序的一部分。如果没有——嗯，迟早会的:)。

我将向您介绍蜻蜓——一种用于处理图像和其他附件的流行宝石。将涵盖的一些功能包括:

*   与 ImageMagick、图像处理和分析集成。
*   如何上传图片到亚马逊 S3 而不是本地文件系统。

作为奖励，这可以很快完成！那么，我们开始吧，好吗？

工作演示可以在[http://sitepoint-dragonfly.herokuapp.com/](http://sitepoint-dragonfly.herokuapp.com)找到。

源代码可以在 [GitHub](https://github.com/bodrovis/sitepoint-dragonfly) 上获得。

## 蜻蜓和替代品

还有其他类似于蜻蜓的解决方案，你可能想在你的项目中使用。最受欢迎的是[载波波](https://github.com/carrierwaveuploader/carrierwave/)和[回形针](https://github.com/thoughtbot/paperclip)。这些都是很好的解决方案，在很多网站上都有使用。

蜻蜓有什么好东西，为什么你应该尝试使用它？

*   真的很容易上手。
*   它与 ImageMagick 完美集成。
*   能够与能够自动存储附件信息的“神奇”列一起工作。
*   它允许验证和回调的规范
*   它适用于各种数据存储或允许您创建自己的数据存储。
*   它有一个创建自定义图像分析器和生成器以及插件的框架。
*   它有一堆扩展(每个扩展都在自己的 gem 中提供)。
*   它可以与 Rails 3 和 4 以及 Ruby 1.8.7、1.9.2、1.9.3、2.0.0、jRuby 1.7.8 和 Rubinius 2.2 兼容。

如果你不熟悉这个宝石，我希望我已经说服你(至少)看一看它:)

请注意，如果你还在使用蜻蜓 0.9 或更早的版本，那么蜻蜓 1.0 已经有了一些突破性的变化。有一个[详细的指南](https://github.com/markevans/dragonfly/wiki/Upgrading-from-0.9-to-1.0)解释了如何迁移到最新版本——我很快就能完成迁移。

## 准备项目

在这个演示中，我将使用 Rails 4.1.1，但是您可以使用 Rails 3 实现相同的解决方案

让我们创建一个简单的 web 应用程序，允许用户分享(上传)他们的照片。对于第一次迭代，该应用程序将是一个非常基本的系统，允许上传任何类型的文件。接下来是添加验证，与亚马逊 S3 的集成，最后是一些优化。

好了，创建一个没有默认测试套件的新应用程序:

```
rails new uploader -T
```

打开您的`Gemfile`并添加以下宝石:

*Gemfile*

```
gem 'dragonfly'
gem 'dragonfly-s3_data_store'
gem 'bootstrap-sass'
group :production do
  gem 'rack-cache', :require => 'rack/cache'
end
```

[`dragonfly`](https://github.com/markevans/dragonfly) 是一个高度可定制的红宝石，用于处理马克·埃文斯创作的图像和其他附件，也是我们今天的主题。

[`dragonfly-s3_data_store`](https://github.com/markevans/dragonfly-s3_data_store) 是`dragonfly`的扩展，允许使用亚马逊 S3 云服务存储文件(我们稍后会谈到)。

[`bootstrap-sass`](https://github.com/twbs/bootstrap-sass) 一块引入推特自举的宝石。这是为了造型的目的，没有必要使用蜻蜓

[`rack-cache`](https://rubygems.org/gems/rack-cache) 是一个在生产
环境中进行缓存的简单解决方案，您可以将其用于小型项目(对于大型项目，您应该实现一个反向缓存代理)。

不要忘记捆绑:

```
bundle install
```

现在，是时候连接一些引导文件了

*application.css.scss*

```
@import 'bootstrap';
```

并创建一个暂时为空的`PhotosController`:

*照片 _ 控制器. rb*

```
class PhotosController < ApplicationController
end
```

## 与蜻蜓融合

生成基本的蜻蜓配置:

```
rails generate dragonfly
```

它将创建蜻蜓的初始化文件(位于`config/initializers/dragonfly.rb`)。

接下来我们需要一个表来存储上传照片的信息。目前，这个表只有两列(更不用说默认的列—`id`、`created_at`和`updated_at`):

*   `title`(`string`)–用户提供的标题。
*   `image_uid`(`string`)——上传照片的路径，由蜻蜓生成。

这些命令将创建并应用迁移:

```
rails g model Photo image_uid:string title:string
rake db:migrate
```

现在打开您新生成的模型。我们需要把它和蜻蜓结合起来:

*photo.rb*

```
[...]
dragonfly_accessor :image
[...]
```

请注意，如果您在模型中指定了`dragonfly_accessor :image`，那么您的表**必须**有一个`image_uid`列。例如，如果您希望将附件命名为`avatar`，那么您的专栏需要命名为`avatar_uid`。第二件要注意的事情是，你可以让蜻蜓自动将原始文件名存储在一个所谓的“魔法”列中。为此，在您的表中添加一个类型为`string`的`image_name`列。

默认情况下，蜻蜓集成了 ImageMagick，这是一个非常酷的图像处理库，允许对图像进行各种修改。只要我们要使用 ImageMagick 的功能，您就应该在[页面](http://www.imagemagick.org/script/binary-releases.php)上选择合适的方法来安装它。如果您不打算在项目中使用 ImageMagick，请注释掉(或删除)这一行:

*config/initializer/dragonfly . Rb*

```
[...]
plugin :imagemagick
[...]
```

如果您使用的是 Rails 4，那么打开生产环境文件并取消对该行的注释以启用 rack cache:

*配置/环境/生产. rb*

```
config.action_dispatch.rack_cache = true
```

酷！在这一点上，我们已经设置了蜻蜓使用我们的应用程序，并准备进入下一部分。

## 上传照片

该是充实控制器的时候了。我们将只添加几个简单的方法:

*照片 _ 控制器. rb*

```
class PhotosController < ApplicationController
  def index
    @photos = Photo.all
  end

  def new
    @photo = Photo.new
  end

  def create
    @photo = Photo.new(photo_params)
    if @photo.save
      flash[:success] = "Photo saved!"
      redirect_to photos_path
    else
      render 'new'
    end
  end

  private

  def photo_params
    params.require(:photo).permit(:image, :title)
  end
end
```

如果你在 Rails 3 上，那么你不会有`photo_params`方法。相反，将这一行添加到您的模型文件中:

*photo.rb*

```
[...]
attr_accessible :image, :title
[...]
```

请注意，您不需要在这里指定`image_uid`——蜻蜓会处理的。

不要忘记设置路线:

*routes.rb*

```
[...]
resources :photos, only: [:new, :create, :index]
root to: 'photos#index'
[...]
```

清单中的最后一点是视图。从索引开始:

*index.html.erb*

```
<h1>List of photos</h1>

<%= link_to 'Add your photo', new_photo_path, class: 'btn btn-lg btn-primary' %>
```

*new.html.erb*

```
<h1>New photo</h1>

<%= form_for @photo do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= f.label :image %>
    <%= f.file_field :image, required: true %>
  </div>

  <%= f.submit 'Submit', class: 'btn btn-primary btn-lg' %>
<% end %>
```

此时，您可以启动您的服务器并尝试上传一些文件。唯一的问题是你不会在主页上看到它们，所以让我们来解决这个问题:

*index.html.erb*

```
<h1>List of photos</h1>

<%= link_to 'Add your photo', new_photo_path, class: 'btn btn-lg btn-primary' %>

<ul class="row" id="photos-list">
  <% @photos.each do |photo| %>
    <li class="col-xs-3">
      <%= link_to image_tag(photo.image.thumb('180x180#').url, alt: photo.title, class: 'img-thumbnail'),
                  photo.image.url, target: '_blank' %>
      <p><%= photo.title %></p>
    </li>
  <% end %>
</ul>
```

也应用一些样式:

*application.css.scss*

```
#photos-list {
  padding: 0;
  margin: 0;
  margin-top: 30px;
  clear: both;
  li {
    list-style-type: none;
    padding: 0;
    margin: 0;
    max-height: 250px;
    margin-bottom: 10px;
    text-align: center;
    p {
      margin: 0;
      margin-top: 10px;
      height: 40px;
      overflow: hidden;
    }
  }
}
```

注意视图中`thumb('180x180#')`方法的使用。多亏了 ImageMagick，我们可以随心所欲地裁剪图像。`#`符号意味着我们希望保留图像的中心部分。如果我们指定`180x180#ne`，那么图像的东北部分将会保留。有许多其他选项你可以通过(例如，是否应该保持纵横比)——在这里[阅读它们](https://markevans.github.io/dragonfly/imagemagick/)。

`image.url`提供原始图像的 url。在我们的例子中，这个图像将在点击缩略图时打开。

## 添加验证

现在，我们的用户可以上传任何文件，不仅仅是图片。此外，这些文件可以是任何大小。在大多数情况下，这不是一件好事，您可能想知道是否有办法给图像添加一些验证。当然啦！

蜻蜓在初始化器中为我们的模型配备了所需的方法:

```
# Add model functionality
if defined?(ActiveRecord::Base)
  ActiveRecord::Base.extend Dragonfly::Model
  ActiveRecord::Base.extend Dragonfly::Model::Validations
end
```

我们需要做的就是修改模型:

*photo.rb*

```
validates :title, presence: true, length: {minimum: 2, maximum: 20}
validates :image, presence: true

validates :image, presence: true
validates_size_of :image, maximum: 500.kilobytes,
                  message: "should be no more than 500 KB", if: :image_changed?

validates_property :format, of: :image, in: [:jpeg, :jpg, :png, :bmp], case_sensitive: false,
                   message: "should be either .jpeg, .jpg, .png, .bmp", if: :image_changed?
```

如您所见，我们的验证确保了`title`不少于 2 个字符，不超过 20 个字符。另外，`image`是必需的，它的大小(不超过 500 KB)和格式(仅。jpg“，”。png“，”。bmp”)是有效的。我们还使用了`image_changed?`方法来确保这些验证只有在文件被更改时才会触发。

蜻蜓允许您指定其它验证。例如，您可能希望检查图像的宽度是否不超过 400 像素，甚至提供一条关于实际宽度的消息:

```
validates_property :width, of: :image, in: (0..400),
                           message: proc{ |actual, model| "Unlucky #{model.title} - was #{actual}" }
```

点击阅读更多相关信息[。](https://markevans.github.io/dragonfly/models/#validations)

我们需要稍微调整一下视图，以便它显示保存时发现的任何错误:

*new.html.erb*

```
<h1>New photo</h1>

<%= form_for @photo do |f| %>
  <%= render 'shared/errors', object: @photo %>
[...]
```

我在这里使用了一个部分，所以让我们创建它:

*shared/errors.html.erb*

```
<% if object.errors.any? %>
  <div id="errors_full_text" class="bg-warning">
    <h2>Found these errors when saving:</h2>
    <ul>
      <% object.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

也应用一些样式:

*application.css.scss*

```
[...]
#errors_full_text {
  padding: 10px;
  margin-bottom: 10px;
  h2 {
    font-size: 18px;
  }
}
```

尝试上传一些无效的文件，然后看错误显示。

## 图像处理和分析

假设我们希望所有上传的文件都转换成 JPEG 格式。蜻蜓为我们提供了一个方便的方法:回调。

在我们的模型中:

*photo.rb*

```
[...]
dragonfly_accessor :image do
  after_assign { |img| img.encode!('jpg', '-quality 80') }
end
[...]
```

上面的代码将所有图像转换为质量为 80 的 JPEG(注意，如果有人试图上传带有透明度的 PNG 文件，透明度将会丢失)。

如果您允许您的用户上传不是图像的文件，您将在这个回调中以一个错误结束。幸运的是，有一种简单的方法可以检查上传的文件是否是图像:

```
dragonfly_accessor :image do
   after_assign do |img|
    img.encode!('jpg', '-quality 80') if img.image?
   end
 end
```

你可以做得更多。例如，旋转上传的图像:

```
dragonfly_accessor :image do
   after_assign do |img|
    img.rotate!(90) # 90 is the amount of degrees to rotate
   end
 end
```

点击这里阅读更多关于蜻蜓的试镜。

有一些其他的方法可以获得关于图像的信息。比如:`width`、`height`、`aspect_ratio`等(完整列表此处为)。

请记住，所有这些方法都是动态分析图像。有一种方法可以在“神奇”栏的帮助下保存这些信息。例如，让我们存储图像大小。为此，创建并应用此迁移:

```
rails g migration add_image_size_to_photos image_size:integer
rake db:migrate
```

然后，稍微调整一下视图:

*index.html.erb*

```
[...]
<p><%= photo.title %></p>
<small><%= number_to_human_size photo.image.size %></small>
[...]
```

现在，上传一张新的图片，观察结果。它的大小会自动存储在`image_size`栏中！很棒，不是吗？

## 使用其他数据存储

蜻蜓会将所有上传的图像存储在本地硬盘的`public/system/dragonfly`目录下。顺便说一下，不要忘记将这个目录添加到`.gitignore`文件中，否则部署时您所有的本地映像都会被推送到服务器上(除非这是您想要的)。

要改变这种行为，请修改`config/initializers/dragonfly.rb`文件。对于本演示，我们使用亚马逊 S3 云文件存储，但也有一些其他选项可用:

*   丹尼尔·莱维特的 Dropbox
*   安东·迪特勒著
*   [马克·埃文斯的沙发](https://github.com/markevans/dragonfly-couch_data_store)

还有其他的，但他们似乎已经过时，不与蜻蜓 1+的工作。您甚至可以[创建自己的数据存储库](https://markevans.github.io/dragonfly/data-stores/#building-a-custom-data-store)。

为了与亚马逊 S3 集成，将`dragonfly-s3_data_store`添加到 Gemfile 中(我们已经这样做了)。蜻蜓很酷的一点是改变数据存储非常容易。你所要做的就是改变初始化文件。当然，你需要一个亚马逊 AWS 账户，你可以在这里创建[。当你完成后打开你的](https://aws.amazon.com/) [AWS 管理控制台](https://console.aws.amazon%20.com)，在列表中找到 S3 并点击它。

创建一个新的 bucket(在这里阅读更多关于 bucket 命名和性能的信息)并记住它的名称。

现在打开“**your account**–Security Credentials”(在右上角)并展开“访问密钥”部分。创建一个新的密钥对并下载它(以后您将没有下载它的选项！)

更改初始化文件(可用选项的完整列表可以在[这里](https://github.com/markevans/dragonfly-s3_data_store#available-configuration-options)找到):

*config/initializer/dragonfly . Rb*

```
require 'dragonfly/s3_data_store'

[...]

url_format "/media/:job/:name"

if Rails.env.development? || Rails.env.test?
  datastore :file,
            root_path: Rails.root.join('public/system/dragonfly', Rails.env),
            server_root: Rails.root.join('public')
else
  datastore :s3,
            bucket_name: YOUR_BUCKET_NAME,
            access_key_id: YOUR_S3_KEY,
            secret_access_key: YOUR_S3_SECRET,
            url_scheme: 'https'
end
[...]
```

小心使用您的密钥对，千万不要暴露给任何人。如果您在 Heroku 上部署，使用它的[环境变量](https://devcenter.heroku.com/articles/config-vars)来存储密钥对。它**不应该**出现在版本控制中！

现在所有用户的文件都会上传到生产中的亚马逊。如果要提供指向这些文件的链接，请按如下方式修改索引视图:

*index.html.erb*

```
<%= link_to image_tag(photo.image.thumb('180x180#').url, alt: photo.title, class: 'img-thumbnail'),
                  photo.image.remote_url, target: '_blank' %>
```

注意使用了`remote_url`而不仅仅是`url`。它会指向亚马逊上的文件。

## 一点优化

在`index.html.erb`中，我们使用`thumb`方法来显示缩略图。这没问题，但是缩略图生成是动态发生的。我们可以在一个页面上有许多图像，这种情况会发生在每个图像上。试着优化一下怎么样？

这个问题很容易解决。在第一次动态处理之后，保存结果缩略图(在我们的例子中，保存到 Amazon 存储中)并直接为后续请求提供服务。显然，我们需要在表中存储对这个缩略图的引用。让我们创建这个表:

```
rails g model Thumb uid:string job:string
rake db:migrate
```

现在将这段代码添加到您的初始值设定项中:

*config/initializer/dragonfly . Rb*

```
[...]
# Override the .url method...
define_url do |app, job, opts|
  thumb = Thumb.find_by_job(job.signature)
  # If (fetch 'some_uid' then resize to '180x180') has been stored already, give the datastore's remote url ...
  if thumb
    app.datastore.url_for(thumb.uid, :scheme => 'https')
    # ...otherwise give the local Dragonfly server url
  else
    app.server.url_for(job)
  end
end

# Before serving from the local Dragonfly server...
before_serve do |job, env|
  # ...store the thumbnail in the datastore...
  uid = job.store

  # ...keep track of its uid so next time we can serve directly from the datastore
  Thumb.create!(
      :uid => uid,
      :job => job.signature   # 'BAhbBls...' - holds all the job info
  )                           # e.g. fetch 'some_uid' then resize to '180x180'
end
[...]
```

如您所见，我们将缩略图存储在服务器上，并将对它的引用保存在`thumbs`表中。我们还重新定义了`url`方法，以便它在动态生成图像之前，尝试在我们的存储中搜索已经生成的图像。

现在尝试重新加载您的主页并检查缩略图的`src`属性——它应该指向 Amazon(当然，如果您在生产环境中)。

## 结论

我希望你已经学到了一些东西，并喜欢阅读它。您使用什么 gem 来管理应用程序中的附件？在评论中分享你的经验吧！

## 分享这篇文章