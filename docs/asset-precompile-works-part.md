# 资产预编译如何工作，第一部分

> 原文：<https://www.sitepoint.com/asset-precompile-works-part/>

![Abstract tooth wheels](img/aea2f50fe66416bb693783271c7f8183.png)

Web 应用程序正变得越来越快。让用户回头客最重要的特征之一就是速度。如果一个 web 应用程序很慢，需要太多的时间来加载，那么它将失去它的用户。如果一个 web 应用程序很快，那么它的用户就会增加。如今，开发一个缓慢的 web 应用程序并不是一种选择。

有许多因素会影响 web 应用程序的性能，其中一个重要因素是加载图像、CSS 文件、JavaScript 文件等资产。每个 web 应用程序都包含一些图像和 CSS 文件来使它看起来漂亮，还有一些 JavaScript 文件来处理用户交互和行为。如果资产加载得更快，web 应用程序的性能应该会更好。有很多策略可以让资源加载更快，比如缩小、压缩(gzipping)、缓存等等。

有一些框架具有内置的功能来对资产执行上述操作。我们(Ruby 社区)热爱 Rails，从 3.1 版本开始，Rails 内置了对资产执行不同操作的支持。感谢 [@sstephenson](https://twitter.com/sstephenson) 和 [@joshpeek](https://twitter.com/joshpeek) 为开发和维护[链轮](https://github.com/sstephenson/sprockets)付出的巨大努力。链轮是一个 Ruby 宝石，标榜自己是一个“基于机架的资产打包系统”。链轮可用于任何基于机架的 web 框架，如 Sinatra、Padrino 等。链轮从 Rails 3.1 开始内置，被视为 Rails 不可或缺的一部分。

为了了解链轮，有一个很好的[导轨](http://guides.rubyonrails.org/asset_pipeline.html)可用。我建议您在阅读本文之前，先阅读关于链轮的 Rails 指南。在本文中，我们将研究资产管道在 Rails 中是如何工作的，以及资产的预编译是如何工作的。

# rake 资源:预编译

在将代码投入生产之前，我们使用`rake assets:precompile`来预编译我们的资产。该命令预编译资产，并将它们放在 Rails 应用程序的`public/assets`目录下。让我们从查看 Rails 资产管道的内部开始我们的旅程。本文基于`Rails 3.2.15`和`Sprocket 2.2.2`。如果你还没有安装`Rails 3.2.15`，那么先安装它。

我在用 RVM 管理我的宝石。安装完`Rails 3.2.15`之后，是时候开始真正的工作了。正如我告诉你的，我们将阅读 Rails 资产管道的内部内容，所以如果你在你最喜欢的编辑器中打开 Rails gem 目录和链轮目录，那将会很棒。我将提供不同代码段的完整 GitHub 路径，因此您也可以在 GitHub 上查看代码。

让我们首先创建一个测试 Rails 应用程序。我们将使用这个应用程序来查看和调整各种配置选项，并查看我们的预编译资产。要创建一个 Rails 应用程序，我们使用以下命令:

```
rails new asset_pipeline_test
```

你可以用任何你喜欢的名字来代替`asset_pipeline_test`。`cd`进入新创建的 Rails 应用程序，发出下面的命令找到 Rails gem 目录的路径(我假设您已经通过发出`bundle install`命令安装了必要的 gem)。

```
bundle show rails
```

这个命令将返回 Rails gem 目录的路径。运行相同的命令，将`rails`改为`sprockets`以获得链轮 gem 目录的路径。

现在我有一个惊喜给你。通过导航到运行这些命令的路径，继续检查链轮 gem 目录的内容。您将在链轮 gem 目录中看到不同的文件。

现在导航到 Rails gem 目录，您可能会惊讶地发现这个目录是空的:-)。看到一个空的目录，我有点震惊。我们都知道每个 gem 都有一个目录，所有的文件都在那里。如果这是真的，那么 Rails gem 文件在哪里？？？Rails 3 是一个重大的重构，与 Rails 2.x 相比，现在事情更加有组织性。Rails gem 文件在`railties-3.2.15`目录中，所以我们将使用`railties-3.2.15`目录来检查 Rails 的不同部分，而不是使用`rails-3.2.15`。

也在您最喜欢的编辑器中打开`actionpack-3.2.15`，因为它有一个目录`sprockets`，其中包含从 Rails 运行链轮所需的代码。

## 链轮::环境

根据链轮，我们需要一个`Sprockets::Environment`类的实例来访问和服务来自您的应用程序的资产。让我们看看这在代码中的什么地方被定义。

[这里的](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/railtie.rb#L21-L33)是负责为我们的 Rails 应用程序创建一个`Sprockets::Environment`实例的代码。您可能想知道为什么这段代码在`railtie.rb`中。这个问题的答案很简单。

正如我前面提到的，Rails 3 是一次重大的重构(感谢 Rails 核心团队的出色工作)。是 Rails 框架的核心，提供了几个钩子来扩展 Rails 和/或修改初始化过程。Railtie 为我们提供了几个挂钩来实现我们想要的功能。我们希望在执行任何其他代码之前，我们的应用程序可以使用一个`Sprockets::Environment`实例，所以我们从`Rails::Railtie`扩展了我们的`Sprockets::Railtie`类。在[行的代码块:23](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/railtie.rb#L23) 中，我们将`Sprocket::Environment`的实例赋给`app.assets`。`app`是我们 Rails 应用程序的一个实例，你对它的形式`Rails.application`很熟悉。

我们有两个版本的资产。`app.assets`和`config.assets`。`config`是在[行:18](https://github.com/rails/rails/blob/v3.2.15/actionpack/lib/sprockets/railtie.rb#L18) 中分配给`config`的`app.config`的实例。`Sprockets::Railtie`上下文或`Rails.application.config.assets`上下文中的`config.assets`是一个配置对象，用于保存`Rails.application.assets`的各种配置选项。

回顾一下，`Rails.application.assets`是`Sprockets::Environment`的实例，`Rails.application.config.assets`是配置对象。

`Rails.application.config.assets`在这里被定义为。如您所见，它包含一些配置选项，我们使用这些选项从 Rails 应用程序的不同文件中设置不同的环境。

现在，我们知道了`Sprockets::Environment`的实例是在哪里创建的，以及`Rails.application.assets`和`Rails.application.config.assets`之间的区别。让我们深入探讨链轮的内部结构。

### 资产类型

根据链轮，有以下三种资产

*   捆绑资产
*   已处理资产
*   静态资产

#### 静态资产

我们将在第 2 部分更详细地了解`Bundled`和`Processed Assets`。这里，我们讨论的是`Static Assets`。静态资产是不需要任何额外处理的资产。它们只是用一个新名字创建的。例如，图像是静态资产。让我们看看静态资产是如何预编译和保存的。

导航到这里，您将看到方法`build_asset`。`build_asset`用于构建资产(duh)。`logical_path`是资产的名称，即`rails.png`,`pathname`类似于`/home/ubuntu/Desktop/work/asset_pipeline_article/app/asseimg/rails.png`(根据应用的`root`，您的路径会有所不同)。

在第 381 行，你会看到下面一行

```
StaticAsset.new(index, logical_path, pathname)
```

`StaticAsset`在`lib/sprockets`目录下的链轮 gem 中定义。`StaticAsset`是`Asset`的子类。`Asset`是所有类型资产的基类。

导航到这里查看`Asset`类的`initialize`方法。在`build_asset`方法中，我们已经看到了`StaticAsset`的一个实例正在被创建。当`StaticAsset`的一个实例被创建时，调用`Asset`的`initialize`方法。对于`StaticAsset`，创建一个摘要，并以包含`file_name-digest.file_ext`的新名称保存一个新文件。摘要是根据文件的内容创建的。

在这里，文件被保存。这个方法负责将资产保存到磁盘，并且非常简单明了。

从 Rails 应用程序的根目录，从命令行运行`rake assets:precompile`。查看`public/assets`目录，您将看到您的 Rails 应用程序的资产。我们此时感兴趣的是`rails.png`。

请注意，`rails.png`有两个版本:一个名为`rails.png` name，另一个名为`rails-a3386665c05a2d82f711a4aaa72d247c.png`。您可能想知道两个文件是如何创建的，因为正如您在`write_to`方法中看到的，只传递了一个`filename`参数。`Sprockets`如何创建两个文件？这个问题将在本文的第 2 部分回答。

您可能还想知道这个`digest`是在哪里以及如何创建的。在`Asset`类的构造函数中，你会看到下面一行:

```
@digest = environment.file_digest(pathname).hexdigest
```

`environment`是`lib/sprockets`目录下链轮 gem 的`index.rb`中定义的`Index`的实例。它是`lib/sprockets`目录下`base.rb`中定义的`Base`的子类。`file_digest`在`base.rb`中定义。导航到`base.rb`中的`file_digest`方法，在第 242 行调用`digest`方法。

在`digest`方法中，您可以看到，如果`@digest`存在，则使用它并返回重复的副本，或者创建新的摘要并返回其重复的版本。`digest_class`返回，默认为`Digest::MD5`。不过，我们也可以指定一些其他的职业，比如`Digest::SHA1`。`Digest::MD5`在这里被赋给`digest_class`属性。使用以下代码行在`digest`方法中创建新的摘要:

```
digest_class.new.update(VERSION).update(version.to_s)
```

这里的`VERSION`是`Sprockets::VERSION`，`version.to_s`是`production-1.0`。

基于这些信息，让我们使用我们的控制台来看看我们是否可以生成与链轮为`rails.png`生成的相同的摘要。在 Rails 应用程序的根目录下键入`rails console`。

为了生成`rails.png`的摘要，我们应该有`rails.png`的路径名。如前所述，路径名是资产的绝对路径。在我的例子中，`rails.png`的路径名是`/home/ubuntu/Desktop/work/asset_pipeline_article/app/asseimg/rails.png`。

在控制台中键入以下命令:

```
digest = Digest::MD5.new
digest.update(Sprockets::VERSION).update('production-1.0')
digest.file("/home/ubuntu/Desktop/work/asset_pipeline_article/app/asseimg/rails.png").hexdigest
```

方法根据文件的内容返回文件的摘要。在我的例子中，这将给我们`rails.png`的`a3386665c05a2d82f711a4aaa72d247c`，确认链轮生成的相同摘要。只要`Sprockets::VERSION`和`version.to_s`相同，所有 Rails 应用程序的默认`rails.png`摘要都相同。

在本文中，我们发现了 Rails 资产管道在基本层面上是如何工作的，静态资产是如何预编译的，以及如何创建摘要。下一部分将深入介绍 Rails 资产管道，包括路径如何分配、路径如何解析、捆绑资产和处理资产如何工作，以及许多其他概念。

我希望你今天学到了一些关于资产管道的知识。小心第二部！

## 分享这篇文章