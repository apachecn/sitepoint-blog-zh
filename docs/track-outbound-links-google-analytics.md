# 如何在谷歌分析中跟踪出站链接

> 原文：<https://www.sitepoint.com/track-outbound-links-google-analytics/>

谷歌分析提供了大量的信息。如果你什么都不做，只是把跟踪脚本添加到你的页面上，你将会面对关于你的站点上的用户活动的无止境的数据和报告。然而，虽然分析显示退出页面，它不会告诉你用户点击了哪些链接离开你的网站。在这篇文章中，我们将发现如何添加出站链接跟踪。

## 谷歌会记录出站链接吗？

大概吧。如果你从一个使用分析的网站链接到另一个使用分析的网站，谷歌*可以*记录这种关系。不幸的是，如果一个或多个出站网站不使用分析，报告会产生误导。

谷歌有额外的收集数据的手段:当你拥有顶级浏览器和搜索引擎时，你可以收集大量的统计数据！但我们正从现场分析转向更可疑的领域；谷歌不一定想分享这些数据。

幸运的是，我们可以自己收集出站链接的详细信息。

## 先升级到万能分析！

在我们继续之前，你必须[升级到通用分析](/upgrading-universal-analytics-guide/)。谷歌可能已经为你开始了这个过程，但跟踪代码必须在你的网页上更新。这很痛苦，但是下面显示的出站链接跟踪代码没有它就无法工作。*(它可以与传统的分析系统一起工作，但它最终会停止工作，所以最好现在升级。)*

## 自定义事件跟踪

分析支持[事件跟踪](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)。通常，它用于记录页面上 JavaScript 控制的交互性，比如打开一个小部件或进行 Ajax 调用。我们可以使用事件跟踪来记录出站链接，但有一些障碍需要克服:

*   该事件必须在所有浏览器上记录，并且不妨碍导航
*   我们不应该需要手动识别或附加单独的处理程序到每个出站链接，并且
*   我们必须确保在出站页面开始加载之前记录该事件。

解决方案是…

1.  我们将为`body`元素附加一个点击事件处理程序。这将接收一个点击链接事件，因为它们在 DOM 中冒泡。
2.  我们可以检测一个链接是否会打开一个与我们的域名不同的网页。如果是出站链接，我们将取消点击事件并启动分析事件跟踪。
3.  在后台，分析通过请求图像信标来发送数据。一旦调用完成，它可以运行一个回调函数，这样我们就可以重定向到出站页面。
4.  我们需要小心，确保跟踪永远不会停止用户导航，即使失败。该过程必须快速，不处理已被其他进程停用的点击，并确保链接工作，即使分析事件失败。

我们希望跟踪在任何地方都能工作，所以我推荐使用一个具有健壮的跨浏览器事件处理的库。在这个例子中，我将使用 jQuery 1.x，因为大多数网站都使用它，但是你可以用一个轻量级的选项来代替，比如 [min.js](https://github.com/remy/min.js) 、 [Zepto.js](http://zeptojs.com/) 、 [Minified.js](http://minifiedjs.com/) 或者你自己的事件处理函数。

完整的代码如下所示。这可以添加到现有的 JavaScript 文件中或者添加到一个`script`块中，只要它被加载到 HTML `body`中的某个地方(理想情况下，就在结束标记之前)。尽管 Google Analytics 跟踪代码可以出现在页面上的任何地方，但是必须首先加载 jQuery(或您的替代方案)。

```
/* Track outbound links in Google Analytics */
(function($) {

  "use strict";

  // current page host
  var baseURI = window.location.host;

  // click event on body
  $("body").on("click", function(e) {

    // abandon if link already aborted or analytics is not available
    if (e.isDefaultPrevented() || typeof ga !== "function") return;

    // abandon if no active link or link within domain
    var link = $(e.target).closest("a");
    if (link.length != 1 || baseURI == link[0].host) return;

    // cancel event and record outbound link
    e.preventDefault();
    var href = link[0].href;
    ga('send', {
      'hitType': 'event',
      'eventCategory': 'outbound',
      'eventAction': 'link',
      'eventLabel': href,
      'hitCallback': loadPage
    });

    // redirect after one second if recording takes too long
    setTimeout(loadPage, 1000);

    // redirect to outbound page
    function loadPage() {
      document.location = href;
    }

  });

})(jQuery); // pass another library here if required
```

记录该事件时，类别名称为“出站”，操作名称为“链接”，值设置为出站页面的 URL。如果需要，您可以在`ga`调用中修改这些内容(第 24 到 26 行)。

一旦实现，访问您的网站，点击几个出站链接。您应该在 Analytics Real-Time > Events 面板中看到活动。几小时后,“行为”>“事件”面板中将会显示更多数据。

请随意使用代码。

## 分享这篇文章