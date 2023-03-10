# 用 JavaScript 阅读和显示服务器端 XML 文章

> 原文：<https://www.sitepoint.com/server-side-xml-javascript-2/>

##### 项目:基于 XML 的 JavaScript Ticker

还有更多可用的属性和方法，使用它们，您可以创建许多客户端应用程序。使用 XML 和 JavaScript 的主要优点是编辑数据变得非常容易。由于 XML 是结构化的，它使得内容的管理变得非常容易。一个例子是文件夹树菜单。另一个是 JavaScript Ticker。您可以在 [DynamicDrive](http://www.dynamicdrive.com/dynamicindex2/xmlticker.htm) 找到这个基于 XML 的 JavaScript Ticker 示例的完整代码。

我们将创建一个基于 XML 的 JavaScript Ticker，可以显示任意数量的消息。ticker 从 XML 文件中读取其内容(即 ticker 样式)、要显示的文本以及特定消息的链接。我们称这个 XML 文件为`ticker_items.xml`。

XML 文档的结构如下:

```
<?xml version="1.0"?>  

<ticker>  

  <tickerstyle  

    pause       = "true" / "false"       "true" for pause onMouseOver  

    timeout     = positive integer       The delay in seconds b/w messages  

    border      = positive integer       The border width of Ticker  

    bordercolor = #HexColor              The border color of Ticker  

    background  = #HexColor              The background color of Ticker  

    width       = positive integer       Ticker width  

    height      = positive integer       Ticker height  

  />  

  <tickerlinkstyle>  

    <mouseout  

      font       = "verdana,arial,helvetica..."     Ticker link font   

      color      = #HexColor                        Ticker link color  

      decoration = "none" / "underline" /  

                   "underline + overline"           Ticker link style  

      weight     = "normal" / "bold"                Ticker link weight  

      size       = <positive integer>pt             Ticker link size  

    />  

    <mouseover  

      font       = "verdana,arial,hevetica..."      Ticker link font  

      color      = #HexColor                        Ticker link color  

      decoration = "none" / "underline" /  

                   "underline + overline"           Ticker link style  

      weight     = "normal" / "bold"                Ticker link weight  

      size       = <positive integer>pt             Ticker link size  

    />  

  </tickerlinkstyle>  

  <tickeritem  

    URL       = A valid URL                         Ticker link URL  

    target    = "_blank" / "_top" / "_self" /  

                <any other valid target name>       Ticker link target  

  > Ticker item 1 text </tickeritem>  

  <tickeritem ...> Ticker item 2 text </tickeritem>  

  ...  

</ticker>
```

##### XML Ticker 脚本

```
<script language="JavaScript1.2">  

// XML Ticker JavaScript  

// (c) 2002 Premshree Pillai  

// http://www.qiksearch.com  

// Use freely as long as all messages are as it is  

// Location of script:   

http://www.qiksearch.com/javascripts/xml/ticker.htm  

var xmlDoc = new ActiveXObject("Microsoft.XMLDOM");  

function loadXML(xmlFile)  

{  

 xmlDoc.async="false";  

 xmlDoc.onreadystatechange=verify;  

 xmlDoc.load(xmlFile);  

 ticker=xmlDoc.documentElement;  

}  

function verify()  

{   

 if (xmlDoc.readyState != 4)  

 {   

  return false;   

 }  

}  

loadXML('ticker_items.xml');  

document.write('<style type="text/css">');  

document.write('.ticker_style{font-family:' +   

 ticker.childNodes(1).childNodes(0).getAttribute('font') + ';   

 font-size:' +   

 ticker.childNodes(1).childNodes(0).getAttribute('size')  

 + '; color:' +   

 ticker.childNodes(1).childNodes(0).getAttribute('color') +   

 '; font-weight:' +   

 ticker.childNodes(1).childNodes(0).getAttribute('weight') +  

 '; text-decoration:' +   

 ticker.childNodes(1).childNodes(0).getAttribute('decoration')   

 + '}');document.write('.ticker_style:hover{font-family:' +   

 ticker.childNodes(1).childNodes(1).getAttribute('font') +   

 '; font-size:' +   

 ticker.childNodes(1).childNodes(1).getAttribute('size')   

 + '; color:' +   

 ticker.childNodes(1).childNodes(1).getAttribute('color') +   

 '; font-weight:' +   

 ticker.childNodes(1).childNodes(1).getAttribute('weight') +   

 '; text-decoration:' +   

 ticker.childNodes(1).childNodes(1).getAttribute  

 ('decoration') + '}<br>');  

document.write('</style>');  

document.write('<table style="border:' +   

 ticker.childNodes(0).getAttribute('border')   

 + ' solid ' + ticker.childNodes(0).getAttribute('bordercolor') +   

 '; background:' + ticker.childNodes(0).getAttribute('background') +   

 '; width:' + ticker.childNodes(0).getAttribute('width') + '; height:'   

 + ticker.childNodes(0).getAttribute('height') + '">  

 <tr><td><div id="ticker_space"></div>  

 </td></tr></table>');  

var item_count=2;  

var timeOutVal=(ticker.childNodes(0).getAttribute('timeout'))*1000;  

var original_timeOutVal=timeOutVal;  

var isPauseContent;  

if(ticker.childNodes(0).getAttribute('pause')=="true")  

{  

 isPauseContent=' onmouseover="setDelay();" onmouseout="reset();"';  

}  

else  

{  

 isPauseContent='';  

}  

function setTicker()  

{  

 document.all.ticker_space.innerHTML='<a href="' +   

 ticker.childNodes(item_count).getAttribute('URL') + '" target="'   

 + ticker.childNodes(item_count).getAttribute('target') +   

 '" class="ticker_style"' + isPauseContent + '>' +    

 ticker.childNodes(item_count).firstChild.text + '</a>';  

 if(item_count==ticker.childNodes.length-1)  

 {  

  item_count=2;  

 }  

 else  

 {  

  item_count++;  

 }  

 setTimeout("setTicker()",timeOutVal);  

}  

function setDelay()  

{  

 timeOutVal=10000000000000;  

 item_count--;  

}  

function reset()  

{  

 timeOutVal=original_timeOutVal;  

 setTicker();  

}  

setTicker();  

</script>
```

正如您在源代码中看到的那样，报价器显示:

*   所有要显示的消息，*   每条消息的链接，*   每个 URL 的目标，*   滚动条静态样式，*   翻转式，*   边框宽度、颜色和背景，*   来自 XML 文件的消息之间的延迟等等。

因此，如果您想要更改 Ticker 的任何参数，您所要做的就是在 XML 文件中进行必要的更改。

这里显示的 ticker 是一个基本的 ticker，它按照 XML 文件中指定的时间间隔旋转消息。你可以在自动收报机上添加许多效果，比如“褪色信息”或“电传打字机”。您还可以添加一些功能来改变跑马灯的速度，或者在一瞬间列出所有的消息！

**Go to page:** [1](/server-side-xml-javascript) | [2](/server-side-xml-javascript-2/)

## 分享这篇文章