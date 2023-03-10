# 如何提高网页性能，充分利用你的主机

> 原文：<https://www.sitepoint.com/how-to-improve-page-performance-and-make-the-most-of-your-hosting/>

![Server farm](img/9f44ae76047e7c6de541c7f7a9287757.png)

*本文由[主办事实](https://hostingfacts.com/)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

你可以花几个月的时间建立一个令人惊叹的网站，却在最后阶段因为优化或托管不足而让自己失望。

[平均页面重量已经达到 2300 kb](http://httparchive.org/interesting.php)，[每年增长 15%左右](https://www.sitepoint.com/average-page-weight-increased-another-16-2015/)。像[谷歌加速移动页面](https://www.ampproject.org/)、 [Facebook 即时文章](https://instantarticles.fb.com/)和广告拦截器这样的项目的兴起凸显了用户对我们创造的网络的失望。肥胖页面:

*   加载和渲染速度较慢
*   在移动网络上挣扎，可能会让用户花钱
*   在较慢的设备和智能手机上响应较慢
*   会影响你的搜索引擎排名
*   更难更新和维护。

很少有开发者会费心去优化他们的网站，那么你为什么要这么做呢？

原因:*没有负面影响*。你的搜索引擎排名提高。您的用户受益于更流畅的体验。你的转化率增加。你的托管费用下降。与现实生活不同，最大幅度的减肥可以用最少的努力实现…

## 站点分析工具

在做出改变之前，评估任何问题的规模。有几个免费工具可以用来报告请求数量、文件大小和服务器响应速度。有的提供改进建议。

*   [Pingdom 网站速度测试](http://tools.pingdom.com/fpt/)
*   [谷歌页面速度洞察](https://developers.google.com/speed/pagespeed/insights/)
*   [GTmetrix](https://gtmetrix.com/)
*   [网页测试](http://www.webpagetest.org/)
*   [网页分析器](http://www.websiteoptimization.com/services/analyze/)

或者，使用浏览器开发工具中的**网络**或**分析工具**来评估你的网站。制作一份统计数据的副本，以便您可以在以后比较改进情况。

以下部分提供了优化建议，从最简单的更改开始。

## 找到一个合适的虚拟主机

你的网站可能花费了大量的时间和金钱来创建。你真的应该以每月 5 美元的价格托管它吗？

花点时间评估你是否需要共享服务器、你自己的私有服务器或者基于云的虚拟服务器上的空间。在 [Hosting Facts](https://hostingfacts.com/hosting-reviews/) 等网站上阅读主机评论，并向其他有类似需求的人寻求建议。这时，他们建议用 [A2 托管](https://hostingfacts.com/hosting-reviews/a2-hosting/)或[一个小橘子](https://hostingfacts.com/hosting-reviews/asmallorange-hosting/)。

## 使用内容交付网络(CDN)

浏览器将 HTTP 请求限制在每个域 4 到 8 个同时连接。一次加载 40 个页面资产是不可能的——文件在每个请求线程上排队。

此外，您的用户可能位于与您的服务器不同的地理位置。法国的用户会看到英国服务器的响应速度比澳大利亚的类似硬件更快。

CDN 通过将网站资产分发到其他服务器来提高下载速度。这些机器在物理上离用户更近，并且从不同的域运行，这使得 HTTP 请求限制增加了一倍以上。

cdn 已经变得更容易使用，并且一旦您配置了 DNs 设置，许多 cdn 会自动处理资产。常见选项包括:

*   [CloudFlare](https://www.cloudflare.com/)
*   [MaxCDN](https://www.maxcdn.com/)
*   [亚马逊 CloudFront](https://aws.amazon.com/cloudfront/)

## 启用 GZIP 压缩

大约三分之一的网站[不支持 Gzip 压缩](http://w3techs.com/technologies/details/ce-gzipcompression/all/all),但它可以大幅减少发送到浏览器的数据量。Gzip 压缩通常由您的 web 主机在服务器上设置——联系他们以获得进一步的建议。

## 启用缓存

缓存确保浏览器下载一次资源文件。本地版本会一直保留，直到您的网站指示它获取更新。第一页的加载速度不会更快，但后续的页面加载速度会大大提高。

WordPress 等内容管理系统的插件使缓存变得简单，例如 [W3 Total Cache](https://wordpress.org/plugins/w3-total-cache/) 或 [WP Super Cache](https://wordpress.org/plugins/wp-super-cache/) 。

其他系统可以采用 HTTP 头中的[过期](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.21)、[最后修改](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.29)、[保活](https://tools.ietf.org/id/draft-thomson-hybi-http-timeout-01.html)或 [Etag](https://en.wikipedia.org/wiki/HTTP_ETag) 等技术。您的主机可能会提供配置选项，或者您可以定义自己的选项。例如，一个阿帕奇人。将所有图像缓存一个月的 htaccess 设置:

```
 <IfModule mod_expires.c>
ExpiresActive On

<FilesMatch "\.(jpg|jpeg|png|gif|svg)$">
ExpiresDefault "access plus 1 month"
</FilesMatch>

</IfModule> 
```

## 优化您的媒体

图片占[页面权重的 60%以上](http://httparchive.org/interesting.php)。平均每个页面需要 55 个大小为 1，457KB 的独立图像、126KB 的字体、400KB 的视频和 45KB 的 Flash。考虑到当前简单化、纯色平面设计的趋势，这似乎有点荒谬！

第一步:*去掉不必要的资产*。你需要背景视频、英雄形象、斜体字体或者 300 个很少有人会看到的图标吗？你能使用字体的[子集吗？你可以用 CSS3 效果替换一些图片吗，比如渐变或者边框？](https://www.sitepoint.com/joy-of-subsets-web-fonts/)

假设需要图像，请确保使用最有效的格式。总的来说:

*   SVG 适用于线图
*   Web 字体可能是单色图标的一个选项
*   PNG 或者 GIF 最适合那些有清晰颜色定义的小图片，比如图标、按钮和截图
*   JPG 最适合摄影或任何细节不太重要的东西。

如果有疑问，请尝试不同的类型，直到找到质量和文件大小之间的最佳折衷。

大图像应该调整大小以降低分辨率。入门级智能手机相机可以拍摄几兆字节的高分辨率照片，但在当今最好的屏幕上，你很少需要超过 2000 像素宽的图像。

下一步:确保你的图片是最佳尺寸。很少有图形包会删除所有可能的数据，大多数会保留不必要的颜色或 EXIF 元数据，如日期、位置和相机设置。一次性压缩任务可以使用在线工具完成，例如 [TinyPNG/JPE](https://tinypng.com/) 或 [smush.it](http://www.imgopt.com/) 。可安装的处理工具如 [OptiPNG](http://optipng.sourceforge.net/) 、 [PNGOUT](http://advsys.net/ken/utils.htm) 、 [jpegtran](http://jpegclub.org/jpegtran/) 和 [jpegoptim](http://jpegoptim.sourceforge.net/) 可以批量压缩图像。您可以将压缩系统如 [imagemin](https://www.npmjs.com/package/gulp-imagemin) 引入您的构建过程，或者 CMS 用户可以选择如 [WP Smush](https://wordpress.org/plugins/wp-smushit/) 自动压缩上传的文件。

较小的图像可以合并成一个图像精灵，以减少 HTTP 请求的数量。这在 [HTTP/2](https://http2.github.io/) 中优势较小，但是每个页面上使用的图标仍然可以从合并中受益。

最后，考虑将 Base64 编码的内联数据 URIs 用于较小的常用图像，例如

```
 .bullet {
    background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQAQMAAAAlPW0iAAAABlBMVEUAAAD///+l2Z/dAAAAM0lEQVR4nGP4/5/h/1+G/58ZDrAz3D/McH8yw83NDDeNGe4Ug9C9zwz3gVLMDA/A6P9/AFGGFyjOXZtQAAAAAElFTkSuQmCC");
} 
```

这减少了请求的数量，尽管维护可能更加困难。在线工具如[DataURL.net](http://dataurl.net/)和[数据:URI 生成器](http://dopiaza.org/tools/datauri/)可以进行一次性转换。你的编辑器/IDE 可能也有编码器插件，但最简单的解决方案是[postscs 资产](https://github.com/assetsjs/postcss-assets#inlining-files)—[postscs 插件](https://github.com/postcss)——它可以神奇地转换任何图像，例如

```
 .inline {
    background-image: inline('image.png');
} 
```

## 连接和缩小 CSS 和 JavaScript

平均每个页面加载 360KB 分布在 22 个独立文件中的 JavaScript 和 76KB 分布在 8 个样式表中的 CSS。其中一些可能由第三方社交媒体或广告插件提供，但您自己的文件可能是:

1.  连接，将所有代码合并到一个文件中，以减少 HTTP 请求，以及
2.  缩小以删除不必要的注释和空白。更极端的工具可以将变量和函数重命名为更短的替代名称，例如`launchWidget()`变成了`w()`。

对于更简单的图形界面，像[考拉](http://koala-app.com/)这样的工具提供了 CSS 和 JavaScript 压缩选项。

诸如 [Sass](http://sass-lang.com/) 、 [LESS](https://lesscss.org/) 和 [Stylus](http://learnboost.github.io/stylus/) 等预处理器或包括 [cssnano for PostCSS](http://cssnano.co/) 在内的构建工具可以在您做出更改时优化 CSS。

JavaScript 可能会稍微难一点，因为源代码顺序很关键。你可以考虑像 [Browserify](http://browserify.org/) 这样的系统来捆绑依赖关系。我经常使用更简单的 Gulp 插件，包括:

*   [gulp-deporder](https://www.npmjs.com/package/gulp-deporder) 控制依赖关系。每个源文件顶部的可选注释决定了合适的顺序，例如// requires: config.js，lib.js
*   [gulp-concat](https://www.npmjs.com/package/gulp-concat) 连接成一个文件
*   [gulp-strip-debug](https://www.npmjs.com/package/gulp-strip-debug) 删除控制台和调试器语句
*   变小。

如果您在每个页面上使用不同的 CSS 和 JavaScript 资源，HTTP/2 可能会使这些技术变得多余，但是对于变化不太频繁的文件，仍然应该考虑使用它。

## 删除不必要的代码

添加代码块比删除代码块更容易，但是我怀疑你可以从大多数网站上删除一半的代码而不会产生明显的差别。罪魁祸首:

### CMS 主题和模板

大多数主题都是通用的，以吸引广泛的用户。您可能只使用了一小部分功能，所以请检查是否有更轻量级的替代功能可用。

### CMS 插件

除非绝对必要，否则避免使用插件。一般来说，前端插件，如传送带或小部件是最糟糕的罪犯，因为它们通常包含单独的 CSS 和 JavaScript 集。

### CSS 框架

Bootstrap 之类的框架包含了一系列风格——其中大部分你都不会用到。像 [UnCSS](https://github.com/giakki/uncss) 这样的工具可以分析你的页面，识别不必要的规则。

### JavaScript 框架

避免使用一个以上的框架，尽可能考虑较小的或模块化的选项。你也许能够[完全抛弃你的框架](https://www.sitepoint.com/do-you-really-need-jquery/)。

### JavaScript 效果

滑动和淡出等简单效果可以使用轻量级 CSS3 动画和转换来实现，而不是使用效率较低的 JavaScript。

### 社交媒体小工具

那个无关痛痒的按钮可以携带半兆字节的隐藏代码。它们很少是必需的—[链接就是你所需要的一切](https://www.sitepoint.com/social-media-button-links/)。

### 广告

如果你注册了 57 个广告网络，每个网络都会提供自己的代码块，这会降低你网站的速度。评估你的收入，放弃利润较低的产品。

### 进一步优化

上述选项将使你的网站速度有明显的不同，而不需要费时的改变。使用上面的工具重新运行你的页面，验证节省的空间，检查使用率和转化率是否增加。

*为了更彻底的优化…*

### 采用构建过程

当这是一项手工任务时，最认真的开发人员会忘记压缩图像或连接 CSS。构建过程可以在运行中自动完成单调乏味的任务；每当你做出改变时，当 JavaScript 为你完成时，它很容易被缩小。

两个最受欢迎的选项是[吞咽](http://gulpjs.com/)和[咕噜](http://gruntjs.com/)，但是对于大多数语言和系统都有解决方案。初始设置需要一点时间，但改进后的工作流程将会节省大量时间。

### 简化您的设计

许多现代网站和应用程序避免了复杂性，以提供一种简化的、以客户为中心的体验。简化可能很难，但重新开始项目通常更容易。质疑所有功能请求，或者要求删除您添加的每个功能。

### 考虑一个静态站点

对于大多数网站来说，CMS 是多余的。很少有人一周收到两次以上的更新，然而 CMS 仍然在后台运行，生成很少改变的页面内容。另一种选择是[静态站点发生器](https://www.sitepoint.com/7-reasons-use-static-site-generator/)。这些提供了许多 CMS 的好处，如模板，但生成了一系列具有更好的性能、安全性和可靠性的平面 HTML 文件。

### 改变你的发展生活方式

当内容很少超过几百个单词时，2.3MB 的页面就没什么可辩解的了。当你以最小的成本快速开发一个网站时，很容易忽略性能。但是当结果是一个没人想用的又慢又笨重的产品时，还有什么意义呢？

从一开始就计划绩效。你的客户可能不会立即意识到这些好处，但更高的排名和更高的参与度将有助于你的项目脱颖而出。考虑:

*   限制了你的连接。在手机信号差的地区或拥挤的公共网络上访问您的网站。每天都有成千上万的用户体验到你的挫败感。
*   评估每个项目的权重，并询问添加到页面的每个资产。

网页肥胖是一种流行病，但显然很少有开发者关心。创建简洁的页面，学习有价值的技能，帮助你留住和赢得新客户。
延伸阅读:

*   [如何加速你的网站](https://hostingfacts.com/how-to-speed-up-your-website/)
*   [减轻页面重量的完整指南](https://www.sitepoint.com/complete-guide-reducing-page-weight/)

## 分享这篇文章