# 使用 Rails 和 Refile 轻松上传文件

> 原文：<https://www.sitepoint.com/easily-allow-file-upload-rails-refile/>

![](img/92750769c982dff12095afd9031d1d47.png)

不能低估在 web 应用程序中支持图片上传的功能。尤其是当它是一个与拥有配置文件的用户有关的应用程序时。有很多可用的宝石来帮助你用几行代码处理这个问题，其中大部分已经在这里的 [SitePoint Ruby](https://www.sitepoint.com/ruby) 上讨论过了。

它们包括:

*   [载波波](https://github.com/carrierwaveuploader/carrierwave)–[图像处理带轨道](https://www.sitepoint.com/image-processing-rails/)由[瓦苏 K](https://www.sitepoint.com/author/vasudevank/) 完成。
*   [回形针](https://github.com/thoughtbot/paperclip)–[用回形针上传文件](https://www.sitepoint.com/uploading-files-with-paperclip/)作者[伊利亚·博德罗夫-克鲁科夫斯基](https://www.sitepoint.com/author/ibodrov/)
*   [蜻蜓](https://github.com/markevans/dragonfly)—[与蜻蜓一起更好地上传](https://www.sitepoint.com/file-uploads-dragonfly/)作者[伊利亚·博德罗夫-克鲁科夫斯基](https://www.sitepoint.com/author/ibodrov/)

在本教程中，我们将学习如何使用 [Refile](https://github.com/refile/refile) 在 Rails 中启用图像上传。正如 [Refile 的 github 页面](https://github.com/refile/refile)上所写的，这是一个用于 Ruby 应用程序的现代文件上传库。它很简单，但功能强大。

对于好奇的读者来说，你可能想知道是什么让 Refile 变得更好…我刚才说了是吗？无论如何，那是留给你决定的。:)

重新归档功能包括:

*   可配置的后端、文件系统、S3 等。
*   方便与 ORM 集成。
*   对图像和其他文件的即时操作。
*   流式 IO 支持快速和内存友好的上传。
*   跨表单工作重新显示，即当验证失败时，即使在 S3 上。
*   轻松直接上传，甚至到 S3。
*   支持多次上传。

你觉得怎么样？你想试试吗？

```
if "answer" = "yes"
  hop_on_board_with_me
else
  still_tag_along
end 
```

我们走吧！

## Rails 应用程序设置

我们将制作一个应用程序，基本上可以保存我们家人和朋友的图像。我们将是这个应用程序的唯一用户，所以它会尽可能简单。

创建一个名为`photobook`的新 Rails 应用程序:

```
rails new photobook 
```

让我们为我们的应用程序生成一个脚手架:

```
rails g scaffold Photo name:string 
```

接下来，我们应用我们的迁移:

```
rake db:migrate 
```

## 集成重新文件

打开您的`Gemfile`并添加宝石:

```
#Gemfile

gem 'refile', require: 'refile/rails'
gem 'refile-mini_magick' 
```

安装您刚刚添加的 gems:

```
bundle install 
```

`refile-mini_magick`用于图像处理。你需要在你的系统上安装 [ImageMagick](http://imagemagick.org/script/index.php) 。

根据您的系统，您可以按照以下步骤安装 ImageMagick:

```
brew install imagemagick # OS X

sudo apt-get install imagemagick # Ubuntu 
```

之后，添加附件方法以在您的模型中使用 Refile:

```
#app/models/photo.rb

class Photo < ActiveRecord::Base
  attachment :profile_image
end 
```

让我们生成一个新的迁移，将`profile_image`的新列添加到我们的`photos`表中:

```
rails generate migration add_profile_image_to_photos profile_image_id:string
rake db:migrate 
```

我们需要设置强参数来允许`profile_image`在我们的`photos_controller`中:

```
#app/controllers/photos_controller.rb

def photo_params
  params.require(:photo).permit(:name, :profile_image)
end 
```

导航到您的视图，在表单中添加附件字段:

```
#app/views/_form.html.erb

<%= form_for(@photo) do |f| %>
  <% if @photo.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@photo.errors.count, "error") %> prohibited this photo from being saved:</h2>

      <ul>
      <% @photo.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :name %><br>
    <%= f.text_field :name %>
  </div>
  <div>
    <%= f.attachment_field :profile_image %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %> 
```

要在视图中显示该文件，请编辑索引并显示页面，如下所示:

```
#app/views/index.html.erb

<p id="notice"><%= notice %></p>

<h1>Listing Photos</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @photos.each do |photo| %>
      <tr>
        <td><%= photo.name %></td>
        <td><%= image_tag(attachment_url(photo, :profile_image, :fill, 100, 100)) %></td>
        <td><%= link_to 'Show', photo %></td>
        <td><%= link_to 'Edit', edit_photo_path(photo) %></td>
        <td><%= link_to 'Destroy', photo, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Photo', new_photo_path %>

#app/views/show.html.erb

<p id="notice"><%= notice %></p>

<p>
  <strong>Name:</strong>
  <%= @photo.name %>
  <%= image_tag attachment_url(@photo, :profile_image, :fill, 100, 100, format: "jpg") %>
</p>

<%= link_to 'Edit', edit_photo_path(@photo) %> |
<%= link_to 'Back', photos_path %> 
```

## 删除附件

您可能想要移除旧图像并添加新图像。在这种情况下，Refile 有一个功能可以提供帮助。当您使用`attachment`方法时，它会向您的模型添加一个属性，该方法设计用于表单中的复选框。

下面是如何做到这一点:

```
#app/views/photo/_form.html.erb

<%= form_for(@photo) do |f| %>
  <% if @photo.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@photo.errors.count, "error") %> prohibited this photo from being saved:</h2>

      <ul>
      <% @photo.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :name %><br>
    <%= f.text_field :name %>
  </div>
  <div>
    <%= f.label :profile_image %>
    <%= f.attachment_field :profile_image %>

    <% if @photo.profile_image_id? %>
      <%= image_tag attachment_url(@photo, :profile_image, :fill, 100, 100, format: "jpg") %>

      <%= f.checkbox :remove_profile_image %>
      <%= f.label :remove_profile_image %>
    <% end %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %> 
```

在上面的代码中，我们使用了一个复选框来选择移除图像的选项。我们不希望复选框在没有图像上传时显示，所以我们使用一个`if`语句来检查是否有一个`profile_image_id`。如果有，将显示轮廓图像和删除它的选项。这使得我们的应用程序看起来很专业。:)

为了让 remove 选项工作，我们需要在强参数中加入 whilelist `:remove_profile_image`。因此，导航到您的`photos_controller`并添加`:remove_profile_image`。

您的控制器应该是这样的:

```
#app/controllers/photos_controller.rb

class PhotosController < ApplicationController
  before_action :set_photo, only: [:show, :edit, :update, :destroy]

  # GET /photos
  # GET /photos.json
  def index
    @photos = Photo.all
  end

  # GET /photos/1
  # GET /photos/1.json
  def show
  end

  # GET /photos/new
  def new
    @photo = Photo.new
  end

  # GET /photos/1/edit
  def edit
  end

  # POST /photos
  # POST /photos.json
  def create
    @photo = Photo.new(photo_params)

    respond_to do |format|
      if @photo.save
        format.html { redirect_to @photo, notice: 'Photo was successfully created.' }
        format.json { render :show, status: :created, location: @photo }
      else
        format.html { render :new }
        format.json { render json: @photo.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /photos/1
  # PATCH/PUT /photos/1.json
  def update
    respond_to do |format|
      if @photo.update(photo_params)
        format.html { redirect_to @photo, notice: 'Photo was successfully updated.' }
        format.json { render :show, status: :ok, location: @photo }
      else
        format.html { render :edit }
        format.json { render json: @photo.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /photos/1
  # DELETE /photos/1.json
  def destroy
    @photo.destroy
    respond_to do |format|
      format.html { redirect_to photos_url, notice: 'Photo was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_photo
      @photo = Photo.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def photo_params
      params.require(:photo).permit(:name, :profile_image, :remove_profile_image)
    end
end 
```

## 文件类型验证

使用 Refile，您可以限制允许用户上传的文件扩展名和内容类型。这对于您的用户来说更像是一个方便的特性，而不是一个安全特性。有了这个功能，你可以警告用户，如果他们试图上传一个无效的文件。

要实现这一点，打开您的模型并添加编辑`attachment`方法行:

```
#app/models/photo.rb

attachment :profile_image, content_type: "image/jpeg" 
```

这将把允许的文件限制为带有 JPEG 扩展名的图像。如果你想使用不同扩展名的组合，它看起来像这样:

```
#app/models/photo.rb

attachment :profile_image, content_type: ["image/jpeg", "image/png", "image/gif"] 
```

这里有一个更好的方法，尤其是当它与图像有关时:

```
#app/models/photo.rb

attachment :profile_image, type: image 
```

那会有用的！

## 结论

现在您知道了如何使用 Refile 在 Rails 应用程序中启用图像上传。你可能想在你的下一个项目中尝试一下。查看 Refile 的文档,看看如何添加 Refile 提供的其他令人兴奋的功能。祝你学习愉快！

## 分享这篇文章