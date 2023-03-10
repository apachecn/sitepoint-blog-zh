# 如何在带有 matchMedia 的 JavaScript 中使用媒体查询

> 原文：<https://www.sitepoint.com/javascript-media-queries/>

*这篇 2011 年的文章更新于 2018 年。*

当第一次被介绍时，[响应式设计](https://www.sitepoint.com/versioning-show-episode-16-with-ethan-marcotte/)是自 CSS 取代表格以来最令人兴奋的网页布局概念之一。底层技术使用[媒体查询](https://www.sitepoint.com/atoz-css-media-queries/)来确定查看设备的类型、宽度、高度、方向、分辨率、纵横比和颜色深度，以服务于不同的样式表。

如果你认为响应式设计只适用于 CSS 布局，你会很高兴听到媒体查询也可以用在 JavaScript 中，正如本文将解释的那样。

## CSS 中的媒体查询

在下面的示例中，cssbasic.css 服务于所有设备。但是，如果它是一个水平宽度为 500 像素或更大的屏幕，csswide.css 也会被发送:

```
<link rel="stylesheet" media="all" href="cssbasic.css" />
<link rel="stylesheet" media="(min-width: 500px)" href="csswide.css" />
```

可能性是无穷无尽的，这种技术早已被互联网上的大多数网站所利用。调整浏览器的宽度会触发网页布局的变化。

有了**媒体查询**现在在 CSS 中修改设计或调整元素大小变得很容易。但是如果您需要更改内容或功能呢？例如，在较小的屏幕上，您可能希望使用较短的标题、较少的 JavaScript 库，或者修改小部件的动作。

可以用 JavaScript 分析视口大小，但是有点混乱:

*   大多数浏览器都支持`window.innerWidth`和`window.innerHeight`。(即 10 版本之前在怪癖模式下需要`document.body.clientWidth`和`document.body.clientHeight`。)
*   `window.onresize`
*   所有主流浏览器都支持`document.documentElement.clientWidth`和`document.documentElement.clientHeight`但是不一致。根据浏览器和模式，将返回窗口或文档尺寸。

即使成功检测到视口尺寸变化，您也必须自己计算方向和纵横比等因素。当它在 CSS 中应用媒体查询规则时，不能保证它会符合您的浏览器的假设。

## 如何用 JavaScript 代码编写媒体查询

幸运的是，可以在 JavaScript 中响应 CSS3 媒体查询状态的变化。[关键 API 是`window.matchMedia`](https://developer.mozilla.org/en/docs/Web/API/Window/matchMedia) 。传递给它的媒体查询字符串与 CSS 媒体查询中使用的字符串相同:

```
const mq = window.matchMedia( "(min-width: 500px)" );
```

根据查询结果，`matches`属性返回 true 或 false。例如:

```
if (mq.matches) {
// window width is at least 500px
} else {
// window width is less than 500px
}
```

您还可以添加一个事件侦听器，该侦听器在检测到更改时触发:

```
// media query event handler
if (matchMedia) {
const mq = window.matchMedia("(min-width: 500px)");
mq.addListener(WidthChange);
WidthChange(mq);
}

// media query change
function WidthChange(mq) {
if (mq.matches) {
// window width is at least 500px
} else {
// window width is less than 500px
}

}
```

您还应该在定义事件后直接调用处理程序。这将确保您的代码可以在页面加载期间或之后进行自我初始化。如果没有它，如果用户不改变他们的浏览器尺寸，`WidthChange()`将永远不会被调用。

在撰写本文时， [`matchMedia`拥有优秀的浏览器全面支持](http://caniuse.com/#feat=matchmedia)，所以你没有理由不能在生产中使用它。

在下面的演示中，检查当你调整浏览器窗口大小时，文本是如何从超过 500 像素的*动态变化到小于 500 像素的*的。或者，[下载样本代码](https://blogs.sitepointstatic.com/examples/tech/jsmediaqueries/jsmediaqueries.zip):**

 **参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )用 JavaScript 进行的笔 [CSS 媒体查询。](https://codepen.io/SitePoint/pen/yoyXoM/)**