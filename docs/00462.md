# 使用 Moment.js 管理日期和时间

> 原文：<https://www.sitepoint.com/managing-dates-times-using-moment-js/>

在 JavaScript 中处理日期和时间总是有点麻烦。原生日期方法冗长且[API 经常不一致](https://stackoverflow.com/questions/2587345/why-does-date-parse-give-incorrect-results)。这就是为什么如果你在 StackOverflow 上问一个与日期相关的问题，你会经常听到“Use Moment.js”的回复。

## Moment.js 是什么？

[Moment.js](https://momentjs.com) 是一把瑞士军刀，用于处理 JavaScript 中的日期。它允许您使用简洁明了的 API 来解析、验证、操作和显示日期和时间。

在本文中，我将向您展示如何使用 Moment.js，并演示它的几个常见用例。

### 对新用户的警告:请改用 date-fns

请注意，截至 2020 年 9 月，我们建议希望实现日期库的用户使用 date-fns 而不是 T1。

就其功能而言，Moment.js 是一个相当沉重的库，Chrome 开发工具现在积极建议不要将其用作核心 Web Vitals 性能测试的一部分。因此，根据 Moment.js 维护者的说法，该项目处于维护模式，新功能开发不再被提上日程。

➤ [学习日期-fns:一个轻量级的 JavaScript 日期库](https://www.sitepoint.com/date-fns-javascript-date-library)

## Moment.js 入门

Moment.js 可以从[项目的主页](http://momentjs.com/)免费下载。Moment.js 既可以在浏览器中运行，也可以在节点应用程序中运行。为了与 Node 一起使用，请使用以下命令安装该模块。

```
npm install moment 
```

然后，简单地使用`require()`并在您的应用程序中使用它，如下所示。

```
const moment = require('moment');
const today = moment();
console.log(today.format());

// 2020-01-09T15:45:51+01:00 
```

为了从浏览器运行 Moment.js，使用一个`<script>`标签来包含它，如下所示。Moment.js 创建了一个全局的`moment`对象，可以用来访问所有的日期和时间解析和操作功能。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>Moment.js</title>
  </head>
  <body>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.24.0/moment.min.js"></script>
    <script> // You have a 'moment' global here
      const today = moment();
      console.log(today.format()); </script>
  </body>
</html> 
```

## 日期格式

在过去，我记得将日期字符串转换成`Date`对象，抓取单个数据片段，然后执行字符串连接。Moment.js 简化了日期转换成任何特定格式的过程。用 Moment 进行日期格式转换很简单，如下例所示。

```
moment().format('YYYY-MM-DD'); 
```

调用`moment()`给我们当前的日期和时间，而`format()`将它转换成指定的格式。本示例将日期格式设置为四位数字的年份，后跟一个连字符，再后跟两位数字的月份、另一个连字符和两位数字的日期。

见 [用 Moment.js](https://codepen.io/SitePoint/pen/XWJEZaJ) 格式化日期用 [CodePen](https://codepen.io) 上的 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
。