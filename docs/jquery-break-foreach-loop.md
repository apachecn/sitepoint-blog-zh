# jQuery 跳出 foreach 循环

> 原文：<https://www.sitepoint.com/jquery-break-foreach-loop/>

使用 jQuery 的中断 foreach 循环的简单 jQuery 代码片段。each()函数当你找到了你想要的值时，你可能不需要遍历其余的结果。

```
var selected = 0;
// Iterate through item in the list.  If we find the selected item, return false to break out of the loop
$('ul#mylist li').each(function(index){
  if ( $(this).hasClass(‘selected’) )
  {
    selected = index;
    return false; 
  } 
});
console.debug('Selected position is: '+ selected);
```

## 分享这篇文章