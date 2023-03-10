# 用任何 JSON 数据在几秒钟内制作动态表格

> 原文：<https://www.sitepoint.com/dynamic-tables-json/>

这篇文章由斯蒂芬·马克斯和[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![Servants unfolding a table](img/7b6b66d9817989b4ff927a31e11ae7af.png)

如果您只是想对一些基本数据进行布局，标准的 HTML 表格可能会很好，但是如果您想从表格中获得更多信息，该怎么办呢？如果您需要从外部 API 获取数据，使您的表可排序或可编辑，那么您将需要一些功能更强大的东西。

如果这听起来很熟悉，那么[制表器](http://olifolkerd.github.io/tabulator/)就是你的库。制表器是一个轻量级的 jQuery UI 插件，旨在使构建复杂的交互式表格变得轻而易举，只需使用几行 JavaScript，您就可以将几乎任何数据源转换成格式精美的交互式表格。

在本教程中，我将带你了解创建你的第一个制表器的基础知识，然后扩展一些选项，为你的表格添加额外的功能。

## 构建您的第一个制表器

让我们从创建一个非常简单的表开始。

由于制表器是一个 jQuery 小部件，你需要包括来自本地资源或你选择的 [CDN](https://code.jquery.com/) 的 [jQuery](https://jquery.com/) 和 [jQuery UI](http://jqueryui.com/) 库。

你需要给自己弄一份制表库的拷贝，这个库可以从 https://github.com/olifolkerd/tabulator,的 GitHub repo 中克隆出来，包含项目中的`tabulator.css`和`tabulator.js`文件。

```
<link rel="stylesheet" href="tabulator.css">
<script type="text/javascript" src="tabulator.js"></script> 
```

创建一个`<div>`元素来保存表格:

```
<div id="example-table"></div> 
```

让我们用一些 JavaScript 将这个元素变成一个制表器:

```
$("#example-table").tabulator(); 
```

这就是了，一张功能齐全的桌子！

好吧，我们还没到那一步。为了完成我们的表，我们需要定义列并加载一些数据。

## 定义列

为了定义表的布局，我们需要提供关于每个列的一些信息。

我们通过将列定义数组传递给制表器构造函数来实现这一点。数组中的每个对象代表表中的一列，并包含其设置参数:

```
$("#example-table").tabulator({
  columns:[
    {title:"Name", field:"name", sortable:true, width:200},
    {title:"Progress", field:"progress", sortable:true, sorter:"number"},
    {title:"Gender", field:"gender", sortable:true},
    {title:"Favourite Color", field:"col", sortable:false},
    {title:"Date Of Birth", field:"dob"},
    {title:"Cheese Preference", field:"cheese"},
  ],
}); 
```

有大量的柱参数可用，在本演示中，我们将介绍其中的一些:

*   **标题**–*必需的*–将显示在列标题中的标题
*   **字段**–*必填*–数据数组中列的键
*   **对齐**–列的文本对齐*(左|中|右)*
*   **宽度**–列宽*(如果没有设置，系统将确定最合适的)*
*   **可排序**–切换用户是否可以按列排序数据
*   **排序器**——如何对列中的数据进行排序*(默认为字符串)*
*   **格式化程序**–如何格式化*(默认为字符串)*列中的数据
*   **onClick**–当用户点击列中的单元格时的回调
*   **可编辑**–确定该数据是否可由用户编辑
*   **编辑器**–当列中的单元格可编辑时使用的编辑器
*   **可见**–显示或隐藏列

## 将数据加载到表中

构建新制表器的最后一步是加载一些数据。这里有几个选项，我们将在这里简要地讨论每一个。

### JavaScript 数组

您可以使用`setData`方法传入一个数据数组。这需要一个数组，表中的每一行都由一个对象定义。

让我们创建一些示例数据:

```
var sampleData = [
  {id:1, name:"Oli Bob", progress:12, gender:"male", rating:1, col:"red", dob:"", car:1, lucky_no:5, cheese:"Cheader"},
  {id:2, name:"Mary May", progress:1, gender:"female", rating:2, col:"blue", dob:"14/05/1982", car:true, lucky_no:10, cheese:"Gouda"},
  {id:3, name:"Christine Lobowski", progress:42, gender:"female", rating:0, col:"green", dob:"22/05/1982", car:"true", lucky_no:12, cheese:"Manchego"},
  {id:4, name:"Brendon Philips", progress:100, gender:"male", rating:1, col:"orange", dob:"01/08/1980", lucky_no:18, cheese:"Brie"},
  {id:5, name:"Margret Marmajuke", progress:16, gender:"female", rating:5, col:"yellow", dob:"31/01/1999", lucky_no:33, cheese:"Cheader"},
]; 
```

然后将其分配给我们的表:

```
$("#example-table").tabulator("setData", sampleData); 
```

### Ajax 请求

要从远程数据源检索 JSON 格式的数据，您可以向`setData`方法传递一个 URL，它将为您执行 Ajax 请求。

```
$("#example-table").tabulator("setData", "http://www.exampleurl.com/data"); 
```

额外的请求参数可以在带有 URL 的对象中传递。

```
$("#example-table").tabulator("setData", "http://www.exampleurl.com/data", {key1:"value1", key2:"value2"}); 
```

### HTML 表格

您也可以将现有的 HTML 表格转换成制表器。

创建您的 HTML 表格:

```
<table id="example-table">
  <thead>
    <tr>
      <th width="200">Name</th>
      <th>Progress</th>
      <th>Gender</th>
      <th>Height</th>
      <th>Favourite Color</th>
      <th>Date of Birth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Billy Bob</td>
      <td>12</td>
      <td>male</td>
      <td>1</td>
      <td>red</td>
      <td></td>
    </tr>
    <tr>
      <td>Mary May</td>
      <td>1</td>
      <td>female</td>
      <td>2</td>
      <td>blue</td>
      <td>14/05/1982</td>
    </tr>
  </tbody>
</table> 
```

然后在 table 元素上调用制表器构造函数来自动提取标题和数据:

```
$("#example-table").tabulator({}); 
```

## 最终结果

您已经构建了您的表，定义了您的列并加载了您的数据，现在您有了一个功能齐全的交互式表。您的工作列表应该是这样的:

参见 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )制作的 Pen [制表器教程——简单表格](http://codepen.io/SitePoint/pen/zKjrgP/)。

## 附加功能

等等，还有更多！为什么要满足于一个简单的基于文本的表格呢？还有很多事情可以做来改善你的表格的外观、感觉和交互性。在接下来的几节中，我们将介绍一些可以用来增强制表器的附加功能。

### 整理

默认情况下，对所有列都启用排序，单击任何列标题都会按该列对数据进行排序。

可以使用`sortable`参数在每列的基础上启用或禁用排序。

```
$("#example-table").tabulator({
  columns:[
    {title:"Name", field:"name",  sortable:true}, //sorting enabled
    {title:"Progress", field:"progress", sortable:false}, //sorting disabled
  ],
}); 
```

默认情况下，所有列都按文本排序。您可以使用`sorter`参数指定不同的分类器。

```
$("#example-table").tabulator({
  columns:[
    {title:"Name", field:"name",  sortable:true}, //sort as string (default)
    {title:"Progress", field:"progress", sortable:true, sorter:"number"}, //sort as number
  ],
}); 
```

有几个内置的排序器可用:

*   **字符串**–排序为字符串*(默认选项)*
*   **数字**–排序为数字*(整数或浮点数，也将使用“，”分隔符处理数字)*
*   **alpha num**–排序为字母数字字符串
*   **布尔型**–按布尔型排序
*   **日期**–按日期排序

要定义一个定制的分类器，需要向参数`sorter`传递一个回调。它应该返回被比较的两个值之间的差。

```
var customSorter = function(a, b, aData, bData){
  //a and b are the two values being compared
  //aData and bData are the row objects for the values being compared (useful if you need to access additional fields in the row data for the sort)

  return a - b; //return the difference between the two values
}

$("#example-table").tabulator({
  columns:[
    {title:"Progress", field:"progress", sortable:true, sorter:customSorter},
  ],
}); 
```

### 格式化

有了制表器，你就不必固定地以纯文本形式显示数据。使用格式化程序，您可以决定希望每列的外观。

包括各种内置的格式化程序:

*   **明文**–将单元格的值显示为文本。*(默认选项)*
*   **文本区**–显示带有回车符的文本*(适用于多行文本)*
*   **money**–将一个数字格式化为货币符号*(如 1234567.8901 - > 1，234，567.89)*
*   **email**–使用 mailto:链接到给定值的锚来呈现数据
*   **link**–将数据呈现为一个链接到给定值的锚点
*   **勾号**–如果值为(true|'true'|'True'|1)，则为绿色勾号，否则为空单元格
*   **勾号**–如果值为(true|'true'|'True'|1)，则为绿色勾号，否则为红色勾号
*   **星级**–基于整数值的图形化星级评定
*   **进度**–从左到右填充单元格的进度条*(使用 0-100 的值作为宽度的百分比)*

您可以使用`formatter`参数设置列的格式化程序。

```
$("#example-table").tabulator({
  columns:[
    {title:"Progress", field:"progress", formatter:"progress" },
  ],
}); 
```

要定义自定义格式化程序，请将回调传递给`formatter`参数。回调应该返回要在单元格中显示的 HTML。

```
var customFormatter = function(value, data, cell, row, options, formatterParams){
  //value - value of the cell
  //data - data for the row the cell is in
  //cell - DOM element of the cell
  //row - DOM element of the row
  //options - options set for this tabulator
  //formatterParams - parameters set for the column

  return "<div></div>"; // must return the HTML or jQuery element for the contents of the cell;
}

$("#example-table").tabulator({
  columns:[
    {title:"Progress", field:"progress", formatter:customFormatter},
  ],
}); 
```

### 过滤

很多时候，您只需要向用户显示数据的子集。您可以使用`setFilter`功能过滤数据。

```
$("#example-table").tabulator("setFilter", "progress", ">", 10); 
```

该函数有三个参数，要过滤的字段，比较函数 *(=，<，< =，>，> =，！=，like)* ，以及过滤值。

要执行更复杂的过滤，您可以向`setFilter`函数传递一个回调。回调必须返回一个布尔值，用`true`表示匹配。

```
var customFilter = (data){
  //data - data for the row being filtered

  return data.name == "bob" && data.progress < 50; //must return a boolean, true if it passes the filter.
}

$("#example-table").tabulator("setFilter", customFilter); 
```

使用`clearFilter`功能清除现有过滤器。

```
$("#example-table").tabulator("clearFilter"); 
```

### 编辑

与标准 HTML 表格相比，使用制表器的主要好处之一是能够编辑表格中的数据。

有许多预配置的编辑器可用:

*   **输入**–纯文本
*   **textarea**–多行文本，允许行垂直调整大小以适应输入的文本
*   **数字**–带有递增和递减按钮的数字
*   **勾选**–复选框
*   **星级**–星级*(可以使用左/右箭头键和回车选择，也可以使用鼠标)*
*   **进度**–进度条*(可以用左/右箭头键和回车选择，也可以用鼠标选择)*

可以使用`editable`参数将列设置为可编辑。

```
$("#example-table").tabulator({
  columns:[
    {title:"Progress", field:"progress", formatter:"progress", editable:true},
  ],
}); 
```

默认情况下，将使用与该列的格式化程序相匹配的编辑器。要指定不同的编辑器，使用`editor`参数。

```
$("#example-table").tabulator({
   columns:[
    {title:"Progress", field:"progress", formatter:"progress", editable:true, editor:"number"},
  ],
}); 
```

要定义一个自定义编辑器，请向`editor`参数传递一个回调。它应该返回要在单元格中显示的编辑器元素。返回`false`将中止编辑并阻止单元格被选中。

```
var customEditor = function(cell, value, data){
  //cell - JQuery object for current cell
  //value - current value for current cell
  //data - data for the current row

  //create and style editor
  var editor = $("<select><option value=''></option><option value='male'>male</option><option value='female'>female</option></select>");

  editor.css({
    "padding":"3px",
    "width":"100%",
    "box-sizing":"border-box",
  });

  //set value of editor to the current value of the cell
  editor.val(value);

  //when the value has been set, trigger the cell to update
  editor.on("change blur", function(e){
    cell.trigger("editval", editor.val());
  });

  //return the editor element
  return editor;
}

$("#example-table").tabulator({
  columns:[
    {title:"Gender", field:"gender", editable:true, editor:customEditor},
  ],
}); 
```

要访问存储在表格中的数据，使用`getData`功能。这将返回行数据对象的数组。

```
var data = $("#example-table").tabulator("getData"); 
```

## 一个功能齐全的例子

如果我们结合以上学到的所有经验，我们可以创建一个功能丰富的交互式表格。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )提供的 Pen [制表器教程——全功能表](http://codepen.io/SitePoint/pen/WGJLZA/)。