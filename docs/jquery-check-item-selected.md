# jQuery 检查当前是否选择了项目

> 原文：<https://www.sitepoint.com/jquery-check-item-selected/>

jQuery 代码片段到**检查元素是否被选中**(也可以用来检查输入是否被聚焦)。


```
//this is one way to do it
console.log($(this).is(":focus"));

//this is another way
function itemHasFocus(id)
{
    var output = false;
    console.log(id);
    //loop for all fields in the form
    $.each($('#'+id+' :input'), function(i,v)
    {
       console.log($(this).is(":focus")); //check item has focus
       if ($(this).is(":focus"))
       {
         output = true;
         return false; //return false skips out of the loop
       }
    });
    return output;
}
//returns true or false
```

## 分享这篇文章