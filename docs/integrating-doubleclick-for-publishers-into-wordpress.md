# 将 DoubleClick for Publishers 集成到 WordPress

> 原文：<https://www.sitepoint.com/integrating-doubleclick-for-publishers-into-wordpress/>

当建立我的新网站时，我想要一个专业的广告管理解决方案。我想做的不仅仅是 AdSense 广告，还想将广告单元整合到基于主题的捆绑包中，以便更好地锁定目标。

我以前听说过 DoubleClick for Publisher。DFP 于 2008 年被谷歌收购，如果你每月的浏览量少于 9000 万次，它是免费的。然后，您将使用 Small Business edition，该版本将用于本教程。

我的网站是建立在 WordPress 上的，整合它需要几个步骤。也就是说，如果你想获得我在自己网站上使用的同样的定位方法。我将首先解释我试图达到的目标，并通过一个截屏展示给你看我是如何做到的。

## 这个想法

我的网站分为几个不同的主题，如安全官员职位或乘客服务代理职位。这是一个所谓的利基网站。对于大多数主题，我都有一个职位描述页面，和一个单独的职位空缺页面。我希望这两个页面允许相同的定向广告。

我还贴了一个空姐工作的系列文章([看看](http://www.heathrowcareers.co.uk/series/air-hostess/)明白我的意思)，一共五页。整个系列应该具有相同的瞄准可能性。文章定位允许内容内广告(468×60)和侧边栏中的大 300×600 单元，还有两个围绕内容设置的站点运行广告单元(728×90 标题和 300×250 侧边栏)。

看一下链接的页面就知道了。在下面的截图中，你可以看到红色的 ROS 广告和蓝色的定向广告。

![DFP for WP](img/c7e1e17a7f7979b46112990017753759.png)

左上角显示大小的小标记是由 Google Publisher 工具栏生成的，将在截屏中显示。

## 意识到

到目前为止，这个想法似乎非常清晰和简单，但是还有一些障碍需要克服:

*   为了优化报道，每个定向广告都需要在 Google DFP 中有自己的单元。
*   页面上的每个目标单元都需要与该页面上的其他单元组合成一个广告区。
*   广告代码需要在内容中实现，这是 WordPress 不太喜欢的。
*   侧边栏广告要有针对性，你需要能够添加自定义侧边栏。
*   标题广告是大多数模板中内置的东西。如果没有，这需要编码(不包括在本教程中)。
*   Google DFP 为每个广告单元使用大量代码，其中一些需要插入到每个页面的。

下面的截屏将带您完成所有这些步骤。我将向您展示如何:

1.  在 Google DFP 中生成广告单元，并将它们组合成广告位置
2.  生成谷歌 DFP 标签(广告代码)，并在你的网站主体(内容、侧栏和标题)中实现
3.  将相关代码插入每个`部分`

 `## 电影剧本

[youtube 4K4bWJeg8ec]

## 资源

在这个截屏中，我使用了以下主题、插件和服务:

主题:

*   [好消息](http://themeforest.net/item/goodnews-premium-wordpress-newsmagazine/1150692)

插件:

*   [代码插入管理器](http://wordpress.org/plugins/q2w3-inc-manager/):用于在页面头部插入代码
*   [自定义字段短码](http://wordpress.org/plugins/custom-fields-shortcodes/):用于在页面正文中插入代码
*   [自定义侧边栏](http://wordpress.org/plugins/custom-sidebars/):当您的模板不允许您添加自定义侧边栏时使用。本教程中未使用
*   [复制小部件](http://wordpress.org/plugins/duplicate-widget/screenshots/):用于在一个侧边栏小部件中设置站点广告代码的运行，并在其他侧边栏中使用相同的代码。这样你只需要改变一个部件

插件浏览器:

*   [Google Publisher 工具栏](https://chrome.google.com/webstore/detail/google-publisher-toolbar/omioeahgfecgfpfldejlnideemfidnkc?hl=nl):用于检查您页面上的广告单元

广告经理:

*   [谷歌双击发布者](https://www.google.com/intl/nl/doubleclick/publishers/welcome/)

演示网站:

*   [HeathrowCareers.co.uk](http://www.heathrowcareers.co.uk/)
*   –>更具体的:[空中小姐职位](http://www.heathrowcareers.co.uk/series/air-hostess/)

## 分享这篇文章`