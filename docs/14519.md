# 使用 DHTML 轻松进行批量更改

> 原文：<https://www.sitepoint.com/easy-bulk-changes-with-dhtml/>

安迪·克拉克发表了一项名为[修剪表单字段](http://www.stuffandnonsense.co.uk/archives/trimming_form_fields.html)的巧妙新技术，它使用漂亮而不引人注目的 DHTML 来允许用户切换可选表单字段的可见性，以便更容易地完成表单。Andy 的代码非常整洁，你应该去读一读他的教程，因为我正准备对它进行扩展。

Andy 的代码循环遍历页面上的每个 div，查找那些带有 fm-optional 类的 div，并切换它们的显示值。这很好，但是实际上有一个更有效的方法来达到同样的效果。不要切换一大堆单独的元素，而是在包含所有您想要切换的元素的元素上更改类，并使用一个简单的 CSS 选择器来定位所包含的元素。

我已经在本页展示了这种替代技术[(改编自 Andy 的例子)。这是 CSS:](http://simon.incutio.com/code/js/bulk-change/)

 `form.remove div.fm-optional {
display: none;
}`

和链接事件处理程序:

 `toggle.onclick = function() {
if (/remove/i.exec(this.firstChild.nodeValue)) {
this.firstChild.nodeValue = 'Display optional fields?';
document.getElementById('example-form').className = 'remove';
} else {
this.firstChild.nodeValue = 'Remove optional fields?';
document.getElementById('example-form').className = 'display';
}
return false;
}`

上面的代码当然可以改进——例如，它没有处理表单有一个或多个需要维护的现有类的可能性。它很好地演示了如何通过切换包含元素上的单个类来对文档进行批量更改。

## 分享这篇文章