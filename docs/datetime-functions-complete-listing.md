# jQuery 日期时间函数–完整列表

> 原文：<https://www.sitepoint.com/datetime-functions-complete-listing/>

这是您唯一需要的日期/时间 jQuery 函数。它比任何其他的日期/时间库工作得更好，并且有最小的开销，有保证的速度和准确性。

包括函数:获取日期，转换日期，有效日期，字符串日期，闰年，比较日期，格式日期，时区和其他堆！！！

[下载 JQUERY4U.datetime.js](http://www.jquery4u.com/scripts/JQUERY4U.datetime.js)

## jQuery 日期/时间完整列表

 `/*___ 文件:" jquery 4 u . datetime . js " _ _ */
；(function($)
{
/* *
* jQuery 日期时间函数-完整列表
*/
var jQuery 4 u = jQuery 4 u | | { }；
JQUERY4U。DATETIME =
{
/**
*该类的名称(用于错误处理和/或调试目的)
* @ type String
*/
Name:' jquery 4 u。日期时间'，

init:function()
{
jquery 4 u。util . handle errors(this)；
date . prototype . jquery 4 u format = this . format；
}，

/**
*以 dd/mm/yyyy 格式返回今天的日期
* @以 dd/mm/yyyy 格式返回{String}日期
*/
todaysDate:function()
{
返回 this . futuredatedays(0)；
}，

/**
*以 dd/mm/yyyy 格式返回明天的日期
* @以 dd/mm/yyyy 格式返回{String}日期
*/
tomorrow sdate:function()
{
返回 this . future datedays(1)；
}，

/**
*以 dd/mm/yyyy 格式返回 7 天后的日期
* @以 dd/mm/yyyy 格式返回{String}日期
*/
weekFromToday:function()
{
返回 this . futuredatedays(7)；
}，

/**
*返回下个月的第一天
* @以 dd/mm/yyyy 格式返回{ String } Date
*/
first day next month:function()
{
var today = new Date()；
nextMonth =新日期(today.getFullYear()、today.getMonth() + 1，1)；
next month . getdate()+'/'+(next month . getmonth()+1)+'/'+next month . getfullyear()；
return this . leading zero(next month . getdate())+'/'+this . leading zero(next month . getmonth()+1)+'/'+next month . get full year()；
}，

/**
*以 dd/mm/yyyy 格式返回未来 x 个日期日期
* @param {Integer} days 以 dd/mm/yyyy 格式返回{String}日期
*/
future datedays:function(days)
{
var future Date = new Date()；
future date . setdate(future date . getdate()+days)；
return this . leading zero(future date . getdate())+'/'+this . leading zero(future date . getmonth()+1)+'/'+this . leading zero(future date。get full year())；
}，

/**
*以 HHMM 格式返回当前时间
* @返回{ String } Time 时间(如 23:12)格式
*/
Time hhmm:function()
{
var today = new Date()；
return this . leading zero(today . get hours())+this . leading zero(today . get minutes())；
}，

/**
*以 HHMMSS 格式返回当前时间
* @以 HHMMSS 格式返回{String}时间(如 23:12:33)
*/
Time HHMMSS:function()
{
var today = new Date()；
return this . leading zero(today . get hours())+':'+this . leading zero(today . get minutes())+':'+this . leading zero(today . get seconds())；
}，

/**
*取澳大利亚格式的日期字符串，返回美国格式的日期字符串
* @ param { String } DD/mm/yyyy 格式的 dateStr Date
* @ param { String }[separator = "-"]返回日期字符串中的分隔符
* @返回 mm/dd/yyyy 格式的{ String } Date
*/
convertUSFormat:function(dateStr，separator)
{
var separator =(type of(separator)= = ' undefined ')？-':分隔符；
var re = new RegExp('([0-9]{ 2 })/([0-9]{ 2 })/([0-9]{ 4 })'，' m ')；
var matches = re . exec(dateStr)；
返回匹配项[2] +分隔符+匹配项[1] +分隔符+匹配项[3]；
}，

/**
*转换 mm/dd/yyyy 格式的日期并返回 dd-mm-yyyy 格式的日期(取决于分隔符)
* @ param {String} mm/DD/yyyy 格式的 dateStr 日期
* @ param { String }[Separator = "-"]返回日期字符串中的分隔符
* @返回 mm-dd-yyyy 格式的{ String }日期(假定“-”是分隔符)
*/
convertUStoAUSDate:function(dateStr，separator)
{【无-':分隔符；
var re = new RegExp('([0-9]{ 2 })/([0-9]{ 2 })/([0-9]{ 4 })'，' m ')；
var matches = re . exec(dateStr)；
返回匹配项[2] +分隔符+匹配项[1] +分隔符+匹配项[3]；
}，

/**
*返回所提供的日期组件是否构成预期日期
* @ param { String } year
* @ param { String } month
* @ param { String } day
* @返回{Boolean} True 如果日期组件与 date 对象
*/
is valid Date:function(year，month，day)
{
var dt = new Date(parse int(year，10)，parseInt(month，10)-1，parseInt(day
if(dt.getDate()！= parseInt(day，10) || dt.getMonth()！= (parseInt(month，10)-1) || dt.getFullYear()！= parseInt(year，10))
{
返回 false
}

返回 true
}，

/**
*取一个 date 对象并以 yyyymmdd 格式返回
* @ param { Date Object } Date obj
* @ returns { String } yyyymmdd 格式的 Date
*/
datetoyyymmdd:function(Date obj)
{
return(Date obj . get full year()+this . leading zero(Date obj . getmonth()+1)+this . leading zero(Date obj . getdate())。toString()；
}，

/**
*取一个 date 对象并以 ddmmyyy 格式返回
* @ param { Date Object } Date obj
* @ returns { String } ddmmyyy 格式的 Date
*/
datetoddmmyyy:function(Date obj)
{
return(this . leading zero(Date obj . getdate())+this . leading zero(Date obj . getmonth()+1)+Date obj . getfull year())。toString()；
}，

/**
*接受 dd/mm/yyyy 格式的日期字符串
* @ param { String } DD/mm/yyyy 格式的 dateString Date
* @ Returns { Date Object }如果日期字符串无效，则返回 false
*/
String toate:function(dateString)
{
try
{
var matches = dateString . match(/([0-9]{ 2 })/([0-9]{ 2 })/([0-9
if(this . is validate(matches[3]，matches[2]，matches[1]) === false)
{
返回 false；
}

返回新日期(matches[3]，parseInt(matches[2]，10)-1，parseInt(matches[1]，10))；
}
(e)
{
返回假；
}
}，

/**
*如果传递的值是个位数就加前导零
* @ param { String } val
* @ returns { String }
*/
leading zero:function(val)
{
var str = val . tostring()；
if(str . length = = 1)
{
str = ' 0 '+str；
}

返回字符串；
}，

/**
*检查返回日期是否等于或晚于出发日期
* @ param { String } departure date
* @ param { String } return date
* @ returns { Boolean }
*/
is departurereturndate valid:function(departure date，return date)
{
var dep = this . String toate(departure date)；
var ret = this . string tote(return date)；
if(dep > ret)
{
返回 false
}

返回 true
}，

/**
*检测所提供的年份是否为闰年
* @ param { Integer } year
* @ returns { Boolean }
*/
isLeapYear:function(year)
{
year = parse int(year，10)；
if(year % 4 = = 0)
{
if(year % 100！= 0)
{
返回 true
}
else
{
if(year % 400 = = 0)
{
返回 true
}
else
{
返回 false
}
}
}
返回假；
}，

比较对象:函数(from，to)
var date result = to . gettime()-from . gettime()；
var date obj = { }；
date obj . weeks = math . round(date result/(1000 * 60 * 60 * 24 * 7))；
date obj . days = math . ceil(date result/(1000 * 60 * 60 * 24))；
date obj . hours = math . ceil(date result/(1000 * 60 * 60))；
date obj . minutes = math . ceil(date result/(1000 * 60))；
date obj . seconds = math . ceil(date result/(1000))；
date obj . milliseconds = date result；
返回日期对象；
}，

comparedatesdmmyyyy:function(from，to)
{
from = from . split('/')；
from = new Date(from[2]，from[1]，from[0])；
to = to . split('/')；
to =新日期(to[2]，to[1]，to[0])；
return this . compare dates(from，to)；
}，

/**
*允许对日期进行良好的格式化，如 PHP 的日期函数
*源自 Jac Wright 在 http://jacwright.com/projects/javascript/date_format 编写的代码
* @ param { Date } Date JavaScript Date 对象
* @param {String}格式日期格式字符串
* @ returns { String }
*/
format:function()
{
var Date，
args =[]. slice . call(arguments)，
returnStr = "，
十二月']，
days = ['星期日'，'星期一'，'星期二'，'星期三'，'星期四'，'星期五'，'星期六']，
methods =
{
//Day
d:function(){ return(date . getdate()` 

## `分享这篇文章`