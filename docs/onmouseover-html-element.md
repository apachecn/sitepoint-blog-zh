# onmouseover (HTML 元素)

> 原文：<https://www.sitepoint.com/onmouseover-html-element/>

## 描述

onmouseover 属性是最常用的事件属性之一。它捕获光标穿过元素边界的时刻，从应用属性的元素的外部移动到内部。它不同于 [onmousemove](https://reference.sitepoint.com/html/event-attributes/onmousemove) 属性，后者用于检测元素边界内的移动。一旦光标定位在元素上，onmouseover 事件将保持活动状态，直到光标移动到元素边界之外——一个由 [onmouseout](https://reference.sitepoint.com/html/event-attributes/onmouseout) 属性捕获的事件。

onmouseover 属性主要用于呈现视觉效果，如图像交换或颜色变化，这种方式在 JavaScript 出现的时候就已经开始使用了。

请注意，此事件属性不能应用于以下元素:

*   小应用程序
*   基础
*   基本字体
*   bdo
*   英国铁路公司
*   字体
*   设计
*   框式支架
*   头
*   超文本标记语言
*   内联框架
*   isindex
*   自指的
*   参数
*   脚本
*   风格
*   标题

## 例子

下面的例子展示了一个简单的
图像交换技术，将鼠标放在图像上可以使图像
变为显示地图上某个位置的图像:

```
<div>
  <img src="map.gif" alt="Hover to reveal the location on the map"
      *onmouseover="this.src='map_location_revealed.gif';"*
      onmouseout="this.src='map.gif';"/>Figures for February’s racing.
</div>
```

## 价值

该属性没有固定值。这里包含的脚本由作者决定，是调用一个或多个已定义的函数，还是简单的 alert()语句。

## 和睦相处

| 微软公司出品的 web 浏览器 | 火狐浏览器 | 旅行队 | 歌剧 |
| --- | --- | --- | --- |
| <abbr title="Internet Explorer 5.5">5.5</abbr> | <abbr title="Internet Explorer 6.0">6.0</abbr> | <abbr title="Internet Explorer 7.0">7.0</abbr> | <abbr title="Firefox 1.0">1.0</abbr> | <abbr title="Firefox 1.5">1.5</abbr> | <abbr title="Firefox 2.0">2.0</abbr> | <abbr title="Safari 1.3">1.3</abbr> | <abbr title="Safari 2.0">2.0</abbr> | <abbr title="Safari 3.0">3.0</abbr> | <abbr title="Opera 9.2">9.2</abbr> | <abbr title="Opera 9.5">9.5</abbr> |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 |

列出的所有浏览器都支持该属性。但是，应该避免这样的内联事件处理程序。就像内联 CSS 样式不受欢迎，但外部定义的 CSS 样式被认为是良好的实践一样，内联事件处理程序应该被剥离，并替换为通过 DOM 不引人注目地附加的事件。

## 分享这篇文章