# JavaScript 时态 API 简介

> 原文：<https://www.sitepoint.com/javascript-temporal-api-introduction/>

**不管你对 JavaScript 的看法如何，每个人都认为日期处理一团糟。`Date()`对象实现是 1995 年直接从 Java 复制过来的。两年后，Java 放弃了它，但为了向后兼容，它仍然保留在 JavaScript 中。**

这些是我们面临的问题:

*   它只支持 UTC 和用户的本地时间
*   不支持非公历日历
*   夏令时行为是不可预测的
*   从字符串中解析日期是不可靠的
*   这个 API 很笨重
*   对象是可变的:日期会随着方法的应用而改变

最流行的选择曾经是 [Moment.js 日期库](https://www.sitepoint.com/managing-dates-times-using-moment-js/)。然而，这已被[置于维护模式](https://momentjs.com/docs/#/-project-status/)，不应真正用于新项目。他们推荐了几个[的备选方案](https://momentjs.com/docs/#/-project-status/recommendations/)，但是每个都有各自的优缺点。

`Date()`对象不能从 JavaScript 中移除，但是一个新的`Temporal`选项正处于 TC39 标准批准过程的[阶段 2。这里讨论的属性和方法可能会改变，但是 API 应该会在 2021 年的某个时候出现在浏览器和运行时中。](https://tc39.es/proposal-temporal/docs/)

## 时间

`Temporal`是一个顶级静态全局对象(就像 [`Math`](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Math) )。

其主要目标是:

*   可预测的跨浏览器/运行时行为
*   用于日期和时间计算的更简单的 API
*   支持非公历日历
*   支持所有时区，包括夏令时算法
*   解析严格指定的 ISO-8601 字符串
*   使所有对象不可变

该 API 是全面的，可能会有变化，但在不久的将来，您可以期待以下关键特性。

## 当前日期和时间

[`Temporal.now`](https://tc39.es/proposal-temporal/docs/now.html) 返回当前日期/时间，可以传递给进一步的方法以提供附加信息。例如:

```
// exact time since the Unix epoch on 1 Janary, 1970 UTC
Temporal.now.instant();
Temporal.now.instant().epochSeconds;
Temporal.now.instant().epochMilliseconds;

// current time zone
Temporal.now.timeZone();

// time in current location, e.g.
// 2021-09-18T04:17:48.435068431-04:00[America/New_York]
Temporal.now.zonedDateTimeISO();

// time in another time zone, e.g.
// 2021-09-18T09:17:48.438068435+01:00[Europe/London]
Temporal.now.zonedDateTimeISO('Europe/London'); 
```

## 创建即时日期/时间

[`Temporal.Instant`](https://tc39.es/proposal-temporal/docs/instant.html) 对象表示时间上最接近纳秒的单个点。它可以由 ISO 8601 格式的字符串或秒、毫秒或微秒数创建:

```
const
  t1 = Temporal.Instant.from('2021-03-30T01:45:00+01:00[Europe/Berlin]'),
  t2 = Temporal.Instant.from('2021-04-01T02:00+01:00'),
  t3 = Temporal.Instant.fromEpochSeconds(1.0e8),
  t4 = Temporal.Instant.fromEpochMilliseconds(1.0e10),
  t5 = Temporal.Instant.epochNanoseconds(1.0e12); 
```

## 创建区域感知日期/时间

[`Temporal.ZonedDateTime`](https://tc39.es/proposal-temporal/docs/zoneddatetime.html) 对象代表一个时区和日历感知的日期/时间，它发生在(或将发生在)一个特定的区域。可以使用各种不同的构造函数:

```
new Temporal.ZonedDateTime(
  1234567890000, // epoch nanoseconds
  Temporal.TimeZone.from('America/Los_Angeles'), // timezone
  Temporal.Calendar.from('iso8601') // default calendar
);

Temporal.ZonedDateTime.from('2025-12-07T03:24:30+02:00[Africa/Cairo]');

Temporal.Instant('2022-08-05T20:06:13+05:45').toZonedDateTime('+05:45');

Temporal.ZonedDateTime.from({
  timeZone: 'America/New_York'
  year: 2025,
  month: 1,
  day: 7,
  hour: 9,
  minute: 30,
  second: 1,
  millisecond: 2,
  microsecond: 3,
  nanosecond: 4
}); 
```

## 创建普通日期和时间

并不总是需要使用精确的时刻，所以时态 API 提供了独立于时区的对象。这些可以用于更简单的事件，比如今天下午两点的会议。

*   [`Temporal.PlainDateTime`](https://tc39.es/proposal-temporal/docs/plaindatetime.html) 指日历上的日期和时间
*   [`Temporal.PlainDate`](https://tc39.es/proposal-temporal/docs/plaindate.html) 指特定的日历日期
*   [`Temporal.PlainTime`](https://tc39.es/proposal-temporal/docs/plaintime.html) 指一天中特定的时间
*   [`Temporal.PlainYearMonth`](https://tc39.es/proposal-temporal/docs/plainyearmonth.html) 指没有日期成分的日期——如“2021 年 4 月的会议”
*   [`Temporal.PlainMonthDay`](https://tc39.es/proposal-temporal/docs/plainmonthday.html) 指没有年份成分的日期——如“圆周率日是 3 月 14 日”。

都有相似的构造函数:

```
// create a new PlainDateTime
// both are 4 May 2021 at 1:14pm and 15 seconds
new Temporal.PlainDateTime(2021, 5, 4, 13, 14, 15);
Temporal.PlainDateTime.from('2021-05-04T13:14:15');

// create a new PlainDate
// both are 4 May, 2021
new Temporal.PlainDate(2021, 5, 4);
Temporal.PlainDate.from('2021-05-04');

// create a new PlainTime
// both are 1:14pm and 15 seconds
new Temporal.PlainTime(13, 14, 15);
Temporal.PlainTime.from('13:14:15');

// create a new year PlainYearMonth
// both are April 2021
new Temporal.PlainYearMonth(2021, 4);
Temporal.PlainYearMonth.from('2019-04');

// create a new PlainMonthDay
// both are 14 March
new Temporal.PlainMonthDay(3, 14);
Temporal.PlainMonthDay.from('03-14'); 
```

## 提取值

所有的`Temporal`对象都可以返回关于特定日期/时间的离散值。例如，使用一个`ZonedDateTime`:

```
const t1 = Temporal.ZonedDateTime.from('2025-12-07T03:24:30+02:00[Africa/Cairo]');

t1.year;        // 2025
t1.month;       // 12
t1.day;         // 7
t1.hour;        // 3
t1.minute;      // 24
t1.second;      // 30
t1.millisecond; // 0
t1.microsecond; // 0
t1.nanosecond;  // 0 
```

其他有用的属性包括:

*   `dayOfWeek`(周一 1 到周日 7)
*   `dayOfYear` (1 至 365 或 366)
*   `weekOfYear` (1 到 52，有时是 53)
*   `daysInMonth` (28、29、30 或 31)
*   `daysInYear` (365 或 366)
*   `inLeapYear` ( `true`或`false`)

## 比较和排序日期

所有的`Temporal`对象都可以使用一个`compare()`函数进行比较，该函数返回一个整数。例如，比较两个`ZonedDateTime`物体:

```
// returns:
//  -1 if t1 is before t2
//   0 if t1 and t2 are the same
//   1 is t1 is after t2
Temporal.ZonedDateTime.compare(t1, t2); 
```

`compare()`可用作数组`sort()`函数，将日期/时间按升序排列(从最早到最晚):

```
const t = [

    '2022-01-01T00:00:00+00:00[Europe/London]',
    '2022-01-01T00:00:00+00:00[Africa/Cairo]',
    '2022-01-01T00:00:00+00:00[America/New_York]'

  ].map( d => Temporal.ZonedDateTime.from(d) )
  .sort( Temporal.ZonedDateTime.compare ); 
```

## 日期计算

提供了几种方法来对任何时态对象执行日期计算。当传递一个`Temporal.Duration`对象时，它们都返回一个相同类型的新时态，该对象在`years`、`months`、`days`、`hours`等中定义了一个时间段。

```
const t1 = Temporal.ZonedDateTime.from('2022-01-01T00:00:00+00:00[Europe/London]');

// add 8 hours 30 minutes
t1.add({ hours: 8, minutes: 30 });

// subtract 5 days
t1.subtract({ days: 5 });

// round to nearest month
t1.round({ smallestUnit: 'month' }); 
```

注意`Plain`日期和时间可以换行。例如，给任何一个`PlainTime`加上 24 小时将返回一个拥有相同值的对象。

`until()`和`since()`方法返回一个`Temporal.Duration`对象，描述从特定日期/时间到现在的时间:

```
// mounths until t1
t1.until().months;

// weeks since t1
t1.since().weeks; 
```

最后，`equals()`方法可以确定两个日期/时间值是否相等:

```
const
  d1 = Temporal.PlainDate.from('2022-01-31');
  d2 = Temporal.PlainDate.from('2021-01-31');

d1.equals(d2); // false 
```

## 用国际化 API 格式化日期

虽然不是时态 API 的一部分，JavaScript [`Intl`(国际化)API](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Intl) 提供了一个 [`DateTimeFormat()`](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat) 构造函数，可用于格式化时态或日期对象:

```
const d = new Temporal.PlainDate(2021, 3, 14);

// 3/14/2021
new Intl.DateTimeFormat('en-US').format(d);

// 14/3/2021
new Intl.DateTimeFormat('en-GB').format(d);

// miércoles, 14 de abril de 2021
new Intl.DateTimeFormat('es-ES', { dateStyle: 'full' }).format(d); 
```

## 关于时间？

四分之一世纪以来,`Date()`对象一直是令开发者沮丧的原因。虽然不能保证`Temporal`会很快成为一个标准特性，但是 JavaScript 日期的未来看起来稍微光明一些。

你今天可以通过[实验聚合填充物](https://github.com/tc39/proposal-temporal/tree/main/polyfill)试用`Temporal`，但是不要在生产中使用它(还不行)！

更多信息:

*   [临时提案文件](https://tc39.es/proposal-temporal/docs/)
*   [临时食谱](https://tc39.es/proposal-temporal/docs/cookbook.html) —示例用法食谱

## 分享这篇文章