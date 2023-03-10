# 使用 jQuery 获取 URL 参数

> 原文：<https://www.sitepoint.com/url-parameters-jquery/>

jQuery 代码片段获取作为参数存储在 url 中的动态变量，并将它们存储为 JavaScript 变量，以便在脚本中使用。随着世界转向动态网络应用，它的用法与[散列 Url 的](http://www.jquery4u.com/urls/hash-urls/)不同。因此，像[解码 URL 字符串](http://www.jquery4u.com/snippets/jquery-decode-url-string/)这样的事情在未来几年将会一直流行。

```
$.urlParam = function(name){
	var results = new RegExp('[\?&]' + name + '=([^&#]*)').exec(window.location.href);
	return results[1] || 0;
}

// example.com?param1=name&param2=&id=6
$.urlParam('param1'); // name
$.urlParam('id');        // 6
$.urlParam('param2');   // null

//example params with spaces
http://www.jquery4u.com?city=Gold Coast
console.log($.urlParam('city'));  
//output: Gold%20Coast

console.log(decodeURIComponent($.urlParam('city')));  
//output: Gold Coast
```

例如，这可用于设置文本输入字段的默认值:

```
$('#city').val(decodeURIComponent($.urlParam('city')));
```

**感谢 bjverde 对此功能的改进:**

```
$.urlParam = function(name){
    var results = new RegExp('[\?&]' + name + '=([^&#]*)').exec(window.location.href);
    if (results==null){
       return null;
    }
    else{
       return results[1] || 0;
    }
}
```

## 分享这篇文章