# Flash 脚本–将 XML 数据加载到 Flash 中

> 原文：<https://www.sitepoint.com/script-load-xml-data-flash/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/xml.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/xml.swf"></object>**

在这里，我将向您展示如何将 xml 数据加载到 Flash 中。注意，本教程假设您对 xml 非常了解。[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/xml.zip)。

**1。**让我们从创建一个 xml 文档开始。打开记事本并插入:

```
<?xml version="1.0" encoding="iso-8859-1"?> 

   <xmltest> 

       <parentnode> 

         <node1>First node value</node1> 

        <node2>Second node value</node2> 

        <node3>Third node value</node3> 

        <node4>Fourth node value</node4> 

        <node5>Fifth node value</node5> 

       </parentnode> 

   </xmltest>
```

**2。**将文件另存为 flash.xml。

**3。**在 Flash 中打开一部新电影。我已经将电影属性设置为宽度:370 和高度:200。

**4。**现在，创建动态文本，并给它一个变量名“txt”。确保在文本选项面板中检查 html。

**5。**右键单击电影中的第一个也是唯一一个帧，然后选择动作。插入:

```
XML_var = new XML(); 

// now load up the url. 

XML_var.load("http://www.flashcircle.com/swffiles/flash.xml"); 

// when xml is loaded call functon displayXML 

XML_var.onLoad = displayXML; 

// display in txt xml is loading 

txt = "Loading XML data..."; 

function displayXML()  

{ 

mainTag = new XML; 

elementTag = new XML; 

articleList = new Array; 

elementList = new Array; 

mainTag = this.firstChild.nextSibling; 

articleList = mainTag.childNodes;  

txt = ""; 

//loop through xml 

for(i=0;i<=articleList.length;i++) 

{//start for 

elementList = articleList[i].childNodes; 

//start for 

for(j=0;j<=elementList.length;j++)  

{//start for 

elementTag = elementList[j]; 

head = elementTag.firstChild.nodeValue; 

if(elementTag.nodeName.toLowerCase() == "node1") 

{txt += head +"";} 

if(elementTag.nodeName.toLowerCase() == "node2") 

{txt += head +"";} 

if(elementTag.nodeName.toLowerCase() == "node3") 

{txt += head +"";} 

if(elementTag.nodeName.toLowerCase() == "node4") 

{txt += head +"";} 

if(elementTag.nodeName.toLowerCase() == "node5") 

{txt += head +"";} 

}//end for 

}//end for 

}
```

让我解释一下每一行是如何工作的。

```
XML_var = new XML()

...declare XML_var as a new xml object.

```
XML_var.load("http://www.flashcircle.com/swffiles/flash.xml")

...load the xml file.

```
XML_var.onLoad = displayXML

...if file is loaded, call the displayXML()/#epc#/ function.

```
`for(i=0;i<=articleList.length;i++)`

...iterate using for loop through xml file to display the elements.
```

```

```

```

## 分享这篇文章