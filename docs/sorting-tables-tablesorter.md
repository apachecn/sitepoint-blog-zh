# 使用表格排序器对表格进行排序

> 原文：<https://www.sitepoint.com/sorting-tables-tablesorter/>

开发网站时，我们经常需要显示一些表格数据。我们可能需要显示从罗马到伦敦的下一趟航班、选定日期范围内的可用酒店，或者像 SitePoint 这样的出版商出版的最新书籍。HTML 为表格数据提供了一个元素，毫不奇怪，这个元素叫做`table`。它的问题是，这个元素没有一个 API 允许您根据某个标准对它的行进行排序，比如从低到高的价格。

由于缺乏 API，一些 JavaScript 库被开发和发布来解决这个问题。在本文中，我将向您介绍 Tablesorter(实际上是它的一个分支)，这是一个用于客户端表排序的 jQuery 插件。

## 什么是 Tablesorter？

由[罗布·加里森](https://github.com/Mottie)开发的[表排序器](https://github.com/Mottie/tablesorter)是由[克里斯蒂安·巴赫](http://twitter.com/lovepeacenukes)开发的原[表排序器](https://github.com/christianbach/tablesorter)库的一个分支。Tablesorter 是一个简单的 jQuery 插件，它提供了基于一个或多个给定列的值的动态行排序，还提供了对使用 HTML `table`元素创建的表进行分页的可能性。正如我在介绍中提到的，这个插件有助于填补 HTML 中没有的这些经常需要的特性的空白。作为一个客户端库，它的主要优势之一是我们的用户不需要重新加载他们正在访问的页面来排序数据。此外，客户端排序通常非常快，除非有非常大量的数据要排序。

你会喜欢这个图书馆的简单性。在最基本的情况下，你必须包含这个库，然后调用一个名为`tablesorter()`的方法，以提供对表格数据进行排序的可能性。最后，这个插件有[丰富的文档](http://mottie.github.io/tablesorter/docs/)，可以帮助你处理你在项目中可能遇到的大多数情况。

现在你知道这个插件是什么了，让我们看看你如何在你的网站中使用它。

## Tablesorter 入门

要使用 Tablesorter，您必须下载它，并将其包含在您打算使用它的页面中。您可以通过几种不同的方式获得 Tablesorter。第一种是通过访问它的 [GitHub 库](https://github.com/Mottie/tablesorter)，第二种是通过下面的 Bower 命令。

```
bower install jquery.tablesorter
```

这个插件由一个主 JavaScript 文件、其他可选的 JavaScript 文件和几个主题组成。下载后，你必须包含一个主题，你可以在“css”文件夹下找到，如下所示:

```
<link rel="stylesheet" href="css/theme.default.css" />
```

在这种情况下，代码包括“默认”主题。您必须在 jQuery 库之后导入 JavaScript 文件:

```
<script src="path/to/jquery.js"></script>
<script src="js/jquery.tablesorter.min.js"></script>
```

在您准备好在您的网站中使用 Tablesorter 之前，还有最后一步要执行。您必须将类`tablesorter`分配给所有您想与插件一起使用的表。这只是为了样式的目的，但这是你真正想做的事情，否则主题不会被应用。虽然插件仍然工作，但是你的用户不会有任何关于添加功能的线索。

### 添加`tablesorter`类

有两种方法可以将类添加到感兴趣的表中。第一种，也是更简单的，是在 HTML 源代码中手动添加类名。但是如果您不能访问或修改 HTML 源代码呢？在这种情况下，您可以使用 JavaScript 来动态添加它。假设您希望允许您的用户对页面中每个表的数据进行排序。为此，您可以编写如下代码，并将其放在页面底部:

```
var tables = document.getElementsByTagName('table');

for (var i = 0; i < tables.length; i++) {
   tables[i].className += ' tablesorter';
}
```

这为您提供了尽可能广泛的浏览器兼容性。但是，如果只需要支持现代浏览器，可以利用 classList API 等方法检索类似 [`queryselectorall()`](http://cjihrig.com/blog/javascripts-selectors-api/) 的 DOM 元素。

使用 jQuery，前面的代码可以缩短为:

```
$('table').addClass('tablesorter');
```

此时，我们可以调用方法`tablesorter()`来公开排序功能。该插件的一个简单示例如下所示:

```
$('table').tablesorter();
```

### 基本演示

为了让您看到这个插件的运行，我们将使用下表:

```
<table class="tablesorter">
   <thead>
      <tr>
          <th>Title</th>
          <th>Author</th>
          <th>Price (in dollars)</th>
      </tr>
   </thead>
   <tbody>
      <tr>
          <td>The CSS3 Anthology, 4th Edition</td>
          <td>Rachel Andrew</td>
          <td>29.00</td>
      </tr>
      <tr>
          <td>Instant jQuery Selectors</td>
          <td>Aurelio De Rosa</td>
          <td>13.26</td>
      </tr>
      <tr>
          <td>Killer UX Design</td>
          <td>Jodie Moule</td>
          <td>29.00</td>
      </tr>
      <tr>
          <td>Jump Start CSS</td>
          <td>Louis Lazaris</td>
          <td>19.00</td>
      </tr>
   </tbody>
</table>
```

将目前列出的片段放在一起，产生了下面的演示:

[https://jsfiddle.net/3jkb13qn/embedded/](https://jsfiddle.net/3jkb13qn/embedded/)

点击标题，你会看到你的表格现在是可排序的！

值得一提的是，Tablesorter 可以在所有标准 HTML `table`上工作，所以你必须在表格中使用`thead`和`tbody`。

## 设置默认顺序

有时，您希望来自服务器的未排序数据显示为已经基于给定标准排序。使用 Tablesorter，这可以通过向`tablesorter()`方法传递一个对象文字来轻松实现。这个对象文字可以包含几个选项来配置插件以您需要的方式工作。[官方文件](https://mottie.github.io/tablesorter/docs/#Configuration)中报告了所有可用选项的列表。

假设您希望根据第一列的值对页面上的所有表格进行降序排序。为此，您必须传递一个`sortList`属性，其值是一个 2D 数组，其中第一个元素表示要排序的列的从 0 开始的索引，第二个元素是用于降序的`1`和用于升序的`0`。因此，为了实现所讨论的目标，你必须写:

```
$('table').tablesorter({
   sortList: [[0, 1]]
});
```

这个示例可以通过设置多列顺序标准来进一步扩展。如果要根据第一列的值按降序排序，第三列的值按升序排序，可以编写:

```
$('table').tablesorter({
   sortList: [[0, 1], [2, 0]]
});
```

下面显示了一个使用前面例子的现场演示，其中的[也可以作为 JSFiddle](https://jsfiddle.net/wpkxzazn/) 使用:

[https://jsfiddle.net/wpkxzazn/embedded/](https://jsfiddle.net/wpkxzazn/embedded/)

## 向表格添加条纹

为了提高数据的可读性，你应该为你的行使用两种不同的颜色:一种用于奇数行，另一种用于偶数行。用 Tablesorter 执行这项任务简单得可笑。您所要做的就是将一个名为`widgets`的属性添加到配置对象中，传递一个包装在数组中的值`zebra`:

```
$('table').tablesorter({
   widgets: ['zebra']
});
```

在我们看到这段代码的结果之前，我想展示一些别的东西。

## 更改默认主题

如果你想改变默认的主题，你必须用一个代表你想要的主题的文件来替换 CSS 文件。所以，假设你想用“蓝色”主题。你必须更换线路:

```
<link rel="stylesheet" href="css/theme.default.css" />
```

使用:

```
<link rel="stylesheet" href="css/theme.blue.css" />
```

接下来，您必须通过在配置对象中设置一个名为`theme`的属性来通知插件这一变化，该属性的值代表您想要使用的主题的名称。在这种情况下，因为使用了“蓝色”主题，所以您必须写:

```
$('table').tablesorter({
   theme: 'blue'
});
```

在我们看到现场演示之前，我想向您介绍最后一个功能:分页。

## 用表排序器分页数据

分页特性是由 Tablesorter 作为外部插件提供的，所以在我们的页面中使用它之前，我们必须包含相关的 JavaScript 和 CSS 文件。分页插件与 Tablesorter 一起下载，两个文件都位于名为“addons/pager”的文件夹中。一旦在页面中包含了这些文件(记住 JavaScript 文件必须包含在 Tablesorter 插件之后)，就必须为页面设置标记，如下所示:

```
<div id="pager" class="pager"> 
   <img src="addons/pager/icons/first.png" class="first"/> 
   <img src="addons/pager/icons/prev.png" class="prev"/> 
   <span class="pagedisplay"></span>
   <img src="addons/pager/icons/next.png" class="next"/> 
   <img src="addons/pager/icons/last.png" class="last"/> 
   <select class="pagesize" title="Select page size"> 
      <option selected="selected" value="2">2</option> 
      <option value="3">3</option>
      <option value="4">4</option>
   </select>
   <select class="gotoPage" title="Select page number"></select>
</div>
```

此时，您必须通过指定包含页面所有元素的包装器来调用`tablesorterPager()`方法。基于前面的标记，您必须编写:

```
$('#my-table')
   .tablesorter()
   .tablesorterPager({
      container: $('#pager')
   });
```

下面是一个表格的例子，它使用了分页器，更改了默认主题，还使用了 zebra stripe 小部件，而[也可以作为 JSFiddle](https://jsfiddle.net/jgydxbdr/) 使用:

[https://jsfiddle.net/jgydxbdr/embedded/](https://jsfiddle.net/jgydxbdr/embedded/)

## 结论

在本文中，我向您介绍了 fork of Tablesorter，这是一个 jQuery 插件，用于将标准的 HTML 表格转换为可排序的表格，而无需刷新页面。这个插件可以成功地解析和排序许多类型的数据，包括单元格中的链接数据。通过使用这个插件，你还可以给你的数据添加分页，这样它们更容易阅读。如果你喜欢这个想法，但不喜欢这个插件，并且你想要一个替代方案，我建议你查看一下[数据表](http://www.datatables.net/)。

## 分享这篇文章