# 发现高分辨率时间 API

> 原文：<https://www.sitepoint.com/discovering-the-high-resolution-time-api/>

在当今世界，性能真的很重要。开发人员需要能够准确地测量他们软件的性能。对于那些在网络上工作的人来说，W3C 有一个全新的 <abbr title="Application programming interface">API</abbr> 来可靠地记录时间。这个 API 就是*高分辨率时间 API* 。本文将探索高分辨率时间 API，并展示如何使用它。

为了测量一个函数的性能，web 开发人员过去常常使用 JavaScript `Date.now()`方法。通常，计时代码看起来像这样:

```
var startTime = Date.now();

// A time consuming function
foo();
var test1 = Date.now();

// Another time consuming function
bar();
var test2 = Date.now();

// Print results
console.debug("Test1 time: " + (test1 - startTime));
console.debug("Test2 time: " + (test2 - test1));
```

方法`Date.now()`基于系统时间检索当前时间戳。不幸的是，它的精度因用户代理而异，所以不太可靠。为了缓解这个问题，W3C 标准化了[高分辨率时间 API](https://www.w3.org/TR/hr-time/ "High Resolution Time API official documentation") 。该 API 被描述为“<q cite="https://www.w3.org/TR/user-timing/">一个 JavaScript 接口，它以亚毫秒级的分辨率提供当前时间，因此它不会受到系统时钟偏差或调整的影响</q>2012 年 10 月 23 日 <sup>rd</sup> 该规范成为了 W3C 提议的推荐标准——成为推荐标准前的最后一步。12 月 17 日<sup>日</sup>他们成为 W3C 推荐标准

## 高分辨率时间 API 的工作原理

我必须承认，这是我读过的最简单的 API，因为它只包含一个方法。该 API 扩展了`Performance`接口，该接口也被[导航定时 API](https://www.w3.org/TR/navigation-timing/ "Navigation Timing API official documentation") 使用。如果你从未听说过，看看[导航计时 API:如何有效地分析页面负载](https://www.sitepoint.com/profiling-page-loads-with-the-navigation-timing-api/ "Navigation Timing API: How to Profile Page Loads Efficiently")。

唯一公开的方法是`now()`，它返回一个以毫秒为单位表示当前时间的`DOMHighResTimeStamp`。时间戳非常准确，精确到千分之一毫秒。请注意，`Date.now()`返回自 1970 年 1 月 1 日 00:00:00 UTC 以来经过的毫秒数，`performance.now()`返回从`performance.timing.navigationStart()`(文档导航的开始)到`performance.now()`调用的毫秒数，小数部分为微秒。`Date.now()`和`performance.now()`的另一个重要区别是，后者是单调递增的，所以两个调用之间的差永远不会是负的。

也许您想知道高分辨率时间 API 将如何改变您的代码。好消息是这不会改变任何事情。你所需要做的就是用`performance.now()`来增加测量的精确度。考虑到这一点，前面的代码将被重写，如下所示。

```
var startTime = performance.now();

// A time consuming function
foo();
var test1 = performance.now();

// Another time consuming function
bar();
var test2 = performance.now();

// Print more accurate results
console.debug("Test1 time: " + (test1 - startTime));
console.debug("Test2 time: " + (test2 - test1));
```

## 和睦相处

目前，很少有浏览器支持高分辨率时间 API。唯一支持该 API 的桌面浏览器是 Internet Explorer 10、不带前缀的 Firefox 15+以及来自版本 20 的带有“webkit”前缀(`performance.webkitNow()`)的 [Chrome。似乎从 24 版](http://trac.webkit.org/changeset/115503 "Chrome introduced the support to High Resolution Time API in version 20")开始 [Chrome 将开始使用无前缀版本。在撰写本文时，还没有手机浏览器支持这个 API。](http://trac.webkit.org/changeset/131106 "Chrome will unprefix performance.now()")

由于支持范围不广，您首先需要一个函数来测试浏览器支持，以及它是否有前缀。如果浏览器使用无前缀版本的 API，下面的函数将返回一个空字符串。如果使用带前缀的版本，则返回前缀。如果不支持 API，则返回`null`。

```
function getPrefix() {
  var prefix = null;
  if (window.performance !== undefined) {
    if (window.performance.now !== undefined)
      prefix = "";
    else {
      var browserPrefixes = ["webkit","moz","ms","o"];
      // Test all vendor prefixes
      for(var i = 0; i < browserPrefixes.length; i++) {
        if (window.performance[browserPrefixes[i] + "Now"] != undefined) {
          prefix = browserPrefixes[i];
          break;
        }
      }
    }
  }
  return prefix;
}
```

对于不支持 API 的浏览器，可以使用一个 shim。

shim 的作者 Tony Gentilcore 是 API 的贡献者之一。

在他题为“【JavaScript 的更好计时器”的文章中，Gentilcore 编写了首先搜索本机支持的代码，并使用`Date.getTime()`方法作为后备。代码如下所示。

```
window.performance = window.performance || {};
performance.now = (function() {
  return performance.now       ||
         performance.mozNow    ||
         performance.msNow     ||
         performance.oNow      ||
         performance.webkitNow ||
         function() { return new Date().getTime(); };
})();
```

## 把所有的放在一起

本节将通过一个简单的演示页面引导您。该演示将首先测试浏览器支持，然后使用一个名为`doBenchmark`的函数，该函数依赖于两个虚拟函数，使用`performance.now()`方法进行基准测试。请注意，我引入了一个与 API 无关的`getTime()`函数。它唯一的目的是避免无用的重复，并有更干净的代码。演示的源代码如下所示。

```
<!DOCTYPE html>
<html>
  <head>
    <title>High Resolution Time API Test Page</title>
    <script>
      function foo() {
        for(var i = 0; i < 10000000; i++);
      }
      function bar() {
        for(var i = 0; i < 100000000; i++);
      }

      function getPrefix() {
        var prefix = null;
        if (window.performance !== undefined) {
          if (window.performance.now !== undefined)
            prefix = "";
          else {
            var browserPrefixes = ["webkit","moz","ms","o"];
            // Test all vendor prefixes
            for(var i = 0; i < browserPrefixes.length; i++) {
              if (window.performance[browserPrefixes[i] + "Now"] != undefined) {
                prefix = browserPrefixes[i];
                break;
              }
            }
          }
        }
        return prefix;
      }

      function getTime() {
        return (prefix === "") ? window.performance.now() : window.performance[prefix + "Now"]();
      }

      function doBenchmark() {
        if (prefix === null)
          document.getElementById("log").innerHTML = "Your browser does not support High Resolution Time API";
        else {
          var startTime = getTime();
          foo();
          var test1 = getTime();
          bar();
          var test2 = getTime();
          document.getElementById("log").innerHTML += "Test1 time: " + (test1 - startTime) + "<br />";
          document.getElementById("log").innerHTML += "Test2 time: " + (test2 - test1) + "<br />";
        }
      }
      var prefix = getPrefix();
      window.onload = doBenchmark;
    </script>
  </head>
  <body>
    <p id="log"></p>
  </body>
</html>
```

## 结论

在整篇文章中，我展示了什么是高分辨率时间 API，以及如何使用它。正如我提到的，它还没有得到广泛的支持，所以要准确地测试您的 web 应用程序，您还需要等待一段时间。然而，正如您所看到的，API 非常简单，因为它只包含一个方法。因此，一旦浏览器支持得到改善，迁移到高分辨率时间将是快速和无痛的。

## 分享这篇文章