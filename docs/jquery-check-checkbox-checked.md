# jQuery 检查复选框是否被选中

> 原文：<https://www.sitepoint.com/jquery-check-checkbox-checked/>

有许多不同的方法来检查复选框是否被选中。您可以使用 jQuery 或普通 JavaScript，这并不重要。这里有 3 个例子来满足你的胃口。当您需要在提交表单之前检查用户是否勾选了复选框时，这可能很有用。

## 检查是否选中了复选框

```
// First way 
$('#checkBox').attr('checked'); 

// Second way 
$('#edit-checkbox-id').is(':checked'); 

// Third way for jQuery 1.2
$("input[@type=checkbox][@checked]").each( 
    function() { 
       // Insert code here 
    } 
);
// Third way == UPDATE jQuery 1.3
$("input[type=checkbox][checked]").each( 
    function() { 
       // Insert code here 
    } 
);
```

## 如果复选框未选中，请选中

```
if ($('input[name=termsckb]').attr('checked') != true) {
	$('#captcha-wrap').hide();
}
```

或者

```
if ($('#email_ckb').attr('checked') != true) {
	$('#captcha-wrap').hide();
}
```

## 分享这篇文章