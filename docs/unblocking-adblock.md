# 解除阻塞 Adblock

> 原文：<https://www.sitepoint.com/unblocking-adblock/>

如果你使用安装了 [Adblock Plus](https://addons.mozilla.org/en-US/firefox/addon/1865) 扩展的[Firefox](http://en.www.mozilla.com/en/firefox/)…你有没有注意到 sitepoint.com 有什么不同？

几周前，我实现了一个全网站范围的脚本，**解除了被 Adblock Plus** 屏蔽的广告。

看，不久前，当整个 [firefox 被屏蔽的灾难](https://www.sitepoint.com/forums/showthread.php?threadid=502783)正在肆虐时，我做了一些关于规避广告屏蔽软件有多容易的摇手声明(当然，我不会说如何)。过了一会儿(不太确定是什么时候……我可能喝醉了),我赌输了，必须证明这一点。赌注很高，我觉得有点脏，但我是个守信用的人，所以我必须这么做。当它发生时，它变成了一个有趣的挑战，有一个相关的点，使…

## 广告还是不广告

我本人并不喜欢广告，我也不反对人们使用广告拦截软件。我知道有一些人认为屏蔽广告等同于盗窃，但我认为他们的论点完全是似是而非的。网络的任何部分都是可以定制的，你可以让网站有一个高对比度的配色方案，因为你需要它来阅读，或者你可以过滤掉所有的图片，因为你没有带宽来加载它们，或者你可以有选择地删除特定类型的内容，因为你只是不想看到它。这都是一样的，反对一个就是反对所有的(这就是为什么法律永远无法干预——将广告拦截活动定为非法将使所有用户修改都成为非法，包括使用辅助技术、用户 <abbr title="Cascading Style Sheets">CSS</abbr> 和用户脚本以及其他扩展)。

无论如何，真正的问题不在于广告屏蔽，而在于拒绝承认人性的商业模式，以及那些想赚钱却假装赚钱不是其动机的内容提供商。在我看来，这种怀疑是相当无礼的——如果你的内容是免费的，就让它免费；如果不是免费的，那就收费。但不要让它表面上免费，然后试图让我感到内疚，当它花费你一些东西。

## 无论如何

那是我的看法，但尽管如此，我还是喜欢把一只古怪的猫扔进鸽子里。所以我还是实现了解封脚本，只是为了给这场辩论浇点油，并提醒那些忘记了一个简单事实的人:**内容控制——以及一般的数字版权管理——是一场军备竞赛，没有人会赢**。

## 它是这样工作的

首先，我们需要确定 Adblock 确实在运行——如果它不在运行，我们就不想运行脚本，否则我们会重复所有的广告。曾经有一个 Firefox bug 暴露了关于已安装的扩展的信息，但是这个 bug 早就被修复了。所以:

```
if(navigator.userAgent.toLowerCase().indexOf('firefox') == -1) { return; }

var self = this, tester = document.createElement('img');
document.getElementsByTagName('body').item(0).appendChild(tester);
tester.setAttribute('src', '/ads/draino.gif');
window.setTimeout(function()
{
	if(!tester || tester.style.display == 'none')
	{
		//adblock is running
	}
}, 10);
```

这里我们假设 Adblock 的过滤器将匹配包含 <q>/ads</q> 的 <abbr title="Uniform Resource Indicator">URI</abbr> ，因为如果它匹配，那么我们将永远无法加载该图像，这就是我们如何知道 Adblock 正在运行。Adblock 的一个巧妙之处在于，它实际上并没有从文档中删除任何东西——从一开始就不会让它们接触到文档。Adblock 与 Firefox 的内容策略 <abbr title="Application Programming Interface">API</abbr> 挂钩，后者将允许的内容控制在 <abbr title="HyperText Transfer Policy">HTTP</abbr> 请求的级别，这意味着被阻止的内容*甚至从未被请求过*。(空的源元素也不显示在动态生成的用户样式表中，该样式表不能被文档中的任何样式覆盖，因为用户样式表总是优先)。

当然，如果测试图像没有被阻塞，那么脚本的其余部分将不会运行，但是考虑到目标 <abbr title="Uniform Resource Indicator">URI</abbr> 的明显性，它很可能会运行。

一旦我们确定 Adblock 正在运行，我们就可以开始寻找可阻止的内容。该脚本定义了一个 <abbr title="Uniform Resource Indicator">URI</abbr> 过滤器的子集(类似于 Adblock 本身，但仅限于我们知道正在运行的广告)，以及一个要检查的元素列表(脚本、图像和 iframes)。根据过滤器测试目标元素的每个实例，看它是否可能被阻塞，如果是，我们记住它的地址。

现在我们有了一个地址列表，我们需要能够手动请求该数据，这可以通过经由`XMLHttpRequest`调用 <abbr title="Hypertext Pre-Processor">PHP</abbr> 脚本来实现——服务器可以从任何域请求数据，然后将其传递回客户端。然而，事情并没有*那么简单，因为即使是 <abbr title="XMLHttpRequest">XHR</abbr> 请求也会受到 Adblock 的影响。我们需要防止我们的请求被阻塞，所以要做到这一点，我们要对地址进行编码，这样过滤器就不会拾取它们。它不需要太花哨，所以 [Rot13 编码](http://en.wikipedia.org/wiki/ROT13)就足够了，我们最终会发出这样的请求:*

```
request.open('GET', 'draino.php?encuri=uggc://jjj.zlfvgr.pbz/nqf/cebzb.wf', true);
```

<abbr title="Hypertext Pre-Processor">PHP</abbr> 脚本对 <abbr title="Uniform Resource Indicator">URI</abbr> 进行解码，然后使用`fopen()`将其内容抓取到缓冲区中(其他方法也是可能的，但这似乎是最直接的；请注意， <abbr title="Hypertext Pre-Processor">PHP</abbr> 脚本本身已经过验证，可以防止注入攻击，并且不允许来自指定主机之外的请求。根据广告代码输出的内容，我们可能会得到 JavaScript、 <abbr title="HyperText Markup Language">HTML</abbr> 或图像数据作为响应。

如果是 <abbr title="HyperText Markup Language">HTML</abbr> ，那将是因为原始广告是一个`<iframe>`，所以在这种情况下，我们只需更改 iframe 的`SRC`以指向 <abbr title="Hypertext Pre-Processor">PHP</abbr> 脚本，我们就完成了:

```
<iframe src="draino.php?encuri=uggc://jjj.zlfvgr.pbz/nqf/cebzb.wf">
```

如果是图像数据，我们会对其进行 base64 编码，以便在客户端我们可以使用该数据创建一个具有[数据 URI](http://en.wikipedia.org/wiki/Data:_URI) 的新图像，该图像没有外部资源，因此不会被阻塞:

```
var img = document.createElement('img');
img.setAttribute('src', 'data:image/gif;base64,' + request.responseText);
element.parentNode.insertBefore(img, element);
```

该代码中提到的`element`是原始广告——通过在它之前直接插入新图像，我们确保它出现在文档中与原始图像相同的位置。

最后，如果响应是 JavaScript，我们需要解析和评估代码。每个广告将是不同的，所以我们基本上需要每个供应商不同的情况。例如，如果提供者返回包含在`document.write`语句中的 <abbr title="HyperText Markup Language">HTML</abbr> ，我们将需要提取输出，并使用`innerHTML`将其指向一个新元素，然后将该新元素直接附加到原始广告脚本之前(就像图像一样，以便它出现在相同的位置):

```
var code = eval(request.responseText.replace('document.write', ''));
var span = document.createElement('span');
span.innerHTML = code;
element.parentNode.insertBefore(span, element);
```

## 这就是我们的方式！

Adblock 围绕着 <abbr title="Uniform Resource Indicator">URI</abbr> 过滤，所以如果我们可以欺骗它让我们请求不允许的数据，我们可以简单地将这些数据直接添加到页面中，然后 Adblock 就没有任何东西可以使用了。我们甚至没有扭曲我们的广告统计数据，因为最初的广告从未被请求过——我们的请求是唯一可以通过的请求！

我们唯一不能处理的是生成一个`<iframe>`的脚本，它本身包含另一个可阻塞的元素。理论上，我们可以进一步深入，递归地解除对 iframe 页面内容的封锁……但这有点荒谬。

## 阻挡解锁器

是的，我从来不想变得邪恶(<q>一旦你开始走上黑暗的道路，它将永远主宰你的命运</q>)。如果你想阻止所有这些工作，你只需要阻止解除阻止脚本-简单地添加 <q>draino</q> 到你的过滤器列表。不管怎样，我下周要把它去掉，这只是一点乐趣；)

除非我让那个脚本也不被屏蔽…

## 分享这篇文章