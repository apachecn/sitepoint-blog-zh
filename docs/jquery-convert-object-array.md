# jquery 将对象转换为数组

> 原文：<https://www.sitepoint.com/jquery-convert-object-array/>

将 jQuery 对象转换成数组的一些选项。您也可以像使用数组一样使用 jQuery 对象。例如:

 `obj = $(' p ')；//将所有段落放入一个 jQuery 对象
for(I = 0；I<obj . length；i++) { //用 obj[i] } [/js]做点什么可以看到，jQuery 对象有。长度和[]。但是，。pop()和。缺少 reverse()。您可能希望将该数组传递给需要本机数组的函数。所以这样做:[js]obj = $(' p ')；//获取所有段落 a = $。make array(obj)；//一种做法 a = obj . to array()；//反过来[/js]也试试:[js] $。每个(piv，函数(名称，值){ console.log(名称，值)；});或者这个 for(PIV 中的 var key){//var obj = PIV[key]；console.log(key，PIV[key])；} [/js]又见:[http://api.jquery.com/jQuery.makeArray/](http://api.jquery.com/jQuery.makeArray/)

[](http://api.jquery.com/jQuery.makeArray/)`

## `分享这篇文章`