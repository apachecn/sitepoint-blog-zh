# jQuery 检查日期是否有效

> 原文：<https://www.sitepoint.com/jquery-check-date-valid/>

**简单的 JavaScript 函数来检查日期是否有效。**

```
function isValidDate(s) {
  var bits = s.split('/');
  var d = new Date(bits[2] + '/' + bits[1] + '/' + bits[0]);
  return !!(d && (d.getMonth() + 1) == bits[1] && d.getDate() == Number(bits[0]));
}

//Test it!
var currentDate = new Date('31/09/2011');
console.log(isValidDate(currentDate.toString()));
console.log(isValidDate('30/09/2011'));
```

**JavaScript 函数来获取一个月中的天数。**

```
function daysInMonth(month, year) {
    return new Date(year, month, 0).getDate();
}

//Test it!
daysInMonth('09','2011');
//Output: 30
```

另请参见:

*   [**jQuery 获取以后的日期/未来的日期**](http://www.jquery4u.com/snippets/jquery-date-future-date/)
*   [**JavaScript 显示日期年份错误**](http://www.jquery4u.com/jquery-date-and-time-2/javascript-showing-wrong-year-date/)
*   [**jQuery DATETIME 函数——完整清单**](http://www.jquery4u.com/jquery-functions/datetime-functions-complete-listing/)

如果没有将日期设置为当月的最后一天，JavaScript 函数将检查日期是否有效。

```
/* check a date is valid, if not revert to last day in month */
validateDateLastDayMonth: function()
{
	/* helpers */
	function isValidDate(s) {
	  var bits = s.split('/');
	  var d = new Date(bits[2] + '/' + bits[1] + '/' + bits[0]);
	  return !!(d && (d.getMonth() + 1) == bits[1] && d.getDate() == Number(bits[0]));
	} 

	function daysInMonth(month, year) {
		return new Date(year, month, 0).getDate();
	}

	/* init */
	var currentDate = new Date(),
	currentMonth = currentDate.getMonth() + 1,
	lastDayOfMonth = new Date(currentDate.getFullYear(), (currentDate.getMonth() - 1), 0).getDate(),
	departureDate = FCL.DATETIME.futureDateDays(14),
	depDate = departureDate.split('/'),
	departureDateMonth = depDate[1];
	if (departureDateMonth != currentMonth) {
		departureDate = FCL.DATETIME.leadingZero(lastDayOfMonth) +'/'+ FCL.DATETIME.leadingZero(currentMonth) +'/'+ depDate[2];
	}

	/* validate date */
	if (!isValidDate(departureDate.toString()))
	{
		var bits = departureDate.split('/');
		departureDate = FCL.DATETIME.leadingZero(daysInMonth(bits[1],bits[2])) +'/'+ FCL.DATETIME.leadingZero(currentMonth) +'/'+ depDate[2];
	}

	$('input[name="depDate"]').val(departureDate);
}
```

## 分享这篇文章