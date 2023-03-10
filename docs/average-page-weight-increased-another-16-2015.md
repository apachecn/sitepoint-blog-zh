# 平均页面权重在 2015 年又增加了 16%

> 原文：<https://www.sitepoint.com/average-page-weight-increased-another-16-2015/>

又发生了。整理了 50 万个最受欢迎网站的技术信息的 HTTP 档案报告称，2015 年平均页面重量增加了 16%，达到 2,262KB。2014 年观察到[类似的增长。](https://www.sitepoint.com/average-page-weight-increases-15-2014/)

该报告分析了可公开访问的内容和购物网站。它不能分析 web 应用程序或登录后的页面，如脸书。

| 技术 | 比 2014 年(KB) | 2015 年以后(KB) | 增加(%) |
| 超文本标记语言 | Fifty-nine | Sixty-six | 12% |
| 半铸钢ˌ钢性铸铁(Cast Semi-Steel) | Fifty-seven | Seventy-six | 33% |
| Java Script 语言 | Two hundred and ninety-five | Three hundred and sixty-three | 23% |
| 形象 | One thousand two hundred and forty-three | One thousand four hundred and forty-three | 16% |
| 闪光 | Seventy-six | Fifty-three | -30% |
| 其他的 | Two hundred and twenty-three | Two hundred and sixty-one | 17% |
| 总数 | One thousand nine hundred and fifty-three | Two thousand two hundred and sixty-two | 16% |

数字是平均值。页面权重不太可能遵循正态分布，但当你仔细分析网页时，这些数字似乎是合理的。

HTML 内容增加了 7KB。文章平均长 12%吗？我表示怀疑。

不出所料，Flash 也下降了 23KB 或 30%。五分之一的站点继续使用闪存—一年来下降了 26%。

CSS 增加了 19KB。几乎没有理由使用八个样式表，但总体文件大小的增加似乎是合理的，因为:

1.  CSS 功能会随着时间的推移而增加。我们使用的效果、动画和响应式布局在几年前是不可能的(它们是否有必要是另一回事)。
2.  像 Sass、LESS 和 Stylus 这样的预处理器倾向于批量编码，因为嵌套和属性重用很容易。
3.  构建工具使内联图像资源变得更加容易。

CSS Flexbox 等特性有助于减少更复杂的基于浮动的布局，但节省的空间非常小。

您可能认为 JavaScript 代码会相应减少，但是它增加了 68KB，达到 363KB，分布在 22 个独立的文件中。代码太多了。有些是框架和库，但我怀疑大多数是社交媒体插件和广告。

其他文件，如字体和视频，增加了 38KB 或 17%。

像往常一样，最大的增长来自图像。我们正在加载额外的 200KB，占整体增长的 65%。每页要访问 55 个单独的图像文件，这似乎有点多。

其他~~高光~~低光:

*   25%的站点不使用 GZIP 压缩
*   发出了 101 个 HTTP 文件请求，而一年前只有 95 个
*   页面包含 896 个 DOM 元素，而不是 862 个
*   资源从 18 个域加载
*   49%的资产是可缓存的
*   52%的网页使用谷歌图书馆，如分析
*   24%的网页现在使用 HTTPS
*   36%的页面包含 4xx 或 5xx HTTP 错误的资产
*   79%的页面使用重定向

## 为什么页面会变得臃肿？

对于 2.2MB 的页面有一个简单的解释:*我们正在做一件糟糕的工作*。

作为一名开发人员，我热爱网络。作为一个用户，往往很糟糕。网站迫切希望通过侵入式广告、恼人的弹出窗口、未充分利用的社交媒体欺诈和侵入式跟踪来“增加参与度”。也许这导致了暂时的收入增加，但是增加的膨胀会适得其反:

*   谷歌降级重量级网站，这可能会损害搜索引擎优化的努力。
*   广告宣称保持内容免费。当用户发现在一个典型的移动数据套餐中浏览一个页面需要花费 0.40 美元时，他们会认为这是免费的吗？
*   在这一年中，广告拦截器成为主流意识，这凸显了用户的挫折感，以及任何人都可以轻易取消令人不快的内容。
*   用户不会等待。[Etsy.com](https://www.etsy.com/)发现 160KB 的额外图片使它们在移动设备上的反弹率增加了 12%。
*   网络活动开始引起政府的关注。例如，如果使用英国移动运营商网络的服务从误导性活动中获利，英国移动运营商可能会被罚款。随着网站变得更加绝望，监管将不可避免地升级。

内容被淹没在 cruft 中。未经压缩，莎士比亚的 37 个剧本总计超过 80 万字或 5MB 下载。现在考虑脸书的即时文章概述，它提出了臃肿页面的替代方案。它包含五个段落，然而讽刺的是，当你观看三分钟的视频时，需要 3.5MB 的带宽外加 50MB。它传达的信息比莎士比亚作品的总和还多吗？也许它更漂亮，但是有必要展示一个 267KB 的男人抱着一只看不见的雪貂的图像吗？

## 否认肥胖

我已经发表了几篇这样的文章。许多人声称没有肥胖危机，所以让我们来解决主要的争论。

有些页面总是很大
图片库、游戏、技术展示等。永远是矮胖的。然而，每个开发者都可以证明他们的页面的重要性。HTTP 存档报告主要分析内容文章和在线商店。每页 2.2MB 相当于半部莎士比亚的戏剧——太荒谬了。

**页面权重并不代表质量**
根据我的经验，页面权重与质量成反比。笨重的页面通常是链接诱饵文章或无意义的营销狂言。当然也有例外，但是如果有一种工具可以对内容进行评级并与膨胀进行比较，那不是很好吗？

用户从不抱怨页面过重
……因为他们抛弃了它们。分析记录那些*成功*访问网站的人。他们不会突出显示那些无法加载肥胖页面或再也不会返回的页面。

**带宽容量每年增加—页面权重被否定**
2015 年你所有网络的带宽增加了 16%吗？而[2014 年的 15%](https://www.sitepoint.com/average-page-weight-increases-15-2014/)？而[2013 年的 32%](https://www.sitepoint.com/average-page-weights-increase-32-2013/)？而 2012 年的[30%](https://www.sitepoint.com/web-page-weight-2012/)？

即使有，也不代表每个人都有同样的经历。智能手机的接入正在爆炸式增长，然而在世界许多地方，移动网络仍然很慢。

即使我们假设每个地方的连接都很好，开发者有责任使用增加的容量吗？页面是否比去年提高了 16%?

**瘦身网页意味着变笨，功能和效果更少**
为什么？在某些情况下，[网站只需几分钟的努力就能节省大量资金](https://www.sitepoint.com/complete-guide-reducing-page-weight/)。激活压缩和连接资产不会改变任何东西，除了性能。

对性能的痴迷导致更多的复杂性和维护
删除不使用或不必要的图片、资产、功能和小工具可以简化网站。这会减少并发症和维护。没有人建议你应该为每一个字节烦恼，但是照顾好千字节，兆字节会自己照顾好自己。

**额外的页面权重是进步的代价**
在某些边缘情况下，或许如此。[光剑逃生](https://lightsaber.withgoogle.com/)有 120MB 的有效载荷，因为它是一个革命性的和实验性的基于浏览器的游戏(HTTP 档案不分析)。苹果的 11.2 兆 iPhone 6S 页面也是如此吗？它提供了几个段落和效果，而这些(大部分)在十年前可能只有它的一小部分大小。

**SitePoint 的页面经常超过 2MB**
咳咳。哦，看——*一只松鼠……*

SitePoint 页面在桌面设备上可能会很大，尽管当通过较慢的网络在小屏幕上查看时，它们会减少到几百千字节。没有一个网站是完美的，性能方面的努力正在进行，但我同意它可以改进。

我为什么要烦恼？其他人很少做
原因:*没什么不好的*。您的用户将从更具响应性的页面和更流畅的体验中受益。你的搜索引擎排名提高。你的转化率增加。你的托管费用下降。你甚至在减少电力消耗，为拯救地球尽自己的一份力量。这需要一点额外的努力，但是最大的优化来自于最简单的改变——参见[减轻页面重量的完整指南](https://www.sitepoint.com/complete-guide-reducing-page-weight/)。

## 无意识肥胖

开发商和网站所有者无意识地制造了肥胖流行病，但是通过解决以下问题可以节省大量成本:

1.  图片和视频。英雄有必要吗？文件可以调整大小或缩小吗？能不能换成 CSS 特效？内容管理者上传的是单一文件吗？会有用户看到全部 55 张图片吗？
2.  第三方内容。广告和社交媒体脚本可能会浪费得离谱。它们有必要吗？有没有[更好的选择](https://www.sitepoint.com/social-media-button-links/)？
3.  态度。性能很少涉及那些坐在 100 兆连接。尝试节流带宽，通过 3G 连接或使用酒店 Wi-Fi 一段时间——这可能会改变看法。

增重远比减肥容易。添加一些额外的小部件或图形似乎是合理的，但它很快就会增加。回去清除多余的垃圾可能是乏味和困难的。答案似乎很明显:*首先不要加*。

很少有网站所有者关心膨胀。性能是开发人员的心态。从项目一开始就考虑它，它不会显著增加开发时间。当你的页面达到肥胖水平后再解决性能问题就太晚了。

## 分享这篇文章