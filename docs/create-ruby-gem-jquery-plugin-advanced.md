# 为 jQuery 插件创建 Ruby Gem:高级

> 原文：<https://www.sitepoint.com/create-ruby-gem-jquery-plugin-advanced/>

![Screenshot 2014-10-12 11.39.13](img/2d46608f598f75e3dfbd6a602ccc78c9.png)

在之前的文章中，我们从头开始创建了一个 gem，它引入了一个 jQuery 插件并将其添加到 Rails 应用程序中。在本帖中，让我们看看一些先进的概念，让我们的 gem 变得更好。

我们将使用与前一篇文章中相同的 gem 和 Rails 应用程序。

## 自定义助手

到目前为止，我们已经通过简单地添加`image_zoomer` CSS 类成功地将缩放效果应用于图像。但是，你不认为我们的宝石值得一个定制助手吗？我的意思是，使用我们自己的助手标签，让用户不必添加`image_zoomer` CSS 类，不是更好吗？

让我们创建一个名为`zoom_image_tag`的自定义助手。默认情况下，使用`zoom_image_tag`添加的图像将应用缩放效果。在 gem 的 **lib/image_zoomer** 目录下创建一个名为 **zoom_image_helper.rb** 的文件，粘贴如下代码:

```
module ImageZoomer
  def zoom_image_tag(*args, &amp;block)
    options = insert_zoom_class(args.extract_options!)
    args << options
    image_tag(*args, &amp;block)
  end

  def insert_zoom_class(options)
    class_name = "image_zoomer"
    if options.key?(:class)
      options[:class] += " #{class_name}"
    elsif options.key?('class')
      options['class'] += " #{class_name}"
    else
      options[:class] = class_name
    end
    options
  end
end
```

我们在`ImageZoomer`模块上定义了两个方法。就像`image_tag`助手一样，`zoom_image_tag`方法接收多个参数和一个块。

`options = insert_zoom_class(args.extract_options!)`正在调用`ActiveSupport`提供的`extract_options!`方法。它用于从一组参数中提取选项。如果数组中的最后一个元素是一个散列，它将删除并返回它，否则它将返回一个空散列。返回的散列被传递给`insert_zoom_class`。

`insert_zoom_class`是一个简单的方法，用于将`class`插入到提供的散列中。密钥可以是符号、字符串，或者根本不提供。这里，`image_zoomer` `class`属性作为散列的一部分返回。

`args appends the` `options`哈希到`args`。由于`extract_options!`从`args`中删除了选项，我们必须将它们追加回来。

`image_tag(*args, &block)`是标准的 Rails `image_tag`助手，带有我们修改过的参数。这里的外卖是`zoom_image_tag`是`image_tag`的简单包装。我们 gem 的用户现在有了一个方便的方法来使他们的生活更轻松。

导航到测试 Rails 应用程序，打开 Rails 应用程序下的**app/views/site/index . html . erb**，将`image_tag`替换为:

```
<%= zoom_image_tag("test.jpg"); %>
```

启动 web 服务器，在浏览器中打开[http://localhost:3000/site/index](http://localhost:3000/site/index)。您将看到一个类似于`undefined method 'zoom_image_tag'`的错误。发生了什么事？？我们没有告诉 Rails】是一个 helper 方法。我们现在就开始吧。

我们知道`image_tag`助手是在`ActionView::Helpers::AssetTagHelper`模块中定义的。我们可以简单地重新打开这个模块并添加`ImageZoomer`模块。打开 **lib/image_zoomer.rb** (在 gem 中)，添加:

```
require "image_zoomer/zoom_image_helper"

module ActionView
  module Helpers
    module AssetTagHelper
      include ImageZoomer
    end
  end
end
```

现在，`zoom_image_tag`将成为`ActionView::Helpers::AssetTagHelper`方法的一部分，加入其他帮手的联盟，如`image_tag`、`javascript_include_tag`等。

重新启动 web 服务器并刷新页面。使用我们的新助手应用图像缩放效果。酷毙了。

### 一次重要的重构

在现有的模块中包含`ImageZoomer`是一种猴子式的修补，被认为是有害的。幸运的是，Rails 提供了一种添加定制助手的优雅方式，称为 Railties，它不使用猴子补丁。Railties 提供了几个钩子来参与初始化过程。点击了解更多关于`Railtie`T2 的信息。

首先，从 **lib/image_zoomer.rb** 中删除新插入的代码，这样它只包含三个`require`语句。在 **lib/image_zoomer/** 目录下创建一个文件 **railtie.rb** ，使用:

```
class Railtie < Rails::Railtie
  initializer "image_zoomer.initialize_zoom_image_helper" do |app|
    ActiveSupport.on_load(:action_view) do
      include ImageZoomer
    end
  end
end
```

这是我们的`Railtie`类，它是根据 Rails 文档从`Rails::Railtie`继承而来的。对`initializer`方法的调用需要一个名字和一个块。这个块执行我们的魔法，调用`ActiveSupport.on_load(:action_view)`并传递另一个块来包含`ImageZoomer`模块。我们的`ImageZoomer`模块只有在`ActionView`满载时才会包含在内。这是自 Rails 3 以来添加助手的标准方式。点击阅读更多关于这个[的内容。](http://simonecarletti.com/blog/2011/04/understanding-ruby-and-rails-lazy-load-hooks/)

用以下代码要求 **lib/image_zoomer.rb** 中的 **railtie.rb** :

```
require "image_zoomer/railtie"
```

保存您的更改，重新启动 web 服务器，注意缩放效果仍然有效。现在，`zoom_image_tag`被 Rails 内置的钩子插入，我们已经消除了猴子补丁。

我们的最终版本 **lib/image_zoomer.rb** 如下所示:

```
require "image_zoomer/version"
require "image_zoomer/engine"
require "image_zoomer/zoom_image_helper"
require "image_zoomer/railtie"
```

## 覆盖默认配置

我们的 gem 运行良好，我们可以称之为“完成”，但是它真的是可扩展的吗？我说的“可扩展”是指，开发者可以改变 jQuery 插件的默认配置吗？默认情况下，变焦镜头的`height`和`width`为`90px`，`zoom-level`为`1.5`。如果开发者不能更新这些设置，我们的 gem 是不可扩展的。换句话说，我们还没有完成。

让我们添加一些可扩展性，以便开发人员可以轻松地覆盖默认设置。

### 配置类

在 gem 中创建一个类，将我们的配置保存在名为**lib/image _ zoomer/configuration . Rb**的文件中，并添加:

```
module ImageZoomer
  class Configuration
    attr_accessor :width, :height, :zoom_level

    def initialize
      @width = 90
      @height = 90
      @zoom_level = 1.5
    end
  end
end
```

我们只是在我们的`ImageZoomer`模块中创建了一个类。`Configuration`类有三个属性:`width`、`height`和`zoom_level`。这个类的实例将获得与 jQuery 插件定义的相匹配的默认值。

需要 **lib/image_zoomer.rb** 中的 **configuration.rb** :

```
require "image_zoomer/configuration"
```

创建该类实例的最佳位置是在**lib/image _ zoomer/rail tie . Rb**中。将以下代码添加到我们的`Railtie`类中:

```
initializer "image_zoomer.configuration" do |app|
  app.config.image_zoomer = ImageZoomer::Configuration.new
end
```

我们再次调用了`initializer`方法，并给它传递了一个名称和一个块。这里的`app`代表`Rails.application`，`app.config`是`Rails::Application::Configuration`的一个实例，用于保存应用程序的所有配置。我们已经创建了一个`ImageZoomer::Configuration`的实例，并将其分配给`app.config.image_zoomer`。

现在，访问`Rails.application.config.image_zoomer`将呈现一个`ImageZoomer::Configuration`的实例。重启你的网络服务器，刷新你的浏览器，确保我们没有破坏任何东西。

要立即使用一个`ImageZoomer::Configuration`实例，启动控制台(`rails console`)并输入`Rails.application.config.image_zoomer`。如果一切设置正确，您将看到我们的`ImageZoomer::Configuration`在运行。

### 为插件提供设置

gem 现在有一个配置类，我们只需要使用它。在 gem 下的 **app/assets/javascripts** 中新建一个名为**image _ zoomer _ options . js . erb**的文件，使用:

```
var __ImageZoomer = {
  options: {
    width: "<%= Rails.application.config.image_zoomer.width %>",
    height: "<%= Rails.application.config.image_zoomer.height %>",
    zoom_level: "<%= Rails.application.config.image_zoomer.zoom_level %>"
  }
}
```

上面的代码非常简单。声明一个 JavaScript 变量(`__ImageZoomer`)，并给它分配一个具有属性`options`的对象。`options`对象有三个属性，对应于`Rails.application.config.image_zoomer`对象的一个属性。这是一个`erb`模板，因为我们想要执行一些 Ruby 代码，而链轮自动处理资产文件中动态代码的执行。因此，`image_zoomer_options.js.erb`将在 HTML 响应中指向结果**/assets/image _ zoomer _ options . js**，看起来像这样:

```
var __ImageZoomer = {
  options: {
    width: "90",
    height: "90",
    zoom_level: "1.5"
  }
}
```

如你所见，我们执行的动态代码和`width`、`height`和`zoom_level`都有默认值，这些值是我们在`ImageZoomer::Configuration`的构造函数中定义的。

有了保存配置值的新 JavaScript 对象，就可以修改 jQuery 插件初始化代码来使用它了。在您的 gem 下打开**app/assets/JavaScript s/image _ zoomer _ initializer . js**，将现有代码替换为:

```
$(function() {
  if (typeof __ImageZoomer == "undefined") {
    __ImageZoomer = {options: {}};
  }
  $(".image_zoomer").each(function() {
    $(this).image_zoomer(__ImageZoomer.options);
  });
});
```

首先，检查`__ImageZoomer`变量是否存在，并在必要时创建它。根据 jQuery 插件[的用法](https://github.com/ilatif/image_zoomer#how-to-use)，我们可以将一个对象传递给`image_zoomer`方法，在该对象中定义的属性将被应用。传递`__ImageZoomer.options`应用我们的设置来改变图像缩放的行为。

重启你的网络服务器，刷新浏览器，你会看到同样的图像缩放体验。`__ImageZoomer`指向默认值，因此缩放体验暂时没有变化。但是，我们已经迈出了重要的一步，让用户能够覆盖默认设置。

### 修改 Rails 中的默认值

Rails 使用初始化器来修改 gem 和核心 Rails 设置的配置。我们也将这样做。首先，`ImageZoomer::Configuration`中应该有一种优雅的方式来更新属性。打开**lib/image _ zoomer/configuration . Rb**，将下面的方法添加到类中:

```
# Override default options with the provided ones in block
def set_options(&amp;block)
  block.yield(self)
end
```

我们已经创建了一个方法`set_options`，它接受一个产生`self`(配置的当前实例)的块。用适当的块调用这个方法将从我们的`Rails.application.config.image_zoomer`初始化器中更新`Image_Zoomer::Configuration`的默认值。

在 Rails 应用程序中，使用以下内容创建**config/initializer/image _ zoomer . Rb**:

```
Rails.application.config.image_zoomer.set_options do |options|
  options.width = 50
  options.height = 50
end
```

这段代码执行的时候，`width`和`height`都改成了`50`，`image_zoomer`还是`1.5`，因为我们没有改。开发人员可以根据自己的需要轻松修改这个块。因为我们已经设置了 jQuery 代码，它将从我们的`Rails.application.config.image_zoomer`中选取最新的值，所以我们可以开始了。

重启 web 服务器，刷新浏览器，观察与之前不同的`width`和`height`。

### 修正警告

等等，宽度和高度没变。怎么了？这是由于一个警告。

链轮缓存资产，以便它不必一次又一次地处理它们。当第一次服务任何资产时，它被缓存，并且该缓存将在后续请求中被服务。即使我们重新启动 web 服务器，该缓存也不会更新。更新任何资产的缓存的一种方法是更改其源代码，但这可能导致无用的更改和充满空格/行的愚蠢的 git 提交。让我们看看更好的方法。打开**lib/image _ zoomer/rail tie . Rb**，把`image_zoomer.configuration`块改成这样:

```
initializer "image_zoomer.configuration" do |app|
  app.config.image_zoomer = ImageZoomer::Configuration.new
  # Clear the cache
  FileUtils.rm_rf("#{Rails.application.root}/tmp/cache/assets") if Rails.env.development?
end
```

链轮将缓存存储在 Rails 应用程序中的指定路径。当执行我们的**lib/image _ zoomer/rail tie . Rb**中的代码时，我们将删除该目录(这是服务器启动和加载 gem 的时间)。我们使用 Ruby 的内置方法来删除开发环境中的文件夹。

重启网络服务器，你最终会看到不同尺寸的变焦镜头。继续在**config/initializer/image _ zoomer . Rb**中随心所欲地更改设置。

## 发布创业板

我们的宝石准备好了。发布一个 gem 非常简单，这要感谢 Rubygems 核心团队。Bundler 已经提供了一些有助于打包和分发宝石的任务。首先，你必须有一个 Rubygem 账户。前往 http://rubygems.org 报名。

创建账户后，更新 **image_zoomer.gemspec** 文件中的相关信息。你可能也想把这个宝石推给 Github。

导航到您的 gem 的根目录并发出以下命令:

```
rake release
```

这个命令将把所有需要的文件编译成一个`.gem`包，将最新的代码作为标签提交给 GitHub，并将 gem 上传到 Rubygems。发布时会要求您提供 Rubygems 凭证，提供这些凭证就可以了。一旦 gem 成功发布，您将会在 Rubygems 的仪表板上看到它。

如果由于某种原因`rake release`失败了，你也可以用`rake build`建造宝石。如果可以，用`gem push path/to/image_name-version.gem`手动将生成的`image_name-version.gem`文件推送到 Rubygems。

## 还剩一件事

现在只剩下一件事了，那就是与你的同事、朋友和其他 Rubyists 分享你的宝石。别忘了给彼得·库珀发一封电子邮件，让它发表在 RubyWeekly 上，这是阅读量最大的 Ruby 时事通讯之一。

## 结论

构建一些东西并为开源做贡献是一种很棒的体验，每个开发者都应该这样做。希望你在这两篇文章中学到了很多。继续为您最喜欢的 jQuery 插件制作一个宝石吧。

## 分享这篇文章