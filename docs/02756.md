# 用 Shrine 上传你可以相信的 Rails 文件

> 原文：<https://www.sitepoint.com/rails-file-uploading-you-can-believe-in-with-shrine/>

![](img/440586f99f9d81c2ba549fc0c4149cd3.png)

在我之前的文章中，我们学习了如何使用 Refile 在 [Rails 中启用文件上传。今天我们来看看另一个上传宝石的文件——](https://www.sitepoint.com/easily-allow-file-upload-rails-refile/)[神社](http://shrinerb.com/)。Shrine 是 Ruby 应用程序中的另一个文件上传工具包。

Shrine 的作者 Janko Marohni 在其介绍性博客文章中指出，Shrine 深受 Refile 和 CarrierWave 的影响。Shrine 对所有东西都使用插件系统，附带了[大量的插件](http://shrinerb.com/#plugins)，其中一些我今天会用到。

我们将使用 Shrine 构建一个存储图像和书籍的 Rails 应用程序。是的，书……你可能想给你的朋友们看看你最新收藏的书。我希望你和我一样喜欢阅读。让我们把这个东西造出来，让全世界都看到。

### ImageMagick 安装

要使用 Shrine，您需要安装 ImageMagick。根据您的操作系统，使用以下步骤之一。

Mac 用户:

```
brew install imagemagick 
```

Ubuntu 用户:

```
sudo apt-get install imagemagick 
```

如果以上两者都不适合你，也就是说你使用的是 Windows 或其他系统，请查看 ImageMagick 网站上的[安装页面](http://www.imagemagick.org/script/binary-releases.php)。Windows 7 及更高版本有二进制版本。

### Rails 应用程序生成

运行命令创建新的应用程序:

```
rails new book-showcase 
```

我们将使用单个控制器

```
rails generate controller Books 
```

设置相应的路线:

*config/routes.rb*

```
 Rails.application.routes.draw do
  resources :books

  root to: "books#index"
end 
```

## 集成神龛

在我们继续创建模型之前，让我们将 Shrine 引入 Rails 应用程序。打开你的**宝石文件**，添加神殿宝石和它的附属物。

*Gemfile*

```
 # Shrine Dependencies
gem 'fastimage'
gem 'image_processing'
gem 'mini_magick'
gem 'shrine' 
```

*   fastimage 用于提取图像尺寸。
*   image_processing 包括一些使用 ImageMagick 的助手
*   mini_magick 是 RMagick 的低内存替代品，RMagick 是 ImageMagick 的 Ruby 包装器

奔跑

```
bundle install 
```

现在让我们创建我们的图书模型:

```
rails generate model Book 
```

打开您的迁移，使其看起来像这样:

*xxx_create_books.rb*

```
 class CreateBooks < ActiveRecord::Migration
  def change
    create_table :books do |t|
      t.string :name
      t.text :image_data

      t.timestamps null: false
    end
  end
end 
```

运行迁移

```
rake db:migrate 
```

我们需要为模型配置 Shrine 功能。在你的**模型**文件夹中创建一个新文件，命名为 **image_uploader.rb** :

```
touch app/models/image_uploader.rb 
```

将以下内容粘贴到您刚刚创建的文件中:

*app/模型/图片* uploader.rb_

```
class ImageUploader < Shrine
  include ImageProcessing::MiniMagick

  plugin :activerecord
  plugin :determine_mime_type
  plugin :logging, logger: Rails.logger
  plugin :remove_attachment
  plugin :store_dimensions
  plugin :validation_helpers
  plugin :versions, names: [:original, :thumb]

  Attacher.validate do
    validate_max_size 2.megabytes, message: 'is too large (max is 2 MB)'
    validate_mime_type_inclusion ['image/jpg', 'image/jpeg', 'image/png', 'image/gif']
  end

  def process(io, context)
    case context[:phase]
    when :store
      thumb = resize_to_limit!(io.download, 200, 200)
      { original: io, thumb: thumb }
    end
  end
end 
```

如前所述，Shrine 为我们提供了不同功能的不同插件。以下是我们上传器中包含的插件及其功能:

*   `:activerecord`:扩展了“附件”接口，支持 ActiveRecord。每当一个“附件”模块被包含时，额外的回调就被添加到模型中。
*   `:determine_mime_type`:存储上传文件的实际 MIME 类型。
*   `:logging`:日志插件记录执行的任何存储/处理/删除。通过将`Rails.logger`传递给`:logger`选项，我们将日志记录器更改为在我们的 Rails 应用程序中有用。
*   `:remove_attachment`:remove _ attachment 插件允许您通过 web 表单上的复选框删除附件。
*   `:store_dimensions`:该插件提取并存储上传图像的尺寸。
*   `:validation_helpers`:为验证附件提供帮助方法。看看下面的`Attacher`块:

*app/模型/图片* uploader.rb_

```
Attacher.validate do
  validate_max_size 2.megabytes, message: 'is too large (max is 2 MB)'
  validate_mime_type_inclusion ['image/jpg', 'image/jpeg', 'image/png', 'image/gif']
end 
```

验证器非常简单。这里，我们将文件大小限制为 2MB 或更小，可接受的文件类型为`jpg`、`png`和`gif`。

*   版本插件可以让你的上传者处理不同版本的图片。要生成版本，您只需像我们在上传器中所做的那样返回一个版本散列。

*app/模型/图片* uploader.rb_

```
plugin :versions, names: [:original, :thumb] 
```

现在，在`process`方法中，处理任何版本(除了`:original`)并返回它。如您所见，我们正在为`store`阶段做这件事，也就是说，当文件被存储时:

```
...

def process(io, context)
  case context[:phase]
  when :store
    thumb = resize_to_limit!(io.download, 200, 200)
    { original: io, thumb: thumb }
  end
end 
```

设置您的`BooksController`:

*app/控制器/书籍* controller.rb_

```
class BooksController < ApplicationController
  before_action :set_book, only: [:show, :edit, :update, :destroy]

  def index
    @books = Book.all
  end

  def show
  end

  def new
    @book = Book.new
  end

  def edit
  end

  def create
    @book = Book.new(book_params)

    if @book.save
      redirect_to @book, notice: 'book was successfully created.'
    else
      render :new
    end
  end

  def update
    if @book.update(book_params)
      redirect_to @book, notice: 'book was successfully updated.'
    else
      render :edit
    end
  end

  def destroy
    @book.destroy
    redirect_to books_url, notice: 'book was successfully destroyed.'
  end

  private

  def set_book
    @book = Book.find(params[:id])
  end

  def book_params
    params.require(:book).permit(:name, :image, :remove_image)
  end
end 
```

您需要为 Shrine 创建一个初始化器:

```
touch config/initializers/shrine.rb 
```

粘贴以下内容:

*config/initializer/shrine . Rb*

```
require "shrine"
require "shrine/storage/file_system"
require "image_processing/mini_magick"

Shrine.storages = {
  cache: Shrine::Storage::FileSystem.new("public", prefix: "uploads/cache"),
  store: Shrine::Storage::FileSystem.new("public", prefix: "uploads/store"),
} 
```

Shrine 有两个默认存储:`FileSystem`和`S3`。

### 文件系统

文件系统存储处理上传到应用程序本身的文件系统。它用一个`base folder`和一个`prefix`初始化，就像我们上面的一样。`prefix`是一个相对于`base folder`的目录，文件将存储在那里，它包含在 URL 中。

### S3

S3 存储负责上传到亚马逊网络服务(AWS) S3 服务。要使用 S3 存储，您需要在您的`Gemfile`中安装 [aws-sdk](https://github.com/aws/aws-sdk-ruby) gem。不要忘记`bundle install`。

现在转到`config/initilazers/shrine.rb`为 S3 储物件做准备。

*config/initializer/shrine . Rb*

```
require "shrine"
require "shrine/storage/s3"

s3_options = {
  access_key_id:     ENV.fetch("S3_ACCESS_KEY_ID"),
  secret_access_key: ENV.fetch("S3_SECRET_ACCESS_KEY"),
  region:            ENV.fetch("S3_REGION"),
  bucket:            ENV.fetch("S3_BUCKET"),
}

Shrine.storages = {
  cache: Shrine::Storage::S3.new(prefix: "cache", **s3_options),
  store: Shrine::Storage::S3.new(prefix: "store", **s3_options),
} 
```

一开始我们要求`shrine`和`shrine s3`。S3 `prefix`选项是通过我们的文件将被上传的桶的名称。在上面的例子中，将有两(2)个桶；一个给`store`，另一个给`cache`。下一个选项`s3-options`，让我们可以访问 AWS。您将需要[获取 AWS 访问密钥和秘密访问密钥](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)并将它们放入环境变量中。

现在让我们在视图上工作。持我们的观点`form`:

*app/views/books/*form . html . erb _

```
<%= form_for(@book) do |f| %>
  <%= render "error_messages", target: @book %>

  <div class="field">
    <%= f.label :name %><br>
    <%= f.text_field :name %>
  </div>

  <div class="field">
    <%= f.label :image %><br>
    <%= f.file_field :image %>
  </div>
  <%- if @book.image_data? %>
    <div class="field">
      <%= image_tag @book.image_url(:thumb) %>
    </div>
    <div class="field">
      Remove attachment: <%= f.check_box :remove_image %>
    </div>
  <%- end %>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %> 
```

我希望我们把错误信息分开，这是我最近养成的一个新习惯。在视图中创建一个名为`application`的文件夹。创建一个分部 calledj `_error_messages.html.erb`并粘贴错误消息:

*app/views/application/* 错误 *messages.html.erb*

```
<% if target.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@book.errors.count, "error") %> prohibited this book from being saved:</h2>

      <ul>
      <% @book.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
<% end %> 
```

形式真的很简单。只需使用`form.file_field :image`来呈现适当的字段。

让我们在第`show`页上工作。此时，当我们试图提交表单时，我们会收到一条`Action Controller: Exception caught`消息，因为缺少`show`模板。

这是您的`show`页面的视图代码应该看起来的样子。

*app/views/books/show . html . erb*

```
<p id="notice"><%= notice %></p>

<div>
  <strong>Name:</strong>
  <%= @book.name %>
</div>
<div>
  <strong>Image:</strong>
  <%= image_tag @book.image_url(:original) %>
</div>

<%= link_to 'Edit', edit_book_path(@book) %> |
<%= link_to 'Back', books_path %> 
```

让我们也设置一下我们的`Edit`页面:

*app/views/books/html.erb*

```
<h1>Editing Book</h1>

<%= render 'form' %>

<%= link_to 'Show', @book %> |
<%= link_to 'Back', books_path %> 
```

和第`index`页:

*app/views/books/index . html . erb*

```
<p id="notice"><%= notice %></p>

<h1>Listing Books</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Image</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @books.each do |book| %>
      <tr>
        <td><%= book.name %></td>
        <td><%= image_tag book.image_url(:thumb) %></td>
        <td><%= link_to 'Show', book %></td>
        <td><%= link_to 'Edit', edit_book_path(book) %></td>
        <td><%= link_to 'Destroy', book, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Book', new_book_path %> 
```

### 版本

在 Rails 应用程序中，您可能希望在原始图像旁边存储一个缩略图(或其他版本)。为此你需要加载我上面提到的`versions`插件。我们已经在`image_uploader.rb`文件中这样做了。现在，让`#process`方法返回一个版本的散列:

*app/视图/模型/图片* uploader.rb_

```
class ImageUploader < Shrine
  include ImageProcessing::MiniMagick

  plugin :activerecord
  plugin :determine_mime_type
  plugin :logging, logger: Rails.logger
  plugin :remove_attachment
  plugin :store_dimensions
  plugin :validation_helpers
  plugin :versions, names: [:original, :large, :medium, :small, :thumb]

  Attacher.validate do
    validate_max_size 2.megabytes, message: 'is too large (max is 2 MB)'
    validate_mime_type_inclusion ['image/jpg', 'image/jpeg', 'image/png', 'image/gif']
  end

  def process(io, context)
    if context[:phase] == :store
      size_700 = resize_to_limit!(io.download, 700, 700)
      size_500 = resize_to_limit(size_700,    500, 500)
      size_300 = resize_to_limit(size_500,    300, 300)
      thumb = resize_to_limit(size_300, 200, 200)

      { original: io, large: size_700, medium: size_500, small: size_300, thumb: thumb }
    end
  end
end 
```

你可以选择想要在视图中呈现的版本，就像我们对`index`的视图所做的那样。打开你的`index`视图，改变渲染图像为小版本。

*app/views/books/index . html . erb*

```
<p id="notice"><%= notice %></p>

<h1>Listing Books</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Image</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @books.each do |book| %>
      <tr>
        <td><%= book.name %></td>
        <td><%= image_tag book.image_url(:small) %></td>
        <td><%= link_to 'Show', book %></td>
        <td><%= link_to 'Edit', edit_book_path(book) %></td>
        <td><%= link_to 'Destroy', book, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Book', new_book_path %> 
```

## 结论

在本文中，我们学习了如何使用 Shrine 在 Rails 应用程序中启用图像上传。如果你想尝试一些这里没有提到的令人兴奋的功能，你可以在 Github 上查看 [Shrine 的页面。Shrine 对将文件上传到后台作业或直接上传有很大的支持，这只是列举了几个我们今天没有提到的特性。此外，Shrine 不仅仅适用于 Rails，你可以在任何 Ruby 应用程序中使用它。](https://github.com/janko-m/shrine)

感谢您的阅读，再见！

## 分享这篇文章