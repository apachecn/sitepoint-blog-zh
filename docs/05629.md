# 隐藏(HTML 属性)

> 原文：<https://www.sitepoint.com/hidden-html-attribute/>

### 描述

使用`hidden`属性(设置为“true”)，可以隐藏任何可能与嵌入内容一起出现的可视元素。通常，它可用于音频内容，其中包含属性的*而非*将导致音频下方的传输控件(播放、暂停等)可见。

### 例子

将`hidden`属性设定为`"true"`以隐藏某些音频上的传输控制:

```
<embed src="Crash.wav" *hidden="true"*></embed>
```

### 价值

仅“`true`”或“`false`”。

## 分享这篇文章