# 使用“清除选择器”查找未使用的 CSS 选择器

> 原文：<https://www.sitepoint.com/find-unused-css-selectors-with-dust-me-selectors/>

普通读者会很清楚我们在过去几个月里为改善 SitePoint 竞赛和市场所做的工作。我在其中的作用是改进前端——更好的语义和可访问性，更高效的代码，没有不必要的脚本，以及协调所有不同的 CSS 样式。

正如你所想象的，我从旧的市场和整个网站继承了很多遗留的 CSS。我需要一些方法来分离它，只保留我们真正需要的规则。

如果你曾经做过一个大项目——尤其是几个人都在处理同一个样式表的时候——你会知道事情会有多快失控。样式是随着时间的推移添加的，然后就被遗忘了，几个月(或者在这种情况下，几年)后重新出现在样式表上可能是一种可怕的经历。有多少是必要的？你并不总是知道，但你害怕删除任何东西，以防某个地方的某个东西正在使用它。

我需要一个能帮我解决这个问题的工具——我可以在开发过程中在后台运行的工具，它将建立一个没有在任何地方使用的规则的配置文件。由于没有任何东西可以做这项工作(至少，我和我的同事都找不到)，我自己写了一个——最初是一个 Greasemonkey 脚本，最后是一个成熟的 Firefox 扩展。

这就是了——尽情享受吧！

对下一个版本的评论、想法、建议？我很想听听你的想法。

## 分享这篇文章