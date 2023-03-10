# mose curu 如何使用微格式来提高可用性

> 原文：<https://www.sitepoint.com/how-mosembro-uses-microformats-to-improve-usability/>

微格式(以及一般的语义标记)的两个广为称赞的好处是数据可移植性和代码重用。第三个好处，也许没有被广泛接受，是可用性。当然，有了数据可移植性和更好的代码可重用性，应用程序中的用户体验可以得到改善，但从前者到后者的转变并不是自动的，这并不奇怪。

任何“可行”的东西都必须以这种方式*制造*，而整合的机会并不总是显而易见的。这就是为什么当我了解到为谷歌的 Android 平台构建的 web 浏览器 mose cum 时，我感到很兴奋，它的目标是利用语义标记(这里是微格式)来提高移动 web 浏览的可用性。它的名字是“移动语义浏览器”的缩写，它的理论很简单:语义标记意味着更丰富的数据，意味着更强的自动化能力，最终意味着更强的可用性。

当然，已经有一些软件在一些有趣的地方采用了浏览器内语义标记支持。对于 Firefox 来说，有一个优秀的[操作符扩展](http://addons.mozilla.org/firefox/4106)，可以让你做一些熟悉的事情，比如将 [hCard](http://microformats.org/wiki/hcard) 数据导出到你的地址簿，或者在其他热门网站上轻松找到与你正在浏览的页面相关的内容。对于 Safari 来说，有一个 [Safari 微格式插件](http://zappatic.net/safarimicroformats/)，它做了这些相同事情的一个子集。

那么 mose cum 的特别之处在哪里呢？

## 手机内置的微格式

mose cur 也可以做这些事情，但这是我第一次看到这些功能被直接集成到网络浏览器的代码库中，而且是移动浏览器。正如 mose cum 的开发者 Aleksander Kmetec 在介绍该浏览器的文章中所解释的那样，这些功能在移动环境中比在桌面上更重要。

> [mose cum]使用户能够快速执行任务，如在地图上查找地址或在日历中添加活动。虽然在计算机上手动执行这些任务非常简单，但在没有合适的键盘、鼠标，甚至没有复制和粘贴文本功能的移动设备上，这可能会非常繁琐。

Aleksander 甚至在 mose cum 的主页上提供了这个挑战的视频[来说明他的观点。该视频展示了使用 mose cur 在地图上查找位置(使用](http://lexandera.com/mosembro/) [adr](http://microformats.org/wiki/adr "Learn more about the adr microformat.") 或 [geo](http://microformats.org/wiki/geo "Learn more about the geo microformat.") 标记)与使用谷歌 Android 内置浏览器的定时比较。使用 mose cur 你可以达到 5 秒，而使用默认的 Android 浏览器你将很难保持在 30 秒以下。

![The Mosembro search interface](img/2bf195b67a6d2194fffe6747d5c68304.png)

mose cur 搜索界面

到目前为止，mose cur 提供的最有趣的功能是<q cite="http://lexandera.com/mosembro/">一个用于搜索整个网站的一致搜索对话框</q>。mose cur 能够检测网站自身搜索表单的存在(如果它使用特定的类似微格式的模式进行标记),并可以在浏览器的 chrome 中呈现自己的 UI 来与该表单交互。(mose cur 使用的模式可见于其[演示页面](http://lexandera.com/mosembrodemo/ "The Mosembro demo page leverages all of Mosembro's features.")。)

当网站的搜索表单在第一次加载页面时无法立即看到时，这样做的好处很快就会显现出来，这在用移动设备浏览网页时并不少见。此外，由于许多网站的搜索形式在位置和外观上因网站而异(甚至在单个网站的重新设计之间)，为被查看的特定网站提供一致的搜索 UI *通常会很有帮助。然而，最重要的是，这是一个网站和用户代理之间的共享语义如何被用来实现创新功能的显著例子。*

## *语义*标记是一个*平等主义* API

亚历山大·克迈特克(Aleksander Kmetec)坚定地表示，mose cum 只是一个概念验证应用程序，但将 mose cum 与苹果的 Mobile Safari(iPhone 和 iPod touch 设备用户唯一可用的浏览器)进行比较并不为过。两种手机浏览器都把可用性作为首要特性，并且都有一些相似的硬编码行为。

例如，当且仅当使用的服务是谷歌地图时，移动 Safari 的用户才会识别出地图行为*。链接到`http://maps.google.com/maps`会触发 Mobile Safari 将查询字符串传递给内置的地图应用程序。不幸的是，指向其他服务的链接，例如`http://maps.yahoo.com/`，没有得到类似的待遇。*

虽然我对苹果公司对谷歌地图链接的“增强”处理没有任何商业上的反对。地图，我真希望我的工具不要*在技术上*关心我喜欢什么地图服务。(谷歌和苹果密切合作已经不是什么秘密，2006 年，谷歌首席执行官 Eric Schmidt 被选入苹果董事会。)

虽然 mose cum 对位置标记的处理也可以通过 Google Maps 进行映射，但是我可以很容易地想象它是通过 Yahoo！的 API。关键是在标记层的*，位置数据本身应该是独立于厂商的，而`http://maps.google.com/maps`当然不是。这是数据可移植性和定制用户体验蓬勃发展所必需的抽象。*

Mobile Safari 提供了许多特殊用途的 URL 方案，允许它用特殊的标记做特殊的事情。一个例子是`tel`方案，它激活对 URL 中指定号码的电话呼叫。看到 mose cum 后受到启发，不禁希望 Mobile Safari 除了使用`tel`方案外，还能使用一个`hCard`的[属性`tel`来打电话。](http://microformats.org/wiki/hcard#Property_List)

今天，您可以使用带有`hCard`和`tel:`链接的 HTML 标记来实现类似的结果:

```
<p class="vcard">
    <span class="fn">Meitar Moscovitz</span>'s <span class="tel"><span class="type">pref</span>erred number is <a href="tel:555-1234" class="value">555-1234</a></span>.
</p> 
```

然而，现在你必须警惕不支持`tel`方案的用户代理将如何表现。您可以在整个站点的 CSS 样式表中隐藏这些链接，可能如下所示:

```
a[href^="tel:"] { display: none; }
```

然后在特定的样式表中为 Mobile Safari 重新启用它们，但这很繁琐，而且是特定于供应商的。此外，您可能希望将电话号码链接到其他合适的位置，例如在线电话目录中的条目。显然，一个更加集成的解决方案，其中浏览器本身理解语义标记，将是更可取的。

最终，mose cum 的概念验证令人兴奋，不是因为它的实现是革命性的，而是因为它有助于铺平道路，使前端 web 开发人员能够超越网页，开始与其他地方存在的硬件和软件的其他功能进行互操作。一致的站点范围的搜索 ui、集成的地图和电话只是在网页和用户代理之间共享语义的几种可能性。你有什么其他的想法来更好地集成用户代理和他们通过网络下载的标记？

## 分享这篇文章