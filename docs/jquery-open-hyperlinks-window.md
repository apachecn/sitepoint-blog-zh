# jQuery 在新窗口中打开所有超链接

> 原文：<https://www.sitepoint.com/jquery-open-hyperlinks-window/>

JavaScript 代码片段来使用 jQuery 定制你的 CSS 超链接，让它们脱颖而出。还有一个使用 jQuery 在新窗口中打开超链接的技巧。

## 在新窗口中打开超链接

```
//open all hyperlinks in a new window using jQuery
$('a[href^="http://"]')
  .attr("target", "_blank");
  $(this).attr('href', newHref).attr("target", "_blank");
```

你甚至可以通过一点点链接在你的超链接上添加图片或文本。本示例将[^]添加到链接文本的末尾。

## 自定义样式外部超链接

```
//open all hyperlinks in a new window using jQuery
//also append a "^" to every link that is external (contains "http://")
$('a[href^="http://"]')
  .attr({
    target: "_blank", 
    title: "Opens in a new window"
  })
  .append(' [^]');
```

接下来，添加一点 jQuery:

```
$(document).ready(function() {
    $("a[@rel='external']").addClass("external").
       .click(function() { window.open($(this).href); return false; });
});
```

这将查找所有带有 rel="external "的锚标记，并使它们在单击时打开一个新窗口。“返回假；”也防止当前页面切换到 URL。作为一个额外的好处，这些链接使 CSS 类成为“外部的”,这样就可以对它们进行样式化，比如:

## 向外部超链接添加背景图像

```
//change background image of external hyperlinks
A.external {
  padding-left: 15px;
  background: url(external-link.png) top left no-repeat;
}
```

## 分享这篇文章