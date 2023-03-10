# Mozilla 解决浏览器 CSS 历史隐私泄露问题

> 原文：<https://www.sitepoint.com/mozilla-to-tackle-browser-css-history-privacy-leak/>

[![](img/bba722f2f09d0378353b9022dfefda46.png "mozilla-foundation")](https://www.sitepoint.com/wp-content/uploads/2010/04/mozilla-foundation.png) 所有浏览器都可以被利用来揭示你访问过的网站；即浏览器历史记录列表中的所有网站。这项技术极其简单。使用 CSS `:visited`选择器将规则应用于链接，该选择器可以应用背景图像或颜色。然后，用 JavaScript 查询该属性以查看是否应用了该值，或者为记录信息服务器端的每个测试链接创建一个唯一的背景图像 URL。有现成的库可以非常快速地检查大量的 URLs 我说的是每分钟几十万个。

嗯， [Mozilla 已经宣布了一项战略](http://blog.mozilla.com/security/2010/03/31/plugging-the-css-history-leak/)来解决这个问题，这将对网页设计师产生一些影响。他们希望其他浏览器制造商也能效仿。它涉及对 Mozilla 代码库的三个主要更改，这些更改将影响浏览器行为:

1.  访问过的链接只能通过颜色来区分:前景、背景、轮廓、边框、SVG 笔画和填充颜色。
2.  在应用任何链接样式之前，已访问和未访问链接的所有样式规则将同时被解析。
3.  当 JavaScript 用于查询链接的计算样式时，将只返回未访问的样式值。

令人惊讶的是，这种策略符合 [CSS 2.1 规范](https://www.w3.org/TR/CSS2/selector.html#link-pseudo-classes)，其中规定:

> 因此，UAs 可以将所有链接视为未访问的链接，或者实施其他措施来保护用户的隐私，同时不同地呈现已访问和未访问的链接。

我不得不承认，我不知道这个问题有多严重，也不知道它有多容易被利用。但是，在读了一些关于这个主题的文章后，这似乎是正确的行动。我也很惊讶浏览器厂商花了这么长时间才解决这个问题。我希望 Mozilla 的例子能够流行起来。

在对 Mozilla 帖子的评论中，人们已经在抱怨他们经常使用背景图片来设计访问过的链接，色盲用户将处于不利地位。你觉得这个策略怎么样？你最喜欢的 CSS 技术会有什么改变吗？这样真的会对你的工作有很大作用还是很容易迁就？

## 分享这篇文章