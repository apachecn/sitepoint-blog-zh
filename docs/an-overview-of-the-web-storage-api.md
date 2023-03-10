# Web 存储 API 概述

> 原文：<https://www.sitepoint.com/an-overview-of-the-web-storage-api/>

Web 开发人员一直渴望一种长期存储数据的方法。Cookies 是一个选项，但它们只能存储 4KB 的数据。此外，cookies 会随每个 HTTP 请求一起发送到服务器。这意味着 cookiess，尤其是大的 cookie，会消耗大量的网络带宽。还有其他实现存储技术的尝试，但大部分都是黑客行为。然后，HTML5 和[网络存储 API](http://dev.w3.org/html5/webstorage/ "Web Storage") 出现了。

Web 存储 API 定义了两种类型的存储区域——本地存储和会话存储。本地存储是持久数据，它会一直存在，直到被显式删除，或者直到浏览器的缓存被清除。根据规范，浏览器应该为每个域分配至少 5MB 的本地存储空间。第二种存储类型，即会话存储，也是持久数据，但是该数据被绑定到“顶级浏览上下文”(即浏览器选项卡或窗口)。会话数据会一直保留，直到被删除或浏览上下文被关闭。当用户与同一网站的多个实例交互时，会话存储特别有用。在这种情况下，使用本地存储可能会导致不同的实例覆盖彼此的数据。

这两种类型的存储区域通过名为“localStorage”和“sessionStorage”的全局对象来访问。两个存储区域实现完全相同的 API。数据存储为键/值对，所有数据都以字符串形式存储。向存储中添加数据时，数据会隐式转换为字符串。但是，当从存储中检索字符串数据时，需要使用函数(如 [parseInt()](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/parseInt "parseInt() Documentation") )将其显式转换为适当的数据类型。在处理对象时，应该使用 [JSON.parse()](http://cjihrig.com/blog/native-browser-support-for-json-part-1/ "Native Browser Support for JSON (Part 1 of 2)") 和 [JSON.stringify()](http://cjihrig.com/blog/native-browser-support-for-json-part-2-of-2/ "Native Browser Support for JSON (Part 2 of 2)") 方法进行序列化和反序列化。

# 检测存储支持

Web 存储 API，像许多其他 HTML5 特性一样，并不是所有的浏览器都支持。要检查浏览器是否支持存储，请使用下面显示的函数。该函数检查全局“localStorage”对象是否存在。可以创建一个类似的函数来检查会话存储，但是可以有把握地假设，如果一个存储区域存在，那么另一个也存在。

```
function localStorageSupported() {
 try {
  return "localStorage" in window && window["localStorage"] !== null;
 } catch (e) {
  return false;
 }
}
```

# 存储数据

使用 setItem()方法将数据添加到存储中。setItem()接受一个键和值作为参数。如果存储中不存在该键，则添加键/值对。如果该键已经存在，则更新该值。下面显示了几个 setItem()用法的例子。这些示例显示了如何将各种类型的数据添加到本地和会话存储中。请注意,“key”参数必须始终是一个字符串，而“value”的类型可以不同。

```
localStorage.setItem("key", "value");
sessionStorage.setItem("foo", 3.14);
localStorage.setItem("bar", true);
sessionStorage.setItem("baz", JSON.stringify(object));
```

还可以使用对象属性赋值语句将数据添加到存储中。下面使用赋值语句重写了前面的 setItem()示例。请注意，对第一行中“key”的赋值将会自动失败。这是因为存储区有一个名为 key()的内置函数，我们将在后面介绍。因此，API 方法是访问存储的首选方式。

```
localStorage.key = "value"; // this fails silently
sessionStorage.foo = 3.14;
localStorage["bar"] = true;
sessionStorage["baz"] = JSON.stringify(object);
```

如果一个站点试图存储太多的数据，最终将超过浏览器的存储配额，并引发异常。为了处理这种情况，在存储数据时应该使用 try-catch 块。这方面的一个例子如下所示。

```
try {
 localStorage.setItem("key", "value");
} catch (e) {
 alert("Exceeded Storage Quota!");
}
```

# 读取存储的数据

要从存储中读取数据，需要使用 getItem()方法。getItem()将一个查找键作为唯一的参数。如果键存在于存储器中，则返回相应的值。如果该键不存在，则返回 null。以下示例使用 getItem()方法检索存储在 setItem()示例中的数据。

```
var string = localStorage.getItem("key");
var number = sessionStorage.getItem("foo");
var boolean = localStorage.getItem("bar");
var object = JSON.parse(sessionStorage.getItem("baz"));
```

还可以通过读取“localStorage”和“sessionStorage”对象的属性来访问存储的数据。下面使用对象属性语法重写了前面的 getItem()示例。

```
var string = localStorage.key;
var number = sessionStorage.foo;
var boolean = localStorage["bar"];
var object = JSON.parse(sessionStorage["baz"]);
```

# 迭代存储的数据

通常，有必要以编程方式循环访问存储中的所有项目。循环的上限由特定存储区域的“长度”属性决定。使用 key()方法可以一次检索一个存储的键。key()接受一个整数参数，作为存储区域的索引。下面显示了在“localStorage”中循环每个键/值对的示例。当然，通过用“session storage”替换“localStorage ”,可以用类似的方式处理会话存储。

```
for (var i = 0; i < localStorage.length; i++) {
 var key = localStorage.key(i);
 var value = localStorage.getItem(key);

 // do something with the key and value
}
```

# 删除存储的数据

当不再需要数据时，应该明确地将其删除。对于本地存储来说尤其如此，因为它甚至会在浏览器关闭后继续存在。要从存储中删除单个的键/值对，可以使用 removeItem()方法。removeItem()方法将待删除的键作为其唯一的参数。如果密钥不存在，那么什么都不会发生。removeItem()方法的示例如下所示。

```
localStorage.removeItem("key");
sessionStorage.removeItem("foo");
localStorage.removeItem("bar");
sessionStorage.removeItem("baz");
```

删除操作符也可用于删除存储的数据。下面使用 delete 代替 removeItem()重写了前面的示例。

```
delete localStorage.key;
delete sessionStorage.foo;
delete localStorage["bar"];
delete sessionStorage["baz"];
```

removeItem()用于删除个别数据，而 clear()方法用于删除所有存储的数据。clear()方法的用法如下所示。

```
localStorage.clear();
sessionStorage.clear();
```

# 存储事件

在任何给定时间，用户都有可能打开同一个站点的多个实例。在一个实例中对存储区域进行的更改需要在其他实例中反映出来。Web 存储 API 使用“Storage”事件来完成这种同步。当存储区域改变时，对于共享该存储区域的任何其他标签/窗口，触发“存储”事件。注意,“存储”事件是针对改变存储区域的标签/窗口而触发的*而不是*。

存储区域可以通过调用 setItem()、removeItem()和 clear()来更改。然而，并不是所有对这些方法的调用都会改变存储区域。例如，对空存储区域调用 clear()或对不存在的键调用 removeItem()不会更改存储区域，因此不会引发事件。

“存储”事件对象有几个感兴趣的字段，如下所述。字段描述之后是一个示例“存储”事件处理程序。

*   “key”――该字段是 setItem()或 removeItem()的关键参数，或者当 clear()导致事件被激发时为 null。
*   “new Value”――setItem()的“value”参数反映在该字段中。调用 removeItem()和 clear()会导致此字段为空。
*   “old value”――该字段保存调用 setItem()或 removeItem()之前的键值。调用 clear()会导致该字段为空。
*   “URL”―“URL”字段存储其存储区域受到影响的页面的地址。
*   “存储区域”―“存储区域”字段对应于被改变的本地或会话存储区域。

```
window.addEventListener("storage", function(event) {
 var key = event.key;
 var newValue = event.newValue;
 var oldValue = event.oldValue;
 var url = event.url;
 var storageArea = event.storageArea;

 // handle the event
});
```

# 示例页面

下面的代码实现了一个用于操作本地存储的示例页。该页面也可在线访问[这里](http://www.cjihrig.com/development/html5/storage.htm "Web Storage Example")。该示例涵盖了整个本地存储 API，包括“storage”事件。为了查看“存储”事件的运行情况，页面必须在同一浏览器的至少两个单独的选项卡/窗口中打开。“存储”事件也只有在页面通过 HTTP 提供时才起作用(即 file://协议不起作用)。

```
<!DOCTYPE html>
<html>
<head>
 <title>Web Storage Example</title>
 <meta charset="UTF-8" />
 <script>
  "use strict";
  window.addEventListener("load", function(event) {
   var key = document.getElementById("key");
   var value = document.getElementById("value");
   var add = document.getElementById("add");
      var remove = document.getElementById("remove");
      var clear = document.getElementById("clear");
      var content = document.getElementById("content");

   add.addEventListener("click", function(event) {
        if (key.value !== "") {
     try {
           localStorage.setItem(key.value, value.value);
     } catch (e) {
      alert("Exceeded Storage Quota!");
     }
          refreshContents();
        }
      });

      remove.addEventListener("click", function(event) {
        if (key.value !== "") {
          localStorage.removeItem(key.value);
          refreshContents();
        }
      });

      clear.addEventListener("click", function(event) {
        localStorage.clear();
        refreshContents();
      });

      window.addEventListener("storage", function(event) {
        var k = event.key;
        var newValue = event.newValue;
        var oldValue = event.oldValue;
        var url = event.url;
        var storageArea = event.storageArea;

        alert("EVENT:n" + k + "n" + newValue + "n" + oldValue + "n" + url + "n" + storageArea);
        refreshContents();
      });

      function refreshContents() {
        var str = "";

        for (var i = 0, len = localStorage.length; i < len; i++) {
          var k = localStorage.key(i);
          var v = localStorage.getItem(k);

          str += "'" + k + "' = '" + v + "'<br />";
        }

    key.value = "";
    value.value = "";
        content.innerHTML = str;
      }

      refreshContents();
    });
  </script>
</head>
<body>
  Key:  <input type="text" id="key" /><br />
  Value: <input type="text" id="value" /><br />
  <input type="button" id="add" value="Add to Storage" />&nbsp;
  <input type="button" id="remove" value="Remove from Storage" />&nbsp;
  <input type="button" id="clear" value="Clear Storage" /><br />
  Contents of Local Storage:<br />
  <span id="content"></span>
</body>
</html>
```

# 要记住的事情

*   本地存储会一直存在，直到它被显式删除或浏览器的缓存被清除。
*   会话存储会一直存在，直到它被显式删除或浏览上下文被关闭。
*   一个浏览器存储的数据不能被另一个浏览器访问。比如 Chrome 存储的数据，Firefox 是看不到的。
*   对象应该存储为 JSON 字符串。
*   出于安全原因，不应存储敏感数据，尤其是本地存储。
*   对存储区域的更改会引发“存储”事件。
*   和许多其他 HTML5 特性一样，web 存储还没有被一致地实现。

[通过 Shutterstock 存储](http://www.shutterstock.com/cat.mhtml?lang=en&search_source=search_form&version=llv1&anyorall=all&safesearch=1&searchterm=storage&search_group=#id=59314054&src=e0f1864e9ad3dfdce4483f891a30273f-1-0)图像

## 分享这篇文章