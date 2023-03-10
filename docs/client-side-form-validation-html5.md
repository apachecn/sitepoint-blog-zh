# 使用 HTML5 的客户端表单验证

> 原文：<https://www.sitepoint.com/client-side-form-validation-html5/>

在构建 web 应用程序时，认真对待安全性是很重要的，尤其是当它与收集用户数据有关时。

不信任任何人是安全领域的一条常见准则，因此永远不要相信用户输入了正确或有效的表单值。例如，在电子邮件表单字段中，用户可能会输入无效的电子邮件地址或恶意数据，而不是输入有效的电子邮件地址，这显然忽略了请求的意图。

在验证表单值时，可以在客户端(web 浏览器)和服务器端(使用您喜欢的服务器端语言)完成。

在过去，客户端验证只能使用 JavaScript 或使用框架中的库来实现(想想 [jQuery 验证插件](http://jqueryvalidation.org))。但这种情况正在改变，或者说已经改变了，因为现在可以使用 HTML5 进行验证，而不必编写复杂的 JavaScript 验证代码。

## 用 HTML5 进行表单验证

HTML5 包括一个相当可靠的表单验证机制，由以下`<input />`属性提供支持:`type`、`pattern`和`require`。由于 HTML5 中的这些新属性，您可以将一些数据验证功能委托给浏览器。

让我们研究一下这些新的表单属性，看看它们如何帮助表单验证。

## `type`属性

这个表单属性指示显示哪种类型的输入控件，比如用于处理简单文本数据的流行的`<input type="text" />`。

一些表单控件继承了验证系统，而无需编写任何代码。例如，`<input type="email" />`验证字段以确保输入的数据实际上是有效的电子邮件地址。如果该字段包含无效值，则不能提交表单进行处理，直到其得到更正。

![Invalid form value](img/0d57360e2b1586a65aa91bf4b4c0fa4e.png)

通过输入无效的电子邮件来尝试下面的演示:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [邮件验证示例](http://codepen.io/SitePoint/pen/BFwhz/)。