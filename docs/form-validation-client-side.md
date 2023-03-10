# 客户端的表单验证

> 原文：<https://www.sitepoint.com/form-validation-client-side/>

客户端的表单验证是必不可少的——它节省了时间和带宽，并为用户提供了更多的选项来指出他们在填写表单时的错误。说到这里，我并不是说你不需要服务器端验证。访问您网站的人可能会使用旧浏览器或禁用 JavaScript，这将破坏仅客户端验证。客户端和服务器端验证是相辅相成的，因此，它们真的不应该独立使用。

##### 为什么客户端验证是好的？

使用客户端验证有两个很好的理由:

1.  这是一种快速的验证形式:如果有问题，提交表单时就会触发警报。

3.  您可以安全地一次只显示一个错误，并关注错误的字段，以帮助确保用户正确地填写您需要的所有详细信息。

##### 两种主要的验证方法

客户端表单验证的两种主要方法是:

*   逐个显示错误，重点放在有问题的字段上
*   同时显示所有错误，服务器端验证风格

虽然服务器端验证需要同时显示所有错误，但在客户端验证的更好方法是一次显示一个错误。这使得可以只突出显示没有正确完成的字段，从而使访问者更容易修改和成功提交表单。如果你同时向用户呈现所有的错误，大多数人会试图立刻记住并改正它们，而不是试图在每次改正后重新提交。

鉴于这些优点，我将只关注一次显示一个错误的验证方法。

##### 如何验证表单

以下面的代码片段为例:

```
<script type="text/javascript" language="javascript"> 

function validateMyForm() { 

if (parseInt(document.forms[0].phone.value)  

        != document.forms[0].phone.value) { 

alert('Please enter a phone number, numbers only'); 

return false; 

} 

return true; 

} 

</script> 

<form action="handler" onsubmit="return validateMyForm();"> 

<p>Phone: <input type="text" id="phone" name="phone" /></p> 

<p><input type="submit" value="Send" /></p> 

</form>
```

这里怎么了？嗯，如果你在这个表单之前添加另一个表单，代码将试图验证错误的表单。

更好的方法是包含一个表单名称:

```
function validateMyForm() { 

if (parseInt(document.forms.myForm.phone.value)  

        != document.forms.myForm.phone.value) { 

<form id="myForm" name="myForm" action="handler"  

onsubmit="return validateMyForm();">
```

这肯定更好，但是移植性仍然不够——如果您想在另一个表单上重用这种验证，您必须首先进行大量的文本替换。

因此，让我们删除表单名称:

```
function validateMyForm(form) { 

if (parseInt(form.phone.value) != form.phone.value) { 

<form action="handler" onsubmit="return validateMyForm(this);">
```

最后一个方法使用了对象`this`，它总是指向当前对象。这使得我们的代码更具可移植性，并节省了打字。

现在让游客的生活变得更容易怎么样？让我们把重点放在触发错误的领域，而不是让他们自己找到错误。

```
function validateMyForm(form) { 

if (parseInt(form.phone.value) != form.phone.value) { 

alert('Please enter a phone number, numbers only'); 

form.phone.focus(); 

form.phone.select(); 

return false; 

}
```

有了这些改变，浏览器将聚焦于未正确填写的字段，甚至为访问者选择文本。如果需要滚动，这也会自动发生。

好的，这很好，但是你不觉得对每个领域来说都有点太多了吗？如果我们创建一个简单的函数库来节省页面的输入和下载时间会怎么样？好吧，接下来我们将这样做，我们将定义我们的基本函数，以使验证代码更短。

```
function validateNumber(field, msg, min, max) {  

if (!min) { min = 0 }  

if (!max) { max = 255 }  

if ( (parseInt(field.value) != field.value) ||   

        field.value.length < min || field.value.length > max) {  

alert(msg);  

field.focus();  

field.select();  

return false;  

}  

return true;  

}
```

该函数执行简单的数字验证—它检查字段是否只包含数字，并且可选地检查它是否在给定的范围内。您会注意到，这段代码将错误消息作为参数传递。要使用这样的函数，我们基本上可以将它添加到 onsubmit 处理程序中，就像这样:

```
<form action="handler"  

onsubmit="return validateNumber(this.phone,   

'Please enter a phone number, numbers only', 5, 10);">
```

像这样调用，它将检查电话号码是否是数字，并且多于 5 位，但少于 10 位。注意 phone 对象是如何作为参数传递的？这允许我们通过助手函数来关注它，而不是只传递字段的值。

另一种验证数字的方法是要求它们在给定的范围内。要让函数进行这种验证，只需将检查行改为:

```
if ((parseInt(field.value) != field.value) ||   

field.value < min || field.value > max) {
```

如果希望对表单应用多个检查，可以在 onsubmit 处理程序中嵌入几个规则。例如，假设除了电话号码之外，我们还需要输入名字和姓氏:

```
<form action="handler"  

onsubmit="return (  

validateNumber(this.phone, 'Please enter a phone   

        number, numbers only', 5, 10) &&  

validateString(this.firstName, 'Please   

        enter your first name', 3, 15) &&  

validateString(this.lastName, 'Please   

        enter your last name', 3, 15)  

);">
```

代码要求所有验证规则的计算结果为`true`(带有逻辑`AND - &&`)。仔细观察就会发现，从服务器脚本语言生成这种代码非常容易…但那是另外一篇文章了。

```
function validateString(field, msg, min, max) {  

if (!min) { min = 1 }  

if (!max) { max = 65535 }  

if (!field.value || field.value.length < min ||   

field.value.max > max) {  

alert(msg);  

field.focus();  

field.select();  

return false;  

}  

return true;  

}
```

如您所见，字符串验证函数看起来或多或少是一样的；你也可以写其他函数，把它们和这些结合起来。

Web 上许多表单中需要的一个公共字段是用户的电子邮件地址。我见过很多这样做的函数，但通常验证电子邮件地址最简单、最容易的方法是使用正则表达式。

现在我们将扩展我们的函数，使之有可能将字段定义为可选的。

```
function validateEmail(email, msg, optional) {  

if (!email.value && optional) {  

return true;  

}  

var re_mail = /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+.)+  

        ([a-zA-Z])+$/;  

if (!re_mail.test(email.value)) {  

alert(msg);  

email.focus();  

email.select();  

return false;  

}  

return true;  

}
```

要验证所需的电子邮件，您应该将其称为:

```
validateEmail(this.email, 'Please enter your email address')
```

如果您希望它是可选的:

```
validateEmail(this.email, 'Please enter a correct   

email address or leave the field blank', true)
```

JavaScript 不能单独用于验证，但是如果有它的话会很有帮助。嵌入到 HTML 中的代码越紧凑越好——这样可以节省下载时间，搜索引擎也会因此而喜欢你！

## 分享这篇文章