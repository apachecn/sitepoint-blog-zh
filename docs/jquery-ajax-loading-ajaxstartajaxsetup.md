# 使用 ajaxStart/ajaxSetup 加载 jQuery Ajax

> 原文：<https://www.sitepoint.com/jquery-ajax-loading-ajaxstartajaxsetup/>

关于如何使用 jQuery.ajaxStart 和 jQuery.ajaxStop 函数的快速 jQuery 代码片段示例**。下面的例子。
**

## 使用 ajaxStart 和 ajaxStop 方法

在下面的例子中，我们用它来显示一个正在加载的图像，并在 ajax 请求被处理时禁用一个点击的表单提交按钮。然后当它返回时，图像被隐藏，提交按钮再次变为可用。

```
var $form = $('#form');

$form.find('.loading')
  .hide()
  .ajaxStart(function()
  {
      $(this).show();
      $form.find('.submit').attr('disabled', 'disabled');
  })
  .ajaxStop(function()
  {
      $(this).hide();
      $form.find('.submit').removeAttr('disabled');
  });
```

## 在 ajax 之前使用插入的代码

另一种方法可能是在 ajax 调用之前添加图像。

```
//show loading image, disable submit button
$form.find('.msg').remove();
$form.find('.loading').show();
$form.find('.submit').attr('disabled', 'disabled');
```

然后向 ajax 函数添加一个完整的处理程序。

```
//hide loading image, enable submit button again
complete: function()
{
    $form.find('.loading').hide();
    $form.find('.submit').removeAttr('disabled');
}
```

## 使用 ajaxSetup()

另一种方法是使用 jQuery.ajaxSetup，以便当所有 ajax 请求都返回“完成”时，隐藏图像并启用表单。

```
$.ajaxSetup( {
   complete:function() {
      $form.find('.loading').hide();
      $form.find('.submit').removeAttr('disabled');
   }
});
```

## 分享这篇文章