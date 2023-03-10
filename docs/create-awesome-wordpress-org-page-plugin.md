# 如何为你的插件创建一个很棒的 WordPress 页面

> 原文：<https://www.sitepoint.com/create-awesome-wordpress-org-page-plugin/>

你刚刚为 WordPress 开发了一个很棒的插件，你想和尽可能多的人分享它。要做到这一点，[WordPress.org](https://wordpress.org/)平台似乎是最好的选择，因为它是官方的 WordPress 插件库。

但是将你的插件提交给 WordPress 并不足以获得用户:你必须以最好的方式介绍你的插件，让 WordPress 用户知道你的插件能做什么。

一个完整的 WordPress.org 页面会让你的插件更有吸引力。你将获得更多的用户，这些用户将知道如何使用你的插件。

## 为什么要创建一个好的插件页面？

一旦安装了一个 WordPress 插件，一个简短的描述可以显示在管理面板的`Installed Plugins`页面上，这要归功于一个格式正确的注释，它被放置在插件的主文件中。虽然这是帮助你的用户记住你的插件做了什么的好方法，但它不能帮助你获得更多的用户。

在决定下载你的插件之前，用户通常会查看你的插件的 WordPress.org 页面，以了解它能做什么。用户会看你插件的描述，所以你应该精确地描述你插件的特性(我们将在下一部分看到如何描述)。但是用户也想知道你的插件是否易于使用:一些截图和一个常见问题解答对此很有用。当您发布一个更新时，您的用户会想知道他们为什么必须下载这个更新(它是修复已知的错误还是根除安全风险？)，所以一定要包括这类信息。

记住你的插件不是唯一的:用户可以在几个插件中选择，所以你需要证明为什么你的比其他的好。一个好的插件页面可以给你带来更多的用户，所以最好确保你的页面尽可能的准确和详细。

## 向插件页面添加信息

WordPress 无法猜测你的插件的有用性。因此，为了显示相关信息，WordPress 使用了两个来源:插件主文件中的评论，以及位于插件文件夹根目录下的`readme.txt`文件。

两者都很重要，所以我们将在下面的两个子部分中看到如何正确地完成它们。

### 正确的评论

你的插件可能包括几个文件，但至少你会有一个主文件。这是 WordPress 加载的文件，通常与你的插件同名(例如，`hello-world.php`代表插件`Hello World`)。为了能被 WordPress 识别，这个主文件必须包含一个以特定方式格式化的“评论”。

“评论”是你填写插件基本信息的地方。并非所有字段都是不需要的；有些是必不可少的，有些则非常有用。

基本上，这是在你的插件主文件的开头的最小注释。

```
<?php
/*
 * Plugin Name: My good plugin page
 * Plugin URI: http://mygoodpluginpage.com
 * Description: A test for correctly completing a plugin page.
 * Version: 1.0
 * Author: Jérémy Heleine
 * Author URI: http://jeremyheleine.me
 * License: MIT
 */
?>
```

`Plugin Name`字段，顾名思义，用来表示插件的名称。为了让 WordPress 识别它，它必须是唯一的。

在`Plugin URI`字段中，指出描述你的插件的 URI，如果有的话。例如，如果你的个人网站上有一个页面准确地描述了你的插件，请在这里注明它的 URL。

`Description`字段很重要，因为它的值显示在用户管理面板的`Installed Plugins`页面中。应该很短。

很有可能，你会提供几个版本的插件，包括新的特性或者错误修复。当前版本号必须显示在`Version`字段中。

你可以猜到`Author`字段必须填写你的名字，而`Author URI`字段指的是你的个人网页，例如，你在那里展示自己和你的项目。

最后，我们找到了`License`字段，您可以在其中指定发布插件的许可证的简称。例如，你可以指明`GPL3`或`MIT`。

除了`License`字段，所有这些字段都被 WordPress 读取以显示关于你的插件的信息，基本上是在`Installed Plugins`页面中。这就是为什么你应该总是指出他们。

![Short description in the administration panel](img/ab60a93c638a4bfce590a1cd7cd93395.png)

为了进一步个性化你的插件，WordPress 在这个特殊的评论中增加了另外三个字段。例如，我们可以找到一个`Network`，它是一个布尔值，指示插件是否可以在安装中的所有站点上激活(当多站点启用时)。默认情况下，`Network`设置为`true`。

如果说`Network`字段的用处有限，那么后两个字段就不一样了:`Text Domain`和`Domain Path`，在这两个字段中，你可以分别指明你的插件的文本域和包含`.mo`文件的目录，以便翻译你的作品。

多亏了这些字段，您将能够翻译其他字段:在`Installed Plugins`页面中，您将能够以多种语言显示描述。

```
/*
 * Plugin Name: Plugin page
 * Plugin URI: http://jeremyheleine.me
 * Description: A test for correctly fill a plugin page.
 * Version: 1.0
 * Author: Jérémy Heleine
 * Author URI: http://jeremyheleine.me
 * Text Domain: myplugin-page
 * Domain Path: /lang/
 * License: MIT
 */
```

注意在`Domain Path`字段中显示的路径是相对于你的插件文件夹的。例如，在前面的评论中，WordPress 将使用我的插件文件夹的`/lang/`子目录中的`.mo`文件。默认情况下，WordPress 会在这个文件夹的根目录下搜索你的`.mo`文件。

### 自述文件

现在我们的插件已经准备好正确地显示在你用户的管理面板上，我们需要在 WordPress.org 上用一个好的插件页面来吸引这些用户。为此，我们只需要一个文件:`readme.txt`，它必须位于插件文件夹的根目录下。

你可以在这里找到一个 WordPress 接受[的例子，在这里你可以看到一个插件的`readme.txt`文件本质上是一个填充了正确信息的部分列表。](https://wordpress.org/plugins/about/readme.txt)

这些部分中的大部分将以标签的形式显示在你的插件页面上。例如，在上面链接的自述文件中，我们会发现几个选项卡，如“*描述*”、“*安装*”、“*常见问题*”或“*截图*”。

#### 页眉

首先，我们找到一个特殊的部分。

```
=== Plugin Name ===
Contributors: First author, second author
Donate link: http://link.for/allowing/users/give/you/money
Tags: tag 1, tag 2, tag 3
Requires at least: 3.7
Tested up to: 4.1
Stable tag: 3.0
License: GPLv2
License URI: http://www.gnu.org/licenses/gpl-2.0.html

A short description of your plugin.
```

正如您可能已经猜到的，这用您的插件的全名替换了`Plugin Name`。它将显示在你的插件页面的标题中，在这部分末尾的简短描述上面。

![Plugin Page Header](img/4d978dd8823a10d1699daaaf15dc5222.png)

使用插件作者和贡献者的列表填写`Contributors`字段。该列表必须包含 WordPress.org 标识符。

这里的`Donate link`表示一个网页的链接，用户可以在那里捐款来支持你的工作。然后，`Tags`字段是与您的插件相关的关键字列表。它们不会显示在你的插件页面上，但是会被 WordPress 用户用来找到你的插件。

`Requires at least`和`Tested up to`字段很重要。在前一种情况下，指出你的用户必须使用的插件的最低版本:例如，如果你的插件完全基于 WordPress 3.7 中出现的功能，指出`3.7`。在后一种情况下，指出你测试你的插件的 WordPress 的最高版本号:如果你知道你的插件在最新版本的 CMS 上工作，你应该指出来。

这里的`Stable tag`字段表示你的插件的当前版本号。这一点很重要，因为 WordPress 会读取这个字段来了解是否有更新。

`License`和`License URI`标签用来表示你发布插件的许可证:前者表示一个简短的名称，后者表示一个链接，在那里我们可以找到关于许可证的更多信息。

#### 描述

就在这些基本信息之后，我们找到了`Description`部分。在这一部分，你应该准确地描述你的插件。例如，你可以指明它能做什么，可以用哪种语言，用户可以在哪里联系你，以及任何其他你的用户可能会发现有用的信息:WordPress 不限制描述的大小，所以你可以自由地写你想写的。

与自述文件的其余部分一样，您可以在描述中使用 Markdown 来设置文本格式。

![Plugin Description](img/7318d3b4d4dfcd81ec6d3258e6bda592.png)

#### 装置

在`Installation`部分，你应该描述用户如何安装你的插件。大多数情况下，这一部分是一个简短的列表，如下所示。

```
== Installation ==

1\. Upload the `myplugin` folder to the `/wp-content/plugins` directory
2\. Activate the plugin through the 'Plugins' menu in WordPress
```

![Plugin Installation](img/6f614443c7d0579e08b4d62328816290.png)

#### 常见问题

此部分包含用户经常向您提出的问题列表。例如，你也可以使用这个部分来描述如何使用你的插件，如何配置一个选项，或者如何个性化一个部件。

下面是一个`FAQ`部分的例子(使用了两个不相关的问题)，但是你可以添加任意多个问题。有用的问题优先。

```
== Frequently Asked Questions ==

= What's the name of this plugin? =

The name of the plugin is the one displayed on this page.

= Do you love kittens? =

Of course.
```

![Plugin FAQ](img/f58742b0a22881dbfbb2eda18720125e.png)

#### 变更日志和升级通知

随着项目的发展，新的想法产生了，随之而来的是新版本的插件。在自述文件的标题中，你需要指出当前的稳定版本，这样你的(未来的)用户就知道你的插件是如何发展的，或者仅仅是他们在最新版本中可能会发现什么。这样，他们就会知道他们一直在等待的某个功能是否最终发布了。

这就是`Changelog`部分的目的。在这一部分，你列出了你的插件已经发布的所有版本，列出了每个版本包含的特性。大多数时候，我们更喜欢按时间倒序列出我们的版本，但是你可以自由地做你想做的。

```
== Changelog ==

= 2.0 =
* Awesome new feature
* A brand new button

= 1.0.1 =
* Oops. Bug fixed.

= 1.0 =
* A beautiful button
```

![Example of a Plugin Changelog](img/b2e1efc9a7dd75143067f1d6db490521.png)

下一部分也是“新功能相关”，称为`Upgrade Notice`。它主要是为已经安装了你的插件的用户准备的:当一个新版本的插件可用时，你可以显示一个特别的消息来指出新的东西，以及为什么你的用户应该升级。

例如，通过下面的部分，您的用户将被通知 2.0 版本可用，并且他们将看到消息“*升级以获得令人敬畏的新功能！*”。

```
== Upgrade Notice ==

= 2.0 =
Upgrade for awesome new features!

= 1.0.1 =
This version fixes an important bug.

= 1.0 =
Hello, this is the first version.
```

#### 另一个想法？

上面的部分应该总是出现在你的自述文件中，因为它们对于用户理解你的插件能做什么和如何做是必不可少的。但是，如果你有其他想法的部分，你可以添加你自己的任何其他“经典”的部分。

```
== My Section ==

This section is unique, my plugin is the only one in the world to use it!

== Another Section ==

Sections are **cool**.
```

## 将图像添加到插件页面

### 截屏

如果你读了 WordPress 提供的 readme 文件的例子和上面的链接，你可能会注意到我们忘记了一个部分:`Screenshots`。它的名字很清楚:在这一部分，你应该包括一些描述我们插件界面的截图。

由于一个简单的自述文件不能包含图片，所以有必要知道将图片上传到哪里。首先，你应该知道 WordPress 接受几种格式；您可以使用 PNG ( `*.png`)、JPEG ( `*.jpg`或`*.jpeg`)和 GIF ( `*.gif`)文件进行截图。

以格式`screenshot-n.ext`命名它们，其中`n`是一个数字，`ext`是文件的扩展名。例如，如果我们有三个 PNG 截图，我们将它们命名为`screenshot-1.png`、`screenshot-2.png`和`screenshot-3.png`。

现在您有两个选择来放置您的文件:在`/assets`目录中或者在包含当前自述文件的目录中(例如，如果您的当前版本是 2.0，则在`/tag/2.0`目录中)。注意，WordPress 将首先在`/assets`目录中搜索你的截图:例如，如果你有文件`/assets/screenshot-1.png`和`/tags/2.0/screenshot-1.png`，将显示`/assets`目录中的文件，而不是`/tags/2.0`目录中的文件。

将截图放入正确的目录后，返回自述文件，用有序列表完成`Screenshots`部分。第一项将描述第一张截图(例如命名为`screenshot-1.png`)，第二项将描述第二张截图，以此类推。

```
== Screenshots ==

1\. This is the administration page of this plugin.
2\. The button is beautiful, isn't it?
3\. This is what your visitors will see.
```

![Plugin Screenshots](img/1ea16e426e93ac3f3888850e4b508edd.png)

### 旗帜

在一个 WordPress.org 插件页面，我们可以在一个大横幅的前景看到插件的名称。这个横幅是可选的，但是您可以添加它来增强您的页面的外观。

您的横幅必须为 772 像素宽、250 像素高，并命名为`banner-772x250.png`或`banner-772x250.jpg`。注意这里不允许 GIF 格式。把这个横幅放在`/assets`目录下，就这样:WordPress 会把它显示在你的插件页面上。

由于宽度为 772 像素，您的图像在高 DPI 显示器上可能会模糊不清。要解决这个问题，您可以添加另一个可选图像，它必须是 1544 宽、500 像素高，并命名为`banner-1544x500.png`(或`.jpg`)。把它放在`/assets`目录中，WordPress 会在高 DPI 显示器上用它来代替小横幅。

## 最后

就像许多其他开发者一样，你热爱你的插件，希望拥有尽可能多的用户。但是提供一个好的插件是不够的:如果你没有正确地描述它，用户不会知道你的插件很棒。

一个好的插件页面可以给你带来更多的用户，要做到这一点，自述文件将是你最好的朋友。为了帮助你制作最好的自述文件，WordPress 为你提供了一个工具:[自述验证器](https://wordpress.org/plugins/about/validator/)，它会告诉你是否有信息丢失。

如果您想检索我们在本文中提到的自述文件，您可以在这里找到一个非常小的示例插件。

## 分享这篇文章