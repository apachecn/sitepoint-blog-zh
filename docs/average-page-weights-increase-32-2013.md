# 2013 年，平均页面权重增加了 32%

> 原文：<https://www.sitepoint.com/average-page-weights-increase-32-2013/>

HTTP 存档报告发布了他们的年终技术统计数据，这些数据整理了来自 30 万个最受欢迎网站的信息。平均页面权重在一年内膨胀了 32%,达到 1,700Kb 或 1.7Mb，现在包含 96 个单独的 HTTP 请求。比 2012 年 [30%的惊人涨幅](/web-page-weight-2012/)还要惨！

这种增长的部分原因是，随着人们寻找礼物，电子商务活动和广告有所增加。然而，很少有网站在一月份减肥，并在全年继续大吃大喝。

该报告分析了公众可访问的内容和购物网站，而不是复杂的网络应用程序。它提供了所使用的具体技术的分类:

| 技术 | 2012 年底 | 2013 年底 | 提高 |
| 超文本标记语言 | 54Kb | 57Kb | +6% |
| 半铸钢ˌ钢性铸铁(Cast Semi-Steel) | 35Kb | 46Kb | +31% |
| Java Script 语言 | 211Kb | 276Kb | +31% |
| 形象 | 793Kb | 1,030Kb | +30% |
| 闪光 | 92Kb | 87Kb | -5% |
| 其他的 | 101Kb | 205Kb | +103% |
| 总数 | 1，286Kb | 1，701Kb | +32% |

HTML 的增长几乎可以忽略不计，尽管考虑到内容精简和设计更简单、更扁平的趋势，这有点令人惊讶。57Kb 对于*来说是相当大的，仅仅是*的内容。

CSS 大小平均增加了 11Kb。有些可以用响应式网页设计和 CSS3 效果来解释，但是减少对厂商前缀的需求应该有所帮助？

然而，HTML 和 CSS 的任何增长都可以被 JavaScript 代码的减少所抵消。现在我们有了更好的浏览器一致性和 CSS3 动画，使用大型脚本库的理由更少了。这并没有发生，现在平均每个页面加载 18 个单独的脚本文件；串联和缩小会有很大帮助。

不出所料，Flash 下降了几千字节，使用该插件的页面从 37%下降到 32%。广告商仍然是主要用户，但 HTML5 替代品开始出现，现在响应式网页设计是一种主流技术。

“其他”文件的大小增加了一倍。这一增长的近三分之一可以归因于网络字体和网络字体图标集，这是可以接受的，因为它应该导致图像使用的减少… *除非它没有*。也许高密度的照片可以证明一些增加是合理的，但是谁会在每一页上加载一兆字节的图像呢？

当你考虑到这些数字是平均值时，它们就更令人震惊了。被分析的网站中大约有一半会更加肥胖。我们网络开发者应该羞愧地抬起头来。

## 原因

我们能责怪什么呢？我的主要怀疑是:

1.  臃肿的 CMS 模板
    典型的 WordPress 主题充斥着各种功能。许多将是作者添加的第三方样式和小部件，以使主题对买家更有用或更有吸引力。许多功能将不会使用，但文件仍然存在。
2.  HTML5 样板文件
    样板文件可能会节省时间，但重要的是要明白它们是通用模板。样式和脚本包含您永远不会使用的特性，HTML 可能会因为深度嵌套的元素和冗长的描述性类名而变得冗长。很少有开发人员费心去删除多余的代码。
3.  **粗心大意**
    开发者天生懒惰；我们编写软件是为了让任务变得更容易。然而，如果你不关心页面权重的后果，你应该吊销你的网络许可证。

即使我们忘记了网站搜索引擎优化、软件效率和用户响应能力，五分之一的网络访问来自手机。在最高效的移动网络上，下载一个 1.7Mb 的页面只需要一分钟——假设手机或平板电脑能够有效地呈现它。潜在客户会愿意等待吗？

移动连接和带宽持续改善，但很少在一年内增长 30%。具有讽刺意味的是，开发人员愿意采用 RWD 技术，同时让同一个网站在他们的目标设备上无法使用。

我很震惊。不可否认，我是在拨号上网时代开始开发的，当时 100Kb 被认为太大了，但是今天的网页比那时好 17 倍吗？

网页权重会降低吗？你的网站临床肥胖吗？它是怎么进入那种状态的？

## 分享这篇文章