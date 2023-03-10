# Mozilla 中的 XML 和 JavaScript

> 原文：<https://www.sitepoint.com/xml-javascript-mozilla/>

**在我的文章“用 JavaScript 读取和显示服务器端 XML”中，我讨论了在 Microsoft Internet Explorer 中使用 JavaScript 操作 XML 文件。在本文中，我将解释如何在 Mozilla 中使用 JavaScript 读取和使用 XML 文件数据。我们将看到如何显示标记值、标记属性值等等。**

从今以后，我不会在本文中明确提到 Mozilla，因为我们将只讨论 Mozilla，它包括 Netscape 6.x、7.x。不过，当我提到 Microsoft Internet Explorer (MSIE，或简称为 IE)时，我会明确告诉您。

##### 示例 XML 文件

我将使用我在上一篇文章中使用过的那个 XML 文件，这样对于那些已经处理过那个文件的人来说就更容易理解了。考虑以下 XML 文件:

```
<?xml version="1.0" ?>  

<company> 

  <employee id="001" sex="M" age="20">Premshree Pillai</employee> 

  <employee id="002" sex="M" age="24">Kumar Singh</employee> 

  <employee id="003" sex="M" age="21">Ranjit Kapoor</employee> 

  <turnover> 

    <year id="2000">100,000</year> 

    <year id="2001">140,000</year> 

    <year id="2002">200,000</year> 

  </turnover> 

</company>
```

如您所见，上面的 XML 文件显示了一家公司的雇员的详细信息，姓名作为标签`<employee>`的主(或节点)值；其他细节，比如员工的 id、性别和年龄，在同一个标签中作为属性`id`、`sex`和`age`给出。该文件还显示了公司的营业额，营业额数字作为标签`<turnover>`的节点值，年份作为同一标签内的属性`year`。

在接下来的小节中，我们将操作上面的 XML 文件，以便它对我们有意义。

##### XML 和 JavaScript

在我们真正开始阅读和进一步操作 XML 文件之前，了解访问者使用的是 Mozilla 还是其他浏览器是很重要的。

***为 Mozilla* 测试**

显然，您不会为一个特定的浏览器编写基于 XML 的 JavaScript 应用程序，因为您可以轻松地扩展对其他浏览器的支持。然而，编写应用程序的方式将取决于访问者使用的浏览器。

为了测试 Mozilla，我们可以使用如下的简单变量:

```
var moz = (typeof document.implementation != 'undefined') && (typeof 

 document.implementation.createDocument != 'undefined');
```

上述变量可用作布尔变量:

```
if(moz) { 

  // Mozilla!! 

} else { 

  // Something else... 

}
```

***加载 XML 文件***

一旦我们确定了浏览器，就该加载 XML 文件了:

```
var xmlDoc=document.implementation.createDocument("", "doc", null) 

xmlDoc.load("someXMLFile.xml"); 

xmlDoc.onload = someProcessingFunction;
```

上面代码中的第一行创建了一个`xmlDoc`对象的实例；第二行加载我们想要的 XML 文件(本例中是 some XML file . XML)；第三行用于进一步处理或操作我们刚刚加载的 XML 文件。

现在，最好创建一个不同的函数来加载 XML 文件:

```
var xmlDoc; 

function importXML(file) { 

  xmlDoc=document.implementation.createDocument("", "doc", null) 

  xmlDoc.load(file); 

  xmlDoc.onload = readXML; 

}
```

***为 Mozilla 和 IE* 加载 XML 文件**

用于 Mozilla 的大部分操作技术也适用于 IE。然而，XML 文件在每个浏览器中的加载方式不同，所以让我们来看一个函数，它将在 Mozilla 和 IE 中加载 XML 文件:

`var xmlDoc;  
function importXML(file) {  
 var xmlDoc;  
 var moz = (typeof document.implementation != 'undefined') && (typeof  
document.implementation.createDocument != 'undefined');  
 var ie = (typeof window.ActiveXObject != 'undefined');  

 if (moz) {  
   xmlDoc = document.implementation.createDocument("", "", null)  
   xmlDoc.onload = readXML;  
 } else if (ie) {  
   xmlDoc = new ActiveXObject("Microsoft.XMLDOM");  
   xmlDoc.async = false;  
   while(xmlDoc.readyState != 4) {};  
 }  
 xmlDoc.load(file);  
}`

上述函数可用于为 Mozilla 和 IE 加载 XML 文件。现在，要加载 XML 文件，必须按如下方式调用该函数:

```
importXML("YourXMLFile.xml");
```

注意变量 ie 是用来测试 IE 的。IE 使用 ActiveX 对象加载 XML 文件。XMLDOM 对象。在下一节中，我们将探索一些可以用来访问 XML 文件数据的方法。

**T2`getElementsByTagName()`**

`getElementsByTagName`方法是 XML DOM(文档对象模型)对象中最常用的方法。顾名思义，该函数返回指定元素中具有给定名称的所有元素(或标签)。基本上，它返回一个对象集合。例如:

`var xmlFile = xmlDoc.getElementsByTagName("company");`

在上面的代码中，包含文档中所有`<company>`元素的对象集合存储在变量`xmlFile`中。请注意，您传递给`getElementsByTagName()`的参数是区分大小写的，即`getElementsByTagName("company")`不同于`getElementsByTagName("ComPanY")`。

***找出一个标记的元素个数***

在本文开头展示的 XML 文件中，我们看到一个`<company>`标签。`getElementsByTagName()`返回的对象集合有一个 length 方法，给出集合中元素的个数。例如，要查找`<company>`标签的数量，使用以下代码:

```
var noOfCompanyTags = xmlDoc.getElementsByTagName("company").length;
```

使用`document.write()`显示变量`noOfCompanyTags`将显示 1。

***显示标签的内容*** 

再次参考 XML 文件，假设我们想要显示第一个雇员的姓名。现在，`<employee>`标签在`<company>`标签内；所以，首先我们需要得到所有`<company>`标签的集合，并通过这个标签得到所有`<employee>`标签的集合。让我们来看看如何一步一步地显示名称:

```
var companies = xmlDoc.getElementsByTagName("company");
```

上面的代码为 companies 变量的`<company>`标记返回一个对象集合。注意，companies 是一个数组。

```
var employees = companies[0].getElementsByTagName("employee");
```

上面的代码将标签`<employee>`的对象集合返回给 employees 变量，同样是在一个数组中。注意公司变量中使用的索引；这是因为我们只需要访问数组的第一个元素。可能只有一个`<company>`标签，但它仍然是一个数组，所以我们使用索引 0 来获取元素。

为了显示第一个雇员的姓名，我们使用以下代码:

```
document.write(employees[0].firstChild.nodeValue);
```

上面的代码会显示`Premshree Pillai`。很明显，employees 是一个 3 元素数组。因此，要显示第二个雇员的姓名，代码应该是:

`document.write(employees[1].firstChild.nodeValue);`

上面的代码会显示`Kumar Singh`。我们用来显示雇员姓名(Premshree Pillai)的所有上述步骤都可以集成到一个代码片段中，如下所示:

```
document.write(xmlDoc.getElementsByTagName("company")[0].getElementsByTagName("employee")[0]  

.firstChild.nodeValue);
```

***访问标签属性***

以属性的形式在 XML 文件中存储信息是非常常见的。因此，我们能够访问 XML 文件中的属性是很重要的。在我们的示例 XML 文件中，我们在`<employee>`标记中存储了各种雇员的详细信息，包括`id`、`sex`和`age`。要提取第一个雇员的年龄，我们可以使用以下代码:

```
document.write(employees[0].getAttribute("age"));
```

上面的代码将输出 20。代码在`employees[0]`对象上使用了`getAttribute()`方法。或者，我们可以使用下面的代码来获得相同的结果:

```
document.write(xmlDoc.getElementsByTagName("company")[0].getElementsByTagName("employee")[0]   

.getAttribute("age"));
```

现在，假设您想以表格形式显示所有雇员的详细信息(id、性别、年龄)。为此，我们必须遍历所有的`<employee>`标签。以下是完成此操作的完整代码(不包括加载文件的代码):

```
var companies=xmlDoc.getElementsByTagName("company");   

var employees=companies[0].getElementsByTagName("employee");   

document.write('<table border="1">');   

document.write('<tr><th>id</th><th>Sex</th><th>Age</th></tr>');   

for(var i=0; i<employees.length; i++) {   

  document.write('<tr>');   

  document.write('<td>' + employees[i].getAttribute("id") + '</td>');   

  document.write('<td>' + employees[i].getAttribute("sex") + '</td>');   

  document.write('<td>' + employees[i].getAttribute("age") + '</td>');   

  document.write('</tr>');   

}   

document.write('<table>');
```

Mozilla 中上述代码的输出如下所示:

![1268_image](img/7a87ca6f14902920e7ed155cd5142ace.png)

***可以写入 XML 文件吗？***

不，使用客户端 JavaScript 写入 XML 文件是不可能的。您可以操作外部 XML 文件的所有内容，并在客户端 JavaScript 应用程序中将其用于显示目的，但是不能接受用户的输入，并使用 JavaScript 对 XML 文件进行更改。

##### 结论

我们现在知道如何测试 Mozilla，如何使用 JavaScript 加载 XML 文档，以及如何在 Mozilla 中使用 JavaScript 操作 XML 文件的内容。使用 XML 和客户端 JavaScript，可以创建几个简单的应用程序:

*   您可以使用 XML 文件存储小型数据库，然后根据需要使用 JavaScript 显示数据
*   您可以创建类似新闻“ticker”的东西，将新闻条目存储在 XML 文件中，JavaScript 读取文件内容并在屏幕上标记新闻条目

这些只是可以使用 XML 和客户端 JavaScript 的几个例子。我希望这篇文章已经让您对在 Mozilla 中使用 XML 和客户端 JavaScript 有了初步的了解。

## 分享这篇文章