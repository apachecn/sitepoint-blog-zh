# 在 CSS 中隐藏元素的 10 种方法

> 原文：<https://www.sitepoint.com/hide-elements-in-css/>

在 CSS 中隐藏一个元素有多种方法，但是它们对可访问性、布局、动画、性能和事件处理的影响各不相同。

## 动画

有些 CSS 隐藏选项是全有或全无。元素要么完全可见，要么完全不可见，没有中间状态。其他的，如透明度，可以有一个值的范围，所以插值 CSS 动画成为可能。

## 易接近

下面描述的每种方法都会在视觉上隐藏一个元素，但它可能会也可能不会对辅助技术隐藏内容。例如，屏幕阅读器仍然可以显示微小的透明文本。进一步的 CSS 属性或 [ARIA 属性](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)如`aria-hidden="true"`可能是描述适当动作所必需的。

请注意，动画也可能导致某些人迷失方向、偏头痛、癫痫或其他身体不适。当在用户偏好中指定时，考虑使用 [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) 媒体查询来关闭动画。

## 事件处理

隐藏将阻止在该元素上触发事件，或者没有任何效果，即该元素不可见，但仍可以被单击或接收其他用户交互。

## 表演

浏览器加载并解析 HTML DOM 和 CSS 对象模型后，页面分三个阶段呈现:

1.  **布局:**生成每个元素的几何图形和位置
2.  **画:**画出每个元素的像素
3.  **组合**:按照适当的顺序放置元素层

仅引起构图变化的效果明显比影响布局的效果更平滑。在某些情况下，浏览器也可以使用硬件加速。

## 1.`opacity`和`filter: opacity()`

可以向 [`opacity: N`](https://developer.mozilla.org/en-US/docs/Web/CSS/opacity) 和 [`filter: opacity(N)`](https://developer.mozilla.org/en-US/docs/Web/CSS/filter) 属性传递 0 到 1 之间的数字，或者 0%到 100%之间的百分比，相应地表示完全透明和完全不透明。

见笔 [隐藏不透明:0](https://codepen.io/SitePoint/pen/vYOgMKx)by site point([@ site point](https://codepen.io/SitePoint))
on[code Pen](https://codepen.io)。