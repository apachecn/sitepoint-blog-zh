# WordPress 4.4 的新功能

> 原文：<https://www.sitepoint.com/whats-new-in-wordpress-4-4/>

*这篇文章已经过时了，[点击](https://www.sitepoint.com/whats-new-in-wordpress-4-6/)查看 WordPress 4.6 的新功能。*

WordPress 4.4 刚刚登陆，命名为“Clifford”，以纪念爵士小号手 Clifford Brown。我们已经涵盖了以前的版本，总是有一些让我兴奋的东西，但没有什么比得上这个版本。这是由于本地响应图像支持，WordPress 现在成为 oEmbed 提供商，以及引入了 WP API 的前半部分。

在本文中，我将深入探讨这个最新版本中的新特性，以及升级时需要了解的内容。

## 升级到 WordPress 4.4

如果你还没有进行常规的网站维护，现在是开始的好时机，点击这里查看我们的[WordPress 维护指南。](https://www.sitepoint.com/definitive-guide-to-wordpress-maintenance/)

### 测试或试运行

不管你只有几个访问者，还是几千个，如果你的网站对你很重要，你应该使用一个临时或测试网站。

尽管尽了最大努力，有时复制一个生产系统是困难的，如果不是不可能的话。然而，对于大多数站点来说，一个临时站点将帮助您确定是否存在任何需要注意的问题。

### 确保您有可信的备份

除了升级之外，你应该总是对你的站点进行可靠的备份。用户遇到的大多数问题都是由第三方插件和主题引起的，所以请确保您知道您的网站正在使用什么插件，并查看他们是否发布了任何有关 4.4 支持的内容。

### 选择一个升级的好时机

对于繁忙的站点来说，并不总是一个好时机，但是请尝试选择一个您有资源来最好地管理任何停机或升级问题的时间。另外，选择一个网站流量较少的时间，比如晚上。

我总是发现 WordPress 的升级和预期的一样有效。我在升级时遇到的所有问题都是由于插件和主题的兼容性问题。这也是另一个选择积极开发、良好支持、高质量插件和主题的好理由。

### 如果你遇到问题

尝试基本的故障诊断步骤，如禁用插件和选择默认主题，以尝试隔离问题所在。看看这篇关于常见 WordPress 问题以及如何解决它们的文章。

如果你需要帮助，你也可以去官方的 WordPress 支持论坛，但是请[确保你已经先阅读了欢迎帖](https://codex.wordpress.org/Forum_Welcome)。

如果你想了解更多关于应用更新的细节，我们也有更新 WordPress、插件和主题的指南。

### 应用更新

如果你在最近一天登录了你的 WordPress 站点，你应该会看到这个警告:

![WordPress 4.4 Update](img/901babef2cb923a781b433922d2d84d7.png)

当您单击“请立即更新”时，您将转到一个页面，该页面提供了有关将此更新应用到网站的更多详细信息，如下所示:

![WordPress 4.4 Upgrade](img/68a94e96d1e504bc8b84a550caca0b3f.png)

当你点击“立即更新”时，WordPress 4.4 将被应用到你的站点，当你看到这个屏幕时，你知道你已经成功了:

![WordPress 4.4 Welcome](img/ecb393500501cbdbd5ee4d3ee6c9b4f6.png)

好吧！如果你已经到了这一步，你现在可以直接进入 WordPress 4.4 的新特性了。

## WordPress 4.4 的新特性

### WP API

被称为 WP API 的 WordPress REST API 并不新鲜。事实上，它已经以插件的形式出现，并且已经在一些非常有趣的项目中使用了很长时间。然而，这是它第一次被包含在 WordPress 核心中。

WP API 分两部分引入，4.4 将包含第一部分。从用户的角度来看，可以把 WP API 想象成一个管道，允许开发者使用 WordPress 作为框架，轻松地创建强大的新应用。对于 WordPress 和致力于此的团队来说，这是一个巨大的里程碑。

要了解更多关于 WP API 的知识，请查看 Ben Shadle 的[介绍和使用 WordPress REST API 的指南](https://www.sitepoint.com/wp-api/)。

### oEmbed 提供程序

你可能已经知道 WordPress 从 2.9 版本开始就支持 oEmbed，并且来源列表随着每个新版本的发布而不断增加。无论是 Twitter 上的推文还是 YouTube 上的视频，你只需从任何可能的 oEmbed 提供商那里复制 URL，然后粘贴到你的 WordPress 编辑器中就可以嵌入内容了。

有了 WordPress 4.4，你的网站现在也是一个 oEmbed *提供商*。

这意味着你可以允许其他人将你网站的内容直接嵌入他们的网站。反之亦然，你可以从另一个 WordPress 4.4 站点抓取任何 URL，然后粘贴到你的帖子中，它会自动显示出来。以下是在 WordPress.org 上添加最新帖子的 URL 时的示例:

![WordPress oEmbed Provider Example](img/db23d4b2f4c21efaa8380f755711a1fe.png)

### 响应图像

移动设备的重要性持续上升，随着如此多的不同屏幕尺寸和设备被用于消费网络内容，确保网站的响应性比以往任何时候都更加重要。

由 SitePoint 自己的天才 [Tim Evko](https://twitter.com/Tevko) 开发，这是一个用户将立即受益的功能。

有了 WordPress 4.4，响应图像现在可以在你的网站上自然地显示任何图像(包括你现有的图像)。这是一个伟大的新功能，肯定会提高用户体验。

这个功能是通过给 WordPress 生成的图像标记添加`srcset`和尺寸支持来实现的。

WordPress 现在将自动在媒体库中创建一些图像尺寸，通过在`srcset`属性中包含图像的可用尺寸，浏览器可以根据设备的能力显示最合适的图像尺寸。从性能角度来看，这也提高了页面速度。

![Desktop Responsive Images](img/e66735aacdf40121e35664a56d2811bf.png)

![Responsive Image Mobile](img/c8257b868c8d46b166c719275b718dc5.png)

### 二十六点

下面的图片展示了新的 WordPress 主题。极简设计是基于一个水平标题，可以选择有一个右边栏和几个自定义颜色选项。该主题首先被设计为移动，因此用户可以在任何设备上享受优化的体验。

![Twenty Sixteen](img/42dd81648cce1b1b8cd48825c15fd056.png)

![Twenty Sixteen Mobile](img/2e0d93058f0a95504e9463b021e32f54.png)

### 其他新增内容

在引擎盖下也进行了大量的工作，其他一些关键变化包括:

*   注释的重大改进
*   术语元 API
*   对多站点的改进
*   定制器的改进
*   更多信息([点击此处查看完整列表](https://codex.wordpress.org/Version_4.4))

## 进一步阅读

如果这对你来说还不够好，这里有一些很棒的资源供你进一步阅读，这样你就能真正收获这个最新版本的好处:

*   [WordPress 4.4 领域指南](https://make.wordpress.org/core/2015/11/11/wordpress-4-4-field-guide/)
*   [WP API——使用 WordPress REST API](https://www.sitepoint.com/wp-api/)
*   [站点点上的 WP API](https://www.sitepoint.com/wordpress/wp-api/)
*   官方 WordPress Codex 文档

我很想听听你对 WordPress 4.4 的想法和反馈！

## 分享这篇文章