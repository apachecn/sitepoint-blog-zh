# jQuery 将元素放在视图前面

> 原文：<https://www.sitepoint.com/jquery-bring-element-front-view/>

要输入的快速 jquery/css 片段在其他输入的前面展开(使用 z-index 将一个元素放在另一个元素的前面)。

这主要是 css 的事情，而不是 jQuery 的事情(虽然你可以使用 jQuery 来修改 CSS)，就像这样:

```
$('element').css('zIndex', 9999);
```

或者说，绝对定位会把某些东西带到顶端:

```
$('element').css('position', 'absolute');
```

**亦见:**https://jsfiddle.net/Town/NMGTp/25/

## 分享这篇文章