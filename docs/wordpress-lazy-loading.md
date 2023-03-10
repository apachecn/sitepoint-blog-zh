# WordPress 的延迟加载:如何将它添加到你的网站

> 原文：<https://www.sitepoint.com/wordpress-lazy-loading/>

*这篇关于 WordPress 懒惰加载的文章最初由扭矩杂志发表，经允许在此转载。*

给你的 WordPress 网站添加惰性负载是提高你的 WordPress 网站速度的有效工具。我们已经[写了大量](http://torquemag.io/2015/08/ways-to-speed-up-wordpress-decrease-page-load-time/)(我们甚至做了一个[案例研究](http://torquemag.io/2017/01/speed-up-your-wordpress-website-case-study/))关于缓慢的页面加载速度如何负面影响转化率、SEO 和其他在线成功的标志。你的网站加载越快越好。

降低网站速度的两个最重要的因素是 HTTP 请求和页面权重。自然，浏览器发送给服务器的请求越多，处理网站的时间就越长。同时，你的页面越大(例如，因为它包含了很多图片)，加载的时间就越长。

惰性加载是一种解决这两个因素的技术。这意味着延迟加载网页的某些部分，直到它们真正被需要。也是这篇博文的主题。在下文中，你将学习惰性加载是如何工作的，它的优点和缺点，以及添加惰性加载到 WordPress 的方法。

我希望你不会太懒而不想继续读下去！

## 什么是惰性负载，你可以用它来做什么？

如前所述，延迟加载是一种防止浏览器最初加载网页部分(通常是图片)并用占位符替换它们的方法。这样，页面变得更小，需要从服务器传输到浏览器的数据也更少。

结果是:一个加载速度更快的网站和更快乐的用户。

但是等等，你不需要你网站的那些部分吗？如果你没有，他们就不会在上面，对吗？不会把页面搞乱吗？

好吧，这就是惰性负载中的*惰性*发挥作用的地方。这种技术不只是简单地禁止浏览器加载资源和图像，而是简单地延迟它，直到真正需要它们的时候。

例如，一张图片不需要出现在页面上，直到访问者向下滚动足够远才能真正看到它。因此，在这种情况发生之前，浏览器并不需要加载它。这正是懒惰负载所做的。

(顺便说一句，还有其他方法可以优化你网站上的图片，让它们加载得更快。)

### 惰性负载用例

在 web 中，惰性加载最常用于图像。你可能在 Buzzfeed 等热门网站上见过它，这些网站使用这种技术让他们的列表条更快地出现在用户的浏览器中。

![And example of Buzzfeed's lazy loading of image](img/32757feb3756df387994c4aec5a48d57.png)

然而，这并不是惰性负载能够延迟的唯一事情。其他例子有视频、脚本和评论。事实上，将 JavaScript 文件移动到页面的页脚是加速页面加载速度的常用技术。

你可能也知道，YouTube 只在你向下滚动到评论时才加载评论。这样，平台就能确保你更快地观看视频。如果对他们来说够好了，为什么对你来说不够好呢？

惰性加载还有一个特殊的应用，叫做无限滚动。这是传统分页的一种替代方式，在传统分页中，每当用户到达页面底部时，就会加载额外的帖子。你可以在 Pinterest 上看到它，或者如果你是 Pocket 的用户。

![Pocket's lazy loading and infinite scroll](img/77cc9a9c0f45122cd2be3ee045a278e0.png)

它消除了大量的点击，对用户体验非常有益。不过无限卷轴[不是没有争议](https://www.smashingmagazine.com/2016/03/pagination-infinite-scrolling-load-more-buttons/)不会是本文的重点。

### 延迟加载的优势

惰性加载的主要优势已经很明显了:更快的加载时间。当你可以去掉一大块页面权重时，它自然会更快地出现在浏览器中。

一个很好的副作用是，你可以为你的访问者节省大量的带宽。尤其是那些在移动设备上使用数据连接的人将会心存感激。除了能更快地看到你的网站，他们也不必把计划浪费在他们可能永远也看不到的图片上。

### 在你的网站上使用延迟加载的缺点

然而，使用懒惰负载并不都是阳光和彩虹。这项技术也有一些缺点。

首先，搜索引擎并不总是友好的。当您为访问者延迟加载资产时，您也会为搜索引擎蜘蛛延迟加载资产。因此，他们可能不会索引你的一些内容，当然，这对于 SEO 来说并不好。即使[有变通办法](https://developers.google.com/webmasters/ajax-crawling/docs/getting-started)。很自然，当对帖子而不仅仅是图片使用延迟加载时，这是一个更大的问题(这就是为什么许多人远离它)。

第二个问题是用户体验。当快速向下滚动网站时，用真实的视觉效果替换占位符图像会使页面内容跳跃，这很烦人。

那么，对懒惰的结论是什么？简而言之:在一定范围内使用这种技术可以让你的 WordPress 站点加载得更快，然而，你需要留意 SEO 和用户体验。

## 如何通过插件给 WordPress 添加惰性加载

虽然可以手动给 WordPress 添加惰性加载(参见 Jay Hoffmann 的教程《优雅主题》)，但是更简单的选择是使用下面的插件。这里列出的许多服务都是简单的“一劳永逸”解决方案，您只需激活它们，无需任何配置。它们中的大多数也非常轻便。

出于这个原因，在没有插件的情况下给 WordPress 添加 lazy load 对我来说似乎是不必要的努力(除非你这样做是为了提高你的技术水平，这是一个合理的理由)。对于大多数用户来说，最好从 WordPress sphere 提供的许多惰性加载插件中选择一个。

### [惰性负载](https://wordpress.org/plugins/lazy-load/)

我们的第一个候选方案是目前最流行的延迟加载图像的免费解决方案。它已经安装了超过 90，000 次，并获得了 4 星评级。其受欢迎的部分原因可能是自动化团队对此做出了贡献。

惰性加载插件使用 jQuery.sonar 来加载出现在视口中的图像(视口是您在设备上看到的站点的一部分)。解压后的插件只有 20KB 大，你需要做的就是安装并激活它。不需要配置。

### [BJ 懒载](https://wordpress.org/plugins/bj-lazy-load/)

![BJ lazyload WordPress plugin](img/b651877ef2d2b5d996dda4e5a20d7ca4.png)

最佳懒加载 WordPress 插件的亚军是 BJ 懒加载。它拥有超过 60，000 次安装和类似的良好评级。

该插件会将内容中的所有图像和 iframes(包括 YouTube 和 Vimeo 视频)替换为占位符，直到被查看。安装，激活，你就可以走了。

### [WP 火箭惰性装载](https://wordpress.org/plugins/rocket-lazy-load/)

![LazyLoad WordPress plugin by WP Rocket](img/9df7aef4eaf908f5564a46a5628439d7.png)

这里我们有一个插件，与流行的缓存插件 WP Rocket 出自同一制造商。它可以延迟加载图像和/或 iframes，包括缩略图、小部件内容、头像和表情符号。更重要的是，该插件不使用任何 JavaScript 库。这可能是它重量不到 10KB 的原因之一。

超过 10，000 个网站信任 WP Rocket 的 Lazy Load，因此它似乎在正常工作。WordPress 目录中的插件站点也包含有选择性地关闭页面或图片的延迟加载或者改变其加载阈值的代码。否则，没有配置选项可用。

### [a3 惰性负载](https://wordpress.org/plugins/a3-lazy-load/)

![A3 Lazy Load WordPress plugin](img/fa28489d6ce2b40ba936a0c95571885f.png)

a3 Lazy Load 声称是“功能最全，难以置信的容易为 WordPress 设置的 Lazy Load 插件”它也是这个列表中少数几个实际上带有一些设置的条目之一。

该插件适用于延迟加载帖子、页面、小工具、缩略图和/或头像中的图像、视频和 iframes。它也适用于 post embeds，并且与 WooCommerce 兼容(阅读我们的 WooCommerce 初学者指南)。

一个 3 Lazy Load 甚至提供了出现在网站上的特效图片。如果这还不够，它还让你选择是在页眉还是页脚加载它的脚本(一个惰性加载插件，惰性加载自己，如何元)，并忽略图像或视频。

最后，它与一整套移动和缓存插件以及内容交付网络兼容。我开始明白他们的说法可能并非完全没有根据。

### [疯狂的懒惰](https://wordpress.org/plugins/crazy-lazy/)

![Crazy Lazy Load WordPress plugin](img/16d9f0781c4ae9072fa19aaadfe4a709.png)

我加入这个插件的主要原因之一是它令人敬畏的标志。我是说，看看那只蜗牛！

然而，除此之外，它也是一个使用很少资源的图像惰性加载插件。Crazy Lazy 附带了通常的功能，并在用户看到图像之前阻止图像的加载。没有必要的配置，只需打开它，你很好。

如果你想配置一些东西，你可以通过 CSS 设置你自己的占位符图片(插件页面包含代码)和排除图片(也有代码)。

### [加速–延迟加载](https://wordpress.org/plugins/speed-up-lazy-load/)

![Speed Up lazy load WordPress plugin](img/e46f4019dc4ae85d6b92cd25142f8682.png)

另一个插件，保持图片和 iframes 在必要时才加载。只有 5KB 光(这些开发者是在竞争谁能做出最小的插件吗？)并且不需要任何配置。它有一个短代码，可以在任何不需要的地方停用延迟加载，并允许您为单个图像停用它。到目前为止，只有 3000+安装，但是，它有一个完整的 5 星评级。

### [WordPress 无限滚动 Ajax 加载更多内容](https://wordpress.org/plugins/ajax-load-more/)

![WordPress Infinite Scroll ajax load more WordPress plugin](img/87ea34a22d12206eadec33a50d77f046.png)

我们之前讨论过无限卷轴，现在这里有一个插件可以把它添加到你的网站上。

WordPress Infinite Scroll 允许你通过构建你自己的自定义短代码来延迟加载文章、单篇文章、页面、评论和其他页面元素。您可以通过帖子类型或格式、日期、类别、标签等查询不同的内容类型。之后，你可以在 [WordPress 编辑器](http://torquemag.io/2015/08/customize-wordpress-wysiwyg-editor/)或者模板中添加简码。

该插件还可以与 WooCommerce 和 Easy Digital Downloads 一起使用，并提供了许多高级插件。

### [WP YouTube Lyte](https://wordpress.org/plugins/wp-youtube-lyte/)

我们的最后一个条目是一个仅用于视频内容的惰性加载插件。它允许你输入特殊的 YouTube 链接，只在点击时加载 YouTube 播放器，而不是更早。

基本上，你需要做的就是改变链接中的一个字母来使它工作。或者，你可以使用短码输入视频内容，或者让插件自动解析 YouTube 链接。另外，如果需要的话，它可以让你只插入音频部分。

或者，检查[视频的延迟加载](https://wordpress.org/plugins/lazy-load-for-videos/)。

## 简而言之，懒惰加载和 WordPress

随着页面加载速度继续成为用户体验和 SEO 的重要因素，惰性加载是您工具箱中的另一个改进工具。这项技术减少了页面权重和 HTTP 请求，允许访问者更快地看到他们想要的页面。

上面，我们已经讨论了惰性加载是如何工作的，以及你可以用它来做什么。除了它主要应用于图像，你还可以将它应用于视频、脚本、评论，甚至帖子和页面。

虽然你需要注意 SEO，但是如果使用方法正确的话，惰性加载仍然是让你的 WordPress 站点更快的好选择。如果你想试一试，这个列表中的一个插件肯定能满足你的需求。

## 分享这篇文章