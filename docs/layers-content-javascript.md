# 用 Javascript 重写图层的内容

> 原文：<https://www.sitepoint.com/layers-content-javascript/>

Web 开发人员每天面临的最常见的任务之一是改变 Web 页面的内容，而无需对 Web 服务器提出额外的请求。完成这项任务最简单的方法是使用图层。

在这里，我将向您展示如何用一个简单的函数重写层的内容，该函数可以在您的 Javascript 代码中反复使用。这个功能在两种主流浏览器中都可以使用——网景 4。*/6/7 和 IE 4/5/6。考虑以下代码片段:

```
<DIV ID="MyLayer" style="position:absolute;top:10px; 

left:10px;">Initial layer text</DIV> 

<script language="Javascript"> 

function WriteLayer(ID,parentID,sText) { 

  if (document.layers) { 

    var oLayer; 

    if(parentID){ 

      oLayer = eval('document.' + parentID + '.document.' + ID + '.document'); 

    }else{ 

      oLayer = document.layers[ID].document; 

    } 

    oLayer.open(); 

    oLayer.write(sText); 

    oLayer.close(); 

  } 

  else if (parseInt(navigator.appVersion)>=5&&navigator. 

appName=="Netscape") { 

    document.getElementById(ID).innerHTML = sText; 

  } 

  else if (document.all) document.all[ID].innerHTML = sText 

} 

</script> 

<form> 

<br><br> 

<input type="button" value="Display Time" onclick="WriteLayer 

('MyLayer',null,Date())"> 

</form>
```

我们先仔细看看`WriteLayer()` 函数。该函数有三个参数:`ID`、`parentID` 和`sText`。`ID`是我们的 div 标签的 ID——在我们的例子中是“`MyLayer`”。第二个参数`parentID`是必需的，因为 Netscape 4。* DOM 适用于嵌套层。这些只是“层中之层”。嵌套层的一个例子是:

```
<DIV ID= 

"ParentLayer"><div ID= "ChildLayer"></DIV></DIV>
```

在 Netscape 4 中，您不能直接访问嵌套层。*.访问它的唯一方法是通过它的父层:

```
var oChildLayer = document.ParentLayer.document.ChildLayer.document;
```

当我们调用`WriteLayer()`函数时，如果我们要重写的层没有嵌套在另一层中，那么`parentID`参数应该为空。如果层是嵌套的，`parentID`应该与父层 ID 相同。

第三个参数`sText`，就是新图层的内容。

我们的`WriteLayer()` 函数使用特定于浏览器的代码来处理层重写。如果浏览器是网景 4。*然后执行以下代码:

```
var oLayer; 

if(parentID){ 

  oLayer = eval('document.' + parentID + '.document.' + ID + '.document'); 

}else{ 

  oLayer = document.layers[ID].document; 

} 

oLayer.open(); 

oLayer.write(sText); 

oLayer.close();
```

在第一步中，我们声明`oLayer`变量，它将包含对我们层的引用。在下一步中，我们检查`parentID`的值是否是`null`。如果不是，则执行下面一行:

```
oLayer = eval('document.' + parentID + '.document.' + ID + '.document');
```

对于那些不熟悉 JavaScript `eval()`函数的人，我将更详细地解释这一行。

`eval()` 函数的参数必须是可以作为有效 JavaScript 语句执行的字符串。如果字符串代表一个表达式， `eval()` 对该表达式求值。如果参数代表一个或多个 JavaScript 语句，`eval()`执行这些语句。如果我们用以下参数调用我们的`WriteText()` 函数:

```
WriteLayer("ChildLayer","ParentLayer","Some text...")
```

则表达式将被计算为:

```
oLayer = document.ParentLayer.document.ChildLayer.document;
```

如果`parentID` 参数为`null` ，则可以直接访问该层:

```
oLayer = document.layers[ID].document;
```

在我们获得了对层的引用之后，我们只需打开层的 document 对象，在其中写入 sText 值，然后关闭文档。

```
oLayer.open(); 

oLayer.write(sText); 

oLayer.close();
```

在 Netscape 6/7 中重写层内容要容易得多。我们所需要的就是将`sText`值赋给我们层的`innerHTML`属性，这个属性是用`getElementById`方法访问的:

```
document.getElementById(ID).innerHTML = sText;
```

在 IE 中重写层内容也很容易，因为我们唯一需要做的事情是将 sText 值赋给我们的层的 innerHTML 属性，该属性通过 all 集合访问:

```
document.all[ID].innerHTML = sText;
```

您可以使用`WriteLayer()`函数将任何有效的 HTML 代码写入该层。

要查看我们的`WriteLayer()`功能，请点击[这里](http://www.webmasterbase.com/examples/jscripttips/example_rewrite.htm)。

## 分享这篇文章