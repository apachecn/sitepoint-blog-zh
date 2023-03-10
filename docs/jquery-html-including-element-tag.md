# jQuery 获取包含元素标记的 html

> 原文：<https://www.sitepoint.com/jquery-html-including-element-tag/>

如果你正在使用大量的 ajax 功能(我知道我是)，获取一个包含标签的元素 html 可能会有用。我敢肯定，一定有更简单的方法，而不仅仅是克隆包装器中的元素，然后使用 jQuery 获取包装器 html，以获得包含元素标记 html 的内部 html。

直到我找到更好的(更高效的方式)这里是代码片段。

```
.clone().wrap('`').parent().html();`
```

**其他方式(来自帖子评论，感谢各位):**

```
//Not sure how portable it is across browsers
$(‘#foo’)[0].outerHTML;
```

```
var foo = $(‘#bar’);
```

```
var foo = $('h1');

console.log(foo);
//output: jQuery(h1) < - DOM Element

console.dir(foo[0]);
//output: DOM element props and funcs

console.log(foo[0]['outerHTML']);
//output: `Title`
```

## 分享这篇文章