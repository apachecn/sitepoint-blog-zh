# 显示/隐藏元素的跨浏览器 css 类

> 原文：<https://www.sitepoint.com/cross-broswer-css-classes-showhide-elements/>

透明度是 CSS 属性中的一种，它有着奇怪的历史，需要许多不同的属性和值来确保跨浏览器的透明度兼容性。为了覆盖你所有的基础，你需要一堆 CSS 语句。幸运的是，它们不会互相干扰，所以每次你想增加透明度的时候都使用它们，不会有太大的麻烦，也不用担心。在这里，他们目前设置为 50%的透明度:

## CSS 只显示元素

```
/* CSS Class to show elements */

.show {

/* Required for IE 5, 6, 7 */
/* ...or something to trigger hasLayout, like zoom: 1; */
width: 100%;

/* Theoretically for IE 8 & 9 (more valid) */
/* ...but not required as filter works too */
/* should come BEFORE filter */
-ms-filter:"progid:DXImageTransform.Microsoft.Alpha(Opacity=100)";

/* This works in IE 8 & 9 too */
/* ... but also 5, 6, 7 */
filter: alpha(opacity=100);

/* Older than Firefox 1.0 */
-moz-opacity:1.0;

/* Safari 1.x (pre WebKit!) */
-khtml-opacity: 1.0;

/* Modern!
/* Firefox 0.9+, Safari 2?, Chrome any?
/* Opera 9+, IE 9+ */
opacity: 1.0;

}
```

## CSS 只隐藏元素

```
/* CSS Class to hide elements */

.hide {

/* Required for IE 5, 6, 7 */
/* ...or something to trigger hasLayout, like zoom: 1; */
width: 0%;

/* Theoretically for IE 8 & 9 (more valid) */
/* ...but not required as filter works too */
/* should come BEFORE filter */
-ms-filter:"progid:DXImageTransform.Microsoft.Alpha(Opacity=0)";

/* This works in IE 8 & 9 too */
/* ... but also 5, 6, 7 */
filter: alpha(opacity=0);

/* Older than Firefox 1.0 */
-moz-opacity:0;

/* Safari 1.x (pre WebKit!) */
-khtml-opacity: 0;

/* Modern!
/* Firefox 0.9+, Safari 2?, Chrome any?
/* Opera 9+, IE 9+ */
opacity: 0;

}
```

[CSS 功能演示](http://www.jquery4u.com/function-demos/css/)

## 分享这篇文章