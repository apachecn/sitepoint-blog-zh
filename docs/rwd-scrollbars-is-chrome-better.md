# 响应式网页设计和滚动条:Chrome 的实现更好吗？

> 原文：<https://www.sitepoint.com/rwd-scrollbars-is-chrome-better/>

在我最近的一篇文章中，[你的响应式网页设计太脆弱了吗？](/responsive-web-design-too-fragile/)，我描述了在触发媒体查询事件时浏览器之间的不一致性:

*   Chrome 和 Safari **不包括**滚动条尺寸。
*   火狐、Opera 和 IE **包含了**滚动条尺寸。

假设我们有一个媒体查询，它检测到最小宽度为 800 像素，例如

```
@media (min-width: 800px) { ... }
```

Chrome 和 Safari 将在正文宽度为 800px 或更大时包含这些样式。但是，如果你的操作系统显示宽度为 20px 的垂直滚动条，其他浏览器会包含 780px 的样式。

让我们阅读一下宽度为的 [W3C 媒体查询规范:](https://www.w3.org/TR/css3-mediaqueries/#width)

> “宽度”媒体功能描述了输出设备的目标显示区域的宽度。对于连续媒体，这是视口的宽度(如 CSS2 第 9.1.1 节[CSS21]所述)，包括呈现的滚动条(如果有)的大小。

Webkit 引擎出错。但是这样更好吗？

乍一看，Chrome 的实现似乎合乎逻辑，但重要的是要理解“宽度”指的是视窗尺寸，即浏览器窗口内的总面积— **而不是页面宽度**。使用上面的假设，Chrome 的视口宽度实际上是 820px (800px 正文+ 20px 滚动条)。在其他浏览器中，视区宽度为 800 像素(780 像素正文+20 像素滚动条)。

不幸的是，知道视窗宽度并不能帮助 CSS 开发，因为你不知道滚动条是否可见或者它的尺寸是多少。然而，Chrome 的替代方案要糟糕得多，因为滚动条的引入或移除会触发媒体查询。这有点复杂，所以我用一个例子来解释:

1.  假设您有一个简单的响应站点，它以 800 像素或更高的像素显示桌面布局，以 799 像素或更低的像素显示移动站点。滚动条宽度为 20px。
2.  目前，用户的浏览器窗口大小为 810 像素的视口宽度。高度足以显示当前页面(在桌面布局中)而不显示滚动条。
3.  你点击进入另一页。这里，内容比视窗高度长，并且出现垂直滚动条。这将机身宽度减少到 790 像素，Chrome 立即从桌面切换到移动布局。用户变得困惑，发誓再也不会回到你的网站。

如果您有动态生成的内容，情况会更糟。如果页面当前适合视窗，并且您显示了增加高度的状态消息，则产生的垂直滚动条会将用户从桌面切换到移动布局(这可能会在不同的位置显示消息)。一旦状态消息被删除，页面将返回到桌面布局。

Firefox、Opera 和 IE 不会表现出这种行为，因为除非用户调整浏览器窗口的大小，否则视窗尺寸不会改变。然而，Chrome 和 Safari 用户可能会体验到不同的布局，只是因为你的网站在一个页面上的内容比另一个页面上的稍微多一些。恶心。

诚然，这是一个边缘案例:

*   移动浏览器通常不会显示永久滚动条，所以这个问题在这些设备上并不明显。
*   桌面浏览器用户将窗口尺寸设置在布局明显改变的地方是不幸运的。
*   你可以通过确保垂直滚动条总是可见来防止 Chrome 切换布局，例如`body { overflow-y: scroll; }`。

也就是说，我认为这是 Webkit 和 [Blink](/blink-rendering-engine-google-chrome/) 团队解决这个问题的有效理由。我怀疑在语法成为标准之前，媒体查询已经在 Webkit 中实现了。然而，我们现在有了 W3C 推荐标准；遵循最终确定的规则并为用户和开发人员提供一致的体验是有意义的。

## 分享这篇文章