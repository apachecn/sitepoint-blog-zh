# 令人困惑的预测

> 原文：<https://www.sitepoint.com/puzzling-forecasts/>

和这个一样，这里有一个现实世界的难题，可能是一个很好的(或令人讨厌的)面试问题——和我今天工作中必须处理的事情有关。

有多种类型的航空天气公告，在世界范围内使用，通常对航空公司等的运营至关重要。许多都是在 XML 出现之前设计的，倾向于“有趣”的格式——理解起来有趣，解析起来更有趣。我想这些设计是由系统限制驱动的——在这个时代，这里或那里的几个字节真的很重要——以及航空业看待世界的独特方式——类似于 [RFC 3339](http://en.wikipedia.org/wiki/RFC_3339) 日期将是纯粹浪费的奢侈品。

例如 [METAR](http://en.wikipedia.org/wiki/METAR) 报告描述了*当前*机场的天气状况，而 [TAF](http://en.wikipedia.org/wiki/TAF) 则是某机场的天气*预报*。

现在，一个给定的 TAF 预报将在最长 24 小时内有效，最短至少一小时(实际上，我相信通常不会少于 6 小时)。这里的“有效”是指包含在预测中的信息被认为是准确的时间段。

你可以在 TAFs online 上查看[这里](http://weather.noaa.gov/weather/taf.shtml)——例如，尝试输入代码 EDDF(德国法兰克福机场)——这些是更长的 TAF 天气预报——有效期长达 24 小时。还有更短时间的 TAF 预报(例如 9 或 12 小时)[这里](http://weather.noaa.gov/weather/shorttaf.shtml)。

查看今天 18:00 [UTC](http://en.wikipedia.org/wiki/UTC) 发布的法兰克福(EDDF)短期预报，我们有；

```
TAF EDDF 061800Z 061904 19012KT 9999
SCT015 BKN030 TEMPO 2204 RA BKN012

```

我突出显示的数字—**061904**—描述了预测的有效性(我相信前两位数字有时会被省略，但在本次讨论中，我们将忽略这一点，并说它始终是六位数字)。

假设您正在构建一个 web 应用程序来检索预测。飞行员在表格中输入一个类似“EDDF”的机场代码和这个六位数的代码。

收到请求后，您首先需要验证飞行员输入的六位数字代码，然后从中提取两个日期来执行选择。

这里有一些验证功能的“单元测试”;

*   输入:150024–函数返回“有效”
*   输入:340312–函数返回“无效”
*   输入:070315–函数返回“有效”
*   输入:081221–函数返回“有效”
*   输入:000305–函数返回“无效”
*   输入:070606–函数返回“有效”

写一个函数来验证六位数代码。

编写另一个函数(这次没有可用的测试)来返回有效期的开始日期/时间和结束日期/时间。

顺便提一下——只是出于兴趣:有开源的 TAF 解码器——[PHP Weather](http://phpweather.sourceforge.net/)支持它，我相信 [Geo::TAF](http://cpan.uwinnipeg.ca/dist/Geo-TAF) 也支持它。而 [Geo::METAR](http://cpan.uwinnipeg.ca/dist/Geo-METAR) 是 [Jeremy Zawodny](http://jeremy.zawodny.com/blog/) 的作品——我猜[这里是为什么](http://jeremy.zawodny.com/blog/archives/007342.html)。无论如何——不要让这分散你的注意力；)

## 分享这篇文章