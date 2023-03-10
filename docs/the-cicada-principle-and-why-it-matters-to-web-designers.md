# 蝉的原则和为什么它对网页设计师很重要(更新)

> 原文：<https://www.sitepoint.com/the-cicada-principle-and-why-it-matters-to-web-designers/>

![Cicada illustration](img/f08f553448df3a09ab7e56cc3e113308.png)

今天我要开始一个看似疯狂的话题，但是请耐心等待——我保证我*做*是有目的的。

几年前，我读了一篇关于[周期性蝉](http://en.wikipedia.org/wiki/Cicada)生活的有趣研究。我们一般不会看到很多这样的小家伙，因为它们一生中的大部分时间都在悄悄地挖地道，啃树根。

然而，每隔 7 年、13 年或 17 年(视物种而定)，这些周期性的蝉会突然从它们的洞穴中集体出现，转变成喧闹的飞行生物，寻找配偶，最后在不久后离开人世。

虽然对于我们以前书呆子气的蝉来说，这是一个令人震惊的“朋克摇滚”消亡，但它提出了一个显而易见的问题:它们采用这种 7 年、11 年或 13 年的生命周期是否纯属偶然？还是这些数字有些特别？

事实证明，这些数字*和*确实有一些共同点。都是[质数](http://en.wikipedia.org/wiki/Prime_number#The_number_of_prime_numbers)——只能被自己和 1 除的数(也就是 2、3、5、7、11、13、17、19、23 等等)。

### 但是这有什么关系呢？

研究表明，捕食蝉的生物种群——通常是鸟类、蜘蛛、黄蜂、鱼和蛇——通常以自己较短的 2-6 年周期繁殖。

因此，如果我们的蝉每 12 年出现一次，任何以 2 年、3 年、4 年或 6 年为周期的捕食者都将能够逐渐使它们自己的繁盛期与这种可预测的蝉宴同步。事实上，他们可能会把这一天定为公共假日，叫做“国家蝉日”,烧烤，每个人都盛装打扮。

显然，如果你是一只蝉，那就没那么喜庆了。

另一方面，如果一窝 17 年的蝉不幸在一个丰收的 3 年黄蜂季节出现，这将是另一个 **51 年**(即 3 x 17)之前，该事件再次发生。在其间的几年里，我们的蝉可以愉快地成千上万地出现，完全压倒当地的捕食者种群，并且大多数情况下*和*都安然无恙。

多么足智多谋的小家伙啊！？！

### 太好了。但是这些和网页设计有什么关系呢？

不久前，我们看了制作无缝瓷砖的过程。尽管无缝瓷砖非常有用，但要保持平衡很难*恰到好处*。

![Seamless tile of a rock wall with an obvious repeating feature](img/c2e3360db7c168fbe98486c99a9b75c4.png)

一方面，您希望图像文件的尺寸尽可能小，以充分利用平铺效果。然而，当你注意到一个与众不同的特征——例如，一些木纹中的一个节——以有规律的间隔重复出现时，它真的打破了有机随机性的错觉。

也许我们从那些蝉那里借用了一些想法，让我们的瓷砖感觉不那么重复？

## 用 CSS 生成有机随机性

### 示例 1:魔法瓷砖

说够了。这里有一个快速的概念验证。这不应该是视觉上的辉煌，但它很好地展示了正在发生的事情。牢记“蝉原理”，我制作了三个正方形、半透明的 png，分别为 29px、37px 和 53px(都是质数)，并在测试页面的宽 DIV 元素上将它们设置为多个背景。

| ![29x29px](img/96cb6b124f5b5c3ad4662b938c4643b9.png) | [29a . png](https://i2.sitepoint.com/examples/primes/29-a.png)(2.0kb) |
| ![37x37px](img/a140e79d5fa7a7073b0bb9f9e178513e.png) | [37a . png](https://i2.sitepoint.com/examples/primes/37-a.png)(1.7kb) |
| ![53x53px](img/5d3b1d63d64374cb5b4075ab5b6c1391.png) | [53-a.png](https://i2.sitepoint.com/examples/primes/53-a.png) (2.5kb) |

```
div {
background-image: url(29-a.png),url(37-a.png), url(53-a.png);
padding:0; margin:0; height: 100%;
}
```

这是结果。向右拖动水平滚动条，查看我们生成新模式。

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的 Pen [基础蝉原理示例](https://codepen.io/SitePoint/pen/XWZejdW)。