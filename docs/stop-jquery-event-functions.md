# 停止 jQuery 事件函数

> 原文：<https://www.sitepoint.com/stop-jquery-event-functions/>

如何停止 jQuery 或 JavaScript 函数的一个简单方法是使用 return false 行。在大多数情况下，这不是最好的方法，还有其他函数可以使用。

**参见:** [停止 jQuery 事件功能](http://www.jquery4u.com/jquery-functions/stop-actions-jquery-preventdefault/)

## 返回错误示例

简单地使用 return false 行来停止函数，并将控制返回给调用函数或页面元素。此 jQuery 函数切换#htmlelement 的隐藏和显示，然后阻止浏览器访问所单击超链接的 href 元素

```
$("a.toggle").click(function () {
    $("#htmlelement").toggle(); // hide/show toggle
    return false; // Prevent browser from visiting the href
});
```

## 要考虑的其他变化:

*   `return false;`
*   `preventDefault()`
*   `stopPropagation`()
*   `stopImmediatePropagation()`

## 你应该使用哪种方法？

### T2`return false;`

仅在其他选项不适用或您不确定或绝望时使用。

### T2`preventDefault()`

当您需要取消默认行为时使用，比如超链接、键盘快捷键或事件等。它还要求您允许在回调中访问事件参数(在本例中，我使用`e`):

```
$("a").click(function (e) {
    // e == our event data
    e.preventDefault();
});
```

### T2`stopPropagation()`

如果您为带有子元素的容器元素指定的规则有例外，可以使用这种方法。例如，假设您禁用了网页上超链接，但特定区域中的链接除外。

```
$("a").click(function () {
   // Do nothing
});

$("#dohyperlinksdiv a").click(function (e) {
    // Don't cancel the browser's default action
    // and don't bubble this event!
    e.stopPropagation();
});
```

 

### T2`stopImmediatePropagation()`

停止所有未来绑定事件的好函数。事件将按照它们被绑定的顺序触发，当它触发 stopImmediatePropagation()函数时，所有进一步绑定的事件都不会被触发。

```
$("div a").click(function () {
   // Do something
});

$("div a").click(function (e) {
   // Do something else
   e.stopImmediatePropagation();
});

$("div a").click(function () {
   // THIS NEVER FIRES
});

$("div").click(function () {
   // THIS NEVER FIRES
});
```

## 结论

除非万不得已，否则不要使用 return false，因为它提供了很差的取消功能，并且记住在退出函数之前要防止元素的默认动作。让我们保持我们的编码漂亮整洁！

## 分享这篇文章