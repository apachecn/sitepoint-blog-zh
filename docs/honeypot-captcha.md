# 蜜罐验证码

> 原文：<https://www.sitepoint.com/honeypot-captcha/>

![honey-pot](img/0b98936ef2727d2e8bf7b0e325b4a5d7.png "honey-pot")

有趣的想法验证码，这是它是如何工作的。创建一个应该留空的蜜罐表单域，然后使用 CSS 对人类用户而不是机器人隐藏它。机器人会填写输入，认为这是一个适当的领域，但用户不会(它将被隐藏，因此保持空白)。

```
 If you see this, leave this form field blank 
and invest in CSS support.
<input type="text" name="honeypot" value=""> 
```

现在，在您的代码中，您可以检查以确保蜜罐字段为空。

```
if($('#honeypot-div input').val() == '') {
	//is human
} else {
	//probably a bot (unless a auto fill script was run)
}
```

这有点像隐形验证码的想法，但它真的很容易实现。PS–我还没有尝试过这个，这只是一个想法，但是如果有人尝试过，你能让我知道它有多好吗？

## 分享这篇文章