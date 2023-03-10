# 使用火狐，拯救地球

> 原文：<https://www.sitepoint.com/use-firefox-save-the-planet/>

![](img/4198910a4b06ea6e877c42cd2637a384.png "firefox-logo")根据[来自 SecTheory](http://www.sectheory.com/browser-power-consumption.htm) 的新研究，将 Firefox 与流行插件如 [NoScript](http://noscript.net/) 和 [Adblock Plus](http://adblockplus.org/en/) 结合使用足以显著降低功耗。使用第三方和内置监控工具，SecTheory 根据 Alexa 的数据查看了前 100 个网站，并监控了它们在客户端的功耗，这是通过访问网站时 CPU 消耗的安培数来测量的，在一台普通的 Windows 机器上使用 Internet Explorer 7.0 和 Firefox 3.0.4。浏览器和系统都打满了补丁，Javascript 打开了，Java、Flash 和 Silverlight 都装上了。

结果很有启发性。根据这项研究，页面上最耗电的是 Flash 和 AJAX 元素，Flash 广告是最大的罪魁祸首。“虽然其他技术可以也确实会导致电力峰值，但它们造成的问题远没有闪存多，这使它成为我们遇到的最不“绿色”的技术，”研究员罗伯特·汉森说。“然而，JavaScript、Java、VBScript 和 Silverlight 都很容易导致问题，它们不应该被视为功耗的可能元凶。”

基于这些结果，SecTheory 想知道阻止电力浪费的主要元凶——JavaScript 和 Flash 广告——是否会对整体电力消耗产生影响。他们使用 Firefox 对 Alexa Top 100 中十个最差的违规者进行了重复测试，但安装了 NoScript 和 Adblock Plus 插件(这十个网站分别被列入白名单和黑名单)。

结果如下。

| **浏览器** | **安培数** | **瓦特** |
| Internet Explorer 7.0 中整个 Alexa 100 的平均值 | Zero point four one four | Forty-eight point eight five two |
| Firefox 3.0.4 中整个 Alexa 100 的平均值 | Zero point four zero six | Forty-seven point nine zero eight |
| Internet Explorer 7.0 中十大滥用权力者的平均值 | Zero point four seven four | Fifty-five point nine three two |
| Firefox 3.0.4 中十大滥用权力者的平均值 | Zero point four eight one | Fifty-six point seven five eight |
| 使用 NoScript 和 Adblock Plus 的 Firefox 3.0.4 中前 10 名滥用权力者的平均值 | Zero point three eight two | Forty-five point zero seven six |

Hansen 说:“考虑到正常的空闲功耗在 0.36 到 0.39 之间，而带有 NoScript 和 Adblock Plus 的 Firefox 获得了 0.382 安培(45.076 瓦)的成绩，这表明动态客户端技术和广告对普通消费者的整体功耗有多么重要。”

虽然汉森承认这不是科学研究——只使用了一台计算机和一组有限的网站，并且安培数随时间的变化使测量变得精确——但他确实认为这提供了“足够的证据来指出每天网络应用中的功耗区域。”

据汉森所说，似乎有一种方法可以让网上冲浪更加“绿色”，那就是使用客户端软件来屏蔽网页中耗电最多的部分。汉森说:“减少浏览器中运行的客户端脚本数量，在不查看页面时将浏览器返回到静态页面，或者在不使用时关闭浏览器，以及使用操作系统内置的省电模式”都是减少上网时功耗的明确方法。不过，他提醒用户，不使用设备时拔掉插头是省电的最佳方式。

汉森希望他的论文将成为更严格的科学研究如何“绿色”网上冲浪的起点。

鉴于谷歌对环境的承诺，这可能是 T2 为 Chrome 添加扩展的又一个原因。谷歌特别将 Adblock 标记为它希望添加到 Chrome 的扩展之一，尽管它的大部分收入来自广告。

(注意:虽然这篇论文没有特别关注其他浏览器，但可以得出结论，在其他浏览器上使用 JavaScript 和广告拦截插件可能会产生相同的效果。)

## 分享这篇文章