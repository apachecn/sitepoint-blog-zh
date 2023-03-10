# DRM:切断你的散文来刁难你的脸

> 原文：<https://www.sitepoint.com/drm-cutting-off-your-prose-to-spite-your-face/>

为了保护你的版权，你应该走多远？

昨天，我点击进入了 MISAustralia.com 上的一个反推特痛骂(讽刺的是通过一个转发)。虽然你可以对内容做出自己的判断，但真正吸引我眼球的是正文字体。为什么一个大型的专业内容网站会选择用难看的、不可读的[等宽字体](http://en.wikipedia.org/wiki/Monospaced_font)来显示他们的内容？

心不在焉地，我拖动选择了一些文本，得到了另一个惊喜——在选择区域有一个漂亮的棋盘图案。

嗯……有意思。这是怎么回事？…

查看源代码时，我的下巴差点撞到桌子。

MISAustralia 的疯狂科学家似乎已经建立了一个内容管理系统，可以自动将每个段落一个字母一个字母地分成两堆。

然后，每一堆都被转储到它自己的 DIV 中，并用不间断的空格填充，然后它们被精确地相互覆盖，以使它们再次可读。

当然，这意味着复制和粘贴文本只涉及最上面的 DIV，并解释了斑马图案和他们选择的单倍行距字体。

很明显，他们的动机是数字版权管理(DRM ),使复制粘贴或屏幕抓取他们的内容变得更加困难。事实上，他们的 HTML 源代码注释称其为“DRM Viewer”。

![View Source: The DRM Viewer showing what Gooogle sees.](img/46450927237552b24059b62950ecc5ef.png)

在很多层面上，这都让我感到震惊。

1.  首先，它让它们的内容对屏幕阅读器和其他辅助技术来说就像天书一样。我不是律师，但我怀疑这是一个有力的歧视诉讼的基础。
2.  其次，这使得他们的内容在任何 RSS 阅读器中都不可读，甚至阻止他们提供 RSS 提要。
3.  第三，它需要使用字体，这进一步侵蚀了其内容的价值。
4.  最后，也是最重要的，它使他们的身体副本(即他们的网站和业务的心脏和灵魂)对谷歌、雅虎和地球上的其他搜索引擎完全不可见。

最后一点让我难以理解。

整个行业(SEO)的发展没有别的原因，只是为了确保谷歌看到并重视你的内容。公司的生死取决于它们让内容可见的能力。这是一家公司打算花费大量的时间、精力和费用，从网络最大的流量提供商那里主动掩盖他们的工作。

就谷歌而言，这不仅仅是低等级的内容，而是“非内容”。根本就不存在。果然是 ***没写成*** 。

举个简单的例子，就拿最近的 EPG 战役来说吧。

在谷歌上搜索[非 DRMed 文章标题，它会出现在第一位](http://www.google.com/search?ie=UTF-8&oe=UTF-8&sourceid=navclient&gfns=1&q=Nine+loses+EPG+battle)。完美！谷歌清楚地知道并访问他们。

然而，让我们进入这篇文章，搜索一个非常具体的短语，“Ice TV 总经理马特·科萨茨说这项裁决是及时的”。

结果:[什么都没有](http://www.google.com.au/search?hl=en&client=firefox-a&rls=org.mozilla%3Aen-US%3Aofficial&as_q=&as_epq=Ice+TV+general+manager+Matt+Kossatz+said+the+ruling+was+timely&as_oq=&as_eq=&num=10&lr=&as_filetype=&ft=i&as_sitesearch=misaustralia.com&as_qdr=all&as_rights=&as_occt=any&cr=&as_nlo=&as_nhi=&safe=images)。Blip。这里没什么好看的，各位，走开。

这当然不足为奇。谷歌怎么知道它在看什么？

我甚至不打算从巨大的可访问性问题开始，因为害怕把它变成一个 10 页的帖子。

#### ![MISAustralia.com: Before and After Greasemonkey](img/57e0972b8faa2018c8a2b1ec905ef4d3.png)最后的讽刺

现在，如果这是一个解决版权困境的万无一失的方法，那么让 99.99%的普通读者去阻止 0.01%的侵权访问者是否值得，这仍然是一个很有争议的问题。

不幸的是，这种数字版权管理是绝对安全的。

任何运行 [Greasemonkey](https://addons.mozilla.org/en-US/firefox/addon/748) 和 [MISaustralia 文本选择器 userscript](http://userscripts.org/scripts/show/30321) (向 Gustav Axelsson 脱帽致敬)的人不仅可以剪切和粘贴他们的小心脏，而且他们可以用相对豪华的 Verdana、Arial 或 Helvetica 字体阅读它。

事实上，如果你是一个阅读这个网站的 Firefox 用户，仅仅出于可读性的原因而不使用这个脚本几乎是愚蠢的。

同样，编写一个自动解析并在其他地方重新发布每一篇新文章的应用程序也是微不足道的。最酷的是，他们甚至不必与原作者竞争。就谷歌而言，他们将“拥有”这些内容。

现在，这些人是一家大型的、一般来说精通技术的公司的一部分。

这是坚果吗？

## 分享这篇文章