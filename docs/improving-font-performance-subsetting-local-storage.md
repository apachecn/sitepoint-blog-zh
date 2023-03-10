# 通过子集化和本地存储提高字体性能

> 原文：<https://www.sitepoint.com/improving-font-performance-subsetting-local-storage/>

最近有一项技术似乎越来越受欢迎，那就是使用一种称为子集化的方法来减小 web 字体的大小，将其编码为 base64，并存储在本地存储中。如果不小心使用 Web 字体，会对性能产生巨大的影响，并且会阻止对网站内容的访问。

这项技术将允许您减少字体文件的大小，并将它们异步存储在本地存储中，在下载字体时显示一个后备系统字体。这一点，再加上本地存储固有的一些特性，允许您持久地缓存您的字体。这意味着它们将留在客户端的机器上，跨会话持续存在，甚至跨设备重启。

## 设置字体子集

字体子集化是提高网页字体性能的最重要的事情之一。子集化只不过是从字体文件中删除不用的字符。未使用的字符通常是您不使用的语言中的字符，或者您的网站或应用程序可能不需要但通常嵌入在字体文件中的特殊字符。通过子集化，您可以将文件大小减少 50%。

您可以使用 [Font Squirrel 的 web 字体生成器](http://www.fontsquirrel.com/tools/webfont-generator)将您的字体子集化并进行 base64 编码成一个最终文件(确保选择专家选项来访问自定义子集，并在表单的“CSS”部分选择“Base64 编码”)。

完成后，您将在一个样式表文件中包含所有的 web 字体，经过压缩，可以随时重复使用。

## 选择备用字体

为了避免让用户在浏览器试图下载字体文件时等待，最好向用户显示一种备用系统字体。这允许立即访问内容(毕竟这是他们来的目的)。

同步加载字体*会在浏览器等待字体文件时将文本留空，用户在等待文件下载完成时将无法阅读内容。*

 *通过*异步*加载并选择合适的备用字体，用户将立即看到以备用字体显示的文本，并且当文件被下载时，字体将转换为您选择的 web 字体。

您可以设置后备字体的样式，使这种过渡更加柔和，减少内容的回流。因为用户可以毫无延迟地访问你的内容，这是对你的网站或应用程序的感知性能的直接改善。

要了解适用于不同操作系统的系统字体，您可以查看以下资源:

*   [CSS 字体堆栈](http://www.cssfontstack.com)–适用于 Mac 和 Windows 的 web 安全 CSS 字体堆栈的完整集合。
*   [iOS 字体](http://iosfonts.com)–列出每个 iOS 版本的所有字体。

在 Android 上，由于叉子的数量和使用它的不同品牌，更难说出哪些是系统字体。然而，Android 上最常见的字体是:Droid Serif、Droid Sans、Droid Mono 和 Roboto。

## 使用本地存储来保存 Web 字体

首先让我们向`<html>` DOM 节点添加一个类，它将保存后备字体样式。使用 JavaScript，这将在以后被一个具有加载字体样式的类所替换。我们还将把指向字体文件的路径保存到一个变量中，供以后重用。

```
document.documentElement.className = 'fallback';
var css_href = '../path/fonts.css';
```

接下来，我们需要通过尝试在本地存储中设置和获取项目来检查本地存储支持。有些浏览器在私有模式下不能存储任何东西，但是`window.localStorage`仍然会返回一个存储对象。我们需要这个额外的请求来确保我们的脚本能够正常工作:

```
var localStorageSupported = function() {
  try {
    localStorage.setItem('test', 'test');
    localStorage.removeItem('test');
      return true;
    } catch(e) {
      return false;
    }
}
```

如果浏览器通过了`localStorageSupported`测试，并且我们的字体文件已经被存储，我们可以使用`injectRawStyle()`函数获取该文件并将其添加到页面标题的`style`标签中。如果浏览器没有通过测试，我们在`onLoad`事件上调用`injectFontsStylesheet()`函数，这样我们就不会阻塞 ui 线程:

```
if (localStorageSupported() && localStorage.webFonts) {
  injectRawStyle(localStorage.webFonts);
} else {
  window.onload = function() {
    injectFontsStylesheet();
  } 
}
```

`injectFontsStylesheet()`函数发出`xhr`请求以获取字体文件内容，在`injectRawStyle`函数的帮助下将其注入到文件头中，并保存到本地存储:

```
function injectFontsStylesheet() {
  var xhr = new XMLHttpRequest();
  xhr.open('GET', css_href, true);
  xhr.onreadystatechange = function() {
    if (xhr.readyState === 4) {
      injectRawStyle(xhr.responseText);
      localStorage.webFonts = xhr.responseText;
    }
    xhr.send();
  }
}
```

这个函数在文档的`head`中创建`style`标签，并通过`text`参数获取其内容。也是在这个阶段，我们用具有 web 字体样式的 font 类替换 fallback 类:

```
function injectRawStyle(text) {
  var style = document.createElement('style');
  style.innerHTML = text;
  document.getElementsByTagName('head')[0].appendChild(style);
  document.documentElement.className = 'webFont';
}
```

现在我们需要回退和 web 字体的实际样式。您可以通过刷新浏览器并观察内容的回流来测试这些样式。我们的目标是尽可能地将后备字体样式与真实的样式相匹配，这样人们就不会察觉到变化了。

```
.fallback {
  font-family: Verdana, sans-serif;
  line-height: 1.58em;
  letter-spacing: 0px;
  font-size: 9px;
} 

.webFont {
  font-family: 'Proxima-Nova', sans-serif;
  line-height: 1.3em;
  letter-spacing: 2px;
  font-size: 13px;
}
```

## 演示和总结

如果你想看一下代码，你可以在下面的代码栏中查看 JavaScript 面板。

参见[码笔](http://codepen.io)上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[469 f 4667 e 96 cc 9 e 89208 AFB 7 e 3 bfbfb 2](http://codepen.io/SitePoint/pen/469f4667e96cc9e89208afb7e3bfbfb2/)。*