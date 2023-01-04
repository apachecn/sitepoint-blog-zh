# jQuery Ajax 验证使用远程规则

> 原文：<https://www.sitepoint.com/jquery-ajax-validation-remote-rule/>

我刚刚发现 jQuery 验证插件有一个名为“remote”的验证规则，它可以用来**发出 ajax 请求，而不是指定一个自定义规则**，ajax 调用它。使用那些**自定义验证规则**，这将为你节省大量时间。[基础 jQuery 表单验证教程(2 分钟)](http://www.jquery4u.com/forms/basic-jquery-form-validation-tutorial/)。

**示例:**检查用户的电子邮件是否已经注册。

## 新的远程方法方式

如您所见，要传递数据，您可以简单地使用密钥对语法，因此在数据下方发送的请求是“&emails=email@jquery4u.com”。后端脚本的返回值要么是 json 编码的 true(验证通过),要么是 html msg(验证失败)。

```
//VALIDATE USER EMAIL
$(':input[name="uAcc"]').rules("add",
{
  "remote" :
  {
      url: 'validateEmail.php',
      type: "post",
      data:
      {
          emails: function()
          {
              return $('#register-form :input[name="email"]').val();
          }
      }
  }
});
```

## 老添加自定义方法方式

```
//VALIDATE USER EMAIL
$.validator.addMethod("validateUserEmail", function(value, element)
{
    var inputElem = $('#register-form :input[name="email"]'),
        data = { "emails" : inputElem.val() },
        eReport = ''; //error report

    $.ajax(
    {
        type: "POST",
        url: validateEmail.php,
        dataType: "json",
        data: data,
        success: function(data)
        {
            if (data !== 'true')
            {
              return '

此电子邮件地址已经注册。

';
            }
            else
            {
               return true;
            }
        },
        error: function(xhr, textStatus, errorThrown)
        {
            alert('ajax loading error... ... '+url + query);
            return false;
        }
    });

}, '');

$(':input[name="email"]').rules("add", { "validateUserEmail" : true} );
```

## 后端 PHP 脚本

```
real_escape_string($_POST['email']);
    $query = "SELECT ID FROM users WHERE user_email = '{$email}' LIMIT 1;";
    $results = $mysqli->query($query);
    if($results->num_rows == 0)
    {
        echo "true";  //good to register
    }
    else
    {
        echo "false"; //already registered
    }
}
else
{
    echo "false"; //invalid post var
}

?>
```

## 另一个有帮助的例子

```
/* register script */

(function($,W,D,undefined)
{
    $(D).ready(function()
    {

         //form validation rules
         $("#register-form").validate({
             rules:
             {
                email:
                {
                    required: true,
                    email: true,
                    "remote":
                    {
                      url: 'validateEmail.php',
                      type: "post",
                      data:
                      {
                          email: function()
                          {
                              return $('#register-form :input[name="email"]').val();
                          }
                      }
                    }
                },
                name:
                {
                    required: true,
                    minlength: 3
                },
                password:
                {
                    required: true,
                    minlength: 8
                },
                password_repeat:
                {
                    required: true,
                    equalTo: password,
                    minlength: 8
                }
             },
             messages:
             {
                 email:
                 {
                    required: "Please enter your email address.",
                    email: "Please enter a valid email address.",
                    remote: jQuery.validator.format("{0} is already taken.")
                 },
                 name: "Please enter your name.",
                 password: "Please enter a password.",
                 password_repeat: "Passwords must match."
             },
             submitHandler: function(form)
             {
                form.submit();
             }
         });

    });

})(jQuery, window, document);
```

### 键入时停止远程验证

默认情况下，你每按一次键，验证插件就会发送一个远程规则的 ajax 请求，导致发送太多的 ajax 请求来验证字段。禁用它的一种方法是停用 onkeyup 验证，这样一旦您在输入中键入了，就只能通过 ajax 验证**远程规则。**

```
$("#register-form").validate({
   onkeyup: false //turn off auto validate whilst typing
});
```

## 分享这篇文章