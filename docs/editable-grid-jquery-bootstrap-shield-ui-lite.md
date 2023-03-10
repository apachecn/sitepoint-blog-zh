# 带有 jQuery、Bootstrap 和 Shield UI Lite 的可编辑网格

> 原文：<https://www.sitepoint.com/editable-grid-jquery-bootstrap-shield-ui-lite/>

在这篇小技巧中，我提供了几个简单的步骤来使用 [Bootstrap](http://getbootstrap.com/) 和 [Shield UI Lite](https://github.com/shieldui/shieldui-lite) 建立一个可编辑的网格(或表格)。

Shield UI Lite 是一个开源的 jQuery 库，其中包括一个 jQuery 网格。网格支持开箱即用的编辑，以及排序、分组和不同的列编辑器。

## 关于 Shield UI Lite

Shield UI 是一家 web 组件开发公司，专门从事纯 JavaScript 开发以及 ASP.NET、ASP.NET MVC 和 Java Wicket 开发的 UI 小部件的设计、开发和营销。该公司提供[数据可视化](https://demos.shieldui.com/web/area-chart/axis-marker)组件，以及一整套标准 web 开发组件，如网格、条形码——一维和二维、扩展输入组件——如数字和屏蔽文本框，以及[等等](https://www.shieldui.com/)。

Shield UI Lite 套件是市场上最新的开源库之一，它的特别之处在于它包含了丰富的组件，所有这些组件都具有丰富的功能和成熟的特性。这包括 [jQuery 网格](https://demos.shieldui.com/web/grid-general/toolbar-template)，它支持所有重要的 web 网格操作。该组件支持使用内嵌或标准编辑表单进行编辑，以及使用外部表单进行编辑。还支持单元格编辑。

除此之外，Shield UI Grid 还有一个内置的 datasource 组件，方便绑定到所有类型的数据——从本地 JSON 结构，到远程 web 服务；内置数据源还负责所有删除、更新和插入操作。

对于数据密集型应用程序，Shield UI jQuery 小部件提供了一个增强的虚拟滚动特性，即使在处理数百万条真实数据记录时也能显著提高性能。要查看组件及其选项的更多示例，您可以参考[在线演示](https://demos.shieldui.com/web/grid-editing/batch-editing)或[文档](https://www.shieldui.com/documentation/grid/javascript/getting.started)。

## 使用代码

对于我们将要创建的可编辑网格，为了简单起见，我使用了一个本地数据源。这些库的源代码可以在 GitHub 上找到[。完整的示例代码，连同所有使用的示例数据，以及额外的 CSS 可在 CodePen 演示](https://github.com/shieldui/shieldui-lite)中[获得。](http://codepen.io/SitePoint/pen/VvZeKd/)

设置布局的第一步是使用标准的引导容器。在我们的例子中，这是一个嵌套了引导面板的`div`。因为任何标准的 web 网格组件通常都包含大量数据，所以我们的布局跨越了屏幕的整个宽度。

这一步的代码很简单，如下所示:

```
<div class="col-md-12">
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="text-center">Bootstrap Editable jQuery Grid
      <span class="fa fa-edit pull-right bigicon"></span></h4>
    </div>
    <div class="panel-body text-center">
      <div id="grid"></div>
    </div>
  </div>
</div>
```

这是设置示例所需的所有 HTML。下一步是添加对示例中使用的所有资源的引用，比如样式表、JavaScript 文件和数据。

示例中使用的数据是一个标准的 JSON 集合，它是单独加载的，以便传递给网格组件。使用本地数据源简化了设置。此外，我们需要用于网格的样式表和用于初始化它的 JavaScript。

这些资源的包含如下所示:

CSS:

```
<link rel="stylesheet" href="shieldui-lite.min.css">
```

脚本:

```
<script src="shieldui-lite-all.min.js"></script>
<script src="shortGridData.js"></script>
```

## 设置网格

该过程的下一步是设置 Shield UI Lite 网格组件。一旦我们包含了必要的资源，我们就可以用 document.ready 处理程序中的一些 JavaScript 来初始化它。

描述组件有两个逻辑部分。第一个是处理将在网格中可视化的数据的数据源，另一个是设置将实际呈现的列，以及任何其他设置，如排序、悬停效果等。

Shield UI Lite 网格有一个内置的`dataSource`属性，这使得将小部件绑定到任何数据(本地或远程)变得很容易。为了将`dataSource`链接到 JSON 数据，我们使用`data`属性并描述将被获取的字段。

实现这一点的代码如下所示:

```
dataSource: {
  data: gridData,
  schema: {
    fields: {
      id: { path: "id", type: Number },
      age: { path: "age", type: Number },
      name: { path: "name", type: String },
      company: { path: "company", type: String },
      month: { path: "month", type: Date },
      isActive: { path: "isActive", type: Boolean },
      email: { path: "email", type: String },
      transport: { path: "transport", type: String }
    }
  }
}
```

## 启用编辑

设置应用程序的下一步是选择网格中可用的属性，以及描述将要呈现的列。

每列都可以有附加属性，如标题文本和宽度。属性设置不是必须的，但是为分布整个布局提供了额外的灵活性。

下面列出了控件中所有属性的代码:

```
rowHover: false,
columns: [
  { field: "name", title: "Person Name", width: "120px" },
  { field: "age", title: "Age", width:"80px" },
  { field: "company", title: "Company Name" },
  { field: "month", title: "Date of Birth", 
  format: "{0:MM/dd/yyyy}", width:"120px" },
  { field: "isActive", title: "Active" },
  { field: "email", title: "Email Address", width: "250px" },
  { field: "transport", title: "Custom Editor", width: "120px", editor: myCustomEditor },
  {
    width: "104px",
    title: "Delete Column",
    buttons: [
      { cls: "deleteButton", commandName: "delete", 
        caption: "<img src='source/delete.png' />
        <span>Delete</span>" }
    ]
  }
],
editing: {
  enabled: true,
  event: "click",
  type: "cell",
  confirmation: {
    "delete": {
      enabled: true,
      template: function (item) {
        return "Delete row with ID = " + item.id
      }
    }
  }
},
```

## 设置自定义编辑器

该小部件支持许多现成的便捷编辑器。一旦通过单击任何单元格将控件置于编辑模式，该单元格的编辑器将取决于该单元格中值的类型。例如，一个数值将有一个带有增量和减量按钮的数字输入。一个日期列将有一个日历输入，方便选择日期。

还有为列提供自定义编辑器的选项。例如，我们可以有一个包含该列所有值的下拉列表，而不是标准的文本框。

这可以通过附加到`getCustomEditorValue`事件并为每个单元格传递一个自定义编辑器来实现。

下面的事件代码片段演示了这一点:

```
events: {
  getCustomEditorValue: function (e) {
    e.value = $("#dropdown").swidget().value();
    $("#dropdown").swidget().destroy();
  }
}
```

和自定义编辑器:

```
function myCustomEditor(cell, item) {
  $('<div id="dropdown"/>')
  .appendTo(cell)
  .shieldDropDown({
    dataSource: {
      data: ["motorbike", "car", "truck"]
    },
    value: !item["transport"] ? null : item["transport"].toString()
  }).swidget().focus();
}
```

如果您想查看有关网格小部件中选项的更多信息，您可以参考文档的[本节](https://www.shieldui.com/documentation/grid/javascript/getting.started)。

这就完成了我们的设置，控件可以使用了。

[**在 CodePen** 上查看完整的工作演示](http://codepen.io/SitePoint/pen/VvZeKd/)

确保在表格/网格中的任何内容单元格内单击，以查看编辑功能是如何工作的。您可以在 Shield UI 网站上查看更多 Shield UI jQuery 网格组件[的使用示例。](https://demos.shieldui.com/web/grid-general/basic-usage)

## 分享这篇文章