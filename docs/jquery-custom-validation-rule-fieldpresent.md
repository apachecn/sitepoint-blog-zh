# jQuery 自定义验证规则–field present

> 原文：<https://www.sitepoint.com/jquery-custom-validation-rule-fieldpresent/>

这篇文章展示了如何使用 [jQuery.validate.js](http://bassistance.de/jquery-plugins/jquery-plugin-validation/) 插件向表单添加自定义验证规则。这篇文章支持在 2 分钟内使用 jQuery 验证[设置表单。我使用了](http://www.jquery4u.com/forms/basic-jquery-form-validation-tutorial/) [$.validator.addMethod()](http://docs.jquery.com/Plugins/Validation/Validator/addMethod#namemethodmessage) 函数来设置定制的验证规则。**我在**下面加了一个现场演示。

**更新日期:2013 年 6 月 30 日:**添加了出生日期的自定义验证规则。

## 例子

**下面的例子**显示了一个自定义验证规则，它检查姓名和电子邮件是否都存在。如果姓名存在，则电子邮件必须存在，反之亦然。

左侧字段验证示例。
![b5f8ddf05cc9e0d77165aab60fe3adec[1]](img/b1d8843ef20223ff6f151b29d3e0a5b1.png)

右侧字段验证示例。
![4f5d3b79c2a2c8284b4b23b785e4f893](img/972720c2a0be6e5321400f99bf960047.png)

两者都在多对上起作用的例子。
![fb3c37c4856ac43787f233f40d40a973](img/d583f5c0b4166f0ae840f91afbb5a3bf.png)

## 现场演示

[https://jsfiddle.net/jquery4u/g2tTC/show/](https://jsfiddle.net/jquery4u/g2tTC/show/)

[在 jsfiddle 中编辑](https://jsfiddle.net/jquery4u/g2tTC/)

## JQUERY

在文章的最后有一个这个片段的补丁版本，它修复了一些跨浏览器和验证递归问题。

```
$.validator.addMethod("fieldPresent", function (value, element, options)
{
    //we need the validation error to appear on the correct element
    var targetEl = $('input[name="'+options.data+'"]'),
        bothEmpty = (value == targetEl.val() == '');

    //trigger error class on target input
    (bothEmpty) ? targetEl.addClass('error') : targetEl.removeClass('error');
    return !bothEmpty;
},
    "Friend's name and email required."
);

$('#myForm').validate({
    onkeyup: true,
    rules: {
        "friend1-name": { "fieldPresent": { data: "friend1-email" } },
        "friend1-email": { "fieldPresent": { data: "friend1-name" } }             
    },
    submitHandler: function(form) {

        console.log('passed validation.');
        //submit form handler
    }
});
```

## 超文本标记语言

```
 <label class="control-label" for="friend1-name">Friend's name</label>

            <input type="text" name="friend1-name" placeholder="Friend's name... ">

        <label class="control-label" for="friend1-email">Email Address</label>

            <input type="text" name="friend1-email" placeholder="Email Address... "> 
```

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

```
.control-group {
    width: 100%;
}

.control-group-inner {
    width: 50%;
    float: left;
    display: inline-block;
}
```

如果上面的 jQuery 代码片段不起作用，这里有一个更新的版本来消除一些错误。有点想避免超时，因为它们是糟糕的编码实践…

```
//custom validation: each friend entered must have an email and a name
$.validator.addMethod("fieldPresent", function (value, element, options)
{
    //we need the validation error to appear on the correct element
    var targetEl = $('input[name="'+options.data+'"]'),
        targetEmpty = (targetEl.val() == ''),
        elEmpty = (value == ''),
        bothEmpty = elEmpty && targetEmpty;

    //trigger error class on target input
    if (!bothEmpty && targetEmpty)
    {
        //error msg doesn't exist yet so wait...
        setTimeout(function()
        {
            if (targetEl.closest('.control-group-inner').find('label.fieldPresentError').length == 0)
            {
                targetEl.addClass('error');
                if (!elEmpty) $(element).closest('.control-group-inner').find('label.fieldPresentError').remove();
                targetEl.closest('.control-group-inner').find('label.fieldPresentError').remove();
                targetEl.after("<label class="error fieldPresentError">Friend's name and email required.</label>");
            }
        }, 500);
    }

    return (bothEmpty || !elEmpty);
},
    "Friend's name and email required."
);
```

## 验证出生日期(3 次输入)

如何验证包含日、月和年 3 个输入的 dob。您需要添加一个自定义验证规则。

```
//custom validation for dob
$.validator.addMethod("dobValid", function (value, element, options)
{
    //we need the validation error to appear on the correct element
    var day = $('input[name="dob-day"]'),
        month = $('input[name="dob-month"]'),
        year = $('input[name="dob-year"]'),
        anyEmpty = ( day.val() == '' || month.val() == '' || year.val() == '' );

    if (anyEmpty) {
        day.add(month).add(year).addClass('error');
    }
    else {
        day.add(month).add(year).removeClass('error');
    }

    return !anyEmpty;
},
    "Please enter your date of birth."
);

...

$form.validate({
    rules: {

        ...

        "dob-year": { required: "required", dobValid: true }

        ...

    },
    messages: {

        ...

    },
    submitHandler: function(form) {

        ...

    }
});
```

## 分享这篇文章