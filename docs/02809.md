# 闭嘴。用 Rails 和 Rubyzip 压缩它

> 原文：<https://www.sitepoint.com/accept-and-send-zip-archives-with-rails-and-rubyzip/>

![](img/4b2ed6db7f8bfda98b6967e02524b6fc.png)

在我们的日常活动中，我们经常与档案打交道。当你想给你的朋友发送一堆文件时，你可能会先把它们存档。当你从网上下载一本书时，它可能会和附带资料一起存档。那么，我们如何在 Ruby 中与存档交互呢？

今天我们将讨论一个叫做 [rubyzip](https://github.com/rubyzip/rubyzip) 的流行工具，它用于管理 zip 存档。在它的帮助下，你可以很容易地阅读和创建档案或生成他们的飞行。在本文中，我将向您展示如何从用户发送的 zip 文件中创建数据库记录，以及如何发送包含表中所有记录的归档文件。

源代码可从 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Archives_with_Rubyzip) 获得。

在开始之前，我想提醒你，各种压缩格式有不同的压缩比。因此，即使您归档文件，其大小也可能保持不变:

*   文本文件压缩得非常好。根据它们的含量，比例约为 3:1。
*   一些图像可以从压缩中受益，但是当使用像*这样的格式时。已经有原生压缩的 jpg* ，不会有太大变化。
*   二进制文件可以压缩到原始大小的两倍。
*   音频和视频通常不适合压缩。

## 入门指南

创建新的 Rails 应用程序:

```
$ rails new Zipper -T 
```

我在这个演示中使用的是 Rails 5 beta 3 和 Ruby 2.2.3，但是 rubyzip 支持 Ruby 1.9.2 或更高版本。

在我们今天的场景中，演示应用程序跟踪动物。每种动物都有以下属性:

*   `name` ( `string`)
*   `age`(`integer`)——当然可以用小数代替
*   `species` ( `string`)

我们想列出所有的动物，给它们添加能力，并以某种格式下载关于它们的数据。

创建并应用相应的迁移:

```
$ rails g model Animal name:string age:integer species:string
$ rake db:migrate 
```

现在让我们为我们的应用程序准备默认页面:

*animals_controller.rb*

```
class AnimalsController < ApplicationController
  def index
    @animals = Animal.order('created_at DESC')
  end
end 
```

*views/animals/index . html . erb*

```
<h1>My animals</h1>

<ul>
  <% @animals.each do |animal| %>
    <li>
      <strong>Name:</strong> <%= animal.name %><br>
      <strong>Age:</strong> <%= animal.age %><br>
      <strong>Species:</strong> <%= animal.species %>
    </li>
  <% end %>
</ul> 
```

*config/routes.rb*

```
[...]
resources :animals, only: [:index, :new, :create]
root to: 'animals#index'
[...] 
```

不错！继续下一节，让我们先来关注创造。

## 从档案中创造动物

介绍`new`动作:

*animals_controller.rb*

```
[...]
def new
end
[...] 
```

*views/animals/index.html.erb

```
<h1>My animals</h1>

<%= link_to 'Add!', new_animal_path %>
[...] 
```

当然，我们可以制作一个基本的 Rails 表单来一个接一个地添加动物，但是让我们允许用户上传带有 JSON 文件的档案。每个文件将包含特定动物的属性。文件结构如下所示:

*   animals.zip
    *   动物-1.json
    *   animal-2.json

每个 JSON 文件将具有以下结构:

```
{
  name: 'My name',
  age: 5,
  species: 'Dog'
} 
```

当然，你可以使用另一种格式，比如 XML。

我们的工作是接收存档，打开它，读取每个文件，并根据输入创建记录。从表格开始:

*views/animals/new . html . erb*

```
<h1>Add animals</h1>

<p>
  Upload a zip archive with JSON files in the following format:<br>
  <code>{name: 'name', age: 1, species: 'species'}</code>
</p>

<%= form_tag animals_path, method: :post, multipart: true do %>
  <%= label_tag 'archive', 'Select archive' %>
  <%= file_field_tag 'archive' %>

  <%= submit_tag 'Add!' %>
<% end %> 
```

这是一个允许用户选择文件的基本表单(不要忘记`multipart: true`选项)。

现在管制员的行动:

*animals_controller.rb*

```
def create
  if params[:archive].present?
    # params[:archive].tempfile ...
  end
  redirect_to root_path
end 
```

我们唯一感兴趣的参数是`:archive`。只要它包含一个文件，它就会响应返回上传文件路径的`tempfile`方法。

[为了读取归档文件](https://github.com/rubyzip/rubyzip#reading-a-zip-file)，我们将使用接受一个块的`Zip::File.open(file)`方法。在这个模块中，你可以获取每个存档文件，或者使用`extract`将其提取到某个地方，或者在`get_input_stream.read`的帮助下将其读入内存。我们真的不需要在任何地方提取我们的存档，所以让我们将内容存储在内存中。

*animals_controller.rb*

```
require 'zip'

[...]

def create
  if params[:archive].present?
    Zip::File.open(params[:archive].tempfile) do |zip_file|
      zip_file.each do |entry|
        Animal.create!(JSON.load(entry.get_input_stream.read))
      end
    end
  end
  redirect_to root_path
end
[...] 
```

很简单，不是吗？`entry.get_input_stream.read`读取文件的内容，`JSON.load`解析它。我们只对*感兴趣。json* 文件，所以让我们使用`glob`方法来限制范围:

*animals_controller.rb*

```
[...]
def create
  if params[:archive].present?
    Zip::File.open(params[:archive].tempfile) do |zip_file|
      zip_file.glob('*.json').each do |entry|
        Animal.create!(JSON.load(entry.get_input_stream.read))
      end
    end
  end
  redirect_to root_path
end
[...] 
```

您还可以将部分代码提取到模型中，并引入基本的错误处理:

*animals_controller.rb*

```
[...]
  def create
    if params[:archive].present?
      Zip::File.open(params[:archive].tempfile) do |zip_file|
        zip_file.glob('*.json').each { |entry| Animal.from_json(entry) }
      end
    end
    redirect_to root_path
  end
  [...]
```

*animal.rb*

```
[...]
class << self
  def from_json(entry)
    begin
      Animal.create!(JSON.load(entry.get_input_stream.read))
    rescue => e
      warn e.message
    end
  end
end
[...] 
```

我还想将用户可以分配的属性列入白名单，防止他覆盖`id`或`created_at`字段:

*animal.rb*

```
[...]
WHITELIST = ['age', 'name', 'species']

class << self
  def from_json(entry)
    begin
      Animal.create!(JSON.load(entry.get_input_stream.read).select {|k,v| WHITELIST.include?(k)})
    rescue => e
      warn e.message
    end
  end
end
[...] 
```

你可以使用黑名单的方法，用除了之外的[代替`select`，但是白名单更安全。](http://api.rubyonrails.org/classes/Hash.html#method-i-except)

太好了！现在继续，创建一个 zip 存档并尝试上传它！

## 生成和下载归档文件

让我们执行相反的操作，允许用户下载包含代表动物的 JSON 文件的档案。

向根页面添加新链接:

*views/animals/index . html . erb*

```
[...]
<%= link_to 'Download archive', animals_path(format: :zip) %> 
```

我们将使用相同的`index`动作，并为其配备`respond_to`方法:

*animals_controller.rb*

```
[...]
def index
  @animals = Animal.order('created_at DESC')

  respond_to do |format|
    format.html
    format.zip do
    end
  end
end
[...] 
```

要将存档发送给用户，您可以在磁盘上的某个位置创建它，也可以动态生成它。[在磁盘上创建归档文件](https://github.com/rubyzip/rubyzip#basic-zip-archive-creation)包括以下步骤:

*   创建一个必须放在归档中的文件阵列:

```
files << File.open("path/name.ext", 'wb') { |file| file << 'content' }
```

*   创建归档:

```
Zip::File.open('path/archive.zip', Zip::File::CREATE) do |z|
```

*   将您的文件添加到归档:

```
 Zip::File.open('path/archive.zip', Zip::File::CREATE) do |z|
  files.each do |f|
    z.add('file_name', f.path)
  end
end 
```

`add`方法接受两个参数:应该出现在存档中的文件名和原始文件的路径和名称。

*   Send the archive:

    ```
     send_file 'path/archive.zip', type: 'application/zip',
          disposition: 'attachment',
          filename: "my_archive.zip" 
    ```

    然而，这意味着所有这些文件和存档本身将在磁盘上保持不变。当然，你可以手动删除它们，甚至试着创建一个临时的 zip 文件，如这里的[所描述的](http://thinkingeek.com/2013/11/15/create-temporary-zip-file-send-response-rails/)，但是这涉及到太多不必要的复杂性。

我想做的是动态生成我们的归档文件，并使用 [send_data](http://api.rubyonrails.org/classes/ActionController/DataStreaming.html#method-i-send_data) 方法将响应显示为附件。这有点棘手，但没有什么是我们不能管理的。

为了完成这个任务，我们需要一个名为`Zip::OutputStream.write_buffer`的方法，它接受一个块:

*animals_controller.rb*

```
[...]
def index
  @animals = Animal.order('created_at DESC')

  respond_to do |format|
    format.html
    format.zip do
      compressed_filestream = Zip::OutputStream.write_buffer do |zos|
      end
    end
  end
end
[...] 
```

要向档案添加新文件，在提供文件名时使用`zos.put_next_entry`。您甚至可以通过说`zos.put_next_entry('nested_dir/my_file.txt')`来指定一个目录来嵌套您的文件。要向文件中写入内容，请使用`print`:

*animals_controller.rb*

```
compressed_filestream = Zip::OutputStream.write_buffer do |zos|
  @animals.each do |animal|
    zos.put_next_entry "#{animal.name}-#{animal.id}.json"
    zos.print animal.to_json(only: [:name, :age, :species])
  end
end 
```

我们不希望像`id`或`created_at`这样的字段出现在文件中，所以通过说`:only`，我们将它们限制为`name`、`age`和`species`。

现在倒带:

```
compressed_filestream.rewind 
```

并发送它:

```
send_data compressed_filestream.read, filename: "animals.zip" 
```

下面是生成的代码:

*animals_controller.rb*

```
[...]
def index
  @animals = Animal.order('created_at DESC')

  respond_to do |format|
    format.html
    format.zip do
      compressed_filestream = Zip::OutputStream.write_buffer do |zos|
        @animals.each do |animal|
          zos.put_next_entry "#{animal.name}-#{animal.id}.json"
          zos.print animal.to_json(only: [:name, :age, :species])
        end
      end
      compressed_filestream.rewind
      send_data compressed_filestream.read, filename: "animals.zip"
    end
  end
end
[...] 
```

继续并尝试“下载存档”链接！

你甚至可以用密码来保护档案。rubyzip 的这个特性是试验性的，将来可能会改变，但目前看来是可行的:

*animals_controller.rb*

```
[...]
compressed_filestream = Zip::OutputStream.write_buffer(::StringIO.new(''), Zip::TraditionalEncrypter.new('password')) do |zos|
[...] 
```

## 定制 Rubyzip

Rubyzip 确实提供了一系列的[配置选项](https://github.com/rubyzip/rubyzip#configuration)，这些选项可以在块中提供:

```
Zip.setup do |c|
end 
```

或者一个接一个:

```
Zip.option = value 
```

以下是可用的选项:

*   `on_exists_proc`–提取过程中是否应该覆盖现有文件？默认为`false`。
*   `continue_on_exists_proc`–创建归档文件时是否应该覆盖现有文件？默认为`false`。
*   `unicode_names`–如果您想在 Windows Vista 和更早版本上存储非 unicode 文件名，请设置此项。默认为`false`。
*   `warn_invalid_date`–如果归档文件的日期格式不正确，是否应该显示警告？默认为`true`。
*   `default_compression`–要使用的默认压缩级别。初始设置为`Zlib::DEFAULT_COMPRESSION`，其他可能值为`Zlib::BEST_COMPRESSION`和`Zlib::NO_COMPRESSION`。
*   `write_zip64_support`–是否应该禁用 Zip64 支持写入？默认为`false`。

## 结论

在本文中，我们看了一下 rubyzip 库。我们编写了一个应用程序，它读取用户的档案，基于它们创建记录，并动态生成档案作为响应。希望所提供的代码片段能在您的项目中派上用场。

一如既往，谢谢你和我在一起，再见！

## 分享这篇文章