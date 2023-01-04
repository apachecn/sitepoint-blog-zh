# jQuery 获取可见元素的 id

> 原文：<https://www.sitepoint.com/jquery-id-visible-element/>

获取可见元素 id 的 jQuery 代码段。

使用可见选择器获取可见元素。

```
:visible
```

使用 not 选择器获取不可见的元素。

```
:not(:visible)
```

例如，获取当前视图中表单的 id。

```
var $visibleForm = $('form:visible'),
    formId = $visibleForm.attr('id');
console.log(formId);
```

## 分享这篇文章