# jQuery 只读输入字段

> 原文：<https://www.sitepoint.com/jquery-read-only-input-field/>

简单的 jQuery 代码片段将输入字段设置为只读，这样就没有人可以更改输入字段的值。我没见过这种用法，但它值得了解。

## 超文本标记语言

```
<input type="file" id="loadfile" name="loadfile" title="" disabled="disabled">
```

## 用 jQuery 添加只读

```
$('input').attr('readonly', true);
```

## 用 jQuery 删除只读

```
$(#input').removeAttr('readonly');
```

**不要忘记**将 jQuery 代码放在文档就绪函数中。参见 [4 个不同的 jQuery 文档就绪示例](http://www.jquery4u.com/dom-modification/types-document-ready/)了解更多信息。

## 分享这篇文章