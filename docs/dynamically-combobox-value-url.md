# 基于 URL 的 jQuery 动态组合框值

> 原文：<https://www.sitepoint.com/dynamically-combobox-value-url/>

基于 URL 字符串中给定的参数动态设置组合框值的小 jQuery 函数。对于根据用户选择的搜索条件在表单结果页面上设置默认值非常有用。

当您没有在 url 中指定参数(即不是“param=1&param=2”)但 url 可能有一个巨大的 SQL 查询参数(如“select = fields+from+table+like+combo value+etc”)时，此函数可以工作。你指定什么字符串出现在你要寻找的值之前(例如，我们将得到值 combovaluevalue)。

```
/* This function sets the combobox with the value after "like" inside the url */
(function($) { 
//get the url variables and set the combo box
var comboBox = $(location).attr('href');         
comboBox = decodeURIComponent(comboBox);  //decode url string
comboBox = comboBox.replace(/"/g, '');    //replace quotes    
var urlArray = comboBox.split("+");             //get params
//the param we're looking for is after "like"
comboBox = urlArray[jQuery.inArray("like", urlArray)+1];
$('#combobox-id > option').each(function(index) {
    //alert($(this).text() + ' ' + $(this).val());
    console.log(index + " " + $(this).attr('value'));
    if ($(this).attr('value') === comboBox) {
        comboBox = index;
    }
});
$('#combobox-id').attr('selectedIndex', jQuery.inArray(comboBox, urlArray));
})(jQuery);
```

## 分享这篇文章