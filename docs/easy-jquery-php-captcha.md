# 简单的 jQuery AJAX PHP 验证码–2 分钟设置

> 原文：<https://www.sitepoint.com/easy-jquery-php-captcha/>

几分钟内设置一个有效的 AJAX 验证码。这是当你需要一个超级快速的验证码的时候用的，不要有不可读的验证码之类的痛苦。这不是一个超级难猜的验证码系统，它只是提供了一个由 PHP 生成的非常基本的验证码。使用这种方法，你可以选择你自己的尺寸，字体，颜色，背景颜色。**我还将它与 jQuery Validate 插件集成，通过远程 ajax 请求来检查验证码是否正确。**

## 特征

*   与 jQuery 验证插件集成。
*   自定义验证码字体，大小，颜色，背景色。
*   PHP 唯一生成的后端。
*   没有 reCaptcha 痛苦，设置在几秒钟内，没有 API 键需要。

## 演示

这个演示是建立在我使用 jQuery，jQuery.validate，Require.js，Backbone.js，bootstrap 创建的轻量级引导之上的。

验证码演示

![easy-jquery-php-captcha](img/8ef6354562d17ba214f2e98729503bbc.png)

## [计] 下载

GitHub 上有一个完整的工作下载包。**星和叉随你便。**:)

[在 GITHUB 上查看](https://github.com/sdeering/easy-jquery-php-captcha)

## 设置

### 超文本标记语言

使用引导标记。

```
<label class="" for="captcha">*Please enter the verication code shown below.</label>
<div id="captcha-wrap">
    <img src="/img/refresh.jpg" alt="refresh captcha" id="refresh-captcha" /> <img src="/php/newCaptcha.php" alt="" id="captcha" />
</div>
<input class="narrow text input" id="captcha" name="captcha" type="text" placeholder="Verification Code"/>
```

### jQuery

验证从 WEBAPP 对象运行，该对象缓存 DOM 元素并设置 captcha 刷新事件。我已经使用 ajax 使用了[远程验证规则](http://www.jquery4u.com/ajax/jquery-ajax-validation-remote-rule/)来检查验证码是否正确。

```
$(function()
{

    //jQuery Captcha Validation
    WEBAPP = {

        settings: {},
        cache: {},

        init: function() {

            //DOM cache
            this.cache.$form = $('#captcha-form');
            this.cache.$refreshCaptcha = $('#refresh-captcha');
            this.cache.$captchaImg = $('img#captcha');
            this.cache.$captchaInput = $(':input[name="captcha"]');

            this.eventHandlers();
            this.setupValidation();

        },

        eventHandlers: function() {

            //generate new captcha
            WEBAPP.cache.$refreshCaptcha.on('click', function(e)
            {
                WEBAPP.cache.$captchaImg.attr("src","/php/newCaptcha.php?rnd=" + Math.random());
            });
        },

        setupValidation: function()
        {

            WEBAPP.cache.$form.validate({
               onkeyup: false,
               rules: {
                    "firstname": {
                        "required": true
                    },
                    "lastname": {
                        "required": true
                    },
                    "email": {
                        "required": true
                    },
                    "captcha": {
                        "required": true,
                        "remote" :
                        {
                          url: '/php/checkCaptcha.php',
                          type: "post",
                          data:
                          {
                              code: function()
                              {
                                  return WEBAPP.cache.$captchaInput.val();
                              }
                          }
                        }
                    }
                },
                messages: {
                    "firstname": "Please enter your first name.",
                    "lastname": "Please enter your last name.",
                    "email": {
                        "required": "Please enter your email address.",
                        "email": "Please enter a valid email address."
                    },
                    "captcha": {
                        "required": "Please enter the verifcation code.",
                        "remote": "Verication code incorrect, please try again."
                    }
                },
                submitHandler: function(form)
                {
                    /* -------- AJAX SUBMIT ----------------------------------------------------- */

                    var submitRequest = $.ajax({
                         type: "POST",
                         url: "/php/dummyScript.php",
                         data: {
                            "data": WEBAPP.cache.$form.serialize()
                        }
                    });

                    submitRequest.done(function(msg)
                    {
                        //success
                        console.log('success');
                        $('body').html('<h1>captcha correct, submit form success!</h1>');
                    });

                    submitRequest.fail(function(jqXHR, textStatus)
                    {
                        //fail
                        console.log( "fail - an error occurred: (" + textStatus + ")." );
                    });

                }

            });

        }

    }

    WEBAPP.init();

});
```

### 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

**newCaptcha.php**文件根据提供的字体和颜色设置创建一个新的验证码图片。它将验证码存储在一个名为 captcha 的 PHP 会话变量中。

```
< ?php
session_start();

$string = '';
for ($i = 0; $i checkCaptcha.php - this is simple. It checks the if the code matches and returns result to the front-end.

[code language="php"]
< ?php
session_start();

if(isset($_REQUEST['code']))
{
    echo json_encode(strtolower($_REQUEST['code']) == strtolower($_SESSION['captcha']));
}
else
{
    echo 0; // no code
}
?>
```

我希望这能帮助你快速设置验证码！如果有，请留言评论！:)

## 分享这篇文章