# jQuery 获取今天的日期日/月/年

> 原文：<https://www.sitepoint.com/jquery-todays-date-ddmmyyyy/>

获取格式为 **dd/mm/yyyy** 的当前日期的 JavaScript 代码片段。对于单月数字，月份的格式以零为前缀(即“04”)。此外，下面是如何使用 jQuery
让[得到未来的日期](http://www.jquery4u.com/snippets/jquery-date-future-date/)

```
var fullDate = new Date()
console.log(fullDate);
//Thu May 19 2011 17:25:38 GMT+1000 {}

//convert month to 2 digits
var twoDigitMonth = ((fullDate.getMonth().length+1) === 1)? (fullDate.getMonth()+1) : '0' + (fullDate.getMonth()+1);

var currentDate = fullDate.getDate() + "/" + twoDigitMonth + "/" + fullDate.getFullYear();
console.log(currentDate);
//19/05/2011
```

**注意:**[console . log()命令](http://www.jquery4u.com/utilities/live-jquery-debugging-firebug/)仅用于 firebug。

如果上面的代码不起作用，试试这个(谢谢 pnilesh):

```
var fullDate = new Date();console.log(fullDate);
var twoDigitMonth = fullDate.getMonth()+"";if(twoDigitMonth.length==1)	twoDigitMonth="0" +twoDigitMonth;
var twoDigitDate = fullDate.getDate()+"";if(twoDigitDate.length==1)	twoDigitDate="0" +twoDigitDate;
var currentDate = twoDigitDate + "/" + twoDigitMonth + "/" + fullDate.getFullYear();console.log(currentDate);
```

## 分享这篇文章