# jQuery JSON 到字符串

> 原文：<https://www.sitepoint.com/jquery-json-string/>

简单的 JavaScript 函数，用于将 JSON 字符串转换成逗号分隔的引用列表以供显示。

另见 JSON.stringify 的 js fiddle[https://jsfiddle.net/ifandelse/6Yj5h/](https://jsfiddle.net/ifandelse/6Yj5h/)

```
//helper function to convert json to string
stripJsonToString: function(json)
{
    return JSON.stringify(json).replace(',', ', ').replace('[', '').replace(']', '');
},
//email@email.com, example@example.com, somebody@somewhere.com
```

## 分享这篇文章