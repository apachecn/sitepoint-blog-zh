# 是时候重新考虑 CSS 中的供应商前缀了

> 原文：<https://www.sitepoint.com/its-time-to-rethink-vendor-prefixes-in-css/>

前缀`-webkit-`现在在网络上的 CSS 中占主导地位，以至于一些网站没有它就无法正常工作。虽然这显然是过去几年来开发人员不太理想的实践的标志，但它导致了 Mozilla 相当不幸但几乎是必要的举措。通过 Firefox 46 或 47(2016 年 4 月或 5 月发布)，Mozilla 计划实现对[一系列非标准 webkit 前缀](https://bugzilla.mozilla.org/show_bug.cgi?id=1170789)的支持，以提高 Firefox 与带`-webkit`前缀(通常是移动)的网络的兼容性。

这并不是一个新想法，[微软 Edge 也支持一系列-webkit-前缀](https://msdn.microsoft.com/en-us/library/mt270097%28v=vs.85%29.aspx)以确保兼容性。 [Opera 已经在 2012 年开始实施-webkit-prefixes](https://www.sitepoint.com/opera-css3-webkit-prefix/)，并且已经转移到基于 WebKit 的 Blink 引擎。

W3C 和浏览器供应商并不打算在生产网站上使用供应商前缀:

> “W3C 的官方政策规定，你不应该在产品代码中使用实验性的属性，但是人们却这样做了，因为他们想让网站看起来很酷，并保持领先。”–[针对不同浏览器优化内容的 W3C 页面](https://www.w3.org/community/webed/wiki/Optimizing_content_for_different_browsers:_the_RIGHT_way)

然而，各地的开发人员都想尽快使用它们来访问最新的特性。虽然前缀对 WebKit 的统治地位造成了一点混乱，但我认为它们成功地帮助 web 快速向前发展。

Mozilla 和微软的做法可能对大多数网站无害。大多数在线网站已经包含了`-moz-`前缀，或者会发现 Mozilla 的更新无需任何操作就能提高网站的兼容性。作为专业的 web 开发人员，我们确实需要彻底地了解一些设计中可能出现的异常结果。你可能已经知道你的哪些作品会因为这次更新而破裂。

开发者们，现在显然是时候重新考虑你们使用前缀的方法，并测试那些网站了。

## 涉及的前缀

Mozilla 可能会考虑加入一系列的前缀。从我收集的信息来看，Mozilla 并不打算匹配 Edge 支持的`-webkit-`前缀列表，因为并非所有这些都是确保 Mozilla Gecko 布局引擎与更广泛的网络兼容所必需的。

Mozilla 可能采用的前缀根据[他们关于兼容性/移动/非标准兼容性的 wiki 页面](https://wiki.mozilla.org/Compatibility/Mobile/Non_Standard_Compatibility)包括以下内容:

*   `-webkit-flexbox`
*   `-webkit-`前缀渐变
*   `-webkit-transforms`
*   `-webkit-transitions`
*   `-webkit-appearance`
*   `-webkit-background-clip`
*   `-webkit-device-pixel-ratio`
*   `-webkit-animation`
*   `-webkit-border*`

其他一些功能也可能受到影响，如`@-webkit-keyframes`。

## 跨浏览器测试将是至关重要的

如果你是一名 web 开发人员，为了避免在 Firefox 上测试新的 CSS 特性而省略了前缀(让我们假设你的截止日期很紧，客户强迫你这么做)，你需要在 Firefox 46 或 47 中重新测试这个站点。这些版本的 Firefox 将在 4 月或 5 月发布，所以你有一点时间提前计划。

为了在这些变化到达 Firefox 46/47 之前测试您的网站，您可以通过`about:config`中的`layout.css.prefixes.webkit`偏好设置在每晚访问 Firefox 中变化的当前状态。如果您安装了最新的夜间版本，它应该默认设置为`true`。并不是所有的`-webkit-`前缀变化都在 Firefox Nightly 中出现，但是这是测试你的网站外观的地方。我会等到大约三月份再对 Firefox 进行彻底的夜间测试。

更紧急的是，微软 Edge 已经在以这种方式解释和显示`-webkit-`前缀。这意味着您的站点中的任何 WebKit 特定的样式可能已经显示在您不期望的浏览器中了。如果您还没有这样做，请在 Windows 10 中访问 Microsoft Edge 并测试这些网站！

## 供应商前缀正在消失

幸运的是，随着浏览器团队寻找更好的解决方案，供应商前缀似乎正在消失。Chrome/Blink 团队已经调整了他们的方法:

> “接下来，我们将保留`about:flags`中“启用实验性 web 平台功能”标志后面的(无前缀)功能，而不是默认启用带有供应商前缀的功能，直到该功能可以默认启用为止。”–[Chrome/Blink 团队](https://www.chromium.org/blink#vendor-prefixes)

Firefox 团队也在采用类似的方法:

> “值得一提的是，Mozilla 内部目前的趋势是[……]避免使用供应商前缀，要么在发布前关闭，要么在足够稳定的情况下发布。至少
> 作为总方针；具体情况可能值得例外。”–[来自 Mozilla 的 Boris】](http://lists.w3.org/Archives/Public/public-webapps/2012OctDec/0731.html)

Microsoft Edge 旨在完全移除对前缀的支持:

> “微软也在去除 Edge 的厂商前缀。这意味着，为了让开发人员利用特殊的 HTML5 或 CSS 特性，他们不必使用特定的 Edge 前缀。相反，他们可以根据网络标准进行编码。”–[Mashable](http://mashable.com/2015/05/11/microsoft-edge-security/#PffnkmWWnEqo)

## 前缀不再造成优雅的退化

这种远离厂商前缀的趋势意味着一件事——通过厂商前缀进行适度的降级显然是不可取的。

使用厂商前缀来定位特定的浏览器(例如，专门针对 Chrome 的东西)并不是厂商前缀的意图；建议开发者使用所有可用的厂商前缀(从`-webkit-`到`-o-`)。如果您正在使用依赖于前缀属性的特性，并且您使用前缀在其他浏览器上优雅地降低了您的设计，这将不再起作用。

## 结论

时代在变。WebKit 的统治地位无意中造成了一个分裂和不兼容的网络，其他浏览器正在寻求通过实现`-webkit-`前缀来扩展它们的兼容性。虽然随着供应商前缀的逐步淘汰，这个问题应该会逐渐消失，但是开发人员需要检查他们对前缀的使用不会在非 WebKit 浏览器中导致意外的结果。

## 有用的链接

*   [Mozilla 发布这些变更文档的意图](https://groups.google.com/forum/?_escaped_fragment_=topic/mozilla.dev.platform/969k-Iryiyo)
*   [Mozilla 在 Bugzilla](https://bugzilla.mozilla.org/show_bug.cgi?id=1170789) 中针对此问题的 Bug 跟踪
*   [微软 Edge 最新版本支持的 WebKit APIs】](https://docs.google.com/spreadsheets/d/173d1p3LkW_LWk-VMnrxGPhTobtKSpED30Fys5ZJLttA/edit#gid=51341101)
*   [关于将-webkit-前缀带入网络生活标准的文档](https://compat.spec.whatwg.org/)
*   [注册覆盖的火狐 WebKit 兼容性变化](http://www.theregister.co.uk/2016/01/04/firefox_webkit_css_support/)

## 分享这篇文章