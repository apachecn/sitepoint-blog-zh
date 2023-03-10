# Dr 设计–SSIs、Java Scrollers 等！

> 原文：<https://www.sitepoint.com/design-ssis-java-scrollers/>

手术开始了，今天设计博士面临着一些有趣的挑战，从表单验证到 SSIs。让我们开始吧…别忘了[把你自己的问题提交给设计博士](mailto:drdesign@sitepoint.com)。

##### 我需要确认！

你好，设计博士，
**在我的网站上，我有简单的反馈和订阅表格，可以通过电子邮件发送给我，也可以写入文件。我的问题是，如果用户在我的表单页面上按下回车键，它就会提交。因此，我最终会收到数百个空白的反馈页面，而且我永远无法确定用户是否打算提交一些东西。**

另外，我的表单结果写着“用户未知”，除非发件人填写电子邮件地址文本框。有没有办法用代码收集寄件人的地址？谢谢，
**金 L.**

Kim，我认为如果我们执行一些简单的验证，这个问题很容易解决。验证是确保用户发送或您接收的数据“有效”的过程。您遇到的一些常见问题是用户不小心按了“enter ”,没有填写必填字段，验证将会阻止这种情况。

虽然我们可以通过简单地移除`<input type= "submit">` 并将其更改为`<input type="button" value="Submit" onClick="this.form.submit();">`来禁用“输入”键，但我不认为这是最佳做法。虽然这可以解决您眼前的问题，但它也会消除许多访问者用来提交表单的主要方法，这将非常令人沮丧。

我们想做的是减少每个人(你和你的用户)的挫折感。因此，让我们保持提交按钮不变，取而代之的是，向您的`<form>` 标签添加一点额外的代码:

```
<form method="POST" action="myfile.cgi"  

onSubmit="return doValidate(this);">
```

接下来，您需要编写 doValidate 函数。这里有一个示例，您可以将它放在文档的`<HEAD>`中:

```
<script language="javascript" type="text/javascript"> 

<!-- 

function doValidate(theform) 

{ 

if(theform.txtname.value=="" || theform.txtemail.value=="" || 

theform.subject.value=="" || theform.message.value=="") 

{ 

alert("Please complete all form fields."); 

return false; 

} 

} 

--> 

</script>
```

这个例子假设表单中有 4 个必填字段:txtname、txtemail、subject 和 message。您可以通过在任何需要的地方添加`theform.myfield.value=""`来简单地验证必填字段。或者你可以变得稍微复杂一些，例如，确保电子邮件地址的格式是有效的，等等。

基本上，这使您能够确保您接收的数据是“真实的”，并将用户的烦恼降至最低。如果您愿意，您甚至可以扩展上面的函数，向用户提供缺少哪些字段的信息。

##### 用 CSS 改变颜色

医生，我的网站大约有 30 页，它们都需要改变颜色，所以我决定用 CSS 来做。有没有可能制作一个拥有一个表格所有属性的 CSS 代码？我如何将它包含在 CSS 样式中？—詹姆斯

嘿，詹姆斯，这段代码应该可以帮助你定义你的桌子的整体外观:

```
<style type="textcss">  

table { background: black; border: thin solid red; }  

td { background: green; }  

.bodyline { background-color: #2E425A; border: 1px #597795 solid; }  

</style> 
```

然后，要在样式中包含它，只需在每个表中包含 class="bodyline ",如下所示:

```
<table class="bodyline">
```

希望这有所帮助！

##### 服务器端包括

医生，我要从 FP2002 转到 DW MX。

FP2002 的一个有用的特性是能够使用一个叫做 Webbot 的 FP 特有的特性。我用过的主要 Webbot 叫做“include”。它允许你获取经常重复的内容，并将其存储在自己的文件中。然后，每当您想在另一个页面中使用该内容时，您只需“包含”它。我想知道是否有一种使用标准编码的方法可以做到这一点？—詹姆斯

詹姆斯，

我完全明白你的意思。当您处理像菜单甚至页面标题和版权这样的标准事物时，这是一个非常有用的特性:这些项目不会经常改变，但如果改变，可能需要几天才能修复。

Dreamweaver 确实允许您通过称为库项目的东西来实现这一点。然而，我的建议是使用某种服务器端处理。这有很多优点(例如，服务器端的处理被搜索引擎蜘蛛索引)，而且，这种方法在每种编程语言中都很常见，因为它使文件更短，更容易管理！

每种编程语言都有自己的包含方式，所以您可能希望查看特定于您使用的语言的参考资料。然而，有一个更简单的方法:你可以使用 SSI(服务器端包含)，这是大多数 Web 主机允许你使用的一个特性。

基本上，您将想要包含的信息保存在一个文本文件中(例如，站点菜单的“menu.txt”)。接下来，使用以下代码包含该文件:

```
<!--#include file="menu.txt"-->
```

唯一的问题是你需要把你的文件扩展名改成。shtm 或者。shtml，这样您的服务器就知道文件中包含了服务器指令。经过这个简单的步骤，您的网站将是一个维护的梦想，而不是一个噩梦！

和 SSIs 玩的开心！

##### 滚动新闻横幅

**您好，医生，我是一级方程式赛车网站的负责人，我正在寻找创建新闻横幅的 HTML 代码。外观和速度并不重要，但是任何建议都会很有帮助。非常感谢——汤姆**

Tom，听起来你在寻找一个简单的 JavaScript“新闻滚动条”,它可以让你节省空间，同时为你的访问者提供最新的信息。

像 [Dynamic Drive](http://www.dynamicdrive.com) 、【JavaScript.com】T2 和 [JS-Examples](http://www.js-examples.com) 这样的网站都包含了这类工具的很好的例子。我相信你会在这些资源中找到你想要的东西。

传播爱！
设计博士

*下个月，当“设计博士”的手术再次开始时，你的问题有了答案。到时候见！*

## 分享这篇文章