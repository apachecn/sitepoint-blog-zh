# 页面可见性 API 简介

> 原文：<https://www.sitepoint.com/introduction-to-page-visibility-api/>

手机很酷。手机应用更酷。不幸的是，在大多数情况下，移动连接很糟糕，因为它们很慢或者你没有无限的带宽。拥有不浪费用户资源的丰富的 web 应用程序是很棒的，尤其是当他们不看那个页面的时候。本文将向您展示如何使用页面可见性 API 部分解决这个问题和其他问题。

在过去的几年中，已经引入了几个新的伟大的 API 来帮助我们的日常工作，例如[地理定位 API](https://www.sitepoint.com/using-the-html5-geolocation-api/ "Using the HTML5 Geolocation API") 、[导航定时 API](https://www.sitepoint.com/profiling-page-loads-with-the-navigation-timing-api/ "Profiling Page Loads with the Navigation Timing API") 和[全屏 API](https://www.sitepoint.com/html5-full-screen-api/ "How to Use the HTML5 Full-Screen API") 。[页面可见性 API](https://www.w3.org/TR/page-visibility/ "Page Visibility API specifications") <q cite="https://www.w3.org/TR/page-visibility/">为网站开发者定义了一种以编程方式确定页面当前可见性状态的方法，以便开发强大且 CPU 高效的 web 应用</q>。从 2012 年 7 月 26 日<sup>到 2012 年 7 月 11 日</sup>，这是 W3C 的候选推荐标准，因此被认为是稳定的。

你可能想知道的第一件事是它们如何提高性能和节省带宽。想象一下这样一种情况，您有一个非常棒基于 AJAX 的 web 应用程序，它每五秒钟来回发送一次数据。如果用户在应用程序运行时将浏览器标签发送到后台，它仍然会每五秒发送一次数据，并且如果用户在 10 分钟后将标签放在前台。如果应用程序减慢更新速度或者停止更新，直到用户再次查看页面，这不是很好吗？这就是资源优化发挥作用的地方，也是页面可见性 API 发挥关键作用的地方。

## 页面可见性 API 是如何制作的

这些 API 非常简单，事实上它们只有一个名为`visibilitychange`的事件和两个只读属性，分别属于`document`、`hidden`和`visibilityState`。`hidden`是一个布尔值，如果页面不可见，即使是最小的部分，它也是`true`，这通常发生在选项卡在后台或浏览器最小化时。需要注意的是，这条规则对于以全屏模式运行的辅助工具有一些例外。你可以通过阅读[隐藏的规格](https://www.w3.org/TR/page-visibility/#dom-document-hidden "hidden specifications")来了解更多。

`visibilityState`是指定文档当前状态的枚举，由以下值组成:

*   `hidden`:文件根本看不见
*   `visible`:文档或文档的一部分是可见的
*   `prerender`:文档在屏幕外加载，不可见
*   `unloaded`:文件将要被卸载

请注意，最后两个值`prerender`和`unloaded`、[是可选的](https://www.w3.org/TR/page-visibility/#pv-prerender "prerender specifications")。此外，像`hidden`属性一样，`hidden`值[也有一些关于辅助技术的例外](https://www.w3.org/TR/page-visibility/#pv-page-hidden "hidden value exceptions")。

## 和睦相处

目前，支持这些 API 的浏览器并不多，那些仍然使用供应商前缀的浏览器也不多。这导致了支持问题，因为您必须管理所有的前缀才能有一个工作代码。目前支持页面可见性 API 的桌面浏览器有 Chrome 13+，Internet Explorer 10，Firefox 10+，以及 [Opera beta 12.10](http://www.opera.com/docs/changelogs/unified/1210b/ "Opera beta 12.10+") 。支持该 API 的移动浏览器是 Android 4.0+上的 Chrome 和 Android 和 Symbian 上的 Opera Mobile 12.1+(来源【MobileHTML5.org——我自己在 Android 4.0 上测试)。

有点烦人的是，根据 camelCase 约定，如果属性带有 vendor 前缀，那么实际的属性名首字母大写，而如果没有前缀，则小写。为了清楚起见，我们以`hidden`属性为例。如您所见，它以小写字母开头，但如果有前缀，则以大写字母“h”开头，因此要测试支持，您不能编写类似如下的代码:

```
var browserPrefixes = ["", "webkit","moz","ms","o"];
for(var i = 0; i < browserPrefixes.length; i++) {
  if (document[browserPrefixes[i] + "hidden"] != undefined)
    // here goes the code
}
```

你必须拆分这些情况，就像下面这样，或者对字符串使用一些技巧。

```
// Test for unprefixed version
if (document.hidden !== undefined)
  // here goes the code
else {
  // Test for prefixed version
  var browserPrefixes = ["webkit", "moz", "ms", "o"];
  for(var i = 0; i < browserPrefixes.length; i++) {
    if (document[browserPrefixes[i] + "Hidden"] != undefined) {
      // here goes the code
    }
  }
}
```

一如既往，就像其他 API 一样，已经发布了一堆 polyfills 来在不支持它们的浏览器中使用这些 API。其中一些聚合填充是[visible . js](https://github.com/addyosmani/visibly.js "visibly polyfill")和 [isVis.js](https://gist.github.com/3793323 "isVis polyfill") 。

## 让我们创建一个工作示例

在本节中，我将指导您创建一个使用页面可见性 API 的简单演示页面。该页面将首先测试浏览器支持，然后统计用户实际查看该页面的次数，并记录其状态。在我们的演示中只有三个关键功能。第一个测试浏览器是否使用厂商前缀版本，它将创建在显示的最后一个代码之上。如果浏览器使用不带前缀的版本，它将返回一个空字符串；如果使用带前缀的版本，它将返回供应商前缀；如果浏览器不支持 API，它将返回`null`。

```
function getPrefix() {
  var prefix = null;
  if (document.hidden !== undefined)
    prefix = "";
  else {
    var browserPrefixes = ["webkit","moz","ms","o"];
    // Test all vendor prefixes
    for(var i = 0; i < browserPrefixes.length; i++) {
      if (document[browserPrefixes[i] + "Hidden"] != undefined) {
        prefix = browserPrefixes[i];
        break;
      }
    }
  }
  return prefix;
}
```

第二个函数记录状态，并在页面显示时增加查看次数。

```
function countView() {
  // The page is in foreground and visible
  if (document.hidden === false || document[prefix + "Hidden"] === false)
    views++;

  document.getElementById("log").innerHTML += "Your view count is: " + views + ". " + "Your page current state is: " + document[(prefix === "" ? "v" : prefix + "V") + "isibilityState"] + "
";
}
```

第三个也是最后一个函数测试浏览器是否支持 API，如果测试结果是`true`，它会将一个处理程序附加到`visibilitychange`事件，否则它会通知用户。请注意，管理供应商前缀也需要这个函数。

```
function testPageVisibilityApi() {
  if (prefix === null)
    document.getElementById("log").innerHTML = "Your browser does not support Page Visibility API";
  else {
    document.addEventListener(prefix + "visibilitychange", countView);
    countView();
  }
}
```

## 把所有的放在一起

给定上一节中显示的函数，最终且完全有效的代码如下。

```
<!DOCTYPE html>
<html>
  <head>
    <title>Page Visibility API Test Page by Aurelio De Rosa</title>
    <script>
      function getPrefix() {
        var prefix = null;
        if (document.hidden !== undefined)
          prefix = "";
        else {
          var browserPrefixes = ["webkit","moz","ms","o"];
          // Test all vendor prefixes
          for(var i = 0; i < browserPrefixes.length; i++) {
            if (document[browserPrefixes[i] + "Hidden"] != undefined) {
              prefix = browserPrefixes[i];
              break;
            }
          }
        }
        return prefix;
      }

      function countView() {
        // The page is in foreground and visible
        if (document.hidden === false || document[prefix + "Hidden"] === false)
          views++;

        document.getElementById("log").innerHTML += "Your view count is: <b>" + views +
          "</b>. " + "Your page current state is: <b>" +
          document[(prefix === "" ? "v" : prefix + "V") + "isibilityState"] + "</b><br />";
      }

      function testPageVisibilityApi() {
        if (prefix === null)
          document.getElementById("log").innerHTML = "Your browser does not support Page Visibility API";
        else {
          document.addEventListener(prefix + "visibilitychange", countView);
          countView();
        }
      }

      var views = 0;
      var prefix = getPrefix();
      window.onload = testPageVisibilityApi;
    </script>
  </head>
  <body>
    <p id="log"></p>
  </body>
</html>
```

其他一些好的例子可以在 Mozilla 开发者网络上找到。

## 结论

在本文中，我展示了什么是页面可见性 API 以及如何使用它们。W3C 的人们帮助移动设备的意图——不仅仅是节省资源和连接带宽——真的很有价值，我希望尽快看到它们被广泛使用。

正如您所看到的，API 非常简单，仅由两个属性和一个事件组成，因此您可以在几分钟内开始使用它们来改进您的 web 应用程序。

然而，目前它们并不真正可靠，因为它们在浏览器中的支持很差，所以你必须使用 polyfill。

如果您对 JavaScript APIs 感兴趣，请查看 SitePoint network … [JSPro](https://www.sitepoint.com/javascript/apis-javascript/) 最新网站上的 API 部分。

## 分享这篇文章