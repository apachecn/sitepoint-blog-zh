# WordPress Core 用哪个 JavaScript 框架？

> 原文：<https://www.sitepoint.com/which-javascript-framework-for-wordpress/>

如果你一直关注 WordPress 新闻的话，你可能会意识到，除了 [Backbone.js](http://backbonejs.org/) (将会保留并继续维护)之外，WordPress Core 很快会包含一个 JavaScript 框架。但是为 WordPress Core 寻找正确的 JavaScript 框架是一件有趣的事情。

## 为 WordPress 选择一个 JavaScript 框架

5 月 23 日[和 5 月 30 日](https://make.wordpress.org/core/2017/05/24/javascript-chat-summary-for-may-23rd/)在 WordPress 贡献者 Slack 上举行的每周 JavaScript 聊天过程中，决定已经缩小到 [React](https://facebook.github.io/react/) 或 [Vue.js](https://vuejs.org/) 。希望 WCEU 会议能进一步阐明这场辩论。

在讨论的过程中，事实上，尤雨溪本人(Vue.js 的创建者)[发表了他的想法](https://make.wordpress.org/core/2017/05/31/javascript-chat-summary-for-may-30th/#comment-32642)。

这场争论相当激烈，双方的人当然都很高兴看到 Core 中包含了一个新的框架，但真正的问题是哪一个，为什么？以下是双方人士就两位候选人提出的几点看法:

## 反应

*   (相对而言)有更高的学习曲线，这可能会成为一个障碍，尤其是对于新的 JS 开发人员或采用 JS 的 WordPress 开发人员
*   许可问题(特别是[这个](https://github.com/facebook/react/blob/af3ba36c7f0cdd75529612d9872c53d63df8fb72/PATENTS#L14-L33)
*   一些 WordPress 的贡献者担心将 WordPress 与 React 联系得如此紧密，进而担心脸书，一家意识形态与 WordPress 不太一致的公司。

## view . js-检视. js

*   有更容易的学习曲线
*   没有被广泛采用，因此有些人认为它的用户群体和维护群体都比较小
*   用模板语言模糊 JS 被一些人视为对开发人员不友好
*   只有一个主要的 Vue.js 维护者被认为是危险的。总线测试，简而言之就是一个项目承受一个重要贡献者突然离开项目(无论什么原因)的能力。

当然还有更多的观点，在每周 JS 聊天的链接摘要中有简洁的总结。此外，许多观点是有争议的。例如，React 的许可问题被一些人认为不是问题；与模板相比，JSX 被视为一个类似的障碍；尤雨溪指出，社区维护者减轻了 Vue.js 生存性的一些危险。

当然，所有这些都让人们再次谈论 WordPress 及其 JavaScript 的未来。未来几年，WordPress 的哪些部分可能是 JavaScript，还是全部？WordPress.com 创建的 WordPress.com 多站点管理面板 [Calypso](https://developer.wordpress.com/calypso/) 也引发了同样的讨论，但是随着时间的推移，这种讨论已经平息了，所以看起来 WordPress-PHP 社区可能会通过这场辩论在 JavaScript 框架的土地上进行另一次疯狂的旅行。

你怎么看待 WordPress 和 JavaScript 的未来？请在评论中告诉我们！

## 分享这篇文章