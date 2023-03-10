# jQuery 获取较晚日期/未来日期

> 原文：<https://www.sitepoint.com/jquery-date-future-date/>

下面是如何使用 jQuery 获取未来日期的方法。未来日期是根据当前日期计算的，例如比当前日期晚几天。如果你不确定，以下是你如何[得到当前日期](http://www.jquery4u.com/snippets/jquery-todays-date-ddmmyyyy/)。


## 使用 jQuery 检查是否是未来日期

```
function isFutureDate()
{
    var date=new Date();
    date.setFullYear(2020,1,1);
    var _now=new Date();
    if(date.getTime()>_now.getTime())
        {
      // date is future
        }
}
```

## 检查是否是 4 位数的年份

此函数检查日期是否有 2 位数的日和月以及 4 位数的年。

```
function twodigits(digits) {
    return (digits > 9) ? digits : '0' + digits;
}
```

## 完整的代码

```
var fullDate = new Date();
var twoDigitDays = twodigits(fullDate.getDate());
var twoDigitMonth = twodigits(fullDate.getMonth() + 1);

var departDate = twoDigitDays + "/" + twoDigitMonth + "/" + fullDate.getFullYear();
console.log(departDate);

fullDate.setDate(fullDate.getDate()+7);
var twoDigitDays = twodigits(fullDate.getDate());
var twoDigitMonth = twodigits(fullDate.getMonth() + 1);

var returnDate = twoDigitDays + "/" + twoDigitMonth + "/" + fullDate.getFullYear();
console.log(returnDate);
```

## 替代示例

```
var currentDate = new Date(),
currentMonth = currentDate.getMonth() + 1,
lastDayOfMonth = new Date(currentDate.getFullYear(), (currentDate.getMonth() - 1), 0).getDate(),
departureDate = futureDateDays(14),
depDate = departureDate.split('/'),
departureDateMonth = depDate[1];
if (departureDateMonth != currentMonth) {
	departureDate = leadingZero(currentMonth) + '/' + leadingZero(lastDayOfMonth) +'/'+ depDate[2];
}
console.log(departureDate);
//quick fix convert back to date using string format mm/dd/yyyy
var validDate = new Date(departureDate);
//then back to string
departureDate = leadingZero(validDate.getDate()) + '/' + leadingZero(validDate.getMonth()+1) +'/'+ validDate.getFullYear();

//Output: 
//09/31/2011
//Date {Sat Oct 01 2011 00:00:00 GMT+1000}
//01/10/2011
```

## 分享这篇文章