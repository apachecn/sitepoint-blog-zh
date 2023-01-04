# 网络字体通过 Typekit 变得真实

> 原文：<https://www.sitepoint.com/web-fonts-get-real-with-typekit/>

网页设计者希望在网页设计中使用更多的字体。他们就要如愿以偿了。

最近我[写了](https://www.sitepoint.com/web-fonts-do-something-positive/)把商业字体带到网络上的问题只能通过人们选择避免大喊大叫的比赛来解决，相反[做一些积极的事情](https://www.sitepoint.com/web-fonts-do-something-positive/)。~~两个选择这样做的人是杰弗里·维恩和朱莉·哈姆伍德。布莱恩·梅森、瑞安·卡弗和格雷格·维恩(朱莉·哈姆伍德正在帮忙做一些市场调查)。~~

杰夫~~和朱莉~~上周[公布了 Typekit](http://blog.typekit.com/2009/05/27/introducing-typekit/) 。在接下来的几个月里， [Typekit](http://typekit.com/) 将会发布，旨在解决目前阻碍网页设计师在设计中使用商业字体的所有问题。

到 Typekit 发布的时候，每一个主流的桌面浏览器都将支持使用 [`@font-face`](https://reference.sitepoint.com/css/at-fontface) 链接到你站点 CSS 中的自定义字体。然后，您可以在您的网站上使用这些字体，而无需用户事先在他们的系统上安装这些字体。

Safari 3.1、Chrome 2、Firefox 3.5(即将推出)、Opera 10(即将推出)都支持链接到 TrueType(。ttf)或 OpenType(。otf)字体文件。这在您的 CSS 代码中是这样的:

```
@font-face {
  font-family: "Custom Font";
  src: url("CustomFont.ttf") format("truetype");
}
```

唯一的问题是，商业字体供应商到目前为止还不愿意以这种方式许可他们的字体使用。

为了解决这个问题，Internet Explorer 4 和更高版本也支持`@font-face`，但需要首先将字体转换为嵌入式 OpenType (EOT)格式，以便浏览器阅读。

```
@font-face {
  font-family: "Custom Font";
  src: url("CustomFont.eot");
}
```

到目前为止，生成 EOT 文件的唯一可行方法是使用微软的 [Web 嵌入字体工具](http://www.microsoft.com/typography/WEFT.mspx)，该工具以不稳定和难以使用而闻名。

对于那些在家玩游戏的人来说，仍然存在以下障碍:

*   字体供应商没有提供包括链接到。ttf/。otf 文件。
*   Internet Explorer 仅支持非标准文件格式，这需要稍微不同的 CSS 代码。
*   Internet Explorer 要求的文件格式很难生成。

当今年夏天发布时，Typekit 将解决所有这些问题。

Typekit 将把字体文件保存在自己的高速服务器上，并且只提供给付费网站(如果是付费字体的话)。这种简单的措施比内置防盗保护的新字体文件格式更可取，足以消除许多字体代工厂(尚未宣布)的担忧，这些代工厂已经签约通过 Typekit 分发他们的字体。

为了支持 Internet Explorer，Typekit 的服务器将自动生成 EOT 版本的字体以及其他浏览器支持的 TrueType/OpenType 版本。设计者将能够在他们的站点中嵌入一个简单的`<script>`标签，该标签将自动调整站点样式表中的标准`@font-face`声明。虽然细节还有待公布，但我希望那些希望避免额外脚本的性能损失的开发人员能够编写`@font-face`交换机服务器端代码，如果他们愿意的话。

[公告](http://blog.typekit.com/2009/05/27/introducing-typekit/)承诺，除了付费服务之外，Typekit 将推出免费服务级别，随后还将推出“专业版”。定价和收费模式的具体细节(订阅还是一次性字体购买？按要求？每种字体？)尚未公布——事实上，我们被告知他们仍在研究细节。

对该公告的反应非常积极，但一些设计师表达了一些担忧。由于字体由第三方托管，服务中断会导致网站丢失自定义字体。从长远来看，任何网络服务都有可能消失，这将再次使网站“无字体”。这两个问题都可以通过谨慎选择后备字体来缓解，这对于不支持字体链接的旧浏览器是必要的。

一些设计者似乎不高兴，他们将不得不为他们“已经拥有”的字体购买新的许可证，但由于这些字体的现有许可证通常不允许网络链接，额外的许可证费用似乎是不可避免的。

如果 Typekit 和类似的解决方案获得成功，我们可以希望在未来的版本中看到 Internet Explorer 支持标准字体格式，因为他们声明到目前为止还没有这样做的原因是商业字体还没有被授权在 web 上使用这些格式。

也许更重要的是，一个蓬勃发展的网络商业字体许可系统将有望刺激高质量屏幕字体设计的复兴。目前，大多数商业屏幕字体主要是为打印而设计的，可能不适合用于 Web 上的整段文本。

你有没有最喜欢的字体，期待在网上使用？

## 分享这篇文章