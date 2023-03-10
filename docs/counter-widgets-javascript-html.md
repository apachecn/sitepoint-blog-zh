# 使用 JavaScript 和 HTML 构建您自己的计数器小部件

> 原文：<https://www.sitepoint.com/counter-widgets-javascript-html/>

建筑形式可能是重复性的工作。通常，特定的表单元素会一致地出现在各种表单中。这些元素有助于创建可以在不同形式的应用程序中重用的组件。

表单中经常出现的一个需求是数字字段。许多表单至少包含一个字段，该字段可以接受出现在某个范围内的数值，并四舍五入为整数(即不允许有小数)。

富客户端应用程序有这样一个小部件——一个后跟“向上”和“向下”箭头的文本字段，允许用户增加或减少字段的值。VB.NET 称这个小部件为数字下载。

为了在普通的 HTML 中提供这种功能，我们可以使用一个下拉列表，其中包含所有可能的值作为选项。但是，这种解决方案有几个缺点:

*   如果你需要用户从大量的数字中选择，下拉列表会变得很长。用户可能不得不滚动列表，并花费大量时间来达到他们想要的值。
*   你需要大量的标记来生成一个下拉菜单。

更好的选择是允许用户在一个简单的文本字段中输入值，并在后端验证他们的输入。

我们需要使用后端验证，因为我们不能依赖用户系统上 JavaScript 的可用性。然而，我们可以使用 JavaScript 来避免令人沮丧的页面重载，并将简单的文本字段转换成“计数器”小部件。

演示页面中的字段通过一个脚本得到增强，该脚本创建链接以在 Javascript 可用时增加或减少字段值。要访问本文中的所有代码，[在这里下载代码档案](https://www.sitepoint.com/examples/counter/counter.zip)。

##### 它是如何工作的

为了实现所需的功能，我们需要做的就是将文件 createcounter.js 嵌入到我们的 HTML 文档中:

```
<script type="text/javascript" src="createcounter.js"></script>
```

我们还需要为我们希望成为计数器的每个文本字段添加一个特殊的 ID:

```
<input type="text" size="3" name="passengers_ctr_1_12" id="passengers_ctr_1_12" value="1" />
```

(name 属性保留在代码中，以确保向后兼容。)

ID 的语法如下:

```
(any name)_ctr_(minimum value)_(maximum value) 

foo_ctr_0_10 

Allows integer values between 0 and 10 for foo 

bar_ctr_-10_10 

Allows integer values between -10 and 10 for bar 

baz_ctr_1_999 

Allows integer values between 1 and 999 for baz
```

代替 ID，我们可以使用一个具有适当名称空间的类`name`或`bespoke`属性。然而，使用 ID 使我们能够在后端复制同样的验证，例如在 PHP 中。

一些开发人员可能不赞成这种标记和业务逻辑的混合，但它使我们不必在后端脚本中复制 JavaScript 代码中的规则。

JavaScript 自动:

*   检查条目是否为数字、整数，并且在指定的范围内
*   加载页面时检查计数器元素的值
*   单击增加链接时，检查并增加值
*   单击减少链接时，检查并减少该值
*   一旦用户输入文本并离开字段，就检查该值

如果条目不是数字，或者低于允许的最小值，脚本会将该值设置为最小值。如果条目超过最大值，脚本会将其设置为最大值。

##### 定制小部件

类别`counterlink`应用于通过该解决方案生成的链接。因此，可以通过 CSS 定制链接。要自定义类名和显示的链接文本，请按如下方式设置变量:

```
var linkclass='counterlink'; 

var addtext='+'; 

var subtext='-'; 

var addbefore=true;
```

变量`addbefore`定义了下降链路的位置。值`true`在文本字段之前应用链接。值`false`在文本字段之后定义它，在递增链接旁边。

##### 该解决方案的缺点

这是 HTML、CSS 和 JavaScript 在不依赖鼠标的情况下所能达到的极限。Real counter widgets 允许用户通过光标键增加和减少数值，并且用户按住任一箭头按钮的时间越长，滚动速度就越快。

可能有一种解决方案允许在某些浏览器环境中使用这种功能，但是它是否能在所有现代浏览器中工作还是一个疑问。

## 分享这篇文章