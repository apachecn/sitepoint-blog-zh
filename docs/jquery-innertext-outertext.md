# jQuery innerText()与 outerText()

> 原文：<https://www.sitepoint.com/jquery-innertext-outertext/>

## jQuery InnerText()与 jQuery OuterText()

jQuery Innertext()和 OuterText()函数几乎完全相同。注意，这些可以被称为 jQuery 函数，但它们实际上是 JavaScript 属性。为了便于讨论，我们将使用 jQuery！:)

jQuery InnerText()属性用指定的文本替换元素的开始和结束标记之间的内容，而 OuterText()属性删除元素并插入指定的文本。

要记住的事情:

* InnerText 只是替换标签内的内容
* Outertext 替换内容和封闭标签本身

## jQuery innerText()函数示例

```
//simple example to change the text of a page element
document.getElementById("elementID").innerText = "I love jQuery4u Blog!";
```

```
 这是将要改变的文本。

<button onclick="para1.innerText='WOW! It changed!'">Change text</button>
<button onclick="para1.innerText='And back again'">Reset</button>
```

[演示](http://samples.msdn.microsoft.com/workshop/samples/author/dhtml/refs/innerText.htm)

## jQuery outerText()函数示例

```
document.getElementById("elementID").outerText = "I love jQuery4u Blog!"
```

```
 是的，**我** < 3 <u>jQuery</u> 。

<button id="myButton" onclick="function1();">Make Text Above Plain</button> 
```

## 分享这篇文章