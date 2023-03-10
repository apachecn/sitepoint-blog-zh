# jQuery。text()函数

> 原文：<https://www.sitepoint.com/jquery-text-function/>

此 jQuery 文本属性有两个版本:。文本()和。文本(val)。

## jQuery text()函数

语法:。文本()

功能:。text()获取所有匹配元素的所有组合文本内容。

浏览器兼容性:text()可以在我们测试过的所有浏览器中运行。

基本示例:

找到第一段中的文本(去掉 html)，然后设置最后一段的 html 以显示它只是文本(红色粗体消失)。

## jQuery 文本(val)函数

语法:。文本(val)

功能:。文本(val)类似于。html()但转义 HTML(用它们的 HTML 实体替换" ")。

浏览器兼容性:text(val)可以在我们测试过的所有浏览器上运行。

基本示例:
向段落添加文本(注意粗体标签被转义)。

```
$("p").text("**Some** new text.");
```

## jQuery text()函数的替代方法

这是一个 jQuery 函数，可以用来代替 jQuery 的。text()保留换行符。

```
(function($){
   $.fn.innerText = function(msg) {
         if (msg) {
            if (document.body.innerText) {
               for (var i in this) {
                  this[i].innerText = msg;
               }
            } else {
               for (var i in this) {
                  this[i].innerHTML.replace(/&lt;br&gt;/gi,"n").replace(/(&lt;([^&gt;]+)&gt;)/gi, "");
               }
            }
            return this;
         } else {
            if (document.body.innerText) {
               return this[0].innerText;
            } else {
               return this[0].innerHTML.replace(/&lt;br&gt;/gi,"n").replace(/(&lt;([^&gt;]+)&gt;)/gi, "");
            }
         }
   };
})(jQuery);
```

## 分享这篇文章