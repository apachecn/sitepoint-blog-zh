# jQuery 检查是否存在垂直滚动

> 原文：<https://www.sitepoint.com/jquery-check-vertical-scroll-present/>

简单的 jQuery 代码片段到**返回 true 或 false 来检查主窗口垂直滚动条是否存在**。对于当用户滚动到页面底部时触发事件非常有用，例如显示相关页面。


```
//checks if vertical scroll is present
//works in FF8 also
verticalScrollPresent: function()
{
  return (document.documentElement.scrollHeight !== document.documentElement.clientHeight);
}
```

```
//a longer winded version of the above
verticalScrollPresent: function()
{
  //return (document.documentElement.scrollHeight !== document.documentElement.clientHeight);

  // Get the computed style of the body element
  var cStyle = document.body.currentStyle||window.getComputedStyle(document.body, "");

  // Check the overflow and overflowY properties for "auto" and "visible" values
  hasVScroll = cStyle.overflow == "visible"
               || cStyle.overflowY == "visible"
               || (hasVScroll && cStyle.overflow == "auto")
               || (hasVScroll && cStyle.overflowY == "auto");

  return !hasVScroll;
}
```

## 分享这篇文章