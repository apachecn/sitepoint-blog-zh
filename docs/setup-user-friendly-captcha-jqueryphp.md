# 使用 jQuery/AJAX/PHP 轻松设置验证码

> 原文：<https://www.sitepoint.com/setup-user-friendly-captcha-jqueryphp/>

![recaptcha2](img/7c5298ed20b801d2d1b273c607262f47.png "recaptcha2")

## 什么是验证码？

如今，当你使用表单时，你需要某种保护来抵御僵尸工具和垃圾邮件发送者。减少垃圾邮件的一种方法是使用一种叫做 Captcha 的东西——它简单地将人类可读的单词放入一个盒子中，用户必须将其键入以证明他们是人类。有很多免费的，我用过一些，发现 Google reCaptcha 非常容易安装和使用。

[jQuery 验证码演示](http://blogoola.com/signup/)
[下载源文件](https://www.sitepoint.com/wp-content/uploads/jquery4u/2011/03/jquery4ucaptcha.zip)

*   **jquery4ucaptcha.zip**
    *   showform.php
    *   jquerycaptcha.js
    *   validateform.php
    *   recaptchalib.php

## 它是如何工作的

![captcha-diagram](img/dd6c6ba886c9827249d8c94076e79bdb.png "captcha-diagram")

## 一个常见的问题解决了

用户输入了验证码，但输入错误。现在，当他们单击“返回”时，他们会丢失刚刚填写的字段中的所有表单数据！灾难！幸运的是，我想出了一种发送 AJAX 请求的方法，这样即使验证码错误，表单输入数据也不会丢失。如果验证码是正确的，用户只是被提示提交。

## 如何设置你的表单验证码

**第一步。**你需要从[谷歌 reCaptcha 网站](http://www.google.com/recaptcha)获得自己的设置密钥。你将得到一个**私钥**和一个**公钥**，你需要这两个密钥来工作。

**第二步。**下载并保存[recaptchalib.php](http://code.google.com/p/recaptcha/downloads/list?q=label:phplib-Latest)。

**第三步。jQuery 代码-jquerycaptcha . js**

```
//Validate the Recaptcha' Before continuing with POST ACTION
function validateCaptcha()
{
	challengeField = $("input#recaptcha_challenge_field").val();
	responseField = $("input#recaptcha_response_field").val();

	var html = $.ajax({
		type: "POST",
		url: "../php/validateform.php",
		data: "form=signup&recaptcha_challenge_field=" + challengeField + "&recaptcha_response_field=" + responseField,
		async: false
		}).responseText;

	//console.log( html );
	if(html == "success") {
		//Add the Action to the Form
		$("form").attr("action", "../php/db-process-form.php");
		$("#submit").attr("value", "Submit");
		//Indicate a Successful Captcha
		$("#captcha-status").html("

成功！谢谢，你现在可以继续了。

");
	} else {
		$("#captcha-status").html("

您输入的安全码不匹配。请再试一次。

");
		Recaptcha.reload();
	}
}
```

**第四步。PHP 代码 1–validateform.php**

```
require_once("recaptchalib.php");

$privatekey = "[yourprivatekeyhere]";
$resp = recaptcha_check_answer ($privatekey,
         $_SERVER["REMOTE_ADDR"],
         $_POST["recaptcha_challenge_field"],
         $_POST["recaptcha_response_field"]);

if (!$resp->is_valid) {
	// What happens when the CAPTCHA was entered incorrectly
    echo "fail";
} else {
	echo "success";
}
```

**第五步。PHP 代码 2–showcaptcha.php**

```
require_once("recaptchalib.php");
$publickey = "[yourpublickeyhere]";
// show the captcha
echo recaptcha_get_html($publickey);
```

**第六步。HTML 代码**

```
 I agree to the terms-and-conditions Terms and Conditions <input type="checkbox" id="termsckb" name="termsckb">  `在下面输入单词(用空格隔开):` 

**第七步。一个不错的选择** 
一个不错的选择是隐藏验证码，直到用户填写完表格或者点击同意条款和条件按钮。这是你怎么做的。

```
$(document).ready(function() {
	if ($('input[name=termsckb]').attr('checked') != true) {
		$('#captcha-wrap').hide();
	}

	$('#termsckb').change(function() {
		 if ($('input[name=termsckb]').attr('checked') == true) {
			 $('#captcha-wrap').show();
			 $("#signupbutton").attr("value", "I am human!");
		 }
		 else {
			 $('#captcha-wrap').hide();
			 $("#signupbutton").attr("value", "Submit");
		 }
	});
});
```

**第八步。定制验证码风格** 
你可以改变验证码的风格和颜色，以适应你的网站。在官方网站上有 4 种不同的选择:红色、白色、黑色和透明。

参见[定制 reCAPTCHA 的外观和感觉](http://code.google.com/apis/recaptcha/docs/customization.html)了解更多信息。我个人更倾向于 clear(透明)选项。

## 分享这篇文章

```