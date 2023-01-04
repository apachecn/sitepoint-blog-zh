# 使用本地存储构建网页

> 原文：<https://www.sitepoint.com/building-web-pages-with-local-storage/>

储物——当你在建造或翻新房子时，储物是你需要的。越多越好。同样的原则也适用于网站建设；你需要在某个地方存储数据。

传统上，这是使用会话或缓存存储在服务器上，但向前发展，有 Web 存储，也称为本地存储或会话存储。HTML5 中的 Web 存储允许您在用户浏览器中存储信息。在我看来，这是一个游戏改变者。通过减少在服务器和客户机之间传输数据的需求，从服务器上移走存储可以提高性能。本文将关注本地存储，而不是会话存储。

Web 存储类似于 HTTP cookies。即使用户关闭浏览器、离开您的网站或重新启动浏览器，数据也会存储在浏览器中。不过最大的区别是 Web 存储并不是在每次请求时都转移到服务器。另一个区别是网络存储不会过期，除非你明确告诉它。

## 会话存储和本地存储有什么区别？

会话存储和本地存储之间的区别在于，即使浏览器关闭，本地存储仍然存在。会话存储数据不会在会话结束后保留，因此如果您关闭窗口或选项卡，数据会消失。但是，当您购物时，如果站点利用会话存储数据存储，一个窗口中的数据将与另一个窗口中的数据完全分开。

## 谁支持什么？

目前主要的浏览器都支持 web 存储。他们在这里:

*   IE8+
*   火狐 3.6 以上版本
*   Opera 10.5 以上
*   铬 5+
*   Safari 4+
*   iOS 3.2 以上
*   Android 2.1+版本

这总是在变化的，所以这是一个了解最新浏览器支持什么的好地方，你可以看看 [quirksmode](http://www.quirksmode.org/html5/storage.html) 。

## 数据存储在哪里？

如果一切都一样，网络不是一个美好的地方吗？根据您使用的浏览器，Web 存储器存储在磁盘的不同位置。我找不到所有浏览器的确切位置，所以如果你看到这篇文章并且你知道，请写一个评论让我们知道它存储在哪里。

**IE8+**–`%userprofiles%/Local Settings/Application Data/Microsoft/Internet Explorer/DOMStore`。有一个包含数据的 XML 文件

**Firefox**–数据存储在 profile 文件夹中的 [webappsstore.sqlite 文件](https://developer.mozilla.org/en/DOM/Storage)中。你可以下载一些 Firefox 插件来查看内容。

**Chrome**–
Windows XP—`C:Documents and Settings%username%Local SettingsApplication DataGoogleChromeUser DataDefault`
Vista/7—`C:Users%username%AppDataLocalGoogleChromeUser DataDefault`
Safari——数据存储在`C:Users%username%AppDataLocalApple ComputerSafariLocalStorage`的 sqllite 文件中。有一个本地存储文件与存储它的网站同名。

## Web 存储和 JavaScript

每个域和子域都有独特的网络存储。Web 存储仅限于一个域，因此如果 web 存储对象的任何成员被来源不同于 Document 对象的脚本访问，就会引发 SecurityError。

通过 [localStorage](https://developer.mozilla.org/en/DOM/Storage) 对象访问 web 存储器。这会返回一个存储接口。该界面有许多可用于操作 web 存储的功能。web 存储中的数据以键/值对的形式存储，因此可以存储字符串，但最有可能的是，您希望存储一个 [JSON](http://en.wikipedia.org/wiki/JSON) 对象。与 [sessionStorage](https://developer.mozilla.org/en/DOM/Storage) 接口不同，当用户关闭浏览器，或者打开多个标签页或窗口时，localStorage 会保存数据。

在使用 localStorage 之前，您需要检查浏览器是否支持它。您可以手动编写，但是更好的方法是使用 [Modernizr](http://www.modernizr.com/) JavaScript 库。Modernizer 会为您检查浏览器功能，以便您可以专注于工作。Modernizr 可以从[这里](http://www.modernizr.com/downloads/modernizr-2.0.6.js)下载。

从头开始，检查 localStorage 是否可用。

```
if (Modernizr.localstorage) {    
    $("#result").text('localStorage is available');
} else {    
    $("#result").text('localStorage is not available');   
}
```

示例代码可以在这里找到。

要添加数据，可以使用 setItem 函数，通过数组语法添加新值，或者创建新属性。下面的示例以三种方式添加数据。

```
 if (Modernizr.localstorage) {    
    var localStore = window.localStorage;
    localStore.setItem["Country"] = "USA";
    localStore["Country"] = "USA";
    localStore.Country = "USA";   
    $("#result").text(localStore.Country);
} else {    
    $("#result").text('localStorage is not available');   
}
```

示例代码可以在这里找到。

如果数据太大，将会抛出内存不足错误。为了避免这种错误，一个好办法是通过 remainingSpace 函数检查有多少空间可用。这将返回一个具有剩余磁盘或内存配额的整数值。

```
 if (Modernizr.localstorage) {
    var localStore = window.localStorage;
    if (localStore.remainingSpace > 0) {
        // you have space to do work
    }
} else {
    $("#result").text('localStorage is not available');
}
```

示例代码可以在这里找到[。](https://jsfiddle.net/malcolm_sheridan/rqrnG/)

## 更新

我忘了提这个，所以这里有一个更新。IE 中有 remaingSpace 功能。IE
团队已经将这种方法提交给 W3C，但是到目前为止它还没有成为规范。
检查是否可以添加数据的更好方法是在设置存储的
代码周围添加一个 try/catch。不出所料，每个浏览器都会抛出不同的错误。下面是我用来填充本地存储的代码
。

```
 if (Modernizr.localstorage) {
var localStore = window.localStorage;
localStore.RandomData = "Random";

for (i = 0; i 
```

这是结果错误名称，后面是每个浏览器的错误消息。

**火狐 7.0.1**

NS_ERROR_DOM_QUOTA_REACHED -已达到永久存储的最大大小

**IE9**

错误-内存不足

**Safari 5.1.1**

配额超出错误-配额超出错误:DOM 异常

铬合金 15.0.874

配额超出错误-配额超出错误:DOM 异常

示例代码可以在这里找到。顺便说一句，IE9 将停止在这个页面上对我的响应，所以测试的唯一方法是在我的个人网站上创建一个单独的页面。这是相同的
代码，你可以在这里访问它[。](http://msdeploy.net/html5/default.html)

从本地存储中删除数据也很容易。调用 removeItem 从存储中移除一项。

```
if (Modernizr.localstorage) {
    var localStore = window.localStorage;
    localStore.Country = 'USA';

    if (localStore.Country) {
        localStore.removeItem("Country");
    }
} else {
    $("#result").text('localStorage is not available');
} 
```

示例代码可以在这里找到。

从存储中读取数据可以通过数组语法或通过属性来完成。

```
if (Modernizr.localstorage) {
    var localStore = window.localStorage;
    localStore.Country = "USA";

    if (localStore.Country) {
        $("#result").text(localStore.Country);
    }
} else {
    $("#result").text('localStorage is not available');
}
```

示例代码可以在这里找到。

## 把所有的放在一起

如果浏览器支持的话，网络存储是很棒的。对于较老的浏览器，它们不支持 web 存储，但会支持 cookies。如果 web 存储不可用，一个好的备用方法是将数据存储在 cookie 中。下面的例子就是这样做的。

```
$(function() {
    $("#write").bind("click", function() {
        if (Modernizr.localstorage) {
            // your browser supports localStorage
            var localStore = window.localStorage;
            localStore.Country = "USA";
        } else {
            // your browser doesn't supports localStorage
            // write to a cookie
            var exdate = new Date();
            exdate.setDate(exdate.getDate() + 365);
            var cookie = "USA" + "; expires=" + exdate.toUTCString();
            document.cookie = "Country=" + cookie;
        }
    });
    $("#read").bind("click", function() {
        $("#result").text("");
        if (Modernizr.localstorage) {
            // your browser supports localStorage
            var localStore = window.localStorage;
            $("#result").text(localStore.Country);
        } else {
            // your browser doesn't supports localStorage
            // write to a cookie
            var cookies = document.cookie.split(";");
            for (i = 0; i 
```

示例代码可以在这里找到。

当你需要建立一个伟大的网站时，网络存储是一个很好的补充。将数据存储在客户机上减少了服务器上所需的内存量，也使数据更接近用户，这意味着更好的性能。好多少，这完全取决于你存储了多少数据。但是常识法则适用；不要将大量数据存储在 web 存储器中。尽可能让东西轻便。你的用户最终会感谢你的。

## 分享这篇文章