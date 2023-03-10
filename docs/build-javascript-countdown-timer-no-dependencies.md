# 只用 18 行 JavaScript 代码构建一个倒计时器

> 原文：<https://www.sitepoint.com/build-javascript-countdown-timer-no-dependencies/>

有时候，你需要建立一个 JavaScript 倒计时钟。你可能有一个活动、一次销售、一次促销或一场游戏。你可以用原始的 JavaScript 构建一个时钟，而不是使用最近的插件。虽然有许多很棒的时钟插件，但使用原始 JavaScript 会有以下好处:

*   您的代码将是轻量级的，因为它将没有依赖性。
*   你的网站会表现得更好。您不需要加载外部脚本和样式表。
*   你会有更多的控制权。你将构建一个完全按照你想要的方式运行的时钟(而不是试图让一个插件屈从于你的意愿)。

所以，事不宜迟，以下是如何用仅仅 18 行 JavaScript 代码制作自己的倒计时钟。

要深入了解 JavaScript 知识，请阅读我们的书， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。

## 基本时钟:倒计时到一个特定的日期或时间

下面是创建一个基本时钟的步骤的简要概述:

*   设置有效的结束日期。
*   计算剩余时间。
*   将时间转换成可用的格式。
*   将时钟数据作为可重复使用的对象输出。
*   在页面上显示时钟，当时钟到达零时停止。

### 设置有效的结束日期

首先，您需要设置一个有效的结束日期。这应该是 JavaScript 的 [Date.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) 方法可以理解的任何格式的字符串。例如:

[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 格式:

```
const deadline = '2015-12-31'; 
```

简短格式:

```
const deadline = '31/12/2015'; 
```

或者，长格式:

```
const deadline = 'December 31 2015'; 
```

这些格式中的每一种都允许您指定准确的时间和时区(或者在 ISO 日期的情况下指定相对于 UTC 的偏移量)。例如:

```
const deadline = 'December 31 2015 23:59:59 GMT+0200'; 
```

您可以在本文的[中阅读更多关于 JavaScript 中日期格式的内容。](https://msdn.microsoft.com/library/ff743760)

### 计算剩余时间

下一步是计算剩余时间。我们需要编写一个函数，它接受一个表示给定结束时间的字符串(如上所述)。然后我们计算那个时间和当前时间之间的差值。看起来是这样的:

```
function getTimeRemaining(endtime){
  const total = Date.parse(endtime) - Date.parse(new Date());
  const seconds = Math.floor( (total/1000) % 60 );
  const minutes = Math.floor( (total/1000/60) % 60 );
  const hours = Math.floor( (total/(1000*60*60)) % 24 );
  const days = Math.floor( total/(1000*60*60*24) );

  return {
    total,
    days,
    hours,
    minutes,
    seconds
  };
} 
```

首先，我们创建一个变量`total`，保存截止日期前的剩余时间。`Date.parse()`函数将时间字符串转换成以毫秒为单位的值。这使得我们可以将两个时间相减，得到中间的时间。

```
const total = Date.parse(endtime) - Date.parse(new Date()); 
```

### 将时间转换成可用的格式

现在我们想把毫秒转换成天、小时、分钟和秒。让我们以秒为例:

```
const seconds = Math.floor( (t/1000) % 60 ); 
```

我们来分析一下这是怎么回事。

1.  将毫秒除以 1000 转换成秒:`(t/1000)`
2.  将总秒数除以 60，取余数。你不需要所有的秒，只需要计算完分钟后剩余的时间:`(t/1000) % 60`
3.  将此向下舍入到最接近的整数。这是因为您想要完整的秒，而不是秒的分数:`Math.floor( (t/1000) % 60 )`

重复此逻辑，将毫秒转换为分钟、小时和天。

### 将时钟数据作为可重复使用的对象输出

准备好天数、小时数、分钟数和秒数后，我们现在准备将数据作为可重用对象返回:

```
return {
  total,
  days,
  hours,
  minutes,
  seconds
}; 
```

这个对象允许您调用您的函数并获得任何计算值。下面是一个如何获得剩余时间的例子:

```
getTimeRemaining(deadline).minutes 
```

方便吧？

### 显示时钟，当它到达零时停止

现在我们有了一个可以显示剩余天数、小时数、分钟数和秒数的函数，我们可以构建我们的时钟了。首先，我们将创建以下 HTML 元素来保存我们的时钟:

```
<div id="clockdiv"></div> 
```

然后，我们将编写一个函数，在新的 div 中输出时钟数据:

```
function initializeClock(id, endtime) {
  const clock = document.getElementById(id);
  const timeinterval = setInterval(() => {
    const t = getTimeRemaining(endtime);
    clock.innerHTML = 'days: ' + t.days + '<br>' +
                      'hours: '+ t.hours + '<br>' +
                      'minutes: ' + t.minutes + '<br>' +
                      'seconds: ' + t.seconds;
    if (t.total <= 0) {
      clearInterval(timeinterval);
    }
  },1000);
} 
```

这个函数有两个参数。这些是包含我们时钟的元素的 id，以及倒计时的结束时间。在函数内部，我们将声明一个`clock`变量，并使用它来存储对时钟容器 div 的引用。这意味着我们不必一直查询 DOM。

接下来，我们将使用`setInterval`每秒执行一个匿名函数。该功能将执行以下操作:

*   计算剩余时间。
*   将剩余时间输出到我们的 div。
*   如果剩余时间为零，停止计时。

此时，剩下的唯一一步是像这样运行时钟:

```
initializeClock('clockdiv', deadline); 
```

恭喜你！现在，您只需 18 行 JavaScript 代码就拥有了一个基本的时钟。

## 准备您的时钟进行展示

在设计时钟之前，我们需要对一些东西进行微调。

*   去除最初的延迟，让你的时钟立即显示。
*   让时钟脚本更有效，这样它就不会不断地重建整个时钟。
*   根据需要添加前导零。

### 消除初始延迟

在时钟中，我们使用`setInterval`每秒更新一次显示。这在大多数情况下是没问题的，除了在开始时会有一秒钟的延迟。为了消除这种延迟，我们必须在间隔开始之前更新一次时钟。

让我们将传递给`setInterval`的匿名函数移到它自己的独立函数中。我们可以把这个函数命名为`updateClock`。在`setInterval`外调用一次`updateClock`函数，然后在`setInterval`内再次调用。这样，时钟显示起来没有延迟。

在您的 JavaScript 中，替换为:

```
const timeinterval = setInterval(() => { ... },1000); 
```

有了这个:

```
function updateClock(){
  const t = getTimeRemaining(endtime);
  clock.innerHTML = 'days: ' + t.days + '<br>' +
                    'hours: '+ t.hours + '<br>' +
                    'minutes: ' + t.minutes + '<br>' +
                    'seconds: ' + t.seconds;
  if (t.total <= 0) {
    clearInterval(timeinterval);
  }
}

updateClock(); // run function once at first to avoid delay
var timeinterval = setInterval(updateClock,1000); 
```

### 避免连续重建时钟

我们需要使时钟脚本更加高效。我们希望只更新时钟中的数字，而不是每秒重建整个时钟。实现这一点的一种方法是将每个数字放在一个`span`标签中，并且只更新那些跨度的内容。

这是 HTML:

```
<div id="clockdiv">
    Days: <span class="days"></span><br>
    Hours: <span class="hours"></span><br>
    Minutes: <span class="minutes"></span><br>
    Seconds: <span class="seconds"></span>
</div> 
```

现在让我们参考这些元素。在定义了`clock`变量之后添加以下代码

```
const daysSpan = clock.querySelector('.days');
const hoursSpan = clock.querySelector('.hours');
const minutesSpan = clock.querySelector('.minutes');
const secondsSpan = clock.querySelector('.seconds'); 
```

接下来，我们需要修改`updateClock`函数，只更新数字。新代码将如下所示:

```
function updateClock(){
    const t = getTimeRemaining(endtime);

    daysSpan.innerHTML = t.days;
    hoursSpan.innerHTML = t.hours;
    minutesSpan.innerHTML = t.minutes;
    secondsSpan.innerHTML = t.seconds;

    ...
} 
```

### 添加前导零

现在时钟不再每秒重建，我们还有一件事要做:添加前导零。例如，时钟不是显示 7 秒，而是显示 07 秒。一个简单的方法是在一个数字的开头加上一串“0 ”,然后去掉最后两位。

例如，要将前导零添加到“秒”值中，您可以这样更改:

```
secondsSpan.innerHTML = t.seconds; 
```

对此:

```
secondsSpan.innerHTML = ('0' + t.seconds).slice(-2); 
```

如果您愿意，您也可以在分钟和小时前添加前导零。如果你已经走了这么远，恭喜你！您的时钟现在可以展示了。

**注意:**您可能需要点击代码笔中的“重新运行”以开始倒计时。

参见 [CodePen](https://codepen.io) 上的 site point([@ site point](https://codepen.io/SitePoint))
风格的笔 [JavaScript 倒计时钟](https://codepen.io/SitePoint/pen/NWxKgxN)。