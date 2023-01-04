# jQuery ASP.NET 更新面板文档就绪

> 原文：<https://www.sitepoint.com/jquery-asp-net-updatepanel-document-ready/>

jQuery 代码片段强制 jQuery 强制在所有回发上调用 pageLoad()函数，即使使用更新面板也是如此。只需用页面加载功能替换 document.ready。

替换本:

```
$(document).ready(function(){
  //Jquery Code
});
```

有了这个:

```
function pageLoad(sender, args){
  //Jquery Code
}
```

## 分享这篇文章