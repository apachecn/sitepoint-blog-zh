# jquery 验证出生日期格式 dd-mm-yy

> 原文：<https://www.sitepoint.com/jquery-validate-date-birth-format-dd-mm-yy/>

dob 到特定格式的 jquery 验证，即 dd-mm-yy

```
$.validator.addMethod("dateFormat",
    function(value, element) {
        return value.match(/^dd?-dd?-dd$/);
    },
    "Please enter a date in the format dd-mm-yyyy.");
And then on your form add:
$('#myForm')
    .validate({
        rules : {
            myDate : {
                dateFormat: true
            }
        }
    });
```

## 分享这篇文章