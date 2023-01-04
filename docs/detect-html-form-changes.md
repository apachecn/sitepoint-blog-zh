# 如何检查一个 HTML 表单是否被修改

> 原文：<https://www.sitepoint.com/detect-html-form-changes/>

表格。没有它们，你的 HTML web 应用程序就会失败——它们是浏览器和服务器之间大多数用户数据传输的基础。毫无疑问，您已经阅读了许多描述 HTML5 或 JavaScript 的表单标签和数据验证的文章。然而，今天我们将讨论如何检查用户是否更改了表单数据。

## 为什么要检查表单更新？

有许多原因可以让您检查表单是否已经更改。例如，如果用户更新了一个或多个字段，但单击离开了页面，您可能会显示“更新未保存”警告。您甚至可以让他们选择保存数据(通过 Ajax)。或者，如果没有进行更新，您的应用程序的效率可以通过不尝试在服务器上验证或重新保存数据来提高。

## JavaScript OnChange 事件——以及为什么不能使用它

您可以将 JavaScript onchange 事件处理程序附加到任何 HTML 表单元素。虽然这似乎是一种可行的方法，而且我在其他地方也看到过这种方法，但这种方法存在许多问题:

*   如果用户改变了一个值，然后又把它改回来，应用程序仍然会认为发生了更新。
*   如果使用 JavaScript 更新表单值，则不会触发 onchange 事件处理程序。
*   向大型表单上的每个元素添加 onchange 处理程序会导致浏览器处理开销。
*   如果在表单中添加或移除元素，您将需要相应地附加和分离事件处理程序。
*   复选框和单选按钮的 onchange 事件在某个浏览器中无法正常工作。*(我怀疑你能猜到是哪一个！)*
*   有一个简单得多的方法…

## 比较默认值

幸运的是，我们不需要经历复杂事件处理的繁琐过程。每个表单元素都有一个与其对象相关联的默认值，即页面加载时表单控件显示的数据。这可以对照当前值进行检查，以发现是否进行了更改。

不幸的是，表单元素类型之间的默认值属性不同…

## 文本<input>和<textarea>字段</textarea>

让我们从简单的元素开始。所有不是“复选框”或“单选”类型的`textarea`和`input`标签都有一个 defaultValue 属性。我们可以将该字符串与当前值进行比较，以确定是否发生了变化，例如:

```
 <!-- name input -->
<input type="text" id="name" name="name" value="Jonny Dough" />

<script>
var name = document.getElementById("name");
if (name.value != name.defaultValue) alert("#name has changed");
</script> 
```

**note:** New HTML5 input types

如果你使用 HTML4 或 XHTML，你的文本输入类型将是“文本”、“隐藏”、“密码”或“文件”HTML5 中引入的新类型也有一个 defaultValue 属性，可以用同样的方式检查。这包括电子邮件，电话，网址，范围，日期，颜色和搜索。

## 复选框和单选按钮

复选框和单选按钮具有 defaultChecked 属性。这可以是真或假，并且可以与元素的检查属性进行比较，例如:

```
 <!-- newsletter opt-in -->
<input type="checkbox" id="optin" name="optin" checked="checked" />

<script>
var optin = document.getElementById("optin");
if (optin.checked != optin.defaultChecked) alert("#optin has changed");
</script> 
```

请注意，复选框和单选按钮也有一个 defaultValue 属性，但它是分配给 Value 属性的内容，而不是按钮的当前状态。

## 下拉框 <select>如果你使用的是一个select框，它很可能允许用户从下拉列表中选择一个项目。 这就有点复杂了。选择框本身不提供默认值属性，但是我们可以检查它的option元素(类似数组)集合。加载页面时，具有“selected”属性的选项的 defaultSelected 属性设置为 true。 我们可以从选择节点的 selectedIndex 属性中检索当前选择的选项的索引号。因此，如果该选项的 defaultSelected 设置为 false，则选择框一定发生了变化，例如: <!-- job title select box --> <select id="job" name="job"> <option>web designer</option> <option selected="selected">web developer</option> <option>graphic artist</option> <option>IT professional</option> <option>other</option> </select> <script> var job = document.getElementById("job"); if (!job.options[job.selectedIndex].defaultSelected) alert("#job has changed"); </script> 这段代码将适用于任何单选选择框，其中一个选项有一个'选择'属性。不幸的是，这里有一些陷阱: 如果没有选项具有“selected”属性，浏览器将默认为第一个选项，但其 defaultSelected 属性将为 false。 如果两个或更多的选项有一个' selected '属性(不合逻辑，但可能)，所有选项都将 defaultSelected 属性设置为 true，但浏览器只能默认最后一个选项。 多个select框允许用户高亮显示任意数量的选项: <!-- skills multi-select box --> <select id="skills" name="skills" multiple="multiple"> <option selected="selected">HTML</option> <option selected="selected">CSS</option> <option selected="selected">JavaScript</option> <option>PHP</option> </select> 多选选择框并不流行，可能是因为一系列的复选框提供了一个更加用户友好的界面。然而，当使用它们时，不止一个option可以将其 defaultSelected 属性设置为 true。select 节点的 selectedIndex 属性无效，因此我们必须依次遍历每个选项，以发现其 selected 属性是否与 defaultSelected 属性匹配。 以下代码将检查对任何选择框的更改，而不管选项是如何定义的: var skills = document.getElementById("skills"), c = false, def = 0, o, ol, opt; for (o = 0, ol = n.options.length; o < ol; o++) { opt = skills.options[o]; c = c || (opt.selected != opt.defaultSelected); if (opt.defaultSelected) def = o; } if (c && !skills.multiple) c = (def != skills.selectedIndex); if (c) alert("#skills has changed"); 这就是检查表单元素是否发生变化的方法。 但是，如果我们有一个通用的、可重用的 JavaScript 函数，可以检测任何形式的变化，在所有浏览器中工作，并且不需要一个庞大的库，这不是很好吗？请密切关注 site point——它很快就会到来！ 分享这篇文章</select>