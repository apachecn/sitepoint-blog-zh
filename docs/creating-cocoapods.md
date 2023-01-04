# 创造椰子

> 原文：<https://www.sitepoint.com/creating-cocoapods/>

在之前的一篇文章中，我们介绍了 CocoaPods，一个用于 cocoa 开发的依赖管理工具，并介绍了使用它的原因和方法。在本文中，我们将看看如何创建自己的 CocoaPod，并让其他人可以在他们的项目中使用它。

对于本教程，我有一个将电话号码格式化的类，我想把它做成一个 CocoaPod。我的手机格式库由类文件`JKEPhoneNumberFormatter.h`和`JKEPhoneNumberFormatter.m`以及一个`README.md`文件组成。

首先，运行以下命令以确保您拥有最新的 CocoaPods 版本。如果还没有安装 CocoaPods，该命令将安装它，如果已经安装了 cocoa pods 并且需要更新，该命令将进行更新。

```
[sudo] gem install cocoapods
```

使用`sudo`命令取决于您的 Ruby 安装。在[之前的教程](https://www.sitepoint.com/cocoapods-good/)中有更多关于这个的内容。

我建议您使用最新版本的 CocoaPods，因为命令语法可能已经改变，并且添加了新的特性。

要检查 CocoaPods 版本，运行`pod --version`。在撰写本文时，最新版本是 0.33.1。

您需要在 CocoaPod 中包含各种文件。这些包括类文件、Podspec、许可证、库中使用的资产和一个演示项目，该项目展示了 Pod 的使用(在项目中使用 Pod 之前，当用户键入`pod try [LIB_NAME]`以查看它做了什么时运行该项目)。

您可以创建自己的 Pod 目录结构和文件，也可以使用 CocoaPods 提供的命令创建一个模板库结构来快速入门。这将生成一个 *Podspec* 文件(我们将在本文后面看看这是什么)、许可文件、自述文件和一个演示项目，其中包含测试文件和一个 [Podfile](http://guides.cocoapods.org/using/the-podfile.html) 。

要使用生成器，请运行下面的命令。

```
pod lib create [pod name]
```

您将面临四个问题，这些问题将决定如何设置您的库。你将被询问是否想要为你创建一个演示项目，你想要使用什么样的测试框架，你是否想要基于视图的测试，以及你想要使用什么作为你的类前缀。

以下是运行命令后创建的目录结构的示例。文件生成后，项目将在 Xcode 中打开。

```
Example
    JKEPhoneNumberFormatter
    JKEPhoneNumberFormatter.xcodeproj
    JKEPhoneNumberFormatter.xcworkspace
    Podfile
    Podfile.lock
    Pods
    Tests
JKEPhoneNumberFormatter.podspec
LICENSE
Pod
    Assets
    Classes
README.md
```

我们将了解如何手动创建 Pod。这种方法的一个优点是，您可以获得一个精简的库结构，只包含您需要的内容。使用生成器，您可能需要检查生成的文件，删除不需要的内容。当然，生成器有它的优势，例如，快速建立库结构，让您知道什么是 Pod 库的一部分。使用哪种方法是个人喜好的问题。

首先，我将我的类文件放在一个`Classes`文件夹中。这不是必需的，但是为了便于使用和可读性，最好将 Pod 的不同部分分开放在文件夹中。文件夹名称也由您决定。另一个常见的命名约定是给文件夹命名为库，例如在我的例子中，文件夹将被标记为`JKEPhoneNumberFormatter`。

我已经包括了一个演示项目，这只是一个项目，显示您的库在使用中。该项目是一个简单的单视图 iPhone 应用程序，它导入了`JKEPhoneNumberFormatter`类，并使用其方法输出格式化的电话号码。

我还包含了自述文件和许可文件。自述文件简要总结了该类的作用和使用方法。您需要在您的库中包含一个代码许可证，否则它将不会被用于发布 pod 的 CocoaPods Trunk 接受(稍后将详细介绍 CocoaPods Trunk)。如果你不确定使用哪种许可，你可以在 [tl 阅读不同的软件许可；法律博士](https://tldrlegal.com/)。这是一个有用的网站，以简单易懂的方式总结了不同的软件许可条款，没有法律术语。

下面是我的目录结构。

```
Classes
    JKEPhoneNumberFormatter.h
    JKEPhoneNumberFormatter.m
Demo
    AddressBook.xcodeproj
    AddressBook
    AddressBookTests
README.md
LICENSE
```

接下来，我们在库结构的根目录下创建一个 [Podspec](http://guides.cocoapods.org/making/specs-and-specs-repo.html) 文件。这是一个描述 Pod 库版本的文件。它包含 Pod 的详细信息，包括版本号、源位置、依赖项等。

导航到库结构的根目录，并运行以下命令。您可以手动创建自己的文件，但是下面的命令会生成一个模板，您可以从它开始。

```
pod spec create JKEPhoneNumberFormatter
```

Podspec 文件是用 Ruby 编写的，它包含了 Pod 的规范。生成的模板有一个属性列表，带有解释它们用途的注释。最终的等级库文件将取决于您的库和您选择提供的信息。CocoaPods 指南给出了不同类型库的不同规范文件的例子。

下面是我的最终规格文件。

我已经删除了所有的评论和一些属性。

```
Pod::Spec.new do |s|

    s.name         = "JKEPhoneNumberFormatter"
    s.version      = "0.1.0"
    s.summary      = "NSFormatter subclass for formatting phone numbers."
    s.homepage     = "https://github.com/echessa/JKEPhoneNumberFormatter"
    s.license      = { :type => "MIT", :file => "LICENSE" }
    s.author       = "Joyce Echessa"
    s.source       = { :git => "https://github.com/echessa/JKEPhoneNumberFormatter.git", :tag => "0.1.0" }
    s.source_files  = "Classes", "Classes/*.{h,m}"
    s.requires_arc = false

end
```

我只包含了几个属性，即名称、版本、摘要、主页、许可证、作者、源代码、requires_arc 和源文件。我把库推送到 Github，加了一个标签`0.1.0`标记版本号。除了 git，还支持以下源属性:hg、bzr、svn 和 HTTP。等级库模板列出了可用的属性，您可以根据需要进行编辑。使用的语法非常简单，但是如果您有任何问题，您可以阅读更多关于使用的[规范语法](http://guides.cocoapods.org/syntax/podspec.html)的内容。

编辑完成后，运行`pod spec lint`命令检查您的规范文件是否有效。如果缺少所需的属性、语法错误、访问源存储库的问题或任何其他问题，您将会收到一条错误消息。

```
pod spec lint JKEPhoneNumberFormatter.podspec
```

在规范通过验证之后，您的库就可以发布到 CocoaPods 规范存储库中了。

在 CocoaPods 版本之前，提交 Pod 需要在 Github 上发出 pull 请求。这被证明是低效的，并且对于 CocoaPods 的维护者来说管理存储库是困难的。有时 Podspecs 会在没有通过`pod lint`的情况下被提交，并且在团队评审时会被拒绝。随着 CocoaPods 变得越来越流行，这种手动审查是不可伸缩的。除了原始库作者之外，很难跟踪其他人的未经授权的提交。

CocoaPods Trunk 就是为了解决这些问题而推出的。它是一个 web 服务，允许开发人员直接从命令行发布 Pods，而不需要创建拉请求。

对于主干，第一个发布 pod 的人将成为等级库报告中 pod 名称的所有者。然后，所有者可以添加其他所有者。只有所有者才能发布 pod 的后续版本。所有者也有责任确保他们的 podspec 正常工作。主干服务只验证规范是否具有注册 podspec 所需的最基本的元数据。

要使用 CocoaPods Trunk，您必须首先向 Trunk 服务注册您的机器。要注册，运行`pod trunk register`命令。这将注册一个新的帐户，或者创建一个新的会话(如果您以前注册过的话)。如果您是第一次注册 Trunk，您必须提供您的电子邮件地址和姓名。如果您以前注册过，可以省略您的姓名。

您还可以向该命令添加其他选项。下面我使用`description`选项来标识我在该会话中使用的机器。这使得以后更容易识别您的会话，尤其是当您使用不同的计算机时。

```
pod trunk register joyce@mydomain.com 'Joyce Echessa' --description='Home Desktop'
```

一封电子邮件将发送到您注册的带有确认链接的电子邮件地址。

要发布您的 Pod，请使用您的 podspec 文件的名称运行以下命令。

```
pod trunk push JKEPhoneNumberFormatter.podspec
```

在推 CocoaPod 之前，它将首先验证您的 podspec。

就是这样。您刚刚创建了一个 CocoaPod，并将其放在 Pod 存储库中供其他人使用。您应该注意，这将是一个公共 pod。CocoaPods 还允许你创建[私有 pod](http://guides.cocoapods.org/making/private-cocoapods.html)。创建 Pod 的步骤是相同的，但是不是使用 Trunk 推送到公共规范报告，而是推送到您自己的存储库。我不会在这里深入讨论整个过程，但是如果你想了解更多关于这个的信息， [CocoaPods 指南](http://guides.cocoapods.org/making/private-cocoapods.html)有一个关于如何做的很好的大纲。私有 pods 的一个优点是，您可以拥有一些内部库，组织中的团队成员可以通过 CocoaPods 轻松访问这些库。

### 结论

我们已经了解了如何将你的图书馆变成一个 CocoaPod，并让其他人也能使用它。如果你想了解更多关于 CocoaPods 的信息，一个很好的起点就是通读 CocoaPods 指南。

## 分享这篇文章