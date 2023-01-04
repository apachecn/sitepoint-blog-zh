# jQuery set innerText()、innerHTML()、textContent()

> 原文：<https://www.sitepoint.com/jquery-set-innertext-2/>

jQuery 可以与普通的 ole JavaScript 结合使用，以改变 html 元素的文本，并将其设置为包含新的内容，可以是文本、图像、代码或任何您想要的内容。在这里，我们将详细查看以下 JS 函数，并向您展示如何、何时以及在何处使用它们。

这篇文章涵盖了以下 jQuery 函数:innerText()、innerHTML()、textContent()

## jQuery innerText()函数

**语法:**document . element id . innertext = value

**功能:** JavaScript 读写属性，指定元素开始和结束标记之间的文本。

**行为:**包括换行符。

浏览器兼容性: innerText()可以在我们测试过的所有浏览器中运行。

**基本示例:**

```
 一个 **p** 元素内的示例文本

<button onclick="alert(myP.innerText);">InnerText</button> 
```

## JavaScript innerHTML()函数

**语法:**

document . getelementbyid(" element id ")。innerHTML = value
document . all . element id . innerHTML = value//仅 IE

**功能:**原生 JavaScript 函数，改变页面元素内的 html。

**行为:**去除换行符。

**浏览器兼容性:**所有浏览器都支持 innerHTML。

**基本示例:**

```
 一个 **p** 元素内的示例文本

<button onclick="alert(myP.innerHTML);">InnerHTML</button> 
```

**高级示例:**正则表达式用 n 替换
标签确保它像 innerText 一样工作，最后的 replace()是一个移除所有 HTML 标签的正则表达式。

```
var message = div.innerHTML.replace(/&lt;br&gt;/gi,"n").replace(/(&lt;([^&gt;]+)&gt;)/gi, "")
```

## jQuery textContent()函数

**语法:**

```
var text = element.textContent;
element.textContent = "i love jQuery (4u :P )";
```

**功能:** jQuery FF 函数改变页面元素的文本。

**行为:**去除换行符。

浏览器兼容性: Firefox 有自己的属性 textContent，Chrome 和 Opera 支持，IE 不支持！

**基本示例:**

给定以下 HTML 片段:

This is some text

```
// Get the text content:
var text = document.getElementById("divA").textContent;
```

```
// Set the text content:
document.getElementById("divA").textContent = "This is some text";
```

## 换行问题

innerText()显示“para1？以及“para2？中间有换行符，但 textcontent()没有:

//ie/intratext():# t1]表示 1，
表示 2；//ff/text content():# T3]表示 1 至 2

## HTML/JS 代码来测试它们，然后选择一个！

```
 jQuery4u - This is a division element that contains some red text.

    <button onclick="GetContent ();"> jQuery4u - Get the contents of the division element!</button> 
```

可用于更改页面元素的其他 jQuery 函数:innertext.replace，innerHTML，innertext，textContent，HTML()，text()，div.innerHTML.replace，document.body.innerText，$.fn.innerText，div:contains，document . getelementbyid(id). innerText。

## 分享这篇文章