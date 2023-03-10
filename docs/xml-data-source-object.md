# 使用 XML 数据源对象

> 原文：<https://www.sitepoint.com/xml-data-source-object/>

XML 数据源对象(DSO)是内置于 Microsoft Internet Explorer 4+中的 Microsoft ActiveX 控件。使用这个对象，可以将外部 XML 文件中的内容或者 HTML 文件中嵌入的 XML 数据提取到 HTML 页面中。

在本文中，我将解释如何包含 XML DSO，从外部 XML 文件中提取内容，提取嵌入在网页中的 XML 数据，并使用 JavaScript 操纵这些数据。

要完成这些示例，您需要这个 zip 文件，其中包含示例代码。

##### 履行

我们可以使用`<OBJECT>`标签初始化 XML-DSO 对象。XML-DSO 的`CLASSID`是:

```
CLSID:550dda30-0541-11d2-9ca9-0060b0ec3d39
```

上面的 ID 唯一地标识了 XML-DSO。我们在网页中初始化该控件，如下所示:

```
<OBJECT ID="SomeID" CLASSID="CLSID:550dda30-0541- 

11d2-9ca9-0060b0ec3d39"></OBJECT>
```

大多数`OBJECT`都有许多与之相关的参数，但是 XML-DSO 不需要任何这样的参数。

现在，我们既可以从外部 XML 文件中提取数据，也可以使用 XML 数据岛，将 XML 代码嵌入 HTML 页面本身。让我们来看看这两种解决方案。

##### 例子

首先，我们将看看如何从 XML 数据岛(包含在 HTML 页面本身中的 XML 数据)中提取数据。看一下下面的代码:

```
<!-- example1.htm --> 

<html> 

<head> 

<title>XML DSO-example1.htm</title> 

</head> 

<body bgcolor="#FFFFFF"> 

<xml id="xmldb"> 

  <db> 

    <member> 

      <name>Premshree Pillai<name> 

      <sex>male</sex> 

    </member> 

    <member> 

      <name>Vinod</name> 

      <sex>male</sex> 

    </member> 

  </db> 

</xml> 

<span datasrc="#xmldb" datafld="name"<</span> 

<br> 

<span datasrc="#xmldb" datafld="sex"></span> 

</body> 

</html>
```

上面的输出是:

```
Premshree Pillai 

male
```

注意，在 example1.htm 的代码中，我们没有初始化 XML-DSO 对象。因此，当您使用 XML 数据岛时，对象是隐式创建的。

在上面的代码中，我们使用`<XML>`标签包含了一个 XML 数据岛。我们为它分配了一个 ID，xmldb，供以后使用。现在，我们可以使用像`<A>`、`<SPAN>`、`<DIV>`等 HTML 标签从 XML 数据岛中提取数据。如您所见，我们在这里使用`<SPAN>`标签提取了数据。注意`<SPAN>`标签中的属性 datasrc 和 datafld。datasrc 用于指定要从中提取数据的数据岛的 ID。datafld 用于指定要从中提取数据的 XML 标记(这里，第一个是 name`<SPAN>`，第二个是 sex`<SPAN>`)。

注意，在我们的 XML 数据岛中有两个`<name>`和`<sex>`标记，但是使用上面的方法，我们只能提取这些标记的第一个实例。为了提取所有实例，我们必须使用`<TABLE>`标签。看一下下面的例子:

```
<!-- example2.htm --> 

<html> 

<head> 

<title>XML DSO-example2.htm</title> 

</head> 

<body bgcolor="#FFFFFF"> 

<xml id="xmldb"> 

  <db> 

    <member> 

      <name>Premshree Pillai<name> 

      <sex>male</sex> 

    </member> 

    <member> 

      <name>Vinod</name> 

      <sex>male</sex> 

    </member> 

  </db> 

</xml> 

<table datasrc="#xmldb" border="1"> 

  <thead> 

    <th>Name</th> 

    <th>Sex</th> 

  </thead> 

  <tr> 

    <td><div datafld="name"></div></td> 

    <td><div datafld="sex"></div></td> 

  </tr> 

</table> 

</body> 

</html>
```

上面的输出是:

![1121_image1](img/ca5ad30b5946ee72780a5011769a32ac.png)

这里，我们使用了`<TABLE>`标签，并使用 HTML 列标签`<TD>`中的 HTML 标签`<DIV>`提取内容。该表将自动遍历`<member>`(`<name>`和`<sex>`的父节点)的每个实例，因此，我们可以以格式化的方式显示所有的姓名和年龄。

现在，我们将看看如何使用 XML-DSO 从外部 XML 文件中提取内容。考虑以下 XML 文件:

```
<!-- example3.xml -->  

<?xml version="1.0" ?>  

<ticker>  

  <item>  

    <message>JavaScript Ticker using XML DSO</message>  

      <URL>http://someURL.com</URL>  

  </item>  

</ticker>
```

现在，考虑下面的 HTML 页面:

```
<!-- example3.htm -->  

<html>  

<head>  

<title>XML DSO-example3.htm</title>  

<script language="JavaScript">  

function load() {  

  var xmlDso=myXML.XMLDocument;  

  xmlDso.load("example3.xml");  

}  

</script>  

</head>  

<body bgcolor="#FFFFFF" onLoad="load()">  

<object id="myXML" CLASSID="clsid:550dda30-0541-11d2-9ca9-  

0060b0ec3d39" width="0" height="0">  

</object>  

<table datasrc="#myXML" border="1">  

  <thead>  

    <th>Message</th>  

    <th>URL</th>  

  </thead>  

  <tr>  

    <td><div datafld="message"></div></td>  

    <td><div datafld="URL"></div></td>  

  </tr>  

</table>  

</body>  

</html>
```

上面的输出是:

![1121_image2](img/122d5a9839b22791082c336fa792808e.png)

遵守 example3.htm 法典。首先，我们创建了一个 id 为 myXML 的 XML-DSO 对象。请注意，我们已经将宽度和高度属性添加到了`<OBJECT>`标签中，并将它们的值设置为 0。这是因为我们想要创建一个 XML-DSO 对象，但是我们不希望它在网页中占据任何空间

接下来，我们创建了一个用`datasrc`作为 myXML 的表，类似于示例 2。我们使用`<DIV>`标签(在`TD`标签中)提取内容，第一列使用 datafld 作为消息，第二列使用 URL。这里的附加代码是我们添加的`<SCRIPT>`。正如你在脚本中看到的，我们已经将变量`xmlDso`设置为`myXML.XMLDocument`，它指的是我们创建的对象。接下来，我们使用 XML-DSO 的`load()method`加载 example3.xml。现在，example3.xml 文件被绑定到对象 myXML。其他的都和前面的例子差不多。

因此，当我们想要使用 XML-DSO 加载外部 XML 文件时，我们必须显式地包含对象，以及加载外部 XML 文件的一小部分 JavaScript。上面的脚本非常特殊，不能用来加载任何 XML 文件。一个更通用的脚本如下:

```
<script language="JavaScript">  

var xmlDso;  

function load(xmlFile, objName) {  

  eval('xmlDso='+objName+'.XMLDocument');  

  xmlDso.load(xmlFile);  

}  

</script>
```

并且，要加载任何 XML 文件，请使用:

```
load("SomeXMLFile.xml","anyXmlDsoObject");  

XML DSO and JavaScript
```

还可以使用 JavaScript 操作 XML DSO 对象。考虑以下 XML 文件:

```
<!-- example4.xml -->  

<?xml version="1.0" ?>  

<myDB>  

  <member>  

    <name>Premshree Pillai</name>  

      <sex>male</sex>  

  </member>  

  <member>  

    <name>Vinod</name>  

    <sex>male</sex>  

  </member>  

  <member>  

    <name>Santhosh</name>  

    <sex>male</sex>  

  </member>  

</myDB>
```

现在，考虑下面的 HTML 文件:

```
<!-- example4.htm -->  

<html>  

<head>  

<title>XML DSO-example4.htm</title>  

<script language="JavaScript">  

function load() {  

  var xmlDso=myDB.XMLDocument;  

  xmlDso.load("example4.xml");  

  /* Get the complete record set */  

  var memberSet=myDB.recordset;  

  /* Go to next data */  

  memberSet.moveNext();  

}  

</script>  

</head>  

<body bgcolor="#FFFFFF" onLoad="load()">  

<object id="myDB" CLASSID="clsid:550dda30-0541-11d2-9ca9-  

0060b0ec3d39" width="0" height="0">  

</object>  

<span datasrc="#myDB" datafld="name"></span>  

</body>  

</html>
```

现在，上面的输出将是:

```
Vinod
```

上面的脚本相当简单明了。最初，我们使用记录集方法将数据文件的全部数据存储到变量`memberSet`中。`moveNext()`方法指向下一个数据项(下一行)。这里可以使用的一些其他方法有:

*   `movePrevious()`:指向上一个数据项。
*   `moveFirst()`:指向第一个数据项。
*   `moveLast()`:指向最后一个数据项。
*   `EOF`:该属性用于检查我们是否到达了数据的末尾。

注意，在上面的方法中，数据是相对于正在显示的节点的父节点指向的。

##### 使用 XML-DSO 的 XML Ticker

到目前为止，我们已经考虑了简单的例子。现在，我们来看一个更动态的例子:“使用 XML-DSO 的 XML Ticker”。在本例中，ticker 从外部示例文件中读取其消息和 URL，并以指定的延迟标记消息。

```
<!-- ticker.xml -->   

<?xml version="1.0" ?>   

<ticker>   

  </item>   

  <item>   

    <message>JavaScripts by Premshree Pillai</message>   

      <URL>http://premshree.resource-locator.com/   

javascripts.htm</URL>   

  </item>   

  <item>   

    <message>The Scripting Newsletter</message>   

      <URL>http://premshree.resource-locator.com/cgi-bin/   

newsletter.pl</URL>   

  </item>   

</ticker>   

<!-- ticker.css -->   

.tickerStyle {   

  font-family:verdana,arial,helvetica; font-size:10pt;    

color:#666666;    

border:#666666 solid 1px; width:400px; height:20px;    

text-decoration:none; text-align:center;   

 background:#FFFFFF   

}   

.tickerStyle:hover {   

  font-family:verdana,arial,helvetica; font-size:10pt;    

color:#FF6600;    

border:#666666 solid 1px; width:400px; height:20px;    

text-decoration:none; text-align:center;   

 background:#FFFFFF   

}   

<!-- ticker.htm -->   

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">   

<html>   

<head>   

<title>XML Ticker using XML DSO</title>   

<link rel="stylesheet" href="ticker.css">   

<!-- begin script -->   

<script language="JavaScript">   

//////////////////////////////////////////////////   

//  XML Ticker using XML DSO    //   

//////////////////////////////////////////////////   

var xmlDso, tickerSet;   

function initTicker(xmlFile, objName, counter, maxMsgs, timeOut) {   

  /* Check for IE4+ */   

  if(document.all&&navigator.userAgent.indexOf("Opera")==-1) {   

    eval('xmlDso='+objName+'.XMLDocument');   

    xmlDso.load(xmlFile);   

    setTimeout("xmlDsoTicker('"+objName+"','"+counter+"',   

'"+maxMsgs+"',   

'"+timeOut+"')", timeOut);   

  }   

  else {   

    alert('This Ticker works with IE4+ only!');   

    return false;   

  }   

}   

function xmlDsoTicker(objName, counter, maxMsgs, timeOut) {   

  /* Get all the data as a record set */   

  eval('tickerSet=' + objName + '.recordset');   

  if(!tickerSet.EOF && counter<maxMsgs-1) {   

    tickerSet.MoveNext();   

    counter++;   

  }   

  else {   

    counter=0;   

    tickerSet.MoveFirst();   

  }   

  setTimeout("xmlDsoTicker('"+objName+"','"+counter+"',   

'"+maxMsgs+"',   

'"+timeOut+"')", timeOut);   

}   

</script>   

<!-- end script -->   

</head>   

<body bgcolor="#FFFFFF">   

  <!-- begin ticker placement -->   

     

  <object id="ticker" CLASSID="clsid:550dda30-0541-11d2-9ca9-   

0060b0ec3d39" width="0"   

 height="0"></object>   

  <a href="" datasrc="#ticker" datafld="URL"   

class="tickerStyle">   

    <span datasrc="#ticker" datafld="message"></span>   

  </a>   

  <script language="JavaScript">   

  var tickerMaxMsgs=2; // Maximum Messages in the XML Data file   

  var tickerCount=tickerMaxMsgs;   

  new initTicker("ticker.xml","ticker",tickerCount,tickerMaxMsgs,2000);   

  </script>   

     

  <!-- end ticker placement -->   

</body>   

</html>
```

输出如下所示:

![1121_image3](img/e39d83999872a6478e4534bc9bf3151c.png)

在您离开之前，[不要忘记下载我们在本文中涉及的所有示例](https://www.sitepoint.com/examples/xmldso/examples.zip)！

## 分享这篇文章