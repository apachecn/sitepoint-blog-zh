# 文件就绪功能

> 原文：<https://www.sitepoint.com/document-ready-function/>

jQuery 文档就绪函数用作所有 jQuery 函数和事件代码的外部容器。它的目的是，只有当你的 HTML 完全加载到网页中时，它才会加载 jQuery 代码。这允许它向保存在 DOM 中的 HTML 对象添加函数和事件，并防止加载错误。

你应该这样做:

```
$(document).ready(function(){

 *// jQuery functions go here...*

 });
```

来源:http://www.w3schools.com/jquery/jquery_syntax.asp

## 分享这篇文章