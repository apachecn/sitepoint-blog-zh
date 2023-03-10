# HTML5 应用程序缓存

> 原文：<https://www.sitepoint.com/html5-application-cache/>

缓存有两种风格:数据缓存和应用程序缓存。

在 HTML5 中，可以借助 [Web storage](https://www.sitepoint.com/html5-web-storage/) 、 [indexedDB](https://www.sitepoint.com/up-close-and-personal-with-html5-indexeddb/) 等完成数据缓存。

应用程序缓存使 web 应用程序能够存储其资源，如 HTML、CSS、图像、JavaScript 页面等。以便在脱机或互联网连接不可用时可以完全或部分访问应用程序。

## 清单文件

应用程序缓存可以在缓存清单文件的帮助下完成。清单文件指定要在浏览器中缓存的资源列表。

### 清单文件中的节

**缓存清单:**清单文件的第一行应该以关键字 CACHE MANIFEST 开头。

```
CACHE MANIFEST
# 2010-02-08:v2
index.html
images/profile.JPG
css/main.css
```

要缓存的资源列表应该在缓存清单部分下面提到。

记住缓存与特定网页相关的图像和 CSS 文件。

在上面的例子中，index.html、配置文件图像和 main.css 文件是浏览器应该缓存的资源。

以#符号开头的行表示注释。

**回退:**此部分通知浏览器在离线期间尝试访问未缓存的文件时要显示的内容。

```
FALLBACK
/html /offline.html
```

上面的示例表明，当浏览器在脱机状态下试图访问 html 文件夹下的未缓存文件时，应该显示 offline.html。

```
/index.html /offline.html
```

指定如果 index.html 没有被缓存，在脱机期间浏览器应该显示 offline.html 而不是 index.html

```
/ /offline.html
```

表示每当用户脱机时，根文件夹下所有未缓存的文件都将被 offline.html 替换。

**网络:**本节列出了只能在联机状态下访问且不应缓存的资源

```
NETWORK
/sample.php
```

上面的示例指定了 sample.php 只能在联机期间访问

```
NETWORK
*
```

network 部分中的星号表示除了 CACHE MANIFEST 部分中提到的资源之外的所有资源都只能在联机时访问。

### 样本文件

```
CACHE MANIFEST
# 2010-02-08:v12

CACHE:
index.html
images/IMG_1713.JPG
css/main.css

# Require the user to be online.
NETWORK:

*

# offline.html will be served in place of all other .html files
FALLBACK:
/index.html /offline.html
```

## 引用清单文件

准备好清单文件后，应该使用 html 标记中的 manifest 属性从您想要缓存的网页中引用它，如下所示。

```
<!DOCTYPE html>
<html manifest="developer.appcache">
Page to be cached goes here

</html>
```

进行引用的页面将被自动缓存，即使它没有在清单文件中列出。

## 缓存清除

缓存将保持不变，直到发生以下任何一种情况
浏览器缓存被用户清除。
更新缓存的资源。请注意，更新清单文件中的资源不会使浏览器自动重新缓存该文件。应该更改清单文件。想知道当要缓存的资源列表没有变化时，应该在清单文件中更改什么？只需为版本添加一个注释，并在资源发生变化时不断更改版本号。这应该够了。
清单文件中的变化。
应用缓存以编程方式更新。

## 缓存事件

可以使用 window 对象以编程方式访问应用程序缓存。

浏览器与清单文件交互并构建缓存的过程涉及许多缓存事件。可以使用应用程序缓存对象的 status 属性以编程方式跟踪缓存过程中应用程序缓存的状态。

```
window.applicationCache.status.
```

当浏览器第一次访问包含清单属性的网页时，会发生以下事件。
检查:当浏览器访问网页并读取`元素上的清单属性时发生。
下载:当浏览器开始下载清单文件中的资源时发生。
进度:提供已下载文件数量和尚未下载文件数量的信息。
缓存:当清单文件中的所有资源都已下载，并且应用程序可以脱机使用时发生。`

 `如果浏览器已经访问了网页，并且能够识别清单文件，则可能会发生以下事件
无更新:清单文件没有变化时发生
下载:缓存清单文件和文件中列出的资源发生变化时发生。浏览器开始下载资源。
进度:提供关于已下载文件数量和尚未下载文件数量的信息
更新就绪–当再次下载清单文件中的资源以进行更改和新应用程序(如果准备好用于离线)时发生

在以下情况下会发生错误事件
清单文件是 404 或 410 页面，因此缓存被中止
在更新运行时缓存清单文件被更改。
下载清单文件中列出的资源时出错。
清单文件未更改，但引用清单的页面未能正确下载

如果从服务器中移除了应用程序的清单文件，则浏览器会移除使用该清单的所有缓存资源，并向 applicationCache 对象发送一个“过时”事件。

## 缓存更新:

您可以通过侦听应用程序缓存对象的 updateready 事件，以编程方式检查应用程序是否具有更新的缓存清单文件。当应用程序缓存处于 updatedready 状态时，使用交换缓存功能将旧的清单文件与新的清单文件交换，并重新加载窗口以重新加载清单文件中已更改的资源。

```
function onUpdateReady() {
if(window.applicationCache.status === window.applicationCache.UPDATEREADY) {
        		window.applicationCache.swapCache();
        		location.reload();

}
}
window.applicationCache.addEventListener('updateready', onUpdateReady);
```

## 结论:

应用程序缓存功能通过支持离线浏览、更快地检索存储在缓存中的资源以及减少服务器负载，揭示了 HTML5 的真正潜力。

## 分享这篇文章`