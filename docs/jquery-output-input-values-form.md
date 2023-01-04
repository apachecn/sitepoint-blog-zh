# jQuery 输出表单中的所有输入值

> 原文：<https://www.sitepoint.com/jquery-output-input-values-form/>

简单的 jQuery 代码片段，用于输出具有特定 id 的表单中输入元素的所有值。您可以通过这种方式获得属性名和值。

```
console.log("----------------------------------");
$('#form-id > input,#form-id > textarea').each(function(index) {
    console.log($(this).attr('name') + " = " + $(this).val());
});
console.log("----------------------------------");
```

**注意:**[console . log()命令](http://www.jquery4u.com/utilities/live-jquery-debugging-firebug/)仅用于 firebug。

## 分享这篇文章