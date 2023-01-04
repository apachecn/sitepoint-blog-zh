# 快速提示:XMLHttpRequest 和 innerHTML

> 原文：<https://www.sitepoint.com/quick-tip-xmlhttprequest-and-innerhtml/>

XMLHttpRequest 是现代 DHTML 保守得最好的秘密之一。如果你以前没有遇到过，这是一种在不刷新整个页面的情况下向托管 web 服务器发出 HTTP 回调的方法——一种类固醇上的[远程脚本](http://www.ashleyit.com/rs/)。它最初是微软的扩展，已经被 Mozilla 浏览器家族和([从 1.2](http://developer.apple.com/internet/webcontent/xmlhttpreq.html) 版本开始)Safari 所采用。之前讨论过的 Sarissa 库为不同的浏览器提供了一个抽象层，或者为一个更轻量级的方法，这个来自 jibbering.com 的[代码(它利用了 IE 的](http://jibbering.com/2002/4/httprequest.html) [JScript 条件编译](http://msdn.microsoft.com/library/en-us/script56/html/js56jsconconditionalcompilation.asp))完美地工作。

充分利用 XMLHttpRequest 通常需要使用服务器端生成的 XML，您的 JavaScript 应用程序可以检索、解析并在更复杂的逻辑中使用这些 XML。但是，为了快速修复，下面的代码将从 URL 加载一个 HTML 片段，并将其直接插入到页面中:

```
 function loadFragmentInToElement(fragment_url, element_id) {
    var element = document.getElementById(element_id);
    element.innerHTML = '<p><em>Loading ...</em></p>';
    xmlhttp.open("GET", fragment_url);
    xmlhttp.onreadystatechange = function() {
        if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
            element.innerHTML = xmlhttp.responseText;
        }
    }
    xmlhttp.send(null);
} 
```

用要插入的 HTML 片段的 URL 和它应该出现的元素的 ID 调用上面的函数。它依靠 jibbering.com 代码来设置 xmlhttp 变量。

它绝对是快速而肮脏的，但是它也是 XMLHttpRequest 扩展强大功能的一个很好的快速演示。

## 分享这篇文章