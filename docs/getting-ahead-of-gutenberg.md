# 超越古腾堡

> 原文：<https://www.sitepoint.com/getting-ahead-of-gutenberg/>

*这篇关于古腾堡的文章是由扭矩杂志原创的[，经允许在此转载。](https://torquemag.io/2018/04/getting-ahead-of-gutenberg/)*

WordPress 有史以来最大的变化之一即将到来。你准备好了吗？你怎么准备？WordPress 为什么要做出这样的改变？自从马特·莫楞威格在 2017 年夏天宣布新的古腾堡编辑器以来，这些问题和更多的问题已经在 WordPress 空间飞了近一年，并且有很好的理由。许多开发人员都担心向后兼容性。请继续阅读，了解古腾堡将如何影响你现有的网站。

## 古腾堡简介

对于那些还没有听说过的人来说，Gutenberg 是 WordPress 管理界面新编辑的昵称。这是一个大新闻，因为尽管自从 WordPress 诞生以来，它的许多方面都发生了变化，但它的编辑器仍然是一个相当基本的视觉和 HTML 用户体验。因此，插件和主题开发者为编辑器创建了附加组件和新功能来满足他们的需求。这导致开发者和许多网站变得严重依赖短代码。WordPress 正在用户在后台创建的内容和在前端显示的内容之间创造一个更加统一的体验。通过这种方式，他们将许多用户认为“必不可少”的一些功能，尤其是非技术用户需要的功能，融入其中。

随着时间的推移，另一个越来越深的裂痕是，编辑在后端看到的并不一定代表前端显示的内容。随着插件和主题开始集成页面构建器和拖放组件以方便使用，WordPress 在默认情况下仍然是纯文本编辑器。古腾堡也解决了这种脱节。它不仅使编辑体验更加自然和有趣，还更准确地呈现了前端将显示的内容——真正的“所见即所得”体验。它将文本、图像、媒体等划分为构建页面的“块”。

## 阻碍

古腾堡的基础是一个“块”的概念您可以以有趣的新方式编辑经典内容部分，如添加高亮和背景色，轻松调整大小，以及轻松包装和嵌入照片和视频等经典元素。这意味着对于用户可能在页面中嵌入的其他项目，这些元素具有自定义块。您可以为联系人表单、推文、块引用等添加块。块的目的是给不同类型的内容创造一种连续性和规律性的感觉。有了一个统一的方法来创建一个块，即使你的最终功能目标与其他的有很大的不同，也有一个已知的“WordPress 方式”来为它创建代码，这本质上有助于平台整体的安全性和可伸缩性。

## 向后兼容性

好吧，所以古腾堡将真正帮助新网站的新用户有更好的体验。我现有的帖子和页面怎么办？这是要砸东西吗？社区里有很多关于向后兼容他们现有帖子的恐慌。正如马蒂亚斯·文图拉(Matias Ventura)恰当地指出的那样，古腾堡的好处在于，它并不打算完全取代旧的东西——你今天拥有和使用的一切都将继续有效——而是用新的东西逐渐塑造行为和发展。

这到底是什么意思？

*   [短码仍然像现在一样工作](https://wordpress.org/gutenberg/handbook/reference/faq/)
*   现有的职位仍然存在，就像今天一样
*   新编辑器保留了旧编辑器的所有功能
*   页面生成器仍然在工作，尽管它们的功能可能会在古腾堡中被部分复制

记住:简单地说，这背后的思想是，你不应该为几乎每个用户都同意对 WordPress 体验“必不可少”的功能下载额外的插件或高级主题。

## 发布计划

随着古腾堡预定在 [WordPress 5.0](https://wordpress.org/gutenberg/handbook/reference/faq/) 发布，以及 [WordPress 4.9.4](https://wordpress.org/news/2018/02/wordpress-4-9-4-maintenance-release/) 已经发布，最后期限肯定是迫在眉睫:WordPress 5.0 很可能在未来几个月登陆。这让人们想知道如何最好地准备，我们当然理解为什么。您可以高枕无忧，因为您知道这些选项和功能是可用的:

*   首先，您现有的页面和帖子将使用[“经典”块](https://wordpress.org/gutenberg/handbook/reference/faq/)转换到古腾堡。这使得它们可以像今天一样存在和显示，而无需任何人工干预。
*   第二，如果你编辑了一个现有的页面或帖子，发生了任何意想不到的事情，你可以使用插件轻松恢复。
*   第三，如果你只是喜欢传统的编辑体验，你可以下载经典的[编辑器插件](https://wordpress.org/plugins/classic-editor/)来恢复到你想要的编辑器。

Gutenberg 令人兴奋，因为它给 WordPress 带来了统一性、安全性和更好的用户体验。鉴于很多代码都在变化，以及人们使用 WordPress 的各种方式，我们的团队强烈建议你在 Gutenberg 作为主要编辑器发布之前，复制到你的 staging 环境并下载 Gutenberg 插件进行测试。有变通办法，比如上面提到的插件。但是我们鼓励每个人先给古腾堡一个尝试。自 2017 年 6 月首次发布以来，古腾堡插件已经经历了近 30 个版本和补丁，因此自然地，兼容性和可用性也有了一些发展。这也意味着，如果你以前尝试过，但不是一个粉丝，你可能应该再试一次，看看它有什么变化。此外，如果你是一名开发人员，你应该看一看古腾堡手册，开始以创造性和有趣的新方式进行调整。

整个 WordPress 社区团结在这个项目周围，帮助确保它的成功，结果令人印象深刻！如果你在测试过程中遇到任何令人担忧的场景，这是你回馈 WordPress 社区的机会，通过[贡献](https://github.com/WordPress/gutenberg/blob/master/CONTRIBUTING.md)你的想法、声音，甚至开发者技能到 Gutenberg 事业中来帮助它变得更好。如果您在测试中遇到任何问题，请[举报](https://github.com/WordPress/gutenberg/issues)！古腾堡团队渴望得到反馈，以帮助确保所有问题都得到解决。WordPress 的开源特性意味着一个蓬勃发展的全球开发者社区投入了大量资金来制作一个适合所有人的成功编辑器。

## 分享这篇文章