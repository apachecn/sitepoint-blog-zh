# RubyMotion 工作流自定义

> 原文：<https://www.sitepoint.com/rubymotion-workflow-customizations/>

![rmyourway](img/3ea0000c56e95b3090b6959a4f801d0d.png)

RubyMotion 是一套简化 iOS 应用开发的工具，将源代码编译成原生 iOS 机器语言。它与 MacRuby 有着共同的传统，这使得它成为有经验的 Ruby 爱好者开发 iPhone/iPod/iPad 应用程序的熟悉选择。RubyMotion 提供了对 iOS 类和库、RubyGems 的直接访问，并支持混合使用 Ruby 和 Objective-C 风格的调用约定。
iOS 应用的结构遵守 MVC(像 Rails)和 RubyMotion 的命令行工作流程是由 Rakefile 驱动的；**就像铁轨一样**。

你在 RubyMotion 中的标准工作流程可能包括 [Bundler](http://gembundler.com/) 和 [RVM](https://rvm.io/) {在撰写本文时，RubyMotion 至少需要 Ruby 1.9 }

但是在很多情况下，这些与 Ruby on Rails 的相似之处只是表面的。

在本文中，我想重点介绍 RubyMotion 应用程序中使用的 Rakefile。我希望一旦你在开发几个 RubyMotion 应用程序时，能提供一些帮助，让你的工作变得更有条理。

我在这里提出的技术和想法是我自己的——没有特别的保证——并且是作为指导提出的。我们鼓励您采用这里介绍的内容，并对其进行扩展或调整，以满足您的需求。

如果你创造了一些特别有趣或独特的东西，我希望你能与社区分享(在 [Gist](https://gist.github.com/) 或 [Github](https://github.com/) 上的知识库中)并评论这篇文章。

### RubyMotion 项目结构

要实例化一个新的应用程序(项目),命令很简单`motion create applicationName`

这构建了一个 RubyMotion 应用程序的结构，如下所示:

```
applicationName

      Rakefile

     resources
     app

          app_delegate.rb

     .gitignore

     spec

          main_spec.rb
```

如果你熟悉 iOS 开发(也许你曾经用 XCode 在 Objective-C 中工作过)，那么`appdelegate.rb`可能是可以识别的。

*   `resources`目录是所有图像、图标和其他**资源**应该被维护的地方。
*   `.gitignore`已经为你定义了许多合理的默认值。尽管您仍然需要为您的项目建立一个存储库。
*   目录是用于测试的(你在为你的 Ruby 写测试，不是吗？).RubyMotion 用的是 Bacon(一个 RSpec 克隆)。默认情况下会创建一个样本等级库。生成这些文件的这个**构建系统**在 Github 的 [HipByte/RubyMotion](https://github.com/HipByte/RubyMotion) 上是开源的。本文将重点讨论的系统部分是`Rakefile`。

RubyMotion `Rakefile`是您的 CLI 工作流的“心脏和灵魂”。与 Rails 非常相似，您需要的大多数任务只需要一个`rake`命令。您可以通过编辑来创建定制的任务，并通过调用`rake -T`来获得当前任务的列表。

以下是 RubyMotion 1.32 的`rake`命令列表

```
rake archive               # Create an .ipa archive
rake archive:distribution  # Create an .ipa archive for distribution (AppStore)
rake build                 # Build everything
rake build:device          # Build the device version
rake build:simulator       # Build the simulator version
rake clean                 # Clear build objects
rake config                # Show project config
rake ctags                 # Generate ctags
rake default               # Build the project, then run the simulator
rake device                # Deploy on the device
rake simulator             # Run the simulator
rake spec                  # Same as 'spec:simulator'
rake spec:device           # Run the test/spec suite on the device
rake spec:simulator        # Run the test/spec suite on the simulator
rake static                # Create a .a static library
```

#### Rake 保存配置细节

与 Rails 不同，在 Rails 中，除非您正在开发定制的命令，否则您很少需要修改`Rakefile`,在 RubyMotion 中，许多特定于您的应用程序的配置细节都在这个文件中。换句话说，RubyMotion 中的每个应用程序都将拥有自己独特的版本`Rakefile`。

您希望需要的大多数库和 gem(由于静态编译，RubyMotion 不支持‘include’)将在`Rakefile`中引用。您的应用程序中使用的特定图标和**共同设计**凭证必须添加到`Rakefile`中。

此外，您可能正在使用的 API 的任何凭证都是从`Rakefile`中读取的。

{您可以随时使用命令`rake config`(见上文)检查当前配置}

#### 进退两难

我总是很犹豫是否要将`Rakefile`包含在我的 Git 存储库中；尤其是我可能公开提供的项目。但是因为它是整个过程中不可或缺的一部分，所以不能简单地将其排除在外。

如何在提供必要的配置细节的同时隐藏任何“专有”凭证一直是 RubyMotion 社区讨论的焦点。一个显而易见的解决方案(也是非常流行的一个)是为那些敏感的数据定义“环境变量”。

尽管这样做效果很好，但我想要更大的灵活性，更喜欢与任何特定设备耦合不那么紧密的东西(我在多台计算机上工作)。

#### 我的解决方案

在我的 RubyMotion 工作流中，我选择将`Rakefile`定义为每个项目中的一个符号链接。我的`Rakefile`符号链接指向**一个单独的 Rakefile** ，它位于一个父目录中。这是一种非常枯燥的管理`Rakefile`、所有“专有”凭证和任何定制(至于额外的 Rake 任务)的方法。为了提供必要的灵活性，我修改了基础库`Rakefile`，以便从每个应用程序主目录中的`app_properties.rb`加载。

下面是一个通用`Rakefile`的例子。这里假设`Rakefile`只存在于任何 RubyMotion 项目的*上一层*。{此文件已匿名发布}

```
#-*- coding: utf-8 -*-

$:.unshift("/Library/RubyMotion/lib")
require 'motion/project'

# custom rake tasks can go here

desc "Open latest crash log"
task :log do
  app = Motion::Project::App.config
  exec "less '#{Dir[File.join(ENV['HOME'], "/Library/Logs/DiagnosticReports/#{app.name}*")].last}'"
end

require './app_properties'  #this is the call to a file that exists in every project
props = AppProperties.new
props.requires.each { |r| require r } if props.requires.size > 0

Motion::Project::App.setup do |app|
  app.name = props.name
  app.identifier = props.identifier
  app.delegate_class = props.delegate
  app.version = props.version.scan(/d+/).flatten.first
  app.short_version = props.version.scan(/d+/).first #required to be incremented for AppStore (http://iconoclastlabs.com/cms/blog/posts/updating-a-rubymotion-app-store-submission)
  app.icons = props.icons
  app.device_family = props.devices
  app.interface_orientations = props.orientations
  app.provisioning_profile = props.provisioning
  app.codesign_certificate = props.developer_certificate
  if props.testflight?
        require 'motion-testflight'
    app.testflight.sdk = 'vendor/TestFlight'
    app.testflight.api_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
    app.testflight.team_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'
    app.testflight.distribution_lists = ['Developers', 'Testers']
  end
  if props.pixate?
      require 'rubygems'
      require 'motion-pixate'
    app.pixate.user = 'USER ID'
    app.pixate.key  = 'XXXX-XXXX-XXXX-XXXX-XXXX-XXXXX-XXXXX-XXXXX-XXXXX-XXXXX-XXXXX-XXXXX-XXXX-XX'
    app.pixate.framework = 'vendor/PXEngine.framework'
  end
end
```

* * *

和一个位于每个 RubyMotion 项目根目录下的文件样本。

```
class AppProperties
  VERSION = '0.9'
  APP_DELEGATE = 'AppDelegate' #default
  COMPANY_NAME = 'com.websembly.'

  def name
     'App Name Here'
  end

  def version
   VERSION
  end

  def requires  #add libraries to listed in Rakefile as 'require'
    ["sugarcube"]
  end

  def frameworks
    []
  end

  def contributors
    ["Thom Parkin" => "https://github.com/ParkinT/"]
  end

  def developer_certificate
    'iPhone Developer: Thom Parkin (1337THX1138)'
  end

  def provisioning
    './provisioning' #symlink
  end

  def testflight?
    false
  end

  def pixate?
    true
  end

  def delegate
    APP_DELEGATE
  end

  def icons
    icn = ["#{self.name}.png", "#{self.name}-72.png", "#{self.name}@2x.png"]
  end

  def devices
    [:iphone, :ipad]
  end

  def orientations
    [:portrait, :landscape_left, :landscape_right] #:portrait_upside_down
  end

  def identifier
    COMPANY_NAME + APP_DELEGATE
  end

end
```

您可能会注意到，*配置文件*也是一个符号链接。

* * *

因此，在我的每个 RubyMotion 项目中，由 RubyMotion `create`命令生成的`Rakefile`被一个个性化的`app_properties.rb`文件所取代，该文件包含通用指令以及该应用程序特有的内容(如应用程序名称)。

随着我在 RubyMotion 工作流中使用更多的库和额外的助手，这种灵活性和价值也在不断增长。例如，我最近开始在我的 RubyMotion 项目中使用 Pixate。为了**将**这种能力添加到**我的所有**ruby motion 项目中，我简单地更新了主文件`Rakefile`，并且可以在每个适用的`app_properties.rb`文件中包含必要的引用。

正如我之前说过的，这是**实现这种效果的一种方式**。这可能不是*最好的*方式，我邀请你改进它。如果你有，请让我知道。

## 分享这篇文章