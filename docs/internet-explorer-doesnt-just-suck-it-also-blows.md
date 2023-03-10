# Internet Explorer 不只是吸，它还吹！

> 原文：<https://www.sitepoint.com/internet-explorer-doesnt-just-suck-it-also-blows/>

这些天我花了大部分时间在 SitePoint 即将发布的**终极 JavaScript 参考文献**上，我可以说这个任务是*在啃我的脑子*。

与即将出版的**终极 CSS 参考文献**的作者不同，我并没有特别倾向于对 ie 浏览器友好。我知道我会遇到错误和怪癖，这些在 <abbr title="Internet Explorer 7">IE7</abbr> 中不会有任何不同，因为 <abbr title="Document Object Model">DOM</abbr> 根本不在那个版本的开发范围内。

即便如此，即使是最简单的事情，在它的实现过程中，明显存在大量混乱的漏洞，这让我感到震惊。

你可能还记得，不久前，我写过关于 Internet Explorer 中 <q>href</q> 属性的[行为，以及对于链接来说，它们是如何作为合格的 <abbr title="Uniform Resource Indicators">URI</abbr> 而不是字面属性值返回的。但是，哦，伙计…这只是冰山一角](https://www.sitepoint.com/dealing-with-unqualified-href-values)

## 你不会想到这有这么难

除了限定链接上 <q>href</q> 属性的值， <abbr title="Internet Explorer">IE</abbr> 对图片的 <q>src</q> 属性做同样的事情。

当检索一个<q>样式</q>属性时， <abbr title="Internet Explorer">IE</abbr> 返回一个`style`对象，而不是一个属性值。检索一个事件处理属性，如 <q>onclick</q> ，它返回包装在匿名函数中的事件处理程序的内容。检索评估为布尔值`true`的属性值(如<q>禁用</q>或<q>检查</q>，当定义时)它返回一个布尔值，检索评估为数字的值(如 <q>tabindex</q> )，它返回一个数字。所有这些值都应该作为字符串返回。

但是这些属性被 <abbr title="Internet Explorer">IE</abbr> 认为是非字符串值；所以如果它们没有被定义，它们会返回`null`，而不是一个空字符串。 <abbr title="Internet Explorer">IE</abbr> 也为它不能识别的未定义属性返回`null`(<abbr title="that is">IE</abbr>)。自定义属性名称)。

为自己辩护，其他未定义但已知的属性正确地返回一个空字符串，这是符合规范的；而 <abbr title="Internet Explorer">IE</abbr> 实际上是唯一做到这一点的浏览器(Firefox、Opera 和 Safari 都返回`null`)。但这并不是真正的辩护，因为它只对它识别的属性这样做——所以它并没有真正按照规范实现，它只是碰巧是正确的！

## 纯粹的阶级

要在 Internet Explorer 中检索一个<q>类</q>属性，您必须将其称为<q>类名</q>；要检索属性的<q>,您必须将其称为</q>的<q>html:</q>

```
//these work in IE, null in others
element.getAttribute('className');
element.getAttribute('htmlFor');
```

这是将属性映射到 <abbr title="HyperText Markup Language">HTML</abbr> <abbr title="Document Object Model">DOM</abbr> 属性的副作用——例如，作为 <abbr title="Document Object Model">DOM</abbr> 属性，您总是必须引用 <q>element.className</q> 而不是 <q>element.class</q> ，因为 <q>class</q> 是 JavaScript 中的保留字。但是，当其他浏览器通过允许字符串值使用原始属性名来协调这一点时，Internet Explorer 却不能:

```
//these work in others, null in IE
element.getAttribute('class');
element.getAttribute('for');
```

还有其他一些属性只能通过它们用于其 <abbr title="Document Object Model">DOM</abbr> 属性等价的大小写名称来引用，即使这些名称不是保留字；我还没有找到具体的模式，但这方面的例子有 <q>tabIndex</q> 和 <q>accessKey</q> 。

## 还有更多…

Internet Explorer 为`getAttribute()`实现了第二个专有参数，用来控制它的行为方式。第二个参数是一个数字标志，它可以取值`0`、`1`或`2`。[据 MSDN](http://msdn2.microsoft.com/en-us/library/ms536429.aspx) :

*   `0`(默认值):执行不区分大小写的属性搜索，如果找到属性，则返回插值。
*   `1`:执行区分大小写的属性搜索。
*   `2`:返回与脚本或源文档中设置的值完全相同的值。

当它说<q>插值</q>时，意味着它不一定会返回一个字符串，正如已经提到的。还要注意它是怎么说的<q>如果找到了**属性**</q>【我的重点】——这似乎意味着 <abbr title="Internet Explorer">IE</abbr> 根本没有使用`getAttribute()`作为节点值的获取器，而是使用它作为 <abbr title="Document Object Model">DOM</abbr> 属性的获取器！如果是真的，这就很好地解释了它的异常行为——如果它正在检索属性值，这就是为什么它需要属性名，为什么它返回适用类型的值；而当 <abbr title="Microsoft Developer Network">MSDN</abbr> 说`getAttribute()` <q>检索指定属性</q>的值时，就是彻头彻尾的撒谎。

`0`和`1`的区别似乎是按照文档实现的——属性名区分大小写，所以搜索 <q>onClick</q> 不会匹配 <q>onclick</q> 。

然而，值`2`的行为与记录不符。当使用这个值时，事件处理属性仍然作为函数返回，<q>样式</q>属性是一个空字符串，评估为布尔值`true`的值返回`65535`(即`2<sup>16</sup>`，一个 16 位数字的最大可能值。那是怎么回事？？)但是，嘿—从更积极的方面来说— <q>href</q> 和 <q>src</q> 属性确实返回它们的文字值，而不是限定的 <abbr title="Uniform Resource Locator">URI</abbr> 。我想我们应该感谢小恩小惠！

你可以明白为什么我说它在侵蚀我的大脑…我的意思是，没有全面地实现标准是一回事，而且已经够糟了，但是 Internet Explorer **甚至没有正确地实现它自己的专有东西！**

## 分享这篇文章