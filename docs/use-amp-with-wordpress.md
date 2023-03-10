# 如何在 WordPress 中使用 AMP

> 原文：<https://www.sitepoint.com/use-amp-with-wordpress/>

*要了解更多有关移动和其他设备尺寸开发的信息，请查看 SitePoint 高级课程[响应提示和技巧](https://www.sitepoint.com/premium/courses/responsive-tips-and-tricks-2976)*

![AMP with WordPress](img/0a05182e5dcf310303d12bf87a366f14.png)

谷歌的加速移动页面(AMP)项目已于 2016 年 2 月 24 日启动。随着这次发布，成千上万的开发人员成为项目的积极参与者。现在无数的网站都有 AMP 版本的页面，很多开发者都在学习使用 AMP——在这种情况下，学习用 WordPress 使用 AMP。

谷歌非常重视 AMP。这也是他们的搜索引擎排名标准之一。通过这种方式，谷歌使 AMP 成为许多网站的必需品。在这篇文章中，我将向你详细介绍谷歌的加速移动页面项目。这包括在你的 WordPress 站点中调用它的步骤。

## AMP 是什么？

一些开发人员可能还不太熟悉 AMP。这是一个移动友好的框架，能够在移动浏览器上快速加载您的网页。它是一种开源技术，旨在使网站发布者能够有效地提高在移动设备上加载内容页面的速度和用户体验。并且所有这些增强对广告收入没有任何影响。

如果您是一名经验丰富的开发人员，您可以通过全面的页面加载优化实现类似的增强。然而，加速的移动页面使得这些优化非常容易执行，而不需要在移动布局上花费太多的时间和精力。

对于已经为 SEO 排名加倍努力的网站来说，这只是给他们的待办事项列表增加了更多的任务，当然，因为 AMP 页面也可以提高你网站的 SEO 排名。这也许是大企业也采用 AMP 的主要原因。

## AMP 项目

AMP 由三个主要部件组成:

*   AMP HTML
*   放大器 JS
*   放大器缓存

### AMP HTML

它是 HTML 的子集，有许多限制、自定义标签和自定义属性。如果您已经熟悉 HTML，适应这一点并不复杂。然而，如果你发现你有任何困难，我建议你访问这个链接，了解更多关于如何[创建你的 AMP HTML 页面](https://www.ampproject.org/docs/tutorials/create/basic_markup)。

### 放大器 JS

AMP 为移动网页提供了数量有限的 JavaScript。现在，关于 AMP 中的 JS，需要记住的一件重要事情是，AMP 不允许使用第三方 JavaScript。

### 放大器缓存

Google AMP 缓存是一个用于传送 AMP 页面的 CDN。你们都知道内容交付网络的核心功能——它们将资源负载分配给离网站观众更近的服务器。对于 AMP 页面，由于可能的距离延迟，该 CDN 将允许最短的加载时间。

## AMP 符号与 SEO 的相关性

回到 2016 年，当谷歌推出 AMP 时，AdAge.com 发表了对谷歌新闻和社交产品高级总监理查德·金格拉斯的采访。在那次采访中，他说 AMP 的使用不会直接与你的搜索排名相关，因为它不是一个直接因素。他接着补充道，“所有其他(搜索引擎排名)信号也需要得到满足。”

然而，经过这样的澄清，一切都变得更加清晰。他说，“如果我们有两篇文章，从信号的角度来看，除了速度之外，其他所有特征都相同，那么是的，我们将重点关注速度，因为这是用户认为有吸引力的。”

甚至 Google 也不否认 AMP 网站对于 SEO 的相关性。速度是搜索引擎优化的一个影响因素。如果一个 AMP 页面由于加载速度更快而获得更多的点击量和更低的跳出率，谷歌肯定会因为更好的用户体验而将网站排名提高。

## 一个人如何能放大自己的网站？

你必须在一个网站上维护两个版本的文章页面。针对默认网络用户的原始文章页面，以及针对潜在移动用户的 AMP 版本。

还要注意，AMP 不允许表单元素和第三方 JavaScript。这意味着您不能在标准实现中放置联系表单或页面上的评论，因为 AMP 主要用于内容的交付。

*   我建议重写整个网站模板来应对这些限制。例如，AMP 页面的 CSS 必须在一行中，并且页面大小小于 50 kb。此外，由于字体的快速加载，它们应该在 [amp-font 扩展](https://www.ampproject.org/docs/reference/components/amp-font)的帮助下加载，以便有效地加载页面。
*   建议多媒体必须特别小心处理。对于图像，你需要利用 [<amp>元素</amp>](https://www.ampproject.org/docs/reference/components/amp-img) 和精确的宽度和高度。另外，如果你的图片是 gif，那么你需要使用单独的 [amp-anim 扩展组件](https://www.ampproject.org/docs/reference/components/amp-anim)。
*   对于视频，有两种选择。嵌入式视频有一个定制标签，叫做 [amp-video](https://www.ampproject.org/docs/reference/components/amp-video) 。然而，如果你想嵌入一个 YouTube 视频，有一个特定的标签叫做 [amp-youtube](https://www.ampproject.org/docs/reference/components/amp-youtube) 。
*   要嵌入幻灯片，您可以使用 [amp-carousel](https://www.ampproject.org/docs/reference/components/amp-carousel) 。除此之外，如果你想添加一个图像灯箱，你可以使用[放大器图像灯箱](https://www.ampproject.org/docs/reference/components/amp-img-lightbox)。
*   对于嵌入社交媒体平台，如 [Twitter](https://www.ampproject.org/docs/reference/components/amp-twitter) 、[脸书](https://www.ampproject.org/docs/reference/components/amp-facebook-comments)、 [Instagram](https://www.ampproject.org/docs/reference/components/amp-instagram) 、 [Pinterest](https://www.ampproject.org/docs/reference/components/amp-pinterest) 和 [Vine](https://www.ampproject.org/docs/reference/components/amp-vine) ，可以使用各自的组件。
*   这一点很重要。现在，一旦一切都准备好了，你就可以进入你的 AMP 页面了，你必须让谷歌知道你的 AMP 网站。你必须在你的主文章页面上添加一个关于你的 AMP 页面信息的标签`<link rel="amphtml" href="http://www.yourblog.com/blog-post/amp/">`，并且在 AMP 页面上添加一个关于主页面信息的规范标签`<link rel="canonical" href="http://www.yourblog.com/blog-post/">`。

你可以在这里了解更多关于 AMP 标签和 AMP [的 Schema.org 元数据。Schema.org 元数据“是使你的内容有资格出现在谷歌搜索新闻传送带的演示中的一个要求”。因此，如果你希望通过 Google AMP 取得成功，你必须把你的模式做好。](https://www.ampproject.org/docs/guides/discovery#use-schema.org-for-most-search-engines)

## 谷歌分析能在 AMP 上工作吗？

是的，当然，分析适用于 AMP。事实上，AMP 上的分析也相当智能。为了防止一个网站由于多个分析跟踪器而变慢，他们致力于“一次测量，多次报告”的核心理念。一般来说，有两种方法可以为您的网站启用 AMP 分析。

*   [amp-pixel 元素](https://www.ampproject.org/docs/reference/components/amp-pixel):这是一个简单的标记，通过使用 GET 请求来计算页面浏览量(类似于跟踪像素)。
*   [分析扩展组件](https://www.ampproject.org/docs/reference/components/amp-analytics):该组件比`amp-pixel`更先进。您可以使用它来测量 AMP 页面上的任何活动。它使您能够指定 JSON 配置，该配置提供了要测量的内容以及将报告发送到哪里的详细信息。

## 如何在 WordPress 网站上使用 AMP

事实上，使用 AMP 最简单的方法之一就是在你的 WordPress 网站上实现它。可以用 automatic/WordPress 开发的官方插件[。](https://wordpress.org/plugins/amp/)

### 步骤一:安装 WordPress 插件

让我们开始安装吧！从上面的链接下载插件并安装在你的 WordPress 网站上。安装完成后，您只需将“/amp/”附加到文章页面。如果你没有启用漂亮的永久链接，你可以试试`?amp=1`。

### 第二步:验证和调整

谷歌搜索控制台从元标签中获取你的文章页面的 AMP 版本，该版本将被插件附加。然而，问题出现了，因为通常需要几天才能检测到这种变化。

那么，现在该怎么办呢？为了处理这些棘手的情况，我建议结合使用 Chrome 验证过程和搜索控制台。为了使用 Chrome 验证流程，请访问您在 Chrome 中的任何 AMP 页面。然后在 URL 的末尾追加`#development=1`。现在点击`Control+Shift+I`打开 Chrome 开发者工具。

刷新页面，要么显示“AMP 验证成功”，要么给出需要修复的问题的详细列表。

你可以看到，仅仅安装一个插件是不够的。您必须通过访问页面并重复上述步骤来验证每个数据，以便从加速的移动页面中受益。

### 第三步:验证模式标记

您已经了解到验证模式标记对于 AMP 页面非常重要。我推荐 Google 的[结构化数据测试工具](https://search.google.com/structured-data/testing-tool/u/0/)来测试页面的有效模式标记。但是，我发现 WordPress 在 logo 的显示上有一些问题。因此，我做了一些修改，以克服这个问题。

转到插件，点击“编辑器”，然后选择“放大器”。在编辑器中修改这些行来修改插件。

```
if ( $site_icon_url ) {
  $metadata['publisher']['logo'] = array(
    '@type' => 'ImageObject',
    'url' => $site_icon_url,
    'height' => self::SITE_ICON_SIZE,
    'width' => self::SITE_ICON_SIZE,
  );
} 
```

收件人:

```
$metadata['publisher']['logo'] = array(
  '@type' => 'ImageObject',
  'url' => 'http://yourdomain.com/wp-content/uploads/logo-company.png',
  'height' => 60,
  'width' => 170,
); 
```

请确保在 URL 中，您指定了您的徽标所在的位置，并相应地指定了高度和宽度。

### 第四步:整合谷歌分析和 AMP WordPress 插件。

现在，您差不多完成了。然而，我更喜欢使用谷歌分析来跟踪统计数据。AMP WordPress 插件没有主动激活`amp-analytics`，但是，它很容易执行。

为了使 AMP WordPress 插件能够与 Google Analytics 协同工作，进入插件->编辑器->选择“AMP”并在它的末尾添加以下代码。

```
add_action( 'amp_post_template_head', 'amp_post_template_add_analytics_js' );
function amp_post_template_add_analytics_js( $amp_template ) {
    $post_id = $amp_template->get( 'post_id' );
    ?>
    <script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>
    <?php
}

add_action( 'amp_post_template_footer', 'xyz_amp_add_analytics' );

function xyz_amp_add_analytics( $amp_template ) {
    $post_id = $amp_template->get( 'post_id' );
    ?>
    <amp-analytics type="googleanalytics" id="analytics1">
    <script type="application/json"> {
      "vars": {
        "account": "UA-XXXXX-Y"  ←(YOUR GOOGLE ANALYTICS PROPERTY ID)
      },
      "triggers": {
        "trackPageview": {
          "on": "visible",
          "request": "pageview"
        }
      }
    } </script>
    </amp-analytics>
    <?php
} 
```

*确保将值`UA-XXXXX-Y`更改为您自己的 Google Analytics 属性 ID！*

进行此更改后，重新验证您的 AMP 页面，然后您的 AMP 页面将可跟踪。

## 对谷歌 AMP 项目的裁决

谷歌希望 AMP 项目能给用户带来良好的体验。然而，问题是 AMP 是否能让一切都变得超级快。这个问题的答案是相当开放的。如果你的网站优化得不好，那么 AMP 将会带来一些显著的提升。

然而，加速移动页面并不是灵丹妙药。提高网站速度的技术从一开始就已经存在。AMP 只是试图结合和消除所有主要的缓慢加载因素，并为用户提供一个更好的解决方案。

你对 AMP 项目有什么看法？请在下面的评论区分享你的观点！

## 分享这篇文章