# 使用 Rails 进行图像处理

> 原文：<https://www.sitepoint.com/image-processing-rails/>

![Collection of images](img/ea7cc866688c796ae4d6df361b550718.png)

图像是任何应用程序的重要组成部分。从社交网络到简单的 bug 追踪器，图像扮演着重要的角色。然而，管理映像并不是一项简单的任务，需要提前进行大量规划。

在本文中，请允许我演示如何在 Rails 中实现这一点。我将向您展示如何处理您的图像，并在后端创建多个版本。我们还将看到如何在不损失质量的情况下通过压缩这些图像来提高页面的性能。

## 入门指南

本教程中的示例运行在 Rails 4.2 上，使用 MongoDb 数据库和 HAML 来呈现视图。然而，这里使用的代码片段应该与任何版本的 Rails 兼容，尽管有微小的配置差异。

### 搭建舞台

ImageMagick 是 POSIX 系统上图像处理的首选库。如果您的系统上没有安装 ImageMagick，可以使用适用于您的操作系统的软件包管理器安装它。在 Ubuntu 上:

```
sudo apt-get -y install imagemagick
sudo apt-get -y install libmagic-dev
sudo apt-get -y install libmagickwand-dev
```

在 Mac OS X 上，我推荐使用自制软件:

```
brew install imagemagick
```

现在，我们需要一个 Ruby 适配器来连接本机 ImageMagick 库。我个人更喜欢 MiniMagick，因为它是轻量级的，并且几乎完成了典型应用程序需要的所有功能:

```
# Gemfile

gem 'mini_magick'
```

## 操场

在构建任何严肃的东西之前，让我们先玩玩 MiniMagick 的一些特性。打开 Rails 控制台(`rails c`)并运行以下命令:

```
# Open an image from a website

image = MiniMagick::Image.open("https://s3.amazonaws.com/StartupStockPhotos/20140808_StartupStockPhotos/85.jpg")

# Get the Image's width
image.width # 4928

# Get the image's height
image.height #3264
```

天哪，太大了。让我们看看是否可以调整它的大小以适应我们的 iPad:

```
image.resize "2048x1536"

# Now get the image's new width and height

p "Width is => #{image.width} and height is => #{image.height}"
```

好吧，这样就行了。等等，这个修改过的文件存储在哪里？

```
image.path # temp path
```

处理后的图像存储在临时路径中，将被清除。要将它保存到磁盘，只需调用 write 方法:

```
image.write "public/uploads/test.jpg"
```

### 转换图像

你要做的最常见的操作之一是将图像转换成不同的格式。MiniMagick 让这变得非常简单:

```
image.format "png"
image.write "public/uploads/test.png"
```

### 变得疯狂

您也可以在单个块中组合多个操作:

```
image.combine_options do |i|
  i.resize "2048x1536"
  i.flip
  i.rotate "-45"
  i.blur "0x15"
end
image.write "public/uploads/blur.png"

![Some weird result](blur.png)
```

好吧，我有点过火了。在我看来，我只是想展示你可以用 MiniMagick 做的所有很酷的东西:)。

现在，让我们看看如何将它与我们的 Rails 应用程序结合起来。

## 上传文件

Carrierwave 是一个奇妙的宝石，它简化了 Ruby 中的文件上传。它还可以很好地与 MiniMagick 交互，让我们的生活更加简单。

```
# Gemfile

gem 'carrierwave'
gem 'carrierwave-mongoid', :require => 'carrierwave/mongoid'
```

注意:如果你使用的是 ActiveRecord 或 DataMapper，配置可能会稍有不同，官方的 Carrierwave [文档](https://github.com/carrierwaveuploader/carrierwave)会为你指明方向。

捆绑以获取所有这些宝石:

```
bundle install
```

创建我们的第一个上传者:

```
#app/uploaders/image_uploader.rb

class ImageUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this Uploader:
  storage :file
  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/images"
  end
end
```

这里的代码是不言自明的。`storage :file`指示服务器将图像存储在本地服务器上，而`store_dir`指定位置。

由于文件是通过互联网发送的，因此最佳做法是始终过滤传入的文件:

```
# app/uploaders/image_uploader.rb
...
# Add a white list of extensions which are allowed to be uploaded.
# For images you might use something like this:
def extension_white_list
  %w(jpg jpeg png gif)
end
...
```

此代码片段过滤掉此处指定的文件类型之外的文件类型。这绝不是万无一失的，但它是抵御任何障碍攻击的第一级过滤器。

将此上传程序安装到我们的图像模型:

```
# app/models/image.rb

class Image
  include Mongoid::Document
  include Mongoid::Timestamps
  include Mongoid::Paranoia
  include Mongoid::Attributes::Dynamic
  include Rails.application.routes.url_helpers

  mount_uploader :media, ImageUploader, mount_on: :media_filename
end
```

编辑 **image_uploader.rb** 处理上传的图像:

```
# app/uploaders/image_uploader.rb

#.....
process :resize_to_fill => [200, 200]
process :convert => 'png'
#.....
```

尝试从 Rails 控制台创建新映像:

```
media = File.open("/Users/test/Desktop/image/jpg")
img = Image.new(:media => media)
img.save
```

上传的图像可在`store_dir`下获得。但是，上传的图像会被立即处理并用 200×200 的图像覆盖。我们将没有原始文件的副本用于将来的编辑。要避免这种情况，请创建文件的多个版本。

```
# app/uploaders/image_uploader.rb

#.....
version :thumb do
  process :resize_to_fit => [100, 100]
  process :convert => 'jpg'
end

version :cover   do
  process :resize_to_fit => [240, 180]
  process :convert => 'jpg'
end

#.....
```

这将创建两个新版本，以及原始图像。检查由 Carrierwave 创建的版本:

```
img.media.versions[:thumb] # returns the thumb image instance
img.media.versions[:cover] # returns the cover image instance
```

你注意到这些图像是瞬间生成的吗？这意味着图像转换发生在同一个线程中，执行被阻塞，直到它完成。在生产应用程序中，不希望在同一个线程中创建图像的多个版本。相反，我们应该有条件地处理这个问题。

```
# app/uploaders/image_uploader/rb

#....
version :cover, :if => :is_live? do
  process :resize_to_fit => [240, 180]
  process :convert => 'jpg'
end

def is_live?(img = nil)
  @is_live
end

def is_live=(value)
  @is_live = value
end
#....
```

现在，当我们尝试创建一个新的图像时，封面版本将不会生成。我们可以在需要时手动触发它，只需运行:

```
img.media.is_live = true
img.save
img.media.recreate_versions! :cover
```

这段代码也运行在前台，是一个阻塞操作，但至少它被推迟到最后一刻。我们可以进一步用 Resque 在后台运行:

```
# lib/resque/image_queue.rb
class ImageQueue
  @queue = :image_queue
  def self.perform(image_id)
    image = Image.find image_id
    img.media.is_live= true
    img.save
    img.media.recreate_versions! :cover
  end
end
```

然后，将其排入队列:

```
Resque.enqueue(ImageQueue, img.id.to_s)
```

## 提高性能

图像很重，会降低网站速度。减轻页面重量的一种方法是压缩这些图像。载波图像优化帮助我们压缩我们的图像没有任何麻烦。

将此添加到您的 gem 文件中:

```
gem 'carrierwave-imageoptimizer'
```

并编辑图像上传程序

```
# app/uploaders/image_uploader.rd

#.....

include CarrierWave::ImageOptimizer
process :optimize
process :quality => 100
#....
```

这将压缩所有图像，而不会造成任何视觉损失。这种工作方式是关于图像的所有元信息都被去除。平均而言，这可以减少 20-30%的大小。

# 包扎

图像处理是一个巨大的垂直领域，我们仅仅触及了表面。我们可以用它做很多很酷的东西。我希望我已经引起了你对这篇文章的兴趣。请在评论中分享你的想法。

## 分享这篇文章