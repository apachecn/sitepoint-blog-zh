# 导航计时 API:如何有效地分析页面负载

> 原文：<https://www.sitepoint.com/profiling-page-loads-with-the-navigation-timing-api-2/>

页面加载时间是用户体验最重要的方面之一。当页面加载太慢时，用户会很快变得沮丧，并把他们的业务转移到其他地方。不幸的是，对缓慢的页面加载进行故障排除通常不是一个简单的过程，因为许多因素都会影响总时间。例如，页面的加载时间会受到用户的浏览器、网络条件、服务器负载和应用程序代码等因素的影响。幸运的是，导航定时 API 可以帮助我们轻松地做到这一点。

作为开发人员，收集这些不同因素的数据的方法在过去受到了限制。对于许多开发人员来说，JavaScript Date 对象一直是收集性能数据的标准。例如，调用页面的 load 事件处理程序后，下面的代码通过比较时间戳来测量加载时间。

```
var start = new Date();

window.addEventListener("load", function() {
  var elapsed = (new Date()).getTime() - start.getTime();
}, false);
```

这种方法有几个问题。第一， [JavaScript 时间是出了名的不准确](http://ejohn.org/blog/accuracy-of-javascript-time/ "Accuracy of JavaScript Time")。其次，使用 Date 对象会带来开销，并使应用程序代码变得混乱。第三，只有当代码在浏览器中运行时，Date 对象才能测量执行时间。Date 对象不能提供任何关于页面加载过程的数据，包括服务器、网络等。

## 提示导航计时 API

为了提供更加准确和全面的页面负载数据，W3C 提出了[导航计时 API](https://www.w3.org/TR/navigation-timing/ "Navigation Timing") 。提议的 API 在整个页面加载过程中提供了更详细的计时信息。与 Date 对象不同，导航计时 API 可以提供与 DNS 查找、TCP 连接建立、页面重定向、构建 DOM 所花费的时间以及各种其他指标相关的测量。导航计时也直接内置在浏览器中，这意味着不会产生额外的开销。

## 检测支架

导航定时 API 目前仅在 Internet Explorer 9+、Firefox 和 Chrome 中支持。因此，在尝试使用它之前，应该检测对 API 的支持。API 在窗口对象中定义为“window.performance.timing”。下面的函数检测 API 是否受支持。

```
function supportsNavigationTiming() {
  return !!(window.performance && window.performance.timing);
}
```

## 记录的事件

API 记录了页面加载过程中许多里程碑发生的时间。这些事件中的每一个都存储为“window.performance.timing”对象的属性。下面的列表描述了每个事件。如果给定的事件没有发生(例如页面重定向)，那么它的值为零。注意:Mozilla 声称事件发生的顺序是[。](https://developer.mozilla.org/en-US/docs/Navigation_timing "Navigation Timing | Mozilla Developer Network")

*   navigation start——这表示浏览器完成卸载前一个文档的提示后的时间。如果没有前面的文档，那么“navigationStart”等于“fetchStart”(见下一项)。这是用户感觉到的页面加载时间的开始。
*   fetchStart―“fetchStart”表示浏览器开始搜索 URL 之前的时间。搜索过程包括检查应用程序缓存，或者如果文件没有被缓存，则从服务器请求文件。
*   domainLookupStart―“domainLookupStart”值对应于对 URL 进行 DNS 查找之前的时间。如果不需要 DNS 查找，则该值与“fetchStart”相同。
*   domainLookupEnd——该值表示 DNS 查找发生后的时间。如果不需要 DNS 查找，则该值与“fetchStart”相同。
*   connect start——这表示浏览器连接到服务器之前的时间。如果 URL 是缓存或本地资源，则该值等于“domainLookupEnd”。
*   connectEnd——一旦建立了与服务器的连接，就设置了“connectEnd”时间。如果 URL 是缓存或本地资源，则该值与“domainLookupEnd”相同。
*   secureConnectionStart――如果使用 HTTPS 协议，则“secureConnectionStart”被设置为安全握手开始之前的时间。如果浏览器不支持 HTTPS，这个值应该是未定义的。
*   request start―“request start”表示浏览器发送 URL 请求之前的时间。API 没有定义“请求端”值。
*   redirect start―“redirect start”表示启动重定向的 URL 提取的开始时间。
*   redirectEnd——如果存在任何重定向，“redirect end”表示收到最后一个重定向响应的最后一个字节之后的时间。
*   response start——这对应于浏览器收到响应的第一个字节后的时间。
*   response end——这表示浏览器收到响应的最后一个字节后的时间。
*   unload eventstart——这表示前一个文档的“unload”事件被触发之前的时间。如果没有以前的文档，或者如果以前的文档来自不同的来源，则该值为零。
*   unload event end——这表示前一个文档的“unload”事件触发后的时间。如果没有以前的文档，或者如果以前的文档来自不同的来源，则该值为零。如果有任何重定向指向不同的原点，那么“unloadEventStart”和“unloadEventEnd”都为零。
*   DOM Loading―“DOM loading”表示“document.readyState”值设置为“loading”之前的时间。
*   domInteractive:“domInteractive”对应于“document.readyState”值设置为“Interactive”之前的时间。
*   domContentLoadedEventStart――这表示 DOMContentLoaded 事件被触发之前的时间。
*   domContentLoadedEventEnd——这表示 DOMContentLoaded 事件触发后的时间。
*   DOM Complete―“DOM complete”值表示“document.readyState”值设置为“complete”之前的时间。
*   loadEventStart——这个值代表窗口的 load 事件被触发之前的时间。如果事件尚未触发，则该值为零。
*   load event end——这表示窗口的 load 事件触发后的时间。如果事件没有被触发，或者仍在运行，则值为零。

## 导航类型

导航定时 API 还定义了用于确定用户如何登陆特定页面的接口。“window.performance”对象还包含一个“navigation”对象，该对象包含两个属性——“type”和“redirectCount”。“type”属性提供了用户导航到当前页面的方法。下面的列表描述了“type”可以保存的值。

*   如果用户通过键入 URL、单击链接、提交表单或通过脚本操作导航到某个页面，则“type”的值为零。
*   如果用户重新加载/刷新页面，那么“type”等于 1。
*   如果用户通过历史导航到一个页面(后退或前进按钮)，那么“类型”等于 2。
*   对于任何其他情况，“类型”等于 255。

“redirectCount”属性包含被重定向到当前页面的次数。如果没有重定向发生，或者如果任何重定向来自不同的源，则“redirectCount”为零。以下示例显示了如何访问导航数据。

```
var navigation = window.performance.navigation;
var navType = navigation.type;
var redirectCount = navigation.redirectCount;
```

## 理解数据

导航计时 API 对于计算页面加载时间的某些部分非常有用。例如，执行 DNS 查找所需的时间可以通过从“timing.domainLookupStart”中减去“timing . domain lookupstart”来计算。以下示例计算了几个有用的指标。“userTime”对应于用户经历的总页面加载延迟。“dns”和“connection”变量分别表示执行 DNS 查找和连接到服务器所需的时间。向服务器发送请求和接收响应所用的总时间存储在“requestTime”中。最后，完成文档获取的总时间(包括访问任何缓存等。)存储在“fetchTime”中。请注意，setTimeout()函数是从窗口加载事件处理程序中调用的。这确保了导航定时数据直到加载事件结束后才被使用。如果要从加载事件处理程序访问计时数据，则“timing.loadEventEnd”的值将为零。

```
window.addEventListener("load", function() {
  setTimeout(function() {
    var timing = window.performance.timing;
    var userTime = timing.loadEventEnd - timing.navigationStart;
    var dns = timing.domainLookupEnd - timing.domainLookupStart;
    var connection = timing.connectEnd - timing.connectStart;
    var requestTime = timing.responseEnd - timing.requestStart;
    var fetchTime = timing.responseEnd - timing.fetchStart;

    // use timing data
  }, 0);
}, false);
```

导航计时 API 可以与 Ajax 调用结合使用，向服务器报告实际的用户数据。这很有用，因为它允许开发人员看到页面在真实世界中对用户的行为。这些数据也可以用来创建页面加载过程的[可视化。事实上，谷歌分析已经](http://www.cjihrig.com/development/html5/navigation-timing.htm "Navigation Timing Data")[将导航计时](http://calendar.perfplanet.com/2011/a-practical-guide-to-the-navigation-timing-api/ "A Practical Guide to the Navigation Timing API")整合到其报告中。

## 要记住的事情

*   JavaScript Date 对象不能准确地测量页面负载数据，因为它在浏览器中运行之前不知道请求。
*   导航定时 API 直接内置在浏览器中，提供更详细的定时测量。
*   该 API 还跟踪用户如何导航到页面。
*   导航定时数据可以发送到服务器进行分析。

## 分享这篇文章