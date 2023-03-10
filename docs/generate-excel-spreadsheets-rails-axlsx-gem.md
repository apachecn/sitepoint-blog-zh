# 使用 Rails 和 Axlsx Gem 生成 Excel 电子表格

> 原文：<https://www.sitepoint.com/generate-excel-spreadsheets-rails-axlsx-gem/>

![xls icon. Flat design style eps 10](img/7e4b57c2ed40ce9e1f1686a176f19fa8.png)

您可能已经基于一些数据在 Rails 应用程序中生成了 CSV 文件。然后可以将这些文件转换成 Excel 格式，以便应用一些样式、格式、添加图表等。然而，将所有这些任务自动化并生成。而不是 xlsx 文件？有了兰迪·摩根创造的 [axlsx](https://github.com/randym/axlsx) 宝石，这是完全可能的！

这个 gem 提供了一个庞大的特性列表，允许您做几乎任何事情，从添加简单的格式规则到生成图表和设置打印选项。在 [GitHub](https://github.com/randym/axlsx/blob/master/examples/example.rb) 上可以找到很多不同的例子。此外，还有其他可用的宝石:

*   [axlsx _ rails](https://github.com/straydogstudio/axlsx_rails)–为 ax lsx 提供 Rails 模板。我们将在本文中使用的一个非常好的解决方案
*   [acts _ as _ ax lsx](https://github.com/randym/acts_as_xlsx)–特殊的 ActiveRecord mixin 为您的模型配备`to_xlsx`方法
*   [activeadmin-xlsx](https://github.com/randym/activeadmin-axlsx)—[active admin](https://github.com/activeadmin/activeadmin/)的插件，这是[在一篇关于 active admin 的 SitePoint 文章中提到的](https://www.sitepoint.com/easy-admin-interfaces-active-admin-rails/)。

今天我们将了解如何借助 axlsx gem 在 Rails 应用程序中生成和定制 Excel 文件。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Generate_Excel_with_axlsx) 上获得。

## 创建应用程序

对于这个演示，我将使用 Rails 5(尽管它仍处于测试阶段)，但 [axlsx_rails](https://github.com/straydogstudio/axlsx_rails) 可以与 Rails 3 和 Rails 4 一起工作。 [axlsx](https://github.com/randym/axlsx) 本身与 Ruby 1.9.2 及更高版本兼容。

让我们称我们的演示应用程序为“ExcelNinja”:

```
$ rails new ExcelNinja -T 
```

我们将在数据库中存储一些样本数据，然后基于这些数据生成 Excel 文件，所以添加一个新的`Product`模型:

```
$ rails g model Product title:string price:decimal
$ rake db:migrate 
```

我将利用 *seeds.rb* 用样本数据填充这个新表。然而，我也希望我的产品看起来或多或少的真实，所以让我们包括 [faker](https://github.com/stympy/faker) gem。它可以生成几乎任何东西，从产品价格到书名和伪 It 短语。

*Gemfile*

```
[...]
gem 'faker'
[...] 
```

奔跑

```
$ bundle install 
```

并调整 *seeds.rb* 文件:

*db/seeds.rb*

```
30.times do
  Product.create({title: Faker::Commerce.product_name, price: Faker::Commerce.price})
end 
```

现在填充表格:

```
$ rake db:seed 
```

好了，最后要做的是设置一些路线，并创建一个控制器和视图。

*config/routes.rb*

```
[...]
resources :products, only: [:index]

root 'products#index'
[...] 
```

*产品 _ 控制器. rb*

```
class ProductsController < ApplicationController
  def index
    @products = Product.order('created_at DESC')
  end
end 
```

*views/products/index . html . erb*

```
<h1>List of products</h1> 
```

在这个视图中，我不会列出任何内容，相反，我们会为用户提供一个下载格式化 Excel 文件的链接，所以请继续下一部分。

## 正在生成 Excel 文件

将一颗新宝石添加到您的*宝石文件*中:

*Gemfile*

```
[...]
gem 'axlsx_rails'
[...] 
```

然后跑

```
$ bundle install 
```

有了这块宝石，你可以像这样引入一种新的回应格式:

*产品 _ 控制器. rb*

```
[...]
def index
  @products = Product.order('created_at DESC')
  respond_to do |format|
    format.html
    format.xlsx
  end
end
[...] 
```

以相应的格式显示新链接:

*views/products/index . html . erb*

```
<h1>List of products</h1>
<%= link_to 'Download as .xlsx', products_path(format: :xlsx) %> 
```

现在创建一个名为 *index.xlsx.axlsx* 的新模板(是的，扩展的名称很复杂，所以要确保输入正确)。这将是一个普通的 Ruby 文件，包含生成*的指令。xlsx* 。在视图内部，有一个`xlsx_package`局部变量可用:

*views/products/index . xlsx . ax lsx*

```
wb = xlsx_package.workbook 
```

这样，我们只需实例化一个新的 Excel 工作簿。每个工作簿都由工作表组成，所以让我们添加一个工作表:

*views/products/index . xlsx . ax lsx*

```
wb = xlsx_package.workbook
wb.add_worksheet(name: "Products") do |sheet|
end 
```

在该块中，您可以定义该表的内容:

*views/products/index . xlsx . ax lsx*

```
wb = xlsx_package.workbook
wb.add_worksheet(name: "Products") do |sheet|
  @products.each do |product|
    sheet.add_row [product.title, product.price]
  end
end 
```

所以用`add_row`我们创建了一个包含产品标题和价格的新行。`name: "Products"`设置工作表的标题。

启动服务器，导航到根页面，然后单击“下载方式”。xlsx”链接。这很简单，不是吗？

要重命名生成的文件，请使用以下代码:

*产品 _ 控制器. rb*

```
[...]
respond_to do |format|
  format.html
  format.xlsx {
    response.headers['Content-Disposition'] = 'attachment; filename="all_products.xlsx"'
  }
end
[...] 
```

或者，您可以使用带有各种选项的`render`方法:

```
render xlsx: 'products', template: 'my/template', filename: "my_products.xlsx", disposition: 'inline',
  xlsx_created_at: 3.days.ago, xlsx_author: "Elmer Fudd" 
```

更重要的是，可以在 mailers 内部生成 Excel 文件—[在此阅读更多信息](https://github.com/straydogstudio/axlsx_rails#mailers)。

## 式样

目前我们的表单看起来有点无聊，所以让我们为价格添加一些背景色。为此，在块中初始化一个带有样式的新变量:

*index.xlsx.axlsx*

```
wb = xlsx_package.workbook
wb.styles do |style|
  highlight_cell = style.add_style(bg_color: "EFC376")

  wb.add_worksheet(name: "Products") do |sheet|
    @products.each do |product|
     sheet.add_row [product.title, product.price], style: [nil, highlight_cell]
    end
  end
end 
```

使用`add_style`方法，我们引入了样式规则，这些规则可以应用于一个或多个单元格。当调用`add_row`时，我们只需传递接受每个单元格样式的`:style`选项。只要我不想样式化第一列(包含产品的标题)，我就简单地指定`nil`作为第一个数组的元素。第二个元素包含我们的定制样式，为产品的价格设置橙色背景。

还有更多的样式可以设置(更多信息，请参考[示例](https://github.com/randym/axlsx/blob/master/examples/example.rb))。例如，让我们为价格添加一个细边框，并将内容水平居中:

*index.xlsx.axlsx*

```
[...]
highlight_cell = style.add_style(bg_color: "EFC376",
                                   border: Axlsx::STYLE_THIN_BORDER,
                                   alignment: { horizontal: :center })
[...] 
```

您可以轻松覆盖任何单元格的样式:

```
sheet.rows.last.cells.first.u = :single
# Or
sheet.rows.last.cells[0].u = :single 
```

基本上，行和单元格被组织为数组的元素。`u`方法使单元格的内容加下划线(由于某种原因，这在 LibreOffice 中不起作用——在这里找到其他已知问题)。

## 格式化

好了，现在我还想显示每个产品的生产日期。修改`add_row`方法调用:

*index.xlsx.axlsx*

```
sheet.add_row [product.title, product.price, product.created_at], style: [nil, highlight_cell] 
```

然而，正如您所记得的，`created_at`列以一种非常难看且不友好的格式存储信息。为该单元格添加格式规则怎么样？也可以通过以下方式实现:

```
date_cell = style.add_style(format_code: "yyyy-mm-dd", border: Axlsx::STYLE_THIN_BORDER) 
```

我还添加了一个细边框，就像我们为价格添加的一样。样式和格式规则可以毫无问题地混合在一起。

现在应用新的样式规则:

*index.xlsx.axlsx*

```
sheet.add_row [product.title, product.price, product.created_at], style: [nil, highlight_cell, date_cell] 
```

让我们也为价格添加格式规则:

*index.xlsx.axlsx*

```
highlight_cell = style.add_style(bg_color: "EFC376",
                                 border: Axlsx::STYLE_THIN_BORDER,
                                 alignment: { horizontal: :center },
                                 :format_code => '#.##') 
```

相反，您可以使用预定义的格式之一:

```
highlight_cell = style.add_style(bg_color: "EFC376",
                                 border: Axlsx::STYLE_THIN_BORDER,
                                 alignment: { horizontal: :center },
                                 num_fmt: 8) 
```

## 添加超链接

通过使用`add_hyperlink`方法并提供对所需单元格的引用，您可以向任何单元格添加超链接。假设我们希望产品标题引导`ProductsController`的`show`动作。让我们快速添加新的方法、视图和路由:

*config/routes.rb*

```
[...]
resources :products, only: [:index, :show]
[...] 
```

*产品 _ 控制器. rb*

```
[...]
def show
  @product = Product.find(params[:id])
end
[...] 
```

*views/products/show . html . erb*

```
<h1><%= @product.title %></h1>
<p>Price: <%= @product.price %></p> 
```

现在添加超链接:

*index.xlsx.axlsx*

```
[...]
@products.each do |product|
  sheet.add_row [product.title, product.price, product.created_at],
                style: [nil, highlight_cell, date_cell]

  sheet.add_hyperlink location: url_for(action: "show", id: product.id, only_path: false),
                      ref: sheet.rows.last.cells.first
  [...]
end
[...] 
```

这里有几件事需要注意。`url_for`方法生成适当的链接。但是，我们必须将`:only_path`选项设置为`false`，否则这个方法将生成一个明显不正确的相对路径。`:ref`选项指定超链接的地址。我们已经知道，单元格可以像数组的元素一样被访问。只要我们在循环中调用`add_hyperlink`，那么`sheet.rows.last`将总是指向新添加的行，而`sheet.rows.last.cells.first`则指向带有产品标题的单元格。

超链接也可以指向其他单元格和工作表。假设您在工作簿中添加了其他工作表:

```
wb.add_worksheet(name: 'Another Sheet') do |sheet|
  sheet.add_row ['a cell']
end 
```

您希望链接指向这个新工作表的 B2 单元格:

```
sheet.add_hyperlink location: "'Another Sheet'!B2", ref: sheet.rows.last.cells.first, target: :sheet 
```

只需提供工作表的名称、单元格的编号，并将`:target`选项设置为`:sheet`。

## 添加公式

您可以使用 axlsx 轻松添加 Excel 公式。假设我们想计算所有产品的总价。当然，这可以直接在 *index.xlsx.axlsx* 文件中完成，但这并不是最佳选择。相反，让我们使用`SUM`函数。

在循环之后添加新的一行代码:

*index.xlsx.axlsx*

```
[...]
@products.each do |product|
  [...]
end
sheet.add_row ['Total', "=SUM(B1:B#{@products.length})"]
[...] 
```

我们不知道有多少产品将存储在数据库中，所以使用插值来生成适当的行号。

## 添加图表

axlsx 支持所有主要的图表类型:条形图、折线图、饼图和散点图。在本演示中，让我们创建一个 3D 条形图来展示产品价格。使用`add_chart`方法:

*index.xlsx.axlsx*

```
[...]
sheet.add_chart(Axlsx::Bar3DChart, start_at: "D1", end_at: "N40") do |chart|
end
[...] 
```

`:start_at`指定图表的左上角，而`:end_at`–右下角。如果您的数据库中有许多产品，这些值必须相应地调整，否则图表会太小。你也可以引入一个像这样的简单公式:

*index.xlsx.axlsx*

```
sheet.add_chart(Axlsx::Bar3DChart, start_at: "D1", end_at: "N#{(@products.length * 1.5).floor}") do |chart|
end 
```

在块中，通过添加数据系列、提供标签和样式规则来简单描述图表:

*index.xlsx.axlsx*

```
[...]
sheet.add_chart(Axlsx::Bar3DChart, start_at: "D1", end_at: "N#{(@products.length * 1.5).floor}") do |chart|
  chart.add_series data: sheet["B1:B#{@products.length}"],
                   labels: sheet["A1:A#{@products.length}"],
                   title: "Products by price",
                   colors: @products.map { "00FF00" }
end
[...] 
```

*   `:data`表示从哪里获取图表数据。
*   `:labels`指定每个系列的标签加载位置。
*   `:title`指定图表本身的标题。
*   `:colors`指定每个系列使用的颜色。

我发现对于 LibreOffice 你必须提供所有系列*的颜色，否则根本不会显示。*

您也可以轻松隐藏网格线:

*index.xlsx.axlsx*

```
sheet.add_chart(Axlsx::Bar3DChart, start_at: "D1", end_at: "N#{(@products.length * 1.5).floor}") do |chart|
  chart.add_series data: sheet["B1:B#{@products.length}"],
                   labels: sheet["A1:A#{@products.length}"],
                   title: "Products by price",
                   colors: @products.map { "00FF00" }

  chart.valAxis.gridlines = false
  chart.catAxis.gridlines = false
end 
```

显然，这些设置在 LibreOffice 中没有任何效果，因为它在任何情况下都不显示网格线。

## 结论

在本文中，我们讨论了 axlsx gem，它允许您轻松地生成 Excel 文件。我们还利用 axlsx_rails 来集成 rails。正如我已经说过的，使用 axlsx 还可以做更多的事情:合并列、添加条件格式、添加页眉和页脚、隐藏或保护工作表等。许多用例可以在[示例文件](https://github.com/randym/axlsx/blob/master/examples/example.rb)中找到。这块宝石也被记录在 YARD 中，所以你可以按照这里[的指示](https://github.com/randym/axlsx#documentation)去阅读它。

不幸的是，axlsx 并不理想，确实存在一些已知的与 LibreOffice 和 Google Docs 的互操作性问题(实际上我们在这个演示中偶然发现了一些)。这些问题的列表可以在[这里](https://github.com/randym/axlsx#documentation)找到。

尽管如此，这些问题都是很小的，总而言之，axlsx 是一个非常好的解决方案，我建议您尝试一下。如果有什么不适合你的地方，请随时在评论中提出你的问题。谢谢你和我呆在一起，再见！

## 分享这篇文章