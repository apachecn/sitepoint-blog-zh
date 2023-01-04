# jQuery 如何读取 XML

> 原文：<https://www.sitepoint.com/jquery-read-xml-plugin/>

有时，您只需要一种简单的方法将 XML 读入 JavaScript 代码，并从 XML 项创建变量。幸运的是，jQuery 可以轻松地读取 xml！:)有许多可用的插件和代码片段，这里是其中的精华！

在本帖中，我们将介绍以下内容来指导您完成 XML 读取选项:

1.  JavaScript XML 解析
2.  jQuery XML 解析
3.  使用 jQuery.get()从 xml 获取数据
4.  jQuery XML 插件
5.  解析 XML 的技巧
6.  有哪些 JS 函数一般不好用

## 常规 JavaScript XML 解析

```
var xmlDoc = request.responseXML;
try // Build Markers, if available
{
  var markers = xmlDoc.getElementsByTagName("marker") ;
  for ( var i = 0; i jQuery XML Parsing
[js]
$(request.responseXML).find(&quot;marker&quot;).each(function() {
  var marker = $(this);
  var point = {
    marker.attr("lat"),
    marker.attr("lng")
  };
});
```

## 使用 jQuery.get()从 xml 获取数据

```
$('Contact',xml).each(function() {
	srno = parseInt($(this).find("srno").text());
	empId = $(this).find("empid").text();
	name = $(this).find("name").text();
	contact = $(this).find("contact-data").text();
	type = $(this).find("type").text();
}
```

## jParse jQuery XML 外挂程式

jParse 是一个 jQuery 插件，它允许您解析用 jQuery 获取的 XML。ajax 方法(使其完全可定制)。它与 jQuery 1.4+兼容，易于使用，超轻量级，只有 4KB！最棒的是，它兼容所有主流浏览器:
[演示](http://jparse.kylerush.net/demo)
[下载](http://jparse.kylerush.net/download)
[来源](http://jparse.kylerush.net/)

## 解析 XML 的技巧

*   尽量将 xml 文件大小限制在 5mb 以内，以避免系统变慢(如果文件太大，可以尝试获取 xml 数据并将其转换为 JSON 对象。有很多方法可以做到这一点，包括 jQuery 插件)
*   如果您对 XML synatax 不熟悉，可以查看 W3School 关于 XML 语法的规则。
*   将您的代码放在$(文档)中。ready(function(){ //here }
*   您可能会发现有些代码在 FF 中运行良好，但在 IE 中却无法运行(似乎 IE 对 jQuery 在本地文件系统上处理 XML 的方式有问题。如果你在一个服务器上上传相同的代码，它没有问题)
*   如果您正在为动态页面提取数据，那么请尝试使用相对路径而不是绝对路径

## 哪些 jQuery XML 命令通常不起作用

```
var response = xmlHttp.responseText;
var sms = $(response).find('node').text();
var sms = $.parseXML(response).find('node').text();
```

延伸阅读:[http://think2loud.com/reading-xml-with-jquery/](http://think2loud.com/reading-xml-with-jquery/)

## 分享这篇文章