# jQuery 为任何类型的输入动态设置值

> 原文：<https://www.sitepoint.com/jquery-set-type-input-dynamically/>

快速分享一个我已经解决的问题，关于**动态设置与输入类型**无关的表单输入值。希望能帮助到外面的人。

## 问题

动态设置表单输入值对于输入、选择、复选框、单选等… **它们都有不同的设置值的方法**。所以我只寻找一个函数，它可以传递输入元素，并根据输入类型设置值。

如你所知如果你使用:input 这个抓取一个表单上的所有输入:

```
$('form :input');
```

**我在**之前看过类似的一个[自动填表器](http://www.jquery4u.com/forms/jquery-autoform/)。还有一个[字段获取和设置功能](http://www.jquery4u.com/forms/jquery-getset-script-manage-form-values/)。

获取输入的类型也相当容易:

```
$('form #input').attr('type');
```

## 使用

您将表单作为 jQuery DOM 元素传递给 field 函数，该函数带有两个参数，用于输入要获取/设置的名称和值。易如反掌！

```
$('#form').field('name', 'sam deering'); //text
$('#form').field('frequency', '20'); //select
$('#form').field('subscribe', 'true'); //checkbox
$('#form').field('language', 'jquery'); //radio
etc...
```

## 完整代码

```
//field function to get/set input values of any type of input
(function () {
    $.fn.field = function (inputName, value)
    {
        console.log('field called...');
        console.log($(this));

        console.log(typeof inputName);

        if (typeof inputName !== "string") return false;
        var $inputElement = $(this).find("[name=" + inputName + "]");
        // var $inputElement = $(this); //direct mapping with no form context

        console.log($inputElement);

        if (typeof value === "undefined" && $inputElement.length >= 1)
        {
            switch ($inputElement.attr("type"))
            {
                case "checkbox":
                    return $inputElement.is(":checked");
                    break;
                case "radio":
                    var result;
                    $inputElement.each(function (i, val) {
                        if ($(this).is(":checked")) result = $(this).val()
                    });
                    return result;
                    break;
                default:
                    return $inputElement.val();
                    break;
            }
        }
        else
        {
            switch ($inputElement.attr("type"))
            {
                case "checkbox":
                    $inputElement.attr({
                        checked: value
                    });
                    break;
                case "radio":
                    $inputElement.each(function (i) {
                        if ($(this).val() == value) $(this).attr({
                            checked: true
                        })
                    });
                    break;
                case undefined:
                    $(this).append('<input type="hidden" name="' + inputName + '" value="' + value + '">');
                    break;
                default:
                    $inputElement.val(value);
                    break;
            }
            return $inputElement;
        }
    }
})();
```

## 分享这篇文章