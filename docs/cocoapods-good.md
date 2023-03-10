# 椰子恐龙:它有什么好处？

> 原文：<https://www.sitepoint.com/cocoapods-good/>

不是绝对没有，这是肯定的。

在我们了解为什么要使用 CocoaPods 以及如何使用它之前，我们应该先了解一下它是什么。

CocoaPods 是一个用于 iOS 和 OS X 开发的依赖管理工具，它在 Cocoa 社区获得了很多关注。使用 CocoaPods 时，您在一个文件中定义项目的依赖关系，在下载所需的源代码并将其链接到 Xcode 工作区之前，CocoaPods 使用该文件来确定和解决库之间的依赖关系。

## 为什么要用 CocoaPods？

我从一个假设的场景开始。

你开始做一个项目，一切都很顺利(可能是个人项目，也可能是团队项目)。随着项目的进展，您发现有必要使用第三方库来帮助项目更快地进行(整个‘不要重新发明轮子’的事情)。您可以通过搜索适当的库并将源代码复制到项目中来实现这一点。

您启动 Github 上的项目存储库(或任何托管它们的地方)来跟踪更新。更新库时，您可以在项目中手动更新它。有时，库可能会破坏您的构建，迫使您恢复到兼容的版本。您必须搜索它并将其复制到您的项目中。添加更多的库，你会开始注意到维护项目依赖关系的时间消耗。

您也必然会遇到这样的情况，您想要使用的库依赖于另一个库，您必须同时获得这两个库。如果您使用共享一个依赖项的多个库，但是所有库都指定了一个不同版本的依赖项，那么您必须找出满足所有依赖项的最佳库版本。

随着项目规模的扩大，您将花费大量时间来管理它的依赖关系，这些时间可以更好地用于编写代码。

CocoaPods 使得管理代码中的依赖更加容易。添加和删除依赖项只是在文件中定义它们并运行命令来安装它们。CocoaPods 读取文件，确定列出的库的依赖项，如果有任何共享的依赖项，它会尝试确定满足所有依赖项的版本。它使用[语义版本化](http://semver.org/)来尝试解析依赖版本。

例如，如果两个不同的 pods 依赖于不同版本的库，例如 1.1.1 和 1.1.2，那么 CocoaPods 可能会选择较新的版本 1.1.2，因为补丁版本通常是向后兼容的。你可以在这里阅读更多关于[的内容。自动解决](http://www.objc.io/issue-6/cocoapods-under-the-hood.html#versioning_and_conflicts)[并不总是发生](http://www.objc.io/issue-6/cocoapods-under-the-hood.html#versioning_and_conflicts)。通常 CocoaPods 会通知您冲突，您必须指定冲突 pod 的版本。但是 CocoaPods 团队[说自动冲突解决是他们的待办事项。](http://guides.cocoapods.org/using/faq.html#cocoapods-doesnt-do-dependency-resolution)

获取 pod 的特定版本是通过指定版本号来完成的，CocoaPods 会为您下载该特定版本，这样您就不用在提交中搜索了。

有了 CocoaPods，用`pod update`命令就可以很容易地更新您的依赖关系。

CocoaPods 还提供了一个可以找到第三方库的中心位置。这提高了您可以在项目中使用的开源库的可发现性。你可以在 cocoapods.org 使用搜索引擎搜索豆荚。

CocoaPods 的另一个优点是，它会生成一个确认文件，其中包含您正在使用的所有 pod 的许可信息。如果没有这个，您将需要复制您正在使用的所有开源库的许可文档，并将它们放在您项目的某个地方。

## 使用椰子

### 安装 CocoaPods

CocoaPods 是作为 Ruby gem 发布的，因此需要在您的系统上安装 Ruby 和 Ruby gems(Ruby 包管理器)。最新版本的 Mac OS X 附带安装了 Ruby，所以您可能不需要安装它。你也可以使用像 RVM 这样的 Ruby 版本管理器在你的系统上安装 Ruby。

要检查是否安装了 Ruby，请在终端中运行`ruby -v`命令。这应该会给你 Ruby 版本号，如果没有安装，你会得到一个错误消息说没有这样的命令。从 1.9 版本开始，Ruby 默认自带 RubyGems。

在安装 CocoaPods 之前，首先更新你的 RubyGems。

```
[sudo] gem update --system
```

要安装 CocoaPods，请运行以下命令。

```
[sudo] gem install cocoapods
```

对于上面的两个命令，`sudo`命令是可选的，这取决于您正在使用的 Ruby 安装。如果您使用默认的系统提供的 Ruby 安装，那么您将需要 root 权限来安装 gems，因此您将需要包含 sudo 命令。如果你使用的是 Ruby 版本管理器，比如 [RVM](https://rvm.io/) 或者 [rbenv](http://rbenv.org/) ，这就不需要了，你可以直接运行`gem install cocoapods`。

在安装过程中，您可能会收到一个警告，询问您是否要覆盖 rake 可执行文件。这是由于 rake gem 在此过程中被更新，因此通过键入`y`确认覆盖。

每当您需要更新 CocoaPods 时，运行您用来安装它的相同命令。

接下来运行以下命令在您的系统上安装 CocoaPods。

```
pod setup
```

这将创建一个`~/.cocoapods/`目录，其中包含从 [CocoaPods 主规范报告](https://github.com/CocoaPods/Specs)中克隆的所有可用的公共 pod 规范。

现在我们已经准备好在我们的项目中使用 CocoaPods 了。

### 在项目中使用 CocoaPods

我们将创建一个简单的项目来演示如何使用 CocoaPods。我创建了一个名为 TestApp 的简单应用程序。这是一个 iPhone 单视图应用程序。我将包括一个格式化数字的 pod。

可以在 [CocoaPods 网站](http://cocoapods.org)搜索豆荚。我搜索了“格式化程序”，得到了几个关于号码格式化程序、地址格式化程序、电话格式化程序等的结果。结果列表包括每个 pod 的网站、文档和规范文件的链接。

我将使用 [AKNumericFormatter](https://github.com/blackm00n/AKNumericFormatter) 库。

为了定义项目的依赖关系，您需要创建一个 [Podfile](http://guides.cocoapods.org/using/the-podfile.html) 并列出所有您想要使用的库，并附带一个可选的版本号。

若要创建 Podfile，请使用“终端”导航到您创建的应用程序的根目录。

```
cd /Path/to/Project/TestApp
```

运行以下命令，这将在指定的文件路径创建一个 Podfile。

```
pod init
```

在文本编辑器中打开刚刚创建的`Podfile`。它将有一些默认代码。您可以指定要在每个目标中使用的窗格。

```
# Uncomment this line to define a global platform for your project
# platform :ios, "6.0"

target "TestApp" do

end

target "TestAppTests" do

end
```

取消指定项目目标的语句的注释，并编辑版本号。平台可以是`ios`也可以是`osx`。

```
platform :ios, "7.0"
```

在 TestApp 目标块中添加以下内容。

```
pod 'AKNumericFormatter'
```

如果没有像上例那样指定版本号，将获取 pod 的最新版本。

您可以通过包含特定的版本号来指定确切的版本。例如，`pod 'AFNetworking', '1.0'`

也可以使用逻辑运算符，例如' > 0.1 '，' > = 0.1 '，' < 0.1 '，' < = 0.1 '。

[悲观算子](http://robots.thoughtbot.com/rubys-pessimistic-operator) `~>`也可以用来控制允许的更新。例如使用`~> 0.1.2`，你将获得 0.1.2 到 0.2 之间的 pod 的最高发布版本，不包括 0.2。

您可能想在 Podfile 中不指定所有的 pods 版本，以便在更新时获取最新版本，但这可能不是一个好主意。您可以升级到一个主要的版本变更，其中可能包含不再适用于您的代码的新特性和 API 变更。将 pod 冻结到特定版本或只允许次要版本更新可能更好。CocoaPods 遵循[语义版本化](http://semver.org/)策略。你可以在给定的链接上阅读更多关于主要版本、次要版本和补丁版本的信息。

也可以在本地机器上使用 pod。

```
pod 'ExamplePod', :path => '~/Documents/ExamplePod'
```

或者来自 git 存储库。还支持 Mercurial 和 SVN。

```
pod 'ExamplePod', :git => 'https://github.com/eguser/ExamplePod.git', :branch => 'dev'
```

回到我们的例子，在编辑完 Podfile 之后，保存它并运行下面的命令。

```
pod install
```

CocoaPods 将分析 Podfile 中列出的依赖项，并将这些库下载到一个名为 Pods 的新项目中，该项目位于一个名为 *TestApp.xcworkspace* 的创建的 Xcode 工作区中。您将在终端中收到一条消息，告诉您使用新的工作区。

如果您在 Xcode 中打开了项目，请关闭它并打开工作区。如果您尝试运行旧项目，它将不会构建。您将收到一条链接错误消息。工作区将有两个项目——您的项目(TestApp)和 Pods 项目。pods 项目中有一个组文件夹，其中包含您下载的 Pods。现在，您可以在应用程序中使用这些库。记住在调用框架的任何方法之前添加导入。

```
#import "<AKNumericFormatter/AKNumericFormatter.h"
```

要从项目中删除一个 pod，从 Podfile 中删除它并运行`pod install`。添加新的 pod 也是如此。

要更新项目的依赖项，请运行`pod update`。

## 椰子和源代码管理

关于什么样的 CocoaPods 文件应该进行版本控制，有各种各样的争论。这只是个人喜好的问题。在你的`.gitignore`文件中有一个添加 Pods 文件夹的[利弊](http://guides.cocoapods.org/using/using-cocoapods.html#should-i-ignore-the-pods-directory-in-source-control)列表。然而，您应该将 [Podfile.lock](http://guides.cocoapods.org/using/using-cocoapods.html#what-is-a-podfilelock) 置于版本控制之下。这个文件记录了安装的 Pod 的版本。

## 试用豆荚

CocoaPods 有一个特性，允许您在项目中使用一个库之前下载并试用它。它下载一个 Pod 的演示项目，并在 Xcode 中打开它。

使用`pod try [LIB_NAME]`命令测试一个 Pod。

```
pod try AFNetworking
```

## 结论

CocoaPods 是一个让管理你的项目更加简单的工具。当处理项目中的依赖关系时，它可以节省您大量的精力和时间，因为它使得添加、删除和更新库更加容易。有关使用 CocoaPods 和故障排除的更多信息，请查看 [CocoaPods 指南](http://guides.cocoapods.org/)。

## 分享这篇文章