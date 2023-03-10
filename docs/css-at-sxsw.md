# SxSW 的 CSS

> 原文：<https://www.sitepoint.com/css-at-sxsw/>

上周，我在德克萨斯州的 Ausin 参加了 SxSW Interactive。会议的两大亮点是周一上午的 CSS 小组:[CSS 高保真设计](http://www.sxsw.com/interactive/panels/index.php?action=detail&con=ia&id=54)和 [CSS:好、坏、丑](http://www.sxsw.com/interactive/panels/index.php?action=detail&con=ia&id=55)。这些演示中的一些幻灯片可以在网上找到:

*   Dan Cederholm:用 CSS 访问标签页
*   [戴夫·谢伊:用 CSS 进行高保真设计](http://www.mezzoblue.com/archives/2004/03/18/hifi_design_/)
*   [道格拉斯·鲍曼:使用 CSS 的高保真设计](http://www.stopdesign.com/present/sxsw2004/hifi/)
*   道格拉斯·鲍曼:CSS:好的，坏的，&丑陋的

上述内容提供了大量信息，但最让我震惊的一点是，至少对我来说，当 Doug Bowman 宣布臭名昭著的 Fahrner 图像替换技术被弃用时。图像替换技术是用图像替换文本的 CSS 方法，FIH 是第一个也是最著名的。虽然旨在提高可访问性，但该技术实际上产生了相反的效果，因为许多屏幕阅读器忽略了设置为显示的文本:CSS 中没有。乔·克拉克去年发表的一篇文章中有完整的技术细节。

各种其他图像替换技术涌现出来，以取代 FIR，但迄今为止，它们都有明显的缺点，即在浏览器中关闭图像时，用户完全看不到内容。我自己在通过拨号连接浏览 Opera 7 时遇到过这种情况，因为 Opera 提供了一个非常有用的键盘快捷键(“g”)，可以立即禁用和重新启用图像，从而大幅提高浏览性能。

我的建议是避免图像替换技术，除非被替换的文本根本不可见，不会对网站的可用性造成很大影响。不幸的是，这在大多数情况下排除了这种技术。但是请记住，从可访问性的角度来看，一个具有适当选择的 alt 属性的图像就像普通的 HTM 文本一样容易访问！

我错过的一个面板是[每个人的可访问性](http://www.sxsw.com/interactive/panels/index.php?action=detail&con=ia&id=10)。谢天谢地，Jeffrey Veen 已经发布了他在小组中使用的笔记:[我不关心可访问性](http://www.veen.com/jeff/archives/000503.html)。Jeffrey 的观点是，使用现代技术正确创建的网站不需要担心可访问性，因为如果你一开始就正确地构建了网站，那么它几乎就是默认的。我完全同意。

## 分享这篇文章