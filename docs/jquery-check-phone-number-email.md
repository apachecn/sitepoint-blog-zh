# 一个 jQuery 脚本，用于检查字符串是电话号码还是电子邮件地址

> 原文：<https://www.sitepoint.com/jquery-check-phone-number-email/>

下面是一个简单的 jQuery 代码片段，用于检查字符串是电话号码还是电子邮件地址。这对于检查表单验证非常有用。然后，您可以有一个名为“电子邮件或电话”的输入，然后在提交时检查值，如果是电话号码，然后将值设置为电话输入，并在表单提交前清除电子邮件输入。

## jQuery 代码

```
//validate name
var name = $('input[name="name"]').val();
if (name.length Phone or email
[js]
//check if they entered a phone number OR email address
var ep_emailval = $('#email').val();
var intRegex = /[0-9 -()+]+$/;
if(intRegex.test(ep_emailval)) {
   console.log("is phone");
   //copy value to phone field
   $('#phone').attr('value',ep_emailval);
   //clear email field
   $('#email').attr('value','');
}
```

### HTML 代码

```
//check if they entered a phone number OR email address
<input type="text" id="email" name="email">
<input type="text" id="phone" name="phone">
```

## 分享这篇文章