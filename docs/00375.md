# 使用 date-fns 在 JavaScript 中管理日期和时间

> 原文：<https://www.sitepoint.com/date-fns-javascript-date-library/>

在 JavaScript 中处理日期是一件痛苦的事情。本地日期方法通常很冗长，而且[偶尔会不一致](https://stackoverflow.com/q/9687521/1136887)。但是好消息就在眼前。date-fns 库提供了一个简单而全面的工具集，用于在 JavaScript 中管理日期和时间。

date-fns 是 moment.js 或原生 JavaScript 日期操作的轻量级替代方案。它为常见任务提供了一套很好的方法，例如:

*   格式化日期
*   国际化(i18n)
*   比较日期
*   排序日期
*   寻找两个日期之间的差距
*   时区之间的转换

让我给你一个例子来说明它有多有用:这是一个[堆栈溢出问题的公认答案，该问题询问如何获得一个月的最后一天](https://stackoverflow.com/q/1924815/1136887):

```
var t = new Date();
alert( new Date(t.getFullYear(), t.getMonth() + 1, 0, 23, 59, 59) ); 
```

当然这是可行的，但是`getMonth`后面的数字代表什么并不明显。现在对比一下可读性更强的:

```
const today = new Date();
console.log( lastDayOfMonth(today) ); 
```

这个`lastDayOfMonth`方法是由 [date-fns](https://date-fns.org/) 提供的，它自称是一个用于在浏览器和 Node.js 中操作 JavaScript 日期的综合工具集

在这篇文章中，我将向您展示如何启动和运行 date-fns。阅读完之后，您将能够把它放到您的项目中，并利用它的许多辅助方法来轻松操纵日期。这将使像`t.getMonth() + 1, 0, 23, 59, 59`这样的代码成为过去。

## 为什么不是 Moment.js？

Moment.js 是一个用 JavaScript 处理日期的极好的库——它有许多很棒的特性，并提供了一大堆有用的工具。然而，它也不是没有批评者。

许多人认为 Moment 对象是可变的(例如像`add`或`subtract`这样的操作改变了原始的 Moment 对象)这一事实让开发人员感到困惑，也是错误的来源。

它也因其庞大的体积而受到抨击。Moment 不太适合现代的“树摇动”算法，如果您需要国际化或时区支持，您可以很快找到一个相当大的 JavaScript 包。

这已经到了如此地步，以至于 Chrome 的开发工具现在强调了这样一个事实，即使用 Moment 会导致糟糕的性能。所有这些都导致了 Moment 维护者[将项目置于维护模式](https://momentjs.com/docs/#/-project-status/)并阻止 Moment 用于未来的新项目。

这使得 date-fns 成为 Moment.js 的最佳替代品之一。

## 安装日期-fns

从这个库的第二个版本开始，安装 date-fns 的唯一方式就是作为一个 npm 包。

```
npm install date-fns 
```

或者通过纱线:

```
yarn add date-fns 
```

您可以将日期-fns 用于 CommonJS 模块系统和 ES 模块:

```
// CommonJS
const { lastDayOfMonth } = require('date-fns'); 
```

或者:

```
// ES Modules
import { lastDayOfMonth } from 'date-fns'; 
```

不幸的是，目前没有 CDN 版本的 date-fns 可用。它的移除和可能的恢复正在本期 GitHub 中讨论。但是，这并不是说你不能在浏览器中使用它，只是你需要在你的工作流程中引入一个捆绑步骤。

现在让我们来看看如何做到这一点。

### 如何在浏览器中捆绑使用日期-fns

我假设您的机器上安装了 Node 和 npm。如果没有，请参考[我们关于安装节点](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)的教程。

接下来，安装[包](https://en.parceljs.org/)。这是一个捆绑器(类似于 Webpack)，它允许你捆绑你的 JavaScript 并在浏览器中提供。

```
npm install -g parcel-bundler 
```

接下来，用一个`package.json`文件创建一个新项目。

```
mkdir datefns
cd datefns
npm init -y 
```

安装 date-fns 库，如上所示:

```
npm install date-fns 
```

> **注意**:这将在你的项目目录下的`node_modules`文件夹中创建一个`date-fns`文件夹。如果你查看`date-fns`文件夹，你会看到更多的文件夹和文件。不过，不要担心，我们不会向客户运送太多这种东西。我们将只选择我们需要的函数，然后通过 package 运行所有东西，以制作一个缩小的树抖动包。

现在创建两个文件，`index.html`和`index.js`。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>date-fns</title>
  </head>
  <body>
    <script src="index.js"></script>
  </body>
</html> 
```

```
import { lastDayOfMonth } from 'date-fns';

const today = new Date();
console.log(lastDayOfMonth(today)); 
```

启动宗地的内置开发服务器:

```
parcel index.html 
```

并导航到 [http://localhost:1234](http://localhost:1234) 。您不会看到页面上显示的任何内容，但是如果您打开浏览器的控制台。您应该知道它已经记录了当月的最后一天。

对于部署，您可以运行:

```
parcel build index.js --experimental-scope-hoisting 
```

让包裹在`dist`文件夹中输出一个缩小的树状图包。

## 日期-fns 基本用法

现在我们已经开始运行了，让我们看看 date-fns 可以做什么。

处理日期时最常见的任务之一是能够很好地格式化它们。我们可以用 date-fns [格式函数](https://date-fns.org/docs/format)做到这一点。

将上面示例页面中的 HTML 修改成这样:

```
<body>
  <h1>The date today is <span></span></h1>
  <script src="index.js"></script>
</body> 
```

在`index.js`中，我们想要导入`format`函数，然后我们可以传递今天的日期和一个格式字符串。然后，我们希望将结果输出到页面。

```
import { format } from 'date-fns';

const today = new Date();
const formattedDate = format(today, 'dd.MM.yyyy');

document.querySelector('h1 > span').textContent = formattedDate; 
```

当然，我们并不局限于一种`dd.MM.yyyy`格式，让我们尝试一些不同的东西:

```
const formattedDate = format(today, 'PPPP'); 
```

这将输出格式化如下:`Wednesday, September 16th, 2020`。你可以在文档中找到格式化选项[的完整列表。](https://date-fns.org/docs/format)

### 更改区域设置

如果你有一个多语言的网站，那么 date-fns 使国际化时间和日期变得简单。让我们迎接我们的德国客人:

```
<h1>Heute ist <span></span></h1> 
```

在 JavaScript 文件中，我们可以导入德语地区并将其传递给`format`函数:

```
import { format } from 'date-fns';
import { de } from 'date-fns/locale';

const today = new Date();
const formattedDate = format(today, 'PPPP', { locale: de });

document.querySelector('h1 > span').textContent = formattedDate; 
```

这将输出如下内容:`Heute ist Mittwoch, 16\. September 2020`。

要求并传递语言环境作为选项可能看起来很复杂，但与 Moment.js 不同，它会在默认情况下使用所有语言环境来扩充您的构建，date-fns 会强制开发人员在需要时手动要求语言环境。

您可以通过查看项目中的`node_modules/date-fns/locale`文件夹来查看可用语言环境的列表。

## 永恒、纯粹和简单

date-fns 的卖点之一是它的功能简单明了。这导致代码易于理解，出错时更容易调试。

让我用 Moment.js 作为反例来演示一下。如前所述，Moment 中的日期是可变的，这会导致意外的行为。

```
const moment = require('moment');
const now = new Date();
const mNow = moment(now);

mNow.add('day', 3);
console.log(mNow.toDate());
mNow.add(3, 'day');
console.log(mNow.toDate());

// 2020-09-19T10:08:36.999Z
// 2020-09-22T10:08:36.999Z 
```

这里有几件事需要注意。Moment 的`add`函数对于接受其参数的顺序并不挑剔(尽管第一个方法现在会抛出一个弃用警告)。但是更令人困惑的是，如果你连续多次调用`add`，将不会得到相同的结果，因为 Moment 对象是可变的:

```
mNow.add(3, 'day'); // add 3 days
mNow.add(3, 'day'); // adds 3 **more** days 
```

现在将它与 date-fns 进行比较，date-fns 将参数保持在一个顺序，并且总是返回相同的结果，为每个调用返回一个新的`Date`对象。

```
import { addDays } from 'date-fns';

const today = new Date();
const threeDaysTime = addDays(3, today);
const sixDaysTime = addDays(threeDaysTime, 3);

console.log(today); // Wed Sep 16 2020 12:11:55 GMT+0200
console.log(threeDaysTime); // Sat Sep 19 2020 12:12:58 GMT+0200
console.log(sixDaysTime); // Invalid Date 
```

还要注意方法名是如何更有表现力的(`addDays`而不仅仅是`add`)，保持事情的一致性，用一个方法做一件事情，而且只做一件事情。

## 比较日期

如果你看看 SitePoint 的 JavaScript 频道上的帖子列表，你会发现有些帖子被列为在某个特定日期发布，而另一些则被列为在 X 天前发布。如果你试图用普通的 JavaScript 实现它，可能需要一段时间，但是用 date-fns 这是轻而易举的事情——只需使用 [formatDistance 方法](https://date-fns.org/v2.16.1/docs/formatDistance)。

让我们比较两个不同的日期。

```
import { formatDistance } from 'date-fns';

const startDate = new Date(2020, 8, 16); // (Sep 16 2020)
const endDate = new Date(2020, 11, 25); // (Dec 25 2020)
const distanceInWords = formatDistance(startDate, endDate);

console.log(`It is ${distanceInWords} until Christmas`);
// It is 3 months until Christmas 
```

请注意，在使用 JavaScript 时，月份是从零开始的(例如，11 月= 12 月)，而天数是从 1 开始计数的。这一次又一次地绊倒了我。

## 使用日期集合

Date-fns 有一些非常方便的助手方法，您可以使用它们以各种方式操作日期集合。

### 对日期集合进行排序

以下示例使用 [compareAsc](https://date-fns.org/docs/compareAsc) 将日期按升序排序。为此，如果第一个日期在第二个日期之后，则返回 1；如果第一个日期在第二个日期之前，则返回-1；如果两个日期相等，则返回 0。

```
import { compareAsc } from 'date-fns';

const date1 = new Date('2005-01-01');
const date2 = new Date('2010-01-01');
const date3 = new Date('2015-01-01');
const arr = [date3, date1, date2];
const sortedDates = arr.sort(compareAsc);

// [ 2005-01-01, 2010-01-01, 2015-01-01 ] 
```

如你所见，日期现在是升序排列的。

与`compareAsc`相对应的方法是[比较](https://date-fns.org/docs/compareDesc)。

```
import { compareDesc } from 'date-fns';
...
const sortedDates = arr.sort(compareDesc);
// [ 2015-01-01, 2010-01-01, 2005-01-01 ] 
```

### 生成两个日期之间的天数

要生成两个日期之间的天数，可以使用我们之前见过的 [addDays 方法](https://date-fns.org/docs/addDays)，以及返回指定范围内的日期数组的[eachdayfininterval 助手](https://date-fns.org/v2.16.1/docs/eachDayOfInterval)。

```
import { addDays, eachDayOfInterval } from 'date-fns';

const today = new Date();
const aWeekFromNow = addDays(today, 7);
const thisWeek = eachDayOfInterval(
  { start: today, end: aWeekFromNow },
);

console.log(thisWeek);

/*
[
  Wed Sep 16 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Thu Sep 17 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Fri Sep 18 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Sat Sep 19 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Sun Sep 20 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Mon Sep 21 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Tue Sep 22 2020 00:00:00 GMT+0200 (Central European Summer Time),
  Wed Sep 23 2020 00:00:00 GMT+0200 (Central European Summer Time)
]
*/ 
```

### 寻找最接近的日期

使用 [closestTo 方法](https://date-fns.org/docs/closestTo)可以在日期数组中找到与某个日期最接近的日期。该代码片段延续了上一个示例:

```
import { addDays, eachDayOfInterval, closestTo } from 'date-fns';
...
const christmas = new Date(2020, 11, 25);
const closestToChristmasDate = closestTo(christmas, thisWeek);

console.log(closestToChristmasDate);
// Wed Sep 23 2020 00:00:00 GMT+0200 (Central European Summer Time) 
```

如果您想获得数组的索引，也可以使用 [closestIndexTo](https://date-fns.org/docs/closestIndexTo) 方法。

### 验证日期

我想看的最后一个助手是 [isValid 方法](https://date-fns.org/v2.16.1/docs/isValid)，顾名思义，它检查给定的日期是否有效。

然而，由于 JavaScript 处理日期的方式，需要注意一些问题:

```
import { isValid } from 'date-fns';

const invalidDate = new Date('2020, 02, 30');
console.log(isValid(invalidDate));
// true, lol, wut? 
```

如果您认为上面的代码片段应该输出`false`，那是情有可原的，因为 2020 年 2 月 30 日显然是一个无效的日期。要了解发生了什么，请在浏览器的控制台中输入`new Date('2020, 02, 30')`。您将会看到`Sun Mar 01 2020`又回来了——JavaScript 从二月底多取了一天，并把它变成了三月一日(这当然是一个有效的日期)。

为了解决这个问题，我们可以在检查日期的有效性之前对其进行解析:

```
import { isValid, parse } from 'date-fns';

const validDate = parse('29.02.2020', 'dd.MM.yyyy', new Date());
const invalidDate = parse('30.02.2020', 'dd.MM.yyyy', new Date());

console.log(validDate);
// Sat Feb 29 2020 00:00:00 GMT+0100 (Central European Standard Time)

console.log(invalidDate);
// Invalid Date

console.log(isValid(validDate));
// true

console.log(isValid(invalidDate));
// false 
```

这可以很容易地提取到一个小的 helper 方法中，例如，用于验证表单中的用户输入。

## 时区

date-fns 的一个缺点是，它目前不像 Moment.js 那样有任何时区助手函数，而是返回代码运行所在的本地时区。

[这个堆栈溢出回答](http://stackoverflow.com/a/15171030)给出了一些关于本机`Date`对象如何实际上不存储“实时区”数据的背景。在这个主题中，你会注意到他们提到了一种在 JavaScript 中本地设置时区的方法。这不是一个全面的解决方案，但它适用于许多只需要输出转换(从 UTC 或本地时间到特定时区)的场景。

```
new Date().toLocaleString("en-US", {timeZone: "America/New_York"}); 
```

时区实际上是一个要解决的复杂问题，这就是为什么 [MomentJS](http://momentjs.com/timezone/) 有一个单独的库。有[计划为 date-fns](https://github.com/date-fns/date-fns/issues/180#issuecomment-264932949) 添加时区支持，但在撰写本文时，这仍是一项正在进行的工作。

然而，npm 上有一个[包可用(基于对 date-fns](https://www.npmjs.com/package/date-fns-tz) 的[未合并的拉请求)，它使用](https://github.com/date-fns/date-fns/pull/707) [Intl API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 添加了对 date-fns v2.0.0 的时区支持。凭借每周 14 万的下载量，它似乎很受欢迎，但在撰写本文时，它已经几个月没有更新了。

也就是说，你可以这样使用它:

```
npm i date-fns-tz 
```

```
import { format, utcToZonedTime } from 'date-fns-tz';

const today = new Date(); // Wed Sep 16 2020 13:25:16
const timeZone = 'Australia/Brisbane'; // Let's see what time it is Down Under
const timeInBrisbane = utcToZonedTime(today, timeZone);

console.log(` Time in Munich: ${format(today, 'yyyy-MM-dd HH:mm:ss')} Time in Brisbane: ${format(timeInBrisbane, 'yyyy-MM-dd HH:mm:ss')}  `);

// Time in Munich: 2020-09-16 13:26:48
// Time in Brisbane: 2020-09-16 21:26:48 
```

## 结论

Date-fns 是一个很棒的小库，它提供了一大堆在 JavaScript 中处理日期和时间的辅助方法。它正在积极开发中，现在 Moment.js 已经进入维护模式，这使它成为 Moment.js 的一个很好的替代品。

我希望这篇文章已经给了你足够的理解和启发，去检查它，并开始在你自己的项目中使用它。

## 分享这篇文章