# 使用 XML 数据遍历

> 原文：<https://www.sitepoint.com/xml-data-traversal/>

在本文中，我们将了解一个基于 XML 的客户端 JavaScript，它从外部 XML 文件中读取数据，遍历 XML 数据，并以树格式显示数据。

我将使用 Microsoft Internet Explorer 内置的 XMLDOM ActiveX 对象来实现这一点。

考虑以下 XML 文件:

```
<?xml version="1.0"?> 

<personal>Personal Details 

  <name>Premshree Pillai</name> 

  <sex>male</sex> 

  <websites>Websites 

    <ws1>http://www.sitepoint.com</ws1> 

    <ws2>http://sitepointforums.com</ws2> 

  </websites> 

</personal>
```

我们希望脚本像这样显示上面的 XML 数据:

*   个人:个人详细信息
*   姓名:普雷姆什里·皮莱
*   性别:男性
*   网站:网站
    *   ws1:http://www.sitepoint.com
    *   ws2:http://sitepointforums.com

##### 该算法

下面是我们将用来实现这一目标的流程:

1.  读取 XML 文件
2.  将变量 tree 指向 XML 数据的第一个节点(XML 标记)。
3.  If the node has child nodes:
    *   打印“
        *   ”；
    *   对于每个子节点，遍历(tree.childNodes(nodeNum))
    *   打印“”；
4.  如果节点没有任何子节点:
    *   打印节点的值。

##### 脚本和解释

让我们来看看剧本:

```
var xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
```

上面的代码创建了一个新的。XMLDOM ActiveX 对象。

```
function loadXML(xmlFile) { 

  xmlDoc.async="false"; 

  xmlDoc.onreadystatechange=verify; 

  xmlDoc.load(xmlFile); 

}
```

`loadXML()`函数用于加载特定的。xml 文件。该函数引用了`verify()`函数，如下所示:

```
function verify() {  

  if(xmlDoc.readyState!=4) 

    return false;  

}
```

XML 文件的加载分 5 个阶段进行:

*   0–对象未初始化
*   1–加载对象正在加载数据
*   2–加载的对象已加载数据
*   3–可以处理来自对象的数据
*   4–对象完全初始化

XML 文件的加载状态可以通过 XMLDOM 的`readyState`属性来访问:如果一个文件(对象)没有初始化，`xmlDoc.readyState`将返回 0，以此类推。因此，通过`loadXML()`函数，我们可以验证 XML 文档的加载状态——显然，我们不想使用部分或完全未初始化的对象。

现在，让我们看看执行 XML 数据遍历的函数，`traverse()`:

```
function traverse(tree) { 

  if(tree.hasChildNodes()) { 

    document.write('<ul><li>'); 

    document.write('<b>'+tree.tagName+' : </b>'); 

    var nodes=tree.childNodes.length; 

    for(var i=0; i<tree.childNodes.length; i++) 

      traverse(tree.childNodes(i)); 

    document.write('</li></ul>'); 

  } 

  else 

    document.write(tree.text); 

}
```

`traverse()`函数是一个递归函数，它将一个节点作为它的参数。

如算法前面所解释的，该函数首先检查节点是否有任何子节点。如果节点有子节点，则使用 HTML 列表(`<ul>`、`<li>`标签)实现必要的缩进。接下来，对于这个节点的每个子节点，函数`traverse()`被(递归地)调用，参数作为那个子节点。

如果节点(传递给`traverse()`的参数)没有子节点，函数将打印该节点(标签)保存的值。这样，XML 文件的树结构就生成了。现在，让我们来看看`initTraverse()`函数:

```
function initTraverse(file) { 

loadXML(file); 

var doc=xmlDoc.documentElement; 

traverse(doc); 

}
```

`initTraverse()`函数将 XML 文件名作为它的参数。该函数首先加载 XML 文件，将变量 doc 设置为 XML 数据的根节点，然后使用`traverse()`函数遍历 XML 数据，以 argument 为根节点(即`doc`)。

当您想要生成 XML 文件的树结构时，就会调用这个函数。

以上所有代码都可以放在一个外部。js 文件。以下代码必须放在必须生成 XML 文件的树形式的位置:

```
initTraverse("anyXMLfile.xml");
```

这是完整的脚本。

```
<script language="JavaScript"> 

var xmlDoc=new ActiveXObject("Microsoft.XMLDOM"); 

function loadXML(xmlFile) { 

  xmlDoc.async="false"; 

  xmlDoc.onreadystatechange=verify; 

  xmlDoc.load(xmlFile); 

} 

function verify() {  

  if(xmlDoc.readyState!=4) 

    return false;  

} 

function traverse(tree) { 

  if(tree.hasChildNodes()) { 

    document.write('<ul><li>'); 

    document.write('<b>'+tree.tagName+' : </b>'); 

    var nodes=tree.childNodes.length; 

    for(var i=0; i<tree.childNodes.length; i++) 

      traverse(tree.childNodes(i)); 

    document.write('</li></ul>'); 

  } 

  else 

    document.write(tree.text); 

} 

function initTraverse(file) { 

  loadXML(file); 

  var doc=xmlDoc.documentElement; 

  traverse(doc); 

} 

</script>
```

## 分享这篇文章